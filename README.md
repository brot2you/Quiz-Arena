# QUIZ ARENA V3.1 ✦ by BROT2YOU

Quiz multijoueur en direct pour la classe, sur le modèle de Kahoot : le prof projette, les élèves jouent sur téléphone, tablette ou ordinateur.
L'appli tient en un seul fichier `index.html`, en JavaScript ES5 compatible avec les anciens iPhone. Elle se publie sur GitHub Pages et est gratuite.

## Fichiers
| Fichier | Rôle |
|---|---|
| `index.html` | toute l'appli (interface, jeu, éditeur, rapports, QR code) |
| `firebase-config.js` | la configuration de ton projet Firebase (vide = mode démo) |
| `database.rules.json` | les règles de sécurité à coller dans Firebase |
| `.nojekyll` | à garder pour GitHub Pages |

## Essayer tout de suite (mode démo, sans rien installer)
Si `firebase-config.js` est vide, l'appli démarre en **mode démo local**. Ouvre la page dans plusieurs onglets du même navigateur : un onglet sert d'écran prof, les autres d'élèves. Ça suffit pour préparer et tester un quiz. Pour que les téléphones des élèves se connectent, il faut Firebase.

## Firebase hybride — sécurité du projet BROT2YOU et projets personnels

QUIZ ARENA dispose désormais de trois modes :

- **BROT2YOU (privé)** : utilise `quiz-arena-brot2you`. La protection réelle dépend des règles publiées dans ce projet.
- **Mon propre Firebase** : un autre enseignant utilise son projet Firebase indépendant ; ses élèves reçoivent sa configuration Web publique dans le QR code / lien.
- **Démo locale** : ajouter `?demo=1` à l'adresse pour jouer dans plusieurs onglets du même navigateur, sans serveur.

### Protéger le Firebase BROT2YOU — action indispensable

1. Dans **Firebase Console**, choisir le projet **quiz-arena-brot2you** → **Authentication** → **Utilisateurs**. Repérer l'**UID du compte Google utilisé comme professeur** sur **ce projet précis**. Cet UID peut être différent de celui de BUZZ ARENA.
2. Ouvrir **[database.rules-private-owner.json](database.rules-private-owner.json)** et remplacer `REPLACE_WITH_QUIZ_ARENA_OWNER_UID` par cet UID (en conservant les apostrophes).
3. Copier **tout le fichier adapté** dans **Realtime Database → Règles** du projet **quiz-arena-brot2you**, puis cliquer sur **Publier**.
4. Dans **Authentication → Méthode de connexion**, activer **Google** pour le professeur et **Anonyme** pour les élèves ; autoriser `brot2you.github.io` dans **Domaines autorisés**.
5. Tester une partie avec le compte Google du propriétaire et un smartphone élève. Un autre compte Google ne doit pas pouvoir créer de partie sur ce Firebase.

⚠️ **Ne pas publier le fichier privé avec l'UID provisoire** : cela bloquerait aussi le propriétaire. Tant que ces règles privées ne sont pas publiées, d'autres utilisateurs peuvent encore consommer tes quotas selon les règles Firebase en service. La séparation visuelle des modes n'est pas une sécurité.

### Configuration d'un Firebase personnel (enseignants extérieurs)

1. Créer un **nouveau projet Firebase** à soi (pas `quiz-arena-brot2you`).
2. Activer **Authentication → Google + Anonyme** ; ajouter `brot2you.github.io` aux domaines autorisés.
3. Créer **Realtime Database** et y publier le contenu complet de **[database.rules.json](database.rules.json)**, prévu pour qu'un professeur Google crée ses propres parties.
4. Récupérer les valeurs de **Paramètres du projet → Application Web → firebaseConfig** : `apiKey`, `authDomain`, `databaseURL`, `projectId`, `appId`. Il s'agit d'une **configuration Web publique**, pas d'une clé privée.
5. Dans QUIZ ARENA, cliquer **⚙️ Choisir mon Firebase** → **Mon propre Firebase**. Coller la configuration et **Enregistrer et recharger**.
6. Lancer un quiz depuis sa bibliothèque. La connexion Google professeur est demandée lors de l'ouverture de la salle. Les élèves scannent **le QR code de cette partie** ou ouvrent le lien correspondant.

Le code PIN **seul** ne suffit pas à identifier un Firebase personnel inconnu depuis un autre appareil : utiliser le QR code ou lien fourni. Les quiz et rapports continuent d'être conservés localement dans le navigateur, pas dans le Firebase BROT2YOU.

**Attention à l'authentification :** les anciennes parties créées sous une identité Firebase anonyme peuvent ne plus être récupérables comme professeur après activation de la connexion Google. Terminer les parties existantes avant de changer les règles et tester une nouvelle session.

---

## Installer Firebase (environ 10 minutes, gratuit)
1. Va sur https://console.firebase.google.com, clique sur **Créer un projet** (Google Analytics n'est pas nécessaire).
2. **Créer › Authentication › Commencer › Mode de connexion** : active **Anonyme**.
3. **Créer › Realtime Database › Créer une base de données** : choisis l'emplacement **europe-west1**, puis démarre en **mode verrouillé**.
4. Dans l'onglet **Règles** de la base, remplace tout le contenu par celui de `database.rules.json`, puis clique sur **Publier**.
5. **Paramètres du projet (⚙️) › Vos applications › Web (</>)** : enregistre une appli, puis copie les valeurs `apiKey`, `authDomain`, `databaseURL`, `projectId` et `appId` dans `firebase-config.js`.
   ⚠️ `databaseURL` est indispensable. Si elle n'apparaît pas, recopie l'adresse affichée en haut de la page Realtime Database.
