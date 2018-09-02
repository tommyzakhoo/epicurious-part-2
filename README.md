
# Epic Curiousity - Part 2
### Monte-Carlo Cross Validation and Checking the Quality of the Recipe Classifier

<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious/master/painting.jpg", width="400">
  <br>
  <i> Still life with basket by Paul Cezanne (1890) </i>
</p>

## Status
Work in progress. Last update: 2 September 2018.

### Table of contents

- [Motivation And Project Description](#motivation-and-project-description)
- [Monte Carlo Cross Validation](#monte-carlo-cross-validation)
- [Confusion Matrix](#confusion-matrix)
- [ROC, AUC, Gini Coefficient](#roc-auc-gini-coefficient)
- [Future Directions](#future-directions)

## Tools, Techniques and Concepts

Python, Monte Carlo Cross Validation, Confusion Matrix, Receiver Operating Characteristic (ROC) Curve, Area Under the Curve (AUC), Gini Index

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
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/histogram.png", width="400">
</p>

The average for these 1000 scores is 252.812, which is an average accuracy of 87.17%. I built the classifier using a simple method, so there are definitely room for improvement, but this is not bad for a start!

## Confusion Matrix

The [confusion matrix](https://en.wikipedia.org/wiki/Confusion_matrix) is a way of presenting the performance of our model with a table. This table is also call a "matrix" in mathematics.

The rows i of this matrix are the predicted class of data points, while the columns j are the actual labels. Thus, number in the (i,j)-th entry of this matrix is the number of recipes that are classified as i while they are actually j. For this project, the classes or labels are breakfast, lunch or dinner.

Note that a diagonal (i,i)-th entry of the matrix shows the number of recipes correctly classified as label i. If my classifier somehow has perfect accuracy, all non-diagonal entries would be zero, and the sum of diagonal entries would equal the number of data points.

What are these data points though? I chose to run the Monte-Carlo Cross Validation an arbitrary 20 number of times, and built the confusion matrix based on the classification and true labels of all the test sets combined. This is done using the confusion_matrix class from sklearn. Code and output are show below.

```python
import pandas as pd
from sklearn.metrics import confusion_matrix # confusion matrix class

true_labels = pd.read_csv('true_labels.csv',header=None)
classified_labels = pd.read_csv('classified_labels.csv',header=None)

confusion_matrix(true_labels,classified_labels)
```
<p align="left">
  <img src="https://raw.githubusercontent.com/tommyzakhoo/epicurious-part-2/master/confusion_matrix.png", width="400">
</p>

## ROC, AUC, Gini Coefficient



## Future Directions

This Epicurious dataset

<!--

```python
s = "Python syntax highlighting"
print s
```

This project is a part of the [Data Science Working Group](http://datascience.codeforsanfrancisco.org) at [Code for San Francisco](http://www.codeforsanfrancisco.org).  Other DSWG projects can be found at the [main GitHub repo](https://github.com/sfbrigade/data-science-wg).

#### -- Project Status: [Active, On-Hold, Completed]

## Project Intro/Objective
The purpose of this project is ________. (Describe the main goals of the project and potential civic impact. Limit to a short paragraph, 3-6 Sentences)

### Partner
* [Name of Partner organization/Government department etc..]
* Website for partner
* Partner contact: [Name of Contact], [slack handle of contact if any]
* If you do not have a partner leave this section out

### Methods Used
* Inferential Statistics
* Machine Learning
* Data Visualization
* Predictive Modeling
* etc.

### Technologies
* R 
* Python
* D3
* PostGres, MySql
* Pandas, jupyter
* HTML
* JavaScript
* etc. 

## Project Description
(Provide more detailed overview of the project.  Talk a bit about your data sources and what questions and hypothesis you are exploring. What specific data analysis/visualization and modelling work are you using to solve the problem? What blockers and challenges are you facing?  Feel free to number or bullet point things here)

## Needs of this project

- frontend developers
- data exploration/descriptive statistics
- data processing/cleaning
- statistical modeling
- writeup/reporting
- etc. (be as specific as possible)

-->
