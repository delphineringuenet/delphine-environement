# Qu'est-ce qu'une bonne documentation d'endpoint ?

> Ce document décrit les **informations à fournir pour documenter un endpoint d'API** : ce qui est indispensable, ce qui est recommandé, et comment les meilleurs acteurs le font.
> Template prêt à l'emploi : [`templates/reference-endpoint.md`](templates/reference-endpoint.md)

Une bonne documentation d'endpoint doit permettre à un développeur de **réussir son appel du premier coup, sans deviner et sans contacter le support**. Elle doit répondre à 5 questions :

| # | Question du développeur | Blocs concernés |
|---|---|---|
| 1 | **Qu'est-ce que ça fait, et est-ce le bon endpoint pour mon besoin ?** | Identification, description |
| 2 | **Ai-je le droit de l'appeler, et où ?** | Accès, sécurité, environnements |
| 3 | **Qu'est-ce que je dois envoyer ?** | Requête, paramètres, exemples |
| 4 | **Qu'est-ce que je vais recevoir ?** | Réponse, schéma, exemples |
| 5 | **Que faire quand ça ne marche pas, et à quoi faire attention ?** | Erreurs, comportement, effets de bord |

---

## 1. Les informations à documenter, bloc par bloc

Niveaux : 🔴 **Indispensable** · 🟠 **Recommandé** · 🟢 **Bonus**

### Bloc 1 — Identification

| Information | Niveau | Pourquoi / bonne pratique |
|---|---|---|
| **Titre orienté action** (« Créer un client ») | 🔴 | Le développeur cherche une action, pas une route |
| **Méthode HTTP + chemin** (`POST /v1/customers`) | 🔴 | Visible en haut, copiable |
| **Résumé en une phrase** | 🔴 | Utilisé dans la navigation et la recherche |
| **Description fonctionnelle** : ce que fait l'appel, dans quel cas l'utiliser (ou non), prérequis métier | 🔴 | Évite d'utiliser le mauvais endpoint |
| **Statut** : stable, bêta, déprécié (avec date de retrait et remplaçant) | 🔴 | Ne pas laisser intégrer un endpoint qui va disparaître |
| **Version d'API** concernée / « disponible depuis » | 🟠 | Utile pour les intégrateurs sur d'anciennes versions |
| Lien vers le **concept** ou le **guide** associé | 🟠 | La référence décrit, le guide explique le « pourquoi » |

### Bloc 2 — Accès et sécurité

| Information | Niveau | Pourquoi / bonne pratique |
|---|---|---|
| **Méthode d'authentification** (clé d'API, OAuth 2.0…) | 🔴 | Lien vers la page Authentification, pas de redite |
| **Scopes / permissions requis** | 🔴 | 1re cause d'erreurs 403 ; Microsoft Graph et GitHub l'affichent sur chaque endpoint |
| **URL de base par environnement** (sandbox, production) | 🔴 | Souvent oublié dans la page endpoint |
| **Limites de débit** spécifiques à l'endpoint, si différentes de la règle générale | 🟠 | Évite les 429 en production |
| Restrictions contractuelles ou d'offre (« réservé aux partenaires Premium ») | 🟠 | Évite une intégration inutile |
| Données sensibles / RGPD manipulées | 🟢 | Aide les équipes sécurité des intégrateurs |

### Bloc 3 — Requête

Séparer les paramètres **par emplacement** : chemin (*path*), requête (*query*), en-têtes (*headers*), corps (*body*).

**Pour chaque paramètre :**

| Information | Niveau | Exemple |
|---|---|---|
| **Nom exact** | 🔴 | `customer_id` |
| **Emplacement** | 🔴 | path / query / header / body |
| **Type et format** | 🔴 | `string`, `date-time` ISO 8601, `integer` en centimes |
| **Obligatoire ou facultatif** | 🔴 | Obligatoire |
| **Description métier** (pas une répétition du nom) | 🔴 | ❌ « L'email du client » → ✅ « E-mail utilisé pour l'envoi des factures. Doit être unique par compte. » |
| **Contraintes** : longueur min/max, bornes, regex, taille de liste | 🔴 | 1 à 254 caractères |
| **Valeurs possibles d'un enum, avec la signification de chacune** | 🔴 | `active` : peut être facturé · `suspended` : … |
| **Valeur par défaut** si absent | 🔴 | `limit` = 20 |
| **Unité** (montants, durées, tailles) | 🔴 | Montant en centimes, devise ISO 4217 |
| **Exemple de valeur** réaliste | 🟠 | `cus_8f2k3` plutôt que `string` |
| **Nullable** (différence entre `null`, absent et vide) | 🟠 | Essentiel pour les mises à jour partielles (PATCH) |
| **Dépendances entre paramètres** (« `iban` obligatoire si `method=sepa` ») | 🟠 | Source fréquente d'erreurs 400 |
| **Objets imbriqués** dépliables, avec le même niveau de détail | 🟠 | Stripe affiche les « child parameters » |
| Paramètre déprécié (et remplaçant) | 🟠 | |

