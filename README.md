# Clinical-Data-Analysis
A data challenge which deals with fictional patient data aimed to find some interesting stats about the patients



```python
import os
os.getcwd()
```




    'C:\\Users\\dhira'




```python
import pandas as pd
import numpy as np
```


```python
xls = pd.ExcelFile("F:/GIT/Test_DataCore.xlsx")

```


```python
coreData = pd.read_excel(xls, 'Test_DataCore')
vitalData = pd.read_excel(xls, 'Test_DataCore_VitalStats')
```


```python
coreData.shape
```




    (10000, 13)




```python
vitalData.shape
```




    (98, 2)




```python
coreData.head(10)
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
      <th>ID</th>
      <th>VISIT_ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>CLINIC_CODE</th>
      <th>BIRTHDATE</th>
      <th>SEX</th>
      <th>RACE</th>
      <th>DATE_OF_DEATH</th>
      <th>VISIT_ACTIVATION_DATE_TIME</th>
      <th>DISCHARGE_TYPE</th>
      <th>FACILITY</th>
      <th>VISIT_STATUS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22300992</td>
      <td>1152389109</td>
      <td>25NOV14:00:31:00</td>
      <td>25NOV14:06:10:00</td>
      <td>0.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25NOV14:00:31:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22300992</td>
      <td>1155584209</td>
      <td>16DEC14:15:00:00</td>
      <td>18DEC14:14:25:00</td>
      <td>NaN</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>16DEC14:01:21:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>2</th>
      <td>22300992</td>
      <td>1156688486</td>
      <td>22DEC14:14:44:00</td>
      <td>22DEC14:15:53:00</td>
      <td>60.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>22DEC14:14:44:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22300992</td>
      <td>1156878696</td>
      <td>23DEC14:13:51:00</td>
      <td>24DEC14:08:18:00</td>
      <td>860.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>23DEC14:13:51:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>4</th>
      <td>22300992</td>
      <td>1157298654</td>
      <td>28DEC14:03:49:00</td>
      <td>28DEC14:10:14:00</td>
      <td>884.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>28DEC14:03:49:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>5</th>
      <td>22300992</td>
      <td>1163018329</td>
      <td>04FEB15:13:32:00</td>
      <td>09FEB15:13:39:00</td>
      <td>860.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>04FEB15:13:32:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>6</th>
      <td>22300992</td>
      <td>1165130415</td>
      <td>18FEB15:14:30:00</td>
      <td>20FEB15:12:18:00</td>
      <td>860.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>18FEB15:14:30:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>7</th>
      <td>22300992</td>
      <td>1166299794</td>
      <td>25FEB15:15:26:00</td>
      <td>25FEB15:23:59:00</td>
      <td>60.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25FEB15:15:26:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>8</th>
      <td>22300992</td>
      <td>1170893580</td>
      <td>25MAR15:12:22:00</td>
      <td>31MAR15:14:43:00</td>
      <td>860.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25MAR15:12:22:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>9</th>
      <td>22300992</td>
      <td>1170899994</td>
      <td>25MAR15:12:49:00</td>
      <td>25MAR15:16:35:00</td>
      <td>0.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25MAR15:12:49:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
  </tbody>
</table>
</div>




```python
coreData['ID'].nunique()
```




    80




```python
vitalData['ID'].nunique()
```




    98




```python
frames = [coreData,vitalData]
combineData = pd.concat(frames, sort = False)
combineData.shape
```




    (10098, 13)




```python
combineData['ID'].nunique()
```




    169




```python
demographicData = combineData.drop_duplicates(subset='ID', keep='first', inplace=False)
```


```python
demographicData.shape
```




    (169, 13)




```python
demographicData.head()
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
      <th>ID</th>
      <th>VISIT_ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>CLINIC_CODE</th>
      <th>BIRTHDATE</th>
      <th>SEX</th>
      <th>RACE</th>
      <th>DATE_OF_DEATH</th>
      <th>VISIT_ACTIVATION_DATE_TIME</th>
      <th>DISCHARGE_TYPE</th>
      <th>FACILITY</th>
      <th>VISIT_STATUS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22300992</td>
      <td>1.152389e+09</td>
      <td>25NOV14:00:31:00</td>
      <td>25NOV14:06:10:00</td>
      <td>0.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25NOV14:00:31:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>33</th>
      <td>23665824</td>
      <td>4.335787e+08</td>
      <td>10MAR03:02:08:00</td>
      <td>13MAR03:14:30:00</td>
      <td>0.0</td>
      <td>14APR52:00:03:05</td>
      <td>Female</td>
      <td>Hispanic</td>
      <td>NaN</td>
      <td>09MAR03:13:48:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>93</th>
      <td>24810016</td>
      <td>3.871856e+08</td>
      <td>09APR02:00:01:00</td>
      <td>10APR02:13:41:00</td>
      <td>230.0</td>
      <td>04DEC57:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>09APR02:00:01:00</td>
      <td>Routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>273</th>
      <td>26523392</td>
      <td>3.305710e+08</td>
      <td>26MAR01:00:01:00</td>
      <td>26MAR01:23:59:00</td>
      <td>269.0</td>
      <td>31DEC67:00:03:05</td>
      <td>Male</td>
      <td>Hispanic</td>
      <td>NaN</td>
      <td>26MAR01:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>299</th>
      <td>27421632</td>
      <td>6.117502e+08</td>
      <td>15JUL06:23:04:00</td>
      <td>16JUL06:08:49:00</td>
      <td>4.0</td>
      <td>05APR62:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>15JUL06:23:04:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
  </tbody>
