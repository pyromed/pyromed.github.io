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

Aquests models utilitzen equacions matemàtiques per representar els processos físics que intervenen en la propagació del foc i permeten estimar com podria comportar-se un incendi sota unes condicions determinades.

Una simulació pot ajudar a respondre preguntes com:

- A quina velocitat es podria propagar el foc?
- En quina direcció avançaria?
- Com canviaria el comportament de l'incendi si augmenta el vent?
- Què passa quan el foc arriba a una zona amb una pendent més pronunciada?
- Com influeix la humitat o el tipus de combustible en la propagació?

És important entendre, però, que una simulació no prediu exactament el futur. Representa una possible evolució del foc a partir de les condicions introduïdes al model.

## El model de Rothermel

El 1972, Richard Rothermel va desenvolupar un model matemàtic per estimar la velocitat de propagació d'un incendi de superfície.

La idea fonamental és relativament senzilla: el foc es propaga quan l'energia produïda per la combustió és suficient per escalfar i encendre el combustible que té al davant.

De manera simplificada, el model es pot expressar així:

$$ R = \frac{I_R \cdot \xi \cdot (1+\phi_w+\phi_s)} {\rho_b \cdot \epsilon \cdot Q_{ig}} $$

on R representa la velocitat de propagació del foc.

| Component        | Què representa?                                                                      |
| ---------------- | ------------------------------------------------------------------------------------ |
| **\(I_R\)**      | Energia produïda per la combustió                                                    |
| **\(\xi\)**      | Fracció de l'energia que contribueix a escalfar el combustible situat davant del foc |
| **\(\phi_w\)**   | Efecte del vent sobre la propagació                                                  |
| **\(\phi_s\)**   | Efecte de la pendent                                                                 |
| **\(\rho_b\)**   | Densitat del llit de combustible                                                     |
| **\(\epsilon\)** | Proporció del combustible que s'escalfa abans de cremar                              |
| **\(Q_{ig}\)**   | Energia necessària perquè el combustible arribi a la ignició                         |

> En essència, el model compara l'energia que el foc produeix i transmet cap endavant amb l'energia necessària per encendre el combustible.

---
<div class="page-navigation">
  <a href="/behaviour/" class="btn btn--primary">
    ← 		Què determina el comportament d'un incendi?
  </a>

  <a href="/simulador/" class="btn btn--primary">
    Simulador →
  </a>
</div>