**En-têtes à documenter systématiquement :** `Authorization`, `Content-Type`, `Accept`, et le cas échéant `Idempotency-Key`, `If-Match` (verrouillage optimiste), en-tête de version, `Accept-Language`.

### Bloc 4 — Exemples de requête

| Information | Niveau | Bonne pratique |
|---|---|---|
| **Exemple complet, copiable et fonctionnel** (`curl`) | 🔴 | URL, en-têtes et corps : doit marcher tel quel en sandbox |
| **Plusieurs scénarios** : minimal + complet + cas métier particuliers | 🟠 | ex. « paiement simple » / « paiement en 3 fois » |
| **Exemples dans plusieurs langages / SDK** | 🟠 | Sélecteur de langage mémorisé sur tout le portail (Stripe) |
| Exemples **testés automatiquement** en CI | 🟠 | Un exemple faux est pire que pas d'exemple |
| Console « Try it » | 🟢 | Pratique, mais ne remplace pas l'exemple écrit |

### Bloc 5 — Réponse

| Information | Niveau | Bonne pratique |
|---|---|---|
| **Code HTTP de succès** (`200`, `201`, `202`, `204`) | 🔴 | `202` → expliquer comment suivre le traitement asynchrone |
| **Schéma de la réponse, champ par champ** : nom, type, description, format | 🔴 | Même niveau d'exigence que pour les paramètres |
| **Champs toujours présents vs optionnels / nullables** | 🔴 | Évite les plantages côté client |
| **Exemple de réponse complet et réaliste** | 🔴 | Cohérent avec l'exemple de requête |
| **En-têtes de réponse utiles** : `Location`, `ETag`, pagination, `X-RateLimit-*`, `X-Request-Id` | 🟠 | |
| Renvoi vers l'**objet** documenté une seule fois (ex. « Retourne un objet Client ») | 🟠 | Évite les divergences entre endpoints |
| Champs à contenu variable selon le contexte (polymorphisme, `expand`) | 🟠 | Expliquer quand chaque variante apparaît |

### Bloc 6 — Erreurs

| Information | Niveau | Bonne pratique |
|---|---|---|
| **Liste des codes HTTP possibles pour cet endpoint** | 🔴 | GitHub affiche un tableau des codes par endpoint |
| **Code d'erreur métier** stable (`insufficient_funds`) | 🔴 | Le développeur code sa logique dessus, pas sur le message |
| **Cause et solution** pour chaque erreur | 🔴 | ❌ « 422 : Unprocessable Entity » → ✅ « 422 `customer_closed` : le client est clôturé ; réactivez-le via `POST /customers/{id}/reactivate` » |
| **Exemple de corps d'erreur** | 🟠 | Format uniforme (RFC 9457 recommandé) |
| **Peut-on réessayer ?** (et comment : backoff, `Retry-After`) | 🟠 | Distinguer les erreurs définitives des erreurs temporaires |
| Lien vers le catalogue global des erreurs | 🟠 | Les erreurs génériques (401, 429, 500) sont décrites une seule fois |

### Bloc 7 — Comportement et règles métier

C'est le bloc **le plus souvent absent**, et c'est pourtant lui qui fait la différence entre une doc correcte et une bonne doc.

