# Machine Learning Interview Questions (70+ Questions)

## General ML Concepts

**Q1: What is the difference between supervised, unsupervised, and reinforcement learning?**
A: Supervised uses labeled data (input-output pairs) for prediction - classification and regression. Unsupervised finds patterns in unlabeled data - clustering and dimensionality reduction. Reinforcement learning uses rewards/penalties for an agent to learn optimal actions through trial and error. Example: Supervised = spam detection, Unsupervised = customer segmentation, RL = game-playing AI.

**Q2: What is the No Free Lunch Theorem in ML?**
A: No single algorithm is universally best for all problems. The performance of all algorithms averaged across all possible problems is equal. In practice: you must match the algorithm to the problem, data characteristics, and constraints. Always try multiple approaches.

**Q3: Explain the difference between parametric and non-parametric models.**
A: Parametric models have a fixed number of parameters (e.g., linear regression coefficients). Complexity doesn't grow with data. Fast to train but high bias. Non-parametric models' complexity grows with data (e.g., k-NN, decision trees). Flexible but can overfit and are slower at prediction. The choice depends on data size and problem complexity.

**Q4: What is the difference between a generative and discriminative model?**
A: Generative models learn P(X, Y) - the joint distribution - and can generate new data. Examples: Naive Bayes, GMM, GANs. Discriminative models learn P(Y|X) - the decision boundary between classes. Examples: Logistic Regression, SVM, Neural Networks. Discriminative models typically perform better with more data; generative models work better with less data and handle missing values naturally.

**Q5: What is the bias-variance tradeoff and how do you manage it?**
A: Bias is error from oversimplifying (underfitting). Variance is error from being too sensitive to training data (overfitting). Total error = Bias² + Variance + Irreducible Error. Manage by: regularization (increases bias, decreases variance), more data (decreases variance), model complexity tuning, ensemble methods (bagging reduces variance, boosting reduces bias).

**Q6: What is the difference between bagging and boosting?**
A: Bagging (Bootstrap Aggregating) trains models in parallel on bootstrap samples and averages predictions. Reduces variance. Example: Random Forest. Boosting trains models sequentially, each correcting previous errors. Reduces bias. Examples: AdaBoost, Gradient Boosting, XGBoost. Bagging is simpler and less prone to overfitting; boosting achieves higher accuracy but requires careful tuning.

**Q7: What is the difference between L1 (Lasso) and L2 (Ridge) regularization?**
A: L1 adds λΣ|w_i| to the loss function. It creates sparsity (shrinks some weights to exactly zero) - useful for feature selection. L2 adds λΣw_i² to the loss function. It shrinks all weights evenly toward zero but never to zero - useful when all features matter. L1 objective: minimize loss + λ|w|. L2: minimize loss + λ||w||². Elastic Net combines both.

**Q8: How does early stopping work?**
A: Early stopping monitors validation performance during training. When validation loss stops improving for a specified number of epochs (patience), training stops. Prevents overfitting by finding the optimal point between underfitting and overfitting. Used extensively in neural networks and gradient boosting.

**Q9: What is the difference between batch learning and online learning?**
A: Batch learning trains on the entire dataset at once. Requires retraining from scratch for new data. Online (incremental) learning updates the model one sample or mini-batch at a time. Good for streaming data, large datasets that don't fit in memory, and real-time applications. Examples: SGDClassifier with partial_fit, streaming ML with Kafka.

**Q10: What is the difference between feature selection and feature extraction?**
A: Feature selection chooses a subset of original features (keeps interpretability). Feature extraction (PCA, autoencoders) creates new features as combinations of original ones (loses interpretability). Selection is preferred when interpretability matters; extraction is preferred for visualization or removing multicollinearity.

**Q11: Explain the concept of "data leakage" and give examples.**
A: Data leakage is when information from outside the training set leaks into the model, causing over-optimistic performance. Examples: (1) Scaling before train/test split, (2) Using target variable to engineer features, (3) Using future data in time series, (4) Including duplicates or overlapping data between train and test, (5) Feature selection using the entire dataset.

