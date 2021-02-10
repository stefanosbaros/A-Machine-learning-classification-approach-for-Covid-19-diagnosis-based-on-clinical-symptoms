# A machine learning classification approach for Covid-19 diagnosis based on clinical symptoms

**Authors**: Stefanos Baros and Ana Jevtic (both authors contributed equally)

## Project description

In this project, we used data that were publicly reported by the Israeli Ministry of Health and designed **three** classifiers that can offer quick screening and efficient medical diagnosis for Covid-19. In particular, the developed classifiers were developed using **logistic regression**, **random forests** and **gradient boosting trees (XGBoost)** techniques and can predict whether an individual may be infected with Covid-19 based on several clinical symptoms such as cough, fever, shortness of breath etc. Our work is motivated by the urgent and timely need for the development of online tools that can aid alleviating the burden on healthcare systems. Our first step was to perform **exploratory data analysis** and **feature engineering** and design new features that could potentially lead to better overall prediction performance. We considered **second and fifth degree interactions** among basic features. We then carried out **model selection via L1 and L2 regularization** to identify the important features and designed the three classifiers. We evaluated the performance of the classifiers via **standard accuracy metrics (recall, precision and accuracy scores) and confusion matrices**.


## Dataset description

The dataset used in this project originated from the [Covid tested individuals data set](https://github.com/nshomron/covidpred/tree/master/data) - which is downloaded from the Israeli Ministry of Health website and translated into English by the authors of the paper [Machine learning-based prediction of COVID-19 diagnosis based on symptoms](https://www.nature.com/articles/s41746-020-00372-6).

Data records symptoms experienced by and Covid-19 test results for **278848** people who were tested between the **11th of March of 2020** and **30th of April of 2020** in Israel. There are 10 columns in the data, the first one recoding the Covid-19 test date, and the remaining ones recording symptoms (cough, fever, sore throat, shortness of breath, headache), gender, whether the individual came in contact with a confirmed Covid-19 positive case, whether the individual is above 60 years old and, finally, the Covid-19 test result.

## Exploratory data analysis

![caption='Correlation matrix positive case'](Correlation_matrix_positive_test_res.png#center)
![caption='Correlation matrix negative case'](Correlation_matrix_negative_test_res.png#center)

We plotted the correlation matrices of the features for two groups. The first group represents the people who got a **positive Covid-19 test result** (left figure) and the second group the people who got a **negative Covid-19 test result** (right figure), Interestingly, one can observe that, for people who tested positive for Covid-19, the features (symptoms) cough, fever, sore throat, shortness of breath and headache are all significantly correlated which means that they tend to appear together. On the other hand, for people who tested negative for Covid-19 one can observe that cough and fever are correlated (meaning they are reported together) and separately, sore throat, shortness of breath and headache are also correlated. This likely means that the people who decided to get tested experienced symptoms of other diseases which belonged to these two disjoint groups. In addition, the correlation of cough and contact with confirmed is higher in the people who tested negative. This potentially means that people who had cough and came in contact with a confirmed positive case more frequently decided to get tested.

## Description of files

There are **5 files** in this project repository:

- `forward_greedy_algorithm_features_selection.py`
- `feature_engineering_and_l1_regularization_sec_deg_inter.py`
- `feature_engineering_and_l1_regularization_five_deg_inter.py`
- `Covid_prediction_classification_approaches_XGBoost.py`
- `Covid_prediction_classification_approaches_log_regression_random_forest.py`


The `forward_greedy_algorithm_features_selection.py` implements a **forward greedy selection algorithm** for sorting features according to their importance. It works as follows. First, we start with an empty set of selected features. We then apply our algorithm, in our case logistic regression, on the set of features obtained by adding one feature at a time to the selected features list and obtain a different predictor every time whose accuracy is recorded. We update the list of selected features every time by choosing the features that yield the predictor with the smallest risk (error).


The `feature_engineering_and_l1_regularization_features_selection_sec_deg_inter.py`  and  `feature_engineering_and_l1_regularization_five_deg_inter.py`
are the files that contain our **feature engineering and model selection** approaches. Therein, we design new features by considering all possible **second and fifth degree** interactions among the basic features in our data. In the sequel, we perform **L1 and L2 regularization** to uncover the list of important features that we later use in model design.

<!-- The  `Covid_prediction_classification_approaches_sec_deg_interactions.py` and  `Covid_prediction_classification_approaches_five_deg_interactions.py` files contain the implementation of the **logistic regression and random forest classification approaches** for predicting whether an individual may be infected or not with Covid-19 based on the symptoms he is experiencing. The **features** used to design these models are the ones obtained via l1 regularization at the previous step where the full features lists were considered respectively to be **basic features and second-way interactions**, **basic features and five-way interactions**. -->

The  `Covid_prediction_classification_approaches_log_regression_random_forest.py` file contains the implementation of the **logistic regression and random forest classification approaches** for predicting whether an individual may be infected or not with Covid-19 based on clinical symptoms using only the **9 basic features**.

The file `Covid_prediction_classification_approaches_XGBoost.py` contains the implementation of the **XGBoost approach** for predicting whether an individual may be infected or not with Covid-19 based on clinical symptoms using only the **9 basic features**.



## Project details and results

### Main challenge 
The main challenge we faced in this project was the **large class imbalance** in the data set. After deleting almost half of the dataset rows that had missing information (whether the individual was 60 years old or above) the data set reported information for **125668 people with negative Covid-19 test result and  12504 people with positive Covid-19 test result**. The **ratio of data points with label 1 versus the ones with label 0 was 9.9%**. To overcome this challenge, we resorted to adjusting the weights on the performance metric used to design the classifiers.


### One-hot encoding

Our departing point was to use **one-hot encoding** to design new binary features that describe the Covid-19 test result, whether the individual is 60 years old and above, whether the individual came in contact with a confirmed Covid-19 positive case, and whether the individual is male or female. Using those features,  we then replaced related columns in the initial data set. 

### Methodology
Our methodology for constructing our classifiers can be broken down in **three main steps**:

- Feature engineering for design of new features
- Model selection via L1 and L2 regularization 
- Design of logistic regression and random forest classifiers

Below, we discuss these three steps in detail.

### Feature engineering for design of new features

The initial **features** list was:

- `features = ['cough', 'fever', 'sore_throat', 'shortness_of_breath' 'head_ache','age_60_and_above', 'gender', 'test_indication']`
            
where `test_indication` captured whether an individual came in contact with a confirmed positive case. After performing **one-hot encoding** the new list of **features** became:

- `features = ['cough', 'fever', 'sore_throat', 'shortness_of_breath', 'head_ache',  'age_60_and_above', 'Male', 'Female', 'Contact_with_confirmed']`

By accounting for all possible **second degree interactions among these main features**, except the male-female one, we obtained the following new list of **45 features**:

- `features = ['cough', 'fever', 'sore_throat', 'shortness_of_breath', 'head_ache', 'age_60_and_above', 'Male', 'Female', 'Contact_with_confirmed', 'cough+fever',                              'cough+sore_throat', 'cough+shortness_of_breath', 'cough+head_ache', 'cough+age_60_and_above', 'cough+Male', 'cough+Female', 'cough+Contact_with_confirmed',                      'fever+sore_throat', 'fever+shortness_of_breath', 'fever+head_ache', 'fever+age_60_and_above', 'fever+Male', 'fever+Female', 'fever+Contact_with_confirmed',                      'sore_throat+shortness_of_breath', 'sore_throat+head_ache', 'sore_throat+age_60_and_above', 'sore_throat+Male', 'sore_throat+Female',                                            'sore_throat+Contact_with_confirmed', 'shortness_of_breath+head_ache', 'shortness_of_breath+age_60_and_above', 'shortness_of_breath+Male',                                        'shortness_of_breath+Female', 'shortness_of_breath+Contact_with_confirmed', 'head_ache+age_60_and_above', 'head_ache+Male', 'head_ache+Female',                                  'head_ache+Contact_with_confirmed', 'age_60_and_above+Male', 'age_60_and_above+Female', 'age_60_and_above+Contact_with_confirmed', 'Male+Contact_with_confirmed',                'Female+Contact_with_confirmed']`

Similarly, by accounting for all possible **five degree interactions among the important main features**, we obtained the following new list of **features**:


- `features = ['cough', 'fever', 'sore_throat', 'shortness_of_breath', 'head_ache', 'age_60_and_above', 'Male', 'Female', 'Contact_with_confirmed', 'fever-sore_throat-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-sore_throat-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-fever-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-fever-sore_throat-head_ache-Contact_with_confirmed', 'cough-fever-sore_throat-shortness_of_breath-Contact_with_confirmed', 'cough-fever-sore_throat-shortness_of_breath-head_ache','cough', 'fever', 'sore_throat', 'shortness_of_breath', 'head_ache', 'age_60_and_above', 'Male', 'Female', 'Contact_with_confirmed', 'fever-sore_throat-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-sore_throat-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-fever-shortness_of_breath-head_ache-Contact_with_confirmed', 'cough-fever-sore_throat-head_ache-Contact_with_confirmed', 'cough-fever-sore_throat-shortness_of_breath-Contact_with_confirmed', 'cough-fever-sore_throat-shortness_of_breath-head_ache']`


These lists of features were subsequently used in model selection.

### Model selection via L1 and L2 regularization

Having constructed a comprehensive list of features including new ones that capture interactions among the main features we performed **logistic regression with L1 and L2 regularization** in order to reveal and retain only the important features. With the regularization penalty defined as **l=1/C**, we performed **logistic regression with both L1 and L2 regularization repeatedly by considering the  values 10, 1, 0.1, 0.01 and 0.001 for the constant C**. For each value C, we obtained a different logistic regression classifier whose performance on the training set and test set was recorded. Once the best value for C is identified, further tuning with finer granularity was performed by considering a range of values for C around it. We note that, **10% of the data is used for validation and 90% for training**. Similar results were obtained with L2 and L1 regularization so here we only discuss the latter. 

#### Performance criterion
The performance criteria we employed in model selection are the **recall/precision scores and the accuracy score**. For our problem however, the **most important score was the recall score** as we wanted to make sure that all designed classifiers were able to **positively identify people which have been infected with Covid-19 with great accuracy** so that these people become aware and explore appropriate treatment options early on.

#### L1 regularization with second degree interactions
We realized that **C=0.01**, which corresponds to a **regularization penalty l=100**, yielded the predictor with the **best accuracy** on the test set. In particular, we obtained a **recall score= 0.782** and accuracy on **test set=0.756**.  For this penalty factor, the reduced list of features we obtained  exactly matched the list of basic features. Thus, we were able to conclude that the **second degree interaction features were not able to help us improve the performance of our logistic regression algorithm**.


#### L1 regularization with fifth degree interactions
We realized that **C=0.01 and C=0.1**, which correspond to a **regularization penalty l=100 and l=10**, yielded the predictors with the **best accuracy** on the test set. In particular, we obtained the same scores as before, a **recall score= 0.782** and accuracy on **test set=0.756**.  For these penalty factors, the reduced list of features we obtained once again exactly matched the list of basic features once again. Thus, we were able to conclude that the **fifth degree interaction features were also not able to help us improve the performance of our logistic regression algorithm**.



### Design of logistic regression, random forest and XG Boost classifiers

In this project, we designed a **logistic regression**, a **random forest** and an **XGBoost** classifier by training our algorithms on **90%** of the data and keeping the remaining **10%** for validation.


#### Logistic regression classifier
To address the class imbalance problem in our data we used the following class weights in the logistic regression algorithm design process, **0.04 for the class with label 0 and 1 for the class with label 1** (solver='lbfgs', class_weight=weights). With these weights, we obtained the **best logistic regression predictor**, the one that yielded the **highest recall score and good overall accuracy score**. The logistic regression coefficients we obtained are:

                        w = [0.546  1.510 3.266  3.129  3.797  0.319  0.141 -0.138  3.497]

#### Random forest classifier
The random forest classifier is designed using **1000 ensembled trees constructed from Bootstrap samples based on the entropy information gain criterion and with maximum depth 10 and class weights as follows: 0.035 for the class with label 0 and 1 for the class with label 1** (n_estimators=1000, random_state=50, criterion='entropy', max_depth=10, bootstrap=True, class_weight={0:0.035,1:1}). We varied the depth of the trees from 4 to 15 in order to allow more complex classes in the model design process. Beyond depth 10, we have not seen any improvement in the performance of the random forest classifier. With the weights set to the values above, we obtained the best predictor, the one that yielded the **highest recall score and good overall accuracy score**.

#### XGBoost classifier
The XGBoost classifier uses a gradient boosting tree classification algorithm. This classifier was designed with the following specifications **(n_estimators=1000, random_state=50, scale_pos_weight=28)** where the scale_pos_weight parameter captures the ratio of the negative class weight to the positive class weight. With these values, we were able to obtain the best predictor, the one that yielded the **highest recall score and good overall accuracy score**.

#### Performance

Suprisingly, all three classifiers, the **logistic regression, random forest and the XGBoost one** resulted in the **same optimal performance** and identical output prediction vectors.

|  | Logistic regression  | Random forest  | XG Boost |
| ------- | --- | --- | --- |
| **Recall score** | 0.782 | 0.782 | 0.782 |
| **Precision score** | 0.246 |  0.246  | 0.246 |
| **Accuracy score** | 0.756 | 0.756 | 0.756 |

The confusion matrix of all three algorithms is the one shown below and was also identical. 

![caption='Confusion matrix'](Confusion_matrix.png#center)

Below we also plot the **Receiver Operating Characteristic (ROC)** curve obtained for the **logistic regression algorithm** and compute the **Area under the ROC Curve (AUC)**.

![caption='ROC Curve'](ROC_curve.png#center)

The **AUC was 0.84**.


