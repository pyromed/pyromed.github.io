---
title: "Simulador d'Àrea Cremada"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Aquest espai cartogràfic interactiu mostra les dades de les àrees afectades i els punts d'ignició associats a la simulació d'incendis de PyroMED.

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

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" crossorigin=""></script>

<script>
  // Initialize map tracking standard viewing box coordinates
  var map = L.map('map').setView([39.6, 2.7], 11);

  L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
  }).addTo(map);

  var allPoints = [];
  var allPolygons = [];
  
  var pointsLayerGroup = L.layerGroup().addTo(map);
  var polygonsLayerGroup = L.layerGroup().addTo(map);

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

  // Jekyll asset path injection parsing via Liquid Engine
  Promise.all([
    fetch('{{ "/assets/data/points.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}').then(r => r.json())
  ]).then(([pointsData, polygonsData]) => {
    allPoints = pointsData.features;
    allPolygons = polygonsData.features;
    
    updateSimulation();
  }).catch(err => console.error('Error loading simulation components:', err));

  document.getElementById('wind-select').addEventListener('change', function() { polygonsLayerGroup.clearLayers(); });
  document.getElementById('time-select').addEventListener('change', function() { polygonsLayerGroup.clearLayers(); });

  function updateSimulation() {
    pointsLayerGroup.clearLayers();
    polygonsLayerGroup.clearLayers();
    document.getElementById('left-metrics').classList.add('hidden');
    document.getElementById('right-panel').style.display = 'none';

    if(allPoints.length === 0) return;

    // Render geographic data points
    var geoJsonLayer = L.geoJSON({ type: "FeatureCollection", features: allPoints }, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, bluePointStyle);
      },
      onEachFeature: function (feature, layer) {
        layer.on('click', function () {
          var currentWind = parseInt(document.getElementById('wind-select').value);
          var currentTime = parseInt(document.getElementById('time-select').value);
          displayIncidentDetails(feature, currentWind, currentTime);
        });
      }
    }).addTo(pointsLayerGroup);

    // Zoom automatically right directly into the coordinates frame bounding layout
    if (geoJsonLayer.getBounds().isValid()) {
        map.fitBounds(geoJsonLayer.getBounds(), { padding: [40, 40] });
    }
  }

  function displayIncidentDetails(pointFeature, wind, time) {
    polygonsLayerGroup.clearLayers();

    // Find overlapping simulation polygon
    var matchingPoly = allPolygons.find(p => 
      String(p.properties.id) === String(pointFeature.properties.id) &&
      parseInt(p.properties.wind) === wind &&
      parseInt(p.properties.time) === time
    );

    if (matchingPoly) {
      L.geoJSON(matchingPoly, { style: activeBurnStyle }).addTo(polygonsLayerGroup);
    }

    var props = pointFeature.properties;

    // --- LEFT SIDEBAR CONTENT HANDLING ---
    document.getElementById('left-metrics').classList.remove('hidden');
    document.getElementById('fire-type').innerText = props.fire_type || "DE SUPERFÍCIE";
    
    var intensity = props.intensity || 248.22;
    var speed = props.spread_speed || 2.6;
    document.getElementById('intensity-val').innerText = intensity;
    document.getElementById('speed-val').innerText = speed;
    
    // Scale tracking bars
    document.getElementById('intensity-bar').style.width = Math.min((intensity / 1000) * 100, 100) + "%";
    document.getElementById('speed-bar').style.width = Math.min((speed / 20) * 100, 100) + "%";
    
    // This populates the custom string text directly from your geojson properties tag!
    document.getElementById('critical-elements').innerText = props.critical_elements || "Plantes adaptades a la sequera -> Molt inflamables";

    // --- RIGHT SIDEBAR CONTENT HANDLING ---
    document.getElementById('right-panel').style.display = 'block';
    document.getElementById('char-elev').innerText = props.elevation || "-";
    document.getElementById('char-slope').innerText = props.slope || "-";
    document.getElementById('char-orient').innerText = props.orientation || "-";
    document.getElementById('char-dens').innerText = props.tree_density || "-";
    document.getElementById('char-canopy').innerText = props.canopy_height || "-";
    document.getElementById('char-under').innerText = props.understory_cover || "-";
    
    if(props.image_url) {
       // Appends Jekyll base path tags to target your image asset catalog folder perfectly
       document.getElementById('env-img').src = '{{ "" | relative_url }}' + props.image_url;
    } else {
       document.getElementById('env-img').src = '{{ "/assets/images/bosc_default.jpg" | relative_url }}';
    }
  }
</script>
