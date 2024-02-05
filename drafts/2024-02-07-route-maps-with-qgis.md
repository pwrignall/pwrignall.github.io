---
title: Create a route map with QGIS
description: How to plot airport pairs as great circles on a map.
date: 2024-02-07
tags:
  - dataviz
  - gis
  - qgis
  - maps
extraWideMedia: true
opengraph:
  image: /assets/images/...jpg
---

This guide will take you through the steps needed to create a route map with QGIS, showing lines between points as great
circles.

When I worked at an aviation news and data company called [FlightGlobal](https://www.flightglobal.com/), we occasionally
wanted to display a map showing newly-announced routes, the reach of a carrier, or the top n routes in a region.

For static maps, intended to be printed, I used the excellent geographic information system application,
[QGIS](https://qgis.org/). Whilst my process most certainly could be streamlined, I'm laying it out here so if you ever
feel like mapping some routes, you can too!