</table>
</div>




```python
import seaborn as sns
sns.set(style = 'darkgrid')
sns.set(rc={'figure.figsize':(11.7,8.27)})
sns.countplot(x='SEX', hue = 'RACE',data=demographicData , palette = "Set1" )

```




    <matplotlib.axes._subplots.AxesSubplot at 0x20260d14da0>




```python
import plotly 
plotly.tools.set_credentials_file(username='dhirajtripathi93', api_key='F4T1zMaGILlUA0pmuSPN')
```


```python
combineData.head()
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
      <th>ID</th>
      <th>VISIT_ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>CLINIC_CODE</th>
      <th>BIRTHDATE</th>
      <th>SEX</th>
      <th>RACE</th>
      <th>DATE_OF_DEATH</th>
      <th>VISIT_ACTIVATION_DATE_TIME</th>
      <th>DISCHARGE_TYPE</th>
      <th>FACILITY</th>
      <th>VISIT_STATUS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>22300992</td>
      <td>1.152389e+09</td>
      <td>25NOV14:00:31:00</td>
      <td>25NOV14:06:10:00</td>
      <td>0.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>25NOV14:00:31:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22300992</td>
      <td>1.155584e+09</td>
      <td>16DEC14:15:00:00</td>
      <td>18DEC14:14:25:00</td>
      <td>NaN</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>16DEC14:01:21:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>2</th>
      <td>22300992</td>
      <td>1.156688e+09</td>
      <td>22DEC14:14:44:00</td>
      <td>22DEC14:15:53:00</td>
      <td>60.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>22DEC14:14:44:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22300992</td>
      <td>1.156879e+09</td>
      <td>23DEC14:13:51:00</td>
      <td>24DEC14:08:18:00</td>
      <td>860.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>23DEC14:13:51:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>4</th>
      <td>22300992</td>
      <td>1.157299e+09</td>
      <td>28DEC14:03:49:00</td>
      <td>28DEC14:10:14:00</td>
      <td>884.0</td>
      <td>24MAY51:00:03:05</td>
      <td>Male</td>
      <td>Black or African American</td>
      <td>NaN</td>
      <td>28DEC14:03:49:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
  </tbody>
</table>
</div>




```python
combineData.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 10098 entries, 0 to 97
    Data columns (total 13 columns):
    ID                            10098 non-null int64
    VISIT_ID                      10000 non-null float64
    ADMISSION_DATE_TIME           10000 non-null object
    DISCHARGE_DATE_TIME           9792 non-null object
    CLINIC_CODE                   9567 non-null float64
    BIRTHDATE                     10000 non-null object
    SEX                           10000 non-null object
    RACE                          10000 non-null object
    DATE_OF_DEATH                 430 non-null object
    VISIT_ACTIVATION_DATE_TIME    10000 non-null object
    DISCHARGE_TYPE                9745 non-null object
    FACILITY                      10000 non-null object
    VISIT_STATUS                  10000 non-null object
    dtypes: float64(2), int64(1), object(10)
    memory usage: 1.1+ MB
    


```python
deadpatientData = combineData.dropna(axis=0, how='any', thresh=None, subset=['DATE_OF_DEATH'], inplace=False)
deadpatientData.shape

```




    (430, 13)




