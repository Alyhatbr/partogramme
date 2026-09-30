# Partogramme — ce qu'il faut savoir

Site de suivi du travail en salle de naissance : on choisit une patiente et sa grossesse,
on saisit les examens (dilatation, hauteur, orientation) et on suit la courbe de dilatation.
La maquette de référence est `maquette.png` : le design du site doit s'y tenir.

## Structure du code

- `index.html` : une seule page avec 5 onglets (Suivi du travail, Patientes, Grossesses,
  Enfants, Praticiens). Les formulaires de saisie sont cachés (`hidden`) et ouverts par un bouton.
- `config.js` : l'URL du projet Supabase et la clé publishable. RIEN D'AUTRE : jamais de clé
  secrète (service_role ou sb_secret) dans ce fichier ni ailleurs dans le dossier.
- `app.js` : onglets, patientes, choix patiente/grossesse de l'onglet Suivi, courbe (Chart.js).
- `grossesses.js`, `examens.js`, `enfants.js`, `praticiens.js` : un fichier par entité.
- `export.js` : export Excel du partogramme avec ExcelJS (bouton en haut, actif quand une
  patiente/grossesse est choisie).
- Bibliothèques (Supabase JS, Chart.js, ExcelJS) chargées depuis le CDN jsdelivr, rien à installer.

## Base de données (Supabase, projet `yqvismrvinnkkkznpion`)

6 tables, toutes avec RLS ouvert en lecture, ajout et modification (données du TP fictives) :

- `patiente` (nom, nom_marital, prenom, gs_rh, date_naissance)
- `grossesse` → lie une `patiente`, 4 praticiens référents (sage-femme, obstétricien,
  anesthésiste, pédiatre), durée du travail et de l'expulsion en type `time` (saisies comme
  h:min via un champ `time` dans le formulaire)
- `examen` → lie une `grossesse` : date_heure (timestamptz), dilatation (0–10 cm),
  presentation_hauteur (-5 à +5), orientation, remarque
- `enfant` → lie une `grossesse` : prenom, sexe, date_heure_naissance, poids (kg, réel),
  taille (cm)
- `praticien` (specialite = Sage-femme / Obstétricien / Anesthésiste / Pédiatre)
- `orientation` : table de référence (9 lignes) remplie à l'avance, ne pas la remplir depuis
  le site ; chaque ligne a un nom et un pictogramme

Types énumérés Postgres à respecter : `groupe_sanguin` (A+, A-, B+, B-, O+, O-, AB+, AB-,
inconnu), `specialite`, `sexe` (Garçon, Fille). Les champs vides des formulaires sont
envoyés comme `null`, jamais comme chaîne vide.

Ne pas renommer les tables ni les colonnes : les noms viennent du TP.

## En ligne

Le site est publié à chaque `git push` :
https://sps-g40-parto.professeurpetitchat.com/