**Q12: What is the difference between a "model" and an "algorithm"?**
A: An algorithm is the procedure used to learn from data (e.g., gradient descent). A model is the output of the algorithm - the learned representation (e.g., learned coefficients in linear regression). Algorithm = learning process, Model = learned result.

**Q13: What is the difference between statistical modeling and machine learning?**
A: Statistical modeling focuses on inference (understanding relationships, hypothesis testing, confidence intervals). ML focuses on prediction accuracy (minimizing error on unseen data). They overlap significantly, but the emphasis differs. In practice, both are important. Linear regression is both a statistical model and an ML algorithm.

**Q14: What is MLOps and why is it important?**
A: MLOps is the practice of deploying, monitoring, and maintaining ML models in production. It includes: version control for data and models, CI/CD for ML pipelines, model monitoring (data drift, concept drift), automated retraining, and governance. Important because most ML models fail in deployment - MLOps ensures reliable, scalable, and maintainable ML systems.

**Q15: What is the difference between ML and DL?**
A: Deep Learning is a subset of ML using multi-layer neural networks. DL automatically learns hierarchical features from raw data (no manual feature engineering). ML requires careful feature engineering. DL needs more data and compute. DL excels at unstructured data (images, text, audio). ML is often sufficient and preferred for structured/tabular data.

## Gradient Boosting & Tree-Based Models

**Q16: Explain Gradient Boosting.**
A: Gradient Boosting builds trees sequentially, where each new tree corrects the errors of the previous ensemble. It fits each tree to the negative gradient (residuals) of the loss function. Steps: (1) Start with a simple prediction (mean). (2) Compute residuals (actual - prediction). (3) Train a tree to predict residuals. (4) Add the tree to the ensemble with a learning rate. (5) Repeat for n_estimators iterations.

**Q17: What is the difference between XGBoost, LightGBM, and CatBoost?**
A: All are gradient boosting frameworks with optimizations:
- XGBoost: Level-wise tree growth, handles missing values natively, regularization in objective. Good default choice.
- LightGBM: Leaf-wise tree growth (faster, more memory efficient), Gradient-based One-Side Sampling (GOSS), Exclusive Feature Bundling (EFB). Best for large datasets.
- CatBoost: Handles categorical features natively (no encoding needed), ordered boosting to reduce overfitting. Best when many categorical features.
XGBoost is the most mature; LightGBM is fastest for large data; CatBoost is easiest for categorical data.

**Q18: What is the learning rate in gradient boosting?**
A: Learning rate (η) scales the contribution of each tree: new_pred = prev_pred + η × tree_prediction. Smaller η (0.01-0.1) requires more trees but generalizes better. Tradeoff: η × n_estimators = effective total learning. Typical approach: set η = 0.1, tune n_estimators via early stopping.

**Q19: What is the difference between level-wise and leaf-wise tree growth?**
A: Level-wise (XGBoost default): grows all nodes at current depth before going deeper. Produces balanced trees. Leaf-wise (LightGBM default): grows the leaf with largest loss reduction. Produces asymmetric, deeper trees. Leaf-wise converges faster but can overfit (controlled by num_leaves and min_data_in_leaf).

**Q20: How does XGBoost handle missing values?**
A: XGBoost learns the best direction (left or right branch) for missing values during training. For each split, it calculates the loss gain for sending missing values left vs right and picks the better direction. This means no imputation is needed. During prediction, missing values follow the learned default direction.

**Q21: What is SMOTE and how does it work?**
A: SMOTE (Synthetic Minority Oversampling Technique) creates synthetic samples for the minority class to handle imbalanced data. It selects a minority sample, finds its k nearest minority neighbors, and creates new samples by interpolating between the sample and a randomly chosen neighbor. new_sample = sample + random(0,1) × (neighbor - sample). This creates realistic synthetic samples rather than just duplicating.

