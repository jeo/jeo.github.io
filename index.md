---
layout: default
title: jeo
name: index
---

<!-- # <span class="logo">jeo</span> -->

## A lightweight spatial library for the JVM

<p class="lead">
  jeo is a spatial library written in Java that provides low level geospatial capabilities
</p>


{% highlight java %}

// create a point
Point p = Geom.point(-115.37, 51.08);

// reproject it
p = Proj.reproject(p, "epsg:4326", "epsg:900913");

// turn it into GeoJSON
String json = Geom.json(p);

// or turn it into a feature 
Schema schema = Schema.build("towns").field("location", Point.class).schema();

// create a postgis dataset
Workspace pg = PostGIS.open(new PostGISOpts("jeo"));
Cursor<Feature> c = pg.create(schema).cursor(new Query().append());

// add the point
Feature town = c.next();
town.put(p);

// write and close
c.write().close();
pg.close();

{% endhighlight %}

<span class="lead">
jeo is open source and licensed under the [Apache Version 2.0](http://www.apache.org/licenses/LICENSE-2.0.html) license.
</span>

