# Projet python

## Introduction

Nous avons tout d'abord choisi une base de données produite par Samuel Allain de la DREES, avec qui nous avons échangé par mail et visio pour mieux comprendre le mode de production et de présentation des données. Cette base a donné lieu à une publication sur le lien entre niveau de vie, maladie chronique et espérance de vie (Allain 2022). Elle n'analysait pas les différences régionales de prévalence des maladies chroniques, raison pour laquelle nous avons utilisé cette partie de la base de données. 
Nous avons voulu interroger l'importance des facteurs environnementaux (pollution, pesticides, ...) au regard des facteurs socioéconomiques.

Ce projet vise à étudier les liens entre maladies chroniques et facteurs socio-économiques. Pour cela on considère différentes données :
- DREES : Contient les taux d'incidences et de prévalences pour diverses maladies chroniques, par tranche d'âge, niveau de vie, région, sexe et niveau de diplôme.
- Insee : On forme une table comportant, pour chaque région métropolitaine :
  - Géographie (pour les cartes) ;
  - Nombre de médecins ;
  - Les surfaces agricoles et production agricole (pour par exemple les maladies pulmonaires) ;
  - Les données socio-économiques (niveau de vie, taux pauvreté, ...).

## Lien vers les données
- Inégalités sociales face aux maladies chroniques : https://data.drees.solidarites-sante.gouv.fr/explore/dataset/er_inegalites_maladies_chroniques/information/
- Nombre de médecins par région : https://www.insee.fr/fr/statistiques/2012677#tableau-TCRD_068_tab1_regions2016
- Nombre de psychiatres par région : https://drees.shinyapps.io/demographie-ps/
- Densité de population par région : https://www.insee.fr/fr/statistiques/fichier/4277596/T20F013.xlsx

## Quelques définitions utiles

- Grandeurs épidémiologiques théoriques :
   - Prévalence $= \frac{\text{nombre de cas présents ou passés}}{\text{population exposée à une date donnée }}$. C'est un stock à une date $t$.
   - Incidence : $ = $ nombre de nouveaux cas de cette maladie observés sur une période donnée. C'est une vitesse d'apparition d'une maladie à une date $t$.

# Bilan de la discussion avec Samuel Allain :

- vartaux = type de maladie (code de pathologie détaillée ou type de pathologie)
- I\_cat et cat = catégorie de la maladie
- ne pas utiliser les variables "traitements"

## Calcul des taux

- Toutes les données sont standardisées par rapport à la structure âge/sexe de la population de référence (spécifiée dans VarPartition)
- $Taux\ non\ standard = \frac{Poids1}{PoidsTot}$. Simple à calculer MAIS peut conduire à des analyses biaisées. Il y a un effet de structure qui n'est pas pris en compte : on compare des populations avec des structures d'âge et de sexe non équivalentes. Exemple : les jeunes sont moins malades, les pauvres sont plus jeunes, donc les pauvres sont moins malades, et donc la pauvreté protège de la maladie. 
- Pour éviter ces biais d'analyse il faut comparer à d'autres variables égales (âge, niveau de vie, niveau de diplômes,...) : on réagrège les taux en appliquant une structure commune aux données. C'est ce qui donne le $Taux\ standard\ direct$ : On pondère par les effectifs de chaque catégorie. 
- Le $Taux\ standard\ indirect$ est une autre méthode de standardisation qui est plutôt utilisée pour les phénomènes rares. C'est l'incidence (ou la prévalence) attendue "compte tenue de l'âge, du sexe, du niveau de vie, ...".

## Comment faire des moyennes dans notre projet python ?

- Formule la plus simple : $Taux\ non\ standard = \frac{\sum\limits_{nivviem}Poids1}{\sum\limits_{nivviem}PoidsTot} = \sum\limits_{nivviem}Poids1 \times TauxNonStand$. \
où $Poids1$ représente l'effectif de la population qui a 1 en prévalence/indicence. 

