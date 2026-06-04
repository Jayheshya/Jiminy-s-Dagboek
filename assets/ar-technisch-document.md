# Technisch Document — `ar.html`
### Jiminy's AR — Morele Spiegel

---

## Inhoudsopgave

1. [Overzicht](#1-overzicht)
2. [Bestandsstructuur](#2-bestandsstructuur)
3. [URL-parameters](#3-url-parameters)
4. [Content aanpassen: ENTRIES](#4-content-aanpassen-entries)
5. [Visuele stijl: CSS-variabelen](#5-visuele-stijl-css-variabelen)
6. [UI-elementen en hun IDs](#6-ui-elementen-en-hun-ids)
7. [Gedragsinstellingen](#7-gedragsinstellingen)
8. [Spraak (TTS)](#8-spraak-tts)
9. [Camera & demo-modus](#9-camera--demo-modus)
10. [Veelvoorkomende aanpassingen](#10-veelvoorkomende-aanpassingen)

---

## 1. Overzicht

`ar.html` is een standalone pagina die een AR-achtige beleving simuleert bovenop een live camerastream. De pagina laadt een dagboekinvoer uit het `ENTRIES`-object op basis van een URL-parameter, en toont drie interactieve momenten: **contextuele uitleg**, **goede keuze** en **slechte keuze**.

**Twee modi:**
- **Camera-modus** — werkt op HTTPS met camera-API. De gebruiker kiest een moment via knoppen onderin.
- **Demo-modus** — fallback zonder camera. Identieke ervaring, via grotere knoppen op een donker scherm.

---

## 2. Bestandsstructuur

Het bestand is volledig zelfstandig (één HTML-bestand). Er zijn geen externe JS-bestanden. De opbouw is:

```
ar.html
├── <head>
│   ├── Google Fonts (IM Fell English, Cinzel, Crimson Pro)
│   └── <style> — alle CSS in één blok
├── <body>
│   ├── #camera-video        — live videostream
│   ├── #camera-loading      — laadscherm bij opstarten
│   ├── #geen-camera         — foutscherm bij geen cameratoegang
│   ├── #demo-mode           — demo-modus scherm
│   ├── #top-bar             — titelbalk + terugknop + geluidsknop
│   ├── #scan-knoppen        — actieknoppen in camera-modus
│   ├── #jiminy-panel        — tekstpaneel van Jiminy
│   ├── #portal-overlay      — geanimeerde cirkel bij keuze
│   ├── #oordeel-banner      — eindoordeel na 3,5 seconde
│   └── <script>             — alle logica in één blok
```

---

## 3. URL-parameters

De pagina leest twee parameters uit de URL:

| Parameter | Standaard       | Functie                                      |
|-----------|-----------------|----------------------------------------------|
| `entry`   | `sora-hart`     | Welke dagboekinvoer wordt geladen             |
| `terug`   | `index.html`    | Waar de "← Dagboek"-knop naartoe verwijst    |

**Voorbeeld:**
```
ar.html?entry=riku-deur&terug=dagboek.html
```

De beschikbare `entry`-waarden zijn de sleutels van het `ENTRIES`-object (zie sectie 4).

---

## 4. Content aanpassen: ENTRIES

Dit is het **belangrijkste onderdeel om aan te passen**. Het `ENTRIES`-object staat bovenaan het `<script>`-blok (regel ~539) en bevat alle teksten en iconen per dagboekinvoer.

### Structuur van één invoer

```javascript
'sleutel-naam': {
  titel: 'Zichtbare naam in de titelbalk',

  context: {
    tekst: 'Korte inleidende zin die cursief boven Jiminy\'s tekst staat.',
    type: 'Morele context',   // Label in het Jiminy-paneel (niet aanpassen tenzij gewenst)
    kleur: '#5bc8d8',         // Accentkleur van het paneel (aqua = context)
  },

  goed: {
    icoon: '💛',              // Emoji in de portalcirkel
    scène: 'Beschrijving van wat er gebeurt bij de goede keuze.',
    oordeel: '"Citaat als eindoordeel van Jiminy."',
  },

  slecht: {
    icoon: '🌑',
    scène: 'Beschrijving van wat er gebeurt bij de slechte keuze.',
    oordeel: '"Citaat als eindoordeel van Jiminy."',
  },

  jiminy_context: 'Wat Jiminy zegt bij de context-scan (voorgelezen via TTS).',
  jiminy_goed:    'Wat Jiminy zegt bij de goede keuze (voorgelezen via TTS).',
  jiminy_slecht:  'Wat Jiminy zegt bij de slechte keuze (voorgelezen via TTS).',
},
```

### Bestaande invoeren

| Sleutel           | Titel                              |
|-------------------|------------------------------------|
| `sora-hart`       | Sora verbrijzelt zijn eigen hart   |
| `riku-deur`       | Riku opent de deur naar duisternis |
| `ansem-verzegeld` | Ansem wordt verzegeld              |

### Nieuwe invoer toevoegen

Voeg een nieuw object toe aan `ENTRIES` vóór de sluitende `};`:

```javascript
'mijn-entry': {
  titel: 'Titel van mijn nieuwe entry',
  context: {
    tekst: 'Inleidende zin.',
    type: 'Morele context',
    kleur: '#5bc8d8',
  },
  goed: {
    icoon: '✨',
    scène: 'Wat er gebeurt bij de goede keuze.',
    oordeel: '"Het oordeel van Jiminy."',
  },
  slecht: {
    icoon: '🌑',
    scène: 'Wat er gebeurt bij de slechte keuze.',
    oordeel: '"Het oordeel van Jiminy."',
  },
  jiminy_context: 'Jiminy\'s toelichting bij context.',
  jiminy_goed:    'Jiminy\'s reactie op de goede keuze.',
  jiminy_slecht:  'Jiminy\'s reactie op de slechte keuze.',
},
```

Roep de pagina dan aan met `ar.html?entry=mijn-entry`.

### Standaard-entry wijzigen

Als er geen `entry`-parameter in de URL staat, wordt `sora-hart` geladen. Verander dat op deze regel:

```javascript
// Regel ~602
const entry = ENTRIES[entryId] || ENTRIES['sora-hart'];
//                                          ↑ pas dit aan
```

---

## 5. Visuele stijl: CSS-variabelen

Alle kleuren staan bovenaan het `<style>`-blok als `:root`-variabelen. Pas hier de kleurpaletten aan:

```css
:root {
  --ink:              #1a1409;   /* Donkerste tekstkleur */
  --parchment:        #f5ead0;   /* Licht perkamentkleur (oordeel-tekst) */
  --parchment-dark:   #e8d9b5;
  --parchment-shadow: #c9b48a;   /* Kleur van Jiminy's tekst */
  --night:            #0a0c14;   /* Achtergrond laadscherm / foutscherm */
  --deep:             #0f1220;   /* Achtergrond demo-modus */
  --starlight:        #c8d4f0;   /* Standaard tekstkleur body */
  --gold:             #c8973a;   /* Goud — primaire accentkleur */
  --gold-dim:         #8a6520;   /* Gedempte goudkleur */
  --gold-rgb:         200, 151, 58; /* RGB-waarden voor goud (gebruik in rgba()) */
  --light-gold:       rgba(200, 151, 58, 0.15);
  --aqua:             #5bc8d8;   /* Accentkleur context-knoppen */
  --aqua-rgb:         91, 200, 216;
  --violet:           #a07cc8;   /* Niet actief gebruikt, beschikbaar */
  --violet-rgb:       160, 124, 200;
}
```

### Kleuren per keuze-type

| Type    | Kleur (tekst/rand)  | Kleur (achtergrond hover) |
|---------|---------------------|---------------------------|
| Context | `#5bc8d8` (aqua)    | `rgba(91,200,216,0.15)`   |
| Goed    | `#5DCAA5` (groen)   | `rgba(29,158,117,0.15)`   |
| Slecht  | `#F09595` (rood)    | `rgba(163,45,45,0.15)`    |

Deze kleuren staan ook hard-coded in de JS (bij `toonKeuze()`). Als je ze wil aanpassen, doe het op **beide** plekken:

```javascript
// Regel ~748
const panelKleur = type === 'goed' ? '#5DCAA5' : '#F09595';
// Regel ~758
const kleur = type === 'goed' ? '#5DCAA5' : '#c05050';
```

### Lettertypen

```css
font-family: 'Cinzel', serif;      /* Titels, knoppen, labels */
font-family: 'IM Fell English', serif; /* Italieke sfeer-teksten */
font-family: 'Crimson Pro', serif; /* Body-tekst (standaard) */
```

Lettertypen worden geladen via Google Fonts in de `<head>`. Verander de `href` daar als je andere fonts wil.

---

## 6. UI-elementen en hun IDs

Hieronder een overzicht van alle HTML-elementen met hun ID en functie:

| ID                    | Element       | Functie                                              |
|-----------------------|---------------|------------------------------------------------------|
| `camera-video`        | `<video>`     | Live camerastream achtergrond                        |
| `camera-loading`      | `<div>`       | Laadscherm (verdwijnt na camera-init)                |
| `geen-camera`         | `<div>`       | Foutscherm bij geen cameratoegang                    |
| `demo-mode`           | `<div>`       | Demo-modus wrapper                                   |
| `demo-content`        | `<div>`       | Inhoud demo-modus (knoppen)                          |
| `top-bar`             | `<div>`       | Bovenste balk met terugknop en muteknop              |
| `back-btn`            | `<a>`         | Terugknop (href ingesteld vanuit JS)                 |
| `entry-label`         | `<div>`       | Titelweergave in de balk                             |
| `mute-btn`            | `<button>`    | 🔊 / 🔇 toggle voor spraak                           |
| `scan-knoppen`        | `<div>`       | Knoppen onderin scherm (alleen camera-modus)         |
| `jiminy-panel`        | `<div>`       | Tekstpaneel met Jiminy's commentaar                  |
| `panel-type`          | `<span>`      | Label bovenaan het paneel ("Morele context", etc.)   |
| `jiminy-text`         | `<div>`       | De eigenlijke tekst in het paneel                    |
| `portal-overlay`      | `<div>`       | Wrapper voor de portalcirkel                         |
| `portal-frame`        | `<div>`       | De geanimeerde cirkel (krijgt class `goed`/`slecht`) |
| `portal-icon`         | `<div>`       | Emoji in de portal                                   |
| `portal-tekst`        | `<div>`       | Scènetekst in de portal                              |
| `oordeel-banner`      | `<div>`       | Eindoordeel banner (verschijnt na 3,5 sec)           |
| `oordeel-label`       | `<div>`       | Label van het oordeel ("✦ Jiminy's oordeel")         |
| `oordeel-tekst`       | `<div>`       | De oordeeltekst zelf                                 |
| `reset-btn`           | `<button>`    | "↩ Nieuwe scan" knop in het oordeel-banner           |
| `retry-btn`           | `<button>`    | "Opnieuw proberen" knop op foutscherm                |
| `demo-from-camera-btn`| `<button>`    | "Demo-modus" op het camerafout-scherm                |

---

## 7. Gedragsinstellingen

### Vertraging oordeel-banner

Het eindoordeel verschijnt **3,5 seconde** na het activeren van een keuze. Pas dit aan:

```javascript
// Regel ~756
setTimeout(() => {
  // ... oordeel tonen
}, 3500); // ← verander dit getal (in milliseconden)
```

### Camera-fallback timeout

Als de camera wel toegang heeft maar `loadedmetadata` niet triggert, wordt na **4 seconden** alsnog verder gegaan:

```javascript
// Regel ~664
setTimeout(() => {
  if (elCameraLoading.style.display !== 'none') {
    verbergLaadscherm(() => { elScanKnoppen.style.display = 'flex'; });
  }
}, 4000); // ← verander dit indien nodig
```

### Laadscherm fade-duur

```javascript
// Regel ~681
function verbergLaadscherm(callback) {
  elCameraLoading.classList.add('fade-out');
  setTimeout(() => { /* ... */ }, 700); // ← fade-duur in ms
}
```

### Aantal particles in de portal

```javascript
// Regel ~731
for (let i = 0; i < 12; i++) { // ← pas het aantal aan (0 = geen particles)
```

---

## 8. Spraak (TTS)

De spraakfunctie gebruikt de Web Speech API van de browser. Teksten worden voorgelezen wanneer een paneel verschijnt.

```javascript
function spreekTekst(tekst) { /* ... */ }
```

### Instelbaarheid

```javascript
const u = new SpeechSynthesisUtterance(tekst);
u.lang  = 'nl-NL';  // Taal — pas aan voor andere talen
u.rate  = 0.88;     // Spreeksnelheid (1.0 = normaal, 0.88 = iets langzamer)
u.pitch = 1.1;      // Toonhoogte (1.0 = normaal)
```

### Mute-knop

De gebruiker kan het geluid uitschakelen via de 🔊-knop rechtsboven. De staat wordt bijgehouden in de variabele `gemuted` (boolean). Bij muteren wordt ook de lopende spraak gestopt (`speechSynth.cancel()`).

### Welke teksten worden voorgelezen?

| Moment          | Tekst die voorgelezen wordt    |
|-----------------|-------------------------------|
| Context-scan    | `entry.jiminy_context`         |
| Goede keuze     | `entry.jiminy_goed`            |
| Slechte keuze   | `entry.jiminy_slecht`          |

De `scène`- en `oordeel`-teksten worden **niet** voorgelezen.

---

## 9. Camera & demo-modus

### Camera opstarten

Bij het laden controleert de pagina:
1. Is het protocol HTTPS (of localhost)?
2. Is `navigator.mediaDevices.getUserMedia` beschikbaar?

Als beide waar zijn → camera gestart. Anders → direct naar demo-modus.

```javascript
// Regel ~636
const isVeilig = location.protocol === 'https:' || location.hostname === 'localhost' || ...
```

**Camera-instellingen:**
```javascript
video: {
  facingMode: { ideal: 'environment' }, // Achtercamera (mobiel)
  width:  { ideal: 1280 },
  height: { ideal: 720  },
}
```

Pas `facingMode` aan naar `'user'` voor de selfie-camera.

### Demo-modus handmatig starten

De functie `startDemoMode()` is global beschikbaar. Je kunt hem ook vanuit een andere pagina aanroepen door de URL-parameter `demo=true` toe te voegen en de logica te koppelen, of gewoon de knop "Demo-modus" te gebruiken.

---

## 10. Veelvoorkomende aanpassingen

### Terugknop naar een andere pagina

Standaard staat de terugknop op `index.html`. Pas dit aan via de URL-parameter `terug=jouwpagina.html`, of verander de fallback:

```javascript
// Regel ~537
const returnUrl = params.get('terug') || 'index.html'; // ← pas hier aan
```

### Teksten in de UI aanpassen

| Tekst                                | Waar te vinden          |
|--------------------------------------|-------------------------|
| "Camera initialiseren…"              | HTML, regel ~430        |
| "Cameratoegang vereist"              | HTML, regel ~436        |
| "Kies een moment"                    | HTML, regel ~484        |
| "Morele Spiegel" (demo-header)       | HTML, regel ~445        |
| "Jiminy's AR-ervaring" (demo-titel)  | HTML, regel ~446        |
| "Op mobiel…" (demo-footer)           | HTML, regel ~471        |
| "↩ Nieuwe scan"                      | HTML, regel ~526        |
| "← Dagboek"                          | HTML, regel ~477        |

### Jiminy's avatar-emoji wijzigen

Het 🦗-icoontje staat op twee plekken:

```html
<!-- HTML, regel ~505 -->
<span class="panel-avatar">🦗</span>
```

```html
<!-- HTML, regel ~449 -->
<span class="btn-icon">🦗</span>
```

### Animaties uitschakelen

Om de portal-animatie te verwijderen:
```css
/* Verander in het <style>-blok: */
.portal-frame {
  animation: none; /* was: portalAppear 0.8s ... */
}
```

Om het Jiminy-avatar-floaten te stoppen:
```css
.panel-avatar {
  animation: none; /* was: floatAvatar 3s ... */
}
```

---

*Document gegenereerd op basis van `ar.html` — Jiminy's Dagboek / DP11*
