<p align="center">
    <strong>Using a Random Forest Model to Predict Human Weight Lifting Errors</strong>
</p>
<p>
    <u>Data preparation</u>
</p>
<p>
After reading about the Human Activity Recognition (HAR) project at    <a href="http://groupware.les.inf.puc-rio.br/har">http://groupware.les.inf.puc-rio.br/har</a> , training and test data were downloaded as csv files using
    the links provided; I changed the “classe” filename to “class”. These files were read into R. The train data set had 19622 observations of 160 variables,
    while the test data set had 20 observations of these same variables. Examination of the two data sets showed that the first column consisted of row
    numbers, second was subject names, while columns 3-7 were time stamps or window codes, not predictive variables. Many of the columns with HAR motion
    variables had little or no data. Since one goal of the analysis was to predict the test data set, I removed the first and 3:7 columns, as well as any
    column that had no data present in the test data set. I tried building models with data sets that both retained and eliminated the subject variable (column
    2 in the original data set); this variable made no significant difference in the ability of the models to predict the test data. If the subject variable
    was removed, the remaining train data set had 19622 observations of 53 variables, while the test data set had 20 observations of the same number of
    variables; if the subject variable was retained, there were 54 variables. For the final model I retained the subject variable, as I wanted to be able to
    examine the results by subjects, as well as by class. I used the complete.cases function to determine whether there were missing data, but there were not.
</p>
<p>
    <u>Model building</u>
</p>
<p>
    In order to build my model, I divided the reduced train data set by class into training and testing data sets, using a 0.7 partition in the
    “createDataPartition” function in the caret package. The training data set had 13737 observations, while the testing data set had 5885 observations. I then
    checked the relative abundance of classes in the original data set and the testing and training data sets. The proportions of classes in each data set did
    not differ until the fourth decimal place (Table 1).
</p>
<p>
    Table 1. Comparison of class proportions in original, training and test data sets.
</p>
<table border="0" cellspacing="0" cellpadding="0" width="607">
    <tbody>
        <tr>
            <td width="165" nowrap="">
            </td>
            <td width="88" nowrap="">
                <p align="center">
                    A
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="center">
                    B
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="center">
                    C
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="center">
                    D
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="center">
                    E
                </p>
            </td>
        </tr>
        <tr>
            <td width="165" nowrap="">
                <p>
                    original_Proportions
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.2843747
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1935073
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1743961
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1638977
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1838243
                </p>
            </td>
        </tr>
        <tr>
            <td width="165" nowrap="">
                <p>
                    training_Proportions
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.2843416
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1934920
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1744195
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1639368
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1838101
                </p>
            </td>
        </tr>
        <tr>
            <td width="165" nowrap="">
                <p>
                    testing_Proportions
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.2844520
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1935429
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1743415
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1638063
                </p>
            </td>
            <td width="88" nowrap="">
                <p align="right">
                    0.1838573
                </p>
            </td>
        </tr>
    </tbody>
</table>
<p>
    In order to determine whether the variables remaining in the training data set had sufficient variance to build a predictive model, I checked the training
    data set for any variables that had near zero variance using the nearZeroVar function. All of them had sufficient variation. Using the findCorrelation
    function from the caret package with a correlation = 0.9, I determined whether any of the training variables were highly correlated, as highly correlated
    variables would increase the variance. Seven additional variables were highly correlated; these variables were removed from the training and testing data
    sets, as well as the test data set to be predicted for the class assignment.
</p>
<p>
    The data had numbers, integers and the subject factor. I could use all of these types of data in a non-parametric model, but not in a parametric model. I
    therefore chose to use a random forests model. In the model, I used 47 predictor variables and a 10-fold cross-validation, repeated three times, to
    determine model accuracy. I also used 1000 trees to build the model. After the model was fitted, I used the varImp function to determine variable
    importance. I then used the predict function to predict the testing data set and determine model accuracy using the confusionMatrix function.
</p>
<p>
    <u>Results</u>
</p>
<p>
    The model accuracy varied from 0.986 to 0.992, while the Kappa was 0.982 to 0.990; both had very low standard deviations (Table 2). The best accuracy was
    given by mtry = 26, which was the value used in the final model. The most important variables in the model were roll_belt, followed by pitch_forearm,
    yaw_belt, roll_forearm, magnet_dumbbell_y, and magnet_dumbbell_z (Table 3). There was a large drop in variable importance after magnet_dumbbell_z (Table
    3).
</p>
<p>
    Table 2. Model accuracy, Kappa and standard deviations for three numbers of samples per node (mtry).
