---
title: "Incendis forestals a la serra de Tramuntana"
layout: splash
permalink: /tramuntana/
header:
  overlay_image: /assets/images/andratx-hero.jpg
  overlay_filter: 0.35
excerpt: "El foc és una pertorbació ecològica natural que regula els ecosistemes. En lloc de combatre'l, hauríem de (re-)aprendre a conviure-hi."

feature_row_incendis_extrems:
  - image_path: /assets/images/burn.jpg
    alt: "Bombers treballen per a controlar l'incendi forestal d'Andratx en 2013"
    title: "Incendis extrems"
    excerpt: "Els incendis esdevenen una amenaça crítica quan mostren un comportament extrem, caracteritzat per alta intensitat, propagació per capçades, llarga durada, gran extensió, complexitat de control i generació de focus secundaris."

feature_row_interficie_urbana:
  - image_path: /assets/images/interficie.jpg
    alt: "Bombers treballen en la interfície urbana-forestal d'Andratx en 2013"
    title: "La interfície urbana-forestal (IUF)"
    excerpt: "La zona d'interfície urbana-forestal representa un dels majors desafhaments en la gestió del foc a causa de la saturació dels serveis d'emergència, la propagació erràtica del foc i la presència d'elements de risc com bombones de butà."

feature_row_severitat:
  - image_path: /assets/images/post-incendi.jpg
    alt: "El paisatge després de l'incendi forestal d'Andratx en 2013"
    title: "Severitat de l'incendi"
    excerpt: "Els incendis d'alta severitat provoquen mortalitat vegetal massiva, degradació del sòl i alteració del cicle de l'aigua, la qual cosa compromet seriosament la regeneració natural postincendi."

feature_row_balears:
  - image_path: /assets/images/carboner.jpg
    alt: "L'ofici de carboner i la producció tradicional de carbó vegetal"
    title: "Els incendis a les Balears"
    excerpt: "L'abandonament del món rural i el cessament de pràctiques tradicionals com el carbonet han eliminat el mosaic paisatgístic, provocant un augment sense precedents de la biomassa i la continuïtat forestal."

feature_row_beneficis:
  - image_path: /assets/images/mosaic-biodiversitat.jpg
    alt: "Mosaic paisatgístic i obertura de nínxols ecològics postincendi"
    title: "El paper ecològic del foc"
    excerpt: "El foc actua com un agent modelador fonamental: estimula la germinació, recicla nutrients de manera eficient, promou la biodiversitat i regula l'estructura de la biomassa del sotabosc."

feature_row_adaptacions:
  - image_path: /assets/images/estepa-rebrot.jpg
    alt: "Una estepa rebrota després de l'incendi forestal d'Andratx"
    title: "Adaptació al règim d'incendis"
    excerpt: "Les plantes de l'ecosistema mediterrani han evolucionat conjuntament amb el foc mitjançant estratègies resilients com la germinació induïda per la calor o la rebrotació des d'estructures subterrànies."

feature_row_paradoxa:
  - image_path: /assets/images/paradoxa.jpg
    alt: "Esquema de producció de biomassa i gestió de l'extinció"
    title: "La paradoxa de l'extinció"
    excerpt: "La supressió total dels incendis de baixa intensitat acumula combustible de manera indefinida, assegurant l'aparició inevitable d'incendis de comportament extrem."

feature_row_gestio:
  - image_path: /assets/images/esquema-gestio.jpg
    alt: "Model conceptual de reducció de risc"
    title: "Gestió forestal adaptativa"
    excerpt: "Atès que la major part del territori de les Illes Balears és de propietat privada, cal un model de cogestió horitzontal que assumeixi el territori com un sistema socioecològic complex."
---

## Simulador d'Incendis Forestals

<div style="display: flex; height: 500px; font-family: sans-serif; margin-bottom: 30px;">
  <div style="width: 25%; background: #0A3054; color: white; padding: 20px; box-sizing: border-box;">
    <h3>INSTRUCCIONS:</h3>
    <ol style="padding-left: 20px; font-size: 14px;">
      <li>Selecciona la velocitat del vent i el temps</li>
      <li>Fes click a qualsevol punt blau</li>
      <li>Fes zoom per veure l'àrea cremada en detall</li>
    </ol>
    
    <label for="windSpeed" style="display:block; margin-top:20px;">Velocitat del vent (km/h):</label>
    <select id="windSpeed" style="width:100%; margin-top:5px; padding:5px;">
      <option value="10" selected>10</option>
      <option value="20">20</option>
      <option value="30">30</option>
    </select>

    <label for="burnTime" style="display:block; margin-top:20px;">Temps (min):</label>
    <select id="burnTime" style="width:100%; margin-top:5px; padding:5px;">
      <option value="120" selected>120</option>
      <option value="240">240</option>
    </select>
  </div>

  <div id="map" style="width: 75%; height: 100%;"></div>
</div>

<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script>
  const map = L.map('map').setView([39.67, 2.48], 13);

  L.tileLayer('https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png', {
    attribution: '&copy; OpenStreetMap contributors &copy; CARTO'
  }).addTo(map);

  let burnLayer;
  let pointsData;
  let polygonsData;

  Promise.all([
    fetch('/assets/data/points.geojson').then(res => res.json()).catch(() => null),
    fetch('/assets/data/burn_polygons.geojson').then(res => res.json()).catch(() => null)
  ]).then(([pointsRes, polygonsRes]) => {
    if(!pointsRes || !polygonsRes) return;
    pointsData = pointsRes;
    polygonsData = polygonsRes;

    L.geoJSON(pointsData, {
      pointToLayer: function (feature, latlng) {
        return L.circleMarker(latlng, {
          radius: 6,
          fillColor: "#4A61E3",
          color: "#fff",
          weight: 1,
          opacity: 1,
          fillOpacity: 0.8
        });
      }
    }).addTo(map);

    updateBurnPolygon();
  });

  function updateBurnPolygon() {
    if (burnLayer) { map.removeLayer(burnLayer); }
    if (!polygonsData) return;

    const selectedWind = document.getElementById('windSpeed').value;
    const selectedTime = document.getElementById('burnTime').value;

    burnLayer = L.geoJSON(polygonsData, {
      filter: function(feature) {
        return feature.properties.wspd === selectedWind && 
               feature.properties.time === selectedTime;
      },
      style: {
        color: "#E34A4A",
        fillColor: "#E34A4A",
        fillOpacity: 0.5,
        weight: 2
      }
    }).addTo(map);
  }

  document.getElementById('windSpeed').addEventListener('change', updateBurnPolygon);
  document.getElementById('burnTime').addEventListener('change', updateBurnPolygon);
</script>

---

{% include feature_row id="feature_row_incendis_extrems" type="right" %}

---

{% include feature_row id="feature_row_interficie_urbana" type="right" %}

---

{% include feature_row id="feature_row_severitat" type="right" %}

---

{% include feature_row id="feature_row_balears" type="right" %}

---

{% include feature_row id="feature_row_beneficis" type="right" %}

---

{% include feature_row id="feature_row_adaptacions" type="right" %}

---

{% include feature_row id="feature_row_paradoxa" type="right" %}

---

{% include feature_row id="feature_row_gestio" type="right" %}
