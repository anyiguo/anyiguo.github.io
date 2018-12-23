---
layout: post
title: "Hadoop.log Error Message Major-minor"
modified:
category: Programming
excerpt: "A quick fix for solving 'major.minor version 51.0' errorin Hadoop.log for Java development"
tags: [Java, Hadoop, Nutch]
image:
  feature:
date: 2015-05-12T14:29:09-05:00
---

# Error message in hadoop.log: major.minor version 51.0

I ran into this error message the other day while doing data crawling with `Apache Nutch`:

```
java.lang.Exception: java.lang.UnsupportedClassVersionError: nl/wizenoze/nutch/crawl/ArticleSignature : Unsupported major.minor version 51.0
```

means that Maven(which runs nutch) and Java are using 2 different versions of java

+ Check Java version

```
java -version

```

you will see something like this: using **java 1.6.0_65** here

```
java version "1.6.0_65"
Java(TM) SE Runtime Environment (build 1.6.0_65-b14-466.1-11M4716)
Java HotSpot(TM) 64-Bit Server VM (build 20.65-b04-466.1, mixed mode)
```


+ Check mvn version

```
mvn --version
```

see something like this: using **java 1.8.0_45** here. 

```
Apache Maven 3.2.5 (12a6b3acb947671f09b81f49094c53f426d8cea1; 2014-12-14T11:29:23-06:00)
Maven home: /usr/local/Cellar/maven/3.2.5/libexec
Java version: 1.8.0_45, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_45.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.10", arch: "x86_64", family: "mac"
```

>So one is using **java 1.6.0_65**, the other is using **java 1.8.0_45**

+ Solution: 

1. change the java path

	```
	export JAVA_HOME="`/usr/libexec/java_home -v '1.8*'`"
	```


2.  edit bash_profile

	[load this function into bash_profile](http://www.jayway.com/2014/01/15/how-to-switch-jdk-version-on-mac-os-x-maverick/)


	```
	function setjdk() {
			if [ $# -ne 0 ]; then

			removeFromPath '/System/Library/Frameworks/JavaVM.framework/Home/bin'

			if [ -n "${JAVA_HOME+x}" ]; then

			removeFromPath $JAVA_HOME

			fi

			export JAVA_HOME= `/usr/libexec/java_home -v $@`

			export PATH=$JAVA_HOME/bin:$PATH

			fi

			}

			function removeFromPath() {

			export PATH=$(echo $PATH | sed -E -e "s;:$1;;" -e "s;$1:?;;")

			}

		setjdk 1.8

	```


3. load the change in bash_profile immediately(so that you don't have to restart the terminal)

	```
	source ~/.bash_profile
	``` 


	[here's a page for editing path environment variables on mac](http://hathaway.cc/post/69201163472/how-to-edit-your-path-environment-variables-on-mac)



Result:

```
java --version
``` 
should output:

```
java version "1.8.0_45"
Java(TM) SE Runtime Environment (build 1.8.0_45-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.45-b02, mixed mode)
```

&

``` 
mvn --version 
```
should output

```
Apache Maven 3.2.5 (12a6b3acb947671f09b81f49094c53f426d8cea1; 2014-12-14T11:29:23-06:00)
Maven home: /usr/local/Cellar/maven/3.2.5/libexec
Java version: 1.8.0_45, vendor: Oracle Corporation
Java home: /Library/Java/JavaVirtualMachines/jdk1.8.0_45.jdk/Contents/Home/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "mac os x", version: "10.10", arch: "x86_64", family: "mac"
```

## The Java version that maven and Java use should match.

