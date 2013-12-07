---
layout: default
title: User Guide
---

# User Guide

Using the jeo library in an application.

## Maven

Applications using the [Maven](http://maven.apache.org/) build system must first
add the OpenGeo maven repository the project `pom.xml`.

{% highlight xml %}
  <repositories>
    <repository>
      <id>opengeo</id>
      <name>OpenGeo Maven Repository</name>
      <url>http://repo.opengeo.org/</url>
    </repository>
  </repositories>
{% endhighlight %}

And then declare the dependency on jeo.

{% highlight xml %}
    <dependency>
      <groupId>org.jeo</groupId>
      <artifactId>jeo</artifactId>
      <version>{{site.version}}</version>
    </dependency>
{% endhighlight %}

## Gradle

Applications using the [Gradle](http://www.gradle.org) build system must add 
the Central and OpenGeo maven repositories to `build.gradle`.

{% highlight groovy %}
repositories {
  mavenCentral()
  maven {
    url 'http://repo.opengeo.org'
  }  
}
{% endhighlight %}

And then declare the dependency no jeo.

{% highlight groovy %}
dependencies {
  compile group: 'org.jeo', name: 'jeo', version: '{{site.version}}'  
}
{% endhighlight %}

## SBT

Applications using the [sbt](http://www.scala-sbt.org) build system must add 
the OpenGeo maven repository to `build.sbt`.

{% highlight scala %}
resolvers += "opengeo" at "http://repo.opengeo.org"
{% endhighlight  %}

And then declare the library dependency no jeo.

{% highlight scala %}
libraryDependencies += "org.jeo" % "jeo" % "{{site.version}}"
{% endhighlight  %}

## Next Steps

Continue on to the <a href="/library">library overview</a> to get started with 
using the apis provided by the jeo library.
