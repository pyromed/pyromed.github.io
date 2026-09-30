---
title: "Simulador de propagació"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Tria la vegetació, el vent, el pendent i el temps des de l'ignició. El mapa mostra fins on arribaria el foc des del punt d'ignició, situat al centre, i sota hi trobaràs la velocitat, la intensitat i la llargada de flama.

<style>
  .sim { --ink:#12354a; background:#d6e6f0; color:var(--ink); padding:20px; border-radius:8px;
         font-family:Arial,sans-serif; font-size:16px; box-sizing:border-box; }
  @media (min-width:1025px) { .sim { width:calc(100% + 160px); margin-left:-80px; } }
  .sim .controls { display:grid; grid-template-columns:repeat(auto-fit,minmax(170px,1fr)); gap:14px; margin-bottom:18px; }
  .sim label { display:block; font-weight:700; font-size:1em; color:var(--ink); margin-bottom:5px; }
  .sim select { width:100%; padding:9px; font-size:1em; border-radius:4px; border:1px solid #8fb0c6; background:#fff; color:var(--ink); }
  .sim .map { max-width:640px; margin:0 auto; }
  .sim svg { width:100%; height:auto; aspect-ratio:1/1; background:#f4f8fb; border-radius:4px; display:block; }
  .sim .legend { font-size:.85em; margin-top:8px; text-align:center; }
  .sim .legend span { display:inline-block; width:12px; height:12px; border-radius:2px; margin:0 5px -2px 12px; }
  .sim .legend span:first-child { margin-left:0; }
  .sim .metrics { display:grid; grid-template-columns:repeat(auto-fit,minmax(190px,1fr)); gap:12px; margin-top:16px; }
  .sim .card { background:#eaf2f8; border-radius:6px; padding:12px; }
  .sim .card h4 { margin:0 0 6px; font-size:.95em; color:var(--ink); }
  .sim .val { font-size:1.5em; font-weight:700; }
  .sim .val small { font-size:.55em; font-weight:400; }
  .sim .chip { display:inline-block; padding:2px 8px; border-radius:10px; font-size:.8em; font-weight:700; margin-left:6px; color:#fff; vertical-align:middle; }
  .sim .seg { display:grid; grid-template-columns:repeat(4,1fr); gap:3px; margin-top:8px; }
  .sim .seg i { height:9px; border-radius:3px; background:#b4cad9; }
  .sim .card p { display:block; margin:5px 0 0; font-size:.78em; color:#3d5e74; }
</style>

<div class="sim">
  <div class="controls">
    <div>
      <label for="dens">Densitat de vegetació</label>
      <select id="dens">
        <option value="0">Baixa (≈0,5 kg/m²)</option>
        <option value="1" selected>Moderada (≈1,5 kg/m²)</option>
        <option value="2">Alta (≈3 kg/m²)</option>
      </select>
    </div>
    <div>
      <label for="wind">Velocitat del vent</label>
      <select id="wind">
        <option value="10">Baixa (10 km/h)</option>
        <option value="20" selected>Moderada (20 km/h)</option>
        <option value="30">Alta (30 km/h)</option>
      </select>
    </div>
    <div>
      <label for="dir">El vent bufa des del</label>
      <select id="dir">
        <option value="0">Nord (vent cap avall)</option>
        <option value="45">Nord-est</option>
        <option value="90">Est</option>
        <option value="135">Sud-est</option>
        <option value="180" selected>Sud (vent cap amunt)</option>
        <option value="225">Sud-oest</option>
        <option value="270">Oest</option>
        <option value="315">Nord-oest</option>
      </select>
    </div>
    <div>
      <label for="slope">Pendent (puja cap al nord)</label>
      <select id="slope">
        <option value="0">Pla</option>
        <option value="1" selected>Moderat (≈20 %)</option>
        <option value="2">Fort (≈40 %)</option>
      </select>
    </div>
    <div>
      <label for="time">Temps des de l'ignició</label>
      <select id="time">
        <option value="15">15 min</option>
        <option value="30" selected>30 min</option>
        <option value="60">60 min</option>
        <option value="120">120 min</option>
      </select>
    </div>
  </div>

  <div class="map">
    <svg id="svg" role="img" aria-label="Àrea afectada pel foc"></svg>
    <div class="legend">
      <span style="background:#d62839"></span>Foc de superfície
      <span style="background:#f4a261"></span>Abast dels focus secundaris
    </div>
    <div class="legend" id="updown" style="font-weight:700;"></div>
  </div>

  <div class="metrics">
    <div class="card"><h4>Velocitat de propagació</h4>
      <div><span class="val"><span id="o-ros">0</span> <small>m/min</small></span><span class="chip" id="l-ros"></span></div>
      <div class="seg" id="s-ros"><i></i><i></i><i></i><i></i></div>
      <p>Alta ≥ 20 · Molt alta ≥ 50 m/min</p></div>
    <div class="card"><h4>Intensitat</h4>
      <div><span class="val"><span id="o-fli">0</span> <small>kW/m</small></span><span class="chip" id="l-fli"></span></div>
      <div class="seg" id="s-fli"><i></i><i></i><i></i><i></i></div>
      <p>Alta ≥ 2.000 · Molt alta ≥ 10.000 kW/m</p></div>
    <div class="card"><h4>Llargada de flama</h4>
      <div><span class="val"><span id="o-fl">0</span> <small>m</small></span><span class="chip" id="l-fl"></span></div>
      <div class="seg" id="s-fl"><i></i><i></i><i></i><i></i></div>
      <p>Alta ≥ 4 · Molt alta ≥ 10 m</p></div>
    <div class="card"><h4>Focus secundaris</h4>
      <div><span class="val"><span id="o-spot">0</span> <small>m</small></span><span class="chip" id="l-spot"></span></div>
      <div class="seg" id="s-spot"><i></i><i></i><i></i><i></i></div>
      <p>Alta ≥ 500 · Molt alta ≥ 1.000 m</p></div>
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
  var TAN_SLOPE = [0, 0.2, 0.4];      // pendent (tangent): pla, moderat ≈20 %, fort ≈40 %
  var BETA = [0.003, 0.006, 0.012];   // relació d'empaquetament per densitat: SUBSTITUEIX-LA pel teu model
  var LB_MAX = 8;               // Finney (1998)
  var TIMES = [15, 30, 60, 120];
  var LEVELS = { ros: [5, 20, 50], fli: [500, 2000, 10000], fl: [2, 4, 10], spot: [100, 500, 1000] };
  var NAMES = ["Baixa", "Moderada", "Alta", "Molt alta"];
  var COLS = ["#2e9e5b", "#d4a300", "#e8791a", "#c1121f"];
  var INK = "#12354a";

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

  // Velocitat (m/min) en una direcció a `off` graus del cap del foc (equació de l'el·lipse)
  function rdAt(P, off) {
    var th = off * Math.PI / 180;
    return P.el.a * (1 - P.el.e * P.el.e) / (1 - P.el.e * Math.cos(th));
  }

  // Vent (km/h) que dóna una velocitat de propagació concreta: inversa de la corba ROS(vent) en pla
  function invert(arr, r) {
    if (r <= arr[0]) return 0;
    for (var i = 1; i < WINDS.length; i++)
      if (r <= arr[i]) return WINDS[i - 1] + (WINDS[i] - WINDS[i - 1]) * (r - arr[i - 1]) / (arr[i] - arr[i - 1]);
    var n = WINDS.length - 1;
    return WINDS[n] + (WINDS[n] - WINDS[n - 1]) * (r - arr[n]) / (arr[n] - arr[n - 1]);
  }

  // Perímetre del foc i abast dels focus secundaris (m); ignició a (0,0), nord = -y
  function shape(P, t) {
    var surf = [], spot = [];
    for (var off = 0; off <= 360; off += 5) {
      var th = off * Math.PI / 180;
      var rd = P.el.a * (1 - P.el.e * P.el.e) / (1 - P.el.e * Math.cos(th));
      var phi = (P.heading + off) * Math.PI / 180;
      var x = rd * t * Math.sin(phi), y = -rd * t * Math.cos(phi);
      var sd = P.spot * rd / P.ros;                       // escalat direccional
      surf.push([x, y]); spot.push([x + sd * Math.sin(phi), y - sd * Math.cos(phi)]);
    }
    return { surf: surf, spot: spot };
  }

  function path(pts) {
    return pts.map(function (p, i) { return (i ? "L" : "M") + p[0].toFixed(1) + " " + p[1].toFixed(1); }).join("") + "Z";
  }
  function fmt(n) {
    if (n >= 100) return Math.round(n).toLocaleString("ca");
    var f = n < 1 ? 100 : 10;   // dues xifres decimals per sota d'1
    return (Math.round(n * f) / f).toLocaleString("ca");
  }
  function txt(x, y, size, color, s, anchor) {
    return '<text x="' + x + '" y="' + y + '" font-size="' + size + '" fill="' + color + '" text-anchor="' + (anchor || "start") + '" font-family="Arial">' + s + '</text>';
  }
  function arrow(x, y, deg, len, k, color) {
    return '<g transform="translate(' + x + ' ' + y + ') rotate(' + deg + ')"><path d="M0 ' + len + 'V-' + len +
      'm-' + 2 * k + ' ' + 2.5 * k + 'l' + 2 * k + ' -' + 2.5 * k + 'l' + 2 * k + ' ' + 2.5 * k +
      '" fill="none" stroke="' + color + '" stroke-width="' + 0.8 * k + '"/></g>';
  }

  function draw(P, span, t, slope, windTo) {
    var k = span / 100, x0 = -span / 2, y0 = -span / 2, svg = $("svg"), h = "";
    svg.setAttribute("viewBox", [x0, y0, span, span].join(" "));
    if (slope > 0)
      h += '<defs><linearGradient id="g" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="' + INK + '" stop-opacity="' + 0.12 * slope +
           '"/><stop offset="1" stop-color="' + INK + '" stop-opacity="0"/></linearGradient></defs>' +
           '<rect x="' + x0 + '" y="' + y0 + '" width="' + span + '" height="' + span + '" fill="url(#g)"/>' +
           txt(0, y0 + 5 * k, 3.2 * k, INK, "amunt ↑", "middle") + txt(0, y0 + span - 2 * k, 3.2 * k, INK, "avall ↓", "middle");

    var cur = shape(P, t);
    h += '<path d="' + path(cur.spot) + '" fill="#f4a261" fill-opacity="0.35" stroke="#e07a1f" stroke-width="' + 0.4 * k + '" stroke-dasharray="' + 2 * k + ' ' + 1.5 * k + '"/>';
    TIMES.forEach(function (tt) {
      if (tt === t) return;
      var s = shape(P, tt).surf;
      h += '<path d="' + path(s) + '" fill="none" stroke="' + INK + '" stroke-opacity=".35" stroke-width="' + 0.3 * k + '"/>' +
           txt(s[0][0] + 1.2 * k, s[0][1], 2.8 * k, INK, tt + " min");
    });
    h += '<path d="' + path(cur.surf) + '" fill="#d62839" fill-opacity="0.65" stroke="#d62839" stroke-width="' + 0.6 * k + '"/>';
    h += '<circle r="' + 1.3 * k + '" fill="' + INK + '" stroke="#fff" stroke-width="' + 0.35 * k + '"/>';

    var len = 10;
    [10, 25, 50, 100, 250, 500, 1000, 2500].forEach(function (n) { if (n <= span / 4) len = n; });
    var sx = x0 + 4 * k, sy = y0 + span - 7 * k;
    h += '<path d="M' + sx + ' ' + sy + 'h' + len + '" stroke="' + INK + '" stroke-width="' + 0.8 * k + '"/>' +
         txt(sx, sy - 1.5 * k, 3.2 * k, INK, len >= 1000 ? len / 1000 + " km" : len + " m");
    var nx = span / 2 - 6 * k, ny = y0 + 12 * k;
    h += txt(nx, ny - 7 * k, 3.6 * k, INK, "N", "middle") + arrow(nx, ny, 0, 4 * k, k * 0.6, INK);
    h += arrow(x0 + 10 * k, y0 + 14 * k, windTo, 6 * k, k, "#0b7285") + txt(x0 + 10 * k, y0 + 24 * k, 3.2 * k, "#0b7285", "vent", "middle");
    svg.innerHTML = h;
  }

  function setLevel(key, v) {
    var lv = 0; LEVELS[key].forEach(function (x) { if (v >= x) lv++; });
    var segs = $("s-" + key).children;
    for (var i = 0; i < 4; i++) segs[i].style.background = i <= lv ? COLS[lv] : "#b4cad9";
    var chip = $("l-" + key);
    chip.textContent = NAMES[lv]; chip.style.background = COLS[lv]; chip.style.color = lv === 1 ? INK : "#fff";
  }

  function update() {
    var d = +$("dens").value, u = +$("wind").value, t = +$("time").value;
    var from = +$("dir").value, slope = +$("slope").value;
    var windTo = (from + 180) % 360, rad = windTo * Math.PI / 180;

    // Rothermel (1972): R = R0 (1 + φw + φs). El vent i el pendent se sumen com a VECTORS DE FACTORS
    // (Albini 1976), no com a velocitats: el pendent empeny cap amunt (nord) amb φs = 5,275 β^-0,3 tan²(pendent).
    var R0 = ROS[d][0];                                  // propagació sense vent ni pendent
    var phiW = interp(ROS[d], u) / R0 - 1;               // factor del vent (en pla)
    var phiS = 5.275 * Math.pow(BETA[d], -0.3) * Math.pow(TAN_SLOPE[slope], 2);
    var vx = phiW * Math.sin(rad), vy = phiW * Math.cos(rad) + phiS;
    var phiE = Math.sqrt(vx * vx + vy * vy);
    var heading = phiE > 0.001 ? (Math.atan2(vx, vy) * 180 / Math.PI + 360) % 360 : 0;
    var ue = invert(ROS[d], R0 * (1 + phiE));            // vent efectiu equivalent (per a Lb, FLI i focus secundaris)

    var P = { ros: R0 * (1 + phiE), fli: interp(FLI[d], ue), spot: interp(SPOT[d], ue), heading: heading };
    P.el = ellipse(P.ros, ue);
    var fl = 0.0775 * Math.pow(P.fli, 0.46);   // Byram (1959)

    $("o-ros").textContent = fmt(P.ros);   setLevel("ros", P.ros);
    $("o-fli").textContent = fmt(P.fli);   setLevel("fli", P.fli);
    $("o-fl").textContent = fmt(fl);       setLevel("fl", fl);
    $("o-spot").textContent = fmt(P.spot); setLevel("spot", P.spot);

    $("updown").textContent = "Velocitat cap amunt (N): " + fmt(rdAt(P, -heading)) + " m/min · cap avall (S): " + fmt(rdAt(P, 180 - heading)) + " m/min";

    var big = shape(P, t), ext = 0;   // vista centrada a l'ignició
    big.surf.concat(big.spot).forEach(function (p) { ext = Math.max(ext, Math.abs(p[0]), Math.abs(p[1])); });
    draw(P, Math.max(ext * 2 * 1.1, 100), t, slope, windTo);
  }

  ["dens", "wind", "dir", "slope", "time"].forEach(function (id) { $(id).addEventListener("change", update); });
  update();
})();
</script>

<p style="font-size:.85em;">Model simplificat: el foc creix com una el·lipse (Anderson 1983) amb l'ignició al centre, i el vent i el pendent se sumen com a factors vectorials segons Rothermel (1972): el pendent empeny el foc cap amunt amb un factor proporcional a tan²(pendent). La llargada de flama ve de la intensitat de Byram (1959).</p>

<div class="page-navigation">
  <a href="/simulations/" class="btn btn--primary">← Com podem anticipar el comportament d’un incendi?</a>
  <a href="/reaprendre/" class="btn btn--primary">Reaprendre a conviure amb el foc →</a>
</div>
