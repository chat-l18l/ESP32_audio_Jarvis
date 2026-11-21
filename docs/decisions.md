# Besluitenlog

Genummerde, afgestemde besluiten. Een besluit wordt niet herschreven: vervang het
met een nieuw nummer en zet bij het oude `Vervangen door B-xxx`.

Status: `vastgesteld` (afgestemd met gebruiker) of `voorstel` (nog niet bevestigd).

## B-001 Projectgeheugen staat in de repo

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: voortgang staat in [status.md](status.md), afgestemde details in dit log.
  Agent-privégeheugen bevat hooguit werkvoorkeuren, geen projectinhoud.
- Reden: zichtbaar voor gebruiker en iedere agent, versiebeheerd.

## B-002 Drie soorten contracten

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: we leggen contracten vast op drie niveaus:
  1. **Module-interface**: eigenaar, pre/post-condities, foutgedrag, buffergrenzen
     en threading per C-module.
  2. **ESP32↔Linux-protocol**: framing, audioformaat, sessie/beurt-ID's,
     deadlines en annulering.
  3. **Acceptatie per stap**: pass/fail-criteria per mijlpaal, vastgelegd vóór code.
- Open: vorm en plaats per soort (zie [status.md](status.md)).

## B-003 Spec per onderwerp plus besluitenlog

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: de spec groeit als losse documenten per onderwerp (hardware, audio,
  protocol, Linux, …). Elk afgestemd detail krijgt een nummer in dit log.
- Reden: kleine stappen blijven traceerbaar zonder zware ADR-bestanden.

## B-004 Committen

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: wijzigingen aan documentatie mag de agent zelf committen.
  Code alleen op expliciet verzoek.

## B-005 Vorm van de contracten

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit:
  1. **Module-interface**: eerst afgesproken in het spec-doc van het onderwerp;
     bij implementatie als korte `Pre:`/`Post:`-comment in de `.h`. Vanaf dan is
     de header de bron en verwijst het doc ernaar.
  2. **Protocol**: `docs/protocol.md` met berichttypes, byte-layout, grenzen,
     deadlines en een protocolversienummer. Geen schema-taal.
  3. **Acceptatie**: per mijlpaal een checklist met genummerde criteria (A1, A2, …)
     en pass/fail. Na een proef vullen we de uitkomst, datum en het bewijs in.
- Reden: eerst afspreken, dan bouwen; één bron per contract zodra er code is.

## B-006 Eerste commit bevat alle bestaande bestanden

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: de eerste commit bevat alle aanwezige bestanden, inclusief de
  Seeed-pdf's en -afbeeldingen in `hardware/louder_esp32/`. Bron, licentie en
  revisiestatus daarvan zijn nog een open punt.

## B-007 Doc-soorten en herkomstmarkering (naar omnibot/sdc2026)

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit:
  - Doc-soorten herkenbaar aan de naam: `docs/<onderwerp>-spec.md` voor
    contracten (met versie, bv. "v1"), `docs/plan-<onderwerp>.md` voor onderzoek
    en plan. Bringup-checklists staan in de README van het proefproject.
  - Feiten krijgen een herkomstlabel: `[confirmed: <bron>, <datum>]`,
    `[verify]` of `[assumption]`. Bron kan zijn: datasheet §, meting, gebruiker.
- Niet overgenomen: verplicht statusblok bovenaan, open vragen gegroepeerd op blokkade.
- Bron: `/home/oetelaar/tmp_omnibot_ng/omnibot/apps/sdc2026/docs`, bekeken 2026-11-15.

## B-008 Taal van documentatie

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: werkdocs (status, besluiten, AGENTS, README, architectuur, coding
  standards) in het Nederlands; `*-spec.md` en het protocol in het Engels.
  Code, identifiers en commentaar in de code in het Engels.
- Open: taal van `plan-*.md` en bringup-READMEs.

## B-009 Hulpmiddelen via pixi

