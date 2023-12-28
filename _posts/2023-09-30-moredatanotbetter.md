---
title: Using less data and appropriate technique to get reliable AI 
date: 2023-7-1 15:00:00 +0700
categories: [data management]
tags: [big data utilization, data investment, common believes]
--- 

# **More data does NOT mean better model**
  
Many individuals harbor the belief that augmenting the volume of data and feeding it into a model will inherently enhance the model's performance. This is a prevalent misconception that has taken root among some managements, either IT related managers or non-IT. However, from both data science practices and classical statistical perspectives, this is not universally accurate. I will illustrate this concept with empirical examples, elucidating when more data can be beneficial and when it may not be advantageous in the realm of enterprise AI initiatives.

For this illustration, I will reference a previous project detailed in one of my previous posts Predicting Productivity - Baseline model. In this project, there were 103 variables or features were conceived through collaborative brainstorming with Subject Matter Experts (SMEs) at the project sites. These features were derived from raw GPS data collected along with other IoT devices, potentially containing inaccuracies and anomalies. Data preparation and cleansing, while essential, fall outside the scope of this article.

The initial run of the project employed three algorithms: XGBoost, CatBoost, and Random Forest. One of these algorithms yielded the best R-squared value of 77%, with other parameters aligning with this optimal R-squared figure. The subsequent phase aims to enhance the model's performance to achieve an R-squared value of at least 90%. The choice of this threshold is intertwined with the expected cost-benefit considerations necessary to render the prediction model valuable and capable of delivering business value. Any prediction accuracy below 92% will render the benefit from prediction into meaningless. This topic will be addressed in a separate discussion on AI ROI, which also grapples with its own set of generalizations.

#### *Additional data & features*
Initially, I enriched the dataset by incorporating new variables sourced from the valuable insights and observations of our Subject Matter Experts (SMEs) who closely monitored daily operations. This enhancement introduced 14 additional features, which encompassed aspects such as lagging truck numbers in segment 6, queues at the weight bridge, and the status change of trucks from active to standby, among others (with some data classified as confidential). The effect of these additions was most pronounced in the case of the XGBoost algorithm, which experienced a whoping 7+% increase in the R-squared metric compared to its performance in the first iteration. In contrast, the other two algorithms only saw a maximum 5% improvement. With these changes, the model now incorporates a total of 117 features, culminating in an exceptional R-squared value of approximately 85% in our predictive model. For parameterse selection and steps refer to my post Predicting Productivity - Baseline model.
 
##### Model test result
![cbc xgb val85](</assets/img/moredata/85 test result.PNG>)

##### Model validation result
![cbc xgb val85](</assets/img/moredata/85 val result.PNG>)


#### *Reducing features & data*
I intentionally positioned this stage as the second step in our simulation, mirroring common practices in data science projects, where it typically falls under the feature selection phase. In the early stages of predictive modeling, especially for numerical datasets like this, it's imperative to identify and include only those features/variables that exert a substantial influence on the predictive model. Two widely used methods for this purpose are the correlation matrix and Principal Component Analysis (PCA). In the context of this discussion, I've provided a correlation matrix containing all 117 features. However, due to its limited readability, I've incorporated a tabular representation below, complete with the inclusion of p-values to validate the feature selection process.

##### Correlation matrix heatmap
![mtx corr](</assets/img/moredata/corr matrix.PNG>)

##### Snapshot tabular correlation per metrics 
![tabular corr](</assets/img/moredata/feature reduction.PNG>)


Out of the 117 features available, I deliberately narrowed down the selection to just 39. Subsequently, I executed the entire process of constructing the prediction model anew, and the results were strikingly improved, boasting a higher level of accuracy as indicated by the R-squared metric. This outcome contradicts a prevailing notion that equates more data to better accuracy, better prediction models, and enhanced AI. Ultimately, through this refinement, I managed to reduce the collective training time for the three algorithms from an initial 9 hours to a more efficient 3.5 hours, while enhancing the predictive model. Nevertheless, the ultimate target of achieving 92% accuracy has yet to be reached with the current model.   

##### Model test result
![all test](</assets/img/moredata/all test result.PNG>)

##### Model validation result
![all val](</assets/img/moredata/all vald result.PNG>)
  
  
#### *Adding new feature*
Upon mapping the variables and experimenting with various optional features, I stumbled upon a particularly impactful feature that significantly elevated the model's accuracy. This feature emanated from the weight bridge, a previously dormant data source primarily used for weekly reporting. The unprocessed data from the weight bridge proved to be an invaluable resource for our prediction model. It became evident that the timestamp in conjunction with the truck positions on the bridge served as potent leading indicators of productivity. Consequently, I undertook the task of preparing, cleaning, and integrating these features into the training model, also creating additional lagging features from the bridge data. This strategic enhancement resulted in a substantial 6.4% increase in the R-squared metric. (For a detailed script snapshot, refer to the final run of the productivity prediction)

##### Model test result
![test92](</assets/img/moredata/39 test result.PNG>)

##### Model validation result
![val92](</assets/img/moredata/30 val result.PNG>)

#### *Conslusion*
This simulation using a real study case underscores a crucial insight: the mere presence of more data doesn't equate to enhanced learning, nor does it guarantee superior AI performance, a misconception that can prevail in some management circles. The term "more" pertains to an abundance in terms of quantity and the number of features. The practice of indiscriminately incorporating all available data into a model can yield adverse effects on its predictive capabilities, and while some improvements might occur, the pivotal question centers on the magnitude of this enhancement.

For organizations blessed with the latitude to store extensive data in a data lake, the option may appear convenient. However, it's vital to recognize that data storage incurs costs, notably in the form of standard storage fees, as observed in GCP (Google Cloud Platform). These expenses encompass not only storage but also retrieval costs in platforms like BigQuery or through various methods, whether local systems or on-premise data lake or data warehouses.

Drawing from my experience as a Six Sigma practitioner and as a citizen data scientist, I hold the conviction that meticulous data structuring and strategic data governance planning are of paramount importance. From an ICT perspective, they yield operational efficiency and streamline data lifecycle management. Simultaneously, from a Six Sigma standpoint, the availability of precise and accurate data accelerates improvement projects significantly, reducing Six Sigma project durations by weeks. Access to data marked by accuracy and precision becomes a catalyst for efficiency and effectiveness across the board.
