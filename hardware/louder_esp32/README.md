# Louder-ESP32 hardwaredossier

## Aanwezig exemplaar

| Eigenschap | Waarde | Herkomst |
| --- | --- | --- |
| Board | Louder-ESP32 met ESP32-WROVER | `[confirmed: gebruiker, 2026-11-15]` |
| Printrevisie | H9 (nieuw) | `[confirmed: gebruiker + Sonocotta PR #207, 2026-11-15]` |
| Voedingsmeting | VDD/10-deler op GPIO36 (ADC1_CH0), vanaf H9 | `[confirmed: Sonocotta PR #207]`, werking `[verify]` |
| Microfoonposities | twee, onbezet; nog geen modules gekozen | `[confirmed: gebruiker, 2026-11-15]` |
| Voeding ontwikkeling | alleen USB-C (editen/flashen) | `[confirmed: gebruiker, 2026-11-15]` |
| Voeding deployment | 12 V / 3 A-adapter; connector en polariteit open | `[confirmed: gebruiker, 2026-11-15]` |
| Luidsprekers | twee (stereo); impedantie en vermogen open | `[confirmed: gebruiker, 2026-11-15]` |
| Modulevariant | ESP32-WROVER-IE (8MB) volgens H6/H7-schema | `[verify]` (opschrift module) |
| Flash/PSRAM-verdeling | onbekend | `[verify]` |
| Pinout gelijk aan H7-schema | ja, volgens ESPHome-config op main (na H9) | `[confirmed: louder-esp32-idf.yaml @ 23fc578e]`; mic GPIO13 daar niet genoemd `[verify]` |

Er staan nog geen eigen foto's in deze map.

### Revisie H9: geen schema gepubliceerd

