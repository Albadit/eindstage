# Agenda Viewer (Metropole Orkest)

*Hoe de applicatie werkt, wat er ontbreekt en doorgaan of opnieuw beginnen?*

*Gebaseerd op: het Basecamp-dossier Tutti & OPAS: het complete dossier (9 september 2026), Documentatie.pdf, de scriptie van Angelique Smit en de broncode in de repository (drie geëxporteerde versies, laatste V 0.1.05). Samengesteld 14 september 2026.*

## 1. Kort antwoord

**Doorgaan, maar eerst de fundering repareren voordat er iets nieuws bij komt.**

De Agenda Viewer is een werkende, geteste en door de klant geaccepteerde agenda die al live staat op het intranet van het Metropole Orkest (Metrostation). De front-end (weergaves, kleurlogica, detailpagina's, PDF-export, instellingenbestand) is degelijk en het behouden waard. De back-end die in V 0.1.05 is toegevoegd (beheer-CRUD, eigen databasetabellen, bestandsupload) is onaf en heeft echte problemen: elk schrijf-endpoint staat open voor anonieme gebruikers, en de beheerdata staat in tabellen die de agenda nooit uitleest.

Het is géén goede basis voor de volledige Tutti-visie (dienstenplanning, contracten, AFAS-koppeling). Het is de agenda-module van Tutti, meer niet. Neem eerst de platformbeslissing voor Tutti; blijft het antwoord "DNN + 2sxc" (waar het dossier van uitgaat), behoud dan deze app en bouw erop verder. Verandert het platform, dan zijn alleen de front-endlogica en het instellingenmodel de moeite waard om mee te nemen.

## 2. Waar deze app in het grotere geheel zit

| Onderdeel | Wat het is |
|---|---|
| OPAS | Het orkestplanningssysteem dat het MO nu gebruikt (mo.opas-online.com). Bron van alle afspraken. Moet uiteindelijk vervangen worden door Tutti. |
| Metrostation | Het intranet van het MO, een DNN-site (DotNetNuke) met de 2sxc-module. |
| Agenda Viewer (deze repo) | Een 2sxc-app binnen Metrostation die de OPAS-afspraken als agenda toont. Gebouwd door Angelique Smit als afstudeerproject (feb-jun 2026). |
| Tutti | Werknaam voor de toekomstige planning- en administratie-app die OPAS + Excel + AFAS-lijm + SharePoint-chaos moet vervangen. Nog in validatiefase. De Agenda Viewer is "het eerste stuk dat al gebouwd is". |

Het Tutti-concept: klik op een productienummer (bijv. 2603M1) en schakel binnen één scherm tussen Agenda / Berichten / Documenten / Muziek voor die productie. De Agenda Viewer implementeert het tabblad Agenda plus een eerste versie van Documenten/Muziek (bladmuziek, audio, bijlagen).

## 3. Hoe de applicatie werkt

### 3.1 Techniek

- **Platform:** DNN 9.x + 2sxc (open-source content-/app-module voor DNN). De app is een 2sxc-"app" die vanuit een zip wordt geïnstalleerd (2sxcApp_AgendaViewer_0.1.2.zip).
- **Serverkant:** Razor-views (\*.cshtml) en C# Web API-controllers (api/\*.cs) die binnen DNN draaien. Lezen van data gaat via 2sxc Visual Queries (SQL DataSources, gedefinieerd in App_Data/app.xml van de export), niet via eigen C#.
- **Clientkant:** kale ES-module JavaScript (geen framework, geen build-stap), Bootstrap 5-klassen uit de site-skin, FontAwesome, jsPDF van een CDN voor de PDF-export.
- **Database:** de SQL Server-database van DNN (connection string SiteSqlServer).

### 3.2 Datastroom (eindgebruikerskant)

```
OPAS  --exportbestand-->  scheduled task op de DNN-server
                                |
                                v
        SQL-tabellen  Rpho_Opas_EventItem  (alle afspraken)
                      Rpho_Opas_Wijziging  (wijzigingshistorie per event)
                                |
                                v
        2sxc Visual Queries  (app/auto/query/RetrieveAfsprakenFromDatabaseOpasEenMaand, ...)
                                |
                                v   $2sxc(...).webApi.get(...)
        js/dataService.js  -->  js/AgendaWeergaveSelector.js  -->  view-builders
                                       (MaandWeergave, WeekWeergave, DagWeergave, LijstWeergave)
```

Belangrijke feiten over de brondata:

- De scheduled import-taak zit niet in deze repo. Die komt uit de opzet van bond voor zusterorkesten (tabelprefix Rpho\_ = dezelfde import als bij andere orkesten). Data moet eerst in OPAS worden vrijgegeven voordat ze verschijnt.
- Er is geen expliciete productie-entiteit in de OPAS-data. Het productienummer wordt uit het eerste woord van EventProjectName geparsed ("2603M1 Music Unites II" → 2603M1). Alles wat per productie groepeert hangt af van deze tekstconventie.
- Het afspraaktype (concert, repetitie, soundcheck, vervoer, opname, overig) wordt afgeleid via trefwoorden in EventText, niet uit EventProjectTypeName. Dit was een expliciete wens van het MO.
- OPAS legt niet vast welke afspraken bij elkaar horen (bus → soundcheck → concert). De kleurgroepering wordt daarom afgeleid uit de vorige/volgende afspraak op dezelfde dag.

### 3.3 Pagina's en routes

Alle views zijn 2sxc-views op DNN-pagina's. Parameters reizen mee in het URL-pad (/agenda/date/2026-09-14) of de querystring en worden gelezen door getParamsFromUrl() in js/AgendaHelper.js.

| Pagina (DNN) | View-bestand | JS-startpunt | Doel |
|---|---|---|---|
| /agenda | AgendaMetropole.cshtml | AgendaWeergaveSelector.js | De agenda met Dag / Week / Maand / Lijst, vorige/volgende, datumkiezer, Download-knop |
| /agenda/agenda_detail?productie=..&eventId=.. | Detail.cshtml + DetailsTitle.cshtml | Agenda_Weergave/Detail.js | Detail van één afspraak: gecategoriseerde velden + wijzigingshistorie |
| /agenda/agenda_detail_productie?productie=.. | DetailsProductie.cshtml, Navigation.cshtml | DetailProducties.js, BlockNavigation.js | Alle afspraken van één productie, gekleurd volgens de groeperingsregel, plus tabbalk Agenda / Productie / Wijzigingen / Bladmuziek / Audio / Bijlage |
| .../wijzigingen | DetailProductieAanpassingen.cshtml | DetailProducties.js | Wijzigingshistorie van de productie |
| .../bladmuziek, .../audio, .../bijlage | Bijlage.cshtml | Bijlage.js | Bestanden bij de productie (ADAM of SharePoint, zie 3.6) |
| AgendaIndexer.cshtml | - | - | Kale HTML-lijst zodat de nieuwe zoekmachine afspraken kan indexeren |
| Beheerpagina's | BeheerProducties/Afspraken/Adressen/Bijlage.cshtml, ProductieAanmaken, AfspraakAanmaken, AdresAanmaken, UploadBijlage.cshtml | AdminTable.js, PostHandler.js, UpdateHandler.js, DeleteHandler.js, FormPreload.js, Bijlage.js | CRUD voor producties, afspraken, adressen en documenten (V 0.1.05, zie 3.5) |

### 3.4 De agenda (eindgebruikerskant) in meer detail

**Status en laden (AgendaWeergaveSelector.js):**

- Bij het laden worden drie maanden tegelijk opgehaald (vorige, huidige, volgende) met één query; daarna wordt lui een maand toegevoegd wanneer navigatie dat nodig heeft (de set loadedMonths voorkomt dubbel ophalen).
- currentView is maand op schermen ≥ 700 px en dag op telefoons (instelbaar in customSettings.defaultOverview\*).
- De Lijst-weergave is anders: die gebruikt geen afspraken maar een query "producties vooruit / achteruit vanaf datum" (4 producties per pagina).
- Elke weergave is een pure functie build<View>(afspraken, currentDate) die een HTML-string teruggeeft die in #agenda-weergave wordt gezet.

**Kleurlogica (AgendaHelper.resolveEntryColors):**

1. Sorteer alle afspraken, groepeer per dag.
1. Bepaal het basistype per afspraak via eventTypeRules (trefwoord in EventText).
1. Een vervoer-afspraak krijgt de kleur van het aangrenzende concert/repetitie/opname; een losse soundcheck krijgt de kleur van het volgende hoofdevenement; twee of meer soundchecks achter elkaar houden hun eigen kleur.
1. Kleuren staan in customSettings.eventTypeColors (WCAG AA gecontroleerd).
- **Maandweergave extra's:** dagen buiten de maand zijn gedimd; max. 2 tegels per dag (1 bij een smal venster), met een "Zie alle"-knop die naar de dagweergave springt; concerten/repetities krijgen voorrang bij de tegelkeuze (de "uitlichten"-story uit V 0.1.04).
- **Dagweergave:** uurraster, scrolt bij laden naar 09:00, afspraken die om 13:30 beginnen staan onder 13:00 (Outlook-achtig, verzoek van het MO).
- **Detailpagina:** de hele rij uit Rpho_Opas_EventItem wordt opgehaald, lege velden verwijderd, daarna gefilterd/gelabeld/gecategoriseerd via drie maps in customSettings.js (detailFilters, detailLabel, detailCategory). Wat getoond wordt is dus een instelling, geen code-aanpassing. Wijzigingshistorie komt uit Rpho_Opas_Wijziging, gesorteerd op WijzigingsDatum.
- **PDF-export (PdfMaker.js):** bouwt een jsPDF-document voor de huidige dag/week/maand/lijst/detail/productie. Bewust sober. Bedoeld als terugvaloptie voor musici die niet kunnen inloggen.
- **Instellingen (js/customSettings.js):** één bestand bevat standaardweergaves, alle query-URL's, alle API-URL's, pagina-URL's, kleuren, startuur van de dagweergave, detailveld-configuratie en de bestandsbron-schakelaars. Dit is de sterkste ontwerpkeuze in de app: het meeste klantspecifieke gedrag is configuratie.

### 3.5 Beheerkant (V 0.1.05, nieuw)

Toegevoegd helemaal aan het einde van de stage. Het introduceert eigen databasetabellen:

- AgendaViewer_Production (nummer, naam, start, einde, IsVisable = "Bestuur"/"Iedereen")
- AgendaViewer_Events (productie-id, titel, type, subtype, ensemble, venue-id, dirigent, start, einde, zichtbaarheid)
- AgendaViewer_Venue (adresgegevens, gebouw-/concertzaal-vlaggen, ingangsbeschrijvingen)
- AgendaViewer_Changes (wordt bij een update geschreven, nooit gelezen)

Flow: formulierpagina → PostHandler.js / UpdateHandler.js / DeleteHandler.js (front-endvalidatie) → POST naar /api/2sxc/app/Agenda Viewer/api/{Post|Update|Delete}Handler/Submit met een JSON-body {EntityType, RequestType, ...} → C#-controller valideert nogmaals (regex-blacklist voor "SQL-injectie", whitelists, lengtecontroles) → geparametriseerd SqlCommand naar de tabellen hierboven.

Beheerlijsten halen alle rijen in één keer op via 2sxc-queries en pagineren/zoeken in de browser (AdminTable.js).

**De agenda leest deze tabellen niet.** Alle eindgebruikersqueries lezen nog steeds Rpho_Opas_EventItem. De documentatie noemt deze omschakeling (of samenvoeging) "toekomstig werk".

### 3.6 Documenten, bladmuziek, audio

Twee mechanismen, te kiezen in customSettings.viewSettings.getBijlageFrom:

- **ADAM** (bestandsopslag van 2sxc in DNN): documenten worden via AdamUploadController geüpload naar adam/Agenda Viewer/Documents met content-type Documents (titel + bestand). De titel wordt gematcht op het productienummer om bestanden op de productiepagina te tonen. Werkt.
- **SharePoint** via een aparte 2sxc-app "Sharepoint Viewer" (Graph API). Code bestaat (getFilesFromSharePoint), maar volgens Documentatie.pdf werkt deze API niet (compile-/validatiefouten). Story V 0.1.06 #1 "Documenten via de SharePoint viewer API" staat nog open in Basecamp.

Bladmuziek zou gefilterd moeten worden op het instrument van de speler; het dossier noemt dit, maar de code in deze repo bevat geen instrumentfilter. Beschouw het als ontwerpintentie, niet als gebouwd gedrag.

### 3.7 Versies in deze repo

| Map | Inhoud |
|---|---|
| V 0.1.02 | Vier weergaves, detail- en productiepagina's, PDF-export, plus een kopie van de DNN-skin (2shineBS5 Skin Copy) met de agenda-specifieke skincontrols (Agenda.ascx, AgendaDetail.ascx, ...) en SCSS |
| V 0.1.04 | Bugfixes, horizontale weekweergave, AgendaIndexer voor zoeken, Bijlage-view, dagweergave scrolt naar 09:00 |
| V 0.1.05 | Beheer-CRUD, eigen tabellen, ADAM upload/update/delete, C#-modellen, de scriptie als PDF |

Dit zijn geëxporteerde kopieën, geen git-historie. De map obj/ met build-output zit erbij. Er is geen diff-spoor tussen versies.

## 4. Wat er ontbreekt

### 4.1 Functionele gaten (ten opzichte van het dossier)

- **Geannuleerde afspraken.** Die zijn simpelweg afwezig. Musici kunnen niet zien dát iets is geannuleerd. Suggestie uit Basecamp: de statuswijziging in de wijzigingshistorie tonen. Nog een open "aandachtspunt".
- **SharePoint-documenten** (V 0.1.06 #1) - open; de Graph-integratie is kapot.
- **Ontwerpkeuzes die bij Lotte Murrath liggen:** welke van de zes detailpagina-wireframes en welk van de vijf ontwerpen voor "uitlichten concerten" geïmplementeerd wordt. Angeliques eigen voorkeur was ontwerp 2 (grijze streepjes-accordion); een variant daarvan zit nu in de maandweergave.
- **Enquêtefunctie** - gearchiveerd.
- **Custom Settings in de 2sxc-UI** (V 0.2.01 #2) - gearchiveerd onderzoek; instellingen vereisen nog steeds bewerken van customSettings.js.
- **Weeknummers in de maandweergave** - gevraagd door Lotte in februari 2026; het weeknummer staat alleen in het label van de weekweergave en op de detailpagina.
- **Filter per orkest / kleur per orkest** - niet nodig voor het MO, maar vereist vóór hergebruik bij NedPhO/RPhO (beschreven in Documentatie.pdf).
- **Al het overige in de Tutti-scope** ontbreekt bewust: dienstenplanning (toewijzing musici), contracten, beschikbaarheid/verlof, notificaties, Outlook/Google-export, SSO/rollen in de app, AFAS-koppeling, rapportages, tabblad Berichten, "Tutti CRM", touring. De Adobe XD proof-of-concept dekt dit; de code niet.

### 4.2 Technische problemen in de code (V 0.1.05)

Gesorteerd op ernst.

1. **Alle schrijf-endpoints zijn [AllowAnonymous].** PostHandlerController.Submit, UpdateHandlerController.Submit, DeleteHandlerController.Submit en alle vier acties van AdamUploadController (upload, list, titel wijzigen, bestand verwijderen). Iedereen die de site-URL kan bereiken kan producties, afspraken, adressen en bestanden aanmaken, wijzigen of verwijderen zonder in te loggen. De scriptie stelt dat de bescherming van DNN-pagina-/rolrechten komt; dat beschermt de pagina's, niet de API. Dit moet gerepareerd zijn vóór de beheerkant in productie gaat.
1. **Twee losgekoppelde datamodellen.** Beheer schrijft naar AgendaViewer\_\*, de agenda leest Rpho_Opas\_\*. Niets wat een beheerder invoert verschijnt ooit in de agenda. IsVisable ("Bestuur"/"Iedereen") wordt opgeslagen maar geen enkele leesquery filtert erop. AgendaViewer_Changes wordt geschreven maar nooit gelezen. De documentatie noemt "bronnen omschakelen of combineren" zelf als belangrijkste to-do.
1. **SQL-tokens in de querytekst.** De 2sxc SQL-queries zetten URL-parameters rechtstreeks in de SQL-string, bijv. WHERE EventProjectName LIKE '[QueryString:productie] %' en STRING_SPLIT('[QueryString:eventIds]', ','). De SQL DataSource van 2sxc zet zulke tokens volgens de documentatie automatisch om in SQL-parameters; dan is dit veilig. Ik kon dat niet verifiëren voor de geïnstalleerde 2sxc-versie. Controleer het op de live site (probeer productie=x' OR 1=1--) voordat je erop vertrouwt.
1. **"SQL-injectie"-regex-blacklists in de controllers** (PatternSqlInjection) weigeren elke invoer met ;, --, /\*, 0x.. of woorden als select, update, replace, cast. De queries zijn al geparametriseerd, dus dit voegt geen veiligheid toe en weigert legitieme namen (een productie genaamd "Update" of een omschrijving met een puntkomma). Verwijderen en vertrouwen op parameters + lengte-/whitelist-checks.
1. **Geen enkele test** (unit, integratie of end-to-end). Elke fix tot nu toe is handmatig en via BrowserStack gecontroleerd.
1. **Geen echte versiebeheer.** Drie snapshot-mappen met obj/-artefacten, een zip per versie en een skin-kopie. Bugs zoals de ontbrekende import in Detail.js (showViewError wordt aangeroepen maar nooit geïmporteerd, waardoor een mislukte details-fetch een ReferenceError geeft in plaats van de foutmelding) glippen er ongemerkt doorheen.
1. **Twee verschillende getWeekNumber-implementaties.** AgendaHelper.js gebruikt een ISO-8601-berekening; AgendaWeergaveSelector.js heeft een eigen niet-ISO-variant voor het label "Week N" en de URL-parameters w= en j=. Ze verschillen rond jaarwissels en week 53, precies het geval dat de shaping-lijst Maandweergave correct wilde hebben.
1. **Hard-gecodeerde omgevingspaden.** /Portals/0/2sxc/Agenda Viewer/... in elke view, de id="testsxc"-hack om 2sxc-API's te laden voor niet-admins, pagina-URL's (/agenda/agenda_detail) in de instellingen. Prima voor één site, lastig voor de ambitie "andere orkesten" uit de documentatie.
1. **Beheerlijsten laden alles.** De afsprakentabel haalt alle rijen op en filtert client-side; de documentatie waarschuwt al dat dit traag wordt.
1. **Tijdzone-afhandeling** is twee keer gerepareerd (V 0.1.03 #3, sync-story) en leunt op GetDutchTime() in de controllers en toLocale\* in de browser. Let op zomer-/wintertijdovergangen.
1. **Achtergebleven console.log-aanroepen** (13) in dataService.js die volledige API-antwoorden naar de console dumpen, ook op productiepagina's.
1. **De OPAS-import** (scheduled task, 48-uursmonitoring) leeft buiten deze repo. Stopt die, dan toont de agenda stilletjes verouderde data; de app heeft geen "laatst gesynchroniseerd"-indicator.

### 4.3 Proces- en continuïteitsgaten

- De stage eindigde in juni 2026; naamgeving (V x.y.z | #n | titel) en lingo zijn vastgelegd voor de volgende stagiair, maar er is geen onboarding buiten de 8 pagina's van Documentatie.pdf.
- De 2sxc visual queries (de echte SQL) bestaan alleen in de app.xml van de geëxporteerde zip, niet in de "Source Code"-mappen. De broncodemappen alleen beschrijven de app niet.
- De scope voor Tutti v1 is niet vastgesteld (gebruiksscan-sessie nog niet gehouden), dus het is onduidelijk in welke Tutti-modules de agenda-app als eerste moet groeien.

## 5. Doorgaan of opnieuw beginnen?

### Argumenten om door te gaan

- **Het werkt en is geaccepteerd:** 32 van 36 user stories af, getest in drie bedrijfsbezoeken, live op Metrostation, het MO heeft al veel details bepaald (kleuren, Outlook-achtige dagweergave, start om 09:00, types op basis van EventText).
- **De front-end is herbruikbaar:** view-builders, kleurgroepering, instellingen-gestuurde detailpagina, PDF-export. Ruwweg 4.000 regels JS die je anders opnieuw schrijft.
- **Platform-fit:** de standaard van bond is DNN + 2sxc, de platformanalyse in het dossier gaat uit van DNN (Plant-an-App + 2sxc) en Metrostation is DNN. Een herbouw in een andere stack vereist een platformbeslissing die nog niet genomen is.
- **De dataproblemen zijn OPAS-problemen,** geen app-problemen (geen productie-entiteit, geen groepering, geen annuleringsvlag). Een nieuwe app op dezelfde data erft ze.

### Argumenten om opnieuw te beginnen

- **De back-endhelft is pas weken oud, onaf en onveilig.** Gebouwd aan het einde van de stage zonder het domeinmodel dat Tutti nodig heeft (geen Person, Ensemble, Season, EventPersonAssignment uit het ER-schema van Martijn, geen rollen).
- **Het beheerdatamodel dupliceert OPAS in plaats van het te vervangen** en is niet aan de agenda gekoppeld.
- Geen tests, geen git-historie, hard-gecodeerde paden.
- Als Tutti op een ander platform belandt (het dossier noemt Plant-an-App low-code en "extern laten toetsen" als open punten), is een 2sxc-specifieke JS-app een doodlopende weg.

### Advies

**Behoud de app, beschouw de back-end van V 0.1.05 als prototype en bouw die helft netjes opnieuw. Begin de front-end niet van nul.**

Concreet:

**Fase A - Wat live staat verstevigen (dagen, geen weken)**

1. Zet de volledige broncode (alle versies, zonder obj/) in git, één branch, tag v0.1.05. Voeg de geëxporteerde app.xml-queries toe aan de repo.
1. Verwijder [AllowAnonymous] van elke schrijf-controller; vereis een DNN-rol (bijv. Planning) via [DnnModuleAuthorize] / SecurityService-checks van 2sxc. Zet tot die tijd de Beheer-pagina's niet op de live site.
1. Repareer de import in Detail.js, maak getWeekNumber één (gebruik de ISO-versie), verwijder de console.logs, verwijder de SQL-blacklist-regexes.
1. Verifieer dat de [QueryString:...]-tokens geparametriseerd worden op de live 2sxc-versie.
1. Toon ergens zichtbaar een "laatst gesynchroniseerd met OPAS"-tijdstip en de geannuleerd-status in de wijzigingshistorie (goedkoop, veel waarde voor musici).

**Fase B - Eén datamodel (de echte beslissing)**

- Beslis of AgendaViewer\_\* de bron van waarheid wordt (Tutti-richting) of een aanvulling blijft. De Tutti-memo zegt "één centrale planning als basis", ontwerp de tabellen dus naar het ER-schema van Martijn (Event, Project, Venue, Person, Ensemble, EventType, Season, EventPersonAssignment) en laat de OPAS-import die vullen in plaats van een parallelle tabel.
- Richt de leesqueries op het nieuwe model, filter op zichtbaarheid, schrap het parsen van het productienummer uit de tekst zodra ProductionNumber een echte kolom is.
- Voeg een minimale testopzet toe (Playwright of Cypress tegen een dev-DNN, plus gewone unit-tests voor AgendaHelper.js).

**Fase C - Uitgroeien naar Tutti (pas nadat de gebruiksscan-sessie de v1-scope heeft vastgesteld)**

- Tabblad Berichten, dienstenplanning, SSO/rollen, Outlook-export, AFAS, in de volgorde die het MO bepaalt.

**Wanneer wél opnieuw beginnen:** alleen als het MT besluit dat Tutti niet op DNN/2sxc gaat draaien, of als bond kiest voor Plant-an-App voor formulieren/workflows en 2sxc voor content (de platformanalyse uit het dossier). Neem ook dan customSettings.js als configuratiemodel, de kleurgroeperingsregel en de categorisering van de detailpagina mee, want daar zitten maanden MO-feedback in.

## 6. Snelle referentie voor wie dit oppakt

- **Begin met lezen:** js/customSettings.js → js/AgendaWeergaveSelector.js → js/AgendaHelper.js → één view-builder (MaandWeergave.js).
- **De SQL achter elke leesactie:** pak 2sxcApp_AgendaViewer_0.1.2.zip uit en zoek op SelectCommand in Apps/Agenda Viewer/2sexy/App_Data/app.xml.
- **Story-naamgeving:** V 0.1.05 | #2 | Documenten inladen via ADAM. De Basecamp-lijst "Angelique stage" (todolist 9692870709) bevat alle 36 stories.
- **Mensen:** Lotte Murrath (MO planning) beslist over UX; Caspar Abbenhuis (MO) is de opdrachtgever; Martijn Verbeek (bond) bepaalt de Tutti-richting; Marnix Bouwman (bond) was technisch begeleider; Dörte is het OPAS-contact voor de export.
- **Lingo:** Afspraak = agenda-evenement; Productie = OPAS-project met een nummer als 2603M1; Gebruiker = musici en medewerkers.
