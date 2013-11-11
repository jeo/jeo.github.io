---
layout: default
title: Frequently Asked Questions
name: faq
---

# Frequently Asked Questions

#### Why jeo?

The jeo library comes from the desire to have a simple geo library in the Java 
world. Specifically a library that is carefully designed to be lightweight enough 
to use in environments other than the classic desktop and server. 

####  Does jeo work on Android?

Yes. The library was designed to be used in "sand boxed" environments where not
all functions of the standard JRE are available. 

####  Should I use jeo instead of other Java libraries like GeoTools?

It depends on your needs. The jeo philosophy is that the library should make it
simple to do simple things. GeoTools is an extremely powerful library that 
provides many advanced features. Unfortunately this comes at the cost of 
complexity. Long story short jeo is not a replacement for GeoTools, it is an 
alternative for developers that have simpler requirements.

####  How is jeo licensed?

Jeo is licensed under the [Apache Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html) license. The core of library has 
the following runtime dependencies. 

* [JTS](http://tsusiatsoftware.net/jts/main.html) - LGPL
* [PROJ4J](http://trac.osgeo.org/proj4j) - Apache Version 2.0
* [SLF4J](http://www.slf4j.org) - MIT

