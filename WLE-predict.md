# Weight Lifting Exercise Prediction
Yvette Janecek, May 26, 2016  
###Overview  
This project will predict the manner in which an exercise is done.  The data for this project is the Weight Lifting Exercise (WLE) Dataset from http://groupware.les.inf.puc-rio.br/har.  



###PreProcessing  
The structure of the data was viewed.  Observations which were blank or included divion by zero were replaced with "NA."  Then observations with NA were removed.  Since the first seven variables were relating to subject and time identification, they were removed from the data.  (This included the variables X, user-name, raw-timestamp-part1, raw-timestamp-part2, cvtd-timestamp, new-window, and num-window.)  


```r
str(pmltraining)
pmltraining[1:7, 1:5]
pmltraining <- read.csv("pml-training.csv",na.strings=c("NA","#DIV/0!", ""))
data <- pmltraining[8:ncol(pmltraining)]
data <- data[,apply(data,2,function(x) !any(is.na(x)))]
str(data)
dim <- dim(data)
```

Once complete, there were still 19622 observations of 53 variables.  The remaining variables are:  

```
##  [1] "roll_belt"            "pitch_belt"           "yaw_belt"            
##  [4] "total_accel_belt"     "gyros_belt_x"         "gyros_belt_y"        
##  [7] "gyros_belt_z"         "accel_belt_x"         "accel_belt_y"        
## [10] "accel_belt_z"         "magnet_belt_x"        "magnet_belt_y"       
## [13] "magnet_belt_z"        "roll_arm"             "pitch_arm"           
## [16] "yaw_arm"              "total_accel_arm"      "gyros_arm_x"         
## [19] "gyros_arm_y"          "gyros_arm_z"          "accel_arm_x"         
## [22] "accel_arm_y"          "accel_arm_z"          "magnet_arm_x"        
## [25] "magnet_arm_y"         "magnet_arm_z"         "roll_dumbbell"       
## [28] "pitch_dumbbell"       "yaw_dumbbell"         "total_accel_dumbbell"
## [31] "gyros_dumbbell_x"     "gyros_dumbbell_y"     "gyros_dumbbell_z"    
## [34] "accel_dumbbell_x"     "accel_dumbbell_y"     "accel_dumbbell_z"    
## [37] "magnet_dumbbell_x"    "magnet_dumbbell_y"    "magnet_dumbbell_z"   
## [40] "roll_forearm"         "pitch_forearm"        "yaw_forearm"         
## [43] "total_accel_forearm"  "gyros_forearm_x"      "gyros_forearm_y"     
## [46] "gyros_forearm_z"      "accel_forearm_x"      "accel_forearm_y"     
## [49] "accel_forearm_z"      "magnet_forearm_x"     "magnet_forearm_y"    
## [52] "magnet_forearm_z"     "classe"
```

###Modeling and Prediction  
The model was developed using the Random Forests method.  This method was selected because it is "unexcelled in accuracy among current algorithms," as described by the authors of the method.  Though unnecessary to develop the model in this method, the data was split into a set for training the model and a set for testing the model.  The testing set was created simply to show the high accuracy of this model.  Additional information on the Random Forests method of can be found at http://www.stat.berkeley.edu/~breiman/RandomForests/cc_home.htm.  
The model can be used to predict the class of an exercise.  See http://groupware.les.inf.puc-rio.br/har for more information.  


```r
index <- createDataPartition(data$classe, p=0.75)[[1]]
trainSet <- data[index,]
testSet <- data[-index,]
trainSet$classe <- factor(trainSet$classe)
testSet$classe <- factor(testSet$classe)
dim(trainSet)
dim(testSet)
set.seed(42)
model <- randomForest(classe~., data=trainSet)
predict <- predict(model, newdata=testSet)
predict.cm <- confusionMatrix(predict, testSet$classe)
accuracy <- 100*(predict.cm$overall["Accuracy"])
```

The resultant model is: 

```
## 
## Call:
##  randomForest(formula = classe ~ ., data = trainSet) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 7
## 
##         OOB estimate of  error rate: 0.4%
## Confusion matrix:
##      A    B    C    D    E  class.error
## A 4182    2    0    0    1 0.0007168459
## B   13 2830    5    0    0 0.0063202247
## C    0   13 2554    0    0 0.0050642774
## D    0    0   17 2393    2 0.0078772803
## E    0    0    2    4 2700 0.0022172949
```

Statisics on the prediction of this model include the following:  

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1395    1    0    0    0
##          B    0  948    4    0    0
##          C    0    0  851    9    0
##          D    0    0    0  795    1
##          E    0    0    0    0  900
## 
## Overall Statistics
##                                          
##                Accuracy : 0.9969         
##                  95% CI : (0.995, 0.9983)
##     No Information Rate : 0.2845         
##     P-Value [Acc > NIR] : < 2.2e-16      
##                                          
##                   Kappa : 0.9961         
##  Mcnemar's Test P-Value : NA             
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            1.0000   0.9989   0.9953   0.9888   0.9989
## Specificity            0.9997   0.9990   0.9978   0.9998   1.0000
## Pos Pred Value         0.9993   0.9958   0.9895   0.9987   1.0000
## Neg Pred Value         1.0000   0.9997   0.9990   0.9978   0.9998
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2845   0.1933   0.1735   0.1621   0.1835
## Detection Prevalence   0.2847   0.1941   0.1754   0.1623   0.1835
## Balanced Accuracy      0.9999   0.9990   0.9965   0.9943   0.9994
```

This model is predicted to be accuracte 99.69% of the time.  

_^conclusion^_
