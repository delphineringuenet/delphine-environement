# Benchmark des templates de documentation API — Portail développeur

> Objectif : identifier les meilleures pratiques de structure et de présentation de la documentation API, comparer les outils capables de les produire, et en déduire un **template cible** pour notre portail développeur.

---

## 1. Périmètre et méthode

Le benchmark porte sur trois niveaux complémentaires :

| Niveau | Question posée | Section |
|---|---|---|
| **Cadres de référence** | Comment organiser la documentation ? | §2 |
| **Portails de référence** | Que font les meilleurs portails du marché ? | §3 |
| **Outils / générateurs** | Avec quel outil produire et maintenir le template ? | §4 |

### Grille d'évaluation

Chaque portail et outil est évalué sur les critères suivants :

| # | Critère | Ce qu'on regarde |
|---|---|---|
| C1 | **Time to first call** | Rapidité pour obtenir un premier appel réussi (quickstart, clés de test, sandbox) |
| C2 | **Référence d'API** | Exhaustivité des endpoints, paramètres, schémas, exemples de réponse |
| C3 | **Interactivité** | Console « Try it », exemples de code multi-langages, copier-coller |
| C4 | **Navigation & recherche** | Arborescence, recherche plein texte, liens profonds |
| C5 | **Gestion des erreurs** | Codes d'erreur documentés, causes, remèdes |
| C6 | **Versioning & changelog** | Versions d'API, dépréciations, historique des changements |
| C7 | **Maintenabilité** | Génération depuis OpenAPI, docs-as-code, revue en PR |
| C8 | **Personnalisation / marque** | Thème, intégration au portail, composants custom |
| C9 | **Accessibilité & i18n** | Multilingue (FR/EN), RGAA/WCAG, responsive |

---

## 2. Cadres de référence pour structurer la documentation

### 2.1 Diátaxis

Cadre le plus utilisé pour structurer une documentation technique. Il distingue 4 types de contenus, qui ne doivent pas être mélangés :

| Type | Orientation | Exemple dans un portail API |
|---|---|---|
| **Tutoriels** | Apprentissage | « Votre premier paiement en 10 minutes » |
| **Guides pratiques (How-to)** | Résolution d'une tâche | « Gérer les webhooks », « Paginer les résultats » |
| **Référence** | Information exhaustive | Référence des endpoints générée depuis OpenAPI |
| **Explications (Concepts)** | Compréhension | « Modèle de données », « Cycle de vie d'une commande » |

➡️ **Apport pour nous** : fournit l'arborescence de haut niveau du portail et évite l'écueil « tout est dans la référence ».

### 2.2 The Good Docs Project

Projet open source proposant des **templates prêts à l'emploi** (Markdown) avec guide de rédaction : *API reference*, *Quickstart*, *How-to*, *Tutorial*, *Concept*, *Troubleshooting*, *Release notes*, *Glossary*…

➡️ **Apport pour nous** : base concrète pour rédiger nos templates de pages éditoriales (quickstart, guide, changelog).

### 2.3 Spécifications OpenAPI / AsyncAPI

- **OpenAPI 3.x** : standard de fait pour décrire les API REST — source unique de vérité pour la référence.
- **AsyncAPI** : équivalent pour les API événementielles (webhooks, Kafka, MQTT).
- Les champs `description`, `summary`, `example(s)`, `tags`, `x-codeSamples` sont ceux qui alimentent directement le rendu de la documentation → **le template commence dans le fichier OpenAPI**.

---

## 3. Benchmark des portails développeurs de référence

| Portail | Points forts à retenir | Points faibles / limites |
|---|---|---|
| **Stripe** | Mise en page 3 colonnes (navigation / texte / code), sélecteur de langage global, clés de test pré-remplies quand l'utilisateur est connecté, objets documentés avant les endpoints, guides très orientés cas d'usage | Volume important, difficile à reproduire sans outillage maison |
| **Twilio** | Quickstarts par langage, tutoriels complets de bout en bout, exemples de code exécutables | Nombreux produits → navigation parfois dense |
| **GitHub (REST)** | Référence par ressource, exemples curl / JavaScript / GitHub CLI, schémas de réponse dépliables, versioning par date affiché clairement | Peu de contenus « concepts » à côté de la référence |
| **Adyen** | API Explorer interactif, versions d'API sélectionnables, nombreux guides d'intégration par cas métier | Ergonomie hétérogène entre sections |
| **Plaid** | Quickstart avec application exemple, sandbox riche avec données de test, glossaire clair | Référence très longue sur une seule page |
| **Shopify** | Séparation nette entre API (GraphQL / REST), guides, apps et changelog ; versioning trimestriel explicite | Complexité liée au nombre d'API |
| **Swan** (FR, BaaS) | Exemple français pertinent : explorer GraphQL, documentation orientée intégrateur, sandbox | Spécifique GraphQL |

