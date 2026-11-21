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

### Revisie H9: eigen schema en printtekening

Sonocotta noemt H9 in [PR #207](https://github.com/sonocotta/esp32-audio-dock/pull/207)
(gemerged 2026-10-30). Voor de bedrading is het
[H9-schema](sonocotta/2610-louder-esp32-h9-schematic.pdf) samen met de
[H9-printtekening](sonocotta/2610-louder-esp32-h9-pcb.pdf) nu de primaire referentie.
Onderstaande eerdere analyses op basis van H6/H7 moeten hiertegen worden gecontroleerd.
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

### Display en knoppen

Voor [B-013](../../docs/decisions.md#b-013-display-voor-discrete-informatie-keuze-en-bevestiging),
[B-015](../../docs/decisions.md#b-015-vier-knoppen-spreek-jakeuze-nee-en-escape) en
[B-016](../../docs/decisions.md#b-016-bedieningspaneel-via-i2c-voor-de-poc).
**PoC-opbouw, geen productontwerp.**

Display en knoppen hangen aan de I²C-bus van het board, direct afgetakt van
de WROVER-module `[confirmed: gebruiker, 2026-11-21]`:

| Apparaat | I²C-adres | Bron | Status |
| --- | --- | --- | --- |
| TAS5805M (versterker, op de print) | 0x2C–0x2F, afhankelijk van ADR | TI-datasheet | adres `[verify]` met I²C-scan |
| MCP23017 (knoppen) | 0x20–0x27 via A0–A2; sdc2026 gebruikt 0x27 | omnibot `314b650`, `config.h` | `[verify]` |
| SSD1362-OLED 3,12 inch | 0x3C in sdc2026 | omnibot `314b650`, `config.h` | `[verify]` |

| Signaal | GPIO | Bron |
| --- | --- | --- |
| I²C SDA | 21 | H6+-tabel hieronder, H9-schema |
| I²C SCL | 27 | idem |

**Knoppen op de MCP23017**, naar het patroon van sdc2026: ingangen op GPIOA met
interne pull-ups, actief laag, knop naar GND. Voorstel: A0 Spreek, A1 Ja/Keuze,
A2 Nee, A3 Escape `[assumption]`. De INT-uitgang wordt niet gebruikt; de ESP32
pollt elke 50 ms. GPIOB blijft vrij, bijvoorbeeld voor een LED die aangeeft dat
de microfoon open is.

**Display:** SSD1362, 3,12 inch, 256×64 pixels met 4-bit grijswaarden
`[confirmed: gebruiker, 2026-11-21]`; sdc2026 stuurt het paneel ook zo aan
(`app_display_ssd13xx.c`, omnibot `314b650`).

Te controleren vóór de eerste displayproef:

- Pull-ups op SDA/SCL: zitten die op de Louder-print, of op de OLED- of
  MCP-module? Te veel parallelle pull-ups maken de bus te zwaar `[verify]`.
- Bekabeling kort houden; 400 kHz zoals in sdc2026, met 100 kHz als terugvaloptie `[assumption]`.
- Stroomverbruik van de OLED-module uit 3V3 (XL1509-buck) `[verify]`.

**Niet gebruikt in de PoC:** de 30-pins OLED-FPC (SPI: CS 15, DC 4, RES 32,
CLK 18, MOSI 23) en de knoppen SW3 (reset) en SW4 (BOOT, GPIO0)
`[confirmed: H9-schema, 2026-11-21]`. Sonocotta gebruikt op die FPC een
SH1106 van 128×64.

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

De [Seeed-productfiche voor SKU 102991934](seeed_102991934_product.pdf)
([origineel bij Seeed](https://files.seeedstudio.com/Bazaar/product_pdf/102991934.pdf))
bevat de [fotogalerij op p. 4](seeed_102991934_page-4.jpg) en het
[geannoteerde hardwareoverzicht op p. 5](seeed_102991934_page-5.jpg).
Zoek op die foto's de WROVER, de TAS5805M en de speakerklemmen uit het
[systeemdiagram](../../docs/architecture.md#systeemdiagram). De foto's illustreren
het product; het schema waarnaar Seeed op p. 6 verwijst is revisie H2, niet H9.

Controle 2026-11-18: de vier bestaande Seeed-PDF's en drie Sonocotta-PDF's voor
H6/H7 waren byte-identiek aan de online bronnen. De twee H9-PDF's komen uit
[deze upstreamversie](https://github.com/sonocotta/esp32-audio-dock/tree/15f7d97e0e4f53390edc7aa212a8a5ac83b9e30f/hardware/3-louder-esp32/rev-h9).

| Bestand | Bron | SHA-256 | Licentie | Status |
| --- | --- | --- | --- | --- |
| `sonocotta/2610-louder-esp32-h9-schematic.pdf` | Sonocotta `15f7d97e`, `rev-h9` | `3d13abfc9745d68fbf0a3e913a1cc0b4a77eff05aafe2ff87ee3b0fbcda0f7ee` | repo-licentie `[verify]` | downloaded 2026-11-18 |
| `sonocotta/2610-louder-esp32-h9-pcb.pdf` | idem | `090486581fd3e46a891600a4868a005df939677121a24ad98cbf54dc9b2d1e84` | idem | downloaded 2026-11-18 |
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
