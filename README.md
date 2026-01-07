# 🎟️ ConForte Symphonic – Ticketverkoop Leaderboard & Dashboard

Deze repository publiceert automatisch een **live dashboard** met de ticketverkoop van ConForte Symphonic, inclusief:

- het **totaal aantal verkochte tickets**
- een **leaderboard** op basis van referrals
- een **cumulatieve verkoopgrafiek** met eenvoudige forecast
- publicatie via **GitHub Pages**, ingebed in **WordPress**

De volledige pipeline draait **automatisch** via DataLab.

---

## 🎯 Doel

Deze repo haalt de output van de **leaderboard-analyse van de ticketverkoop** en publiceert deze als een **GitHub Page**:

👉 **Live dashboard:**  
https://conforte-symphonic.github.io/leaderboard-ticketverkoop/

Deze pagina wordt gebruikt als **read-only dashboard** voor delen via `<iframe>` op conforte.be/ledensectie.

---

## 🎼 Jenkins – Ticketverkoop & analyse

De ticketverkoop en analyse gebeuren via een **DataLab workbook**:

👉 **DataLab workbook (Jenkins):**  
https://www.datacamp.com/datalab/w/4be02e50-2ff0-4f26-867c-70a1dfa6b814/edit

Dit notebook:
- haalt data op uit de Stamhoofd API
- verwerkt en analyseert de orders
- genereert tabellen en grafieken
- exporteert het eindresultaat naar HTML
- publiceert automatisch naar GitHub Pages

De workbook runt elke dag om 23u55 automatisch.

---

## 🧩 Overzicht van de volledige flow
```
Stamhoofd API
     ↓
DataLab notebook (Python)
     ↓
Analyse + matching + visualisatie
     ↓
HTML generatie (index.html)
     ↓
Push naar GitHub repo (docs/)
     ↓
GitHub Pages
     ↓
WordPress iframe
```

---

## 🔐 Databron: Stamhoofd API

### Wat wordt opgehaald
- Alle **orders** uit één specifieke webshop
- Per order:
  - aantal tickets
  - datum/tijd
  - ingevulde **Referral** (verplicht veld)

### Authenticatie
- Via een **API key**, opgeslagen als environment variable:
```
  STAMHOOFD_API_KEY
```

### Endpoint
- Webshop orders endpoint (`/webshop/{id}/orders`)
- Inclusief pagination via `updatedSince` / `afterNumber`

---

## 🧠 Verwerking & logica

### 1. Orders filteren
- Orders **zonder referral** worden genegeerd (meestal tests).
- Tickets worden geteld via `cart.items[].amount`.

---

### 2. Referral-classificatie

Elke referral wordt geclassificeerd als één van:

| Type | Beschrijving |
|------|-------------|
| **Muzikant** | Matcht (fuzzy) met een naam uit `leden ConForte 2026.csv` |
| **Via koren** | Bevat expliciete koor-referenties |
| **Onbekend** | Geen duidelijke match |

#### Speciale regels
- Instrumentnamen (`;Viool`, `(Cello)`, `- Hobo`, …) worden **volledig verwijderd**
- Handmatige overrides (bv. `Maarten → Maarten Van den Broeck`)
- Voornaam-only matching met strengere drempel

---

### 3. Leaderboard-logica

- Muzikanten worden **gegroepeerd en gesorteerd op tickets (aflopend)**
- Alleen muzikanten krijgen:
  - een **ranking**
  - 🥇🥈🥉 voor top 3
- `"via koren"` en `"onbekend"`:
  - staan **onderaan**
  - **geen ranking**
- Onderaan staat altijd een **Totaal**-rij

---

### 4. Verkoopgrafiek & forecast

- Cumulatieve ticketverkoop per dag
- Verticale lijn: **15 maart 2026** (laatste concertdag)
- Horizontale lijn: **2000 tickets** (max capaciteit)
- **Lineaire forecast** (op laatste N dagen) met:
  - stippellijn
  - marker op verwachte datum waarop 2000 tickets bereikt worden

---

## 📊 Output

### In DataLab
- Overzichtstabellen (debug / analyse)
- Grafiek (matplotlib)
- Leaderboard (pandas → HTML)

### Naar HTML
Alles wordt samengebracht in **één HTML-pagina** met:
- KPI: totaal verkochte tickets
- Grafiek (als embedded PNG)
- Leaderboard (HTML-tabel)

---

## 🌍 Publicatie via GitHub Pages

### Repository
```
https://github.com/ConForte-Symphonic/leaderboard-ticketverkoop
```

### Structuur
```
docs/
└── index.html  ← automatisch gegenereerd
```

### Public URL
```
https://conforte-symphonic.github.io/leaderboard-ticketverkoop/
```

### Update-mechanisme
- DataLab script:
  - genereert `docs/index.html`
  - pusht via **GitHub Contents API**
- Authenticatie via classic PAT:
```
  GITHUB_TOKEN (scope: public_repo)
```

---

## 🌐 WordPress integratie

De GitHub Pages-site wordt ingesloten via `<iframe>`:
```html
<iframe
  src="https://conforte-symphonic.github.io/leaderboard-ticketverkoop/"
  style="width:100%; height:1200px; border:0;"
  loading="lazy">
</iframe>
```

Geen WordPress plugins nodig.

---

## ⏰ Automatische updates (DataLab)

Het notebook draait **dagelijks automatisch**.

Tijdstip wordt empirisch bepaald op basis van historische verkoopuren.

**Resultaat:**
- Elke dag een up-to-date dashboard
- Geen manuele tussenkomst nodig

---

## 📁 Bestanden

| Bestand | Doel |
|---------|------|
| `notebook.ipynb` | Volledige pipeline (API → analyse → HTML → GitHub) |
| `leden ConForte 2026.csv` | Canonieke lijst muzikanten |
| `docs/index.html` | Gegenereerde publieke pagina |
| `README.md` | Deze documentatie |

---

## 🔧 Vereisten

**Python 3.10+**

**Packages:**
- `pandas`
- `numpy`
- `requests`
- `matplotlib`
- `rapidfuzz`

**DataLab environment met:**
- `STAMHOOFD_API_KEY`
- `GITHUB_TOKEN`

---

## 🛡️ Privacy & veiligheid

- Geen persoonsgegevens worden publiek gemaakt
- Enkel geaggregeerde tellingen
- API-sleutels worden niet gecommit
- GitHub Pages is read-only output

---

## ✨ Mogelijke uitbreidingen

- Meerdere concerten / webshops
- Meerdere leaderboards (per datum / per koor)
- Export naar PDF
- E-mailrapport
- Interactieve grafiek (Plotly)

---

## 👥 Onderhoud

Dit project is opgezet zodat:
- **1 technisch persoon** het notebook onderhoudt (maar deze runt elke dag om 23u55)
- **iedereen** het resultaat kan raadplegen via WordPress

