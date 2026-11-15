# Louder-ESP32 hardwaredossier

## Aanwezig exemplaar

Door de gebruiker bevestigd: Louder-board met ESP32-WROVER en twee onbezette
microfoonposities. Exacte printrevisie, modulevariant, flash/PSRAM en fysieke
padvolgorde zijn nog niet vastgesteld. Er staan nog geen eigen foto's of lokale
kopieën van schema's in deze map.

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

Voor de onderzochte H6+-documentatie:

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
