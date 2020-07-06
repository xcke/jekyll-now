---
title: GIT Tips
layout: post
categories: netdev
---

Check out some of these useful GIT tips: 

## remove all the branches that has been merged

`git branch --merged | egrep -v "(^\*|master|dev)" | xargs git branch -d`

[source](https://stackoverflow.com/questions/6127328/how-can-i-delete-all-git-branches-which-have-been-merged)

## Git ignore SSL certificate

To disable TLS/SSL verification for a single git command
try passing -c to git with the proper config variable, or use Flow's answer:

`git -c http.sslVerify=false clone https://example.com/path/to/git`  
To disable SSL verification for a specific repository  
If the repository is completely under your control, you can try:  

`git config --global http.sslVerify false`  
There are quite a few SSL configuration options in git. From the man page of git config:  

```
http.sslVerify
    Whether to verify the SSL certificate when fetching or pushing over HTTPS.
    Can be overridden by the GIT_SSL_NO_VERIFY environment variable.

http.sslCAInfo
    File containing the certificates to verify the peer with when fetching or pushing
    over HTTPS. Can be overridden by the GIT_SSL_CAINFO environment variable.

http.sslCAPath
    Path containing files with the CA certificates to verify the peer with when
    fetching or pushing over HTTPS.
    Can be overridden by the GIT_SSL_CAPATH environment variable.
A few other useful SSL configuration options:

http.sslCert
    File containing the SSL certificate when fetching or pushing over HTTPS.
    Can be overridden by the GIT_SSL_CERT environment variable.

http.sslKey
    File containing the SSL private key when fetching or pushing over HTTPS.
    Can be overridden by the GIT_SSL_KEY environment variable.

http.sslCertPasswordProtected
    Enable git's password prompt for the SSL certificate. Otherwise OpenSSL will
    prompt the user, possibly many times, if the certificate or private key is encrypted.
    Can be overridden by the GIT_SSL_CERT_PASSWORD_PROTECTED environment variable.
```

## Git ignore update
```
git rm -r --cached .
git add .
git commit -m ".gitignore is now working"
```