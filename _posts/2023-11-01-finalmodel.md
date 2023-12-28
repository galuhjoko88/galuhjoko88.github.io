---
title: Productivity prediction - final model - R-sq=95%+
date: 2023-10-31 01:00:00 +0700
categories: [machine learning]
tags: [xgboost, prediction, mining, hauling]
---


## Final prediction model performance = 95%+

Building upon the initial framework, extensive exploration of variables was undertaken to enhance the predictive model. These variables were derived from discussions with Subject Matter Experts (SME) and organized through an [affinity diagram](https://www.sixsigmadaily.com/the-affinity-diagram-tool/), a tool commonly employed for such purposes (diagram not disclosed due to confidentiality). Alternative tools, such as mind mapping, were also utilized for variable mapping. The affinity diagram revealed several lagging variables, including the truck number in the last segment (S6) during the final 15 minutes, truck speed in the last 15 minutes, and weighing activity in the last hour, 30 minutes, and 15 minutes. Additionally, certain road segments were tested, resulting in a whopping increase of 43 variables. However, only two variables—total port throughput and the last 15-minute weighing count—demonstrated substantial improvements in prediction performance.

Inclusion of variables beyond these two yielded only marginal enhancements, and considering the resource cost, particularly in terms of training time, it was deemed inefficient to train the model further. Utilizing these two carefully curated and cleaned variables in the baseline sequence algorithm significantly elevated prediction performance to over 95% in both testing and validation phases.

Reiterating from my other posts about [more data does not mean better model](https://galuhjoko88.github.io/posts/moredatanotbetter/) that blindly augmenting data in the algorithm can lead to counterproductive outcomes, with increased time and calculation costs, and some data contributing minimally to performance improvement. The project's most challenging aspect involved identifying, preparing, and cleaning potential variables. Remarkably, the final prediction algorithm streamlined the original 103 variables to a mere 39. Despite the initial addition of 43 variables, a proper feature/variable reduction process rendered the model more efficient, achieving optimal R-square performance with only 39 variables.

Depicting as diagram, the position of the new features that generated valuable insight for the prediction model lies between the checkpoint of production units (trucks) and the end processing unit (Port) in my case. Incorporating these variables as features greatly improve the prediction capability and increasing feasibility of this prediction model as tools that can generate value added for the business. 

![final diagram](</assets/img/finalmodel/network model final.PNG>)

The upcoming section will present the code utilized in the prediction model. Specifically, I will focus on showcasing the code for XGBoost, as it emerged as the most effective algorithm during the model training phase, delivering optimal results among the various algorithms tested.

~~~
import numpy as np
import datetime as dt
import seaborn as sns
import pandas as pd
from scipy import stats
import matplotlib.pyplot as plt
import math
from xgboost import plot_importance

from catboost import CatBoostRegressor, Pool, cv
from catboost import MetricVisualizer
from sklearn.model_selection import train_test_split
from sklearn.model_selection import TimeSeriesSplit
from sklearn.preprocessing import StandardScaler
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import r2_score, mean_squared_error, make_scorer
from sklearn.model_selection import GridSearchCV

from scipy.stats import boxcox
from os import listdir

import warnings
warnings.filterwarnings("ignore", category=DeprecationWarning)
warnings.filterwarnings("ignore", category=UserWarning)
warnings.filterwarnings("ignore", category=RuntimeWarning)
warnings.filterwarnings("ignore", category=FutureWarning)

import xgboost as xgb
from sklearn.model_selection import GridSearchCV

~~~

The same Exploratory Data Analysis (EDA) methodology was employed in conjunction with the baseline model as follow 

~~~
tmp=pd.to_numeric(raw_data.countport)
fig, ax = plt.subplots(1,2,figsize=(15,5))
sns.distplot(pd.to_numeric(tmp), ax=ax[0], kde=False, color="blue")

# this will show a highlight of the data used
raw_data.describe()
~~~

![trips dist](</assets/img/finalmodel/tripsdistribution.PNG>)

I utilized these feature variables to govern the features employed in the training, testing, and validation phases.

~~~
features = ['hour',
    'portbib_kosongan_pseg',
    'portbir_kosongan_pseg',
	^^^ cencored and too long to pasted here ^^^
	'lag1_s5_muatan',
    'countport']
	
features_spearman1 = ['hour',
    'portbib_kosongan_pseg',
    'portbir_kosongan_pseg',
	^^^ cencored and too long to pasted here ^^^
	'port_thru',
	'lag15_port_thru',
    'countport']
	
# there are several sets of these feature set, I will not show them all.
~~~

Let's take a glance at the initial set of features employed, referred to as features_spearman1. Other features set are skipped in this article. 

~~~
data1=data1[features_spearman1]
data1.head()
~~~

![data head](</assets/img/finalmodel/rawdatadescribe.PNG>)

These lines are instrumental in partitioning the dataset into training and test samples. Specifically, a 30% random sample is designated for testing purposes. For validation, a 5-fold cross-validation approach is employed, and further validation is conducted using a completely new data sample.

~~~
trp = data1.countport
dep = data1.drop(['countport'],axis=1)

X, X_val, y, y_val = train_test_split(dep,trp,random_state = 104,test_size=0.3,shuffle=True)
~~~


As mentioned earlier, I will exclusively present the XGBoost parameters and results, as this algorithm demonstrated the most favorable performance in my scenario. For the sake of simplicity, parameters and results for other algorithms will not be included in this article. The following are the parameters employed for XGBoost:

~~~
# parameters used in XGB that will also used in cross validation 
params = { 'colsample_bytree': [0.8],
             'learning_rate': [0.3],
             'max_depth': [3],
             'n_estimators': [250],
             'objective': ['reg:squarederror']
         }

# parameters used in fitting the model. 

fit_param = {
            'early_stopping_rounds': 50
            ,'eval_metric': 'rmse'
            ,'verbose': False
            ,'eval_set': [(X_val, y_val)]
            }

xgbr = xgb.XGBRegressor(seed = 20)
xgbmodel = GridSearchCV(estimator=xgbr,param_grid=params \
                        , scoring='neg_mean_squared_error', verbose=1 , cv= 5)

regresor = xgbmodel.fit(X, y, **fit_param)
~~~


This section is dedicated to obtaining the optimal parameters derived from the training stage, aiming to secure the best configuration for the model.

~~~
regresor.best_params_
~~~

To gain insights into the learning curve between the training and test datasets, the following script can be employed:

~~~
xx = len(result['validation_0']['rmse'])
x_axis = range(0, xx)

fig, ax = plt.subplots()

ax.plot(x_axis, result['validation_0']['rmse'], label='Train')
ax.plot(x_axis, result['validation_1']['rmse'], label='Test')
ax.legend()
plt.ylabel('RMSE')
plt.title('XGBoost RMSE')
plt.show()
~~~

![learning curve](</assets/img/finalmodel/learning curve.PNG>)


The script below extracts the top 20 features that were deemed to have the most significant contribution to the prediction results of the XGBoost algorithm:

~~~
importances = list(xgbfit.feature_importances_)
feature_importancesxgb = [(feature, round(importance, 4)) for feature, importance in zip(X.columns, importances)]
feature_importancesxgb = sorted(feature_importancesxgb, key = lambda x: x[1], reverse = True)[:20]

val = pd.DataFrame(feature_importancesxgb)

last_val = val.sort_values(by=[1], ascending=True)
last_val.plot.barh(x=0, y=1, rot=0, title=" XGBoost Feature Importances", figsize =(7,10))
plt.show()
~~~

![xgb importance](</assets/img/finalmodel/xgb importance.PNG>)

In practical world, some say that this importances metrics can be used as a means to reduce the insignificant features in the algorithm. In my personal opinion, that might be trus for some cases or in the initial/baseline model. Model iteration will affect the importance feature relatively to other features so prudent observation is advised.   

Storing and showing the performance metrics for this algorithm resulted in the following table:

![xgb test result](</assets/img/finalmodel/final test result.PNG>)

with corresponding additional validation result as follow

![xgb val result](</assets/img/finalmodel/final val result.PNG>)

Executing the XGBoost algorithm with meticulous cleaning and judicious feature selection yielded a final R-square result of 95.3%, surpassing the performance of other algorithms. In conclusion, for my specific case, this model stands as the chosen prediction model, playing a pivotal role in supporting truck management throughout the hauling process



