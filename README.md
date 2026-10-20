# L’Orchidbleue — Sondage du temps des Fêtes 2026–2027

**Correctif V4 — pour le dépôt `orchidbleue/sondage_fetes`.**

Site statique en français pour GitHub Pages. Présentation hivernale aux couleurs du logo officiel, animations GSAP (respecte les préférences de mouvement réduit), calendrier interactif et formulaire **nominatif**.

## CORRIGER L’ERREUR « réponse non confirmée » (votre cas)

Le formulaire GitHub Pages reçoit bien un message `ok: false` du Google Apps Script : cela signifie que le serveur a rencontré une erreur. L’ancienne version du fichier `backend/Code.gs` présente dans le dépôt GitHub pouvait refuser un ancien format de feuille. Ce correctif :

- conserve l’ancien onglet `Réponses` sous forme d’archive en cas d’incompatibilité ;
- recrée les six colonnes de dates offertes et les colonnes de noms ;
- mémorise l’identifiant du tableur pendant l’initialisation, même si aucun tableur n’est « actif » à l’exécution Web ;
- ajoute `diagnostiquerSondage` (sans afficher les noms) ;
- donne un message d’erreur plus précis côté site (`app.js`).

**À faire dans cet ordre :**

1. Ouvrir **le Google Sheets actuel** → **Extensions → Apps Script**.
2. Remplacer **tout** `Code.gs` par **`backend/Code.gs` de ce ZIP**, puis enregistrer.
3. Choisir `initialiserSondage` → **Exécuter** → autoriser si nécessaire. Vous devez voir les onglets `Réponses` et `Bilan`. En cas de données de l’ancienne version, elles sont archivées et ne sont pas comptées dans le nouveau bilan.
4. Choisir `diagnostiquerSondage` → **Exécuter** → vérifier dans **Journal d’exécution** que la configuration est correcte (« Connexion OK… »). Si cela échoue, il faut résoudre l’erreur affichée avant de publier.
5. **Déployer → Gérer les déploiements → Modifier (crayon) → Version : Nouvelle version → Déployer.** Il faut modifier le **déploiement Web existant** pour conserver la même URL `/exec`, pas créer un autre déploiement.
6. Sur GitHub, remplacer `app.js` par celui du ZIP. Mettre aussi à jour `backend/Code.gs` dans le dépôt **pour archivage** ; toutefois, GitHub ne remplace **pas** le code directement dans Apps Script. Les autres fichiers du design n’ont pas besoin de changer.
7. Faire un test avec de **faux noms**, une date offerte, puis contrôler `Réponses` (les noms en colonnes C et D) et `Bilan`. Si erreur, consulter **Apps Script → Exécutions → dernière exécution `doPost` → Journaux** pour le détail. Ne partagez pas les vrais noms dans une capture publique.

**`config.js` dans ce ZIP contient déjà la même URL `/exec` que le dépôt GitHub public consulté le 20 octobre 2026.** Si vous avez créé un **nouveau** déploiement depuis, conservez plutôt votre URL de déploiement actuelle. L’URL Apps Script n’est pas un mot de passe, mais ne publiez pas le tableur ni une clé privée.

## Contenu du ZIP

- `index.html` : accueil, calendrier, champs des noms et confirmation.
- `styles.css` : design, animations et adaptations mobiles.
- `app.js` : choix des journées, validation obligatoire des deux noms, envoi au serveur.
- `config.js` : URL du Google Apps Script existant (préconfigurée pour ce dépôt).
- `backend/Code.gs` : serveur Google Apps Script et tableau de bilan Google Sheets (correctif pour les paramètres régionaux français et migration des anciennes réponses).
- `assets/logo-officiel.png` : logo fourni par l’Orchidbleue, utilisé sur toutes les pages.
- `assets/favicon.svg` : icône hivernale du navigateur.

## Calendrier du sondage

Les deux périodes visibles sont **21–26 décembre 2026** et **28 décembre 2026–2 janvier 2027**.

| Journées pouvant être cochées | Journées bloquées |
|---|---|
| 21, 22 et 23 décembre | 24, 25 et **26 décembre (samedi)** |
| 28, 29 et 30 décembre | 31 décembre, 1er et **2 janvier (samedi)** |

Une famille peut sélectionner plusieurs dates. La case « aucun service nécessaire » est également possible, mais les champs **nom de la personne responsable** et **nom du/de la participant·e** sont toujours obligatoires. La demande est un **sondage**, pas une réservation ferme.

## 1 — Enregistrer les réponses dans votre Google Sheets

**Important : GitHub Pages seul ne stocke pas les réponses.** Un petit serveur Google Apps Script sert à écrire dans un tableur privé.

