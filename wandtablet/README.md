# Wandtablet met Fully Kiosk, WallPanel en Kiosk Mode

Een Android-tablet aan de muur als dashboard, met een fotolijst als screensaver, pop-ups die vanzelf sluiten en een lader die alleen tussen 15% en 90% laadt.

📖 **Uitleg, inclusief de instellingen van Fully Kiosk:** [Wandtablet-dashboard met Fully Kiosk en WallPanel](https://fonferek.tech/blog/wandtablet-dashboard-fully-kiosk-wallpanel/)

| Bestand | Soort | Wat het doet |
|---|---|---|
| [`dashboard-config.yaml`](dashboard-config.yaml) | Dashboardconfiguratie | Kiosk Mode en WallPanel: kopbalk en zijbalk weg, fotolijst met klok, temperatuur en een melding voor morgen |
| [`bubble-card-popup.yaml`](bubble-card-popup.yaml) | Kaart | Voorbeeld van een pop-up (Bubble Card) die na 30 seconden sluit |
| [`automation-screensaver.yaml`](automation-screensaver.yaml) | Automatisering | Screensaver aan op het hoofdscherm, uit op andere pagina's, weer aan na wegtikken |
| [`automation-helderheid.yaml`](automation-helderheid.yaml) | Automatisering | Helderheid 35% met screensaver, 100% zonder |
| [`script-ajax-app.yaml`](script-ajax-app.yaml) | Script | Opent de Ajax-app en haalt na anderhalve minuut het dashboard terug |
| [`automation-laden.yaml`](automation-laden.yaml) | Automatisering | Lader (Shelly of slimme stekker) aan onder 15%, uit boven 90% |

## Nodig

- [Fully Kiosk Browser](https://www.fully-kiosk.com/) op de tablet (met licentie) en de [Fully Kiosk-integratie](https://www.home-assistant.io/integrations/fully_kiosk/)
- Via HACS: [WallPanel](https://github.com/j-a-n/lovelace-wallpanel), [Kiosk Mode](https://github.com/NemesisRE/kiosk-mode), [Bubble Card](https://github.com/Clooos/Bubble-Card) en [Digital Clock](https://github.com/wassy92x/lovelace-digital-clock)
- De helper `input_boolean.wallpanel_screensaver` (**Helpers → Helper aanmaken → Schakelaar**)

Gebruik in Fully Kiosk en in de automations het **lokale IP-adres** van Home Assistant, niet je Nabu Casa- of VPN-adres. Valt je internet weg, dan blijft de tablet gewoon werken.
