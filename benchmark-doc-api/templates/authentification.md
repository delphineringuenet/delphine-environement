# Authentification

> Toutes les requêtes à l'API <Nom> doivent être authentifiées.

## Méthode utilisée

<!-- Choisir et garder la ou les méthodes concernées -->

| Méthode | Cas d'usage |
|---|---|
| **Clé d'API** (`Authorization: Bearer`) | Appels serveur à serveur simples |
| **OAuth 2.0 – Client Credentials** | Applications partenaires, machine à machine |
| **OAuth 2.0 – Authorization Code + PKCE** | Accès au nom d'un utilisateur final |

## Obtenir vos identifiants

1. Créez une application dans **Mes applications**.
2. Récupérez le `client_id` et le `client_secret` (ou la clé d'API).
3. Choisissez les **scopes** nécessaires.

## Exemple : obtenir un jeton (Client Credentials)

```bash
curl -X POST https://auth.<domaine>.com/oauth2/token \
  -d grant_type=client_credentials \
  -d client_id=<CLIENT_ID> \
  -d client_secret=<CLIENT_SECRET> \
  -d scope="<scope1> <scope2>"
```

```json
{
  "access_token": "eyJhbGciOi...",
  "token_type": "Bearer",
  "expires_in": 3600
}
```

## Utiliser le jeton

```bash
curl https://api.<domaine>.com/v1/<ressource> \
  -H "Authorization: Bearer <ACCESS_TOKEN>"
```

## Scopes disponibles

| Scope | Accès accordé |
|---|---|
| `<ressource>:read` | Lecture |
| `<ressource>:write` | Création / modification |

## Environnements

| Environnement | URL de base | Données |
|---|---|---|
| Sandbox | `https://sandbox.api.<domaine>.com` | Fictives |
| Production | `https://api.<domaine>.com` | Réelles |

## Bonnes pratiques de sécurité

- Stockez les secrets dans un coffre (vault), jamais dans le code source.
- Renouvelez le jeton avant expiration (`expires_in`).
- Demandez uniquement les scopes nécessaires.
- Révoquez immédiatement une clé compromise depuis le portail.

## Erreurs d'authentification

| HTTP | Code | Cause | Solution |
|---|---|---|---|
| 401 | `invalid_token` | Jeton expiré ou invalide | Regénérez un jeton |
| 403 | `insufficient_scope` | Scope manquant | Ajoutez le scope à l'application |
