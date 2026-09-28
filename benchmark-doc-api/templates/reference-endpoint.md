# <Ressource> (ex. Clients)

> Page de référence **générée depuis la spécification OpenAPI**. Ce template décrit le rendu attendu ; les rédacteurs enrichissent les champs `description` et `example` de la spec plutôt que cette page.
>
> Mise en page cible : **2 colonnes** — explications à gauche, requête / réponse à droite.

## Présentation

<Une à trois phrases : à quoi sert la ressource, lien vers le concept associé.>

## L'objet <Ressource>

| Attribut | Type | Description |
|---|---|---|
| `id` | string | Identifiant unique, préfixé `cus_` |
| `email` | string | Adresse e-mail du client |
| `status` | enum | `active` · `suspended` · `closed` |
| `created_at` | string (date-time, ISO 8601) | Date de création |
| `metadata` | object | Paires clé/valeur libres |

```json
{
  "id": "cus_8f2k3",
  "email": "jeanne@example.com",
  "status": "active",
  "created_at": "2026-01-01T10:00:00Z",
  "metadata": {}
}
```

---

## Créer un <ressource>

`POST /v1/<ressources>`

<Description fonctionnelle : ce que fait l'appel, effets de bord, événements webhook émis.>

**Scopes requis** : `<ressource>:write` · **Idempotence** : en-tête `Idempotency-Key` supporté

### Paramètres

**En-têtes**

| Nom | Requis | Description |
|---|---|---|
| `Authorization` | Oui | `Bearer <token>` |
| `Idempotency-Key` | Non | Clé unique pour rejouer la requête sans doublon |

**Corps (JSON)**

| Nom | Type | Requis | Description | Contraintes |
|---|---|---|---|---|
| `email` | string | Oui | E-mail du client | Format e-mail, 254 car. max |
| `metadata` | object | Non | Données libres | 50 clés max |

### Exemple de requête

```bash
curl -X POST https://api.<domaine>.com/v1/<ressources> \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{ "email": "jeanne@example.com" }'
```

<!-- Onglets : curl | JavaScript | Python | Java | PHP -->

### Réponses

| HTTP | Description |
|---|---|
| `201 Created` | Ressource créée — retourne l'objet |
| `400 Bad Request` | Paramètre invalide (`validation_error`) |
| `401 Unauthorized` | Authentification manquante |
| `409 Conflict` | Ressource déjà existante |

```json
{
  "id": "cus_8f2k3",
  "email": "jeanne@example.com",
  "status": "active",
  "created_at": "2026-01-01T10:00:00Z",
  "metadata": {}
}
```

---

## Lister les <ressources>

`GET /v1/<ressources>`

### Paramètres de requête

| Nom | Type | Défaut | Description |
|---|---|---|---|
| `limit` | integer | 20 | Nombre d'éléments (1–100) |
| `cursor` | string | — | Curseur de pagination retourné par l'appel précédent |
| `status` | enum | — | Filtre sur le statut |

### Réponse

```json
{
  "data": [ { "id": "cus_8f2k3", "...": "..." } ],
  "has_more": true,
  "next_cursor": "eyJpZCI6..."
}
```

<!-- Répéter le bloc pour : Récupérer, Mettre à jour, Supprimer -->

---

## Événements webhook associés

| Événement | Déclencheur |
|---|---|
| `<ressource>.created` | Création |
| `<ressource>.updated` | Modification |

## Voir aussi

- [Guide : <cas d'usage>](guide-pratique.md)
- [Erreurs](erreurs.md)
