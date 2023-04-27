# Credit Card Fraud Detection

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

The ML binary classifiers under consideration are: K-Nearest Neighbors (K-NN), Logistic Regression (LR), Decision Tree (DT), Random Forest (RF) and XGBoosting.
After model selection with grid search to identify the optimal hyper-parameters, the models have been interpreted and analyzed considering the following:
* the execution time;
* the confusion matrix and threshold-based metrics;
* the threshold-free metrics;  
* the precision top-k metrics.



