# Drie meldingen: afval, waterlek en lege batterijen

📖 **Uitleg:** [Drie meldingen die je echt helpen: afval, waterlek en lege batterijen](https://fonferek.nl/blog/home-assistant-meldingen-afval-waterlek-batterij/)

| Bestand | Wat het doet |
|---|---|
| [`afval-morgen.yaml`](afval-morgen.yaml) | De avond voor de ophaaldag om 18:00 een melding welke container aan de straat moet (agenda van [Twente Milieu](https://www.home-assistant.io/integrations/twentemilieu/) of een andere afvalagenda) |
| [`waterlek-wasmachine.yaml`](waterlek-wasmachine.yaml) | Direct een melding als de watersensor bij de wasmachine nat wordt |
| [`batterij-bijna-leeg.yaml`](batterij-bijna-leeg.yaml) | Een melding als een sensor onder 15% zit, met het type batterij (`battery_size`) erbij |

Alle drie sturen naar de meldingsgroep `notify.ik`. Maak die eerst aan: **Instellingen → Apparaten en diensten → Helpers → Helper aanmaken → Groep → Meldingsgroep**. Krijg je een nieuwe telefoon, dan pas je alleen de groep aan.

Plakken: **Instellingen → Automatiseringen en scènes → Automatisering aanmaken → ⋮ → Bewerken in YAML**. Vervang daarna de entiteiten door die van jezelf.
