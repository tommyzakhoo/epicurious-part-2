
# Epic Curiousity - Part 2
### Monte-Carlo Cross Validation and Checking the Quality of the Recipe Classifier

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/painting.jpg", width="400">
  <br>
  <i> Still life with basket by Paul Cezanne (1890) </i>
</p>

## Status

Completed.

## Table of contents

- [Tools, Techniques and Concepts](#tools-techniques-and-concepts)
- [Motivation And Project Description](#motivation-and-project-description)
- [Monte Carlo Cross Validation](#monte-carlo-cross-validation)
- [Confusion Matrix](#confusion-matrix)
- [ROC and AUC](#roc-and-auc)
- [Summary and Final Thoughts](#summary-and-final-thoughts)

## Tools, Techniques and Concepts

Python, Monte Carlo Cross Validation, Confusion Matrix, Receiver Operating Characteristic (ROC) Curve, Area Under the Curve (AUC)

## Motivation And Project Description

In part 1 of this project, I built a classifier for labeling Epicurious recipes as breakfast, lunch or dinner that had a 86.25% accuracy on the test set. Simple cross validation was used to evaluate the classifier. In this part of the project, I will implement better cross validation and classifier evaluation.

## Monte-Carlo Cross Validation

In part 1, cross validation was done using a simple "holdout" method: 291 recipes were selected at random to be in a test set, while the remaining 2000 make up the training set. 

Here, I implemented a restricted version of [Monte-Carlo cross validation](https://en.wikipedia.org/wiki/Cross-validation_(statistics)#Repeated_random_sub-sampling_validation) in which I repeat the holdout method with a new, randomly selected, test set each time. A score (number of correctly classified recipes out of 291), is also computed each time. I then took the average of these scores.

The code from the part 1 is easily put inside a loop or a function and repeated. I did this for 1000 times and gathered the scores in a csv file here: [scores_1000.csv](scores_1000.csv). 

```python
import matplotlib.pyplot as plt # library for plotting data
import pandas as pd

plt.rcParams.update({'font.size': 22}) # font size in histogram plot

scores = pd.read_csv('scores_1000.csv',header=None) # load the 1000 scores
scores.hist() # plot a histogram

plt.xlabel('Scores')
plt.ylabel('Counts')
plt.title('')
plt.show() # customize axes labels and title, before drawing on screen
```
Pandas dataframes have their own method for plotting histograms. Using the code above, I produced a histogram for my 1000 scores below.

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/histogram.png", width="600">
</p>

The average for these 1000 scores is 252.812, which is an average accuracy of 87.17%. I built the classifier using a simple method, so there are definitely room for improvement, but this is not bad for a start!

## Confusion Matrix

The [confusion matrix](https://en.wikipedia.org/wiki/Confusion_matrix) is a way of presenting the performance of our model with a table. This table is also call a "matrix" in mathematics.

The rows i of this matrix are the predicted class of data points, while the columns j are the actual labels. Thus, number in the (i,j)-th entry of this matrix is the number of recipes that are classified as i while they are actually j. For this project, the classes or labels are breakfast, lunch or dinner.

Note that a diagonal (i,i)-th entry of the matrix shows the number of recipes correctly classified as label i. If my classifier somehow has perfect accuracy, all non-diagonal entries would be zero, and the sum of diagonal entries would equal the number of data points.

What are these data points though? I chose to run the Monte-Carlo Cross Validation an arbitrary 20 number of times for illustration, and built the confusion matrix based on the classification and true labels of all the test sets combined. This is done using the confusion_matrix class from sklearn. Code and output are show below.

```python
import pandas as pd
from sklearn.metrics import confusion_matrix # confusion matrix class

true_labels = pd.read_csv('true_labels.csv',header=None)
classified_labels = pd.read_csv('classified_labels.csv',header=None)

confusion_matrix(true_labels,classified_labels)
```
<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/confusion_matrix.png">
</p>

I computed these from the confusion matrix: number of data points = 5820, accuracy = (982 + 875 + 3219) / 5820 = 87.2164%, misclassification rate = 1 - accuracy = 12.7835%. Since this is multi-class classification, I cannot calculate true/false positive rates, specificity, precision, prevalence etc.

## ROC and AUC

In this section, I will be using the Receiver Operating Characteristic (ROC) Curve to evaluate my classifier. Since the usual ROC curve is for binary classification, but what I have done here is one-vs-all multi-class classification, I am going to treat plot three ROC curves.

The ROC curve is a plot of the true positive rate vs. the false positive rate as the discrimination threshold is increased. For my logistic regression model, the discrimination threshold is the theshold predicted probability beyond which a recipe would be classified as breakfast, lunch or dinner. Note that so far I have been using the "default" 0.5 threshold, which simply means the label with the highest probability is chosen to be the predicted label.

Again, the code from part 1 is easily modified to do this. For example, once I modify the code to give me the true labels for breakfast recipes in the test set (breakfast_true), and the probabilities produced by the logistic regression model (breakfast_proba), I could use the code below to get the false positive rates (fpr), true positive rates (tpr) for various discrimination thresholds.

```python

import matplotlib.pyplot as plt # for visualizing the results
from sklearn import metrics # for roc_curve and auc methods

fpr, tpr, thresholds = metrics.roc_curve(breakfast_true,breakfast_proba,pos_label=1)

plt.plot(fpr,tpr) # plot the false positive rates (fpr) on the x-axis, and true positive rates (tpr
plt.show()
```
<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/B_roc.png", width="600">
</p>

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/L_roc.png", width="600">
</p>

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/D_roc.png", width="600">
</p>

The ROC curves for each meal type is plotted above. It has a simple interpretation. 

For example, in the case of breakfast, if I classify very few recipes as breakfast, by setting the threshold to something high like 0.90 probability, then I will make a lot less mistakes. So,the false positive rate will be low. However, the tradeoff is that very few recipes that are actually breakfast will get labeled as breakfast (true positive) too.

On the other hand, if I set the threshold for being labeled breakfast to a very low value, say 0.05 probability, I will make a lot of mistakes (false positives). However, a lot of the breakfast recipes will be labeled correctly as well (true positives).

The ROC curve is a visualization of this tradeoff. The "Area Under the Curve" (AUC) refers to the area under the ROC curve. This is an indicator of how good the model is at making this tradeoff. Higher AUC means better model.

Once I have the fpr and tpr from before, all I have to do to get the AUC is to use the line of code below.

```python
metrics.auc(fpr, tpr)
```
The AUC for all 30 ROC curves, to four significant figures, are listed in the table below.

| Breakfast | Lunch       | Dinner       |
| :-:       | :-:         | :-:          |
|0.9818     |0.9392       |0.9450        |
|0.9772     |0.9141       |0.9495        |
|0.9644     |0.9088       |0.9406        |
|0.9647     |0.9096       |0.9503        |
|0.96788    |0.9328       |0.9466        |
|0.9824     |0.9181       |0.9443        |
|0.9656     |0.9131       |0.9456        |
|0.9638     |0.9285       |0.9571        |
|0.9582     |0.9352       |0.9400        |
|0.9684     |0.9428       |0.9500        |

I would say these are very good numbers! Surprising for such a simpe model. 

Perhaps breakfast, lunch, dinner recipes are just relatively easy to tell apart. The AUC numbers are best for breakfast recipes, which is not surprising since I have seen in part 1 that this population prefers to consume certain things like eggs only during breakfast. Lunch fared the worst, probably because many lunch dishes can also be eaten for dinner, and bread can be used in both lunch and breakfast recipes.

## Summary and Final Thoughts

Again, the following is a short summary of what I did here.

- Implemented Monte-Carlo cross validation.
- Obtained 87.17% average accuracy.
- Evaluated the model with a confusion matrix.
- Plotted ROC curve and calculated AUC values for the model.
- Obtained good AUC numbers, ranging from 0.9088 to 0.9818.

This concludes my small project. The Epicurious dataset contains an abundant of data, and this project have only scratched the tip of the iceberg. I look forward to doing more with it!

<!--

```python
s = "Python syntax highlighting"
print s
```
-->

