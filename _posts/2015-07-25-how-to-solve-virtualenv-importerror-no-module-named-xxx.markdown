---
layout: post
title: "How to Solve: Virtualenv importError No Module Named XXX"
modified:
category: Programming
excerpt: How to solve ImportError no module named XXX under virtualenv
tags: [Python, Virtualenv, Virtualenvwrapper, Flask]
image:
  feature:
date: 2015-07-25T13:45:09-05:00
---

I was working on an open source project called [Census Reporter](https://github.com/censusreporter/censusreporter/) today on Github and the setup called for `virtualenv` and `django` on my laptop. I followed the instruction and `pip install -r requirements.txt`, but when I tried to run `python manage.py runserver`, an `ImportError` message poped out:


{% highlight bash %}
ImportError: No module named django.core.management?
{% endhighlight%}

  Here's how I solved this:

1. First, check to verify that `django` is installed in the virtualenv by running `pip freeze`. It should show something like this:

{% highlight bash %}
(census)Anyis-MacBook-Pro:censusreporter Anyi$ pip freeze
You are using pip version 6.1.1, however version 7.1.0 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
boto==2.27.0
Django==1.5.4   <--- This shows that Django is installed
ecdsa==0.10
newrelic==2.16.0.12
numpy==1.7.0
paramiko==1.12.0
pycrypto==2.6.1
python-memcached==1.53
requests==1.2.0
unicodecsv==0.9.4

{% endhighlight %}


2. If `Django` is installed, check to see if your `virtualenv` is created with the right version of `Python`. 

I have both `Python2` and `Python3` on my mac, and `Python3` is my default Python interpreter. However, `Census Reporter` is built in `Python2`, so I have to create a new `virtualenv` and specify that I want it in `Python2`.

{% highlight bash%}
deactivate
mkvirtualenv -p /usr/bin/python2.7 --no-site-packages census-new
{% endhighlight %}

If you don't specify `-p`, then the `virtualenv` will be created with the default Python which is `Python3` in this case. 

3. Finally, declare `Python2.7` when running the server:

{% highlight bash %}

python2.7 manage.py runserver
Validating models...
0 errors found
July 25, 2015 - 19:14:49
Django version 1.5.4, using settings 'config.dev.settings'
Development server is running at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
{% endhighlight%}
