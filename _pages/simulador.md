---
title: "Simulador de propagació"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Tria la vegetació, el vent, el pendent i el temps des de l'ignició. Els dos mapes mostren el mateix foc: a l'esquerra sense murs i a la dreta amb dos murs de pedra de 2 m a dalt del pendent, perquè es vegi quan els murs aturen el foc i quan no.

<style>
  .sim { --ink:#12354a; display:grid; grid-template-columns:270px 1fr; gap:18px; background:#d6e6f0; color:var(--ink);
         padding:20px; border-radius:8px; font-family:Arial,sans-serif; font-size:16px; box-sizing:border-box; }
  @media (min-width:1025px) { .sim { width:calc(100% + 160px); margin-left:-80px; } }
  @media (max-width:1024px) { .sim { grid-template-columns:1fr; } }
  .sim label { display:block; font-weight:700; font-size:1em; color:var(--ink); margin-bottom:5px; }
  .sim select { width:100%; padding:9px; font-size:1em; border-radius:4px; border:1px solid #8fb0c6; background:#fff; color:var(--ink); }
  .sim .field { margin-bottom:14px; }
  .sim .out { border-top:2px solid #a9c3d4; padding-top:8px; }
  .sim .row { display:flex; justify-content:space-between; align-items:center; gap:8px; margin:14px 0 5px; }
  .sim .row b { font-size:1.15em; white-space:nowrap; }
  .sim .chip { display:inline-block; padding:2px 8px; border-radius:10px; font-size:.8em; font-weight:700; margin-left:6px; color:#fff; }
  .sim .seg { display:grid; grid-template-columns:repeat(4,1fr); gap:3px; }
  .sim .seg i { height:9px; border-radius:3px; background:#b4cad9; }
  .sim small { display:block; margin-top:3px; font-size:.78em; color:#3d5e74; }
  .sim .stage { min-width:0; }
  .sim .maps { display:grid; grid-template-columns:repeat(auto-fit,minmax(280px,1fr)); gap:12px; }
  .sim figure { margin:0; }
  .sim figcaption { font-weight:700; margin-bottom:6px; }
  .sim svg { width:100%; height:auto; background:#f4f8fb; border-radius:4px; display:block; }
  .sim .maps svg { aspect-ratio:1/1; }
  .sim .profile { margin-top:14px; }
  .sim .profile svg { max-height:300px; }
  .sim .status { font-size:.9em; margin-top:6px; min-height:3.2em; }
  .sim .legend { font-size:.85em; margin-top:10px; }
  .sim .legend span { display:inline-block; width:12px; height:12px; border-radius:2px; margin:0 5px -2px 12px; }
  .sim .legend span:first-child { margin-left:0; }
  @media (prefers-reduced-motion:reduce) { .sim * { transition:none !important; } }
</style>

<div class="sim">
  <div>
    <div class="field">
      <label for="dens">Densitat de vegetació</label>
      <select id="dens">
        <option value="0">Baixa (≈0,5 kg/m²)</option>
        <option value="1" selected>Moderada (≈1,5 kg/m²)</option>
        <option value="2">Alta (≈3 kg/m²)</option>
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
        <option value="30" selected>30 min</option>
        <option value="60">60 min</option>
        <option value="120">120 min</option>
      </select>
    </div>

    <div class="out">
      <div class="row"><span>Velocitat de propagació <span class="chip" id="l-ros"></span></span><b><span id="o-ros">0</span> m/min</b></div>
      <div class="seg" id="s-ros"><i></i><i></i><i></i><i></i></div>
      <small>Alta ≥ 20 · Molt alta ≥ 50 m/min</small>

      <div class="row"><span>Intensitat <span class="chip" id="l-fli"></span></span><b><span id="o-fli">0</span> kW/m</b></div>
      <div class="seg" id="s-fli"><i></i><i></i><i></i><i></i></div>
      <small>Alta ≥ 2.000 · Molt alta ≥ 10.000 kW/m</small>

      <div class="row"><span>Llargada de flama <span class="chip" id="l-fl"></span></span><b><span id="o-fl">0</span> m</b></div>
      <div class="seg" id="s-fl"><i></i><i></i><i></i><i></i></div>
      <small>Alta ≥ 4 · Molt alta ≥ 10 m</small>

      <div class="row"><span>Focus secundaris <span class="chip" id="l-spot"></span></span><b><span id="o-spot">0</span> m</b></div>
      <div class="seg" id="s-spot"><i></i><i></i><i></i><i></i></div>
      <small>Alta ≥ 500 · Molt alta ≥ 1.000 m</small>

      <div class="row"><span>Vent efectiu (vent + pendent)</span><b><span id="o-ue">0</span> km/h</b></div>
    </div>
  </div>

  <div class="stage">
    <div class="maps">
      <figure>
        <figcaption>Sense murs</figcaption>
        <svg id="svg-a" role="img" aria-label="Foc sense murs"></svg>
        <div class="status" id="st-a"></div>
      </figure>
      <figure>
        <figcaption>Amb dos murs de 2 m a dalt del pendent (8 i 10 m)</figcaption>
        <svg id="svg-b" role="img" aria-label="Foc amb dos murs"></svg>
        <div class="status" id="st-b"></div>
      </figure>
    </div>
    <figure class="profile">
      <figcaption>Vista lateral cap amunt: flama contra murs</figcaption>
      <svg id="svg-p" role="img" aria-label="Perfil de la flama i els murs"></svg>
    </figure>
    <div class="legend">
      <span style="background:#d62839"></span>Foc de superfície
      <span style="background:#f4a261"></span>Abast dels focus secundaris
      <span style="background:#7a5a2e"></span>Murs
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
  var SLOPE_PCT = [0, 0.2, 0.4];
  var LB_MAX = 8;               // Finney (1998)
  var WALLS = [8, 10];          // distància dels murs a dalt del pendent (m)
  var WALL_H = 2;               // alçada dels murs (m)
  var TIMES = [15, 30, 60, 120];
  // Llindars de classificació [Alta, ... ] -> baixa | moderada | alta | molt alta
  var LEVELS = { ros: [5, 20, 50], fli: [500, 2000, 10000], fl: [2, 4, 10], spot: [100, 500, 1000] };
  var NAMES = ["Baixa", "Moderada", "Alta", "Molt alta"];
  var COLS = ["#2e9e5b", "#d4a300", "#e8791a", "#c1121f"];
  var INK = "#12354a", WALLC = "#7a5a2e";

  var $ = function (id) { return document.getElementById(id); };

  function interp(arr, u) {
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

  function rdAt(P, offDeg) {
    var th = offDeg * Math.PI / 180;
    return P.el.a * (1 - P.el.e * P.el.e) / (1 - P.el.e * Math.cos(th));
  }
  function flame(P, rd) { return 0.0775 * Math.pow(P.fli * rd / P.ros, 0.46); }  // Byram (1959), FLI direccional

  // Perímetre del foc i abast dels focus secundaris (m). Amb murs: el mur atura el foc
  // en les direccions on la flama és <= alçada del mur; si la flama és més alta, el salta.
  function shape(P, t, walls) {
    var surf = [], spot = [], hit = false, crossed = false;
    for (var off = 0; off <= 360; off += 5) {
      var rd = rdAt(P, off), phi = (P.heading + off) * Math.PI / 180;
      var x = rd * t * Math.sin(phi), y = -rd * t * Math.cos(phi);
      if (walls && y < -WALLS[0]) {
        hit = true;
        if (flame(P, rd) <= WALL_H) y = -WALLS[0];
      }
      var sd = P.spot * rd / P.ros;
      var sx = x + sd * Math.sin(phi), sy = y - sd * Math.cos(phi);
      if (walls && sy < -WALLS[1]) crossed = true;
      surf.push([x, y]); spot.push([sx, sy]);
    }
    return { surf: surf, spot: spot, hit: hit, crossed: crossed };
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
  function txt(x, y, size, color, s, anchor) {
    return '<text x="' + x + '" y="' + y + '" font-size="' + size + '" fill="' + color + '" text-anchor="' + (anchor || "start") + '" font-family="Arial">' + s + '</text>';
  }

  function draw(svg, id, P, view, t, walls, slope, windTo) {
    var k = view.span / 100, x0 = view.cx - view.span / 2, y0 = view.cy - view.span / 2;
    svg.setAttribute("viewBox", [x0, y0, view.span, view.span].join(" "));
    var h = "";
    if (slope > 0)
      h += '<defs><linearGradient id="g' + id + '" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="' + INK + '" stop-opacity="' +
           0.12 * slope + '"/><stop offset="1" stop-color="' + INK + '" stop-opacity="0"/></linearGradient></defs>' +
           '<rect x="' + x0 + '" y="' + y0 + '" width="' + view.span + '" height="' + view.span + '" fill="url(#g' + id + ')"/>' +
           txt(view.cx, y0 + 5 * k, 3.2 * k, INK, "amunt ↑", "middle") +
           txt(view.cx, y0 + view.span - 2 * k, 3.2 * k, INK, "avall ↓", "middle");

    if (walls)
      WALLS.forEach(function (w) { h += '<path d="M' + x0 + ' ' + -w + 'h' + view.span + '" stroke="' + WALLC + '" stroke-width="' + 0.5 * k + '"/>'; });

    var cur = shape(P, t, walls);
    h += '<path d="' + path(cur.spot) + '" fill="#f4a261" fill-opacity="0.35" stroke="#e07a1f" stroke-width="' + 0.4 * k + '" stroke-dasharray="' + 2 * k + ' ' + 1.5 * k + '"/>';
    TIMES.forEach(function (tt) {
      if (tt === t) return;
      var s = shape(P, tt, walls);
      h += '<path d="' + path(s.surf) + '" fill="none" stroke="' + INK + '" stroke-opacity=".35" stroke-width="' + 0.3 * k + '"/>' +
           txt(s.surf[0][0] + 1.2 * k, s.surf[0][1], 2.8 * k, INK, tt + " min");
    });
    h += '<path d="' + path(cur.surf) + '" fill="#d62839" fill-opacity="0.7" stroke="#d62839" stroke-width="' + 0.6 * k + '"/>';
    h += '<circle r="' + 1.3 * k + '" fill="#5b3fd6" stroke="#fff" stroke-width="' + 0.35 * k + '"/>';
    if (walls) h += txt(x0 + 3 * k, -WALLS[1] - 1.5 * k, 3.2 * k, WALLC, "murs (8 i 10 m)");

    var target = view.span / 4, len = 10;
    [10, 25, 50, 100, 250, 500, 1000, 2500].forEach(function (n) { if (n <= target) len = n; });
    var sx = x0 + 4 * k, sy = y0 + view.span - 7 * k;
    h += '<path d="M' + sx + ' ' + sy + 'h' + len + '" stroke="' + INK + '" stroke-width="' + 0.8 * k + '"/>' +
         txt(sx, sy - 1.5 * k, 3.2 * k, INK, len >= 1000 ? len / 1000 + " km" : len + " m");
    var nx = x0 + view.span - 6 * k, ny = y0 + 12 * k;
    h += txt(nx, ny - 7 * k, 3.6 * k, INK, "N", "middle") + arrow(nx, ny, 0, 4 * k, k * 0.6, INK);
    h += arrow(x0 + 10 * k, y0 + 14 * k, windTo, 6 * k, k, "#0b7285") + txt(x0 + 10 * k, y0 + 24 * k, 3.2 * k, "#0b7285", "vent", "middle");
    svg.innerHTML = h;
    return cur;
  }

  // Perfil lateral: flama (cap amunt) contra els murs, amb el pendent inclinat
  function profile(flU, slope) {
    var ang = Math.atan(SLOPE_PCT[slope]) * 180 / Math.PI, xe = 24;
    var top = Math.max(flU * 1.15, 4) + SLOPE_PCT[slope] * xe + 1, fs = top / 12, sw = top / 90;
    var h = "", ff = flU * 0.5;
    h += '<g transform="rotate(' + -ang + ')">' +
         '<rect x="-3" y="0" width="' + (xe + 3) + '" height="' + 4 * fs + '" fill="#b9cfdd"/>' +
         '<path d="M-3 0H' + xe + '" stroke="' + INK + '" stroke-width="' + 2 * sw + '"/>' +
         '<path d="M0 ' + -WALL_H + 'H' + xe + '" stroke="' + WALLC + '" stroke-width="' + sw + '" stroke-dasharray="' + 4 * sw + ' ' + 3 * sw + '"/>' +
         txt(xe, -WALL_H - 0.4 * fs, fs, WALLC, "2 m", "end");
    WALLS.forEach(function (w) {
      h += '<rect x="' + (w - 0.2) + '" y="' + -WALL_H + '" width="0.4" height="' + WALL_H + '" fill="' + WALLC + '"/>' +
           txt(w, 1.6 * fs, fs, INK, w + " m", "middle");
    });
    h += '<path d="M-0.7 0Q-0.7 ' + -ff + ' 0 ' + -flU + 'Q0.7 ' + -ff + ' 0.7 0Z" fill="#d62839" fill-opacity=".85"/>' +
         '<circle r="' + 0.35 + '" fill="#5b3fd6"/>' +
         txt(1.2, -flU, fs * 1.1, INK, "flama cap amunt: " + fmt(flU) + " m") +
         txt(0, 1.6 * fs, fs, INK, "ignició", "middle") + '</g>';
    var svg = $("svg-p");
    svg.setAttribute("viewBox", [-3, -top, 27, top + 2].join(" "));
    svg.innerHTML = h;
  }

  function setLevel(key, v) {
    var lv = 0; LEVELS[key].forEach(function (x) { if (v >= x) lv++; });
    var segs = $("s-" + key).children;
    for (var i = 0; i < 4; i++) segs[i].style.background = i <= lv ? COLS[lv] : "#b4cad9";
    var chip = $("l-" + key);
    chip.textContent = NAMES[lv]; chip.style.background = COLS[lv]; chip.style.color = lv === 1 ? INK : "#fff";
  }

  function wallMsg(cur, flU) {
    var m = !cur.hit ? "El foc encara no arriba als murs."
          : flU <= WALL_H ? "Flama cap amunt de " + fmt(flU) + " m (≤ 2 m): els murs aturen el foc de superfície."
          : "Flama cap amunt de " + fmt(flU) + " m (> 2 m): el foc salta els murs.";
    return m + (cur.crossed ? " Els focus secundaris els poden creuar." : " Els focus secundaris no els creuen.");
  }

  function update() {
    var d = +$("dens").value, u = +$("wind").value, t = +$("time").value;
    var from = +$("dir").value, slope = +$("slope").value;
    var windTo = (from + 180) % 360, rad = windTo * Math.PI / 180;

    var vx = u * Math.sin(rad), vy = u * Math.cos(rad) + SLOPE_KMH[slope];   // el pendent empeny cap al nord
    var ue = Math.sqrt(vx * vx + vy * vy);
    var heading = ue > 0.01 ? (Math.atan2(vx, vy) * 180 / Math.PI + 360) % 360 : 0;

    var P = { ros: interp(ROS[d], ue), fli: interp(FLI[d], ue), spot: interp(SPOT[d], ue), heading: heading };
    P.el = ellipse(P.ros, ue);
    var fl = flame(P, P.ros);

    $("o-ros").textContent = fmt(P.ros);   setLevel("ros", P.ros);
    $("o-fli").textContent = fmt(P.fli);   setLevel("fli", P.fli);
    $("o-fl").textContent = fmt(fl);       setLevel("fl", fl);
    $("o-spot").textContent = fmt(P.spot); setLevel("spot", P.spot);
    $("o-ue").textContent = fmt(ue);

    // Vista comuna, ajustada al temps triat (els murs són a prop de l'ignició)
    var big = shape(P, t, false), xs = [0], ys = [0, -WALLS[1]];
    big.surf.concat(big.spot).forEach(function (p) { xs.push(p[0]); ys.push(p[1]); });
    var mnx = Math.min.apply(null, xs), mxx = Math.max.apply(null, xs);
    var mny = Math.min.apply(null, ys), mxy = Math.max.apply(null, ys);
    var view = { cx: (mnx + mxx) / 2, cy: (mny + mxy) / 2, span: Math.max(mxx - mnx, mxy - mny, 60) * 1.2 };

    var flU = flame(P, rdAt(P, -heading));   // flama en direcció nord (cap amunt)
    draw($("svg-a"), "a", P, view, t, false, slope, windTo);
    var b = draw($("svg-b"), "b", P, view, t, true, slope, windTo);
    $("st-a").textContent = "Cap barrera: el foc avança lliurement.";
    $("st-b").textContent = wallMsg(b, flU);
    profile(flU, slope);
  }

  ["dens", "wind", "dir", "slope", "time"].forEach(function (id) { $(id).addEventListener("change", update); });
  update();
})();
</script>

<p style="font-size:.85em;">Model simplificat: el foc creix com una el·lipse (Anderson 1983) amb l'ignició en un focus. El vent i el pendent se sumen com a vectors per obtenir el vent efectiu, i la llargada de flama ve de la intensitat de Byram (1959). Un mur de 2 m atura el foc de superfície quan la flama en aquella direcció no supera els 2 m; els focus secundaris el poden creuar igualment.</p>

<div class="page-navigation">
  <a href="/simulations/" class="btn btn--primary">← Com podem anticipar el comportament d’un incendi?</a>
  <a href="/reaprendre/" class="btn btn--primary">Reaprendre a conviure amb el foc →</a>
</div>
