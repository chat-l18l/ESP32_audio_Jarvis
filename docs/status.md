# Status: waar we zijn

Lees dit eerst aan het begin van een sessie; werk het bij aan het eind.

Laatst bijgewerkt: 2026-11-21

## Fase

Afstemmen van werkwijze en spec. Er wordt nog niet gebouwd.

## Laatste sessie

- [B-013](decisions.md#b-013-display-voor-discrete-informatie-keuze-en-bevestiging): klein discreet display voor geheimen, keuze uit enkele opties en Ja/Nee/Escape-bevestiging van belangrijke acties. De eis staat vast; voorgestelde regels staan in [Display en bevestiging](architecture.md#display-en-bevestiging). H9 heeft een 30-pins OLED-FPC op SPI ([hardwaredossier](../hardware/louder_esp32/README.md#display-en-knoppen)).
- [B-014](decisions.md#b-014-assistent-als-expliciete-harness) (voorstel): assistent opgebouwd als expliciete harness naar Harness²; zie [plan-agent-harness.md](plan-agent-harness.md).

Eerder (2026-11-18):

- [B-011](decisions.md#b-011-drie-knoppen-voor-bediening-zonder-scherm): drie knoppen. Lokaal terugkeren naar een hoorbaar hoofdniveau staat vast; het keuzegebaar is nog een voorstel.

- Voorlopig [B-010](decisions.md#b-010-lokale-dienstkeuze-en-push-to-talk-in-het-lab): lokaal diensten ontdekken en kiezen, menuspraak op de ESP32 en PTT in v1. Het [labscenario](architecture.md#labscenario) beschrijft voorraadcorrectie en uitval.

- [Documentatiewegwijzer](README.md) en [protocolafweging met proefopzet](plan-protocolkeuze.md) toegevoegd. De transportkeuze staat open.

Eerder:

- H9 online bevestigd (Sonocotta PR #207); H6/H7-schema's opgehaald; pixi-omgeving opgezet.
- Hardware: revisie H9, voeding, luidsprekers vastgelegd in het dossier.
- omnibot/sdc2026-docs bekeken als inspiratie (B-007).
- Repository verkend; werkwijze B-001 t/m B-009 vastgelegd in
  [decisions.md](decisions.md).

## Open vragen

### Onderzoeksbacklog

- [ ] [B-012](decisions.md#b-012-alle-berichten-verwijst-naar-een-vaste-selectie) toetsen: vaste berichtselectie bij vertraagde spraak, dubbele opdrachten en gedeeltelijke uitval. Voor een losse opdracht zonder eerder aangeboden lijst de gebruikersinteractie afstemmen.

- [ ] [Context en initiatief in het lab](plan-context-en-initiatief.md): literatuur verzamelen en met twee labscenario's toetsen welke kennis en acties buiten de LLM-gesprekscontext moeten blijven bestaan.

### Display en bevestiging (B-013)

- [ ] Knopindeling: hoe passen Ja, Nee en Escape naast Spreek en Keuze (B-011)? Vier knoppen of Keuze met focus en een lange druk?
- [ ] Kiezen uit vier opties: één knop per optie of bladeren en bevestigen?
- [ ] Displaytype: de SH1106 (128×64) van Sonocotta of iets anders; wat betekent "discreet" (kijkhoek, helderheid, automatisch uit)?
- [ ] Welke acties vereisen een displaybevestiging? Een vaste lijst in de backend (voorstel).
- [ ] Mag het scherm passief status tonen, of alleen op verzoek?
- [ ] Welke GPIO's zijn fysiek bereikbaar voor extra knoppen (5/14/35 via de LAN-header?).

### Assistent-harness (B-014)

- [ ] Harness²-paper lezen (arXiv 2610.14224); één labscenario uitwerken als kleine benchmark.

### Protocolkeuze

- [x] Voorlopig: discovery en gebruikerskeuze op de ESP32 (B-010).
- [ ] Welk keuzegebaar werkt zonder scherm het beste: kort voor status en lang voor de voorgelezen optie, of een optie kiezen met meerdere korte drukken? Zie de [gebruikersproef](architecture.md#gesproken-bediening).
- [ ] Discovery via directe aankondigingen of een catalogus op Linux? Hoe onderscheiden we beschikbare diensten van verdwenen of nog ladende instanties?
- [ ] Vóór de proef: welke wachtrijduur, tijd tot stilte en minimale vrije interne RAM accepteren we? Wat doen we met te late audio?
- [ ] Na de proef: welke topologie en welk transport kiezen we, op grond van welke metingen? Zie de [afweging en proefopzet](plan-protocolkeuze.md).

### Overige open punten

Werkwijze:

- [x] Contractvormen (B-005) en eerste commit (B-006) afgestemd.
- [ ] Taal van `plan-*.md` en bringup-READMEs (B-008).
- [ ] Code-commentaar in het Engels aangenomen in B-008; bevestigen.

Tooling:

- [x] pixi-omgeving met poppler (B-009).
- [ ] PlatformIO via pixi; `PLATFORMIO_CORE_DIR` (B-009).

Hardware (zie [hardwaredossier](../hardware/louder_esp32/README.md)):

- [x] Revisie H9 bevestigd (gebruiker + Sonocotta PR #207). H9-schema en printtekening op 2026-11-18 opgenomen in het hardwaredossier.
- [ ] Eerdere pinout- en voedingsanalyse op basis van H7 vergelijken met H9 en de fysieke print.
- [ ] ESP32-chiprevisie (ESPHome-config vraagt ≥ 3.1); uit bootlog halen.
- [x] Schema's H6 en H7 opgehaald en vastgelegd met bron en SHA-256; pinout
      in het H7-schema komt overeen met de H6+-tabel.
- [ ] Waarschuwingstekst bij VIN lezen; diode-OR suggereert dat USB-C + 12 V
      samen veilig is `[assumption]`.
- [ ] Licentie Seeed-bestanden en hardwaremap Sonocotta.
- [ ] Luidsprekers: impedantie en vermogen (twee stuks, stereo).
- [ ] Microfoonmodule kiezen (nog geen; blokkeert eerste mijlpaal niet).
- [ ] Foto's van beide boardzijden in `hardware/louder_esp32/`.

## Volgende stap

Knopindeling voor display en bevestiging afstemmen (B-011/B-013). Daarna de
acceptatiechecklist voor de eerste mijlpaal: alleen afspelen.
