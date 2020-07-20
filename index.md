##### The platform specification used:

<table>
<thead>
<tr class="header">
<th>Spec</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>OS</td>
<td>Windows 10 Pro - 64 bit</td>
</tr>
<tr class="even">
<td>CPU</td>
<td>AMD Ryzen 5 - 3400G</td>
</tr>
<tr class="odd">
<td>RAM</td>
<td>16GB DDR4 3000MHz</td>
</tr>
<tr class="even">
<td>Storage</td>
<td>500GB SSD - M.2 NVMe (PCIe)</td>
</tr>
<tr class="odd">
<td>Tool</td>
<td>RStudio</td>
</tr>
</tbody>
</table>

Human Activity Recognition
--------------------------

### 1. Background

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now
possible to collect a large amount of data about personal activity
relatively inexpensively. These type of devices are part of the
quantified self movement – a group of enthusiasts who take measurements
about themselves regularly to improve their health, to find patterns in
their behavior, or because they are tech geeks. One thing that people
regularly do is quantify how much of a particular activity they do, but
they rarely quantify how well they do it. In this project, the goal will
be to use data from accelerometers on the belt, forearm, arm, and
dumbell of 6 participants. They were asked to perform barbell lifts
correctly and incorrectly in 5 different ways.

Six male participants aged between 20 and 28 years old with little
weight lifting experience were asked to perform one set of 10
repetitions of the Unilateral Dumbbell (1.25kg) Biceps Curl in five
different fashions:

-   Class A - exactly according to the specification ,
-   Class B - throwing the elbows to the front,
-   Class C - lifting the dumbbell only halfway,
-   Class D - lowering the dumbbell only halfway,
-   Class E - throwing the hips to the front.

Class A corresponds to the specified execution of the exercise, while
the other 4 classes correspond to common mistakes. Participants were
supervised by an experienced weight lifter to make sure the execution
complied to the manner they were supposed to simulate.

More information is available from the website here: [Human Activity
Recognition
info](http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har)
(see the section on the Weight Lifting Exercise Dataset).

#### The goal of this project is to predict the manner in which they did the exercise.

This is the “classe” variable in the training set. This report will
consist detail on:

1.  How to build the model?
2.  How to use cross validation?
3.  What the expected out of sample error is,
4.  Why the choices are being made.

#### What steps to achieve the above objective?

1.  Perform Exploratory Data Analysis
2.  Slice data from training data into two parts for training and cross
    validation checking
3.  Build 3 different model namely Random Forrest, Gradient Boosted
    Model and Linear Discriminant Analysis.
4.  Check which model has the highest accuracy and what is the expected
    out sample error for that model.

### 2. Exploratory Data Analysis

First of all we need to download the train and test data set from the
link provided.

    ## Set the URL and file name for training dataset supplied
    trainUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
    trainFile <- "./data/ML-Train.csv"

    ## Set the URL and file name for testing dataset supplied
    testUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
    testFile <- "./data/ML-Test.csv"

    if (!file.exists("./data")) {
        dir.create("./data")
    }
    if (!file.exists(trainFile)) {
        download.file(trainUrl, destfile = trainFile, method = "curl")
    }
    if (!file.exists(testFile)) {
        download.file(testUrl, destfile = testFile, method = "curl")
    }

After the files are download, we need to read the CSV files and explore
in detail the training and testing dataset using the method here.

    ##Various indicators of missing data (i.e., “NA”, “#DIV/0!” and “”) are all set to NA so they can be processed
    trainRaw <- read.csv(trainFile, na.strings=c("NA","#DIV/0!",""))
    testRaw <- read.csv(testFile, na.strings=c("NA","#DIV/0!",""))

    dim(trainRaw) ##Observations and variables of raw train dataset
    dim(testRaw) ##Observations and variables of raw test dataset
    ##str(trainRaw) ##Structure of the raw train dataset 
    ##str(testRaw) ##Structure of the raw test dataset

    table(trainRaw$classe) ##Tabulation of raw train data with the variable (Classe) and its frequency
    sum(complete.cases(trainRaw)) ##Sum of complete rows of a data frame

    ## [1] 19622   160
    ## [1]  20 160
    ## 
    ##    A    B    C    D    E 
    ## 5580 3797 3422 3216 3607 
    ## [1] 0

The raw train data contains 19622 observations and 160 variables.
Whereas the test raw data contains 20 observations and 160 variables.  
From the observation above we can see that there is no complete data set
(without missing values).

We need to tidy and clean the data to improve the speed and accuracy of
the model. First we remove all the data with NA values followed by
removing columns that are not relevant to our model prediction.

