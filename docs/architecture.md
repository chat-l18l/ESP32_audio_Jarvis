# Architectuur en eerste besluiten

## Vastgelegd

| Besluit | Reden |
| --- | --- |
| Bestaande Louder-ESP32-WROVER behouden | Dit is de aanwezige hardware. |
| PlatformIO met ESP-IDF | Sluit aan bij de projecten en voorkeur van de gebruiker. |
| Eigen firmware bij voorkeur in C | Kleine expliciete modules en zichtbare kosten. |
| Zelfstandige Linux-coördinator | Geen Home Assistant als fundament. |
| STT, LLM en TTS buiten de ESP32 | Zware verwerking en modelkeuze blijven verwisselbaar. |
| GPU-API aanvankelijk via SSH-tunnel | De GPU-pc is momenteel via SSH bereikbaar. |
| Ontwerp eerst, kleine hardwareproeven daarna | Voorkomt grote implementaties op onbewezen aannamen. |

## Verantwoordelijkheden

```text
Louder ESP32 -- audio + besturing --> lokale Linux-coördinator
                                      |-- Whisper / lokale TTS
                                      |-- model-API via SSH-tunnel --> GPU-pc
                                      `-- officiële API's --> cloudproviders
```

De ESP32 beheert capture, playback, mute en lokale bediening. Eén audio-eigenaar
beheert de samenhang tussen I²S en versterkertoestand. Pas taken toevoegen wanneer
blokkeren of timing dat nodig maakt. Linux beheert sessies, resampling, modellen,
credentials en annulering. SSH is beheer/tunnel, niet het inferenceprotocol.

Provider- en model-ID's zijn Linux-configuratie. STT/LLM/TTS hebben verschillende
rollen en capabilities; ontwerp hun concrete interfaces zodra de eerste werkende
spraakpipeline die nodig heeft. Geen generiek pluginframework vooraf.

## Audiohypothese: eerst bewijzen

Voor de onderzochte H6+-uitvoering delen microfoon en versterker BCLK/WS. De
voorgestelde basis is 48 kHz, Philips-I²S, twee 32-bit slots en één geselecteerde
microfoon. Een gemeenschappelijk TX/RX-klokdomein voorkomt omschakelen tussen
opname en weergave. Controleer dit tegen de werkelijke revisie en microfoonmodule.

Later kan PCM16 mono op 48 kHz naar Linux: 96.000 bytes/s zonder transportoverhead.
Linux filtert/resamplet naar 16 kHz voor Whisper en levert playback op 48 kHz.
Het netwerkformaat is niet hetzelfde als de DMA-slotindeling.

De eerste interactie is push-to-talk en functioneel halfduplex. Wake word, VAD,
AEC, twee-microfoonverwerking en vrije barge-in zijn vervolgstappen met eigen
resource- en herkenningstests. Jarvis-wake-wordondersteuning op dit WROVER-board
en een fysieke TAS5805M-SDOUT-referentieroute naar de MCU zijn niet bevestigd.

## Eerste firmwaremijlpaal

Eerst de open punten in het [hardwaredossier](../hardware/louder_esp32/README.md)
afhandelen. Daarna één project: `tests/bringup/audio/esp_audio/`.

1. Kies en pin een PlatformIO-platform voor klassieke ESP32 en een onderbouwde
   boardconfiguratie. Leg flashgrootte, PSRAM, console en drivercompatibiliteit vast.
2. Bouw een minimale `app_main` en controleer bootdiagnostiek via de console.
3. Initialiseer I²S en versterker in de vereiste volgorde, met mute als starttoestand.
4. Speel één kort bekend signaal op begrensd volume en keer terug naar stilte.
5. Controleer fouten en herstart voordat microfooncapture wordt toegevoegd.

Deze mijlpaal heeft geen netwerk, modelkoppeling of assistant-FSM nodig.

| Contract | Bescherming / verificatie |
| --- | --- |
| Geen ongewenste playback bij init of fout | Mute vóór activering; geen unmute na mislukte init; koude start/reset testen. |
| Geen DSP-configuratie zonder vereiste klokken | Initialisatievolgorde uit driver/TI-documentatie; log stappen en uitkomst. |
| Buffers blijven geldig tijdens I/O | Eén eigenaar; I/O afronden vóór reset/deinit; vaste buffers en eindige timeouts. |
| Ontbrekende versterker veroorzaakt geen rebootlus | Fout loggen, playback niet starten, herstelpad expliciet houden. |
| Build reproduceerbaar | Vastgezette versies en exacte buildcommando's in de project-README. |

Acceptatie: build geslaagd, gecontroleerde korte playback, terugkeer naar stilte,
herhaalbare herstart en foutgedrag zonder ongecontroleerd geluid. Hardwaregedrag
is pas geverifieerd na een geregistreerde proef, niet na alleen een build.

## Wat volgt

Eén microfoon valideren; daarna audio naar Linux en push-to-talk; vervolgens lokale
Whisper/TTS, een GPU-model en cloudadapters. Kies het netwerkprotocol vóór de eerste
stream, met expliciete framing, formaat, grenzen, deadlines en sessie/beurt-ID's.
Na annulering mogen oude chunks niet opnieuw hoorbaar worden.

Nog nodig: boardfoto's/revisie, microfoonmodule, voeding/luidsprekers, lokale
Linux-specificaties en GPU-hostinformatie. Leg transport-, model- en latencykeuzes
vast wanneer die informatie en eerste metingen beschikbaar zijn.
