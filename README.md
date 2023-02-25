# Linear Discriminant Analysis
The program is used to practice linear (predictive) discriminant analysis. It is based on the SAS [DISCRIM](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm) and [STEPDISC](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm) procedures.
The program produces ranking functions, allows to make a prediction from a data set and a variable selection mechanism has been implemented.
Automatic reporting of results is also available, in HTML or PDF format.

Carried out by [AbdDia](https://github.com/AbdDia) and [ASKruchinina](https://github.com/ASKruchinina) and [Dataymeric](https://github.com/dataymeric) as part of a project in L3 IDS (Bachelor in Computer Science and Statistics for Data Science) at the Université Lumière Lyon 2 (France).

## Usage and explanations
A demo program has been made available (`demo_program.py`) with the corresponding data in the `data` folder.

After loading your data using the `pandas` library and finding the name of the categorical variable, the user will have to create an object from the `LinearDiscriminantAnalysis` class of the `discriminant_analysis.py` module by specifying the two parameters (dataset and name of the categorical variable).

```python
from discriminant_analysis import LinearDiscriminantAnalysis as LDA

lda = LDA(dataset, varName)
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
* `V`: the total covariance matrix
* `Vb`: the biased covariance matrix
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

![Confusion Matrix](https://cdn.discordapp.com/attachments/1079151811388260352/1079154048906825739/confusion_matrix.png)

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
![Wilks Decay Curve](https://cdn.discordapp.com/attachments/1079151811388260352/1079151908306042931/wilks_curve.png)

### Attributes available after calling :
* `infoWilksDecay`: list of wilks lambda values according to the number of selected variables
* `figWilksDecay`: Wilks lambda decay curve

## Reporting
### Discriminant functions (`fit()`)
Provides output similar to [SAS PROC DISCRIM](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_discrim_sect029.htm), in HTML or PDF output. Requires importing the project `reporting` module.

```python
from reporting import HTML, PDF

HTML().discrim_html_output(lda, outputFileName.html)
PDF().discrim_pdf_output(lda, outputFileName.pdf)
```

### Variable selection (`stepdisc()`)
Provides output similar to [SAS PROC STEPDISC](https://support.sas.com/documentation/cdl/en/statug/63962/HTML/default/viewer.htm#statug_stepdisc_sect017.htm), in HTML output.
Requires importing the `reporting` module of the project.

```python
from reporting import HTML

HTML().stepdisc_html_output(lda, outputFileName.html)
```

# References
1. James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). An Introduction to Statistical Learning : with Applications in R (Springer Texts in Statistics) (1st ed. 2013, Corr. 7th printing 2017 éd.). Springer.
2. Jia Li. Linear Discriminant Analysis [[Diapositives]](http://personal.psu.edu/jol2/course/stat597e/notes2/lda.pdf). Department of Statistics of The Pennsylvania State University. 
3. Ricco Rakotomalala. [Cours Analyse Discriminante](https://cours-machine-learning.blogspot.com/p/analyse-discriminante.html).
4. Ricco Rakotomalala. (2020). [Analyse Discriminante Linéaire sous R](http://eric.univ-lyon2.fr/ricco/tanagra/fichiers/fr_Tanagra_LDA_MASS_R.pdf).
4. Ricco Rakotomalala. (2020). [Pratique de l’Analyse Discriminante Linéaire](https://eric.univ-lyon2.fr/ricco/cours/cours/Pratique_Analyse_Discriminante_Lineaire.pdf).
