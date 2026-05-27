---
title: "Simulador d'Àrea Cremada"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Aquest espai cartogràfic interactiu mostra les dades de les àrees afectades i els punts d'ignició associats a una simulació d'incendis.

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" crossorigin="" />

<style>
  /* Dashboard layout configuration matching your layout templates */
  .dashboard-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    background-color: #1a425a; 
    color: #ffffff;
    padding: 20px;
    border-radius: 8px;
    font-family: Arial, sans-serif;
  }

  .sidebar-controls {
    flex: 1;
    min-width: 220px;
    display: flex;
    flex-direction: column;
    gap: 15px;
  }

  .control-group {
    display: flex;
    flex-direction: column;
    gap: 5px;
  }

  .control-group label {
    font-weight: bold;
    font-size: 0.9em;
  }

  .control-group select {
    padding: 8px;
    border-radius: 4px;
    border: 1px solid #ccc;
    background-color: #fff;
    color: #333;
  }

  .map-container {
    flex: 2;
    min-width: 350px;
    height: 550px;
    position: relative;
  }

  #map {
    width: 100%;
    height: 100%;
    border-radius: 4px;
  }

  .info-panel {
    flex: 1;
    min-width: 250px;
    background: rgba(255, 255, 255, 0.1);
    padding: 15px;
    border-radius: 4px;
    max-height: 550px;
    overflow-y: auto;
    display: none; 
  }

  .info-panel img {
    width: 100%;
    border-radius: 4px;
    margin-bottom: 15px;
  }

  .metric-bar-container {
    background: #444;
    border-radius: 4px;
    height: 15px;
    width: 100%;
    margin-top: 5px;
    position: relative;
  }

  .metric-bar {
    background: #e65c00;
    height: 100%;
    border-radius: 4px;
    transition: width 0.3s ease;
  }

  .hidden { display: none; }
</style>

<div class="dashboard-container">
  
  <div class="sidebar-controls">
    <div>
      <strong>INSTRUCCIONS:</strong><br>
      1- Selecciona la velocitat del vent i el temps<br>
      2- Fes click a qualsevol punt blau<br>
      3- Fes zoom per veure l'àrea cremada en detall
    </div>
    
    <hr style="border-color: rgba(255,255,255,0.2);">

    <div class="control-group">
      <label for="wind-select">Velocitat del vent (km/h):</label>
      <select id="wind-select">
        <option value="10">10</option>
        <option value="20" selected>20</option>
        <option value="30">30</option>
      </select>
    </div>

    <div class="control-group">
      <label for="time-select">Temps (min):</label>
      <select id="time-select">
        <option value="30" selected>30</option>
        <option value="60">60</option>
        <option value="120">120</option>
      </select>
    </div>

    <div id="left-metrics" class="hidden">
      <p><strong>Tipus de foc:</strong> <span id="fire-type">DE SUPERFÍCIE</span></p>
      
      <p>Intensitat [kW/m]: <span id="intensity-val">0</span></p>
      <div class="metric-bar-container">
        <div id="intensity-bar" class="metric-bar"></div>
      </div>
      
      <p style="margin-top:15px;">Velocitat de propagació [m/min]: <span id="speed-val">0</span></p>
      <div class="metric-bar-container">
        <div id="speed-bar" class="metric-bar"></div>
      </div>

      <p style="margin-top:20px;"><strong>Perill d'incendi:</strong> <span id="danger-level">Moderat</span></p>
      <p><strong>Elements crítics:</strong><br><small id="critical-elements">-</small></p>
    </div>
  </div>

  <div class="map-container">
    <div id="map"></div>
  </div>

  <div class="info-panel" id="right-panel">
    <img id="env-img" src="{{ '/assets/images/bosc_default.jpg' | relative_url }}" alt="Entorn">
    
    <h4>Historial d'incendis</h4>
    <p id="fire-history">Cremada fa 10 y 30 anys; molts arbres van sobreviure.</p>
    
    <h4>Entorn</h4>
    <p id="env-desc">Antigues terrasses agrícoles abandonades...</p>
    
    <h4>Característiques</h4>
    <ul style="padding-left:20px; font-size:0.9em;">
      <li>Elevació: <span id="char-elev">-</span> m</li>
      <li>Pendent: <span id="char-slope">-</span>%</li>
      <li>Orientació: <span id="char-orient">-</span></li>
      <li>Densitat: <span id="char-dens">-</span> arbres/ha</li>
      <li>Alçada dosser: <span id="char-canopy">-</span> m</li>
      <li>Coberta de sotabosc: <span id="char-under">-</span>%</li>
    </ul>
  </div>

</div>

