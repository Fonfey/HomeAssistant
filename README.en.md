🇳🇱 [Nederlands](README.md) | 🇬🇧 **English**

# Home Assistant – Marstek home battery, Zonneplan & air conditioning

Blueprints and a dashboard for Home Assistant to smartly charge and discharge a **Marstek Venus home battery** based on the **Zonneplan** electricity price, plus a blueprint to control an **air conditioner** based on temperature and humidity.

![Energy dashboard](https://github.com/Fonfey/HomeAssistant/blob/main/EnergyDashboard.png)

> **Note:** the blueprints and dashboard are in Dutch. The field names in Home Assistant are shown in Dutch below, with the English meaning next to them. [Zonneplan](https://www.zonneplan.nl/) is a Dutch energy supplier with dynamic hourly prices.

---

## Contents

| File | Type | What it does |
|---|---|---|
| [`Marstek_Opladen.yaml`](Marstek_Opladen.yaml) | Blueprint | Force-charges the batteries with an adjustable power and duration |
| [`Marstek_Ontladen.yaml`](Marstek_Ontladen.yaml) | Blueprint | Force-discharges the batteries with an adjustable power and duration |
| [`Marstek_Laden_laagste_stroomprijs.yaml`](Marstek_Laden_laagste_stroomprijs.yaml) | Blueprint | Starts charging at the cheapest hour of the day |
| [`Marstek_Ontladen_Hoogste_stroomprijs.yaml`](Marstek_Ontladen_Hoogste_stroomprijs.yaml) | Blueprint | Starts discharging at the most expensive hour of the day |
| [`Marstek_Veiligheid.yaml`](Marstek_Veiligheid.yaml) | Blueprint | Returns the batteries to standby if they get stuck charging or discharging |
| [`Airco-Temperatuur_en_luchtvochtigheid.yaml`](Airco-Temperatuur_en_luchtvochtigheid.yaml) | Blueprint | Controls an air conditioner based on temperature and humidity within a time window |
| [`EnergyDashboard.yaml`](EnergyDashboard.yaml) | Dashboard | Overview of electricity price, batteries, solar panels and consumption |
| [`EnergyHelpers.yaml`](EnergyHelpers.yaml) | Template sensors | Helpers required by the dashboard and blueprints |

---

## How it works together

The Marstek blueprints work in two layers:

```
Charge at lowest price      ──starts──▶   Marstek - Opladen   (charge)
Discharge at highest price  ──starts──▶   Marstek - Ontladen  (discharge)
```

1. **Opladen** (charge) and **Ontladen** (discharge) do the actual work: they put the batteries in the right mode and restore them afterwards. They have no trigger of their own.
2. **Lowest / highest price** decide *when* this happens and start the right automation.

So create the automations for **Opladen** and **Ontladen** first, then the price-based ones.

There is also **Marstek - Veiligheid** (safety): a safety net that restores the batteries if they get stuck charging or discharging after a restart or a stopped automation. This is strongly recommended.

---

## Requirements

### Home Assistant

- Home Assistant **2024.10** or newer
- [HACS](https://hacs.xyz/) for the integrations and cards below that are not built into Home Assistant

### Integrations

| Integration | Used for | Install via |
|---|---|---|
| [Zonneplan ONE](https://github.com/fsaris/home-assistant-zonneplan-one) | Current electricity price and today's prices (`sensor.zonneplan_current_electricity_tariff` with `forecast` attribute) | HACS → Integrations |
| [Marstek Venus Modbus](https://github.com/ViperRNMC/marstek_venus_modbus) | Controlling the batteries: *force mode*, *RS485 control mode*, *charge/discharge power* and daily discharge energy | HACS → Integrations |
| P1 meter, e.g. [DSMR Smart Meter](https://www.home-assistant.io/integrations/dsmr/) or [HomeWizard](https://www.home-assistant.io/integrations/homewizard/) | Power from and to the grid (`sensor.p1_meter_vermogen`) | Built into Home Assistant |
| Your solar inverter | Solar panel power (`sensor.pv_power`) | Depends on your brand |

### For the dashboard

| Card / integration | Used in the dashboard for | Install via |
|---|---|---|
| [Mushroom](https://github.com/piitaya/lovelace-mushroom) | The price cards at the top: current price (green, orange or red) and today's cheapest and most expensive hour | HACS → Frontend |
| [B2500D Card](https://github.com/Neisi/b2500d-card) | The battery cards *Marstek voor* and *Marstek achter*: state of charge, solar input and power per battery | HACS → Frontend |
| [Entity Progress Card](https://github.com/francois-le-ko4la/lovelace-entity-progress-card) | The cards with a coloured bar: stored energy, discharged today, consumption, self-sufficiency, current discharge, solar panels and grid connection load | HACS → Frontend |
| [ApexCharts Card](https://github.com/RomRider/apexcharts-card) | The *Zon • batterij • net* graph: solar, battery and grid power over the last 24 hours | HACS → Frontend |
| [Solcast PV Forecast](https://github.com/BJReplay/ha-solcast-solar) | The forecast in the *Solar forecast* card (optional; without Solcast the card only shows actual production) | HACS → Integrations |

### What is needed for what

| Component | Zonneplan | Marstek Modbus | P1 meter | Solar panels | Solcast | Helpers |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| Marstek - Opladen / Ontladen | | ✅ | | | | |
| Charge at lowest price | ✅ | ✅ | | optional | | ✅ |
| Discharge at highest price | ✅ | ✅ | | | | ✅ |
| Marstek - Veiligheid (safety) | | ✅ | | | | |
| Air conditioning temperature and humidity | | | | | | |
| Energy dashboard | ✅ | ✅ | ✅ | ✅ | optional | ✅ |

The air conditioning blueprint only needs an air conditioner (`climate` entity) and a temperature and humidity sensor.

---

## Importing the blueprints

Click a button below, or in Home Assistant go to **Settings → Automations & scenes → Blueprints → Import blueprint** and paste the link to the file.

| Blueprint | Import |
|---|---|
| Marstek - Opladen (charge) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Opladen.yaml) |
| Marstek - Ontladen (discharge) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Ontladen.yaml) |
| Marstek - Laden bij laagste stroomprijs (charge at lowest price) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Laden_laagste_stroomprijs.yaml) |
| Marstek - Ontladen bij hoogste stroomprijs (discharge at highest price) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Ontladen_Hoogste_stroomprijs.yaml) |
| Marstek - Veiligheid (safety) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FMarstek_Veiligheid.yaml) |
| Airco Temperatuur en luchtvochtigheid (air conditioning) | [![Import](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2FFonfey%2FHomeAssistant%2Fblob%2Fmain%2FAirco-Temperatuur_en_luchtvochtigheid.yaml) |

After an update on GitHub, click **Re-import blueprint** on the blueprint in Home Assistant to get the latest version.

---

## Marstek - Opladen (charge)

Force-charges the batteries and then returns them to normal mode.

**What happens**
1. Batteries to *standby*, work mode *anti_feed*, RS485 control off
2. Wait 10 seconds
3. Charge power set to the configured value, batteries to *charge*, RS485 control on
4. Charge for the configured duration, or shorter: charging stops as soon as all batteries reach 100% (if you selected state of charge sensors) or the solar panels produce more than 1000 W (if you selected a solar power sensor)
5. Back to *standby*, work mode *anti_feed*, RS485 control off

**Settings** (you can select multiple batteries for each entity)

| Field (Dutch) | Meaning | Example |
|---|---|---|
| Force mode | Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | Work mode | `select.marstek_1_user_work_mode` |
| RS485-besturing | RS485 control | `switch.marstek_venus_modbus_rs485_control_mode` |
| Laadvermogen-entiteiten | Charge power entities | `number.marstek_venus_modbus_set_charge_power` |
| Laadvermogen per batterij | Charge power per battery | Default 2500 W, freely adjustable |
| Laadduur | Charge duration | Default 1 hour |
| Laadniveau-sensoren (optioneel) | State of charge sensors (optional) | `sensor.marstek_1_battery_soc`. One per battery; charging stops as soon as all batteries reach 100%. Empty = always the full duration |
| Zonnepanelen vermogen (optioneel) | Solar panel power (optional) | `sensor.pv_power`. Charging stops as soon as solar produces more than 1000 W. Multiple inverters are added up. Empty = ignore |

**Choosing the charge and discharge power**

The power applies *per battery*. With two batteries at 2500 W you draw 5000 W from your grid connection. On a 1x35A connection (max. 8050 W) that leaves little room for the rest of the house. Never enter more than your battery can handle (check its specifications; a Marstek Venus E can do at most 2500 W), and choose a lower value if your connection can't handle it or if you want to charge more gently. Use the [Grid connection load](#grid-connection-load-meterkast-belasting) sensor to keep an eye on this.

## Marstek - Ontladen (discharge)

Works the same as *Opladen*, but puts the batteries in *discharge* mode with the configured discharge power.

| Field (Dutch) | Meaning | Example |
|---|---|---|
| Force mode | Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | Work mode | `select.marstek_1_user_work_mode` |
| RS485-besturing | RS485 control | `switch.marstek_venus_modbus_rs485_control_mode` |
| Ontlaadvermogen-entiteiten | Discharge power entities | `number.marstek_venus_modbus_set_discharge_power` |
| Ontlaadvermogen per batterij | Discharge power per battery | Default 2500 W, freely adjustable |
| Ontlaadduur | Discharge duration | Default 1 hour |

## Marstek - Laden bij laagste stroomprijs (charge at lowest price)

Checks on every price change whether the battery should start charging. The charge automation is started when:

- the current hour is the **cheapest hour of today**;
- the battery is **below 60%**;
- the solar panels produce **less than 1000 W** (only if you selected a solar power sensor);
- the charge automation is not already running.

| Field (Dutch) | Meaning | Example |
|---|---|---|
| Huidig stroomtarief | Current electricity price | `sensor.zonneplan_current_electricity_tariff` |
| Batterij laadniveau (SoC) | Battery state of charge | `sensor.marstek_average_battery_soc` |
| Zonnepanelen vermogen (optioneel) | Solar panel power (optional) | `sensor.pv_power`. Multiple inverters are added up. Leave empty if you don't have solar panels |
| Laad-automation | Charge automation | `automation.marstek_opladen` |

## Marstek - Ontladen bij hoogste stroomprijs (discharge at highest price)

Checks on every price change whether the battery should start discharging. The discharge automation is started when:

- the current hour is the **most expensive hour of today**;
- the battery is **above 90%**;
- the discharge automation is not already running.

| Field (Dutch) | Meaning | Example |
|---|---|---|
| Huidig stroomtarief | Current electricity price | `sensor.zonneplan_current_electricity_tariff` |
| Batterij laadniveau (SoC) | Battery state of charge | `sensor.marstek_average_battery_soc` |
| Ontlaad-automation | Discharge automation | `automation.marstek_ontladen` |

## Marstek - Veiligheid (safety)

A safety net for when the batteries get stuck in forced charging or discharging.

**Why is this needed?**

*Opladen* and *Ontladen* wait until the charge or discharge duration has passed and only then return the batteries to standby. If Home Assistant restarts during that time, or the automation is stopped, that last step never runs. The batteries then keep charging or discharging, with RS485 control on, until you intervene yourself.

**When does it step in?**

- **After a Home Assistant restart**, after 1 minute (so the Marstek integration has connected first);
- **when a battery stays in** *charge* or *discharge* **longer than the maximum duration**.

It only steps in when a battery is still in *charge* or *discharge* and *Opladen* and *Ontladen* are both not running. A normal charging session is never interrupted.

**What does it do?**

The same steps as the end of *Opladen* and *Ontladen*: force mode to *standby*, work mode to *anti_feed* and RS485 control off. Optionally you get a notification.

After a restart, charging does not resume automatically. The price-based blueprints start again at the next price change if needed.

| Field (Dutch) | Meaning | Example / default |
|---|---|---|
| Force mode | Force mode | `select.marstek_venus_modbus_force_mode` |
| Werkmodus | Work mode | `select.marstek_1_user_work_mode` |
| RS485-besturing | RS485 control | `switch.marstek_venus_modbus_rs485_control_mode` |
| Laad-automation | Charge automation | `automation.marstek_opladen` |
| Ontlaad-automation | Discharge automation | `automation.marstek_ontladen` |
| Maximale duur | Maximum duration | Default 2 hours. Choose slightly longer than your longest charge or discharge duration |
| Melding (optioneel) | Notification (optional) | E.g. `notify.mobile_app_phone`, or leave empty |

## Airco Temperatuur en luchtvochtigheid (air conditioning)

Controls an air conditioner between a start and end time. Every 15 minutes it checks:

| Situation | Action |
|---|---|
| Temperature above the maximum | Cool to 20 °C |
| Temperature below the minimum | Heat to 18 °C |
| Temperature OK, humidity above the maximum | Dry |
| Temperature OK, humidity below the minimum | Fan only |
| Temperature OK, humidity between 48 and 52% | Air conditioner off |
| End time reached | Air conditioner off |

| Field (Dutch) | Meaning | Default |
|---|---|---|
| Airco | Air conditioner | – |
| Temperatuursensor | Temperature sensor | – |
| Luchtvochtigheidssensor | Humidity sensor | – |
| Maximale temperatuur | Maximum temperature | 24 °C |
| Minimale temperatuur | Minimum temperature | 16 °C |
| Maximale luchtvochtigheid | Maximum humidity | 60% |
| Minimale luchtvochtigheid | Minimum humidity | 40% |
| Starttijd | Start time | 17:00 |
| Eindtijd | End time | 18:30 |

Create a separate automation from this blueprint for each room.

---

## Energy dashboard

A dashboard showing the current electricity price, the cheapest and most expensive hour of today, buttons to charge or discharge manually, the status of both batteries, consumption, self-sufficiency, and graphs of solar, battery and grid.

See [Requirements](#requirements) for the required integrations and cards.

### Required helpers

The dashboard uses a number of custom template sensors. They are in [`EnergyHelpers.yaml`](EnergyHelpers.yaml):

| Sensor | Used for |
|---|---|
| `sensor.zplan_laagste_tarief_vandaag`, `sensor.zplan_tijdstip_laagste_tarief` | Cheapest hour card |
| `sensor.zplan_hoogste_tarief_vandaag`, `sensor.zplan_tijdstip_hoogste_tarief` | Most expensive hour card |
| `sensor.marstek_total_ac_power` | Current discharge and graph |
| `sensor.marstek_total_stored_energy` | Stored energy |
| `sensor.marstek_total_discharging_energy_today` | Discharged today |
| `sensor.marstek_average_battery_soc` | The price-based blueprints |
| `sensor.zonne_zelfverbruik_percentage` | Self-sufficiency (see below) |
| `sensor.meterkast_belasting` | Grid connection load (see below) |

Put the file in your `packages` folder, or copy the sensors into your `configuration.yaml`, and reload via **Developer tools → YAML → Template entities**.

#### Self-sufficiency (Zelfverbruik)

Shows what share of your **current household consumption** you cover yourself with solar panels and battery, without power from the grid.

- **100%**: everything comes from solar and/or battery
- **0%**: everything comes from the grid

Household consumption is calculated as *solar + grid + battery* (grid positive when importing, battery positive when discharging). Exporting to the grid does not lower the percentage.

#### Grid connection load (Meterkast belasting)

Shows what percentage of your grid connection's maximum capacity you are using right now. This tells you whether you are getting close to what your main fuse can handle, for example when the battery is charging while the washing machine and oven are on.

The calculation is: *current grid power / maximum power × 100*. Both import and export count, because the fuse is loaded in both directions.

**Set `max_vermogen` to match your connection.** You can find it on your main fuse or in your energy contract (e.g. *1x35A* or *3x25A*). Calculate: *number of phases × amps × 230 V*.

| Connection | `max_vermogen` |
|---|---|
| 1x25A | 5750 |
| 1x35A | 8050 |
| 1x40A | 9200 |
| 3x25A | 17250 |
| 3x35A | 24150 |

> On a 3-phase connection this is an average across all phases, so a single phase can already be at its limit while the percentage is still low.

### Home Assistant Energy dashboard

The *Power sources*, *Energy usage*, *Energy distribution* and *Solar forecast* cards use data from the built-in Energy dashboard. Set that up first via **Settings → Dashboards → Energy** (grid, solar panels and battery), otherwise these cards stay empty.

For the *Solar forecast* card, add a **solar production forecast** to your solar panels in the Energy settings, for example from [Solcast](https://github.com/BJReplay/ha-solcast-solar). Without a forecast the card only shows actual production.

### Installation

1. Install the [integrations and cards](#requirements) and the [helpers](#required-helpers), then refresh your browser (Ctrl+F5).
2. Go to **Settings → Dashboards → Add dashboard → New dashboard from scratch**.
3. Open the new dashboard, click the **pencil → ⋮ → Raw configuration editor**.
4. Replace everything with the contents of [`EnergyDashboard.yaml`](EnergyDashboard.yaml) and click **Save**.
5. Replace the entities with your own (Ctrl+F in the editor).

> ⚠️ Do not paste this into the raw editor of an existing dashboard: it will overwrite your other tabs.

### Entities to adjust

A complete list is at the top of [`EnergyDashboard.yaml`](EnergyDashboard.yaml). In short:

| Component | Entities |
|---|---|
| Electricity price | `sensor.zonneplan_current_electricity_tariff` |
| Solar panels and grid | `sensor.pv_power`, `sensor.p1_meter_vermogen` |
| Battery 1 and 2 | `sensor.marstek_1_*`, `sensor.marstek_2_*` |
| Totals (custom template sensors) | `sensor.marstek_total_stored_energy`, `sensor.marstek_total_discharging_energy_today`, `sensor.marstek_total_ac_power` |
| Other template sensors | `sensor.zonne_zelfverbruik_percentage`, `sensor.meterkast_belasting` |
| Buttons | `automation.marstek_opladen`, `automation.marstek_ontladen` |

Missing a sensor? Remove the card that uses it. A different number of batteries? See [Number of batteries](#number-of-batteries).

---

## Number of batteries

Everything in this repository is set up for **2 batteries** (Marstek Venus E, 5.12 kWh and 2500 W each). If you have 1, 3 or 4, adjust the following.

### Blueprints: nothing to change

*Opladen*, *Ontladen* and *Veiligheid* let you select multiple entities in every field. Just pick one per battery. The price-based blueprints use a single SoC sensor (the average), so they work with any number of batteries.

### Helpers ([`EnergyHelpers.yaml`](EnergyHelpers.yaml))

Four sensors add up the batteries. They are marked with `AANTAL BATTERIJEN` (number of batteries) in the file.

| Sensor | 1 battery | 3 or 4 batteries |
|---|---|---|
| Marstek Total AC Power | Remove the `marstek_2` line | Add a line for `marstek_3` (and `marstek_4`) |
| Marstek Average Battery SoC | Remove the `marstek_2` line and change `/ 2` to `/ 1` | Add lines and change `/ 2` to `/ 3` or `/ 4` |
| Marstek Total Stored Energy | Remove the `marstek_2` line | Add a line for `marstek_3` (and `marstek_4`) |
| Marstek Total Discharging Energy Today | Remove the `_2` line | Add a line for `_3` (and `_4`) |

> ⚠️ Pay special attention to the `/ 2` divisor in the average SoC. If you leave it with 1 battery, you'll see half the real percentage. *Charge at lowest price* would then think a full battery is at 50% and start charging when it shouldn't.

Example for 3 batteries (average SoC):

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

| Component | What to change | 1 battery | 2 batteries | 3 batteries | 4 batteries |
|---|---|---|---|---|---|
| Battery cards (*Marstek voor* / *achter*) | One card per battery | Remove the *Marstek achter* card | – | Copy a card, fill in `marstek_3` | Copy two cards |
| *Opgeslagen* (stored) | `max_value` and last colour threshold (kWh) | 5.12 | 10.24 | 15.36 | 20.48 |
| *Dagelijks Ontladen* (discharged today) | `max_value` and last colour threshold (kWh) | 5.12 | 10.24 | 15.36 | 20.48 |
| *Actuele ontlaad* (current discharge) | `max_value` and last colour threshold (W) | 2500 | 5000 | 7500 | 10000 |

You can scale the intermediate colour thresholds (e.g. 3 and 7 kWh for *Opgeslagen*) proportionally.

Different model? Use that model's capacity (kWh) and maximum power (W) instead of 5.12 kWh and 2500 W.

---

## Notes

- The Zonneplan forecast gives prices in 1/10,000,000 euro; the blueprints convert this automatically.
- When you use these blueprints, disable any old automations that do the same thing, so they don't run at the same time.
- Use at your own risk. After setting up, check that your batteries behave as expected.
