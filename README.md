
### Preparing predictions for package gross weight - final report

**Author** Martins Berzins

#### Executive summary

The document contains an informative description of the work task, within which machine learning models must be used to prepare predictions of package weight based on package characteristics and other available information.

#### Rationale

A company that manufactures plywood requested the development of a model that calculates the weight of a single package based on the package’s characteristics, information about the recipient, as well as information about the time when the package was prepared. The problem is that the current accounting system calculates the package weight, but it may not be accurate. At times, the actual weight of the packages differs from what the system has calculated. When these packages are loaded onto a truck, the total weight can exceed the permitted limit, and it is no longer possible to reorganize the cargo. In such cases, there is a risk of receiving a fine for an overloaded shipment. The goal of the task is to find a way to identify the potential risk during cargo assembly that the truckload may end up heavier than allowed.

#### Research Question

The goal is to prepare a prediction of the package weight in kilograms, based on 39 package characteristics used for the calculation.

#### Data Sources

Two data files were provided for model development — dataset-train.csv and dataset-test.csv. The larger file, dataset-train.csv, contains 217,720 rows and 40 columns. Each row represents information about one package. The 40 columns include one column, ‘GrossActualKg’, which is the target variable for the calculation, as well as 39 feature columns, most of which are categorical, with only a few containing numeric values. The second file, dataset-test.csv, contains a subset of rows from the larger file — a total of 45,721 rows.

In this final task only the large dataset with 217,720 rows is used.

#### Methodology



