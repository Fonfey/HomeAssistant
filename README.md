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
| [`Marstek_Veiligheid.yaml`](Marstek_Veiligheid.yaml) | Blueprint | Zet de batterijen terug naar standby als ze blijven hangen in laden of ontladen |
| [`Airco-Temperatuur_en_luchtvochtigheid.yaml`](Airco-Temperatuur_en_luchtvochtigheid.yaml) | Blueprint | Regelt een airco op temperatuur en luchtvochtigheid binnen een tijdvenster |
| [`EnergyDashboard.yaml`](EnergyDashboard.yaml) | Dashboard | Overzicht van stroomprijs, batterijen, zonnepanelen en verbruik |
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

Daarnaast is er **Marstek - Veiligheid**: een vangnet dat de batterijen terugzet als ze na een herstart of een gestopte automation blijven hangen in laden of ontladen. Dit wordt sterk aangeraden.

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

### Voor het dashboard

| Kaart / integratie | Waarvoor in het dashboard | Installeren |
|---|---|---|
| [Mushroom](https://github.com/piitaya/lovelace-mushroom) | De prijskaarten bovenaan: actuele prijs (groen, oranje of rood) en het goedkoopste en duurste uur van vandaag | HACS → Frontend |
| [B2500D Card](https://github.com/Neisi/b2500d-card) | De batterijkaarten *Marstek voor* en *Marstek achter*: laadniveau, zonne-invoer en vermogen per batterij | HACS → Frontend |
| [Entity Progress Card](https://github.com/francois-le-ko4la/lovelace-entity-progress-card) | De kaarten met een gekleurde balk: *Opgeslagen*, *Dagelijks ontladen*, *Verbruik*, *Zelfverbruik*, *Actuele ontlaad*, *Zonnepanelen* en *Meterkast belasting* | HACS → Frontend |
| [ApexCharts Card](https://github.com/RomRider/apexcharts-card) | De grafiek *Zon • batterij • net*: vermogen van zonnepanelen, batterij en net over de afgelopen 24 uur | HACS → Frontend |
| [Solcast PV Forecast](https://github.com/BJReplay/ha-solcast-solar) | De voorspelling in de kaart *Verwachte zon* (optioneel; zonder Solcast toont de kaart alleen de werkelijke opbrengst) | HACS → Integraties |

### Wat waarvoor nodig is

| Onderdeel | Zonneplan | Marstek Modbus | P1-meter | Zonnepanelen | Solcast | Helpers |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| Marstek - Opladen / Ontladen | | ✅ | | | | |
| Laden bij laagste stroomprijs | ✅ | ✅ | | optioneel | | ✅ |
| Ontladen bij hoogste stroomprijs | ✅ | ✅ | | | | ✅ |
| Marstek - Veiligheid | | ✅ | | | | |
| Airco Temperatuur en luchtvochtigheid | | | | | | |
| Energie-dashboard | ✅ | ✅ | ✅ | ✅ | optioneel | ✅ |

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
| Marstek - Veiligheid | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Veiligheid.yaml) |
| Airco Temperatuur en luchtvochtigheid | [![Importeren](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FAirco-Temperatuur_en_luchtvochtigheid.yaml) |

Na een update op GitHub kun je in Home Assistant bij de blueprint op **Opnieuw importeren** klikken om de nieuwste versie op te halen.

**Keuzelijsten tonen alleen passende entiteiten.** Zo kun je geen verkeerde entiteit kiezen, ook als de namen bij jou anders zijn:

| Veld | Toont alleen |
|---|---|
| Force mode, Werkmodus, RS485-besturing, (Ont)laadvermogen-entiteiten | Entiteiten van de [Marstek Venus Modbus](https://github.com/ViperRNMC/marstek_venus_modbus)-integratie |
| Huidig stroomtarief | Sensoren van de [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one)-integratie |
| Laadniveau (SoC) | Sensoren met apparaatklasse *Batterij* |
| Zonnepanelen vermogen | Sensoren met apparaatklasse *Vermogen* |
| Temperatuur- / luchtvochtigheidssensor | Sensoren met apparaatklasse *Temperatuur* / *Vochtigheid* |

Zie je je sensor niet in de lijst? Dan heeft hij waarschijnlijk geen (juiste) apparaatklasse. Bij een eigen template-sensor voeg je `device_class: battery` of `device_class: power` toe; bij andere sensoren kun je de apparaatklasse vaak instellen via de instellingen van de entiteit (**Weergeven als**).

---

## Marstek - Opladen

Laadt de batterijen geforceerd en zet ze daarna terug in de normale stand.

**Wat er gebeurt**
0. Alleen als minstens één batterij onder het ingestelde percentage zit (als je laadniveau-sensoren hebt gekozen)
1. Batterijen naar *standby*, werkmodus *anti_feed*, RS485-besturing uit
2. 10 seconden wachten
3. Laadvermogen op de ingestelde waarde, batterijen op *charge*, RS485-besturing aan
4. Laden gedurende de ingestelde laadduur, of korter: het laden stopt direct zodra alle batterijen 100% zijn (als je laadniveau-sensoren hebt gekozen) of de zonnepanelen meer dan 1000 W leveren (als je een zonnepanelen-sensor hebt gekozen)
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
| Laadniveau-sensoren (optioneel) | `sensor.marstek_1_battery_soc`. Eén per batterij; laden stopt zodra alle batterijen 100% zijn. Leeg = altijd de volledige laadduur |
| Alleen laden onder (optioneel) | Standaard 100%. Er wordt alleen geladen als minstens één batterij onder dit percentage zit. Werkt alleen met laadniveau-sensoren |
| Zonnepanelen vermogen (optioneel) | `sensor.pv_power`. Laden stopt zodra de zonnepanelen meer dan 1000 W leveren. Meerdere omvormers worden opgeteld. Leeg = hier niet op letten |

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
- de zonnepanelen **minder dan 1000 W** leveren (alleen als je een zonnepanelen-sensor hebt gekozen);
- de laad-automation niet al bezig is.

| Veld | Voorbeeld |
|---|---|
| Huidig stroomtarief | `sensor.zonneplan_current_electricity_tariff` |
| Batterij laadniveau (SoC) | `sensor.marstek_average_battery_soc` |
| Zonnepanelen vermogen (optioneel) | `sensor.pv_power`. Meerdere omvormers worden opgeteld. Leeg laten als je geen zonnepanelen hebt |
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

## Marstek - Veiligheid

Een vangnet voor als de batterijen blijven hangen in geforceerd laden of ontladen.

**Waarom is dit nodig?**

*Opladen* en *Ontladen* wachten tot de laad- of ontlaadduur voorbij is en zetten de batterijen daarna pas terug naar standby. Herstart Home Assistant in die tijd, of stop je de automation, dan wordt die laatste stap nooit uitgevoerd. De batterijen blijven dan laden of ontladen, met RS485-besturing aan, totdat je zelf ingrijpt.

**Wanneer grijpt hij in?**

- **Na een herstart van Home Assistant**, na 1 minuut (zodat de Marstek-integratie eerst verbinding heeft);
- **als een batterij langer dan de maximale duur** op *charge* of *discharge* staat.

Hij grijpt alleen in als een batterij nog op *charge* of *discharge* staat én *Opladen* en *Ontladen* allebei niet draaien. Een normale laadbeurt wordt dus nooit onderbroken.

**Wat doet hij?**

Dezelfde stappen als het einde van *Opladen* en *Ontladen*: force mode naar *standby*, werkmodus naar *anti_feed* en RS485-besturing uit. Optioneel krijg je een melding.

Na een herstart gaat het laden niet vanzelf verder. De stroomprijs-blueprints starten bij de volgende prijswijziging opnieuw als dat nodig is.

| Veld | Voorbeeld / standaard |
|---|---|
| Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | `select.marstek_1_user_work_mode` |
| RS485-besturing | `switch.marstek_venus_modbus_rs485_control_mode` |
| Laad-automation | `automation.marstek_opladen` |
| Ontlaad-automation | `automation.marstek_ontladen` |
| Maximale duur | Standaard 2 uur. Kies iets langer dan je langste laad- of ontlaadduur |
| Melding (optioneel) | Bijvoorbeeld `notify.mobile_app_telefoon`, of leeg laten |

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
4. Vervang alles door de inhoud van [`EnergyDashboard.yaml`](EnergyDashboard.yaml) en klik op **Opslaan**.
5. Vervang de entiteiten door die van jezelf (Ctrl+F in de editor).

> ⚠️ Plak dit niet in de ruwe editor van een bestaand dashboard: dan worden je andere tabbladen overschreven.

### Entiteiten om aan te passen

Bovenaan [`EnergyDashboard.yaml`](EnergyDashboard.yaml) staat een volledige lijst. In het kort:

| Onderdeel | Entiteiten |
|---|---|
| Stroomprijs | `sensor.zonneplan_current_electricity_tariff` |
| Zonnepanelen en net | `sensor.pv_power`, `sensor.p1_meter_vermogen` |
| Batterij 1 en 2 | `sensor.marstek_1_*`, `sensor.marstek_2_*` |
| Totalen (eigen template-sensoren) | `sensor.marstek_total_stored_energy`, `sensor.marstek_total_discharging_energy_today`, `sensor.marstek_total_ac_power` |
| Overige template-sensoren | `sensor.zonne_zelfverbruik_percentage`, `sensor.meterkast_belasting` |
| Knoppen | `automation.marstek_opladen`, `automation.marstek_ontladen` |

Mis je een sensor? Verwijder dan de kaart die hem gebruikt. Een ander aantal batterijen? Zie [Aantal batterijen](#aantal-batterijen).

---

## Aantal batterijen

Alles in deze repository is gemaakt voor **2 batterijen** (Marstek Venus E, 5,12 kWh en 2500 W per stuk). Heb je er 1, 3 of 4, dan pas je het volgende aan.

### Blueprints: niets aanpassen

*Opladen*, *Ontladen* en *Veiligheid* laten je bij elk veld meerdere entiteiten kiezen. Kies er gewoon één per batterij. De stroomprijs-blueprints gebruiken één SoC-sensor (het gemiddelde) en werken dus met elk aantal.

### Helpers ([`EnergyHelpers.yaml`](EnergyHelpers.yaml))

Vier sensoren tellen de batterijen bij elkaar op. Ze zijn in het bestand gemarkeerd met `AANTAL BATTERIJEN`.

| Sensor | 1 batterij | 3 of 4 batterijen |
|---|---|---|
| Marstek Total AC Power | Regel met `marstek_2` weghalen | Regel toevoegen voor `marstek_3` (en `marstek_4`) |
| Marstek Average Battery SoC | Regel met `marstek_2` weghalen en `/ 2` wijzigen in `/ 1` | Regels toevoegen en `/ 2` wijzigen in `/ 3` of `/ 4` |
| Marstek Total Stored Energy | Regel met `marstek_2` weghalen | Regel toevoegen voor `marstek_3` (en `marstek_4`) |
| Marstek Total Discharging Energy Today | Regel met `_2` weghalen | Regel toevoegen voor `_3` (en `_4`) |

> ⚠️ Let vooral op de deler `/ 2` bij de gemiddelde SoC. Laat je die bij 1 batterij staan, dan zie je de helft van het echte percentage. *Laden bij laagste stroomprijs* denkt dan bijvoorbeeld dat een volle batterij op 50% staat en gaat onterecht laden.

Voorbeeld voor 3 batterijen (gemiddelde SoC):

```yaml
state: >
  {{
    (
      (
        (states('sensor.marstek_1_battery_soc') | float(0)) +
        (states('sensor.marstek_2_battery_soc') | float(0)) +
        (states('sensor.marstek_3_battery_soc') | float(0))
      ) / 3
    ) | round(1)
  }}
```

### Dashboard ([`EnergyDashboard.yaml`](EnergyDashboard.yaml))

| Onderdeel | Wat aanpassen | 1 batterij | 2 batterijen | 3 batterijen | 4 batterijen |
|---|---|---|---|---|---|
| Batterijkaarten (*Marstek voor* / *achter*) | Eén kaart per batterij | Kaart *Marstek achter* verwijderen | – | Kaart kopiëren, `marstek_3` invullen | Twee kaarten kopiëren |
| *Opgeslagen* | `max_value` en laatste kleurgrens (kWh) | 5.12 | 10.24 | 15.36 | 20.48 |
| *Dagelijks Ontladen* | `max_value` en laatste kleurgrens (kWh) | 5.12 | 10.24 | 15.36 | 20.48 |
| *Actuele ontlaad* | `max_value` en laatste kleurgrens (W) | 2500 | 5000 | 7500 | 10000 |

De tussenliggende kleurgrenzen (bijvoorbeeld 3 en 7 kWh bij *Opgeslagen*) kun je naar verhouding mee schalen.

Heb je een ander model? Reken dan met de capaciteit (kWh) en het maximale vermogen (W) van dat model in plaats van 5,12 kWh en 2500 W.

---

## Let op

- De Zonneplan-forecast geeft prijzen in 1/10.000.000 euro; de blueprints rekenen dit zelf om.
- Gebruik je deze blueprints, zet dan je oude losse automations met dezelfde functie uit, zodat ze niet tegelijk starten.
- Gebruik op eigen risico. Controleer na het instellen of je batterijen doen wat je verwacht.

---

## Bijdragen en licentie

Vragen, bugs en verbeteringen zijn welkom; lees eerst [CONTRIBUTING.md](CONTRIBUTING.md). Dit project valt onder de [MIT-licentie](LICENSE).
