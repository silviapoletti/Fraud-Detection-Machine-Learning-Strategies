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

The ML binary classifiers under consideration are: **K-Nearest Neighbors** (K-NN), **Logistic Regression** (LR), **Decision Tree** (DT), **Random Forest** (RF) and **XGBoost**. All the models have been implemented using the Scikit-learn library.
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
* **Balanced XGBoost** is a cost-sensitive learning algorithm in which the misclassification costs is a hyperparameter to be identified through model selection;
* **SMOTE oversampling** consists in artificially increasing the proportion of samples from the minority class;
* **Random undersampling** and **Edited Nearest Neighbors** undersampling consist in reducing the imbalance ratio by removing samples from the majority class (well-suited for large datasets);
* **Hybrid resampling** combines oversampling the minority class and undersampling the majority class.

&nbsp;

<img align="right" width="50%" src="https://github.com/silviapoletti/fraud-detection/blob/7e395e617991b1f431f4dfb4984438e7d4caeffb/visualizations/imbalanced_learning_parallel_ensemble.png">

Moreover, some parallel-based ensemble methods like **Balanced Bagging** and **Balanced Random Forest** have been tested for comparison.

Ensemble methods consist in training multiple prediction models (called baseline learners) for the same prediction task, and in combining their outputs to make the final prediction. 
In parallel-based ensemble methods each baseline learner is trained in parallel, using either a subset of the training data, a subset of the training features, or a combination of both. 
On the contrary, in iterative-based ensemble methods such as XGBoost the baseline classifiers are trained in sequence.

# Feed-forward neural networks classifiers

A **feed-forward NN** with one hidden layer and Dropout has been compared to a similar architecture using **embedding layers**, which consider extra categorical inputs, namely the raw terminal id and the day of the week. Indeed, embedding layers learn a representation of each modality of the categorical variable in a continuous vector space of dimension $k$, chosen by the user. Note that learning an embedding of dimension $k$ for a categorical feature is computationally equivalent to learning a classical fully connected layer that takes as input the one-hot encoding of the feature and outputs $k$ neurons.

The following graphs show how the performance metric changes according to the NN hyper-parameter values (for each parameter under consideration, the other parameters are fixed to epochs=20, batch_size=64, dropout=0.2).
<p align="center">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/2ed92729a56c09a2c28edb7a15bf00e51eecfa6c/visualizations/NN_epochs.png" width="80%">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/2ed92729a56c09a2c28edb7a15bf00e51eecfa6c/visualizations/NN_batches.png" width="80%">
  <img src="https://github.com/silviapoletti/fraud-detection/blob/2ed92729a56c09a2c28edb7a15bf00e51eecfa6c/visualizations/NN_dropout.png" width="80%">
</p>

All the deep learning models, including the ones in the following sections, have been implemented in PyTorch.

# Autoencoders for anomaly detection

Fraud detection can be formalized as as an **unsupervised learning** anomaly detection or outlier detection taks, aiming to identify items that are rare or differ significantly from the "normal" behavior, observable in the majority of the data. 

<img align="right" width="50%" src="https://github.com/silviapoletti/fraud-detection/blob/cd579d1da2a54dfbc2e96e330787e42486c9ce19/visualizations/autoencoder_performance.png">

An **Autoencoder** can therefore be used to model the normal behavior of data and detect outliers using the reconstruction error as an indicator. Indeed, the reconstruction error of "normal" data will be lower than the reconstruction error of outliers.

For comparison, **Isolation Forest** has been implemented as an alternative unsupervised anomaly detector that relies on tree-based models.

Finally, the autoencoder can be used in a **semi-supervised learning** setting in which a feed-forward NN trained on labeled data is provided with the unsupervised risk score computed by the autoencoder (reconstruction error) as an additional variable to the supervised model. The three metrics for our data are very close, with or without the additional feature. Nevertheless, with a different setting there can be a benefit, especially if the quantity of available unlabeled data is much larger than the quantity of labeled data. Moreover, this semi-supervised technique can be improved by training two separate autoencoders, one for each class, and by using both reconstruction errors as additional variables.

# Sequential models for context-aware fraud detection

Context-aware fraud detection is based on feature aggregation in order to consider the context (e.g. the cardholder history) associated with a transaction to make predictions. The context is established based on a landmark variable, which here can be the Customer ID. Concretely, one starts by building the sequence (usually of fixed-size) of historical transactions, chronologically ordered from the oldest to the current one, that have the same Customer ID as the current transaction.

There are two main sequential learning approaches to deal with sequential dependency between data points:
* Sliding window methods, which often tend to ignore the order between data points within the window (e.g. **1D convolutional neural networks**);
* Sequential models that are designed explicitly to consider the sequential order between consecutive data points (e.g. recurrent neural networks such as **Long Short-Term Memory networks** which make use of several gates, i.e. neurons with sigmoid activations, to cleverly select the right information to keep from the previous state and the right information to integrate from the current input).

In addition, the **Attention module** takes in input all the hidden states of the sequential model and combines them in a relevant manner with respect to a certain context. More precisely, given a context vector $c$ and the sequence of hidden states $h_i$, the Attention module computes an attention score $a_i$ for each hidden state, generally using a similarity measure like a dot product between $c$ and $h_i$. Then, these scores are normalized with softmax and used to compute a global output state with a linear combination $\sum a_i * h_i$. 

<p align="center">
    <img src="https://github.com/Fraud-Detection-Handbook/fraud-detection-handbook/blob/main/Chapter_7_DeepLearning/images/attention.png" width="70%">
</p>
<p align="center">
    <em>Source: https://github.com/Fraud-Detection-Handbook/fraud-detection-handbook/blob/main/Chapter_7_DeepLearning/images/attention.png</em>
</p>

In fraud detection, a reasonable choice is to consider a representation of the transaction that we aim to classify (i.e. the last transaction) as context, in order to select the correct elements from the previous transactions. As representation, it's possible to use a projection of the last transaction.







