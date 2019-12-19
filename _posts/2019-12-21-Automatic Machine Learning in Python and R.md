
---
title: " Automatic Machine Learning "
subtitle: automl in Python and R
gh-repo: NanaAkwasiAbayieBoateng/NanaAkwasiAbayieBoateng.github.io
gh-badge: [star, fork, follow]
tags:  [automl, h2o, autokeras, google cloud platform, automatic deep learning]
---


#### Automated Machine Learning 

Automated Machine Learning (AutoML) has increased greatly the efficiency of building machine learning models. AutoML achieves this by automating in some applications data pre-processing ,feature engineering, feature extraction , feature selection and hyper-parameter tuning when building machine learning models. AutoML has also reduced the expertise in  academic domains  such as computer science, statistics and other academic knowledge hitherto needed  to build machine learning models. Repetitive task which do not need any expertise in machine learning could be easily automated by AutoML. AutoML can have some drawback which include the amount of time used in training. Since AutoML trains many models and selects the best performing model as the final model, large computaional resources is needed to reduce the time involved in training.




#### Data

Appliances energy prediction dataset from UCI machine learning repository would be used for this exercise. Further description of data and the data itself is 
 available [here](https://archive.ics.uci.edu/ml/datasets/Appliances+energy+prediction#). The goal is to predict home Appliance  energy use in Wh given several features including temperature in various rooms of the house and humidity etc.

#### Attribute Information:

- date time year-month-day hour:minute:second
- Appliances, energy use in Wh
lights, energy use of light fixtures in the house in Wh
- T1, Temperature in kitchen area, in Celsius
- RH_1, Humidity in kitchen area, in %
- T2, Temperature in living room area, in Celsius
- RH_2, Humidity in living room area, in %
- T3, Temperature in laundry room area
- RH_3, Humidity in laundry room area, in %
- T4, Temperature in office room, in Celsius
- RH_4, Humidity in office room, in %
- T5, Temperature in bathroom, in Celsius
- RH_5, Humidity in bathroom, in %
- T6, Temperature outside the building (north side), in Celsius
- RH_6, Humidity outside the building (north side), in %
- T7, Temperature in ironing room , in Celsius
- RH_7, Humidity in ironing room, in %
- T8, Temperature in teenager room 2, in Celsius
- RH_8, Humidity in teenager room 2, in %
- T9, Temperature in parents room, in Celsius
- RH_9, Humidity in parents room, in %
- To, Temperature outside (from Chievres weather station), in Celsius
- Pressure (from Chievres weather station), in mm Hg
- RH_out, Humidity outside (from Chievres weather station), in %
- Wind speed (from Chievres weather station), in m/s
- Visibility (from Chievres weather station), in km
- Tdewpoint (from Chievres weather station), Â°C
- rv1, Random variable 1, nondimensional
- rv2, Random variable 2, nondimensional


```python
import numpy as np
import pandas as pd
from google.colab import files
import io
from sklearn.model_selection import train_test_split
import rpy2
%load_ext rpy2.ipython
```

    The rpy2.ipython extension is already loaded. To reload it, use:
      %reload_ext rpy2.ipython
    


```python
uploaded = files.upload()
```



     <input type="file" id="files-5333c190-8c44-4f40-82a7-fbb1943d7fea" name="files[]" multiple disabled />
     <output id="result-5333c190-8c44-4f40-82a7-fbb1943d7fea">
      Upload widget is only available when the cell has been executed in the
      current browser session. Please rerun this cell to enable.
      </output>
      <script src="/nbextensions/google.colab/files.js"></script> 


    Saving energydata_complete.csv to energydata_complete.csv
    


```python
for fn in uploaded.keys():
  print('User uploaded file "{name}" with length {length} bytes'.format(
      name=fn, length=len(uploaded[fn])))
```

    User uploaded file "energydata_complete.csv" with length 11979363 bytes
    


```python
dateparse = lambda x: pd.datetime.strptime(x, '%Y-%m-%d %H:%M:%S')

energy_data=pd.read_csv(io.StringIO(uploaded['energydata_complete.csv'].decode('utf-8')),
                        parse_dates=['date'], 
                        date_parser=dateparse)
energy_data.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>date</th>
      <th>Appliances</th>
      <th>lights</th>
      <th>T1</th>
      <th>RH_1</th>
      <th>T2</th>
      <th>RH_2</th>
      <th>T3</th>
      <th>RH_3</th>
      <th>T4</th>
      <th>RH_4</th>
      <th>T5</th>
      <th>RH_5</th>
      <th>T6</th>
      <th>RH_6</th>
      <th>T7</th>
      <th>RH_7</th>
      <th>T8</th>
      <th>RH_8</th>
      <th>T9</th>
      <th>RH_9</th>
      <th>T_out</th>
      <th>Press_mm_hg</th>
      <th>RH_out</th>
      <th>Windspeed</th>
      <th>Visibility</th>
      <th>Tdewpoint</th>
      <th>rv1</th>
      <th>rv2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2016-01-11 17:00:00</td>
      <td>60</td>
      <td>30</td>
      <td>19.89</td>
      <td>47.596667</td>
      <td>19.2</td>
      <td>44.790000</td>
      <td>19.79</td>
      <td>44.730000</td>
      <td>19.000000</td>
      <td>45.566667</td>
      <td>17.166667</td>
      <td>55.20</td>
      <td>7.026667</td>
      <td>84.256667</td>
      <td>17.200000</td>
      <td>41.626667</td>
      <td>18.2</td>
      <td>48.900000</td>
      <td>17.033333</td>
      <td>45.53</td>
      <td>6.600000</td>
      <td>733.5</td>
      <td>92.0</td>
      <td>7.000000</td>
      <td>63.000000</td>
      <td>5.3</td>
      <td>13.275433</td>
      <td>13.275433</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2016-01-11 17:10:00</td>
      <td>60</td>
      <td>30</td>
      <td>19.89</td>
      <td>46.693333</td>
      <td>19.2</td>
      <td>44.722500</td>
      <td>19.79</td>
      <td>44.790000</td>
      <td>19.000000</td>
      <td>45.992500</td>
      <td>17.166667</td>
      <td>55.20</td>
      <td>6.833333</td>
      <td>84.063333</td>
      <td>17.200000</td>
      <td>41.560000</td>
      <td>18.2</td>
      <td>48.863333</td>
      <td>17.066667</td>
      <td>45.56</td>
      <td>6.483333</td>
      <td>733.6</td>
      <td>92.0</td>
      <td>6.666667</td>
      <td>59.166667</td>
      <td>5.2</td>
      <td>18.606195</td>
      <td>18.606195</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2016-01-11 17:20:00</td>
      <td>50</td>
      <td>30</td>
      <td>19.89</td>
      <td>46.300000</td>
      <td>19.2</td>
      <td>44.626667</td>
      <td>19.79</td>
      <td>44.933333</td>
      <td>18.926667</td>
      <td>45.890000</td>
      <td>17.166667</td>
      <td>55.09</td>
      <td>6.560000</td>
      <td>83.156667</td>
      <td>17.200000</td>
      <td>41.433333</td>
      <td>18.2</td>
      <td>48.730000</td>
      <td>17.000000</td>
      <td>45.50</td>
      <td>6.366667</td>
      <td>733.7</td>
      <td>92.0</td>
      <td>6.333333</td>
      <td>55.333333</td>
      <td>5.1</td>
      <td>28.642668</td>
      <td>28.642668</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2016-01-11 17:30:00</td>
      <td>50</td>
      <td>40</td>
      <td>19.89</td>
      <td>46.066667</td>
      <td>19.2</td>
      <td>44.590000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.723333</td>
      <td>17.166667</td>
      <td>55.09</td>
      <td>6.433333</td>
      <td>83.423333</td>
      <td>17.133333</td>
      <td>41.290000</td>
      <td>18.1</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>45.40</td>
      <td>6.250000</td>
      <td>733.8</td>
      <td>92.0</td>
      <td>6.000000</td>
      <td>51.500000</td>
      <td>5.0</td>
      <td>45.410389</td>
      <td>45.410389</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2016-01-11 17:40:00</td>
      <td>60</td>
      <td>40</td>
      <td>19.89</td>
      <td>46.333333</td>
      <td>19.2</td>
      <td>44.530000</td>
      <td>19.79</td>
      <td>45.000000</td>
      <td>18.890000</td>
      <td>45.530000</td>
      <td>17.200000</td>
      <td>55.09</td>
      <td>6.366667</td>
      <td>84.893333</td>
      <td>17.200000</td>
      <td>41.230000</td>
      <td>18.1</td>
      <td>48.590000</td>
      <td>17.000000</td>
      <td>45.40</td>
      <td>6.133333</td>
      <td>733.9</td>
      <td>92.0</td>
      <td>5.666667</td>
      <td>47.666667</td>
      <td>4.9</td>
      <td>10.084097</td>
      <td>10.084097</td>
    </tr>
  </tbody>
</table>
</div>




```python
energy_data.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 19735 entries, 0 to 19734
    Data columns (total 29 columns):
    date           19735 non-null datetime64[ns]
    Appliances     19735 non-null int64
    lights         19735 non-null int64
    T1             19735 non-null float64
    RH_1           19735 non-null float64
    T2             19735 non-null float64
    RH_2           19735 non-null float64
    T3             19735 non-null float64
    RH_3           19735 non-null float64
    T4             19735 non-null float64
    RH_4           19735 non-null float64
    T5             19735 non-null float64
    RH_5           19735 non-null float64
    T6             19735 non-null float64
    RH_6           19735 non-null float64
    T7             19735 non-null float64
    RH_7           19735 non-null float64
    T8             19735 non-null float64
    RH_8           19735 non-null float64
    T9             19735 non-null float64
    RH_9           19735 non-null float64
    T_out          19735 non-null float64
    Press_mm_hg    19735 non-null float64
    RH_out         19735 non-null float64
    Windspeed      19735 non-null float64
    Visibility     19735 non-null float64
    Tdewpoint      19735 non-null float64
    rv1            19735 non-null float64
    rv2            19735 non-null float64
    dtypes: datetime64[ns](1), float64(26), int64(2)
    memory usage: 4.4 MB
    


```python
energy_data.describe().transpose()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Appliances</th>
      <td>19735.0</td>
      <td>97.694958</td>
      <td>102.524891</td>
      <td>10.000000</td>
      <td>50.000000</td>
      <td>60.000000</td>
      <td>100.000000</td>
      <td>1080.000000</td>
    </tr>
    <tr>
      <th>lights</th>
      <td>19735.0</td>
      <td>3.801875</td>
      <td>7.935988</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>70.000000</td>
    </tr>
    <tr>
      <th>T1</th>
      <td>19735.0</td>
      <td>21.686571</td>
      <td>1.606066</td>
      <td>16.790000</td>
      <td>20.760000</td>
      <td>21.600000</td>
      <td>22.600000</td>
      <td>26.260000</td>
    </tr>
    <tr>
      <th>RH_1</th>
      <td>19735.0</td>
      <td>40.259739</td>
      <td>3.979299</td>
      <td>27.023333</td>
      <td>37.333333</td>
      <td>39.656667</td>
      <td>43.066667</td>
      <td>63.360000</td>
    </tr>
    <tr>
      <th>T2</th>
      <td>19735.0</td>
      <td>20.341219</td>
      <td>2.192974</td>
      <td>16.100000</td>
      <td>18.790000</td>
      <td>20.000000</td>
      <td>21.500000</td>
      <td>29.856667</td>
    </tr>
    <tr>
      <th>RH_2</th>
      <td>19735.0</td>
      <td>40.420420</td>
      <td>4.069813</td>
      <td>20.463333</td>
      <td>37.900000</td>
      <td>40.500000</td>
      <td>43.260000</td>
      <td>56.026667</td>
    </tr>
    <tr>
      <th>T3</th>
      <td>19735.0</td>
      <td>22.267611</td>
      <td>2.006111</td>
      <td>17.200000</td>
      <td>20.790000</td>
      <td>22.100000</td>
      <td>23.290000</td>
      <td>29.236000</td>
    </tr>
    <tr>
      <th>RH_3</th>
      <td>19735.0</td>
      <td>39.242500</td>
      <td>3.254576</td>
      <td>28.766667</td>
      <td>36.900000</td>
      <td>38.530000</td>
      <td>41.760000</td>
      <td>50.163333</td>
    </tr>
    <tr>
      <th>T4</th>
      <td>19735.0</td>
      <td>20.855335</td>
      <td>2.042884</td>
      <td>15.100000</td>
      <td>19.530000</td>
      <td>20.666667</td>
      <td>22.100000</td>
      <td>26.200000</td>
    </tr>
    <tr>
      <th>RH_4</th>
      <td>19735.0</td>
      <td>39.026904</td>
      <td>4.341321</td>
      <td>27.660000</td>
      <td>35.530000</td>
      <td>38.400000</td>
      <td>42.156667</td>
      <td>51.090000</td>
    </tr>
    <tr>
      <th>T5</th>
      <td>19735.0</td>
      <td>19.592106</td>
      <td>1.844623</td>
      <td>15.330000</td>
      <td>18.277500</td>
      <td>19.390000</td>
      <td>20.619643</td>
      <td>25.795000</td>
    </tr>
    <tr>
      <th>RH_5</th>
      <td>19735.0</td>
      <td>50.949283</td>
      <td>9.022034</td>
      <td>29.815000</td>
      <td>45.400000</td>
      <td>49.090000</td>
      <td>53.663333</td>
      <td>96.321667</td>
    </tr>
    <tr>
      <th>T6</th>
      <td>19735.0</td>
      <td>7.910939</td>
      <td>6.090347</td>
      <td>-6.065000</td>
      <td>3.626667</td>
      <td>7.300000</td>
      <td>11.256000</td>
      <td>28.290000</td>
    </tr>
    <tr>
      <th>RH_6</th>
      <td>19735.0</td>
      <td>54.609083</td>
      <td>31.149806</td>
      <td>1.000000</td>
      <td>30.025000</td>
      <td>55.290000</td>
      <td>83.226667</td>
      <td>99.900000</td>
    </tr>
    <tr>
      <th>T7</th>
      <td>19735.0</td>
      <td>20.267106</td>
      <td>2.109993</td>
      <td>15.390000</td>
      <td>18.700000</td>
      <td>20.033333</td>
      <td>21.600000</td>
      <td>26.000000</td>
    </tr>
    <tr>
      <th>RH_7</th>
      <td>19735.0</td>
      <td>35.388200</td>
      <td>5.114208</td>
      <td>23.200000</td>
      <td>31.500000</td>
      <td>34.863333</td>
      <td>39.000000</td>
      <td>51.400000</td>
    </tr>
    <tr>
      <th>T8</th>
      <td>19735.0</td>
      <td>22.029107</td>
      <td>1.956162</td>
      <td>16.306667</td>
      <td>20.790000</td>
      <td>22.100000</td>
      <td>23.390000</td>
      <td>27.230000</td>
    </tr>
    <tr>
      <th>RH_8</th>
      <td>19735.0</td>
      <td>42.936165</td>
      <td>5.224361</td>
      <td>29.600000</td>
      <td>39.066667</td>
      <td>42.375000</td>
      <td>46.536000</td>
      <td>58.780000</td>
    </tr>
    <tr>
      <th>T9</th>
      <td>19735.0</td>
      <td>19.485828</td>
      <td>2.014712</td>
      <td>14.890000</td>
      <td>18.000000</td>
      <td>19.390000</td>
      <td>20.600000</td>
      <td>24.500000</td>
    </tr>
    <tr>
      <th>RH_9</th>
      <td>19735.0</td>
      <td>41.552401</td>
      <td>4.151497</td>
      <td>29.166667</td>
      <td>38.500000</td>
      <td>40.900000</td>
      <td>44.338095</td>
      <td>53.326667</td>
    </tr>
    <tr>
      <th>T_out</th>
      <td>19735.0</td>
      <td>7.411665</td>
      <td>5.317409</td>
      <td>-5.000000</td>
      <td>3.666667</td>
      <td>6.916667</td>
      <td>10.408333</td>
      <td>26.100000</td>
    </tr>
    <tr>
      <th>Press_mm_hg</th>
      <td>19735.0</td>
      <td>755.522602</td>
      <td>7.399441</td>
      <td>729.300000</td>
      <td>750.933333</td>
      <td>756.100000</td>
      <td>760.933333</td>
      <td>772.300000</td>
    </tr>
    <tr>
      <th>RH_out</th>
      <td>19735.0</td>
      <td>79.750418</td>
      <td>14.901088</td>
      <td>24.000000</td>
      <td>70.333333</td>
      <td>83.666667</td>
      <td>91.666667</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>Windspeed</th>
      <td>19735.0</td>
      <td>4.039752</td>
      <td>2.451221</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>3.666667</td>
      <td>5.500000</td>
      <td>14.000000</td>
    </tr>
    <tr>
      <th>Visibility</th>
      <td>19735.0</td>
      <td>38.330834</td>
      <td>11.794719</td>
      <td>1.000000</td>
      <td>29.000000</td>
      <td>40.000000</td>
      <td>40.000000</td>
      <td>66.000000</td>
    </tr>
    <tr>
      <th>Tdewpoint</th>
      <td>19735.0</td>
      <td>3.760707</td>
      <td>4.194648</td>
      <td>-6.600000</td>
      <td>0.900000</td>
      <td>3.433333</td>
      <td>6.566667</td>
      <td>15.500000</td>
    </tr>
    <tr>
      <th>rv1</th>
      <td>19735.0</td>
      <td>24.988033</td>
      <td>14.496634</td>
      <td>0.005322</td>
      <td>12.497889</td>
      <td>24.897653</td>
      <td>37.583769</td>
      <td>49.996530</td>
    </tr>
    <tr>
      <th>rv2</th>
      <td>19735.0</td>
      <td>24.988033</td>
      <td>14.496634</td>
      <td>0.005322</td>
      <td>12.497889</td>
      <td>24.897653</td>
      <td>37.583769</td>
      <td>49.996530</td>
    </tr>
  </tbody>
</table>
</div>



We would perform  some light feature engineering by creating additional time-based features from the date column and drop the date column.


```python
energy_data['hour']  = energy_data['date'].dt.hour
energy_data['day'] = energy_data['date'].dt.day
energy_data['month'] = pd.DatetimeIndex(energy_data['date']).month
energy_data['week'] = energy_data['date'].dt.week
energy_data['weekday']= energy_data['date'].dt.weekday
energy_data['quarter']= energy_data['date'].dt.quarter
energy_data['year'] =   pd.DatetimeIndex(energy_data['date']).year
Energy_Data = energy_data.drop(['date'],axis=1)
```


```python
x= Energy_Data.drop('Appliances',axis=1)
y= Energy_Data.Appliances
train_x, test_x, train_y, test_y = train_test_split(x, y,test_size=0.3, random_state=148)
```


#### Cloud AutoML
Google Cloud AutoML is among the early implementors and also popular AutoML software around. Googles AutoML is however not open source and you need some Google account and GCP  credit to train AutoML on Google cloud.
Google AutoML uses neural architecture search to find a the optimal  neural network architecture.



####  Tree-Based Pipeline Optimization Tool(TPOT)
TPOT  library built on top of scikit-learn, and my favorite thing about it is that when you export a model you're actually exporting all the Python code you need to train that model.


TPOT is a Python Automated Machine Learning tool built on top of scikit-learn ML library that optimizes machine learning pipelines using genetic programming.  TPOT automates the building of ML pipelines by combining a flexible expression tree representation of pipelines with stochastic search algorithms such as genetic programming. TPOT explores thousands of possible pipelines, selects the best one for your data and finaly provides you with the python code. Both regression and classfication models can be built with TPOTRegressor  and TPOTClassifier.







```python
!conda install numpy scipy scikit-learn pandas joblib
!pip install deap update_checker tqdm stopit
!pip install xgboost
!pip install dask[delayed] dask[dataframe] dask-ml fsspec>=0.3.3
!pip install scikit-mdr skrebate
!pip install tpot
!pip install h2o
```

    /bin/bash: conda: command not found
    Collecting deap
    [?25l  Downloading https://files.pythonhosted.org/packages/81/98/3166fb5cfa47bf516e73575a1515734fe3ce05292160db403ae542626b32/deap-1.3.0-cp36-cp36m-manylinux2010_x86_64.whl (151kB)
    [K     |████████████████████████████████| 153kB 9.7MB/s 
    [?25hCollecting update_checker
      Downloading https://files.pythonhosted.org/packages/17/c9/ab11855af164d03be0ff4fddd4c46a5bd44799a9ecc1770e01a669c21168/update_checker-0.16-py2.py3-none-any.whl
    Requirement already satisfied: tqdm in /usr/local/lib/python3.6/dist-packages (4.28.1)
    Collecting stopit
      Downloading https://files.pythonhosted.org/packages/35/58/e8bb0b0fb05baf07bbac1450c447d753da65f9701f551dca79823ce15d50/stopit-1.1.2.tar.gz
    Requirement already satisfied: numpy in /usr/local/lib/python3.6/dist-packages (from deap) (1.17.4)
    Requirement already satisfied: requests>=2.3.0 in /usr/local/lib/python3.6/dist-packages (from update_checker) (2.21.0)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker) (2019.11.28)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker) (3.0.4)
    Requirement already satisfied: idna<2.9,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker) (2.8)
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker) (1.24.3)
    Building wheels for collected packages: stopit
      Building wheel for stopit (setup.py) ... [?25l[?25hdone
      Created wheel for stopit: filename=stopit-1.1.2-cp36-none-any.whl size=11955 sha256=b00e7de38c823d411546398acbbfbd742ab219c67563f28813ab039fb102c3be
      Stored in directory: /root/.cache/pip/wheels/3c/85/2b/2580190404636bfc63e8de3dff629c03bb795021e1983a6cc7
    Successfully built stopit
    Installing collected packages: deap, update-checker, stopit
    Successfully installed deap-1.3.0 stopit-1.1.2 update-checker-0.16
    Requirement already satisfied: xgboost in /usr/local/lib/python3.6/dist-packages (0.90)
    Requirement already satisfied: scipy in /usr/local/lib/python3.6/dist-packages (from xgboost) (1.3.3)
    Requirement already satisfied: numpy in /usr/local/lib/python3.6/dist-packages (from xgboost) (1.17.4)
    Collecting scikit-mdr
      Downloading https://files.pythonhosted.org/packages/5f/f5/e070a1e64f0cde221fcb7d76baaaf9ddeb493ddc744d1215e6d79a89b764/scikit-MDR-0.4.4.tar.gz
    Collecting skrebate
      Downloading https://files.pythonhosted.org/packages/7d/01/764fdd40b0e9f01624725c3cf1ffa00071fabb66b62d39fc2c28b34f3edb/skrebate-0.6.tar.gz
    Requirement already satisfied: numpy in /usr/local/lib/python3.6/dist-packages (from scikit-mdr) (1.17.4)
    Requirement already satisfied: scipy in /usr/local/lib/python3.6/dist-packages (from scikit-mdr) (1.3.3)
    Requirement already satisfied: scikit-learn in /usr/local/lib/python3.6/dist-packages (from scikit-mdr) (0.21.3)
    Requirement already satisfied: matplotlib in /usr/local/lib/python3.6/dist-packages (from scikit-mdr) (3.1.2)
    Requirement already satisfied: joblib>=0.11 in /usr/local/lib/python3.6/dist-packages (from scikit-learn->scikit-mdr) (0.14.1)
    Requirement already satisfied: python-dateutil>=2.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->scikit-mdr) (2.6.1)
    Requirement already satisfied: pyparsing!=2.0.4,!=2.1.2,!=2.1.6,>=2.0.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->scikit-mdr) (2.4.5)
    Requirement already satisfied: cycler>=0.10 in /usr/local/lib/python3.6/dist-packages (from matplotlib->scikit-mdr) (0.10.0)
    Requirement already satisfied: kiwisolver>=1.0.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->scikit-mdr) (1.1.0)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.6/dist-packages (from python-dateutil>=2.1->matplotlib->scikit-mdr) (1.12.0)
    Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from kiwisolver>=1.0.1->matplotlib->scikit-mdr) (42.0.2)
    Building wheels for collected packages: scikit-mdr, skrebate
      Building wheel for scikit-mdr (setup.py) ... [?25l[?25hdone
      Created wheel for scikit-mdr: filename=scikit_MDR-0.4.4-cp36-none-any.whl size=14639 sha256=31556baa5705805509e6137f5cca9052a7c3477a793e4b103999dd0270d93111
      Stored in directory: /root/.cache/pip/wheels/ee/50/91/9d48f7807ff574cdbf8f82fc65b7075b23acc9106d3cced38f
      Building wheel for skrebate (setup.py) ... [?25l[?25hdone
      Created wheel for skrebate: filename=skrebate-0.6-cp36-none-any.whl size=29329 sha256=bcfcffab1900c3ef9d524807205086e19cea6b52ce1b6b7dcedf631bb835ac8e
      Stored in directory: /root/.cache/pip/wheels/f5/99/36/c827bcfa6852c6d068895b2723c57cea84f93642270c6dc05c
    Successfully built scikit-mdr skrebate
    Installing collected packages: scikit-mdr, skrebate
    Successfully installed scikit-mdr-0.4.4 skrebate-0.6
    Collecting tpot
    [?25l  Downloading https://files.pythonhosted.org/packages/a5/29/f38a5751276cd901bca8f04ca9a98569a9d4eacd3236bc19a0bf0c834f74/TPOT-0.11.0.tar.gz (896kB)
    [K     |████████████████████████████████| 901kB 9.7MB/s 
    [?25hRequirement already satisfied: numpy>=1.16.3 in /usr/local/lib/python3.6/dist-packages (from tpot) (1.17.4)
    Requirement already satisfied: scipy>=1.3.1 in /usr/local/lib/python3.6/dist-packages (from tpot) (1.3.3)
    Requirement already satisfied: scikit-learn>=0.21.0 in /usr/local/lib/python3.6/dist-packages (from tpot) (0.21.3)
    Requirement already satisfied: deap>=1.2 in /usr/local/lib/python3.6/dist-packages (from tpot) (1.3.0)
    Requirement already satisfied: update_checker>=0.16 in /usr/local/lib/python3.6/dist-packages (from tpot) (0.16)
    Collecting tqdm>=4.36.1
    [?25l  Downloading https://files.pythonhosted.org/packages/7f/32/5144caf0478b1f26bd9d97f510a47336cf4ac0f96c6bc3b5af20d4173920/tqdm-4.40.2-py2.py3-none-any.whl (55kB)
    [K     |████████████████████████████████| 61kB 10.0MB/s 
    [?25hRequirement already satisfied: stopit>=1.1.1 in /usr/local/lib/python3.6/dist-packages (from tpot) (1.1.2)
    Requirement already satisfied: pandas>=0.24.2 in /usr/local/lib/python3.6/dist-packages (from tpot) (0.25.3)
    Requirement already satisfied: joblib>=0.13.2 in /usr/local/lib/python3.6/dist-packages (from tpot) (0.14.1)
    Requirement already satisfied: requests>=2.3.0 in /usr/local/lib/python3.6/dist-packages (from update_checker>=0.16->tpot) (2.21.0)
    Requirement already satisfied: python-dateutil>=2.6.1 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.24.2->tpot) (2.6.1)
    Requirement already satisfied: pytz>=2017.2 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.24.2->tpot) (2018.9)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker>=0.16->tpot) (2019.11.28)
    Requirement already satisfied: idna<2.9,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker>=0.16->tpot) (2.8)
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker>=0.16->tpot) (1.24.3)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests>=2.3.0->update_checker>=0.16->tpot) (3.0.4)
    Requirement already satisfied: six>=1.5 in /usr/local/lib/python3.6/dist-packages (from python-dateutil>=2.6.1->pandas>=0.24.2->tpot) (1.12.0)
    Building wheels for collected packages: tpot
      Building wheel for tpot (setup.py) ... [?25l[?25hdone
      Created wheel for tpot: filename=TPOT-0.11.0-cp36-none-any.whl size=75684 sha256=0ddafc0671be97eb25a6c00ab77b16fc1948da1d74d7d1bc3b6b38146c023d48
      Stored in directory: /root/.cache/pip/wheels/5e/79/3b/49ccea9a29f28d0cdecbca22d71515c23bf45e1e65bc925cb8
    Successfully built tpot
    Installing collected packages: tqdm, tpot
      Found existing installation: tqdm 4.28.1
        Uninstalling tqdm-4.28.1:
          Successfully uninstalled tqdm-4.28.1
    Successfully installed tpot-0.11.0 tqdm-4.40.2
    



    Collecting h2o
    [?25l  Downloading https://files.pythonhosted.org/packages/6b/d1/aaa74df4716b2454d6034261807ef3855d014862c801f0e9b803b568006d/h2o-3.28.0.1.tar.gz (123.4MB)
    [K     |████████████████████████████████| 123.4MB 85kB/s 
    [?25hRequirement already satisfied: requests in /usr/local/lib/python3.6/dist-packages (from h2o) (2.21.0)
    Requirement already satisfied: tabulate in /usr/local/lib/python3.6/dist-packages (from h2o) (0.8.6)
    Requirement already satisfied: future in /usr/local/lib/python3.6/dist-packages (from h2o) (0.16.0)
    Collecting colorama>=0.3.8
      Downloading https://files.pythonhosted.org/packages/c9/dc/45cdef1b4d119eb96316b3117e6d5708a08029992b2fee2c143c7a0a5cc5/colorama-0.4.3-py2.py3-none-any.whl
    Requirement already satisfied: urllib3<1.25,>=1.21.1 in /usr/local/lib/python3.6/dist-packages (from requests->h2o) (1.24.3)
    Requirement already satisfied: certifi>=2017.4.17 in /usr/local/lib/python3.6/dist-packages (from requests->h2o) (2019.11.28)
    Requirement already satisfied: idna<2.9,>=2.5 in /usr/local/lib/python3.6/dist-packages (from requests->h2o) (2.8)
    Requirement already satisfied: chardet<3.1.0,>=3.0.2 in /usr/local/lib/python3.6/dist-packages (from requests->h2o) (3.0.4)
    Building wheels for collected packages: h2o
      Building wheel for h2o (setup.py) ... [?25l[?25hdone
      Created wheel for h2o: filename=h2o-3.28.0.1-py2.py3-none-any.whl size=123410554 sha256=48d05d4aad07b2788d4159a48e303bd6edd8891ea45ff685cb27556a8d9318d3
      Stored in directory: /root/.cache/pip/wheels/ff/82/f4/3fe9c895b93c53b25ae44dc44aa54dad571c711f48bc485fe9
    Successfully built h2o
    Installing collected packages: colorama, h2o
    Successfully installed colorama-0.4.3 h2o-3.28.0.1
    


```python
from tpot import TPOTClassifier
from tpot import TPOTRegressor
from sklearn.datasets import load_digits
from sklearn.model_selection import train_test_split

# create & fit TPOT classifier with 
pipeline_optimizer = TPOTRegressor(generations=5,
                                    population_size=20,
                                    cv=5,
                                    random_state=42,
                                    #njobs=-1,
                                    scoring= 'neg_mean_squared_error', #'f1', 'f1_macro', 'f1_micro',
                                    max_time_mins= 60,  #MAX_TIME_MINS
                                    max_eval_time_mins	=20,
                                    verbosity=2)


pipeline_optimizer.fit(train_x, train_y)
print(pipeline_optimizer.score(test_x, test_y))
```


    HBox(children=(FloatProgress(value=0.0, description='Optimization Progress', max=20.0, style=ProgressStyle(des…


    Generation 1 - Current best internal CV score: -5690.647614823849
    Generation 2 - Current best internal CV score: -5690.647614823849
    Generation 3 - Current best internal CV score: -5526.782440894636
    Generation 4 - Current best internal CV score: -5344.624150306781
    
    
    TPOT closed during evaluation in one generation.
    WARNING: TPOT may not provide a good pipeline if TPOT is stopped/interrupted in a early generation.
    
    
    TPOT closed prematurely. Will use the current best pipeline.
    
    Best pipeline: RandomForestRegressor(CombineDFs(input_matrix, input_matrix), bootstrap=False, max_features=0.05, min_samples_leaf=1, min_samples_split=14, n_estimators=100)
    -4983.7293780759865
    

    /usr/local/lib/python3.6/dist-packages/sklearn/preprocessing/_function_transformer.py:97: FutureWarning: The default validate=True will be replaced by validate=False in 0.22.
      "validate=False in 0.22.", FutureWarning)
    /usr/local/lib/python3.6/dist-packages/sklearn/preprocessing/_function_transformer.py:97: FutureWarning: The default validate=True will be replaced by validate=False in 0.22.
      "validate=False in 0.22.", FutureWarning)
    


```python

# save our model code
#tpot.export('tpot_pipeline.py')


pipeline_optimizer.export('tpot_exported_pipeline.py')

# print the model code to see what it says
#!cat tpot_pipeline.py

```


```python
predict(test_x)
```

#### H20.ai AutoML
H2O’s AutoML can be used for automating the machine learning workflow, which includes automatic training and tuning of many models within a user-specified time-limit.

H2O AutoML performs Random Search followed by a stacking stage. By default it uses the H2O machine learning package, which supports distributed training. It is available in both Python and R languages.


```python
import h2o
from h2o.automl import H2OAutoML

# initilaize an H20 instance running locally
h2o.init(nthreads=-1)

h2o.cluster().show_status()


# convert our data to h20Frame
Energy_Data_h2o = h2o.H2OFrame(Energy_Data)



#train_data = train_data.cbind(y_data)
splits = Energy_Data_h2o.split_frame(ratios = [0.7], seed = 1)
train = splits[0]
test = splits[1]

folds = 5

# Run AutoML for 20 base models (limited to 1 hour max runtime by default)
aml = H2OAutoML(max_models=20,
                max_runtime_secs=600,
                # validation_frame=val_df,
                nfolds = folds,
                #balance_classes = TRUE, 
                stopping_metric='AUTO',  #aucpr,mean_per_class_error,logloss
                sort_metric = "RMSE",
                seed=1)
aml.train(y = 'Appliances', training_frame=train
          #leaderboard_frame = test
          )



# The leader model can be access with `aml.leader`
# save the model out (we'll need to for tomorrow!)
h2o.save_model(aml.leader)
```

    Checking whether there is an H2O instance running at http://localhost:54321 ..... not found.
    Attempting to start a local H2O server...
      Java Version: openjdk version "11.0.4" 2019-07-16; OpenJDK Runtime Environment (build 11.0.4+11-post-Ubuntu-1ubuntu218.04.3); OpenJDK 64-Bit Server VM (build 11.0.4+11-post-Ubuntu-1ubuntu218.04.3, mixed mode, sharing)
      Starting server from /usr/local/lib/python3.6/dist-packages/h2o/backend/bin/h2o.jar
      Ice root: /tmp/tmpyby0u3va
      JVM stdout: /tmp/tmpyby0u3va/h2o_unknownUser_started_from_python.out
      JVM stderr: /tmp/tmpyby0u3va/h2o_unknownUser_started_from_python.err
      Server is running at http://127.0.0.1:54321
    Connecting to H2O server at http://127.0.0.1:54321 ... successful.
    


<div style="overflow:auto"><table style="width:50%"><tr><td>H2O cluster uptime:</td>
<td>02 secs</td></tr>
<tr><td>H2O cluster timezone:</td>
<td>Etc/UTC</td></tr>
<tr><td>H2O data parsing timezone:</td>
<td>UTC</td></tr>
<tr><td>H2O cluster version:</td>
<td>3.28.0.1</td></tr>
<tr><td>H2O cluster version age:</td>
<td>1 day </td></tr>
<tr><td>H2O cluster name:</td>
<td>H2O_from_python_unknownUser_dm09ir</td></tr>
<tr><td>H2O cluster total nodes:</td>
<td>1</td></tr>
<tr><td>H2O cluster free memory:</td>
<td>2.938 Gb</td></tr>
<tr><td>H2O cluster total cores:</td>
<td>2</td></tr>
<tr><td>H2O cluster allowed cores:</td>
<td>2</td></tr>
<tr><td>H2O cluster status:</td>
<td>accepting new members, healthy</td></tr>
<tr><td>H2O connection url:</td>
<td>http://127.0.0.1:54321</td></tr>
<tr><td>H2O connection proxy:</td>
<td>{'http': None, 'https': None}</td></tr>
<tr><td>H2O internal security:</td>
<td>False</td></tr>
<tr><td>H2O API Extensions:</td>
<td>Amazon S3, XGBoost, Algos, AutoML, Core V3, TargetEncoder, Core V4</td></tr>
<tr><td>Python version:</td>
<td>3.6.9 final</td></tr></table></div>



<div style="overflow:auto"><table style="width:50%"><tr><td>H2O cluster uptime:</td>
<td>02 secs</td></tr>
<tr><td>H2O cluster timezone:</td>
<td>Etc/UTC</td></tr>
<tr><td>H2O data parsing timezone:</td>
<td>UTC</td></tr>
<tr><td>H2O cluster version:</td>
<td>3.28.0.1</td></tr>
<tr><td>H2O cluster version age:</td>
<td>1 day </td></tr>
<tr><td>H2O cluster name:</td>
<td>H2O_from_python_unknownUser_dm09ir</td></tr>
<tr><td>H2O cluster total nodes:</td>
<td>1</td></tr>
<tr><td>H2O cluster free memory:</td>
<td>2.938 Gb</td></tr>
<tr><td>H2O cluster total cores:</td>
<td>2</td></tr>
<tr><td>H2O cluster allowed cores:</td>
<td>2</td></tr>
<tr><td>H2O cluster status:</td>
<td>accepting new members, healthy</td></tr>
<tr><td>H2O connection url:</td>
<td>http://127.0.0.1:54321</td></tr>
<tr><td>H2O connection proxy:</td>
<td>{'http': None, 'https': None}</td></tr>
<tr><td>H2O internal security:</td>
<td>False</td></tr>
<tr><td>H2O API Extensions:</td>
<td>Amazon S3, XGBoost, Algos, AutoML, Core V3, TargetEncoder, Core V4</td></tr>
<tr><td>Python version:</td>
<td>3.6.9 final</td></tr></table></div>


    Parse progress: |█████████████████████████████████████████████████████████| 100%
    AutoML progress: |████████████████████████████████████████████████████████| 100%
    




    '/content/StackedEnsemble_AllModels_AutoML_20191218_010817'




```python
# View the top five models from the AutoML Leaderboard
lb = aml.leaderboard
lb.head(rows=5)
```


<table>
<thead>
<tr><th>model_id                                           </th><th style="text-align: right;">   rmse</th><th style="text-align: right;">  mean_residual_deviance</th><th style="text-align: right;">    mse</th><th style="text-align: right;">    mae</th><th style="text-align: right;">   rmsle</th></tr>
</thead>
<tbody>
<tr><td>StackedEnsemble_AllModels_AutoML_20191218_010817   </td><td style="text-align: right;">68.7509</td><td style="text-align: right;">                 4726.69</td><td style="text-align: right;">4726.69</td><td style="text-align: right;">33.606 </td><td style="text-align: right;">0.396076</td></tr>
<tr><td>StackedEnsemble_BestOfFamily_AutoML_20191218_010817</td><td style="text-align: right;">68.7863</td><td style="text-align: right;">                 4731.55</td><td style="text-align: right;">4731.55</td><td style="text-align: right;">33.5578</td><td style="text-align: right;">0.396007</td></tr>
<tr><td>XRT_1_AutoML_20191218_010817                       </td><td style="text-align: right;">69.6868</td><td style="text-align: right;">                 4856.24</td><td style="text-align: right;">4856.24</td><td style="text-align: right;">33.9568</td><td style="text-align: right;">0.401883</td></tr>
<tr><td>DRF_1_AutoML_20191218_010817                       </td><td style="text-align: right;">69.7698</td><td style="text-align: right;">                 4867.82</td><td style="text-align: right;">4867.82</td><td style="text-align: right;">33.744 </td><td style="text-align: right;">0.401009</td></tr>
<tr><td>XGBoost_2_AutoML_20191218_010817                   </td><td style="text-align: right;">69.8614</td><td style="text-align: right;">                 4880.61</td><td style="text-align: right;">4880.61</td><td style="text-align: right;">33.9859</td><td style="text-align: right;">0.402586</td></tr>
</tbody>
</table>





    




```python
#save model as MOJO file
#path = "./", the path can be specified
#also in the parenthesis
aml.leader.download_mojo()

# print the rmse for the cross-validated data
#lb.rmse(xval=True)
```




    '/content/StackedEnsemble_AllModels_AutoML_20191218_010817.zip'




```python
#Predict Using Leader Model
pred = aml.predict(test)
pred.head()
```

    stackedensemble prediction progress: |████████████████████████████████████| 100%
    


<table>
<thead>
<tr><th style="text-align: right;">  predict</th></tr>
</thead>
<tbody>
<tr><td style="text-align: right;">  61.1797</td></tr>
<tr><td style="text-align: right;">  96.4973</td></tr>
<tr><td style="text-align: right;">  92.1388</td></tr>
<tr><td style="text-align: right;"> 175.122 </td></tr>
<tr><td style="text-align: right;"> 314.226 </td></tr>
<tr><td style="text-align: right;"> 250.165 </td></tr>
<tr><td style="text-align: right;"> 141.156 </td></tr>
<tr><td style="text-align: right;"> 107.482 </td></tr>
<tr><td style="text-align: right;">  69.8182</td></tr>
<tr><td style="text-align: right;">  89.4973</td></tr>
</tbody>
</table>





    




```python
#the standard model_performance() method can be applied to
# the AutoML leader model and a test set to generate an H2O model performance object.
perf = aml.leader.model_performance(test)
perf
```

    
    ModelMetricsRegressionGLM: stackedensemble
    ** Reported on test data. **
    
    MSE: 5064.8189589784215
    RMSE: 71.16754147066219
    MAE: 34.120372351242764
    RMSLE: 0.3951929658613881
    R^2: 0.5334201696104182
    Mean Residual Deviance: 5064.8189589784215
    Null degrees of freedom: 5865
    Residual degrees of freedom: 5860
    Null deviance: 63677819.057285145
    Residual deviance: 29710228.01336742
    AIC: 66698.39903447537
    




    




```python
h2o.cluster().shutdown()
```

#### R

We will also train the h20 automl model also in R.


```python
# activate R magic to run R in google colab notebook
import rpy2
%load_ext rpy2.ipython
```

    The rpy2.ipython extension is already loaded. To reload it, use:
      %reload_ext rpy2.ipython
    


```r
%%R
# Next, we download, install and initialize the H2O package for R.
install.packages("h2o", repos=(c("http://s3.amazonaws.com/h2o-release/h2o/master/1497/R", getOption("repos"))))
library(h2o)
localH2O = h2o.init()
#localH2O = h2o.init(nthreads = -1,strict_version_check = FALSE,startH2O = FALSE)

h2o.no_progress()  # Turn off progress bars for notebook readability
```


    
    H2O is not running yet, starting it now...
    
    Note:  In case of errors look at the following log files:
        /tmp/RtmpDB345V/h2o_UnknownUser_started_from_r.out
        /tmp/RtmpDB345V/h2o_UnknownUser_started_from_r.err
    
    
    Starting H2O JVM and connecting: .. Connection successful!
    
    R is connected to the H2O cluster: 
        H2O cluster uptime:         2 seconds 441 milliseconds 
        H2O cluster timezone:       Etc/UTC 
        H2O data parsing timezone:  UTC 
        H2O cluster version:        3.26.0.2 
        H2O cluster version age:    4 months and 21 days !!! 
        H2O cluster name:           H2O_started_from_R_root_uiy706 
        H2O cluster total nodes:    1 
        H2O cluster total memory:   2.94 GB 
        H2O cluster total cores:    2 
        H2O cluster allowed cores:  2 
        H2O cluster healthy:        TRUE 
        H2O Connection ip:          localhost 
        H2O Connection port:        54321 
        H2O Connection proxy:       NA 
        H2O Internal Security:      FALSE 
        H2O API Extensions:         Amazon S3, XGBoost, Algos, AutoML, Core V3, Core V4 
        R Version:                  R version 3.6.1 (2019-07-05) 
    
    


Convert the Energy_Data from pandas  dataframe in python to an R dataframe.


```r
%%R -i Energy_Data

head(Energy_Data,5)
```

    /usr/local/lib/python3.6/dist-packages/rpy2/robjects/pandas2ri.py:191: FutureWarning: from_items is deprecated. Please use DataFrame.from_dict(dict(items), ...) instead. DataFrame.from_dict(OrderedDict(items)) may be used to preserve the key order.
      res = PandasDataFrame.from_items(items)
    


      Appliances lights    T1     RH_1   T2     RH_2    T3     RH_3       T4
    0         60     30 19.89 47.59667 19.2 44.79000 19.79 44.73000 19.00000
    1         60     30 19.89 46.69333 19.2 44.72250 19.79 44.79000 19.00000
    2         50     30 19.89 46.30000 19.2 44.62667 19.79 44.93333 18.92667
    3         50     40 19.89 46.06667 19.2 44.59000 19.79 45.00000 18.89000
    4         60     40 19.89 46.33333 19.2 44.53000 19.79 45.00000 18.89000
          RH_4       T5  RH_5       T6     RH_6       T7     RH_7   T8     RH_8
    0 45.56667 17.16667 55.20 7.026667 84.25667 17.20000 41.62667 18.2 48.90000
    1 45.99250 17.16667 55.20 6.833333 84.06333 17.20000 41.56000 18.2 48.86333
    2 45.89000 17.16667 55.09 6.560000 83.15667 17.20000 41.43333 18.2 48.73000
    3 45.72333 17.16667 55.09 6.433333 83.42333 17.13333 41.29000 18.1 48.59000
    4 45.53000 17.20000 55.09 6.366667 84.89333 17.20000 41.23000 18.1 48.59000
            T9  RH_9    T_out Press_mm_hg RH_out Windspeed Visibility Tdewpoint
    0 17.03333 45.53 6.600000       733.5     92  7.000000   63.00000       5.3
    1 17.06667 45.56 6.483333       733.6     92  6.666667   59.16667       5.2
    2 17.00000 45.50 6.366667       733.7     92  6.333333   55.33333       5.1
    3 17.00000 45.40 6.250000       733.8     92  6.000000   51.50000       5.0
    4 17.00000 45.40 6.133333       733.9     92  5.666667   47.66667       4.9
           rv1      rv2 hour day month week weekday quarter year
    0 13.27543 13.27543   17  11     1    2       0       1 2016
    1 18.60619 18.60619   17  11     1    2       0       1 2016
    2 28.64267 28.64267   17  11     1    2       0       1 2016
    3 45.41039 45.41039   17  11     1    2       0       1 2016
    4 10.08410 10.08410   17  11     1    2       0       1 2016
    



```r
%%R

# Identify predictors and response
y <- "Appliances"
x <- setdiff(names(Energy_Data), y)
```


```r
%%R

#convert R dataframe o h2o dataframe
Energy_Data_h2o = as.h2o(Energy_Data)

# split into train and validation sets
splits = h2o.splitFrame(Energy_Data_h2o,ratios = c(0.7), seed = 1)
train = splits[[1]]
test = splits[[2]]
```


```r
%%R
library(h2o)


# For binary classification, response should be a factor
#train[,y] <- as.factor(train[,y])
#test[,y] <- as.factor(test[,y])

# import the  dataset:
#df<- h2o.importFile("https://")




# set the number of folds for you n-fold cross validation:
folds <- 5

# Run AutoML for 20 base models (limited to 1 hour max runtime by default)
model_h2o_automl = h2o.automl(x = x, y = y,
                  nfolds = folds,
                  # validation_frame=val_df,
                  training_frame = train,
                  max_models = 20,
                  max_runtime_secs=600,
                  sort_metric = "RMSE",
                  #balance_classes = TRUE, 
                stopping_metric='AUTO',  #aucpr,mean_per_class_error,logloss
                
                seed=1
                  )

```


```r
%%R
# Get model ids for all models in the AutoML Leaderboard
model_ids <- as.data.frame(model_h2o_automl@leaderboard$model_id)[,1]
# Get the "All Models" Stacked Ensemble model
se <- h2o.getModel(grep("StackedEnsemble_AllModels", model_ids, value = TRUE)[1])
# Get the Stacked Ensemble metalearner model
metalearner <- h2o.getModel(se@model$metalearner$name)
metalearner
```


    Model Details:
    ==============
    
    H2ORegressionModel: glm
    Model ID:  metalearner_AUTO_StackedEnsemble_AllModels_AutoML_20191218_024428 
    GLM Model: summary
        family     link                              regularization
    1 gaussian identity Elastic Net (alpha = 0.5, lambda = 0.1497 )
      number_of_predictors_total number_of_active_predictors number_of_iterations
    1                         19                           6                    1
                                                          training_frame
    1 levelone_training_StackedEnsemble_AllModels_AutoML_20191218_024428
    
    Coefficients: glm coefficients
                                                    names coefficients
    1                                           Intercept    -6.641758
    2                        XRT_1_AutoML_20191218_024428     0.252550
    3                        DRF_1_AutoML_20191218_024428     0.173411
    4                    XGBoost_2_AutoML_20191218_024428     0.198408
    5                    XGBoost_1_AutoML_20191218_024428     0.137834
    6                        GBM_4_AutoML_20191218_024428     0.000000
    7                        GBM_3_AutoML_20191218_024428     0.000000
    8                        GBM_2_AutoML_20191218_024428     0.000000
    9                        GBM_5_AutoML_20191218_024428     0.000000
    10          GBM_grid_1_AutoML_20191218_024428_model_1     0.128306
    11                       GBM_1_AutoML_20191218_024428     0.000000
    12                   XGBoost_3_AutoML_20191218_024428     0.000000
    13              DeepLearning_1_AutoML_20191218_024428     0.000000
    14          GBM_grid_1_AutoML_20191218_024428_model_3     0.000000
    15          GLM_grid_1_AutoML_20191218_024428_model_1     0.000000
    16      XGBoost_grid_1_AutoML_20191218_024428_model_2     0.380006
    17 DeepLearning_grid_1_AutoML_20191218_024428_model_2     0.000000
    18          GBM_grid_1_AutoML_20191218_024428_model_2     0.000000
    19 DeepLearning_grid_1_AutoML_20191218_024428_model_1     0.000000
    20      XGBoost_grid_1_AutoML_20191218_024428_model_1     0.000000
       standardized_coefficients
    1                  97.560747
    2                  18.034622
    3                  12.359363
    4                  13.761426
    5                   9.470020
    6                   0.000000
    7                   0.000000
    8                   0.000000
    9                   0.000000
    10                 10.556734
    11                  0.000000
    12                  0.000000
    13                  0.000000
    14                  0.000000
    15                  0.000000
    16                 12.679330
    17                  0.000000
    18                  0.000000
    19                  0.000000
    20                  0.000000
    
    H2ORegressionMetrics: glm
    ** Reported on training data. **
    
    MSE:  4651.294
    RMSE:  68.20039
    MAE:  33.06181
    RMSLE:  0.3919449
    Mean Residual Deviance :  4651.294
    R^2 :  0.5512544
    Null Deviance :143753580
    Null D.o.F. :13868
    Residual Deviance :64508792
    Residual D.o.F. :13862
    AIC :156496.8
    
    
    
    H2ORegressionMetrics: glm
    ** Reported on cross-validation data. **
    ** 5-fold cross-validation on training data (Metrics computed for combined holdout predictions) **
    
    MSE:  4664.595
    RMSE:  68.29784
    MAE:  33.09259
    RMSLE:  0.3921523
    Mean Residual Deviance :  4664.595
    R^2 :  0.5499711
    Null Deviance :143772363
    Null D.o.F. :13868
    Residual Deviance :64693262
    Residual D.o.F. :13861
    AIC :156538.4
    
    
    Cross-Validation Metrics Summary: 
                                  mean          sd  cv_1_valid  cv_2_valid
    mae                      33.082054  0.45924768    33.94574   33.453297
    mean_residual_deviance   4659.3423    244.0951   5096.3237   5001.1846
    mse                      4659.3423    244.0951   5096.3237   5001.1846
    null_deviance          2.8754472E7   1749350.6 3.0964342E7 3.1220312E7
    r2                        0.549355  0.01679809   0.5363588  0.55255246
    residual_deviance      1.2938652E7    824356.4 1.4356344E7 1.3968309E7
    rmse                      68.21254   1.7876369    71.38854   70.719055
    rmsle                   0.39201766 0.003685681  0.39849618  0.39207774
                            cv_3_valid  cv_4_valid  cv_5_valid
    mae                      33.361572    32.31076   32.338898
    mean_residual_deviance   4634.8745    4320.175    4244.153
    mse                      4634.8745    4320.175    4244.153
    null_deviance          2.9694604E7 2.4724726E7  2.716838E7
    r2                      0.55832946   0.5139777   0.5855564
    residual_deviance      1.3107425E7 1.2001446E7 1.1259738E7
    rmse                      68.07991   65.728035    65.14716
    rmsle                   0.39180788  0.39492768  0.38277885
    



```r
%%R
#install.packages("tidyverse")
library(tidyverse)
#The leader model is stored at `aml@leader` and the leaderboard is stored at `aml@leaderboard`.

#lb = model_h2o_automl@leaderboard
lb=model_h2o_automl@leaderboard%>%as_tibble()

#Now we will view a snapshot of the top models.  Here we should see the 
#two Stacked Ensembles at or near the top of the leaderboard.  
#Stacked Ensembles can almost always outperform a single model.

print(lb)


#To view the entire leaderboard, specify the `n` argument of 
#the `print.H2OFrame()` function as the total number of rows:

print(lb, n = nrow(lb))

```


    # A tibble: 21 x 6
       model_id                          mean_residual_devi…  rmse   mse   mae rmsle
       <chr>                                           <dbl> <dbl> <dbl> <dbl> <dbl>
     1 StackedEnsemble_AllModels_AutoML…               4665.  68.3 4665.  33.1 0.392
     2 StackedEnsemble_BestOfFamily_Aut…               4716.  68.7 4716.  33.3 0.393
     3 XRT_1_AutoML_20191218_024428                    4775.  69.1 4775.  33.6 0.399
     4 DRF_1_AutoML_20191218_024428                    4849.  69.6 4849.  33.8 0.400
     5 XGBoost_2_AutoML_20191218_024428                4880.  69.9 4880.  34.0 0.402
     6 XGBoost_1_AutoML_20191218_024428                4964.  70.5 4964.  34.7 0.409
     7 GBM_4_AutoML_20191218_024428                    5270.  72.6 5270.  36.3 0.424
     8 GBM_3_AutoML_20191218_024428                    5502.  74.2 5502.  37.5 0.438
     9 GBM_2_AutoML_20191218_024428                    5543.  74.5 5543.  37.9 0.442
    10 GBM_5_AutoML_20191218_024428                    5626.  75.0 5626.  39.0 0.452
    # … with 11 more rows
    # A tibble: 21 x 6
       model_id                        mean_residual_devi…  rmse    mse   mae  rmsle
       <chr>                                         <dbl> <dbl>  <dbl> <dbl>  <dbl>
     1 StackedEnsemble_AllModels_Auto…               4665.  68.3  4665.  33.1  0.392
     2 StackedEnsemble_BestOfFamily_A…               4716.  68.7  4716.  33.3  0.393
     3 XRT_1_AutoML_20191218_024428                  4775.  69.1  4775.  33.6  0.399
     4 DRF_1_AutoML_20191218_024428                  4849.  69.6  4849.  33.8  0.400
     5 XGBoost_2_AutoML_20191218_0244…               4880.  69.9  4880.  34.0  0.402
     6 XGBoost_1_AutoML_20191218_0244…               4964.  70.5  4964.  34.7  0.409
     7 GBM_4_AutoML_20191218_024428                  5270.  72.6  5270.  36.3  0.424
     8 GBM_3_AutoML_20191218_024428                  5502.  74.2  5502.  37.5  0.438
     9 GBM_2_AutoML_20191218_024428                  5543.  74.5  5543.  37.9  0.442
    10 GBM_5_AutoML_20191218_024428                  5626.  75.0  5626.  39.0  0.452
    11 GBM_grid_1_AutoML_20191218_024…               5645.  75.1  5645.  40.7 NA    
    12 GBM_1_AutoML_20191218_024428                  5711.  75.6  5711.  38.7  0.452
    13 XGBoost_3_AutoML_20191218_0244…               5816.  76.3  5816.  39.2  0.457
    14 DeepLearning_1_AutoML_20191218…               8251.  90.8  8251.  55.0 NA    
    15 GBM_grid_1_AutoML_20191218_024…               8475.  92.1  8475.  52.1  0.605
    16 GLM_grid_1_AutoML_20191218_024…               8643.  93.0  8643.  52.6  0.615
    17 XGBoost_grid_1_AutoML_20191218…               9491.  97.4  9491.  55.0  0.795
    18 DeepLearning_grid_1_AutoML_201…               9818.  99.1  9818.  59.6 NA    
    19 GBM_grid_1_AutoML_20191218_024…              10290. 101.  10290.  60.0  0.699
    20 DeepLearning_grid_1_AutoML_201…              10584. 103.  10584.  67.7 NA    
    21 XGBoost_grid_1_AutoML_20191218…              10961. 105.  10961.  61.0  1.68 
    



```r
%%R
#install.packages("yardstick")
#Examine the variable importance of the metalearner (combiner) 
#algorithm in the ensemble.  This shows us how much each base learner is contributing to the ensemble. The AutoML Stacked Ensembles use the default metalearner algorithm (GLM with non-negative weights), so the variable importance of the metalearner is actually the standardized coefficient magnitudes of the GLM. 

print(h2o.varimp_plot(model_h2o_automl@leader))
#We can also plot the base learner contributions to the ensemble.

print(h2o.varimp(model_h2o_automl))


## Save Leader Model

#There are two ways to save the leader model -- binary format 
#and MOJO format.  If you're taking your leader model to production,
# then we'd suggest the MOJO format since it's optimized for production use.

#h2o.saveModel(model_h2o_automl@leader, path = "")



#h2o.download_mojo(model_h2o_automl@leader, path = "")

# Eval ensemble performance on a test set
perf <- h2o.performance(model_h2o_automl@leader, newdata = test)
print(perf)

print('Train set r2 :')
print(h2o.r2(model_h2o_automl@leader))


pred=predict(model_h2o_automl@leader, newdata = test)

#df= bind_cols(truth=test$Appliances,response=pred)

df= h2o.cbind(test$Appliances,pred)
names(df)[1:2] = c("truth","response")
print('Test set r2 :')
print(yardstick::rsq(as_tibble(df),truth,response))
#test_performance = model_h2o_automl.model_performance(test)
#print(test_performance)
df
```


    NULL
    NULL
    H2ORegressionMetrics: stackedensemble
    
    MSE:  5102.83
    RMSE:  71.43409
    MAE:  33.77602
    RMSLE:  0.3921076
    Mean Residual Deviance :  5102.83
    
    [1] "Train set r2 :"
    [1] 0.9321445
    [1] "Test set r2 :"
    # A tibble: 1 x 3
      .metric .estimator .estimate
      <chr>   <chr>          <dbl>
    1 rsq     standard       0.530
      truth  response
    1    50  60.92641
    2    60 110.36650
    3    60 112.35890
    4    70 186.47200
    5   230 320.81606
    6   430 259.65633
    
    [5866 rows x 2 columns] 
    



```r
%%R
ggplot(Energy_Data, aes(x = Appliances)) + 
  geom_density(trim = TRUE) + 
  geom_density(data = Energy_Data, trim = TRUE, col = "red")
```


![png](output_35_0.png)



```python
## Ensemble Exploration

#To understand how the ensemble works, let's take a peek inside the 
#Stacked Ensemble "All Models" model.  The "All Models" ensemble is 
#an ensemble of all of the individual models in the AutoML run.  
#This is often the top performing model on the leaderboard.



# Get model ids for all models in the AutoML Leaderboard
model_ids <- as.data.frame(aml@leaderboard$model_id)[,1]
# Get the "All Models" Stacked Ensemble model
se <- h2o.getModel(grep("StackedEnsemble_AllModels", model_ids, value = TRUE)[1])
# Get the Stacked Ensemble metalearner model
metalearner <- h2o.getModel(se@model$metalearner$name)


```

    /content
    

#### auto-sklearn
auto-sklearn is an automated machine learning toolkit and a drop-in replacement for a scikit-learn estimator. It provides a scikit-learn-like interface in Python and uses Bayesian optimization to find good machine learning pipelines.

It uses  automatic ensemble construction and Meta-learning to warm-start the search procedure, this means that the search is more likely to start with good pipelines.
Auto-sklearn can be installed using either pip or  conda commands. An example code fro training a regression model is given below



```python
!ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
!brew install swig
!swig -version

!pip install swig
!pip install pyrfr
!scikit-learn
!pip install auto-sklearn
```

    /bin/bash: brew: command not found
    /bin/bash: swig: command not found
    


```python
import autosklearn.classification
import sklearn.model_selection
import sklearn.datasets
import sklearn.datasets
import sklearn.metrics
import autosklearn.regression




#automl = autosklearn.classification.AutoSklearnClassifier()
#automl.fit(train_y, train_y)
#y_hat = automl.predict(test_y)
#print("Accuracy score", sklearn.metrics.accuracy_score(test_y, y_hat))

feature_types = (['numerical'] * 27))

