---
title: Productivity Prediction - Baseline
date: 2023-07-01 15:00:00 
categories: [machine learning, decision support]
tags: [random tree, xgboost, productivity, supply chain]
---


# **Productivity Prediction**

#### **The result (1st phase - initial model): 77% hourly production  predicted.**

**Why is it important ??** 
This data science project holds the potential to significantly enhance business value, provided that accuracy standards, as per my calculations (with a minimum threshold of 90%), are met. By accurately predicting how productivity may fluctuate in correlation with the population of trucks in a closed system, we can efficiently allocate resources and manage costs. In many cases, operations management primarily relies on trial-and-error approaches. Unfortunately, such methods often result in substantial costs, especially when unsuccessful attempts are irreversible. In the context of a closed queuing network with adaptable resource allocation, the impact is substantial. The addition of a single truck (resource) can notably diminish idle time, subsequently increasing the utilization of key assets by 4% to 8%, contingent on the available capacity.
Knowing when to add or reduce resources in this process network is crucial to increase efficiency, increase utilization while increasing total production. Image below showing the concept of closed queuing network showing dependencies of each assets from each others. 

![Closedloop](</assets/img/pdtypred1st/closed loop.PNG>)

While the following showed the closed queue network in my case study where the productivity at the *End Processing Unit* will be forecasted. 

![Haulnetwork](</assets/img/pdtypred1st/simple hauling network.PNG>)

This machine learning forecasting technique relies on an extensive dataset comprising of a whopping 371 million raw data entries from 6-months time frame. This data is derived from an ETL process executed within a VPN server, sourced from GPS and various other sensors. The raw GPS data undergoes intricate SQL transformations, detailed in a forthcoming article, to convert it into actionable insights. 

Within this experimental setup, we are dealing with a total of 104 potential variables, out of which 4 fall under the category of categorical variables. Our journey commences with the essential library imports, including staples like pandas, numpy, datetime, and os, to facilitate our analysis.

~~~ 
import numpy as np
import pandas as pd
import datetime as dt
from os import listdir

from scipy import stats
import matplotlib.pyplot as plt
import math

import xgboost as xgb
from xgboost import plot_importance

from catboost import CatBoostRegressor, Pool, cv
from catboost import MetricVisualizer

from sklearn.model_selection import TimeSeriesSplit
from sklearn.model_selection import GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import r2_score

from scipy.stats import boxcox
~~~

To summarize, our workflow involves the utilization of essential data manipulation libraries such as pandas and numpy. We'll employ matplotlib for data visualization, while sklearn will play a pivotal role in fine-tuning our models and incorporating statistical techniques. The data for our analysis will be directly sourced from BigQuery, facilitating access to substantial datasets. In this article, we'll explore three distinct models, for which we will employ three dedicated libraries: xgboost, catboost, and random forest. Each of these model libraries offers unique capabilities and advantages, enabling a comprehensive evaluation of their respective performances in the context of our analysis. 