1. Dans le compte Google de l’organisme, ouvrez <https://sheets.google.com/> et créez un tableur, par exemple « Sondage Orchidbleue — Fêtes 2026-2027 ».
2. Cliquez **Extensions → Apps Script**, puis remplacez le code du fichier `Code.gs` par **tout le contenu** de `backend/Code.gs`.
3. Enregistrez. Dans la barre d’outils, choisissez `initialiserSondage` et cliquez **Exécuter**. Acceptez les autorisations Google. Deux onglets seront créés : `Réponses` et `Bilan`.
4. Dans Apps Script, choisissez **Déployer → Nouveau déploiement → Application Web**. Sélectionnez **Exécuter en tant que : Moi** et **Qui a accès : Tout le monde**. Copiez l’URL du déploiement se terminant par `/exec`.
5. Si vous démarrez un **nouveau** projet ou changez de déploiement, ouvrez `config.js` et remplacez son URL par la nouvelle, par exemple :

   ```js
   window.ORCHID_CONFIG = {
     SCRIPT_URL: "https://script.google.com/macros/s/VOTRE_IDENTIFIANT/exec"
   };
   ```

6. Publiez le site sur GitHub Pages (instructions ci-dessous). Sur votre site publié, testez avec **deux vrais noms de test**, choisissez une date, envoyez. Vérifiez que la confirmation indique que la réponse est enregistrée et qu’une ligne apparaît dans l’onglet `Réponses`. Vérifiez également que le `Bilan` actualise le compteur.
7. Pour tester la modification, sur le **même navigateur**, remplissez exactement les **mêmes deux noms** et renvoyez une sélection différente. La ligne de cet identifiant local sera mise à jour.
8. Supprimez manuellement les réponses de test avant de partager le sondage (conservez les en-têtes de la première ligne).

**Si vous modifiez ensuite le code Apps Script :** choisissez **Déployer → Gérer les déploiements → Modifier → Nouvelle version** pour que le changement soit appliqué à votre URL actuelle.

### Colonnes du Google Sheets

L’onglet `Réponses` contient :

1. Identifiant de réponse (utilisé pour les modifications depuis un même navigateur).
2. Dernière modification.
3. **Nom de la personne responsable.**
4. **Nom du ou de la participant·e.**
5. « Aucun besoin » : **1 = oui, 0 = non** (format numérique compatible avec les bilans français).
6. Liste des journées sélectionnées.
7. Une colonne 1/0 pour chacune des **six journées offertes**.

L’onglet `Bilan` présente le nombre total de réponses, le nombre sans besoins et **les besoins par journée**, avec graphique.

### Correction si votre Google Sheets contient déjà une ancienne réponse

Votre premier test peut montrer « Identifiant de réponse », « Date de réponse », « Aucun besoin », « Journées sélectionnées », ainsi que les samedis comme journées offertes. C’est **l’ancienne version anonyme du script** et non la nouvelle. L’identifiant aléatoire n’est pas un bug : il sert uniquement à modifier une réponse depuis le même navigateur.

Pour réparer votre Google Sheets existant **sans perdre le premier test** :

1. Dans le classeur existant, ouvrez **Extensions → Apps Script**.
2. Ouvrez `Code.gs`, sélectionnez tout, et **remplacez tout** avec le contenu du nouveau `backend/Code.gs` fourni ici. Enregistrez.
3. Sélectionnez la fonction **`initialiserSondage`**, puis cliquez sur **Exécuter**. Si l’onglet `Réponses` contenait des anciennes colonnes, il sera renommé automatiquement en `Réponses - archive AAAAMMJJ-HHMMSS`. Les anciennes réponses restent conservées en archive, et un nouvel onglet `Réponses` avec les colonnes `Personne responsable` et `Participant·e` sera créé. La feuille `Bilan` et son graphique seront recréés.
4. Allez dans **Déployer → Gérer les déploiements**. Modifiez le déploiement Web existant avec le crayon et choisissez **Version → Nouvelle version**, puis **Déployer**. Ne créez pas un second déploiement si vous souhaitez conserver votre URL `/exec` actuelle.
5. Assurez-vous que **le site GitHub Pages contient le nouvel `app.js`** et les fichiers de votre sondage actuel (champs des noms et samedis désactivés). `config.js` doit contenir l’URL `/exec` du déploiement utilisé.
6. Soumettez **une nouvelle réponse de test**, avec les deux noms et le lundi 21 décembre, puis vérifiez que les noms figurent en colonnes **C et D**, le besoin en colonne **G** (valeur `1`) et le Bilan affiche **1** réponse avec **1** besoin pour le lundi. Les données de l’archive ne sont volontairement pas ajoutées au nouveau bilan : elles pourraient inclure des jours fermés.

