---
layout: post
title: Package control installation behind proxy
date: 2019-06-12
author: xcke
categories: code

---
Sublime3 text editor is one of the best Editor I have seen, and using it for a while. It is beautiful and also easy to extend. There are a number of packages I use, so the first step after an installation is to setup package-manager. 
If you are behind a proxy, the following snipet can help. •

```python
import urllib.request,os,hashlib; h = '6f4c264a24d933ce70df5dedcf1dcaee' + 'ebe013ee18cced0ef93d5f746d80ef60'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler({"http":"http://[proxy_username]:[proxy_password]@[proxy_IP_or_host]:[proxy_port]"})) ); by = urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by);

```

Don’t forget to check the actual hash, and update in the “h” variable and also the proxy settings:)
[https://packagecontrol.io/installation](https://packagecontrol.io/installation)

What packages I’m using? 

```json
{
	"bootstrapped": true,
	"in_process_packages":
	[
	],
	"installed_packages":
	[
		"Ansible",
		"Dockerfile Syntax Highlighting",
		"EasyDiff",
		"FileDiffs",
		"Generate Password",
		"GenerateUUID",
		"Jinja2",
		"JSONLint",
		"JsonTree",
		"Network Tech",
		"Package Control",
		"Pretty JSON",
		"Scratchpad",
		"SFTP",
		"TodoReview"
	]
}
```
