# Analyse Discriminante Linéaire (FR)
*English version below*

Le programme sert à la pratique de l'analyse discriminante linéaire (prédicitive). Il se base sur le fonctionnement des procédures [DISCRIM](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm) et [STEPDISC](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm) de SAS.
Le programme produit les fonctions de classement, permet de réaliser une prédiction à partir d'un ensemble de données et un mécanisme de sélection de variables a été implémenté.
Un reporting automatique des résultats est également disponible, en HTML ou en PDF.
Réalisé par [AbdDia](https://github.com/AbdDia) et [ASKruchinina](https://github.com/ASKruchinina) et [Valinquish](https://github.com/Valinquish) dans le cadre d'un projet en L3 IDS (Informatique et Statistique pour la Science des Données) à l'Université Lyon 2.

# Usage et explications
Un programme de démonstration a été mis à disposition (`demo_program.py`) avec les données correspondantes dans le dossier `data`.

Après avoir chargé vos données à l'aide de la bibliothèque [`pandas`](https://pandas.pydata.org) et avoir repéré le nom de la variable catégorielle, l’utilisateur devra créer un objet issu de la classe `LinearDiscriminantAnalysis` du module `discriminant_analysis.py` en spécifiant les deux paramètres (jeu de données et nom de la variable catégorielle).

```python
from discriminant_analysis import LinearDiscriminantAnalysis as LDA

lda = LDA(dataset, varName)
```

À partir de là, l’utilisateur pourra directement appeler les différentes méthodes pour réaliser les calculs qu’il souhaite, ainsi que les différents attributs de l'objet créé, s’il veut récupérer des paramètres précis.

### Attributs disponibles après la création de l'objet :
* `dataset` : le jeu de données
* `classEtiquette` : le nom de la variable cible
* `classNames` : les noms des valeurs prises pour la variable cible
* `varNames` : les noms des variables explicatives
* `n` : la taille de l'échantillon
* `p` : le nombre de variables explicatives
* `K` : le nombre de classes
* `V` : les matrices de covariance totale 
* `Vb` : les matrices de covariance biaisée
* `W` : la matrice de covariance intra-classe
* `Wb` : la matrice de covariance intra-classe biaisée

Ainsi, pour afficher la matrice de covariance biaisée, il suffit de faire :
```python
lda.Vb 
```

## Fonctions de classement
`fit()` produit les fonctions de classement.

```python
lda.fit()
```

### Attributs disponibles après l'appel de la fonction :
* `intercept_` : l’intercept calculé par le modèle
* `coef_` : les coefficients du modèle
* `infoFuncClassement` : les valeurs de la fonction de classement

## Fonction de prédiction
`predict()` permet de réaliser une prédiction à partir d'un ensemble de données et retourne le vecteur des classes prédites.

```python
y_pred = lda.predict(valeurs_a_predire)
```

## Matrice de confusion et taux de précision
Avoir les prédictions c’est très bien, mais **comment peut-on savoir ou plutôt comment peut-on mesurer si ces prédictions sont correctes ou non ?** La matrice de confusion et le taux de précision y aident. 

La fonction `confusion_matrix()` (*matrice de confusion*) « permet d’obtenir une évaluation non biaisée des performances du modèle en déploiement ». Elle prend le vecteur des vraies valeurs de la variable cible qui n’étaient pas utilisées lors de l’apprentissage et le vecteur des valeurs cibles prédites. Ainsi, **on peut estimer si la fonction est capable de bien prédire**. 
La fonction renvoie la matrice numérique. La fonction renvoie également un graphique de la matrice de confusion.

La fonction `accuracy_score()` calcule le taux de précision. Elle prend en entrée les vraies valeurs de la variable cible et les prédictions et renvoie la proportion des prédictions correctes.

```python
lda.confusion_matrix(y_true, y_pred)
lda.accuracy_score(y_true, y_pred)
```
### Attributs disponibles après l'appel de la fonction :
* `confusionMatrix` : la matrice de confusion
* `confusionMatrixGraph` : graphique de la matrice de confusion
* `accuracy` : le taux de précision

## Sélection de variables
La fonction `stepdisc()` **permet de réaliser une méthode de sélection de variable**. Elle permet à l’utilisateur d’aborder **une approche ascendante** (`forward`), ou **descendante** (`backward`). 
Il est nécessaire de fixer le seuil d'arrêt (risque).

```python
lda.stepdisc(slentry, method)
```

### Exemple : méthode de sélection ascendante
```python
lda.stepdisc(0.01,'forward')
```

### Attributs disponibles après l'appel de la fonction :
* `infoStepResults` : affiche les différentes valeurs pour la dernière étape
* `stepdiscSummary` : résumé de l’approche de sélection de variables

## Courbe de décroissance de Wilks
La fonction `wilks_decay()` permet de calculer la valeur du lambda de Wilks en fonction du nombre de variables explicatives sélectionnées et d’afficher la courbe du décroissance du lambda de Wilks. Par exemple, s’il y a 40 variables explicatives, les valeurs du lambda seront pour {1, 2, …, 40}.
 
```python
lda.wilks_decay()
lda.figWilksDecay 
```
![Wilks Decay Curve](https://cdn.discordapp.com/attachments/330319467068522506/806299747823517696/wilks_curve.png)

### Attributs disponibles après l'appel de la fonction :
* `infoWilksDecay` : liste des valeurs du lambda de wilks en fonction du nombre de variables sélectionnées
* `figWilksDecay` : courbe de la décroissance du lambda de Wilks

## Reporting
### Fonctions de classement (`fit()`)
Fournit des sorties similaires à la [PROC DISCRIM de SAS](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm), en sortie HTML ou PDF. Nécessite d'importer le module `reporting` du projet.

```python
from reporting import HTML, PDF

HTML().discrim_html_output(lda, outputFileName.html)
PDF().discrim_pdf_output(lda, outputFileName.pdf)
```

### Sélection de variables (`stepdisc()`)
Fournit des sorties similaires à la [PROC STEPDISC de SAS](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm), en sortie HTML.
Nécessite d'importer le module `reporting` du projet.

```python
from reporting import HTML

HTML().stepdisc_html_output(lda, outputFileName.html)
```

----

# Linear Discriminant Analysis (EN)
The program is used to practice linear (predictive) discriminant analysis. It is based on the SAS [DISCRIM](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm) and [STEPDISC](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm) procedures.
The program produces ranking functions, allows to make a prediction from a data set and a variable selection mechanism has been implemented.
Automatic reporting of results is also available, in HTML or PDF format.
Carried out by [AbdDia](https://github.com/AbdDia) and [ASKruchinina](https://github.com/ASKruchinina) and [Valinquish](https://github.com/Valinquish) as part of a project in L3 IDS (Computer Science and Statistics for Data Science) at the Lumière Lyon 2 University (France).

# Usage and explanations
A demo program has been made available (`demo_program.py`) with the corresponding data in the `data` folder.

After loading your data using the [`pandas`](https://pandas.pydata.org) library and finding the name of the categorical variable, the user will have to create an object from the `LinearDiscriminantAnalysis` class of the `discriminant_analysis.py` module by specifying the two parameters (dataset and name of the categorical variable).

```python
from discriminant_analysis import LinearDiscriminantAnalysis as LDA

lda = LinearDiscrimnantAnalysis(dataset, varName)
```

From there, the user will be able to directly call the different methods to perform the calculations he wants, as well as the different attributes of the created object, if he wants to retrieve specific parameters.

### Attributes available after the creation of the :
* `dataset`: the data set
* `classLabel`: the name of the target variable
* `classNames`: the names of the values taken for the target variable
* `varNames`: the names of the explanatory variable
* `n`: the sample size
* `p`: the number of explanatory variables
* `K`: the number of classes
* `V`: the total covariance matrices
* `Vb`: the biased covariance matrices
* `W`: the intra-class covariance matrix
* `Wb`: the biased intra-class covariance matrix

Thus, to display the biased covariance matrix, just do :
```python
lda.Vb 
```

## Discriminant functions
`fit()` produces the discriminant functions.

```python
lda.fit()
```

### Attributes available after calling :
* `intercept_`: the intercept computed by the model
* `coef_`: the coefficients of the model
* `infoFuncClassification`: the values of the classification function

## Prediction function
`predict()` allows to make a prediction from a set of data and returns the vector of the predicted classes.

```python
y_pred = lda.predict(values_a_predict)
```

## Confusion matrix and accuracy score
Having predictions is all very well, but **how can we know or rather measure whether these predictions are correct or not?** The confusion matrix and the accuracy rate help. 

The function `confusion_matrix()` (*confusion matrix*) "provides an unbiased assessment of the model's performance in deployment". It takes the vector of true values of the target variable that were not used during training and the vector of predicted target values. Thus, **one can estimate whether the function is capable of good prediction**. 
The function returns the numerical matrix. The function also returns a graph of the confusion matrix.

The function `accuracy_score()` calculates the prediction rate. It takes as input the true values of the target variable and the predictions and returns the proportion of correct predictions.

```python
lda.confusion_matrix(y_true, y_pred)
lda.accuracy_score(y_true, y_pred)
```

### Attributes available after calling :
* `confusionMatrix`: the confusion matrix
* `confusionMatrixGraph`: graph of the confusion matrix
* `accuracy`: the accuracy's score

## Variable selection (Stepwise Discriminant Analysis)
The function `stepdisc()` **allows to realize a stepwise discriminant analysis**. It allows the user to take **a `forward` approach** or **a `backward` approach**. 
It is necessary to set the stopping threshold (risk).

```python
lda.stepdisc(slentry, method)
```

### Example: forward selection method
```python
lda.stepdisc(0.01, 'forward')
```

### Attributes available after calling :
* `infoStepResults`: displays the different values for the last step
* `stepdiscSummary`: summary of the variable selection approach

## Wilks decay curve
The function `wilks_decay()` is used to calculate the value of Wilks' lambda as a function of the number of selected explanatory variables and to display the curve of the Wilks' lambda decay. For example, if there are 40 explanatory variables, the lambda values will be for {1, 2, ..., 40}.
 
```python
lda.wilks_decay()
lda.figWilksDecay 
```
![Wilks Decay Curve](https://cdn.discordapp.com/attachments/330319467068522506/806299747823517696/wilks_curve.png)

### Attributes available after calling :
* `infoWilksDecay`: list of wilks lambda values according to the number of selected variables
* `figWilksDecay`: Wilks lambda decay curve

## Reporting
### Discriminant functions (`fit()`)
Provides output similar to [SAS PROC DISCRIM](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm), in HTML or PDF output. Requires importing the project `reporting' module.

```python
from reporting import HTML, PDF

HTML().discrim_html_output(lda, outputFileName.html)
PDF().discrim_pdf_output(lda, outputFileName.pdf)
```

### Variable selection (`stepdisc()`)
Provides output similar to [SAS PROC STEPDISC](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm), in HTML output.
Requires importing the `reporting' module of the project.

```python
from reporting import HTML

HTML().stepdisc_html_output(lda, outputFileName.html)
```

# References
1. James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). An Introduction to Statistical Learning : with Applications in R (Springer Texts in Statistics) (1st ed. 2013, Corr. 7th printing 2017 éd.). Springer.
2. Jia Li. Linear Discriminant Analysis [[Diapositives]](http://personal.psu.edu/jol2/course/stat597e/notes2/lda.pdf). Department of Statistics of The Pennsylvania State University. 
3. Ricco Rakotomalala. [Cours Analyse Discriminante](http://eric.univ-lyon2.fr/%7Ericco/cours/cours_analyse_discriminante.html).
4. Ricco Rakotomalala. (2020). [Analyse Discriminante Linéaire sous R](http://eric.univ-lyon2.fr/~ricco/tanagra/fichiers/fr_Tanagra_LDA_MASS_R.pdf).
4. Ricco Rakotomalala. (2020). [Pratique de l’Analyse Discriminante Linéaire](http://eric.univ-lyon2.fr/~ricco/cours/cours/Pratique_Analyse_Discriminante_Lineaire.pdf).