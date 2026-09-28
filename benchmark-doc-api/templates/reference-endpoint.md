# Créer un client

> Template de page endpoint. Structure et justification de chaque bloc : [`../bonne-doc-endpoint.md`](../bonne-doc-endpoint.md).
> Idéalement généré depuis la spécification OpenAPI : les rédacteurs enrichissent `description` et `examples` dans la spec plutôt que cette page.

`Stable` · Disponible depuis la v1.0

```
POST /v1/customers
```

| Environnement | URL de base |
|---|---|
| Sandbox | `https://sandbox.api.<domaine>.com` |
| Production | `https://api.<domaine>.com` |

## Description

Crée un client à qui vous pourrez ensuite rattacher des moyens de paiement et des factures.

Utilisez cet endpoint lors de l'inscription d'un nouvel utilisateur dans votre application. Pour modifier un client existant, utilisez [Mettre à jour un client](#).

> Voir aussi le concept : [Cycle de vie d'un client](#).

## Permissions

| Authentification | Scope requis |
|---|---|
| OAuth 2.0 (Bearer) — voir [Authentification](authentification.md) | `customers:write` |

Limite de débit : 100 requêtes / minute (règle générale, voir [Erreurs › Limites](erreurs.md#limites-de-débit-rate-limiting)).

## Paramètres

### En-têtes

| Nom | Obligatoire | Description |
|---|---|---|
| `Authorization` | Oui | `Bearer <ACCESS_TOKEN>` |
| `Content-Type` | Oui | `application/json` |
| `Idempotency-Key` | Recommandé | Identifiant unique (UUID v4) permettant de rejouer la requête sans créer de doublon. Conservé 24 h. |

### Corps de la requête

| Nom | Type | Obligatoire | Description | Contraintes | Exemple |
|---|---|---|---|---|---|
| `email` | string (email) | Oui | E-mail utilisé pour l'envoi des factures. Doit être unique sur votre compte. | 254 caractères max | `jeanne@example.com` |
| `name` | string | Non | Nom affiché sur les factures. | 1 à 100 caractères | `Jeanne Martin` |
| `type` | enum | Non (défaut : `individual`) | `individual` : particulier · `company` : entreprise (active les champs `company.*`) | — | `company` |
| `company` | object | Si `type=company` | Informations légales de l'entreprise. | — | — |
| ↳ `company.siren` | string | Oui (si `company` est présent) | Numéro SIREN. | 9 chiffres | `552100554` |
| `preferred_locale` | string | Non (défaut : `fr-FR`) | Langue des e-mails envoyés au client (BCP 47). | `fr-FR`, `en-GB` | `fr-FR` |
| `metadata` | object | Non | Paires clé/valeur libres pour stocker vos propres identifiants. Non visible par le client. | 50 clés max, valeurs de 500 caractères max | `{ "crm_id": "A-42" }` |

## Exemples de requête

**Particulier (minimal)**

```bash
curl -X POST https://sandbox.api.<domaine>.com/v1/customers \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -H "Idempotency-Key: 3f1c7e0a-8b1d-4c52-9a1e-2d7f6b0c9e11" \
  -d '{ "email": "jeanne@example.com" }'
```

**Entreprise**

```bash
curl -X POST https://sandbox.api.<domaine>.com/v1/customers \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "compta@acme.fr",
    "name": "ACME SAS",
    "type": "company",
    "company": { "siren": "552100554" }
  }'
```

<!-- Onglets : curl | JavaScript | Python | Java | PHP -->

## Réponse

**`201 Created`** — retourne un [objet Client](#objet-client).

En-têtes de réponse :

| En-tête | Description |
|---|---|
| `Location` | URL de la ressource créée |
| `X-Request-Id` | Identifiant de la requête, à communiquer au support |

```json
{
  "id": "cus_8f2k3",
  "email": "jeanne@example.com",
  "name": null,
  "type": "individual",
  "company": null,
  "status": "active",
  "preferred_locale": "fr-FR",
  "metadata": {},
  "created_at": "2026-01-01T10:00:00Z"
}
```

### Objet Client

| Champ | Type | Toujours présent | Description |
|---|---|---|---|
| `id` | string | Oui | Identifiant unique, préfixé `cus_` |
| `email` | string | Oui | E-mail du client |
| `name` | string \| null | Oui (peut valoir `null`) | Nom affiché |
| `type` | enum | Oui | `individual` · `company` |
| `company` | object \| null | Oui (`null` si `type=individual`) | Informations légales |
| `status` | enum | Oui | `active` : peut être facturé · `suspended` : facturation bloquée temporairement · `closed` : clôturé, définitif |
| `preferred_locale` | string | Oui | Langue des communications |
| `metadata` | object | Oui (peut être vide) | Vos données libres |
| `created_at` | string (date-time ISO 8601, UTC) | Oui | Date de création |

## Erreurs

| HTTP | Code métier | Cause | Solution | Réessayable |
|---|---|---|---|---|
| 400 | `validation_error` | Paramètre manquant ou invalide | Voir le détail dans `errors[]` | Non |
| 400 | `company_required` | `type=company` sans objet `company` | Ajouter `company.siren` | Non |
| 401 | `invalid_token` | Jeton absent ou expiré | Regénérer un jeton | Non |
| 403 | `insufficient_scope` | Scope `customers:write` manquant | Ajouter le scope à l'application | Non |
| 409 | `email_already_exists` | Un client utilise déjà cet e-mail | Récupérer le client existant via `GET /v1/customers?email=` | Non |
| 429 | `rate_limit_exceeded` | Limite de débit atteinte | Attendre la durée indiquée par `Retry-After` | Oui |
| 5xx | `internal_error` | Erreur temporaire | Réessayer avec backoff exponentiel **et la même** `Idempotency-Key` | Oui |

```json
{
  "type": "https://developers.<domaine>.com/erreurs/email_already_exists",
  "title": "E-mail déjà utilisé",
  "status": 409,
  "code": "email_already_exists",
  "detail": "Un client avec l'e-mail 'jeanne@example.com' existe déjà (cus_8f2k3).",
  "request_id": "req_7Hk29"
}
```

Erreurs communes à tous les endpoints : voir [Erreurs](erreurs.md).

## Comportement

- **Idempotence** : avec `Idempotency-Key`, une requête rejouée dans les 24 h retourne la réponse initiale sans créer de doublon.
- **Traitement** : synchrone ; le client est immédiatement utilisable.
- **Règles métier** : l'e-mail est unique par compte (insensible à la casse). Un client `closed` ne peut pas être réactivé.

## Effets de bord

| Événement webhook | Quand |
|---|---|
| `customer.created` | Après la création réussie |

## Voir aussi

- [Récupérer un client](#) · [Lister les clients](#) · [Mettre à jour un client](#)
- Guide : [Onboarder un nouveau client](guide-pratique.md)
