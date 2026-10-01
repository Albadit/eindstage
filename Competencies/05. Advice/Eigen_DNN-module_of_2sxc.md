# Eigen DNN-module of 2sxc

*Adviesrapport · Tutti · Metropole Orkest*

Een onderbouwde afweging voor de doorontwikkeling van Tutti, dat verschuift van een agenda op het intranet naar een applicatie met producties, bezettingen, bladmuziek en rechten - inclusief de nadelen van zelfbouw en de situaties waarin 2sxc juist de betere keuze blijft.

- **Project:** Tutti - planning & administratie
- **Platform:** Metrostation (DNN)
- **Datum:** 14 september 2026
- **Advies:** Eigen DNN-module, 2sxc voor content

---

## Inhoud

- [Managementsamenvatting](#managementsamenvatting-waar-het-advies-op-neerkomt)
- [1 · Inleiding](#hoofdstuk-1-inleiding)
- [2 · De twee opties](#hoofdstuk-2-de-twee-opties-technisch-beschreven)
- [3 · Analyse per criterium](#hoofdstuk-3-analyse-per-criterium)
  - [3.1 Controle](#31-controle-over-architectuur-en-code)
  - [3.2 Bedrijfslogica](#32-complexe-bedrijfslogica)
  - [3.3 Vrijheid voor beheerders](#33-minder-vrijheid-voor-beheerders-is-hier-een-voordeel)
  - [3.4 Kans op fouten](#34-kans-op-fouten)
  - [3.5 Database](#35-database-en-datastructuur)
  - [3.6 Security](#36-security-en-autorisatie)
  - [3.7 Testbaarheid](#37-testbaarheid)
  - [3.8 Debugging](#38-debugging-en-foutanalyse)
  - [3.9 Performance](#39-performance)
  - [3.10 Schaalbaarheid](#310-schaalbaarheid)
  - [3.11 API-first](#311-api-first-en-toekomstige-frontends)
  - [3.12 AI-ondersteuning](#312-ai-ondersteunde-ontwikkeling)
  - [3.13 Vendor lock-in](#313-vendor-lock-in-en-afhankelijkheden)
  - [3.14 Kennis in het team](#314-kennis-binnen-het-ontwikkelteam)
- [4 · Tijd en kosten](#hoofdstuk-4-ontwikkeltijd-en-kosten)
- [5 · Nadelen en risico's](#hoofdstuk-5-nadelen-en-risicos-van-een-eigen-module)
- [6 · Waar 2sxc wint](#hoofdstuk-6-waar-2sxc-juist-de-betere-keuze-is)
- [7 · Vergelijkingstabel](#hoofdstuk-7-vergelijkingstabel)
- [8 · Eindadvies](#hoofdstuk-8-eindadvies)
- [Bijlagen](#bijlagen-verantwoording)

---

## Managementsamenvatting - Waar het advies op neerkomt

Tutti verschuift van "een agenda op het intranet" naar "een applicatie met een domein": producties met een productienummer, afspraken met een soort en een status, bezettingen per afspraak, bladmuziek die per musicus op instrument gefilterd wordt, documenten, wijzigingshistorie, een enquête met een tijdvenster, en rollen die van elkaar verschillen - vaste musici, remplaçanten, planners, productie en beheerders. Die verschuiving verandert wat een goed platform is. 2sxc is uitstekend in het eerste en wordt bewerkelijk in het tweede.

Het advies is daarom om **de bedrijfslogica van Tutti onder te brengen in een eigen DNN-module**, met een eigen relationeel databaseschema voor producties, afspraken en bezettingen, een servicelaag waarin de zichtbaarheidsregels centraal staan, en een REST-API als enige ingang. 2sxc blijft in beeld voor wat het op Metrostation goed doet: nieuwsberichten, praktische informatie en de uitgelicht-blokken die de redactie zelf beheert.

Dit is geen gratis keuze. Een eigen module kost aan het begin meer tijd, legt de verantwoordelijkheid voor security en migraties bij het ontwikkelteam, en levert code op die onderhouden moet worden. Het rapport benoemt die nadelen expliciet en beschrijft per nadeel hoe het risico beperkt kan worden.

- **Hoofdadvies:** Eigen module voor het Tutti-domein
- **Nuance:** 2sxc houden voor redactionele content
- **Grootste risico:** Zelfgebouwde rechten op bladmuziek
- **Terugverdientijd:** Indicatief 1 à 2 jaar

## Hoofdstuk 1 - Inleiding

### 1.1 Aanleiding

Tutti is de nieuwe planning- en administratie-app van het Metropole Orkest. Het eerste onderdeel - de agenda binnen het intranet Metrostation - draait op DNN (DotNetNuke) en is met **2sxc** gebouwd. Voor de verdere ontwikkeling ligt de keuze voor: doorgaan op 2sxc, of **een eigen DNN-module** ontwikkelen.

De aanleiding voor die vraag is dat de aard van de functionaliteit verschuift. De eerste versies gingen over het tonen van afspraken uit OPAS in vier weergaven: maand, week, dag en lijst. Wat er nu op de rol staat, is van een andere orde:

- een **beheeromgeving** waarin het productieteam producties aanmaakt en afspraken bij een productienummer inplant, met een actief-veld en een aanpas- en verwijderfunctie;
- **bladmuziek per musicus** - alleen de partij die bij het instrument van die speler hoort;
- **documenten** die via SharePoint of ADAM bij de productie komen, met versies en toegangsrechten;
- een **wijzigingshistorie** per afspraak en per productie, zichtbaar voor wie het aangaat;
- een **enquête per productie** die pas 24 uur na afloop open mag en tot drie maanden daarna, met antwoorden die per productienummer én per persoon inzichtelijk moeten zijn;
- **rollen die van elkaar verschillen**: vaste musici, remplaçanten, planners, het productieteam en beheerders zien niet hetzelfde.

Naarmate dat toeneemt, verandert de aard van het systeem. Een agendaweergave met een handvol velden is iets anders dan een applicatie waarin de vraag "mag deze musicus deze partij zien?" van vier of vijf factoren tegelijk afhangt. Dit rapport onderzoekt welke van de twee richtingen voor Tutti het meest geschikt is voor de verdere ontwikkeling en het onderhoud.

### 1.2 Onderzoeksvraag

**Waarom is het ontwikkelen van een eigen DNN-module voor Tutti een betere keuze dan doorgaan met 2sxc - technisch, en op het gebied van beheer, veiligheid, onderhoudbaarheid, schaalbaarheid en toekomstige ontwikkeling?**

De vraag is bewust gericht geformuleerd, maar het onderzoek is dat niet: het rapport benoemt ook de situaties waarin het antwoord de andere kant op wijst, en sluit af met de voorwaarden waaronder het advies herzien zou moeten worden.

### 1.3 Aanpak en verantwoording

Het rapport is opgebouwd uit drie soorten materiaal, die door het hele document herkenbaar gelabeld zijn:

- **Feiten** - controleerbare eigenschappen van DNN, 2sxc en .NET, met bronvermelding in bijlage B, plus wat er in Basecamp over Tutti is vastgelegd.
- **Technische argumenten** - redeneringen die uit die feiten volgen en uit gangbare software-engineeringpraktijk.
- **Aannames** - dingen die niet gemeten zijn maar wel meewegen, zoals ontwikkelsnelheid en teamsamenstelling.

Daarnaast zijn **risico's** en **adviezen** apart gemarkeerd. Waar een uitspraak niet hard te maken is, staat dat er ook.

> **AANNAME**
>
> Het rapport gaat uit van een klein ontwikkelteam bij een DNN-specialist, met stevige C#-, SQL- en JavaScript-kennis en werkende kennis van DNN, waarbij 2sxc-ervaring aanwezig maar niet diepgaand is. Verandert die samenstelling, dan verschuift vooral hoofdstuk 4 (tijd en kosten) en paragraaf 3.14.

### 1.4 Begrippen

- **Tutti** - de nieuwe planning- en administratie-app van het Metropole Orkest, waarvan de agendamodule inmiddels op Metrostation draait.
- **Metrostation** - het DNN-intranet van het Metropole Orkest waarin Tutti draait.
- **OPAS** - het huidige planningssysteem waaruit productienummers, afspraken, tijden, locaties en dirigenten worden geïmporteerd.
- **Afspraak** - een evenement in de agenda: concert, repetitie, soundcheck, vervoer, opname of educatie.
- **Eigen DNN-module** - een zelfgebouwde DNN-extensie in C#, met eigen databasetabellen, een eigen servicelaag, eigen API-endpoints en een eigen frontend, die gebruikmaakt van DNN voor hosting, authenticatie en de basisrollen.
- **2sxc** - een CMS- en meta-plugin voor DNN en Oqtane waarmee contenttypes, data, views en apps grotendeels via configuratie en templates worden opgebouwd.
- **EAV** - Entity-Attribute-Value: een opslagmodel waarin veldwaarden als rijen in generieke tabellen staan in plaats van als kolommen in een tabel per entiteit.
- **Configuratiedrift** - het verschijnsel dat omgevingen (ontwikkel, test, productie) uit elkaar gaan lopen doordat instellingen op de ene omgeving wel en op de andere niet zijn aangepast.

## Hoofdstuk 2 - De twee opties technisch beschreven

### 2.1 Wat 2sxc is

2sxc is geen simpele module maar een platform bovenop DNN. De belangrijkste onderdelen, vertaald naar Tutti:

- **Contenttypes** - datastructuren die een beheerder in de browser aanmaakt en wijzigt; voor Tutti zouden dat `Productie`, `Afspraak`, `Bezetting` en `Bladmuziekpartij` zijn.
- **EAV-opslag** - die data komt terecht in het generieke EAV-datamodel van 2sxc, niet in een tabel per entiteit.
- **Views en templates** - Razor- of Token-templates; in Tutti de maand-, week-, dag- en lijstweergave en de detailpagina's.
- **DataSources en queries** - een visueel koppelbaar systeem om data te filteren en sorteren, bijvoorbeeld "afspraken van deze productie, op datum".
- **Apps** - een verpakking van contenttypes, views, queries en code die geïmporteerd en geëxporteerd kan worden.
- **REST-API** - 2sxc biedt een headless API op de eigen data, plus de mogelijkheid eigen Razor-WebAPI-controllers te schrijven.

> **FEIT**
>
> 2sxc is open source, wordt ontwikkeld door 2sic en draait op zowel DNN als Oqtane. De actuele documentatie beschrijft versie 22; de laatste LTS-versie op moment van schrijven is 21.07.00 (2 april 2026). Er is een patron-/sponsormodel waarbij een deel van de aanvullende functionaliteit alleen beschikbaar is voor financiële ondersteuners.

Belangrijk om vast te stellen: 2sxc is volwassen, breed gebruikt en goed gedocumenteerd, en de huidige agendamodule van Tutti is er in korte tijd mee gebouwd. De bezwaren in dit rapport gaan niet over kwaliteit, maar over de *match* tussen het model van 2sxc en het type applicatie dat Tutti aan het worden is.

### 2.2 Wat een eigen DNN-module is

Een eigen Tutti-module bestaat in de kern uit vier lagen die allemaal in de eigen repository staan:

- **Datalaag** - tabellen als `Productie`, `Afspraak`, `Bezetting`, `Instrument`, `Partij`, `Document` en `Wijziging`, met primary keys, foreign keys, constraints en indexes, beheerd via migrations.
- **Domein- en servicelaag** - services als `AgendaService`, `BezettingService` en `ZichtbaarheidService`, waarin de bedrijfsregels en de autorisatiebeslissingen staan.
- **API-laag** - DNN WebAPI-controllers die die services ontsluiten, met authenticatie via DNN en M365-SSO.
- **Frontend** - de agendaweergaven en detailpagina's, die uitsluitend via die API praten.

DNN blijft verantwoordelijk voor hosting, portals, gebruikersaccounts, authenticatie en de basisrollen - en OPAS blijft voorlopig de bron van de planning. De module bouwt daarop voort in plaats van het over te doen.

### 2.3 De keten in beeld

Het verschil is het makkelijkst te zien aan het aantal lagen tussen een gebruikersactie - een musicus die op een afspraak klikt - en de database, en aan hoeveel van die lagen tijdens runtime door configuratie bepaald worden (geel gemarkeerd):

**Keten bij 2sxc**

`Musicus  →  Configuratie  →  Contenttype  →  Template  →  JavaScript / API  →  2sxc / EAV  →  DNN  →  Database`

*Buiten de broncode aanpasbaar: Configuratie, Contenttype, Template, 2sxc / EAV.*

**Keten bij een eigen module**

`Musicus  →  Agenda-UI  →  API  →  ZichtbaarheidService  →  Repository  →  Database`

Beide ketens zijn legitiem. Het verschil is dat de bovenste keten meer schakels heeft die *buiten de broncode om* aangepast kunnen worden, en dat is precies de schakel waar in een complexe applicatie de meeste onverwachte problemen ontstaan (zie 3.4).

## Hoofdstuk 3 - Analyse per criterium

*Veertien criteria, steeds met het argument, de tegenwerping en een conclusie die zo eerlijk mogelijk is over hoe hard het punt werkelijk is. De voorbeelden komen uit Tutti zelf.*

### 3.1 Controle over architectuur en code

Bij een eigen module staat elk van de volgende onderdelen in de eigen codebase, onder versiebeheer, en kan het naar behoefte worden ingericht:

- de architectuur en de laagindeling;
- de backend-logica en de domeinmodellen (productie, afspraak, bezetting, partij);
- de frontend en de manier waarop de agendaweergaven hun data ophalen;
- de API's, hun contract en hun versionering;
- de databasestructuur en de migraties;
- autorisatie en permissions - wie welke afspraak, welk document en welke partij ziet;
- validatie van invoer bij het inplannen van een afspraak;
- foutafhandeling en de vorm van foutmeldingen;
- logging en audit trails onder de wijzigingshistorie;
- caching- en performancestrategie voor de maandweergave;
- de manier waarop toekomstige uitbreidingen worden ingepast.

Bij 2sxc is een deel hiervan een gegeven. Het datamodel is EAV, de manier waarop views aan data gekoppeld worden ligt vast, de permissions werken zoals 2sxc ze definieert, en uitbreidingen passen zich naar de conventies van het platform. Dat is geen gebrek - het is de prijs van het gemak, en bij veel projecten een goede ruil.

Het wordt pas een probleem wanneer de applicatie iets nodig heeft dat net niet in het model past. Een concreet voorbeeld uit Tutti zelf: de regel dat een soundcheck of vervoersafspraak de kleur krijgt van het concert of de repetitie ernaast. Dat is geen eigenschap van de afspraak, maar van de *volgorde* van afspraken op een dag. In 2sxc belandt zulke logica al snel in de template, terwijl het eigenlijk een domeinregel is die ook moet gelden als dezelfde data straks via een API naar een mobiele app gaat.

> **ARGUMENT**
>
> Controle is geen doel op zich. Het wordt waardevol op het moment dat je regelmatig iets moet doen wat het platform niet had voorzien. De vraag is dus niet "willen we controle", maar "hoe vaak komen we in Tutti buiten de gebaande paden". Gezien de wensenlijst uit 1.1 is het antwoord: vaak.

### 3.2 Complexe bedrijfslogica

Neem de regel die in Tutti als user story is vastgelegd voor de detailpagina: *een musicus ziet alleen de bladmuziek van de productie die bij zijn eigen instrument hoort.* Volledig uitgeschreven is dat: een partij is zichtbaar als de gebruiker op de bezettingslijst van die productie staat, én de partij bij zijn instrument en lessenaarnummer hoort, én de productie in OPAS is vrijgegeven - waarbij een remplaçant alleen de producties ziet waarvoor hij is ingehuurd, en de planner en het productieteam alles zien.

In een eigen module is dat één methode in één service - `ZichtbaarheidService.MagPartijZien(gebruiker, partij)` - met unittests en een stack trace als hij faalt. In een 2sxc-opzet is diezelfde regel realistisch verdeeld over een permissionconfiguratie op het contenttype `Bladmuziekpartij`, een filter in een query, een `if` in de Razor-template van de detailpagina en een controle in de JavaScript van de agenda. Alle vier zijn te bouwen. Het probleem is dat de regel dan nergens in zijn geheel bestaat.

Dat heeft drie concrete gevolgen:

- **Wijzigen wordt riskant.** Komt er een tweede lessenaar bij, of mag een remplaçant voortaan ook de repetitie-opname beluisteren, dan moet je vier plekken vinden. Vergeet je er één, dan is het lek niet zichtbaar in de code maar alleen in het gedrag.
- **Testen wordt lastig.** Je kunt de regel niet los aanroepen; je kunt alleen via de UI controleren of een trompettist inderdaad geen altvioolpartij ziet.
- **Overdracht wordt duur.** De volgende stagiair kan de regel niet lezen, alleen reconstrueren - en juist in dit project is die overdracht al een keer aan de orde geweest.

Hetzelfde geldt voor de andere onderdelen uit 1.1: het enquêtevenster van 24 uur tot drie maanden na afloop, het uitlichten van een concert dat buiten de eerste twee afspraken van een dag valt, het toewijzen van een remplaçant op een openstaande positie, en de statusovergang concept → review → gepubliceerd in het beheeroverzicht. Dat zijn geen contentvraagstukken maar domeinvraagstukken.

> **TEGENWERPING**
>
> Het is eerlijk om te zeggen dat 2sxc dit niet onmogelijk maakt. Met eigen C#-code in een 2sxc-app, custom DataSources en eigen WebAPI-controllers kun je een servicelaag bouwen die grotendeels dezelfde structuur heeft. Maar dan gebruik je 2sxc vooral nog als opslag- en hostingmechanisme, terwijl je de nadelen van het EAV-model (3.5) wel houdt. Op dat punt is de vraag gerechtvaardigd wat 2sxc nog toevoegt.

### 3.3 Minder vrijheid voor beheerders is hier een voordeel

Dit is het argument dat het makkelijkst verkeerd begrepen wordt, dus expliciet: het gaat er niet om het Metropole Orkest te beperken, het gaat erom **welke knoppen er zijn**.

2sxc geeft beheerders de mogelijkheid contenttypes, velden, views en configuraties aan te passen. Voor het nieuwsoverzicht op Metrostation is dat precies goed - de redactie kan zelf een veld toevoegen zonder een ontwikkelaar. Bij de agenda en het beheeroverzicht verandert het karakter van die vrijheid:

- het veld waaruit het soort afspraak wordt afgeleid hernoemen of verwijderen breekt tegelijk de kleurlogica, de afkortingen in de maandweergave en het filter op de detailpagina;
- een permissie-instelling op het contenttype `Bladmuziekpartij` aanpassen kan bladmuziek zichtbaar maken voor musici die niet op de bezettingslijst staan;
- omgevingen gaan uiteenlopen: de fout op Metrostation is niet reproduceerbaar op de testomgeving, omdat de configuratie daar anders is;
- een ontwikkelaar kan niet meer uit de code afleiden in welke staat de applicatie verkeert - dat moet per omgeving worden gecontroleerd;
- support wordt duurder, omdat elke melding vanuit het orkest begint met vaststellen hoe deze omgeving is ingericht.

Een eigen module draait dat om. Het productieteam krijgt een **ontworpen beheerinterface** - precies de schermen uit de user story "Beheeroverzicht": afspraken inplannen bij een productie, en producties aanmaken met startdatum, einddatum, naam, productienummer en een actief-veld. Instellingen die bewust bedoeld zijn om gewijzigd te worden, met validatie erop, en niets daarbuiten. Wie een veld wil toevoegen, vraagt om een wijziging - die dan via code review, tests en een release gaat.

> **RISICO**
>
> De keerzijde is reëel: elke kleine wens wordt een ontwikkelverzoek. Dat kost doorlooptijd en kan als star overkomen, zeker bij een klant die gewend is dat een aanpassing in de agenda snel geregeld is. Beperk dat door bij het ontwerp bewust te bepalen wat configureerbaar moet zijn - de kleuren per afspraaktype, de afkortingen, het tijdvenster van de enquête, het uur waarop de dagweergave opent - en dat netjes als instelling aan te bieden in plaats van het hard te coderen.

### 3.4 Kans op fouten

Terug naar de twee ketens uit 2.3. Het aantal schakels is niet het echte punt; het punt is hoeveel schakels **niet in de repository staan**. In de 2sxc-keten zijn dat er vier: de configuratie, het contenttype, de template en de EAV-laag met zijn queries. Elk daarvan kan op Metrostation een andere staat hebben dan op test, zonder dat er een commit bij hoort.

De ontwikkelhistorie van de agendamodule laat zien om wat voor soort fouten het gaat. In de eerste versies zaten onder meer: tijden die er een of twee uur naast zaten, afspraken zonder projectnaam die als `undefined` in de agenda kwamen, en lege afspraken uit de database die wel een begin- en eindtijd hadden maar verder niets. Dat zijn precies de fouten die in de data- en configuratielaag ontstaan en die pas op het scherm zichtbaar worden.

Daaruit volgen vier praktische problemen:

- **Reproduceerbaarheid.** Een bug die van configuratie afhangt, is pas te reproduceren als je die configuratie hebt overgenomen.
- **Herkomst.** Bij een verkeerde tijd op het scherm zijn er meerdere plekken waar die fout kan zijn ontstaan - de OPAS-export, de import, de tijdzone-instelling, de query of de template - en geen enkele stack trace die het aanwijst.
- **Terugdraaien.** Een codewijziging draai je terug met een revert. Een configuratiewijziging van drie weken geleden vaak niet.
- **Regressies.** Een wijziging in het gedeelde contenttype `Afspraak` raakt de maand-, week-, dag- én lijstweergave tegelijk, en er is geen compiler die je daarop wijst.

Bij een eigen module zit vrijwel alles wat gedrag bepaalt in code: typefouten vangt de compiler, gedragsfouten vangen tests, en wat er draait is exact wat in de repository staat voor die versie.

> **NUANCE**
>
> 2sxc-apps kunnen geëxporteerd en in versiebeheer opgenomen worden, en met een strikte werkwijze - nooit direct op Metrostation configureren, altijd exporteren en deployen - is een groot deel van dit risico af te dekken. Dat vraagt wel discipline die niet door het gereedschap wordt afgedwongen. Bij een eigen module dwingt het gereedschap het af.

### 3.5 Database en datastructuur

Dit is technisch gezien het sterkste argument, en tegelijk het meest genuanceerde.

2sxc slaat app-data op in een EAV-model: waarden staan als rijen in generieke tabellen, met de structuur als metadata erbij. Dat maakt het mogelijk om in de browser een veld aan `Afspraak` toe te voegen zonder database-migratie - precies wat 2sxc zo prettig maakt voor content. De prijs daarvan is dat de database de betekenis van je data niet kent:

- er is geen foreign key van `Afspraak` naar `Productie`, dus de database kan niet tegenhouden dat een afspraak naar een verwijderd productienummer verwijst;
- er is geen unique constraint die afdwingt dat een musicus maar één keer op de bezettingslijst van dezelfde afspraak staat;
- er zijn geen indexes op jouw velden - terwijl de maandweergave juist filtert op datumbereik plus productie;
- een export van "alle diensten per musicus per productie", zoals de planning die voor de urenverantwoording wil, is bewerkelijk omdat je eerst het EAV-model moet ontrafelen;
- de structuur van het domein staat deels in de database in plaats van in de code.

Bij een eigen schema keert dat om. Een tabel per entiteit, `Bezetting` met een foreign key naar zowel `Afspraak` als `Musicus` en een unique constraint op dat paar, een check die voorkomt dat een eindtijd vóór een begintijd ligt, een index op `(Datum, ProductieId)`, en migrations die de geschiedenis van het schema in de repository vastleggen. De database bewaakt dan een deel van de bedrijfsregels, ook als er ooit een bug in de applicatiecode zit - en de lege afspraken uit 3.4 waren er nooit in gekomen.

Er is nog een gevolg dat verder reikt dan alleen de database: **de datastructuur wordt leesbaar vanuit de codebase**. Wie wil weten hoe Tutti in elkaar zit, leest de modellen en de migrations. Dat is niet alleen prettig voor mensen, maar ook voor gereedschap (zie 3.12) - en het betekent dat je voor de meeste ontwikkeltaken geen verbinding met de Metrostation-productiedatabase nodig hebt om de structuur te begrijpen.

> **SECURITY-GEVOLG**
>
> Dat laatste weegt hier extra, omdat de Tutti-data persoonsgegevens van musici bevat: bezettingslijsten, aanwezigheid, ziek- en verlofmeldingen, en volgens de user stories op termijn ook contact-, bank- en verzekeringsgegevens. Ontwikkelgereedschap en analysetools hoeven minder snel toegang tot die productiedata te krijgen om de applicatie te kunnen begrijpen. **Maar het is geen beveiligingsmaatregel op zichzelf.** Goed secrets management, strikte toegangscontrole, gescheiden ontwikkel-, test- en productieomgevingen en geanonimiseerde testdata blijven onverminderd nodig.

> **TEGENWERPING**
>
> Het EAV-model van 2sxc is geoptimaliseerd en heeft een eigen cachinglaag; voor de huidige agenda van het Metropole Orkest is het ruim voldoende snel. Het bezwaar gaat dus niet primair over snelheid, maar over integriteit en inzichtelijkheid. Omgekeerd geldt: een slecht ontworpen eigen schema zonder indexes of constraints is beroerder dan een goed gebruikt EAV-model.

### 3.6 Security en autorisatie

Voordelen van een eigen module:

- **Eén plek voor autorisatie.** Of de vraag nu uit de agendaweergave, de detailpagina of straks een mobiele app komt, de beslissing "mag deze musicus deze partij zien" wordt op dezelfde plek genomen.
- **Consistente validatie.** De regel dat een enquête pas 24 uur na afloop en tot drie maanden erna open is, zit in de servicelaag en niet in de knop op de pagina.
- **Logging en audit trails.** De wijzigingshistorie die Tutti toch al toont - "kledingvoorschrift aangepast", "soundcheck verplaatst" - wordt dan bijgehouden in een eigen tabel, met de velden die dit domein nodig heeft.
- **Bewuste API-oppervlakte.** Je bepaalt per endpoint welke velden naar buiten gaan; de bankgegevens van een musicus staan dan niet per ongeluk in een agendaresponse.
- **Minder afhankelijkheid van configuratie.** Een permissie die in code staat, verandert niet doordat iemand een vinkje omzet.

> **RISICO - DIT IS HET GROOTSTE**
>
> Zelfgebouwde autorisatie betekent zelfgebouwde autorisatiefouten. De permissionlaag van 2sxc is door veel installaties heen gebruikt en getest; jouw laag is dat niet. Eén vergeten controle op het bladmuziek-endpoint is genoeg om auteursrechtelijk beschermd materiaal en persoonsgegevens van musici bij de verkeerde mensen te krijgen - en remplaçanten maken dat scherper, omdat zij per productie toegang hebben en niet permanent. Dit risico is beheersbaar maar niet te negeren: verplicht een *deny by default*-opzet, laat autorisatie nooit in de controller maar altijd in de service plaatsvinden, schrijf per endpoint een permissietest die ook de negatieve gevallen dekt, en laat de autorisatielaag apart reviewen voordat hij live gaat.

Beide opties erven overigens de authenticatie en het gebruikersbeheer van DNN, inclusief de M365-SSO die in de user stories voor zowel musici als planners is vastgelegd. Het verschil zit in de autorisatielaag daarboven, niet in het inloggen zelf.

### 3.7 Testbaarheid

Een eigen module is te testen op elk niveau, met standaard .NET-gereedschap:

- **Unit tests** op de services - inclusief de bladmuziekregel uit 3.2, los van database en UI;
- **Integratietests** op repositories tegen een testdatabase;
- **API-tests** op het contract van de endpoints;
- **Permissietests** die per rol vastleggen wat wel en niet mag: "een remplaçant die alleen voor productie 2604M1 is ingehuurd, krijgt de documenten van 2603M1 niet" - de belangrijkste categorie, gezien 3.6;
- **Databasetests** die controleren of migrations schoon draaien op een lege én een gevulde database;
- **End-to-end tests** op de belangrijkste scenario's: een productie aanmaken, afspraken inplannen, publiceren en terugzien in de maandweergave.

Logica die verdeeld is over configuratie, contenttypes, Razor-templates en JavaScript is in de praktijk vooral end-to-end te testen. Dat type test is trager, brozer en geeft minder precies aan waar het misgaat. Bovendien: als de configuratie per omgeving kan verschillen, test je strikt genomen die omgeving, niet de applicatie.

> **ADVIES**
>
> Maak testbaarheid geen bijzaak van de planning. De testsuite is het grootste deel van de reden waarom een eigen module op termijn goedkoper wordt - zonder tests vervalt een flink deel van het argument in dit rapport.

### 3.8 Debugging en foutanalyse

Bij een eigen module heeft een fout een exception, een stack trace, een regelnummer en een logregel met correlatie-id. Je kunt een breakpoint zetten in `ZichtbaarheidService` en de aanroep volgen tot in de query.

Bij verdeelde logica begint de zoektocht een stap eerder. Toen in de agenda de tijden een uur verschoven, waren de kandidaten: de OPAS-export, de import, de tijdzone-instelling van de server, de query of de template. Razor-templates werken bovendien vaak met dynamisch getypeerde data, waardoor een verkeerde veldnaam pas tijdens runtime opvalt en dan als lege waarde verschijnt in plaats van als fout - precies het patroon achter de `undefined`-projectnamen uit 3.4.

Dit is geen theoretisch punt: het verschil zit in hoeveel tijd een gemiddelde melding vanuit het orkest kost, en dat is een terugkerende kostenpost over de hele levensduur.

### 3.9 Performance

Een eigen module maakt optimalisatie *mogelijk*: een index op datumbereik en productie voor de maandweergave, per endpoint bepalen wat je ophaalt - een maandcel heeft genoeg aan tijd, type, titel en productienummer en niet aan de volledige bezettingslijst - een eigen cachingstrategie, en paginering op databaseniveau voor de lijstweergave.

Dat is in Tutti geen abstract punt: de maandweergave haalt zes weken aan afspraken tegelijk op, en in de shaping-vragen staat de keuze tussen alles in één keer laden of lazy loading per week nog expliciet open. Dat soort keuzes maak je makkelijker als je de queries zelf schrijft.

> **BELANGRIJKE NUANCE**
>
> Dat betekent niet dat een eigen module automatisch sneller is. Een slecht geschreven eigen module die per afspraak apart de bezetting ophaalt (N+1) en zonder indexes werkt, presteert slechter dan een goed opgezette 2sxc-app met caching. Performance is hier een *mogelijkheid*, geen gegeven - en die mogelijkheid moet je met meten waarmaken, niet met aannames. De Lighthouse-ronde die de agendamodule al heeft gehad, is daar het goede voorbeeld van.

### 3.10 Schaalbaarheid

Als Tutti groeit - van de agenda naar bezettingen, documenten, bladmuziek, contracten en de enquête per productie - verschuift de last van "afspraken tonen" naar "grote hoeveelheden gerelateerde data filteren op basis van rechten". Een musicus die zijn hele seizoen overziet, een planner die alle openstaande posities in een periode opvraagt, of een export van alle diensten per musicus voor de urenverantwoording: dat is het type belasting waarbij een relationeel schema met de juiste indexes zijn werk doet en een generiek opslagmodel meer moeite kost.

Daar komt bij dat je bij een eigen module de vrijheid hebt om per onderdeel te schalen: de bladmuziekbibliotheek kan een eigen leesmodel of cache krijgen zonder dat de agenda verandert.

> **AANNAME**
>
> Er zijn geen belastingcijfers van Metrostation gebruikt. Een orkest van deze omvang, met vaste musici plus remplaçanten en enkele tientallen producties per seizoen, is qua datavolume bescheiden - beide opties voldoen ruim. Het argument wordt pas zwaar als de historie van meerdere seizoenen doorzoekbaar moet blijven, of als permissiechecks over veel rijen tegelijk moeten lopen. Neem dit criterium dus mee als toekomstscenario, niet als acuut probleem.

### 3.11 API-first en toekomstige frontends

Een eigen module kan vanaf het begin API-first worden opgezet: de business logic zit achter een API, en de agenda op Metrostation is daar de eerste consument van - niet de enige. Dat is voor Tutti geen hypothetisch scenario. De user stories voor musici gaan over pushnotificaties bij gewijzigde afspraken, een koppeling met de eigen Outlook-agenda en bladmuziek onderweg bekijken; de designs gaan uit van gebruik op telefoon en tablet. Dezelfde backend kan later bediend worden door een mobiele app, een losse Tutti-frontend of een beheerdashboard, zonder dat de logica verhuisd hoeft te worden.

> **TEGENWERPING**
>
> 2sxc heeft zelf een headless REST-API en kan dus ook headless gebruikt worden. Het verschil is waar je API-contract vandaan komt: bij 2sxc volgt het uit de contenttypes en de 2sxc-conventies, bij een eigen module ontwerp je het zelf, inclusief versionering, foutcodes en welke velden je juist níet blootstelt - relevant zodra AFAS, SharePoint of een externe partij meeleest.

### 3.12 AI-ondersteunde ontwikkeling

AI-codeassistentie werkt het best wanneer de relevante informatie **in de codebase staat, statisch getypeerd is en een snelle terugkoppeling heeft**. Op alle drie scoort een eigen module beter:

- **Alles op één plek.** `Productie`, `Afspraak`, `Bezetting`, de repositories, de services, de controllers, de permissies en de agenda-frontend staan in dezelfde repository. Een assistent kan de relatie tussen een veldwijziging en de vier agendaweergaven vinden door te zoeken, en een wijziging voorstellen die alle betrokken bestanden raakt.
- **Statische typering.** C# geeft een compiler als vangnet. Hernoem je `ProductieNummer`, dan faalt de build - in plaats van dat er stilletjes weer `undefined` in de agenda verschijnt.
- **Schema zonder databaseverbinding.** Omdat het schema uit ORM-modellen en migrations te lezen is, kan een assistent de structuur van Tutti begrijpen zonder toegang tot een draaiende database - laat staan tot de Metrostation-productiedatabase met de gegevens van de musici.
- **Tests als feedback.** Een voorstel voor de bladmuziekregel is direct verifieerbaar door de permissietests te draaien.

Bij 2sxc staat een deel van de waarheid buiten de code: de definities van `Afspraak` en `Bladmuziekpartij` leven in de database of in een exportbestand, de configuratie in de beheeromgeving, en de logica in een mengsel van Razor, JavaScript en queries. Een assistent moet die context expliciet aangeleverd krijgen en kan minder makkelijk zelf het geheel overzien.

> **GEEN OVERDREVEN CLAIM**
>
> Dit is een verschil in gemak, geen absolute grens. 2sxc-apps kunnen geëxporteerd en in de repository gezet worden, wat een groot deel van deze kloof dicht. Omgekeerd helpt AI ook niet bij een eigen module die slecht gestructureerd is of geen documentatie heeft. De kwaliteit van AI-ondersteuning hangt af van projectstructuur, documentatie en gereedschap - de architectuurkeuze maakt het alleen makkelijker of moeilijker, niet goed of slecht.

### 3.13 Vendor lock-in en afhankelijkheden

Hier is een tegenargument dat eerlijk benoemd moet worden, omdat het de intuïtie tegenspreekt.

> **FEIT**
>
> 2sxc draait op zowel DNN als Oqtane. DNN Platform blijft bewust op .NET Framework; Microsoft ondersteunt die versie naar verwachting tot ten minste 2031. Een migratie van DNN naar .NET Core is door de DNN-community afgewogen en niet gekozen, onder meer omdat alle bestaande modules en themes daarbij zouden breken.

Daaruit volgt: een klassieke eigen DNN-module is gebonden aan DNN én aan .NET Framework. Een 2sxc-app is in dat opzicht juist mobieler, omdat 2sxc zelf de stap naar Oqtane al gezet heeft. Wie alleen naar platformbinding kijkt, komt dus op een punt vóór 2sxc uit.

Het tegenargument is dat het bij lock-in niet alleen gaat om waar je code draait, maar om **hoeveel van je bedrijfslogica uitdrukbaar is in gewone .NET-code**. De regels van Tutti - wie welke partij ziet, wanneer de enquête open is, hoe een soundcheck zijn kleur krijgt - zijn in C#-klassen en SQL uit te drukken en daarmee overzetbaar naar welk .NET-platform dan ook. In een 2sxc-app zijn diezelfde regels deels uitgedrukt in 2sxc-concepten, die alleen binnen 2sxc bestaan.

> **ADVIES - DIT MAAKT HET VERSCHIL**
>
> Bouw het Tutti-domein als een **aparte .NET-class library** (bijvoorbeeld op .NET Standard) die niets van DNN weet, en houd de DNN-module dun: alleen hosting, authenticatie-integratie en de API-controllers. Dan is de DNN-afhankelijkheid een laag van beperkte omvang in plaats van het hele systeem, en blijft een toekomstige stap naar Oqtane, een losse Tutti-webapplicatie of een eigen API-project realistisch - wat past bij een app die volgens de memo van bond uiteindelijk de centrale planning moet worden en niet alleen binnen Metrostation hoeft te leven. Zonder die scheiding vervalt dit voordeel grotendeels.

### 3.14 Kennis binnen het ontwikkelteam

Een eigen module vraagt om C#, SQL, REST en JavaScript, plus werkende kennis van DNN. Dat is grotendeels overdraagbare kennis: wie ooit een .NET-webapplicatie heeft gebouwd, kan meelezen in de servicelaag.

2sxc vraagt daarbovenop om specifieke kennis: de EAV-denkwijze, DataSources en queries, het view- en app-model, de permissiestructuur en de manier waarop Razor binnen 2sxc werkt. Die kennis is waardevol, maar smaller. Bij Tutti is dat geen theoretisch punt: de agendamodule is voor een groot deel door een stagiair gebouwd, met een expliciete notitie in de backlog waarin de gebruikte naamgeving en termen zijn vastgelegd "zodat de volgende stagiair begrijpt waar ik het over heb". Dat is precies het moment waarop het verschil tussen algemene en platformspecifieke kennis zich laat voelen.

> **NUANCE**
>
> Als het team vandaag veel 2sxc-ervaring heeft en weinig ervaring met het opzetten van een gelaagde .NET-applicatie, keert dit argument op korte termijn om. De vraag is dan welke kennis je de komende jaren wilt opbouwen. Dat is een organisatiekeuze, geen technische.

## Hoofdstuk 4 - Ontwikkeltijd en kosten

*Dit hoofdstuk is bewust kritisch: op korte termijn is 2sxc vrijwel zeker de snellere en goedkopere optie - de agendamodule is er tenslotte binnen één stageperiode mee opgeleverd.*

### 4.1 Korte termijn

Een eigen module vraagt vooraf om werk dat bij 2sxc grotendeels al gedaan is:

- het schema voor producties, afspraken, bezettingen en partijen ontwerpen en de eerste migrations schrijven;
- de domein- en servicelaag opzetten;
- de API bouwen, inclusief foutafhandeling en validatie;
- autorisatie inrichten en aantoonbaar maken;
- het beheeroverzicht bouwen dat 2sxc kant-en-klaar zou hebben geleverd;
- de vier agendaweergaven opnieuw bouwen op de nieuwe API;
- de testsuite opzetten;
- build, deployment en migraties inrichten en onderhouden.

Vooral het laatste punt wordt vaak onderschat: bij 2sxc is een wijziging soms een import; bij een eigen module hoort er een releaseproces bij.

### 4.2 Lange termijn

Daar staat tegenover dat het werk per wijziging verandert zodra het fundament er ligt:

- de architectuur is expliciet, dus nieuwe functionaliteit - contracten, tourlogistiek, verlofaanvragen - heeft een voor de hand liggende plek;
- er zijn minder workarounds nodig om iets in het model te passen;
- debuggen kost minder tijd, omdat de fout een stack trace heeft;
- geautomatiseerde tests vangen regressies af in plaats van de musici;
- refactoren kan met gereedschapsondersteuning, omdat de compiler meewerkt;
- de oplossing is niet afhankelijk van 2sxc-specifieke constructies;
- het bouwen van iets nieuws wordt voorspelbaar in plaats van afhankelijk van wat het platform toelaat.

### 4.3 De afweging tussen beide

Het kernpunt is dat de twee opties een verschillende *vorm* van kosten hebben. 2sxc heeft lage instapkosten en kosten die meestijgen met de complexiteit: elke extra regel die niet in het model past, kost extra. Een eigen module heeft hoge instapkosten en een vlakkere curve: het meeste extra werk is voorzienbaar.

Voor Tutti is er bovendien een aanwijzing hoe vaak er gewijzigd wordt. De agendamodule ging in een paar maanden van V 0.1 naar V 0.1.06, met zesendertig user stories, en een flink deel daarvan waren wijzigingen ná oplevering: de weekweergave is omgegooid toen de ochtend-, middag- en avondindeling verwarrend bleek, de kleurlogica is herzien na het bedrijfsbezoek, het dagoverzicht opent nu op 09:00 in plaats van middernacht, en over het uitlichten van concerten zijn vijf ontwerpvarianten voorgelegd. Dat is geen kritiek - zo hoort het te gaan - maar het laat zien dat dit een applicatie is die blijft bewegen.

> **AANNAME - INDICATIEF, NIET GEMETEN**
>
> Bij dit type applicatie is het redelijk te verwachten dat het eerste substantiële onderdeel in een eigen module grofweg **twee tot drie keer** zoveel bouwtijd kost als een vergelijkbare 2sxc-implementatie, en dat latere wijzigingen aan complexe logica **sneller en met minder nawerk** gaan. Bij het ontwikkeltempo dat de agendamodule laat zien, ligt het omslagpunt naar verwachting ergens tussen het eerste en het tweede jaar. Dit is een inschatting op basis van de aard van het werk, geen meting; wie dit harder wil maken, kan drie wijzigingen uit de bestaande backlog - bijvoorbeeld het uitlichten van concerten, het beheeroverzicht en het inladen van documenten via ADAM - in beide opzetten uitwerken en de werkelijke uren vergelijken.

Voor een applicatie die af is en niet meer verandert, is deze hele redenering irrelevant en wint de goedkoopste start. Voor Tutti, dat volgens de routekaart doorgroeit richting bezetting, contracten en uiteindelijk de centrale planning, is het omgekeerde waar.

## Hoofdstuk 5 - Nadelen en risico's van een eigen module

*Negen nadelen die echt bestaan, met per nadeel de maatregel die het risico beperkt. Als deze maatregelen niet worden genomen, is het advies in dit rapport niet houdbaar.*

**Hogere initiële ontwikkelkosten**

> *Beperking* - Begin klein: bouw eerst het onderdeel met de meeste bedrijfslogica - het beheeroverzicht met producties, afspraken en bezetting - in de eigen module, en laat de agendaweergaven voorlopig staan. Zo betaal je de investering gespreid en zie je vroeg of de aanpak werkt.

**Langere doorlooptijd tot de eerste zichtbare functionaliteit**

> *Beperking* - Lever een dunne verticale doorsnede op - één productie aanmaken, één afspraak inplannen, terugzien in de agenda - in plaats van eerst een compleet fundament. Dat geeft de planning van het MO binnen enkele weken iets werkends om op te reageren.

**Meer verantwoordelijkheid voor security**

> *Beperking* - Autorisatie uitsluitend in de servicelaag, deny by default, verplichte permissietests per endpoint inclusief de negatieve gevallen (remplaçant, oud-musicus, niet-ingedeelde speler), en een aparte securityreview voordat bladmuziek en persoonsgegevens via de module lopen.

**Database-migraties moeten zelf beheerd worden**

> *Beperking* - Een migrationframework vanaf dag één, migrations in versiebeheer, altijd voorwaarts-compatibel, en het migratiepad testen op een kopie van de Metrostation-data voordat het naar productie gaat.

**Meer code om te onderhouden**

> *Beperking* - Code review op alles, een vaste architectuurbeschrijving die iedereen volgt, en geen eigen frameworks bouwen waar bestaande bibliotheken volstaan.

**Grotere verantwoordelijkheid voor backward compatibility**

> *Beperking* - Versioneer de API vanaf het begin, breek nooit stilzwijgend een contract, en houd een korte changelog bij zodra er een tweede consument is - de mobiele weergave is de eerste kandidaat.

**Ontwikkelaars moeten DNN én .NET kennen**

> *Beperking* - Houd de DNN-specifieke laag dun (zie 3.13), zodat het grootste deel van de codebase gewone .NET is waar iedere .NET-ontwikkelaar in kan werken. Documenteer de DNN-integratie apart, en houd de lijst met projecttermen bij die in de backlog al is begonnen.

**Slechte architectuur veroorzaakt technische schuld**

> *Beperking* - Leg de laagindeling en de afhankelijkheidsrichting vooraf vast, toets daarop in reviews, en plan expliciet ruimte voor refactoring in plaats van die te laten afhangen van de restcapaciteit.

**Functionaliteit die 2sxc standaard biedt moet opnieuw gebouwd worden**

> *Beperking* - Bouw die functionaliteit niet opnieuw als je hem niet nodig hebt. Waar wel - inline tekstbewerking, mediabeheer, het inladen en hernoemen van documenten via ADAM - gebruik je bestaande bibliotheken, of laat je dat deel juist in 2sxc staan (zie hoofdstuk 8).

> **RISICO OP PROCESNIVEAU**
>
> Het grootste risico is niet technisch maar organisatorisch: een eigen module die zonder tests, zonder CI/CD en zonder reviews gebouwd wordt, levert alle nadelen op en geen van de voordelen. De voordelen in dit rapport zijn voorwaardelijk - ze bestaan alleen als de bijbehorende werkwijze er ook is. Bij een project dat deels door stagiairs wordt gebouwd en overgedragen, verdient dat extra aandacht.

## Hoofdstuk 6 - Waar 2sxc juist de betere keuze is

2sxc is een volwassen, goed doordacht platform en in de volgende situaties duidelijk de verstandiger optie - ook binnen Metrostation:

- **Contentmodules** - de nieuwsberichten op de homepage, de praktische-informatiepagina's per productie en de uitgelicht-blokken.
- **Relatief kleine websites** waar de bouwtijd zwaarder weegt dan de architectuur.
- **Prototypes en proefopstellingen**, waar snel iets werkends laten zien het doel is - zoals de eerste agendaversie feitelijk was.
- **Snel CMS-functionaliteit toevoegen** aan Metrostation zonder releaseproces.
- **Pagina's die overwegend uit content bestaan** met beperkte logica erachter.
- **Situaties waarin flexibiliteit voor contentbeheerders belangrijker is dan strikte bedrijfsregels** - een redactie die zelf een veld aan een nieuwsbericht wil toevoegen zonder ontwikkelaar.

Die voordelen zijn echt. Ze wegen bij Tutti alleen minder zwaar, om één reden: de zwaartepunten liggen ergens anders. Tutti heeft niet vooral behoefte aan snel nieuwe velden, maar aan regels die kloppen, rechten op bladmuziek en persoonsgegevens die aantoonbaar goed staan, en gedrag dat over drie jaar nog te begrijpen is. De kracht van 2sxc - flexibiliteit in de handen van de beheerder - is bij een applicatie met complexe rechten eerder een risico dan een voordeel (zie 3.3).

## Hoofdstuk 7 - Vergelijkingstabel

Geen cijfers of sterren, maar per onderdeel een korte onderbouwing en een richting. "Genuanceerd" betekent dat het verschil in de praktijk beide kanten op werkt.

| Onderdeel | 2sxc | Eigen DNN-module | Richting |
| --- | --- | --- | --- |
| **Ontwikkelsnelheid korte termijn** | Snel: agendamodule in één stageperiode opgeleverd | Traag: schema, services, API, beheerscherm en tests eerst bouwen | **2sxc** |
| **Onderhoudbaarheid** | Bladmuziekregel verspreid over configuratie, query, template en script | Eén service per regel, met compiler en tests als vangnet | **Eigen module** |
| **Controle** | Binnen de conventies van het platform | Volledig over architectuur, data, API en gedrag | **Eigen module** |
| **Flexibiliteit voor de klant** | Hoog: beheerder kan zelf types en views aanpassen | Beperkt tot wat bewust is aangeboden, zoals kleuren en afkortingen | **2sxc** |
| **Kans op configuratiefouten** | Verhoogd: het veld voor afspraaktype hernoemen breekt vier weergaven | Klein: wat draait staat in de repository | **Eigen module** |
| **Databasecontrole** | EAV-model: geen FK van afspraak naar productie, geen eigen indexes | Eigen schema; unique constraint per musicus per afspraak | **Eigen module** |
| **Testbaarheid** | Vooral end-to-end; de zichtbaarheidsregel is lastig los te testen | Unit, integratie, API, permissies en E2E met standaardgereedschap | **Eigen module** |
| **Security** | Beproefde permissielaag, maar configuratie-afhankelijk | Centrale, testbare autorisatie op bladmuziek - en eigen fouten daarin | **Eigen module, mits getest** |
| **Performance-optimalisatie** | Goede caching; minder grip op queries en indexes | Eigen index op datum en productie voor de maandweergave | **Eigen module** |
| **AI-ondersteunde ontwikkeling** | Definities van Afspraak en Partij leven buiten de code | Alles in de repository, statisch getypeerd, tests als feedback | **Eigen module** |
| **Debugging** | Een tijdzonefout kan in vijf lagen zitten, zonder stack trace | Exception, stack trace, breakpoint, logging met correlatie-id | **Eigen module** |
| **Schaalbaarheid** | Ruim voldoende voor de agenda; zwaarder bij seizoensbrede queries | Per onderdeel te optimaliseren en te schalen | **Eigen module** |
| **API-mogelijkheden** | Headless REST-API aanwezig, vorm volgt het platform | Eigen contract voor mobiel, AFAS en SharePoint | **Eigen module** |
| **Toekomstbestendigheid** | Sterk voor content; de Tutti-regels blijven 2sxc-specifiek | Domeinlogica in gewone .NET, mits de lagen gescheiden blijven | **Eigen module** |
| **Vendor lock-in** | Gebonden aan 2sxc, maar draait op DNN én Oqtane | Gebonden aan DNN/.NET Framework; logica wel overzetbaar | **Genuanceerd** |
| **Benodigde specialistische kennis** | 2sxc-specifiek: EAV, DataSources, views, app-model | Algemeen: C#, SQL, REST, JavaScript + basis DNN | **Eigen module** |

De richting in de laatste kolom is een oordeel binnen de context van Tutti, niet een algemene uitspraak over de producten.

## Hoofdstuk 8 - Eindadvies

### 8.1 Het advies

Voor Tutti heeft **een eigen DNN-module de voorkeur** voor alles wat tot het domein behoort: producties en productienummers, afspraken met hun soort en status, bezettingen en openstaande posities, bladmuziek per instrument, documenten, de wijzigingshistorie, de enquête per productie en de rechten van musici, remplaçanten, planners en beheerders.

De reden is niet dat 2sxc tekortschiet - de huidige agenda bewijst het tegendeel - maar dat Tutti vraagt om eigenschappen die een eigen module beter levert: controle, voorspelbaarheid, testbaarheid, aantoonbare veiligheid op bladmuziek en persoonsgegevens, schaalbaarheid, een expliciete architectuur en een domein dat in gewone .NET-code is uitgedrukt. De hogere investering aan het begin wordt naar verwachting terugverdiend door goedkoper onderhoud en voorspelbaarder doorontwikkeling - mits de werkwijze uit hoofdstuk 5 er ook daadwerkelijk is.

### 8.2 Niet alles of niets

> **AANBEVOLEN VARIANT**
>
> Zet de twee naast elkaar in plaats van 2sxc te vervangen. Laat **redactionele content in 2sxc** - nieuwsberichten, praktische informatie per productie, uitgelicht-blokken, teksten die het MO zelf wil beheren - en breng **het Tutti-domein onder in de eigen module**. Dat levert het beste van beide: de redactie houdt haar vrijheid waar die onschadelijk is, en de logica rond producties, bezetting en bladmuziek krijgt de structuur die ze nodig heeft. De grens moet dan wel expliciet zijn afgesproken en gedocumenteerd - een praktische scheidslijn is: alles wat aan een productienummer hangt en een rechtenvraag oproept, hoort in de module.

### 8.3 Randvoorwaarden

Het advies geldt alleen wanneer aan deze voorwaarden wordt voldaan:

1. Het Tutti-domein komt in een **DNN-onafhankelijke .NET-class library**; de DNN-module blijft dun.
2. Autorisatie zit in de servicelaag, werkt **deny by default** en heeft permissietests per endpoint, inclusief de remplaçant- en oud-musicusgevallen.
3. Er is vanaf dag één een **migrationframework** en een geautomatiseerde build- en deploystraat.
4. Er is een **vastgelegde architectuurbeschrijving** en code review op alles wat binnenkomt.
5. De eerste oplevering is een **dunne verticale doorsnede** - productie aanmaken, afspraak inplannen, terugzien in de agenda - zodat de aanpak zich vroeg bewijst.
6. De grens tussen 2sxc-content en Tutti-domein is **expliciet vastgelegd**.

### 8.4 Wanneer dit advies herzien moet worden

Een advies dat nooit fout kan zijn, is geen advies. Heroverweeg de keuze als:

- Tutti niet doorgroeit naar bezetting, contracten en rechten, en feitelijk een agendaweergave op OPAS-data blijft;
- het team de werkwijze uit hoofdstuk 5 niet kan waarmaken - dan is 2sxc met een strikte exportdiscipline de veiliger keuze;
- het Metropole Orkest of bond besluit naar Oqtane te migreren voordat de eigen module volwassen is; dan is de scheiding uit 3.13 nog belangrijker, of wint 2sxc op dit punt;
- er na een eerste verticale doorsnede blijkt dat de bouwtijd structureel veel hoger uitvalt dan de inschatting in 4.3.

## Bijlagen - Verantwoording

### Bijlage A - Aannames en beperkingen

- Er zijn geen belastingcijfers, gebruikersaantallen of prestatiemetingen van Metrostation gebruikt; schaalbaarheid is daarom als toekomstscenario behandeld en niet als gemeten probleem.
- De tijdsinschatting in 4.3 is een redenering, geen meting. Een vergelijkende uitwerking van drie wijzigingen uit de bestaande backlog zou dit hard maken.
- Het rapport gaat uit van de teamsamenstelling zoals beschreven in 1.3.
- De beschrijving van 2sxc is gebaseerd op de officiële documentatie, niet op een audit van de bestaande Tutti-implementatie. Als die implementatie al een eigen servicelaag en geëxporteerde apps in versiebeheer kent, zijn de bezwaren in 3.4 en 3.12 kleiner dan hier beschreven.
- De projectvoorbeelden - user stories, releasehistorie en de genoemde bugs - komen uit wat in Basecamp over Tutti en de agendamodule is vastgelegd; productienummers zijn illustratief gebruikt.
- Kosten zijn kwalitatief behandeld; er zijn geen tarieven of begrotingen in verwerkt.

### Bijlage B - Bronnen

- [2sxc and EAV Docs](https://docs.2sxc.org/) - versie, platformondersteuning (DNN en Oqtane), EAV-datamodel, DataSources, headless REST-API.
- [Content-Type (Schema/Object-Type) - 2sxc docs](https://docs.2sxc.org/basics/data/content-types/index.html) - hoe contenttypes zijn opgebouwd en beheerd worden.
- [Licenses in 2sxc](https://docs.2sxc.org/basics/licenses-features/licenses/index.html) - vrije licenties versus patron-/enterprise-licenties.
- [Roadmap of EAV and 2sxc](https://docs.2sxc.org/abyss/releases/roadmap.html) - richting van het platform.
- [The Technical Future of DNN - Mitchel Sellers](https://mitchelsellers.com/blog/article/the-technical-future-of-dnn) - keuze voor .NET Framework, ondersteuning tot circa 2031, alternatieven (Oqtane, Orchard Core).
- [The Technical Future of DNN - DNN Community](https://dnncommunity.org/blogs/Post/7625/The-Technical-Future-of-DNN) - dezelfde afweging vanuit de community.
- [Oqtane Roadmap and History](https://docs.oqtane.org/guides/roadmap/index.html) - status van het .NET-gebaseerde alternatief.
- Basecamp - project "MO: 'Tutti' – Planning & Administration App" en het Metropole Orkest-project: user stories voor musicus en planner, releasehistorie V 0.1 t/m V 0.1.06, beheeroverzicht, enquêtefunctie, ADAM- en SharePoint-koppeling en de OPAS-synchronisatie.

---

*Adviesrapport ter ondersteuning van een architectuurbeslissing binnen het Tutti-project. Feiten over DNN, 2sxc en .NET zijn gecontroleerd tegen de bronnen in bijlage B; de projectvoorbeelden komen uit het Basecamp-materiaal van Tutti. De weging en het advies zijn een oordeel binnen de context van dit project en geen algemene uitspraak over de geschiktheid van 2sxc.*
