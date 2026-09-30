---
title: "Simulador de propagació"
layout: single
permalink: /simulador/
author_profile: true
sidebar:
  nav: "main"
---

Tria la vegetació, el vent, el pendent i el temps des de l'ignició. Els dos mapes mostren el mateix foc, amb el punt d'ignició al centre: a l'esquerra sense murs i a la dreta amb murs de contenció cada 10 m i alçades entre 0,2 i 3 m, com en un terreny abancalat.

<style>
  .sim { --ink:#12354a; background:#d6e6f0; color:var(--ink); padding:20px; border-radius:8px;
         font-family:Arial,sans-serif; font-size:16px; box-sizing:border-box; }
  @media (min-width:1025px) { .sim { width:calc(100% + 160px); margin-left:-80px; } }
  .sim .controls { display:grid; grid-template-columns:repeat(auto-fit,minmax(170px,1fr)); gap:14px; margin-bottom:18px; }
  .sim label { display:block; font-weight:700; font-size:1em; color:var(--ink); margin-bottom:5px; }
  .sim select { width:100%; padding:9px; font-size:1em; border-radius:4px; border:1px solid #8fb0c6; background:#fff; color:var(--ink); }
  .sim .maps { display:grid; grid-template-columns:repeat(auto-fit,minmax(300px,1fr)); gap:14px; }
  .sim figure { margin:0; }
  .sim figcaption { font-weight:700; margin-bottom:6px; }
  .sim svg { width:100%; height:auto; aspect-ratio:1/1; background:#f4f8fb; border-radius:4px; display:block; }
  .sim .status { font-size:.9em; margin-top:6px; min-height:3.2em; }
  .sim .legend { font-size:.85em; margin-top:8px; }
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

  <div class="maps">
    <figure>
      <figcaption>Sense murs</figcaption>
      <svg id="svg-a" role="img" aria-label="Foc sense murs"></svg>
      <div class="status" id="st-a"></div>
    </figure>
    <figure>
      <figcaption>Amb murs de contenció cada 10 m (0,2–3 m d'alçada)</figcaption>
      <svg id="svg-b" role="img" aria-label="Foc amb murs"></svg>
      <div class="status" id="st-b"></div>
    </figure>
  </div>
  <div class="legend">
    <span style="background:#d62839"></span>Foc sense murs
    <span style="background:#6a3fb5"></span>Foc amb murs
    <span style="background:#f4a261"></span>Abast dels focus secundaris
    <span style="background:#7a5a2e"></span>Murs (més gruixuts = més alts)
    <span style="background:#2f7d4a"></span>Arbres i arbustos (esquemàtic)
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
  var SLOPE_KMH = [0, 8, 16];   // vent equivalent del pendent (pla, moderat, fort)
  var LB_MAX = 8;               // Finney (1998)
  var WALL_SPACING = 10;        // murs paral·lels cada 10 m (el primer, a 10 m de l'ignició)
  // Alçada dels murs: varia entre 0,2 i 3 m, de mur a mur i al llarg de cada mur (trams de 12 m).
  // Determinista: sempre és el mateix terreny. Substitueix-ho per les teves alçades reals si les tens.
  var WALL_SEG = 12, H_MIN = 0.2, H_MAX = 3;
  function wallH(i, x) {
    var s = Math.sin(i * 127.1 + Math.floor(x / WALL_SEG) * 311.7) * 43758.5453;
    return H_MIN + (H_MAX - H_MIN) * (s - Math.floor(s));
  }
  var TIMES = [15, 30, 60, 120];
  var TREES = [15, 45, 90], SHRUBS = [40, 65, 80];   // arbres 1 : 3 : 6 com la càrrega
  var LEVELS = { ros: [5, 20, 50], fli: [500, 2000, 10000], fl: [2, 4, 10], spot: [100, 500, 1000] };
  var NAMES = ["Baixa", "Moderada", "Alta", "Molt alta"];
  var COLS = ["#2e9e5b", "#d4a300", "#e8791a", "#c1121f"];
  var INK = "#12354a", WALLC = "#7a5a2e", RED = "#d62839", PURPLE = "#6a3fb5";

  var $ = function (id) { return document.getElementById(id); };

  var rnd = (function (a) { return function () { a |= 0; a = a + 0x6D2B79F5 | 0; var t = Math.imul(a ^ a >>> 15, 1 | a);
    t = t + Math.imul(t ^ t >>> 7, 61 | t) ^ t; return ((t ^ t >>> 14) >>> 0) / 4294967296; }; })(20240611);
  var TP = [], SP = [];
  for (var i = 0; i < 90; i++) TP.push([rnd(), rnd()]);
  for (i = 0; i < 80; i++) SP.push([rnd(), rnd()]);

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

  // Perímetre del foc i abast dels focus secundaris (m), ignició a (0,0), nord = -y.
  // Cap amunt, el foc s'atura al primer mur (en el punt on el toca) l'alçada del qual és >= a la
  // flama en aquella direcció; si la flama és més alta, el salta. Els murs no aturen el foc cap avall ni els focus secundaris.
  function shape(P, t, wall) {
    var surf = [], spot = [], hit = false;
    for (var off = 0; off <= 360; off += 5) {
      var rd = rdAt(P, off), phi = (P.heading + off) * Math.PI / 180;
      var sn = Math.sin(phi), cs = Math.cos(phi);
      var x = rd * t * sn, y = -rd * t * cs;
      if (wall && y < -WALL_SPACING) {
        hit = true;
        var fl = flame(P, rd);
        for (var i = 1; y < -WALL_SPACING * i; i++) {
          var xi = WALL_SPACING * i * sn / cs;          // on el raig del foc toca el mur i
          if (fl <= wallH(i, xi)) { x = xi; y = -WALL_SPACING * i; break; }
        }
      }
      var sd = P.spot * rd / P.ros;
      surf.push([x, y]); spot.push([x + sd * Math.sin(phi), y - sd * Math.cos(phi)]);
    }
    return { surf: surf, spot: spot, hit: hit };
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

  function vegetation(view, dens) {
    var k = view.span / 100, x0 = view.cx - view.span / 2, y0 = view.cy - view.span / 2, items = [], h = "";
    TP.slice(0, TREES[dens]).forEach(function (p) { items.push([p[1], p[0], 1]); });
    SP.slice(0, SHRUBS[dens]).forEach(function (p) { items.push([p[1], p[0], 0]); });
    items.sort(function (a, b) { return a[0] - b[0]; });
    items.forEach(function (it) {
      var x = x0 + it[1] * view.span, y = y0 + it[0] * view.span;
      if (it[2])
        h += '<path d="M' + x + ' ' + (y - 6 * k) + 'L' + (x - 1.7 * k) + ' ' + (y - 2.8 * k) + 'H' + (x + 1.7 * k) + 'Z' +
             'M' + x + ' ' + (y - 4.2 * k) + 'L' + (x - 2.1 * k) + ' ' + (y - 0.6 * k) + 'H' + (x + 2.1 * k) + 'Z" fill="#2f7d4a" fill-opacity=".8"/>' +
             '<path d="M' + x + ' ' + (y - 0.6 * k) + 'v' + 0.9 * k + '" stroke="#6b4a2b" stroke-width="' + 0.5 * k + '"/>';
      else
        h += '<ellipse cx="' + x + '" cy="' + y + '" rx="' + 1.5 * k + '" ry="' + 1 * k + '" fill="#8dbb5f" fill-opacity=".85"/>';
    });
    return h;
  }

  function draw(svg, id, P, view, t, wall, slope, windTo, dens) {
    var k = view.span / 100, x0 = -view.span / 2, y0 = -view.span / 2, col = wall ? PURPLE : RED;
    svg.setAttribute("viewBox", [x0, y0, view.span, view.span].join(" "));
    var h = "";
    if (slope > 0)
      h += '<defs><linearGradient id="g' + id + '" x1="0" y1="0" x2="0" y2="1"><stop offset="0" stop-color="' + INK + '" stop-opacity="' +
           0.12 * slope + '"/><stop offset="1" stop-color="' + INK + '" stop-opacity="0"/></linearGradient></defs>' +
           '<rect x="' + x0 + '" y="' + y0 + '" width="' + view.span + '" height="' + view.span + '" fill="url(#g' + id + ')"/>' +
           txt(0, y0 + 5 * k, 3.2 * k, INK, "amunt ↑", "middle") + txt(0, y0 + view.span - 2 * k, 3.2 * k, INK, "avall ↓", "middle");

    h += vegetation(view, dens);

    if (wall) {   // murs cada 10 m; gruix i opacitat segons l'alçada (si n'hi ha massa, se'n dibuixen només alguns)
      var n = Math.floor(view.span / 2 / WALL_SPACING), step = Math.max(1, Math.ceil(n / 22));
      var seg = WALL_SEG * Math.max(1, Math.ceil(view.span / WALL_SEG / 60)), dd = ["", "", "", ""];
      for (var i = 1; i <= n; i += step)
        for (var xs = Math.floor(x0 / seg) * seg; xs < x0 + view.span; xs += seg) {
          var hh = wallH(i, xs + seg / 2), c = hh < 0.75 ? 0 : hh < 1.5 ? 1 : hh < 2.25 ? 2 : 3;
          dd[c] += 'M' + xs + ' ' + -i * WALL_SPACING + 'h' + seg;
          dd[c] += 'M' + xs + ' ' + i * WALL_SPACING + 'h' + seg;   // els de baix, només il·lustratius
        }
      var wsw = [0.15, 0.25, 0.4, 0.6], wop = [0.5, 0.65, 0.8, 0.95];
      dd.forEach(function (p, c) {
        if (p) h += '<path d="' + p + '" stroke="' + WALLC + '" stroke-width="' + wsw[c] * k + '" stroke-opacity="' + wop[c] + '"/>';
      });
    }

    var cur = shape(P, t, wall);
    h += '<path d="' + path(cur.spot) + '" fill="#f4a261" fill-opacity="0.35" stroke="#e07a1f" stroke-width="' + 0.4 * k + '" stroke-dasharray="' + 2 * k + ' ' + 1.5 * k + '"/>';
    TIMES.forEach(function (tt) {
      if (tt === t) return;
      var s = shape(P, tt, wall);
      h += '<path d="' + path(s.surf) + '" fill="none" stroke="' + INK + '" stroke-opacity=".35" stroke-width="' + 0.3 * k + '"/>' +
           txt(s.surf[0][0] + 1.2 * k, s.surf[0][1], 2.8 * k, INK, tt + " min");
    });
    h += '<path d="' + path(cur.surf) + '" fill="' + col + '" fill-opacity="0.65" stroke="' + col + '" stroke-width="' + 0.6 * k + '"/>';
    h += '<circle r="' + 1.3 * k + '" fill="' + INK + '" stroke="#fff" stroke-width="' + 0.35 * k + '"/>';
    if (wall) h += txt(view.span / 2 - 2 * k, y0 + view.span - 2 * k, 3.2 * k, WALLC, "murs cada 10 m, 0,2–3 m", "end");

    var target = view.span / 4, len = 10;
    [10, 25, 50, 100, 250, 500, 1000, 2500].forEach(function (n) { if (n <= target) len = n; });
    var sx = x0 + 4 * k, sy = y0 + view.span - 7 * k;
    h += '<path d="M' + sx + ' ' + sy + 'h' + len + '" stroke="' + INK + '" stroke-width="' + 0.8 * k + '"/>' +
         txt(sx, sy - 1.5 * k, 3.2 * k, INK, len >= 1000 ? len / 1000 + " km" : len + " m");
    var nx = view.span / 2 - 6 * k, ny = y0 + 12 * k;
    h += txt(nx, ny - 7 * k, 3.6 * k, INK, "N", "middle") + arrow(nx, ny, 0, 4 * k, k * 0.6, INK);
    h += arrow(x0 + 10 * k, y0 + 14 * k, windTo, 6 * k, k, "#0b7285") + txt(x0 + 10 * k, y0 + 24 * k, 3.2 * k, "#0b7285", "vent", "middle");
    svg.innerHTML = h;
    return cur;
  }

  function setLevel(key, v) {
    var lv = 0; LEVELS[key].forEach(function (x) { if (v >= x) lv++; });
    var segs = $("s-" + key).children;
    for (var i = 0; i < 4; i++) segs[i].style.background = i <= lv ? COLS[lv] : "#b4cad9";
    var chip = $("l-" + key);
    chip.textContent = NAMES[lv]; chip.style.background = COLS[lv]; chip.style.color = lv === 1 ? INK : "#fff";
  }

  function wallMsg(cur, flU) {
    var m = !cur.hit ? "El foc encara no arriba al primer mur (10 m amunt)."
          : flU > H_MAX ? "Flama cap amunt de " + fmt(flU) + " m (> " + fmt(H_MAX) + " m): supera tots els murs i el foc els salta."
          : flU <= H_MIN ? "Flama cap amunt de " + fmt(flU) + " m: tots els murs aturen el foc de superfície cap amunt."
          : "Flama cap amunt de " + fmt(flU) + " m: els murs més alts que això aturen el foc cap amunt i els més baixos el deixen passar.";
    return m + " Els focus secundaris els poden creuar igualment.";
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

    // Vista comuna centrada a l'ignició, ajustada al temps triat
    var big = shape(P, t, false), ext = 0;
    big.surf.concat(big.spot).forEach(function (p) { ext = Math.max(ext, Math.abs(p[0]), Math.abs(p[1])); });
    var view = { cx: 0, cy: 0, span: Math.max(ext * 2 * 1.1, 100) };

    var flU = flame(P, rdAt(P, -heading));   // flama en direcció nord (cap amunt)
    draw($("svg-a"), "a", P, view, t, false, slope, windTo, d);
    var b = draw($("svg-b"), "b", P, view, t, true, slope, windTo, d);
    $("st-a").textContent = "Sense murs: el foc avança lliurement.";
    $("st-b").textContent = wallMsg(b, flU);
  }

  ["dens", "wind", "dir", "slope", "time"].forEach(function (id) { $(id).addEventListener("change", update); });
  update();
})();
</script>

<p style="font-size:.85em;">Model simplificat: el foc creix com una el·lipse (Anderson 1983) amb l'ignició al centre, i el vent i el pendent se sumen com a vectors. La llargada de flama ve de la intensitat de Byram (1959). Els murs de contenció, paral·lels entre ells i a les corbes de nivell cada 10 m, aturen el foc que puja quan la flama en aquella direcció no supera l'alçada del mur (que varia entre 0,2 i 3 m al llarg de cada mur); no l'aturen cap avall ni als focus secundaris. Els arbres i arbustos són esquemàtics i no estan a escala.</p>

<div class="page-navigation">
  <a href="/simulations/" class="btn btn--primary">← Com podem anticipar el comportament d’un incendi?</a>
  <a href="/reaprendre/" class="btn btn--primary">Reaprendre a conviure amb el foc →</a>
</div>