```python
deadpatientData.head(10)
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
      <th>ID</th>
      <th>VISIT_ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>CLINIC_CODE</th>
      <th>BIRTHDATE</th>
      <th>SEX</th>
      <th>RACE</th>
      <th>DATE_OF_DEATH</th>
      <th>VISIT_ACTIVATION_DATE_TIME</th>
      <th>DISCHARGE_TYPE</th>
      <th>FACILITY</th>
      <th>VISIT_STATUS</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>393</th>
      <td>28438144</td>
      <td>288391247.0</td>
      <td>13JUN00:16:35:00</td>
      <td>01JUL00:23:04:00</td>
      <td>656.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>13JUN00:14:21:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>394</th>
      <td>28438144</td>
      <td>317139774.0</td>
      <td>22DEC00:15:48:00</td>
      <td>29DEC00:19:59:00</td>
      <td>656.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>22DEC00:10:13:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>395</th>
      <td>28438144</td>
      <td>407925810.0</td>
      <td>03SEP02:00:01:00</td>
      <td>03SEP02:23:59:00</td>
      <td>7.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>03SEP02:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>396</th>
      <td>28438144</td>
      <td>432931710.0</td>
      <td>04MAR03:00:01:00</td>
      <td>07MAR03:23:59:00</td>
      <td>7.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>04MAR03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>discharged</td>
    </tr>
    <tr>
      <th>397</th>
      <td>28438144</td>
      <td>463914971.0</td>
      <td>09OCT03:12:33:00</td>
      <td>14OCT03:21:47:00</td>
      <td>7.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>09OCT03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>398</th>
      <td>28438144</td>
      <td>466317161.0</td>
      <td>20OCT03:00:01:00</td>
      <td>27OCT03:15:51:00</td>
      <td>590.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>20OCT03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>399</th>
      <td>28438144</td>
      <td>466497883.0</td>
      <td>28OCT03:00:01:00</td>
      <td>28OCT03:18:04:00</td>
      <td>7.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>28OCT03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>400</th>
      <td>28438144</td>
      <td>467186068.0</td>
      <td>01NOV03:21:24:00</td>
      <td>07NOV03:08:50:00</td>
      <td>656.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>01NOV03:21:24:00</td>
      <td>discharge (routine)</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>401</th>
      <td>28438144</td>
      <td>471168456.0</td>
      <td>02DEC03:00:01:00</td>
      <td>03DEC03:09:25:00</td>
      <td>7.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>02DEC03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
    <tr>
      <th>402</th>
      <td>28438144</td>
      <td>474220350.0</td>
      <td>12DEC03:00:01:00</td>
      <td>23DEC03:11:28:00</td>
      <td>590.0</td>
      <td>21MAR88:00:03:05</td>
      <td>Female</td>
      <td>Black or African American</td>
      <td>2015-07-02 00:00:00.000</td>
      <td>12DEC03:00:01:00</td>
      <td>routine</td>
      <td>H + H Facility</td>
      <td>closed discharged</td>
    </tr>
  </tbody>
</table>
</div>




```python
deadpatientData['ID'].nunique()

```




    98




```python
deadpatientData.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 430 entries, 393 to 97
    Data columns (total 13 columns):
    ID                            430 non-null int64
    VISIT_ID                      332 non-null float64
    ADMISSION_DATE_TIME           332 non-null object
    DISCHARGE_DATE_TIME           325 non-null object
    CLINIC_CODE                   328 non-null float64
    BIRTHDATE                     332 non-null object
    SEX                           332 non-null object
    RACE                          332 non-null object
    DATE_OF_DEATH                 430 non-null object
    VISIT_ACTIVATION_DATE_TIME    332 non-null object
    DISCHARGE_TYPE                325 non-null object
    FACILITY                      332 non-null object
    VISIT_STATUS                  332 non-null object
    dtypes: float64(2), int64(1), object(10)
    memory usage: 47.0+ KB
    


