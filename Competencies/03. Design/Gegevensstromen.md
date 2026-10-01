# Gegevensstromen - Tutti

*Ontwerp · Tutti · Metropole Orkest*

Dit document beschrijft welke gegevens waar vandaan komen, waar ze naartoe gaan en wie ze mag zien. Eerst het overzicht, daarna de vier stromen apart, elk stap voor stap.

| Gegeven | Waarde |
| --- | --- |
| Documenttype | Ontwerp (gegevensstromen) |
| Competentie | Design |
| Deelvraag | 2 - architectuur- en integratieaanpak; 3 - data- en autorisatiemodel |
| Auteur | Ardit Fazliji |
| Versie van Tutti | 00.03.00 |
| Datum | 1 oktober 2026 |
| Gerelateerd | [Architectuurontwerp](<Architectuurontwerp.md>) · [Databaseontwerp](<Databaseontwerp.md>) · [Backendontwerp](<Backendontwerp.md>) · [Technische SRS - Tutti](<../02. Analysis/Technische_SRS_Tutti.md>), hoofdstuk 2 en 8 |

## Inhoud

1. [Overzicht](#1-overzicht)
2. [De OPAS-planning komt binnen](#2-de-opas-planning-komt-binnen)
3. [Een planner wijzigt de planning](#3-een-planner-wijzigt-de-planning)
4. [Iemand bekijkt de planning](#4-iemand-bekijkt-de-planning)
5. [Documenten en bladmuziek](#5-documenten-en-bladmuziek)

## 1. Overzicht

Getrokken lijnen: gegevens die worden geschreven of verstuurd. Gestippelde lijnen: gegevens die alleen worden gelezen.

```mermaid
flowchart LR
    subgraph bronnen["Bronnen buiten Tutti"]
        OPAS["OPAS"]
        SP[("SharePoint")]
        DNNU[("DNN-gebruikers<br/>en rollen")]
    end

    subgraph opslag["Database van de site"]
        RPHO[("Rpho_Opas_*<br/>OPAS-planning")]
        SETTINGS[("PortalSettings<br/>laatste import")]
        TUTTI[("Tutti_*<br/>eigen planning")]
        AUDITT[("AuditLog")]
        CHANGET[("ChangeLog")]
    end

    subgraph tutti["Tutti en de modules ernaast"]
        IMPORT["OPAS-import<br/>elk uur"]
        LEES["Services: lezen<br/>rechten, kleuren,<br/>OPAS en Tutti samen"]
        SCHRIJF["Services: schrijven<br/>valideren, conflicten"]
        DOCS["DocumentService<br/>controleert rechten"]
        VIEWER["SharePoint Viewer<br/>instrumentrollen"]
    end

    subgraph mensen["Gebruikers"]
        PLANNER["Planner"]
        MUSICUS["Musicus"]
    end

    OPAS -- "XML-export" --> IMPORT
    IMPORT -- "afspraken, wijzigingen" --> RPHO
    IMPORT -- "tijdstip import" --> SETTINGS

    PLANNER -- "productie, afspraak, bezetting<br/>(JSON via de API)" --> SCHRIJF
    SCHRIJF -- "rij + RowVersion" --> TUTTI
    SCHRIJF -- "wie, wat, oud, nieuw" --> AUDITT
    SCHRIJF -- "zichtbare wijziging" --> CHANGET

    RPHO -.-> LEES
    SETTINGS -. "48 uur geen import: waarschuwing" .-> LEES
    TUTTI -.-> LEES
    CHANGET -.-> LEES
    DNNU -. "wie is de bezoeker" .-> LEES
    DNNU -. "wie is de bezoeker" .-> SCHRIJF
    LEES -- "agenda, afspraak, projectkamer,<br/>met label Tutti of OPAS" --> MUSICUS
    LEES --> PLANNER

    MUSICUS -- "documenten opvragen" --> DOCS
    DOCS -- "zoek op productienummer" --> VIEWER
    SP -.-> VIEWER
    VIEWER -. "alleen wat de rol toelaat" .-> DOCS
    DOCS -- "lijst; een bestand pas bij openen" --> MUSICUS
    DOCS -- "download" --> AUDITT
```

## 2. De OPAS-planning komt binnen

OPAS is het planningssysteem van het orkest en blijft leidend. Tutti kopieert niets: het leest bij elke pagina de tabellen die de OPAS-import vult.

```mermaid
sequenceDiagram
    autonumber
    participant O as OPAS
    participant I as OPAS-import (DNN-taak)
    participant DB as Database
    participant T as Tutti (OpasRepository)

    loop elk uur
        O->>I: XML-export met de vrijgegeven planning
        I->>DB: afspraken bijwerken in Rpho_Opas_EventItem
        I->>DB: wat veranderde in Rpho_Opas_Wijziging
        I->>DB: tijdstip in PortalSettings (OpasLastXmlImportDate)
    end
    Note over T: bij elke pagina met OPAS-gegevens
    T->>DB: SELECT op de OPAS-tabellen
    DB-->>T: rijen
    T->>T: productienummer uit de projectnaam, soort uit de tekst, geannuleerd herkennen
    T->>DB: SELECT tijdstip laatste import
    T->>T: langer dan OpasAlertHours (48 uur) geleden? Dan een waarschuwing op de pagina
```

- Alleen wat in OPAS is **vrijgegeven**, komt in de export en dus in Tutti.
- Wie bij welke OPAS-afspraak speelt, slaat de import niet op. Daarom hebben OPAS-afspraken in Tutti geen bezetting.
- Zijn de OPAS-tabellen niet te lezen, dan toont Tutti de eigen planning met een melding welk deel ontbreekt.
- De waarschuwing na 48 uur zonder import volgt eis `AVL-008` uit de Technische SRS.

## 3. Een planner wijzigt de planning

Alles wat in Tutti wordt ingevoerd, gaat via de API. Een wijziging, de audittrail en de wijzigingshistorie worden in één transactie opgeslagen: alle drie of geen.

```mermaid
sequenceDiagram
    autonumber
    actor P as Planner
    participant B as Browser (tutti.mvc.js)
    participant A as API (EventsController)
    participant S as EventService
    participant V as VisibilityService
    participant DB as Database

    P->>B: wijzigt de begintijd en slaat op
    B->>A: PUT /API/Tutti/v1/events/10 (JSON, RowVersion, antiforgery-token)
    A->>A: ingelogd? taal, trace-id
    A->>S: Update(caller, 10, gegevens)
    S->>V: mag deze caller deze productie beheren?
    V-->>S: ja
    S->>S: valideren, bepalen wat er verandert, conflicten zoeken
    S->>DB: BEGIN
    S->>DB: UPDATE Event ... WHERE RowVersion = de gelezen versie
    alt iemand anders sloeg intussen op
        DB-->>S: 0 rijen
        S->>DB: ROLLBACK
        A-->>B: 409, met aanbod om opnieuw te laden
    else versie klopt
        S->>DB: INSERT AuditLog (wie, oude en nieuwe waarde, trace-id)
        S->>DB: INSERT ChangeLog (alleen als musici de afspraak konden zien)
        S->>DB: COMMIT
        A-->>B: 200, met de nieuwe RowVersion
        B->>B: pagina herladen, "Opgeslagen" tonen
    end
```

- Tijden worden in de browser omgezet van Amsterdamse tijd naar UTC; de database rekent in UTC.
- Een conflict (dezelfde persoon of zaal tegelijk elders) is een waarschuwing, geen weigering.
- Wijzigingen aan een concept komen wel in de audittrail, niet in de wijzigingshistorie: niemand buiten de planning kende de afspraak nog.

## 4. Iemand bekijkt de planning

Een pagina is een gewone link. De server haalt de gegevens op met de rechten van de bezoeker en stuurt een complete pagina terug.

```mermaid
sequenceDiagram
    autonumber
    actor M as Musicus
    participant D as DNN
    participant C as AgendaController
    participant S as EventService / OpasService
    participant DB as Database
    participant V as Razor-view

    M->>D: GET /tutti/events?view=week&date=20261001
    D->>C: ingelogde gebruiker
    C->>C: caller maken: id, Tutti-rollen, persoon
    C->>S: agenda van deze week voor deze caller
    S->>DB: Tutti-afspraken, met de rechten van de caller in de WHERE
    S->>DB: OPAS-afspraken (als de caller Opas.Read heeft)
    DB-->>S: rijen
    S->>S: alleen gepubliceerd, kleuren volgens de groeperingsregel
    S-->>C: afspraken van beide bronnen
    C->>V: view model: dagen, kolommen, teksten, Amsterdamse tijd
    V-->>M: HTML, elke afspraak met label Tutti of OPAS
```

- Wat de bezoeker niet mag zien, wordt niet opgehaald: de rechten staan in de query zelf.
- Weergave, datum en filters staan in het webadres, zodat herladen en delen dezelfde weergave geven.

## 5. Documenten en bladmuziek

De bestanden blijven in SharePoint. Tutti vraagt de lijst pas op als de pagina er al is, en een bestand gaat pas over de lijn als iemand het opent. Er zijn twee sloten: Tutti controleert de rechten op de productie, de SharePoint Viewer de instrumentrollen.

```mermaid
sequenceDiagram
    autonumber
    actor M as Musicus
    participant B as Browser
    participant A as API (DocumentsController)
    participant S as DocumentService
    participant SV as SharePoint Viewer
    participant SP as SharePoint
    participant DB as Database

    M->>B: opent de afspraak of het tabblad Documenten
    B->>A: GET .../productions/351/documents
    A->>S: lijst voor deze caller
    S->>S: Document.Read? productie zichtbaar?
    S->>SV: Search op productienummer, met de cookie van de musicus
    SV->>SP: zoeken
    SV-->>S: alleen de bestanden die de instrumentrol toelaat
    S-->>B: namen, soorten, groottes, document-id's (geen adressen)
    B-->>M: "2 bestanden beschikbaar" of de lijst per soort

    M->>B: kiest Openen
    B->>A: GET .../documents/content?document=ID
    A->>S: dit bestand voor deze caller
    S->>SV: opnieuw zoeken: staat ID in de lijst van deze musicus?
    alt nee
        S-->>B: 404, "bestaat niet of is niet zichtbaar"
    else ja
        S->>DB: download in de audittrail (één keer per 10 minuten)
        S->>SV: ProxyDownload
        SV->>SP: bestand ophalen
        SV-->>B: bestand, in delen doorgestuurd
    end
```

- Er gaan geen wachtwoorden, tokens of SharePoint-adressen naar de browser.
- Een document-id uit een andere bron opent niets: het moet voorkomen in wat de viewer op dat moment voor deze bezoeker laat zien.
- PDF, afbeeldingen, audio, video en tekst openen in een nieuw tabblad; al het andere wordt een download.
- Het vastleggen van downloads volgt eis `SEC-015` uit de Technische SRS.
