## Aunée : to encourage and to facilitate the access to organic food as well as to preserve the water resources

Python 3.4
Alpha project

Official Website (mainwhile only in French) : https://aunee.frama.site
**CAUTION : at this stage, data only exist for France. Any contribution to enrich the database for other countries is very welcome !**


### Dependances (18/06/2018):
None

### Données d'entrée
`inputFR.py` contains environmental data (climate and soil), biological and dietical data relative to each crop from the software's database for France. Actually, they are organized in **dictionnaries**, respectively  `environment`, `plants`, and `nutrition`. **Any proposal or comment is welcome** for a better data management ! Ideally, data should be able to get modified by users in an interactive table in a GUI.


### Function's hierarchy
Aunée is composed by three parts:
1. **Step 1**: `MDL_eligibilityTest(x, data)` : each crop get tested during a succession of 5 eligibility tests for one or more selected area(s). At the end of these 5 tests, it does the **synthesis of their results** to get a crops selection (dict too) that can potentially grow naturally in the area of interest.
   1. `ASSESS_Tmin_germ(crop,x,PRA)`: **fruit trees** only. Keeps only crops for which the area's minimum temperature is colder than the temperature required for the end of the tree's wintering at least once during winter:
   2. `ASSESS_Tmin(crop,x,PRA)`: excludes crops for which the minimum temperature of the study area is lower than the crop's the minimum tolerable temperature ;
   3. `ASSESS_Sunshine(crop,x,PRA)`: for "exotic" crops only. It compares the area's sunshine with the shiniest place in France to get a comparison for crops that can only grow in the South of France for example.
   4. `ASSESS_Water(crop,x,PRA)`: excludes crops which can't get **enough water** during their growing season in the selected area.
   5. `ASSESS_pH(crop,x,PRA)`: only keeps crops for which the **soil pH** is tolerable.
   
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\TRANSLATION IN PROGRESS\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\ thanks for your patience \*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\
\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*
   
2. `ASSESS_Priority(x,PRA)`: Calcul d'**indices "d''adaptabilité" et de priorité** devant **aider au choix des cultures** lors de l'élaboration de la **rotation**. Cette fonction fait partie du "step1(.py)"

3. **Étape 2** : `MDL_Rotations(crop,x,PRA)`. Élaboration d'une **rotation culturale jusqu'à épuisement des ressources du sol**: si plus aucune culture ne peut pousser par manque d'éléments nutritifs, la rotation est terminée. **Pour chaque test** précédent le choix de la culture suivante, on calcule un** indice compris entre 0 et 1 pour estimer la qualité de la ressource** par rapport aux exigences de la plante (plus l'indice est haut, meilleure est la ressource/condition climatique).
   1. `ASSESS_SeedingDate(PRA, x)`: On cherche la **période de plantation idéale** pour chaque culture pouvant être plantée le plus tôt possible après la culture précédente (pour la première culture, le mois de départ est fixé à mars)
   2. `ASSESS_WaterResources(PRA,x)`: On vérifie que la culture pourra recevoir **assez d'eau** si elle est plantée à la date déterminée en 3.1..
   3. `ASSESS_Nutrients(x, PRA)`: On vérifie que les éléments nutritifs du sol sont suffisants (prise en compte de la décomposition des cultures précédentes). Pour cela, on utilise la sous-fonction `ASSESS_NutrientsMargin (PRA, x)`
   4. `ASSESS_PestDiseases(x, PRA)`: On vérifie si la famille botanique de la culture est déjà présente dans la rotation et si la fréquence minimale entre deux cultures de cette famille est respectée. L'**indice de "ravageurs et maladies"** calculé suite à ce test est **pondéré par 2** lors de la synthèse des indices: les tests précédents suppriment les cultures de la liste s'ils ne correspondent pas aux critères établis, contrairement à la fonction ` ASSESS_PestDiseases()`. De ce fait, et compte tenu de l'**impact des ravageurs et maladies sur le rendement**, on lui importe plus d'importance qu'aux autres indices.
   5. `SelectedCrop = SELECT_CashCrop(x,PRA,data)`: **Sélection de la culture (principale) suivante** en fonction de la synthèse des indices précédents.
   6. `SELECT_CompanionCrop(x,PRA)` : Évalue la possibilité d'implanter une "culture compagnon" (ex. trèfle) si les ressources en eau et en nutriments sont suffisantes (calcul d'après `ASSESS_Water_CompanionCrop(x, PRA)` et `ASSESS_Nutrients_CompanionCrop(x, PRA)`).
   7. `APPLY_ResiduesDecomposition_of_PreviousCrops(PRA, x)`: Simulation de la **décomposition** des résidus de la **culture précédente**.
   8. `APPLY_ResiduesDecomposition_of_CompanionCrop(x)`: Préparation de la** décomposition** des résidus de la** culture sélectionnée** (évaluation du délais et de la quantité des éléments nutritifs rendus au sol).
   9. `APPLY_SelectedCC_Kill(PRA,x)`: **Si culture compagnon**: Simulation de la décomposition des résidus de la culture compagnon
   10. `APPLY_SelectedCrop_Harvest(PRA, x)`: Simulation de la **récolte** (les nutriments contenus dans la plante sont prélevés du sol)
   11. Calcul du **rendement** en fonction de l'indice de ravageurs et maladies
   12. Adaptation du rendement **en fonction de la sécheresse et potentiels ravageurs et maladies** à laquelle la culture a dû faire face
   13. `APPLY_ResiduesDecomposition_of_SelectedCrop(x)` : Calcul des nutriments rendus sur la durée par les résidus de la culture sélectionnée avant de choisir la suivante.
   
4. **Étape 3:** `MDL_QTTperPerson(x,nutrition)`. Détermination de la **quantité d'éléments nutritionnels obtenus** d'après les rendements calculés lors de l'étape 2. On **compare** ensuite cette quantité aux **besoins nutritionnels** de la population en **prenant en compte les classes d'âge des différentes strates de la population**.


### Projet d'interface graphique:
Une fois le code fonctionnel et stable, il est prévu de lui offrir une **interface graphique cross-plateforme la plus épurée et intuitive possible**. Une maquette est disponible sur le site officiel https://aunee.frama.site 
La possibilité de créer un GUI grâce à **Django** a été énoncée à plusieurs reprise. Il serait intéressant de savoir si une **utilisation hors ligne** serait possible. L'idéal serait que el programme puisse être utilisé **en ligne comme hors ligne**.

### Informations supplémentaires
Le travail réalisé en amont du projet ainsi que son manuel complet pour la version du 19 juin 2018 sont disponibles sur [le site officiel](https://aunee.frama.site/downloads).
Un [forum](https://aunee.frama.site/forum) et un [canal de tchat](https://riot.im/app/#/room/!YFmzdHPDCsNrHQQsUW:matrix.org) sont disponibles pour ceux qui le souhaitent pour dialoguer plus aisément.
