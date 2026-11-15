# ESP32 audio Jarvis

Een interactieve spraakassistant met een Louder-ESP32-WROVER, een zelfstandige
Linux-coördinator, een GPU-pc en verwisselbare lokale en cloudmodellen.

**Status:** ontwerp en hardware-identificatie. Er is nog geen firmwareproject,
hardwarevalidatie of complete verzameling schema's en foto's in deze repository.

## Uitgangspunt

Minder code, minder mechanismen, duidelijke contracten. Bouw de kleinste werkende
stap en verifieer die voordat de volgende verantwoordelijkheid wordt toegevoegd.
Eenvoud mag niet afhangen van het overslaan van foutafhandeling.

- Bestaand board behouden: klassieke ESP32-WROVER, geen ESP32-S3.
- ESP32-firmware bouwen met PlatformIO en ESP-IDF, eigen code bij voorkeur in C.
- Zelfstandige Linux-service; Whisper en TTS als bestaande engines hergebruiken.
- GPU-pc met twee RTX4090's aanvankelijk via een SSH-tunnel naar een model-API.
- Provider- en modelkeuze op Linux, buiten de firmware.
- Eerst hardwaredossier en relevante ontwerpbesluiten, daarna audio-bringup.

## Wegwijzer

- [Status: waar we zijn](docs/status.md)
- [Besluitenlog](docs/decisions.md)
- [Coding standards](docs/contributing/coding-standards.md)
- [Architectuur en eerste mijlpaal](docs/architecture.md)
- [Boardidentificatie en bronnen](hardware/louder_esp32/README.md)
- [Instructies voor coding agents](AGENTS.md)

## Indeling

We volgen de organisatie van Omnibot: documentatie bij het onderwerp en zelfstandige
PlatformIO-projecten per toepassing of hardwareproef. Mappen ontstaan pas wanneer
ze inhoud hebben. De beoogde indeling is:

```text
apps/jarvis/esp_jarvis/               uiteindelijke PlatformIO-toepassing
tests/bringup/audio/esp_audio/       eerste zelfstandige audio-hardwareproef
hardware/louder_esp32/               eigen board, bedrading en bronverwijzingen
docs/                               ontwerp, werkwijze en coding standards
```

`esp_` betekent hier ESP32; gebruik `linux_` voor toekomstige hosttoepassingen.
De eerste code komt in de audio-hardwareproef. De toepassingsmap volgt pas wanneer
er assistantfunctionaliteit is. Houd geen gekopieerde drivers naast elkaar in stand.

## Eerstvolgende stap

Leg foto's van beide boardzijden, revisie, moduleopschrift, microfoonposities,
voeding en luidsprekers vast. Koppel daaraan het juiste schema. Kies vervolgens
een vastgezette WROVER-toolchain en bouw de minimale audio-hardwareproef volgens
de [acceptatiecriteria](docs/architecture.md#eerste-firmwaremijlpaal).
