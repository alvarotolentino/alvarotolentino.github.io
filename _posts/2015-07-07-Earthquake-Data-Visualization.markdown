---
layout: post
title:  "Data Visualization with Google Maps API"
author: Alvaro Tolentino
date:   2015-07-07 02:49:17
categories: research
image: /assets/img/post/earthquake1.png
tags: [Data Visualization Google Maps API]
---
The  visualization has its origins in the very beginning of humanity. We can find expressions as old as those found in the regions of Maros and Pangkep in Indonesia, impressions on the rock dating from about 40,000 years ago; or those found in the caves of Altamira in Spain.

As they were, these intended to express an idea, a situation, an experience, or whatever that the creator or designer wants. 

<img src="/assets/img/post/cave-art-hand-stencils.jpg" class="img-responsive img-bordered img-center" alt="image">
<h5>CREDIT: Kinez Riza</h5>


The etymological origin of the term Visualization is rooted in two Latin words (1000 B.C.)

<div class="tag-box tag-box-v2 box-shadow shadow-effect-1">
  <span class="color-blue">videre</span>:to see, to recognize<br/>
  <span class="color-blue">visus</span>:the faculty of seeing, the power of sight
</div>

while semantically it can be defined as:

<div class="tag-box tag-box-v2 box-shadow shadow-effect-1">
  <span class="color-blue">to visualize</span>: to form a mental image, to render... visible
</div>


Now, if artistic expression as those above are used to express aspects as those discussed, the Data Visualization is looking for graph the information contained in data, whatever the source thereof.

Below a Data visualization or data visualisation description from Wikipedia


<blockquote class="text-right bq-dark margin-bottom-50">
  <p><em> 
  Data visualization or data visualisation is viewed by many disciplines as a modern equivalent of visual communication. It is not owned by any one field, but rather finds interpretation across many (e.g. it is viewed as a modern branch of descriptive statistics by some, but also as a grounded theory development tool by others)</em></p>
  <small> <em>
    Wikipedia
  </em> </small>  
</blockquote>

Well, after some context, time to the experimentation.

The target of this first approach to Data Visualization is showing the earthquakes using the Google Maps API and a dataset from a GeoJSON file which contains the latitude, longitude, intensity and some aditional information.

To start using Google Maps API we must follow these steps:

<h3>Load the Google API</h3>
This is a Javascript library which can be invoked using the following tag:

{% highlight javascript %}
  <script src="https://maps.googleapis.com/maps/api/js"/>
{% endhighlight %}

Additionally we can use the API key in the URL.

{% highlight javascript %}
  <script src="https://maps.googleapis.com/maps/api/js?key=YOUR_KEY"/>
{% endhighlight %}

<h3>Create the container</h3>
Where the map will be displayed.

{% highlight html%}
  <div id="map-canvas" style="width:800px;height:380px;"></div>
{% endhighlight %}

<h3>Create the Map object</h3>

