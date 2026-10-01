# Frameworks of CMS

*Adviesrapport · Architectuurkeuze · Bond for Web Solutions*

Een onderbouwde afweging tussen een framework-gebaseerde architectuur en een traditioneel CMS-platform voor grote, bedrijfskritische en sterk op maat gemaakte applicaties - inclusief de nadelen van frameworks en de situaties waarin een CMS aantoonbaar de verstandiger keuze blijft.

- **Onderwerp:** Platformkeuze voor complexe applicaties
- **Vergeleken:** React/Next.js, Vue/Nuxt, ASP.NET Core, Laravel vs. DNN, WordPress
- **Datum:** 23 september 2026
- **Advies:** Framework bij domeinlogica, CMS bij content

---

## Inhoud

- [Managementsamenvatting](#managementsamenvatting-waar-het-advies-op-neerkomt)
- [1 · Inleiding](#hoofdstuk-1-inleiding)
- [2 · De twee benaderingen](#hoofdstuk-2-de-twee-benaderingen-technisch-beschreven)
- [3 · Analyse per criterium](#hoofdstuk-3-analyse-per-criterium)
  - [3.1 Schaalbaarheid](#31-schaalbaarheid)
  - [3.2 Performance](#32-performance)
  - [3.3 Onderhoudbaarheid](#33-onderhoudbaarheid)
  - [3.4 Codekwaliteit en architectuur](#34-codekwaliteit-en-architectuur)
  - [3.5 Flexibiliteit](#35-flexibiliteit)
  - [3.6 Testbaarheid](#36-testbaarheid)
  - [3.7 CI/CD en DevOps](#37-cicd-en-devops)
  - [3.8 Security](#38-security)
  - [3.9 Integraties en API's](#39-integraties-en-apis)
  - [3.10 Datamodelvrijheid](#310-database--en-datamodelvrijheid)
  - [3.11 Modules en plugins](#311-afhankelijkheid-van-modules-en-plugins)
  - [3.12 Upgrade-risico's](#312-upgrade-risicos)
  - [3.13 Vendor lock-in](#313-vendor-lock-in)
  - [3.14 Technische schuld](#314-technische-schuld)
  - [3.15 Beschikbaarheid van developers](#315-beschikbaarheid-van-developers)
  - [3.16 Lange-termijnondersteuning](#316-lange-termijnondersteuning)
  - [3.17 Geschiktheid voor maatwerk](#317-geschiktheid-voor-maatwerk)
  - [3.18 Geschiktheid voor grote teams](#318-geschiktheid-voor-grote-teams)
- [4 · Kosten korte en lange termijn](#hoofdstuk-4-kosten-op-korte-en-lange-termijn)
- [5 · Nadelen en risico's van frameworks](#hoofdstuk-5-nadelen-en-risicos-van-een-framework-aanpak)
- [6 · Waar een CMS wint](#hoofdstuk-6-waar-een-cms-juist-de-betere-keuze-is)
- [7 · Vergelijkingstabel](#hoofdstuk-7-vergelijkingstabel)
- [8 · Conclusie en advies](#hoofdstuk-8-conclusie-en-advies)
- [Bijlagen](#bijlagen-verantwoording)

---

## Managementsamenvatting - Waar het advies op neerkomt

Een CMS en een framework lossen verschillende problemen op. Een CMS - DNN, WordPress - is gebouwd rond de aanname dat *content* het hart van het systeem is en dat een beheerder zonder ontwikkelaar moet kunnen publiceren. Een framework - ASP.NET Core, Laravel, React/Next.js, Vue/Nuxt - gaat uit van de aanname dat *gedrag* het hart is: regels, toestanden, rechten en integraties die in code worden uitgedrukt en getest.

Zolang een project overwegend content is, wint het CMS met afstand: minder bouwtijd, een redactieomgeving die er al is, en een beheerder die zelfstandig kan werken. Het kantelpunt ligt op het moment dat de **bedrijfslogica zwaarder gaat wegen dan de content**. Vanaf dat punt werken de eigenschappen die een CMS sterk maken - configuratie buiten de code, uitbreidbaarheid via plugins, een generiek datamodel - systematisch tegen: ze verplaatsen gedrag naar plekken die de compiler niet ziet, geen test afdekt en geen versiebeheer vastlegt.

Het advies is daarom niet "framework in plaats van CMS", maar **een grens trekken op basis van waar het gedrag zit**. Domeinlogica, rechten, transacties en integraties horen in een framework-architectuur met een eigen datamodel, een servicelaag en een API. Redactionele content hoort in een CMS. Dit rapport onderbouwt die grens per criterium, benoemt de reële nadelen van frameworks - hogere startkosten, snellere veroudering van het JavaScript-ecosysteem, en een zwaardere afhankelijkheid van de kwaliteit van het team - en geeft de voorwaarden waaronder het advies niet opgaat.

- **Hoofdadvies:** Framework bij domeinlogica
- **Nuance:** CMS blijft sterk voor content
- **Grootste risico framework:** Hogere startkosten, afhankelijk van teamdiscipline
- **Grootste risico CMS:** Gedrag verspreid over plugins en configuratie

## Hoofdstuk 1 - Inleiding

### 1.1 Aanleiding

Bij de start van een webproject valt de platformkeuze vaak vroeg, en vaak op basis van wat er al staat. Een organisatie die een intranet op DNN heeft, bouwt de volgende functionaliteit als DNN-module. Een organisatie met een WordPress-site zoekt een plugin. Dat is een verdedigbare reflex: de omgeving is er, het beheer is ingeregeld, en de eerste versie staat snel.

Het probleem ontstaat later. Wat begint als "een formulier op de site" groeit uit tot een aanvraagproces met statussen, rollen, goedkeuringen, koppelingen naar een backoffice en een auditspoor. Wat begint als "een agenda op het intranet" groeit uit tot een planningsapplicatie met producties, bezettingen, documenten en rechten per persoon - de ontwikkeling die het Tutti-project bij het Metropole Orkest doormaakt, en die als doorlopend voorbeeld in dit rapport wordt gebruikt.

Op dat moment blijkt dat de platformkeuze geen implementatiedetail was maar een architectuurkeuze, en dat hij achteraf duur is om te herzien. Dit rapport onderzoekt daarom wanneer een framework beter past dan een CMS, en waarom.

### 1.2 Onderzoeksvraag

**Waarom zijn softwareframeworks technisch en praktisch beter geschikt voor grote, complexe en bedrijfskritische applicaties dan traditionele CMS-platformen - en in welke situaties geldt dat juist niet?**

De vraag is gericht geformuleerd, het onderzoek is dat niet. Hoofdstuk 5 behandelt de nadelen van frameworks, hoofdstuk 6 de situaties waarin een CMS de betere keuze is, en paragraaf 8.4 de omstandigheden waaronder dit advies herzien moet worden.

### 1.3 Aanpak en verantwoording

Het rapport is opgebouwd uit vier soorten materiaal, die door het hele document herkenbaar gelabeld zijn:

- **Feiten** - controleerbare, actuele eigenschappen van de vergeleken technologieën, met bronvermelding in bijlage B. Alle versie- en ondersteuningsgegevens zijn gecontroleerd op 23 september 2026.
- **Technische argumenten** - redeneringen die uit die feiten en uit gangbare software-engineeringpraktijk volgen.
- **Aannames** - zaken die niet gemeten zijn maar wel meewegen, zoals teamsamenstelling en ontwikkelsnelheid.
- **Tegenwerpingen** - argumenten die tegen de strekking van dit rapport in gaan, in neutrale opmaak zodat ze niet als waarschuwing gelezen worden.
- **Risico's en adviezen** - apart gemarkeerd.

Waar publiek beschikbare cijfers een vergelijking niet dragen, staat dat er expliciet bij. Dat geldt in het bijzonder voor prestatiemetingen: veldmetingen over platformen heen vergelijken verschillende populaties websites en zijn geen gecontroleerd experiment (zie 3.2).

> **AANNAME**
>
> Het rapport gaat uit van een professioneel ontwikkelteam van drie tot tien personen met C#-, PHP- of JavaScript-kennis, dat werkt met versiebeheer, code review en een geautomatiseerde buildstraat. Ontbreken die werkwijzen, dan verschuift het oordeel op meerdere criteria richting het CMS - zie 5 en 8.4.

### 1.4 Begrippen

- **CMS** - contentmanagementsysteem: een kant-en-klare applicatie met een database, een beheerinterface, een gebruikersmodel en een uitbreidingsmechanisme, waarop functionaliteit via modules, plugins en configuratie wordt toegevoegd. In dit rapport: DNN (DotNetNuke) en WordPress.
- **Framework** - een verzameling bibliotheken en conventies waarmee een ontwikkelaar een applicatie bouwt. Het framework levert bouwstenen (routing, ORM, validatie, authenticatie); de applicatie zelf bestaat volledig uit eigen code. In dit rapport: ASP.NET Core MVC, Laravel, React met Next.js, en Vue met Nuxt.
- **Domeinlogica** - de regels die het probleem van de organisatie beschrijven: welke statusovergangen mogen, wie wat mag zien, wanneer iets verplicht is.
- **Hook / filter** - een uitbreidingspunt waarop een plugin zich tijdens runtime inschrijft om het gedrag van het CMS of van een andere plugin te wijzigen.
- **ORM** - object-relational mapper: een laag die databasetabellen op objecten in code afbeeldt (Entity Framework Core, Eloquent).
- **SSR / SSG** - server-side rendering en static site generation: het renderen van pagina's op de server of tijdens de build in plaats van in de browser.
- **Technische schuld** - het toekomstige werk dat ontstaat door een keuze die nu sneller was maar later duurder is.
- **Vendor lock-in** - de mate waarin een oplossing niet losgemaakt kan worden van de leverancier of het platform waarop hij gebouwd is.

## Hoofdstuk 2 - De twee benaderingen technisch beschreven

### 2.1 Wat een CMS is

Een CMS is een *af product* dat je configureert en uitbreidt. De kern - database, beheerinterface, gebruikers, rechten, paginastructuur, mediabeheer - is gegeven en wordt door de leverancier of de community onderhouden. Functionaliteit komt erbij via een uitbreidingsmechanisme:

- **WordPress** - plugins en thema's die zich via `add_action` en `add_filter` op hooks inschrijven. Content staat in een vast schema (`wp_posts`, `wp_postmeta`). Een plugin mág eigen tabellen aanmaken via `$wpdb` en `dbDelta()` - WooCommerce en andere volwassen applicatieplugins doen dat ook - maar de kern, de beheerinterface, de REST-API en de zoekfunctie weten niets van die tabellen, waardoor veel plugins alsnog op `wp_postmeta` uitkomen.
- **DNN** - modules (C#), skins en providers, geplaatst op pagina's binnen een portal. DNN levert portals, rollen, authenticatie en een module-API. Eigen tabellen zijn hier de normale werkwijze en niet de uitzondering, en ze zijn beter in het platform geïntegreerd dan bij WordPress.

De winst is direct: een redacteur kan dezelfde dag publiceren, de rechtenstructuur staat er, en voor veel standaardbehoeften bestaat een module of plugin die vandaag geïnstalleerd kan worden.

> **FEIT**
>
> DNN Platform draait op .NET Framework - de officiële systeemeisen noemen .NET Framework 4.8 als minimum, met IIS en SQL Server. De laatste stabiele release is 10.3.3 (23 juli 2026). Er is *geen* gepubliceerde, gedateerde roadmap voor een overstap naar .NET 5+/.NET Core; wat er is, is een community-initiatief voor een MVC-pijplijn en een blog uit 2020 waarin een .NET Core-transitie op circa 8.000 ontwikkeluren werd geraamd en als onbetaalbaar werd beoordeeld. WordPress staat op 7.1.2 (22 september 2026).

### 2.2 Wat een framework is

Een framework is geen af product maar een *startpunt*. Het levert bouwstenen; de applicatie is eigen code van de eerste regel af:

- **ASP.NET Core MVC** - routing, dependency injection, model binding, validatie, authenticatie/autorisatie met policies, Entity Framework Core als ORM met migraties, en een ingebouwd testmodel.
- **Laravel** - routing, Eloquent als ORM met migrations en seeds, form requests voor validatie, policies en gates voor autorisatie, queues, events, en een testlaag op basis van PHPUnit/Pest.
- **React met Next.js** en **Vue met Nuxt** - componentgebaseerde frontends met SSR, SSG en incrementele regeneratie, routing, databinding en een eigen buildpijplijn. Deze twee zijn primair presentatielaag. Ze hebben wel een eigen serverruntime - route handlers, server actions, Nitro - maar zijn niet bedoeld als vervanging van een domeinbackend.

De prijs is even direct: er is geen beheerinterface, geen gebruikersbeheer met knoppen, geen redactieomgeving. Alles wat een CMS meelevert, moet bewust gebouwd of toegevoegd worden. Wat je ervoor terugkrijgt, is dat **het gedrag van de applicatie volledig in de repository staat**.

> **FEIT**
>
> De vier frameworks lopen sterk uiteen in de hardheid van hun ondersteuningsbeloften. .NET kent een expliciet beleid: even versienummers zijn LTS met drie jaar ondersteuning, oneven versies STS met 24 maanden; .NET 10 (11 november 2025) loopt tot 14 november 2028. Laravel publiceert per versie een tabel met 18 maanden bugfixes en 2 jaar securityfixes; Laravel 13 verscheen 17 maart 2026. Next.js hanteert sinds kort een eigen LTS-model: de huidige major is Active LTS, de vorige major krijgt kritieke bug- en securityfixes tot twee jaar na zijn eerste release. Nuxt belooft minimaal zes maanden ondersteuning na het verschijnen van de volgende major. React en Vue publiceren géén ondersteuningsvenster voor oudere versies; React zegt alleen securityfixes te backporten naar alle getroffen majors.

### 2.3 De keten in beeld

Het verschil is het scherpst te zien aan de weg die één gebruikersactie aflegt, en aan hoeveel schakels in die weg tijdens runtime door configuratie of door losse uitbreidingen bepaald worden (geel gemarkeerd):

**Keten bij een CMS met plugins/modules**

`Gebruiker  →  Thema / skin  →  Plugin A  →  Hook-keten  →  Plugin B  →  CMS-core  →  Database`

*Buiten de broncode aanpasbaar: Thema / skin, Plugin A, Hook-keten, Plugin B.*

**Keten bij een framework**

`Gebruiker  →  Component / view  →  Controller  →  Domeinservice  →  Repository / ORM  →  Database`

Beide ketens zijn legitiem, en de bovenste is niet "slecht". Het verschil is dat in de bovenste keten vier van de zeven schakels *buiten de broncode om* gewijzigd kunnen worden, door een beheerder of door een plugin-update. Bij een klein, contentgericht systeem is dat precies de gewenste flexibiliteit. Bij een systeem met complexe regels is het de plek waar het grootste deel van de onverklaarbare problemen ontstaat - en waar een ontwikkelaar het moeilijkst kan aantonen dat een fout weg is.

### 2.4 Het kantelpunt

De kern van dit rapport laat zich in één afweging samenvatten. Zet op de ene as hoeveel *content* een systeem bevat, en op de andere hoeveel *gedrag*:

**Veel content, weinig gedrag**

Een corporate site, een nieuwsportaal, een informatieve intranetpagina. De regels zijn "wie mag publiceren" en "wat staat waar". Een CMS wint hier op vrijwel elk criterium: sneller, goedkoper, zelfstandig te beheren.

**Weinig content, veel gedrag**

Een planningsapplicatie, een aanvraag- of goedkeuringsproces, een klantportaal met transacties. De regels zijn voorwaardelijk, samengesteld en veranderen. Een framework wint hier, om de redenen in hoofdstuk 3.

**Veel content én veel gedrag**

Het meest voorkomende geval bij grote organisaties - en precies de situatie waarin de hybride scheiding uit 8.2 het beste werkt: CMS voor de content, framework voor het domein, met één koppelvlak ertussen.

**Weinig van beide**

Een campagnesite, een landingspagina, een eenmalig formulier. Hier weegt bouwtijd het zwaarst en is de goedkoopste route vrijwel altijd de juiste - CMS, sitebuilder of een statische generator.

De rest van dit rapport onderbouwt waarom dat kantelpunt bestaat en waar het ongeveer ligt.

## Hoofdstuk 3 - Analyse per criterium

*Achttien criteria, steeds met het argument, met een tegenwerping waar die er is, en met een conclusie die zo eerlijk mogelijk is over hoe hard het punt werkelijk is. Kosten krijgen een eigen hoofdstuk (4).*

### 3.1 Schaalbaarheid

Schaalbaarheid gaat over twee dingen die vaak door elkaar lopen: méér gelijktijdige gebruikers aankunnen, en méér functionaliteit kunnen dragen zonder dat het systeem onhoudbaar wordt. Frameworks doen het op beide beter, maar om verschillende redenen.

**Technisch schalen.** Een framework-applicatie is in de regel *stateless* te maken: sessie in Redis of in een token, geen lokale bestandsafhankelijkheid, en dan horizontaal te schalen achter een load balancer. ASP.NET Core en Laravel zijn hier expliciet op ingericht; Next.js en Nuxt schalen bovendien via SSG en incrementele regeneratie, waarbij een groot deel van het verkeer nooit een applicatieserver raakt. Een CMS kan dat ook - DNN met een webfarm, WordPress achter een full-page cache - maar met meer randvoorwaarden: gedeelde bestandsopslag, plugins die lokale state bijhouden, en caching die precies daar stopt waar de pagina persoonlijk wordt.

**Dat laatste is het echte verschil.** Cache is de belangrijkste schaalstrategie van een CMS, en cache werkt slecht bij gepersonaliseerde content. Een publieke nieuwspagina is één keer te renderen voor iedereen; een agendaweergave waarin elke musicus zijn eigen afspraken, zijn eigen bezetting en zijn eigen bladmuziek ziet, is dat niet. Precies op het punt waar een applicatie complex wordt, valt de belangrijkste schaalstrategie van het CMS weg.

**Functioneel schalen.** Een framework-applicatie kan gesplitst worden: een leespad met een eigen, geoptimaliseerde query, een zwaar rapportageproces in een achtergrondqueue, een integratie in een aparte service. Bij een CMS loopt vrijwel alles door dezelfde requestcyclus en dezelfde hook-keten, ook als maar één onderdeel zwaar is.

> **TEGENWERPING**
>
> Schaalbaarheid is voor veel projecten een *theoretisch* probleem. Een intranet met 200 gebruikers loopt op geen enkel platform tegen een grens aan, en een CMS met goede caching bedient moeiteloos honderdduizenden bezoekers per maand. Wie schaalbaarheid als doorslaggevend argument gebruikt zonder belastingcijfers, bouwt een oplossing voor een probleem dat hij niet heeft. Het argument telt pas mee als er gepersonaliseerde content, zware queries of een zichtbaar groeipad zijn.

**Conclusie:** frameworks schalen beter, maar het verschil is pas relevant bij personalisatie, zware queries of aantoonbare groei. **[Framework]**

### 3.2 Performance

Hier is eerlijkheid belangrijker dan retoriek, want dit is het criterium waarop de meeste ongefundeerde claims worden gemaakt.

> **FEIT - MET CAVEAT**
>
> Volgens het HTTP Archive Web Almanac 2025 haalt 48% van de mobiele origins wereldwijd een voldoende op de Core Web Vitals. In het CMS-hoofdstuk van datzelfde rapport haalt 45% van de WordPress-sites dat op mobiel, tegen 74% voor Wix, 79% voor TYPO3 en 85% voor Duda. Een recenter overzicht op basis van het Core Web Vitals Technology Report (meetmoment april 2026) komt voor WordPress op circa 49%, tegen circa 67% voor Astro, 64% voor Drupal en 58% voor Joomla.

> **CAVEAT - WAAROM DIT GÉÉN BEWIJS IS DAT FRAMEWORKS SNELLER ZIJN**
>
> Dit zijn **veldmetingen over verschillende populaties websites**, geen gecontroleerd experiment. WordPress-origins in deze datasets zijn oververtegenwoordigd in het mkb en de lange staart, met zware thema's en veel externe scripts; origins op moderne frameworks zijn gemiddeld nieuwer, door ontwikkelaars gebouwd en vaak beter gehost. Het Web Almanac zelf schrijft de variatie primair toe aan implementatiekeuzes en niet aan het platform. Let er bovendien op dat Astro in dit rijtje zelf een JavaScript-framework is: het cijfer komt uit dezelfde dataset als dat van WordPress en heeft dus dezelfde beperking. Voor Next.js, Nuxt en React publiceert geen enkele geraadpleegde bron een vergelijkbaar percentage; die cijfers zijn alleen uit het interactieve dashboard te lezen en zijn daarom niet in dit rapport opgenomen. De verdedigbare uitspraak is: "WordPress-sites halen in de praktijk minder vaak een voldoende dan de meeste andere platformen." De onverdedigbare uitspraak is: "een framework is X% sneller."

Eén cijfer verdient nog aparte aandacht: met circa 49% ligt WordPress in de meting van april 2026 ongeveer op het wereldwijde gemiddelde van 48% uit 2025. De achterstand bestaat dus ten opzichte van de best presterende platformen, niet ten opzichte van het web als geheel - en de twee percentages komen bovendien uit verschillende meetmomenten.

Wat wél technisch te onderbouwen is, zijn de *mechanismen*:

- **Renderstrategie.** Next.js en Nuxt kunnen per route kiezen tussen statisch genereren, server renderen of client renderen. Een CMS rendert in de regel elke pagina op dezelfde manier en compenseert achteraf met cache.
- **Controle over queries.** In een framework bepaalt de ontwikkelaar welke query draait, welke kolommen hij ophaalt en welke index hij gebruikt. In WordPress lopen veel plugin-queries via `wp_postmeta`, waar filteren op een eigenschap een join op een sleutel-waardetabel betekent in plaats van een indexlookup op een kolom.
- **Payload.** Een framework-frontend bundelt alleen wat de pagina nodig heeft en kan per route splitsen. Een CMS laadt de scripts en stylesheets van alle actieve plugins, ook op pagina's waar die plugin niets doet - een bekend en goed gedocumenteerd patroon bij WordPress.
- **Waar de winst níet zit.** Serverrekentijd is zelden de bottleneck. In de praktijk zitten de grootste verliezen in externe scripts, afbeeldingen en render-blocking CSS - en die zijn op beide platformen even goed of slecht op te lossen.

**Conclusie:** een framework geeft meer gereedschap om performance te sturen, maar levert het niet vanzelf. Een slecht gebouwde Next.js-applicatie is trager dan een goed verzorgde WordPress-site. **[Framework, mits benut]**

### 3.3 Onderhoudbaarheid

Onderhoudbaarheid is de vraag: als iemand over twee jaar het gedrag moet wijzigen, hoe lang duurt het dan om te vinden waar dat gedrag staat, en hoe zeker is hij dat hij niets anders breekt?

Neem een concrete regel uit Tutti: *een remplaçant ziet de bladmuziek van een productie pas zodra hij voor een afspraak van die productie is ingedeeld, en niet meer nadat de productie is afgesloten.* Die ene zin raakt vier dingen: een rol, een koppeling, een status en een datum.

**In een framework**

De regel staat op één plek - bijvoorbeeld een methode `MagPartijZien(persoon, productie)` in een `BladmuziekPolicy`. Wie hem wil wijzigen, zoekt op de naam, vindt één bestand, en ziet via de aanroepende code precies waar hij geldt. De compiler of de statische analyse wijst aan wat er breekt.

**In een CMS**

Dezelfde regel is realistisch verdeeld over: een rechteninstelling op de module, een filter in een query-definitie, een `if` in een template, en een datumcontrole in een script. Vier plekken, drie ervan buiten de repository. Wie er één vergeet, krijgt geen foutmelding - alleen een remplaçant die iets ziet dat hij niet mag zien.

Dit is geen theoretisch bezwaar maar het meest voorspelbare gevolg van het uitbreidingsmodel zelf: hooks en configuratie zijn ontworpen om gedrag op afstand te kunnen wijzigen, en dat is precies wat "verspreid" betekent.

**De tegenwerping is reëel:** een framework-applicatie met een slechte structuur is even onvindbaar. Een 900 regels lange controller met domeinlogica erin is niet beter dan vier plugins. Het verschil is dat het framework de *mogelijkheid* biedt om het goed te doen en het CMS die mogelijkheid deels wegneemt - niet dat het framework het vanzelf goed doet.

**Conclusie:** frameworks maken gedrag vindbaar en toetsbaar; CMS-platformen verspreiden het structureel. **[Framework]**

### 3.4 Codekwaliteit en architectuur

Een framework dwingt geen goede architectuur af, maar maakt er wel ruimte voor. De gangbare indeling - presentatie, applicatie, domein, infrastructuur - is in ASP.NET Core en Laravel direct uit te drukken in projecten, namespaces en dependency injection, en de afhankelijkheidsrichting is af te dwingen met architectuurtests of met projectreferenties die de compiler controleert.

Bij een CMS is die indeling lastiger vol te houden, om drie redenen:

- **Het CMS bepaalt de instapplek.** Een WordPress-plugin begint bij een hook; een DNN-module bij een control of een controller die het platform aanroept. De structuur daaronder is vrij, maar de bovenkant ligt vast.
- **Het domeinmodel is niet van jou.** In WordPress is vrijwel alles een `post` met meta. Een "Productie" met een productienummer, een status en een reeks afspraken is geen post - maar wordt er wel een, omdat het schema dat oplegt. DNN is hier duidelijk sterker: een eigen module mag eigen tabellen aanmaken, en dat is de reden dat DNN voor complexe applicaties bruikbaarder is dan WordPress.
- **Statische typering ontbreekt vaak waar het telt.** In een framework is een `Afspraak` een klasse met eigenschappen; hernoem je er een, dan faalt de build. In een CMS is diezelfde afspraak vaak een verzameling sleutel-waardeparen, waarin een typefout in een veldnaam pas in productie zichtbaar wordt.

> **AANNAME**
>
> Dit argument veronderstelt dat het team architectuur daadwerkelijk toepast: lagen scheiden, afhankelijkheden één kant op laten wijzen, domeinlogica buiten controllers houden. Gebeurt dat niet, dan vervalt het grootste deel van het voordeel en houd je alleen de hogere bouwkosten over.

**Conclusie:** frameworks maken een expliciete architectuur mogelijk en controleerbaar; CMS-platformen leggen een deel van de structuur op en het datamodel vaak helemaal. **[Framework]**

### 3.5 Flexibiliteit

Flexibiliteit is het criterium waarop de vergelijking het vaakst misgaat, omdat het woord twee tegengestelde dingen betekent.

**Flexibiliteit voor de beheerder**

Een redacteur die zelf een veld toevoegt, een pagina herschikt of een blok verplaatst, zonder ontwikkelaar en zonder release. Hierin wint het CMS overtuigend - dat is waar het voor gebouwd is.

**Flexibiliteit voor de ontwikkelaar**

Elk gedrag kunnen implementeren zoals het probleem het vraagt, zonder te hoeven passen binnen het model van het platform. Hierin wint het framework, want er is geen model waarbinnen iets moet passen.

Bij een contentsite is de eerste soort waardevol en de tweede overbodig. Bij een applicatie met rechten draait dat om: een beheerder die zelf een veld kan toevoegen aan een entiteit waaraan autorisatieregels hangen, kan buiten het zicht van de ontwikkelaar een beveiligingsgat maken. Dezelfde eigenschap is in de ene context een voordeel en in de andere een risico.

Een framework kan overigens gerichte beheerdersvrijheid gewoon aanbieden - een beheerscherm voor de zaken die veilig aanpasbaar zijn, zoals afspraaktypen, kleuren of e-mailteksten. Het verschil is dat die vrijheid dan *bewust is ontworpen en begrensd* in plaats van standaard aanwezig.

**Conclusie:** het CMS is flexibeler voor beheerders, het framework voor ontwikkelaars. Welke soort telt, hangt volledig af van het type systeem. **[Genuanceerd]**

### 3.6 Testbaarheid

Dit is een van de hardste verschillen, omdat het niet over smaak gaat maar over wat technisch mogelijk is.

In een framework is de regel uit 3.3 een functie met invoer en uitvoer. Een test ziet eruit als: *gegeven een remplaçant die niet is ingedeeld, en een productie met status "gepubliceerd", verwacht dat `MagPartijZien` onwaar teruggeeft.* Die test draait in milliseconden, zonder database, zonder browser, en faalt onmiddellijk als iemand de regel later stilletjes wijzigt. De backendframeworks leveren hiervoor een standaardopzet: xUnit of NUnit met de ingebouwde testhost van ASP.NET Core, PHPUnit of Pest bij Laravel. React en Vue leveren zelf geen testopzet, maar Vitest met Testing Library is daar de gangbare combinatie.

In een CMS is diezelfde regel verspreid over configuratie, query, template en script (3.3). Er is geen functie om aan te roepen. Wat overblijft is een end-to-end test: browser starten, inloggen als remplaçant, naar een productie navigeren, controleren dat de downloadknop er niet staat. Zulke tests zijn waardevol maar traag, bewerkelijk in onderhoud en instabiel - en ze breken bij elke themawijziging, ook als het gedrag correct is. Het gevolg is voorspelbaar: ze worden minder vaak geschreven en sneller uitgezet.

**De nuance:** DNN-modules met een eigen servicelaag zijn wél unit-testbaar, precies omdat het gewone C#-klassen zijn. Het verschil is dan niet absoluut maar gradueel: de *eigen* code is testbaar, de configuratie en de interactie met andere modules niet.

**Conclusie:** frameworks maken de kern van de logica direct en snel testbaar; bij een CMS blijft een substantieel deel van het gedrag alleen end-to-end te controleren. **[Framework]**

### 3.7 CI/CD en DevOps

Een geautomatiseerde straat werkt alleen als geldt: *wat in versiebeheer staat, is wat er draait.* Dat uitgangspunt heet reproduceerbaarheid, en het is de scheidslijn op dit criterium.

Bij een framework klopt die aanname vrijwel volledig. Code, databaseschema (via migraties), configuratie en afhankelijkheden staan alle vier in de repository. De pijplijn is standaard: build, tests, statische analyse, artefact, uitrol naar test, uitrol naar productie, en bij een fout terug naar de vorige versie.

Bij een CMS klopt de aanname gedeeltelijk. De code van eigen modules staat in versiebeheer, maar daarnaast leeft er state in de database die het gedrag bepaalt: paginastructuur, moduleplaatsingen, rechteninstellingen, plugin-opties, contenttypedefinities. Die state wordt in de beheerinterface gemaakt en verschilt daardoor per omgeving. Het gevolg is **configuratiedrift**: test en productie lopen uiteen zonder dat iemand code heeft gewijzigd, en een bug die op test niet reproduceerbaar is, kost dagen.

> **RISICO**
>
> Configuratiedrift is bij CMS-projecten een veelvoorkomende en lastig te diagnosticeren oorzaak van "het werkte op test". Er bestaan tegenmaatregelen - exports in versiebeheer, WP-CLI-scripts, DNN-pakketten - maar ze vragen discipline die het platform niet afdwingt, en ze dekken zelden alles. Bij een framework is het probleem grotendeels weggenomen doordat er simpelweg minder state buiten de code bestaat.

> **TEGENWERPING**
>
> Een deel van die state is wél in versiebeheer te krijgen: WP-CLI-scripts en `wp config` kunnen WordPress-configuratie als code vastleggen, en DNN-installatiepakketten kunnen paginastructuur en moduleplaatsing meenemen. Een CMS-project dat die middelen consequent inzet, komt een heel eind. Het verschil is dat het platform dat niet afdwingt en dat de dekking zelden volledig is - bij een framework is het de standaardsituatie in plaats van een discipline die je erbij organiseert.

**Conclusie:** frameworks zijn substantieel beter reproduceerbaar en daarmee beter automatiseerbaar. **[Framework]**

### 3.8 Security

Op dit criterium is het beeld gemengder dan vaak wordt voorgesteld, en het loont om twee soorten risico te scheiden.

**Risico 1 - kwetsbaarheden in wat je installeert.** Hier is het verschil groot en goed gedocumenteerd.

> **FEIT**
>
> Patchstack registreerde over 2025 **11.334 nieuwe kwetsbaarheden** in het WordPress-ecosysteem, een stijging van 42% ten opzichte van 2024. Daarvan zat ruim **91% in plugins** en bijna 9% in thema's; in WordPress core zelf werden 6 kwetsbaarheden gevonden - minder dan een tiende procent van het totaal - alle als laag beoordeeld. **46% was op het moment van publieke bekendmaking nog niet gepatcht.** Wordfence kwam over 2024 op 8.223 kwetsbaarheden met een vergelijkbare verdeling (96% plugins, 5 in core). De twee tellingen komen uit verschillende databases met verschillende criteria en zijn niet één op één vergelijkbaar.

De conclusie die deze cijfers wél dragen: het probleem zit niet in de CMS-kern maar in het *uitbreidingsecosysteem*. WordPress core is, gemeten naar gevonden kwetsbaarheden, opvallend solide. Het aanvalsoppervlak ontstaat doordat een typische site een aanzienlijk aantal plugins draait van evenzoveel verschillende auteurs, met uiteenlopende onderhoudskwaliteit en zonder gedeelde beveiligingsreview.

> **FEIT**
>
> De OWASP Top 10:2025 voegt **A03 - Software Supply Chain Failures** toe als nieuwe categorie. Dat is precies het risicotype dat een plugin-ecosysteem vergroot: je vertrouwt code van derden die met volledige rechten binnen je applicatie draait.

**Risico 2 - fouten in je eigen code.** Hier draait het argument om. Een framework-applicatie bestaat uit eigen code, en elke autorisatiecontrole die je vergeet te schrijven, is er een die er niet is. Een CMS levert een beproefde authenticatie-, sessie- en rechtenlaag mee die door duizenden installaties is uitgetest. Wie zelf bouwt, neemt die verantwoordelijkheid over.

Wat een framework daar tegenover zet, is niet "minder fouten" maar **aantoonbaarheid**: autorisatie op één plek (een policy, een authorization handler), deny-by-default als uitgangspunt, en permissietests per endpoint inclusief de negatieve gevallen. De vraag "kan een remplaçant die niet is ingedeeld deze partij downloaden?" is dan te beantwoorden met een test die draait, niet met een klik door de beheerinterface.

> **FEIT - MET CAVEAT**
>
> Sucuri rapporteerde over 2023 dat **39,1%** van de door hen opgeschoonde besmette sites een verouderd CMS draaide op het moment van infectie. 95,5% van de gehackte sites in dat onderzoek draaide WordPress - maar dat cijfer volgt grotendeels uit het marktaandeel van WordPress en uit Sucuri's eigen klantenbestand, en is *geen* besmettingskans per site. Sucuri heeft na de editie over 2023 geen nieuw jaarrapport gepubliceerd.

> **REIKWIJDTE VAN DIT CRITERIUM**
>
> Alle cijfers hierboven gaan over het WordPress-ecosysteem, omdat daar publieke meetreeksen voor bestaan. Voor DNN bestaan vergelijkbare openbare cijfers niet, en de DNN-markt voor modules is vele malen kleiner. Dit criterium is daarom *voor WordPress* hard te maken en niet CMS-breed. Wat wel voor beide geldt, is het mechanisme: elke geïnstalleerde uitbreiding draait met volledige rechten binnen de applicatie.

**Conclusie:** WordPress levert een beproefde beveiligingsbasis maar sleept via plugins een aanzienlijk en meetbaar aanvalsoppervlak mee; voor DNN geldt hetzelfde mechanisme zonder dat er cijfers bij te leggen zijn. Een framework verkleint dat oppervlak en maakt autorisatie toetsbaar, maar legt de verantwoordelijkheid volledig bij het team. **[Framework, mits getest]**

### 3.9 Integraties en API's

Grote applicaties staan zelden alleen. Tutti koppelt aan OPAS voor de planning, aan AFAS voor personeelsgegevens, aan SharePoint en ADAM voor documenten en aan Microsoft 365 voor authenticatie. Dat is een normaal beeld voor een bedrijfskritisch systeem.

Bij een framework is een integratie gewone applicatiecode: een client met een eigen interface, foutafhandeling met retries en een circuit breaker, een achtergrondqueue voor werk dat mag wachten, een mapping tussen het externe en het eigen model, en tests met een dubbel voor de externe partij. Voor de backendframeworks zijn hiervoor volwassen voorzieningen beschikbaar: `IHttpClientFactory` met `Microsoft.Extensions.Http.Resilience` of Polly in .NET, en queues, jobs en events in Laravel. Next.js en Nuxt bieden route handlers voor de serverkant, maar geen vergelijkbare resilience- of queuelaag - die hoort dan ook in de backend.

Bij een CMS lopen integraties meestal via een plugin of een module van derden. Dat is snel als er een bestaande koppeling is, en moeizaam zodra de koppeling net anders moet: een veld dat niet wordt overgenomen, een retrystrategie die niet instelbaar is, of een foutafhandeling die je niet kunt zien. Je hebt dan de keuze tussen de plugin forken - waarmee je het updatepad opgeeft - of eromheen bouwen.

**Het omgekeerde geldt ook.** Als de benodigde koppeling wél bestaat en goed onderhouden is, is de CMS-route dramatisch goedkoper: een dag configureren tegen weken bouwen. Het argument is dus niet "frameworks integreren beter", maar "frameworks integreren *voorspelbaarder*": de kosten hangen af van je eigen werk en niet van het bestaan en de kwaliteit van een plugin.

**API's aanbieden** is een vergelijkbaar beeld. Zowel WordPress als DNN biedt REST-endpoints, maar de vorm daarvan volgt het platform en het datamodel. Wie een contract wil dat past bij zijn eigen domein - versioneerbaar, met eigen foutcodes en een eigen autorisatiemodel - bouwt dat in een framework aanzienlijk directer.

**Conclusie:** een framework maakt integratiekosten voorspelbaar en het API-contract eigendom van het project. **[Framework]**

### 3.10 Database- en datamodelvrijheid

Dit is het criterium waarop WordPress en DNN duidelijk uit elkaar lopen, en dat verdient expliciete behandeling.

**WordPress** heeft een vast kernschema. Een plugin kan eigen tabellen aanmaken via `$wpdb` en `dbDelta()` - WooCommerce doet dat voor zijn ordertabellen - maar doet daarmee afstand van wat de kern gratis levert: de beheerinterface, de REST-API, zoeken, revisies en het rechtenmodel werken alleen op posts. Dat is de reden dat de meeste plugins alsnog custom post types met custom fields gebruiken, en die slaan hun waarden op in `wp_postmeta`: een sleutel-waardetabel. De praktische gevolgen bij een groeiend datamodel:

- er is geen foreign key tussen een afspraak en een productie - de relatie is een meta-waarde die naar een id verwijst, zonder dat de database die relatie kent of bewaakt;
- filteren en sorteren op meerdere eigenschappen betekent meerdere joins op dezelfde meta-tabel in plaats van een indexlookup op kolommen;
- een unique constraint als "één musicus komt per afspraak maar één keer in de bezetting voor" is niet in de database af te dwingen en moet in PHP worden bewaakt - waar een gelijktijdige tweede aanvraag er alsnog langs kan.

**DNN** is hier wezenlijk beter, niet omdat eigen tabellen er technisch wél kunnen en bij WordPress niet, maar omdat ze er de *normale* werkwijze zijn: een module met eigen tabellen, sleutels, constraints en indexes blijft volwaardig meedoen met het platform. Wie in DNN een echte applicatie bouwt, heeft op dit punt grotendeels de vrijheid van een framework. Dat is de belangrijkste reden dat DNN voor complexe maatwerktoepassingen een serieuzere basis is dan WordPress.

Bij een framework is dit geen onderwerp: het schema *is* het datamodel. Entity Framework Core en Eloquent leveren migraties die in versiebeheer staan, en de database doet waar hij goed in is - referentiële integriteit bewaken, uniciteit afdwingen en met indexes snel zoeken.

**Conclusie:** frameworks geven volledige datamodelvrijheid, DNN grotendeels, en WordPress alleen ten koste van de platformvoordelen die de reden waren om WordPress te kiezen. Het verschil wordt groter naarmate het model meer relaties en regels bevat. **[Framework (DNN dichtbij, WordPress met inlevering)]**

### 3.11 Afhankelijkheid van modules en plugins

Dit is het criterium waar de gevraagde vergelijking het concreetst wordt. Neem één functionaliteit uit Tutti: **een enquête per productie die pas 24 uur na de laatste afspraak opengaat, drie maanden later sluit, alleen zichtbaar is voor wie daadwerkelijk in de bezetting stond, en waarvan de antwoorden per productienummer én per persoon terug te zoeken moeten zijn.**

**Route via een CMS**

Een formulierplugin of -module levert het grootste deel direct: velden, opslag, e-mailbevestiging, export. De rest - het tijdvenster dat van een afspraakdatum afhangt, de zichtbaarheid die van de bezetting afhangt - moet via de uitbreidingspunten van die plugin. Wat die plugin niet aanbiedt, kun je niet doen zonder hem te wijzigen.

**Route via een framework**

Alles is eigen code. Het tijdvenster is een methode `IsOpen(productie, nu)`, de zichtbaarheid een policy, de opslag een tabel met een foreign key naar productie én persoon. Het deel dat de plugin gratis gaf, moet je bouwen; het deel dat de plugin blokkeerde, is een gewone dag werk.

De afweging draait dus om de verhouding tussen die twee delen. Zolang de standaardfunctionaliteit het grootste deel dekt, wint het CMS. Zodra de uitzonderingen het werk gaan bepalen, keert dat om. Of dat gebeurt, is een empirische vraag per project en geen wetmatigheid - maar in applicaties met rechten, statussen en integraties is het een herkenbaar patroon.

Daarbovenop komen vier afhankelijkheidsrisico's die los van de functionaliteit bestaan:

- **Onderhoud.** Een plugin waar je functionaliteit op leunt, kan door zijn auteur worden gestaakt. Dan sta je voor de keuze: zelf overnemen, vervangen, of blijven draaien op code die geen securityfixes meer krijgt.
- **Overname en licentiewijziging.** Plugins worden verkocht, en nieuwe eigenaren veranderen prijsmodellen of voegen telemetrie toe. Dat is een commercieel risico dat je contractueel niet hebt afgedekt.
- **Onderlinge conflicten.** Twee plugins die op dezelfde hook ingrijpen, geven een uitkomst die van laadvolgorde afhangt - en die volgorde is zelden expliciet.
- **Ongebruikte last.** Elke plugin die je voor één functie installeert, brengt zijn hele codebase, zijn scripts en zijn aanvalsoppervlak mee.

**De eerlijke tegenwerping:** een framework heeft óók afhankelijkheden. Een gemiddeld Next.js- of Laravel-project trekt honderden npm- of Composer-pakketten binnen, en die dragen precies dezelfde supply-chainrisico's. Het verschil zit in de *positie* van de afhankelijkheid: een npm-pakket is meestal een bibliotheek die je aanroept en die je kunt vervangen, terwijl een plugin gedrag toevoegt binnen de draaiende applicatie, met volledige rechten, vaak zonder dat je precies weet wat hij doet. Het verschil is reëel maar gradueel, niet absoluut.

**Conclusie:** bij een CMS is de afhankelijkheid structureel en zit ze in het functionele hart; bij een framework is ze breder maar beter begrensd en vervangbaar. **[Framework]**

### 3.12 Upgrade-risico's

Een upgrade is bij beide een risico, maar het risico heeft een andere vorm.

**Bij een CMS** is de upgrade een gebeurtenis met een onbekend aantal bewegende delen: de kern, een thema en - bij WordPress - vaak tientallen plugins, elk met een eigen releaseritme en een eigen opvatting over compatibiliteit. Het moeilijkste geval is niet een plugin die breekt - dat zie je meteen - maar een plugin die niet meegaat met een nieuwe kernversie en waarvoor geen alternatief bestaat. Dan heb je de keuze tussen twee onwenselijke opties: niet upgraden en het securityrisico accepteren, of upgraden en functionaliteit verliezen.

**Bij een framework** is de upgrade een voorspelbaarder project: één of enkele majors met een gepubliceerde upgrade-gids, breaking changes die gedocumenteerd zijn, en een testsuite die aanwijst wat er stukging. De kosten zijn niet lager, maar ze zijn *vooraf in te schatten* - en dat is bij planning het punt dat telt.

> **FEIT**
>
> WordPress ondersteunt officieel alleen de laatste versie, maar het securityteam backport fixes naar oudere branches "als service". Dat gebeurt aantoonbaar: de gecoördineerde securityrelease van 17 september 2026 patchte negentien branches tegelijk, tot en met 5.3. Het is echter een coulanceregeling zonder gepubliceerde einddatum per branch - niet iets waar een organisatie een meerjarenplanning op kan bouwen. DNN publiceert geen formele LTS-regeling; de ondersteuning volgt in de praktijk de nieuwste 10.x-release.

**De tegenwerping voor frameworks is stevig en verdient eerlijke behandeling:** het JavaScript-ecosysteem veroudert sneller dan een CMS. Nuxt 3 is sinds 31 juli 2026 uit ondersteuning, Next.js 15 valt op 21 oktober 2026 uit de Maintenance-LTS, en React en Vue publiceren geen ondersteuningsvenster voor oudere versies. Een React/Next-frontend vraagt daardoor structureel meer onderhoudsaandacht dan een WordPress-thema. De backendframeworks staan er beduidend beter voor: .NET 10 loopt tot 14 november 2028 en Laravel 13 krijgt securityfixes tot 17 maart 2028, beide met een gepubliceerde datum.

**Conclusie:** upgraden is bij een framework beter planbaar, maar in het JavaScript-deel van de stack ook vaker aan de orde. **[Framework voor planbaarheid]**

### 3.13 Vendor lock-in

Lock-in is geen ja-of-nee-vraag maar een vraag naar *wat* vastzit en *hoe duur* het is om los te komen.

- **Bij een CMS** zit je vast aan het platform én aan de plugins. De content is meestal nog te exporteren, maar het gedrag zit in plugins die op geen enkel ander platform bestaan. Migreren betekent in de praktijk herbouwen.
- **Bij DNN** komt daar een specifieke afhankelijkheid bij: het platform draait op .NET Framework met IIS en SQL Server, en er is geen gepubliceerd pad naar .NET Core. Oqtane, het moderne .NET-alternatief van dezelfde oorspronkelijke auteur, is expliciet géén migratiepad - het deelt naar eigen zeggen geen technische gelijkenis met DNN en modules en thema's zijn niet overdraagbaar.
- **Bij een backendframework** is de lock-in beperkt tot het framework zelf, en de domeinlogica is daar grotendeels van los te houden. Een `BladmuziekPolicy` in een aparte class library is gewone C# of PHP en overleeft een framework-migratie.
- **Bij een frontendframework** is de lock-in juist reëler dan vaak erkend wordt. React-componenten zijn niet naar Vue over te zetten zonder herschrijven, en Next.js-specifieke functionaliteit bindt bovendien deels aan de conventies van dat framework en aan hostingplatformen die daarop zijn ingericht.

**Conclusie:** op dit criterium wint het framework alleen als je er bewust naar ontwerpt - domeinlogica in een framework-onafhankelijke laag. Doe je dat niet, dan is het verschil met een CMS kleiner dan het lijkt. **[Genuanceerd]**

### 3.14 Technische schuld

Beide benaderingen bouwen schuld op, maar van een verschillend soort - en dat verschil bepaalt of de schuld aflosbaar is.

**Schuld in een framework**

Zit in de eigen code: een te grote klasse, logica in de verkeerde laag, een ontbrekende test. Vervelend, maar *zichtbaar en aflosbaar*: refactoren kan, de tests vangen de gevolgen op, en de compiler wijst de plekken aan.

**Schuld in een CMS**

Zit vaak in keuzes die niet te herzien zijn zonder migratie: een entiteit die als post-type is gemodelleerd, een plugin die het hart van een proces draagt, een workflow die in configuratie is vastgelegd. *Onzichtbaar en moeilijk aflosbaar* - je kunt het niet refactoren, je kunt het alleen vervangen.

Het verschil is dus niet in de eerste plaats hoeveel schuld ontstaat, maar of je hem kunt aflossen. Bij een framework is de schuld zichtbaar en in delen af te lossen; bij een CMS blijft ze lang onzichtbaar en wordt ze uiteindelijk in één keer opeisbaar, in de vorm van een migratie.

**De tegenwerping:** een framework zonder discipline stapelt schuld sneller dan een CMS, juist omdat er geen kader is dat je tegenhoudt. Een CMS dwingt een minimum aan structuur af; een leeg framework-project doet dat niet.

**Conclusie:** frameworks maken technische schuld zichtbaar en aflosbaar - mits het team hem daadwerkelijk aflost. **[Framework]**

### 3.15 Beschikbaarheid van developers

Dit criterium wordt vaak in het voordeel van het CMS aangevoerd, en dat klopt maar gedeeltelijk.

> **FEIT**
>
> In de Stack Overflow Developer Survey 2025 gaf van de professionele ontwikkelaars **46,9%** aan het afgelopen jaar met React gewerkt te hebben, **21,5%** met Next.js, **21,3%** met ASP.NET Core, **18,4%** met Vue.js, **12,4%** met WordPress, **9,3%** met Laravel en **4,1%** met Nuxt.js. DotNetNuke komt in de enquête niet voor. Dit zijn gebruikspercentages over het afgelopen jaar, geen cijfers over beschikbaarheid op de arbeidsmarkt.

Twee dingen volgen hieruit. Ten eerste: de meestgebruikte frameworks - React, Next.js, ASP.NET Core en Vue - worden door méér professionele ontwikkelaars gebruikt dan WordPress, en voor een .NET-organisatie is ASP.NET Core-kennis breder beschikbaar dan DNN-kennis. Laravel en Nuxt scoren in deze enquête juist lager dan WordPress, wat laat zien dat "framework" op dit criterium geen homogene categorie is. Ten tweede, en belangrijker: **framework-kennis is overdraagbaar en CMS-kennis veel minder.** Een .NET-ontwikkelaar die nog nooit een regel Laravel heeft gezien, herkent daar binnen korte tijd routing, ORM, validatie en dependency injection. Een ontwikkelaar die DNN niet kent, moet portals, het modulemodel, skins en providers leren voordat hij productief is.

> **FEIT**
>
> W3Techs meet op 23 september 2026 dat WordPress op 40,2% van alle websites draait, goed voor 58,8% van de CMS-markt. DotNetNuke staat op 0,1% van alle websites - in de lange staart waar tientallen platformen dezelfde afronding delen. Voor de inhuurmarkt betekent dat: WordPress-kennis is breed beschikbaar, DNN-kennis is specialistisch en schaars.

**De tegenwerping:** marktaandeel is niet hetzelfde als beschikbaarheid van ontwikkelaars die een *complexe applicatie* kunnen bouwen. Veel WordPress-werk is sitebouw, en dat is een ander vak dan applicatie-ontwikkeling. Het cijfer van 40,2% zegt dus weinig over de vijver waaruit je voor een maatwerkproject vist.

**Conclusie:** voor complexe applicaties is de vijver bij frameworks groter en de kennis beter overdraagbaar; voor contentsites geldt het omgekeerde. **[Framework]**

### 3.16 Lange-termijnondersteuning

Het beeld is hier gemengd, en het is niet eerlijk om er een eenduidige winnaar van te maken.

| Technologie | Ondersteuningsbelofte | Concrete einddatum |
| --- | --- | --- |
| **.NET 10 (LTS)** | 3 jaar voor LTS, 24 maanden voor STS; gepubliceerd beleid | 14 november 2028 |
| **Laravel 13** | 18 maanden bugfixes, 2 jaar securityfixes; tabel per versie | 17 maart 2028 (security) |
| **Next.js 16** | Active LTS; vorige major 2 jaar Maintenance LTS | Next.js 15 tot 21 oktober 2026 |
| **Nuxt 4** | Minimaal 6 maanden na de volgende major | Nuxt 3 verliep 31 juli 2026 |
| **React 19 / Vue 3** | Geen ondersteuningsvenster voor oudere versies; alleen securitybackports naar getroffen majors | Geen gepubliceerde datum |
| **WordPress 7.1** | Officieel alleen de laatste versie; backports naar oudere branches als coulance | Geen gepubliceerde datum per branch |
| **DNN 10.3** | Geen gepubliceerde LTS-regeling; draait op .NET Framework 4.8 | Geen gepubliceerde datum |

Wat hieruit volgt is geen "frameworks winnen", maar iets preciezers: **de backendframeworks publiceren een contract, de frontendframeworks en de CMS-platformen grotendeels niet.** Wie op lange termijn wil kunnen plannen, kiest aan de backendkant .NET of Laravel - daar staan datums in een tabel waar een organisatie een meerjarenbegroting op kan bouwen.

Voor DNN geldt bovendien een structureel punt. .NET Framework 4.8 wordt ondersteund als onderdeel van Windows en heeft daarmee geen eigen einddatum - het loopt dus niet op korte termijn af. Maar het is ook *gesloten voor doorontwikkeling*: nieuwe taalfeatures, nieuwe bibliotheken en het grootste deel van het moderne .NET-ecosysteem komen alleen naar .NET 5+. Een platform dat daaraan vastzit, veroudert niet door een einddatum maar door stilstand.

**Conclusie:** de backendframeworks bieden de hardste ondersteuningsbeloften; het JavaScript-ecosysteem de zachtste; CMS-platformen zitten daartussenin met coulanceregelingen zonder datum. **[Genuanceerd, backendframeworks voorop]**

### 3.17 Geschiktheid voor maatwerk

Dit is het criterium waar het verschil het minst betwistbaar is, want het volgt direct uit de definitie. Een CMS is een product met uitbreidingspunten; maatwerk moet door die punten passen. Een framework heeft geen uitbreidingspunten omdat er geen product is om uit te breiden.

Het praktische gevolg is een verschil in *kostenverloop*. Bij een CMS is het eerste, grootste deel van de functionaliteit heel goedkoop en wordt elke volgende stap duurder, omdat je steeds verder van het model af komt te staan. Bij een framework is het begin relatief duur - je bouwt het fundament - en blijft de prijs per functie daarna vlakker. De precieze vorm van die twee curves is niet gemeten en verschilt per project; wat hier beweerd wordt, is de *richting* ervan.

Het omslagpunt ligt daarmee niet bij een bepaald aantal schermen maar bij de vraag: **hoeveel van wat we nodig hebben is standaard?** Is dat het meeste, dan is het CMS goedkoper en blijft dat zo. Is het een minderheid, dan betaal je bij het CMS eerst voor functionaliteit die je niet gebruikt, en daarna nog eens voor het omzeilen ervan.

Een herkenbaar patroon uit de praktijk: een CMS-project waarin steeds meer logica in losse scripts en custom code terechtkomt, bouwt feitelijk een applicatie binnen een CMS - met alle beperkingen van het CMS en geen van de voordelen van een framework. Dat is de duurste van alle uitkomsten, en het is de uitkomst die ontstaat wanneer de platformkeuze nooit expliciet is herzien.

> **TEGENWERPING**
>
> Veel projecten bereiken het omslagpunt nooit. Als de standaardfunctionaliteit blijft dekken - en dat is bij een groot deel van de websites het geval - dan is het CMS niet alleen aan het begin goedkoper maar blijft het dat over de hele levensduur. Het argument hierboven geldt voor de projecten waarin het maatwerk structureel toeneemt, niet voor projecten die stabiel blijven binnen het model.

**Conclusie:** naarmate het aandeel maatwerk stijgt, verschuift het kostenvoordeel in de regel naar het framework. **[Framework]**

### 3.18 Geschiktheid voor grote teams

Parallel werken vraagt drie dingen: duidelijke grenzen tussen werkpakketten, een betrouwbare manier om werk samen te voegen, en een vangnet dat aangeeft wanneer twee wijzigingen elkaar in de weg zitten.

Een framework levert alle drie via gewone software-engineeringpraktijk: modules of projecten met expliciete grenzen, branches en pull requests in Git, en een testsuite die bij het samenvoegen draait. Twee ontwikkelaars die aan verschillende services werken, raken elkaar niet.

Bij een CMS werkt het samenvoegen van *code* net zo goed - maar het samenvoegen van *configuratie* niet. Twee ontwikkelaars die op dezelfde ontwikkelomgeving een contenttype of een moduleplaatsing wijzigen, overschrijven elkaars werk zonder conflict en zonder melding, want die wijzigingen staan in de database en niet in Git. De gangbare oplossing is ieder een eigen omgeving met een eigen database, wat de synchronisatievraag uit 3.7 terugbrengt op teamniveau.

**De tegenwerping:** ook een framework-project kan een team in de weg zitten, bijvoorbeeld als alle logica in één bestand belandt of als er geen afgesproken laagindeling is. En de CMS-oplossing van "ieder een eigen omgeving" werkt in de praktijk prima voor de dagelijkse gang van zaken. Het punt is smaller dan het lijkt: die oplossing verplaatst het probleem naar het samenvoegen van databases, en daar bestaat geen merge-model voor - waar Git dat voor code wel heeft.

**Conclusie:** frameworks passen beter bij parallel werkende teams, vooral doordat al het gedrag in versiebeheer samen te voegen is. **[Framework]**

## Hoofdstuk 4 - Kosten op korte en lange termijn

### 4.1 Waarom de vergelijking meestal scheef wordt gemaakt

Platformkeuzes worden vaak verdedigd met de bouwkosten van de eerste versie, omdat dat het enige getal is dat vooraf bekend is. Dat getal is echter een slechte voorspeller van de totale kosten, want bij een systeem met een lange levensduur komt daar doorontwikkeling, onderhoud, beheer en het oplossen van problemen bij - en juist daar lopen de twee benaderingen uiteen.

> **AANNAME**
>
> Dit hoofdstuk gaat ervan uit dat bij een systeem met een levensduur van vijf tot tien jaar de initiële bouw niet de grootste kostenpost is. Dat is een waarneming uit de praktijk en geen cijfer uit een bron; wie over harde getallen beschikt voor het eigen projectportfolio, zou die hier in de plaats moeten zetten.

### 4.2 Korte termijn - het CMS wint

Dit hoeft geen betoog en moet niet weggeredeneerd worden. Een CMS levert op dag één een database, een beheerinterface, gebruikersbeheer, rechten, een mediabibliotheek en een redactieomgeving. Een framework levert een leeg project. Voor een eerste werkende versie is het CMS in vrijwel alle gevallen sneller en goedkoper, en voor een prototype of een proefopstelling is dat het enige criterium dat telt.

### 4.3 Lange termijn - waar de kosten omslaan

Vier kostenposten bepalen het verloop na de eerste oplevering:

- **Doorontwikkeling.** Bij een CMS wordt elke wijziging die niet in het model past duurder dan de vorige (3.17). Bij een framework blijft de prijs per functie vlakker, omdat het fundament al staat.
- **Foutzoeken.** Een fout die uit de interactie tussen vier configuratieplekken voortkomt, kost een veelvoud van een fout met een stack trace. Dit is de kostenpost die bij CMS-projecten het vaakst wordt onderschat, omdat hij niet op een offerte staat.
- **Onderhoud van afhankelijkheden.** Bij een CMS: plugin-updates, compatibiliteitscontroles en het vervangen van gestaakte plugins. Bij een framework: pakketupdates en periodieke majorupgrades - aan de JavaScript-kant vaker dan aan de backendkant (3.12).
- **Regressie.** Zonder automatiseerbare tests is elke release een handmatige controle. Dat is een terugkerende kostenpost die bij een framework grotendeels geautomatiseerd kan worden en bij een CMS maar gedeeltelijk (3.6).

> **AANNAME - EXPLICIET ALS ZODANIG**
>
> Er zijn in dit rapport geen bedragen, tarieven of terugverdientijden opgenomen, omdat daar geen betrouwbare, algemeen geldende cijfers voor bestaan. De uitspraak "een framework verdient zich terug" is bovendien niet in het algemeen houdbaar: ze hangt af van de levensduur van het systeem, het aandeel maatwerk en de kwaliteit van het team. Wat wél houdbaar is: het *kostenverloop* verschilt systematisch - het CMS start laag en loopt op, het framework start hoog en vlakt af. Waar de lijnen elkaar kruisen, is per project te schatten maar niet in het algemeen te stellen.

### 4.4 De kostenpost die bij beide wordt vergeten

Bij het CMS is dat **de migratie die je uiteindelijk toch doet**: het moment waarop de applicatie-binnen-een-CMS niet langer houdbaar is en er herbouwd moet worden, tegen de volle prijs en met een systeem dat in bedrijf moet blijven. Bij het framework is dat **het beheerwerk dat het CMS gratis leverde**: gebruikersbeheer, een redactieomgeving, een mediabibliotheek en exportfuncties die je zelf bouwt of inkoopt. Beide horen in de vergelijking thuis, en in de praktijk ontbreken ze er allebei vaak in.

## Hoofdstuk 5 - Nadelen en risico's van een framework-aanpak

*Acht nadelen die echt bestaan, met per nadeel de maatregel die het risico beperkt. Worden deze maatregelen niet genomen, dan is het advies in dit rapport niet houdbaar.*

**Hogere initiële kosten en langere doorlooptijd**

> *Beperking* - Lever een dunne verticale doorsnede op - één entiteit aanmaken, één actie uitvoeren, één keer terugzien - in plaats van eerst een compleet fundament. Dat geeft de opdrachtgever binnen weken iets werkends en toetst de aanpak vroeg.

**Geen beheerinterface uit de doos**

> *Beperking* - Reken beheerschermen vanaf het begin mee in de scope, of gebruik een bestaande admin-oplossing (Laravel Nova/Filament, een generieke CRUD-laag) voor de schermen waar maatwerk geen waarde toevoegt.

**Volledige verantwoordelijkheid voor security**

> *Beperking* - Autorisatie uitsluitend in de servicelaag, deny by default, permissietests per endpoint inclusief de negatieve gevallen, en een securityreview voordat persoonsgegevens of rechtenlogica live gaan. Gebruik de authenticatievoorzieningen van het framework in plaats van zelf te bouwen.

**Het JavaScript-ecosysteem veroudert snel**

> *Beperking* - Plan majorupgrades als terugkerend onderhoud in plaats van als incident, houd frameworkspecifieke code aan de rand van de applicatie, en overweeg server-gerenderde views waar een rijke client geen aantoonbare meerwaarde heeft.

**Sterk afhankelijk van de kwaliteit van het team**

> *Beperking* - Leg de laagindeling en de afhankelijkheidsrichting vooraf vast, toets daarop in code review, en zorg dat minstens één ervaren ontwikkelaar de architectuur bewaakt. Een framework zonder discipline levert de nadelen op zonder de voordelen.

**Meer eigen code betekent meer te onderhouden code**

> *Beperking* - Bouw geen eigen frameworks waar bestaande bibliotheken volstaan, houd de codebase klein, en verwijder wat niet meer gebruikt wordt in plaats van het te laten staan.

**Redacteuren verliezen zelfstandigheid**

> *Beperking* - Laat redactionele content in een CMS of een headless CMS staan (8.2), of bied bewust begrensde beheerschermen aan voor de zaken die veilig aanpasbaar zijn - teksten, typen, kleuren, e-mailsjablonen.

**Supply-chainrisico verdwijnt niet, het verandert van vorm**

> *Beperking* - Een lockfile in versiebeheer, geautomatiseerde kwetsbaarheidsscans op npm- en NuGet-/Composer-pakketten, terughoudendheid met kleine pakketten van één auteur, en een expliciete review bij het toevoegen van een afhankelijkheid.

> **RISICO OP PROCESNIVEAU**
>
> Het grootste risico is niet technisch maar organisatorisch. Een framework-applicatie die zonder tests, zonder CI/CD en zonder code review wordt gebouwd, levert alle nadelen van zelfbouw op en geen van de voordelen - en presteert dan aantoonbaar slechter dan een goed opgezet CMS-project. De voordelen in dit rapport zijn **voorwaardelijk**: ze bestaan alleen als de bijbehorende werkwijze er ook is.

## Hoofdstuk 6 - Waar een CMS juist de betere keuze is

Een CMS is geen verouderde technologie en de keuze ervoor is in veel situaties de professioneel juiste. Concreet:

- **Contentgedreven websites** - corporate sites, nieuwsportalen, informatieve intranetpagina's. Hier is het CMS niet alleen goedkoper maar ook inhoudelijk beter passend.
- **Redactionele zelfstandigheid als harde eis.** Als een communicatieafdeling zonder ontwikkelaar moet kunnen publiceren, pagina's herschikken en campagnes opzetten, levert een CMS dat vandaag en een framework pas na maanden bouwen.
- **Standaardfunctionaliteit die grotendeels dekt.** Een webshop met gangbare eisen is met WooCommerce sneller en goedkoper dan met een zelfgebouwde betaal- en voorraadmodule, en brengt minder risico mee omdat de betalingsverwerking via een gecertificeerde provider loopt in plaats van via eigen code. (Een SaaS-platform als Shopify is vaak nog geschikter, maar valt buiten de CMS-definitie in 1.4.)
- **Prototypes en proefopstellingen.** Snel iets werkends laten zien om een idee te toetsen - precies wat de eerste agendaversie van Tutti feitelijk was.
- **Kleine projecten met een korte levensduur.** Campagnesites, evenementpagina's, tijdelijke formulieren: hier weegt bouwtijd zwaarder dan architectuur, en is elk uur dat aan structuur opgaat verspild.
- **Een klein team zonder applicatie-ontwikkelaars.** Als de werkwijze uit hoofdstuk 5 niet realistisch is, is een CMS met strakke pluginhygiëne de veiliger keuze dan een half gebouwde framework-applicatie.
- **Waar het beheer van de content zelf het product is.** Versiebeheer van teksten, redactionele workflows, vertaalbeheer en mediabibliotheken zijn volwassen CMS-functionaliteit die je niet licht opnieuw bouwt.

Die voordelen zijn echt en verdwijnen niet zodra een project groter wordt. Wat verandert, is hun *gewicht*: naarmate het zwaartepunt verschuift van "wat staat er op de pagina" naar "wat mag deze gebruiker en wat gebeurt er daarna", gaan andere eigenschappen de doorslag geven - en dat zijn precies de eigenschappen waarin een framework sterker is.

> **PRAKTISCHE TOETS**
>
> Een bruikbare vuistregel om de keuze vroeg te maken: **schrijf de tien belangrijkste regels van het systeem op in gewone zinnen.** Gaan ze overwegend over wie wat mag publiceren en waar het komt te staan, kies dan een CMS. Gaan ze over voorwaarden, statusovergangen, rechten die van meerdere factoren afhangen en gevolgen in andere systemen, kies dan een framework. Zitten ze er allebei in, dan is de hybride uit 8.2 waarschijnlijk het antwoord.

## Hoofdstuk 7 - Vergelijkingstabel

Geen scores of sterren, maar per onderdeel een korte onderbouwing en een richting. "Genuanceerd" betekent dat het verschil in de praktijk beide kanten op werkt. De richting geldt binnen de context van dit rapport - grote, complexe, bedrijfskritische applicaties - en niet als algemene uitspraak over de producten.

| Onderdeel | Framework | CMS | Richting |
| --- | --- | --- | --- |
| **Schaalbaarheid** | Stateless te schalen; leespaden en zware taken apart te optimaliseren | Leunt op cache, die wegvalt bij gepersonaliseerde content | **Framework** |
| **Performance** | Renderstrategie per route, eigen queries en indexes, gerichte bundels | Uniforme rendering; plugin-assets laden ook waar ze niets doen | **Framework, mits benut** |
| **Onderhoudbaarheid** | Eén regel op één vindbare plek, met compiler en tests als vangnet | Regel verspreid over configuratie, query, template en script | **Framework** |
| **Codekwaliteit en architectuur** | Lagen, DI en afhankelijkheidsrichting expliciet en afdwingbaar | Instapplek en datamodel liggen deels vast (DNN sterker dan WordPress) | **Framework** |
| **Flexibiliteit** | Voor de ontwikkelaar: geen model om binnen te passen | Voor de beheerder: zelf velden en pagina's aanpassen zonder release | **Genuanceerd** |
| **Testbaarheid** | Unit-, integratie-, API- en permissietests met standaardgereedschap | Groot deel van het gedrag alleen end-to-end te controleren | **Framework** |
| **CI/CD en DevOps** | Alles in versiebeheer; wat in Git staat is wat er draait | Gedragsbepalende state in de database; configuratiedrift | **Framework** |
| **Security** | Klein aanvalsoppervlak, autorisatie centraal en testbaar; eigen fouten | Beproefde basis, maar 91% van de WP-kwetsbaarheden zit in plugins | **Framework, mits getest** |
| **Integraties en API's** | Eigen client, retries, queues en een eigen API-contract | Snel als de koppeling bestaat; moeizaam zodra hij net anders moet | **Framework** |
| **Database en datamodel** | Eigen schema met foreign keys, constraints, indexes en migraties | DNN: eigen tabellen zijn de normale werkwijze. WordPress: alleen met verlies van de platformvoordelen | **Framework** |
| **Plugin-afhankelijkheid** | Bibliotheken die je aanroept en kunt vervangen | Plugins draaien mee met volledige rechten in het functionele hart | **Framework** |
| **Upgrade-risico's** | Gedocumenteerde breaking changes; tests wijzen aan wat breekt | Kern, thema en tientallen plugins met eigen releaseritmes | **Framework** |
| **Vendor lock-in** | Domeinlogica los te houden; frontendframeworks binden juist sterk | Gedrag zit in plugins die nergens anders bestaan; DNN zit vast aan .NET Framework | **Genuanceerd** |
| **Technische schuld** | Zichtbaar in code en refactorbaar | Vaak vastgelegd in datamodel en plugins; alleen te vervangen | **Framework** |
| **Beschikbaarheid developers** | Overdraagbare kennis; React 46,9% en ASP.NET Core 21,3%, maar Laravel 9,3% en Nuxt 4,1% | WordPress breed (12,4%) maar vaak sitebouw; DNN specialistisch | **Framework** |
| **Lange-termijnondersteuning** | .NET en Laravel publiceren data; React, Vue en Nuxt nauwelijks | WordPress backport als coulance; DNN zonder formele LTS | **Backendframeworks voorop** |
| **Geschiktheid voor maatwerk** | Prijs per functie blijft vlakker na de investering in het fundament | Het standaarddeel is goedkoop, elke stap daarbuiten duurder | **Framework** |
| **Geschiktheid voor grote teams** | Branches, pull requests en tests dekken al het gedrag af | Code is samen te voegen, configuratie in de database niet | **Framework** |
| **Redactionele zelfstandigheid** | Alleen wat bewust als beheerscherm is gebouwd | Publiceren, herschikken en velden toevoegen zonder ontwikkelaar of release | **CMS** |
| **Tijd tot een eerste werkende versie** | Eerst fundament: schema, services, API, schermen | Database, rechten en redactie staan er op dag één | **CMS** |
| **Standaardbeheerfunctionaliteit** | Gebruikersbeheer, media en workflows zelf bouwen of inkopen | Meegeleverd, volwassen en door duizenden installaties uitgetest | **CMS** |
| **Kosten korte termijn** | Leeg project; beheer en redactie moeten gebouwd worden | Database, beheer, rechten en redactie op dag één aanwezig | **CMS** |
| **Kosten lange termijn** | Vlakker kostenverloop, mits de werkwijze uit hoofdstuk 5 er is | Oplopend door foutzoeken, pluginonderhoud en handmatige regressie | **Framework** |

Van de drieëntwintig onderdelen wijzen er vier naar het CMS en zijn er drie genuanceerd. De criteria in hoofdstuk 3 gaan overwegend over eigenschappen die bij complexe applicaties zwaar wegen; de vier CMS-onderdelen hierboven zijn er bewust aan toegevoegd omdat ze in hoofdstuk 6 wel worden behandeld maar anders niet in de tabel zouden staan. Bij een contentgedreven website zou zowel de selectie als de weging er wezenlijk anders uitzien.

## Hoofdstuk 8 - Conclusie en advies

### 8.1 De conclusie

Voor **grote, bedrijfskritische en sterk op maat gemaakte applicaties heeft een framework-gebaseerde architectuur de voorkeur**. Niet omdat CMS-platformen tekortschieten - ze doen waarvoor ze gebouwd zijn uitstekend - maar omdat zulke applicaties eigenschappen vragen die een framework structureel beter levert: alle gedrag in versiebeheer, een datamodel dat bij het probleem past, autorisatie op één toetsbare plek, tests die de regels vastleggen, een reproduceerbare uitrol, en een architectuur die parallel werken toelaat.

De onderliggende reden laat zich in één zin samenvatten: **een CMS is ontworpen om gedrag buiten de code aanpasbaar te maken, en dat is precies wat je bij complexe bedrijfslogica niet wilt.** Wat bij een contentsite flexibiliteit heet, heet bij een applicatie met rechten onvoorspelbaarheid.

Twee kanttekeningen horen in dezelfde conclusie thuis. Ten eerste is het voordeel *voorwaardelijk*: zonder tests, code review en een geautomatiseerde straat verdampt het grootste deel ervan (hoofdstuk 5). Ten tweede is het *niet universeel*: op korte-termijnkosten en op redactionele zelfstandigheid wint het CMS, en die criteria zijn in veel projecten doorslaggevend.

### 8.2 Niet alles of niets

> **AANBEVOLEN VARIANT**
>
> Zet de twee naast elkaar in plaats van de één door de ander te vervangen. Laat **redactionele content in het CMS** - nieuws, praktische informatie, teksten die de organisatie zelf wil beheren - en breng **het domein onder in een framework-architectuur** met een eigen datamodel, een servicelaag en een API. De redactie houdt haar vrijheid waar die onschadelijk is, en de logica krijgt de structuur die ze nodig heeft.
>
> Een praktische scheidslijn: **alles wat een rechtenvraag oproept of een statusovergang kent, hoort in de applicatie; alles wat alleen getoond wordt, hoort in het CMS.** Die grens moet expliciet zijn afgesproken en gedocumenteerd, met één koppelvlak ertussen - in de regel een API die de ene kant van de andere afschermt.

In de praktijk zijn er drie werkbare vormen van deze hybride:

1. **Headless CMS met een framework-frontend** - het CMS levert content via een API, Next.js of Nuxt rendert. Geschikt als content het grootste deel is maar de presentatie en de performance eigen sturing vragen.
2. **Applicatie naast het CMS op hetzelfde platform** - bijvoorbeeld een eigen module met eigen tabellen binnen DNN, die authenticatie en hosting van het platform overneemt maar zijn eigen domein beheert. De laagdrempeligste route voor een organisatie die al op DNN staat.
3. **Zelfstandige applicatie met SSO** - een volledig eigen ASP.NET Core- of Laravel-applicatie die dezelfde identiteitsprovider gebruikt als het intranet en er vanuit de navigatie naar gelinkt wordt. De schoonste scheiding en de duurste.

### 8.3 Randvoorwaarden

Het advies geldt alleen wanneer aan deze voorwaarden wordt voldaan:

1. De domeinlogica komt in een **framework-onafhankelijke laag** - een class library of een servicelaag zonder verwijzingen naar het webframework - zodat een latere migratie de kern niet raakt.
2. Autorisatie zit op **één plek**, werkt deny by default en heeft permissietests per endpoint, inclusief de negatieve gevallen.
3. Er is vanaf dag één **versiebeheer, code review, een migratieframework en een geautomatiseerde build- en deploystraat**.
4. Er is een **vastgelegde architectuurbeschrijving** waar in reviews op getoetst wordt.
5. De eerste oplevering is een **dunne verticale doorsnede**, zodat de aanpak zich vroeg bewijst in plaats van pas na het fundament.
6. De **grens tussen CMS-content en applicatiedomein** is expliciet vastgelegd, inclusief het koppelvlak.
7. Majorupgrades - vooral aan de JavaScript-kant - staan als **terugkerend onderhoud** in de planning en niet als incident.

### 8.4 Wanneer dit advies niet opgaat

Een advies dat nooit fout kan zijn, is geen advies. Kies een CMS, of heroverweeg de keuze, als:

- het systeem in de kern **content blijft** en de logica beperkt blijft tot publiceren en tonen;
- **redactionele zelfstandigheid een harde eis is** en er geen budget is om beheerschermen te bouwen;
- het team de werkwijze uit hoofdstuk 5 **niet kan waarmaken** - dan is een CMS met strakke pluginhygiëne veiliger dan een half afgemaakte applicatie;
- de benodigde functionaliteit **grotendeels standaard is** en een volwassen, goed onderhouden module of plugin die dekt;
- het een **project met een korte levensduur** is, waarbij de lange-termijnkosten uit hoofdstuk 4 simpelweg niet optreden;
- de organisatie **al zwaar in één platform heeft geïnvesteerd** en de migratiekosten het voordeel binnen de resterende levensduur niet goedmaken - dan is de hybride uit 8.2 vrijwel altijd het betere antwoord dan een volledige overstap.

### 8.5 Samengevat voor de besluitvorming

- **Contentgedreven site:** CMS
- **Applicatie met rechten en processen:** Framework
- **Beide in één systeem:** Hybride met expliciete grens
- **Zonder tests en CI/CD:** CMS - framework levert dan niets op

## Bijlagen - Verantwoording

### Bijlage A - Aannames en beperkingen

- Er zijn geen eigen metingen verricht. Alle kwantitatieve gegevens komen uit publiek beschikbare bronnen die in bijlage B zijn opgenomen, gecontroleerd op 23 september 2026.
- De prestatiecijfers in 3.2 zijn **veldmetingen over verschillende populaties websites** en geen gecontroleerd experiment. Ze onderbouwen dat WordPress-origins in de praktijk minder vaak een voldoende halen; ze onderbouwen géén uitspraak over de snelheid van een framework ten opzichte van een CMS bij gelijke omstandigheden.
- Voor Next.js, Nuxt en React publiceert geen van de geraadpleegde bronnen een Core Web Vitals-percentage; die cijfers zijn alleen uit het interactieve dashboard van het Core Web Vitals Technology Report te lezen en zijn daarom weggelaten. Het wél opgenomen Astro-cijfer komt uit dezelfde dataset als dat van WordPress en draagt dus dezelfde beperking - het is opgenomen omdat een gepubliceerde secundaire bron het noemt, niet omdat het steviger is.
- De kwetsbaarheidscijfers van Patchstack en Wordfence komen uit verschillende databases met verschillende inclusiecriteria en zijn niet één op één vergelijkbaar. Ze betreffen bovendien het WordPress-ecosysteem; vergelijkbare openbare cijfers voor DNN bestaan niet in deze vorm.
- De cijfers uit de Stack Overflow Developer Survey 2025 geven aan welk deel van de respondenten een technologie het afgelopen jaar heeft gebruikt. Het zijn geen arbeidsmarktcijfers en geen uitspraak over beschikbaarheid voor inhuur. De resultaten van de enquête van 2026 waren op 23 september 2026 nog niet gepubliceerd.
- Het Sucuri-cijfer over verouderde software dateert uit het jaarrapport over 2023; er is sindsdien geen nieuwe editie verschenen. Het aandeel WordPress onder gehackte sites volgt grotendeels uit marktaandeel en klantsamenstelling en is geen besmettingskans per site.
- Kosten zijn kwalitatief behandeld. Er zijn bewust geen tarieven, bedragen of terugverdientijden opgenomen, omdat daar geen betrouwbare algemeen geldende cijfers voor bestaan.
- Het rapport vergelijkt *categorieën*. Binnen beide categorieën zijn de verschillen groot: DNN is op datamodel en testbaarheid aanzienlijk sterker dan WordPress, en een backendframework biedt hardere ondersteuningsgaranties dan een frontendframework.
- De voorbeelden uit Tutti dienen ter illustratie van een herkenbaar groeipatroon en zijn geen beoordeling van de huidige implementatie van dat project.

### Bijlage B - Bronnen

Alle bronnen geraadpleegd op 23 september 2026.

- [.NET and .NET Core Support Policy - Microsoft](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core) - LTS van 3 jaar, STS van 24 maanden, einddatum .NET 10 op 14 november 2028.
- [.NET Framework lifecycle - Microsoft Learn](https://learn.microsoft.com/en-us/lifecycle/products/microsoft-net-framework) - .NET Framework 4.8 ondersteund als onderdeel van het besturingssysteem, zonder eigen einddatum.
- [Release Notes - Laravel](https://laravel.com/docs/releases) - ondersteuningstabel per versie: 18 maanden bugfixes, 2 jaar securityfixes; Laravel 13 op 17 maart 2026.
- [Support Policy - Next.js](https://nextjs.org/support-policy) - Active LTS en Maintenance LTS, tweejarig venster vanaf de eerste release van een major.
- [Roadmap - Nuxt](https://nuxt.com/docs/4.x/community/roadmap) - minimaal zes maanden ondersteuning na het verschijnen van de volgende major; Nuxt 3 tot 31 juli 2026.
- [Versioning Policy - React](https://react.dev/community/versioning-policy) - geen LTS; securityfixes worden naar getroffen majors gebackport.
- [Releases - Vue.js](https://vuejs.org/about/releases.html) - semver en releasecyclus, zonder ondersteuningsvenster per minor.
- [DNN Platform Releases - DNN Community](https://dnncommunity.org/Platform/Releases) - releasehistorie; 10.3.3 op 23 juli 2026.
- [Requirements - DNN Docs](https://docs.dnncommunity.org/content/getting-started/setup/requirements/) - .NET Framework 4.8 als minimum, IIS en SQL Server.
- [DNN Technology Future in 2025 and Beyond - DNN Community](https://dnncommunity.org/blogs/Post/20702/DNN-Technology-Future-in-2025-and-Beyond) - MVC-pijplijn als huidige richting, zonder toezegging of tijdlijn voor .NET Core.
- [The Technical Future of DNN - DNN Community (2020)](https://dnncommunity.org/blogs/Post/7625/The-Technical-Future-of-DNN) - de raming van circa 8.000 ontwikkeluren voor een .NET Core-transitie en de conclusie dat die niet op te brengen was.
- [Oqtane](https://www.oqtane.org/) - modern .NET-alternatief van de oorspronkelijke DotNetNuke-auteur; expliciet geen technische gelijkenis met DNN en geen migratiepad.
- [Security - WordPress.org](https://wordpress.org/about/security/) - officieel wordt alleen de laatste versie ondersteund; backports naar oudere branches als coulance.
- [Releases - WordPress.org](https://wordpress.org/download/releases/) - releasehistorie, waaronder de gecoördineerde securityrelease van 17 september 2026.
- [State of WordPress Security in 2026 - Patchstack](https://patchstack.com/whitepaper/state-of-wordpress-security-in-2026/) - 11.334 kwetsbaarheden over 2025, 91% in plugins, 46% ongepatcht bij bekendmaking.
- [2024 Annual WordPress Security Report - Wordfence](https://www.wordfence.com/blog/2025/04/2024-annual-wordpress-security-report-by-wordfence/) - 8.223 kwetsbaarheden over 2024, 96% in plugins, 5 in core.
- [OWASP Top 10:2025](https://top10.owasp.org/2025/) - actuele editie, met A03 Software Supply Chain Failures als nieuwe categorie.
- [2023 Hacked Website & Malware Threat Report - Sucuri](https://sucuri.net/reports/2023-hacked-website-report/) - 39,1% van de besmette sites draaide een verouderd CMS.
- [Usage statistics of content management systems - W3Techs](https://w3techs.com/technologies/overview/content_management) - WordPress 40,2% van alle websites en 58,8% van de CMS-markt; DotNetNuke 0,1%.
- [Developer Survey 2025 - Technology - Stack Overflow](https://survey.stackoverflow.co/2025/technology) - gebruikspercentages per framework onder professionele ontwikkelaars.
- [Web Almanac 2025 - CMS - HTTP Archive](https://almanac.httparchive.org/en/2025/cms) - Core Web Vitals per CMS, met de kanttekening dat implementatiekeuzes zwaarder wegen dan het platform.
- [Core Web Vitals Technology Report - HTTP Archive](https://httparchive.org/reports/cwv-tech) - interactief dashboard met Core Web Vitals per technologie; meetmoment april 2026 voor de cijfers in 3.2.
- [Web Almanac 2025 - Performance - HTTP Archive](https://almanac.httparchive.org/en/2025/performance) - 48% van de mobiele origins haalt een voldoende op de Core Web Vitals.

---

*Adviesrapport ter ondersteuning van een architectuurbeslissing. De feiten over versies, ondersteuningstermijnen, kwetsbaarheden en marktaandelen zijn gecontroleerd tegen de bronnen in bijlage B op 23 september 2026; waar een cijfer een vergelijking niet draagt, staat die beperking erbij. De weging en het advies zijn een oordeel binnen de context van grote, bedrijfskritische en sterk op maat gemaakte applicaties, en geen algemene uitspraak over de geschiktheid van CMS-platformen.*