**Pourquoi les `#ERROR!` auparavant ?** Certaines formules du bilan utilisaient des virgules pour séparer leurs arguments; un Google Sheets en français peut exiger un point-virgule. Le nouveau script utilise uniquement des formules `COUNTA` et `SUM`, à **un seul argument**, et enregistre « aucun besoin » numériquement (1/0). Il évite ainsi la dépendance au séparateur régional.

**Important :** copier le nouveau code dans l’éditeur **ne met pas automatiquement à jour l’application Web déjà déployée**. L’étape « Nouvelle version » ci-dessus est donc obligatoire pour que le site continue d’enregistrer les nouvelles colonnes.

## 2 — Mettre le site sur GitHub Pages

1. Sur GitHub, créez un dépôt, par exemple `sondage-orchidbleue`.
2. Décompressez ce ZIP, puis placez les fichiers `index.html`, `app.js`, `styles.css`, `config.js` **et le dossier `assets/`** à la racine du dépôt. Le dossier `backend/` peut y être conservé car il ne contient aucune clé privée, mais il n’est pas utile côté visiteur.
3. Ouvrez **Settings → Pages → Deploy from a branch**. Choisissez la branche `main`, le dossier `/(root)` et enregistrez.
4. Votre lien GitHub Pages apparaîtra après le déploiement. Vérifiez la version mobile, un choix multiple, les six dates bloquées et les deux noms obligatoires.
5. **Ne partagez pas le formulaire tant que la bannière jaune « Mode aperçu » s’affiche** : elle signifie que rien ne s’enregistre dans Google Sheets.

## 3 — Données personnelles et précautions essentielles

Cette version recueille volontairement le **nom d’une personne responsable et celui d’un ou d’une participant·e**. Il ne s’agit plus d’un sondage anonyme.

- Gardez le **Google Sheets privé**, uniquement accessible au personnel autorisé.
- Assurez-vous que le texte d’information relatif à la confidentialité répond aux exigences et pratiques de votre organisme et indique, au besoin, le contact responsable et la durée de conservation. Détruisez les données lorsqu’elles ne sont plus nécessaires à la planification.
- Le formulaire public **n’exige pas de connexion** : il ne vérifie pas l’identité d’une personne et il n’empêche pas techniquement les soumissions indésirables.
- Pour faciliter les modifications sur le même navigateur, une empreinte SHA-256 des **deux noms normalisés**, associée à un identifiant aléatoire, est mémorisée localement après réception réussie. **Les noms eux-mêmes ne sont pas conservés dans le stockage local** par ce code. La feuille Google Sheets, elle, conserve les noms en clair.
- Les réponses envoyées depuis un autre appareil ou navigateur peuvent former une nouvelle ligne, même avec les mêmes noms. La déduplication n’est donc **pas garantie**. Les noms doivent être orthographiés de manière identique sur un même navigateur pour réutiliser son identifiant. Pour gérer les doublons, utilisez les colonnes nominatives du tableur.
- La collecte s’effectue par formulaire POST vers Google Apps Script, dans un iframe caché; une confirmation du serveur est attendue avant d’afficher un succès réel. Si le serveur ne répond pas, le site n’affirme pas avoir enregistré la réponse.
- Le frontend et l’URL `/exec` sont publics; **ne mettez aucune clé privée dans le code GitHub Pages**.
- Les noms peuvent être des informations personnelles, possiblement associées à des personnes vulnérables : évitez de publier le classeur ou les bilans individuels.

## 4 — Personnalisation

- **Lorem ipsum :** modifier le paragraphe `id="intro-placeholder"` dans `index.html`.
- **Titres et consignes :** modifier les textes dans `index.html`.
- **Couleurs et saison :** modifier les variables de `:root` en haut de `styles.css`.
- **Logo :** `assets/logo-officiel.png` (fichier fourni dans la demande).
- **Dates :** si le calendrier change, modifier **à la fois** `DAYS` dans `app.js` et `JOURS_OUVERTS` dans `backend/Code.gs`, puis **redéployer Apps Script**. La version actuelle est limitée aux Fêtes 2026–2027.

## 5 — Test local

Ouvrez le dossier contenant `index.html`, puis lancez :

```bash
python -m http.server 8000
```

Visitez `http://localhost:8000`. Sans URL `/exec` dans `config.js`, le site est en **mode aperçu** : sélection, champs obligatoires et confirmation sont testables, mais aucune réponse n’est enregistrée.
