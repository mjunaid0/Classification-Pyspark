# Classification-Pyspark
This is a repository of classification template using pyspark.

I tried to make a template of classification machine learning using pyspark. I will try to explain step by step from load data, data cleansing and making a prediction. I created some functions in pyspark to make an automation, so user just need to update or replace the dataset.

To test my template, I used data Home_Quote_Conversion from Kaggle https://www.kaggle.com/c/homesite-quote-conversion. This dataset represent the activity who are interested in buying policies from Homesite. QuoteConversion_Flag indicates whether the customer purchased a policy and the task is to predict QuoteConversion_Flag for each QuoteNumber in the test set.

In general, the steps of classification machine learning are:

* Load libraries
  
  The first step in applying classification model is we have to load all libraries are needed. The basic libraries for classification are LogisticRegression, RandomForestClassifier, GBTClassifier, etc. Below the capture of all libraries are needed in classification:
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/load_libraries.png)


* Load Data into Spark Dataframe.
  
  Because we will work on spark environment so the dataset must be in spark dataframe. In this step, I created function to load data into spark dataframe. To run this function, first we have to define type of file of dataset (text or parquet) and path where dataset is stored and delimeter like ',' for example or other. 
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/load_dataset_function.png)
  
  
* Check the data.
  
  After load data, lets do some check of the dataset such as numbers of columns, numbers of observations, names of columns, type of columns, etc. In this part, we also do some changes like rename columns name if the column name too long, change the data type if data type not in accordance or drop unnecessary column and check the proportion of target. Those changes apply in both data train and data test.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/check_data.png)
  
  
* Define categorical and numerical variables.
  
  In this step, I tried to split the variables based on it's data types. If data types of variables is string will be saved in list called **cat_cols** and if data types of variables is integer or double will be saved in list called **num_cols**. This split applied on data train and data test. This step applied to make easier in the following step so I don't need to define categorical and numerical variables manually.
  Pictures below is example of code of define categorical and numerical variables in data train.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/define_categorical_numerical_variables1.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/define_categorical_numerical_variables2.png)
  
  
* Sample data
  
  If the dataset is too large, we can take sample of data. 
  Note: this step is optional.
   
* Check Missing Values.
  
  Sometimes the data received is not clean. So, we need to check whether there are missing values or not. Output from this step is the name of columns which have missing values and the number of missing values. To check missing values, actually I created two method:
   - Using pandas dataframe, 
   - Using pyspark dataframe.
  But the prefer method is method using pyspark dataframe so if dataset is too large we can still calculate / check missing values.
  Both data train and data test has to apply this step.
  This function refer to https://github.com/UrbanInstitute/pyspark-tutorials/blob/master/04_missing-data.ipynb.
  
    Pictures below are example check missing values using pyspark dataframe in data train.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/check_missing_values.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/check_missing_values2.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/check_missing_values3.png)
  
  
* Handle Missing Values.
  
  The approach that used to handle missing values between numerical and categorical variables is different. For numerical variables I fill the missing values with average in it's columns. While for categorical values I fill missing values use most frequent category in that column, therefore count categories which has max values in each columns is needed. 
 ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/handle_missing_values.jpg)
 ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/handle_missing_values2.jpg)
 

* Compare categorical variables in data train and data test.
 
 In this step, we check whether categories between data train and data test same or not. If not, categories in data test will be equated with data train. This step is needed to avoid error in feature engineering, if there are differences categories between data train and data test the error will appear at feature engineering process in data test so the modelling process cannot be applied in data test.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/function_compare_categorical_variables.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/callfunction_compare_categorical_variables.jpg)
  
  
* EDA 
  
  Create distribution visualization in each variables to get some insight of dataset. Pictures below are example of visualization of data train.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA1.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA2.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA3.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA4.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA5.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/EDA6.png)
  
  
* Handle insignificant categories in data train.
  
  Sometimes there are categories with fewest amount, those categories I called insignificant categories. Those insignificant categories will be replaced with the largest numbers of categories in each categorical columns. Sometimes this replacing will make better modelling. 
  
  Note: the determination of threshold that category have fewest amount is based on trial n error. In this case I used threshold 98% for maximum amount and 0.7% for minimum amount. Each categories in a column that have percentage under 0.7% will be replaced with category that has percentage equal or lower than 98%.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/insignificant_categories_function.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/call_insignificant_categories_function.jpg)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/insignificant_categories_function3.jpg)
  
  
* Handle insignificant categories in data test.
  
  To handle insignificant categories in data test, I refer to insignificant categories in data train. Categories that replaced will be equated with data train to avoid differences categories between data train and data test. As known those differences will trigger error in feature angineering and modelling process.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/insignificant_categories_function4.jpg)


* Handle outlier.
  
  Outlier is observations that fall below lower side or above upper side.
  
  To handle outlier the approach is by replacing the value greater than upper side with upper side value and replacing the value lower than lower side with lower side value. So, we need calculate upper and lower side from quantile value, quantile is probability distribution of variable. In General, there are three quantile:

   - Q1 = the value that cut off 25% of the first data when it is sorted in ascending order.
   - Q2 = cut off data, or median, it's 50 % of the data
   - Q3 = the value that cut off 75% of the first data when it is sorted in ascending order.
   - IQR or interquartile range is range between Q1 and Q3. IQR = Q3 - Q1.

  Upper side = Q3 + 1.5 * IQR
  Lower side = Q1 - 1.5 * IQR

  To calculate quantile in pyspark dataframe I created a function and then created function to calculate uper side, lower side, replacing upper side and replacing lower side. function of replacing upper side and lower side will looping as much as numbers of numerical variables in dataset (data train or data test). This step also apply in both data train and data test.
  
  Pictures below are example of handle outlier in data train, for data test the treatment is the same just call the function and apply it to data test.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/handle_outlier.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/handle_outlier2.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/handle_outlier3.png)

