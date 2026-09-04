---
title: "Simulador d'Àrea Cremada"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Aquest espai cartogràfic interactiu mostra els punts d'ignició i les àrees afectades associats a una simulació d'incendis.

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" crossorigin="" />

<style>
  /* BREAKOUT THEME OVERRIDE */
  @media (min-width: 1025px) {
    .dashboard-container {
      width: calc(100% + 160px) !important;
      margin-left: -80px !important;
    }
  }

  /* 2-column CSS Grid Layout */
  .dashboard-container {
    display: grid !important;
    grid-template-columns: 250px 1fr !important;
    gap: 15px !important;
    background-color: #1a425a !important; 
    color: #ffffff !important;
    padding: 20px !important;
    border-radius: 8px !important;
    font-family: Arial, sans-serif !important;
    box-sizing: border-box !important;
  }

  .sidebar-controls {
    display: flex !important;
    flex-direction: column !important;
    gap: 15px !important;
  }

  .control-group {
    display: flex !important;
    flex-direction: column !important;
    gap: 5px !important;
  }

  .control-group label {
    font-weight: bold !important;
    font-size: 0.9em !important;
  }

  .control-group select {
    padding: 8px !important;
    border-radius: 4px !important;
    border: 1px solid #ccc !important;
    background-color: #fff !important;
    color: #333 !important;
    width: 100% !important;
  }

  .map-container {
    height: 550px !important;
    position: relative !important;
    min-width: 0 !important;
  }

  #map {
    width: 100% !important;
    height: 100% !important;
    border-radius: 4px !important;
    background-color: #112233 !important; 
  }

  /* Custom styling for the Reset View UI button inside Leaflet container */
  .leaflet-control-resetview {
    background: #ffffff;
    color: #333333;
    padding: 6px 10px;
    font-weight: bold;
    font-size: 12px;
    border-radius: 4px;
    cursor: pointer;
    box-shadow: 0 1px 5px rgba(0,0,0,0.4);
    border: none;
  }
  .leaflet-control-resetview:hover {
    background: #f4f4f4;
    color: #000000;
  }

  .metric-bar-container {
    background: #444 !important;
    border-radius: 4px !important;
    height: 15px !important;
    width: 100% !important;
    margin-top: 5px !important;
  }

  .metric-bar {
    background: #e65c00 !important;
    height: 100% !important;
    border-radius: 4px !important;
    transition: width 0.3s ease !important;
    width: 0%;
  }

  .hidden { display: none !important; }

  @media (max-width: 1024px) {
    .dashboard-container {
      grid-template-columns: 1fr !important;
    }
  }
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
        <option value="15">15</option>
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

</div>

<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" crossorigin=""></script>

