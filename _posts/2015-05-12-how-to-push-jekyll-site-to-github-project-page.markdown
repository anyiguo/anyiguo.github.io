---
layout: post
title: "How to Push Jekyll Site to Github Project Page"
modified:
category: Programming
excerpt: "Solve the problem of CSS/JS file not showing up in Github Project Page"
tags: [Git, Github, Jekyll]
image:
  feature:
date: 2015-05-12T22:59:10-05:00
---



## Problem


I made my Jekyll blog site today. After checking that everything looks good on my `localhost`, I decided to push to `Github` and create a `Github Project Page` by:

```
git push --set-upstream origin gh-pages
```
But the github page did not show the images, CSS or JS files. It only showed the contents of the html page:

![screenshot:failure to load css files](https://github.com/yanniey/yanniey.github.io/blob/master/images/jekyll_error_screenshot.png?raw=true)


## Solution

After reading the [Jekyll doc](http://jekyllrb.com/docs/github-pages/), I realized that I needed to reconfigure the urls according to the name of the github repo that I created for this site. 

For example, I created a `test` repo and its address is `yanniey.github.io/test`

1. Then I added `baseurl: /test` to the `_config.yml` file 

2. Then I found the `_layouts/pages.html` and `_layouts/posts.html` files  and changed anything that looks like 

	```{{ site.url }}/images/{{ page.image.feature }}```

	to 
 
	```{{ site.baseurl }}/images/{{ page.image.feature }}``` <-- change `url` to `baseurl`

3. Basically you repeat this process (i.e. adding **"base"** to **"{{site.url}}"**) until everything shows up. 

4. Run `jekyll serve --baseurl` to test and see if your new configuration works. 

![Successfully loaded screenshot](https://github.com/yanniey/yanniey.github.io/blob/master/images/jekyll_error_success.png?raw=true)

