# Onderzoeksplan: context en initiatief in het lab

**Backlog — onderzoekswens, 2026-11-18.** De [filmscène](https://www.youtube.com/watch?v=DZaAFADoF1M) en de voorbeelden van de bouwer geven ons een ontwerpvraag: hoe kan de assistent aansluiten bij eerder werk, ook na een nieuwe sessie of een wissel van taalmodel? Onderstaande voorbeelden zijn gewenste interacties, geen letterlijke transcriptie of vastgestelde architectuur.

| Interactiepatroon | Wat willen we onderzoeken? |
| --- | --- |
| **Verwijzen naar eerder werk.** ‘Gebruik het materiaal van de buitenmantel van de satelliet uit project X.’ | Zoek het juiste onderdeel én de betreffende ontwerprevisie terug. Toon de bron bij het voorstel. Als twee ontwerpen passen, stel één onderscheidende vraag. |
| **Aansluiten bij iemands werkwijze.** De gebruiker wil berichten doorgaans per project horen. | Hoe ontstaat een voorkeur uit eerdere interacties, hoe corrigeert de gebruiker die, en hoe voorkomen we dat een eenmalige keuze een blijvende regel wordt? |
| **De buitenwereld volgen.** Zijn er nieuwe publicaties over Tony, of in ons lab over een gevolgd project? | Welke dienst volgt de gekozen bronnen, herkent dubbele berichten en bewaart publicatiedatum en herkomst? Wanneer is iets relevant genoeg om te melden? |
| **Op een passend moment initiatief nemen.** Er zijn duizend ongelezen spraakberichten. | Laat een timer of gebeurtenis aanleiding zijn voor een samenvatting. Bied groepering, volgorde en presentatie aan, en vergelijk direct melden met wachten tot de gebruiker klaar is met een taak. Een melding opent de microfoon niet; antwoorden blijft push-to-talk. |
| **Een kort vervolg begrijpen.** Op het voorstel over de berichten zegt de gebruiker: ‘Verwijder ze.’ | Welke verzameling is zojuist aangeboden? Een later binnengekomen bericht hoort daar niet vanzelf bij. Onderscheid het begrijpen van de verwijzing, toestemming voor de handeling en de uitkomst die de berichtendienst terugmeldt. |

## Aanpak en beoogde opbrengst

Zie ook [B-012](decisions.md#b-012-alle-berichten-verwijst-naar-een-vaste-selectie): de selectiegrens is een harde eis; de uitvoering hieronder is een voorstel.

1. **Literatuuronderzoek.** Zoek in ACM Digital Library en via Google Scholar naar *mixed-initiative interaction*, *conversational grounding*, *personal information retrieval* en *notification timing / interruptibility*. Startpunten zijn Horvitz, [*Principles of Mixed-Initiative User Interfaces*](https://doi.org/10.1145/302979.303030), en Dumais e.a., [*Stuff I've Seen*](https://doi.org/10.1145/860435.860451). Noteer per geselecteerd paper de onderzochte taak, het bewijs, de beperking en één idee dat we in het lab kunnen toetsen.
2. **Verantwoordelijkheden vergelijken.** Gebruik dezelfde opdrachten voor twee varianten: alleen gespreksgeschiedenis aan het LLM geven, of projectfeiten, voorkeuren, berichtselecties en geplande gebeurtenissen expliciet door de backend laten beheren. Beschrijf per gegeven wie het mag wijzigen, hoe lang het geldig blijft en wat bij een modelwissel behouden moet blijven. Kies pas daarna welke onderdelen nodig zijn.
3. **Kleine reproduceerbare proef.** Geef twee fictieve projecten verschillende materialen en revisies. Vraag naar de buitenmantel, begin een nieuwe sessie en herhaal met een ander model. Laat vervolgens een nieuw proefbericht binnenkomen tussen het voorlezen van een selectie en ‘verwijder ze’. Leg vast: klopt de ontwerpbron, vraagt Jarvis door bij twijfel, komt de melding op een bruikbaar moment en blijft het nieuwe bericht behouden?

**Klaar voor bespreking** zodra er een korte literatuurnotitie, een vergelijking van beide varianten en proefresultaten liggen. Leg een eventuele architectuurkeuze daarna vast in het [besluitenlog](decisions.md).

## Waar ligt de tijdgrens van ‘alle’?

| Voorbeeldtijd | Gebeurtenis | Wat weet de gebruiker? |
| --- | --- | --- |
| 10:00:00 | De dienst leest de selectie uit mailboxrevisie 41 voor: A en B. | ‘Er zijn twee voicemails.’ |
| 10:00:03 | PTT-beurt 7: ‘Verwijder alle voicemails.’ De beurt verwijst naar die selectie. | A en B zijn bedoeld. |
| 10:00:04 | C wordt zichtbaar in revisie 42. Zijn afzendtijd was 09:58. | C was geen deel van de opdracht. |
| 10:00:08 | Na vertraging bij spraakherkenning bereikt de opdracht de berichtendienst. | Zowel ‘verwijder alles wat er nu is’ als filteren op afzendtijd zou C ten onrechte wissen. |

Registreer per beurt PTT-begin en -einde (apparaat, sessie en lokale monotone tijd); registreer aan backendzijde ontvangst en commit. Bewaar bij berichten de brontijd én het moment/de revisie waarop ze in de mailbox zichtbaar werden. UTC-tijden helpen bij reconstructie, lokale monotone tijden bij het meten van duur. Klokafwijking, gelijke timestamps en vertraagde bezorging maken ze ongeschikt als enige selectiegrens. Een mailboxrevisie ordent alleen wijzigingen **binnen die mailbox**; de koppeling tussen de beurt en de vooraf bekende selectie legt het verband tussen beide systemen.

**Selecteren.** De dienst die de lijst aanbiedt, legt in één consistente lezing de mailbox, revisie en stabiele, niet-hergebruikte bericht-ID's vast. Een ondoorzichtig selectietoken volstaat voor het board; het hoeft niet duizend ID's te ontvangen. Later ophalen mag ook als de opslag exact die historische selectie kan reconstrueren. Een losse opdracht zonder eerdere lijst is een ander geval: ontbreekt een betrouwbare grens voor wat bij het spreken bekend was, voer dan niets uit. Bied bijvoorbeeld eerst de huidige selectie aan en vraag of de gebruiker *die* bedoelt. Een verlopen token mag nooit stilzwijgend een verse selectie opleveren.

**Uitvoeren en herstellen.** Geef de logische opdracht één `command_id`, gebonden aan gebruiker, mailbox, actie en selectie. Een retry gebruikt dezelfde ID en inhoud; hergebruik met andere inhoud wordt geweigerd. Bij één database: registreer de unieke opdracht, wijzig uitsluitend de geselecteerde ID's en bewaar de uitkomst in dezelfde duurzame transactie. Een gelijktijdig duplicaat of een retry na een verloren antwoord krijgt de bestaande uitkomst terug. ‘Reeds afwezig’ is iets anders dan ‘door deze opdracht verwijderd’. Bewaar het resultaat ten minste zolang retries worden geaccepteerd; daarna geen automatische heruitvoering.

Bij een externe voicemailprovider omvat de lokale transactie het externe effect niet. Bewaar voortgang per bericht; gebruik waar mogelijk dezelfde idempotentiesleutel bij de provider. Valt de verbinding na een deelbewerking weg, vraag eerst de uitkomst op of controleer de toestand. Blijft die onbekend, meld dat en claim geen voltooiing. Idempotent betekent hier: één intentie heeft bij herhaling hetzelfde bedoelde effect, niet dat er maar één netwerkverzoek plaatsvindt.

**Terugmelden.** Lees na bevestigde uitvoering de mailbox opnieuw voor de melding, niet om de wisopdracht uit te breiden. Vergelijk met de oorspronkelijke revisie en selectie. Als A en B verwijderd zijn en C bij die controle aanwezig is: ‘De twee geselecteerde voicemails zijn verwijderd. Er is één nieuwe binnengekomen. Wil je die voorlezen, archiveren of verwijderen?’ Bind het vervolg aan C. Komt er tijdens het uitspreken nog D bij, dan blijft ook D buiten die vervolgkeuze.

**Te toetsen:** injecteer C vóór uitvoering met een oudere afzendtijd; verlies de bevestiging ná de commit en herhaal na herstart; laat een externe dienst uitvallen na A maar vóór een bevestigde uitkomst voor B. Controleer telkens zowel de overgebleven ID's als wat Jarvis over de uitkomst zegt.
