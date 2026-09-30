---
title: "Simulador de propagació"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Tria la vegetació, el vent, el pendent i el temps des de l'ignició. Els dos mapes mostren el mateix foc: a l'esquerra sense mur i a la dreta amb un mur de pedra, perquè es vegi què atura i què no.

<style>
  .sim { display:grid; grid-template-columns:250px 1fr; gap:16px; background:#1a425a; color:#fff;
         padding:20px; border-radius:8px; font-family:Arial,sans-serif; box-sizing:border-box; }
  @media (min-width:1025px) { .sim { width:calc(100% + 160px); margin-left:-80px; } }
  @media (max-width:1024px) { .sim { grid-template-columns:1fr; } }
  .sim label { display:block; font-weight:bold; font-size:.9em; margin-bottom:4px; }
  .sim select { width:100%; padding:8px; border-radius:4px; border:1px solid #ccc; background:#fff; color:#333; }
  .sim .field { margin-bottom:12px; }
  .sim .out { border-top:1px solid rgba(255,255,255,.25); padding-top:10px; }
  .sim .row { display:flex; justify-content:space-between; align-items:baseline; gap:8px; margin:9px 0 2px; font-size:.9em; }
  .sim .row b { font-size:1.2em; white-space:nowrap; }
  .sim .bar { background:#444; border-radius:4px; height:10px; }
  .sim .bar i { display:block; height:100%; width:0; background:#e65c00; border-radius:4px; transition:width .3s; }
  .sim .stage { min-width:0; }
  .sim .maps { display:grid; grid-template-columns:repeat(auto-fit,minmax(280px,1fr)); gap:12px; }
  .sim figure { margin:0; }
  .sim figcaption { font-weight:bold; margin-bottom:6px; }
  .sim svg { width:100%; height:auto; aspect-ratio:1/1; background:#112233; border-radius:4px; display:block; }
  .sim .status { font-size:.85em; margin-top:6px; min-height:2.6em; }
  .sim .legend { font-size:.8em; margin-top:10px; opacity:.85; }
  .sim .legend span { display:inline-block; width:12px; height:12px; border-radius:2px; margin:0 4px -2px 10px; }
  @media (prefers-reduced-motion:reduce) { .sim .bar i { transition:none; } }
</style>

<div class="sim">
  <div>
    <div class="field">
      <label for="dens">Densitat de vegetació</label>
      <select id="dens">
        <option value="0">Baixa</option>
        <option value="1" selected>Moderada</option>
        <option value="2">Alta</option>
      </select>
    </div>
    <div class="field">
      <label for="wind">Velocitat del vent</label>
      <select id="wind">
        <option value="10">Baixa (10 km/h)</option>
        <option value="20" selected>Moderada (20 km/h)</option>
        <option value="30">Alta (30 km/h)</option>
      </select>
    </div>
    <div class="field">
      <label for="dir">El vent bufa des del</label>
      <select id="dir">
        <option value="0">Nord</option>
        <option value="45">Nord-est</option>
        <option value="90">Est</option>
        <option value="135">Sud-est</option>
        <option value="180" selected>Sud</option>
        <option value="225">Sud-oest</option>
        <option value="270">Oest</option>
        <option value="315">Nord-oest</option>
      </select>
    </div>
    <div class="field">
      <label for="slope">Pendent (puja cap al nord)</label>
      <select id="slope">
        <option value="0">Pla</option>
        <option value="1" selected>Moderat (≈20 %)</option>
        <option value="2">Fort (≈40 %)</option>
      </select>
    </div>
    <div class="field">
      <label for="time">Temps des de l'ignició</label>
      <select id="time">
        <option value="15">15 min</option>
        <option value="30">30 min</option>
        <option value="60" selected>60 min</option>
        <option value="120">120 min</option>
      </select>
    </div>
    <div class="field">
      <label for="wall">Mur (mapa de la dreta)</label>
      <select id="wall">
        <option value="up" selected>A dalt del pendent (nord)</option>
        <option value="down">A baix del pendent (sud)</option>
      </select>
    </div>

    <div class="out">
      <div class="row"><span>Velocitat de propagació</span><b><span id="o-ros">0</span> m/min</b></div>
      <div class="bar"><i id="b-ros"></i></div>
      <div class="row"><span>Intensitat</span><b><span id="o-fli">0</span> kW/m</b></div>
      <div class="bar"><i id="b-fli"></i></div>
      <div class="row"><span>Llargada de flama</span><b><span id="o-fl">0</span> m</b></div>
      <div class="bar"><i id="b-fl"></i></div>
      <div class="row"><span>Distància de focus secundaris</span><b><span id="o-spot">0</span> m</b></div>
      <div class="bar"><i id="b-spot"></i></div>
      <div class="row"><span>Vent efectiu (vent + pendent)</span><b><span id="o-ue">0</span> km/h</b></div>
    </div>
  </div>

  <div class="stage">
    <div class="maps">
      <figure>
        <figcaption>Sense mur</figcaption>
        <svg id="svg-a" role="img" aria-label="Foc sense mur"></svg>
        <div class="status" id="st-a"></div>
      </figure>
      <figure>
        <figcaption id="cap-b">Amb mur</figcaption>
        <svg id="svg-b" role="img" aria-label="Foc amb mur"></svg>
        <div class="status" id="st-b"></div>
      </figure>
    </div>
    <div class="legend">
      <span style="background:#e63946"></span>Foc de superfície
      <span style="background:#f4a261"></span>Abast dels focus secundaris
      <span style="background:#d6b88a"></span>Mur
    </div>
  </div>
</div>

<script>
(function () {
  // ---- Valors de referència: SUBSTITUEIX-LOS pels resultats de firebehavioR ----
  // Columnes = vent efectiu (km/h) a WINDS. Files = densitat baixa, moderada, alta.
  var WINDS = [0, 10, 20, 30];
  var ROS  = [[0.6, 1.5, 3, 5],       [1.2, 3, 6, 10],       [2, 5, 10, 16]];          // m/min
  var FLI  = [[50, 150, 350, 700],    [150, 500, 1200, 2500],[400, 1200, 3000, 6000]]; // kW/m
  var SPOT = [[20, 60, 120, 200],     [40, 120, 250, 400],   [60, 200, 400, 650]];     // m (cfis)
  var SLOPE_KMH = [0, 8, 16];   // vent equivalent del pendent (pla, moderat, fort)
  var LB_MAX = 8;               // Finney (1998)
  var WALL_DIST = 300;          // distància del mur al punt d'ignició (m)
  var TIMES = [15, 30, 60, 120];

  var $ = function (id) { return document.getElementById(id); };

  function interp(arr, u) {  // interpolació lineal, amb extrapolació més enllà de 30 km/h
    if (u <= 0) return arr[0];
    for (var i = 1; i < WINDS.length; i++)
      if (u <= WINDS[i]) return arr[i - 1] + (arr[i] - arr[i - 1]) * (u - WINDS[i - 1]) / (WINDS[i] - WINDS[i - 1]);
    var n = WINDS.length - 1;
    return arr[n] + (arr[n] - arr[n - 1]) * (u - WINDS[n]) / (WINDS[n] - WINDS[n - 1]);
  }

  function ellipse(ros, ue) {
    var mph = ue / 1.609344;
    var lbRaw = 0.936 * Math.exp(0.2566 * mph) + 0.461 * Math.exp(-0.1548 * mph) - 0.397;
    var lb = Math.min(Math.max(lbRaw, 1), LB_MAX);
    var s = Math.sqrt(lb * lb - 1);
    var bros = ros / ((lb + s) / (lb - s));
    var a = (ros + bros) / 2;
    return { a: a, e: (ros - bros) / 2 / a };
  }

  // Perímetre del foc i abast dels focus secundaris (m). wallY: y del mur (y negativa = nord) o null.
  function shape(P, t, wallY) {
    var surf = [], spot = [], reached = false, crossed = false;
    for (var off = 0; off <= 360; off += 5) {
      var th = off * Math.PI / 180;
      var rd = P.el.a * (1 - P.el.e * P.el.e) / (1 - P.el.e * Math.cos(th));
      var phi = (P.heading + off) * Math.PI / 180;
      var x = rd * t * Math.sin(phi), y = -rd * t * Math.cos(phi);
      if (wallY !== null && (wallY < 0 ? y <= wallY : y >= wallY)) { y = wallY; reached = true; }
      var sd = P.spot * rd / P.ros;             // escalat direccional, com a l'R
      var sx = x + sd * Math.sin(phi), sy = y - sd * Math.cos(phi);
      if (wallY !== null && (wallY < 0 ? sy < wallY : sy > wallY)) crossed = true;
      surf.push([x, y]); spot.push([sx, sy]);
    }
    return { surf: surf, spot: spot, reached: reached, crossed: crossed };
  }

  function path(pts) {
    return pts.map(function (p, i) { return (i ? "L" : "M") + p[0].toFixed(1) + " " + p[1].toFixed(1); }).join("") + "Z";
  }
  function fmt(n) { return n >= 100 ? Math.round(n).toLocaleString("ca") : (Math.round(n * 10) / 10).toLocaleString("ca"); }
  function arrow(x, y, deg, len, k, color) {
    return '<g transform="translate(' + x + ' ' + y + ') rotate(' + deg + ')"><path d="M0 ' + len + 'V-' + len +
      'm-' + 2 * k + ' ' + 2.5 * k + 'l' + 2 * k + ' -' + 2.5 * k + 'l' + 2 * k + ' ' + 2.5 * k +
      '" fill="none" stroke="' + color + '" stroke-width="' + 0.8 * k + '"/></g>';
  }

  function draw(svg, id, P, view, t, wallY, slope, windTo) {
    var k = view.span / 100, x0 = view.cx - view.span / 2, y0 = view.cy - view.span / 2;
    svg.setAttribute("viewBox", [x0, y0, view.span, view.span].join(" "));
    var h = "";
    if (slope > 0)
      h += '<defs><linearGradient id="g' + id + '" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="#fff" stop-opacity="' +
           0.1 * slope + '"/><stop offset="1" stop-color="#fff" stop-opacity="0"/></linearGradient></defs>' +
           '<rect x="' + x0 + '" y="' + y0 + '" width="' + view.span + '" height="' + view.span + '" fill="url(#g' + id + ')"/>' +
           '<text x="' + view.cx + '" y="' + (y0 + 5 * k) + '" font-size="' + 3 * k + '" fill="#fff" fill-opacity=".6" text-anchor="middle" font-family="Arial">amunt ↑</text>' +
           '<text x="' + view.cx + '" y="' + (y0 + view.span - 2 * k) + '" font-size="' + 3 * k + '" fill="#fff" fill-opacity=".6" text-anchor="middle" font-family="Arial">avall ↓</text>';

    if (wallY !== null)
      h += '<path d="M' + x0 + ' ' + wallY + 'h' + view.span + '" stroke="#d6b88a" stroke-width="' + 1.3 * k + '"/>';

    var cur = shape(P, t, wallY);
    h += '<path d="' + path(cur.spot) + '" fill="#f4a261" fill-opacity="0.22" stroke="#f4a261" stroke-width="' + 0.4 * k + '" stroke-dasharray="' + 2 * k + ' ' + 1.5 * k + '"/>';
    TIMES.forEach(function (tt) {
      if (tt === t) return;
      var s = shape(P, tt, wallY);
      h += '<path d="' + path(s.surf) + '" fill="none" stroke="#fff" stroke-opacity=".35" stroke-width="' + 0.3 * k + '"/>';
      h += '<text x="' + (s.surf[0][0] + 1.2 * k) + '" y="' + s.surf[0][1] + '" font-size="' + 2.8 * k + '" fill="#fff" fill-opacity=".6" font-family="Arial">' + tt + ' min</text>';
    });
    h += '<path d="' + path(cur.surf) + '" fill="#e63946" fill-opacity="0.65" stroke="#e63946" stroke-width="' + 0.6 * k + '"/>';
    h += '<circle r="' + 1.3 * k + '" fill="#5856d6" stroke="#fff" stroke-width="' + 0.35 * k + '"/>';
    if (wallY !== null)
      h += '<text x="' + (x0 + 3 * k) + '" y="' + (wallY + (wallY < 0 ? -1.5 : 4) * k) + '" font-size="' + 3 * k + '" fill="#d6b88a" font-family="Arial">mur</text>';

    // Escala, nord i vent
    var target = view.span / 4, len = 50;
    [50, 100, 250, 500, 1000, 2500, 5000].forEach(function (n) { if (n <= target) len = n; });
    var sx = x0 + 4 * k, sy = y0 + view.span - 7 * k;
    h += '<path d="M' + sx + ' ' + sy + 'h' + len + '" stroke="#fff" stroke-width="' + 0.8 * k + '"/>' +
         '<text x="' + sx + '" y="' + (sy - 1.5 * k) + '" font-size="' + 3 * k + '" fill="#fff" font-family="Arial">' + (len >= 1000 ? len / 1000 + " km" : len + " m") + '</text>';
    var nx = x0 + view.span - 6 * k, ny = y0 + 12 * k;
    h += '<text x="' + nx + '" y="' + (ny - 7 * k) + '" font-size="' + 3.4 * k + '" fill="#fff" text-anchor="middle" font-family="Arial">N</text>' + arrow(nx, ny, 0, 4 * k, k * 0.6, "#fff");
    h += arrow(x0 + 10 * k, y0 + 14 * k, windTo, 6 * k, k, "#4cc9f0") +
         '<text x="' + (x0 + 10 * k) + '" y="' + (y0 + 24 * k) + '" font-size="' + 3 * k + '" fill="#4cc9f0" text-anchor="middle" font-family="Arial">vent</text>';
    svg.innerHTML = h;
    return cur;
  }

  function wallMsg(s, up) {
    var where = up ? "a dalt" : "a baix";
    if (!s.reached && !s.crossed) return "El foc encara no arriba al mur (" + where + ") ni els focus secundaris.";
    if (!s.reached) return "El foc de superfície no arriba al mur, però els focus secundaris sí que el poden creuar.";
    if (s.crossed) return "El mur atura el foc de superfície, però els focus secundaris el poden creuar.";
    return "El mur atura el foc de superfície i els focus secundaris no arriben a l'altra banda.";
  }

  function update() {
    var d = +$("dens").value, u = +$("wind").value, t = +$("time").value;
    var from = +$("dir").value, slope = +$("slope").value, up = $("wall").value === "up";
    var windTo = (from + 180) % 360, rad = windTo * Math.PI / 180;

    // Vent + pendent com a vectors (el pendent empeny cap amunt = nord)
    var vx = u * Math.sin(rad), vy = u * Math.cos(rad) + SLOPE_KMH[slope];
    var ue = Math.sqrt(vx * vx + vy * vy);
    var heading = ue > 0.01 ? (Math.atan2(vx, vy) * 180 / Math.PI + 360) % 360 : 0;

    var P = { ros: interp(ROS[d], ue), spot: interp(SPOT[d], ue), heading: heading };
    var fli = interp(FLI[d], ue), fl = 0.0775 * Math.pow(fli, 0.46);  // Byram (1959)
    P.el = ellipse(P.ros, ue);

    $("o-ros").textContent = fmt(P.ros);  $("b-ros").style.width = Math.min(P.ros / 25 * 100, 100) + "%";
    $("o-fli").textContent = fmt(fli);    $("b-fli").style.width = Math.min(fli / 10000 * 100, 100) + "%";
    $("o-fl").textContent = fmt(fl);      $("b-fl").style.width = Math.min(fl / 15 * 100, 100) + "%";
    $("o-spot").textContent = fmt(P.spot);$("b-spot").style.width = Math.min(P.spot / 1000 * 100, 100) + "%";
    $("o-ue").textContent = fmt(ue);

    // Mateixa vista per als dos mapes: escala fixa a 120 min + mur
    var wallY = up ? -WALL_DIST : WALL_DIST, big = shape(P, 120, null), xs = [0], ys = [0, wallY];
    big.surf.concat(big.spot).forEach(function (p) { xs.push(p[0]); ys.push(p[1]); });
    var mnx = Math.min.apply(null, xs), mxx = Math.max.apply(null, xs);
    var mny = Math.min.apply(null, ys), mxy = Math.max.apply(null, ys);
    var view = { cx: (mnx + mxx) / 2, cy: (mny + mxy) / 2, span: Math.max(mxx - mnx, mxy - mny, 200) * 1.2 };

    draw($("svg-a"), "a", P, view, t, null, slope, windTo);
    var b = draw($("svg-b"), "b", P, view, t, wallY, slope, windTo);
    $("st-a").textContent = "Cap barrera: el foc avança lliurement.";
    $("st-b").textContent = wallMsg(b, up);
    $("cap-b").textContent = "Amb mur " + (up ? "a dalt del pendent" : "a baix del pendent") + " (" + WALL_DIST + " m)";
  }

  ["dens", "wind", "dir", "slope", "time", "wall"].forEach(function (id) { $(id).addEventListener("change", update); });
  update();
})();
</script>

<p style="font-size:.85em;">Model simplificat: el foc creix com una el·lipse (Anderson 1983) amb l'ignició en un focus. El vent i el pendent se sumen com a vectors per obtenir el vent efectiu, i la llargada de flama ve de la intensitat de Byram (1959). El mur atura el foc de superfície, però no els focus secundaris.</p>

<div class="page-navigation">
  <a href="/simulations/" class="btn btn--primary">← Com podem anticipar el comportament d’un incendi?</a>
  <a href="/reaprendre/" class="btn btn--primary">Reaprendre a conviure amb el foc →</a>
</div>
