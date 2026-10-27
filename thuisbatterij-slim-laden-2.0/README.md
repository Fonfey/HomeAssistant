# Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren

De opvolger van de stroomprijs-blueprints: één automation die de Marstek-batterijen de hele dag op de Zonneplan-prijs laat letten.

📖 **Volledige uitleg, stap voor stap:** [Thuisbatterij slim laden 2.0: goedkoop laden, sparen voor de dure uren](https://fonferek.tech/blog/home-assistant-thuisbatterij-slim-laden-prijsdrempel/)

| Stand | Wanneer | Wat de batterij doet |
|---|---|---|
| **Laden van het net** | In de goedkoopste 2 aaneengesloten uren van de dag | Laadt geforceerd met vol vermogen |
| **Sparen** | Stroomprijs onder je prijsdrempel | Ontlaadt niet; zonne-overschot laadt wel bij |
| **Leveren** | Stroomprijs boven je prijsdrempel | Levert precies wat het huis verbruikt (`anti_feed`), niets aan het net |

## Bestanden

| Bestand | Waar het heen moet | Wat je zelf invult |
|---|---|---|
| [`goedkoopste-uren-sensor.jinja`](goedkoopste-uren-sensor.jinja) | **Helpers → Template → Binaire sensor** "Goedkoopste 2 uur stroom", veld *Statussjabloon* | Niets. Alleen `uren = 2` als je een ander laadblok wilt |
| [`automation-slim-op-stroomprijs.yaml`](automation-slim-op-stroomprijs.yaml) | Nieuwe automatisering → Bewerken in YAML | Je batterij-entiteiten op de plekken met `JOUW_` |
| [`dashboard-kaart.yaml`](dashboard-kaart.yaml) | Kaart toevoegen → Handmatig (optioneel) | Niets |
| [`voorwaarden-handmatig-laden.yaml`](voorwaarden-handmatig-laden.yaml) | In de automation, boven `actions:` (optioneel) | De namen van je Opladen/Ontladen-automations |

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
