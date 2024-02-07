---
title: How to create an airline route map with QGIS
description: A step-by-step guide to plotting airport pairs as great circles on a map.
date: 2024-02-07
tags:
  - dataviz
  - gis
  - qgis
  - maps
extraWideMedia: true
opengraph:
  image: /assets/images/qgis-routes/00-qgis.png
---

This guide will take you through the steps to plot a route map in QGIS, with lines between points as great circles.

When I worked at an aviation news and data company called [FlightGlobal](https://www.flightglobal.com/), we occasionally
wanted to display a map showing newly-announced routes, the geographical reach of a carrier, or the top n routes in a
region.

For static maps, intended to be printed, I used the excellent geographic information system application,
[QGIS](https://qgis.org/). Whilst my process most certainly could be streamlined, I'm laying it out here so if you ever
feel like mapping some routes, you can too!

## Data prep

Route maps tend to use [great circles](https://en.wikipedia.org/wiki/Great-circle_distance) to connect points on a map.
Because of things like weather these are not the exact coordinates an aircraft would follow, but they're used because
they're the shortest distance between two points. And they look nice.

I had an R script I used to use for this. But I've lost it. My attempt to re-write it in Python took a while but,
thanks to the [geographiclib](https://geographiclib.sourceforge.io/Python/doc/examples.html) package and its
examples, I got something working that does a decent job.

The [script is on GitHub](https://github.com/pwrignall/gclines) with instructions and takes a couple of inputs to
produce two outputs: a file containing unique airports, _airport_points.csv_, and a file containing all
the points needed to create the great circle lines in QGIS, _route_points.csv_.

## Adding data layers to QGIS

Once you've [installed QGIS](https://qgis.org/en/site/forusers/download.html), you'll need to add your data as layers
in the application. Once all of your data layers are in, you can experiment with different styles and map projections.

### Map base layer

For this I recommend the brilliant [Natural Earth](https://www.naturalearthdata.com/) project. Medium scale should be
fine for this use case, so you can navigate to the _1:50m Physical_ downloads and save the _Land_ layer &mdash; which
includes polygons for land and major islands of the world &mdash; to a working directory on your device.

1. In QGIS, in an empty project, add that layer through the menu options: **Layer &rarr; Add Layer &rarr; Add Vector
Layer&hellip;**

2. By _Vector Dataset(s)_, browse to your downloaded Natural Earth .zip file and then click **Add**. Close this dialog
box.

{% figure "/assets/images/qgis-routes/01-basemap.png", "After adding your land layer you should have something
like this." %}

### Route points

Now we'll use the outputs from the earlier script and add them as another layer to the project. For this example, I've
plotted the hypothetical route travelled by the Champions League trophy since 1993, from the venue of that year's final
in Munich (MUC) through all the winners' nearest airports and the other final venues.

3. Click **Layer &rarr; Add Layer &rarr; Add Delimited Text Layer&hellip;**

4. By _File Name_, browse to your working directory and select the **route_points.csv** file.
  
5. Under _File Format_, verify **CSV** is selected.

6. Under _Geometry Definition_, select **EPSG:4326 - WGS 84** as the _Geometry CRS_ and click **Add**. You can then
close this dialog.

7. With the route_points layer selected, find the **Zoom to Layer** button on the toolbar or within the _View_ menu to
zoom in and focus on your routes, if you like.

{% figure "/assets/images/qgis-routes/02-route-points.png", "Blobs on shapes, zoomed in. We'll style this later." %}

### Points to lines

Whilst there are QGIS plugins to convert point pairs to lines, I don't think they had all the features I was looking
for. I may revisit this and see if [Beeline](https://plugins.qgis.org/plugins/Beeline/) (which led me to the Python
functions I used in my own script) can offer a simpler alternative to what I'm doing.
Instead, I've used a plugin called [Points2One](https://plugins.qgis.org/plugins/points2one/) to transform the long list
of longitude/latitude coordinate sequences to a set of lines.

Install the plugin through the menu, **Plugins &rarr; Manage and Install Plugins&hellip;** and search for
"Points2One".

8. Select the Points2One dialog through **Vector &rarr; Points2One &rarr; Points2One**.

9. _Input vector layer_ should be **route_points**. You'll want to **Create lines** and _Group features by_ **route**.
Enter an _Output shapefile_ filename in which your lines will be saved and check the _Add result to canvas_ box:

{% figure "/assets/images/qgis-routes/03-points2one.png", "Points2One options." %}

10. Click **OK** and **Close** on the Points2One dialog.

{% figure "/assets/images/qgis-routes/04-blobs-and-lines.png", "Lines connecting the blobs." %}

### Airport points

Our final layer will be marks for the individual airports. This was the other output file generated by the earlier
script.

11. Click **Layer &rarr; Add Layer &rarr; Add Delimited Text Layer&hellip;**

12. By _File Name_, browse to your working directory and select the **airport_points.csv** file.
  
13. Ensure _File Format_ is **CSV** and _Geometry CRS_ is **EPSG:4326 - WGS 84**. Click **Add** and close the dialog.
Because our route points include the airports as start and end points, this addition won't be obvious until we adjust
the styling.

## Changing the map's appearance

### Layer symbology

Editing symbology, or how layers are styled &mdash; sometimes based on some data attribute like you would do on a
choropleth &mdash; can be done by double-clicking a layer in QGIS. You can choose an in-built style or make up your own.
You can modify object fills and strokes and that's all I'll be doing for this map, using **Simple Fill**, **Simple
Line** and **Simple Marker** depending on the layer type.

You can also adjust the canvas colour through **Project &rarr; Properties &rarr; General &rarr; Background color**.

Toggle a layer's visibility completely by clicking the eye-shaped icon next to a layer. We want to do this now for the
route_points layer.

{% figure "/assets/images/qgis-routes/05-after-some-styling.png", "Choosing colours is hard." %}

### Airport labels

We can add text labels to give the viewer an idea of which airport is where.

Double-click the airport_points layer then select **Labels**. Choose **Single Labels** from the dropdown. For _Value_
I'm using the Expression Builder next to the field dropdown and have entered `"iata_code"  || ' ' || "loc_name"` which 
will concatenate the airport code and municipality (maybe not the most intuitive name in all cases, as you'll see) from
the airports input file.

Within this dialog you can adjust the label font, placement and any text effects like buffers and shadows.

{% figure "/assets/images/qgis-routes/06-label-maker.png", "Adding a buffer to improve legibility." %}

### Map projection

When the geographic extent of a map is limited to a specific region, it's a good opportunity to use a different map
projection to minimise the distortions we make when flattening a sphere onto a rectangle.

My go-to tool for choosing a good projection is [Projection Wizard](https://projectionwizard.org/). With it, we can
specify the boundaries of the map and receive some suggested projections in return. For this region, I'll optimise by
equal area and use an Albers equal-area conic projection. The tool also gives the coordinate string you need
for QGIS in either proj4 or WKT formats. QGIS accepts both but prefers WKT.

Within QGIS, define a new projection from **Settings &rarr; Custom Projections&hellip;** Click the plus icon to add a
new projection, give it a name and paste the text from Projection Wizard into the _Parameters_ box. Click **OK**.

Now re-project your map to the newly-defined projection by selecting **Project &rarr; Properties&hellip;**. In the 
dialog click **CRS** (Coordinate Reference System). Within _Predefined Coordinate Reference Systems_ scroll down until
you see some under **User-defined** and select the name you just created. Click **OK** and there's your re-projected
map!

From here you can export the map as an image or PDF to further tweak it. Here's my end product, after exporting and
adding a title. I also took the opportunity to edit airport_points.csv and tidy up the location name labels.

{% figure "/assets/images/qgis-routes/07-reprojected.png", "Lots of visits to Milan, Munich and Madrid." %}

## Summary

And that's it, although quite an involved process end-to-end this doesn't take too long once you're used to it. Except
maybe choosing the colours. With this method you can create quite detailed explanatory maps as well as more abstract
ones like this. Enjoy!

{% figure "/assets/images/qgis-routes/08-world-lines.png", "Route information from openflights.org" %}