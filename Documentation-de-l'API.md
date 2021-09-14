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
- Une parcelle est associée à une ou plusieurs adresses. 

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

### `/bases-locales/create-demo`

- `POST` : Créer une base locale de démonstration

### `/bases-locales/search`

- `GET` : Retourner les bases locales associées à une adresse électronique et un code commune.

### `/base-locale/recovery`

- `POST` : Envoi par courriel les bases locales ainsi que leurs jetons d'administration associées à l’adresse électronique indiquée

### `/bases-locales/{id}`

- `GET` : Retourne les informations de la base locale
- `PUT` : Modifier la base locale
- `DELETE` : Supprimer la base locale

### `/bases-locales/{id}/transform-to-draft`

- `POST` : Transforme une base locale de démonstration en brouillon, dans le cas où l’utilisateur souhaiterait conserver une base-adresse créée en mode démonstration

### `/bases-locales/{id}/token/renew`

- `POST` : Réinitialisation du jeton de la base locale et envoi du nouveau jeton par courriel

### `/bases-locales/{id}/upload`

- `POST` : Créer une base locale depuis un fichier importé

### `/bases-locales/{id}/csv`

- `GET` : Récupérer la base locale au format CSV

### `/bases-locales/{id}/communes/{codeCommune}`

- `GET` : Retourne (et calcule) les informations pertinentes à l'échelle d'une commune
- `PUT` : Ajouter une commune à la base locale
- `DELETE` : Supprimer une commune de la base locale

### `/bases-locales/{id}/communes/{codeCommune}/batch`

- `POST` : Modifier les numéros d’une commune par lot

### `/bases-locales/{id}/communes/{codeCommune}/populate`

- `POST` : Préremplir la commune avec des adresses existantes

### `/bases-locales/{id}/communes/{codeCommune}/geojson`

- `GET` : Retourne toutes les adresses de la commune au format GeoJSON

### `/bases-locales/{id}/communes/{codeCommune}/voies`

- `POST` : Créer une voie dans la commune
- `GET` : Lister les voies d'une commune

### `/bases-locale/{id}/communes/{id}/toponymes`

- `POST` : Ajouter un toponyme
- `GET` : Retourne les toponymes d’une base locale

### `/bases-locales/{id}/communes/{id}/parcelles`

- `GET` : Retourne les parcelles d’une commune

### `/bases-locale/{id}/numeros/batch`

- `POST` : Modifier les numéros par lot

### `/voies/{id}`

- `GET` : Retourner les informations concernant une voie
- `PUT` : Modifier une voie
- `DELETE` : Supprimer une voie

### `/voies/{id}/numeros`

- `POST` : Créer un numéro dans la voie
- `GET` : Récupérer les numéros d'une voie

### `/voies/{id}/batch`

- `POST` : Modifier les numéros d'une voie par lot

### `/numeros/{id}`

- `GET` : Retourner les informations d'un numéro de voie
- `PUT` : Modifier un numéro de voie
- `DELETE` : Supprimer un numéro de voie

### `/toponymes/{id}`

- `GET` : Retourne les informations d’un toponyme
- `POST` : Ajouter un toponyme
- `DELETE` : Supprimer un toponyme

### `/toponymes/{id}/numeros`

- `GET` : Retourner les numéros d’un toponyme

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
| nom      | ✅ | Nom de la Base Adresse Locale |
| emails    | ✅ | Tableau devant toujours contenir au moins une valeur |
| token     | ❌ | Généré par l'application. Renouvelable via URL dédiée. (protégé) |
| communes  | ⚠️ | Éditable via routes spécifiques |
| enableComplement | ✅ | Active la saisie du complément de nom de voie dans l'éditeur |

### Voie

| Attributs | Éditable | Commentaire
| --------- | -------- | -----------
| nom       | ✅       | Nom de la voie |
| complement | ✅      | Complément de nom de voie (nom du lieu-dit, commune déléguée…) (optionnel) |
| code      | ❌       | Code FANTOIR ou séquence libre (= `null` pour le moment) |
| commune   | ❌       | Référence |
| numeros   | ⚠️       | Éditable via routes spécifiques |
| typeNumerotation | ✅       | `numerique` ou `metrique` |
| trace | ✅       | Line GeoJSON. Champ optionnel |
| _bal | ❌ | Référence |

### Toponyme

| Attributs   | Éditable | Commentaire
| ---------   | -------- | -----------
| nom | ✅ | Nom du toponyme |
| positions | ✅ | Tableaux des positions du toponyme |
| parcelles | ✅ | Tableaux des parcelles du toponyme |
| commune   | ❌       | Référence |
| _bal | ❌ | Référence |

### Numéro

| Attributs | Éditable | Commentaire
| --------- | -------- | -----------
| commune   | ❌       | Référence |
| voie      | ✅       | Référence (modification = déplacer le numéro dans une autre voie de la commune) |
| numero    | ✅       |`9` (Integer) |
| suffixe   | ✅       |`bis` |
| numeroComplet | ❌ | `9` + `bis` => `9bis` |
| comment | ✅ | Complément pour l’édition des adresse (optionnel et protégé) |
| parcelles | ✅ | Tableau des parcelles du numéro |
| positions | ✅       | Tableaux des positions |
| certifie | ✅ | Booléen qui précise si l’adresse est certifié par la commune ([Lien vers la documentation](https://guide.mes-adresses.data.gouv.fr/publier-une-base-adresse-locale-1/certifier-ses-adresses)) |
| _bal | ❌ | Référence |

### Position

| Attributs   | Éditable | Commentaire
| ---------   | -------- | -----------
| point | ✅       | Point GeoJSON |
| source      | ✅       | Texte libre (optionnel) |
| type        | ✅       | `entrée`, `délivrance postale`, `bâtiment`, `cage d'escalier`, `logement`, `parcelle`, `segment`, `'service technique` |

## Publication d'une base locale

Cette fonctionnalité est hors périmètre pour le moment.
Cette API se limite à proposer les fonctionnalités nécessaire à la gestion d'une base locale.

Si la création et la gestion d'une base peuvent être réalisés par tous (dans le respect des règles de contrôle d'accès), la publication de la base sur adresse.data.gouv.fr doit être maîtrisée.

Plusieurs pistes de réflexion existent :
- FranceConnect Élu
- Authentification data.gouv.fr
- Adresse email connue de la mairie (données DILA)