automl = autosklearn.regression.AutoSklearnRegressor(
        time_left_for_this_task=120,
        per_run_time_limit=30,
        tmp_folder='/tmp/',
        output_folder='/tmp/',
    )
automl.fit(train_x, train_y,
               feat_type=feature_types)

print(automl.show_models())
predictions = automl.predict(test_x)
print("R2 score:", sklearn.metrics.r2_score(test_y, predictions))
```

#### Automatic Deep Learning
AutoKeras is an open source software library for automated machine learning (AutoML).  The ultimate goal of AutoML is to provide easily accessible deep learning tools to domain experts with limited data science or machine learning background. AutoKeras provides functions to automatically search for architecture and hyperparameters of deep learning models. This makes very slow because it requires full retraining from scratch of each model
AutoKeras is useful for  (2D) Image classification. Many other ML deep learning task has not been implemented so far.
  Auto-Keras uses network morphism to reduce training time in neural architecture search. Further, it uses a Gaussian process (GP) for Bayesian optimization of guiding network morphism.




```python
!!pip install autokeras
```






    ['Collecting autokeras',
     '\x1b[?25l  Downloading https://files.pythonhosted.org/packages/c2/32/de74bf6afd09925980340355a05aa6a19e7378ed91dac09e76a487bd136d/autokeras-0.4.0.tar.gz (67kB)',
     '',
|


```python
from tensorflow import keras
from keras.datasets import mnist
from autokeras.image.image_supervised import ImageClassifier

