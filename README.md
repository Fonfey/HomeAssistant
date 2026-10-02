# Home Assistant – Marstek thuisbatterij, Zonneplan & airco

Blueprints en een dashboard voor Home Assistant om een **Marstek Venus thuisbatterij** slim te laden en ontladen op basis van de **Zonneplan**-stroomprijs, plus een blueprint om een **airco** te regelen op temperatuur en luchtvochtigheid.

![Energie-dashboard](https://github.com/Fonfey/HomeAssistant/blob/main/EnergyDashboard.png)

---

## Inhoud

| Bestand | Soort | Wat het doet |
|---|---|---|
| [`Marstek_Opladen.yaml`](Marstek_Opladen.yaml) | Blueprint | Laadt de batterijen geforceerd met 2500 W gedurende een in te stellen tijd |
| [`Marstek_Ontladen.yaml`](Marstek_Ontladen.yaml) | Blueprint | Ontlaadt de batterijen geforceerd met 2500 W gedurende een in te stellen tijd |
| [`Marstek_Laden_laagste_stroomprijs.yaml`](Marstek_Laden_laagste_stroomprijs.yaml) | Blueprint | Start het opladen op het goedkoopste uur van de dag |
| [`Marstek_Ontladen_Hoogste_stroomprijs.yaml`](Marstek_Ontladen_Hoogste_stroomprijs.yaml) | Blueprint | Start het ontladen op het duurste uur van de dag |
| [`Airco-Temperatuur_en_luchtvochtigheid.yaml`](Airco-Temperatuur_en_luchtvochtigheid.yaml) | Blueprint | Regelt een airco op temperatuur en luchtvochtigheid binnen een tijdvenster |
| [`energie_dashboard.yaml`](energie_dashboard.yaml) | Dashboard | Overzicht van stroomprijs, batterijen, zonnepanelen en verbruik |

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

- Home Assistant 2024.10 of nieuwer
- [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one)-integratie (voor `sensor.zonneplan_current_electricity_tariff` met `forecast`-attribuut)
- Marstek Venus via Modbus, met de entiteiten voor *force mode*, *user work mode*, *RS485 control mode* en *charge/discharge power*
- Een sensor met het vermogen van je zonnepanelen (voor *Laden bij laagste stroomprijs*)

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
3. Laadvermogen op 2500 W, batterijen op *charge*, RS485-besturing aan
4. Laden gedurende de ingestelde laadduur
5. Terug naar *standby*, werkmodus *anti_feed*, RS485-besturing uit

**Instellingen** (bij elke entiteit kun je meerdere batterijen kiezen)

| Veld | Voorbeeld |
|---|---|
| Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | `select.marstek_1_user_work_mode` |
| RS485-besturing | `switch.marstek_venus_modbus_rs485_control_mode` |
| Laadvermogen | `number.marstek_venus_modbus_set_charge_power` |
| Laadduur | Standaard 1 uur |

## Marstek - Ontladen

Werkt hetzelfde als *Opladen*, maar zet de batterijen op *discharge* met 2500 W ontlaadvermogen.

| Veld | Voorbeeld |
|---|---|
| Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | `select.marstek_1_user_work_mode` |
| RS485-besturing | `switch.marstek_venus_modbus_rs485_control_mode` |
| Ontlaadvermogen | `number.marstek_venus_modbus_set_discharge_power` |
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

### Benodigde kaarten (via HACS → Frontend)

- [Mushroom](https://github.com/piitaya/lovelace-mushroom)
- [B2500D Card](https://ha.fonferek.cloud/hacs/repository/1052179687)
- [Entity Progress Card](https://github.com/francois-le-ko4la/lovelace-entity-progress-card)
- [ApexCharts Card](https://github.com/RomRider/apexcharts-card)

### Installeren

1. Installeer de kaarten hierboven en ververs je browser (Ctrl+F5).
2. Ga naar **Instellingen → Dashboards → Dashboard toevoegen → Nieuw dashboard vanaf nul**.
3. Open het nieuwe dashboard, klik op het **potlood → ⋮ → Ruwe configuratie-editor**.
4. Vervang alles door de inhoud van [`energie_dashboard.yaml`](energie_dashboard.yaml) en klik op **Opslaan**.
5. Vervang de entiteiten door die van jezelf (Ctrl+F in de editor).

> ⚠️ Plak dit niet in de ruwe editor van een bestaand dashboard: dan worden je andere tabbladen overschreven.

### Entiteiten om aan te passen

Bovenaan [`energie_dashboard.yaml`](energie_dashboard.yaml) staat een volledige lijst. In het kort:

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
