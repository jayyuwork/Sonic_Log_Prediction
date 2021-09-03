# Technical Description of PDDA Machine Learning Competition Solution

## 1. Introduction
## 1.1 Background
- Well logs are interpreted/processed to estimate the in-situ petrophysical and geomechanical properties, which is essential for subsurface characterization. Various types of logs exist, and each provides distinct information about subsurface properties. Certain well logs, like gamma ray (GR), resistivity, density, and neutron logs, are considered as “easy-to-acquire” conventional well logs that are run in most of the wells. Other well logs, like nuclear magnetic resonance, dielectric dispersion, elemental spectroscopy, and sometimes sonic logs, are only run in limited number of wells.

- Sonic travel-time logs contain critical geomechanical information for subsurface characterization around the wellbore. Often, sonic logs are required to complete the well-seismic tie workflow or geomechanical properties prediction. When sonic logs are absent in a well or an interval, a common practice is to synthesize them based on its neighboring wells that have sonic logs. This is referred to as sonic log synthesis or pseudo sonic log generation.

## 1.2 Problem Statement
- Compressional travel-time (DTC) and shear travel-time (DTS) logs are not acquired in all the wells drilled in a field due to financial or operational constraints. Under such circumstances, machine learning techniques can be used to predict DTC and DTS logs to improve subsurface characterization. The goal of the “SPWLA’s 1st Petrophysical Data-Driven Analytics Contest” is to develop data-driven models by processing “easy-to-acquire” conventional logs from Well #1, and use the data-driven models to generate synthetic compressional and shear travel-time logs (DTC and DTS, respectively) in Well #2. A robust data-driven model for the desired sonic-log synthesis will result in low prediction errors, which can be quantified in terms of Root Mean Squared Error(RMSE) by comparing the synthesized and the original DTC and DTS logs.

- You are provided with two datasets: Well #1 dataset and Well #2 dataset. You need to build a generalizable data-driven models using Well #1 dataset. Following that, you will deploy the newly developed data-driven models on Well #2 dataset to synthesize DTS and DTC logs. The data-driven model should use feature sets derived from the following seven logs: Caliper, Neutron, Gamma Ray, Deep Resistivity, Medium Resistivity, Photo-electric factor and density. The data-driven model should synthesize two target logs: DTC and DTS logs. https://github.com/pddasig/Machine-Learning-Competition-2020

## 2. Methods and Results
### 2.1 Machine learning algorithm
- Machine learning algorithms, such as support vector machine, random forest and XGBoost, only produce a point-to-point mapping from input features to targets. However, well logs often demonstrate a trend with depths at a specific location, because sequence strata were deposited continuously most of times at a region (Pham & Wu, 2019). Long short-term memory (LSTM) is a type of recurrent neural network (RNN) whose output is not only determined by the input from current step but also the input from previous steps (Chen & Zhang, 2020). 
- Here, a bidirectional LSTM is selected because it considers both overlying and underlying steps when predicting for the current steps. Different parameters have been tested, including number of layers, number neurons, batch size, and epochs. By the early-stopping, the training process will be stopped once the loss is no longer decreasing.
###	2.2 Data and feature selection
Missing values (values equals to -999), whether they're from features or from targets, were dropped. The caliper (CAL) measures the borehole diameter and is physically irrelevant to travel times of compressional waves and shear waves. Thus, this feature was dropped before building the model. Extremely high values of neutron (CNC > 40) are regarded as data outliers, and they were dropped to make the training data cleaner.
### 2.3	Results
Overall, the predicted curves of DTC and DTS are quite close to their true curves, except for some points where the predicted curves exhibit strong fluctuations while the true curves are smoother (Figure 1). These fluctuations are the major sources of prediction errors and are usually very difficult to predict (Figure 2).
 
##	3. Lessons Learned
###	3.1 Batch size
The training performance is finished with a batch size of 16 and 21 epochs. As told by the competition committee, the training set is combined from 3 different wells. It would be better to avoid mixing samples from different training wells in a batch, as the long-term dependencies of LSTM are sensitive to spatial information of wells. One way of setting the batch size is to find the highest common factor of the number of samples among all the training wells.
### 3.2 Overfitting
Overfitting might be a problem, as the model training is only stopped once the MAE stops decreasing. In order to build a more solid model, a better protocol would be splitting the data into two sets: one for training and one for validation. In this situation, two of the three wells can be used as the training set, and the other well can be held as the validation set. The validation set would be very useful to evaluate the performance of the model training and thus to mitigate the overfitting issue.
### 3.3 Algorithm selection
Due to limited time, only LSTM and bidirectional LSTM were tested for this competition. Other RNN methods would be worthy of investigation in future work. Depth information, an indicator of the gap in well logs, can help analyze the spatial resolution of data and the selection of models. Furthermore, it can be incorporated to the inputs of physics-constrained RNN models. Unfortunately, this information is unavailable.
## 4.	References
- N. Pham, & X. Wu. (2019). Missing sonic log prediction using convolutional long short-term memory. Society of Exploration Geophysicists.
- Y. Chen and D. Zhang. (2020). Physics-Constrained Deep Learning of Geomechanical Logs. IEEE Transactions on Geoscience and Remote Sensing.