6. **Authentication › Paramètres › Domaines autorisés** : ajoute `brot2you.github.io`.
7. Publie les fichiers à la racine du dépôt (ex. `Quiz-Arena`), puis active **Settings › Pages › Deploy from branch › main / root**.
8. Ouvre l'adresse sur l'ordinateur du prof. La pastille en haut à droite doit afficher **🟢 Multijoueur en ligne**.

Avant ta première vraie séance, fais une partie test avec deux téléphones.

> La clé `apiKey` n'est pas un mot de passe : elle peut être publique. Ce sont les règles de sécurité qui protègent les données.

## Ce que fait l'appli
**Création**
- 7 types de questions : Quiz (une réponse), Multi (plusieurs réponses, points partiels), Vrai/Faux, Réponse libre (tapée, majuscules, accents et ponctuation ignorés, variantes acceptées), Puzzle (remettre dans l'ordre), Curseur (valeur avec marge), Sondage (sans points).
- Images par adresse https ou importées depuis l'appareil (redimensionnées automatiquement).
- Temps de 5 s à 4 min, points standard, doubles ou aucun.
- **Import rapide** : colle une question par ligne (`Question | *bonne | mauvaise | …`), y compris depuis un tableur. Le type est détecté tout seul.
- Brouillon sauvegardé automatiquement et proposé à la reprise. Export et import JSON. Les quiz des V1/V2 sont récupérés automatiquement.

**Partie en direct**
- Choix du mode : Classique ou **Équipes** (un appareil par équipe, avec les prénoms des membres).
- Options : questions affichées sur les appareils, ordre des questions mélangé, ordre des réponses mélangé, **pseudos imposés** (générateur de pseudos amusants en anglais), musique.
- Salle d'attente avec grand PIN, **QR code**, joueurs qui apparaissent en direct, **exclusion** d'un joueur en cliquant sur son pseudo et **verrouillage** de la salle.
- Annonce de la question, puis réponses avec chrono et barre de temps. La question se termine **automatiquement quand tout le monde a répondu**. Bouton « Passer ».
- Révélation : histogramme des réponses, mots tapés, position sur le curseur, bon ordre du puzzle.
- **Barème Kahoot** : jusqu'à 1000 pts selon la rapidité, et **bonus de série** 🔥 jusqu'à +500.
- Sur le téléphone, après chaque question : juste ou faux, points gagnés, série, rang et écart avec le joueur juste devant.
- Classement des 5 premiers après chaque question, puis **podium animé** avec confettis.
- Musique et effets sonores synthétisés (aucun fichier audio, libres de droits). Plein écran. L'écran reste allumé pendant la partie (si le navigateur le permet).
- **Reprise après rechargement**, côté prof comme côté élève.

**Après la partie**
- **Rapports** : réussite par question, questions « à retravailler » (moins de 50 %), résultats par élève et temps moyen.
- **Export CSV détaillé** (réponse, résultat, points et temps pour chaque élève et chaque question), lisible dans Excel ou LibreOffice.

## Sécurité et fiabilité
- Les **bonnes réponses ne sont jamais envoyées aux élèves** avant la correction. Le quiz complet est stocké dans une zone que seul le prof peut lire, et l'ordre du puzzle est masqué par des jetons aléatoires.
- L'heure de chaque réponse vient du **serveur Firebase** : les horloges décalées des téléphones ne faussent plus ni les points ni les délais.
- Un élève ne peut répondre qu'une fois, pendant la question, et seulement s'il est dans la salle. Il ne peut ni modifier son score ni changer de pseudo, et ne peut pas revenir s'il a été exclu.
- Les salles expirent au bout de 6 h. À la fermeture, les réponses et les données de la partie sont **supprimées** du serveur (RGPD). Les rapports restent uniquement sur l'ordinateur du prof.

## En cas de problème
- **« Firebase à configurer » ou 🧪 mode démo** : `firebase-config.js` est vide ou n'a pas été envoyé sur GitHub.
- **« Connexion anonyme refusée »** : active Anonyme (étape 2).
- **« Accès refusé » / PERMISSION_DENIED** : les règles de `database.rules.json` n'ont pas été publiées (étape 4).
- **Un élève ne peut plus répondre** : le temps est écoulé, ou il a été exclu.

## Limites (honnêtement)
- Les scores sont calculés par le navigateur du prof, pas par un serveur. C'est suffisant pour la classe, mais pas pour une évaluation à enjeu.
- Les quiz et les rapports sont stockés **sur l'appareil du prof** (pas de compte) : exporte régulièrement en JSON et en CSV.
- Fonctions de Kahoot absentes : mode devoir à faire seul (il faudrait un serveur payant pour corriger sans exposer les réponses), banque publique de quiz, génération par IA, vidéos dans les questions, nuage de mots, questions ouvertes et « épingle sur image », réactions et emojis, comptes et rapports en ligne.
