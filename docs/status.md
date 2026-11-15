# Status: waar we zijn

Lees dit eerst aan het begin van een sessie; werk het bij aan het eind.

Laatst bijgewerkt: 2026-11-15

## Fase

Afstemmen van werkwijze en spec. Er wordt nog niet gebouwd.

## Laatste sessie

- H6/H7-schema's van Sonocotta opgehaald; pixi-omgeving opgezet.
- Hardware: revisie H9, voeding, luidsprekers vastgelegd in het dossier.
- omnibot/sdc2026-docs bekeken als inspiratie (B-007).
- Repository verkend; werkwijze B-001 t/m B-009 vastgelegd in
  [decisions.md](decisions.md).

## Open vragen

Werkwijze:

- [x] Contractvormen (B-005) en eerste commit (B-006) afgestemd.
- [ ] Taal van `plan-*.md` en bringup-READMEs (B-008).
- [ ] Code-commentaar in het Engels aangenomen in B-008; bevestigen.

Tooling:

- [x] pixi-omgeving met poppler (B-009).
- [ ] PlatformIO via pixi; `PLATFORMIO_CORE_DIR` (B-009).

Hardware (zie [hardwaredossier](../hardware/louder_esp32/README.md)):

- [ ] Revisie: gebruiker leest H9, Sonocotta kent voor klassieke ESP32 alleen
      t/m H7. Foto van opschrift nodig (6 vs 9?).
- [x] Schema's H6 en H7 opgehaald en vastgelegd met bron en SHA-256; pinout
      in het H7-schema komt overeen met de H6+-tabel.
- [ ] Waarschuwingstekst bij VIN lezen; diode-OR suggereert dat USB-C + 12 V
      samen veilig is `[assumption]`.
- [ ] Licentie Seeed-bestanden en hardwaremap Sonocotta.
- [ ] Luidsprekers: impedantie en vermogen (twee stuks, stereo).
- [ ] Microfoonmodule kiezen (nog geen; blokkeert eerste mijlpaal niet).
- [ ] Foto's van beide boardzijden in `hardware/louder_esp32/`.

## Volgende stap

Onderwerp voor onderwerp de spec, te beginnen met de hardware-identificatie.
