# Protocolafweging: ESP32 ↔ Linux

**Status: open.** Gewenst is een verbinding waarop ESP32 en Linux zelfstandig berichten kunnen sturen `[confirmed: gebruiker, 2026-11-18]`. Asynchroon betekent hier bijvoorbeeld dat Linux playback kan starten zonder te wachten op een nieuw verzoek van het board. Dat kan ook op een directe socket. Pub/sub lost een andere vraag op: **moet de afzender weten welke diensten zijn berichten afnemen?**

## Eigenschappen waarop we vergelijken

Sinds [B-010](decisions.md#b-010-lokale-dienstkeuze-en-push-to-talk-in-het-lab) hoort discovery bij de ESP32. Vergelijk voor **beide** kandidaten of het board een bruikbare lijst kan tonen of uitspreken nadat de primaire assistent verdwijnt. Test één functie met twee gelijktijdige studentversies. Bij TCP kan Linux bijvoorbeeld cataloguswijzigingen sturen; bij pub/sub kan het board aankondigingen volgen. Een beschikbare gebruikersdienst is niet hetzelfde als een losse STT-worker. Deze eis kiest nog geen protocol.

- **Berichtgrenzen:** onderscheid opname, einde opname, antwoordaudio, status en stop. Een nieuw audioblok mag niet op een applicatiebevestiging voor het vorige hoeven wachten.
- **Begrensde achterstand:** bij het [voorgestelde netwerkformaat](architecture.md#audiohypothese-eerst-bewijzen) van 48 kHz mono PCM16 is 20 ms audio 1.920 bytes. Tien blokken kosten 19.200 bytes én 200 ms aan audio, exclusief transportbuffers. Leg vast wat bij een volle buffer vervalt of wanneer een beurt wordt afgebroken.
- **Annulering:** stop lokaal, leeg de playbackbuffer en verwerp late blokken van de oude beurt. Betrouwbare levering alleen is niet voldoende: op TCP kan een dringend bericht niet voorbij eerder verzonden bytes in dezelfde richting.
- **Herstel:** koppel berichten aan sessie en beurt. Gebruik zo nodig een volgnummer om ontbrekende of dubbele applicatieblokken te herkennen. Een nieuwe verbinding mag oude antwoorden niet opnieuw hoorbaar maken.
- **Dynamische beschikbaarheid:** onderscheid een bereikbare GPU-host, een levende STT-dienst en een geladen model dat werk kan aannemen. Meerdere waarnemers mogen een gebeurtenis zien; een opdracht krijgt één geselecteerde uitvoerder. Na een timeout is de uitkomst mogelijk onbekend.
- **Kosten op de WROVER:** vergelijk vrij intern RAM naast wifi en I²S-DMA, CPU-gebruik, wachtrijdiepte en tijd tot stilte, met de beoogde authenticatie en versleuteling `[verify: hardwareproef]`.

## Alternatieven

| Optie | Voordeel voor Jarvis | Nadeel of grens |
| --- | --- | --- |
| Eigen berichten op TCP/TLS | Eén bidirectionele verbinding; zelf bepalen hoe groot berichten en buffers mogen zijn. Leerzaam en een korte route naar Linux. | TCP levert bytes: één `recv()` kan een deelframe of meerdere frames bevatten. Zelf framing, lengtevalidatie, versiebeheer en herstel bouwen. Geen ingebouwde discovery of subscriptions. |
| HTTPS | Eenvoudig voor configuratie, diagnostiek of een complete opname uploaden. | Klassieke request/response past slecht bij spontaan stop of playback vanaf Linux. Streaming kan, maar vraagt aanvullende afspraken. |
| WebSocket over TLS | Permanente bidirectionele verbinding met binaire berichtgrenzen; ook vanuit een browser toegankelijk. | Geen discovery of pub/sub zonder applicatielogica; dezelfde TCP-wachttijden. |
| MQTT over TCP/TLS | Eén broker met topics; statusmonitoren en apparaten kunnen onafhankelijk aansluiten. Last Will en retained status helpen bij aanwezigheidsmeldingen. | Broker in het audiopad. QoS is geen deadline. Gebruik geen retained live-audio en bepaal wat een persistente sessie na reconnect nog mag afleveren. |
| Zenoh-pico met Linux-router | Eén API voor publicaties, subscriptions, queries en liveliness. Een vaste TCP-verbinding met de router kan blijven bestaan als diensten verhuizen. | Meer configuratie en een extra afhankelijkheid. Liveliness bewijst geen modelgereedheid of exclusieve uitvoering. WROVER-resourcegebruik en routerherstart testen. |
| WebRTC | Realtime media, jitterbuffer, verliesafhandeling, versleuteling en NAT-traversal; datakanaal naast media mogelijk. | Een sessie is niet hetzelfde als een geordende TCP-stroom; media gaan meestal over UDP. Extra signalering en mediastack op het board. Interessanter bij browseraudio of een onbetrouwbaar netwerk. |

**Praktijkvoorbeelden:** [Wyoming](https://github.com/rhasspy/wyoming) gebruikt JSON-headers en binaire payloads over TCP voor spraakgebeurtenissen. De [ESPHome Native API](https://esphome.io/components/api/) gebruikt een apparaatverbinding met Protocol Buffers. [Home Assistant MQTT](https://www.home-assistant.io/integrations/mqtt/) laat discovery en availability via topics zien. [Zenoh](https://zenoh.io/docs/overview/abstractions/) en [zenoh-pico](https://github.com/eclipse-zenoh/zenoh-pico) zijn referenties voor communicatie op basis van benoemde gegevensstromen. Dit zijn voorbeelden om van te leren, geen standaard waaraan Jarvis moet voldoen.

## Voorbeeld van pub/sub-namen

Schets voor de proef, geen API-contract. `lab1` is een satelliet; de beurt-ID en het volgnummer zitten in het bericht, zodat de subscription tussen beurten kan blijven bestaan.

| Sleutel | Uitgever → afnemer | Inhoud |
| --- | --- | --- |
| `jarvis/v1/sat/lab1/audio/up` | ESP32 → coördinator | Microfoonblokken |
| `jarvis/v1/sat/lab1/audio/down` | Coördinator → ESP32 | Antwoordaudio |
| `jarvis/v1/sat/lab1/event` | ESP32 → coördinator | Knop, einde opname, lokaal gestopt |
| `jarvis/v1/sat/lab1/control` | Coördinator → ESP32 | Stop of einde beurt |
| `jarvis/v1/service/stt/gpu1/status` | STT-instantie → coördinator | `loading`, `ready`, `busy` |

Een statusmonitor kan alleen de laatste stroom volgen, zonder microfoonaudio te ontvangen. Een nieuwe STT-instantie die zich meldt is nog geen opdracht om een lopende opname te transcriberen.

## Voorgestelde beslisproef

Vergelijk **eigen TCP-framing** als directe referentie met **Zenoh-pico via een router**. Gebruik dezelfde WROVER, blokduur, audio, netwerkcondities en beveiliging. Spreek de grenswaarden vooraf af. Er zijn nog geen metingen.

| Proef | Wat registreren we? |
| --- | --- |
| Stream dezelfde opname; vertraag de ontvanger | Minimum vrij intern RAM, CPU-gebruik, maximale queue in bytes en ms, verlies of afbreken bij overschrijding. |
| Stop met een gevulde playbackbuffer; stuur daarna een oud antwoord | Tijd tot hoorbare stilte en of oude audio alsnog speelt. |
| Verbreek en herstel de verbinding tijdens een beurt | Tijd tot de volgende bruikbare beurt; eventuele herhaling van audio of opdrachten. Test bij Zenoh ook routerherstart. |
| Verwijder een STT-dienst en voeg een tweede toe | Tijd tot bruikbaarheid, keuze van één uitvoerder, gedrag zonder beschikbare dienst. |
| Voeg een passieve statusmonitor toe | Welke code, configuratie en subscriptions veranderen? Moet de ESP32 opnieuw gebouwd worden? |

Beantwoord eerst de [open protocolvragen](status.md#protocolkeuze). Leg de keuze en doorslaggevende argumenten vervolgens vast in het [besluitenlog](decisions.md). Werk daarna de draadinterface uit volgens B-005.
