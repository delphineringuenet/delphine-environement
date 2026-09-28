# Quickstart — <Nom de l'API>

> **Objectif** : réaliser votre premier appel à l'API <Nom> en moins de 10 minutes.
> **Durée estimée** : 10 min · **Niveau** : débutant

## Ce que vous allez faire

À la fin de ce guide, vous aurez :
- obtenu une clé d'API de test ;
- effectué un premier appel `<MÉTHODE> /<ressource>` ;
- compris la réponse retournée.

## Prérequis

- Un compte sur le portail développeur ([créer un compte](<lien>)).
- Un outil pour envoyer des requêtes HTTP : `curl`, Postman, ou l'un de nos SDK.

## Étape 1 — Obtenir une clé d'API de test

1. Connectez-vous au portail.
2. Allez dans **Mes applications › Créer une application**.
3. Copiez la clé de l'environnement **Sandbox**.

> ⚠️ Ne partagez jamais votre clé. Les clés de sandbox commencent par `test_`.

## Étape 2 — Effectuer votre premier appel

```bash
curl https://sandbox.api.<domaine>.com/v1/<ressource> \
  -H "Authorization: Bearer <VOTRE_CLE_API>"
```

<!-- Onglets : curl | JavaScript | Python | Java -->

## Étape 3 — Lire la réponse

```json
{
  "id": "res_123",
  "status": "active",
  "created_at": "2026-01-01T10:00:00Z"
}
```

| Champ | Signification |
|---|---|
| `id` | Identifiant unique de la ressource |
| `status` | Statut courant |

## En cas de problème

| Symptôme | Cause probable | Solution |
|---|---|---|
| `401 Unauthorized` | Clé absente ou invalide | Vérifiez l'en-tête `Authorization` |
| `404 Not Found` | Mauvaise URL ou environnement | Vérifiez l'URL de base sandbox |

## Étapes suivantes

- [Comprendre l'authentification](authentification.md)
- [Guide : <cas d'usage principal>](guide-pratique.md)
- [Référence complète de l'API](reference-endpoint.md)
