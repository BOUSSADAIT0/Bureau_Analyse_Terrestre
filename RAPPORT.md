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
* Pipeline propre (train passé → test futur, sans fuite) : **rappel 11,1 %**, **précision 0,1 %**.
* Split aléatoire (Phase 4/5) : **rappel 21,1 %**, **précision 0,5 %**.
* Les scores baissent encore, mais la méthode est plus sérieuse : pas de fuite, pas de témoins séparés, pas de futur dans le train.