**Q22: What is the difference between SMOTE and ADASYN?**
A: Both are oversampling techniques. SMOTE creates equal numbers of synthetic samples for all minority instances. ADASYN creates more synthetic samples for minority instances that are harder to learn (nearer to majority class). ADASYN focuses on the decision boundary; SMOTE distributes evenly. ADASYN can be noisier as it focuses on borderline regions.

**Q23: How do you handle imbalanced datasets besides SMOTE?**
A: (1) Algorithm-level: class weights, cost-sensitive learning. (2) Data-level: oversampling minority, undersampling majority (RandomUnderSampler, TomekLinks, ENN). (3) Ensemble: BalancedRandomForest, EasyEnsemble, RUSBoost. (4) Threshold tuning: adjust decision threshold to optimize F1 or precision-recall. (5) Anomaly detection approach for extreme imbalance.

**Q24: What is the difference between Gini importance and permutation importance?**
A: Gini importance (default in tree models): total reduction in Gini impurity from splits on a feature. Biased toward high-cardinality features. Permutation importance: shuffles a feature's values and measures the drop in model performance. Model-agnostic and more reliable. Permutation importance is preferred for understanding feature impact in production.

**Q25: What is SHAP and how is it different from feature importance?**
A: SHAP (SHapley Additive exPlanations) uses game theory to explain individual predictions. It shows each feature's contribution to a specific prediction. Unlike global feature importance, SHAP provides local explanations and shows direction of impact (positive/negative). SHAP is more informative but computationally expensive. Used to explain why a specific loan was denied or fraud flagged.

## Dimensionality Reduction & Feature Engineering

**Q26: What is t-SNE and how is it different from PCA?**
A: t-SNE (t-Distributed Stochastic Neighbor Embedding) is a non-linear dimensionality reduction technique for visualization. It preserves local structure (nearby points in high-D stay nearby in low-D). PCA is linear and preserves global structure. t-SNE is stochastic (different runs give different results) and should not be used for feature extraction or inference - only visualization.

**Q27: What is UMAP and how is it better than t-SNE?**
A: UMAP (Uniform Manifold Approximation and Projection) is a non-linear dimensionality reduction method that is faster than t-SNE, better preserves global structure, and scales to much larger datasets. UMAP has a more solid mathematical foundation (manifold learning + topological data analysis). For large-scale visualization, use UMAP; for small datasets, t-SNE is still fine.

**Q28: What is the difference between PCA and LDA?**
A: PCA is unsupervised (ignores labels) and finds directions of maximum variance. LDA (Linear Discriminant Analysis) is supervised and finds directions that maximize class separation. LDA is better for classification pre-processing. PCA is more general and widely used. LDA can only reduce to at most (num_classes - 1) dimensions.

**Q29: What is the difference between CountVectorizer and TfidfVectorizer?**
A: CountVectorizer creates a matrix of raw word counts. TfidfVectorizer uses TF-IDF (Term Frequency - Inverse Document Frequency): TF-IDF = TF × log(1 + N/DF), where N = total documents, DF = documents containing the word. TF-IDF downweights common words (the, and, of) and highlights distinctive words. In practice, TF-IDF nearly always outperforms raw counts.

**Q30: What is word embedding and how is it different from bag-of-words?**
A: Bag-of-words represents text as sparse vectors of word counts (or TF-IDF), ignoring word order and semantics. Word embeddings (Word2Vec, GloVe, FastText) learn dense vector representations where similar words have similar vectors. Embeddings capture semantic relationships: king - man + woman ≈ queen. Embeddings are more informative but require more data to train.

**Q31: What is feature scaling and when is it necessary?**
A: Feature scaling transforms features to a similar scale. Necessary for: Linear Regression, Logistic Regression, SVM, KNN, PCA, Neural Networks (distance-based and gradient-based methods). Not necessary for: Decision Trees, Random Forest, Gradient Boosting (threshold-based methods). Always scale for models that use distance or gradient.

