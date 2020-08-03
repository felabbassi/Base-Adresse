## Objectif

Cette API a pour vocation de permettre la gestion de bases d'adresses à l'échelon local, habituellement au niveau commune ou intercommunalité.

## Fonctionnement général

Tout d'abord l'utilisateur créé une base locale, puis il y ajoute des communes qu'il peut pré-remplir.

La base locale peut aussi être importée (format BAL/AITF 1.1).

Ensuite il peut gérer les voies (et toponymes), les numéros et les positions associées.

- Une base locale peut avoir plusieurs communes
- Une commune a plusieurs voies
- Une voie est associée à une seule commune.
- Une voie a plusieurs numéros (sauf les voies-toponymes)
- Un numéro est associé à une seule voie.
- Un numéro peut avoir plusieurs positions.
- Une position est associé à un seul numéro ou à une seule voie (dans le cas d'une voie-toponyme).

Une base locale peut être publiée, ou exportée au format BAL/AITF 1.1.

## Contrôle d'accès

Le créateur d'une base locale devient l'administrateur de cette base. Un jeton de sécurité lui est alors fourni.

Ce jeton est lié à la base locale, et peut-être renouvelé sur demande de tout utilisateur disposant du jeton.

Si le jeton est perdu, un nouveau peut-être envoyé par email sous réserve de communiquer au moins une adresse email connue.

Le jeton doit être passé à l'API via l'en-tête HTTP `Authorization`.
Exemple : `Authorization: Token f66gdjfehfv66DBD`

## Points d'accès

### `/bases-locales`

- `POST` : Créer une base locale
- `GET` : Lister les bases locales ou rechercher parmi les bases locales

### `/bases-locales/{id}`

- `GET` : Récupérer les information de la base locale
- `PUT` : Modifier la base locale
- `DELETE` : Supprimer la base locale

### `/bases-locales/{id}/token/renew`

- `POST` : Renouveler le jeton de sécurité

### `/bases-locales/{id}/token/reinit`

- `POST` : Envoyer la procédure de réinitialisation du jeton par email

### `/bases-locales/{id}/upload`

- `POST` : Créer une base locale depuis un fichier importé

### `/bases-locales/{id}/csv`

- `GET` : Récupérer la base locale au format CSV


### `/bases-locales/{id}/communes/{codeCommune}`

- `GET` : Retourne (et calcule) les informations pertinentes à l'échelle d'une commune
- `PUT` : Ajouter une commune à la base locale
- `DELETE` : Supprimer une commune de la base locale

### `/bases-locales/{id}/communes/{codeCommune}/populate`

- `POST` : Préremplir la commune avec des adresses existantes

### `/bases-locales/{id}/communes/{codeCommune}/geojson`

- `GET` : Retourne toutes les adresses de la commune au format GeoJSON

### `/bases-locales/{id}/communes/{codeCommune}/voies`

- `POST` : Créer une voie dans la commune
- `GET` : Lister les voies d'une commune

### `/voies/{id}`

- `GET` : Retourner les informations concernant une voie
- `PUT` : Modifier une voie
- `DELETE` : Supprimer une voie

### `/voies/{id}/numeros`

- `POST` : Créer un numéro dans la voie
- `GET` : Récupérer la liste des numéros d'une voie

### `/numeros/{id}`

- `GET` : Retourner les informations d'un numéro de voie
- `PUT` : Modifier un numéro de voie
- `DELETE` : Supprimer un numéro de voie

## Modèles

### Champs communs

| Attributs | Éditable | Commentaire |
| --------- | -------- | ----------- |
| _id      | ❌ | Identifiant unique de l'objet |
| _created      | ❌ | Date de création de l'objet |
| _updated | ❌ | Date de mise à jour de l'objet |

### Base locale

| Attributs | Éditable | Commentaire |
| --------- | -------- | ----------- |
| nom      | ✅ | _Optionnel_ |
| description | ✅ | _Optionnel_ |
| emails    | ✅ | Tableau devant toujours contenir au moins une valeur. |
| token     | ❌ | Généré par l'application. Renouvelable via URL dédiée. |
| communes  | ⚠️ | Éditable via routes spécifiques |
| enableComplement | ✅ | Active la saisie du complément de nom de voie dans l'éditeur

### Voie

| Attributs | Éditable | Commentaire
| --------- | -------- | -----------
| nom       | ✅       |
| complement | ✅      | Complément de nom de voie (nom du lieu-dit, commune déléguée…). Champ optionnel.
| code      | ❌       | Code FANTOIR ou séquence libre (= `null` pour le moment)
| commune   | ❌       | Référence
| numeros   | ⚠️       | Éditable via routes spécifiques
| positions | ✅       | Tableau avec au maximum une position pour le moment
| _bal | ❌ | Référence |

### Numéro

| Attributs | Éditable | Commentaire
| --------- | -------- | -----------
| commune   | ❌       | Référence
| voie      | ✅       | Référence (modification = déplacer le numéro dans une autre voie de la commune)
| numero    | ✅       |`9` (Integer)
| suffixe   | ✅       |`bis`
| numeroComplet | ❌ | `9` + `bis` => `9bis`
| positions | ✅       |
| _bal | ❌ | Référence |

### Position

| Attributs   | Éditable | Commentaire
| ---------   | -------- | -----------
| point | ✅       | Point GeoJSON
| source      | ✅       | Texte libre. Optionnel
| type        | ✅       | `entrée`, `délivrance postale`, `bâtiment`, `cage d'escalier`, `logement`, `parcelle`, `segment`, `'service technique`

## Publication d'une base locale

Cette fonctionnalité est hors périmètre pour le moment.
Cette API se limite à proposer les fonctionnalités nécessaire à la gestion d'une base locale.

Si la création et la gestion d'une base peuvent être réalisés par tous (dans le respect des règles de contrôle d'accès), la publication de la base sur adresse.data.gouv.fr doit être maîtrisée.

Plusieurs pistes de réflexion existent :
- FranceConnect Élu
- Authentification data.gouv.fr
- Adresse email connue de la mairie (données DILA)