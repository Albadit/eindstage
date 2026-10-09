# Testplan - Tutti

*Testplan · Tutti-module 00.03.01 · Bond for Web Solutions / Metropole Orkest*

Dit testplan legt vast hoe Tutti, de planning- en administratieapplicatie van het Metropole Orkest (MO), wordt getest: op welke niveaus, tegen welke eisen, met welke testgevallen en wanneer een versie mag worden opgeleverd. Het koppelt de testnummers uit de Technische SRS aan de tests die in de broncode bestaan, en maakt zichtbaar wat nog niet getest is.

| Gegeven | Waarde |
| --- | --- |
| Documenttype | Testplan |
| Competentie | Realisation (teststrategie ook Design) |
| Deelvraag | 5 - in welke mate voldoet de gerealiseerde oplossing aan de requirements en kwaliteitscriteria |
| Auteur | Ardit Fazliji |
| Versie | 0.1 (concept) |
| Datum | 8 oktober 2026 |
| Status | Concept |
| Testobject | Tutti-module versie 00.03.01, repository `Dnn.Modules.Tutti`, branch `feature/module-scaffold` |
| Gerelateerd | [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>) (hoofdstuk 18 en 22), [Architectuurontwerp - Tutti](<../03. Design/Architectuurontwerp.md>), [Backendontwerp - Tutti](<../03. Design/Backendontwerp.md>) |

## Inhoud