~~~
from google.cloud import bigquery
sql = """
with tmp2 as (
with tmp1 as ( 
select tp.*
  ,lag(tp.countport) over (order by tp.year, tp.month, tp.day, tp.hour, tp.subtime) as lag_countport
	...
	...  **(this is a hidden script containing 
    ...     columns and data aggregation)**
	...
,avg(lag1_s5_muatan_pseg) as lag1_s5_muatan
from tmp2 
group by 1,2,3,4

client = bigquery.Client()
raw_data = client.query(sql).to_dataframe()
~~~

Next, lets have a peek about the data.
~~~
raw_data.describe()
~~~

![Raw Data Description](</assets/img/pdtypred1st/rawdatadescribe.PNG>)

For this analysis, I will utilize the trip data as the dependent variables to be predicted. 

~~~
## This is example for plot distribution

tmp=pd.to_numeric(raw_data.trips)
fig, ax = plt.subplots(1,2,figsize=(15,5))
sns.distplot(pd.to_numeric(tmp), ax=ax[0], kde=False, color="blue")
sns.distplot(np.log(np.isfinite(pd.to_numeric(tmp))), ax=ax[1], bins=20, kde=False, color="limegreen")
~~~

##### PIC

There is strongly visible outlier in trips data. However, is it really outliers ? Apparently not, this were normal operation. To know this we must understand the business, not only the numbers. Some data on holiday date are not appropriate as independent variables. This will be discussed in antoher article for data cleaning. 

There are other 103 variables which will not displayed in this article for the sake of readability. As for the cleaning phase from anomalies, I used a simple inter-quartile method. I will put the explanation for this in other article about data cleaning because it's gonna be too long to put in this article. The multiplier for IRQ used are 2 from a separate study regarding this data.  

~~~
# These are only example for duration data in segmen 6, Put these iterations into function

q = np.percentile(data1['durs6_muatan'], [25,75])
iqr = q[1]-q[0]
up_limit = q[1] + 2*iqr
if q[0] >2*iqr:
    low_limit = q[0] - 2*iqr
else:
    low_limit=0.001

# repeat these cleaning process until no more outliers found
~~~ 

Following the iterations in data preparation and cleansing, our next course of action involves partitioning the data into training and testing subsets, which will be accomplished through the 'train_test_split' function within the sklearn library. Additionally, I've created several dataframes to serve as repositories for gathering model performance metrics. These metrics encompass key indicators such as Root Mean Square Error (RMSE), Mean Squared Error (MSE), Mean Absolute Error (MAE), and R-squared (R2). These performance statistics will be instrumental in the comparative evaluation of various models, facilitating the selection of the most suitable model for predicting productivity outcomes. 

~~~
trp = data1.trips
X, X_val, y, y_val = train_test_split(dep,trp,random_state = 104,test_size=0.3,shuffle=True)
error_table_val = pd.DataFrame(columns=['Method','MSE_val','RMSE_val','MAE_val','R2_val'])
error_table=pd.DataFrame(columns=['Method','MSE','RMSE','MAE','R2'])
~~~ 

In this analysis, I initiated the modeling process with the CatBoost regression model, which, while well-known for its prowess in handling categorical data, extends its utility to regression tasks. Distinguishing itself from conventional gradient boosting models, CatBoost employs an innovative technique by constructing oblivious trees. These trees adhere to a unique rule where nodes at the same level uniformly evaluate the same predictor using identical conditions. Below, you'll find a summary of the specific parameters I employed for this model. This approach leverages the inherent strengths of CatBoost to enhance its performance in regression tasks, offering a fresh perspective in predictive modeling.

~~~
params = {'depth'         : [4,5,6,7,8,9,10],
          'learning_rate' : [0.01,0.02,0.03,0.04],
          'iterations'    : [10,30,50,70,90,100,150],
            'random_seed' : [20],
            'logging_level' : ['Silent'],
            'l2_leaf_reg' : [3,5,7,10]
         }

CBC= CatBoostRegressor()

grid_CBC = GridSearchCV(estimator=CBC, param_grid = params, cv = 2, n_jobs=-1)
grid_CBC.fit(X, y)
~~~

To enhance the verification and validation of the ultimate model, I employed a different new portion of data for validation purposes. This dataset, in combination with a 5-fold cross-validation approach, collectively served as the means to assess and conclude the overall performance of my final model.

~~~
y_predict_val = cbc_fit.predict(X_val)
~~~

Concluding our first model utilizing the CatBoost regression algorithm described earlier, it's time to examine the variables that made a notable impact on the model's performance. In this article, I will employ the 'feature_importances_' function provided by the fitted CatBoost model mentioned previously to investigate and assess the significance of individual variables in the model's overall performance.

~~~
importances = list(cbc_fit.feature_importances_)
feature_importancescbc = [(feature, round(importance, 2)) for feature, importance in zip(X.columns, importances)]
feature_importancescbc = sorted(feature_importancescbc, key = lambda x: x[1], reverse = True)[:20]

val = pd.DataFrame(feature_importancescbc)

last_val = val.sort_values(by=[1], ascending=True)
last_val.plot.barh(x=0, y=1, rot=0, title=" CatBoost Feature Importances", figsize =(7,10))
plt.show()
~~~
![CatBoost Feature Importance](</assets/img/pdtypred1st/cbc feat importance.PNG>)

CatBoost's performance result using test and another set of validation data set resulted in the following figures
![CatBoost test](</assets/img/pdtypred1st/cbc val test 1st.PNG>)


Regarding the performance evaluation of CatBoost, the calculated metrics presented below reveal the following results. During the 5-fold cross-validation, we observed an R-squared value of 88.1%, accompanied by an MAE of 16.64 and an RMSE of 24.4 trips. In an additional validation exercise using newly acquired data, the R-squared value was 84.2%, with an MAE of 19.32 and an RMSE of 29.1 trips. These outcomes were expected due to the inherent variability in the process.

For the second model, I employed XGBoost with the parameters detailed below. Just like CatBoost parameters above, these parameters encompass a range of possibilities to maximize the potential of capturing an optimal parameters, maximizing hyperparameter tuning with GridsearchCV.

~~~
params = { 'objective':['reg:squarederror','reg:absoluteerror'],
            'max_depth': [3,5],
           'learning_rate': [0.3,0.5],
           'n_estimators': [100,250,350],
           'colsample_bytree': [0.1,0.3,0.5,0.8] 
         } 

xgbr = xgb.XGBRegressor(seed = 20)
xgbmodel = GridSearchCV(estimator=xgbr,param_grid=params, scoring='neg_mean_squared_error', verbose=1)
regresor = xgbmodel.fit(X, y)

importances = list(xgbfit.feature_importances_)
feature_importancesxgb = [(feature, round(importance, 2)) for feature, importance in zip(X.columns, importances)]
feature_importancesxgb = sorted(feature_importancesxgb, key = lambda x: x[1], reverse = True)[:20]

val = pd.DataFrame(feature_importancesxgb)
last_val = val.sort_values(by=[1], ascending=True)
last_val.plot.barh(x=0, y=1, rot=0, title=" XGBoost Feature Importances", figsize =(7,10))
plt.show()
~~~
![XGB Feature Importance](</assets/img/pdtypred1st/xgb feat importance.PNG>)

Fitting XGB model into the test data shows these result, in this result I also compared the validation result using another data set specifically selected randomly for additional validation.   
![XGB test result](</assets/img/pdtypred1st/xgb val test 1st.PNG>)

Taking a closer look at our second model using XGBoost algorithm. The performance metrics for XGBoost during the 5-fold cross-validation exhibit superior results when compared to CatBoost. However, when assessed through additional validation, XGBoost's performance shows a slightly less favorable outcome. We will retain these results in their current state and proceed with our exploration of the Random Forest algorithm, implementing the specified parameters for further evaluation. This comparative analysis highlights the nuances in performance between these algorithms, setting the stage for our continued investigation into Random Forest's capabilities using following parameters. 

~~~
params = { 
    'n_estimators': [200, 500, 700, 1000],
    'random_state': [42],
    'max_depth' : [4,6,8],
    'criterion': ['mse'],
    'min_samples_leaf': [1],
    'min_samples_split': [2],
}
rfr = RandomForestRegressor(random_state = 42) # Train the model on training data
rf = GridSearchCV(estimator=rfr, param_grid=params, cv= 5)
rf.fit(X, y)
~~~

Using these parameters for random forest algorithm, I got test result 

![RF result](</assets/img/pdtypred1st/val-test RF 1st.PNG>)


The Random Forest algorithm exhibited disappointing results when contrasted with the other two algorithms. Its performance significantly lagged behind. Based on this initial iteration for each algorithm, we can provisionally deduce that XGBoost and CatBoost outperform the random forest algorithm in this case study and are viable options as prediction models for productivity.




