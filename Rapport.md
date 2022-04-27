# Rapport de Projet IA<img src="https://creacampus.fr/wp-content/uploads/2021/07/insa-logo.jpg" alt="img" align="right" align="top" style="zoom:20%;" />

Louis Descamps 

--------------

## Sujet 

Le but du sujet est de vérifier l'hypothèse suivante 

> Les performances des algorithmes de Machine Learning permettent de modéliser une tendance d'une manière efficace. 

Pour cela nous tenterons de modéliser les tendances du covid19 et de prédire son évolution au sein d'une population. 

Autrement dit notre but est de créer une IA qui peut : 

- Prédire le nombre de décès à venir liés aux Covid. 
- Prédire le nombre de nouveaux cas positifs. 

Nous travaillerons avec un $\Delta T$ d'une journée (24h). 

<div style="page-break-after: always; break-after: page;"></div>

# Choix et Analyse des données 

On travaillera sur l'ensemble du territoire français en exploitant les données de [synthèses des indicateurs](https://www.data.gouv.fr/fr/datasets/synthese-des-indicateurs-de-suivi-de-lepidemie-covid-19/?reuses_page=1#community-reuses) du ministères de la santé.

On pourra lire la description des données sur le site de téléchargement, voici un rapide survol : 



Les bases de données diffère par la prise en compte de la région et du département. 

**Contexte : ** Chaque données est entourée de son contexte avec les champs `date`, `dep` (Département) et `reg` (Région). 

**Situation Hospitalière :** On note 3 catégories majeures `hosp` (pour les hospitalisations), `rea` (pour les patients en réanimations), `rad` (pour les retours à domicile suite à une entrée à hôpital). Ces trois catégories sont complétées par `incid_***` Le nombre de nouveaux admis/relâchés en 24h. 

**Décès :** Notamment `dc_tot` (le nombre de décès totaux dû au Covid19 dans des établissement de santé). 

**Tests :** Qui inclue notamment les cas confirmés `conf`. 

**Indicateurs mathématiques :** Ceux qui pourraient nous être particulièrement utile étant `R` le facteur de reproduction du virus, `TO` LA proportion d'occupation des services d'urgences par des cas Covid.  

---------

On travaillera sur la base de donnée qui ne prends pas en compte les départements et les régions. On mettra au point notre algorithme sur cette base de données. On pourra utiliser les données par région pour vérifier la capacité de prédiction de l'algorithme dans un second temps. 



On gardera uniquement huit colonnes.  `date`, `TO`, `R`, `hosp`, `rad`, `conf`, `conf_j1` et `dc_tot`. On travaillera sur ce jeu de données plus petit pour simplifier les calculs et permettre un meilleur apprentissage de l'algorithme. Beaucoup de ses données sont absentes en parties de la base de données. On utilisera `conf_j1` pour calculer `conf` avec la formule `conf(n) = conf(n-1)+conf_j1(n)`. On utilisera  `dchosp` et `esms_dc` pour calculer `dc_tot` avec la formule `dc_tot = dchosp + esms_dc`. 

Les manquements dans la base de données ne permettent pas de commencer l'étude avant novembre 2020. 

-------

On crée une nouvelle base de donnée avec ces modifications que l'on utilisera dans la suite. Elle contient 508 lignes et 8 colonnes. 



## Création des batchs

Il faut maintenant séparé les données en batch d'entrainement et de test. On peut par exemple choisir 