**Q32: How do you detect and remove multicollinearity?**
A: Detection: (1) Correlation matrix - |r| > 0.8 indicates high correlation. (2) VIF (Variance Inflation Factor) - VIF > 10 indicates serious multicollinearity. Removal: (1) Drop one of the correlated features. (2) PCA to create uncorrelated components. (3) Ridge Regression (handles multicollinearity without dropping features). (4) Combine correlated features into a single feature.

**Q33: What is the difference between label encoding, one-hot encoding, and ordinal encoding?**
A: Label encoding: assigns integers 0,1,2... to categories (implies order). One-hot: creates binary columns for each category (no order implied). Ordinal encoding: explicitly defines the order (small=0, medium=1, large=2). Use label/ordinal for ordinal data. Use one-hot for nominal data with few categories. Use target encoding for nominal data with many categories.

## Model Evaluation & Selection

**Q34: What is the difference between underfitting and overfitting? How do you detect them?**
A: Underfitting: model too simple, makes many errors on both train and test. Detection: low train accuracy, low test accuracy. Overfitting: model memorizes noise, performs well on train but poorly on test. Detection: high train accuracy, low test accuracy, large gap between train and test metrics.

**Q35: How do you choose between different models?**
A: (1) Define success metrics (accuracy, F1, RMSE, business KPIs). (2) Start with simple baselines (mean, linear model). (3) Use cross-validation to estimate performance. (4) Consider interpretability needs. (5) Consider computational constraints (training time, inference time, memory). (6) Trade off: complex models (gradient boosting, neural nets) often perform better but require more resources and data.

**Q36: What is the difference between stratified cross-validation and regular k-fold?**
A: Regular k-fold: randomly splits data into k folds. Stratified: preserves class proportions in each fold, matching the overall dataset. Use stratified for classification with imbalanced data. Use regular k-fold for regression or balanced classification. TimeSeriesSplit for time series.

**Q37: What is the difference between grid search and random search?**
A: Grid search: tries all combinations of specified hyperparameter values. Exhaustive but computationally expensive. Random search: randomly samples hyperparameter combinations from distributions. More efficient - it finds good combinations faster. For hyperparameters with diminishing returns (most real cases), random search is preferred. Bayesian optimization is even more efficient.

**Q38: What is Bayesian hyperparameter optimization?**
A: Bayesian optimization builds a probabilistic model (Gaussian Process) mapping hyperparameters to performance. It uses an acquisition function to decide which hyperparameters to try next, balancing exploration (try uncertain regions) and exploitation (try known good regions). More efficient than grid/random search - typically finds better hyperparameters in fewer iterations. Libraries: Hyperopt, Optuna, Scikit-Optimize.

**Q39: What is the difference between validation and test set?**
A: Validation set: used during model development for hyperparameter tuning and model selection. Used many times. Test set: used only once at the end to estimate real-world performance. Never tune on test data. A common split: 60% train, 20% validation, 20% test.

**Q40: What is the difference between Type I and Type II errors?**
A: Type I error (False Positive): incorrectly rejecting the null hypothesis. "False alarm." Example: predicting fraud when transaction is legitimate. Type II error (False Negative): failing to reject a false null hypothesis. "Miss." Example: not catching actual fraud. Tradeoff: decreasing Type I increases Type II and vice versa.

**Q41: What is the difference between correlation and covariance?**
A: Covariance measures how two variables move together (direction). Correlation standardizes covariance by dividing by standard deviations (both direction and strength, range [-1, 1]). Correlation is scale-independent; covariance is scale-dependent. Correlation is preferred for measuring relationships.

