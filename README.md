# Bureau d'Analyse Terrestre

Projet OVNI — analyse des relevés Klaxo-3.

## Lancer l'analyse

```bash
pip install -r requirements.txt
jupyter notebook nettoyage_ufo.ipynb
```

Ouvrir le notebook et exécuter toutes les cellules (Run All). Les Phases 1 à 6 s'enchaînent dans l'ordre.

Télécharger le CSV si absent :

```bash
curl -L -o releves_klaxo3.csv https://raw.githubusercontent.com/planetsig/ufo-reports/master/csv-data/ufo-complete-geocoded-time-standardized.csv
```

Les résultats et décisions sont dans `RAPPORT.md`.