</p>
<table border="0" cellspacing="0" cellpadding="0" width="489">
    <tbody>
        <tr>
            <td width="41" nowrap="">
                <p>
                    mtry
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="center">
                    Accuracy
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="center">
                    Kappa
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="center">
                    Accuracy SD
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="center">
                    Kappa SD
                </p>
            </td>
        </tr>
        <tr>
            <td width="41" nowrap="">
                <p align="right">
                    2
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.9905367
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.9880285
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.002550307
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.003226706
                </p>
            </td>
        </tr>
        <tr>
            <td width="41" nowrap="">
                <p align="right">
                    26
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.9918472
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.9896866
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.002705221
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.003422689
                </p>
            </td>
        </tr>
        <tr>
            <td width="41" nowrap="" valign="bottom">
                <p align="right">
                    51
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.985829
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.9820723
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.003370349
                </p>
            </td>
            <td width="112" nowrap="">
                <p align="right">
                    0.004263862
                </p>
            </td>
        </tr>
    </tbody>
</table>
<table border="0" cellspacing="0" cellpadding="0" align="left" width="528">
    <tbody>
        <tr>
            <td width="180" nowrap="">
                <p align="center">
                    Variable Name
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="center">
                    Overall
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p align="center">
                    Variable Name
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="center">
                    Overall
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    roll_belt
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    100
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    accel_forearm_x
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    18.86
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    pitch_forearm
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    59.53
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    accel_dumbbell_z
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    17.69
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    yaw_belt
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    54.49
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    magnet_belt_z
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    17.23
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    pitch_belt
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    46.05
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    magnet_forearm_z
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    15.89
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    roll_forearm
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    44.65
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    accel_belt_z
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    15.86
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    magnet_dumbbell_y
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    43.25
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    magnet_belt_y
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    14.81
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    magnet_dumbbell_z
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    42.04
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    magnet_belt_x
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    12.45
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    accel_dumbbell_y
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    24.73
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    yaw_arm
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    11.74
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    magnet_dumbbell_x
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    19.08
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    roll_arm
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    10.74
                </p>
            </td>
        </tr>
        <tr>
            <td width="180" nowrap="">
                <p>
                    roll_dumbbell
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    18.87
                </p>
            </td>
            <td width="19" nowrap="" valign="bottom">
            </td>
            <td width="180" nowrap="">
                <p>
                    yaw_dumbbell
                </p>
            </td>
            <td width="75" nowrap="" valign="bottom">
                <p align="right">
                    10.38
                </p>
            </td>
        </tr>
    </tbody>
</table>
<p>
    Table 3. Variable importance in model building for the first 20 of 47 variables used to predict class.
</p>
<p>
    The final model predicted the testing data set with a 0.9930 accuracy, with a 95% confidence interval of 0.9906 to 0.9950; the accuracy was unusual because
    testing accuracy is usually lower than training accuracy, but the confidence interval for this prediction is greater. The Kappa statistic for this
    prediction was 0.9912. The confusion matrix shows that class E was predicted most poorly (14 samples committed to classes B, C, and D), while class A, the
    control, was confused the least, with single samples commited to classes D and E (Table 4). The smallest class, class D, was confused only with class C
    (Table 4).
</p>
<p>
    Table 4. Confusion matrix for testing data set predicted by the model trained on the training data set.
</p>
<table border="0" cellspacing="0" cellpadding="0" width="359">
    <tbody>
        <tr>
            <td width="91" nowrap="" valign="bottom">
            </td>
            <td width="268" nowrap="" colspan="5">
                <p align="center">
                    Reference
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="">
                <p>
                    Prediction
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p>
                    A
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p>
                    B
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p>
                    C
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p>
                    D
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p>
                    E
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="">
                <p align="right">
                    A
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p align="right">
                    1672
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p align="right">
                    6
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="">
                <p align="right">
                    B
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p align="right">
                    1131
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    4
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    2
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="">
                <p align="right">
                    C
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p align="right">
                    2
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    1020
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    11
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    5
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="">
                <p align="right">
                    D
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p align="right">
                    1
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    2
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    953
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    7
                </p>
            </td>
        </tr>
        <tr>
            <td width="91" nowrap="" valign="bottom">
                <p align="right">
                    E
                </p>
            </td>
            <td width="51" nowrap="" valign="bottom">
                <p align="right">
                    1
                </p>
            </td>
            <td width="47" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    0
                </p>
            </td>
            <td width="57" nowrap="" valign="bottom">
                <p align="right">
                    1068
                </p>
            </td>
        </tr>
    </tbody>
</table>
<p>
    When the model was used to predict classes for the 20 cases in the class test set, it predicted all of them correctly.
</p>

