# webflow-builder

Plugin Claude Code pour créer des sites Webflow de A à Z — du brainstorm à la publication, en passant par la maquette.

## Le flow complet

```
1. Brainstorm          → Créer le PRD, style guide, sitemap, copy deck
2. Maquette            → Google Stitch ou Pencil (recommandé, optionnel)
3. Build Webflow       → Construire le site en respectant la maquette et la DA
4. Audit + Publish     → Vérifier et publier (via skills officiels Webflow)
```

---

## Installation

### Étape 1 : Installer ce plugin

```bash
# UNE commande, c'est tout
git clone https://github.com/ohvignas/webflow-builder.git ~/.claude/skills/webflow-builder
```

C'est tout. Les skills sont automatiquement détectés par Claude Code au prochain lancement.

### Étape 2 : Installer le MCP Webflow (obligatoire)

```bash
claude mcp add --transport http webflow https://mcp.webflow.com/mcp
```

Suivre les instructions d'authentification quand Claude le demande.
Doc : https://developers.webflow.com/mcp

### Étape 3 : Installer les skills officiels Webflow (recommandé)

```bash
claude plugin marketplace add webflow/webflow-skills
claude plugin install webflow-skills@webflow-skills
```

Repo : https://github.com/webflow/webflow-skills

### Étape 4 : Installer un outil de maquette (recommandé — choisir un ou les deux)

#### Option A : Google Stitch (recommandé)

Stitch génère des UI complètes (HTML/CSS) depuis un prompt texte, via Gemini 2.5 Pro.

```bash
# Setup guidé (gcloud + auth + config MCP)
npx @_davideast/stitch-mcp init
```

Prérequis : Node.js 18+, un compte Google Cloud.
Doc : https://github.com/davideast/stitch-mcp

#### Option B : Pencil

Design vectoriel directement dans l'IDE.

1. Ouvrir VS Code ou Cursor
2. Extensions → chercher "Pencil" → Installer
3. Le MCP Pencil est inclus automatiquement

### Vérification

Relancer Claude Code, puis taper :

```
/webflow-new-project
```

Si le skill se lance, c'est bon.

---

## Utilisation

### Comment lancer les skills

**1. Par slash command :**
```
/webflow-new-project     → Nouveau projet from scratch
/webflow-build-page      → Construire une nouvelle page
/webflow-discovery       → Scanner un site existant
/stitch-mockup           → Maquette avec Google Stitch
/pencil-mockup           → Maquette avec Pencil
```

**2. En langage naturel :**
```
"Je veux créer un nouveau site web"           → webflow-new-project
"Ajoute une page pricing à mon site"          → webflow-build-page
"Fais-moi une maquette de la homepage"        → te demande Stitch ou Pencil
"Scanne mon site Webflow"                     → webflow-discovery
"Audite mon site" / "Publie le site"          → skills officiels Webflow
```

### Quel skill utiliser ?

| Tu veux... | Skill |
|------------|-------|
| Créer un site from scratch | `/webflow-new-project` |
| Ajouter une page à un site existant | `/webflow-build-page` |
| Maquette UI rapide (prompt → UI) | `/stitch-mockup` |
| Maquette vectorielle dans l'IDE | `/pencil-mockup` |
| Scanner un site avant de le modifier | `/webflow-discovery` |
| Auditer (SEO, liens, accessibilité) | Skills officiels Webflow |
| Publier | `/safe-publish` (skill officiel) |

### Premier lancement

```
Toi : /webflow-new-project

Claude : "On va brainstormer ensemble. C'est quoi le nom de ton projet ?"
Toi : "Mon super projet"
Claude : "Quel est l'objectif principal du site ?"
Toi : "Vendre des formations en ligne"
...
(Questions 1 par 1 → 4 docs créés → choix Stitch/Pencil → build Webflow)
```

---

## Skills

| Skill | Description |
|-------|-------------|
| `webflow-new-project` | Brainstorm complet → 4 docs de design → maquette → build Webflow |
| `stitch-mockup` | Générer des UI via Google Stitch (Gemini 2.5 Pro) depuis les docs de design |
| `pencil-mockup` | Créer des maquettes .pen (wireframe + version stylée) dans l'IDE |
| `webflow-build-page` | Construire une page Webflow avec plan validé + maquette optionnelle |
| `webflow-discovery` | Scanner un site existant et documenter sa DA, composants et patterns |

## Workflows

### Nouveau site from scratch
```
webflow-new-project
  ├── Brainstorm (questions 1 par 1)
  ├── Création des 4 docs (PRD, style guide, sitemap, copy deck)
  ├── stitch-mockup ou pencil-mockup (au choix)
  ├── Setup Webflow
  ├── webflow-discovery
  ├── webflow-build-page (pour chaque page)
  ├── accessibility-audit ← skill officiel
  ├── link-checker ← skill officiel
  └── safe-publish ← skill officiel
```

### Nouvelle page sur site existant
```
webflow-discovery (si pas déjà fait)
  └── webflow-build-page
        ├── Mini docs de design pour la page
        ├── Maquette Stitch ou Pencil (recommandé)
        ├── Plan de page validé
        ├── Construction section par section
        └── Audit + publish
```

## Documents générés

```
~/.claude/webflow/{project-name}/
├── prd.md              ← Identité, cible, objectif, conversion
├── style-guide.md      ← Couleurs, fonts, espacements, composants
├── sitemap.md          ← Pages + sections détaillées + SEO
├── copy-deck.md        ← Textes réels pour chaque section
├── mockups/            ← Maquettes (screenshots, HTML, .pen)
│   ├── home.png
│   ├── home.html       ← HTML/CSS généré par Stitch
│   └── ...
└── discovery/          ← Docs auto-générés depuis Webflow
    ├── discovery.md
    ├── components.md
    ├── styles.md
    └── pages.md
```

## Complémentarité

| Ce plugin | Skills officiels Webflow | Google Stitch | Pencil |
|-----------|------------------------|---------------|--------|
| Brainstorm + docs de design | — | — | — |
| — | — | UI depuis prompt texte | Design vectoriel IDE |
| Discovery DA site | Audit santé site | — | — |
| Construction guidée | Publication sécurisée | — | — |
| — | Audit accessibilité, liens, assets | — | — |
