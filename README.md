🇳🇱 **Nederlands** | 🇬🇧 [English](README.en.md)

# Home Assistant – Marstek thuisbatterij, Zonneplan & airco

Blueprints en een dashboard voor Home Assistant om een **Marstek Venus thuisbatterij** slim te laden en ontladen op basis van de **Zonneplan**-stroomprijs, plus een blueprint om een **airco** te regelen op temperatuur en luchtvochtigheid.

![Energie-dashboard](https://github.com/Fonfey/HomeAssistant/blob/main/EnergyDashboard.png)

---

## Inhoud

| Bestand | Soort | Wat het doet |
|---|---|---|
| [`Marstek_Opladen.yaml`](Marstek_Opladen.yaml) | Blueprint | Laadt de batterijen geforceerd met een in te stellen vermogen en tijd |
| [`Marstek_Ontladen.yaml`](Marstek_Ontladen.yaml) | Blueprint | Ontlaadt de batterijen geforceerd met een in te stellen vermogen en tijd |
| [`Marstek_Laden_laagste_stroomprijs.yaml`](Marstek_Laden_laagste_stroomprijs.yaml) | Blueprint | Start het opladen op het goedkoopste uur van de dag |
| [`Marstek_Ontladen_Hoogste_stroomprijs.yaml`](Marstek_Ontladen_Hoogste_stroomprijs.yaml) | Blueprint | Start het ontladen op het duurste uur van de dag |
| [`Airco-Temperatuur_en_luchtvochtigheid.yaml`](Airco-Temperatuur_en_luchtvochtigheid.yaml) | Blueprint | Regelt een airco op temperatuur en luchtvochtigheid binnen een tijdvenster |
| [`EnergyDashboad.txt`](EnergyDashboad.txt) | Dashboard | Overzicht van stroomprijs, batterijen, zonnepanelen en verbruik |
| [`EnergyHelpers.yaml`](EnergyHelpers.yaml) | Template-sensoren | Helpers die het dashboard en de blueprints nodig hebben |

---

## Hoe het samenwerkt

De Marstek-blueprints werken in twee lagen:

```
Laden bij laagste stroomprijs   ──start──▶   Marstek - Opladen
Ontladen bij hoogste stroomprijs ──start──▶   Marstek - Ontladen
```

1. **Opladen** en **Ontladen** doen het echte werk: ze zetten de batterijen in de juiste modus en zetten ze na afloop terug. Ze hebben geen eigen trigger.
2. **Laagste / hoogste stroomprijs** bepalen *wanneer* dat gebeurt en starten de juiste automation.

Maak daarom eerst de automations voor **Opladen** en **Ontladen** aan, en daarna die voor de stroomprijs.

---

## Vereisten

### Home Assistant

- Home Assistant **2024.10** of nieuwer
- [HACS](https://hacs.xyz/) voor de integraties en kaarten hieronder die niet standaard in Home Assistant zitten

### Integraties

| Integratie | Waarvoor | Installeren |
|---|---|---|
| [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one) | Actuele stroomprijs en prijzen van vandaag (`sensor.zonneplan_current_electricity_tariff` met `forecast`-attribuut) | HACS → Integraties |
| [Marstek Venus Modbus](https://github.com/ViperRNMC/marstek_venus_modbus) | Aansturen van de batterijen: *force mode*, *RS485 control mode*, *charge/discharge power* en dagelijkse ontlaadenergie | HACS → Integraties |
| P1-meter, bijvoorbeeld [DSMR Smart Meter](https://www.home-assistant.io/integrations/dsmr/) of [HomeWizard](https://www.home-assistant.io/integrations/homewizard/) | Vermogen van en naar het net (`sensor.p1_meter_vermogen`) | Standaard in Home Assistant |
| Omvormer van je zonnepanelen | Vermogen van de zonnepanelen (`sensor.pv_power`) | Afhankelijk van je merk |
| [Solcast PV Forecast](https://github.com/BJReplay/ha-solcast-solar) | Verwachte zonne-opbrengst voor de kaart *Verwachte zon* (optioneel) | HACS → Integraties |

### Kaarten (alleen voor het dashboard)

| Kaart | Installeren |
|---|---|
| [Mushroom](https://github.com/piitaya/lovelace-mushroom) | HACS → Frontend |
| [B2500D Card](https://github.com/Neisi/b2500d-card) | HACS → Frontend |
| [Entity Progress Card](https://github.com/francois-le-ko4la/lovelace-entity-progress-card) | HACS → Frontend |
| [ApexCharts Card](https://github.com/RomRider/apexcharts-card) | HACS → Frontend |

### Wat waarvoor nodig is

| Onderdeel | Zonneplan | Marstek Modbus | P1-meter | Zonnepanelen | Solcast | Helpers | Kaarten |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Marstek - Opladen / Ontladen | | ✅ | | | | | |
| Laden bij laagste stroomprijs | ✅ | ✅ | | ✅ | | ✅ | |
| Ontladen bij hoogste stroomprijs | ✅ | ✅ | | | | ✅ | |
| Airco Temperatuur en luchtvochtigheid | | | | | | | |
| Energie-dashboard | ✅ | ✅ | ✅ | ✅ | optioneel | ✅ | ✅ |

De airco-blueprint heeft alleen een airco (`climate`-entiteit) en een temperatuur- en luchtvochtigheidssensor nodig.

---

## Blueprints importeren

Klik op een knop hieronder, of ga in Home Assistant naar **Instellingen → Automatiseringen en scènes → Blueprints → Blueprint importeren** en plak de link naar het bestand.

| Blueprint | Importeren |
|---|---|
| Marstek - Opladen | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Opladen.yaml) |
| Marstek - Ontladen | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Ontladen.yaml) |
| Marstek - Laden bij laagste stroomprijs | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Laden_laagste_stroomprijs.yaml) |
| Marstek - Ontladen bij hoogste stroomprijs | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Ontladen_Hoogste_stroomprijs.yaml) |
| Airco Temperatuur en luchtvochtigheid | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FAirco-Temperatuur_en_luchtvochtigheid.yaml) |

Na een update op GitHub kun je in Home Assistant bij de blueprint op **Opnieuw importeren** klikken om de nieuwste versie op te halen.

---

## Marstek - Opladen

Laadt de batterijen geforceerd en zet ze daarna terug in de normale stand.

**Wat er gebeurt**
1. Batterijen naar *standby*, werkmodus *anti_feed*, RS485-besturing uit
2. 10 seconden wachten
3. Laadvermogen op de ingestelde waarde, batterijen op *charge*, RS485-besturing aan
4. Laden gedurende de ingestelde laadduur
5. Terug naar *standby*, werkmodus *anti_feed*, RS485-besturing uit

**Instellingen** (bij elke entiteit kun je meerdere batterijen kiezen)

| Veld | Voorbeeld |
|---|---|
| Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | `select.marstek_1_user_work_mode` |
| RS485-besturing | `switch.marstek_venus_modbus_rs485_control_mode` |
| Laadvermogen-entiteiten | `number.marstek_venus_modbus_set_charge_power` |
| Laadvermogen per batterij | Standaard 2500 W, zelf in te vullen |
| Laadduur | Standaard 1 uur |

**Laad- en ontlaadvermogen kiezen**

Het vermogen geldt *per batterij*. Met twee batterijen op 2500 W vraag je dus 5000 W van je aansluiting. Bij een 1x35A-aansluiting (max. 8050 W) blijft er dan weinig ruimte over voor de rest van het huis. Vul nooit meer in dan je batterij aankan (kijk in de specificaties; een Marstek Venus E kan maximaal 2500 W), en kies een lagere waarde als je aansluiting dat niet aankan of als je rustiger wilt laden. Gebruik de sensor [Meterkast belasting](#meterkast-belasting) om dit in de gaten te houden.

## Marstek - Ontladen

Werkt hetzelfde als *Opladen*, maar zet de batterijen op *discharge* met het ingestelde ontlaadvermogen.

| Veld | Voorbeeld |
|---|---|
| Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | `select.marstek_1_user_work_mode` |
| RS485-besturing | `switch.marstek_venus_modbus_rs485_control_mode` |
| Ontlaadvermogen-entiteiten | `number.marstek_venus_modbus_set_discharge_power` |
| Ontlaadvermogen per batterij | Standaard 2500 W, zelf in te vullen |
| Ontlaadduur | Standaard 1 uur |

## Marstek - Laden bij laagste stroomprijs

Controleert bij elke prijswijziging of de batterij moet gaan laden. De laad-automation wordt gestart als:

- het huidige uur het **goedkoopste uur van vandaag** is;
- de batterij **onder 60%** zit;
- de zonnepanelen **minder dan 1000 W** leveren;
- de laad-automation niet al bezig is.

| Veld | Voorbeeld |
|---|---|
| Huidig stroomtarief | `sensor.zonneplan_current_electricity_tariff` |
| Batterij laadniveau (SoC) | `sensor.marstek_average_battery_soc` |
| Zonnepanelen vermogen | `sensor.pv_power` |
| Laad-automation | `automation.marstek_opladen` |

## Marstek - Ontladen bij hoogste stroomprijs

Controleert bij elke prijswijziging of de batterij moet gaan ontladen. De ontlaad-automation wordt gestart als:

- het huidige uur het **duurste uur van vandaag** is;
- de batterij **boven 90%** zit;
- de ontlaad-automation niet al bezig is.

| Veld | Voorbeeld |
|---|---|
| Huidig stroomtarief | `sensor.zonneplan_current_electricity_tariff` |
| Batterij laadniveau (SoC) | `sensor.marstek_average_battery_soc` |
| Ontlaad-automation | `automation.marstek_ontladen` |

## Airco Temperatuur en luchtvochtigheid

Regelt een airco tussen een start- en eindtijd. Elke 15 minuten wordt gecontroleerd:

| Situatie | Actie |
|---|---|
| Temperatuur boven het maximum | Koelen naar 20 °C |
| Temperatuur onder het minimum | Verwarmen naar 18 °C |
| Temperatuur in orde, luchtvochtigheid boven het maximum | Drogen |
| Temperatuur in orde, luchtvochtigheid onder het minimum | Alleen ventileren |
| Temperatuur in orde, luchtvochtigheid tussen 48 en 52% | Airco uit |
| Eindtijd bereikt | Airco uit |

| Veld | Standaard |
|---|---|
| Airco | – |
| Temperatuursensor | – |
| Luchtvochtigheidssensor | – |
| Maximale temperatuur | 24 °C |
| Minimale temperatuur | 16 °C |
| Maximale luchtvochtigheid | 60% |
| Minimale luchtvochtigheid | 40% |
| Starttijd | 17:00 |
| Eindtijd | 18:30 |

Maak per kamer een eigen automation aan met deze blueprint.

---

## Energie-dashboard

Een dashboard met de actuele stroomprijs, het goedkoopste en duurste uur van vandaag, knoppen om handmatig te laden of ontladen, de status van beide batterijen, verbruik, zelfverbruik en grafieken van zon, batterij en net.

Zie [Vereisten](#vereisten) voor de benodigde integraties en kaarten.

### Benodigde helpers

Het dashboard gebruikt een aantal eigen template-sensoren. Die staan in [`EnergyHelpers.yaml`](EnergyHelpers.yaml):

| Sensor | Gebruikt voor |
|---|---|
| `sensor.zplan_laagste_tarief_vandaag`, `sensor.zplan_tijdstip_laagste_tarief` | Kaart met het goedkoopste uur |
| `sensor.zplan_hoogste_tarief_vandaag`, `sensor.zplan_tijdstip_hoogste_tarief` | Kaart met het duurste uur |
| `sensor.marstek_total_ac_power` | Actuele ontlaad en grafiek |
| `sensor.marstek_total_stored_energy` | Opgeslagen |
| `sensor.marstek_total_discharging_energy_today` | Dagelijks ontladen |
| `sensor.marstek_average_battery_soc` | De stroomprijs-blueprints |
| `sensor.zonne_zelfverbruik_percentage` | Zelfverbruik (zie hieronder) |
| `sensor.meterkast_belasting` | Meterkast belasting (zie hieronder) |

Zet het bestand in je `packages`-map, of kopieer de sensoren naar je `configuration.yaml`, en herlaad via **Ontwikkelhulpmiddelen → YAML → Template-entiteiten**.

#### Zelfverbruik

Laat zien welk deel van je **huidige verbruik thuis** je zelf dekt met zonnepanelen en batterij, zonder stroom van het net.

- **100%**: alles komt van zon en/of batterij
- **0%**: alles komt van het net

Het verbruik thuis wordt berekend als *zon + net + batterij* (net positief bij afname, batterij positief bij ontladen). Teruglevering verlaagt het percentage niet.

#### Meterkast belasting

Laat zien hoeveel procent van de maximale capaciteit van je aansluiting je op dit moment gebruikt. Zo zie je of je in de buurt komt van wat je hoofdzekering aankan, bijvoorbeeld als de batterij laadt terwijl ook de wasmachine en oven aan staan.

De berekening is: *huidig netvermogen / maximaal vermogen × 100*. Afname en teruglevering tellen allebei mee, omdat de zekering in beide richtingen belast wordt.

**Pas `max_vermogen` aan op jouw aansluiting.** Die vind je op je hoofdzekering of energiecontract (bijvoorbeeld *1x35A* of *3x25A*). Reken: *aantal fases × ampère × 230 V*.

| Aansluiting | `max_vermogen` |
|---|---|
| 1x25A | 5750 |
| 1x35A | 8050 |
| 1x40A | 9200 |
| 3x25A | 17250 |
| 3x35A | 24150 |

> Bij een 3-fase-aansluiting is dit een gemiddelde over alle fases. Eén fase kan dus al vol zitten terwijl het percentage nog laag is.

### Energie-dashboard van Home Assistant

De kaarten *Verbruik per bron*, *Energiegebruik*, *Energieverdeling* en *Verwachte zon* gebruiken de gegevens van het ingebouwde Energie-dashboard. Stel dat eerst in via **Instellingen → Dashboards → Energie** (net, zonnepanelen en batterij), anders blijven deze kaarten leeg.

Voor de kaart *Verwachte zon* voeg je bij je zonnepanelen in de Energie-instellingen een **opbrengstvoorspelling** toe, bijvoorbeeld van [Solcast](https://github.com/BJReplay/ha-solcast-solar). Zonder voorspelling toont de kaart alleen de werkelijke opbrengst.

### Installeren

1. Installeer de [integraties en kaarten](#vereisten) en de [helpers](#benodigde-helpers), en ververs je browser (Ctrl+F5).
2. Ga naar **Instellingen → Dashboards → Dashboard toevoegen → Nieuw dashboard vanaf nul**.
3. Open het nieuwe dashboard, klik op het **potlood → ⋮ → Ruwe configuratie-editor**.
4. Vervang alles door de inhoud van [`EnergyDashboad.txt`](EnergyDashboad.txt) en klik op **Opslaan**.
5. Vervang de entiteiten door die van jezelf (Ctrl+F in de editor).

> ⚠️ Plak dit niet in de ruwe editor van een bestaand dashboard: dan worden je andere tabbladen overschreven.

### Entiteiten om aan te passen

Bovenaan [`EnergyDashboad.txt`](EnergyDashboad.txt) staat een volledige lijst. In het kort:

| Onderdeel | Entiteiten |
|---|---|
| Stroomprijs | `sensor.zonneplan_current_electricity_tariff` |
| Zonnepanelen en net | `sensor.pv_power`, `sensor.p1_meter_vermogen` |
| Batterij 1 en 2 | `sensor.marstek_1_*`, `sensor.marstek_2_*` |
| Totalen (eigen template-sensoren) | `sensor.marstek_total_stored_energy`, `sensor.marstek_total_discharging_energy_today`, `sensor.marstek_total_ac_power` |
| Overige template-sensoren | `sensor.zonne_zelfverbruik_percentage`, `sensor.meterkast_belasting` |
| Knoppen | `automation.marstek_opladen`, `automation.marstek_ontladen` |

Heb je maar één batterij of mis je een sensor? Verwijder dan de kaart die hem gebruikt.

---

## Let op

- De Zonneplan-forecast geeft prijzen in 1/10.000.000 euro; de blueprints rekenen dit zelf om.
- Gebruik je deze blueprints, zet dan je oude losse automations met dezelfde functie uit, zodat ze niet tegelijk starten.
- Gebruik op eigen risico. Controleer na het instellen of je batterijen doen wat je verwacht.
