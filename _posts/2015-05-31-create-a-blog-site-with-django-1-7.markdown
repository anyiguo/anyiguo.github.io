---
layout: post
title: "Create a Blog with Django 1.7"
modified:
category: Python
excerpt: Create a blog and RSS feed with Django 1.7 and Django_markdown
tags: [Python, Django, Github]
image:
  feature:
date: 2015-05-31T23:08:21-05:00
---

Here's a Django blog app that I wrote over the weekend. This is the first Django app that I managed to run successfully, and I'm very happy about it finally working. I feel that I'm finally starting to get a handle of Django and the use of frameworks in generating dynamic sites, and I think that my experience with Rails, albeit shallow, was conductive to my understanding of concepts such as MVC(Model-View-Controller) and database migrations.

Tutorial: [`Building a Blog with Django 1.7 in 16 mins`](https://www.youtube.com/watch?v=7rgph8en0Jc&list=PLW_p8RG8r7sO5bHayIJQ1AK-Xem2QYx48&index=6) 


#### Features:

+ Django_markdown 

+ Blog tags

+ RSS feed

+ Testing in Django



The origin tutorial by is very helpful, but some key steps for setting up `Django_markdow` were missing and I needed to reconfigure my local settings, so I thought that I should write down my solutions in case someone else runs into the same problem.

Screenshot:

![Django blog site screenshot](https://github.com/yanniey/Django_blog/blob/master/Blog_screenshot.png?raw=true)


___


#### Use of `Django_markdown`

Django_markdown allows you to write entries in Markdown format(instead of the default Django `Charfield` or `Textfield`)

![Django_markdown](https://github.com/yanniey/Django_blog/blob/master/django%20markdown%20blog.png?raw=true) 


___



#### Install Django_markdown app to create a blog

1. models.py

	```
	from django_markdown.models import MarkdownField
	...
	body = MarkdownField()  <--- change this line
```

2. Settings.py

	```
	# Static files (CSS, JavaScript, Images)
	# https://docs.djangoproject.com/en/1.7/howto/static-files/

	STATIC_URL = '/static/'

	STATIC_ROOT = os.path.join(BASE_DIR, "static")

	STATICFILES_DIRS = (

	)

	# Markdown
	MARKDOWN_EDITOR_SKIN = 'simple'

	```

3. urls.py

	```
	from django.conf.urls import patterns, include, url
	from django.contrib import admin

	urlpatterns = patterns('',
		...
	    url(r'^markdown/', include("django_markdown.urls")),  <--- add this line
	)
	```

4. admin.py

	```
	from django.contrib import admin
	from . import models
	from django_markdown.admin import MarkdownModelAdmin  <--- add this line

	# Register your models here.
	class EntryAdmin(MarkdownModelAdmin):      <--- change this line
		list_display = ("title","created")
		# slug will be automatically populated using JavaScript
		prepopulated_fields = {"slug":("title",)}

	admin.site.register(models.Entry,EntryAdmin)

	```

5. In shell (terminal)

	```
	manage.py collectstatic
	```
	
6. Remember to copy the `static` folder on [this page](https://github.com/yanniey/Django_blog/tree/master/static/admin) to your root folder so that your CSS and JavaScript files can be properly rendered. Alternatively, you can try adjust the `STATICFILES_DIR` in `settings.py` to achieve the same result.