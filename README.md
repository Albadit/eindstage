# Design and Realisation of a Reusable Orchestra Production Application

**Ardit Fazliji** - CMI Informatica, Hogeschool Rotterdam - 1023891@hr.nl
Graduation Project INFAFS04 / INFAFS26 - Bond for Web Solutions, Rotterdam

| Role | Name |
| --- | --- |
| Company supervisor | Martijn Verbeek - martijnverbeek@bondforwebsolutions.nl |
| Technical supervisor | Marnix Bouwman - marnixbouwman@bondforwebsolutions.nl |
| School supervisor | [name] - [email] |

<!--
  Template guidance (remove when done):
  - Keep the headings of each section and replace the body with your own text.
  - This is not a full report: be precise and self-explanatory, and provide evidence for your project.
  - Expected length: minimum 5, maximum 10 pages.
-->

---

**Abstract** - [In one paragraph: what the main problem was and what has been done.]

**Keywords** - orchestra production planning, DNN, 2sxc, role-based access control, multi-tenant configuration, [...]

---

## 1. Introduction

<!-- Start with the context of the project. -->

Bond for Web Solutions is a Rotterdam software company that builds digital platforms and custom web applications for organisations in, among others, the cultural and public sector. Bond manages digital environments for several orchestra clients. Within the production domain, planning, appointments, changes, sheet music, audio and documents are spread across different systems and storage sources, and each orchestra works slightly differently (filters, colour coding, casting information, document storage).

[Expand the context here.]

### 1.1 Problem Statement

Bond does not yet have a single reusable and technically substantiated solution to centrally manage and expose production planning and related information for different orchestra clients. Multiple data sources, client-specific requirements and sensitive files raise questions about architecture, integration, authorisation, maintainability and scalability.

### 1.2 Research Questions

**Main question:** How can a secure, maintainable and reusable web application be designed and realised that centrally manages and exposes orchestra productions, planning and related information, while meeting the differing technical and functional needs of orchestra clients?

**Sub-questions:**

1. Which functional and non-functional requirements are necessary, and which parts must be configurable per orchestra?
2. Which architecture and integration approach is most suitable for production data, changes and documents from the existing data sources?
3. How should the data and authorisation model be set up to manage roles and access to sensitive information securely?
4. How should the user interface be designed so that planning and production information is clear, responsive and accessible?
5. To what extent does the realised solution meet the requirements and quality criteria after technical testing and user validation?

<!-- Replace with your finalised research questions if they changed during the project. -->

## 2. Background

<!-- Write a short background here, if it is necessary. Otherwise remove this section. -->

[E.g. the existing DNN / 2sxc environments, SharePoint / 2sxc ADAM document storage, and the orchestra software currently in use.]

## 3. Results

<!-- Explain the work you have done and the results you have achieved as your final product.
     Clarify how each research question is addressed and satisfied in your work. -->

### 3.1 [Sub-question 1 - Requirements]

[Result + reference to the exhibit, e.g. [Tutti-dossier](<Competenties/02. Analysis/Tutti-dossier.docx>).]

### 3.2 [Sub-question 2 - Architecture and integration]

[...]

### 3.3 [Sub-question 3 - Data and authorisation model]

[...]

### 3.4 [Sub-question 4 - User interface]

[...]

### 3.5 [Sub-question 5 - Evaluation]

[...]

<!--
  Formulas: number them if needed, e.g.  a + b = 0   (1)
  Citing references: use numbered brackets, e.g. "An example of using a reference [1]."
  Figures and tables: place them after they are cited; figure captions go below the figure,
  table titles above the table. Refer to them as "Fig. 1" / "Table 1", e.g.

  ![Fig. 1. Example of a figure caption.](<Competenties/04. Design/architecture.png>)
-->

## 4. Competencies

<!-- Clarify how your project and your activities address each competency. This section is very crucial.
     Link each claim to the exhibit(s) in the Competenties folder that prove it. -->

### 4.1 Professional Skills, Manage and Control

[Explain here …]

Evidence:
- [Logbook](<Competenties/01. Professional Skills & Manage and Control/logboek.xlsx>)

### 4.2 Analysis

[Explain here …]

Evidence:
- [Agenda Viewer](<Competenties/02. Analysis/Agenda Viewer.docx>)
- [Tutti-dossier](<Competenties/02. Analysis/Tutti-dossier.docx>)

### 4.3 Design

[Explain here …]

Evidence:
- [...](<Competenties/03. Design/>)

### 4.4 Realisation

[Explain here …]

Evidence:
- [Source code](Broncode/)
- [...](<Competenties/04. Realisation/>)

### 4.5 Advice

[Explain here …]

Evidence:
- [Own DNN module or 2sxc](<Competenties/05. Advice/Eigen_DNN-module_of_2sxc.docx>)
- [CMS frameworks](<Competenties/05. Advice/Frameworks_of_CMS.docx>)

## 5. Use of AI

<!-- Required for admissibility (Course Manual §4.2): describe whether and how AI was used
     within the project, and how this aligns with the company's policy. -->

[Which AI tools were used, for which tasks, how the output was verified, and how this aligns with Bond for Web Solutions' AI policy.]

## 6. Conclusion and Reflection

[Write your conclusion here.]

## 7. Deliverables

<!-- Explain the contents of your Graduation Folder: what each folder contains.
     Use proper naming for the folder and its contents. Allowed formats: images, recorded videos,
     plain text, PDF, project source code. Consult your supervisor on what needs to be delivered. -->

```
eindstage/
├── README.md                                   This file
├── Administratie/                              Graduation proposal, graduation agreement and
│                                               the company supervisor's evaluation form
├── Competenties/                               Exhibits (professional products) per competency
│   ├── 01. Professional Skills & Manage and Control/
│   ├── 02. Analysis/
│   ├── 03. Design/
│   ├── 04. Realisation/
│   └── 05. Advice/
├── Broncode/                                   Source code of the realised application
└── Presentaties/                               Halfway presentation and Graduation Session slides
```

| Folder | Contents |
| --- | --- |
| [Administratie](Administratie/) | Graduation proposal ([docx](Administratie/Afstudeervoorstel_1023891.docx), [pdf](Administratie/Afstudeervoorstel_1023891.pdf)), graduation agreement, company supervisor evaluation |
| [01. Professional Skills & Manage and Control](<Competenties/01. Professional Skills & Manage and Control/>) | Logbook, [planning, meeting notes, feedback, ...] |
| [02. Analysis](<Competenties/02. Analysis/>) | Agenda Viewer analysis, Tutti-dossier, [requirements / SRS, ...] |
| [03. Design](<Competenties/03. Design/>) | [Architecture, data and authorisation model, wireframes, test strategy, ...] |
| [04. Realisation](<Competenties/04. Realisation/>) | [Test reports, deployment documentation, demo video, ...] |
| [05. Advice](<Competenties/05. Advice/>) | Advice on own DNN module vs. 2sxc, comparison of CMS frameworks, [...] |
| [Broncode](Broncode/) | [Source code of the application] |
| [Presentaties](Presentaties/) | [Halfway presentation, final presentation] |

## References

<!-- Citations are numbered consecutively within brackets, e.g. [1]. -->

1. [Author(s), "Title," *Source*, vol. X, pp. X–X, Month Year.]