1. [Doel en scope](#1-doel-en-scope)
2. [Teststrategie](#2-teststrategie)
3. [Testgevallen](#3-testgevallen)
4. [Testdata en omgeving](#4-testdata-en-omgeving)
5. [Criteria](#5-criteria)
6. [Beperkingen en vervolgstappen](#6-beperkingen-en-vervolgstappen)

## 1. Doel en scope

### 1.1 Doel

De Technische SRS beschrijft in hoofdstuk 18 een teststrategie en in hoofdstuk 22 testnummers (`TC-021` t/m `TC-091`), maar zegt in 22 zelf dat die nummers "nog niet aan een testplan gekoppeld" zijn. Dit testplan maakt die koppeling. Per testgeval staat welke geautomatiseerde test of handmatige controle het aantoont, of dat het nog ontbreekt. Daarmee is dit plan de basis voor het testrapport, dat deelvraag 5 beantwoordt.

### 1.2 Wat wordt getest

Het testobject is de eigen DNN-module van Tutti zoals die op 8 oktober 2026 in versie 00.03.01 is gebouwd:

- de bedrijfsregels en de servicelaag (`Tutti.Core`): zichtbaarheid en rechten, groeperingsregel, conflictdetectie, wijzigingshistorie, audittrail, agenda-export;
- de datalaag: migrations, repositories en het lezen van de OPAS-tabellen;
- de koppeling met de SharePoint Viewer voor documenten en bladmuziek;
- de API-laag (foutafhandeling, statuscodes) en de schermen (Razor-views, adressen, teksten in het Nederlands en Engels);
- de installatie, upgrade en verwijdering van de module.

### 1.3 Wat niet wordt getest

| Onderdeel | Reden |
| --- | --- |
| Inloggen via M365 (`AUTH-001`, `TC-021`) | Tutti bouwt geen eigen login; het inloggen doet DNN op Metrostation. Tutti test alleen wat er gebeurt met een ingelogde of niet-ingelogde gebruiker. |
| Afspraak kopiëren (`TC-032`), drag-and-drop (`TC-034`), openstaande posities (`TC-035`), bladmuziekrevisies (`TC-053`), meldingen (`TC-061`) | Deze functionaliteiten zijn in versie 00.03.01 niet gebouwd. Ze krijgen een test wanneer ze worden gebouwd. |
| AFAS-koppeling | Niet gebouwd; de health check meldt AFAS als "NotConfigured". |
| De SharePoint Viewer zelf en SharePoint | Dat is een andere 2sxc-app. Tutti test de koppeling ermee, tegen een nagebootste viewer. |
| De OPAS-import (scheduled task) | Die is niet van Tutti. Tutti test alleen het lezen van de tabellen die de import vult. |

### 1.4 Kwaliteitsaspecten

Het afstudeervoorstel (§4.4) noemt vijf kwaliteitsaspecten. Tabel 1 laat zien hoe elk aspect in dit plan wordt aangetoond.

**Tabel 1. Kwaliteitsaspecten en hoe ze getest worden**

| Kwaliteitsaspect | Hoe getest | Testgevallen |
| --- | --- | --- |
| Functionaliteit | Unit- en integratietests van de bedrijfsregels en de datalaag; handmatige controlelijst op de ontwikkelsite | `TC-031` t/m `TC-054` |
| Security | Permissietests per rol, inclusief de gevallen die moeten falen; tests op foutmeldingen zonder interne details | `TC-022`, `TC-023`, `TC-101`, `TC-107` |
| Onderhoudbaarheid en herbruikbaarheid | Migrations die twee keer kunnen draaien; controle dat elke migration in het manifest staat; ERD in de documentatie gelijk aan het echte schema | `TC-106`, `TC-109` |
| Performance | Nog niet gemeten (zie 6.1) | `TC-081` |
| Gebruiksvriendelijkheid en toegankelijkheid | Handmatige controles; nog geen geautomatiseerde toegankelijkheidstest (zie 6.1) | `TC-091`, `TC-112`, `TC-113` |

## 2. Teststrategie

### 2.1 Testniveaus

De strategie volgt de niveaus uit de Technische SRS (hoofdstuk 18), aangepast aan wat er in de broncode staat. Tabel 2 noemt per niveau de testprojecten en de omgeving.

**Tabel 2. Testniveaus**

| Niveau | Wat | Hulpmiddel | Omgeving | Wanneer |
| --- | --- | --- | --- | --- |
| Unit | Bedrijfsregels en services, los van database en DNN: rechten per rol, groeperingsregel, conflictdetectie, wijzigingshistorie, documenten, agenda-export, OPAS-regels, instellingen | xUnit, project `Tutti.Core.Tests`, met nagebootste repositories (`Fakes.cs`) | Elke ontwikkelmachine en CI | Elke build |
| Integratie database | Migrations (twee keer achter elkaar, en een upgrade vanaf een oud schema), repositories, de SQL-zichtbaarheidsregels, het lezen van de OPAS-tabellen, verwijderen van de module | xUnit, project `Dnn.Modules.Tutti.IntegrationTests` | SQL Server LocalDB, wegwerpdatabase per run | Elke build |
| Koppeling SharePoint Viewer | Wat Tutti doet bij elk antwoord van de viewer: lijst, bestand in delen, weigering, time-out, storing | xUnit met een nagebootste viewer (`FakeViewer`) over echte HTTP | LocalDB + lokale HTTP-server | Elke build |
| API en presentatie | Foutafhandeling en statuscodes van de API, compilatie van alle Razor-views, teksten in beide talen, korte adressen | xUnit (`ApiErrorFilterTests`, `ApiErrorPipelineTests`, `RazorViewCompilationTests`, `PresentationTests`, `TuttiUrlTests`) | Elke ontwikkelmachine en CI | Elke build |
| Documentatie | Het ERD in de documentatie komt overeen met het geïnstalleerde schema; links tussen de datamodeldocumenten werken | xUnit (`DataModelDocTests`) | LocalDB | Elke build |
| Echte OPAS-data | Alle producties en afspraken uit de echte OPAS-tabellen worden zonder fouten gelezen | xUnit (`LiveOpasDatabaseTests`), alleen met een ingestelde verbinding | Lokale kopie van Metrostation | Handmatig, voor een versie die OPAS raakt |
| Handmatig op de ontwikkelsite | Tien controles na installatie: overzicht, agenda, projectkamer, afspraak, documenten, openen en downloaden, als musicus, als remplaçant, health check, beheer | Controlelijst "Checking a site by hand" in de README van de repository | Lokale kopie van Metrostation (DNN 9.13.2) | Na elke installatie van een nieuwe versie |
| Acceptatie en gebruikersvalidatie | Het acceptatiescenario uit de Technische SRS (18.2) met de planning van het MO | Handmatig | `[AANVULLEN: acceptatieomgeving]` | `[AANVULLEN: wanneer en met wie]` |

### 2.2 Automatisering en CI

Alle geautomatiseerde tests draaien in GitHub Actions bij elke push naar `main` en bij elke pull request (`.github/workflows/build.yml`). De workflow bouwt de oplossing, start LocalDB, draait alle tests en bewaart daarna het installatiepakket. Een mislukte stap stopt de workflow, dus er komt geen pakket van een build met een falende test (eis `DEP-007`).

Twee controles zitten al in de build zelf. Een compilerwaarschuwing breekt de build (`TreatWarningsAsErrors`), en de NuGet-audit weigert pakketten met bekende kwetsbaarheden.

### 2.3 Keuzes in deze strategie

- **Unittests tegen nagebootste repositories, integratietests tegen een echte database.** De regels over wie wat ziet staan twee keer in de code: in C# voor één afspraak en in SQL voor lijsten. Daarom wordt dezelfde regel op beide niveaus getest, bijvoorbeeld `TC-101`.
- **Een nagebootste SharePoint Viewer in plaats van de echte.** De echte viewer werkt alleen met een ingelogde gebruiker op Metrostation en een verbinding met SharePoint. Met de nagebootste viewer zijn ook storingen, time-outs en vreemde antwoorden te testen, wat met de echte niet kan.
- **Nog geen end-to-end UI-tests.** De Technische SRS noemt Playwright of iets vergelijkbaars. Dat is nog niet ingericht. De schermen worden nu gedekt door de compilatie- en presentatietests en de handmatige controlelijst. `[AANVULLEN: besluit of en wanneer end-to-end UI-tests worden toegevoegd]`

## 3. Testgevallen

### 3.1 Testgevallen uit de Technische SRS

Tabel 3 koppelt de testnummers uit hoofdstuk 22 van de Technische SRS aan de tests in de broncode. De kolom **Test** noemt de naam van de test zoals die in de code staat, zodat die terug te vinden is.

**Tabel 3. Testgevallen uit de Technische SRS**

| ID | Eis | Scenario | Verwacht resultaat | Test | Dekking |
| --- | --- | --- | --- | --- | --- |
| TC-021 | `AUTH-001` Inloggen via M365 | - | - | Buiten Tutti (zie 1.3) | n.v.t. |
| TC-022 | `AUTH-007` Remplaçant ziet alleen toegewezen producties | Een remplaçant vraagt een productie op waaraan die niet is toegewezen, en een OPAS-productie met een bijna gelijk productienummer | Niet gevonden (404); alleen de productie waarvoor de remplaçant is ingehuurd is zichtbaar | `A_substitute_sees_only_the_production_they_were_hired_for`, `A_substitute_hired_for_2605M1_does_not_get_2605M10` | Geautomatiseerd |
| TC-023 | `AUTH-008` Bladmuziek gefilterd op instrument | Een musicus probeert een partij te openen die de viewer voor het eigen instrument niet toont, met het id van een collega | Geweigerd; de partij staat niet in de lijst | `A_part_the_viewer_hides_from_this_musician_cannot_be_opened_with_a_colleagues_id`, `A_folder_the_visitors_instrument_does_not_reach_is_refused` | Geautomatiseerd |
| TC-031 | `PLN-001` Afspraak aanmaken | Een planner maakt een afspraak aan zonder titel of tijd, of met een einde vóór het begin | Validatiefout per veld; er wordt niets opgeslagen | `An_event_without_title_type_or_time_frame_reports_every_field_at_once`, `An_event_that_ends_before_it_starts_is_rejected_with_its_own_code` | Geautomatiseerd |
| TC-032 | `PLN-002` Afspraak kopiëren | - | - | Niet gebouwd | Open |
| TC-033 | `PLN-003` Dag-, week- en maandweergave | De maand en de dag worden opgebouwd uit de afspraken van een periode | Maand in hele weken met ISO-weeknummers; overlappende afspraken naast elkaar in de dagweergave | `The_month_view_is_a_grid_of_whole_weeks_with_iso_week_numbers`, `The_day_view_places_events_by_time_and_puts_overlapping_ones_side_by_side` | Geautomatiseerd |
| TC-034 | `PLN-005` Drag-and-drop | - | - | Niet gebouwd | Open |
| TC-035 | `PLN-006` Openstaande posities | - | - | Niet gebouwd | Open |
| TC-036 | `PLN-007` Aanwezigheid bijhouden | Een planner zet een musicus op ziek | Opgeslagen en vastgelegd in de audittrail | `Every_change_of_attendance_is_audited` | Geautomatiseerd |
| TC-038 | `PLN-004` Dubbele inzet markeren | Een musicus wordt op twee overlappende afspraken gezet | Waarschuwing, maar het opslaan gaat door; geannuleerde afspraken en verschillende zalen geven geen conflict | `A_double_booking_is_flagged_but_never_blocked`, `Cancelled_events_and_different_venues_do_not_conflict` | Deels (zie 6.2) |
| TC-041 | `EVT-001` Kleur per type, groepering | Een bus en een soundcheck vlak voor een concert | Ze krijgen de kleur van het concert; een losse bus houdt zijn eigen kleur | `Bus_and_soundcheck_before_a_concert_inherit_the_concert_colour`, `A_standalone_transport_keeps_its_own_colour` | Geautomatiseerd |
| TC-042 | `EVT-002` Type afgeleid uit `EventText` | Een OPAS-afspraak met een tekst als "repetitie" | Het type komt uit de tekst, niet uit het projecttype | `The_type_of_an_event_is_read_from_what_the_planning_wrote` | Geautomatiseerd |
| TC-043 | `EVT-003` Lege afspraken niet tonen | Een OPAS-rij zonder project, tijd of tekst | Wordt niet getoond | Geen gerichte test; het filter staat in de code | Open (zie 6.2) |
| TC-051 | `DOC-001` Documenten per productie | Een musicus opent het tabblad Documenten van een productie | De bestanden onder het productienummer, gegroepeerd; niets bij een productie die niet gepubliceerd is | `The_documents_of_an_opas_production_are_found_by_its_production_number`, `A_musician_gets_no_documents_of_a_production_that_is_not_published` | Geautomatiseerd |
| TC-052 | `INT-001` (§22) Agenda exporteren naar Outlook | Een musicus exporteert de eigen agenda | Een geldig .ics-bestand met precies de eigen gepubliceerde diensten | `A_musician_exports_exactly_their_own_published_services` | Geautomatiseerd |
| TC-054 | `INT-002` (§22) Wijzigingen uit OPAS verwerken | OPAS markeert een afspraak als geannuleerd en registreert wijzigingen | Geannuleerd getoond; wijzigingen nieuwste eerst | `An_event_is_cancelled_when_opas_says_anything_but_no`, `The_changes_the_import_saw_are_shown_newest_first_in_its_own_words` | Geautomatiseerd |
| TC-071 | `LOG-006` Rolwijzigingen vastgelegd | Een planner geeft iemand toegang tot een productie | Vastgelegd als rechtenwijziging in de audittrail | `Giving_someone_access_to_a_production_is_audited_as_a_permission_change` | Geautomatiseerd |
| TC-081 | `PERF-002` Maandweergave binnen 2 seconden | - | - | Niet gemeten | Open (zie 6.1) |
| TC-091 | `ACC-003` Kleur nooit het enige onderscheid | Legenda en afspraken bekijken | Type staat ook als tekst; geannuleerd ook als label | Handmatig | Handmatig |

`INT-001` en `INT-002` betekenen in hoofdstuk 8 en hoofdstuk 22 van de Technische SRS iets anders. Dit plan bedoelt de betekenis uit hoofdstuk 22.

### 3.2 Verplichte tests uit de Technische SRS

Hoofdstuk 18.1 van de Technische SRS stelt zeven eisen aan de testsuite. Tabel 4 laat zien hoe ver de suite daaraan voldoet.

**Tabel 4. Verplichte tests**

| ID | Eis (kort) | Hoe gedekt | Stand |
| --- | --- | --- | --- |
| TST-001 | Elke bedrijfsregel heeft een unittest | Tests voor de groeperingsregel, de conflictdetectie en de beschrijving van wijzigingen | Gedekt |
| TST-002 | Permissietest per endpoint per rol, inclusief wat moet falen | Permissietests per rol op de servicelaag, waar de rechten worden beslist; niet per endpoint via HTTP | Deels |
| TST-003 | Migrations op een lege én een gevulde database | Alle migrations twee keer op een lege database; een upgrade van een oud schema met gegevens | Gedekt |
| TST-004 | OPAS-import met vervuilde records | Lege velden en dubbele rijen worden goed gelezen; lege afspraken niet gericht getest (`TC-043`) | Deels |
| TST-005 | Regressietest voor elke bug uit productie | Werkwijze; Tutti draait nog niet in productie | n.v.t. |
| TST-006 | Toegankelijkheid per release getoetst | Geen geautomatiseerde toets | Open |
| TST-007 | Performance per release gemeten | Geen meting | Open |

### 3.3 Aanvullende testgevallen

Op 8 oktober 2026 heb ik met behulp van AI een kwaliteitsreview van Tutti uitgevoerd op negen gebieden, waaronder security, betrouwbaarheid, privacy en toegankelijkheid. De belangrijkste bevindingen zijn opgelost in versie 00.03.01. Tabel 5 noemt de testgevallen die daarbij en eerder zijn toegevoegd en die niet in de Technische SRS staan. Ze krijgen nummers vanaf `TC-101`, zodat ze niet botsen met de nummers uit de SRS.

**Tabel 5. Aanvullende testgevallen**

| ID | Eis | Scenario | Verwacht resultaat | Test | Dekking |
| --- | --- | --- | --- | --- | --- |
| TC-101 | Zichtbaarheid per productie | Een productieleider die in productie A lid is en in productie B alleen meespeelt | Concepten en de hele bezetting alleen in A; in B alleen het gepubliceerde en de eigen rij | `A_production_lead_sees_concepts_and_the_whole_line_up_only_where_they_plan`, `Concepts_show_only_in_the_productions_the_caller_plans_as_a_member` | Geautomatiseerd |
| TC-102 | Afspraak niet dubbel na een time-out | Hetzelfde verzoek om een afspraak aan te maken komt twee keer binnen met dezelfde sleutel | Eén afspraak; het tweede verzoek krijgt de eerste terug | `An_event_sent_again_with_the_same_request_key_is_created_once`, `A_request_key_creates_one_event_and_finds_it_again_for_the_same_user_only` | Geautomatiseerd |
| TC-103 | Groot bestand in delen | Een browser haalt een bestand in drie delen op | SharePoint wordt één keer doorzocht; een andere gebruiker zoekt zelf | `The_parts_of_one_file_search_the_store_once_and_each_user_searches_for_themselves` | Geautomatiseerd |
| TC-104 | Viewer die blijft falen | Vijf keer achter elkaar een time-out van de viewer | Tutti stopt 30 seconden met aanroepen en meldt direct "niet beschikbaar"; een weigering telt niet mee | `A_store_that_keeps_failing_is_left_alone_for_a_while_and_then_tried_again` | Geautomatiseerd |
| TC-105 | Audittrail blijft na verwijderen | De module wordt verwijderd en opnieuw geïnstalleerd | Alle tabellen weg behalve de audittrail, die nog steeds niet te wijzigen is | `Uninstalling_drops_the_planning_but_keeps_the_audit_trail_and_a_reinstall_works` | Geautomatiseerd |
| TC-106 | Elke migration wordt uitgevoerd | Een migration staat in de map maar niet in het manifest | De test faalt | `Every_migration_script_is_registered_in_the_manifest_and_none_is_newer_than_the_version` | Geautomatiseerd |
| TC-107 | Audittrail kan niet worden aangepast | Een rij in de audittrail wijzigen of verwijderen | De database weigert | `The_audit_trail_stores_json_and_cannot_be_changed_or_deleted` | Geautomatiseerd |
| TC-108 | Gelijktijdig wijzigen | Twee planners slaan dezelfde afspraak op met een verouderde versie | Conflict (409); er wordt niets opgeslagen | `A_stale_row_version_is_a_concurrency_conflict_and_nothing_is_committed` | Geautomatiseerd |
| TC-109 | Documentatie klopt met het schema | Een kolom wordt toegevoegd zonder het ERD aan te passen | De test faalt | `The_erd_in_the_docs_matches_the_installed_schema` | Geautomatiseerd |
| TC-110 | Storing van de viewer raakt de rest niet | De viewer is niet bereikbaar | De OPAS-planning werkt; de documenten melden dat ze niet beschikbaar zijn | `When_the_viewer_is_down_opas_still_works_and_the_documents_say_so` | Geautomatiseerd |
| TC-111 | Health check voor monitoring | Niet ingelogd `GET /API/Tutti/v1/health/ready` aanroepen | `{"status":"Healthy"}` met status 200, geen versie of details | Handmatig na installatie | Handmatig |
| TC-112 | Bezetting opslaan zonder herladen | Een planner slaat de aanwezigheid van één musicus op | De pagina blijft staan, de focus blijft op de knop, de melding staat bij die rij | Handmatig na installatie | Handmatig |
| TC-113 | Paginatitel per scherm | Een afspraak of productie openen | De titel van het browsertabblad noemt de afspraak of productie | Handmatig na installatie | Handmatig |

`TC-111` t/m `TC-113` hangen af van DNN of van de browser. Daarom zijn ze alleen op de ontwikkelsite te controleren, door iemand die kan inloggen.

### 3.4 Acceptatiescenario

Het acceptatiescenario uit de Technische SRS (18.2) loopt van "planner maakt productie aan" tot "remplaçant ziet deze productie niet". In versie 00.03.01 staat het scherm Beheer uit, zodat een planner producties nog niet via de schermen kan aanmaken. Het scenario kan daarom nog niet volledig met het MO worden doorlopen. `[AANVULLEN: wanneer het acceptatiescenario met de planning van het MO wordt uitgevoerd]`

## 4. Testdata en omgeving

### 4.1 Testdata

- **Unittests:** in het geheugen opgebouwde producties, afspraken en personen met verzonnen namen (bijvoorbeeld "Jan de Vries") en verzonnen productienummers (2637M1, 2603M1).
- **Integratietests:** een nieuwe LocalDB-database per run, met de echte migrations, de DNN-tabellen die Tutti leest en een kopie van de definitie van de OPAS-tabellen. De database wordt na de run verwijderd. Er staan geen echte persoonsgegevens in.
- **Echte OPAS-data:** alleen in `LiveOpasDatabaseTests`, tegen de lokale kopie van Metrostation. Die tests draaien alleen als de verbinding is ingesteld, lezen alleen en bewaren niets. De verbindingsgegevens staan niet in de repository.

### 4.2 Omgevingen

| Omgeving | Wat | Gebruikt voor |
| --- | --- | --- |
| Ontwikkelmachine | .NET SDK 10, SQL Server LocalDB | Alle geautomatiseerde tests |
| CI | GitHub Actions, Windows-runner, .NET 10, LocalDB | Alle geautomatiseerde tests bij push en pull request |
| Ontwikkelsite | Lokale kopie van Metrostation (DNN 9.13.2) met de echte OPAS-tabellen en de SharePoint Viewer | Handmatige controlelijst, `TC-111` t/m `TC-113`, echte OPAS-data |
| Acceptatie | `[AANVULLEN: acceptatieomgeving van het MO]` | Acceptatiescenario |

## 5. Criteria

### 5.1 Wanneer is een test geslaagd

Een geautomatiseerde test is geslaagd als hij groen is in CI. Een handmatige controle is geslaagd als het resultaat overeenkomt met de kolom "Verwacht resultaat" of met de controlelijst in de README van de repository.

### 5.2 Wanneer mag een versie worden opgeleverd

Een nieuwe versie van Tutti gaat pas naar een site als aan alle punten hieronder is voldaan:

1. De build heeft geen waarschuwingen en de NuGet-audit vindt geen kwetsbaarheden.
2. Alle geautomatiseerde tests zijn groen in CI.
3. Een nieuwe migration staat in het manifest en de versie is opgehoogd (`TC-106`).
4. Na installatie op de ontwikkelsite is de handmatige controlelijst doorlopen, inclusief de handmatige testgevallen van die versie.
5. Er zijn geen openstaande bevindingen met prioriteit Critical of High uit een review, of ze zijn bewust geaccepteerd en vastgelegd.

Dit sluit aan op de definition of done uit de Technische SRS (18.3). Daar staat ook dat een item op acceptatie aan de aanvrager is getoond. Dat gebeurt nog niet (zie 3.4).

### 5.3 Als een test faalt

De fout wordt opgelost voordat de versie wordt opgeleverd. Een fout die buiten een test om is gevonden, krijgt eerst een test die de fout laat zien en daarna de oplossing (`TST-005`).

## 6. Beperkingen en vervolgstappen

### 6.1 Wat dit plan nog niet dekt

- **Performance (`TST-007`, `TC-081`):** er is nog geen meting. Nodig is een vaste dataset van meerdere seizoenen en een meting van de maandweergave en de documentenlijst.
- **Toegankelijkheid (`TST-006`):** er is geen geautomatiseerde toets. De review van 8 oktober 2026 heeft de schermen alleen in de code beoordeeld.
- **Het script in de browser:** het script (`tutti.mvc.js`) heeft geen eigen tests. De wijzigingen van 8 oktober 2026 zijn eenmalig in een browser zonder venster gecontroleerd, met nagebootste API-antwoorden. Dat is geen herhaalbare test in CI.
- **Permissietests per endpoint (`TST-002`):** de rechten worden getest op de servicelaag, niet per endpoint via HTTP.
- **Gebruikersvalidatie:** nog niet uitgevoerd (zie 3.4).

### 6.2 Bekende bevindingen die nog een test nodig hebben

De kwaliteitsreview van 8 oktober 2026 vond ook fouten in de functionaliteit die nog niet zijn opgelost. Elk van deze punten krijgt eerst een test die de fout laat zien (`TST-005`):

- Conflicten bij dubbele inzet worden niet getoond op de pagina van één afspraak, en op het beheerscherm alleen binnen dezelfde productie (`TC-038`).
- Als bij een gepubliceerde afspraak zowel het begin als het einde op dezelfde dag verandert, staat alleen de wijziging van het begin in de wijzigingshistorie.
- Wijzigingen aan een gepubliceerde afspraak in een productie die nog concept is, komen later in de wijzigingshistorie van musici terecht.
- Lege OPAS-afspraken worden wel weggefilterd, maar daar is geen gerichte test voor (`TC-043`, `TST-004`).

### 6.3 Vervolgstappen

1. De handmatige testgevallen `TC-111` t/m `TC-113` uitvoeren na installatie van versie 00.03.01 op de ontwikkelsite.
2. Regressietests schrijven voor de bevindingen in 6.2 en de fouten oplossen.
3. Een performancemeting en een toegankelijkheidstoets toevoegen.
4. Het acceptatiescenario met de planning van het MO plannen.
5. De resultaten vastleggen in een testrapport in deze map.