**Q42: What is the relationship between p-value and confidence interval?**
A: A 95% confidence interval that does not include zero corresponds to p < 0.05. CI provides a range of plausible effect sizes, while p-value only gives a binary reject/accept. CI is more informative than p-value. In ML, we often prefer effect sizes and confidence intervals over hypothesis testing.

**Q43: What is the multiple testing problem and how do you address it?**
A: When testing many hypotheses simultaneously, the chance of false positives increases. If you test 100 features at α=0.05, expect 5 false positives by chance. Corrections: Bonferroni (α/m, very conservative), FDR (False Discovery Rate - Benjamini-Hochberg, less conservative). In ML, feature selection during cross-validation naturally handles this.

**Q44: How do you handle concept drift in production ML?**
A: Concept drift occurs when the relationship between features and target changes over time. Detection: monitor model performance metrics over time, statistical tests (Kullback-Leibler divergence, Population Stability Index). Response: (1) Retrain periodically (fixed schedule). (2) Trigger-based retraining (when drift detected). (3) Online learning continuously updates. (4) Ensemble with older and newer models.

**Q45: What is the difference between data drift and concept drift?**
A: Data drift (covariate shift): distribution of input features changes. Example: customer demographics change over time. Concept drift: relationship between features and target changes. Example: customer behavior during COVID is different from pre-COVID. Both require model retraining, but the solutions differ. Data drift needs feature distribution monitoring; concept drift needs performance monitoring.

## Deep Learning & Neural Networks

**Q46: What is the difference between a perceptron and a neural network?**
A: A perceptron is a single artificial neuron with step activation - can only learn linearly separable patterns (like AND, OR). A neural network stacks multiple layers of neurons with non-linear activations (ReLU, sigmoid, tanh). Multi-layer networks are universal approximators - can learn any function. Deep learning uses many layers to learn hierarchical representations.

**Q47: Why is ReLU preferred over sigmoid in hidden layers?**
A: ReLU (max(0, x)): (1) No vanishing gradient problem (gradient is 1 for positive inputs). (2) Computationally cheap (just max operation). (3) Sparse activation (many neurons output 0). Sigmoid: saturates at 0 and 1, causing vanishing gradients in deep networks. Sigmoid is still used for output layers in binary classification. ReLU can cause "dead neurons" - LeakyReLU and ELU address this.

**Q48: What is the vanishing gradient problem?**
A: In deep networks with sigmoid/tanh, gradients become extremely small as they backpropagate through many layers. Early layers learn very slowly or stop learning. Solutions: ReLU activation, batch normalization, residual connections (ResNet), LSTM for RNNs, proper weight initialization (He, Xavier).

**Q49: What is batch normalization and why is it used?**
A: Batch normalization normalizes each layer's inputs to have zero mean and unit variance. Benefits: (1) Allows higher learning rates (faster convergence). (2) Reduces internal covariate shift. (3) Provides some regularization (slight noise from mini-batch statistics). (4) Reduces sensitivity to weight initialization. Used as a layer after activation in most modern architectures.

**Q50: What is dropout and how does it prevent overfitting?**
A: Dropout randomly sets a fraction (typically 0.2-0.5) of neuron outputs to zero during training. It prevents units from co-adapting and forces the network to learn redundant representations. This is equivalent to training an ensemble of subnetworks. Dropout is only active during training; at inference, all neurons are used but scaled down.

**Q51: What is the difference between SGD, Adam, and RMSprop?**
A: SGD: basic optimizer, updates weights in direction of negative gradient. May oscillate and converge slowly. Adam: combines momentum (accelerates in consistent directions) + adaptive learning rates per parameter. Most popular default optimizer. RMSprop: adaptive learning rates, divides by moving average of squared gradients. Good for RNNs. Adam is the safest default choice.

**Q52: What is transfer learning and when would you use it?**
A: Transfer learning takes a pre-trained model (trained on a large dataset) and fine-tunes it on a smaller, related dataset. Used when: (1) Limited labeled data, (2) Limited compute resources, (3) Need faster training. Example: Use ImageNet-trained ResNet for medical image classification. Fine-tune last few layers while freezing early layers (feature extractors).

