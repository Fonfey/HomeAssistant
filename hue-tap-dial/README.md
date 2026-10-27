# Philips Hue Tap Dial: één knop voor alles

De draaiknop dimt steeds wat je het laatst met een knop hebt aangezet. Knop 3 en 4 bladeren door de scènes uit de Hue-app, zonder dat je in Home Assistant een lijst hoeft bij te houden.

📖 **Uitleg:** [Hue Tap Dial in Home Assistant: één draaiknop voor meerdere lampen](https://fonferek.tech/blog/hue-tap-dial-home-assistant-een-knop-voor-alles/)

| Bestand | Kort indrukken | Lang indrukken |
|---|---|---|
| [`knop-1.yaml`](knop-1.yaml) | Lamp A aan, draaiknop dimt lamp A | Lamp A uit, draaiknop dimt groep C |
| [`knop-2.yaml`](knop-2.yaml) | Lamp B aan, draaiknop dimt lamp B | Lamp B uit |
| [`knop-3.yaml`](knop-3.yaml) | Volgende scène van lamp A | Lamp A uit |
| [`knop-4.yaml`](knop-4.yaml) | Volgende scène van lamp B | Lamp B uit |
| [`draaiknop.yaml`](draaiknop.yaml) | Dimt de actieve groep, 1–15% per beweging | |

## Eerst de helpers

- **Keuzelijst** `input_select.tap_dial_groep` met de opties `Lamp A`, `Lamp B` en `Groep C`
- **Tekst** `input_text.lamp_a_scene` en `input_text.lamp_b_scene` (maximale lengte 255)

Vervang daarna in de bestanden `event.tap_dial_*`, `light.lamp_a`, `light.lamp_b` en `light.groep_c` door je eigen entiteiten, en pas de namen in de keuzelijst aan als je die verandert. De scènes vinden zichzelf: alles wat begint met `scene.lamp_a_` hoort bij lamp A.