- Datum: 2026-11-15 — Status: vastgesteld
- Besluit: alle ontwikkelhulpmiddelen komen via een pixi-omgeving in de repo-root
  (`pixi.toml`, `pixi.lock` in git, `.pixi/` niet). Eerste inhoud: `poppler`,
  `poppler-data` (pdf's lezen/renderen). Commando's: `pixi run <tool>`.
- Open: PlatformIO via pixi, en waar PlatformIO zijn toolchains bewaart
   (`PLATFORMIO_CORE_DIR`), beslissen bij de eerste firmwarestap.

## B-010 Lokale dienstkeuze en push-to-talk in het lab

- Datum: 2026-11-18 — Status: voorstel (voorlopig besluit van de gebruiker).
- De ESP32 ontdekt beschikbare diensten en laat de gebruiker via lokale TTS kiezen. Onderscheid **functie** (labassistent, intercom, muziek) van **instantie** (bijvoorbeeld de standaardassistent, `test1` van groep 1 of de versie van groep 2). Bij uitval biedt het board de nog beschikbare opties aan; de gebruiker bepaalt de bestemming van zijn spraak.
- In v1 opent de gebruiker met de draagbare microfoon iedere spreekbeurt via push-to-talk. Geen wake word of achtergrondopname: ook een vervolgvraag bij de kast begint met de knop.
- De geselecteerde backend verwerkt de vraag en eventuele voorraadcorrectie. Lokale TTS bedient het keuzemenu ook als de primaire assistent wegvalt. De haalbaarheid van verstaanbare Nederlandse menuspeech op WROVER is te testen.
- Open: lokale bediening van het menu, manier van discovery en taakverdeling met Linux. Zie [labscenario en voorgestelde proeven](architecture.md#labscenario).

## B-011 Drie knoppen voor bediening zonder scherm

- Datum: 2026-11-18 — Status: vastgesteld `[confirmed: gebruiker, 2026-11-18]`.
- Aangevuld door [B-013](#b-013-display-voor-discrete-informatie-keuze-en-bevestiging): er komt een klein display. De knoprollen blijven gelden; de spraakbediening blijft werken zonder naar het scherm te kijken.
- **Spreek (PTT)** opent de microfoon voor de spreekbeurt. **Keuze** dient voor het kiezen en bevestigen van een lokaal voorgelezen optie. **Escape** gaat één menuniveau omhoog. Op het hoofdniveau blijft Escape daar en meldt de ESP32 dat hoorbaar. Zo komt de gebruiker zonder tellen altijd terug op een herkenbaar punt, ook zonder backend.
- Een nog niet bevestigde keuze vervalt bij Escape. Keuze en Escape openen de microfoon niet. De lokale interface houdt de actieve modus en de positie in het menu apart bij: een voorgelezen optie is nog niet de actieve dienst.
- Nog te kiezen: het gebaar voor status, bladeren en bevestigen. Eén korte druk voor status en een reeks korte drukken voor een optienummer zijn besproken mogelijkheden, geen vastgestelde gebaren. Zie [bedieningsvoorstel](architecture.md#gesproken-bediening).

## B-012 ‘Alle berichten’ verwijst naar een vaste selectie

- Datum: 2026-11-18 — Status: vastgesteld `[confirmed: gebruiker, 2026-11-18]`.
- Leg de relatie tussen spreekbeurt, zichtbare berichten en opdracht vast. Alleen de toen bedoelde bericht-ID's mogen worden gewijzigd, ook bij vertraging of herhaling. Een later ontvangen bericht vraagt een nieuwe keuze. De berichtendienst bewaakt deze grens, niet het taalmodel.
- Tijdregistratie, voorbeeld en te toetsen uitvoeringsvoorstel: [onderzoeksplan](plan-context-en-initiatief.md#waar-ligt-de-tijdgrens-van-alle).

## B-013 Display voor discrete informatie, keuze en bevestiging

- Datum: 2026-11-21 — Status: vastgesteld als eis `[confirmed: gebruiker, 2026-11-21]`; uitwerking open.
- De terminal krijgt naast audio een klein, discreet display met drie functies:
  1. **Discreet tonen:** informatie die niet hardop mag, zoals een wachtwoord.
  2. **Kiezen:** uit enkele opties (bijvoorbeeld vier) met de knoppen van het device.
  3. **Bevestigen:** voor belangrijke acties is spraak te onbetrouwbaar. Een vraag als "Alle voicemails verwijderen?" wordt op het display bevestigd met Ja, Nee of Escape.
- Reden: een verkeerd verstane spraakopdracht mag geen onomkeerbare actie uitvoeren, en geheimen horen niet uit een luidspreker.
- Open: displaytype, knopindeling, welke acties een bevestiging vereisen en het berichtcontract. Zie [Display en bevestiging](architecture.md#display-en-bevestiging).

## B-014 Assistent als expliciete harness

- Datum: 2026-11-21 — Status: voorstel.
- Beschrijf de assistent op Linux als losse componenten met elk een eigenaar: prompt, guardrails, geheugen, tools, skills, scripts en middleware, naar [google-research/harness2](https://github.com/google-research/harness2).
- Guardrails, zoals de bevestigingsplicht uit B-013 en de selectiegrens uit B-012, liggen in code en zijn niet aanpasbaar door het taalmodel of een automatische verbeterlus.
- Afweging en onderzoeksvragen: [plan-agent-harness.md](plan-agent-harness.md).

## B-015 Vier knoppen: Spreek, Ja/Keuze, Nee en Escape

- Datum: 2026-11-21 — Status: vastgesteld `[confirmed: gebruiker, 2026-11-21]`.
- Breidt [B-011](#b-011-drie-knoppen-voor-bediening-zonder-scherm) uit met een aparte **Nee**-knop. Elke knop heeft één vaste betekenis, ook bij bevestigen op het display ([B-013](#b-013-display-voor-discrete-informatie-keuze-en-bevestiging)):
  - **Spreek (PTT):** opent de microfoon; nooit een antwoord op een bevestigvraag.
  - **Ja/Keuze:** kiest of bevestigt. Bij een bevestigvraag het enige pad naar uitvoering.
  - **Nee:** wijst de getoonde vraag af. De dienst hoort expliciet "nee".
  - **Escape:** verlaat de vraag of het menuniveau, zoals in B-011. De dienst hoort "geannuleerd".
- Nee en Escape voeren allebei niets uit. Het verschil is wat de dienst terughoort: een antwoord, of het afbreken van de interactie.
- Open: wat Nee betekent in het gesproken menu zonder openstaande vraag, en hoe een keuze uit vier opties met deze knoppen werkt.

## B-016 Bedieningspaneel via I²C voor de PoC: SSD1362-OLED en MCP23017

- Datum: 2026-11-21 — Status: vastgesteld `[confirmed: gebruiker, 2026-11-21]`.
- Dit is een **proof of concept**, geen product. Na de PoC volgt een hardware-redesign.
- Display en knoppen hangen aan de bestaande I²C-bus van het board (GPIO21 SDA, GPIO27 SCL), direct afgetakt van de WROVER-module. Op die bus zit ook de TAS5805M.
  - **Display:** SSD1362-OLED van 3,12 inch, hetzelfde type als in omnibot/sdc2026. Resolutie: de gebruiker noemt 128×64, maar sdc2026 stuurt het paneel aan als 256×64 met 4-bit grijswaarden `[verify]`.
  - **Knoppen:** de vier knoppen van [B-015](#b-015-vier-knoppen-spreek-jakeuze-nee-en-escape) op een MCP23017. De ESP32 pollt ze; 20 keer per seconde (elke 50 ms) is genoeg.
- Gevolg: geen extra ESP32-GPIO's nodig voor knoppen; de 30-pins OLED-FPC op SPI wordt in de PoC niet gebruikt.
- Reden: eenvoudig aan te sluiten, bekend uit sdc2026 en geen ingreep in de print.
- Open: de busverdeling tussen versterker, knoppen en display; zie [Display en bevestiging](architecture.md#display-en-bevestiging).