**Q53: What is the difference between CNNs and RNNs?**
A: CNNs (Convolutional Neural Networks) use convolution operations to capture spatial patterns. Designed for grid-like data: images, videos. RNNs (Recurrent Neural Networks) have loops to capture sequential patterns. Designed for time series, text, audio. CNNs are parallelizable; RNNs are sequential. Modern approach: Transformers are replacing RNNs for sequential data.

**Q54: What is an autoencoder?**
A: An autoencoder is a neural network that learns to reconstruct its input. It has a bottleneck (encoder) that compresses data and a decoder that reconstructs it. Uses: dimensionality reduction, anomaly detection (high reconstruction error = anomaly), denoising, feature learning. Unlike PCA, autoencoders can learn non-linear transformations.

**Q55: What is the difference between supervised pretraining and self-supervised learning?**
A: Supervised pretraining uses labeled data (e.g., ImageNet labels for image models). Self-supervised learning creates labels from the data itself (e.g., predicting masked words in BERT, predicting rotation angle, contrastive learning). Self-supervised learning enables using massive unlabeled data for pretraining, then fine-tuning on small labeled datasets.

## Advanced Topics & Production

**Q56: What is the difference between XGBoost and Random Forest?**
A: XGBoost is gradient boosting (sequential trees correcting errors). Random Forest is bagging (parallel trees averaging predictions). XGBoost reduces bias; Random Forest reduces variance. XGBoost usually performs better on structured/tabular data but requires more hyperparameter tuning. Random Forest is simpler and works well out of the box. XGBoost includes built-in regularization, handles missing values, and supports custom loss functions.

**Q57: What is LightGBM and when should you use it?**
A: LightGBM is Microsoft's gradient boosting framework with optimizations: leaf-wise tree growth (faster than level-wise), Gradient-based One-Side Sampling (GOSS - focuses on high-gradient samples), Exclusive Feature Bundling (EFB - bundles sparse features). Best for: large datasets, high-dimensional data, when training speed is critical. LightGBM can handle millions of rows efficiently.

**Q58: What is CatBoost and when should you use it?**
A: CatBoost (Yandex) is a gradient boosting framework that handles categorical features natively - no preprocessing needed. Uses ordered boosting (an algorithmic improvement to reduce overfitting when training on small datasets). Best for: datasets with many categorical features, when you want minimal preprocessing, education/competition settings. CatBoost is the easiest to use of the three main boosting libraries.

**Q59: What is the difference between hard voting and soft voting in ensemble methods?**
A: Hard voting: majority vote among classifiers (each classifier gets one vote). Soft voting: averages predicted probabilities from each classifier. Soft voting generally performs better because it weights predictions by confidence. Example: Classifier A predicts class 1 with 99% confidence, classifier B predicts class 0 with 51%. Hard voting gives class 1; soft voting gives class 0.

**Q60: What is stacking in ensemble learning?**
A: Stacking combines multiple models using a meta-model. Level 0: diverse base models (Random Forest, SVM, Neural Net). Level 1: meta-model trained on base model predictions. The meta-model learns which base models to trust. Stacking often achieves the best performance but is complex and prone to overfitting if not done carefully with proper cross-validation.

**Q61: What is the difference between precision-recall curve and ROC curve?**
A: Both evaluate classifier performance. ROC (TPR vs FPR) is optimistic for imbalanced data - high ROC-AUC can coexist with poor precision. Precision-Recall curve is more informative for imbalanced classification. Use ROC when classes are balanced; use Precision-Recall when the positive class is rare.

**Q62: What is the F-beta score?**
A: Fβ = (1+β²) × (precision × recall) / (β² × precision + recall). β controls the weight of recall vs precision. β=1: F1 (equal weight). β>1: more weight on recall (catch all positives). β<1: more weight on precision (avoid false positives). Example: Cancer screening uses β=2 (recall is more important). Spam detection uses β=0.5 (precision is more important).

