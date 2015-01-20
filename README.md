# Google-Driving-Sample
Google Driving 

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"
    "http://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<!--
 Copyright 2010 Google Inc. 
 Licensed under the Apache License, Version 2.0: 
 http://www.apache.org/licenses/LICENSE-2.0 
 -->

<html>
<head>
<meta name="viewport" content="initial-scale=1.0, user-scalable=no"/>
<meta http-equiv="content-type" content="text/html; charset=UTF-8"/>
<title>Google Maps JavaScript API v3 Example: Optimized Directions</title>
<script type="text/javascript" src="https://maps.google.com/maps/api/js?sensor=false"></script>
<script type="text/javascript">

  var directionDisplay;
  var directionsService = new google.maps.DirectionsService();
  var map;
  var origin = null;
  var destination = null;
  var waypoints = [];
  var markers = [];
  var directionsVisible = false;

  var rendererOptions = {
  draggable: true
  };

  function initialize() {
    directionsDisplay = new google.maps.DirectionsRenderer(rendererOptions);
    var chicago = new google.maps.LatLng(37.7749295, -122.4194155);
    var myOptions = {
      zoom:13,
      mapTypeId: google.maps.MapTypeId.ROADMAP,
      center: chicago
    }
    map = new google.maps.Map(document.getElementById("map_canvas"), myOptions);
    directionsDisplay.setMap(map);
    directionsDisplay.setPanel(document.getElementById("directionsPanel"));
    
    google.maps.event.addListener(map, 'click', function(event) {
      if (origin == null) {
        origin = event.latLng;
        addMarker(origin);
      } else if (destination == null) {
        destination = event.latLng;
        addMarker(destination);
      } else {
        if (waypoints.length < 9) {
          waypoints.push({ location: destination, stopover: true });
          destination = event.latLng;
          addMarker(destination);
        } else {
          alert("Maximum number of waypoints reached");
        }
      }
    });
  }

  function addMarker(latlng) {
    markers.push(new google.maps.Marker({
      position: latlng, 
      map: map,
      icon: "https://maps.google.com/mapfiles/marker" + String.fromCharCode(markers.length + 65) + ".png"
    }));    
  }

  function calcRoute() {
    if (origin == null) {
      alert("Click on the map to add a start point");
      return;
    }
  
    if (destination == null) {
      alert("Click on the map to add an end point");
      return;
    }
    
    var mode;
    switch (document.getElementById("mode").value) {
      case "bicycling":
        mode = google.maps.DirectionsTravelMode.BICYCLING;
        break;
      case "driving":
        mode = google.maps.DirectionsTravelMode.DRIVING;
        break;
      case "walking":
        mode = google.maps.DirectionsTravelMode.WALKING;
        break;
    }
    
    var request = {
        origin: origin,
        destination: destination,
        waypoints: waypoints,
        travelMode: mode//,
        /*
        optimizeWaypoints: document.getElementById('optimize').checked,
        avoidHighways: document.getElementById('highways').checked,
        avoidTolls: document.getElementById('tolls').checked
        */
    };
    
    directionsService.route(request, function(response, status) {
      if (status == google.maps.DirectionsStatus.OK) {
        directionsDisplay.setDirections(response);
      }
    });
    
    clearMarkers();
    directionsVisible = true;
  }
  
  function updateMode() {
    if (directionsVisible) {
      calcRoute();
    }
  }
  
  function clearMarkers() {
    for (var i = 0; i < markers.length; i++) {
      markers[i].setMap(null);
    }
  }
  
  function clearWaypoints() {
    markers = [];
    origin = null;
    destination = null;
    waypoints = [];
    directionsVisible = false;
  }
  
  function reset() {
    clearMarkers();
    clearWaypoints();
    directionsDisplay.setMap(null);
    directionsDisplay.setPanel(null);
    directionsDisplay = new google.maps.DirectionsRenderer();
    directionsDisplay.setMap(map);
    directionsDisplay.setPanel(document.getElementById("directionsPanel"));    
  }
</script>
</head>
<body onload="initialize()" style="font-family: sans-serif;">
  <div>
    <div>Find direction:</div>
    <div>
      <select id="mode" onchange="updateMode()">
        <option value="driving">Driving</option>
        <option value="walking">Walking</option>
      </select>
      <div>
        <input type="button" value="Reset" onclick="reset()" />
      </div>
      <div>
        <input type="button" value="Get Direction" onclick="calcRoute()" />
      </div>  
     </div>
    </div>
    <div style="position:relative; border: 1px; width: 610px; height: 400px;">
      <div id="map_canvas" style="border: 1px solid black; position:absolute; width:398px; height:398px"></div>
      <div id="directionsPanel" style="position:absolute; left: 410px; width:240px; height:400px; overflow: auto">      
    </div>
  </div> 
</body>
</html>


