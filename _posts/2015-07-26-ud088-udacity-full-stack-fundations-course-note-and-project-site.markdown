---
layout: post
title: "UD088 Udacity Full Stack Fundations Course Note"
modified:
category: Python, Database
excerpt: Course note ofr UD088 Udacity Full Stack Fundations
tags: [Python, Flask, MOOC, Programming, Udacity, courseNote]
image:
  feature:
date: 2015-07-26T13:37:27-05:00
---

# UD088 - Udacity's Full Stack Foundations course

Python + SQLAlchemy + MySql+ Flask

## [Course Link](https://www.udacity.com/course/full-stack-foundations--ud088)

## My [Github Repo is here](https://github.com/yanniey/Udacity_Full_Stack_Fundamentals)



Version 1 Before and After my custom CSS:
![Version1](https://github.com/yanniey/Udacity_Full_Stack_Fundamentals/blob/master/Version1.gif?raw=true)

---

Lesson 3 Notes

Flask: 

1. views are under the `templates` folder, call with `render_template(template,variable)`.
2. static files are under the `static` folder 

Flask functions: 
+ `@app.route('/',method=['GET','POST'])`
+ `render_template`
+ `url_for()`
+ `redirect(url_for('restaurantMenu',restaurant_id=restaurant_id))`
+ `get_flashed_messages()`
+ `jsonify` for creating JSON files for API use with `serialize()`

---

Lesson 1 Notes

## Setup

{% highlight bash %}
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from database_setup import Base, Restaurant, MenuItem

engine = create_engine('sqlite:///restaurantMenu.db')
Base.metadata.bind=engine
DBSession = sessionmaker(bind = engine)
session = DBSession()
{% endhighlight %}

OR

{% highlight bash %}
>>> from sqlalchemy import create_engine
>>> from sqlalchemy.orm import sessionmaker
>>> from database_setup import Base,Restaurant, MenuItem
>>> engine = create_engine('sqlite:///restaurantmenu.db')
>>> Base.metadata.bind=engine
>>> DBSession=sessionmaker(bind=engine)
>>> session=DBSession()
>>> AnyiFirstRestaurant = Restaurant(name="Bamboo Palace")
>>> session.add(AnyiFirstRestaurant)
>>> session.commit()
>>> session.query(Restaurant).all()
[<database_setup.Restaurant object at 0xb6b485ac>]
{% endhighlight %}



## add MenuItem

{% highlight bash %}
>>> ramen = MenuItem(name="Ramen",description = "Anyi's favorite comfort food", course="Entree",price="$10.99",restaurant=AnyiFirstRestaurant)
>>> session.add(ramen)
>>> session.commit()
>>> session.query(MenuItem).all()
[<database_setup.MenuItem object at 0xb6b485ec>]
{% endhighlight %}

## First Result

{% highlight bash %}
>>> firstResult = session.query(Restaurant).first()
>>> firstResult
<database_setup.Restaurant object at 0xb6b485ac>
>>> firstResult.name
u'Bamboo Palace'
{% endhighlight %}

## Update an item

{% highlight bash %}
>>>veggieBurgers = session.query(MenuItem).filter_by(name="Veggie Burger")

>>> for veggieBurger in veggieBurgers:
...     print veggieBurger.id
...     print veggieBurger.price
...     print veggieBurger.restaurant.name
...     print "\n"

2
$7.50
Urban Burger


10
$5.99
Urban Burger


21
$9.50
Panda Garden


27
$6.80
Thyme for That Vegetarian Cuisine


37
$7.00
Andala's


43
$9.50
Auntie Ann's Diner'


>>> UrbanVeggieBurger = session.query(MenuItem).filter_by(id=2).one()
>>> print UrbanVeggieBurger.price
$7.50

>>> UrbanVeggieBurger.price = '$2.99'
>>> session.add(UrbanVeggieBurger)
>>> session.commit()
{% endhighlight %}

## Update multiple items

{% highlight bash %}
>>> for veggieBurger in veggieBurgers:
...     if veggieBurger.price !="$2.99":
...             veggieBurger.price = "$2.99"
...             session.add(veggieBurger)
...             session.commit()
{% endhighlight%}

## Delete an item

{% highlight bash %}
>>> spinach = session.query(MenuItem).filter_by(name="Spinach Ice Cream").one()
>>> print spinach.name
Spinach Ice Cream
>>> print spinach.restaurant.name
Auntie Ann's Diner'
>>> session.delete(spinach)
>>> session.commit()
{% endhighlight%}

---

## Syllabus

+ Lesson 1 - Working with the CRUD

In the first lesson, you will learn about CRUD; Create, Read, Update, and Delete. You will learn why this acronym is important in web development and implement CRUD operations on a database. You will also learn to use an ORM (Object-Relational Mapping) as an alternative to SQL.

+ Lesson 2 - Making a Web Server

In the second lesson, you will build a web server from scratch using Python and some of the pre-installed libraries it includes. You will learn what GET and POST requests are and how we use them to retrieve and modify information on a web site. We will then use the concepts learned in Lesson 1 to add CRUD functionality to our website.

+ Lesson 3 - Developing with Frameworks

In the third lesson, we will discuss web frameworks like Django and Ruby on Rails. You will see how web frameworks simplify the development process and allow us to create web applications faster. We will use the Flask web framework to develop our own web application. We will also discuss API's (Application Programming Interfaces) and add JSON (JavaScript Object Notation) endpoints to our application to allow data to be sent in a format alternative to HTML.

+ Lesson 4 - Iterative Development

In the last lesson, you will build an entire web application on your own. You will learn about the iterative development process and how developing iteratively allows you to have a working prototype throughout all stages of the development process.