<script>
  // 1. Initialize map WITHOUT the standard base map layer tiles
  // (We remove L.tileLayer completely to give you a clean slate)
  var map = L.map('map').setView([39.6, 2.7], 11);

  // Global variables to store your custom spatial layers
  var allPoints = [];
  var allPolygons = [];
  var allContours = []; // Added to hold your topographic lines
  
  // Dedicated overlay layers
  var contoursLayerGroup = L.layerGroup().addTo(map); // Static background
  var polygonsLayerGroup = L.layerGroup().addTo(map); // Dynamic burn footprints
  var pointsLayerGroup = L.layerGroup().addTo(map);   // Interactive blue points

  // Component Style Profiles (Adjust colors to make them pop against a dark/light dashboard!)
  var contourStyle = {
    color: "#4a6b82",    // Subtle blue-grey contour lines
    weight: 1,
    opacity: 0.6
  };

  var bluePointStyle = {
    radius: 6,
    fillColor: "#5856d6",
    color: "#fff",
    weight: 1.5,
    opacity: 1,
    fillOpacity: 0.9
  };

  var activeBurnStyle = {
    color: "#e63946",
    fillColor: "#e63946",
    weight: 2,
    fillOpacity: 0.5
  };

  // 2. Fetch all three GeoJSON assets concurrently from your Jekyll assets folder
  Promise.all([
    fetch('{{ "/assets/data/points.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/contours.geojson" | relative_url }}').then(r => r.json()) // Fetching your contours!
  ]).then(([pointsData, polygonsData, contoursData]) => {
    allPoints = pointsData.features;
    allPolygons = polygonsData.features;
    allContours = contoursData.features;
    
    // Build initial layout render
    updateSimulation();
  }).catch(err => console.error('Error loading simulation layer maps:', err));

  document.getElementById('wind-select').addEventListener('change', function() { polygonsLayerGroup.clearLayers(); });
  document.getElementById('time-select').addEventListener('change', function() { polygonsLayerGroup.clearLayers(); });

  function updateSimulation() {
    pointsLayerGroup.clearLayers();
    polygonsLayerGroup.clearLayers();
    contoursLayerGroup.clearLayers(); // Clean slate refresh
    
    document.getElementById('left-metrics').classList.add('hidden');
    document.getElementById('right-panel').style.display = 'none';

    if(allPoints.length === 0) return;

    // A. Render your custom Contour lines as the absolute background canvas layer
    var contourLayer = L.geoJSON({ type: "FeatureCollection", features: allContours }, {
      style: contourStyle
    }).addTo(contoursLayerGroup);

    // B. Render interactive point layer nodes over the contour canvas background
    var geoJsonLayer = L.geoJSON({ type: "FeatureCollection", features: allPoints }, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, bluePointStyle);
      },
      onEachFeature: function (feature, layer) {
        layer.on('click', function (e) {
          L.DomEvent.stopPropagation(e);
          var currentWind = parseInt(document.getElementById('wind-select').value);
          var currentTime = parseInt(document.getElementById('time-select').value);
          displayIncidentDetails(feature, currentWind, currentTime);
        });
      }
    }).addTo(pointsLayerGroup);

    // C. Zoom the viewport smoothly to center directly over your localized contour bounds!
    if (contourLayer.getBounds().isValid()) {
        map.fitBounds(contourLayer.getBounds(), { padding: [20, 20] });
    }
  }

  function displayIncidentDetails(pointFeature, wind, time) {
    // Clear out old active polygons completely upon each new click event
    polygonsLayerGroup.clearLayers();

    var pointId = String(pointFeature.properties.id);

    // Filter matching burn layout criteria safely by converting indices to clear strings
    var matchingPoly = allPolygons.find(p => {
      var matchId = String(p.properties.id || p.properties.site_id);
      var matchWind = parseInt(p.properties.wind);
      var matchTime = parseInt(p.properties.time);
      
      return matchId === pointId && matchWind === wind && matchTime === time;
    });

    if (matchingPoly) {
      L.geoJSON(matchingPoly, { style: activeBurnStyle }).addTo(polygonsLayerGroup);
    } else {
      console.warn("No overlapping layout map found for point ID:", pointId, "Wind:", wind, "Time:", time);
    }

    // Populate Side Control Dashboards
    var props = pointFeature.properties;

    // --- Left Panels updates ---
    document.getElementById('left-metrics').classList.remove('hidden');
    document.getElementById('fire-type').innerText = props.fire_type || "DE SUPERFÍCIE";
    
    // Pull the calculated values explicitly or apply standard defaults if unavailable
    var intensity = props.intensity || 248.22;
    var speed = props.spread_speed || 2.6;
    
    document.getElementById('intensity-val').innerText = intensity;
    document.getElementById('speed-val').innerText = speed;
    
    document.getElementById('intensity-bar').style.width = Math.min((intensity / 1000) * 100, 100) + "%";
    document.getElementById('speed-bar').style.width = Math.min((speed / 20) * 100, 100) + "%";
    
    document.getElementById('critical-elements').innerText = props.critical_elements || "Plantes adaptades a la sequera -> Molt inflamables";

    // --- Right Panels updates ---
    document.getElementById('right-panel').style.display = 'block';
    document.getElementById('char-elev').innerText = props.elevation || "-";
    document.getElementById('char-slope').innerText = props.slope || "-";
    document.getElementById('char-orient').innerText = props.orientation || "-";
    document.getElementById('char-dens').innerText = props.tree_density || "-";
    document.getElementById('char-canopy').innerText = props.canopy_height || "-";
    document.getElementById('char-under').innerText = props.understory_cover || "-";
    
    if(props.image_url) {
       document.getElementById('env-img').src = '{{ "" | relative_url }}' + props.image_url;
    } else {
       document.getElementById('env-img').src = '{{ "/assets/images/bosc_default.jpg" | relative_url }}';
    }
  }
</script>
