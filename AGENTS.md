# Jarvis projectafspraken

## Begin hier

- Lees eerst `docs/status.md` (waar we zijn) en `docs/decisions.md` (afgestemde
  besluiten). Werk `status.md` bij aan het eind van een sessie; leg elk afgestemd
  detail vast als genummerd besluit. Markeer feiten met `[confirmed: …]`,
  `[verify]` of `[assumption]` (B-007).
- Lees `README.md`, `docs/architecture.md` en
  `docs/contributing/coding-standards.md` vóór wijzigingen.
- Het aanwezige board is een Louder met klassieke ESP32-WROVER, niet een S3/P4.
- PlatformIO met ESP-IDF is de gekozen firmwareomgeving.
- Hardware-identificatie en ontwerp gaan vooraf aan audio-implementatie.
- Bewaar onbevestigde pinouts, modules en instellingen als expliciete open punten.

## Klein en expliciet

- Maak de kleinste correcte wijziging. Geen speculatieve lagen of herbruikbaarheid.
- Gebruik C, structs en functies; C++ alleen met concrete meerwaarde, geen exceptions.
- Publieke eigen C-symbolen: `jrv_<module>_<functie>`; file-local helpers en
  statische toestand: `s_`. Constanten en enumwaarden: hoofdletters.
- Gebruik `.clang-format`; vier spaties en Linux-functieaccolades.
- Leg ownership, buffergrenzen en fouttoestand vast waar het gedrag ontstaat.
- Hergebruik buffers. DMA-geheugen en PSRAM zijn niet uitwisselbaar.
- Eén eigenaar voor I²S, mute en versterkertoestand; geen extra taak zonder noodzaak.
- Gebruik `assert()` voor interne programmeerfouten. Handel runtimefouten ook
  zonder assertions af; geen rebootlus bij een ontbrekend apparaat of busfout.
- Modelleer niet-triviaal toestandsafhankelijk gedrag met `switch`, expliciete
  `current_state`/`next_state` en één overgangscommit per stap.
- Gebruik geen `ESP_ERROR_CHECK` voor verwachte hardware- of verbindingsfouten.

## Repository en verificatie

- Zelfstandige PlatformIO-projecten onder `apps/` en `tests/bringup/`.
- Voer `pio run` uit vanuit het betreffende project. Pin platform en dependencies.
- Neem P4-debugtools, USB-instellingen of flashgrootte niet over voor WROVER.
- Iedere hardwareproef krijgt een README met bedrading, exacte build/upload/monitor-
  commando's, verwachte resultaten en pass/fail-criteria.
- Hosttests alleen voor betekenisvolle pure logica; sanitizers waar beschikbaar.
- Vermeld wat werkelijk is gebouwd/getest en wat fysieke verificatie vereist.
- Hardwaremateriaal hoort in `hardware/`, met bron, revisie en verificatiestatus.
- Geen credentials of audio-opnamen in git. Documentatiewijzigingen mag de agent
  zelf committen; code alleen op expliciet verzoek (B-004).

De firmwaremappen en buildcommando's zijn momenteel nog gepland; claim geen build
of hardwarevalidatie voordat het betreffende project bestaat en is gecontroleerd.
