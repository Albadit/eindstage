# Architectuurontwerp - Tutti

*Ontwerp · Tutti · Metropole Orkest*

Dit document beschrijft hoe Tutti in elkaar zit: waar het draait, uit welke delen het bestaat, met welke systemen het samenwerkt en waarom het zo is opgezet. Het gaat over versie 00.03.00.

| Gegeven | Waarde |
| --- | --- |
| Documenttype | Ontwerp (architectuur) |
| Competentie | Design |
| Deelvraag | 2 - architectuur- en integratieaanpak; 3 - data- en autorisatiemodel |
| Auteur | Ardit Fazliji |
| Versie van Tutti | 00.03.00 |
| Datum | 1 oktober 2026 |
| Gerelateerd | [Databaseontwerp](<Databaseontwerp.md>) · [Backendontwerp](<Backendontwerp.md>) · [Gegevensstromen](<Gegevensstromen.md>) · [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>) · [Eigen DNN-module of 2sxc](<../05. Advice/Eigen_DNN-module_of_2sxc.md>) |

## Inhoud

1. [Wat Tutti is](#1-wat-tutti-is)
2. [Uitgangspunten](#2-uitgangspunten)
3. [Waar Tutti draait](#3-waar-tutti-draait)
4. [Uit welke delen Tutti bestaat](#4-uit-welke-delen-tutti-bestaat)
5. [De lagen](#5-de-lagen)
6. [Twee manieren waarop de browser met Tutti praat](#6-twee-manieren-waarop-de-browser-met-tutti-praat)
7. [Samenwerking met andere systemen](#7-samenwerking-met-andere-systemen)
8. [Belangrijke ontwerpkeuzes](#8-belangrijke-ontwerpkeuzes)
9. [Beveiliging](#9-beveiliging)
10. [Installatie en configuratie](#10-installatie-en-configuratie)
11. [Kwaliteit](#11-kwaliteit)
12. [Afwijkingen van de Technische SRS](#12-afwijkingen-van-de-technische-srs)
13. [Review en openstaande punten](#13-review-en-openstaande-punten)

## 1. Wat Tutti is

Tutti is de nieuwe planning- en administratieapplicatie van het Metropole Orkest (MO). Op dit moment bevat Tutti de planning (producties en afspraken), de agenda in vier weergaven, de pagina van een afspraak, de projectkamer per productie, beheerschermen voor planners, de bezetting met aanwezigheid, de wijzigingshistorie, en de documenten en bladmuziek van een productie. Afspraken en producties komen uit twee bronnen: wat in Tutti zelf is ingevoerd, en de bestaande planning in OPAS. Beide staan op dezelfde pagina's, met een label dat zegt waar ze vandaan komen.

## 2. Uitgangspunten

Het ontwerp volgt het advies uit het adviesrapport [Eigen DNN-module of 2sxc](<../05. Advice/Eigen_DNN-module_of_2sxc.md>): het domein van Tutti in een eigen DNN-module met eigen tabellen, en de domeinlogica in een aparte .NET Standard-library die niets van DNN weet (randvoorwaarde 1 uit hoofdstuk 8.3 van dat rapport). De technische eisen komen uit de [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>). Waar de gebouwde versie daarvan afwijkt, staat dat met de reden in [hoofdstuk 12](#12-afwijkingen-van-de-technische-srs).

## 3. Waar Tutti draait

Tutti is geen losse website, maar een **eigen DNN-module**. DNN (DotNetNuke) is het systeem waarop het intranet Metrostation draait. Tutti staat op een pagina van dat intranet (bijvoorbeeld `/tutti`) en gebruikt wat DNN al heeft: het inloggen, de gebruikers en de rollen.

| Onderdeel | Versie of technologie |
| --- | --- |
| Platform | DNN 9.10.2 of hoger; ontwikkeld en getest op DNN 9.13.2 (de ontwikkelkopie van Metrostation) |
| Webserver | IIS op Windows, .NET Framework 4.8 |
| Database | De SQL Server-database van de DNN-site zelf; Tutti heeft geen eigen database |
| Schermen | Razor-views (HTML die op de server wordt gemaakt), CSS en één klein script zonder framework |
| Installatie | Eén installatiebestand (`Tutti_<versie>_Install.zip`) dat een beheerder in DNN installeert via Extensions |

Omdat Tutti binnen DNN draait, hoeft niemand een extra account te hebben: wie op Metrostation is ingelogd, is ook in Tutti ingelogd. Wat iemand in Tutti mag, hangt af van de DNN-rollen van die persoon (zie [hoofdstuk 9](#9-beveiliging)).

## 4. Uit welke delen Tutti bestaat

De code staat in twee projecten en twee testprojecten.

| Project | Wat erin zit | Kent DNN? |
| --- | --- | --- |
| `src/Tutti.Core` (.NET Standard 2.0) | Het hart: alle regels, controles en rechten, de services die een handeling uitvoeren, en de beschrijving van de gegevens | Nee |
| `src/Dnn.Modules.Tutti` (.NET Framework 4.8) | Alles wat met DNN en de buitenwereld te maken heeft: schermen, de API, SQL, de koppeling met de SharePoint Viewer, installatie | Ja |
| `tests/Tutti.Core.Tests` | Unittests van de regels en rechten | Nee |
| `tests/Dnn.Modules.Tutti.IntegrationTests` | Tests tegen een echte SQL Server-database (LocalDB), de pagina's, de API en de koppelingen | Ja |

De scheiding is bewust: alles wat bepaalt *wat mag en wat klopt*, zit in `Tutti.Core` en weet niets van DNN, SQL Server of SharePoint. Daardoor kunnen de regels snel en volledig getest worden, en kan een koppeling worden vervangen zonder dat een regel verandert.

## 5. De lagen

Een verzoek gaat altijd van boven naar beneden door vier lagen. Een laag praat alleen met de laag eronder.

| Laag | Map | Verantwoordelijk voor | Mag niet |
| --- | --- | --- | --- |
| 1. Presentatie | `Views/`, `Resources/css`, `Resources/js` | De pagina's tonen, dialogen openen, opslaan via de API | Regels toepassen of zelf beslissen wat iemand mag zien |
| 2. Controllers | `Controllers/` (pagina's), `Api/` (opslaan en opvragen) | Een webverzoek vertalen naar een service-aanroep, en het antwoord naar HTML of JSON | Regels bevatten |
| 3. Domein | `src/Tutti.Core` | Valideren, rechten controleren, regels toepassen, bepalen wat er verandert | DNN, SQL of HTTP kennen |
| 4. Data en koppelingen | `Data/`, `Adapters/` | SQL uitvoeren, de SharePoint Viewer aanroepen | Beslissingen nemen |

Laag 3 bepaalt *wat* er van de opslag nodig is via interfaces (bijvoorbeeld `IProductionRepository`, `IOpasRepository`, `IDocumentGateway`); laag 4 levert de uitvoering daarvan. In `Components/Startup.cs` wordt bij het starten vastgelegd welke uitvoering bij welke interface hoort (dependency injection).

## 6. Twee manieren waarop de browser met Tutti praat

1. **Een pagina openen.** Een link is een gewone GET naar een MVC-controller. Die vraagt de services om de gegevens en laat een Razor-view de HTML maken. De pagina komt compleet uit de server; er is geen single-page app.
2. **Iets opslaan of opvragen.** Een dialoog stuurt JSON naar de API onder `/API/Tutti/v1/`. Na een geslaagde opslag herlaadt de pagina, zodat wat op het scherm staat altijd is wat de server heeft. Ook de lijst met documenten en de regel "Documenten en bladmuziek" worden via de API opgevraagd, nadat de pagina al getoond is.

Er wordt nooit iets naar een pagina gepost, omdat DNN een POST naar een pagina kan omleiden en dan de inhoud kwijtraakt. Beide wegen komen uit bij **dezelfde services**: een scherm kan dus nooit iets tonen wat de API zou weigeren.

## 7. Samenwerking met andere systemen

| Systeem | Wat het levert | Wat Tutti ermee doet |
| --- | --- | --- |
| **DNN** | Inloggen, gebruikers, rollen, profielvelden | Leest wie de bezoeker is en welke rollen die heeft. Personen in Tutti zíjn DNN-gebruikers; hoofdinstrument en dienstverband zijn profielvelden (`TuttiInstrument`, `TuttiEmployment`). Tutti schrijft niets in de tabellen van DNN |
| **OPAS** (via de OPAS-import) | De bestaande planning. Een geplande taak in DNN leest elk uur de export van OPAS en zet die in de tabellen `dbo.Rpho_Opas_*` | Leest die tabellen bij elke pagina rechtstreeks. Tutti kopieert niets en wijzigt niets; OPAS blijft leidend |
| **SharePoint Viewer** (2sxc-app op dezelfde site) | Toegang tot de documenten, bladmuziek en audio in SharePoint, met de instrumentrollen van de bezoeker | Tutti roept de viewer aan vanaf de server, als de bezoeker zelf, en zoekt op productienummer. Tutti heeft geen eigen SharePoint-account, geen wachtwoorden en geen kopie van bestanden |
| **SQL Server** (de database van de site) | Opslag | De eigen tabellen `Tutti_*` voor de planning die in Tutti wordt ingevoerd |

Zonder de OPAS-import of de SharePoint Viewer blijft de rest van Tutti werken; de pagina zegt dan welk deel ontbreekt.

## 8. Belangrijke ontwerpkeuzes

| Keuze | Waarom |
| --- | --- |
| Pagina's op de server maken (Razor binnen DNN) | Past bij DNN, werkt zonder zwaar script, en elke pagina is een gewone link die je kunt bewaren of delen |
| Alle regels en rechten in `Tutti.Core` | Eén plek om te controleren en te testen; controllers en schermen beslissen nooit |
| OPAS lezen, niet kopiëren | Geen dubbele gegevens en niets om bij te houden. Nadeel: een OPAS-productie en een Tutti-productie met hetzelfde nummer staan als twee producties tot de latere overgang |
| Documenten via de SharePoint Viewer, als de bezoeker | De viewer bepaalt al welke mappen bij welk instrument horen; Tutti krijgt zo geen ruimere rechten dan de bezoeker zelf |
| Personen zijn DNN-gebruikers | Iedereen heeft toch al een DNN-account; een eigen personenlijst zou een tweede, mogelijk afwijkende kopie zijn |
| Korte adressen met de bron in het nummer | `/tutti/events/t12` is afspraak 12 van Tutti, `/tutti/events/o3555` afspraak 3555 van OPAS. Tutti en OPAS nummeren los van elkaar, dus de letter is nodig. Een URL-provider zet de lange DNN-adressen om |
| Tijden in UTC opslaan, in Amsterdamse tijd tonen | Zomer- en wintertijd gaan dan nooit mis bij opslaan |
| Nederlands en Engels | De taal volgt de taal van de DNN-pagina; elke tekst bestaat in beide talen |

## 9. Beveiliging

- **Alleen ingelogd.** Elke pagina en elk API-adres vraagt een ingelogde DNN-gebruiker.
- **Rechten op één plek.** `VisibilityService` met de `PermissionMatrix` beslist voor elke handeling wie wat mag. Rollen zijn DNN-rollen in de rolgroep *Tutti*: Administrator, Planner, Productieleider, Musicus, Remplaçant en Functioneel beheer. Een verborgen knop is gemak, geen slot: de server controleert altijd opnieuw.
- **Niet zichtbaar is niet bestaand.** Wat iemand niet mag zien, geeft "niet gevonden" (404), alsof het niet bestaat. Wat iemand wel mag zien maar niet mag wijzigen, geeft "niet toegestaan" (403) en wordt vastgelegd.
- **Opslaan is beschermd** met het antiforgery-token van DNN, zodat een andere website niet namens een gebruiker kan opslaan.
- **Alles wordt vastgelegd.** Elke wijziging komt in de audittrail (wie, wat, oude en nieuwe waarde, wanneer), in dezelfde transactie als de wijziging zelf. De audittrail kan niet worden aangepast of verwijderd.
- **Geen geheimen in de browser.** Geen wachtwoorden, tokens of SharePoint-adressen gaan naar de browser.
- **Veilige bestanden.** Alleen PDF, afbeeldingen, audio, video en platte tekst openen in de browser; al het andere wordt als download gegeven, zodat een bestand nooit als pagina van het intranet kan draaien.

## 10. Installatie en configuratie

Het installatiebestand bevat de twee DLL's, de schermen en stijlen, de databasescripts en een manifest (`Tutti.dnn`). Bij installeren registreert DNN:

| Onderdeel in het manifest | Doet |
| --- | --- |
| Module | De module die een beheerder op een pagina zet |
| Script | Maakt de `Tutti_*`-tabellen aan of werkt ze bij (`00.01.00.SqlDataProvider`, `00.01.01.SqlDataProvider`) |
| UrlProvider | De korte adressen (`/tutti/events`, `/tutti/productions/o351`) |
| Cleanup | Ruimt bestanden van vorige versies op |

Er hoeft niets te worden ingesteld als Tutti en de SharePoint Viewer op dezelfde site draaien. Voor uitzonderingen zijn er drie optionele instellingen in `web.config` (`Tutti.SharePointViewer.BaseUrl`, `.AppName`, `.TimeoutSeconds`). De waarschuwing "geen nieuwe OPAS-import" gebruikt de instellingen van de OPAS-import zelf (`OpasLastXmlImportDate`, `OpasAlertHours`).

## 11. Kwaliteit

- Bij elke wijziging in GitHub wordt Tutti gebouwd, getest en verpakt (GitHub Actions).
- Ongeveer 570 automatische tests: regels, rechten per rol (ook wat moet mislukken), elke SQL-opdracht tegen een echte database, alle schermen, beide talen, de koppelingen met OPAS en de SharePoint Viewer.
- De build weigert pakketten met bekende beveiligingslekken.
- Wat de tests niet kunnen, staat als handmatige controlelijst in de README van de broncode.

## 12. Afwijkingen van de Technische SRS

De [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>) beschrijft het doelbeeld. Versie 00.03.00 bouwt het deel dat niet van een open beslissing afhangt. Afwijkingen in het datamodel staan in het [Databaseontwerp](<Databaseontwerp.md#7-afwijkingen-van-de-technische-srs>).

| Technische SRS | Gebouwd in 00.03.00 | Waarom |
| --- | --- | --- |
| Frontendtechnologie nog open (`TD-007`) | Razor-views binnen DNN, pagina's op de server | Zie hoofdstuk 8: past bij DNN, werkt zonder zwaar script, elke pagina is een gewone link |
| Data access via een ORM met migrations (§3.2) | Handgeschreven SQL met parameters; de databasescripts van DNN als migratiemechanisme | DNN heeft al een migratiemechanisme, en zo komt er geen extra bibliotheek in de gedeelde `bin`-map van de site |
| OPAS-gegevens importeren in Tutti (§8.4, §19 fase 1) | De OPAS-tabellen rechtstreeks lezen, niets kopiëren | Geen dubbele gegevens; het nadeel staat in hoofdstuk 8 |
| Documenten en bladmuziek als tabellen (`Document`, `SheetMusicPart`) | Nog niet; documenten komen via de SharePoint Viewer en worden niet opgeslagen | Wacht op `TD-003` (rechten SharePoint-koppeling) en `TD-008` (waar de metadata staat) |
| `ZichtbaarheidService` | `VisibilityService` met `PermissionMatrix` | Dezelfde rol, een andere naam |

## 13. Review en openstaande punten

**Review van het ontwerp.** [AANVULLEN: wie heeft dit ontwerp beoordeeld (bijvoorbeeld de technisch begeleider), wanneer, welke feedback er kwam en wat er daarna is aangepast.]

**Openstaande punten:**

- Documenten en bladmuziek als eigen tabellen, zodra `TD-003` en `TD-008` beslist zijn.
- De AFAS-sleutel van een persoon (`MIG-006`) heeft nog geen plek; die wordt een derde profielveld naast instrument en dienstverband.
- Een OPAS-productie en een Tutti-productie met hetzelfde productienummer staan tot de overgang van OPAS naar Tutti als twee producties.

## Meer technische documentatie

In de repository van de broncode (`Dnn.Modules.Tutti`, map `docs/`) staat meer technische uitleg: `how-tutti-works.md` (uitgebreide technische uitleg, Engels), `api.md` (alle adressen van de API), `opas.md` (de OPAS-tabellen en hoe ze op het scherm komen) en `documents.md` (de koppeling met de SharePoint Viewer).
