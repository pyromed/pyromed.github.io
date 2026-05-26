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
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js" crossorigin=""></script>

<div id="map" style="width: 100%; height: 550px; border-radius: 4px; box-shadow: 0 2px 5px rgba(0,0,0,0.15); margin-bottom: 20px;"></div>

<script>
  // 1. Initialize the map (centered broadly around the western Mediterranean)
  var map = L.map('map').setView([39.6, 2.7], 9);

  // 2. Add a clean background map layer (CartoDB Positron is great for data overlays)
  L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
  }).addTo(map);

  // 3. Style functions for your R layers
  var polygonStyle = {    "color": "#e65c00",    "weight": 2,    "fillOpacity": 0.4  };
  var pointMarkerOptions = {    radius: 6,    fillColor: "#ff0000",    color: "#000",    weight: 1,    opacity: 1,    fillOpacity: 0.8  };

  // 4. Fetch and display Burn Polygons
  fetch('{{ "/assets/data/burn_polygons.geojson" | relative_url }}')
    .then(response => response.json())
    .then(data => {
      var polygonLayer = L.geoJSON(data, { style: polygonStyle }).addTo(map);
      // Automatically zoom the map to fit your R data bounds
      map.fitBounds(polygonLayer.getBounds());
    })
    .catch(err => console.error('Error carregant polígons:', err));

  // 5. Fetch and display Ignition Points
  fetch('{{ "/assets/data/points.geojson" | relative_url }}')
    .then(response => response.json())
    .then(data => {
      L.geoJSON(data, {
        pointToLayer: function (feature, latlng) {
          return L.circleMarker(latlng, pointMarkerOptions);
        }
      }).addTo(map);
    })
    .catch(err => console.error('Error carregant punts:', err));
</script>

### Exploració de Dades
* **Polígons de Crema (`burn_polygons.geojson`):** Representen el perímetre estimat de propagació extret del model d'R.
* **Punts d'Ignició (`points.geojson`):** Indiquen el punt d'inici originari registrat o utilitzat com a base meteorològica.

> **Nota de rendiment:** Si els fitxers d'R generats tenen milers de coordenades complexes, pot ser que triguin un instant a carregar-se completament en dispositius mòbils.
