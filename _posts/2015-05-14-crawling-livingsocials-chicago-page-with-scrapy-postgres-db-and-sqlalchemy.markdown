---
layout: post
title: "Crawling LivingSocial's Chicago Page with Scrapy, Postgres DB and SQLAlchemy"
modified:
category: Python
excerpt: "How to crawl data with Scrapy, build a pipeline with SQLAlchemy and store crawled data into PSQL."
tags: [Python, Scrapy, ORM, Postgres, Data_crawling]
image:
  feature:
date: 2015-05-14T22:31:50-05:00
---

Last month I wrote a [Scrapy app](https://github.com/yanniey/Scrapy_craigslist_apartment) for crawling data on Craigslist's Chicago apartment page. I got an introduction to Xpath and DOM node selection through the project, and I was able to export the parsed data into a JSON file at the time.

Now that I've done some data crawling with Apache Nutch, I've got **a lot** better at Xpath. So I thought I should do a Scrapy 2.0 project on crawling data and storing it into a database. I followed a great tutorial on [NewCoder.io](http://newcoder.io/) and managed to create my very first Postgres database 

I wonder why MAMP and Postgres both use elephant as their logo. 

Here's a brief of what I did:

1. Config Scrappy spider to crawl and parse HTML
2. Set up Postgre database to store the parsed data
3. Create a pipeline to connect parsed data and database with [SQLAlchemy](http://www.sqlalchemy.org/) ORM


### Result:

PSQL db: 

![postgres sql screenshot](https://github.com/yanniey/Scrapy_livingsocial_chicago/blob/master/PSQL%20screenshot.png?raw=true)

Json output:

![Json output](https://github.com/yanniey/Scrapy_livingsocial_chicago/blob/master/output.png?raw=true)
