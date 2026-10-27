# Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren

De opvolger van de stroomprijs-blueprints: één automation die de Marstek-batterijen de hele dag op de Zonneplan-prijs laat letten.

📖 **Volledige uitleg, stap voor stap:** [Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren](https://fonferek.tech/blog/home-assistant-thuisbatterij-slim-laden-prijsdrempel/)

| Stand | Wanneer | Wat de batterij doet |
|---|---|---|
| **Laden van het net** | In de goedkoopste 2 aaneengesloten uren van de dag | Laadt geforceerd met vol vermogen |
| **Sparen** | Stroomprijs onder je prijsdrempel | Ontlaadt niet; zonne-overschot laadt wel bij |
| **Leveren** | Stroomprijs boven je prijsdrempel | Levert precies wat het huis verbruikt (`anti_feed`), niets aan het net |

## Snelst: als blueprint

[![Blueprint importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2Fthuisbatterij-slim-laden-2.0%2FMarstek_Slim_op_stroomprijs.yaml)

Importeer de blueprint en maak er een automatisering van. Je kiest je batterij-entiteiten, de stroomprijs, de prijsdrempel en de sensor voor het goedkoopste blok uit keuzelijsten; je hoeft niets in YAML te typen. Het laad- en ontlaadvermogen, de wachttijd na een herstart en eventuele automations voor handmatig laden stel je in onder **Instellingen**.

De twee helpers (prijsdrempel en goedkoopste blok) maak je eerst aan, zie hieronder.

## Alle bestanden

Liever geen blueprint? Gebruik dan [`automation-slim-op-stroomprijs.yaml`](automation-slim-op-stroomprijs.yaml) en vul zelf je entiteiten in.

| Bestand | Waar het heen moet | Wat je zelf invult |
|---|---|---|
| [`goedkoopste-uren-sensor.jinja`](goedkoopste-uren-sensor.jinja) | **Helpers → Template → Binaire sensor** "Goedkoopste 2 uur stroom", veld *Statussjabloon* | Niets. Alleen `uren = 2` als je een ander laadblok wilt |
| [`Marstek_Slim_op_stroomprijs.yaml`](Marstek_Slim_op_stroomprijs.yaml) | Blueprint: importeren met de knop hierboven | Alles via keuzelijsten |
| [`automation-slim-op-stroomprijs.yaml`](automation-slim-op-stroomprijs.yaml) | Nieuwe automatisering → Bewerken in YAML | Je batterij-entiteiten op de plekken met `JOUW_` |
| [`dashboard-kaart.yaml`](dashboard-kaart.yaml) | Kaart toevoegen → Handmatig (optioneel) | Niets |
| [`voorwaarden-handmatig-laden.yaml`](voorwaarden-handmatig-laden.yaml) | Alleen bij de losse YAML, boven `actions:` (optioneel). De blueprint heeft hier een instelling voor | De namen van je Opladen/Ontladen-automations |

Maak ook de helper **Batterij prijsdrempel** aan: **Helpers → Getal**, min 0, max 1, stap 0,01, eenheid €/kWh. Begin met **0,30** en kies daarna de juiste drempel met de tabel in de blog (laadprijs ÷ rendement, plus een paar cent).

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
- [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one) (HACS), voor de uurprijzen van vandaag
- Een eigen groep in de meterkast per batterij
