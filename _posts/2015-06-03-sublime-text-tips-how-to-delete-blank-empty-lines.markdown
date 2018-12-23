---
layout: post
title: "Sublime Text Tips: How to delete blank/empty lines"
modified:
category: Programming
excerpt: How to delete blank/empty lines in Sublime Text 2 and 3
tags: [Sublime, Shell]
image:
  feature:
date: 2015-06-03T22:33:24-05:00
---

To change from: 

```
This

has

empty

lines.
```
to:

```
This
has
empty
lines.
```

Steps:

1. Select text 

2. 
	+ PC: Press `Ctrl` + `H`
	+ Mac: `Command` + `Alt` + `F`
	+ Or choose Find -> Replace in menu

3. Make sure that regular expression is on by checking the `.*` in the `Find` box
	1. Find: `^\n`
	2. Replace with:(thing, leave it blank). 
	3. `Replace All`



