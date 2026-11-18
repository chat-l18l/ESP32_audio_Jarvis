# Documentatiewegwijzer

Begin bij het [systeemdiagram](architecture.md#systeemdiagram). Volg voor een ontwerpkeuze dit spoor:

| Vraag | Waar kijk of schrijf ik? | Inhoud |
| --- | --- | --- |
| Waarom overwegen we dit? | `plan-<onderwerp>.md`, zoals de [protocolafweging](plan-protocolkeuze.md) | Kandidaten, argumenten, bronnen en voorgestelde proef. |
| Wat staat nog open? | [Open vragen](status.md#open-vragen) | Concrete vraag of ontbrekende meting. Sluit af met een link naar het besluit of bewijs. |
| Wat is gekozen? | [Besluitenlog](decisions.md) | Genummerde keuze, status en reden. Verwijs naar de afweging; vervang een achterhaald besluit met een nieuw nummer. |
| Wat garandeert een onderdeel? | `docs/<onderwerp>-spec.md` | Versie, eigenaar, pre- en postcondities, grenzen en foutgedrag. Een modulecontract verhuist bij implementatie naar de publieke header (B-005). |
| Hoe sluit ik aan? | Publieke `.h` voor C; `docs/protocol.md` voor ESP32 ↔ Linux | Aanroepen of berichttypen, parameters of byte-indeling, versie en een gebruiksvoorbeeld. Voor een aparte service-API past `docs/<dienst>-api-spec.md` zodra die interface bestaat. |
| Hoe verifieer ik dat? | README naast de proef onder `tests/bringup/` | Genummerde acceptatiecriteria, procedure, pass/fail en bewijs. Bedrading en schema's staan in het [hardwaredossier](../hardware/louder_esp32/README.md). |

Bijvoorbeeld: *hoeveel audio mag wachten?* hoort bij de open vragen. *Na stop wordt audio van de oude beurt verworpen* beschrijft gewenst gedrag voor een contract. *In welk veld staat de beurt-ID?* hoort bij de protocol-API. Eén interfacebeschrijving kan zowel contract als API bevatten: verwijs naar dezelfde bron. De spec- en API-paden hierboven zijn naamafspraken voor de bouwfase, geen claim dat die interfaces al vastliggen.
