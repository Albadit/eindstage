# Vragenlijst eerste gesprek met het MO

*Interviewvoorbereiding · Tutti · Metropole Orkest*

Deze vragenlijst bereidt mijn eerste gesprek met het Metropole Orkest (MO) over Tutti voor. Het gesprek moet duidelijk maken hoe de gebruikers nu plannen en werken, wat zij van Tutti nodig hebben en welke open beslissingen het MO zelf moet nemen.

| Gegeven | Waarde |
| --- | --- |
| Documenttype | Interviewvoorbereiding (vragenlijst) |
| Competentie | Analysis |
| Deelvraag | 1 - requirements en configureerbare onderdelen; 4 - gebruikersinterface; 5 - validatie |
| Auteur | Ardit Fazliji |
| Versie | 0.1 (concept) |
| Datum | 9 oktober 2026 |
| Gesprek met | [AANVULLEN: naam en rol van de gesprekspartner(s) bij het MO] |
| Datum gesprek | [AANVULLEN: datum en plaats van het gesprek] |
| Status | Voorbereid, gesprek nog niet gehouden |
| Gerelateerd | [Technische SRS - Tutti](<Technische_SRS_Tutti.md>) · [Tutti & OPAS: het complete dossier](<Tutti_OPAS_dossier.md>) · [Testplan - Tutti](<../04. Realisation/Testplan_Tutti.md>) |

## Inhoud

