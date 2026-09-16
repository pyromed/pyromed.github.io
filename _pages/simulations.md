---
title: "Com podem anticipar el comportament d'un incendi?"
layout: splash
permalink: /simulations/
header:
  overlay_image: /assets/images/andratx-hero.jpg
  overlay_filter: 0.35
---

## Simulació d'incendis forestals

Els incendis forestals són fenòmens complexos en què interactuen constantment la topografia, els combustibles i la meteorologia. Per comprendre com aquestes variables influeixen en la propagació del foc, els investigadors i els serveis de gestió utilitzen models de simulació d'incendis forestals.

<div style="text-align: center; margin: 20px 0;">
  <img src="/assets/images/area.jpg"
       alt="Comportament del foc"
       style="width: 50%; height: auto; border-radius: 6px;">

  <p style="font-size: 0.8em; font-style: italic; margin-top: 8px; color: #555; line-height: 1.4;">
    Figura: Representació simplificada del creixement el·líptic d'un incendi.
    La velocitat de propagació és màxima al cap de l'incendi, disminueix als
    flancs i és mínima a la cua, generant una àrea cremada de forma
    aproximadament el·líptica. Adaptada de Van Wagner (1969).
  </p>
</div>

No existeix un únic model capaç de reproduir tots els processos que intervenen en un incendi. Els diferents models de simulació simplifiquen la realitat de maneres diferents segons allò que es vol estudiar.

Podem imaginar-los com una escala de complexitat:

| Tipus de model              | Què intenta respondre?                                                               |
| --------------------------- | ------------------------------------------------------------------------------------ |
| **Comportament del foc**    | A quina velocitat i en quina direcció es pot propagar?                               |
| **Creixement de l'incendi** | Com pot evolucionar el perímetre i l'àrea cremada amb el temps?                      |
| **Propagació al paisatge**  | Com interactua el foc amb la topografia, els combustibles i el territori?            |
| **Foc i atmosfera**         | Com pot el foc modificar la meteorologia i generar el seu propi comportament extrem? |



És important entendre, però, que una simulació no prediu exactament el futur. Representa una possible evolució del foc a partir de les condicions introduïdes al model. Com més processos intentem representar, més complex és el model i més informació i capacitat de càlcul necessita.

> L'objectiu no és predir exactament què farà un incendi real, sinó explorar com diferents condicions poden modificar el seu comportament.
---

## Un model simplificat

El simulador que trobaràs a continuació utilitza una aproximació simplificada per representar el creixement d'un incendi.

El model de Rothermel és un model matemàtic que ajuda a estimar com de ràpid es propagarà un incendi. Per fer-ho, té en compte les característiques del combustible i factors com el vent i la pendent. La velocitat de propagació estimada es pot utilitzar després per representar com podria créixer l’incendi al llarg del temps.


<div class="page-navigation">
  <a href="/behaviour/" class="btn btn--primary">
    ← 		Què determina el comportament d'un incendi?
  </a>

  <a href="/simulador/" class="btn btn--primary">
    Simulador →
  </a>
</div>