<script>
  var map = L.map('map', {
    crs: L.CRS.Simple,
    minZoom: -5
  });

  var allPoints = [];
  var allPolygons = [];
  var initialBounds = null; // Stores global data boundaries for the reset layout view trigger
  
  // Layer Groups
  var demLayerGroup = L.layerGroup().addTo(map);
  var wallsLayerGroup = L.layerGroup().addTo(map);
  var housingLayerGroup = L.layerGroup().addTo(map);
  var polygonsLayerGroup = L.layerGroup().addTo(map);
  var pointsLayerGroup = L.layerGroup().addTo(map);

  // Thicker Walls Style applied
  var wallStyle = { color: "#a18262", weight: 3.6, opacity: 0.8 };
  var housingStyle = { color: "#aaaaaa", fillColor: "#cccccc", weight: 1, fillOpacity: 0.4 };
  
  var bluePointStyle = { radius: 7, fillColor: "#5856d6", color: "#fff", weight: 2, opacity: 1, fillOpacity: 0.9 };
  var activeBurnStyle = { color: "#e63946", fillColor: "#e63946", weight: 3, fillOpacity: 0.6 };

  Promise.all([
    fetch('{{ "/assets/data/points.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/walls.geojson" | relative_url }}').then(r => r.json()).catch(() => null),
    fetch('{{ "/assets/data/housing.geojson" | relative_url }}').then(r => r.json()).catch(() => null)
  ]).then(([pointsData, polygonsData, wallsData, housingData]) => {
    allPoints = pointsData.features;
    allPolygons = polygonsData.features;
    
    updateSimulation();

    // Calculate baseline coordinate boundaries
    var dataBounds = L.geoJSON(pointsData).getBounds();
    if(dataBounds.isValid()) {
      initialBounds = dataBounds; // Save global reference globally
      map.fitBounds(initialBounds, { padding: [40, 40] });
      
      // Optional DEM Overlay matching configuration setup
      var demImageUrl = '{{ "/assets/images/dem_background.png" | relative_url }}';
      L.imageOverlay(demImageUrl, dataBounds, { opacity: 0.4 }).addTo(demLayerGroup);
    }

    if (wallsData) {
      L.geoJSON(wallsData, { style: wallStyle }).addTo(wallsLayerGroup);
    }

    if (housingData) {
      L.geoJSON(housingData, { style: housingStyle }).addTo(housingLayerGroup);
    }

    // CREATE RESET VIEW BUTTON UI: Programmatically attach custom floating interface button
    var resetControl = L.Control.extend({
      options: { position: 'topleft' },
      onAdd: function (map) {
        var btn = L.DomUtil.create('button', 'leaflet-control-resetview');
        btn.innerHTML = '🌍 Restablir Vista';
        btn.title = 'Torna a la vista general de la simulació';
        
        L.DomEvent.on(btn, 'click', function (e) {
          L.DomEvent.stopPropagation(e);
          if (initialBounds) {
            map.fitBounds(initialBounds, { padding: [40, 40] });
          }
        });
        return btn;
      }
    });
    map.addControl(new resetControl());

  }).catch(criticalErr => {
    console.error("Data framework loading failure:", criticalErr);
  });

  document.getElementById('wind-select').addEventListener('change', function() { updateSimulation(); });
  document.getElementById('time-select').addEventListener('change', function() { updateSimulation(); });

  function getFeatureId(f) {
    if (!f || !f.properties) return null;
    var p = f.properties;
    var potentialId = p.PlotID ?? p.plotid ?? p.PlotId ?? p.id ?? p.ID ?? p.Id ?? p.site_id ?? p.FID;
    return potentialId !== undefined && potentialId !== null ? String(potentialId).trim() : null;
  }

  function updateSimulation() {
    pointsLayerGroup.clearLayers();
    polygonsLayerGroup.clearLayers();
    document.getElementById('left-metrics').classList.add('hidden');

    if(allPoints.length === 0) return;

    L.geoJSON({ type: "FeatureCollection", features: allPoints }, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, bluePointStyle);
      },
      onEachFeature: function (feature, layer) {
        layer.on('click', function (e) {
          L.DomEvent.stopPropagation(e);
          var currentWind = document.getElementById('wind-select').value;
          var currentTime = document.getElementById('time-select').value;
          displayIncidentDetails(feature, currentWind, currentTime);
        });
      }
    }).addTo(pointsLayerGroup);
  }

  function displayIncidentDetails(pointFeature, wind, time) {
    polygonsLayerGroup.clearLayers();
    var pointId = getFeatureId(pointFeature);

    var matchingPoly = allPolygons.find(p => {
      var matchId = getFeatureId(p);
      var rawWind = p.properties ? (p.properties.windspeed ?? p.properties.wspd ?? p.properties.wind ?? p.properties.WIND) : null;
      var rawTime = p.properties ? (p.properties.time ?? p.properties.TIME ?? p.properties.Time) : null;
      
      var matchWind = rawWind !== undefined && rawWind !== null ? String(parseFloat(rawWind)) : "";
      var matchTime = rawTime !== undefined && rawTime !== null ? String(rawTime).trim() : "";
      
      return matchId === pointId && matchWind === String(parseFloat(wind)) && matchTime === String(time).trim();
    });

    if (matchingPoly) {
      // RENDERS BURN POLYGON ON MAP
      L.geoJSON(matchingPoly, { style: activeBurnStyle }).addTo(polygonsLayerGroup);
      
      // AUTO ZOOM REMOVED: map.fitBounds(...) was removed from here so the viewport stays exactly where it is!
    } else {
      console.warn("Lookup mismatch! Point ID:", pointId, "Wind:", wind, "Time:", time);
      alert("No s'ha trobat cap polígon per a ID: " + pointId + " amb Vent: " + wind + " i Temps: " + time);
    }

    var props = pointFeature.properties;
    document.getElementById('left-metrics').classList.remove('hidden');
    document.getElementById('fire-type').innerText = props.fire_type || "DE SUPERFÍCIE";
    
    var intensity = parseFloat(props.intensity || 248.22);
    var speed = parseFloat(props.spread_speed || 2.6);
    
    document.getElementById('intensity-val').innerText = intensity;
    document.getElementById('speed-val').innerText = speed;
    
    document.getElementById('intensity-bar').style.width = Math.min((intensity / 1000) * 100, 100) + "%";
    document.getElementById('speed-bar').style.width = Math.min((speed / 20) * 100, 100) + "%";
    document.getElementById('critical-elements').innerText = props.critical_elements || "Plantes adaptades a la sequera -> Molt inflamables";
  }
</script>

---
<div class="page-navigation">
  <a href="/simulations/" class="btn btn--primary">
    ← 	Com podem anticipar el comportament d’un incendi?
  </a>

  <a href="/simulador/" class="btn btn--primary">
    Simulador →
  </a>
</div>