* Feature Engineering.
  
  Before splitting the data train, all categorical variables must be made numerical. There are several approaches to categorical variables in SparkML, including:
  - StringIndexer, which is to encode the string label into the index label by sequencing the string frequency descending and giving the smallest index (0) at most string frequency.
  - One-hot Encoding, which is mapping the label column (string label) on the binary column.
  - Vector assembler, which is mapping all columns in vector.
  
  In this step, first I check the distinct values in each categorical columns between data train and data test. If data train has distinct values more than data test in one or more categorical column, data train and data test will be joined then apply feature engineering on that data combination - this merger is needed to avoid error in modelling due to differences length of vector between data train and data test- length of vector (result of feature engineering of data combination) must be same between data train and data test so we can move to the next step, modelling and prediction. But if distinct values between data train and data test same, we will apply feature angineering on data train and data test separately then move to the next step modelling and prediction.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/feature_engineering.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/feature_engineering2.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/call_function_feature_engineering.png)

* Split Data train to train and test.
  
  This step just apply on data train. In order to make validation on the model that we are used, we need to split data train into train and test data. Data train will be split with percentage: train 70% and test 30% and define seed 24 so the random data that we split will not change. We can define seed with any value.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/split_data_train.png)
  
* Modelling.
  
  Algorithm that I used to make a model and prediction are:
   - Logistic Regression Logistic regression used logit function in prediction the probability.
   - Decision Tree This algorithm will find the most significant independent variable to create a group.
   - Random Forest This algorithm build multiple decision trees and merges them together and use bagging method.
   - Gradient Boosting This algorithm use boosting ensemble technic. This technique employs the logic in which the subsequent predictors    learn from the mistakes of the previous predictors.
   
* Evaluation.
  
  To evaluate model I used four metrics, they are:
    - ROC
      ROC (Receiver Operating Characteristic) The graph shows the true positive rate versus the false positive rate. This metric is           between 0 and 1 with a better model scoring higher. An area of 1 represents a perfect test; an area of .5 represents a worthless         test.
      So, The model is said to be good enaught if the value of the area under the curve is above 0.5.

    - Gini Coefficient
      Gini is ratio between the ROC curve and the diagnol line & the area of the above triangle. So, we can calculate Gini by this             formula: Gini = 2*AUC - 1 Such as AUC ROC, Gini above 50% or 60% is good model.

    - Confusion Matrix
      Confusion Matrix is a table is used to describe performance of a classification model. Some definition are: 
          - Accuracy = Proportion of total number of predictions that were correct 
          - Precision (Positive Predictive Value) : Proportion of positive cases that were correctly identified. 
          - Negative Predictive Value : Proportion of negative cases that were correctly identified. 
          - Sensitivity (Recall) : Proportion of actual positive cases which are correctly identified. 
          - Specificity : Proportion of actual negative cases which are correctly identified.

    - Log Loss
      Log Loss is one of model performance evaluation in classification model. The purpose of model is to minimize log loss value. 
      A perfect model would have of log loss of 0. Log Loss increase when predicted probability diverges from actual label.
  Pictures below will explain how to create model and make a prediction and also evaluate those model with those four metrics.
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression_ev1.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression_ROC.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression_ROC_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/logistic_regression_Gini_LogLoss.png)
    
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Decision_Tree.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Decision_Tree_ev1.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Decision_Tree_ROC.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Decision_Tree_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Decision_Tree_Gini_LogLoss.png)
    
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Random_Forest.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Random_Forest_ev1.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Random_Forest_ROC.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Random_Forest_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/Random_Forest_Gini_LogLoss.png)
    
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/gradient_boosting.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/gradient_boosting_ev1.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/gradient_boosting_ROC_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/gradient_boosting_confusion_matrix.png)
    ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/gradient_boosting_Gini_LogLoss.png)
    
  
* Hyper-Parameter Tuning.
  
  In this step, I provided hyper-parameter tuning script for all those model above. So could be compared the model evaluation between model with and without hyper parameter tuning. From those result we can choose model with the best evaluation to make prediction in data test. 
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/hyper_parameter_tuning_LogisticRegression.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/hyper_parameter_tuning_DecisionTree.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/hyper_parameter_Random_Forest.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/hyper_parameter_tuning_GradientBoost.png)

* Implementation Modelling to data test.
  
  After all the steps above are executed, now we know which one model that has best evaluation. And that is the perfect model to make prediction our data test. We can choose the top two model from four model then transform that model to data test. In this case, I choose Logistic Regression and Gradient Boosting to make prediction. Then save the prediction into csv file.
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/implement_to_data_test.png)
  ![alt text](https://github.com/elsyifa/Classification-Pyspark/blob/master/Image/implement_to_data_test2.png)
  
  **VIOLAAAAAA,, we got our prediction!!!!!**
  
  For more details please see my code.
