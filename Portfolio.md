# Portfolio afstudeerproject

*Ontwerp en realisatie van een herbruikbare orkestproductie-applicatie*

| Gegeven | Waarde |
| --- | --- |
| Naam | Ardit Fazliji |
| Studentnummer | 1023891 |
| Opleiding | Informatica, Hogeschool Rotterdam (CMI) |
| Cursus | Graduation Project, INFAFS04 / INFAFS25 |
| Bedrijf | Bond for Web Solutions, Vlaardingweg 62, 3044 CK Rotterdam |
| Company supervisor | Martijn Verbeek |
| Technisch begeleider | Marnix Bouwman |
| School supervisor | [AANVULLEN: naam school supervisor] |
| Periode | [AANVULLEN: officiële start- en einddatum] |
| Versie | Concept, 8 oktober 2026 |

Dit portfolio laat per competentie zien wat ik in het afstudeerproject heb gedaan, waarom, met welk resultaat, en waar het bewijsmateriaal staat. De [README](<README.md>) is de leeswijzer van de Graduation Folder; dit portfolio is de uitgebreide toelichting per competentie.

---

## Inhoud

- [Voorwoord](#voorwoord)
- [1. Inleiding](#1-inleiding)
- [2. Afstudeerbedrijf](#2-afstudeerbedrijf)
- [3. Afstudeeropdracht](#3-afstudeeropdracht)
- [4. Competentiematrix](#4-competentiematrix)
- [5. Professional Skills & Manage and Control](#5-professional-skills--manage-and-control)
- [6. Analysis](#6-analysis)
- [7. Design](#7-design)
- [8. Realisation](#8-realisation)
- [9. Advice](#9-advice)
- [10. Gebruik van AI](#10-gebruik-van-ai)
- [11. Reflectie en conclusie](#11-reflectie-en-conclusie)
- [Literatuurlijst](#literatuurlijst)
- [Bewijsmateriaal](#bewijsmateriaal)

Elk competentiehoofdstuk heeft dezelfde opbouw: **Leerdoel**, **Toelichting**, **Aanpak**, **Resultaat**, **Bewijsmateriaal**, **Feedback company supervisor** en **Reflectie**.

---

## Voorwoord

Voor mijn afstudeerproject werk ik bij Bond for Web Solutions, een Rotterdams softwarebedrijf dat digitale platforms en maatwerk webapplicaties bouwt. Mijn opdracht gaat over Tutti: de nieuwe planning- en administratieapplicatie van het Metropole Orkest (MO), die de planning, afspraken, documenten en bladmuziek van producties op één plek moet samenbrengen.

Dit portfolio beschrijft mijn werk per competentie. Ik leg daarbij vooral uit hoe ik te werk ben gegaan en waarom ik keuzes heb gemaakt, en verwijs voor elke bewering naar het bewijsmateriaal in de Graduation Folder.

[AANVULLEN: eventueel een korte dankbetuiging aan de company supervisor, de technisch begeleider, de school supervisor en collega's.]

---

## 1. Inleiding

Bond for Web Solutions beheert digitale omgevingen voor meerdere orkestklanten. Binnen het productiedomein zijn planning, afspraken, wijzigingen, bladmuziek, audio en documenten verdeeld over verschillende systemen en opslagbronnen. Bij het Metropole Orkest staat de planning in OPAS, met daarnaast Excel, AFAS en SharePoint, en sinds 2026 een agenda op het intranet (de Agenda Viewer). Daarnaast verschillen orkesten in hun werkwijze, bijvoorbeeld in filters, kleurcodering, bezettingsinformatie en documentopslag.

**Probleemstelling.** Bond beschikt nog niet over één herbruikbare en technisch onderbouwde oplossing waarmee productieplanning en bijbehorende informatie voor verschillende orkestklanten centraal kan worden beheerd en ontsloten. Door verschillende databronnen, klantspecifieke wensen en gevoelige bestanden ontstaan vraagstukken rond architectuur, integratie, autorisatie, onderhoudbaarheid en schaalbaarheid.

**Hoofdvraag.** Hoe kan een veilige, onderhoudbare en herbruikbare webapplicatie worden ontworpen en gerealiseerd die orkestproducties, planning en bijbehorende informatie centraal beheert en ontsluit en tegelijk aansluit op verschillende technische en functionele behoeften van orkestklanten?

**Deelvragen** en waar ze in dit portfolio beantwoord worden:

| # | Deelvraag | Competentie |
| --- | --- | --- |
| 1 | Welke functionele en niet-functionele requirements zijn noodzakelijk en welke onderdelen moeten per orkest configureerbaar zijn? | [Analysis](#6-analysis) |
| 2 | Welke architectuur- en integratieaanpak is het meest geschikt voor productiegegevens, wijzigingen en documenten uit de bestaande databronnen? | [Analysis](#6-analysis), [Advice](#9-advice), [Design](#7-design) |
| 3 | Hoe moet het data- en autorisatiemodel worden ingericht om rollen en toegang tot gevoelige informatie veilig te beheren? | [Design](#7-design) |
| 4 | Hoe moet de gebruikersinterface worden ontworpen zodat planning en productie-informatie overzichtelijk, responsive en toegankelijk is? | [Design](#7-design) |
| 5 | In welke mate voldoet de gerealiseerde oplossing aan de requirements en kwaliteitscriteria na technische tests en gebruikersvalidatie? | [Realisation](#8-realisation) |

[AANVULLEN: één alinea over de verhouding tussen het afstudeerproject (een herbruikbare orkestproductie-applicatie voor meerdere orkestklanten) en Tutti (de applicatie voor het Metropole Orkest). Bijvoorbeeld: is Tutti de eerste toepassing van de herbruikbare applicatie?]

---

## 2. Afstudeerbedrijf

**Bond for Web Solutions** is een Rotterdams softwarebedrijf dat digitale platforms en maatwerk webapplicaties ontwikkelt voor organisaties in onder andere de culturele en publieke sector. Het bedrijf richt zich vooral op het bouwen en doorontwikkelen van bestaande platforms, waarbij systemen zoals Microsoft 365, SharePoint en externe API's in één samenhangende oplossing worden geïntegreerd. De focus ligt op gebruiksvriendelijkheid, betrouwbaarheid en het hergebruik van bestaande infrastructuur. De standaard van Bond is DNN met 2sxc; het intranet van het Metropole Orkest (Metrostation) draait op DNN.

| Rol | Naam | Rol in mijn opdracht |
| --- | --- | --- |
| Company supervisor | Martijn Verbeek | Scope, voortgang, bedrijfsbelang en feedback op resultaten |
| Technisch begeleider | Marnix Bouwman | Technische context, architectuur- en codereviews |
| School supervisor | [AANVULLEN] | Begeleiding vanuit de Hogeschool Rotterdam |

**Beleid van Bond over het gebruik van AI:** [AANVULLEN: wat Bond toestaat en verwacht bij het gebruik van AI-tools.]

---

## 3. Afstudeeropdracht

De opdracht is om te onderzoeken hoe planning, afspraken, documenten en bladmuziek van orkestproducties in één webapplicatie kunnen worden samengebracht, en die applicatie te ontwerpen en te realiseren. De oplossing moet aansluiten op de bestaande DNN-omgeving van Bond en tegelijk zo zijn opgezet dat verschillen tussen orkestklanten beheersbaar blijven.

**Vertrekpunt.** Tutti bestaat bij de start nog niet als applicatie. Er is wel werk van anderen waarop ik voortbouw:

- de **Agenda Viewer**, een 2sxc-app op Metrostation, gebouwd door Angelique Smit tijdens haar stage;
- het **ER-schema en de designs** van Martijn Verbeek;
- de user stories, de functiescan en de OPAS-analyse in **Basecamp**.

In dit portfolio maak ik steeds duidelijk wat mijn eigen werk is en waar ik voortbouw op werk van anderen.

**Binnen scope** (afstudeervoorstel §3.7): producties, afspraken en agendaweergaven; detailinformatie en wijzigingen; beheerfunctionaliteit voor producties en afspraken; ontsluiten van bladmuziek, audio en documenten; authenticatie, autorisatie en rolgebaseerde toegang; configureerbare verschillen tussen orkestklanten; een responsive en toegankelijke gebruikersinterface, geautomatiseerde tests, technische documentatie en evaluatie.

**Buiten scope:** een volledige vervanging van bestaande orkestsoftware; financiële administratie, contractbeheer, personeelsadministratie, urentelling en verlofbeheer; een native mobiele app of uitrol naar alle klanten; commerciële afspraken en licentiemodellen.

**Werkwijze.** Ik werk in korte iteraties. Per iteratie selecteer ik requirements, ontwerp, bouw en test ik ze en laat ik het resultaat beoordelen. De voortgang houd ik bij in Basecamp en in mijn [logboek](<Competencies/01. Professional Skills & Manage and Control/logboek.md>).

---

## 4. Competentiematrix

Welk bewijsmateriaal welke competentie aantoont. De nummers verwijzen naar het hoofdstuk [Bewijsmateriaal](#bewijsmateriaal).

| # | Bewijsmateriaal | Professional Skills & Manage and Control | Analysis | Design | Realisation | Advice |
| --- | --- | :-: | :-: | :-: | :-: | :-: |
| B1 | Afstudeervoorstel | X | X | X | X | X |
| B2 | Logboek met bewijs per dag | X | | | | |
| B3 | Kanbanbord in Basecamp | X | | | | |
| B4 | Versiebeheer en build-straat (GitHub) | X | | | X | |
| B5 | Tutti & OPAS: het complete dossier | | X | | | |
| B6 | Analyse Agenda Viewer | | X | | | X |
| B7 | Technische SRS - Tutti | | X | X | | |
| B8 | Architectuurontwerp - Tutti | | | X | | |
| B9 | Databaseontwerp - Tutti | | | X | | |
| B10 | Backendontwerp - Tutti | | | X | | |
| B11 | Gegevensstromen - Tutti | | | X | | |
| B12 | Broncode Tutti-module | | | | X | |
| B13 | Automatische tests | X | | | X | |
| B14 | Adviesrapport Eigen DNN-module of 2sxc | | X | | | X |
| B15 | Adviesrapport Frameworks of CMS | | | | | X |
| B16 | Testrapport | | | | X | |
| B17 | Halfway presentation | X | | | | |
| B18 | Procesverslag en reflectie | X | | | | |
| B19 | Beoordeling company supervisor | X | | | | |

B16 tot en met B19 bestaan nog niet; zie [Bewijsmateriaal](#bewijsmateriaal).

---

## 5. Professional Skills & Manage and Control

### Leerdoel

Ik wil het afstudeerproject zelfstandig plannen en bewaken, met stakeholders communiceren, werken volgens de ontwikkel- en kwaliteitsafspraken van Bond, en mijn voortgang, keuzes en verwerkte feedback zo vastleggen dat iemand anders ze kan nalopen.

### Toelichting

Tutti is een project met veel betrokkenen: het Metropole Orkest als klant, de company supervisor, de technisch begeleider, en daarnaast eerdere ontwikkelaars wier werk ik overneem. Er is veel bestaand materiaal in Basecamp, maar nog geen planning voor de verdere bouw. Daarom moest ik eerst overzicht maken van wat er al was en wat er nog moest gebeuren, voordat ik kon gaan bouwen.

Voor Manage and Control gaat het ook om de beheersing van het ontwikkelproces zelf: versiebeheer, automatisch bouwen en testen, en een vaste plek voor taken en voortgang.

### Aanpak

- **Logboek met bewijs.** Ik houd per werkdag bij wat ik heb gedaan en waarom, en koppel elke dag aan bewijs: het document, de commit of een schermafbeelding. Zo kan een examinator elke regel controleren.
- **Kanbanbord in Basecamp.** Ik heb de taken voor Tutti als kaarten in de Card Table van het Basecamp-project gezet en daarna verdeeld over de kolommen Triage, Backlogs, To Do, In progress en Testing. Zo zien het team en ik in één overzicht wat de planning is en wat de stand van zaken is.
- **Versiebeheer en automatische controle.** De broncode staat in een Git-repository op GitHub. Bij elke wijziging wordt Tutti automatisch gebouwd, getest en verpakt met GitHub Actions, en de build weigert pakketten met bekende beveiligingslekken.
- **Feedback verwerken.** Feedback van mijn docent over de mappenstructuur en over de focus van mijn documentatie heb ik verwerkt (25-09-2026). Feedback op Tutti heb ik verwerkt in de schermen (06-10-2026).
- **Communicatie met stakeholders.** [AANVULLEN: hoe en hoe vaak ik met de company supervisor, de technisch begeleider en het MO overleg, bijvoorbeeld wekelijkse afstemming, Basecamp-berichten of demo's.]

### Resultaat

- Een logboek van 11 werkdagen (88 uur, 16-09-2026 t/m 07-10-2026) waarin elke dag naar bewijs verwijst.
- Een kanbanbord dat is gegroeid van een eerste backlog met 28 kaarten naar een verdeeld bord: 14 kaarten in Triage, 27 in Backlogs, 11 in To Do, 3 in In progress en 9 in Testing (zie [B3](#b3-kanbanbord-in-basecamp)).
- Een ontwikkelstraat waarin elke wijziging automatisch wordt gebouwd en met ongeveer 570 tests wordt gecontroleerd.
- De documentatie volgt de mappenstructuur die mijn docent vroeg, met één map per competentie.

[AANVULLEN: resultaat van overleg met stakeholders en van de halfway presentation.]

### Bewijsmateriaal

- [B2 Logboek](#b2-logboek)
- [B3 Kanbanbord in Basecamp](#b3-kanbanbord-in-basecamp)
- [B4 Versiebeheer en build-straat](#b4-versiebeheer-en-build-straat)
- [B17 Halfway presentation](#b17-halfway-presentation) *(nog toe te voegen)*
- [B18 Procesverslag en reflectie](#b18-procesverslag-en-reflectie) *(nog toe te voegen)*

### Feedback company supervisor

[AANVULLEN: letterlijke feedback van Martijn Verbeek op deze competentie.]

### Reflectie

[AANVULLEN: reflectie op het proces, niet op wat ik geleerd heb. Beantwoord bijvoorbeeld: Klopte mijn planning met wat er echt gebeurde, en hoe heb ik bijgestuurd? Wanneer heb ik te laat of te vroeg om hulp of feedback gevraagd? Wat doe ik in de volgende iteratie anders en waarom?]

---

## 6. Analysis

*Deelvraag 1 (requirements en configureerbaarheid) en deelvraag 2 (architectuur en integratie).*

### Leerdoel

Ik wil requirements, gebruikersbehoeften, bestaande systemen, data en kwaliteitsaspecten analyseren, en op basis daarvan de kernproblemen en technische randvoorwaarden van Tutti vaststellen voordat ik ga ontwerpen en bouwen.

### Toelichting

Tutti moet bestaande systemen aanvullen en op termijn OPAS vervangen. Om dat goed te doen, moest ik eerst begrijpen wat er al bestond: de planning in OPAS, de Agenda Viewer die al live staat, en de wensen die het MO in Basecamp had vastgelegd. Een nieuw systeem op dezelfde data erft de problemen van die data; zonder die analyse zou ik dezelfde fouten opnieuw bouwen.

### Aanpak

- **Bronnen verzamelen.** Alles wat in Basecamp over Tutti en OPAS stond, heb ik samengebracht in één dossier: aanleiding, oplossingsrichting, fasering, OPAS-knelpunten, functiescan, user stories, architectuur en open punten. Het dossier beschrijft werk van anderen; mijn bijdrage is het verzamelen, ordenen en analyseren.
- **Bestaand systeem analyseren.** Ik heb de broncode van de Agenda Viewer (drie geëxporteerde versies, laatste V 0.1.05) onderzocht naast de documentatie en de scriptie van Angelique Smit. Ik keek hoe de applicatie werkt, wat er ontbreekt en welke technische problemen er zijn.
- **Technische requirements vastleggen.** In de Technische SRS heb ik de systeemcontext, de architectuur, het datamodel, het API-ontwerp, de integraties, de beveiliging, logging, performance, deployment, de teststrategie, de migratie vanuit OPAS, de risico's en de open beslissingen (`TD-001` t/m `TD-010`) vastgelegd. De functionele requirements staan in de user stories; de SRS verwijst ernaar via een traceability-tabel.
- **Technische haalbaarheid onderzoeken.** Ik heb onderzocht waar de gegevens in DNN zijn opgeslagen en hoe ze worden opgehaald (23-09-2026), en dat plan daarna in de echte DNN-omgeving getest (24-09-2026).
- **Interviews en requirementsessies.** [AANVULLEN: welke gesprekken ik met het MO, de company supervisor of gebruikers heb gevoerd, met datum en uitkomst.]

### Resultaat

- De analyse van de Agenda Viewer laat zien dat de frontend degelijk en geaccepteerd is (32 van 36 user stories af, live op Metrostation), maar dat de backend van V 0.1.05 onaf en onveilig is: elk schrijf-endpoint staat open voor anonieme gebruikers, de beheerdata staat in tabellen die de agenda niet uitleest, en er zijn geen tests en geen git-historie. Daaruit volgde het advies om de frontend te behouden en de backend opnieuw te bouwen (zie [Advice](#9-advice)).
- De Technische SRS (versie 0.1, 18 september 2026) is het doelbeeld voor de bouw. De keuzes die tijdens de bouw anders zijn uitgevallen, staan met reden in het [Architectuurontwerp](<Competencies/03. Design/Architectuurontwerp.md>), hoofdstuk 12.
- Het haalbaarheidsonderzoek in DNN bevestigde dat de aanpak bruikbaar was voordat ik erop verder bouwde.

[AANVULLEN: welke onderdelen per orkest configureerbaar moeten zijn (tweede helft van deelvraag 1) en waar dat is vastgelegd.]

### Bewijsmateriaal

- [B5 Tutti & OPAS: het complete dossier](#b5-tutti--opas-het-complete-dossier)
- [B6 Analyse Agenda Viewer](#b6-analyse-agenda-viewer)
- [B7 Technische SRS - Tutti](#b7-technische-srs---tutti)

### Feedback company supervisor

[AANVULLEN: letterlijke feedback van Martijn Verbeek op deze competentie.]

### Reflectie

[AANVULLEN: reflectie op het proces. Bijvoorbeeld: Waren de bronnen in Basecamp betrouwbaar genoeg, en hoe heb ik dat gecontroleerd? Welke requirements heb ik met gebruikers getoetst en welke niet? Wat zou ik in de analysefase anders aanpakken?]

---

## 7. Design

*Deelvraag 2 (architectuur en integratie), deelvraag 3 (data- en autorisatiemodel) en deelvraag 4 (gebruikersinterface).*

### Leerdoel

Ik wil de softwarearchitectuur, het data- en autorisatiemodel, de integraties, de gebruikersinteractie en de teststrategie van Tutti ontwerpen, en belangrijke ontwerpen laten beoordelen voordat ze worden gerealiseerd.

### Toelichting

Tutti draait binnen een bestaande DNN-omgeving, moet samenwerken met OPAS en de SharePoint Viewer, en geeft toegang tot gevoelige informatie zoals bladmuziek en persoonsgegevens. Het ontwerp moest daarom vooral twee vragen beantwoorden: hoe Tutti in de bestaande omgeving past zonder er afhankelijk van te worden, en hoe je zeker weet dat iemand alleen ziet wat die persoon mag zien.

### Aanpak

- **Eerste ontwerp in de SRS.** Het datamodel (ERD), de lagen, het API-ontwerp en het rollen- en rechtenmodel heb ik eerst in de Technische SRS uitgewerkt (17 en 18 september 2026).
- **Ontwerp volgt het advies.** Het ontwerp volgt het [adviesrapport](<Competencies/05. Advice/Eigen_DNN-module_of_2sxc.md>): het domein in een eigen DNN-module met eigen tabellen, en de domeinlogica in een aparte .NET Standard-library die niets van DNN weet.
- **Ontwerpkeuzes onderbouwen.** Per keuze heb ik vastgelegd waarom: pagina's op de server maken met Razor binnen DNN, alle regels en rechten op één plek, OPAS lezen in plaats van kopiëren, documenten via de SharePoint Viewer als de bezoeker, en korte adressen met de bron in het nummer.
- **Ontwerpdocumenten van de gebouwde versie.** Op 1 oktober 2026 heb ik vier ontwerpdocumenten gemaakt die versie 00.03.00 beschrijven: architectuur, database, backend en gegevensstromen, met diagrammen. Waar de gebouwde versie afwijkt van de SRS, staat dat met de reden erbij.
- **Gebruikersinterface.** [AANVULLEN: wireframes, mockups of schermontwerpen, en hoe ze zijn getoetst. Bijvoorbeeld de designs van Martijn Verbeek als vertrekpunt.]
- **Review van het ontwerp.** [AANVULLEN: wie het ontwerp heeft beoordeeld, wanneer, welke feedback er kwam en wat ik daarna heb aangepast.]

### Resultaat

- **Architectuur.** Tutti bestaat uit een dunne DNN-module en de library `Tutti.Core` met de regels, rechten en services. Controllers en schermen beslissen nooit zelf.
- **Autorisatiemodel.** Een centrale `VisibilityService` met een `PermissionMatrix` beslist voor elke handeling wie wat mag, op basis van DNN-rollen: Administrator, Planner, Productieleider, Musicus, Remplaçant en Functioneel beheer. Wat iemand niet mag zien, geeft "niet gevonden" (404). Elke wijziging komt in een audittrail die niet kan worden aangepast.
- **Datamodel.** De `Tutti_*`-tabellen, gekoppeld aan DNN-gebruikers en aan de OPAS-tabellen, met regels die de database zelf bewaakt.
- **Gegevensstromen.** Vier stromen staan stap voor stap beschreven: de OPAS-planning komt binnen, een planner wijzigt de planning, iemand bekijkt de planning, en documenten en bladmuziek.
- **Teststrategie.** Vastgelegd in de [Technische SRS](<Competencies/02. Analysis/Technische_SRS_Tutti.md#18-testing-strategy>), hoofdstuk 18.

### Bewijsmateriaal

- [B7 Technische SRS - Tutti](#b7-technische-srs---tutti), hoofdstukken 3, 5, 7, 9 en 18
- [B8 Architectuurontwerp - Tutti](#b8-architectuurontwerp---tutti)
- [B9 Databaseontwerp - Tutti](#b9-databaseontwerp---tutti)
- [B10 Backendontwerp - Tutti](#b10-backendontwerp---tutti)
- [B11 Gegevensstromen - Tutti](#b11-gegevensstromen---tutti)

### Feedback company supervisor

[AANVULLEN: letterlijke feedback van Martijn Verbeek op deze competentie.]

### Reflectie

[AANVULLEN: reflectie op het proces. Bijvoorbeeld: De ontwerpdocumenten beschrijven de gebouwde versie; welk deel van het ontwerp lag vast vóór het bouwen (SRS, advies) en welk deel is tijdens het bouwen ontstaan? Hoe heb ik afwijkingen van de SRS bewaakt? Wat zou ik eerder laten reviewen?]

---

## 8. Realisation

*Deelvraag 5 (in welke mate de oplossing voldoet na tests en gebruikersvalidatie).*

### Leerdoel

Ik wil de gekozen oplossing realiseren en integreren met de bestaande systemen, testautomatisering toepassen, de software beschikbaar maken in een representatieve omgeving, en met tests aantonen in welke mate de oplossing aan de eisen voldoet.

### Toelichting

Tutti moet binnen DNN draaien, gegevens uit OPAS tonen en documenten via de SharePoint Viewer ontsluiten, zonder dat iemand meer ziet dan die persoon mag. Dat maakt de realisatie gevoelig: een fout in de rechten of een kapotte koppeling is direct zichtbaar voor musici en planners. Daarom bouw ik in kleine versies en test ik elke versie automatisch.

### Aanpak

- **Iteratief bouwen in versies.** Elke versie voegt een afgebakend stuk functionaliteit toe en staat in de release notes van de module.
- **Testautomatisering.** Unit tests voor de domeinlaag en integratietests tegen een echte SQL Server-database, die bij elke wijziging automatisch draaien.
- **Integratie met bestaande systemen.** OPAS wordt rechtstreeks gelezen in plaats van gekopieerd; documenten en bladmuziek komen via de SharePoint Viewer, met de rechten van de bezoeker.
- **Beschikbaar maken.** Tutti wordt geleverd als installatiepakket voor DNN, met de DLL's, de schermen, de databasescripts en een manifest.

### Resultaat

| Datum | Resultaat |
| --- | --- |
| 30-09-2026 | Versie 00.02.00: de planning uit OPAS en documenten via SharePoint in Tutti; beveiliging, foutafhandeling en downloads verbeterd |
| 01-10-2026 | Versie 00.03.00: afspraken en producties uit OPAS en Tutti op dezelfde pagina's, met een label en filter per bron, en korte adressen met een bronletter (`/tutti/events/t12`, `/tutti/events/o3555`) met integratietests |
| 06-10-2026 | Aanpassingen op basis van feedback, en een scherm Instellingen waarin een beheerder de tijdzone, de themakleuren en de kleuren van de legenda kan aanpassen |
| 07-10-2026 | Export naar Outlook: één afspraak of de zichtbare afspraken downloaden als `.ics`-bestand. Gekozen voor dit standaardformaat zodat het ook in Google Agenda en Apple Agenda werkt; de export toont alleen afspraken die de gebruiker mag zien. Gecontroleerd met tests en met een losstaande iCalendar-lezer |

In totaal zijn er ongeveer 570 automatische tests. Ze dekken de regels, de rechten per rol (ook wat moet mislukken), elke SQL-opdracht tegen een echte database, alle schermen, beide talen en de koppelingen met OPAS en de SharePoint Viewer.

[AANVULLEN: testrapport met resultaten per kwaliteitsaspect uit het afstudeervoorstel (§4.4: functionaliteit, security, onderhoudbaarheid, performance, gebruiksvriendelijkheid en toegankelijkheid), gebruikersvalidatie, en de omgeving waarin Tutti draait (test, acceptatie of productie).]

### Bewijsmateriaal

- [B12 Broncode Tutti-module](#b12-broncode-tutti-module)
- [B13 Automatische tests](#b13-automatische-tests)
- [B4 Versiebeheer en build-straat](#b4-versiebeheer-en-build-straat)
- [B16 Testrapport](#b16-testrapport) *(nog toe te voegen)*

### Feedback company supervisor

[AANVULLEN: letterlijke feedback van Martijn Verbeek op deze competentie.]

### Reflectie

[AANVULLEN: reflectie op het proces. Bijvoorbeeld: Hielp het werken in kleine versies om problemen vroeg te zien? Welke fout vonden de tests die ik anders had gemist? Wat kon ik niet automatisch testen en hoe heb ik dat opgelost?]

---

## 9. Advice

*Deelvraag 2 (architectuur- en integratieaanpak).*

### Leerdoel

Ik wil oplossingsrichtingen vergelijken en Bond onderbouwd adviseren over architectuur, integraties en verdere doorontwikkeling, inclusief de afwegingen rond security, performance, onderhoudbaarheid en schaalbaarheid, en de situaties waarin mijn advies niet opgaat.

### Toelichting

Bij de start lagen er drie vragen die eerst beantwoord moesten worden: bouwen we verder op de Agenda Viewer of beginnen we opnieuw, bouwen we Tutti in 2sxc of in een eigen DNN-module, en wanneer is een framework beter dan een CMS? Die keuzes bepalen de rest van het project, dus ik wilde ze niet op gevoel maken.

### Aanpak

- **Doorgaan of opnieuw beginnen.** In de analyse van de Agenda Viewer heb ik de argumenten voor beide kanten naast elkaar gezet en een advies in drie fasen gegeven.
- **Eigen DNN-module of 2sxc.** Ik heb de twee opties eerst technisch beschreven en daarna per criterium vergeleken. Ook heb ik de ontwikkeltijd en kosten ingeschat, de nadelen en risico's van een eigen module benoemd, en beschreven waar 2sxc juist de betere keuze blijft.
- **Frameworks of CMS.** Ik heb een framework-architectuur (React/Next.js, Vue/Nuxt, ASP.NET Core, Laravel) vergeleken met een CMS (DNN, WordPress), met dezelfde opbouw: per criterium, kosten op korte en lange termijn, nadelen, en de situaties waarin een CMS verstandiger is.
- **Grenzen van het advies.** Bij elk advies heb ik vastgelegd onder welke voorwaarden het geldt en wanneer het herzien moet worden.

### Resultaat

- **Agenda Viewer (14 september 2026):** de app behouden, de backend van V 0.1.05 als prototype beschouwen en opnieuw bouwen, en de frontend niet van nul beginnen.
- **Eigen DNN-module of 2sxc (14 september 2026):** het Tutti-domein in een eigen DNN-module, en 2sxc behouden voor redactionele content. De scheidslijn: "alles wat aan een productienummer hangt en een rechtenvraag oproept, hoort in de module". Het advies geldt onder zes randvoorwaarden, waaronder een DNN-onafhankelijke library, autorisatie die standaard alles weigert met permissietests, en een geautomatiseerde build- en deploystraat.
- **Frameworks of CMS (23 september 2026):** geen keuze tussen een framework en een CMS, maar een grens op basis van waar het gedrag zit. Domeinlogica, rechten, transacties en integraties horen in een framework-architectuur; redactionele content hoort in een CMS.
- **Toegepast.** Het advies is gevolgd in het ontwerp en de bouw: Tutti is een eigen DNN-module met de domeinlogica in een aparte library, en de build wordt automatisch gebouwd en getest (zie [Design](#7-design) en [Realisation](#8-realisation)).

[AANVULLEN: wie het advies heeft geaccepteerd en wanneer (bijvoorbeeld de company supervisor of het MT van het MO), en of en hoe ik het advies heb gepresenteerd.]

### Bewijsmateriaal

- [B6 Analyse Agenda Viewer](#b6-analyse-agenda-viewer), hoofdstuk 5
- [B14 Adviesrapport Eigen DNN-module of 2sxc](#b14-adviesrapport-eigen-dnn-module-of-2sxc)
- [B15 Adviesrapport Frameworks of CMS](#b15-adviesrapport-frameworks-of-cms)

### Feedback company supervisor

[AANVULLEN: letterlijke feedback van Martijn Verbeek op deze competentie.]

### Reflectie

[AANVULLEN: reflectie op het proces. Bijvoorbeeld: Heb ik de alternatieven eerlijk vergeleken, of stond mijn voorkeur al vast? Hoe heb ik de stakeholders bij het advies betrokken? Wat zou ik doen als een van de herzieningsmomenten optreedt?]

---

## 10. Gebruik van AI

De Course Manual (§4.2) eist dat de Graduation Folder beschrijft of en hoe AI in het project is gebruikt; zonder die beschrijving is de folder niet ontvankelijk.

| Onderdeel | Invulling |
| --- | --- |
| Gebruikte AI-tools | [AANVULLEN: bijvoorbeeld Claude Code; de repository bevat instructies voor documentatie en het logboek in `.claude/skills/`] |
| Waarvoor | [AANVULLEN: bijvoorbeeld documenten structureren en taal corrigeren, code genereren of reviewen, tests schrijven] |
| Hoe ik de uitkomst heb gecontroleerd | [AANVULLEN: bijvoorbeeld feiten tegen de broncode en de documenten nagelopen, gegenereerde code gereviewd en getest] |
| Wat ik zelf heb gedaan | [AANVULLEN: welke keuzes, analyses en adviezen van mijzelf zijn] |
| Aansluiting op het beleid van Bond | [AANVULLEN] |

---

## 11. Reflectie en conclusie

[AANVULLEN aan het einde van het project:]

- **Antwoord op de hoofdvraag:** in welke mate Tutti een veilige, onderhoudbare en herbruikbare webapplicatie is geworden, met verwijzing naar de antwoorden op de vijf deelvragen.
- **Wat ik anders zou doen** als ik opnieuw zou beginnen, en waarom.
- **Hoe dit project heeft bijgedragen** aan mijn professionele ontwikkeling.
- **Vervolgstappen** voor Bond en het MO, bijvoorbeeld de open beslissingen uit de Technische SRS en de openstaande punten uit het [Architectuurontwerp](<Competencies/03. Design/Architectuurontwerp.md#13-review-en-openstaande-punten>).

---

## Literatuurlijst

1. Hogeschool Rotterdam, "Graduation Project INFAFS04 / INFAFS25 - Course Manual," 2025-2026. Beschikbaar: [Documenten/](<Documenten/2025 - 2026 INFAFS04_INFAFS25 Course Manual.pdf>)
2. R.M. van Hoek, "Opstellen van een ReadMe-document voor afstudeerders," versie 0.3, 8 juli 2025. Beschikbaar: [Documenten/](<Documenten/Inhoud_van_werkinstructie_voor_readme_file.pdf>)
3. DNN Community, "Requirements - DNN Docs." Beschikbaar: https://docs.dnncommunity.org/content/getting-started/setup/requirements/
4. 2sxc, "2sxc and EAV Docs." Beschikbaar: https://docs.2sxc.org/
5. OWASP Foundation, "OWASP Top 10:2025." Beschikbaar: https://top10.owasp.org/2025/

De bronnen van de adviesrapporten staan in de bijlage *Verantwoording* van elk rapport.

---

## Bewijsmateriaal

### B1 Afstudeervoorstel

Het goedgekeurde afstudeervoorstel met probleemstelling, hoofdvraag en deelvragen, scope, planning en competentiematrix.

- [Afstudeervoorstel (pdf)](<Administratie/Afstudeervoorstel_1023891.pdf>)

### B2 Logboek

Logboek per werkdag met wat ik deed, waarom, en een link naar het bewijs van die dag.

- [Logboek](<Competencies/01. Professional Skills & Manage and Control/logboek.md>)

### B3 Kanbanbord in Basecamp

De Card Table in het Basecamp-project "MO: 'Tutti' - Planning & Administration App".

![Card Table in Basecamp met 28 kaarten in de kolom Backlogs](<Competencies/01. Professional Skills & Manage and Control/kaban_board_v1.png>)

*Figuur 1. Eerste versie van het kanbanbord: alle 28 functionaliteiten staan nog in de backlog.*

![Card Table in Basecamp met kaarten verdeeld over Triage, Backlogs, To Do, In progress en Testing](<Competencies/01. Professional Skills & Manage and Control/kaban_board_v2.png>)

*Figuur 2. Het kanbanbord nadat de taken zijn verdeeld over de kolommen Triage (14), Backlogs (27), To Do (11), In progress (3) en Testing (9).*

### B4 Versiebeheer en build-straat

De broncode staat in de repository `Bond-for-web-solutions/Dnn.Modules.Tutti` op GitHub. Elke wijziging wordt met GitHub Actions gebouwd, getest en verpakt. De commits per dag staan in het [logboek](<Competencies/01. Professional Skills & Manage and Control/logboek.md>).

[AANVULLEN: schermafbeelding van de commitgeschiedenis en van een geslaagde GitHub Actions-run, omdat de repository mogelijk niet openbaar is.]

### B5 Tutti & OPAS: het complete dossier

Alles uit Basecamp over Tutti en OPAS, samengesteld en geordend op 9 september 2026. Beschrijft werk van anderen; mijn bijdrage is de compilatie en analyse.

- [Tutti & OPAS: het complete dossier](<Competencies/02. Analysis/Tutti_OPAS_dossier.md>)

### B6 Analyse Agenda Viewer

Analyse van de bestaande Agenda Viewer: hoe hij werkt, wat er ontbreekt en het advies om door te gaan of opnieuw te beginnen (14 september 2026).

- [Agenda Viewer (Metropole Orkest)](<Competencies/02. Analysis/Agenda_Viewer.md>)

### B7 Technische SRS - Tutti

Technische requirements, architectuur, datamodel, API, integraties, beveiliging, teststrategie, migratie en open beslissingen (versie 0.1, 18 september 2026).

- [Technische SRS - Tutti](<Competencies/02. Analysis/Technische_SRS_Tutti.md>)

### B8 Architectuurontwerp - Tutti

Waar Tutti draait, uit welke delen het bestaat, de ontwerpkeuzes met redenen, beveiliging en de afwijkingen van de Technische SRS.

- [Architectuurontwerp - Tutti](<Competencies/03. Design/Architectuurontwerp.md>)

### B9 Databaseontwerp - Tutti

De tabellen van Tutti, hoe ze samenhangen met DNN en OPAS, en de regels die de database bewaakt.

- [Databaseontwerp - Tutti](<Competencies/03. Design/Databaseontwerp.md>)

### B10 Backendontwerp - Tutti

De controllers, de API, de services, de rollen en de `PermissionMatrix`.

- [Backendontwerp - Tutti](<Competencies/03. Design/Backendontwerp.md>)

### B11 Gegevensstromen - Tutti

Het overzicht en vier sequentiediagrammen van de belangrijkste gegevensstromen.

- [Gegevensstromen - Tutti](<Competencies/03. Design/Gegevensstromen.md>)

### B12 Broncode Tutti-module

[AANVULLEN: de broncode staat nog in de aparte repository `Dnn.Modules.Tutti`; de map [Broncode](<Broncode/>) is nog leeg. Kopieer de code of een export naar de Graduation Folder, of verwijs met een link en schermafbeeldingen.]

### B13 Automatische tests

Ongeveer 570 unit- en integratietests in de repository `Dnn.Modules.Tutti`.

[AANVULLEN: schermafbeelding of export van een testrun met het aantal geslaagde tests.]

### B14 Adviesrapport Eigen DNN-module of 2sxc

- [Eigen DNN-module of 2sxc](<Competencies/05. Advice/Eigen_DNN-module_of_2sxc.md>) (14 september 2026)

### B15 Adviesrapport Frameworks of CMS

- [Frameworks of CMS](<Competencies/05. Advice/Frameworks_of_CMS.md>) (23 september 2026)

### B16 Testrapport

[AANVULLEN: nog te maken in `Competencies/04. Realisation/`.]

### B17 Halfway presentation

[AANVULLEN: slides en feedback van de halfway presentation in [Presentaties](<Presentaties/>).]

### B18 Procesverslag en reflectie

[AANVULLEN: nog te maken in `Competencies/01. Professional Skills & Manage and Control/`.]

### B19 Beoordeling company supervisor

[AANVULLEN: ingevuld beoordelingsformulier van de company supervisor in `Administratie/`.]
