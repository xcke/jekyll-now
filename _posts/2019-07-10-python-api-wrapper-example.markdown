---
title: Python API wrapper example
layout: post
categories: netdev
---

Python API wrapper example

It is easy to create a python wrapper for a REST API. I have seen a good example from the [Wekan](https://wekan.github.io "Wekan") project, and used this as an example to interact with GNS3-Server. 
I have created a new module for my application, that is usually a new folder in the main project directory. This folder will also include two files:

```python
- __init__.py : Will include the main API Class
- methods.py: Will include data classes related to the API endpoints
```

A good example for the init.py: 

```python
class GNS3ServerApi:
    def api_call(self, url, data=None):
        if data is None:
            api_response = self.session.get(
                "{}{}".format(self.api_url, url),
                verify=False
            )
        else:
            api_response = self.session.post(
                "{}{}".format(self.api_url, url),
                data=data,
                verify=False
            )
        return api_response.json()

    def __init__(self, api_url):
        self.session = requests.Session()
        self.api_url = api_url

    def get_server_version(self):
        server_data = self.api_call("/v2/version")
        return Server(self, server_data)

    def get_projects(self):
        projects_data = self.api_call("/v2/projects")
        return [Project(self, project_data) for project_data in projects_data]

    def get_project_data(self, project_id):
        project_data = self.api_call("/v2/projects/{}".format(project_id))
        return Project(self, project_data)

    def get_project_nodes(self, project_id):
        nodes_data = self.api_call("/v2/projects/{}/nodes".format(project_id))
        return [Node(self, node_data) for node_data in nodes_data]

    def get_project_links(self, project_id):
        links_data = self.api_call("/v2/projects/{}/links".format(project_id))
        return [Link(self, link_data) for link_data in links_data]
```

I think the interesting part here is how the “return” construct reflects the structure of the API. 
And the Classes related to the API Wrapper class: 
```python
import json

class Server:
    def __init__(self, api, server_data):
        self.api = api
        self.data = server_data
        self.version = server_data["version"]

    def __repr__(self):
        return json.dumps(self.data, indent=4)

class Project:
    projects = dict()
    def __init__(self, api, project_data):
        self.api = api
        self.data = project_data
        self.name = project_data["name"]
        self.project_id = project_data["project_id"]
        Project.projects[self.project_id] = self

    def __repr__(self):
        return json.dumps(self.data, indent=4)

class Port:
    def __init__(self, node, port_data):
        self.node = node
        self.data = port_data
        self.name = port_data["name"]
        self.adapter_number = port_data["adapter_number"]
        self.port_number = port_data["port_number"]

    def __repr__(self):
        return json.dumps(self.data, indent=4)

class Node:
    nodes = dict()
    def __init__(self, api, node_data):
        self.api = api
        self.data = node_data
        self.name = node_data["name"]
        self.node_id = node_data['node_id']
        self.console = node_data['console']
        self.node_label = node_data['label']
        self.x = node_data['x']
        self.y = node_data['y']
        self.ports = [ Port(self,port_data) for port_data in node_data['ports']]
        Node.nodes[self.node_id] = self
        self.flavor = node_data['console']

    def __repr__(self):
        return json.dumps(self.name, indent=4)

class Link:
    links = dict()
    def __init__(self, api, link_data):
        self.api = api
        self.data = link_data
        self.link_id = link_data["link_id"]
        self.nodes = link_data["nodes"]
        self.device_a = link_data["nodes"][0]["node_id"]
        self.device_port_a = link_data["nodes"][0]["label"]["text"]
        self.device_port_a_x = link_data["nodes"][0]["label"]["x"]
        self.device_port_a_y = link_data["nodes"][0]["label"]["y"]
        self.device_b = link_data["nodes"][1]["node_id"]
        self.device_port_b = link_data["nodes"][1]["label"]["text"]
        Link.links[self.link_id] = self

    def __repr__(self):
        return json.dumps(self.data, indent=4)
```

It is very easy to extend this with new endpoints one would like to use in the application itself. Just create the sub-function under the GNS3ServerApi Class, and also the related data Class in the methods.py. 