### Motifs récurrents chez les meilleurs

1. **Page d'accueil orientée tâches** (« Je veux… ») plutôt qu'orientée produit.
2. **Quickstart < 10 minutes** avec clé de test / sandbox fournie immédiatement.
3. **Référence en 2 ou 3 colonnes** : explication à gauche, requête / réponse à droite.
4. **Exemples de code multi-langages** synchronisés sur toute la page.
5. **Objets / modèles de données documentés** séparément et réutilisés.
6. **Page Erreurs centralisée** avec code HTTP, code métier, cause et remède.
7. **Authentification** expliquée une fois, en tête, avec un exemple copiable.
8. **Changelog + politique de versioning et de dépréciation** visibles.
9. **Recherche plein texte** performante (souvent Algolia ou recherche IA).
10. **Feedback par page** (« Cette page vous a-t-elle aidé ? »).

---

## 4. Benchmark des outils de génération de documentation API

| Outil | Modèle | Source | Rendu référence | Try it | Guides éditoriaux | Docs-as-code | Remarques |
|---|---|---|---|---|---|---|---|
| **Swagger UI** | Open source | OpenAPI | Liste d'opérations dépliables | ✅ | ❌ | ✅ | Standard historique, UX datée, peu personnalisable |
| **Redoc / Redocly** | OSS (Redoc) + SaaS (Redocly) | OpenAPI | 3 colonnes, très lisible | Redocly uniquement | Redocly uniquement | ✅ | Excellent rendu de référence ; linting OpenAPI (Redocly CLI) |
| **Scalar** | OSS + offre SaaS | OpenAPI | Moderne, 2-3 colonnes | ✅ | Offre SaaS | ✅ | Très bon rapport qualité / effort, intégrable dans de nombreux frameworks |
| **Stoplight Elements** | Open source | OpenAPI | Composant web intégrable | ✅ | Limité | ✅ | Pratique pour l'intégrer dans un portail existant |
| **Docusaurus + plugin OpenAPI** | Open source | Markdown/MDX + OpenAPI | Pages générées par endpoint | ✅ | ✅ | ✅ | Très flexible, hébergement libre, i18n natif ; demande de l'intégration front |
| **Mintlify** | SaaS | MDX + OpenAPI | Moderne, 2 colonnes | ✅ | ✅ | ✅ (Git) | Démarrage rapide, recherche IA ; dépendance fournisseur |
| **ReadMe** | SaaS | OpenAPI + éditeur | 2 colonnes | ✅ | ✅ | Partiel | Métriques d'usage API, édition par non-techniques ; coût |
| **Bump.sh** | SaaS (FR) | OpenAPI / AsyncAPI | Référence claire | Partiel | Limité | ✅ | Points forts : **diff et changelog automatiques** entre versions, AsyncAPI |
| **Fern** | SaaS + OSS | OpenAPI / Fern def | Moderne | ✅ | ✅ | ✅ | Génère aussi les **SDK** clients |
| **GitBook** | SaaS | Éditeur + blocs OpenAPI | Blocs intégrés aux pages | ✅ | ✅ | Sync Git | Idéal pour contenus éditoriaux, référence moins riche |
| **Backstage** | Open source | Catalogue + TechDocs | Plugins API | Via plugins | ✅ (TechDocs) | ✅ | Plutôt portail **interne** (catalogue de services) |

### Scoring indicatif (1 = faible, 5 = excellent)

> Notes à challenger lors d'un POC avec **notre** spec OpenAPI et nos contraintes (hébergement, SSO, budget, RGAA).

| Outil | C1 | C2 | C3 | C4 | C5 | C6 | C7 | C8 | C9 | **Total /45** |
|---|---|---|---|---|---|---|---|---|---|---|
| Swagger UI | 2 | 4 | 4 | 2 | 3 | 2 | 4 | 2 | 2 | **25** |
| Redocly | 3 | 5 | 4 | 4 | 4 | 4 | 5 | 4 | 3 | **36** |
| Scalar | 3 | 5 | 5 | 4 | 4 | 3 | 5 | 4 | 3 | **36** |
| Stoplight Elements | 3 | 4 | 4 | 3 | 3 | 2 | 4 | 3 | 3 | **29** |
| Docusaurus + OpenAPI | 4 | 4 | 4 | 4 | 4 | 4 | 5 | 5 | 5 | **39** |
| Mintlify | 5 | 4 | 5 | 5 | 4 | 3 | 4 | 4 | 3 | **37** |
| ReadMe | 5 | 4 | 5 | 4 | 4 | 4 | 3 | 4 | 3 | **36** |
| Bump.sh | 3 | 4 | 3 | 3 | 3 | 5 | 5 | 3 | 3 | **32** |
| Fern | 4 | 4 | 5 | 4 | 4 | 3 | 5 | 4 | 3 | **36** |
| GitBook | 4 | 3 | 3 | 4 | 3 | 3 | 3 | 4 | 4 | **31** |

