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
  /* Hard override of layout templates using explicit grid properties */
  .dashboard-container {
    display: grid !important;
    grid-template-columns: 260px 1fr 280px !important;
    gap: 15px !important;
    background-color: #1a425a !important; 
    color: #ffffff !important;
    padding: 20px !important;
    border-radius: 8px !important;
    font-family: Arial, sans-serif !important;
    width: 100% !important;
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
    background-color: #113043 !important;
  }

  .info-panel {
    background: rgba(255, 255, 255, 0.1) !important;
    padding: 15px !important;
    border-radius: 4px !important;
    max-height: 550px !important;
    overflow-y: auto !important;
    opacity: 0; /* Keeps layout tracking intact without collapsing columns */
    transition: opacity 0.2s ease;
  }

  .info-panel.visible {
    opacity: 1 !important;
  }

  .info-panel img {
    width: 100% !important;
    border-radius: 4px !important;
    margin-bottom: 15px !important;
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

  /* Responsive fallback stack for smaller screen sizes */
  @media (max-width: 1024px) {
    .dashboard-container {
      grid-template-columns: 1fr !important;
    }
    .info-panel {
      max-height: none !important;
      opacity: 1 !important;
      display: none;
    }
    .info-panel.visible {
      display: block !important;
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
  // Initialize Map Framework
  var map = L.map('map').setView([39.6, 2.7], 11);

  // Add standard base layer map tiles so you aren't loading onto a pitch-black container
  L.tileLayer('https://{s}.basemaps.cartocdn.com/rastertiles/voyager/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap'
  }).addTo(map);

  var allPoints = [];
  var allPolygons = [];
  
  var contoursLayerGroup = L.layerGroup().addTo(map);
  var polygonsLayerGroup = L.layerGroup().addTo(map);
  var pointsLayerGroup = L.layerGroup().addTo(map);

  var contourStyle = { color: "#8bb2cc", weight: 0.8, opacity: 0.4 };
  var bluePointStyle = { radius: 7, fillColor: "#5856d6", color: "#fff", weight: 2, opacity: 1, fillOpacity: 0.9 };
  var activeBurnStyle = { color: "#e63946", fillColor: "#e63946", weight: 3, fillOpacity: 0.6 };

  // Load GeoJSON content pipelines securely
  Promise.all([
    fetch('{{ "/assets/data/points.geojson" | relative_url }}').then(r => r.json()),
    fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}').then(r => r.json())
  ]).then(([pointsData, polygonsData]) => {
    allPoints = pointsData.features;
    allPolygons = polygonsData.features;
    
    // Initial paint of map assets
    updateSimulation();

    // Background asynchronous loading for minor contour details
    fetch('{{ "/assets/data/contours.geojson" | relative_url }}')
      .then(r => r.json())
      .then(contoursData => {
        var contourLayer = L.geoJSON(contoursData, { style: contourStyle }).addTo(contoursLayerGroup);
        if (contourLayer.getBounds().isValid()) {
            map.fitBounds(contourLayer.getBounds(), { padding: [20, 20] });
        }
      }).catch(err => {
        console.warn("Contours bypassed, auto-focusing on points framework.");
        executeFallbackZoom(pointsData);
      });

  }).catch(criticalErr => {
    console.error("Data tracking failure:", criticalErr);
  });

  function executeFallbackZoom(pointsData) {
    var tempLayer = L.geoJSON(pointsData);
    if(tempLayer.getBounds().isValid()) {
      map.fitBounds(tempLayer.getBounds(), { padding: [40, 40] });
    }
  }

  // CRITICAL FIX: Re-render everything fresh when dropdown controls tweak to reset broken clicks
  document.getElementById('wind-select').addEventListener('change', function() { updateSimulation(); });
  document.getElementById('time-select').addEventListener('change', function() { updateSimulation(); });

  function updateSimulation() {
    // Completely wipe all rendering components to prevent element stacking locks
    pointsLayerGroup.clearLayers();
    polygonsLayerGroup.clearLayers();
    
    document.getElementById('left-metrics').classList.add('hidden');
    document.getElementById('right-panel').classList.remove('visible');

    if(allPoints.length === 0) return;

    L.geoJSON({ type: "FeatureCollection", features: allPoints }, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, bluePointStyle);
      },
      onEachFeature: function (feature, layer) {
        // Safe, repeatable click processing pipeline
        layer.on('click', function (e) {
          L.DomEvent.stopPropagation(e);
          
          // Pull raw user inputs straight from real-time DOM states
          var currentWind = document.getElementById('wind-select').value;
          var currentTime = document.getElementById('time-select').value;
          
          displayIncidentDetails(feature, currentWind, currentTime);
        });
      }
    }).addTo(pointsLayerGroup);
  }

  function displayIncidentDetails(pointFeature, wind, time) {
    polygonsLayerGroup.clearLayers();

    // Match checking using string conversions to eliminate data-type filtering mismatches
    var pointId = String(pointFeature.properties.id || pointFeature.properties.site_id);

    var matchingPoly = allPolygons.find(p => {
      var matchId = String(p.properties.id || p.properties.site_id);
      var matchWind = String(p.properties.wind);
      var matchTime = String(p.properties.time);
      
      return matchId === pointId && matchWind === String(wind) && matchTime === String(time);
    });

    if (matchingPoly) {
      var burnGeoLayer = L.geoJSON(matchingPoly, { style: activeBurnStyle }).addTo(polygonsLayerGroup);
      if(burnGeoLayer.getBounds().isValid()) {
         map.fitBounds(burnGeoLayer.getBounds(), { padding: [30, 30] });
      }
    } else {
      console.warn("Data mismatch lookup: Point ID:", pointId, "| Wind Requested:", wind, "| Time Requested:", time);
      alert("No s'ha trobat cap polígon per a ID: " + pointId + " amb Vent: " + wind + " i Temps: " + time);
    }

    var props = pointFeature.properties;

    // --- Left Panels updates ---
    document.getElementById('left-metrics').classList.remove('hidden');
    document.getElementById('fire-type').innerText = props.fire_type || "DE SUPERFÍCIE";
    
    var intensity = parseFloat(props.intensity || 248.22);
    var speed = parseFloat(props.spread_speed || 2.6);
    
    document.getElementById('intensity-val').innerText = intensity;
    document.getElementById('speed-val').innerText = speed;
    
    document.getElementById('intensity-bar').style.width = Math.min((intensity / 1000) * 100, 100) + "%";
    document.getElementById('speed-bar').style.width = Math.min((speed / 20) * 100, 100) + "%";
    
    document.getElementById('critical-elements').innerText = props.critical_elements || "Plantes adaptades a la sequera -> Molt inflamables";

    // --- Right Panels updates ---
    document.getElementById('right-panel').classList.add('visible');
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