Below is the method used to remove NA values in both datasets.

    ## Remove colums with NAs
    trainClean1 <- trainRaw[, colSums(is.na(trainRaw)) == 0]
    testClean1 <- testRaw[, colSums(is.na(testRaw)) == 0]

    ## str(trainClean1) ##Structure of the raw train dataset after removing NA values
    ## str(testClean1) ##Structure of the raw test dataset after removing NA values
    ## dim(trainClean1) ##Observations and variables of raw train dataset after
    ## removing NA values dim(testClean1) ##Observations and variables of raw test
    ## dataset after removing NA values

    table(trainClean1$classe)  ##Tabulation of train data with the variable (Classe) and its frequency
    sum(complete.cases(trainClean1))  ##Sum of complete rows of a data frame

    ## 
    ##    A    B    C    D    E 
    ## 5580 3797 3422 3216 3607 
    ## [1] 19622

The complete cases (without missing values) is now complete at 19622
rows while maintaining the same amount frequency in the variable
“Classe”. Then we proceed with removing unneccassary columns data such
as X, user\_name, raw\_timestamp\_part\_1, raw\_timestamp\_part\_2,
cvtd\_timestamp, new\_window and num\_window using method below.

    ## Remove the first 7 columns as they are not necessary for modeling purpose
    trainFinal <- trainClean1[, -c(1:7)]
    testFinal <- testClean1[, -c(1:7)]

    ## str(trainFinal) ##Structure of the final train dataset str(testFinal)
    ## ##Structure of the final test dataset

    dim(trainFinal)  ##Observations and variables of final train dataset
    dim(testFinal)  ##Observations and variables of final test dataset

    ## [1] 19622    53
    ## [1] 20 53

**The final clean train data contains 19622 observations and 53
variables. Whereas the test raw data contains 20 observations and 53
variables. We have removed a total of 107 variables from the raw data to
the final data of both training and test datasets.**

### 3. Slice data from training data for cross validation checking

The training data will be sliced into two parts. This first is the
training set with 70% of the original training data and it will be used
to train the model. The remaining 30% data is a validation set used to
validate model performance. Below is the method carried to slice the
training data.

    set.seed(12345)  # For reproducibile purpose

    # Partition rows into training-70% and validation-30%
    inTrain <- createDataPartition(trainFinal$classe, p = 0.7, list = FALSE)
    trainData <- trainFinal[inTrain, ]
    validateData <- trainFinal[-inTrain, ]

    ## dim(trainData) ##Observations and variables of final partitioned train dataset
    ## dim(validateData) ##Observations and variables of final partitioned validation
    ## dataset

The partitioned train data contains 13737 observations and 53 variables.
Whereas the validation data contains 5885 observations and 53 variables.

### 4. Building Model

We will be building three different models namely Random Forrest,
Gradient Boosted Model and Linear Discriminant Analysis. Please click
the link for the detail of [Random
Forest](https://en.wikipedia.org/wiki/Random_forest), [Gradient Boosted
Model](https://en.wikipedia.org/wiki/Gradient_boosting) and [Linear
Discriminant
Analysis](https://machinelearningmastery.com/linear-discriminant-analysis-for-machine-learning/)

    ## Random Forest Model Fit
    modRf <- train(classe ~ ., method = "rf", data = trainData, trControl = trainControl(method = "cv"), 
        number = 3)

    ## Gradient Boosted Model Fit
    ModGbm <- train(classe ~ ., method = "gbm", data = trainData, verbose = FALSE)

    ## Linear Discriminant Analysis Model Fit
    modLda <- train(classe ~ ., method = "lda", data = trainData)

    ## Naive Bayes Model Fit modNb <- train(classe~., method='nb', data=trainData)

The model fit using the training data is tested against the validation
data. Predicted values for the validation data are then compared to the
actual values. This allows forecasting the accuracy and overall
out-of-sample error, which indicate how well the model will perform with
other data.

    predRf <- predict(modRf, newdata = validateData)

    predGbm <- predict(ModGbm, newdata = validateData)

    predLda <- predict(modLda, newdata = validateData)

    ## predNb <- predict(modNb, newdata=validateData)

After validation is being carried out, we will look at the accuracy of
each model.

    accuracy <- rbind(confusionMatrix(predRf, as.factor(validateData$classe))$overall["Accuracy"], 
        confusionMatrix(predGbm, as.factor(validateData$classe))$overall["Accuracy"], 
        confusionMatrix(predLda, as.factor(validateData$classe))$overall["Accuracy"])
    row.names(accuracy) <- c("RF", "GBM", "LDA")

    accuracy

    ##      Accuracy
    ## RF  0.9952421
    ## GBM 0.9621071
    ## LDA 0.7070518

Based on results, the Random Forest prediction is far better than the
other models.

### 5. Conclusion

From the accuracy test that we have carried out above, Random Forest
model has higher accuracy of 99.52421% with estimated expected out of
sample error of 0.47579% Therefore Random Forest model will be used to
predict the test cases.

### 6. Predicting Test Result

Information on different prediction classes on the test cases

-   Class A - exactly according to the specification ,
-   Class B - throwing the elbows to the front,
-   Class C - lifting the dumbbell only halfway,
-   Class D - lowering the dumbbell only halfway,
-   Class E - throwing the hips to the front.

<!-- -->

    predTest <- predict(modRf, newdata = testFinal)
    predTest

    ##  [1] B A B A A E D B A A B C B A E E A B B B
    ## Levels: A B C D E
