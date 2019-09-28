---
title: Workflow for creating new Blog post
layout: post
categories: blogging
---
I have migrated from Wordpress to Github, as it supports more easily the Development like blogging. This way, I can treat Blog-as-code. Usually I capture the ideas in [Ulysses](https://ulysses.app), that is a Markdown writer application. So let's see what is required:

### Solution elements:
* Cloudflare (free tier): To host the domain, also provide caching and protection. 
* GitHub Pages: To host the blog as source code, and render HTML content using the [JekyII](https://jekyllrb.com) Blog engine. This is happening behind the scene on Github’s servers. 
* GitHub Desktop Client: To easily push changes to Blog source code (markdown and Jinja2 like syntax for templating HTML). 
* SublimeTextEditor: This is my favorite Editor, and it also supports JekyII package. With a simple shortcut I can add post or page. 

### The workflow:
1. Let us create the content in Ulysses. This is part of the SetApps subscription for Mac, so no need to buy the software. 
2. In SublimeTexEdtitor3 create a new JekyII post. Here I need to assign the category the post part of. This new text file be saved in the _posts directory. _
![](/images/sublimetext_jekyII.png)
3. From Ulysses copy the text as Markdown code, and paste it to the post.
4. If Picture was embedded, we need to put it into the images folder. 
5. Save the file. Go to GitHub Desktop client, and add new files, commit changes, push them too remote. 
6. Verify everything is OK on the main site. GitHub usually requires a 10-20seconds until renders the HTML code.

This way it is more fun to write. In Ulysses I can create the content, and publishing it also easy. Cloudflare and GitHub Pages are free and very fast services. The main advantage is that code highlighting is lot more effective here, this was the main motivation for the change. 