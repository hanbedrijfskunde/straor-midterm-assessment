# PRD: PROMEF Assessment Tool

## Context

Als sr. consultant/docent in het F-cluster (simulatie adviesbureau "The Giraffe Company") moet je op twee assessmentdagen (7 en 10 april) in totaal 10 duo-gesprekken voeren van 20+10 minuten. Tijdens die gesprekken moet je per student 6 criteria scoren, observaties vastleggen, en daarna gerichte feedback geven. Het probleem: je moet maximaal present zijn in het gesprek terwijl je tegelijk data vastlegt. Een papieren formulier is traag en moet achteraf overgetypt worden.

**Doel**: Een lokale browser-app (single HTML file) die de volledige assessmentcyclus ondersteunt: planning, dataverzameling, verwerking en terugkoppeling.

---

## Gegevens uit documenten

### Deelnemers (uit deelnemers.csv)
- **BKN-F01** (di 7 apr 09:00-14:30): 4 teams, assessoren Witek + Arthur
  - Team 1: Maud, Lynn
  - Team 2: Maria, Lennard
  - Team 3: Amar, Aldin
  - Team 4: Ivan, Baran
- **BKN-F02** (vr 10 apr 09:00-14:30): 6 teams, assessoren Witek + Marlous
  - Team 1: Chris, Tim
  - Team 2: Siemen, Tijm
  - Team 3: Donny, Nick
  - Team 4: Jesse, Tim
  - Team 5: Max, Maurice
  - Team 6: Julius, Vincent

### 6 Beoordelingscriteria (elk 1-4 punten)
1. **Veranderen en evalueren** - demonstreert uitleg theoretisch concept uit veranderen/evalueren fase
2. **Sociaal communicatieve vaardigheden** - gespreksvaardigheden passend bij startende beroepsprofessional
3. **Schakelen en verbinden** - demonstreert omzetten weerstand in veranderbereidheid
4. **Professionaliseren (bijdrage)** - professionele bijdrage aan resultaat + groei als professional
5. **Professionaliseren (intercultureel)** - bespreekt intercultureel issue en aanpak
6. **Handelen vanuit waarden** - analyseert en lost morele kwestie op (OOA gedragsregels)

### Scoreniveaus
- Onder niveau (1), Op niveau (2), Boven niveau (3), Excellent (4)
- Alle criteria moeten minimaal "Op niveau" (2) zijn om te slagen
- Scoresleutel: 24=10, 20=9, 16=8, 14-15=7, 12-13=6, 10-11=5, 05-09=4

---

## Functionele eisen

### 1. Planning & Voorbereiding

#### Kalenderoverzicht (hoofdweergave dashboard)
- Dagweergave per assessmentdag (di 7 apr, vr 10 apr) naast elkaar als kolommen
- Verticale as: tijdslots van 30 minuten (09:00, 09:30, 10:00, ... t/m 14:30), gebaseerd op gespreksduur 20 min + 10 min feedback = 30 min per team
- Elk team wordt als kaart/blok weergegeven in een tijdslot met: teamnummer, namen studenten, beoordelingsstatus (kleurindicatie)
- **Drag & drop**: teams kunnen versleept worden naar een ander tijdslot binnen dezelfde dag om de volgorde aan te passen. Volgorde wordt opgeslagen in localStorage.
- Klik op een teamblok om het assessment voor dat team te openen
- Lege tijdslots tonen als beschikbaar (lichtgrijze achtergrond)
- Visuele indicatie per teamblok:
  - Grijs: nog niet beoordeeld
  - Oranje: deels beoordeeld
  - Groen: volledig beoordeeld
- Onder de kalender: assessoren per dag en totaalvoortgang

#### Overige planning
- CSV-import voor deelnemersdata (herbruikbaar voor volgende cohorten)
- Automatische verdeling van teams over tijdslots bij import (elk team krijgt een volgend slot van 30 min)

### 2. Dataverzameling tijdens assessment
**Dit is de kritieke fase - UX moet geoptimaliseerd zijn voor minimale afleiding.**
**Primair apparaat: laptop (muis/trackpad). Touch is geen vereiste.**

#### Per team-gesprek:
- **Header**: teamnummer, namen van beide studenten
- **Duo- vs. individuele scoring**: per criterium een toggle om aan te geven of de score voor het hele duo geldt of per student apart. Default: individueel, maar criteria 4 (professionele bijdrage) kan typisch als duo-score.
- **Per student (of duo), per criterium**:
  - 4 grote klik-knoppen: Onder / Op / Boven / Excellent (kleurgecodeerd)
  - Klik om te scoren, klik opnieuw om te wijzigen
  - Observatie-chips: voorgedefinieerde tags die je kunt klikken (geen typen nodig)
  - Optioneel: inklapbaar vrij tekstveld voor uitzonderlijke observaties

