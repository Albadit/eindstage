# Backendontwerp - Tutti

*Ontwerp · Tutti · Metropole Orkest*

Dit document beschrijft de code achter de schermen van Tutti: welke onderdelen er zijn, wat elk onderdeel doet, en hoe een verzoek van de browser tot aan de database gaat. Het is bedoeld voor een ontwikkelaar die Tutti moet begrijpen of uitbreiden. Het grote plaatje staat in het [Architectuurontwerp](<Architectuurontwerp.md>).

| Gegeven | Waarde |
| --- | --- |
| Documenttype | Ontwerp (backend) |
| Competentie | Design |
| Deelvraag | 2 - architectuur- en integratieaanpak; 3 - data- en autorisatiemodel |
| Auteur | Ardit Fazliji |
| Versie van Tutti | 00.03.00 |
| Datum | 1 oktober 2026 |
| Gerelateerd | [Architectuurontwerp](<Architectuurontwerp.md>) · [Databaseontwerp](<Databaseontwerp.md>) · [Gegevensstromen](<Gegevensstromen.md>) · [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>), hoofdstuk 7, 9 en 12 |

## Inhoud

1. [Een verzoek van begin tot eind](#1-een-verzoek-van-begin-tot-eind)
2. [Mappen en bestanden](#2-mappen-en-bestanden)
3. [De ingangen](#3-de-ingangen)
4. [De services](#4-de-services)
5. [Rechten](#5-rechten)
6. [De regels](#6-de-regels)
7. [Gegevens opslaan en lezen](#7-gegevens-opslaan-en-lezen)
8. [De koppeling met de SharePoint Viewer](#8-de-koppeling-met-de-sharepoint-viewer)
9. [Fouten en logging](#9-fouten-en-logging)
10. [Iets toevoegen: waar hoort het](#10-iets-toevoegen-waar-hoort-het)

## 1. Een verzoek van begin tot eind

Elk verzoek, of het nu een pagina is of een opslag via de API, doorloopt dezelfde stappen:

1. **DNN** ontvangt het verzoek. De URL-provider (`Components/TuttiUrlProvider.cs`) vertaalt het korte adres, zoals `/tutti/events/o3555`, naar de controller en actie die DNN verwacht.
2. **De controller** controleert dat er iemand is ingelogd en maakt van de DNN-gebruiker een *caller* (`CallerContextFactory`): gebruikers-id, de Tutti-rollen die uit de DNN-rollen volgen, en het persoonsnummer als het account actief is op deze site. Vanaf hier krijgt alles de caller mee; niets leest "de huidige gebruiker" uit een globale plek.
3. **De service** in `Tutti.Core` voert de handeling uit: controleert via `VisibilityService` of de caller het mag, valideert de invoer, past de regels toe en vraagt de gegevens op of slaat ze op.
4. **De repository** voert de SQL uit, met de rechten van de caller al in de `WHERE`-clausule, zodat er nooit iets wordt opgehaald wat de caller niet mag zien.
5. **Het antwoord** gaat terug als HTML (een pagina) of JSON (de API). Een fout wordt een nette foutpagina of een JSON-foutmelding met een code en een trace-id.

## 2. Mappen en bestanden

| Map | Inhoud |
| --- | --- |
| `src/Dnn.Modules.Tutti/Controllers` | `PageControllers.cs` (de pagina's), `TuttiMvcController.cs` (gemeenschappelijke basis) |
| `src/Dnn.Modules.Tutti/Api` | `Controllers.cs` en `OpasControllers.cs` (de API), `ApiInfrastructure.cs` (filters, foutafhandeling, caller) |
| `src/Dnn.Modules.Tutti/Data` | De repositories met de SQL, en `DbSession` (verbinding en transactie) |
| `src/Dnn.Modules.Tutti/Adapters` | `SharePointViewerClient.cs`, de koppeling met de SharePoint Viewer |
| `src/Dnn.Modules.Tutti/Components` | Opstart (`Startup.cs`), API-routes, de URL-provider, hulpfuncties voor de views |
| `src/Dnn.Modules.Tutti/Models` | De view models: wat een pagina nodig heeft om te tonen |
| `src/Dnn.Modules.Tutti/Providers/.../SqlDataProvider` | De databasescripts per versie |
| `src/Tutti.Core/Services` | De services: één per onderwerp |
| `src/Tutti.Core/Security` | `VisibilityService` en de `PermissionMatrix` |
| `src/Tutti.Core/Rules` | De regels: groeperingsregel, conflicten, wijzigingen, tijd, documenten |
| `src/Tutti.Core/Data` | De interfaces die de opslag en koppelingen moeten leveren |
| `src/Tutti.Core/Contracts` | De DTO's: de vorm waarin gegevens uit de services komen, dezelfde voor pagina's en API |
| `src/Tutti.Core/Domain` | De entiteiten en opsommingen (status, rolgroep, aanwezigheid) |

## 3. De ingangen

### 3.1 Pagina's (MVC-controllers)

Alle paginacontrollers erven van `TuttiMvcController`. Die weigert bezoekers die niet zijn ingelogd en maakt van elke weigering of fout de foutpagina van Tutti in plaats van de algemene foutmelding van DNN.

| Controller | Adres | Toont | Gebruikt |
| --- | --- | --- | --- |
| `HomeController` | `/tutti` | Mijn producties: productiekaarten uit Tutti en OPAS, eigen diensten van vandaag, laatste wijzigingen | Production-, Event-, Opas-, Change-, ReferenceService |
| `AgendaController` | `/tutti/events?view=&date=&source=&mine=` | De agenda in maand, week, dag of lijst | Event-, Opas-, ReferenceService |
| `EventController` | `/tutti/events/t12`, `/tutti/events/o3555` | Eén afspraak; voor een planner ook bewerken, annuleren, bezetting | Event-, Assignment-, Change-, OpasService |
| `ProductionController` | `/tutti/productions/o351/documents` | De projectkamer met tabbladen agenda, informatie, wijzigingen, documenten | Production-, Event-, Opas-, Change-, ReferenceService |
| `ManageController` | `/tutti/manage`, `/tutti/manage/5` | Beheer: producties zoeken, aanmaken, afspraken plannen | Production-, Event-, ReferenceService |

Een controller bevat geen regels: hij haalt de gegevens op via services, zet ze in een view model en geeft dat aan een Razor-view. Een view roept nooit zelf een service aan.

### 3.2 API (`/API/Tutti/v1/`)

Alle API-controllers erven van `TuttiApiController`. Elk verzoek gaat door deze filters, in deze volgorde van betekenis:

| Filter | Doet |
| --- | --- |
| `DnnAuthorize` | Alleen ingelogde gebruikers |
| `RequestLanguage` | Kiest de taal van de antwoorden (Nederlands of Engels) uit het verzoek |
| `TuttiJsonConfig` | Eén JSON-vorm: camelCase, opsommingen als tekst, tijden in UTC |
| `TraceIdFilter` | Geeft elk verzoek een trace-id, terug te vinden in het antwoord, de applicatielog en de audittrail |
| `ValidateModelState` | Kapotte JSON of een verkeerd type wordt een validatiefout met het veld erbij |
| `ValidateAntiForgeryToken` | Op elke schrijfactie: beschermt tegen opslaan namens iemand vanaf een andere site |
| `TuttiDomainErrors`, `TuttiExceptionFilter` | Zet een weigering of fout om in één vaste foutvorm (`code`, `message`, `traceId`) |

De belangrijkste adressen (de volledige lijst staat in `docs/api.md` in de broncode):

| Adres | Methodes | Wat | Recht |
| --- | --- | --- | --- |
| `productions`, `productions/{id}` | GET, POST, PUT, DELETE | Producties lezen, aanmaken, wijzigen, verwijderen | `Production.Read` / `.Manage` |
| `productions/{id}/status` | POST | Status zetten: concept, review, gepubliceerd | `Production.Manage` |
| `productions/{id}/members` | GET, POST, DELETE | Wie bij een productie hoort (productieleiders, remplaçanten) | `Production.Manage` |
| `productions/{id}/events`, `events/{id}` | GET, POST, PUT, DELETE | Afspraken | `Event.Read` / `.Manage` |
| `events/{id}/cancel` | POST | Annuleren; de afspraak blijft zichtbaar als geannuleerd | `Event.Manage` |
| `events/{id}/assignments`, `assignments/{id}` | GET, POST, PUT, DELETE | Bezetting: rol, instrument, lessenaar, aanwezigheid | `Event.Assignment.Read` / `.Manage` |
| `calendar`, `me/calendar` | GET | Agenda over een periode, of alleen de eigen diensten | `Event.Read` |
| `changes`, `productions/{id}/changes` | GET | Wijzigingshistorie | `Event.Read` |
| `persons`, `me` | GET | Personen zoeken; het eigen profiel en de eigen rechten | `Person.Read` / ingelogd |
| `reference/...` | GET | Soorten afspraken, zalen, instrumenten, seizoenen | ingelogd |
| `opas/...` | GET | De OPAS-planning: status van de import, producties, afspraken, wijzigingen | `Opas.Read` |
| `.../documents`, `.../documents/content` | GET | Lijst met documenten van een productie; één bestand | `Document.Read` |
| `audit` | GET | De audittrail | `Audit.Read` |
| `health` | GET | Werken database, OPAS-import en SharePoint Viewer? | ingelogd |

## 4. De services

De services staan in `Tutti.Core/Services`.

| Service | Verantwoordelijk voor |
| --- | --- |
| `ProductionService` | Producties zoeken, aanmaken, wijzigen, publiceren, verwijderen (niet als er nog afspraken zijn), leden beheren |
| `EventService` | Afspraken plannen, wijzigen, annuleren, verwijderen; de agenda over een periode; conflicten melden |
| `AssignmentService` | De bezetting: musici indelen met rol, instrument, lessenaar en aanwezigheid |
| `PersonService` | Personen zoeken (de actieve DNN-gebruikers) en het eigen profiel |
| `ReferenceService` | Vaste lijsten: soorten afspraken met kleur en afkorting, zalen, instrumenten, seizoenen |
| `ChangeService` | De wijzigingshistorie die musici lezen, in hun taal en in Amsterdamse tijd |
| `AuditService` | De audittrail lezen, en weigeringen vastleggen |
| `OpasService` | De OPAS-planning omzetten naar producties, afspraken en wijzigingen zoals Tutti ze toont, met de rechten van de caller |
| `DocumentService` | De documenten van een productie opzoeken en één bestand doorgeven, na alle controles |

Daarnaast:

| Onderdeel | Doet |
| --- | --- |
| `VisibilityService` + `PermissionMatrix` | Beslist per handeling of de caller het mag en hoe ver dat reikt |
| `AccessGuard` | Haalt een productie op en weigert als de caller er niet bij mag (404 of 403) |
| `AuditWriter` | Schrijft een regel in de audittrail: wie, wat, oude en nieuwe waarde, trace-id |
| `DownloadLedger` | Zorgt dat een download die in delen binnenkomt (een grote partituur) één keer in de audittrail staat |

## 5. Rechten

Rollen zijn DNN-rollen in de rolgroep *Tutti*. Tutti maakt ze aan de eerste keer dat een beheerder de module opent; wie welke rol heeft, wordt in DNN beheerd. De rollen volgen de rollen uit de [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>), hoofdstuk 9.2.

| Rol | Mag |
| --- | --- |
| Tutti Administrator (en DNN-beheerders) | Alles |
| Tutti Planner | Alle producties, afspraken en bezettingen beheren en publiceren |
| Tutti Productieleider | De producties beheren waarvan die persoon lid is |
| Tutti Musicus | De eigen producties en diensten lezen, zodra ze gepubliceerd zijn; de hele OPAS-planning lezen |
| Tutti Remplaçant | Alleen de producties waarvoor die persoon is ingehuurd |
| Tutti Functioneel beheer | Alleen de audittrail lezen |

De `PermissionMatrix` legt per rol per recht ook het **bereik** vast: `All` (alle producties), `OwnProductions` (producties waarvan iemand lid is of waarop iemand is ingedeeld), `OwnRecord` (alleen de eigen regel, zoals de eigen dienst of de bladmuziek van het eigen instrument) of niets. Een recht dat niet in de matrix staat, is geweigerd (deny by default).

Drie regels gelden daarbovenop:

- Wie niets mag beheren, ziet alleen wat gepubliceerd is. Een afspraak die na publicatie is geannuleerd, blijft zichtbaar als geannuleerd.
- Ingedeeld zijn maakt een productie zichtbaar, niet bewerkbaar. Bewerken vraagt een expliciet lidmaatschap.
- Niet mogen zien geeft 404 (alsof het niet bestaat); wel zien maar niet mogen wijzigen geeft 403 en komt in de audittrail.

## 6. De regels

De regels staan in `Tutti.Core/Rules`.

| Regel | Wat |
| --- | --- |
| Status | Een productie en een afspraak gaan van concept via review naar gepubliceerd; annuleren is een aparte stap |
| Groeperingsregel (`ColorGroupingRule`) | Vervoer of soundcheck direct voor of na een concert, repetitie of opname krijgt diens kleur (maximaal zes uur ertussen) |
| Conflicten (`ConflictDetector`) | Dezelfde persoon of zaal op twee afspraken die overlappen: een waarschuwing, nooit een weigering |
| Wijzigingen (`EventChangeDescriber`) | Bij een gepubliceerde afspraak wordt vastgelegd *wat* veranderde; de zin wordt pas bij het lezen gemaakt, in de taal van de lezer |
| Tijd (`AmsterdamTime`, `CalendarMath`) | Opslaan in UTC, tonen in Amsterdamse tijd; een afspraak over middernacht staat op beide dagen |
| OPAS | Het productienummer uit de projectnaam halen, de soort afspraak uit de tekst, geannuleerd herkennen |
| Documenten (`DocumentRules`) | Alleen bestanden met het productienummer in naam of map; soort bepalen (bladmuziek, audio, overig); dubbele kopieën één keer |

## 7. Gegevens opslaan en lezen

- **Repositories** (`Data/`) voeren met de hand geschreven SQL uit, altijd met parameters. Er is geen ORM: de databasescripts van DNN zijn al het migratiemechanisme, en zo komt er geen extra bibliotheek in de gedeelde `bin`-map van de site. Dit wijkt af van de Technische SRS (§3.2); zie het [Architectuurontwerp](<Architectuurontwerp.md#12-afwijkingen-van-de-technische-srs>).
- **`DbSession`** is één verbinding en transactie per verzoek. Alle repositories van een verzoek delen die, zodat een wijziging, de audittrail en de wijzigingshistorie samen worden opgeslagen of helemaal niet.
- **Gelijktijdig bewerken.** Elke bewerkbare rij heeft een `RowVersion`. De browser stuurt mee welke versie hij las; heeft iemand anders intussen opgeslagen, dan is het antwoord `409` met het aanbod om opnieuw te laden.
- **Zacht verwijderen.** Een verwijderde productie, afspraak of indeling krijgt een `DeletedAt` en blijft bewaard.
- **OPAS** wordt gelezen door `OpasRepository`, alleen met `SELECT`. Ontbreken de OPAS-tabellen, dan meldt Tutti dat op de pagina en werkt de rest door.

## 8. De koppeling met de SharePoint Viewer

`SharePointViewerClient` (achter de interface `IDocumentGateway`) roept de API van de SharePoint Viewer op dezelfde site aan: `Search` om de bestanden van een productienummer te vinden, `ProxyDownload` om één bestand door te geven en `Ping` voor de statuscontrole. Het stuurt de inlogcookie van de bezoeker mee, zodat de viewer de instrumentrollen van die bezoeker toepast. Een bestand wordt alleen doorgegeven als het op dat moment voorkomt in wat de viewer voor deze bezoeker en deze productie laat zien; een document-id uit een andere bron opent dus niets. Bestanden worden in delen doorgestuurd en nooit in het geheugen van de server bewaard.

## 9. Fouten en logging

- Elke fout heeft één vorm: een HTTP-status, een vaste `code` (bijvoorbeeld `EVENT_NOT_FOUND`, `CONCURRENCY_CONFLICT`, `EXTERNAL_UNAVAILABLE`), een leesbare `message` in de taal van de gebruiker en een `traceId`. Dit volgt de foutafhandeling uit de Technische SRS, hoofdstuk 12.
- Onverwachte fouten gaan met alle details naar de applicatielog van DNN, maar de gebruiker krijgt nooit technische details te zien.
- Gewone weigeringen ("niet gevonden", "niet toegestaan") worden niet als crash gelogd.

## 10. Iets toevoegen: waar hoort het

| Soort wijziging | Plek |
| --- | --- |
| Een regel, controle of recht | `Tutti.Core`, met een unittest |
| Een nieuwe query of kolom | Een repository in `Data/`, achter een interface in `Tutti.Core/Data` |
| Een nieuw scherm | Een actie in `Controllers/`, een view model in `Models/`, een view in `Views/` |
| Een nieuwe schrijfactie | Een actie in een API-controller plus een route in `ServiceRouteMapper` |
| Een nieuw extern systeem | Een interface in `Tutti.Core/Data` en een adapter in `Adapters/` |
| Een tekst | Beide `SharedResources`-bestanden (schermen) of beide woordenboeken in `Texts.cs` (meldingen) |
| Een databasewijziging | Een nieuw `NN.NN.NN.SqlDataProvider`-script, en het [Databaseontwerp](<Databaseontwerp.md>) bijwerken |