### Lecture par scénario

| Si notre priorité est… | Option à privilégier |
|---|---|
| Maîtrise totale, hébergement interne, i18n FR/EN, accessibilité | **Docusaurus + plugin OpenAPI** (ou Scalar intégré) |
| Aller vite avec un rendu premium, équipe réduite | **Mintlify** ou **ReadMe** |
| Qualité de la référence + gouvernance OpenAPI (linting) | **Redocly** |
| Suivi des changements d'API et communication aux partenaires | **Bump.sh** (en complément) |
| Générer SDK + docs à partir d'une même source | **Fern** |

---

## 5. Template cible recommandé

### 5.1 Arborescence du portail

```
Portail développeur
├── Accueil                        → « Je veux… » (cas d'usage principaux)
├── Démarrer
│   ├── Quickstart                 → templates/quickstart.md
│   ├── Authentification           → templates/authentification.md
│   └── Environnements (sandbox / production)
├── Guides                         → templates/guide-pratique.md
│   ├── <Cas d'usage 1>
│   └── Webhooks, pagination, idempotence…
├── Concepts                       → modèle de données, cycles de vie, glossaire
├── Référence API                  → générée depuis OpenAPI (templates/reference-endpoint.md)
│   ├── <Ressource A> : objet + endpoints
│   └── <Ressource B> : objet + endpoints
├── Erreurs                        → templates/erreurs.md
├── Changelog & versioning         → templates/changelog.md
└── Support                        → contact, statut de service, FAQ
```

### 5.2 Templates fournis

| Fichier | Usage |
|---|---|
| [`templates/quickstart.md`](templates/quickstart.md) | Premier appel en moins de 10 minutes |
| [`templates/authentification.md`](templates/authentification.md) | Méthode d'authentification, obtention des clés, exemples |
| [`templates/reference-endpoint.md`](templates/reference-endpoint.md) | Structure d'une page de référence (objet + endpoint) |
| [`templates/guide-pratique.md`](templates/guide-pratique.md) | Guide orienté tâche (How-to) |
| [`templates/erreurs.md`](templates/erreurs.md) | Catalogue des erreurs |
| [`templates/changelog.md`](templates/changelog.md) | Journal des changements et politique de dépréciation |
| [`templates/openapi-exemple.yaml`](templates/openapi-exemple.yaml) | Exemple d'annotation OpenAPI qui alimente la référence |

### 5.3 Règles de rédaction (charte)

- **Une page = un objectif** (principe Diátaxis).
- Titres à l'infinitif pour les guides (« Créer un client »), noms pour la référence (« Clients »).
- Chaque endpoint a : un `summary` (≤ 60 caractères), une `description`, **au moins un exemple de requête et de réponse**, les erreurs possibles.
- Exemples de code **testés** (idéalement exécutés en CI contre la sandbox).
- Pas de valeur secrète réelle dans les exemples : `sk_test_...`, `<VOTRE_CLE_API>`.
- Langue : décider tôt d'une stratégie FR/EN (référence en anglais + guides bilingues est un compromis fréquent).
- Accessibilité : contrastes, navigation clavier, alternatives textuelles aux schémas (RGAA).

---

## 6. Prochaines étapes proposées

1. **Valider les critères et leur pondération** avec les parties prenantes (produit, tech, partenaires).
2. **Short-list de 2-3 outils** selon le scénario retenu (§4).
3. **POC d'une semaine** : même spec OpenAPI réelle + 1 quickstart + 1 guide sur chaque outil.
4. **Test utilisateur** avec 3-5 développeurs (interne/partenaires) : mesurer le *time to first call*.
5. **Décision & industrialisation** : pipeline docs-as-code (lint OpenAPI → build → preview en PR → publication).

---

## Informations utiles pour affiner ce benchmark

- Type d'API exposées (REST, GraphQL, événementielles/webhooks) ?
- Public cible (partenaires, clients, développeurs internes, grand public) ?
- Outil de portail déjà choisi ou non (API Manager type Apigee, Kong, Azure APIM, Gravitee…) ?
- Contraintes : hébergement, SSO, budget, langues, conformité RGAA ?
