---
title: "Incendis forestals a la serra de Tramuntana"
layout: splash
permalink: /tramuntana/
header:
  overlay_image: /assets/images/andratx-hero.jpg
  overlay_filter: 0.35
excerpt: "El foc és una pertorbació ecològica natural que regula els ecosistemes. En lloc de combatre'l, hauríem de (re-)aprendre a conviure-hi."
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

## Incendis extrems

Els incendis esdevenen un problema greu quan presenten un **comportament extrem**, caracteritzat per:
* **Alta intensitat:** generen una gran quantitat d'energia calorífica.
* **Propagació per la capçada**.
* **Llarga durada:** persistents durant dies o fins i tot mesos.
* **Gran extensió** ($\ge 500$ hectàrees).
* **Dificultat de control:** es propaguen de manera erràtica i imprevisible, representant un gran risc per als equips d'extinció i les poblacions.
* **Creen nous incendis** a partir de fragments d'escorça cremada i inclús poden crear la seva pròpia meteorologia.

<img src="/assets/images/burn.jpg" alt="Incendis extrems" style="width:100%; border-radius:6px; margin: 15px 0;">
*Imatge: Bombers treballen per a controlar l'incendi forestal d'Andratx en 2013. Autor: NA Font: Ara Balears*

---

## La zona d'interfase urbana-forestal

La **zona d'interfase urbana-forestal** representa un gran desafiament en la **gestió d'incendis forestals**. Les raons per les quals aquestes zones compliquen les feines d'extinció i augmenten el risc per als bombers forestals són les següents:
* **Superen les capacitats dels bombers**, ja que cal coordinar diverses tasques simultànies, com la **supressió de l'incendi forestal**, l'**evacuació de la comunitat** i la **protecció de les infraestructures**.
* **Propagació erràtica i impredictible** especialment quan interactuen els combustibles forestals amb elements urbans.
* **Elements perillosos a les llars:** Alguns habitatges poden contenir **elements explosius**, com **bombones de butà**, o materials que **augmenten la transferència de calor**, com finestres obertes.

<img src="/assets/images/interficie.jpg" alt="Interfase urbana forestal" style="width:100%; border-radius:6px; margin: 15px 0;">
*Imatge: Bombers treballen per a controlar l'incendi forestal d'Andratx en 2013. Autor: NA Font: Ultima Hora*

---

## Severitat de l'incendi

Els incendis de **comportament extrem** solen ser d'**alta severitat**, ja que causen una gran mortalitat vegetal, una intensa **degradació del sòl** i alteren el **cicle de l'aigua**. Aquestes condicions dificulten considerablement la **regeneració natural post-incendi**, perquè la destrucció de la vegetació i la pèrdua de matèria orgànica al sòl afecten la capacitat de l'ecosistema per recuperar-se.

<img src="/assets/images/post-incendi.jpg" alt="Post incendi" style="width:100%; border-radius:6px; margin: 15px 0;">
*Imatge: El paisatge després de l'incendi forestal d'Andratx en 2013. Autor: NA Font: Diario de Mallorca*

---

## Els incendis a les Balears

L'ús cultural del foc va ser fonamental a les Illes Balears per a la neteja de terres, la millora dels cultius i les pastures, així com per a la producció tradicional de carbó vegetal. 

L'augment de la biomassa i la continuïtat forestal, juntament amb la supressió total dels incendis forestals i l'increment de l'aridesa provocada pel canvi climàtic, han agreujat significativament el risc d'incendis a la conca mediterrània en les últimes dècades.

<img src="/assets/images/carboner.jpg" alt="Carboner" style="width:100%; border-radius:6px; margin: 15px 0;">
*Fotografia: L'ofici de carboner i la producció de carbó vegetal. Font: IPCIME*

---

## Beneficis del foc a l'ecosistema

Quan el foc es manté en un règim natural, aporta dinàmiques positives:
* **Regeneració:** estimula la germinació de llavors de plantes germinadores.
* **Cicle de nutrients:** allibera nutrients de la matèria vegetal cremada, enriquint el sòl.
* **Biodiversitat:** Els incendis obren espais per a noves plantes i ajuden a mantenir la diversitat ecològica.
* **Control de vegetació:** Els incendis redueixen la densitat de vegetació, prevenint incendis més grans.

<img src="/assets/images/mosaic-biodiversitat.jpg" alt="Beneficis del foc" style="width:100%; border-radius:6px; margin: 15px 0;">

---

## Les plantes estan adaptades al règim d'incendis

Les comunitats vegetals del bosc mediterrani han desenvolupat una elevada resiliència als incendis, gràcies a dos mecanismes clau de regeneració post-incendi: **germinació** i **rebrotació**.

> **La resiliència al foc és la capacitat d'un ecosistema per recuperar-se després d'un incendi, tornant a la seva estructura, funció i dinàmiques ecològiques prèvies a l'incendi.**

<img src="/assets/images/estepa-rebrot.jpg" alt="Estepa rebrotant" style="width:100%; border-radius:6px; margin: 15px 0;">
*Fotografia: Una estepa rebrota després de l'incendi forestal d'Andratx en 2013.*

---

## La paradoxa de l'extinció

La supressió dels incendis forestals no és viable, ni ecològicamente desitjable, com afirmen diversos experts en la matèria (Moreira et al., 2020).

> **Paradoxalment, la supressió d'incendis forestals assegura l'aparició d'incendis de comportament extrem.**

És crucial **replantejar els objectius de la gestió d'incendis**, passant de la simple extinció dels focs a una visió més adaptada a la realitat natural dels ecosistemes. Aquest enfocament implica **(re-)aprendre a conviure amb els incendis forestals**.

<img src="/assets/images/paradoxa.jpg" alt="Paradoxa de l'extinció" style="width:100%; border-radius:6px; margin: 15px 0;">
*Bibliografia: Moreira F, Ascoli D, Safford H, et al. 2020. Wildfire Management in Mediterranean-type regions: paradigm change needed. Environmental Research Letters 15, 011001.*

---

## Gestió forestal

Atès que gran part de les Balears és de propietat privada i molt fragmentada en petites parcel·les, és fonamental que s'adopti un enfocament de cogestió forestal.

> **Hem de reconèixer les Balears com un sistema on l'humà i l'entorn estan estretament vinculats i a on la gestió és una responsabilitat compartida.**

<img src="/assets/images/esquema-gestio.jpg" alt="Esquema gestió forestal" style="width:100%; border-radius:6px; margin: 15px 0;">
*Figura: Model conceptual que destaca els principals objectius i les accions per reduir el risc de pèrdua d'habitatge com a conseqüència d'incendis forestals. Figura adaptada de: Calkin DE, Cohen J.D., Finney M.A., Thompson M.P. 2013.*
