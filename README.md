# Credit Card Fraud Detection

<img align="right" width="30%" src="https://prop.idcheck.tech/assets/img/fraud-detection.png">

This repository aims to summarize the most convinient Machine Learning and Deep Learning approaches to carry out fraud detection. The code is based on and highlights the main takeaways of the [Fraud Detection Handbook](https://fraud-detection-handbook.github.io/fraud-detection-handbook/Foreword.html) by Le Borgne et al.

Machine Learning for credit card fraud detection has become an active research field, mainly aiming to address the following challenges:
* the amount of transaction data to be analyzed is usually huge;
* the percentage of fraudulent transactions in a real-world dataset is typically well under 1%, and this may require the use of imbalanced learning tecniques;
* the spending habits of credit card users are different during weekdays, weekends and vacation periods, and more generally evolve over time, as well as fraud patterns and these time-dependent changes require the design of online learning strategies that can cope with temporal changes in statistical distributions;
* fraud detection systems must be scalable in order to allow for a quick detection fraudulent transactions;
* transactional data typically contain numerous categorical features that must be transformed into numerical features with feature aggregation tecniques, graph-based transformations or deep-learning approaches such as feature embeddings;
* there's a lack of public real-world datasets due to confidentiality reasons.

[Here](https://raw.githubusercontent.com/Fraud-Detection-Handbook/fraud-detection-handbook/main/Chapter_References/shared_functions.py
) you can find the `shared functions` that Le Borgne et al. made available for fraud detections.

[Here](https://github.com/Fraud-Detection-Handbook/simulated-data-raw) you can find the simulated historical data (labeled as 'genuine' or 'fraudulent') generated by the authors considering class imbalance (less than 1% of fraudulent transactions), a mix of numerical and categorical features (with categorical features involving a very large number of values), non-trivial relationships between features, and time-dependent fraud scenarios.

The simulated fraud scenarios are as follows:
* **Scenario 1**. Any transaction whose amount is more than $220$ is a fraud. This scenario is not inspired by a real-world scenario. Rather, it will provide an obvious fraud pattern that should be detected by any baseline fraud detector. 
* **Scenario 2**. Every day, a list of two terminals is drawn at random. All transactions on these terminals in the next 28 days will be marked as fraudulent. This scenario simulates a criminal use of a terminal, through phishing for example. Detecting this fraud scenario will be possible by adding features that keep track of the number of fraudulent transactions on the terminal. 
* **Scenario 3**. Every day, a list of 3 customers is drawn at random. In the next 14 days, $\frac{1}{3}$ of their transactions have their amounts multiplied by $5$ and marked as fraudulent. This scenario simulates a card-not-present fraud where the credentials of a customer have been leaked. Detecting this scenario will require adding features that keep track of the spending habits of the customer.

# Machine Learning binary classifiers

The ML binary classifiers under consideration are: K-Nearest Neighbors (K-NN), Logistic Regression (LR), Decision Tree (DT), Random Forest (RF) and XGBoosting. All the models have been implemented using the Scikit-learn library.
After model selection with grid search to identify the optimal hyper-parameters, the models have been interpreted and analyzed considering the execution time, the confusion matrix and threshold-based metrics, the threshold-free metrics and the precision top-k metrics.

<p align="center">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/4034de90bfa26512aa0e57ef44951d3ac717eed0/visualizations/ML_comparison.png">
</p>

The TPR for low FPR values is what matters most in a fraud detection problem, since the number of cards detected as fraudulent that can be manually checked by fraud investigators is in practice very limited. While PR curves are useful to highlight the performances of fraud detection systems for low FPR values, they however remain difficult to interpret from an operational point of view and Precision Top- K metrics address this issue.

The following graphs focus only on the best ML classifier, namely XGBoost.

<p align="center">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/3743935d9933267ee764808e749942d61a37254c/visualizations/topk_random.png" height=150>
  <img src="https://github.com/silviapoletti/fraud-detection/blob/7a6e48f7ba70bdb81276c7284f2e1c7a077d4e28/visualizations/topk_xgboost.png" height=150>
</p>

According to XGBoost, the features that better identify the fraud event are:
* low value of the average amount of money spent by the costumer in the 30 days window, but high values for the corresponding features in the 1 and 7 days windows;
* high transiction amount;
* high number of customer's transactions in the 30 days window, but low number for the corresponding feature in the 7 days windows;
* low number of transactions in a terminal in the 30 days window, but high terminal risk in the 7 days windows.

<p align="center">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/3ff7d7657366f7e74883797b6b5b50c8025ff3cb/visualizations/feat_importance_xgboost.png">
</p>

#  Imbalanced learning strategies

<img align="right" width="50%" src="https://github.com/silviapoletti/fraud-detection/blob/7de14175a7f4dcb891903fe5c29fc282f100011d/visualizations/imbalanced_learning.png">

Various imbalanced learning strategies have been combined with the XGBoost model:
* Balanced XGBoost is a cost-sensitive learning algorithm in which the misclassification costs is a hyperparameter to be identified through model selection;
* SMOTE oversampling consists in artificially increasing the proportion of samples from the minority class;
* Random undersampling and Edited Nearest Neighbors undersampling consist in reducing the imbalance ratio by removing samples from the majority class (well-suited for large datasets);
* Hybrid resampling combines oversampling the minority class and undersampling the majority class.

Moreover, some parallel-based ensemble methods like Balanced Bagging and Balanced Random Forest have been tested for comparison.
Ensemble methods consist in training multiple prediction models (called baseline learners) for the same prediction task, and in combining their outputs to make the final prediction. In parallel-based ensemble methods each baseline learner is trained in parallel, using either a subset of the training data, a subset of the training features, or a combination of both. On the contrary, in iterative-based ensemble methods such as XGBoost the baseline classifiers are trained in sequence.

# Feed-forward Neural Networks

A feed-forward NN with one hidden layer and Dropout has been compared to a similar architecture using embedding layers, which consider extra categorical inputs, namely the raw terminal id and the day of the week. Indeed, embedding layers learn a representation of each modality of the categorical variable in a continuous vector space of dimension $k$, chosen by the user. Note that learning an embedding of dimension $k$ for a categorical feature is computationally equivalent to learning a classical fully connected layer that takes as input the one-hot encoding of the feature and outputs $k$ neurons.

<p align="center">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/e45d68547de6c0c2663ad9b0883d7e33b4d46894/visualizations/balanced_bagging.png" width="80%">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/e45d68547de6c0c2663ad9b0883d7e33b4d46894/visualizations/balanced_rf.png" width="80%">
</p>

# Autoencoders

# Sequential models


