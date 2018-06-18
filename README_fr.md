## Aunée : encourager et faciliter l'accès à une alimentation sans produits chimiques et préserver nos resources en eau.

Python 3.4
Projet en alpha

### Dépendances (au 18 juin 2018):
* aucune

### Données d'entrée
`inputFR.py` contient les données environnementales (climat et sol), biologiques et nutritionnelles relatives à chaque culture prise en compte par le programme pour la France. Actuellement, elles sont présentées sous forme de **dictionnaires**, respectivement `environment`, `plants` et `nutrition`. **Toute proposition d'amélioration est la bienvenue** (dans l'idéal, les données devront pouvoir être modifiées par l'utilisateur via un tableau interactif) !


### Hiérarchie des fonctions d'Aunée
VegAu se compose de trois parties:
1. **Étape 1**: `MDL_eligibilityTest(x, data)`. Soumission de chaque culture à un test d'éligibilité pour chaque PRA pour le temps de sa période de gestation
   1. `ASSESS_Tmin_germ(crop,x,PRA)`: **Arbres fruitiers** uniquement: vérification que la **température minimum** est bien en-dessous de la température nécessaire à la **fin de l'hivernage** des arbres fruitiers au moins une fois pendant l'hiver;
   2. `ASSESS_Tmin(crop,x,PRA)`: On vérifie que la **température minimum** ne descende pas en dessous de celle supporté par la culture;
   3. `ASSESS_Sunshine(crop,x,PRA)`: pour les **cultures nécessitant beaucoup de soleil uniquement**, compare l'ensoleillement de la zone concernée avec le plus haut ensoleillement enregistré en France.
   4. `ASSESS_Water(crop,x,PRA)`: On vérifie que les** besoins en eau** de la culture sont couverts par les précipitations;
   5. `ASSESS_pH(crop,x,PRA)`: On vérifie que le** pH du sol** est bien compris dans la fenêtre de pH supportée par la culture.
   6.`ASSESS_PRAedibility()`:  **Synthèse des tests précédents** pour aboutir à un dictionnaire de cultures qui répondent à tous ces critères pour la PRA courante.
2. `ASSESS_Priority(x,PRA)`: Calcul d'**indices "d''adaptabilité" et de priorité** devant **aider au choix des cultures** lors de l'élaboration de la **rotation**. Cette fonction fait partie du "step1(.py)"
3. **Étape 2** : `MDL_Rotations(crop,x,PRA)`. Élaboration d'une **rotation culturale jusqu'à épuisement des ressources du sol**: si plus aucune culture ne peut pousser par manque d'éléments nutritifs, la rotation est terminée. **Pour chaque test** précédent le choix de la culture suivante, on calcule un** indice compris entre 0 et 1 pour estimer la qualité de la ressource** par rapport aux exigences de la plante (plus l'indice est haut, meilleure est la ressource/condition climatique).
   1. `FindOptimalSeedingDate()`: On cherche la **période de plantation idéale** pour chaque culture pouvant être plantée le plus tôt possible après la culture précédente (pour la première culture, le mois de départ est fixé à mars)
   2. `ASSESS_OptimalWaterRessources()`: On vérifie que la culture pourra recevoir **assez d'eau** si elle est plantée à la date déterminée en 3.1..
   3. `ASSESS_Nutrients()`: On vérifie que les éléments nutritifs du sol sont suffisants (prise en compte de la décomposition des cultures précédentes)
   4. `ASSESS_PestDiseases()`: On vérifie si la famille botanique de la culture est déjà présente dans la rotation et si la fréquence minimale entre deux cultures de cette famille est respectée. L'**indice de "ravageurs et maladies"** calculé suite à ce test est **pondéré par 2** lors de la synthèse des indices: les tests précédents suppriment les cultures de la liste s'ils ne correspondent pas aux critères établis, contrairement à la fonction ` ASSESS_PestDiseases()`. De ce fait, et compte tenu de l'**impact des ravageurs et maladies sur le rendement**, on lui importe plus d'importance qu'aux autres indices.
   5. `SelectedCrop = SelectCrop()`: **Sélection de la culture suivante** en fonction de la synthèse des indices précédents.
   6. `ASSESS_Water_CompanionCrop()` et `ASSESS_Nutrients_CompanionCrop()` : Évaluation de la possibilité d'implanter une "culture compagnon" (ex. trèfle) si les ressources en eau et en nutriments est suffisante.
   7. `APPLY_ResiduesDecomposition_of_PreviousCrops()`: Simulation de la **décomposition** des résidus de la **culture précédente**.
   8. `SelectedCrop_Harvest()`: Simulation de la **récolte** (les nutriments contenus dans la plante sont prélevés du sol)
   9. `APPLY_ResiduesDecomposition_of_SelectedCrop()`: Préparation de la** décomposition** des résidus de la** culture sélectionnée** (évaluation du délais et de la quantité des éléments nutritifs rendus au sol).
   10. `APPLY_ResiduesDecomposition_of_CompanionCrop()`: **Si culture compagnon**: Simulation de la décomposition des résidus de la culture compagnon
   11. Calcul du rendement en fonction de l'indice de ravageurs et maladies
   12. Adaptation du rendement en fonction de la sécheresse à laquelle la culture a dû faire face
4. **Étape 3:** `ASSESS_Nutritional_feasibility()`. Détermination de la quantité d'éléments nutritionnels obtenus d'après les rendements calculés lors de l'étape 2. On compare ensuite cette quantité aux besoins nutritionnels de la population en prenant en compte les classes d'âge des différentes strates de la population.


### Projet d'interface graphique:
Une fois le code fonctionnel et stable, il est prévu de lui offrir une **interface graphique cross-plateforme la plus épurée et intuitive possible**. Une maquette est disponible dans le **dossier VegAu_GUI**.

