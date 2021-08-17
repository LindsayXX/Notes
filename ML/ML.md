## Basics
### Data processing
#### 1. **Outliers**
- Identify: Background knowledge & Statistic (z score, )
- Cause
	- mismeasurement/bug in the system
	- unique behavior
	- => bias or random?
- Deal (depend on the cause&goal, think of the consequence)
	- Drop the observation
	- Fill it in as missing values (if it's incorrect)
	- Transformation
	- Test seperatly (with & without) - evalute
- Consequence
	- better model performance
	- not skewed analysis
	- ok to discard the extreme cases?
- Examples
#### 2. Missing values (/incorrect values)
- MCAR (rare)
	- deleting
	- may introduce bias
- Missing at random (depend on other variables)
	- imputing w/ KNN, Linear regression, mean, mode, median
	- may reduce data variance
- MNAR
	 - analysis why they are missing, collect more if possible
	 - simple imputation or deletion will lead to model bias
	 
#### 3. Imbalanced dataset
- collect more samples
- weighted sampling (over/under sampling)
- sample weight
	- unbalanced class e.g. weighted CEL
	- different weight on different samples e.g. ages, time
- look at metric: precision and recall
- generate systhetic data

### Feature engineering
#### 1. (Reduce) Data dimensions
- Curse of dimensionality
	- sparsely distributed makes data points *equally distant* and invalidates algos that based on distance metrics, e.g. euclidean distance
- Feature representation: from PCA to AE/NN
- Features Selection
	- Unsupervised, e.g.  Correlation
	- **Supervised**, e.g. decision tree, L1 regulation, correlation/statistic,  MARS
	    (- Wrapper: Search for well-performing subsets of features. e.g. RFE
	    (- Filter: Select subsets of features based on their relationship with the target. e.g. Statistical Methods, Feature Importance Methods
	    (- Intrinsic: Algorithms that perform automatic feature selection during training. e.g. Decision Trees)
	
#### 2. Engineer new features ?
- brainstrom / domain-specific knowledge
- based on current features - Automatic methods
	- unsupervised/self-supervised, e.g. PCA, AE, Umap, Kmeans, dbscan, etc.
	- supervised through auxillary task
- 

### Models
#### 1. Decision trees & Random Forest
- Decision trees
	- tree-based prediction (regression/classification) algo that sequentially ask questions regarding the values of certain features, e.g. predict if playing tennis based on weathers
	- train: split the data into subset according to the answer of the questions, and the goal is to find the best split that maximize the information gain (defination)
	- for regression? 
	- overfitting and avoid
		- pruning 
		- maximum depth
	- **Gini Impurity**: another defination of predictiablity
		- similar to entropy but more strongly peaked at equal probablities
		
- Random Forest -- ensemble of decision trees
	- **bagging** (bootstrap aggregation): training data is sampled with replacement from samples 
	- **feature randomness**: train on random subsets of features!
	- majority(classification) / average (regression)
	- vs. decision tree
		- reduce over-fitting
		

#### 2. SVM
- To find a *hyperplane* (--decision boundary) in an N-dim feature space that can distinctly classifies the data points
- by Maximize the *margin* -- distance of decision boundary between **both** classes
- cost function: dual problem formulation
	- reform to Hinge loss
- **kernels**: 
	- by transforming, we can find a (linear) hyperplan that can seprate classes
	- by using kernel trick, we can transform without having to optimize in the high-dim space (implicitly) becuase optimization function only involved the dot product of two transformed data points.
		
- support vectors: data points on the margin that will affect the decision boundary
- slack variables
	- C-value: penalty
	- additional part on cost function = regularzation
- multi-class: one-vs-all
	
#### 3. Neural Networks
- batchnorm - covariante shift
- activation functions
	- ReLU $max (0, x)$ 
		- for vanishing gradient
		- dying ReLU - high LR - leaky ReLU
	- sigmoid $\frac{1}{1 + e^{-z}}$
	- softmax $\frac{e^z_i}{\sum^K_{j=1}e^{z_j}}$
	- 
- dropout - overfitting 
- residual 
	- skip connection
- optimizer
	- adam, lr + momentum
	- sgd, learning rate
- normalization
- mini batch - SGD
- Gradient
	- vanishing gradient
		- back-propogation
		- ReLU
	- exploding gradient
		- gradient clip
		- weight initial
		- batch norm
		- residual 
	
#### 4. KNN v.s. K-means
- K-means
	- unsupervised clustering method
	- 
- KNN 
	- non-perametric classification / regression
#### 5. Ensemble methods
- Boosting
	- iterative technique which adjust the weight of an observation based on the last classification
	- 
- Bagging vs. Boosting
	- Bagging decrease variance, not bias
		- avoid over-fitting
		- handles higher dimensionality data well
		- maintains accuracy for missing data
		- each model is independent of the other, therefore can be performed in parallel
		- Cons: not interpretable, computationally expensize
	- Boosting decreases bias, not variance
		- works well with interactions
		- Cons: performs poorly if the data has a high variance, computationally expensive
- majority vote, mean, blending (train again on outputs/results)

#### 6. Linear regression vs. tree-based models
- LR vs Desicion Tree
	- desicion trees support non-linearity, where LR supports only linear solutions
	- when there are large number of features with less data (with low noise), LR may outperform DT/RF. In general cases, DT will have better average accuracy
	- LR is a statstical model with better interpretation
	- DT handles colinearity better than LR
	- For categorical independent variables, decision tree are better than linear regression
		- LR will likely to add bias 
		- LR will need to introduce cross-variable

#### 7. 5 Assumptions of linear regression
- **L**inear relationship
- Multivariate **N**ormality (only for Y) 
	- histogram, Q-Q plot
	- goodness of fit test: Kolmogorov-Smirnov test
- No or little multicollinearity 
	- VIF (=1/Tolerance)
- No auto-correlation -- samples are **I**ndependent
- **E**qual variance/Homoscedasticity: variances of the residuals should be equal along the regression line

#### 8. Logistic Regression
- one layer NN with sigmoid as activation function
- loss: **cross-entropy** 
- can be used for multi-class classification also
- 

#### 9. Deep learning
- RNN
	- 
- CNN
	- reception field
- Reco sys
	- 
### Modeling details
#### 1. Overfitting
- what is
- why/cause
	- non-representitive data
	- noisy examples
	- too complex model
- how to deal
	- simpler model
	- regularization
	- bagging
	- for gradient boosting: early stopping
	- for NN: early stopping, dropout, add noise
	- validation set

#### 2. L1, L2 regularization
- L1/LASSO: regularization for sparsity
	- more robust with more than one solution
	- suitable when there are few important features
- L2/Ridge: regularization for simplicity 
	- less robust with only one solution
	- suitable for many variable with small/medium effects
	- when there are colinear features
	- weight decay
#### 3. hyperparameters
- parameters that are choose to control the learning process 
- choose a set of hyperparemeters, train and validate on validation set
	- random search, grid search
	- **bayesian optimization**?
	- genetic algo
#### 4. model comparsion
- Bias and variance trade-off
	- bias: prediction error (difference between average/expected prediction and the correct value)
	- variance: how model vary depending on data (the variability of a model prediction for a given data point between different realizations of the model.)
	 - simple model (high b, low v) vs complex model (low b, high v)
 - Other advantages of simple models
 - Selection bias
	 - from the data we collected
### Model Evaluation
#### 1. evaluation metrics for classification and regression
- Classification
	- Accuracy
	- Precision, Recall
	- F1 score
		- harmonic mean of precision and recall
		- 2 *  (precision  *  recall) /(precision + recall)
		- precision and recall are evenly weighted
	- Sensitivity, specificity
		- specificity: true negative rate TN / (TN + FP)
		- sensitivity = recall = hit rate
	- Confusion matrix
	- AUC, ROC curve
		- ROC curve: trade-off between sensitivity (or TPR) and specificity (1 – FPR)
		- 
- Regression
	- Mean Square Error
	- Root MSE
		- same unit, to evaluate and report
	- Mean Absolute Error
		- changes in MAE are linear and therefore intuitive.
		- MSE and RMSE punish larger errors more than smaller errors
	
#### 2. validation, cross-validation
- validation
- cross-validation
#### 3.  Precision and recall (trade-off)
- Precision: of all the samples that predicted positive (TP + FP), how many are correct (TP)
- Recall: of all the positive samples (TP + FN), how many are predicted correctly (TP) 
- Improved precision for ranking: (Normalized) Discounted cumulative gain
- Improved recall for ranking 
	- Normalized Discounted Cumulative Gain
- Improved recall: AP, 
#### 4.  Difference between ROC curve and precision-recall curve?
- ROC curve is sensitivity (recall) and specificity 
	- trade-off between catching positive samples v.s. negative samples
- Precision-recall curve
	- trade-off between catching positive samples vs. positive predictive value (performance)
	- mirror with ROC curve
	- AUC or precision-recall is average precision (AP)