```python
newData = deadpatientData[['ID','ADMISSION_DATE_TIME','DISCHARGE_DATE_TIME','DATE_OF_DEATH',]]
newData.head(10)

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
      <th>ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>DATE_OF_DEATH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>393</th>
      <td>28438144</td>
      <td>13JUN00:16:35:00</td>
      <td>01JUL00:23:04:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>394</th>
      <td>28438144</td>
      <td>22DEC00:15:48:00</td>
      <td>29DEC00:19:59:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>395</th>
      <td>28438144</td>
      <td>03SEP02:00:01:00</td>
      <td>03SEP02:23:59:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>396</th>
      <td>28438144</td>
      <td>04MAR03:00:01:00</td>
      <td>07MAR03:23:59:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>397</th>
      <td>28438144</td>
      <td>09OCT03:12:33:00</td>
      <td>14OCT03:21:47:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>398</th>
      <td>28438144</td>
      <td>20OCT03:00:01:00</td>
      <td>27OCT03:15:51:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>399</th>
      <td>28438144</td>
      <td>28OCT03:00:01:00</td>
      <td>28OCT03:18:04:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>400</th>
      <td>28438144</td>
      <td>01NOV03:21:24:00</td>
      <td>07NOV03:08:50:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>401</th>
      <td>28438144</td>
      <td>02DEC03:00:01:00</td>
      <td>03DEC03:09:25:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>402</th>
      <td>28438144</td>
      <td>12DEC03:00:01:00</td>
      <td>23DEC03:11:28:00</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
  </tbody>
</table>
</div>




```python
newData['ADMISSION_DATE_TIME'] = pd.to_datetime(newData['ADMISSION_DATE_TIME'], format='%d%b%y:%H:%M:%S')
newData['DISCHARGE_DATE_TIME'] = pd.to_datetime(newData['DISCHARGE_DATE_TIME'], format='%d%b%y:%H:%M:%S')


```

    C:\Users\dhira\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    C:\Users\dhira\Anaconda3\lib\site-packages\ipykernel_launcher.py:2: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    


```python
newData['ADMISSION_DATE_TIME'] = newData['ADMISSION_DATE_TIME'].dt.date
newData['DISCHARGE_DATE_TIME'] = newData['DISCHARGE_DATE_TIME'].dt.date