- Ca va être compliqué pour nous avec nos données (en fait impossible) de faire des taux standardisés.
- Pour éviter une analyse biaisée due à l'utilisation de taux non standardisés, on peut utiliser les lignes $NaN$ dans $VarPartition$ et $VarGroupage$ :
    - Aucun problème pour $VarPartition = NaN$ : on a bien une ligne avec les taux standardisés par $VarGroupage$ et $ValGroupage$, pour chaque maladie.
    - ATTENTION pour $VarGroupage = NaN$ : Si on fait une moyenne au sein de la région, on se heurte au fait qu'ici, les standardisations ont été faites VIS-A-VIS de la population de la région (âge, niveau de vie, diplômes, ...) donc il faut faire attention à l'analyse...
    
    
## Différentes maladies :

- varTauxLib est une sous-catégorie de maladie ;
- catLib est une maladie.

On se concentre ici uniquement sur catLib. Ceci nous empêche d'utiliser les taux non standardisés calculés par varGroupage déjà présents (puisqu'ils sont calculés pour chaque sous-catégorie de maladie), mais cela simplifie nos analyses.

## Deux possibilités :
- Faire des taux non standardisés, au risque de faire des analyses de corrélations ou de causalités biaisées ;
- Faire des moyennes de taux standardisés, au risque d'obtenir des chiffres faux...

## Conséquences :
- D'après notre discussion avec Samuel Allain, chacune des deux méthodes devrait nous donner des taux qui ne sont pas très éloignés d'un véritable taux standardisé.
- Ainsi, dans la mesure où notre analyse n'a pas vocation à être publiée, nous choisirons suivant les cas l'une des deux options, tout en gardant à l'esprit les limites de nos analyses, et de nos statistiques descriptives.
- Si on s'arrête à ces grandeurs, alors on va prendre en compte des _facteurs de confusion_ et arriver à

- Taux standardisés : élimine les effets liés à des "facteurs de confusion" (âge, sexe, ...)
    - Standardisation directe : 
    - Standardisation indirecte : 

## Organisation du notebook

- Partie 1 : Analyse de la table de la DREES seule.
- Partie 2 : Constitution, puis analyse d'une table descriptive des régions. On a pour l'instant, pour chacune des régions métropolitaines :
  - Géographie (pour les cartes) ;
  - Nombre de médecins ;
  - Les surfaces agricoles et production agricole (pour par exemple les maladies pulmonaires) ;
  - Les données socio-économiques (nivviem, taux pauvreté, ...).
- Partie 3 : Analyse jointe des deux tables.

## Pour faire tourner le notebook

- Toutes les cellules s'exécutent linéairement ;
- Toutefois, les cellules avant des sorties graphiques html peuvent mettre un peu de temps à s'afficher, voir même ne pas s'afficher sur certains ordinateurs ;
- Pour cette raison, nous avons travaillé sur le service sspcloud, qui fonctionne parvient parfaitement à afficher des graphiques html.

## Principales conclusions 

A FINIR

## Bibliographie

- Allain, Samuel. 2022. Les maladies chroniques touchent plus souvent les personnes modestes et réduisent davantage leur espérance de vie. 1243. Direction de la recherche, des études, de l’évaluation et des statistiques (DREES).
- Anon. 2012. « Géographie de la santé », Géoconfluences.
- Anon. s. d. « ACP, bases mathématiques ». 
- Santé publique France. s. d.-a. Influence de l’environnement social sur la survie des patients atteints d’un cancer en France. Étude du réseau Francim.
- Santé publique France. s. d.-b. Le diabète en France : les chiffres 2020.
- Santé publique France. s. d.-c. Les agriculteurs et la maladie de Parkinson.
- Santé publique France. 2022. Bulletin épidémiologique hebdomadaire, Journée mondiale du diabète. 22. Santé publique France.
- Santé publique France. s. d. Pollution atmosphérique : quels sont les risques ?

