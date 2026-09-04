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

No existeix un únic model capaç de reproduir tots els processos que intervenen en un incendi. Els diferents models de simulació simplifiquen la realitat de maneres diferents segons allò que es vol estudiar.

Podem imaginar-los com una escala de complexitat:

-| Tipus de model              | Què intenta respondre?                                                               |
| --------------------------- | ------------------------------------------------------------------------------------ |
| **Comportament del foc**    | A quina velocitat i en quina direcció es pot propagar?                               |
| **Creixement de l'incendi** | Com pot evolucionar el perímetre i l'àrea cremada amb el temps?                      |
| **Propagació al paisatge**  | Com interactua el foc amb la topografia, els combustibles i el territori?            |
| **Foc i atmosfera**         | Com pot el foc modificar la meteorologia i generar el seu propi comportament extrem? |


És important entendre, però, que una simulació no prediu exactament el futur. Representa una possible evolució del foc a partir de les condicions introduïdes al model.

> Com més processos intentem representar, més complex és el model i més informació i capacitat de càlcul necessita.

## Un model simplificat

El simulador que trobaràs a continuació utilitza una aproximació simplificada per representar el creixement d'un incendi.

Primer, el model de Rothermel estima la velocitat de propagació del foc a partir de característiques com el combustible, el vent i la pendent.

Després, aquesta velocitat s'utilitza per representar com podria créixer espacialment l'incendi al llarg del temps.

L'objectiu no és predir exactament què farà un incendi real, sinó explorar com diferents condicions poden modificar el seu comportament.
---
<div class="page-navigation">
  <a href="/behaviour/" class="btn btn--primary">
    ← 		Què determina el comportament d'un incendi?
  </a>

  <a href="/simulador/" class="btn btn--primary">
    Simulador →
  </a>
</div>
