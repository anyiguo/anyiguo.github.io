---
layout: post
title: "Web Scraping with Scrapy, Pymongo and MongoDB"
modified:
category: Python
excerpt: How to crawl the latest questions of StackOverflow and put data into a MongoDB database
tags: [Programming, Pymongo, MongoDB, Scrapy, Web Scraping, StackOverflow]
image:
  feature:
date: 2015-07-13T21:34:12-05:00
---

I recently finished a [M101P:MongoDB for Developer](https://university.mongodb.com/courses/M101P/about) course (which is really good) and it is my first introduction to the world of non-relational databases. I thought it would be a good opportunity to connect my Scrapy skill with MongoDB and learn more about database pipelines at the same time, so I wrote a spider that crawls the latest questions on StackOverflow and puts the data into a MongoDB database. Here's the code [repo on Github](https://github.com/yanniey/Scrapy_MongoDB_StackOverFlow.git). 


## Part 1: Spider is named `stack`, see `stack_spider.py`

Use [Scrapy](http://scrapy.org/) to crawl the latest submitted questions on [StackOverflow](https://stackoverflow.com/), output data to JSON file, validate the data entires, then import to [MongoDB](https://www.mongodb.org/) database.

## Part 2: Spider is named `stack_crawler`, see`stack_crawler.py`

Use Scrapy's [Crawlspider](http://doc.scrapy.org/en/latest/topics/spiders.html#crawlspider) to extend the scraper so that it crawls through the pagination links at the bottom of each page and scrapes the questions (question title and URL) from each page. (Translate: let's scrape > 50 questions at a time!)


![Output data in MongoDB](https://github.com/yanniey/Scrapy_MongoDB_StackOverFlow/blob/master/Output%20data%20in%20MongoDB.png?raw=true)


![Output data in JSON file](https://github.com/yanniey/Scrapy_MongoDB_StackOverFlow/blob/master/Output%20data%20in%20JSON%20file.png?raw=true)

Note:
Replace deprecated `pymongo.Connection()` with `MongoClient.Connection()` according to PyMongo 3.0's update

Output `JSON` files in `Scrapy`:

```
scrapy crawl stack -o items.json -t json
```

```
scrapy crawl stack_crawler -o items3.json -t json
```