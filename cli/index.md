---
layout: default
title: Command Line Interface
---

# Command Line Interface

The `jeo` command provides a command line tool for exploring data 
formats supported by jeo. The general syntax for the utility is:

{% highlight bash %}

 $ jeo

 usage: jeo <command> [<args>]

  Commands:

      drivers    Lists avialable format drivers
      query      Executes a query against a data set
      info       Provides information about a data source
      convert    Converts between data sets
      serve      Starts NanoHTTPD web server

  For detailed help on a specific command use jeo <command> -h

{% endhighlight %}

## Data Sources

Most commands take one or more data sources as input. A data source is a 
workspace, or a data set within a workspace. The following uri syntax is used
to reference a data source:

    [<driver>://][<primary-option>][?<secondary-options>]*][#<dataset>]

The following is an example a PostGIS uri:

    pg://jeo?host=localhost&port=5432&user=jdeolive#states

For file based data uris can be specified simply as a file path with the 
caveat that the file has an extension that identifies the driver. An example
of a GeoJSON path:

     /data/states.json

### driver

Name or alias identifying the driver to use to read the data source.

### main-option

The main driver option used to connect to the data source. In the case of file
based drivers this is the file path. For database based drivers this is the name
of the database. 

### secondary-options

Secondary driver options to use to connect to the data source. Secondary options
are specified as key value pairs. 

### dataset

The name of a data set within a workspace. This option is only for workspace 
drivers. 

## Commands

### drivers


The `drivers` command lists all supported drivers.

    jeo drivers [options]

    Options:
      -x, --debug
         Runs command in debug mode
      -h, --help
         Provides help for this command
  
### info

The `info` command provides information about a data source. 

    jeo info [options] datasource

    Options:
      -x, --debug
         Runs command in debug mode
      -h, --help
         Provides help for this command

When the data source specifies a workspace the command output will list all 
data sets of the workspace. When the data source specifies a data set 
information about the data set such as schema, spatial extent, and projection
will be displayed. 

### query

The `query` command provides information about a data set. 

    jeo query [options] dataset

    Options:
        -b, --bbox
           Bounding box (xmin,ymin,xmax,ymax)
        -f, --filter
           Predicate used to constrain results
        -c, -count
           Maximum number of results to return
        -s, -summary
           Summarize results only
        -x, --debug
           Runs command in debug mode
        -h, --help
           Provides help for this command


### convert

The `convert` command converts a data set between formats. 

    jeo convert [options] source target

    Options:
        -fc, --from-crs
           Source CRS override
        -tc, --to-crs
           Target CRS
        -h, --help
           Provides help for this command
        -x, --debug
           Runs command in debug mode

## Examples

List all supported drivers.

{% highlight bash %}

   $ jeo drivers

{% endhighlight %}

Info about data sets in a PostGIS workspace

{% highlight bash %}

   $ jeo info pg://jeo?host=localhost

{% endhighlight %}

Info about a specific data set in a PostGIS workspace

{% highlight bash %}

   $ jeo info pg://jeo#states

{% endhighlight %}

Query a GeoJSON data set

{% highlight bash %}

   $ jeo query states.json

{% endhighlight %}

Query a GeoJSON data set with spatial extent

{% highlight bash %}

   $ jeo query -b -124.731422,24.955967,-66.969849,49.371735 states.json

{% endhighlight %}

Query a GeoJSON data set with attribute filter

{% highlight bash %}

   $ jeo query -f "STATE_NAME = 'New York'" states.json

{% endhighlight %}

Convert a PostGIS table to GeoJSON

{% highlight bash %}

   $ jeo convert pg://jeo#states states.json

{% endhighlight %}

Reproject a GeoJSON file to PostGIS

{% highlight bash %}

   $ jeo convert -fc epsg:4326 -tc epsg:900913 states.json pg://jeo#states

{% endhighlight %}