#### Voorgedefinieerde observatie-tags per criterium:
1. **Veranderen en evalueren**: `helder uitgelegd` `theorie correct` `goed voorbeeld` `te oppervlakkig` `geen theorie` `verband met praktijk`
2. **Communicatie**: `actief luisteren` `helder verwoord` `professionele toon` `afwachtend` `onderbreekt` `goede vragen`
3. **Schakelen en verbinden**: `weerstand herkend` `creatieve oplossing` `draagvlak gecreeerd` `empathisch` `te confronterend` `stakeholders betrokken`
4. **Professionaliseren (bijdrage)**: `duidelijke bijdrage` `initiatief getoond` `groei zichtbaar` `reflectief` `passief` `samenwerking sterk`
5. **Professionaliseren (intercultureel)**: `issue benoemd` `goed omgegaan` `geleerd van` `oppervlakkig` `geen voorbeeld` `bewust van impact`
6. **Handelen vanuit waarden**: `dilemma helder` `OOA verwezen` `genuanceerd` `eigen positie` `te zwart-wit` `ethisch bewust`

#### Snel-notitie mogelijkheden:
- Tab-toets of knoppen om tussen studenten te wisselen
- Optioneel tekstveld per criterium (standaard ingeklapt, uitklappen met klik)
- Alles auto-saved naar localStorage

### 3. Verwerking (na afloop)
- Automatische berekening totaalscore per student
- Automatische omzetting naar cijfer via scoresleutel
- Vlaggen van studenten met "Onder niveau" op een of meer criteria (= onvoldoende)
- Overzicht per groep (BKN-F01 / BKN-F02)
- Vergelijking tussen teams

### 4. Presentatie & Terugkoppeling
- **Per student**: feedbackrapport met scores, observaties, sterke punten, ontwikkelpunten
- **Export opties**:
  - PDF per student (feedbackrapport)
  - CSV totaaloverzicht (voor administratie)
  - JSON backup (voor hergebruik/archivering)
- **Dashboard**: visueel overzicht scores per criterium, gemiddelden, verdeling

---

## Niet-functionele eisen

- **Single HTML file**: geen server, geen dependencies, geen build step
- **Offline**: werkt volledig zonder internet (na eerste keer openen)
- **localStorage**: alle data persistent opgeslagen in browser
- **Responsive**: geoptimaliseerd voor laptop, bruikbaar op tablet
- **Klik-first**: grote knoppen (min 44px), duidelijke hover/focus states, keyboard shortcuts voor power-users
- **Snel**: pagina laadt instant, geen loading states
- **Donkere/lichte modus**: om schermafleiding te minimaliseren
- **Geen spraak-API nodig**: invoer via klikken en optioneel tekstveld

---

## Technische aanpak

- Single `index.html` met inline `<style>` en `<script>`
- Vanilla JS (geen frameworks) voor snelheid en eenvoud
- CSS Grid/Flexbox voor responsive layout
- localStorage API voor persistentie
- Geen externe API's nodig
- Blob API + download voor exports (CSV, JSON)
- Print CSS voor PDF-export (Ctrl+P / Cmd+P)
- FileReader API voor CSV-import

---

## Schermen / Views

1. **Dashboard** - overzicht dagen, teams, voortgang
2. **Assessment** - de kernview tijdens gesprekken (minimal UI)
3. **Resultaten** - overzicht scores, cijfers, vlaggen
4. **Feedback** - per-student rapport genereren/bekijken
5. **Exporteren** - data exporteren in diverse formaten, CSV import, backup/restore

---

## Verificatie / Testen

1. Open `index.html` in browser (studentendata is voorgeladen)
2. Controleer of alle studenten en teams correct worden weergegeven op het dashboard
3. Klik op een team en score alle 6 criteria voor beide studenten
4. Controleer automatische cijferberekening en zak/slaag logica
5. Ga naar Feedback, selecteer een student, en controleer het rapport
6. Exporteer CSV en controleer correctheid
7. Sluit browser, heropen - check of data bewaard is gebleven (localStorage)
8. Test duo-score toggle: stel een criterium in als duo-score en check dat beide studenten dezelfde score krijgen
