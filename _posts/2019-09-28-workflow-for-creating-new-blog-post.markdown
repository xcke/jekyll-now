---
title: Workflow for creating new Blog post
layout: post
categories: blogging
---
I have migrated from Wordpress to Github Pages, as it supports more development style blogging. This way, I can treat content as souce code repository. Usually I capture the ideas in [Ulysses](https://ulysses.app), that is a great markdown writer application. What I really like in Ulysses is simplicity, distraction free writing experience and structure.

### Solution elements:
My workflow requires the following elements: 

* Cloudflare (free tier): To host the domain, also provide caching and protection. 
* GitHub Pages: To host the blog as source code, and render HTML content using the [JekyII](https://jekyllrb.com) Blog engine. This is happening behind the scene on Github’s servers. 
* GitHub Desktop Client: To easily push changes to Blog source code (markdown and Jinja2 like syntax for templating HTML). 
* SublimeTextEditor: This is my favorite Editor, and it also supports JekyII package. With a simple shortcut I can add post or page. 

### The workflow:
And these are the steps I usually go over during blog post development: 

1. Create the content in Ulysses. Make notes. Practice writing. Ulysses is part of SetApps subscription for Mac - which is good news. 

![](/images/ulysses.png)

2. In SublimeTexEdtitor3 create a new JekyII post. Here I need to assign the category the post part of. This new text file be saved in the \_posts directory.

![](/images/sublimetext_jekyII.png)

3. From Ulysses copy the text as Markdown code, and paste it to the post.
4. If Picture was embedded, we need to put it into the images folder. 
5. Save the file. Go to GitHub Desktop client, and add new files, commit changes, push them too remote.
6. Verify everything is OK on the main site. GitHub usually requires a 10-20seconds until renders the HTML code.

This way it is more fun to write. In Ulysses I can create the content, and publishing it also easy. Cloudflare and GitHub Pages are free and very fast services. The main advantage is that code highlighting is lot more effective here, this was the main motivation for the change. 