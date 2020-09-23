# Machine_Learning---Exercise_Activity_Prediction
A group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it.

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, the goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. 

Six male participants aged between 20 and 28 years old with little weight lifting experience were asked to perform one set of 10 repetitions of the Unilateral Dumbbell (1.25kg) Biceps Curl in five different fashions: 

*  Class A - exactly according to the specification , 
*  Class B - throwing the elbows to the front, 
*  Class C - lifting the dumbbell only halfway, 
*  Class D - lowering the dumbbell only halfway, 
*  Class E - throwing the hips to the front.

Class A corresponds to the specified execution of the exercise, while the other 4 classes correspond to common mistakes. Participants were supervised by an experienced weight lifter to make sure the execution complied to the manner they were supposed to simulate.

More information is available from the website here: [Human Activity Recognition info](http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har) (see the section on the Weight Lifting Exercise Dataset).

#### The goal of this project is to predict the manner in which they did the exercise.

This is the "classe" variable in the training set. This report will consist detail on:

1.  How to build the model?
2.  How to use cross validation? 
3.  What the expected out of sample error is, 
4.  Why the choices are being made.

#### What steps to achieve the above objective?
1.  Perform Exploratory Data Analysis
2.  Slice data from training data into two parts for training and cross validation checking
3.  Build 3 different model namely Random Forrest, Gradient Boosted Model and Linear Discriminant Analysis.
4.  Check which model has the highest accuracy and what is the expected out sample error for that model.