(x_train, y_train), (x_test, y_test) = mnist.load_data()
x_train = x_train.reshape(x_train.shape + (1,))
x_test = x_test.reshape(x_test.shape + (1,))

clf = ImageClassifier(verbose=True)
clf.fit(x_train, y_train, time_limit=12 * 60 * 60)
clf.final_fit(x_train, y_train, x_test, y_test, retrain=True)
y = clf.evaluate(x_test, y_test)
print(y)

```

    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:526: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      _np_qint8 = np.dtype([("qint8", np.int8, 1)])
    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:527: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      _np_quint8 = np.dtype([("quint8", np.uint8, 1)])
    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:528: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      _np_qint16 = np.dtype([("qint16", np.int16, 1)])
    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:529: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      _np_quint16 = np.dtype([("quint16", np.uint16, 1)])
    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:530: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      _np_qint32 = np.dtype([("qint32", np.int32, 1)])
    /usr/local/lib/python3.6/dist-packages/tensorflow/python/framework/dtypes.py:535: FutureWarning: Passing (type, 1) or '1type' as a synonym of type is deprecated; in a future version of numpy, it will be understood as (type, (1,)) / '(1,)type'.
      np_resource = np.dtype([("resource", np.ubyte, 1)])
    


<p style="color: red;">
The default version of TensorFlow in Colab will soon switch to TensorFlow 2.x.<br>
We recommend you <a href="https://www.tensorflow.org/guide/migrate" target="_blank">upgrade</a> now 
or ensure your notebook will continue to use TensorFlow 1.x via the <code>%tensorflow_version 1.x</code> magic:
<a href="https://colab.research.google.com/notebooks/tensorflow_version.ipynb" target="_blank">more info</a>.</p>



    Using TensorFlow backend.
    

    Better speed can be achieved with apex installed from https://www.github.com/nvidia/apex.
    Downloading data from https://s3.amazonaws.com/img-datasets/mnist.npz
    11493376/11490434 [==============================] - 1s 0us/step
    Saving Directory: /tmp/autokeras_NEPR81
    Preprocessing the images.
    Preprocessing finished.
    
    Initializing search.
    Initialization finished.
    
    
    +----------------------------------------------+
    |               Training model 0               |
    +----------------------------------------------+
                                                                                                        
    No loss decrease after 5 epochs.
    
    
    Saving model.
    +--------------------------------------------------------------------------+
    |        Model ID        |          Loss          |      Metric Value      |
    +--------------------------------------------------------------------------+
    |           0            |   0.4400691822171211   |         0.9648         |
    +--------------------------------------------------------------------------+
    
    
    +----------------------------------------------+
    |               Training model 1               |
    +----------------------------------------------+
    
    No loss decrease after 5 epochs.
    
    
    Saving model.
    +--------------------------------------------------------------------------+
    |        Model ID        |          Loss          |      Metric Value      |
    +--------------------------------------------------------------------------+
    |           1            |  0.07899918230250477   |         0.9936         |
    +--------------------------------------------------------------------------+
    
    
    +----------------------------------------------+
    |               Training model 2               |
    +----------------------------------------------+
    Epoch-6, Current Metric - 0.97:  77%|███████████████████▎     | 360/465 [01:57<00:35,  2.96 batch/s]

#### auto_ml

auto_ml is designed for production. It is very simple to use and turning out to be one of my favorites. More informattion on this package can be found [here](https://auto-ml.readthedocs.io/en/latest/index.html). You can train 
XGBoost, Deep Leaarning with TensorFlow & Keras,CatBoost  and LightGBM which are all integrated with auto_ml.
Generally, just pass one of them in for model_names. `ml_predictor.train(data, model_names=[‘DeepLearningClassifier’]).`

Available options are - DeepLearningClassifier and DeepLearningRegressor - XGBClassifier and XGBRegressor - LGBMClassifier and LGBMRegressor.
Among the numerous automation this package provides include feature engineering like one-hot encoding, creating new features like day_of_week,minutes etc from date-time features, feature normalization etc.



```python
!pip install dill
!pip install -r advanced_requirements.txt,
!pip install auto_ml
!pip install xgboost
!pip install catboost
!pip install tensorflow
!pip install keras
!pip install ipywidgets
!jupyter nbextension enable --py widgetsnbextension
```

    Requirement already satisfied: dill in /usr/local/lib/python3.6/dist-packages (0.2.9)
    [31mERROR: Could not open requirements file: [Errno 2] No such file or directory: 'advanced_requirements.txt,'[0m
    Requirement already satisfied: auto_ml in /usr/local/lib/python3.6/dist-packages (2.9.10)
    Requirement already satisfied: numpy<2.0,>=1.11.0 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (1.16.1)
    Requirement already satisfied: dill<0.3,>=0.2.5 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.2.9)
    Requirement already satisfied: h5py<3.0,>=2.7.0 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (2.8.0)
    Requirement already satisfied: scipy<2.0,>=0.14.0 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (1.2.0)
    Requirement already satisfied: scikit-learn<1.0,>=0.18.1 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.20.2)
    Requirement already satisfied: pathos<0.3.0,>=0.2.1 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.2.5)
    Requirement already satisfied: pandas<1.0,>=0.18.0 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.25.3)
    Requirement already satisfied: tabulate<1.0,>=0.7.5 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.8.6)
    Requirement already satisfied: sklearn-deap2<0.3,>=0.2.1 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (0.2.2)
    Requirement already satisfied: python-dateutil<3.0,>=2.6.1 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (2.6.1)
    Requirement already satisfied: lightgbm<2.1,>=2.0.11 in /usr/local/lib/python3.6/dist-packages (from auto_ml) (2.0.12)
    Requirement already satisfied: six in /usr/local/lib/python3.6/dist-packages (from h5py<3.0,>=2.7.0->auto_ml) (1.12.0)
    Requirement already satisfied: pox>=0.2.7 in /usr/local/lib/python3.6/dist-packages (from pathos<0.3.0,>=0.2.1->auto_ml) (0.2.7)
    Requirement already satisfied: ppft>=1.6.6.1 in /usr/local/lib/python3.6/dist-packages (from pathos<0.3.0,>=0.2.1->auto_ml) (1.6.6.1)
    Requirement already satisfied: multiprocess>=0.70.9 in /usr/local/lib/python3.6/dist-packages (from pathos<0.3.0,>=0.2.1->auto_ml) (0.70.9)
    Requirement already satisfied: pytz>=2017.2 in /usr/local/lib/python3.6/dist-packages (from pandas<1.0,>=0.18.0->auto_ml) (2018.9)
    Requirement already satisfied: deap>=1.0.2 in /usr/local/lib/python3.6/dist-packages (from sklearn-deap2<0.3,>=0.2.1->auto_ml) (1.3.0)
    Requirement already satisfied: xgboost in /usr/local/lib/python3.6/dist-packages (0.90)
    Requirement already satisfied: scipy in /usr/local/lib/python3.6/dist-packages (from xgboost) (1.2.0)
    Requirement already satisfied: numpy in /usr/local/lib/python3.6/dist-packages (from xgboost) (1.16.1)
    Requirement already satisfied: catboost in /usr/local/lib/python3.6/dist-packages (0.20.1)
    Requirement already satisfied: six in /usr/local/lib/python3.6/dist-packages (from catboost) (1.12.0)
    Requirement already satisfied: numpy>=1.16.0 in /usr/local/lib/python3.6/dist-packages (from catboost) (1.16.1)
    Requirement already satisfied: scipy in /usr/local/lib/python3.6/dist-packages (from catboost) (1.2.0)
    Requirement already satisfied: plotly in /usr/local/lib/python3.6/dist-packages (from catboost) (4.1.1)
    Requirement already satisfied: matplotlib in /usr/local/lib/python3.6/dist-packages (from catboost) (3.1.2)
    Requirement already satisfied: pandas>=0.24.0 in /usr/local/lib/python3.6/dist-packages (from catboost) (0.25.3)
    Requirement already satisfied: graphviz in /usr/local/lib/python3.6/dist-packages (from catboost) (0.10.1)
    Requirement already satisfied: retrying>=1.3.3 in /usr/local/lib/python3.6/dist-packages (from plotly->catboost) (1.3.3)
    Requirement already satisfied: python-dateutil>=2.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->catboost) (2.6.1)
    Requirement already satisfied: cycler>=0.10 in /usr/local/lib/python3.6/dist-packages (from matplotlib->catboost) (0.10.0)
    Requirement already satisfied: kiwisolver>=1.0.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->catboost) (1.1.0)
    Requirement already satisfied: pyparsing!=2.0.4,!=2.1.2,!=2.1.6,>=2.0.1 in /usr/local/lib/python3.6/dist-packages (from matplotlib->catboost) (2.4.5)
    Requirement already satisfied: pytz>=2017.2 in /usr/local/lib/python3.6/dist-packages (from pandas>=0.24.0->catboost) (2018.9)
    Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from kiwisolver>=1.0.1->matplotlib->catboost) (42.0.2)
    Requirement already satisfied: tensorflow in /usr/local/lib/python3.6/dist-packages (1.13.1)
    Requirement already satisfied: keras-preprocessing>=1.0.5 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.1.0)
    Requirement already satisfied: grpcio>=1.8.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.15.0)
    Requirement already satisfied: protobuf>=3.6.1 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (3.10.0)
    Requirement already satisfied: six>=1.10.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.12.0)
    Requirement already satisfied: termcolor>=1.1.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.1.0)
    Requirement already satisfied: numpy>=1.13.3 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.16.1)
    Requirement already satisfied: tensorboard<1.14.0,>=1.13.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.13.1)
    Requirement already satisfied: tensorflow-estimator<1.14.0rc0,>=1.13.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.13.0)
    Requirement already satisfied: gast>=0.2.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (0.2.2)
    Requirement already satisfied: wheel>=0.26 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (0.33.6)
    Requirement already satisfied: astor>=0.6.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (0.8.1)
    Requirement already satisfied: keras-applications>=1.0.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (1.0.8)
    Requirement already satisfied: absl-py>=0.1.6 in /usr/local/lib/python3.6/dist-packages (from tensorflow) (0.8.1)
    Requirement already satisfied: setuptools in /usr/local/lib/python3.6/dist-packages (from protobuf>=3.6.1->tensorflow) (42.0.2)
    Requirement already satisfied: werkzeug>=0.11.15 in /usr/local/lib/python3.6/dist-packages (from tensorboard<1.14.0,>=1.13.0->tensorflow) (0.16.0)
    Requirement already satisfied: markdown>=2.6.8 in /usr/local/lib/python3.6/dist-packages (from tensorboard<1.14.0,>=1.13.0->tensorflow) (3.1.1)
    Requirement already satisfied: mock>=2.0.0 in /usr/local/lib/python3.6/dist-packages (from tensorflow-estimator<1.14.0rc0,>=1.13.0->tensorflow) (3.0.5)
    Requirement already satisfied: h5py in /usr/local/lib/python3.6/dist-packages (from keras-applications>=1.0.6->tensorflow) (2.8.0)
    Requirement already satisfied: keras in /usr/local/lib/python3.6/dist-packages (2.2.5)
    Requirement already satisfied: keras-applications>=1.0.8 in /usr/local/lib/python3.6/dist-packages (from keras) (1.0.8)
    Requirement already satisfied: keras-preprocessing>=1.1.0 in /usr/local/lib/python3.6/dist-packages (from keras) (1.1.0)
    Requirement already satisfied: six>=1.9.0 in /usr/local/lib/python3.6/dist-packages (from keras) (1.12.0)
    Requirement already satisfied: scipy>=0.14 in /usr/local/lib/python3.6/dist-packages (from keras) (1.2.0)
    Requirement already satisfied: pyyaml in /usr/local/lib/python3.6/dist-packages (from keras) (3.13)
    Requirement already satisfied: h5py in /usr/local/lib/python3.6/dist-packages (from keras) (2.8.0)
    Requirement already satisfied: numpy>=1.9.1 in /usr/local/lib/python3.6/dist-packages (from keras) (1.16.1)
    Requirement already satisfied: ipywidgets in /usr/local/lib/python3.6/dist-packages (7.5.1)
    Requirement already satisfied: traitlets>=4.3.1 in /usr/local/lib/python3.6/dist-packages (from ipywidgets) (4.3.3)
    Requirement already satisfied: ipykernel>=4.5.1 in /usr/local/lib/python3.6/dist-packages (from ipywidgets) (4.6.1)
    Requirement already satisfied: ipython>=4.0.0; python_version >= "3.3" in /usr/local/lib/python3.6/dist-packages (from ipywidgets) (5.5.0)
    Requirement already satisfied: widgetsnbextension~=3.5.0 in /usr/local/lib/python3.6/dist-packages (from ipywidgets) (3.5.1)
    Requirement already satisfied: nbformat>=4.2.0 in /usr/local/lib/python3.6/dist-packages (from ipywidgets) (4.4.0)
    Requirement already satisfied: ipython-genutils in /usr/local/lib/python3.6/dist-packages (from traitlets>=4.3.1->ipywidgets) (0.2.0)
    Requirement already satisfied: decorator in /usr/local/lib/python3.6/dist-packages (from traitlets>=4.3.1->ipywidgets) (4.4.1)
    Requirement already satisfied: six in /usr/local/lib/python3.6/dist-packages (from traitlets>=4.3.1->ipywidgets) (1.12.0)
    Requirement already satisfied: jupyter-client in /usr/local/lib/python3.6/dist-packages (from ipykernel>=4.5.1->ipywidgets) (5.3.4)
    Requirement already satisfied: tornado>=4.0 in /usr/local/lib/python3.6/dist-packages (from ipykernel>=4.5.1->ipywidgets) (4.5.3)
    Requirement already satisfied: prompt-toolkit<2.0.0,>=1.0.4 in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (1.0.18)
    Requirement already satisfied: setuptools>=18.5 in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (42.0.2)
    Requirement already satisfied: pygments in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (2.1.3)
    Requirement already satisfied: simplegeneric>0.8 in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (0.8.1)
    Requirement already satisfied: pexpect; sys_platform != "win32" in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (4.7.0)
    Requirement already satisfied: pickleshare in /usr/local/lib/python3.6/dist-packages (from ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (0.7.5)
    Requirement already satisfied: notebook>=4.4.1 in /usr/local/lib/python3.6/dist-packages (from widgetsnbextension~=3.5.0->ipywidgets) (5.2.2)
    Requirement already satisfied: jupyter-core in /usr/local/lib/python3.6/dist-packages (from nbformat>=4.2.0->ipywidgets) (4.6.1)
    Requirement already satisfied: jsonschema!=2.5.0,>=2.4 in /usr/local/lib/python3.6/dist-packages (from nbformat>=4.2.0->ipywidgets) (2.6.0)
    Requirement already satisfied: pyzmq>=13 in /usr/local/lib/python3.6/dist-packages (from jupyter-client->ipykernel>=4.5.1->ipywidgets) (17.0.0)
    Requirement already satisfied: python-dateutil>=2.1 in /usr/local/lib/python3.6/dist-packages (from jupyter-client->ipykernel>=4.5.1->ipywidgets) (2.6.1)
    Requirement already satisfied: wcwidth in /usr/local/lib/python3.6/dist-packages (from prompt-toolkit<2.0.0,>=1.0.4->ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (0.1.7)
    Requirement already satisfied: ptyprocess>=0.5 in /usr/local/lib/python3.6/dist-packages (from pexpect; sys_platform != "win32"->ipython>=4.0.0; python_version >= "3.3"->ipywidgets) (0.6.0)
    Requirement already satisfied: jinja2 in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (2.10.3)
    Requirement already satisfied: nbconvert in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (5.6.1)
    Requirement already satisfied: terminado>=0.3.3; sys_platform != "win32" in /usr/local/lib/python3.6/dist-packages (from notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.8.3)
    Requirement already satisfied: MarkupSafe>=0.23 in /usr/local/lib/python3.6/dist-packages (from jinja2->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (1.1.1)
    Requirement already satisfied: entrypoints>=0.2.2 in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.3)
    Requirement already satisfied: testpath in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.4.4)
    Requirement already satisfied: mistune<2,>=0.8.1 in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.8.4)
    Requirement already satisfied: defusedxml in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.6.0)
    Requirement already satisfied: pandocfilters>=1.4.1 in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (1.4.2)
    Requirement already satisfied: bleach in /usr/local/lib/python3.6/dist-packages (from nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (3.1.0)
    Requirement already satisfied: webencodings in /usr/local/lib/python3.6/dist-packages (from bleach->nbconvert->notebook>=4.4.1->widgetsnbextension~=3.5.0->ipywidgets) (0.5.1)
    Enabling notebook extension jupyter-js-widgets/extension...
          - Validating: [32mOK[0m
    


```python
from auto_ml import Predictor
from auto_ml.utils import get_boston_dataset
from auto_ml.utils_models import load_ml_model

