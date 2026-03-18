---
name: webflow-new-project
description: Use when the user wants to create a new website or web project from scratch. Guides through a complete brainstorming session, creates all design documents (PRD, style guide, sitemap, copy deck), then proposes to create a Pencil mockup before building in Webflow. Also use when the user says "new site", "new project", "start from scratch", or "build me a website".
---

# Webflow New Project

Brainstormer avec l'utilisateur pour créer tous les documents de design nécessaires
avant de toucher Webflow ou Pencil.

<HARD-GATE>
Ne jamais ouvrir Webflow, créer une page, choisir des couleurs, des fonts,
ou lancer une maquette Pencil avant que les 4 documents soient complets
et validés par l'utilisateur.
</HARD-GATE>

## Flow complet

```
Brainstorm (questions 1 par 1)
  → PRD validé
  → Style Guide validé
  → Sitemap validé
  → Copy Deck validé
  → Proposition maquette Pencil (recommandé mais optionnel)
    → Si oui : invoquer pencil-mockup
    → Si non : passer directement à Webflow
  → Setup Webflow
  → webflow-discovery
  → webflow-build-page
```

## Checklist

### Phase 1 : Brainstorm (une question à la fois)

- [ ] 1. Expliquer le process à l'utilisateur :
         "On va d'abord brainstormer ensemble pour créer tous les documents
         de design. Ensuite on pourra faire une maquette dans Pencil si tu veux,
         et enfin construire sur Webflow."
- [ ] 2. Poser les questions d'identité (une par une) :
         - Quel est le nom du projet / de la marque ?
         - C'est quoi l'objectif principal du site ? (vendre, informer, leads...)
         - Qui est la cible ? (âge, niveau technique, besoins)
         - Cite 2-3 sites que tu aimes visuellement
- [ ] 3. Poser les questions de structure (une par une) :
         - Quelles pages faut-il ? (home, about, contact, blog, shop...)
         - Est-ce qu'il y a du contenu dynamique ? (blog, catalogue, avis...)
         - Quelle est l'action de conversion principale ? (formulaire, achat, rdv...)
- [ ] 4. Poser les questions de design (une par une) :
         - Tu as déjà une charte graphique ? (couleurs, fonts, logo)
         - Style visuel : minimaliste, bold, corporate, créatif ?
         - Dark mode, light mode, ou les deux ?
- [ ] 5. Poser les questions techniques (une par une) :
         - Tu as un domaine ? Lequel ?
         - Tu as déjà un compte Webflow ?
         - Multilingue ?

### Phase 2 : Création des documents

- [ ] 6. Créer le dossier `~/.claude/webflow/{project-name}/`
- [ ] 7. Rédiger et sauvegarder le PRD (`prd.md`) → utiliser le template
- [ ] 8. Faire valider le PRD par l'utilisateur
- [ ] 9. Rédiger et sauvegarder le Style Guide (`style-guide.md`) → utiliser le template
         - Proposer des couleurs concrètes (hex) basées sur les références citées
         - Proposer des fonts concrètes (Google Fonts de préférence)
         - Montrer la palette et demander validation
- [ ] 10. Faire valider le Style Guide par l'utilisateur
- [ ] 11. Rédiger et sauvegarder le Sitemap (`sitemap.md`) → utiliser le template
          - Lister chaque page avec ses sections détaillées
          - Pour chaque section : type (hero, features, CTA, FAQ...) + contenu prévu
- [ ] 12. Faire valider le Sitemap par l'utilisateur
- [ ] 13. Rédiger et sauvegarder le Copy Deck (`copy-deck.md`) → utiliser le template
          - Écrire les vrais textes : titres, descriptions, CTAs, boutons
          - Adapter le tone of voice à la cible
- [ ] 14. Faire valider le Copy Deck par l'utilisateur

### Phase 3 : Maquette Pencil (recommandée)

- [ ] 15. Demander à l'utilisateur :
          "Les 4 documents sont prêts. Je recommande de créer une maquette
          dans Pencil avant de construire sur Webflow — ça permet de valider
          le rendu visuel rapidement. On fait la maquette ?"
- [ ] 16. Si oui :
          a. Vérifier que le MCP Pencil est installé
          b. Invoquer le skill `pencil-mockup` avec le chemin des docs
          Si non :
          a. Passer à l'étape 17

### Phase 4 : Transition vers Webflow

- [ ] 17. Guider l'utilisateur pour créer le site dans Webflow si pas encore fait
- [ ] 18. Vérifier que le MCP Webflow est installé et connecté
- [ ] 19. Invoquer `webflow-discovery` sur le nouveau site
- [ ] 20. Invoquer `webflow-build-page` en chargeant les docs de design

## Prérequis MCP

### Webflow MCP (obligatoire pour le build)
```bash
claude mcp add --transport http webflow https://mcp.webflow.com/mcp
```

### Pencil MCP (recommandé pour les maquettes)
Pencil doit être installé comme extension dans votre IDE (VS Code / Cursor).
Le MCP Pencil est fourni avec l'extension — pas d'installation séparée.
Si le MCP Pencil n'est pas détecté, proposer de continuer sans maquette.

### Skills officiels Webflow (recommandé)
```bash
claude plugin marketplace add webflow/webflow-skills
claude plugin install webflow-skills@webflow-skills
```

## Dossier de sortie

Tous les documents sont sauvegardés dans :
```
~/.claude/webflow/{project-name}/
├── prd.md
├── style-guide.md
├── sitemap.md
├── copy-deck.md
├── mockups/          ← créé par pencil-mockup si utilisé
│   ├── home.pen
│   ├── home.png
│   └── ...
└── discovery/        ← créé par webflow-discovery après setup Webflow
    ├── discovery.md
    ├── components.md
    ├── styles.md
    └── pages.md
```

Une fois le site Webflow créé, le dossier peut être renommé avec le site-id
pour rester cohérent avec webflow-discovery.

## Règles

- UNE question à la fois, jamais plusieurs
- Proposer des choix multiples quand c'est possible
- Ne jamais deviner les couleurs/fonts : demander ou proposer avec validation
- Chaque document doit être validé AVANT de passer au suivant
- La maquette Pencil est recommandée mais JAMAIS obligatoire
- Si l'utilisateur est pressé, on peut fusionner certaines validations