1. [Doel van het gesprek](#1-doel-van-het-gesprek)
2. [Aanpak](#2-aanpak)
3. [Vragen](#3-vragen)
4. [Na het gesprek](#4-na-het-gesprek)

---

## 1. Doel van het gesprek

Tot nu toe heb ik Tutti geanalyseerd en gebouwd op basis van documenten: het materiaal in Basecamp, de Agenda Viewer en de Technische SRS. Wat de gebruikers bij het MO zelf vinden, heb ik nog niet rechtstreeks gehoord. Dit gesprek is daarom de eerste keer dat ik de requirements bij de gebruikers zelf controleer.

Het gesprek heeft drie doelen:

1. **De huidige werkwijze begrijpen.** Hoe plant het MO nu met OPAS, Excel en SharePoint, en waar gaat het mis?
2. **Requirements en prioriteiten ophalen.** Wat moet Tutti in elk geval kunnen, wat kan later en wat hoeft niet? Dit helpt bij de open beslissing over de scope van versie 1 (`TD-006`).
3. **Open beslissingen en validatie voorbereiden.** Een deel van de open beslissingen in de Technische SRS, hoofdstuk 21, ligt bij het MO (`TD-002`, `TD-005`, `TD-006`, `TD-009`). Ook wil ik afspreken hoe en met wie het acceptatiescenario uit het testplan wordt doorlopen.

## 2. Aanpak

Ik houd een semigestructureerd interview: de vragen hieronder zijn de leidraad, maar ik vraag door op wat de gesprekspartner belangrijk vindt. Het gesprek duurt ongeveer een uur. Omdat dat te kort is voor alle vragen, staan de **kernvragen** (★) voorop. De overige vragen stel ik als er tijd over is, of ik stuur ze later per mail of via Basecamp.

| Onderdeel | Tijd (indicatief) |
| --- | --- |
| Kennismaking en doel van het gesprek | 5 minuten |
| Huidige werkwijze en knelpunten (3.1, 3.2) | 15 minuten |
| Korte demo van Tutti en feedback (3.3) | 15 minuten |
| Wensen, scope en open beslissingen (3.4 t/m 3.8) | 20 minuten |
| Validatie en vervolg (3.9, 3.10) | 5 minuten |

Bij het begin vraag ik toestemming om aantekeningen te maken (en eventueel het gesprek op te nemen). Ik leg uit dat de antwoorden gebruikt worden voor Tutti en voor mijn afstudeerproject.

> **AANNAME**
>
> Ik neem aan dat de gesprekspartner zicht heeft op de planning en op het gebruik door musici. Is dat niet zo, dan vraag ik bij 3.10 wie de overige vragen kan beantwoorden.

---

## 3. Vragen

De kolom **Waarom** laat zien welke open beslissing, welk knelpunt of welk document de vraag voedt.

### 3.1 Kennismaking en rol

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V01 ★ | Wat is uw rol binnen het MO en hoe hebt u te maken met de planning? | Bepalen vanuit welk perspectief de antwoorden komen (planner, productieleider, musicus, MT). |
| V02 | Hoe ziet een gewone week er voor u uit als het om planning en afspraken gaat? | Concreet beeld van het dagelijks werk in plaats van algemene wensen. |
| V03 | Wie zijn volgens u de belangrijkste gebruikers van Tutti, en wie gebruikt het het vaakst? | Prioriteit tussen de doelgroepen (planner, musicus, remplaçant, productieleider). |

### 3.2 Huidige werkwijze en knelpunten

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V04 ★ | Hoe verloopt het nu van een nieuwe productie tot de afspraken in de agenda van de musici staan? Welke stappen en welke systemen (OPAS, Excel, SharePoint, mail) zitten daartussen? | Huidige situatie in kaart brengen; dossier §02 "de waarheid is versnipperd". |
| V05 ★ | Waar gaat het nu het vaakst mis of kost het het meeste tijd? Kunt u een recent voorbeeld geven? | Echte knelpunten in plaats van aangenomen knelpunten. |
| V06 | Welke informatie houdt u nu bij in Excel of op papier, omdat OPAS het niet goed ondersteunt? | Excel mag geen parallel systeem blijven; welke functionaliteit moet Tutti overnemen. |
| V07 | Hoe hoort een musicus nu dat een afspraak is gewijzigd of geannuleerd? | Knelpunt "geannuleerde afspraken niet zichtbaar" (dossier §05); eisen aan de wijzigingshistorie. |
| V08 | Welke functies van OPAS gebruikt u echt, en welke nooit? Klopt de gebruiksscan van mei 2026 nog? | Gebruiksscan controleren; alleen bouwen wat gebruikt wordt. |
| V09 | Zijn er dingen die OPAS goed doet en die Tutti zeker niet mag verliezen? | Voorkomen dat Tutti een stap terug is. |

### 3.3 Feedback op de huidige versie van Tutti

Hier laat ik kort de huidige versie van Tutti zien: de agenda met afspraken uit OPAS en Tutti, de projectkamer van een productie, het scherm Instellingen en de export naar Outlook.

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V10 ★ | Wat valt u als eerste op? Wat is duidelijk en wat is verwarrend? | Eerste indruk en gebruiksvriendelijkheid (deelvraag 4). |
| V11 ★ | Kunt u de afspraken van volgende week vinden, en daarna alle afspraken van één productie? | Kleine taak laten uitvoeren in plaats van alleen meningen vragen. |
| V12 | Welke weergave (maand, week, dag, lijst) zou u het meest gebruiken, en op welk apparaat: telefoon, tablet of laptop? | Prioriteit van de weergaven en eisen aan de responsive weergave. |
| V13 | Is de kleurcodering per afspraaktype duidelijk? Welke afspraaktypes zijn voor u het belangrijkst? | Openstaande vraag over afspraaktypes en hoofdcategorieën (K07). |
| V14 | Werkt een .ics-bestand importeren in Outlook voor u, of verwacht u dat afspraken vanzelf in de agenda verschijnen? | Validatie van de gebouwde export; keuze tussen bestand, feed of Microsoft Graph (`TD-004`). |
| V15 | Wat mist u nu het meest in wat u hebt gezien? | Ontbrekende functionaliteit voor de backlog. |

### 3.4 Gebruikers, rollen en rechten

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V16 ★ | Wie mag wat zien? Welke informatie mag een musicus, een remplaçant of een productieleider juist níet zien? | Autorisatiemodel en permission matrix (deelvraag 3). |
| V17 | Hoe lang moet een remplaçant toegang hebben: alleen tijdens de productie, of ook daarna? | Open knelpunt remplaçanten (dossier §05). |
| V18 | Wie mag producties en afspraken aanmaken of wijzigen? Moet een wijziging eerst worden goedgekeurd? | Rollen planner en productieleider; nodig voor het scherm Beheer. |
| V19 | Moeten concepten van producties al zichtbaar zijn voor musici, of pas na vrijgeven? | Werkwijze "eerst vrijgeven in OPAS" (dossier §05) vertalen naar Tutti. |

### 3.5 Documenten en bladmuziek

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V20 ★ | Welke documenten horen bij een productie (bladmuziek, reisdocumenten, programma) en waar staan die nu? | Scope van de documentfunctionaliteit; SharePoint-koppeling. |
| V21 | Moet een musicus alleen de partij van het eigen instrument zien, of alle bladmuziek? | Ontwerpintentie "bladmuziek filteren op instrument" bevestigen. |
| V22 | Wie zet de documenten klaar, en moeten sommige documenten tijdelijk verborgen blijven? | Rechten op documenten (`TD-003`) en zichtbaarheid. |
| V23 | Moet Tutti documenten alleen tonen, of moeten gebruikers ook kunnen uploaden of wijzigen? | Waar de documentmetadata leeft (`TD-008`). |

### 3.6 Koppelingen met andere systemen

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V24 | Welke gegevens uit de planning zijn nodig voor uren en verloning in AFAS, en hoe gaan die er nu in? | Open beslissing over de AFAS-koppeling (`TD-002`). |
| V25 | Logt iedereen, ook remplaçanten, in met een Microsoft 365-account van het MO? | Inloggen via SSO; export voor gebruikers zonder M365-account. |
| V26 | Zijn er nog andere systemen waar planningsinformatie in terechtkomt, zoals Dropbox of een eigen mailing? | Onbekende koppelingen vroeg ontdekken. |

### 3.7 Scope en prioriteiten

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V27 ★ | Als Tutti over drie maanden maar drie dingen goed kan, welke drie moeten dat zijn? | Scope van versie 1 (`TD-006`); prioriteren. |
| V28 | Wat hoeft voorlopig níet in Tutti, bijvoorbeeld contractbeheer of tourkoffers? | Open punten in dossier §13; afbakening. |
| V29 | Moet Tutti OPAS op termijn helemaal vervangen, of blijft OPAS voorlopig de bron van de planning? | Bepaalt of Tutti alleen leest of ook zelf plant. |
| V30 | Wanneer zou u Tutti in de praktijk willen gebruiken, en voor welke productie of welk seizoen? | Realistische planning en een eerste pilot. |

### 3.8 Gegevens, privacy en oude data

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V31 | Moeten oude seizoenen uit OPAS in Tutti komen, of is het genoeg dat ze in OPAS te raadplegen blijven? | Open beslissing over migratie (`TD-005`). |
| V32 | Hoe lang moeten persoonsgegevens en de audittrail bewaard blijven? Wie bij het MO beslist daarover? | Bewaartermijn (`TD-009`); AVG. |
| V33 | Zijn er gegevens die extra gevoelig zijn, zoals adresgegevens of contractinformatie van musici? | Securityeisen en wat niet in Tutti thuishoort. |

### 3.9 Validatie en testen

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V34 ★ | Wie van het MO kan Tutti de komende weken testen, en hoeveel tijd hebben zij daarvoor? | Acceptatiescenario uit het testplan plannen. |
| V35 | Is er een omgeving waar het MO Tutti kan uitproberen zonder de echte planning te raken? | Acceptatieomgeving is nog `[AANVULLEN]` in het testplan. |
| V36 | Hoe geeft u het liefst feedback: in Basecamp, per mail of in een korte sessie? | Feedbacklus voor de iteraties afspreken. |
| V37 | Wanneer vindt u Tutti "goed genoeg" om te gebruiken? | Acceptatiecriteria vanuit de gebruiker. |

### 3.10 Afsluiting

| Nr | Vraag | Waarom |
| --- | --- | --- |
| V38 ★ | Met wie moet ik nog meer spreken, bijvoorbeeld een musicus of een productieleider? | Andere stakeholders betrekken (Analysis: requirements met verschillende stakeholders). |
| V39 | Mag ik u na het gesprek nog vragen stellen, en wanneer spreken we elkaar weer? | Vervolgafspraak. |
| V40 | Is er iets dat ik niet heb gevraagd en dat u wel belangrijk vindt? | Ruimte voor onderwerpen die ik heb gemist. |

---

## 4. Na het gesprek

Direct na het gesprek werk ik de aantekeningen uit tot een kort verslag in deze map. Daarna verwerk ik de antwoorden als volgt:

| Uitkomst | Waar het terechtkomt |
| --- | --- |
| Nieuwe of gewijzigde wensen | Backlog in Basecamp en, bij een technische eis, de [Technische SRS - Tutti](<Technische_SRS_Tutti.md>) |
| Antwoorden op open beslissingen (`TD-002`, `TD-005`, `TD-006`, `TD-009`) | Technische SRS, hoofdstuk 21, met wie besliste en wanneer |
| Feedback op de demo | Nieuwe taken op het kanbanbord in Basecamp |
| Afspraken over testen | [Testplan - Tutti](<../04. Realisation/Testplan_Tutti.md>), onderdeel acceptatie |

> **OPEN PUNT**
>
> Verslag van het gesprek: [AANVULLEN: na het gesprek uitwerken]
