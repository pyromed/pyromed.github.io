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
  /* BREAKOUT THEME OVERRIDE: Forces the container to be wider than the standard narrow theme column */
  @media (min-width: 1025px) {
    .dashboard-container {
      width: calc(100% + 160px) !important;
      margin-left: -80px !important;
    }
  }

  /* Robust 3-column CSS Grid Layout */
  .dashboard-container {
    display: grid !important;
    grid-template-columns: 250px 1fr 280px !important;
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
    min-width: 0 !important; /* Prevents leaflet wrapper from distorting grid track */
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
    display: block !important;
    visibility: hidden; /* Keeps slot allocation alive without showing template placeholders */
    opacity: 0;
    transition: opacity 0.2s ease;
  }

  .info-panel.visible {
    visibility: visible !important;
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

  /* Smooth stacking rules for smaller device windows */
  @media (max-width: 1024px) {
    .dashboard-container {
      grid-template-columns: 1fr !important;
    }
    .info-panel {
      max-height: none !important;
      display: none !important;
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
      <p><strong>Elements crítics:</strong><br><small id="critical-elements">-
