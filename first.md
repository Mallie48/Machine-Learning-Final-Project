---
title: "Final Project-Machine Learning Course"
author: "Maliheh Shomali"
date: "February 21, 2018"
output: html_document
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## Introduction
The goal of this project is to predict the manner of a group of enthusiasts who take measurements about themselves regularly using devices such as Jawbone Up, Nike FuelBand, and Fitbit.
This is the "classe" variable in the training set. You may use any of the other variables to predict with. I created a report describing how I built the model, how I used cross validation, what I thought the expected out of sample error is, and why I made the choices I did. I also used my prediction model to predict 20 different test cases. Decision tree was used to create the model. 

## Model Used
The exacted outcome variable is **classe**, a 5 level of factor variable. In this dataset, participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in 5 different fashion: 

  Class A: exactly according to the specification
  
  Class B: throwing the elbows to the front 
  
  Class c: lifting the dumbbell only halfway  
  
  Class D: lowering the dumbbell only halfway
  
  Class E:  throwing the hips to the front.

Class A correspond to the specified execution of the exercise, while the other 4 classes correspond to common mistakes. 

## Data
The training data for this project are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv

The test data are available here:
https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv

The data for this project come from this source: http://web.archive.org/web/20161224072740/http:/groupware.les.inf.puc-rio.br/har. 

### Lodaing libraries
```{r}
library(rpart.plot)
library(lattice)
library(ggplot2)
library(caret)
library(rpart)
library(randomForest)
```


### Data Analysis
Loading the data form the provided link and identifying the missing data (NA, " ", and #DIV/0) and labeling  them as "NA".

```{r Weight Lifting Exercise}
# Download the dataset
url.train <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
url.test <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

# Load the training and test sets
training_set <- read.csv(url(url.train), na.strings = c("NA", "", "#DIV0!"))
testing_set <- read.csv(url(url.test), na.strings = c("NA", "", "#DIV0!"))

# Exploring the data
dim(training_set)
dim(testing_set)
table (training_set$classe)
head(colnames(training_set))
tail(colnames(training_set))

# Removing all variables containing NA values
training_set <- training_set[, colSums(is.na(training_set)) == 0]
testing_set <- testing_set[, colSums(is.na(testing_set)) == 0]


# Removing the varibales that are irrelevent to the prediction (column 1 to 7)
training_set <- training_set[, -c(1:7)]
testing_set <- testing_set[, -c(1:7)]

# Excluding the "classe" in training dataset.
training_set$classe <- factor(training_set$classe)

# Partitioning the dataset into training (60%), testing (20%) and validation part (20%).
set.seed(1235)
training_partition<- createDataPartition(y = training_set$classe, p = 0.7, list = F)
training_dataset <- training_set[training_partition,]
testing_dataset <- training_set[-training_partition,]


dim(training_dataset)
dim(testing_dataset)
```



### Prediction by decision tree model
```{r}
decision_tree_model <- rpart(classe ~ ., data = training_dataset, method = "class")

decision_tree_prediction <- predict(decision_tree_model, testing_dataset, type = "class")

# Plot Decision Tree
rpart.plot(decision_tree_model, main = "Decision Tree")

# Confusion matrix to test the results
confusionMatrix(decision_tree_prediction, testing_dataset$classe)
```

### Prediction by random forest model
```{r}
random_forest_model <- randomForest(classe ~. , data = training_dataset, method = "class")
random_forest_prediction <- predict(random_forest_model, testing_dataset, type = "class")

confusionMatrix(random_forest_prediction, testing_dataset$classe)
```

```{r}
# System information for reproducing the project
sessionInfo()
```

## Conclusion
Based on the result, the random forest model is chosen for the prediction, since the accuracy of random forest model (0.9951) is higher than of the decision tree model (0.7468).
