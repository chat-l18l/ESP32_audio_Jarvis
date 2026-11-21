# Plan: de assistent als harness (inspiratie: Harness²)

**Backlog: inspiratie en onderzoeksvragen, 2026-11-21.** Dit betreft de assistent
op Linux, niet de firmware. Er is nog niets gekozen; zie
[B-014](decisions.md#b-014-assistent-als-expliciete-harness).

## Bron

[google-research/harness2](https://github.com/google-research/harness2)
(Apache-2.0, bekeken op 2026-11-21, laatste push 2026-11-02). Bijbehorend paper:
[arXiv 2610.14224](https://arxiv.org/abs/2610.14224). Alleen de README en de
bestandsindeling zijn gelezen `[confirmed: README, 2026-11-21]`. Het paper en de
code zijn nog niet bestudeerd `[verify]`.

## Wat Harness² doet

- Een **harness** is alles rond een taalmodel waarmee het als agent kan werken:
  prompts, tools, geheugen, skills en orkestratie. De modelgewichten blijven gelijk.
- Er zijn acht bewerkbare componenten: *system prompt, guardrails, memory, tool
  guidance, subagents, skills, scripts* en *plugins*.
- **Op taakniveau** stelt het systeem wijzigingen aan de harness voor (*propose*),
  probeert die op dezelfde taak uit (*probe*) en combineert de varianten die
  beter werken (*compose*). Daarvoor zijn geen juiste antwoorden vooraf nodig.
- **Op harnessingniveau** bewaart het systeem de gekozen harness en de lessen
  waarvoor bewijs is. Die sturen de verbetering bij de volgende taak.
- De executors draaien met onbeperkte netwerktoegang, en Codex zonder sandbox.
  Dat is geschikt voor benchmarks, niet voor een labsysteem met echte gegevens.

## Vertaling naar Jarvis

Het bruikbare idee: beschrijf de assistent als een set losse componenten met elk
een eigenaar. Een studentgroep of modelwissel ([B-010](decisions.md#b-010-lokale-dienstkeuze-en-push-to-talk-in-het-lab))
levert dan een andere harness rond dezelfde diensten, geen ander systeem.

| Harness²-component | Jarvis-voorbeeld | Wie mag wijzigen? |
| --- | --- | --- |
| System prompt | Rol, taal, toon en beknoptheid van gesproken antwoorden | Ontwikkelaar per assistentinstantie |
| Guardrails | Acties die bevestiging op het display vereisen ([B-013](decisions.md#b-013-display-voor-discrete-informatie-keuze-en-bevestiging)), de vaste selectiegrens ([B-012](decisions.md#b-012-alle-berichten-verwijst-naar-een-vaste-selectie)), geen geheimen via TTS | Alleen ontwikkelaar via review; **nooit** een automatische verbeterlus |
| Memory | Projectfeiten, voorkeuren, berichtselecties; zie het [contextplan](plan-context-en-initiatief.md) | Backenddiensten; de gebruiker kan corrigeren |
| Tool guidance | Beschrijving van inventaris-, berichten- en displaytools | Ontwikkelaar |
| Subagents | Bijvoorbeeld een zoekagent voor het projectarchief | Ontwikkelaar |
| Skills | Vaste werkwijzen, bijvoorbeeld "berichten per project samenvatten" | Ontwikkelaar; later mogelijk voorgesteld door een verbeterlus |
| Scripts | Deterministische hulpfuncties: selectie vastleggen, aantallen tellen | Ontwikkelaar |
| Plugins/middleware | Toestemmingscontrole vóór een toolaanroep, logging | Ontwikkelaar via review |

Een harde grens: **wat een gebruiker op het display bevestigt, en welke acties
een bevestiging nodig hebben, ligt in code buiten het taalmodel.** Een
verbeterlus mag prompts en skills voorstellen, maar geen guardrail verzwakken.

## Onderzoeksvragen

1. Kunnen de [labscenario's](architecture.md#labscenario) en de proef in het
   [contextplan](plan-context-en-initiatief.md#aanpak-en-beoogde-opbrengst) dienen
   als kleine benchmark: een taak, criteria en een beoordeling? Studentgroepen
   kunnen dan hun harnessvarianten op dezelfde taken vergelijken.
2. Werkt *propose, probe, compose* **offline** op opgenomen of fictieve scenario's?
   Een probe mag nooit een echte destructieve actie uitvoeren; tools zijn dan
   nagebootst.
3. Hoe leggen we "lessen" vast als leesbare, reviewbare tekst met bewijs, in
   plaats van stille promptwijzigingen?
4. Welke componenten moeten behouden blijven bij een modelwissel, en welke zijn
   modelspecifiek?

**Klaar voor bespreking** zodra het paper is gelezen en vraag 1 met één scenario
is uitgewerkt. Een architectuurkeuze volgt pas daarna.
