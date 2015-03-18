**Using a Random Forest Model to Predict Human Weight Lifting Errors**

**Data preparation**

After reading about the Human Activity Recognition (HAR) project at [http://groupware.les.inf.puc-rio.br/har](http://groupware.les.inf.puc-rio.br/har) , training and test data were downloaded as csv files using the links provided; I changed the "classe" filename to "class". &nbsp;These files were read into R. &nbsp;The train data set had 19622 observations of 160 variables, while the test data set had 20 observations of these same variables. &nbsp;Examination of the two data sets showed that the first column consisted of row numbers, second was subject names, while columns 3-7 were time stamps or window codes, not predictive variables. Many of the columns with HAR motion variables had little or no data. &nbsp;Since one goal of the analysis was to predict the test data set, I removed the first and 3:7 columns, as well as any column that had no data present in the test data set. &nbsp;I tried building models with data sets that both retained and eliminated the subject variable (column 2 in the original data set); this variable made no significant difference in the ability of the models to predict the test data. &nbsp;If the subject variable was removed, the remaining train data set had 19622 observations of 53 variables, while the test data set had 20 observations of the same number of variables; if the subject variable was retained, there were 54 variables. For the final model I retained the subject variable, as I wanted to be able to examine the results by subjects, as well as by class. &nbsp;I used the complete.cases function to determine whether there were missing data, but there were not.

**Model building**

&nbsp; &nbsp; &nbsp; &nbsp; In order to build my model, I divided the reduced train data set by class into training and testing data sets, using a 0.7 partition in the "createDataPartition" function in the caret package. The training data set had 13737 observations, while the testing data set had 5885 observations. &nbsp;I then checked the relative abundance of classes in the original data set and the testing and training data sets. &nbsp;The proportions of classes in each data set did not differ until the fourth decimal place (Table 1).

Table 1. &nbsp;Comparison of class proportions in original, training and test data sets.

| &nbsp; | A | B | C | D | E |
| --- | --- | --- | --- | --- | --- |
| original\_Proportions | 0.2843747 | 0.1935073 | 0.1743961 | 0.1638977 | 0.1838243 |
| training\_Proportions | 0.2843416 | 0.1934920 | 0.1744195 | 0.1639368 | 0.1838101 |
| testing\_Proportions | 0.2844520 | 0.1935429 | 0.1743415 | 0.1638063 | 0.1838573 |

&nbsp; &nbsp; &nbsp; &nbsp; In order to determine whether the variables remaining in the training data set had sufficient variance to build a predictive model, I checked the training data set for any variables that had near zero variance using the nearZeroVar function. &nbsp;All of them had sufficient variation. &nbsp;Using the findCorrelation function from the caret package with a correlation = 0.9, I determined whether any of the training variables were highly correlated, as highly correlated variables would increase the variance. &nbsp;Seven additional variables were highly correlated; these variables were removed from the training and testing data sets, as well as the test data set to be predicted for the class assignment.

&nbsp; &nbsp; &nbsp; &nbsp; The data had numbers, integers and the subject factor. &nbsp;I could use all of these types of data in a non-parametric model, but not in a parametric model. &nbsp;I therefore chose to use a random forests model. &nbsp;In the model, I used 47 predictor variables and a 10-fold cross-validation, repeated three times, to determine model accuracy. &nbsp;I also used 1000 trees to build the model. &nbsp;After the model was fitted, I used the varImp function to determine variable importance. &nbsp;I then used the predict function to predict the testing data set and determine model accuracy using the confusionMatrix function.

**Results**

&nbsp; &nbsp; &nbsp; &nbsp; The training data set model accuracy varied from 0.986 to 0.992, while the Kappa was 0.982 to 0.990; both had very low standard deviations (Table 2). &nbsp;The best accuracy was given by mtry = 26, which was the value used in the final model. &nbsp;The most important variables in the model were roll\_belt, followed by pitch\_forearm, yaw\_belt, roll\_forearm, magnet\_dumbbell\_y, and magnet\_dumbbell\_z (Table 3). &nbsp;There was a large drop in variable importance after magnet\_dumbbell\_z (Table 3).

Table 2. &nbsp;Model accuracy, Kappa and standard deviations for model with 47 predictor variables.

| mtry | Accuracy | Kappa | Accuracy SD | Kappa SD |
| --- | --- | --- | --- | --- |
| 2 | 0.9905367 | 0.9880285 | 0.002550307 | 0.003226706 |
| 26 | 0.9918472 | 0.9896866 | 0.002705221 | 0.003422689 |
| 51 | 0.985829 | 0.9820723 | 0.003370349 | 0.004263862 |

&nbsp;

| &nbsp; | Variable Name | Overall | &nbsp; | &nbsp; | Variable Name | Overall |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | roll\_belt | 100 | &nbsp; | 11 | accel\_forearm\_x | 18.86 |
| 2 | pitch\_forearm | 59.53 | &nbsp; | 12 | accel\_dumbbell\_z | 17.69 |
| 3 | yaw\_belt | 54.49 | &nbsp; | 13 | magnet\_belt\_z | 17.23 |
| 4 | pitch\_belt | 46.05 | &nbsp; | 14 | magnet\_forearm\_z | 15.89 |
| 5 | roll\_forearm | 44.65 | &nbsp; | 15 | accel\_belt\_z | 15.86 |
| 6 | magnet\_dumbbell\_y | 43.25 | &nbsp; | 16 | magnet\_belt\_y | 14.81 |
| 7 | magnet\_dumbbell\_z | 42.04 | &nbsp; | 17 | magnet\_belt\_x | 12.45 |
| 8 | accel\_dumbbell\_y | 24.73 | &nbsp; | 18 | yaw\_arm | 11.74 |
| 9 | magnet\_dumbbell\_x | 19.08 | &nbsp; | 19 | roll\_arm | 10.74 |
| 10 | roll\_dumbbell | 18.87 | &nbsp; | 20 | yaw\_dumbbell | 10.38 |

Table 3. &nbsp;Variable importance in model building for the first 20 of 47 variables used to predict class.

&nbsp;

&nbsp; &nbsp; &nbsp; &nbsp; The final model predicted the testing data set with a 0.9930 accuracy, with a 95% confidence interval of 0.9906 to 0.9950; this accuracy was unusual because testing accuracy is usually lower than training accuracy, but the confidence interval for this prediction is greater than that for the training data set. &nbsp; The Kappa statistic for this prediction was 0.9912. &nbsp;The confusion matrix shows that class E was predicted most poorly (14 samples committed to classes B, C, and D), while class A, the control, was confused the least, with single samples commited to classes D and E (Table 4). &nbsp;The smallest class, class D, was confused only with class C (Table 4).

Table 4. &nbsp;Confusion matrix for testing data set predicted by the model trained on the training data set.

| &nbsp; | Reference |
| --- | --- |
| Prediction | A | B | C | D | E |
| A | 1672 | 6 | 0 | 0 | 0 |
| B | 0 | 1131 | 4 | 0 | 2 |
| C | 0 | 2 | 1020 | 11 | 5 |
| D | 1 | 0 | 2 | 953 | 7 |
| E | 1 | 0 | 0 | 0 | 1068 |

&nbsp;

&nbsp; &nbsp; &nbsp; &nbsp; When the model was used to predict classes for the 20 cases in the class test set, it predicted all of them correctly.

&nbsp; &nbsp; &nbsp; &nbsp; I also tried building the model with the 7 most important variables from the initial run (1-7 in Table 3). &nbsp;This model had a slightly lower accuracy, but overall accuracy and Kappa were still very high (Table 5). &nbsp;The order of importance of the variables changed somewhat in this new model, although roll\_belt was still the &nbsp;most important variable. &nbsp;The final model was built with mtry = 2. &nbsp;When this new model was used to predict the testing data set, the overall accuracy was reduced to 0.9852 with 0.9818 to 0.9881 95% confidence interval; the new Kappa was 0.9813. &nbsp;The confusion matrix showed more errors in class predictions (Table 6), with classes B and C both having 23 false positives. &nbsp;This new model, however, also predicted accurately all of the 20 test predictions for the class submission.

Table 5. &nbsp;Model accuracy, Kappa and standard deviations for new model using the seven most important predictor variables from the original model.

| mtry | Accuracy | Kappa | Accuracy SD | Kappa SD |
| --- | --- | --- | --- | --- |
| 2 | 0.9836692 | 0.9793493 | 0.003972036 | 0.005021719 |
| 4 | 0.9833298 | 0.9789187 | 0.00391891 | 0.004955957 |
| 7 | 0.9782827 | 0.9725349 | 0.004227099 | 0.005347556 |

&nbsp;

&nbsp;

&nbsp;

Table 6. &nbsp;Confustion matrix for testing data set predicted by the reduced model that had the 7 most important variables from the original model.

| &nbsp; | Reference |
| --- | --- |
| Prediction | A | B | C | D | E |
| A | 1656 | 5 | 0 | 0 | 0 |
| B | 5 | 1116 | 16 | 0 | 6 |
| C | 12 | 14 | 1003 | 8 | 4 |
| D | 1 | 4 | 5 | 953 | 2 |
| E | 0 | 0 | 2 | 3 | 1070 |

&nbsp;

&nbsp;