**Q63: What is the difference between L1 and L2 loss functions?**
A: L1 loss (MAE) = |y - ŷ|. More robust to outliers - doesn't penalize large errors quadratically. L2 loss (MSE) = (y - ŷ)². Punishes large errors heavily - sensitive to outliers. L2 is differentiable everywhere (nice for optimization). L1 has non-differentiable point at zero. Huber loss combines both: quadratic for small errors, linear for large errors.

**Q64: What is the CAP curve and how is it different from ROC?**
A: CAP (Cumulative Accuracy Profile) curve plots the cumulative number of positive cases vs total cases sorted by predicted probability. The "lift" is the ratio of model performance to random performance. CAP is more interpretable for business stakeholders than ROC. Example: "Our model captures 80% of fraud cases by reviewing only 10% of transactions."

**Q65: How do you handle time series in ML?**
A: Special considerations: (1) No shuffling - respect temporal order. (2) Use TimeSeriesSplit for cross-validation. (3) Create lag features (yesterday's value, last week's average). (4) Rolling statistics (7-day moving average, 30-day std). (5) Seasonal decomposition. (6) Stationarity tests (ADF test). (7) Models: ARIMA, Prophet, LSTM, XGBoost with engineered time features.

**Q66: What is the difference between ARIMA and Prophet?**
A: ARIMA is a traditional time series model (AutoRegressive Integrated Moving Average). Handles trends and seasonality but requires data to be stationary. Prophet (Facebook) is designed for business forecasting - handles missing values, outliers, holidays, and seasonality automatically. Prophet is easier to use and more robust; ARIMA requires more expertise but can be more accurate with clean data.

**Q67: What is the difference between bagging, boosting, and stacking?**
A: Bagging: parallel training on bootstrap samples (reduces variance). Boosting: sequential training correcting previous errors (reduces bias). Stacking: meta-model combining diverse base models (can reduce both). Bagging is simplest and hardest to overfit. Boosting achieves highest accuracy on structured data. Stacking captures best of all worlds.

**Q68: What is the difference between a parametric and non-parametric model?**
A: Parametric models (linear regression, logistic regression): fixed number of parameters, fast to train, require fewer data points, high bias, assume functional form. Non-parametric models (k-NN, decision trees, SVMs): parameters grow with data, slower prediction, need more data, flexible, no distributional assumptions.

**Q69: Explain the concept of "curse of dimensionality" in detail.**
A: As dimensions increase: (1) Data becomes sparse (points are far apart). (2) Distances between all points converge to similar values (meaningless). (3) The volume increases exponentially - need exponentially more data to maintain density. (4) Noise dominates signal. Rule of thumb: need 10× training samples for each additional dimension. Solutions: feature selection, dimensionality reduction (PCA), domain knowledge to create informative features.

**Q70: What is the difference between normalization and standardization?**
A: Normalization (MinMaxScaler): scales data to [0,1] range. x' = (x - min) / (max - min). Standardization (StandardScaler): scales to zero mean, unit variance. x' = (x - μ) / σ. Normalization is sensitive to outliers (min/max affected). Standardization is more robust and preserves outlier information. Use standardization for most ML algorithms; use normalization for neural networks with bounded activations or when data naturally has bounds.

**Q71: How do you deploy an ML model to production?**
A: Steps: (1) Save trained model (pickle, joblib, ONNX). (2) Create prediction API (Flask, FastAPI). (3) Containerize with Docker. (4) Deploy on cloud (AWS SageMaker, GCP AI Platform, Azure ML). (5) Set up monitoring (latency, throughput, prediction drift). (6) Set up retraining pipeline. Alternative: batch predictions (Spark, scheduled jobs). Edge deployment (TensorFlow Lite, ONNX Runtime) for mobile/IoT.

