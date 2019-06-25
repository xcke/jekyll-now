---
title: Jinja2 dynamic variables
layout: post
---
Just a nice trick. Let’s assume we are using a CSV file and also a YAML file to feed input data to python `template.render()` function. 

In the CSV file, one row is a single Node we would like to generate configuration for. However, some type of variables is easier to maintain in a YAML file. Think about how nice hierarchy we can create. 

To connect these two together on the template level we can do something like this:
Our input YAML file (globals)
```yaml
nodes:
	TESTNODE:
		hostname: cisco
```
Our input CSV file (node\_csv):
```csv
INDEX,NODE_NAME,NODE_ID,SITE_ID
10,TESTNODE,7,163
```
Python template render function: 
```python
template.render(node_csv=node_csv,globals=globals)
```

In Jinja2 we can use the following :

```jinja2
{% set node_name = node_csv.NODE_NAME %}

hostname {{ globals.nodes.get(node_name).hostname }}
```

The point here is to use the _get_ function to reference a Jinja2 variable inside a variable. 