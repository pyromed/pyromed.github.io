---
permalink: /about/
title: ""
layout: single
author_profile: true
---

<img src="{{ '/assets/images/Humphreys.jpg' | relative_url }}"
     alt="Alicia Azpeleta and Sean Henning"
     style="display: block; margin: 20px auto 30px auto; max-width: 700px; width: 100%; border-radius: 8px;">

<!-- Language Switcher Buttons -->
<div style="text-align: center; margin-bottom: 30px;">
  <button onclick="setLanguage('ca')" id="btn-ca" style="padding: 8px 16px; margin-right: 10px; cursor: pointer; font-weight: bold; background-color: #002b49; color: white; border: none; border-radius: 4px;">Català</button>
  <button onclick="setLanguage('en')" id="btn-en" style="padding: 8px 16px; cursor: pointer; font-weight: normal; background-color: #e0e0e0; color: #333; border: none; border-radius: 4px;">English</button>
</div>

<!-- CATALAN CONTENT -->
<div id="content-ca" class="lang-content" markdown="1">

### Dra. Alicia Azpeleta

**Investigadora principal i cofundadora**

La Dra. Alicia Azpeleta compta amb més d’una dècada d’experiència internacional en ecologia forestal i del foc, combinant recerca desenvolupada a Nord-amèrica i la Mediterrània. La seva especialització se situa en la intersecció entre els incendis forestals, el canvi climàtic, la resiliència dels boscos i la dinàmica històrica dels paisatges.

La seva recerca integra la dendrocronologia, la reconstrucció de la història del foc, els inventaris forestals, la teledetecció i la modelització predictiva per comprendre com els ecosistemes responen a les pertorbacions i com la gestió del paisatge pot contribuir a reduir el risc d’incendi forestal. Durant onze anys als Estats Units, Alicia va desenvolupar la seva recerca a la Northern Arizona University com a investigadora doctoral i membre del professorat, col·laborant amb agències federals, gestors del territori i la tribu Mescalero Apache en l’estudi de la història del foc, la resiliència dels boscos i la gestió forestal adaptada al clima.

Actualment és investigadora postdoctoral a la Universitat de les Illes Balears, on estudia com les herències de l’ús històric del territori i els paisatges culturals mediterranis influeixen en el comportament dels incendis forestals i en la resiliència dels ecosistemes. La seva perspectiva científica contribueix a garantir que PyroMED combini una recerca ecològica rigorosa amb enfocaments pràctics per construir paisatges més resilients.

### Sean E. Henning

**Especialista operatiu i cofundador**

Sean Henning aporta a l’equip més de dues dècades d’experiència operativa directa en la gestió d’incendis forestals. Antic professional del Servei Forestal dels Estats Units (USFS), la seva experiència se situa en la intersecció entre les operacions de camp i els sistemes avançats d’anàlisi i suport a la presa de decisions.

Al llarg de la seva trajectòria, Sean ha desenvolupat i implementat plans operatius de perill d’incendi en boscos nacionals dels Estats Units i ha participat, com a bomber forestal, en centenars d’incendis forestals. Ha treballat com a especialista en informació geoespacial per a diversos boscos nacionals, el Southwest Geographic Area Coordination Center (GACC) i el National Interagency Coordination Center (NICC). La seva àmplia experiència en gestió d’incidents i sistemes de suport a la presa de decisions garanteix que els nostres marcs científics estiguin plenament alineats amb les necessitats pràctiques i el ritme de treball de la gestió dels incendis forestals.

### PyroMED

Com a cofundadors de PyroMED, Alicia i Sean han unit les seves trajectòries i experiències complementàries per crear una iniciativa sense ànim de lucre que superi les barreres tradicionals entre la comunitat científica i els professionals que treballen sobre el terreny.

Creiem que protegir els nostres paisatges requereix un llenguatge compartit. Mitjançant l’intercanvi internacional de coneixement i el desenvolupament d’eines basades en dades i adaptades a les realitats locals, volem donar eines a les comunitats i als gestors del territori perquè puguin adaptar-se de manera proactiva a un clima canviant i aprendre a conviure amb el foc de manera segura.

</div>


<!-- ENGLISH CONTENT -->
<div id="content-en" class="lang-content" style="display: none;" markdown="1">

### Dr. Alicia Azpeleta

**Principal Investigator & Co-Founder**

Dr. Alicia Azpeleta brings over a decade of international experience in forest and fire ecology, combining research across North America and the Mediterranean. Her expertise lies at the intersection of wildfire, climate change, forest resilience, and historical landscape dynamics.

Her research integrates dendrochronology, fire-history reconstruction, forest inventories, remote sensing, and predictive modelling to understand how ecosystems respond to disturbance and how landscape management can reduce wildfire risk. During eleven years in the United States, Alicia worked at Northern Arizona University as a doctoral researcher and faculty member, collaborating with federal agencies, land managers, and the Mescalero Apache Tribe on fire history, forest resilience, and climate-adaptive management.

She is currently a postdoctoral researcher at the Universitat de les Illes Balears, where her work focuses on how historical land-use legacies and Mediterranean cultural landscapes influence wildfire behaviour and ecosystem resilience. Her scientific perspective ensures that PyroMED combines rigorous ecological research with practical approaches to building more resilient landscapes.

### Sean E. Henning

**Operational Specialist & Co-Founder**

Sean Henning brings over two decades of boots-on-the-ground, operational wildfire experience to the team. A former practitioner with the United States Forest Service (USFS), Sean’s expertise lies at the critical intersection of field operations and advanced analytical systems.

Throughout his career, Sean has developed and implemented fire danger operating plans across U.S. National Forests and contributed to hundreds of active wildfire incidents. He has served as a geospatial specialist for multiple national forests, the Southwest Geographic Area Coordination Center (GACC), and the National Interagency Coordination Center (NICC). His deep immersion in incident management and decision-support systems ensures that our scientific frameworks are completely aligned with the fast-paced, practical realities of wildfire workflows and management needs.

### PyroMED

As co-founders of PyroMED, Alicia and Sean united their complementary backgrounds to create a non-profit initiative that breaks down the traditional silos between scientists and practitioners.

We believe that protecting our landscapes requires a shared language. By fostering international knowledge exchange and building data-driven, localized tools, we empower communities and land managers to proactively adapt to a changing climate and learn to safely coexist with fire.

</div>

<!-- JavaScript to handle language toggling -->
<script>
function setLanguage(lang) {
  const caDiv = document.getElementById('content-ca');
  const enDiv = document.getElementById('content-en');
  const btnCa = document.getElementById('btn-ca');
  const btnEn = document.getElementById('btn-en');

  if (lang === 'ca') {
    caDiv.style.display = 'block';
    enDiv.style.display = 'none';
    btnCa.style.backgroundColor = '#002b49';
    btnCa.style.color = 'white';
    btnCa.style.fontWeight = 'bold';
    btnEn.style.backgroundColor = '#e0e0e0';
    btnEn.style.color = '#333';
    btnEn.style.fontWeight = 'normal';
  } else {
    caDiv.style.display = 'none';
    enDiv.style.display = 'block';
    btnEn.style.backgroundColor = '#002b49';
    btnEn.style.color = 'white';
    btnEn.style.fontWeight = 'bold';
    btnCa.style.backgroundColor = '#e0e0e0';
    btnCa.style.color = '#333';
    btnCa.style.fontWeight = 'normal';
  }
}
</script>
