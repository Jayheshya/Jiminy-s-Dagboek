# Jiminy's AR — Opzet & Marker Training

## Structuur

```
jiminy-dagboek/
├── index.html
├── stijl.css
├── ar.html              ← de AR-ervaring (dit bestand)
├── ar-markers/          ← NFT marker bestanden (te trainen)
│   ├── context.fset
│   ├── context.fset3
│   ├── context.iset
│   ├── goed.fset
│   ├── goed.fset3
│   ├── goed.iset
│   ├── slecht.fset
│   ├── slecht.fset3
│   └── slecht.iset
└── assets/
    ├── jiminyumbrella.png
    ├── marker-context.jpg   ← de afgedrukte afbeelding (context)
    ├── marker-goed.jpg      ← de afgedrukte afbeelding (goede keuze)
    └── marker-slecht.jpg    ← de afgedrukte afbeelding (slechte keuze)
```

---

## Stap 1 — NFT markers trainen

Gebruik de AR.js NFT marker trainer:  
👉 https://carnaux.github.io/NFT-Marker-Creator/

**Aanbevelingen voor goede herkenning:**
- Gebruik hoog-contrast afbeeldingen (veel details, asymmetrisch)
- Minimale afmetingen: 400×400px
- Upload elk van de drie afbeeldingen afzonderlijk
- Download de drie gegenereerde bestanden (`.fset`, `.fset3`, `.iset`) per marker
- Zet ze in de `ar-markers/` map met de bestandsnamen `context`, `goed`, `slecht`

---

## Stap 2 — AR activeren vanuit index.html

Voeg een AR-knop toe aan elke `.entry-content` in `index.html`:

```html
<!-- Voeg dit toe BINNEN .entry-content, ná de .moral-weight div -->
<a href="ar.html?entry=sora-hart&terug=index.html" class="ar-launch-btn">
  <span>🦗</span> Open AR-ervaring
</a>
```

Voeg de bijbehorende CSS toe aan `stijl.css`:

```css
.ar-launch-btn {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  margin-top: 16px;
  font-family: 'Cinzel', serif;
  font-size: 10px;
  letter-spacing: 3px;
  text-transform: uppercase;
  color: var(--gold);
  text-decoration: none;
  padding: 9px 18px;
  border: 1px solid rgba(var(--gold-rgb), 0.35);
  border-radius: 1px;
  background: rgba(var(--gold-rgb), 0.05);
  transition: all 0.25s ease;
}
.ar-launch-btn:hover {
  background: rgba(var(--gold-rgb), 0.12);
  border-color: var(--gold);
}
```

---

## Stap 3 — Entry IDs koppelen

Elke entry in `index.html` krijgt een uniek `entry`-ID dat overeenkomt met een sleutel in het `ENTRIES`-object in `ar.html`.

Huidige entries in `ar.html`:
| ID | Entry |
|----|-------|
| `sora-hart` | Sora verbrijzelt zijn eigen hart |
| `riku-deur` | Riku opent de deur naar duisternis |
| `ansem-verzegeld` | Ansem wordt verzegeld |

**Nieuwe entries toevoegen:** voeg een blok toe aan het `ENTRIES`-object bovenin `ar.html`. Geen andere codewijzigingen nodig (TR-03 ✓).

---

## Stap 4 — Hosting op GitHub Pages

GitHub Pages levert automatisch HTTPS — dit is vereist voor camera-toegang.

```bash
# Zorg dat alle bestanden in dezelfde repository staan
git add ar.html ar-markers/ assets/
git commit -m "Add AR experience"
git push
```

---

## Demo modus

Op desktop (of zonder camera) toont de pagina automatisch de **demo modus**: drie knoppen waarmee de volledige AR-beleving gesimuleerd kan worden zonder camera. Ideaal voor presentaties.

---

## Geluid (Web Speech API)

Fase 1 gebruikt de ingebouwde Web Speech API als placeholder (TR-05 aanpassing; echt ingesproken audio is buiten scope voor v1).  
De stemtaal is ingesteld op `nl-NL` met een lichte pitch-aanpassing voor de Jiminy-stem.

Toekomstig: vervang `spreekTekst()` door een `<audio>`-element met echte opnames per entry.
