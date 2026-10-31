# Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren

De opvolger van de stroomprijs-blueprints: één automation die de Marstek-batterijen de hele dag op de Zonneplan-prijs laat letten. Met een **dynamische drempel** levert de batterij alleen in de duurste uren tot het volgende laadblok, en met een **laaddoel** laadt hij overdag niet verder dan nodig, zodat er ruimte blijft voor de zon.

📖 **Volledige uitleg, stap voor stap:** [Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren](https://fonferek.nl/blog/home-assistant-thuisbatterij-slim-laden-prijsdrempel/)

| Stand | Wanneer | Wat de batterij doet |
|---|---|---|
| **Laden van het net** | In de goedkoopste 2 aaneengesloten uren van de dag, tot het laaddoel | Laadt geforceerd met vol vermogen |
| **Sparen** | Stroomprijs onder de drempel | Ontlaadt niet; zonne-overschot laadt wel bij |
| **Leveren** | Stroomprijs op of boven de drempel | Levert precies wat het huis verbruikt (`anti_feed`), niets aan het net |

## Snelst: als blueprint

[![Blueprint importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2Fthuisbatterij-slim-laden-2.0%2FMarstek_Slim_op_stroomprijs.yaml)

Importeer de blueprint en maak er een automatisering van. Je kiest alleen je **batterijen**; de blueprint zoekt per batterij zelf *Force Mode*, *User Work Mode*, *RS485 Control Mode*, *Set Charge Power* en *Max Discharge Power* erbij. Daarna kies je de stroomprijs, de drempel (de dynamische drempel, of een vaste prijs), de sensor voor het goedkoopste blok en optioneel het laaddoel. Je hoeft niets in YAML te typen. Een andere naam voor je batterij is geen probleem, en entiteit-ID's in het Engels, Nederlands of Duits worden herkend. Alleen als je de entiteit-ID's zelf hebt hernoemd, gebruik je de losse YAML. Het laad- en ontlaadvermogen, de wachttijd na een herstart en eventuele automations voor handmatig laden stel je in onder **Instellingen**.

De helpers maak je eerst aan, zie hieronder. Heb je de blueprint al eerder geïmporteerd? Importeer hem dan opnieuw (overschrijven) om de dynamische drempel en het laaddoel te kunnen kiezen; bestaande automatiseringen blijven werken.

## Alle bestanden

Liever geen blueprint? Gebruik dan [`automation-slim-op-stroomprijs.yaml`](automation-slim-op-stroomprijs.yaml) en vul zelf je entiteiten in.

| Bestand | Waar het heen moet | Wat je zelf invult |
|---|---|---|
| [`goedkoopste-uren-sensor.jinja`](goedkoopste-uren-sensor.jinja) | **Helpers → Template → Binaire sensor** "Goedkoopste 2 uur stroom", veld *Statussjabloon* | Niets. Alleen `uren = 2` als je een ander laadblok wilt |
| [`dynamische-drempel-sensor.jinja`](dynamische-drempel-sensor.jinja) | **Helpers → Template → Sensor** "Batterij dynamische drempel", eenheid €/kWh | Je SoC-sensoren (`JOUW_`), capaciteit, ondergrens-SoC en verbruiksprofiel |
| [`laaddoel-sensor.jinja`](laaddoel-sensor.jinja) | **Helpers → Template → Sensor** "Batterij laaddoel", eenheid % (optioneel) | Totale capaciteit, je Forecast.Solar-sensor en hetzelfde verbruiksprofiel |
| [`Marstek_Slim_op_stroomprijs.yaml`](Marstek_Slim_op_stroomprijs.yaml) | Blueprint: importeren met de knop hierboven | Alles via keuzelijsten |
| [`automation-slim-op-stroomprijs.yaml`](automation-slim-op-stroomprijs.yaml) | Nieuwe automatisering → Bewerken in YAML | Je batterij-entiteiten op de plekken met `JOUW_` |
| [`dashboard-kaart.yaml`](dashboard-kaart.yaml) | Kaart toevoegen → Handmatig (optioneel) | Niets |
| [`voorwaarden-handmatig-laden.yaml`](voorwaarden-handmatig-laden.yaml) | Alleen bij de losse YAML, boven `actions:` (optioneel). De blueprint heeft hier een instelling voor | De namen van je Opladen/Ontladen-automations |

## De vier helpers

1. **Batterij prijsdrempel**: **Helpers → Getal**, min 0, max 1, stap 0,01, eenheid €/kWh. Dit is de **ondergrens**: onder deze prijs levert de batterij nooit. Begin met **0,20** en kies daarna de juiste ondergrens met de tabel in de blog.
2. **Goedkoopste 2 uur stroom**: binaire sensor uit [`goedkoopste-uren-sensor.jinja`](goedkoopste-uren-sensor.jinja).
3. **Batterij dynamische drempel**: sensor uit [`dynamische-drempel-sensor.jinja`](dynamische-drempel-sensor.jinja). Verdeelt de energie in de batterij over de duurste uren tot het volgende laadblok; de prijs van het laatste gedekte uur is de drempel, nooit lager dan de ondergrens.
4. **Batterij laaddoel** (optioneel): sensor uit [`laaddoel-sensor.jinja`](laaddoel-sensor.jinja). 100% bij een laadblok 's nachts of een negatieve prijs; overdag minder, met ruimte voor de verwachte zonnestroom uit [Forecast.Solar](https://www.home-assistant.io/integrations/forecast_solar/). Zonder Forecast.Solar is het altijd 100%.

## Samen met de blueprints uit versie 1

De blueprints staan in [`thuisbatterij-blueprints/`](../thuisbatterij-blueprints/).

| Blueprint | Wat je ermee doet |
|---|---|
| Laden bij laagste stroomprijs | **Uitzetten**: deze automation doet het laden |
| Ontladen bij hoogste stroomprijs | **Uitzetten**: de stand *Leveren* vervangt hem |
| Opladen / Ontladen | Mag blijven als handmatige knop (met [`voorwaarden-handmatig-laden.yaml`](voorwaarden-handmatig-laden.yaml)) |
| Veiligheid | Mag blijven, met een maximale duur van minstens 2 uur en 15 minuten |

## Nodig

- [Marstek Venus Modbus](https://github.com/ViperRNMC/marstek_venus_modbus) (HACS), met de Local API aan in de Marstek-app
- [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one) (HACS), voor de uurprijzen van vandaag en morgen
- [Forecast.Solar](https://www.home-assistant.io/integrations/forecast_solar/) (optioneel), voor het laaddoel
- Een eigen groep in de meterkast per batterij
