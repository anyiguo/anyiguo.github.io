---
layout: post
title: "Python Version Error for MongoDB M101"
modified:
category: Python
excerpt: How to handle error message "print 'usage validate.py -m mongoConnectString' "
tags: [Sublime, Python, MongoDB]
image:
  feature:
date: 2015-07-03T01:30:24-05:00
---

 I had some problems with assignment 6.5 which required running a 3-node replica set on my local end. The `validate.py` file had an error message of:

 ```
 Traceback (most recent call last):
  File "validate.py", line 3, in <module>
    eval(compile(base64.b64decode(code), "<string>", 'exec'))
  File "<string>", line 36
    print "usage validate.py -m mongoConnectString"
                                                  ^

```
  Eventually I figured out that it's because my default Python version was Python3,but this course required Python2, so I was able to solve my problem through an easy `python2 validate.py`.

  By the way, I think [M101: MongoDB for Developers](https://university.mongodb.com/courses/M101P/about) is a great introductory course for non-relational databases. I learned about indexing, data aggregation, sharding and many best practices for building apps with a DBA's mindset. They also have a Udacity course called [Data wrangling with MongoDB](https://www.udacity.com/course/data-wrangling-with-mongodb--ud032) for people who're interested in furthering their MongoDB skill. 

  And here's my [Github repo](https://github.com/yanniey/MongoDB_For_Developers_M101P-05-2015) for the course notes and assignments.  
