# Tutti & OPAS: het complete dossier

*Metropole Orkest · bond for web solutions*

Alles wat er in Basecamp staat over de Tutti planning- & administratie-app en over OPAS, het systeem dat hij moet gaan vervangen - van de eerste functiescan tot de agenda-app die inmiddels live op Metrostation draait.

- **Samengesteld:** 9 september 2026
- **Bronnen:** Basecamp 3197668 · projecten 42262548 & 32164328
- **Periode:** mei 2025 – september 2026

---

## Inhoud

- [Wat is Tutti](#01-wat-is-de-tutti-app)
- [Aanleiding](#02-aanleiding-de-waarheid-is-versnipperd)
- [Oplossingsrichting](#03-oplossingsrichting)
- [Route & fasering](#04-route-fasering)
- [OPAS in detail](#05-opas-in-detail)
- [Functiescan](#06-functiescan-welke-modules-heeft-een-orkest-app-nodig)
- [User stories](#07-user-stories)
- [Formulieren](#08-formulieren-en-lijstweergaves)
- [Architectuur](#09-architectuur-techniek-en-team)
- [Agenda-app releaselog](#10-wat-er-al-gebouwd-is-de-agenda-app)
- [Designs](#11-designs-en-wireframes)
- [Koppelingen](#12-koppelingen-en-omliggende-systemen)
- [Open punten](#13-open-punten-en-beslissingen-die-nog-moeten-vallen)
- [Betrokkenen](#14-betrokkenen)
- [Bronnen](#15-waar-het-in-basecamp-staat)

---

## 01 - Wat is de Tutti App

Tutti is de werknaam voor een nieuwe planning- en administratie-app voor het Metropole Orkest, bedoeld als opvolger van de versnipperde combinatie OPAS + Excel + AFAS + SharePoint. De naam is muzikaal: *tutti* is de aanwijzing dat het hele orkest speelt.

- **Basecamp-project:** MO: ‘Tutti’ – Planning & Administration App (id 42262548)
- **Opdrachtgever:** Metropole Orkest - Caspar Abbenhuis, Hildelies (MT), Lotte Murrath (planning)
- **Uitvoerder:** bond for web solutions - Martijn Verbeek (initiatief/shaping), Angelique Smit (stage, bouw), Yahya Yildiz (stage, SharePoint/OPAS-export)
- **Status:** Validatiefase. Proof of concept in Adobe XD; agenda-module al gebouwd en getest binnen Metrostation.
- **Gestart:** 12 mei 2025 (eerste functielijst + user stories); memo richting MT op 28 mei 2026
- **Verwant systeem:** **Metrostation** - het bestaande DNN-intranet van het MO, waar de agenda-app in draait

### Het concept in één zin

Het Metropole Orkest werkt met productienummers (bijv. 2603M1). Het idee van Tutti is dat je op een productienummer klikt en binnen dat ene scherm kunt schakelen tussen **Agenda**, **Berichten**, **Documenten** en **Muziek** - alle data rond één productie op één plek.

Diezelfde gedachte keert in september 2026 terug in Martijns pitch naar aanleiding van de Metrostation-enquête: maak van elke productie een eigen “communityruimte” met automatisch nieuws, agenda, documenten, wijzigingen, teamleden en formulieren. Het doel dat hij daar formuleert: *“Iedere medewerker weet binnen 30 seconden wat er voor zijn of haar productie vandaag relevant is.”*

## 02 - Aanleiding: de waarheid is versnipperd

Op 28 mei 2026 schreef Martijn Verbeek een memo aan Caspar en Hildelies na een “voeten op tafel”-gesprek over het opnieuw inrichten van het plannings- en administratieproces.

#### Observaties

- Planning is de kern van de hele organisatie.
- Dienstenplanning gebeurt voor een groot deel in **OPAS**. (Caspar corrigeerde de eerste versie van de memo, waar ‘Excel’ stond, op 3 juni 2026.)
- Contractbeheer is verdeeld over meerdere systemen.
- Rapportages zijn belangrijk, maar worden vooral via Excel gemaakt.
- HR-gegevens, verloning en urenregistratie lopen via **AFAS**.
- Informatie staat op meerdere plekken: OPAS, Excel, SharePoint, AFAS, Metrostation.

#### Gevolgen

- Dubbel werk en dubbele licentiekosten
- Kans op fouten
- Afhankelijkheid van mensen in plaats van systeem
- Weinig eenduidig inzicht

## 03 - Oplossingsrichting

Het gedeelde beeld uit het gesprek:

- **Eén centrale planning als basis.** Daaruit volgen: toewijzing van musici, contracten (automatisch afgeleid) en input voor uren en verloning.
- **AFAS blijft leidend** voor HR-gegevens, salarisverwerking en urenverantwoording.
- **SharePoint** als bestandssysteem; Outlook en SharePoint (waar al voor betaald wordt) slimmer inzetten.
- Eventueel Dropbox, als dat beheersbaar en simpel te koppelen is.

#### Kernprincipe

**De planning wordt leidend voor de operatie, AFAS blijft leidend voor HR en verloning.** Koppelingen met Outlook, SharePoint en AFAS zijn ondersteunend, niet leidend.

#### Aandachtspunten bij de uitwerking

- Niet het huidige systeem kopiëren, maar opnieuw ontwerpen.
- Duidelijke keuzes maken in wat wel en niet meegaat.
- Extern laten toetsen of de aanpak slim is; simpel houden.
- AFAS positioneren als HR- en verloningssysteem, niet als planningssysteem.
- Voorkomen dat Excel een “parallel systeem” blijft.
- Zorgen voor goede aansluiting tussen planning en urenregistratie.
- Kernteam is bekwaam; team actief betrekken voor draagvlak.

## 04 - Route & fasering

#### Fase 0 - Validatie

- Inventariseren wat echt nodig is (gebruiksscan) → eerste opzet van schema door Martijn
- Verschillen en knelpunten bespreken
- Topfunctionaliteiten bepalen: wat is écht nodig, wat is cruciaal
- Duidelijk maken waar nu de “waarheid” ligt (OPAS, Excel, AFAS)

#### Fase 1 - MVP - een demo om het zichtbaar te maken

- Basisplanning (projecten, activiteiten, kalender)
- Dienstenplanning (toewijzing musici)
- Basis contractgeneratie
- Eenvoudige rapportage
- Eerste aansluiting op AFAS - “bond is goed in koppelingen”

#### Fase 2+ - Integraties en opschonen

- Integraties met Outlook, SharePoint, AFAS
- Betere afstemming tussen planning en uren/verloning
- Minder handmatige overdracht tussen systemen
- Uiteindelijk opschonen van Excel en oude werkwijzen

#### Eerstvolgende stap (per memo)

- Gebruiksscan laten invullen door betrokkenen
- Gezamenlijke sessie om uitkomsten te bespreken
- Vaststellen: wat moet in versie 1, wat kan later, wat stopt, en hoe planning en AFAS op elkaar aansluiten
- Resultaat: heldere scope voor v1 plus ruimte voor een gedegen beslis- en investeringsdocument

## 05 - OPAS in detail

*OPAS is het orkestplanningssysteem waar het MO nu mee werkt (mo.opas-online.com, beheer via Dörte). Het levert de dienstenplanning en voedt via een export/koppeling het intranet Metrostation. Tutti moet er op termijn de plek van innemen.*

### Hoe OPAS nu aan Metrostation hangt

- Een **scheduled task** leest periodiek een OPAS-bestand in en verwerkt wijzigingen in het intranet. (Bij zusterorkesten is hier monitoring op gezet: geen nieuw bestand binnen 48 uur → waarschuwingsmail.)
- Data moet in OPAS-beheer eerst worden **vrijgegeven** voordat het op het intranet verschijnt; het streven is ca. 1,5 jaar vooruit vrijgeven.
- Uit de OPAS-export komen o.a. productienummer, titel, dirigent, begin-/eindtijd, locatie en eventtype. De agenda-app bepaalt het type afspraak op EventText, niet op EventProjectTypeName - dat was een expliciete wens van het MO.

### Bekende OPAS-knelpunten in Basecamp

| Onderwerp | Kern | Status |
| --- | --- | --- |
| Synchronisatie annuleringen & tijden | Geannuleerde voorstellingen bleven op Metrostation staan (bijv. “Music Unites II”, 19 april); tijden weken af tussen OPAS en intranet (tijdzone). Wens: annuleringen automatisch markeren/verwijderen, sync-logging, duidelijke eigenaar van de controle. | **afgerond 21-10-2025** |
| Geannuleerde events zichtbaar maken | Geannuleerde events worden nu niet getoond, maar voor musici is niet zichtbaar dát iets is geannuleerd. Suggestie: statuswijziging in de tijd tonen (“14-4 status gewijzigd van actief naar gecanceld”). | **aandachtspunt** |
| Lege afspraken in de database | Records met alleen begin-/eindtijd, zonder naam of type. Worden nu uitgefilterd in de agenda-app. | **opgelost** |
| Afspraken zonder projectnaam | Werden ingeladen als undefined. Nu uitgefilterd. | **opgelost** |
| Samenhang tussen afspraken | OPAS legt niet vast welke afspraken bij elkaar horen, behalve via de productie. De kleurlogica is daarom afgeleid uit “vorige/volgende” afspraak. | **structurele beperking** |
| OPAS-export & extra metadata | Kan de export de zichtbaarheid van producties sturen (bestanden rond een repetitie verbergen, handmatige aan/uit-schakelaar voor Lotte)? Is metadata als ‘Componist’ te extraheren? | **afgerond 16-06-2026** |
| SSO-inlog op OPAS | Onderzoek naar inloggen op OPAS via SSO/M365, hergebruik van OPAS-gebruikersgegevens op het intranet, rolgebaseerde users, koppeling per productie. | **onderzoek** |
| Remplaçanten | Kan een remplaçant die in OPAS is aangemaakt automatisch toegang tot het intranet krijgen? Vragen over scope en duur van de toegang. | **onderzoek** |
| Knippen/plakken uit OPAS | Tabellen uit OPAS verliezen hun opmaak bij kopiëren naar het intranet. | **issue** |
| Menu-link naar OPAS | Menu-item ‘opas’ linkte naar het icoon in plaats van naar OPAS Online. | **opgelost** |

### De gebruiksscan

Het document **OPAS Gebruiksscan + Ontwikkelroute Nieuwe Planning App.pdf** (bond, 28 mei 2026) bevat een ingevulde gebruikstabel per functionaliteit met kolommen *Gebruik · Cruciaal · Alleen OPAS · Opmerking*. Uit deel 1, blok 1 en 2:

| Functionaliteit | Gebruik | Cruciaal | Alleen OPAS | Opmerking |
| --- | --- | --- | --- | --- |
| Kalender & evenementen | **✓** | **★** | **⚠** | Kern van alle planning |
| Productiebeheer (projecten) | **✓** | **★** | **⚠** | Structuur van alles |
| Activiteitenbeheer | **✓** | **★** | **⚠** | Repetities / concerten |
| Data-overzicht | **✓** | **★** | **⚠** | Belangrijk voor productie |
| Zoeken & filters | **⚠** |  |  | Kan beter benut |
| Dashboard | **✗** |  |  | Nauwelijks gebruikt |
| Rapportages | **✓** | **★** | **⚠** | Essentieel maar export-based |
| Data & analyse | **⚠** |  |  | Vaak Excel |

De volledige PDF is niet uitgelezen - alleen pagina 1 is als preview zichtbaar in Basecamp.

## 06 - Functiescan: welke modules heeft een orkest-app nodig

Op 12 mei 2025 zette Martijn een lijst van veertien high-level modules neer, met per module de belangrijkste functionele kenmerken en een controlevraag aan de planning. De vraag aan Angelique en Caspar: *welke gebruiken jullie nu, welke zijn cruciaal voor het primaire proces, en welke kunnen alleen in OPAS?*

| Module | Kern | Vraag / uitkomst |
| --- | --- | --- |
| **Kalender & evenementen** | Dag-, week-, maand- en lijstweergave; planning voor projecten én losse activiteiten; geavanceerd zoeken | Kern van alles - is als eerste gebouwd in de agenda-app |
| **Dashboard** | Centraal overzicht van projecten, taken, notities, berichten en KPI’s; snelkoppelingen op projectcode | Nauwelijks gebruikt in OPAS |
| **Contractbeheer** | Contractcyclus voor musici, remplaçanten, dirigenten, solisten, technici; contract- en reisdocumenten, bulk-printen | “Of staan dit soort zaken gewoon in SharePoint? Denk aan 2565 - {projectnaam} \ reisdocumenten” |
| **Muziekbeheer** | Repertoire met metadata, uitvoeringshistorie per werk, fysieke partituurcollectie | **Niet nodig.** Het MO bewaart muziek extern; OPAS heeft de functie maar die wordt niet gebruikt. Muziekbeheer loopt via SharePoint. |
| **Rapportages** | Standaardrapporten in PDF/Word/HTML/Excel, custom templates, directe export | Essentieel, maar nu export-gedreven |
| **Data & analyse** | Log van uitvoeringen gekoppeld aan personeelsdata; trends in personeelsinzet en repertoire | “Is dit hetzelfde als uren, dagdelen per muzikant per productie?” |
| **Tourmanagement** | Reis- en hotelgroepen, visadocumenten, ATA-formulieren, logistieke reisschema’s | - |
| **Personeelsbeheer** | Drag-&-drop muzikantentoewijzing in een grid, aanwezigheid per groep/type, open posities, salarisstroken volgens configureerbare regels | Raakvlak met AFAS |
| **Adresboek / “Tutti CRM”** | Contacten per rol (venues, solisten, orkestleden), mailings, communicatiehistorie, whereabouts, laatste contact | Expliciet als “Tutti CRM” benoemd |
| **Instrumenteninventaris** | Instrumenten van orkest, musici of derden; reparatiehistorie, verzekering, waardering, koppeling aan transportkisten | “Heeft a, b, c z’n reserve mondstuk bij zich?” |
| **Werkinformatie** | Metadata rond projecten, repertoire, uitvoeringen; potloodagenda; status van een event; custom velden en tags | “Wat houden we allemaal bij?” |
| **Tourkoffers** | Packlists per tourcase, barcodelabels, controlerapporten vóór vertrek | “Of is dit niet im Frage?” |
| **Middelen / resources** | Zalen, meubels, apparatuur; toewijzing en kostentracking; opname in productierapporten | - |
| **Data (import/export)** | Herbruikbare import/export-scripts, API-synchronisatie, logging en foutafhandeling | Denk aan koppelingen met HRM, salaris, SharePoint, urenregistratie |
| **Performance** | Complete log van uitvoeringen gekoppeld aan werk en musicus | “Welk project hielden we wat aan over? Welke sectie presteert het beste? Hoe verantwoorden we onze inzet als groep?” |

#### Wat de functiescan opleverde

Op 14 april 2026 zaten Angelique Smit en Else de Schiffart samen om OPAS in te zien, vooral vanaf de productiekant; daar kwam per functie aan bod wat wel en niet gebruikt wordt. Dat is vastgelegd in de bezoekverslagen van 9 en 14 april (Docs & Files → Bedrijfsbezoek). De duidelijkste conclusie: **muziekbeheer hoeft niet in de app**, dat loopt via SharePoint.

## 07 - User stories

Twee sets, geschreven op 12 mei 2025, als startpunt voor het programma van eisen. Ze zijn per epic geordend.

### Muzikant - 18 stories

**Persoonlijk rooster & notificaties**

- Persoonlijke week- en dagplanning inzien
- Push- of e-mailnotificatie bij nieuwe of gewijzigde afspraken
- Koppeling naar eigen agenda (Outlook/Google)

**Bladmuziek & repertoire**

- Partituur en stemmen per productie online bekijken/downloaden
- Annotaties bij eigen partijen maken
- Revisies van bladmuziek per e-mail of in de portal zien

**Beschikbaarheid & verlof**

- Beschikbaarheid doorgeven (aanwezig, ziek, verlof)
- Verlofaanvraag via formulier indienen
- Status van verlof- of vervangingsaanvraag volgen

**Communicatie & documenten**

- Berichten en nieuws in één centrale inbox
- Contracten, reisdocumenten en declaratieformulieren inzien
- Ontvangstbevestiging sturen bij gelezen documenten

**Touring & logistiek**

- Reis- en hotelgegevens inzien (vluchten, hotels, busritten)
- Tour-contactpersoon snel vinden en bereiken

**Profiel, data, toegang**

- Eigen gegevens inzien en bijwerken (contact, bank, verzekering)
- Instrumenteninventaris en toegewezen instrumenten bekijken
- Inloggen met M365-account (SSO)
- Alleen toegang tot producties en documenten met rechten

### Planner - 15 stories

**Evenement- & projectplanning**

- Nieuw project/evenement aanmaken met datum, locatie, type
- Bestaand evenement kopiëren inclusief sub-activiteiten
- Wisselen tussen dag-, week- en maandweergave
- Conflicten automatisch markeren (dubbele zaal of dubbele inzet)

**Muzikanten- & personeelsroostering**

- Musici per activiteit toewijzen via drag-&-drop
- Openstaande posities (vacatures) zien
- Aanwezigheid bijhouden (aanwezig, ziek, verlof)
- Salarisstroken genereren op basis van uren en functieregelingen

**Repetitie- & resourceplanning**

- Repetities plannen met gekoppelde ruimtereservering
- Instrumenten en middelen reserveren per activiteit (vleugel, stoelen, standaards)
- Groeps- en reisroosters voor tournees genereren

**Integratie, rapportage, toegang**

- Planning exporteren naar Outlook/Google Calendar
- Standaardrapporten draaien (planningsoverzicht, personeelsinzet, kostenbegroting)
- Historische planningsgegevens per productie inzien
- Inloggen via M365 (SSO) met rol- en rechtenafbakening

## 08 - Formulieren en lijstweergaves

Afgeleid uit de user stories: zes formulieren en zeven lijstweergaves voor de planner.

### Formulieren

| Formulier | Doel | Belangrijkste velden |
| --- | --- | --- |
| Event Details | Nieuw event aanmaken of klaarmaken voor publicatie | Naam, Project (ID), Seizoen, datum, start/eind, Locatie (VenueID), Ensemble, Dirigent, EventType, teaser + beschrijving, status (Draft/Review/Published) |
| Musici-toewijzing | Rollen en functies aan het event koppelen | EventID (autofill), PersonID, rolgroep (solist, productieleider, orkestlid), functie (trompettist, altviool), aanwezigheidsstatus, notities |
| Repetitieplanning | Oefensessies en logistiek inplannen | EventID, datum/tijd rehearsalblok, locatie, beschikbare instrumenten/resources, bindende resource-toewijzingen |
| Documenten & Bladmuziek Upload | Sheet music en productiedocumenten koppelen | EventID, bestand of SharePoint-link, versienummer/revisiedatum, toegangsrechten |
| Workflow-/Publicatiecontrole | Review en publiceren naar intranet via SSO/RBAC | EventID, goedkeuringsstatus, reviewer, feedback, publicatiedatum |
| Notificatie- & Agenda-instellingen | Meldingen en agenda-export instellen | EventID, type notificatie, ontvangers via Azure AD-claims, export-opties (Outlook, Google Calendar) |

### Lijstweergaves

- **Events overzicht** - datum, tijd, naam, locatie, status, aantal toewijzingen; filters op seizoen/project/status; acties bewerken, kopiëren, publiceren
- **Musici-rosters per event** - met directe statuswijziging (ziek → vervanger zoeken)
- **Repetitie- & resourceplanning** - resources toewijzen of verplaatsen
- **Documenten- & bladmuziekbibliotheek** - versie, laatste wijziging, toegangsrechten
- **Openstaande vacatures** - rolgroep, functie, aantal posities, geïnteresseerden
- **Publicatie- & reviewstatus**
- **Notificatielog & agenda-exports**

## 09 - Architectuur, techniek en team

### Platformanalyse

| Component | Rol | Benodigd | Te onderzoeken |
| --- | --- | --- | --- |
| **Plant-an-App** | Low-code op DNN-basis voor databound formulieren, workflows en mobiele views | Licentie, dev-omgeving (Visual Studio + PaA-extensie), SQL Server | Ondersteunde datatypes en relaties; workflow-engine (approval, trigger-events); mobiele/responsieve lay-outs |
| **2sxc** | Open-source content- en app-module voor DNN, geschikt voor JSON-gebaseerde data-apps | 2sxc-installatie in DNN, kennis van C# Razor of JS front-end | Custom content-types en API-endpoints; data-binding en inline editing; O365-connectors of externe API’s |
| **DNN CMS** | Hostingplatform voor beide | DNN 9.x, IIS/Windows-server, SQL Server | Authenticatie/autorisatie (AD, OAuth, SSO); tenant-beheer bij multi-organisatie; uitrol en versiebeheer van modules |
| **Integratiepunten** | - | - | Hoe wisselen Plant-an-App en 2sxc data uit - REST-API of shared DB-schema? SSO en rol-/rechtenbeheer centraal in DNN. |

### Technische inrichting

- **Ontwikkelomgeving:** Windows-VM of lokale machine met IIS, SQL Server, Visual Studio en NodeJS; installatie van DNN 9.x, de 2sxc-module en Plant-an-App-extensies.
- **Database:** centrale OPAS-achtige datamodule met tabellen voor evenementen, repertoire, personeelsdata.
- **UI-laag:** Plant-an-App voor formulieren en workflows; 2sxc voor content-rijke portals en documentbeheer.
- **Security & beheer:** DNN-rollen per gebruikersgroep; logging en auditing (wie deed wat, wanneer).

### ER-schema

Martijn legde op 12 mei 2025 een eerste ER-schema vast: **Event** staat centraal en is verbonden met *Venue, Person* (dirigent), *Ensemble, EventType, Season* en *Project*. **EventPersonAssignment** koppelt Event aan Person voor alle overige rollen (solisten, productieleiders, etc.). Bijbehorende bestanden: datamodel_opas_minimaal.docx en opas_export_intranet_bond.xml (5,86 MB).

### Proof of Concept

Voorgestelde aanpak: kies 2–3 kernscenario’s (bijv. “Evenement aanmaken + planner uitnodigen” of “Muzikant rooster genereren en exporteren”), bouw in Plant-an-App een formulier + workflow + notificaties, bouw in 2sxc een content-type voor repertoire met front-end weergave, test de integratie (logins, datadoorvoer, UX-consistentie) en rapporteer wat snel lukte, waar de beperkingen zitten en wat prestaties en onderhoud betekenen.

### Team en tijdpad (indicatief)

**Team:** 1 DNN-architect/DevOps (installatie, security) · 1 Plant-an-App developer (low-code workflows) · 1 2sxc/C#- of JS-developer (custom apps) · 1 functioneel analist (eiseninventarisatie + tests).

**Tijdpad:** functioneel onderzoek & eisen 2–3 weken → omgevingssetup & training 1–2 weken → PoC-ontwikkeling & test 3–4 weken → evaluatie & besluitvorming 1–2 weken.

**Tools:** Basecamp voor tickets en backlog; SharePoint en Basecamp voor documentatie; testgereedschappen.

## 10 - Wat er al gebouwd is: de agenda-app

*Terwijl de grote Tutti-scope nog in validatie zit, is het eerste stuk al gebouwd en getest: een nieuwe agenda binnen Metrostation. Dit is de stage-opdracht van Angelique Smit, met 36 user stories in de lijst “Angelique stage”, waarvan 32 afgerond.*

### Naamgeving van de stories

V (versie implementatie) | #(nummer userstory) | (titel) - bijvoorbeeld V 0.1 | #1 | Maandoverzicht: eerste build, eerste userstory.

**Lingo** (bewust vastgelegd “zodat de volgende stagiair begrijpt waar ik het over heb”): *Afspraak* = evenement in de agenda - repetitie, concert, soundcheck, vervoer of overig. *MO* = Metropole Orkest. *Gebruiker* = verzamelnaam voor de doelgroepen van de webapplicatie, bijvoorbeeld orkestspelers en beheersleden.

### Kleurcodering van afspraken

Elk afspraaktype heeft een eigen kleur, minimaal WCAG 2 AA en zoveel mogelijk APCA-compliant. Concert, repetitie en soundcheck gelden als hoofdcategorieën:

- Concert
- Repetitie
- Soundcheck
- Vervoer
- Opname
- Educatie

De groeperingsregel: staat een vervoers- of soundcheckafspraak direct vóór of ná een concert, repetitie of opname, dan krijgt die de kleur van dat hoofdevenement - zo zie je in één oogopslag wat bij elkaar hoort. Staan er twee of meer soundchecks achter elkaar, dan behouden die hun eigen kleur.

### De vier weergaves (V 0.1)

| Weergave | Opzet | Apparaten |
| --- | --- | --- |
| **Maand** | Grid; dagen buiten de maand donkerder ingekleurd; per afspraak begin-/eindtijd, productienummer, (ingekorte) titel en soort afspraak | Tablet en desktop - niet mobiel |
| **Week** | Oorspronkelijk verdeeld in ochtend (0:00–12:00), middag (12:00–17:00) en avond (17:00–0:00); swipen links-rechts. *In V 0.1.04 weer verwijderd* - het leek alsof er drie verschillende weken stonden. | Mobiel en tablet |
| **Dag** | Per uur; afspraken die niet op een heel uur beginnen worden bij het vorige uur getoond (13:30 onder 13:00). Meer dan twee events naast elkaar, onder elkaar op kleine schermen. Outlook-achtig, op verzoek van het MO. | Mobiel en tablet |
| **Lijst** | Alles onder elkaar, “niet te overstimulerend op visueel gebied”; op mobiel per dag gescheiden | Alle |

### Detail- en productiepagina

- **Detailpagina van een afspraak:** titel, tijd, soort afspraak en extra informatie zoals kledingvoorschriften, bezetting en notities vanuit het MO; plus documenten en bladmuziek van het productienummer. Bladmuziek wordt gefilterd op het instrument van de speler.
- **Productiedetailpagina:** alle afspraken binnen een productie op datumvolgorde, gekleurd volgens de groeperingsregel, plus de wijzigingshistorie van die productie. Werkt tot 300px schermbreedte (geverifieerd via BrowserStack).

### Releaselog

| Versie | Story | Aanleiding / inhoud |
| --- | --- | --- |
| V 0.1 #1–#6 | Maand-, week-, dag- en lijstweergave; detailoverzicht; productiedetailpagina | Eerste build, afgerond 20 april 2026 |
| V 0.1.01 #1 | Kleuren afspraken aanpassen | Bedrijfsbezoek 9 april: veel verschillende kleuren werken afleidend; bij elkaar horende afspraken dezelfde kleur |
| V 0.1.01 #2 | Mobiele week/maandweergave | Niet horizontaal scrollen, liever tekst inkorten |
| V 0.1.01 #3 | Dagweergave | “Meer op een Outlook-dagweergave laten lijken” |
| V 0.1.01 #4 | Bugfix: wrapping op macOS | Events wrapten vroegtijdig in maand- en weekweergave |
| V 0.1.01 #5 | Bugfix: lege afspraken weghalen | Records in de database met alleen tijden |
| V 0.1.01 #6 | Type afspraak & afkorting | Type baseren op EventText i.p.v. EventProjectTypeName; vergelijkbare types (bus heen / retour) dezelfde afkorting |
| V 0.1.01 #7 | Laatste wijzigingen | Wijzigingshistorie op detail- en productiepagina, gesorteerd op wijzigingsdatum |
| V 0.1.01 #8 | Bugfix: foutafhandeling | Bleef “aan het laden…” tonen; nu een nette foutmelding |
| V 0.1.01 #9 | Bugfix: overgeslagen maand | 31 maart + 1 maand sprong naar mei; nu naar de laatste geldige datum van de maand |
| V 0.1.01 #10 | Terugknop | Terug naar de agenda vanaf het detailoverzicht |
| V 0.1.02 #1 | Scrollende detailweergave | Voorstel; eerst voorleggen aan Lotte |
| V 0.1.02 #2 | Export/download agenda | Fall-back als inloggen niet lukt: download per dag, week, maand of productie als PDF, met of zonder details |
| V 0.1.02 #3 | Google Lighthouse | Scoorde op 1 van de 4 categorieën boven de 80; punten vanuit de webapplicatie wegwerken (prestatie, toegankelijkheid, best practices, SEO) |
| V 0.1.03 #2 | Bugfix: terugknop met datum | Huidige datum als parameter meesturen naar currentDate |
| V 0.1.03 #3 | Bugfix: verkeerde tijden | Tijden stonden +1 of +2 uur in de toekomst |
| V 0.1.03 #4 | Keyboard controls | Zwarte border op het geselecteerde element |
| V 0.1.03 #5 | Pagina voor indexeren nieuwe zoekmachine | - |
| V 0.1.03 #6 | Laatste wijzigingen doorlinken | Module op de hoofdpagina moest naar de nieuwe agenda linken, met werkende parameters |
| V 0.1.04 #2 | Dagoverzicht start om 9:00 | Bij het laden naar 9:00 scrollen; de meeste afspraken beginnen pas dan, en reisafspraken rond middernacht verwarden |
| V 0.1.04 #3 | Lay-out weekweergave | Ochtend/middag/avond weer weggehaald; alle afspraken in één vlak per dag |
| V 0.1.04 #4 | Uitlichten concerten en repetities | In de maandweergave staan reizen vaak vóór concerten. Vijf ontwerpvoorstellen met voor- en nadelen voorgelegd aan Lotte; Angeliques eigen voorkeur was design 2 (grijze streepjes-accordion). |
| V 0.1.04 #5 | Bugfix: afspraken zonder projectnaam | Werden als undefined ingeladen |
| V 0.1.05 #1 | Beheeroverzicht | Beheerpagina voor het inplannen van afspraken bij een productie (tabel met datum, tijd, soort, extra info, aanpasknop) en een aparte pagina om producties aan te maken en te beheren met startdatum, einddatum, naam, productienummer, ID en een ‘actief’-veld |
| V 0.1.05 #1 | Stijling detail- en productiedetailpagina | Zes wireframes voorgelegd (uitklapbare sidebar vs. menubalk boven, meer witruimte, herplaatste downloadknop); keuze lag bij Lotte |
| V 0.1.05 #2 | Documenten inladen via ADAM | Bestanden bij de productie inladen vanuit de site zelf; de manier van inladen instelbaar in de app-instellingen |
| V 0.1.05 #4 | Updaten en verwijderen ADAM-bestanden | Titel aanpassen zonder kopie; verwijderknop verwijdert bestand en titel; bij hergebruik alleen titel en content-type |
| V 0.1.06 #1 | Documenten via de SharePoint viewer API | Openstaand. Bestanden rond de productie via de bestaande SharePoint-API ophalen, ingebouwd in de webapplicatie in plaats van via een aparte app |
| V 0.2.01 #2 | Custom Settings naar 2sxc-data | Gearchiveerd onderzoek: app-instellingen aanpasbaar maken vanuit de 2sxc-UI zodat de broncode niet aangepast hoeft te worden |
| Archief | Enquêtefunctie | Standaardenquête per productie, in te vullen van 24 uur tot 3 maanden na afloop, zichtbaar op detail- en hoofdpagina; beheerpagina met antwoorden geordend op productienummer en per persoon. In de wacht voor andere taken binnen het MO. |

### Parallel spoor: maandweergave op Metrostation

In het Metropole Orkest-project loopt een eigen lijst “Maandweergave” (gestart 27 november 2025 door Sally Alsayed), met shaping-vragen die vooraf beantwoord moesten worden:

- **Ontwerp:** wordt de basis een Outlook-achtige maandkalender? Blokjes per afspraak of één knop per dag die de dagplanning opent? Referentie: FullCalendar-demo’s.
- **Event-weergave:** wat is direct zichtbaar (tijd, type) en wat pas na klikken? Krijgt elke eventsoort een eigen visualisatie, en wat is de legenda?
- **Data:** eventnaam, tijden, adres, instrumenten, muziekstukken, eventtype, dagdeel en status (bevestigd, concept, geannuleerd). Alles tegelijk ophalen of lazy loading per week/dag? Nieuwe endpoint nodig? Filtering in de API?
- **Navigatie:** wisselen tussen week en maand, vorige/volgende periode, één klik terug naar vandaag, correcte weeknummering inclusief week 53 en jaarwissels.

Lotte Murrath gaf in februari 2026 terug: in het maandoverzicht graag de eventnaam erbij en niet alleen tijden, weeknummers erbij, lichte kleur werkt goed, en zij zou de kleurenlegenda in OPAS navragen.

## 11 - Designs en wireframes

### Initiële designs - Martijn Verbeek, 13 februari 2026

Negen PNG’s in Docs & Files → Tutti App → “Martijn Verbeek initiele designs”. Ze tonen de app ín Metrostation, in een donker thema met de Metropole Orkest-header:

- Tutti App - design b.png - het geheel
- … agenda.png - scherm “Uitgelicht”: een raster van productietegels met titel en productienummer (2603M1, 2604M1, 2605M1…), met een schakelaar maand / week / dag / lijst
- … agenda – project home.png - de productiepagina met de vier tabs **AGENDA · BERICHTEN · DOCUMENTEN · MUZIEK** onder de productietitel
- … project home – Documenten.png en … Berichten.png - de andere tabs
- … MUZIEK - bladmuziek.png en … MUZIEK - audio.png - muziek gesplitst in bladmuziek en audio
- Productie App - Home.png (12 mei 2025) - de allereerste snelle schets: “alle data van een event bij elkaar, klikken op een productienummer, via tabjes alles bij elkaar, mix van data uit de app en handmatig toegevoegd”

### Wireframes - Angelique Smit, 12 maart 2026

- Wireframe Agenda Maand Donkere Afspraken.png
- Wireframe Agenda Maand Licht-Donker Afspraken.png

## 12 - Koppelingen en omliggende systemen

| Systeem | Rol nu | Rol in de nieuwe opzet |
| --- | --- | --- |
| **OPAS** | Dienstenplanning, productienummers, agenda-bron voor Metrostation | Wordt uiteindelijk vervangen door de centrale planning in Tutti |
| **AFAS** | HR-gegevens, salarisverwerking, urenverantwoording | Blijft leidend voor HR en verloning; koppeling met de planning is expliciet een open vraag (“koppeling met AFAS?”) |
| **SharePoint** | Bestandssysteem: reisdocumenten, bladmuziek, per productie in mappen als 2565 - {projectnaam} | Blijft het bestandssysteem; een SharePoint–Metrostation-koppeling is geoffreerd (16 juni 2026) voor het automatisch tonen van audio- en videobestanden met behoud van mappenstructuur en rechten |
| **Metrostation** | Het DNN-intranet van het MO - centrale plek voor planning en informatie | Draagt de agenda-app; op termijn de “digitale productiecommunity” |
| **Outlook / M365** | Agenda en mail; SSO | Ondersteunend: agenda-export en SSO-inlog |
| **Excel** | Rapportages en delen van de planning | Moet geen parallel systeem blijven |
| **Dropbox** | Wordt gebruikt voor muziek; er ligt een vraag of een koppeling met Metrostation mogelijk is | Eventueel, mits beheersbaar en simpel |
| **ADAM** (DNN) | - | Gebruikt in de agenda-app om documenten bij een productie in te laden, te hernoemen en te verwijderen |

Er staat ook een losse to-do “Andere systemen” (18 juni 2025): omschrijf het landschap van overige ICT-systemen - HRM, CRM, Dropbox, SharePoint, Power Automate - en de vraag naar een AFAS-koppeling. Die is nog niet ingevuld.

### Signaal uit de DEVOPS-standaard van bond

## 13 - Open punten en beslissingen die nog moeten vallen

- **Scope v1.** De validatiefase is nog niet afgerond: gebruiksscan invullen, gezamenlijke sessie, vaststellen wat in versie 1 moet, wat later kan en wat stopt.
- **Aansluiting planning ↔ AFAS.** Hoe precies gegevens uitgewisseld worden voor uren en verloning.
- **Contractbeheer:** in de app of gewoon in SharePoint?
- **Data & analyse:** is dat hetzelfde als uren en dagdelen per muzikant per productie?
- **Tourkoffers:** hoort dat wel bij de scope?
- **Design-keuzes bij Lotte:** welk van de zes detailpagina-wireframes en welk van de vijf ontwerpen voor het uitlichten van concerten wordt geïmplementeerd, en of die wens überhaupt doorgevoerd wordt.
- **SharePoint viewer API** (V 0.1.06 #1) staat nog open.
- **Enquêtefunctie** is gearchiveerd in afwachting van andere taken binnen het MO; los daarvan loopt er wel een eigen enquête-app in het Metrostation-project.
- **Geannuleerde events** zijn voor musici niet als geannuleerd zichtbaar.
- **Continuïteit:** Angeliques stage liep af; de naamgeving en lingo zijn expliciet vastgelegd voor de volgende stagiair.

## 14 - Betrokkenen

| Naam | Rol |
| --- | --- |
| Martijn Verbeek | bond - initiatiefnemer, shaping, functiescan, memo’s en offertes |
| Angelique Smit | bond (stage) - bouw van de agenda-app, wireframes, OPAS-functiescan met Else de Schiffart |
| Yahya Yildiz | bond (stage) - SharePoint-koppeling, OPAS-export en metadata |
| Caspar Abbenhuis | Metropole Orkest - opdrachtgever, beoordeelt de functielijst en user stories |
| Lotte Murrath | Metropole Orkest - planning; beslist over agenda- en designkeuzes |
| Hildelies | Metropole Orkest - MT, geadresseerde van de memo |
| Else de Schiffart | Metropole Orkest - OPAS-uitleg (auteur van OPAS uitleg.pdf) |
| Sally Alsayed | bond - shaping maandweergave |
| Marnix Bouwman, Rahul Udhaya, Floor Cloo, Martijn van ’t Hart | bond - overige betrokkenen bij Metrostation |
| Dörte | OPAS-zijde - contact voor de koppeling |

## 15 - Waar het in Basecamp staat

#### Project: MO ‘Tutti’ – Planning & Administration App

- Memo – Verkenning nieuwe planning- en administratie app - *Docs & Files → Wat als we het opnieuw zouden bedenken? · document 9938562479*
- OPAS Gebruiksscan + Ontwikkelroute Nieuwe Planning App.pdf (324 KB) - *zelfde map · upload 9938119872*
- OPAS uitleg.pdf (3,2 MB) - gemaakt door Else de Schiffart - *Docs & Files → OPAS handleiding + fotos*
- Bezoekverslag 9 April & 14 April.pdf - *Docs & Files → Bedrijfsbezoek*
- ER-schema, datamodel_opas_minimaal.docx, opas_export_intranet_bond.xml - *Docs & Files (hoofdmap)*
- Tutti App - 9 initiële designs + 2 wireframes - *Docs & Files → Tutti App*
- To-dolijst “Onderzoek” - 10 items: functiescan, user stories, functioneel onderzoek, platformanalyse, technische inrichting, PoC, planning & skills, formulieren, andere systemen - *To-dos · todolist 8638214191*
- To-dolijst “Angelique stage” - 36 user stories, 32 afgerond - *To-dos · todolist 9692870709*
- Chat - opdracht aan Caspar (12 mei 2025) en vervolgvraag (26 juni 2025) - *Chat room*

#### Project: Metropole Orkest (Metrostation)

- Notities voor gesprek met Martijn en Yahya, 26 februari 2026 - het Tutti-concept - *To-dos · 9638072544*
- Onderzoek OPAS export en haalbaarheid overige metadata - *To-dos · 9692828349*
- OPAS en agenda: wijzigingen en tijd - sync-user story - *To-dos · 8489848107*
- Maandweergave, Maandweergave: DATA inladen, Nieuwe knop (Deze Maand) - *To-dos → lijst Maandweergave*
- SharePoint koppeling - offerte 16 juni 2026 - *Message Board · 10000772402*
- Enkele gedachtes n.a.v. de enquete - pitch 8 september 2026 - *Message Board · 10280364889*
- Bezoekverslag 10-03-2026.pdf - *Docs & Files → Bezoekverslagen*
- Enquete: de 7 vragen - *Docs & Files · document 9858277704*

---

*Samengesteld uit de Basecamp-projecten van het Metropole Orkest. Citaten zijn letterlijk overgenomen uit Basecamp-berichten, to-do’s en documenten; de tabel uit de gebruiksscan komt van pagina 1 van de PDF, de overige pagina’s zijn niet uitgelezen.*
