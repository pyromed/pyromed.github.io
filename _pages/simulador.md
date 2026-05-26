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
  /* Dashboard layout matching your screenshots */
  .dashboard-container {
    display: flex;
    flex-wrap: wrap;
    gap: 20px;
    background-color: #1a425a; /* Deep blue background */
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

  .sidebar-controls h3 {
    margin-top: 0;
    color: #fff;
    font-size: 1.1em;
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
    display: none; /* Hidden until a blue point is clicked */
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
        <option value="20">20</option>
        <option value="30">30</option>
      </select>
    </div>

    <div class="control-group">
      <label for="time-select">Temps (min):</label>
      <select id="time-select">
        <option value="30">30</option>
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
      <p><strong>Elements crítics:</strong><br><small id="critical-elements">Plantes adaptades a la sequera -> Molt inflamables</small></p>
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
  // Initialize map centered on the simulation area
  var map = L.map('map').setView([39.6, 2.7], 11);

  L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
  }).addTo(map);

  // Global variables to store parsed GeoJSON feature structures
  var allPoints = [];
  var allPolygons = [];
  
  // Layer groups to easily clear map objects dynamically
  var pointsLayerGroup = L.layerGroup().addTo(map);
  var polygonsLayerGroup = L.layerGroup().addTo(map);

  // Styling properties
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

  // Fetch both GeoJSON data assets concurrently
  Promise.all([
    fetch('{{ "/assets/data/points.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}').then(r => r.json())
  ]).then(([pointsData, polygonsData]) => {
    allPoints = pointsData.features;
    allPolygons = polygonsData.features;
    
    // Initial display filter
    updateSimulation();
  }).catch(err => console.error('Error loading simulation layer maps:', err));

  // Handle changes in dropdown configurations
  document.getElementById('wind-select').addEventListener('change', updateSimulation);
  document.getElementById('time-select').addEventListener('change', updateSimulation);

  function updateSimulation() {
    // 1. Clear previous layers and hide side dynamic info panels
    pointsLayerGroup.clearLayers();
    polygonsLayerGroup.clearLayers();
    document.getElementById('left-metrics').classList.add('hidden');
    document.getElementById('right-panel').style.display = 'none';

    var selectedWind = parseInt(document.getElementById('wind-select').value);
    var selectedTime = parseInt(document.getElementById('time-select').value);

    // 2. Filter data points matching chosen UI conditions
    // (Ensure your GeoJSON features include properties like 'wind' and 'time')
    var filteredPoints = allPoints.filter(f => {
      return f.properties.wind === selectedWind && f.properties.time === selectedTime;
    });

    if(filteredPoints.length === 0) return;

    // 3. Render matching points onto map
    var geoJsonLayer = L.geoJSON({ type: "FeatureCollection", features: filteredPoints }, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, bluePointStyle);
      },
      onEachFeature: function (feature, layer) {
        layer.on('click', function () {
          displayIncidentDetails(feature, selectedWind, selectedTime);
        });
      }
    }).addTo(pointsLayerGroup);

    // Adjust perspective bounding box frame
    map.fitBounds(geoJsonLayer.getBounds(), { padding: [20, 20] });
  }

  function displayIncidentDetails(pointFeature, wind, time) {
    polygonsLayerGroup.clearLayers();

    // 1. Find corresponding polygon using a shared relational ID (e.g., 'id' or 'site_id')
    var matchingPoly = allPolygons.find(p => 
      p.properties.id === pointFeature.properties.id &&
      p.properties.wind === wind &&
      p.properties.time === time
    );

    if (matchingPoly) {
      L.geoJSON(matchingPoly, { style: activeBurnStyle }).addTo(polygonsLayerGroup);
    }

    // 2. Populate dynamic values inside UI Container panels
    var props = pointFeature.properties;

    // Left Panel Metrics & Scaling Bars
    document.getElementById('left-metrics').classList.remove('hidden');
    document.getElementById('fire-type').innerText = props.fire_type || "DE SUPERFÍCIE";
    document.getElementById('intensity-val').innerText = props.intensity || "248.22";
    document.getElementById('speed-val').innerText = props.spread_speed || "2.6";
    
    // Relative visual tracking bar percentages (Modify 1000/20 maximum normalizations if needed)
    document.getElementById('intensity-bar').style.width = Math.min((props.intensity / 1000) * 100, 100) + "%";
    document.getElementById('speed-bar').style.width = Math.min((props.spread_speed / 20) * 100, 100) + "%";
    
    // Right Sidebar Panel Attributes
    document.getElementById('right-panel').style.display = 'block';
    document.getElementById('char-elev').innerText = props.elevation || "185,0";
    document.getElementById('char-slope').innerText = props.slope || "21";
    document.getElementById('char-orient').innerText = props.orientation || "Nord est";
    document.getElementById('char-dens').innerText = props.tree_density || "76,4";
    document.getElementById('char-canopy').innerText = props.canopy_height || "9,0";
    document.getElementById('char-under').innerText = props.understory_cover || "74";
    
    if(props.image_url) {
       document.getElementById('env-img').src = props.image_url;
    }
  }
</script>
