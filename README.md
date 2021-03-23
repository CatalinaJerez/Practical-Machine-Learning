# Practical-Machine-Learning

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%

The results are published in: [https://rpubs.com/Jerez/Practical_Machine_Learning](https://rpubs.com/Jerez/Practical_Machine_Learning)

%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%%


**from coursera project**

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. More information is available from the [website](http://groupware.les.inf.puc-rio.br/har) (see the section on the Weight Lifting Exercise Dataset).

### Data 

- The training data for this project are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv](https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv)

- The test data are available here: [https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv](https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv)

- The data for this project come from this source: [http://groupware.les.inf.puc-rio.br/har](http://groupware.les.inf.puc-rio.br/har). If you use the document you create for this class for any purpose please cite them as they have been very generous in allowing their data to be used for this kind of assignment.


### Project goal

The goal of your project is to predict the manner in which they did the exercise. This is the “classe” variable in the training set. You may use any of the other variables to predict with. You should create a report describing how you built your model, how you used cross validation, what you think the expected out of sample error is, and why you made the choices you did. You will also use your prediction model to predict 20 different test cases.

## How to do

### Getting the data 

````{r, message = FALSE, warning = FALSE}
set.seed(3)

# Library 
library(caret)
library(tidyverse)
library(randomForest)
library(rpart)
library(rpart.plot)


# Url's

url.train <-"https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
url.test <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

# Preprocessing and cleaning

# Remove redundant variables
training <- read.csv(url(url.train), na.strings=c("NA","#DIV/0!",""))
testing  <- read.csv(url(url.test), na.strings=c("NA","#DIV/0!",""))

# Delete missing values
training <- training[,colSums(is.na(training)) == 0]
testing  <- testing[,colSums(is.na(testing)) == 0]

# Some variables are irrelevant for the project: 
# user_name, raw_timestamp_part_1, raw_timestamp_part_,2 cvtd_timestamp, 
# new_window, and  num_window (columns 1 to 7). 
# We can delete these variables.
training <- training[,-c(1:7)]
testing  <- testing[,-c(1:7)]

# Partioning the training set into two
# 60% for data.train, 40% for data.test

inTrain    <- createDataPartition(y=training$classe, p=0.6, list=FALSE)
data.train <- training[inTrain,  ] 
data.test  <- training[-inTrain, ]

# Dimensions
dim(data.train); dim(data.test)

# The variable "classe" contains 5 levels: A, B, C, D and E. 
# A plot of the outcome variable will allow us to see the frequency of each levels 
# in the train data set and compare one another.

plot(data.train$classe, col="grey50", main="Levels of the variable classe within the train data set", xlab="Classe levels", ylab="Frequency")
`````

### Prediction model I: Decision tree
````{r}
# Model
tree.model <- rpart(classe ~ ., data = data.train, method="class")

# Predicting
tree.pred  <- predict(tree.model, data.test, type = "class")

# Plot of the Decision Tree
rpart.plot(tree.model, main="Classification Tree", extra=102, under=TRUE, faclen=0)

# Test results on the test data set:
confusionMatrix(tree.pred, data.test$classe)

`````