column_descriptions = {
    'Appliances': 'output',
    #'CHAS': 'categorical'
}

train_data= pd.concat([train_x,train_y],axis=1)
ml_predictor = Predictor(type_of_estimator='regressor', 
                         column_descriptions=column_descriptions
                         )

#ml_predictor.train(train_data,model_names=['DeepLearningRegressor']), r2=0.22
#ml_predictor.train(train_data,model_names=['XGBRegressor'])  #r2=0.32
#ml_predictor.train(train_data,model_names=['LGBMRegressor']) #0.497
ml_predictor.train(train_data)   #0.431

ml_predictor.score(test_x, test_y)
```

    Welcome to auto_ml! We're about to go through and make sense of your data using machine learning, and give you a production-ready pipeline to get predictions with.
    
    If you have any issues, or new feature ideas, let us know at http://auto.ml
    You are running on version 2.9.10
    Now using the model training_params that you passed in:
    {}
    After overwriting our defaults with your values, here are the final params that will be used to initialize the model:
    {'presort': False, 'learning_rate': 0.1, 'warm_start': True}
    Running basic data cleaning
    Fitting DataFrameVectorizer
    Now using the model training_params that you passed in:
    {}
    After overwriting our defaults with your values, here are the final params that will be used to initialize the model:
    {'presort': False, 'learning_rate': 0.1, 'warm_start': True}
    
    
    ********************************************************************************************
    About to fit the pipeline for the model GradientBoostingRegressor to predict Appliances
    Started at:
    2019-12-16 01:53:15
    [1] random_holdout_set_from_training_data's score is: -103.844
    [2] random_holdout_set_from_training_data's score is: -102.638
    [3] random_holdout_set_from_training_data's score is: -101.687
    [4] random_holdout_set_from_training_data's score is: -100.815
    [5] random_holdout_set_from_training_data's score is: -100.113
    [6] random_holdout_set_from_training_data's score is: -99.56
    [7] random_holdout_set_from_training_data's score is: -99.099
    [8] random_holdout_set_from_training_data's score is: -98.695
    [9] random_holdout_set_from_training_data's score is: -98.152
    [10] random_holdout_set_from_training_data's score is: -97.829
    [11] random_holdout_set_from_training_data's score is: -97.534
    [12] random_holdout_set_from_training_data's score is: -97.089

     :
     :
     :
     
    [9700] random_holdout_set_from_training_data's score is: -78.201
    [9800] random_holdout_set_from_training_data's score is: -78.196
    [9900] random_holdout_set_from_training_data's score is: -78.183
    The number of estimators that were the best for this training dataset: 9200
    The best score on the holdout set: -78.17831855991089
    Finished training the pipeline!
    Total training time:
    0:19:50
    
    
    Here are the results from our GradientBoostingRegressor
    predicting Appliances
    Calculating feature responses, for advanced analytics.
    

    /usr/local/lib/python3.6/dist-packages/sklearn/model_selection/_split.py:2179: FutureWarning: From version 0.21, test_size will always complement train_size unless both are specified.
      FutureWarning)
    

    The printed list will only contain at most the top 100 features.
    +----+----------------+--------------+---------+-------------------+-------------------+-----------+-----------+-----------+-----------+
    |    | Feature Name   |   Importance |   Delta |   FR_Decrementing |   FR_Incrementing |   FRD_abs |   FRI_abs |   FRD_MAD |   FRI_MAD |
    |----+----------------+--------------+---------+-------------------+-------------------+-----------+-----------+-----------+-----------|
    | 33 | year           |       0.0000 |  0.0000 |            0.0000 |            0.0000 |    0.0000 |    0.0000 |    0.0000 |    0.0000 |
    | 32 | quarter        |       0.0004 |  0.2454 |            0.0000 |            0.0000 |    0.0000 |    0.0000 |    0.0000 |    0.0000 |
    | 29 | month          |       0.0044 |  0.6681 |            1.8807 |           -0.5714 |    2.7292 |    0.7609 |    0.1994 |    0.0000 |
    | 31 | weekday        |       0.0096 |  1.0071 |            1.1695 |           -1.1261 |    3.9296 |    4.1527 |    1.0363 |    0.9279 |
    | 23 | Visibility     |       0.0121 |  5.9874 |            2.5418 |            1.7824 |    5.9795 |    4.3472 |    1.6654 |    1.6835 |
    | 28 | day            |       0.0149 |  4.2629 |           -1.1345 |            8.7127 |    3.9208 |   12.0951 |    1.0589 |    1.2265 |
    | 25 | rv1            |       0.0157 |  7.1657 |           56.4534 |           96.4440 |   57.2148 |   97.1504 |   47.4928 |   64.5670 |
    | 26 | rv2            |       0.0178 |  7.1657 |           65.4006 |           66.8796 |   66.3326 |   67.5132 |   60.2277 |   60.9206 |
    |  1 | T1             |       0.0188 |  0.7852 |           13.8211 |            1.1122 |   19.1298 |    9.2902 |    8.3319 |    3.7755 |
    | 17 | T9             |       0.0204 |  0.9972 |            3.8749 |           17.1198 |   12.5666 |   23.9141 |    6.9691 |   13.0813 |
    | 30 | week           |       0.0213 |  2.8238 |            6.6785 |            8.0968 |    9.4417 |   12.5097 |    3.7337 |    1.9681 |
    | 22 | Windspeed      |       0.0219 |  1.2516 |            0.3866 |            2.6343 |    7.8313 |    7.5808 |    4.5071 |    2.9881 |
    | 13 | T7             |       0.0229 |  1.0471 |           19.2027 |            2.7276 |   24.3899 |   11.8721 |    6.9290 |    5.4307 |
    | 21 | RH_out         |       0.0230 |  7.5409 |            4.5123 |            5.7414 |   10.1780 |   10.9347 |    4.3237 |    4.2214 |
    |  7 | T4             |       0.0234 |  1.0108 |            4.8105 |            3.0581 |   11.4415 |   11.6706 |    6.1204 |    5.2510 |
    |  3 | T2             |       0.0235 |  1.0783 |           14.0835 |            0.8992 |   19.7000 |   11.4377 |    6.4855 |    5.7581 |
    | 19 | T_out          |       0.0239 |  2.6511 |            6.8862 |           -4.4735 |   18.3031 |   10.5233 |   10.0484 |    7.6458 |
    |  9 | T5             |       0.0240 |  0.9025 |           13.1377 |            3.0813 |   19.8936 |   10.4180 |    7.6748 |    4.9818 |
    | 18 | RH_9           |       0.0264 |  2.0675 |            5.6641 |            0.8102 |   13.4686 |   13.8479 |    8.2520 |    8.7596 |
    | 24 | Tdewpoint      |       0.0299 |  2.0609 |            2.5114 |            5.8576 |   15.2535 |   15.9113 |    7.3289 |    8.4186 |
    |  0 | lights         |       0.0302 |  3.9307 |            0.0000 |            0.0000 |    0.0000 |    0.0000 |    0.0000 |    0.0000 |
    | 12 | RH_6           |       0.0324 | 15.9312 |            6.9685 |           23.3361 |   14.6424 |   29.8704 |    5.8741 |    9.1803 |
    | 16 | RH_8           |       0.0334 |  2.6452 |            5.3547 |            1.5003 |   18.7197 |   13.6153 |    9.8327 |    7.8713 |
    | 11 | T6             |       0.0343 |  3.0657 |            0.7589 |           13.1492 |   20.2709 |   21.9876 |   11.7236 |   10.2991 |
    | 14 | RH_7           |       0.0353 |  2.5387 |           12.6561 |            2.0688 |   18.6964 |   12.7747 |    8.9771 |    6.7723 |
    |  8 | RH_4           |       0.0372 |  2.1545 |           14.7021 |           11.6144 |   21.2902 |   19.7052 |    7.5457 |    6.6670 |
    | 15 | T8             |       0.0375 |  0.9570 |           -3.6613 |           10.3051 |   15.2607 |   14.0218 |    9.4964 |    8.0907 |
    | 10 | RH_5           |       0.0381 |  4.6173 |           34.8231 |            0.3044 |   39.5146 |    8.5136 |   10.9724 |    4.7449 |
    |  2 | RH_1           |       0.0390 |  1.9196 |            0.8117 |           12.8458 |   17.0123 |   18.3431 |    9.3164 |   10.8043 |
    |  4 | RH_2           |       0.0426 |  1.9942 |           14.9731 |            1.1853 |   17.7433 |   15.2093 |    8.9040 |    8.3585 |
    |  6 | RH_3           |       0.0441 |  1.5894 |            8.8671 |           16.8765 |   23.3520 |   19.1881 |    9.8736 |   11.1776 |
    | 20 | Press_mm_hg    |       0.0484 |  3.6523 |            7.5979 |           12.3749 |   18.9428 |   23.5722 |    7.7871 |    8.6312 |
    |  5 | T3             |       0.0491 |  0.9847 |           19.9205 |            5.5366 |   27.4795 |   16.7018 |   10.3640 |   10.0421 |
    | 27 | hour           |       0.1439 |  3.3978 |            2.3847 |           -1.8274 |   20.2401 |   21.1750 |   11.4552 |   14.4227 |
    +----+----------------+--------------+---------+-------------------+-------------------+-----------+-----------+-----------+-----------+
    
    
    *******
    Legend:
    Importance = Feature Importance
         Explanation: A weighted measure of how much of the variance the model is able to explain is due to this column
    FR_delta = Feature Response Delta Amount
         Explanation: Amount this column was incremented or decremented by to calculate the feature reponses
    FR_Decrementing = Feature Response From Decrementing Values In This Column By One FR_delta
         Explanation: Represents how much the predicted output values respond to subtracting one FR_delta amount from every value in this column
    FR_Incrementing = Feature Response From Incrementing Values In This Column By One FR_delta
         Explanation: Represents how much the predicted output values respond to adding one FR_delta amount to every value in this column
    FRD_MAD = Feature Response From Decrementing- Median Absolute Delta
         Explanation: Takes the absolute value of all changes in predictions, then takes the median of those. Useful for seeing if decrementing this feature provokes strong changes that are both positive and negative
    FRI_MAD = Feature Response From Incrementing- Median Absolute Delta
         Explanation: Takes the absolute value of all changes in predictions, then takes the median of those. Useful for seeing if incrementing this feature provokes strong changes that are both positive and negative
    FRD_abs = Feature Response From Decrementing Avg Absolute Change
         Explanation: What is the average absolute change in predicted output values to subtracting one FR_delta amount to every value in this column. Useful for seeing if output is sensitive to a feature, but not in a uniformly positive or negative way
    FRI_abs = Feature Response From Incrementing Avg Absolute Change
         Explanation: What is the average absolute change in predicted output values to adding one FR_delta amount to every value in this column. Useful for seeing if output is sensitive to a feature, but not in a uniformly positive or negative way
    *******
    
    None
    
    
    ***********************************************
    Advanced scoring metrics for the trained regression model on this particular dataset:
    
    Here is the overall RMSE for these predictions:
    78.12730544452076
    
    Here is the average of the predictions:
    99.33728042751186
    
    Here is the average actual value on this validation set:
    98.55429826042898
    
    Here is the median prediction:
    71.29957584827399
    
    Here is the median actual value:
    60.0
    
    Here is the mean absolute error:
    39.930776664246636
    
    Here is the median absolute error (robust to outliers):
    17.292385522544976
    
    Here is the explained variance:
    0.43110768950362766
    
    Here is the R-squared value:
    0.4310505453591652
    Count of positive differences (prediction > actual):
    3353
    Count of negative differences:
    2568
    Average positive difference:
    35.947832693155526
    Average negative difference:
    -45.131248290052
    
    
    ***********************************************
    
    
    




    -78.12730544452076



#### autoxgboost
source | documentation | R | Optimization: Bayesian Optimization | -

autoxgboost aims to find an optimal xgboost model automatically using the machine learning framework mlr and the bayesian optimization framework mlrMBO.

Autoxgboost is different from most frameworks on this page in that it does not search over multiple learning algorithms. Instead, it restricts itself to finding a good hyperparameter configuration for xgboost. The exception to this is a preprocessing step for categorical variables, where the specific encoding strategy to use is tuned as well.


```r
%%R
install.packages("devtools")
install.packages("usethis")
install.packages("remotes")
install.packages("githubinstall")
install.packages("ghit")