```

    C:\Users\dhira\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    C:\Users\dhira\Anaconda3\lib\site-packages\ipykernel_launcher.py:2: SettingWithCopyWarning:
    
    
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
    
    


```python
newData
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
      <th>ID</th>
      <th>ADMISSION_DATE_TIME</th>
      <th>DISCHARGE_DATE_TIME</th>
      <th>DATE_OF_DEATH</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>393</th>
      <td>28438144</td>
      <td>2000-06-13</td>
      <td>2000-07-01</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>394</th>
      <td>28438144</td>
      <td>2000-12-22</td>
      <td>2000-12-29</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>395</th>
      <td>28438144</td>
      <td>2002-09-03</td>
      <td>2002-09-03</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>396</th>
      <td>28438144</td>
      <td>2003-03-04</td>
      <td>2003-03-07</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>397</th>
      <td>28438144</td>
      <td>2003-10-09</td>
      <td>2003-10-14</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>398</th>
      <td>28438144</td>
      <td>2003-10-20</td>
      <td>2003-10-27</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>399</th>
      <td>28438144</td>
      <td>2003-10-28</td>
      <td>2003-10-28</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>400</th>
      <td>28438144</td>
      <td>2003-11-01</td>
      <td>2003-11-07</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>401</th>
      <td>28438144</td>
      <td>2003-12-02</td>
      <td>2003-12-03</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>402</th>
      <td>28438144</td>
      <td>2003-12-12</td>
      <td>2003-12-23</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>403</th>
      <td>28438144</td>
      <td>2003-12-17</td>
      <td>2004-01-07</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>404</th>
      <td>28438144</td>
      <td>2004-02-13</td>
      <td>NaT</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>405</th>
      <td>28438144</td>
      <td>2004-02-12</td>
      <td>2004-02-20</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>406</th>
      <td>28438144</td>
      <td>2004-02-25</td>
      <td>2004-02-26</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>407</th>
      <td>28438144</td>
      <td>2004-04-26</td>
      <td>2004-05-01</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>408</th>
      <td>28438144</td>
      <td>2004-05-05</td>
      <td>2004-05-07</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>409</th>
      <td>28438144</td>
      <td>2004-05-12</td>
      <td>2004-05-13</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>410</th>
      <td>28438144</td>
      <td>2004-07-10</td>
      <td>2004-07-10</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>411</th>
      <td>28438144</td>
      <td>2004-09-21</td>
      <td>2004-09-22</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>412</th>
      <td>28438144</td>
      <td>2004-12-01</td>
      <td>2004-12-01</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>413</th>
      <td>28438144</td>
      <td>2004-12-08</td>
      <td>2004-12-08</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>414</th>
      <td>28438144</td>
      <td>2005-04-07</td>
      <td>2005-04-07</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>415</th>
      <td>28438144</td>
      <td>2005-04-30</td>
      <td>2005-05-02</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>416</th>
      <td>28438144</td>
      <td>2005-05-02</td>
      <td>2005-05-03</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>417</th>
      <td>28438144</td>
      <td>2005-10-25</td>
      <td>2005-10-25</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>418</th>
      <td>28438144</td>
      <td>2005-10-26</td>
      <td>2005-10-26</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>419</th>
      <td>28438144</td>
      <td>2005-11-02</td>
      <td>2005-11-04</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>420</th>
      <td>28438144</td>
      <td>2006-01-26</td>
      <td>2006-01-26</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>421</th>
      <td>28438144</td>
      <td>2006-04-03</td>
      <td>2006-04-04</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>422</th>
      <td>28438144</td>
      <td>2006-11-06</td>
      <td>2006-11-08</td>
      <td>2015-07-02 00:00:00.000</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>68</th>
      <td>64368864</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>11MAY2015</td>
    </tr>
    <tr>
      <th>69</th>
      <td>64906912</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>20SEP2015</td>
    </tr>
    <tr>
      <th>70</th>
      <td>65664032</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>29JUN2015</td>
    </tr>
    <tr>
      <th>71</th>
      <td>66123008</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>14JAN2015</td>
    </tr>
    <tr>
      <th>72</th>
      <td>66242400</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>09JUN2015</td>
    </tr>
    <tr>
      <th>73</th>
      <td>67152960</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>27OCT2015</td>
    </tr>
    <tr>
      <th>74</th>
      <td>67378976</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>01NOV2015</td>
    </tr>
    <tr>
      <th>75</th>
      <td>69495776</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>30OCT2015</td>
    </tr>
    <tr>
      <th>76</th>
      <td>71635200</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>02JUL2015</td>
    </tr>
    <tr>
      <th>77</th>
      <td>71929984</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>08JUN2015</td>
    </tr>
    <tr>
      <th>78</th>
      <td>71936032</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>21JUN2015</td>
    </tr>
    <tr>
      <th>79</th>
      <td>72760128</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>16SEP2015</td>
    </tr>
    <tr>
      <th>80</th>
      <td>75103616</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>16MAY2015</td>
    </tr>
    <tr>
      <th>81</th>
      <td>76259232</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>18JUN2015</td>
    </tr>
    <tr>
      <th>82</th>
      <td>76640704</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>28JUL2015</td>
    </tr>
    <tr>
      <th>83</th>
      <td>76682144</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>18OCT2015</td>
    </tr>
    <tr>
      <th>84</th>
      <td>77370048</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>10SEP2015</td>
    </tr>
    <tr>
      <th>85</th>
      <td>77373408</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>10AUG2015</td>
    </tr>
    <tr>
      <th>86</th>
      <td>80459680</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>01NOV2015</td>
    </tr>
    <tr>
      <th>87</th>
      <td>82681088</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>21OCT2015</td>
    </tr>
    <tr>
      <th>88</th>
      <td>83874784</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>20MAY2015</td>
    </tr>
    <tr>
      <th>89</th>
      <td>83918912</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>28OCT2015</td>
    </tr>
    <tr>
      <th>90</th>
      <td>84279776</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>22OCT2015</td>
    </tr>
    <tr>
      <th>91</th>
      <td>84284032</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>16OCT2015</td>
    </tr>
    <tr>
      <th>92</th>
      <td>84789600</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>25OCT2015</td>
    </tr>
    <tr>
      <th>93</th>
      <td>85254400</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>08OCT2014</td>
    </tr>
    <tr>
      <th>94</th>
      <td>85384320</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>18DEC2014</td>
    </tr>
    <tr>
      <th>95</th>
      <td>85620416</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>20OCT2015</td>
    </tr>
    <tr>
      <th>96</th>
      <td>86567936</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>24SEP2015</td>
    </tr>
    <tr>
      <th>97</th>
      <td>86708832</td>
      <td>NaT</td>
      <td>NaT</td>
      <td>09FEB2005</td>
    </tr>
  </tbody>
</table>
<p>430 rows × 4 columns</p>
</div>




```python
newData.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 430 entries, 393 to 97
    Data columns (total 4 columns):
    ID                     430 non-null int64
    ADMISSION_DATE_TIME    332 non-null object
    DISCHARGE_DATE_TIME    325 non-null object
    DATE_OF_DEATH          430 non-null object
    dtypes: int64(1), object(3)
    memory usage: 16.8+ KB
    
