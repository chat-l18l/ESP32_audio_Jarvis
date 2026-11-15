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
