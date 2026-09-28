# Erreurs

> L'API <Nom> utilise les codes HTTP standards et retourne un corps d'erreur au format uniforme (compatible [RFC 9457 – Problem Details](https://www.rfc-editor.org/rfc/rfc9457)).

## Format d'une erreur

```json
{
  "type": "https://developers.<domaine>.com/erreurs/validation_error",
  "title": "Paramètre invalide",
  "status": 400,
  "code": "validation_error",
  "detail": "Le champ 'email' doit être une adresse e-mail valide.",
  "instance": "/v1/customers",
  "request_id": "req_7Hk29",
  "errors": [
    { "field": "email", "message": "Format invalide" }
  ]
}
```

| Champ | Description |
|---|---|
| `code` | Code métier stable, à utiliser dans votre logique applicative |
| `detail` | Message lisible, susceptible d'évoluer — ne pas parser |
| `request_id` | À communiquer au support |

## Codes HTTP

| HTTP | Signification | Réessayer ? |
|---|---|---|
| 400 | Requête invalide | Non — corriger la requête |
| 401 | Non authentifié | Non — vérifier les identifiants |
| 403 | Accès refusé | Non — vérifier les scopes |
| 404 | Ressource introuvable | Non |
| 409 | Conflit | Selon le cas |
| 422 | Règle métier non respectée | Non |
| 429 | Trop de requêtes | Oui — après `Retry-After` |
| 500 / 502 / 503 | Erreur serveur | Oui — avec backoff exponentiel |

## Catalogue des codes métier

| Code | HTTP | Cause | Comment corriger |
|---|---|---|---|
| `validation_error` | 400 | Un ou plusieurs paramètres invalides | Voir le tableau `errors` |
| `invalid_token` | 401 | Jeton expiré ou invalide | Regénérer un jeton |
| `insufficient_scope` | 403 | Scope manquant | Ajouter le scope à l'application |
| `resource_not_found` | 404 | Identifiant inconnu | Vérifier l'identifiant et l'environnement |
| `rate_limit_exceeded` | 429 | Quota dépassé | Respecter `Retry-After` |

## Limites de débit (rate limiting)

| En-tête | Description |
|---|---|
| `X-RateLimit-Limit` | Nombre de requêtes autorisées sur la fenêtre |
| `X-RateLimit-Remaining` | Requêtes restantes |
| `Retry-After` | Secondes à attendre avant de réessayer |