{% highlight javascript%}
  var map;
  map = new google.maps.Map(document.getElementById('map-canvas'), {
    center: { lat: 20, lng: -160 },
{% endhighlight %}


<h3>Add a Event Listener</h3>

{% highlight javascript%}
  google.maps.event.addDomListener(window, 'load', initialize);
{% endhighlight %}

But it can also be expressed as follows:

{% highlight javascript%}
google.maps.event.addDomListener(window, 'load', function() {
  map = new google.maps.Map(document.getElementById('map-canvas'), {
    center: { lat: 20, lng: -160 },
    zoom: 2
  });
{% endhighlight %}

<h3>Load the data from a Geojson file</h3>

{% highlight javascript%}
  map.data.loadGeoJson('/data/earthquake.geojson');
{% endhighlight %}

Below three examples of the use of Google Maps API

<h3>Default</h3>
<h5>Javascript</h5>
{% highlight javascript linenos %}
  <script>
    var map;
    
    google.maps.event.addDomListener(window, 'load', function() {
      map = new google.maps.Map(document.getElementById('map-canvas1'), {
        center: { lat: 20, lng: -160 },
        zoom: 2
      });
    
      map.data.loadGeoJson('/data/earthquake.geojson');
    });
  </script>
{% endhighlight %}

<h5>Result</h5>
<div id="map-canvas1" style="width:auto;height:380px;"></div>


<h3>Simple</h3>
<h5>Javascript</h5>
{% highlight javascript linenos %}
<script>
  var map;
  
  google.maps.event.addDomListener(window, 'load', function() {
    map = new google.maps.Map(document.getElementById('map-canvas2'), {
      center: { lat: 20, lng: -160 },
      zoom: 2
    });
  
    map.data.loadGeoJson('/data/earthquake.geojson');
  
    map.data.setStyle(function(feature) {
      var mag = Math.exp(parseFloat(feature.getProperty('magnitude'))) * 0.1;
      return({
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
</script>
{% endhighlight %}

<h5>Result</h5>
<div id="map-canvas2" style="width:auto;height:380px;"></div>


<h3>Custom</h3>
<h5>Javascript</h5>
{% highlight javascript linenos %}
<script>
var map;
var mapStyle = [{
  'featureType': 'all',
  'elementType': 'all',
  'stylers': [{'visibility': 'off'}]
}, {
  'featureType': 'landscape',
  'elementType': 'geometry',
  'stylers': [{'visibility': 'on'}, {'color': '#fcfcfc'}]
}, {
  'featureType': 'water',
  'elementType': 'labels',
  'stylers': [{'visibility': 'off'}]
}, {
  'featureType': 'water',
  'elementType': 'geometry',
  'stylers': [{'visibility': 'on'}, {'hue': '#5f94ff'}, {'lightness': 60}]
}];

google.maps.event.addDomListener(window, 'load', function() {
  map = new google.maps.Map(document.getElementById('map-canvas3'), {
    center: { lat: 20, lng: -160 },
    zoom: 2,
    styles: mapStyle
  });

  map.data.setStyle(styleFeature);

  map.data.loadGeoJson('/data/earthquake.geojson');
});

// Defines the callback function referenced in the jsonp file.
function eqfeed_callback(data) {
  map.data.addGeoJson(data);
}

function styleFeature(feature) {
  var low = [151, 83, 34];   // color of mag 1.0
  var high = [5, 69, 54];  // color of mag 6.0 and above
  var minMag = 1.0;
  var maxMag = 6.0;

  // fraction represents where the value sits between the min and max
  var fraction = (Math.min(feature.getProperty('magnitude'), maxMag) - minMag) /
      (maxMag - minMag);

  var color = interpolateHsl(low, high, fraction);

  return {
    icon: {
      path: google.maps.SymbolPath.CIRCLE,
      strokeWeight: 0.5,
      strokeColor: '#fff',
      fillColor: color,
      fillOpacity: 2 / feature.getProperty('magnitude'),
      // while an exponent would technically be correct, quadratic looks nicer
      scale: Math.pow(feature.getProperty('magnitude'), 2)
    },
    zIndex: Math.floor(feature.getProperty('magnitude'))
  };
}

function interpolateHsl(lowHsl, highHsl, fraction) {
  var color = [];
  for (var i = 0; i < 3; i++) {
    // Calculate color based on the fraction.
    color[i] = (highHsl[i] - lowHsl[i]) * fraction + lowHsl[i];
  }

  return 'hsl(' + color[0] + ',' + color[1] + '%,' + color[2] + '%)';
}

    </script>
{% endhighlight %}

<h5>Result</h5>
<div id="map-canvas3" style="width:auto;height:380px;"></div>

<script src="https://maps.googleapis.com/maps/api/js?key=AIzaSyDeo37ZEPzpUI8AHly34EFA4We-irhnOJA"></script>

<script>
  var map;
  
  google.maps.event.addDomListener(window, 'load', function() {
    map = new google.maps.Map(document.getElementById('map-canvas1'), {
      center: { lat: 28, lng: 15 },
      zoom: 2
    });
  
    map.data.loadGeoJson('/data/earthquake.geojson');
  });
</script>

<script>
  var map;
  
  google.maps.event.addDomListener(window, 'load', function() {
    map = new google.maps.Map(document.getElementById('map-canvas2'), {
      center: { lat: 28, lng: 15 },
      zoom: 2
    });
  
    map.data.loadGeoJson('/data/earthquake.geojson');
  
    map.data.setStyle(function(feature) {
      var mag = Math.exp(parseFloat(feature.getProperty('magnitude'))) * 0.08;
      return({
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
</script>

<script>
    var map;
    var mapStyle = [{
      'featureType': 'all',
      'elementType': 'all',
      'stylers': [{'visibility': 'on'}]
    }, {
      'featureType': 'landscape',
      'elementType': 'geometry',
      'stylers': [{'visibility': 'on'}, {'color': '#fcfcfc'}]
    }, {
      'featureType': 'water',
      'elementType': 'labels',
      'stylers': [{'visibility': 'on'}]
    }, {
      'featureType': 'water',
      'elementType': 'geometry',
      'stylers': [{'visibility': 'on'}, {'hue': '#5f94ff'}, {'lightness': 60}]
    }];
    
    google.maps.event.addDomListener(window, 'load', function() {
      map = new google.maps.Map(document.getElementById('map-canvas3'), {
        center: { lat: 28, lng: 15 },
        zoom: 2,
        styles: mapStyle
      });
    
      map.data.setStyle(styleFeature);
    
      map.data.loadGeoJson('/data/earthquake.geojson');
    });
    
    // Defines the callback function referenced in the jsonp file.
    function eqfeed_callback(data) {
      map.data.addGeoJson(data);
    }
    
    function styleFeature(feature) {
      var low = [151, 83, 34];   // color of mag 1.0
      var high = [5, 69, 54];  // color of mag 6.0 and above
      var minMag = 1.0;
      var maxMag = 6.0;
    
      // fraction represents where the value sits between the min and max
      var fraction = (Math.min(feature.getProperty('magnitude'), maxMag) - minMag) /
          (maxMag - minMag);
    
      var color = interpolateHsl(low, high, fraction);
    
      return {
        icon: {
          path: google.maps.SymbolPath.CIRCLE,
          strokeWeight: 0.5,
          strokeColor: '#fff',
          fillColor: color,
          fillOpacity: 2 / feature.getProperty('magnitude'),
          // while an exponent would technically be correct, quadratic looks nicer
          scale: Math.pow(feature.getProperty('magnitude'), 2)
        },
        zIndex: Math.floor(feature.getProperty('magnitude'))
      };
    }
    
    function interpolateHsl(lowHsl, highHsl, fraction) {
      var color = [];
      for (var i = 0; i < 3; i++) {
        // Calculate color based on the fraction.
        color[i] = (highHsl[i] - lowHsl[i]) * fraction + lowHsl[i];
      }
    
      return 'hsl(' + color[0] + ',' + color[1] + '%,' + color[2] + '%)';
    }
</script>