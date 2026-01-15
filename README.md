
# Preparing predictions for package gross weight - final report

**Author** Martins Berzins

## Executive summary

The document contains an informative description of the work task, within which machine learning models must be used to prepare predictions of package weight based on package characteristics and other available information.

## Rationale

A company that manufactures plywood requested the development of a model that calculates the weight of a single package based on the package’s characteristics, information about the recipient, as well as information about the time when the package was prepared. The problem is that the current accounting system calculates the package weight, but it may not be accurate. At times, the actual weight of the packages differs from what the system has calculated. When these packages are loaded onto a truck, the total weight can exceed the permitted limit, and it is no longer possible to reorganize the cargo. In such cases, there is a risk of receiving a fine for an overloaded shipment. The goal of the task is to find a way to identify the potential risk during cargo assembly that the truckload may end up heavier than allowed.

## Research Question

The goal is to prepare a prediction of the package weight in kilograms, based on 39 package characteristics used for the calculation.

## Data Sources

Two data files were provided for model development — dataset-train.csv and dataset-test.csv. The larger file, dataset-train.csv, contains 217,720 rows and 40 columns. Each row represents information about one package. The 40 columns include one column, "GrossActualKg", which is the target variable for the calculation, as well as 39 feature columns, most of which are categorical, with only a few containing numeric values. The second file, dataset-test.csv, contains a subset of rows from the larger file — a total of 45,721 rows.

In this final task only the large dataset with 217,720 rows is used.

## Methodology

The main objective of this assignment is to create a machine learning model that can predict parcel weight with high accuracy. Based on the information obtained in previous studies, the author will train only tree-based models.

---

#### DATA PREPARE

The following data preparation steps were performed:

* The data were loaded for processing.
* Basic information about numerical features was printed, showing the number of values, mean, standard deviation, minimum, and maximum values (table 1).
<p align="center">
 <img src=images/numerical_feature_basic_info.png width=50%/>
 <br>
 <em>Table 1. Numerical feature information</em>
</p>

* Basic information about categorical features was printed, showing for each feature: the feature name, number of categories, number of records with the category “-”, number of records with the category “-/-”, the top 10 most frequently used categories, and the number of records per category. Table 2 and 3 shows the list of categorical features and also count of records with "none" values "-" and "-/-". 
 
<p align="center">
 <img src=images/categorical_feature_basic_info_1.png width=50%/>
 <br>
 <em>Table 2. Categorical feature information (part 1)</em>
</p>

<p align="center">
 <img src=images/categorical_feature_basic_info_2.png width=50%/>
 <br>
 <em>Table 3. Categorical feature information (part 2)</em>
</p>

* The data were split into training and test sets using the "train_test_split()" function.
* After splitting the data into training and test sets, code was executed to ensure that all category values present in the test set were also present in the training set. If a category appeared in the test set but not in the training set, one record containing that category was moved from the test set to the training set.
* A process was prepared to encode categorical features using "CatBoostEncoder()". The use of "OneHotEncoder()" was not possible because the encoding created too many features, and such a data structure could not be used for model training with limited resources. In this case, "StandardScaler()" was not used, as only tree-based models were trained, for which scaling is not essential.
* Code was created that, after training each ML model, retrieves performance metrics and stores them in a separate data structure for later analysis.

---

#### MODEL DEVELOPMENT

The following models were trained:

* A "DecisionTreeRegressor()" model with default parameters.
* Search for the best "DecisionTreeRegressor()" model using "GridSearchCV()".
* An "ExtraTreesRegressor()" model with default parameters.
* Search for the best "ExtraTreesRegressor()" model using "GridSearchCV()".
* A "RandomForestRegressor()" model with specific parameters (suggested by resoruces in Internet).
* An "XGBRegressor()" model with specific parameters (suggested by resources in Internet).
* An extensive search for the best "XGBRegressor()" model was done using "RandomizedSearchCV()". The model was tuned sequentially in four steps:

  * first, the hyperparameters "n_estimators" and "learning_rate" were tuned;
  * second, the hyperparameters "max_depth", "min_child_weight", and "gamma" were tuned;
  * third, the hyperparameters "subsample", "colsample_bytree", and "colsample_bylevel" were tuned;
  * fourth, the hyperparameters "reg_alpha" and "reg_lambda" were tuned.
* The "XGBRegressor()" model with best hyperparameters was trained separately in order to retain training process statistics for data analysis.

---

#### REPORTING

* A table of training results was prepared, including information about the model deployed in the Azure system (table 4 bellow).
* Several reports showing the most important features were prepared.
* A report showing the residual distribution was prepared.
* A report showing residuals for all predicted values was prepared.
* A report showing predicted values versus actual values was prepared.
* A report showing root mean squared error after certain number of boosting iterations was prepared.

