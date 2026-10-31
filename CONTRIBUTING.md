🇳🇱 **Nederlands** | 🇬🇧 English below

# Bijdragen

Leuk dat je wilt helpen! Dit project bevat de blueprints, automations en dashboards uit mijn Home Assistant-blogs op [fonferek.nl](https://fonferek.nl/), per onderwerp in een eigen map: de thuisbatterij (Marstek Venus en Zonneplan), het energie-overzicht, meldingen, de wandtablet en de Hue Tap Dial. Bijdragen in het Nederlands en in het Engels zijn allebei welkom.

## Een probleem melden

1. Kijk eerst bij de [bestaande issues](../../issues) of het probleem al bekend is.
2. Open een [nieuw issue](../../issues/new/choose) en kies **Bug melden**.
3. Vermeld in elk geval:
   - welke map en welk bestand of welke blueprint het betreft;
   - je versie van Home Assistant en van de integraties (Marstek Venus Modbus, Zonneplan ONE);
   - hoeveel batterijen je hebt;
   - wat je verwachtte en wat er gebeurde;
   - de **trace** van de automation (Instellingen → Automatiseringen → je automation → ⋮ → Traces), en eventueel regels uit het logboek.

Haal persoonlijke gegevens weg voordat je logboeken of traces deelt, zoals adressen, coördinaten, tokens en IP-adressen.

## Een idee voorstellen

Open een issue met **Idee of verbetering**. Beschrijf vooral welk probleem je wilt oplossen; dan kunnen we samen kijken naar de beste aanpak.

## Een pull request maken

1. Fork de repository en maak een branch met een duidelijke naam, bijvoorbeeld `fix/veiligheid-herstart`.
2. Maak je wijziging en **test die in je eigen Home Assistant**.
3. Werk de documentatie bij: de `README.md` van de map waarin je iets verandert, en bij een nieuw onderwerp ook het overzicht in [`README.md`](README.md) en [`README.en.md`](README.en.md). Hoort er een blog bij, dan pas ik die zelf aan.
4. Open een pull request en vul het sjabloon in.

### Richtlijnen voor blueprints

- Houd `source_url` gelijk aan de link naar het bestand op de `main`-branch, zodat het opnieuw importeren blijft werken. Verplaats of hernoem een blueprint daarom niet zonder goede reden.
- Hernoem of verwijder geen inputs van een bestaande blueprint; voeg nieuwe inputs toe met een `default:`, zodat bestaande automations blijven werken.
- Verhoog `homeassistant.min_version` als je een functie van een nieuwere Home Assistant gebruikt.
- Gebruik selectors met een `filter` (bijvoorbeeld `integration: marstek_modbus`), zodat gebruikers alleen passende entiteiten zien.
- Zet geen eigen entiteit-ID's vast in een blueprint; maak er een input van.
- Gebruik native Home Assistant-opties waar dat kan, en Jinja-templates alleen als het niet anders kan.
- Batterij-blueprints moeten blijven werken met één of meer batterijen.
- Denk aan veiligheid: een batterij mag nooit in geforceerd laden of ontladen blijven hangen. Controleer of [`Marstek_Veiligheid.yaml`](thuisbatterij-blueprints/Marstek_Veiligheid.yaml) je wijziging nog afdekt.
- Schrijf namen en beschrijvingen in het Nederlands, net als de bestaande blueprints.

### Dashboard en helpers

- Noem in de README van de map nieuwe HACS-kaarten of -integraties die het dashboard nodig heeft.
- Werk een screenshot (zoals `thuisbatterij-blueprints/EnergyDashboard.png`) alleen bij als de indeling echt verandert.
- Haal persoonlijke gegevens uit voorbeelden: geen eigen IP-adressen, namen of agenda-items.

## Gedragscode

Door bij te dragen ga je akkoord met de [gedragscode](CODE_OF_CONDUCT.md).

## Licentie

Bijdragen vallen onder de [MIT-licentie](LICENSE) van dit project.

---

# Contributing (English)

Thanks for helping out! Issues and pull requests in English are welcome.

- **Bugs:** check the [existing issues](../../issues), then open a [new one](../../issues/new/choose). Include the folder and file or blueprint, your Home Assistant and integration versions, the number of batteries, and the automation trace. Remove personal data first.
- **Ideas:** open an issue describing the problem you want to solve.
- **Pull requests:** test the change in your own Home Assistant, update the README of the folder you change, keep `source_url` pointing at `main` (don't move blueprints without a good reason), don't rename existing blueprint inputs, use filtered selectors instead of hard-coded entity IDs, and make sure a battery can never get stuck in forced charge or discharge.

By contributing you agree to the [Code of Conduct](CODE_OF_CONDUCT.md) and to license your work under the [MIT License](LICENSE).
