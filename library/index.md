---
layout: library
title: Library Overview
---

# Library Overview

An overview of the different parts of the jeo library.

<section id="geom">

## Geometries

<hr/>

Jeo utilizes the [JTS](http://tsusiatsoftware.net/jts/main.html) library for geometry support. 

The `Geom` class adds additional convenience 
methods for working with geometry objects such as a builder for composing complex geometry objects.

{% highlight java %}
// build a polygon with a hole
Geom.build().points(0,0,10,0,10,10,0,10,0,0).ring()
   .points(4,4,6,4,6,6,4,6,4,4).ring().toPolygon();
{% endhighlight %}

{% include api.html class="Geom" package="org.jeo.geom"  description="Geometry utility class" %}

The `Geom.iterate` method makes it easy to iterate over geometry collections.

{% highlight java %}
MultiPoint mp = Geom.build().points(0,0, 1,1, 2,2, 3,3).toMultiPoint();
for (Point p : Geom.iterate(mp)) {
  // do something with p
}
{% endhighlight %}
</section>

<section id="proj">

## Projections

<hr/>

Jeo utilizes the [Proj4J](http://trac.osgeo.org/proj4j) library for projection and coordinate reference 
system support.

The `Proj` class provides convenience methods for 
creating coordinate reference system objects and preforming transformations 
between them.

{% highlight java %}

// canonical geopgraphic
CoordinateReferenceSystem crs1 = Proj.crs("epsg:4326");

// google mercator
CoordinateReferenceSystem crs2 = Proj.crs("+proj=merc", "+a=6378137",
 "+b=6378137", "+lat_ts=0.0", "+lon_0=0.0", "+x_0=0.0", "+y_0=0", "+k=1.0",
 "+units=m", "+nadgrids=@null", "+wktext", "+no_defs");

// re-project
Point p = Geom.point(-115, 51);
p = Proj.reproject(p, crs1, crs2);

{% endhighlight %}

{% include api.html class="Proj" package="org.jeo.proj"  description="Projection utility class" %}

</section>

<section id="data">

## Working with Data

<hr/>

In jeo the `Driver` interface is the abstraction 
for a spatial data format. A driver is responsible for reading data in a 
specific format. This list of supported drivers can be found in the 
<a href="#formats">formats</a> section.

{% include api.html class="Driver" package="org.jeo.data"  description="Data access interface" %}

For instance to read GeoJSON data the `GeoJSON` driver is used. The following
example reads a file named `points.json`. 

{% highlight java %}

GeoJSON drv = new GeoJSON();
drv.open(new File("points.json"), null);

{% endhighlight %}

{% include api.html class="GeoJSON" package="org.jeo.geojson"  description="GeoJSON format driver" %}

{% include info.html title="Driver Options" message="The second argument to the open method is a map of parameters that allow for specifying driver specific options when reading data." %}

The type of object returned from `Driver.open` depends on the driver. In the
above example an instance of the `VectorDataset` interface is returned. This class is the abstraction for vector datasets and  provides a number of data 
access methods.

{% highlight java %}

// read the data
VectorDataset data = new GeoJSON().open(new File("points.json"), null);

// get the spatial bounds
Envelope bbox = data.bounds();

// count the number of features
data.count(new Query());

// iterate over the features
for (Feature f : data.cursor(new Query())) {
  System.out.println(f);
}

// dispose
data.close();

{% endhighlight %}

{% include api.html class="VectorDataset" package="org.jeo.data"  description="Vector data access interface" %}

The above code snippet introduces two new classes. The `Query` class is used to 
query a vector dataset and controls what data is returned from the query. The 
`Feature` class represents an object in a vector dataset. Both of these concepts
 are covered in greater detail in the <a href="#data_vector">vector data</a> section.

{% include api.html class="Query" package="org.jeo.data" description="Vector data query class" %}

{% include api.html class="Feature" package="org.jeo.feature" description="An item of a vector dataset" %}

In the above GeoJSON example the driver was used to open a specific dataset. 
Other types of formats are containers for multiple datasets. An example is a 
PostGIS database.

{% highlight java %}

   // create a map of connection options
   Map opts = new HashMap();
   opts.put(PostGIS.HOST, "localhost");
   opts.put(PostGIS.PORT, 5432);
   opts.put(PostGIS.DB, "jeo");
   opts.put(PostGIS.USER, "jdeolive");

   // create a driver and open a connection
   PostGIS drv = new PostGIS();
   Workspace db = drv.open(opts);

{% endhighlight %}

In this example the result of `Driver.open` returns an instance of the 
`Workspace` class. A workspace is a container for datasets. 

{% highlight java %}

   // iterate over layers in the workspace
   for (DatasetHandle dataset : db.list()) {
      System.out.println(dataset);
   }

   // get a specific dataset
   db.get("states");

   // dispose the workspace
   db.close();

{% endhighlight %}

{% include api.html class="Workspace" package="org.jeo.data" description="Container for datasets" %}

{% include info.html title='Disposing Resources' message='All data objects such as workspaces and datasets should be disposed after use. This objects extend from java.io.Closeable and instance compatible with the Java 7 "try-with" block.' %}

</section>

<section id="vector-data">

## Vector Data

In the previous section the `VectorDataset` interface was introduced. This 
interface is the primary abstraction used for access to a vector data set.  

### Features

A vector dataset is a collection of `Feature` objects. A feature a simply a map
of named attributes, any of which can be a geometry object.

{% highlight java %}

 // grab a feature 
 Feature f = ...;

 // get some attributes
 f.get("name");
 f.get("location")

 // set some attributes
 f.set("name", "foo")
 f.set("location", Geom.point(0,0));

{% endhighlight %} 

Typically a feature object has a single geometry object. It is not uncommon for
a feature to contain multiple geometry objects, but in this case one is 
designated the default. The ``Feature.geometry()`` method is used to obtain 
the default geometry of a feature. It is also perfectly valid for a feature to 
have no geometry attribute, in which case the ``Feature.geometry()`` returns null.

{% highlight java %}

 // grab a feature 
 Feature f = ...;

 // get the default geometry
 Geometry g = f.geometry();

 // set the default geometry
 f.put(Geom.point(0,0));

{% endhighlight %}

{% include info.html title='Default Geometry' message='Typically the default geometry of a feature is the first one encountered when iterating through the feature attributes' %}

A feature `Schema` is used to describe the  structure and attributes of a feature 
object. A schema is a collection of `Field` objects, each field containing a 
name, a type, and an optional coordinate reference system.

{% highlight java %}

 // grab a feature
 Feature f = ...;

 // gets its schema
 Schema schema = f.schema();

 // iterate over all fields
 for (Field fld : schema) {
   System.out.println(fld.getName());
   System.out.println(fld.getType());
   Systme.out.println(fld.getCRS());
 }

{% endhighlight %}

{% include api.html class="Schema" package="org.jeo.feature" description="Describes the structure of a Feature object" %}

<!--
TODO: Feature crs
-->

### Queries

The `Query` class is used to obtain features from a  vector dataset. A query 
contains a number of properties that control what features are returned in a result set. This includes:

* bounding box - Spatial extent from which to return features
* attribute filter - Attribute predicate for which returned features must match
* limit - Maximum number of features to return
* offset - Offset into result set from which to start returning features

Additionally a query can specify options that transform returned features such
as:

* re-projection - Reproject geometries to a specific crs
* simplification - Simplify geometries with a specific tolerance

As an example:

{% highlight java %}

 // grab all features 
 Query q = new Query();

 // grab all features in a specific area
 Query q = new Query().bounds(new Envelope(...));

 // grab all features with some specific attributes
 Query q = new Query().filter("SAMP_POP > 2000000");

 // paged result set
 Query q = new Query().offset(100).limit(10);

 // reproject
 Query q = new Query().reproject("epsg:900913");

 // chain them all together
 Query q = new Query().bounds(new Envelope(...)).filter("SAMP_POP > 2000000")
   .offset(100).limit(10).reproject("epsg:900913");

{% endhighlight %}

<!--
TODO: sorting
-->

Cursors
-------

The `Cursor` class is used to return a result set  of feature objects from a 
query. A cursor is for the most part an iterator in the normal java sense.

{% highlight java %}

 // get a dataset
 VectorData dataset = ...;

 // query it
 Cursor<Feature> c = dataset.cursor(new Query());

 // iterate
 whille (c.hasNext()) {
   Feautre f = c.next();
   System.out.println(f);
 }

 // close the cursor
 c.close();

{% endhighlight %}

{% include api.html class="Cursor" package="org.jeo.data" description='Iterator for data objects' %}

A cursor implements `java.util.Iterable` and so the java for each provides
a shorthand for iterating through a cursor.

{% highlight java %}

 for (Feature f : dataset.cursor(new Query())) {
   System.out.println(f);
 }

{% endhighlight %}


 It is important that a cursors `Cursor.close` method be called when it is
 no longer needed. When a cursor is used with a for-each as above the close 
 method will be called automatically upon loop completion. However if an
 exception or some other control flow event occurs causing the loop to 
 terminate prematurely it is up to the application to ensure close is 
 called. 

Cursors can also be used to write to a vector dataset. By default a cursor
is considered read-only. The `Query.update` and `Query.append` 
methods are used to obtain a write cursor. The former is used to update 
existing features of the dataset, and the latter is used to add new features.

The `Cursor.write` and `Cursor.remove` methods are used in write mode. 

{% highlight java %}

// update every attribute value to a specific value
Cursor<Feature> c = dataset.cursor(new Query().append());
while (c.hasNext()) {
  Feature f = c.next();
  f.set("name", "foo");
  c.write();
}

// remove a feature
Cursor<Feature> c = dataset.cursor(new Query().update());
c.next();
c.remove();

// add a new feature to the dataset
Cursor<Feature> c = dataset.cursor(new Query().append());
Feature f = c.next();
f.set("name", "bar");
c.write();

{% endhighlight %}

</section>





