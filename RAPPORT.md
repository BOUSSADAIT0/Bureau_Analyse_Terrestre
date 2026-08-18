## Partie 1

* Le fichier contient **88 875 lignes**.
* **88 679 lignes** ont été chargées correctement.
* **196 lignes** sont mal formatées.
* Certaines données sont incorrectes ou manquantes.
* On a trouvé **189 canulars** seulement.
* Les canulars représentent **0,21 % des données**.
* Le premier modèle donnait de très bons résultats.
* Mais il utilisait des informations qui donnaient déjà la réponse.
* On a donc retiré ces informations du modèle.
* Après correction, les résultats sont beaucoup moins bons.
* Le modèle détecte seulement une partie des canulars.
* Comme les canulars sont très rares, **l'accuracy peut être trompeuse**.
* Il est donc préférable de regarder le **rappel et la précision**.
* Au final, le modèle corrigé est moins performant, mais plus honnête.

## Partie 2

* **1 664 événements** ont été signalés par plus d'un témoin (même heure + même ville).
* Le plus gros événement compte **29 témoins** (Tinley Park, 2004).
* **612 relevés** ont un commentaire copié mot pour mot.
* Avec un split aléatoire, **586 événements** multi-témoins et **1 508 relevés** se retrouvent à cheval entre train et test.
* On a donc découpé par **événement** puis par **date d'observation** (`datetime`, pas `date_posted`).
* Date de coupure : **14 mars 2012 à 21h30**. Train : **70 775 relevés**. Test : **17 904 relevés**.
* Canulars : **0,254 %** dans le train, **0,050 %** dans le test (le futur en contient moins).
* Les 3 colonnes les plus trouées sont `country`, `state` et `shape`.
* Les canulars ne sont pas beaucoup plus fréquents quand il manque une valeur (ex. state : 0,27 % vs 0,21 %).
* On remplace les trous par `UNKNOWN` et on garde une colonne `_missing` pour ne pas perdre l'info.
* **7 022 durées** restent inutilisables après traitement. Médiane des durées valides : **180 secondes**.
* **193 durées** dépassent 1 jour ; on plafonne à **86 400 s** pour éviter les aberrations.
* **22 018 villes** uniques dont **14 177** n'apparaissent qu'une fois → regroupement en `OTHER` si < 5 occurrences.
* L'heure est encodée en sin/cos pour que 23h soit proche de minuit, pas de 20h.
* Le pipeline final produit **362 colonnes** au lieu de ~22 000 avec un OneHot brut sur toutes les villes.
* Pipeline propre (train passé → test futur, sans fuite) : **rappel 11,1 %**, **précision 0,1 %** (seuil 0,5).
* Split aléatoire (Phase 4/5) : **rappel 21,1 %**, **précision 0,5 %**.
* Les scores baissent encore, mais la méthode est plus sérieuse : pas de fuite, pas de témoins séparés, pas de futur dans le train.

## Partie 3

### Phase 13 — La facture du Bureau

* Grille : canular raté = **30 cr**, honnête marqué canular = **2 cr**.
* Seuil par défaut (0,5) : **2 980 crédits** (8 canulars ratés + 1 370 fausses alertes).
* Seuil optimal (coût minimum) : **0,67** → **270 crédits** (9 canulars ratés, 0 fausse alerte).
* Écart : **2 710 crédits** économisés en abandonnant le seuil 0,5.
* Décision : avec cette grille, le Bureau ne flaggue presque rien — mieux vaut rater un canular que lancer 1 370 équipes sur du vent.

### Phase 14 — Une promesse à 80 %

* Le système annonce des probas autour de 33–46 % alors que le taux réel de canulars est ~0,05 % → **trop confiant**.
* Après calibration isotonique, les probas se rapprochent de la réalité (tranche basse ~0,05 % réel).
* Les tranches avec moins de 20 relevés ne prouvent rien.

### Phase 15 — Deux analystes, deux chiffres

* Partie test : **17 904 relevés**, dont **9 canulars** seulement.
* Rappel au seuil optimal (0,67) : **0,0 %** → intervalle bootstrap **[0,0 % – 0,0 %]** (200 tirages).
* Précision : **0,0 %** → intervalle **[0,0 % – 0,0 %]**.
* Réponse au Conseil : comparer 0,31 et 0,34 n'a pas de sens — avec 9 canulars, déplacer 2 ou 3 labels change tout.

### Phase 16 — Trois dossiers

* **Dossier 1** (index 19511) : dahlonega, proba 0,53, vrai canular — ressemble aux honnêtes.
* **Dossier 2** (index 12674) : new york city, proba 0,67, proche du seuil — pas canular.
* **Dossier 3** (index 19896) : towson, proba 0,49, canular raté — angle mort.
* Importance globale (permutation) : **`state`** en tête, puis latitude/longitude. Pas `comments` (absent du modèle).

### Phase 17 — Angle mort géographique

* **US** : 15 029 relevés test, 6 canulars (0,04 %).
* **GB** : 192 relevés, 1 canular (0,52 %).
* **CA** : 583 relevés, 0 canular.
* **Autres** : 2 100 relevés, 2 canulars (0,10 %).
* Rappel global 0 % masque des zones très différentes. Décision : **même frontière partout** (simplicité), les petites zones ne permettent pas d'ajuster un seuil.

### Phase 18 — Transmission d'archive

* La proportion de canulars **varie d'une année à l'autre** — les habitudes d'annotation du Bureau ont changé.
* Épreuve ancien→récent (split Phase 8) : rappel **0 %** au seuil 0,67 (vs 11,1 % au seuil 0,5 en Phase 10).
* Surveillance sans étiquettes :
  * % prédictions canular → alerte si > **0,16 %** (hebdo).
  * Part US → alerte si écart > **10,9 pts** vs train (mensuel).
  * Proba moyenne du flux → alerte si > **0,579** (hebdo, train + 2 écarts-types).