## Results

#### DATA PREPARE

* For encoding categorical features, it is much more appropriate to use "CatBoostEncoder()" rather than "OneHotEncoder()". "CatBoostEncoder()" did not increase the number of features and made it possible to use all features and the largest dataset during model training.
* The analysis of categorical feature values showed the value distribution for each feature. It can be seen that most categorical features have missing values represented as “-” or “-/-”. This means that, when describing a single parcel, only a subset of features is used at a time, not all of them. It is important that all features are used for model training, not just a few.

---

#### MODEL DEVELOPMENT RESULTS

* Overall, all tree-based modeling approaches demonstrated good results. A comparison of performance metrics is shown in the table 4. Record with name "Azure model" is added to this list manually. It represents the data given by Azure Cloud platform after training a model on the same data. Author used metrics given by Auzure as target which must be reached by localy trained models.

<p align="center">
 <img src=images/training_results.png width=100%/>
 <br>
 <em>Table 4. Tree-based model training results</em>
</p>

* The use of "GridSearchCV()" for model tuning was difficult because the model training times were too long.
* Using "RandomizedSearchCV()" for model tuning produced good results in this case. The author chose to use "RandomizedSearchCV()" together with "XGBRegressor()" because it is recommended online due to the large number of hyperparameters that can be configured.
* The online-recommended method of tuning hyperparameters for an "XGBRegressor()" model gradually produced results. This was done in four stages, where in each subsequent stage the hyperparameters obtained in the previous stage were used. The initial model was prepared with parameters suggested by ChatGPT as reasonable starting values.
  The hyperparameters were obtained in the following sequence:

  * Stage 1 – "n_estimators", "learning_rate"
  * Stage 2 – "max_depth", "min_child_weight", "gamma"
  * Stage 3 – "subsample", "colsample_bytree", "colsample_bylevel"
  * Stage 4 – "reg_alpha", "reg_lambda"

In table 5 you can see hyperparameters for the best "XGBRegressor()" model obtained in four stage search process.

<p align="center">
 <img src=images/xgbregressor_stage_4_final.png width=50%/>
 <br>
 <em>Table 5. Hyperparameters for the best XGBRegressor model</em>
</p>

---

#### REPORTING

* In table 4 it can be seen that the resulting "XGBRegressor" model is as good as the Azure model. Some indicators are slightly better, some are slightly worse. 
* Figure 1 below shows how often each feature appears in a split condition. It is evident that some features are used more frequently than others.

<p align="center">
 <img src=images/feature_importance_weight.png width=70%/>
 <br>
 <em>Figure 1. Feature importance - usage in split conditions</em>
</p>

* Figure 2 below shows which features were the most important for achieving maximum model accuracy. It can be seen that accuracy is driven by a few features, while most features are insignificant in terms of accuracy in the majority of cases.

<p align="center">
 <img src=images/feature_importance_gain.png width=70%/>
 <br>
 <em>Figure 2. Feature importance - impact on accuracy</em>
</p>

* In the author’s view, the figure 3 illustrates how features are used in model construction. From the author’s perspective, it is important that all features are used in model development in order to achieve accuracy in a production environment. Figure 3 show only 20 features. However, in .ipynb file you can see usage statistics of all features.

<p align="center">
 <img src=images/feature_importance_cover.png width=70%/>
 <br>
 <em>Figure 3. Feature importance - usage in model construction</em>
</p>

* The author also prepared plots showing how much the predicted values differ from the actual values. However, in the author’s opinion, these plots were not very informative. Comparing various metrics with previously prepared models better characterizes the model. 
* Figure 4 shown below illustrates how training tree-based models improves overall prediction accuracy after training a certain number of trees.

<p align="center">
 <img src=images/xgbregressor_rmse.png width=70%/>
 <br>
 <em>Figure 4. XGBRegressor Root mean squared error</em>
</p>

## Next steps

* The best "XGBRegressor" model should be deployed in a cloud environment and an API interface to the model should be created.
* Code should be developed in the ERP system that sends requests and receives the predicted quantity.
* The obtained predictions should be compared with those generated by the model deployed in the Azure environment.

## Outline of the project

* Link to the file in which model training is performed: https://github.com/martin7x7/Assignment24_1/blob/main/package_weight_final.ipynb

 ## Contact and Further Information
 
Discord: martin7x7

[LinkedIn: linkedin.com/in/berzinsmartins/](https://www.linkedin.com/in/berzinsmartins/)
