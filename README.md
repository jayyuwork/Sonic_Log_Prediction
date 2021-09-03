# Technical Description of PDDA Machine Learning Competition Solution
## 1. Machine learning algorithm
- Machine learning algorithms, such as support vector machine, random forest and XGBoost, only produce a point-to-point mapping from input features to targets. However, well logs often demonstrate a trend with depths at a specific location, because sequence strata were deposited continuously most of times at a region (Pham & Wu, 2019). Long short-term memory (LSTM) is a type of recurrent neural network (RNN) whose output is not only determined by the input from current step but also the input from previous steps (Chen & Zhang, 2020). 
- Here, a bidirectional LSTM is selected because it considers both overlying and underlying steps when predicting for the current steps. Different parameters have been tested, including number of layers, number neurons, batch size, and epochs. By the early-stopping, the training process will be stopped once the loss is no longer decreasing.
##	2. Data and feature selection
Missing values (values equals to -999), whether they're from features or from targets, were dropped. The caliper (CAL) measures the borehole diameter and is physically irrelevant to travel times of compressional waves and shear waves. Thus, this feature was dropped before building the model. Extremely high values of neutron (CNC > 40) are regarded as data outliers, and they were dropped to make the training data cleaner.
## 3.	Results
Overall, the predicted curves of DTC and DTS are quite close to their true curves, except for some points where the predicted curves exhibit strong fluctuations while the true curves are smoother (Figure 1). These fluctuations are the major sources of prediction errors and are usually very difficult to predict (Figure 2).
 
##	4. Lessons learned
###	4.1 Batch size
The training performance is finished with a batch size of 16 and 21 epochs. As told by the competition committee, the training set is combined from 3 different wells. It would be better to avoid mixing samples from different training wells in a batch, as the long-term dependencies of LSTM are sensitive to spatial information of wells. One way of setting the batch size is to find the highest common factor of the number of samples among all the training wells.
### 4.2 Overfitting
Overfitting might be a problem, as the model training is only stopped once the MAE stops decreasing. In order to build a more solid model, a better protocol would be splitting the data into two sets: one for training and one for validation. In this situation, two of the three wells can be used as the training set, and the other well can be held as the validation set. The validation set would be very useful to evaluate the performance of the model training and thus to mitigate the overfitting issue.
### 4.3 Algorithm selection
Due to limited time, only LSTM and bidirectional LSTM were tested for this competition. Other RNN methods would be worthy of investigation in future work. Depth information, an indicator of the gap in well logs, can help analyze the spatial resolution of data and the selection of models. Furthermore, it can be incorporated to the inputs of physics-constrained RNN models. Unfortunately, this information is unavailable.
## 5.	References
N. Pham, & X. Wu. (2019). Missing sonic log prediction using convolutional long short-term memory. Society of Exploration Geophysicists.
Y. Chen and D. Zhang. (2020). Physics-Constrained Deep Learning of Geomechanical Logs. IEEE Transactions on Geoscience and Remote Sensing.