**Q72: What is A/B testing in ML?**
A: A/B testing compares the current model (control) against a new candidate model (treatment). Users are randomly split. Metrics (conversion rate, revenue, CTR) are compared. Statistical significance tests determine if the new model is better. Important: (1) Run long enough for statistical power. (2) One metric at a time. (3) Watch for novelty effects. (4) Segment analysis.

**Q73: What is the difference between online and batch prediction?**
A: Online (real-time) prediction: model predicts for individual requests in milliseconds. Used for fraud detection, recommendations, search ranking. Batch prediction: model predicts for large datasets in scheduled jobs. Used for daily credit scoring reports, weekly customer churn lists, monthly sales forecasts.

**Q74: Explain how you would build an end-to-end ML system.**
A: (1) Problem definition: business goal, success metrics. (2) Data collection: gathering, labeling, validation. (3) EDA: distributions, correlations, missing values. (4) Feature engineering: encoding, scaling, creation. (5) Model selection: baselines, candidate algorithms. (6) Training: cross-validation, hyperparameter tuning, ensembles. (7) Evaluation: test set, business metrics, fairness. (8) Deployment: API, monitoring, logging. (9) Maintenance: retraining, drift detection, A/B testing.

**Q75: What is the difference between reproducibility and replicability?**
A: Reproducibility: same data + same code = same results. Replicability: different data + same method = consistent findings (generalization). In ML, reproducibility requires: (1) Set random seeds, (2) Version control for data + code, (3) Track hyperparameters, (4) Log experiments (MLflow, Weights & Biases). Reproducibility is critical for scientific integrity and debugging.

## Hyperparameter Tuning (Bonus Section)

**Q76: What are hyperparameters and how are they different from model parameters?**
A: Model parameters are learned from data (coefficients, tree splits). Hyperparameters are set BEFORE training and control the learning process (learning rate, max_depth, n_estimators). Example: In Linear Regression, coefficients are parameters; whether to use L1 or L2 regularization is a hyperparameter.

**Q77: Compare Grid Search, Random Search, and Bayesian Optimization.**
A: Grid Search: tries all combinations exhaustively. Simple but wasteful - many hyperparameters don't affect performance equally. Random Search: samples from distributions. More efficient - finds good regions faster. Bayesian Optimization: uses past evaluations to choose next hyperparameters. Most efficient but requires sequential computation. Rule: use random search for most cases; use Bayesian for expensive evaluations (deep learning).

**Q78: What is Hyperband and how does it accelerate hyperparameter tuning?**
A: Hyperband uses early stopping to allocate resources efficiently. It runs many configurations with few resources (few epochs, few iterations), eliminates poor performers, and continues only the promising ones. This finds good hyperparameters faster than random search. Used in: Optuna, Keras Tuner. Particularly valuable when training is expensive.

**Q79: How do you select hyperparameters for production?**
A: (1) Start with defaults (most libraries have good defaults). (2) Use random search with 30-60 iterations. (3) Validate via cross-validation. (4) Use a validation set separate from the test set. (5) Consider deployment constraints: large n_estimators means slow inference. (6) Monitor: hyperparameters that worked last month may need adjustment as data evolves.

**Q80: What is the difference between manual search, automated search, and AutoML?**
A: Manual: expert chooses hyperparameters based on experience. Fast but suboptimal. Automated search: Grid/Random/Bayesian/Evolutionary search. Systematic but computationally expensive. AutoML (AutoKeras, AutoGluon, TPOT): automates entire pipeline (model selection + hyperparameter tuning + feature engineering + ensemble). Best for non-experts or baseline building. AutoML rarely beats expert-tuned models but provides competitive baselines quickly.

---

*This comprehensive list covers fundamental ML concepts, tree-based models, gradient boosting, dimensionality reduction, model evaluation, deep learning, production ML, and hyperparameter tuning. Good luck with your interview!*