Sonocotta noemt H9 in [PR #207](https://github.com/sonocotta/esp32-audio-dock/pull/207)
(gemerged 2026-10-30), maar `hardware/3-louder-esp32/` bevat t/m `rev-h7`
(stand `23fc578e`). Het H7-schema is daarom de dichtstbijzijnde referentie.
Bekend verschil H9 ten opzichte van H7: een 10:1-deler van de voeding naar
GPIO36 (`SENSOR_VP`, in H7 onaangesloten). Andere verschillen zijn onbekend `[verify]`.

Volgens dezelfde PR: lees met 12 dB attenuatie; absolute nauwkeurigheid op H9
nog niet door Sonocotta gemeten. De ESPHome-config vraagt ESP32-chiprevisie
≥ 3.1 (`minimum_chip_revision`); of dit board dat heeft `[verify]` (bootlog).

### Voeding volgens H6/H7-schema (blad 2)

`[confirmed: H7-schema blad 2, 2026-11-15]` voor de schakeling; gedrag `[verify]`:

- USB-C `VBUS` → Schottky D4 (1N5819WS) → `VDD`.
- DC-jack DC2 / schroefklem U6 → diode D3 (M7) → `VDD`.
- `VDD` voedt PVDD van de TAS5805M en buck U3 (XL1509-3.3) → 3V3.

Gevolgen, nog te bevestigen:

- Op alleen USB is `VDD` ≈ 4,7 V: net boven TAS5805M-minimum 4,5 V en begrensd
  door USB-stroom en D4. Playback op laag volume waarschijnlijk mogelijk, niet
  representatief. `[assumption]`
- De twee bronnen vormen een diode-OR: USB-C tegelijk met 12 V lijkt veilig voor
  de pc. `[assumption]` — de waarschuwingstekst bij VIN op de achterkant lezen
  vóór gelijktijdig aansluiten.

### Overige aansluitingen op H6/H7 (niet in gebruik voor Jarvis)

USR-ES1/W5500-LAN (SPI 18/19/23, CS 5, INT 35, RST 14), IR-ontvanger (GPIO39),
OLED-FPC (SPI, CS1 15, DC 4, RES 32), RGB-LED-header (GPIO12, strapping-pin),
CH340C met auto-reset.

Open vragen voor deze configuratie:

- Revisieopschrift en WROVER-opschrift fotograferen. `[verify]`
- Waarschuwingstekst bij VIN lezen. `[verify]`
- Impedantie en vermogen van beide luidsprekers.

Vóór implementatie vastleggen:

- Scherpe foto's van voor- en achterkant, revisieopschrift en WROVER-module.
- Detailfoto's van de microfoonposities en connectoren.
- Gekozen microfoonmodule inclusief pinout, oriëntatie en L/R-selectie.
- Voedingsspanning, stroomcapaciteit, connector/polariteit en luidsprekerimpedantie.
- Exact toepasselijk schema en eventuele verschillen met de aanwezige print.

## Referentie-informatie: niet als montagebewijs gebruiken

Sonocotta documenteert microfoonheaders vanaf klassieke revisie H6. Schema's
noemen ICS-43434-modules; de README noemt INMP441 als getest. Behandel de posities
als aansluitingen voor modules, niet als bewezen footprints voor kale MEMS-chips.

Voor H6+; netlabels gecontroleerd in het H7-schema blad 1
`[confirmed: H7-schema, 2026-11-15]`, fysieke board `[verify]`:

| Signaal | Klassieke ESP32 GPIO |
| --- | --- |
| I²S BCLK, gedeeld | 26 |
| I²S WS, gedeeld | 25 |
| Microfoondata naar MCU | 13 |
| Audiodata naar TAS5805M | 22 |
| I²C SDA / SCL | 21 / 27 |
| TAS5805M PDN / FAULT | 33 / 34 |

Verifieer netten en fysieke padvolgorde tegen het daadwerkelijke board. Twee
microfoonposities bewijzen geen werkende beamforming of echo-onderdrukking.

De TAS5805M is geen microfoon-ADC. TI specificeert 2 × 23 W bij 8 Ω, 21 V en
1% THD+N; dit is geen gegarandeerd continu boardvermogen. Het aanbevolen
PVDD-bereik is 4,5–26,4 V. Een passende 12V/3A-voeding is een kandidaat voor
eerste laagvolumetests, na connector- en polariteitscontrole. BTL-luidsprekeruitgangen
zijn geen line-out: verbind geen luidsprekerklem met massa of een andere uitgang.

## Lokale bestanden

Geen H9-bestanden beschikbaar; zodra Sonocotta `rev-h9` publiceert, ophalen en
vergelijken met H7.

| Bestand | Bron | SHA-256 | Licentie | Status |
| --- | --- | --- | --- | --- |
| `sonocotta/2508-louder-esp32-h6-schematic.pdf` | sonocotta/esp32-audio-dock @ `23fc578e`, `hardware/3-louder-esp32/rev-h6/` | `ff6266e3…` | Apache-2.0 (repo) `[verify]` | downloaded 2026-11-15 |
| `sonocotta/2508-louder-esp32-h7-schematic.pdf` | idem, `rev-h7/` (titelblok zegt H6) | `4c2e9e8f…` | idem | downloaded 2026-11-15 |
| `sonocotta/2508-louder-esp32-h7-pcb.pdf` | idem, `rev-h7/` | `6d4a49c7…` | idem | downloaded 2026-11-15 |
| `seeed_louder_esp32_h2_schematic.pdf` | Seeed productpagina `[verify]` | `4cd5b070…` | onbekend | needs_check; revisie H2, niet dit board |
| `seeed_louder_esp32_users_manual_en.pdf` | idem | `fcbf84d4…` | onbekend | needs_check |
| `seeed_louder_esp32_testing_procedure.pdf` | idem | `9e057f91…` | onbekend | needs_check |
| `seeed_102991934_product.pdf`, `seeed_102991934_page-{4,5}.jpg` | idem | `a81c0cdb…`, `24002b4d…`, `ae7b9ead…` | onbekend | needs_check |

Volledige hashes: `sha256sum hardware/louder_esp32/{sonocotta/*,seeed_*}`.
PDF's lezen/renderen: `pixi run pdftoppm -r 150 -png <pdf> <prefix>`.

## Bronnen en bewaring

- [Exacte Seeed-productomschrijving](https://www.seeedstudio.com/Louder-ESP32-p-6401.html)
- [Sonocotta hardware en firmware](https://github.com/sonocotta/esp32-audio-dock)
- [Louder revisies](https://github.com/sonocotta/esp32-audio-dock/tree/main/hardware/3-louder-esp32)
- [TAS5805M gegevens en datasheet](https://www.ti.com/product/TAS5805M)
- [ESP-IDF: I²S op klassieke ESP32](https://docs.espressif.com/projects/esp-idf/en/v5.5.2/esp32/api-reference/peripherals/i2s.html)
- [Sonocotta TAS5805M-driver](https://github.com/sonocotta/esp32-tas5805m-dac)

Bij opname van foto's/schema's/datasheets: bewaar origineel naast eventuele
annotaties, met bron-URL, revisie/commit, verkrijgingsdatum, SHA-256, licentie en
status (`downloaded`, `verified` of `needs_check`). Download niet alle revisies als
vervanging voor identificatie. De H7-schemabron heeft volgens het vooronderzoek
nog een H6-titelblok; een mapnaam alleen bewijst de revisie niet.
