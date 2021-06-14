# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
**In 1-2 sentences, explain the problem statement: e.g "This dataset contains data about... we seek to predict..."**

In this project we have used UCI Bank Marketing dataset, which is related with direct marketing campaigns of a Portuguese baking institution. The classification goal is predict if the client will subscribe a term deposit (variable 'y'). 

**In 1-2 sentences, explain the solution: e.g. "The best performing model was a ..."**

n this project, we have used scikit-learn Logistic Regression and tuned the hyperparameters(optimal) using HyperDrive. We also used AutoML to build and optimize a model on the same dataset, so that we can compare the results of the two methods. The best performing model was obtained through AutoML - VotingEnsemble with accuracy of 0.9171

## Scikit-learn Pipeline
**Explain the pipeline architecture, including data, hyperparameter tuning, and classification algorithm.**

0. In first instances, i change the train.py and second, the firts cells not run with credetials propouse i chasnge by this: 
``` Python
from azureml.core import Workspace, Experiment
from azureml.core import Workspace

ws = Workspace.from_config()
ws.write_config(path='.azureml')

exp = Experiment(workspace=ws, name="udacity-project")

print('Workspace name: ' + ws.name, 
      'Azure region: ' + ws.location, 
      'Subscription id: ' + ws.subscription_id, 
      'Resource group: ' + ws.resource_group, sep = '\n')

run = exp.start_logging()
```
1 . Import data using TabularDatasetFactory
    - Cleaning of data - handling NULL values, one-hot encoding of categorical features and preprocessing of date
    - Splitting of data into train and test data
    - Using scikit-learn logistic regression model for classification

Create SKLearn Estimator for training the model selected (logistic regression) by passing the training script and later the estimator is passed to the hyperdrive configuration

2 . Configuration of Hyperdrive
      - Selection of parameter sampler
      - Selection of primary metric
      - Selection of early termination policy
      - Selection of estimator (SKLearn)
      - Allocation of resources
      - Other configuration details

3. Save the trained optimized model

  As specified above, we have used logistic regression model for our binary classification problem and hyperdrive tool to choose the best hyperparameter values from the parameter search space. Under the hood logistic regression uses logistic/sigmoidal function to estimate the probabilities between the dependent/target variable and one or more independent variables(features). In the below image, we can see that which hyperdrive run gave the best result.

**Parameter Sampler

The parameter sampler I chose was RandomParameterSampling because it supports both discrete and continuous hyperparameters. It supports early termination of low-performance runs and supports early stopping policies. In random sampling , the hyperparameter 


```Python
ps = RandomParameterSampling(
{
    '--C' : choice(0.03,0.3,3,10,30),
    '--max_iter' : choice(25,50,75,100)
})


# Specify a Policy
policy = BanditPolicy(evaluation_interval = 3, slack_factor = 0.1)

if "training" not in os.listdir():
    os.mkdir("./training")

# Create a SKLearn estimator for use with train.py
est =  SKLearn(
    source_directory = '.',
    compute_target = comp_trget,
    entry_script = 'train.py'
)

```

** Early Stopping Policy

The early stopping policy I chose was BanditPolicy because it is based on slack factor and evaluation interval. Bandit terminates runs where the primary metric is not within the specified slack factor compared to the best performing run

## AutoML
**In 1-2 sentences, describe the model and hyperparameters generated by AutoML.**
- Import data using TabularDatasetFactory
- Cleaning of data - handling NULL values, one-hot encoding of categorical features and preprocessing of date
- Splitting of data into train and test data
- Configuration of AutoML
- Save the best model generated

The below snapshots gives the explanation of the best model prediction by highlighting feature importance values and discovering patterns in data at training time. It also shows differnt metrics and their value for model interpretability and explanation.
## Pipeline comparison
**Compare the two models and their performance. What are the differences in accuracy? In architecture? If there was a difference, why do you think there was one?**

Both the approaches - Logistics + Hyperdrive and AutoML follow similar data processing steps and the difference lies in their configuration details. In the first approach our ML model is fixed and we use hyperdrive tool to find optimal hyperparametets while in second approach different models are automatic generated with their own optimal hyperparameter values and the best model is selected. In the below image, we see that the hyperdrive approach took overall 11m 51s and the best model had an accuracy of ~0.9146 and the automl approach took overall 28m 58s and the best model had an acccuracy of ~0.9169.

## Future work
**What are some areas of improvement for future experiments? Why might these improvements help the model?**
To check or measure the fairness of the models
Leverage additional interactive visualizations to assess which groups of users might be negatively impacted by a model and compare multiple models in terms of their fairness and performance
## Proof of cluster clean up
**If you did not delete your compute cluster in the code, please complete this section. Otherwise, delete this section.**
**Image of cluster marked for deletion**

<img src= 'https://github.com/fcgomezr/Udacity-Azure-ML/blob/main/Proof%20delete.png'>
