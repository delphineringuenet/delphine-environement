# Changelog & versioning

## Politique de versioning

- Version majeure dans l'URL : `/v1`, `/v2`.
- Les changements **non cassants** (ajout d'un champ, d'un endpoint, d'une valeur d'enum optionnelle) sont déployés sans changement de version.
- Les changements **cassants** entraînent une nouvelle version majeure.
- Une version dépréciée reste disponible **<x> mois** après l'annonce.
- Les endpoints dépréciés renvoient les en-têtes `Deprecation` et `Sunset`.

> Abonnez-vous aux changements : <flux RSS / newsletter / webhook>.

## Légende

| Badge | Signification |
|---|---|
| 🆕 **Ajout** | Nouvelle fonctionnalité |
| 🔧 **Modification** | Changement non cassant |
| ⚠️ **Dépréciation** | Retrait programmé |
| 💥 **Cassant** | Action requise de votre part |
| 🐛 **Correctif** | Correction d'anomalie |

---

## AAAA-MM-JJ — v1.4.0

- 🆕 **Ajout** — Endpoint `GET /v1/<ressources>/{id}/history`. [Référence](reference-endpoint.md)
- 🔧 **Modification** — Le champ `metadata` accepte désormais 50 clés (au lieu de 20).
- ⚠️ **Dépréciation** — `GET /v1/<ancien-endpoint>` sera retiré le AAAA-MM-JJ. Utilisez `<nouvel endpoint>`. [Guide de migration](<lien>)

## AAAA-MM-JJ — v1.3.2

- 🐛 **Correctif** — La pagination retournait un `next_cursor` vide sur la dernière page.
