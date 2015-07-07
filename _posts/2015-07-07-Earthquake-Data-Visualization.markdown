---
layout: post
title:  "Earthquake with Google Maps API"
author: Alvaro Tolentino
date:   2015-07-07 02:49:17
categories: research
image: /assets/img/post/earthquake.png
tags: [Earthquake Google Maps API]
---
This is a example of how to generate a data visualization using Google Maps API and a Geojson file with the information of the earthquakes.

This is a example of how to generate a data visualization using Google Maps API and a Geojson file with the information of the earthquakes.

This is a example of how to generate a data visualization using Google Maps API and a Geojson file with the information of the earthquakes.

This is a example of how to generate a data visualization using Google Maps API and a Geojson file with the information of the earthquakes.



<script src="https://maps.googleapis.com/maps/api/js"></script>
<script>
var map;

google.maps.event.addDomListener(window, 'load', function() {
  map = new google.maps.Map(document.getElementById('map-canvas'), {
    center: { lat: 20, lng: -160 },
    zoom: 3
  });

  // Get the earthquake data (JSONP format)
  // This feed is a copy from the USGS feed, you can find the originals here:
  //   http://earthquake.usgs.gov/earthquakes/feed/v1.0/geojson.php
  var script = document.createElement('script');
  script.setAttribute('src',
      'https://storage.googleapis.com/maps-devrel/quakes.geo.json');
  document.getElementsByTagName('head')[0].appendChild(script);

  // Add a basic style.
  map.data.setStyle(function(feature) {
    var mag = Math.exp(parseFloat(feature.getProperty('mag'))) * 0.1;
    return /** @type {google.maps.Data.StyleOptions} */({
      icon: {
        path: google.maps.SymbolPath.CIRCLE,
        scale: mag,
        fillColor: '#f00',
        fillOpacity: 0.35,
        strokeWeight: 0
      }
    });
  });
});

// Defines the callback function referenced in the jsonp file.
function eqfeed_callback(data) {
  map.data.addGeoJson(data);
}
</script>


<div id="googleMap" style="width:500px;height:380px;"></div>