| Information | Niveau | Exemple |
|---|---|---|
| **Idempotence** : l'appel peut-il être rejoué sans risque ? | 🔴 (écriture) | `Idempotency-Key` conservée 24 h |
| **Pagination** : type (curseur, offset), taille max, ordre par défaut | 🔴 (listes) | `limit` max 100, tri par `created_at` décroissant |
| **Filtres et tri** disponibles | 🔴 (listes) | |
| **Synchrone ou asynchrone** et délai de traitement | 🔴 | « Le virement passe à `executed` sous 24 h » |
| **Transitions d'état** autorisées | 🟠 | Une commande `shipped` ne peut plus être annulée |
| **Règles de gestion** non exprimées par le schéma | 🟠 | Unicité, plafonds, jours ouvrés |
| Cohérence des données (délai avant qu'une création apparaisse dans une liste) | 🟢 | |
| Cache, `ETag`, requêtes conditionnelles | 🟢 | |

### Bloc 8 — Effets de bord et liens

| Information | Niveau | Exemple |
|---|---|---|
| **Événements / webhooks déclenchés** | 🟠 | `customer.created` |
| **Autres ressources impactées** | 🟠 | « Crée aussi un mandat SEPA » |
| **Endpoints liés** (étape suivante, opération inverse) | 🟠 | Créer → Récupérer → Supprimer |
| Historique des changements de l'endpoint | 🟢 | Lien vers le changelog filtré |

---

## 2. Benchmark : comment les références du marché documentent un endpoint

Observation des pages endpoint publiques. Le détail peut varier selon les sections et évoluer : **à revérifier au moment de la décision**.

| Information | Stripe | GitHub REST | Microsoft Graph | Adyen | Twilio |
|---|---|---|---|---|---|
| Titre orienté action | ✅ | ✅ | ✅ | ✅ | ✅ |
| Description fonctionnelle | ✅ | ✅ | ✅ | ✅ | ✅ |
| Permissions / scopes **sur la page** | ➖ | ✅ (jetons fine-grained) | ✅ (tableau dédié) | ➖ | ➖ |
| Paramètres séparés par emplacement | ✅ | ✅ | ✅ | ✅ | ✅ |
| Objets imbriqués dépliables | ✅ | ✅ | ➖ | ✅ | ➖ |
| Exemples de code multi-langages | ✅ | ✅ (curl, JS, CLI) | ✅ (HTTP + SDK) | ✅ | ✅ |
| Exemple de réponse | ✅ | ✅ | ✅ | ✅ | ✅ |
| Codes HTTP listés **par endpoint** | ➖ (page Erreurs globale) | ✅ | ➖ | ✅ | ➖ |
| Objet documenté une seule fois et réutilisé | ✅ | ➖ | ✅ (type de ressource) | ➖ | ✅ |
| Console « Try it » | ➖ | ➖ | ✅ (Graph Explorer) | ✅ (API Explorer) | ➖ |

✅ présent de façon systématique · ➖ absent de la page ou traité ailleurs

### Ce qu'il faut retenir de chacun

- **Stripe** : la description des paramètres. Chacun a une explication métier et ses sous-paramètres, et **l'objet est documenté avant les endpoints**. Le code et la réponse s'affichent en colonne de droite, alignés sur le texte.
- **GitHub** : l'exhaustivité technique. On trouve sur la page **les permissions requises** et **le tableau des codes HTTP possibles**.
- **Microsoft Graph** : une structure de page **fixe et prévisible** (Permissions → Requête HTTP → Paramètres optionnels → En-têtes → Corps → Réponse → Exemples). Le développeur sait où chercher.
- **Adyen** : **les réponses documentées par code HTTP** et la console interactive intégrée.
- **Twilio** : des exemples de code nombreux et prêts à l'emploi.

➡️ **Notre cible** : reprendre la **structure fixe** de Microsoft Graph, la **qualité des descriptions** de Stripe et les **permissions + codes HTTP par endpoint** de GitHub.

---

## 3. Structure type d'une page endpoint (ordre recommandé)

```
1. Titre (action) + badge de statut
2. MÉTHODE /chemin  ·  environnements
3. Description : ce que fait l'appel, quand l'utiliser
4. Permissions / scopes requis
5. Paramètres
   5.1 Chemin
   5.2 Requête (query)
   5.3 En-têtes
   5.4 Corps
6. Exemples de requête (multi-scénarios, multi-langages)
7. Réponse : code, schéma, en-têtes, exemple
8. Erreurs : code HTTP · code métier · cause · solution · réessayable ?
9. Comportement : idempotence, pagination, asynchronisme, règles métier
10. Effets de bord : webhooks, ressources impactées
11. Voir aussi : endpoints liés, guide, changelog
```

---

## 4. Rendre ces informations maintenables : correspondance avec OpenAPI

Pour que la doc reste à jour, ces informations doivent vivre dans la **spécification OpenAPI**, pas dans une page rédigée à la main.

| Information | Champ OpenAPI 3.x |
|---|---|
| Titre / résumé | `summary` |
| Description, règles métier, comportement | `description` (Markdown) |
| Regroupement dans la navigation | `tags` |
| Identifiant stable (SDK, liens) | `operationId` |
| Statut déprécié | `deprecated: true` (+ explication dans `description`) |
| Environnements | `servers` |
| Authentification et scopes | `security` + `components.securitySchemes` |
| Paramètres path / query / header | `parameters` (`in`, `required`, `schema`, `description`, `example`) |
| Corps de requête | `requestBody` |
| Type, format, contraintes | `type`, `format`, `minLength`, `maxLength`, `minimum`, `maximum`, `pattern`, `enum`, `default`, `nullable` / `type: [..., "null"]` |
| Dépendances entre paramètres | `oneOf` / `discriminator` + `description` |
| Exemples de requête / réponse | `examples` (plusieurs scénarios nommés) |
| Réponses et erreurs | `responses` (un bloc par code HTTP) |
| En-têtes de réponse | `responses.<code>.headers` |
| Objets réutilisés | `components.schemas` + `$ref` |
| Exemples de code | `x-codeSamples` (extension supportée par plusieurs outils) |
| Webhooks émis | `webhooks` (OpenAPI 3.1) ou `callbacks` |

Voir l'exemple annoté : [`templates/openapi-exemple.yaml`](templates/openapi-exemple.yaml).

💡 Un **linter OpenAPI** (Spectral, Redocly CLI) peut rendre obligatoires les champs 🔴 : description non vide, exemple présent, réponses d'erreur déclarées, etc. La qualité est alors vérifiée à chaque PR.

---

## 5. Les erreurs fréquentes

| ❌ À éviter | ✅ À faire |
|---|---|
| Description qui répète le nom : « `status` : le statut » | Expliquer le sens et chaque valeur possible |
| Exemples `"string"`, `0`, `"example"` générés automatiquement | Valeurs réalistes et cohérentes entre requête et réponse |
| Seule la réponse `200` est documentée | Toutes les réponses possibles, erreurs comprises |
| Unités implicites (montant en euros ou en centimes ?) | Unité et format explicites |
| Jargon interne (noms de tables, de systèmes back-office) | Vocabulaire métier du client |
| Règles métier connues seulement de l'équipe | Règles écrites dans la `description` |
| Exemple de code jamais testé | Exemples exécutés en CI contre la sandbox |
| Même objet décrit différemment sur plusieurs endpoints | Objet unique référencé (`$ref`) |
| Endpoint déprécié sans date ni remplaçant | Date de retrait + endpoint de remplacement + guide de migration |

---

## 6. Checklist de revue d'un endpoint

À utiliser en revue de PR ou avant de publier un endpoint.

**Identification & accès**
- [ ] Titre orienté action et résumé en une phrase
- [ ] Description : ce que fait l'appel et quand l'utiliser
- [ ] Statut indiqué (stable / bêta / déprécié + date + remplaçant)
- [ ] Authentification et scopes requis

**Requête**
- [ ] Chaque paramètre : type, format, obligatoire, description métier, contraintes, valeur par défaut
- [ ] Chaque enum : signification de chaque valeur
- [ ] Unités explicites (montants, durées)
- [ ] Dépendances entre paramètres décrites
- [ ] Exemple `curl` complet qui fonctionne en sandbox

**Réponse**
- [ ] Code(s) de succès
- [ ] Schéma champ par champ, champs optionnels / nullables identifiés
- [ ] Exemple réaliste, cohérent avec la requête

**Erreurs**
- [ ] Codes HTTP possibles listés
- [ ] Code métier, cause et solution pour chaque erreur spécifique
- [ ] Erreurs réessayables identifiées

**Comportement**
- [ ] Idempotence (écritures)
- [ ] Pagination, tri et filtres (listes)
- [ ] Synchrone ou asynchrone
- [ ] Règles métier et transitions d'état
- [ ] Webhooks déclenchés
