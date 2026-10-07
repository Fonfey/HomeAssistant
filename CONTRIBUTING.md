🇳🇱 **Nederlands** | 🇬🇧 English below

# Bijdragen

Leuk dat je wilt helpen! Dit project bevat Home Assistant-blueprints, een dashboard en helpers voor een Marstek Venus thuisbatterij, Zonneplan-stroomprijzen en een airco. Bijdragen in het Nederlands en in het Engels zijn allebei welkom.

## Een probleem melden

1. Kijk eerst bij de [bestaande issues](../../issues) of het probleem al bekend is.
2. Open een [nieuw issue](../../issues/new/choose) en kies **Bug melden**.
3. Vermeld in elk geval:
   - welke blueprint of welk bestand het betreft;
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
3. Werk de documentatie bij in **zowel [`README.md`](README.md) als [`README.en.md`](README.en.md)**.
4. Open een pull request en vul het sjabloon in.

### Richtlijnen voor blueprints

- Houd `source_url` gelijk aan de link naar het bestand op de `main`-branch, zodat het opnieuw importeren blijft werken.
- Verhoog `homeassistant.min_version` als je een functie van een nieuwere Home Assistant gebruikt.
- Gebruik selectors met een `filter` (bijvoorbeeld `integration: marstek_modbus`), zodat gebruikers alleen passende entiteiten zien.
- Zet geen eigen entiteit-ID's vast in een blueprint; maak er een input van.
- Gebruik native Home Assistant-opties waar dat kan, en Jinja-templates alleen als het niet anders kan.
- Blueprints moeten blijven werken met één of meer batterijen.
- Denk aan veiligheid: een batterij mag nooit in geforceerd laden of ontladen blijven hangen. Controleer of [`Marstek_Veiligheid.yaml`](Marstek_Veiligheid.yaml) je wijziging nog afdekt.
- Schrijf namen en beschrijvingen in het Nederlands, net als de bestaande blueprints.

### Dashboard en helpers

- Noem in de README nieuwe HACS-kaarten of -integraties die je dashboard nodig heeft.
- Werk een nieuwe screenshot (`EnergyDashboard.png`) alleen bij als de indeling echt verandert.

## Gedragscode

Door bij te dragen ga je akkoord met de [gedragscode](CODE_OF_CONDUCT.md).

## Licentie

Bijdragen vallen onder de [MIT-licentie](LICENSE) van dit project.

---

# Contributing (English)

Thanks for helping out! Issues and pull requests in English are welcome.

- **Bugs:** check the [existing issues](../../issues), then open a [new one](../../issues/new/choose). Include the blueprint, your Home Assistant and integration versions, the number of batteries, and the automation trace. Remove personal data first.
- **Ideas:** open an issue describing the problem you want to solve.
- **Pull requests:** test the change in your own Home Assistant, update both `README.md` and `README.en.md`, keep `source_url` pointing at `main`, use filtered selectors instead of hard-coded entity IDs, and make sure a battery can never get stuck in forced charge or discharge.

By contributing you agree to the [Code of Conduct](CODE_OF_CONDUCT.md) and to license your work under the [MIT License](LICENSE).
