# practical-machine-learning-project
output: html_document
    
---

Assignment 
Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it.  

Objective
This report objective is to predict the class of exercise an individual performed while wearing fitness tracker bu using machine learning algorithm.

loading library  
```{r, warning = False, include = False}
library(caret)
library(rpart)
library(rpart.plot)
library(randomForest)
library(corrplot)

Acquiring and cleaning the data

training.file   <- './data/pml-training.csv'
test.cases.file <- './data/pml-testing.csv'
training.url    <- 'http://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv'
test.cases.url  <- 'http://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv'

if (!file.exists("./data")) {
  dir.create("./data")
}
if (!file.exists(trainFile)) {
  download.file(trainUrl, destfile=trainFile, method="curl")
}
if (!file.exists(testFile)) {
  download.file(testUrl, destfile=testFile, method="curl")
}
```  
Crossvalidation
Cross-validation
cross-validation will be performed by splitting the training data in training (75%) and testing (25%) data.

```{r datasplitting, echo=TRUE, results='hide'}
subSamples <- createDataPartition(y=training$classe, p=0.75, list=FALSE)
subTraining <- training[subSamples, ] 
subTesting <- training[-subSamples, ]
```
Decision Tree
```{r}
set.seed(12345)
DTmodel <- rpart(classe ~ ., data = myTraining, method = "class")
fancyRpartPlot(DTmodel)
DTpredict <- predict(DTmodel, myTesting, type = "class")
confusionMatrix(DTpredict, myTesting$classe)

Ramdom forest predictions

```{r}
RFmodel <- randomForest(classe ~ ., data = myTraining)
RFpredict <- predict(RFmodel, myTesting, type = "class")
confusionMatrix(RFpredict, myTesting$classe)

Result

The confusion matrices show, that the Random Forest algorithm performens better than decision trees. The accuracy for the Random Forest model was 0.995 (95% CI: (0.993, 0.997)) compared to 0.739 (95% CI: (0.727, 0.752)) for Decision Tree model. The random Forest model is choosen.

Expected out-of-sample error
The expected out-of-sample error is estimated at 0.005, or 0.5%. The expected out-of-sample error is calculated as 1 - accuracy for predictions made against the cross-validation set. Our Test data set comprises 20 cases. With an accuracy above 99% on our cross-validation data, we can expect that very few, or none, of the test samples will be missclassified.

Submission
In this section the files for the project submission are generated using the random forest algorithm on the testing data.

```{r submission, echo=TRUE}
# Perform prediction
predictSubmission <- predict(modFitRF, testing, type="class")
predictSubmission
# Write files for submission
pml_write_files = function(x){
  n = length(x)
  for(i in 1:n){
    filename = paste0("./data/submission/problem_id_",i,".txt")
    write.table(x[i],file=filename,quote=FALSE,row.names=FALSE,col.names=FALSE)
  }
}
pml_write_files(predictSubmission)

