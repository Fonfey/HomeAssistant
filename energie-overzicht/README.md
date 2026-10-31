# Energie-overzicht: wat kosten stroom en gas echt?

Per dag, maand en jaar zien wat je van het net haalt, wat je teruglevert, wat gas kost, wat zon en slim laden opleveren en wat je bespaart. Elke kWh en m³ tegen de prijs van dat moment.

📖 **Volledige uitleg, stap voor stap:** [Een eerlijk energie-overzicht in Home Assistant: wat kosten stroom en gas echt?](https://fonferek.nl/blog/home-assistant-energie-overzicht-kosten-besparing/)

| Bestand | Waar het heen moet | Wat je zelf invult |
|---|---|---|
| [`custom_templates/energie.jinja`](custom_templates/energie.jinja) | `custom_templates/energie.jinja` in je config-map | Je meters en je stroom- en gasprijs, bovenaan het bestand (alles met `JOUW_`) |
| [`packages/energie_overzicht.yaml`](packages/energie_overzicht.yaml) | `packages/energie_overzicht.yaml` | Niets |
| [`dashboard-weergave.yaml`](dashboard-weergave.yaml) | Onder `views:` in de Raw-configuratie-editor van je dashboard | Niets |
| [`automation-avondbericht.yaml`](automation-avondbericht.yaml) | Nieuwe automatisering → Bewerken in YAML | Je telefoon (`notify.mobile_app_JOUW_TELEFOON`) |

Packages moeten aanstaan in `configuration.yaml`:

```yaml
homeassistant:
  packages: !include_dir_named packages
```

Daarna: **Reload custom templates**, configuratie controleren en **Sjabloon-entiteiten** herladen (of Home Assistant herstarten als je packages net hebt aangezet). Je ziet dan 39 sensoren die beginnen met `sensor.energie_`.

Nodig: HACS met [Mushroom](https://github.com/piitaya/lovelace-mushroom) voor het dashboard, en optioneel [Forecast.Solar](https://www.home-assistant.io/integrations/forecast_solar/) voor de zonverwachting in het avondbericht.
