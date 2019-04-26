---
layout: post
title:  "On-demand GNS3-Server automation using Packet"
---

### On-demand GNS3-Server automation using Packet 
GNS3 was extensively developed in the past years. It is good that the Client and Server functions are decoupled, so we can use the GUI but still run the simulation on cloud resources. For this kind of virtualization bare metal service required as GNS3 using KVM/QEMU, and most cloud providers are not supporting Nested virtualization. 

[Packet](http://packet.com) provides on-demand physical server resources, so it is a good target to run ESXi or GNS3 server in the cloud. 

The main idea is to book the required resource only for the time of the simulation. Persistency still required so that the GNS3 images and project data is available for future use.

Deployment of the solution is controlled via an Ansible playbook. For this to work we need the following tasks: 
- Add an SSH key to Packet
- Create the Server at Packet
- Attache storage to the server using S3FS
- Verify the server is reachable via SSH
- Connect to the server and download GNS3-server installation script
- Run this script
- update DDNS entry
- Connect and mount the Block storage
- restart GNS3 service
- configure some basic security

We would also require another playbook to destroy the server:
- Unmount /opt and disconnect the storage
- Destroy the server

Now let’s see what is required on the client side. 

### Ansible, Packet-Python and the requirements: 
Installing the automation framework (Ansible) on the client machine is straightforward.  [Documentation](https://docs.ansible.com/ansible/latest/scenario_guides/guide_packet.html) also available how to use Ansible with Packet.com. Generally what we require is two packages:
- Ansible
- Packet-Python

I would suggest to use pip for the installation. I am using [Miniconda3](https://docs.conda.io/en/latest/miniconda.html) on my Mac, and that comes now with a default Python3.7 environment. 

### Packet.com - registration and setup

> Unlike AWS or Google Cloud (which provide only Virtual Machines), Packet gives you full access to a true dedicated server, but with the same automation and flexibility you expect from a public cloud.    Since you have direct access to the virtualization instructions on the CPU, running GNS3 on Packet is a great experience.

Registration at packet.com is easy and automatic like in case of any cloud provider. _There is a special bonus for GNS3 users as described_ [here](https://docs.gns3.com/1IyRZzUYKOe9DzR8YJXO33ONeoZwlkIm0ciHIYuqGFwM/index.html). 

In short, what to do: 
- Register, setup the details, enable the GNS3 promo code, add payment method.
- Enable 2FA authentication
- Create a project, create an API key, write down the project id.  This project ID you can find on the **Project Settings** page.

With these steps completed we are ready to leverage the packet.com API.
### Duckdns - registration and setup
This is a nice free DDNS solution. DDNS is required so that the server can be reference in GNS3 with a single hostname. 
- Just go to [www.duckdns.org](https://www.duckdns.org)
- Login using one of the method
- Write down the sub domain and Token
### Storage - Wasabi - registration and setup
In our solution persistence is still required. We could use Packet’s block storage, but the availability of the service is region dependent. Wasabi seems to fit, and it is cost effective, can be used for other proposes as well. It is fast also. Just head to [http://wasabi.com](http://wasabi.com), register.
- Create a new Object Storage bucket (write down the name). Use the proper region. 
- Create an Access Key, and write down the Key and Secret.

---- 
###  Creating the Ansible environment. 
In a dedicated directory I have created the following files:
- `hosts`: File includes variables and ansible definitions, statics.
- `packet-playbook.yml`: Ansible Playbook file to create and deploy the server
- `packet-delete-playbook.yml`: to destroy the server already running.
- `.newhost`: Temporal file that will contain the information about the device we have provisioned.
- `passwd-s3fs.j2` : Template for password file. 

~~~bash
area-x51:Ansible xcke$ tree  
.
|-- ansible.sublime-project
|-- ansible.sublime-workspace
|-- hosts
|-- packet-delete-playbook.ym
|-- packet-playbook.yml
|-- passwd-s3fs.j2

area-x51:Ansible xcke$ cat passwd-s3fs.j2 
{{ s3_access_key_id }}:{{ s3_secret_access_key }}

~~~


#### The hosts file
Don’t forget to fill in the missing information from Packet, Duckdns, Wasabi. Also reference the Pub and Priv SSH keys. 
~~~yaml
[all:vars]
# Base URL of packet for API access
base_url=https://api.packet.net
# Project ID, that is you might get from the Web GUI / Project Settings tab.
project_id=
# Auth Token for the Packet access, create an API Key for your account. 
auth_token=
# The Public Key of your SSH key, that will be used to manage the device.
my_ssh_key_file=
# Domain of your duckdns.org DDNS service (Free)
duckdns_subdomain=
# Token of your duckdns.org Service
duckdns_token=
ansible_host_key_checking=False
# Parameters of the S3 storage access, that will be mapped to /opt. I'm using wasabisys
s3fs_fuse_cache_folder=/tmp
s3fs_fuse_url=https://s3.eu-central-1.wasabisys.com
s3fs_fuse_bucket=
s3fs_fuse_mount_point=/opt
s3_access_key_id=
s3_secret_access_key=

# Parameters of the "created" device.
[packetdevices]
[packetdevices:vars]
ansible_ssh_private_key_file=
ansible_ssh_user=root
ansible_host_key_checking=False

~~~

#### Playbook to provision the server
~~~yaml
# TASK 1. Deploy the Packet.NET device
- name: create packet.net device
  hosts: localhost
  tasks:
  # Add our Pub Key to Packet, so no password required to reach the device using SSH. 
  - packet_sshkey:
      key_file: "{{my_ssh_key_file}}"
      auth_token: "{{auth_token}}"
      label: tutorial key
  # Provision a device based on the parameters below
  - packet_device:
      project_id: "{{ project_id }}"
      auth_token: "{{auth_token}}"
      hostnames: gns3vm
      operating_system: ubuntu_18_04
      plan: x1.small.x86
      # https://www.packet.com/cloud/servers/
      # For GNS3 we want to use c1.small.x86 or x1.small.x86
      facility: fra2
      state: active
      wait_timeout: 600
    register: newhosts
  # print details of the server
  - debug:
      msg: "System {{ newhosts.items() }}"
  # Wait until the device reachable via SSH. 
  - name: wait for ssh
    wait_for:
      delay: 1
      host: "{{ item.public_ipv4 }}"
      port: 22
      state: started
      timeout: 500
    loop: "{{ newhosts.devices }}"
  # Add the node to a device group (Dynamic Ansible inventory) - will be referenced later.
  - name: add host to multiple groups
    add_host:
      hostname: "{{ newhosts.devices[0].public_ipv4}}"
      groups:
        - packetdevices
  # Save details of the node(s), so we can use it in other playbook aswell. 
  - name: Save Device data
    local_action: copy content="{{ newhosts }}" dest=".newhost"
  # Add SSH fingerprint to known_hosts so ansible will not prompt for y/n, error. 
  - name: tell the host about our servers it might want to ssh to
    shell: ssh-keyscan -H "{{ newhosts.devices[0].public_ipv4}}" >> ~/.ssh/known_hosts
  # Get my public address using ipinfo.io
  - name: get my public_ipv4 address, I will allow this in the firewall:)
    ipinfoio_facts:
      timeout: 5
    register: ipdata
  - debug:
      msg: "My IP information {{ ipdata.items() }}"
# TASK 2. Deploy the Server
- name: Server prep
  hosts: packetdevices
  tasks:
  # Execute apt update
  - name: Update and upgrade apt packages
    become: true
    apt:
      update_cache: yes
  # apt install s3fs
  - name: Install S3FS and UFW APT package
    apt:
      name: s3fs,ufw
  # Download the latest GNS3-server install script
  - name: Download GNS3 installer
    get_url: url=https://raw.githubusercontent.com/GNS3/gns3-server/master/scripts/remote-install.sh dest=/tmp/remote-install.sh mode=0755
  # Execute the installation script. Special tag "Never" might be used to skipp task from the playbook. Good for debug.
  - name: Execute the GNS3 installer
    shell: bash /tmp/remote-install.sh --with-iou --with-i386-repository
    tags: [ 'never', 'debug' ]
  # Register the IPv4 address of the node to our DDNS. 
  - name: Add Dynamic DNS entry
    shell: echo url="https://www.duckdns.org/update?domains="{{duckdns_subdomain}}"&token="{{duckdns_token}}"&ip=" | curl -k -o /tmp/duck.log -K -
  # Generate the password file based on our template, and save it on the server. This will be used in the next task.
  - template: src=passwd-s3fs.j2 dest=/root/.passwd-s3fs mode=0600
  # Save the mount list in the mount_list variable. 
  - name: See if {{ s3fs_fuse_mount_point }} Is Already Mounted
    command: mount
    register: mount_list
  # Mount the S3FS storage. NOTE: UID/GID is the GNS3 user. We will run this in case it is not already mounted. 
  - name: mount folder {{ s3fs_fuse_mount_point }} to s3 bucket {{ s3fs_fuse_bucket }} using s3fs
    command: >
      s3fs
      -o use_cache={{ s3fs_fuse_cache_folder }}
      -o url={{ s3fs_fuse_url }}
      -o passwd_file="/root/.passwd-s3fs"
      -o noatime
      -o allow_other
      -o uid=1000
      -o gid=1000
      -o nonempty
      {{ s3fs_fuse_bucket }} {{ s3fs_fuse_mount_point }}
    register: command_result
  # make sure this task is idempotent: ignore the 'already mounted' error thrown by s3fs.
  # https://groups.google.com/forum/#!topic/ansible-project/cIaQTmY3ZLE
    failed_when: >
      'according to mtab, s3fs is already mounted' not in command_result.stderr and command_result.rc == 1
    when: s3fs_fuse_mount_point not in mount_list.stdout
  # restart GNS3 service.
  - name: Restart GNS3 Service
    service:
      name: gns3
      state: restarted
  # Configure some basic security :-)
  - name: UFW security ruleset - Allow SSH
    ufw:
      rule: allow
      name: OpenSSH
  - name: UFW security ruleset - Allow everything from my IP
    ufw:
      rule: allow
      src: "{{ hostvars.localhost.ipdata.ansible_facts.ip }}"
  - name: Deny everything else and enable UFW
    ufw:
      state: enabled
      direction: incoming
      policy: deny



~~~

#### Playbook to decommission the server
~~~yaml
# TASK 1. - Load vars into the Python dictionary "newhosts"
- hosts: localhost
  vars:
    newhosts: "{{ lookup('file', '.newhost') | from_json }}"
  tasks:
  # Load variables from the file .newhost
  - debug:
      msg: "Loaded variables: {{ newhosts.items() }}"
  # Add the host to host group that will be used as reference in the playbook. 
  - name: add host to multiple groups
    add_host:
      hostname: "{{ newhosts.devices[0].public_ipv4}}"
      groups:
        - packetdevices
# TASK 2. - Prepare the node(s) for decommission. 
- name: Server prep
  hosts: packetdevices
  tasks:
  # Stop GNS3 service on the Node
  - name: Stop service gns3, if started
    service:
      name: gns3
      state: stopped
  # Unmount the S3 storage
  - name: Mount down /opt
    mount:
      path: /opt
      fstype: fuse.s3fs
      state: unmounted
# TASK 3. - Destroy the machine using Packet API
- name: Destroy Device by uuid
  hosts: localhost
  vars:
    newhosts: "{{ lookup('file', '.newhost') | from_json }}"
  tasks:
  # Use the project_id to "absent" the devices by ID. Device ID was loaded in Task 1. into the newhost dict.
  - packet_device:
      project_id: "{{ project_id }}"
      auth_token: "{{ auth_token }}"
      state: absent
      device_ids:
        - "{{ newhosts.devices[0].id }}"
    register: results
    retries: 3  
    delay: 5
  - debug:
      msg: "Status: {{ results }}"

~~~

To start the playbook navigate to the directory and issue:
  ansible-playbook -i hosts packet-playbook.yml

