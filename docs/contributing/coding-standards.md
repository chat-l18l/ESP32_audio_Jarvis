# Coding standards

## Referentie en toepassing

Gebaseerd op de op 2026-11-15 bekeken werkboom van
`/home/oetelaar/tmp_omnibot_ng/omnibot` (HEAD
`d3a9e5fad74747a335f062782326bc229ca61a26`), in het bijzonder:

- `AGENTS.md` en `docs/contributing/repository-conventions.md`;
- `docs/contributing/firmware-patterns.md`;
- `apps/sdc2026/esp_sdc2026/platformio.ini`;
- `src/app_boot.c`, `src/app_fsm.c` en `test/native/CMakeLists.txt`
  binnen dat PlatformIO-project.

We nemen organisatie en principes over. Robot-, P4- en Zenoh-specifieke code en
buildinstellingen zijn geen Jarvis-afhankelijkheden. De concrete Ponytail-bron is
nog niet geïdentificeerd; het afgesproken principe is: eenvoudiger en minder code
waar dat het contract behoudt.

## Code en opmaak

- GNU C, C23 waar de gekozen toolchain dat ondersteunt; forceer geen nieuwe
  toolchain voor alleen de taalversie. C++ alleen met aantoonbare meerwaarde.
- Geen exceptions, diepe overerving, generieke frameworks of doorgeefwrappers.
- Vier spaties, geen tabs, `snake_case`, `.clang-format` als opmaakbron.
- Publiek: `jrv_<module>_<functie>` en bijbehorende types; file-local helpers en
  statische toestand: `s_`; constanten/enumwaarden: hoofdletters met moduleprefix.
- Benoem eenheden in velden en constanten, bijvoorbeeld `_ms`, `_hz`, `_bytes`.
- Eén bestand of module per concrete verantwoordelijkheid, niet per paar regels.
- Korte comments voor reden, contract en niet-triviaal gedrag. Gebruik
  `Why:`, `Pre:` en `Post:` waar die informatie toevoegen, niet als verplicht ritueel.

## Data, resources en fouten

- Initialiseer context expliciet en geef die per pointer door waar nodig.
- Benoem eigenaar en levensduur van handles en buffers; geef lengte/capaciteit mee.
- Reserveer en hergebruik buffers; houd stack en intern DMA-geheugen begrensd.
- Maak initialisatie, cleanup en geldig gedrag na gedeeltelijk falen expliciet.
- Behoud I²C-devicehandles van init tot deinit; voeg ze niet per transactie toe.
- Statusretouren voor operationele fouten; gebruik platformfouttypen direct waar passend.
- `assert()` controleert interne programmeerfouten, niet externe invoer, ontbrekende
  hardware, geheugentekort of netwerkuitval. Die blijven ook in release afgehandeld.
- Eén eigenaar is eenvoudiger dan meerdere locks. Bij gedeelde toegang: één
  vastgelegd synchronisatieprotocol voor samenhangende toestand en levensduur.
- Houd ISR-werk klein; geen netwerk-I/O of zware verwerking in callbacks.

## Toestand en asynchroon werk

Gebruik voor niet-triviale toestandsafhankelijkheid een expliciete FSM met volledige
`switch`-dekking, `current_state`, `next_state` en één commitpunt per stap.
Voeg pas een event-enum of overgangstabel toe als die het gedrag verduidelijkt.
Een ongeldige toestand resulteert in een gelogde fout en een gedefinieerde stille
fouttoestand; het systeem blijft niet ongecontroleerd afspelen.

Begrens queues, opnameduur, wachttijden en retries. Accepteer een asynchroon resultaat
alleen voor de actuele sessie/beurt. Annuleren stopt lokale playback en maakt oude
audio ongeldig; het bewijst niet dat een remote verzoek geen effect heeft gehad.

## Build en verificatie

- Zelfstandig PlatformIO-project met `framework = espidf`; pin platform en drivers.
- Leg de opgeloste ESP-IDF/toolchainversies vast bij de eerste werkende build.
- Geen stille package-updates of waarschuwingsonderdrukking voor eigen code.
- Formatteer alleen gewijzigde eigen code; herschrijf geen externe code voor stijl.
- Elke codewijziging heeft exacte verificatiecommando's en relevante foutscenario's.
- Hardwareproeven beschrijven bedrading, startcondities, verwacht gedrag en herstel.
- Test pure logica op de host wanneer dat concrete grensgevallen of toestandsovergangen
  verifieert; gebruik dan waar mogelijk AddressSanitizer en UndefinedBehaviorSanitizer.
- Een geslaagde build is geen geslaagde hardwaretest. Rapporteer beide apart.