#remotes::install_github("edwardcooper/automl")
library("devtools")
library(githubinstall)
# library("ghit")
#devtools::install_github("ja-thomas/autoxgboost")
remotes::install_github("ja-thomas/autoxgboost")
#ghit::install_github("cloudyr/ghit")
# githubinstall("autoxgboost")
```


```r
%%R

library(autoxgboost)


#install.packages("devtools")
#install.packages("usethis")
#install.packages("remotes")
#remotes::install_github("edwardcooper/automl")
library("devtools")
pacman::p_load(lime,DALEX,forcats,ALEPlot,pdp,iBreakDown,localModel,breakDown,
              xfun,clipr,clipr,sf,spdep,lubrridate)
library(tidyverse)
#devtools::install_github("ja-thomas/autoxgboost")
#remotes::install_github("ja-thomas/autoxgboost")
file= '/Users/energydata_complete.csv'
energy_data= readr::read_csv(file)

energy_data$hour  = hour(energy_data$date)
energy_data$day =   wday(energy_data$date)
energy_data$month = month(energy_data$date)
energy_data$week =  week(energy_data$date)
#energy_data$weekday= weekdays(energy_data$date)
energy_data$quarter=quarter(energy_data$date)
energy_data$year =   year(energy_data$date)




energydata  =
  energy_data %>%select(-date)




library(rsample)

data_split <- initial_split(energydata, strata = "Appliances", prop = 0.70)

energydata_train <- training(data_split)
energydata_test  <- testing(data_split)

reg_task <- makeRegrTask(data = energydata_train, target = "Appliances")
set.seed(1234)
#system.time(reg_auto <- autoxgboost(reg_task))
# saveRDS(reg_auto, file = "D:/SDIautoxgboost_80.rds")


#data.task = makeRegrTask(data = iris, target = "Appliances")
ctrl = makeMBOControl()
ctrl = setMBOControlTermination(ctrl, iters = 2L) #Speed up Tuning by only doing 1 iteration
res = autoxgboost(reg_task, control = ctrl, tune.threshold = FALSE)
print(res)

pred=predict(res,data.frame(energydata_test))

library(yardstick)pred$data


print(yardstick::rmse(pred$data,truth,response))
print(yardstick::rsq(pred$data,truth,response))
```

    /usr/local/lib/python3.6/dist-packages/rpy2/rinterface/__init__.py:146: RRuntimeWarning: Installing package into ‘/usr/local/lib/R/site-library’
    (as ‘lib’ is unspecified)
    
      warnings.warn(x, RRuntimeWarning)
    

    
  