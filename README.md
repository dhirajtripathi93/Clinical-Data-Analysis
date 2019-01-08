# Clinical-Data-Analysis
A data challenge which deals with fictional patient data aimed to find some interesting stats about the patients

 

## Submitted by - Dhiraj Tripathi
### email: dhiraj.tripathi@rutgers.edu | contact: 551-358-9117




In this data challenge, we are given a dataset which contains two sheets in xlsx format. There are different columns in these sheets, which describe many properties regarding the fictional patient data. Our goal is to narrow down the search programmatically and explore the data to get some interesting findings. By the end of this python notebook, we should be able to answer most of the questions asked in the problem statement.

Let's start our challenge by importing some necessary packages. The code written below will get us the current working directory in which this Ipython notebook is going to be saved.
In [1]:


import os
os.getcwd()


Out[1]:
'C:\\Users\\dhira'

Pandas and Numpy are the most used packages in python to perform a variety of basic to complex mathematical computation
In [2]:


import pandas as pd
import numpy as np




Reading the Excel File
In [3]:


completeFile = pd.ExcelFile("F:/GIT/Test_DataCore.xlsx")




Reading the sheets independently to create seperate DataFrames
In [4]:


Sheet1 = pd.read_excel(completeFile, 'Test_DataCore')
Sheet2 = pd.read_excel(completeFile, 'Test_DataCore_VitalStats')




Check Dimensions of the Sheets to get information on number of rows and columns
In [5]:


Sheet1.shape


Out[5]:
(10000, 13)
In [6]:


Sheet2.shape


Out[6]:
(98, 2)

The info command in python gives us information on the structure of the data. An important thing to note here is that all the date columns are not of the dtype DateTime. We need to convert these columns in order to perform data operations without any errors
In [7]:


Sheet1.info()



<class 'pandas.core.frame.DataFrame'>
RangeIndex: 10000 entries, 0 to 9999
Data columns (total 13 columns):
ID                            10000 non-null int64
VISIT_ID                      10000 non-null int64
ADMISSION_DATE_TIME           10000 non-null object
DISCHARGE_DATE_TIME           9792 non-null object
CLINIC_CODE                   9567 non-null float64
BIRTHDATE                     10000 non-null object
SEX                           10000 non-null object
RACE                          10000 non-null object
DATE_OF_DEATH                 332 non-null object
VISIT_ACTIVATION_DATE_TIME    10000 non-null object
DISCHARGE_TYPE                9745 non-null object
FACILITY                      10000 non-null object
VISIT_STATUS                  10000 non-null object
dtypes: float64(1), int64(2), object(10)
memory usage: 1015.7+ KB
In [8]:


Sheet2.info()



<class 'pandas.core.frame.DataFrame'>
RangeIndex: 98 entries, 0 to 97
Data columns (total 2 columns):
ID               98 non-null int64
DATE_OF_DEATH    98 non-null object
dtypes: int64(1), object(1)
memory usage: 1.6+ KB

Data Cleaning

The biggest challenge in this dataset is that the data was not at all consistent especially the date columns. Some of the date columns even contain microseconds and hence converting them into a consistent format was necessary.
In [9]:



Sheet1['ADMISSION_DATE_TIME'] = pd.to_datetime(Sheet1['ADMISSION_DATE_TIME'],format='%d%b%y:%H:%M:%S').dt.normalize()
Sheet1['DISCHARGE_DATE_TIME'] = pd.to_datetime(Sheet1['DISCHARGE_DATE_TIME'],format='%d%b%y:%H:%M:%S').dt.normalize()
Sheet1['BIRTHDATE'] = pd.to_datetime(Sheet1['BIRTHDATE'],format='%d%b%y:%H:%M:%S').dt.normalize()
Sheet1['DATE_OF_DEATH'] = pd.to_datetime(Sheet1['DATE_OF_DEATH'],format = '%Y-%m-%d %H:%M:%S.%f').dt.normalize()
Sheet1['VISIT_ACTIVATION_DATE_TIME'] = pd.to_datetime(Sheet1['VISIT_ACTIVATION_DATE_TIME'],format='%d%b%y:%H:%M:%S').dt.normalize()
​




Check the information of the sheets again and you will notice that the dtype of the date columns are now changed to DateTime64[ns]. Here [ns] represents nano-seconds. So, even if the date column has micro-seconds, we should be able to tackle that.
In [10]:


Sheet1.info()



<class 'pandas.core.frame.DataFrame'>
RangeIndex: 10000 entries, 0 to 9999
Data columns (total 13 columns):
ID                            10000 non-null int64
VISIT_ID                      10000 non-null int64
ADMISSION_DATE_TIME           10000 non-null datetime64[ns]
DISCHARGE_DATE_TIME           9792 non-null datetime64[ns]
CLINIC_CODE                   9567 non-null float64
BIRTHDATE                     10000 non-null datetime64[ns]
SEX                           10000 non-null object
RACE                          10000 non-null object
DATE_OF_DEATH                 332 non-null datetime64[ns]
VISIT_ACTIVATION_DATE_TIME    10000 non-null datetime64[ns]
DISCHARGE_TYPE                9745 non-null object
FACILITY                      10000 non-null object
VISIT_STATUS                  10000 non-null object
dtypes: datetime64[ns](5), float64(1), int64(2), object(5)
memory usage: 1015.7+ KB

An important thing to notice is the clean format of all the Date columns in the sheets.
In [11]:


Sheet1.head()


Out[11]:

ID
VISIT_ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
CLINIC_CODE
BIRTHDATE
SEX
RACE
DATE_OF_DEATH
VISIT_ACTIVATION_DATE_TIME
DISCHARGE_TYPE
FACILITY
VISIT_STATUS
0
22300992
1152389109
2014-11-25
2014-11-25
0.0
2051-05-24
Male
Black or African American
NaT
2014-11-25
discharge (routine)
H + H Facility
discharged
1
22300992
1155584209
2014-12-16
2014-12-18
NaN
2051-05-24
Male
Black or African American
NaT
2014-12-16
discharge (routine)
H + H Facility
discharged
2
22300992
1156688486
2014-12-22
2014-12-22
60.0
2051-05-24
Male
Black or African American
NaT
2014-12-22
routine
H + H Facility
closed discharged
3
22300992
1156878696
2014-12-23
2014-12-24
860.0
2051-05-24
Male
Black or African American
NaT
2014-12-23
routine
H + H Facility
closed discharged
4
22300992
1157298654
2014-12-28
2014-12-28
884.0
2051-05-24
Male
Black or African American
NaT
2014-12-28
discharge (routine)
H + H Facility
closed discharged
In [12]:


Sheet2['DATE_OF_DEATH'] = pd.to_datetime(Sheet2['DATE_OF_DEATH']).dt.normalize()
​



In [13]:


Sheet2.head()


Out[13]:

ID
DATE_OF_DEATH
0
819392
2015-07-10
1
15150240
2015-11-15
2
28438144
2015-07-02
3
28653184
2015-10-30
4
30041536
2015-02-25

Data Munging
We need to merge the dataframes and combine it into one. Both the dataframes contain redundant information on patient IDs and the date of death information for them, along with some extra variables.
In [14]:


combineData = Sheet1.append(Sheet2,sort=False)
combineData.shape


Out[14]:
(10098, 13)
In [15]:


combineData.head()


Out[15]:

ID
VISIT_ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
CLINIC_CODE
BIRTHDATE
SEX
RACE
DATE_OF_DEATH
VISIT_ACTIVATION_DATE_TIME
DISCHARGE_TYPE
FACILITY
VISIT_STATUS
0
22300992
1.152389e+09
2014-11-25
2014-11-25
0.0
2051-05-24
Male
Black or African American
NaT
2014-11-25
discharge (routine)
H + H Facility
discharged
1
22300992
1.155584e+09
2014-12-16
2014-12-18
NaN
2051-05-24
Male
Black or African American
NaT
2014-12-16
discharge (routine)
H + H Facility
discharged
2
22300992
1.156688e+09
2014-12-22
2014-12-22
60.0
2051-05-24
Male
Black or African American
NaT
2014-12-22
routine
H + H Facility
closed discharged
3
22300992
1.156879e+09
2014-12-23
2014-12-24
860.0
2051-05-24
Male
Black or African American
NaT
2014-12-23
routine
H + H Facility
closed discharged
4
22300992
1.157299e+09
2014-12-28
2014-12-28
884.0
2051-05-24
Male
Black or African American
NaT
2014-12-28
discharge (routine)
H + H Facility
closed discharged

Total Number of Unique Patients:
AS asked in the problem statement, we used the 'nunique' function to find out how many unique records exist in the patient ID columns. This will tell us the total number of patients in the dataset.
In [16]:


combineData['ID'].nunique()


Out[16]:
169
In [17]:


demographicData = combineData.drop_duplicates(subset='ID', inplace=False)
demographicData.head()


Out[17]:

ID
VISIT_ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
CLINIC_CODE
BIRTHDATE
SEX
RACE
DATE_OF_DEATH
VISIT_ACTIVATION_DATE_TIME
DISCHARGE_TYPE
FACILITY
VISIT_STATUS
0
22300992
1.152389e+09
2014-11-25
2014-11-25
0.0
2051-05-24
Male
Black or African American
NaT
2014-11-25
discharge (routine)
H + H Facility
discharged
33
23665824
4.335787e+08
2003-03-10
2003-03-13
0.0
2052-04-14
Female
Hispanic
NaT
2003-03-09
routine
H + H Facility
closed discharged
93
24810016
3.871856e+08
2002-04-09
2002-04-10
230.0
2057-12-04
Female
Black or African American
NaT
2002-04-09
Routine
H + H Facility
discharged
273
26523392
3.305710e+08
2001-03-26
2001-03-26
269.0
2067-12-31
Male
Hispanic
NaT
2001-03-26
routine
H + H Facility
closed discharged
299
27421632
6.117502e+08
2006-07-15
2006-07-16
4.0
2062-04-05
Female
Black or African American
NaT
2006-07-15
discharge (routine)
H + H Facility
closed discharged
In [18]:


demographicData.shape


Out[18]:
(169, 13)
In [82]:


demographicData['ID'].value_counts()


Out[82]:
7453600     1
21542752    1
38659264    1
69495776    1
36042944    1
3435936     1
44648352    1
35764288    1
28438144    1
20292384    1
26154688    1
50986208    1
42370720    1
34861120    1
52633504    1
60000864    1
4626720     1
19401984    1
3136000     1
39541824    1
84279776    1
53067840    1
10087392    1
28283808    1
25332832    1
35001792    1
22386336    1
12356512    1
84284032    1
54848192    1
50413440    1
84789600    1
6674528     1
18837952    1
20718880    1
46070080    1
7339360     1
52263456    1
83918912    1
37472512    1
9075808     1
24803520    1
6223168     1
32739168    1
55521760    1
10709216    1
47221440    1
57631168    1
52898720    1
30972256    1
63040320    1
13288576    1
66704736    1
819392      1
51375520    1
15181376    1
82681088    1
80459680    1
64927520    1
297472      1
86567936    1
19353600    1
76259232    1
64906912    1
64050112    1
24810016    1
64368864    1
37511488    1
15150240    1
71936032    1
76640704    1
16293312    1
44109184    1
20039712    1
9774688     1
55047104    1
12728352    1
2205504     1
66242400    1
12613664    1
14058688    1
28653184    1
60754176    1
33221440    1
67378976    1
51996896    1
63941248    1
67152960    1
12137440    1
47483072    1
77373408    1
85620416    1
22495648    1
83874784    1
42519456    1
55738144    1
24663968    1
33104736    1
77370048    1
31249568    1
40674368    1
71635200    1
29779232    1
85384320    1
38058496    1
18739168    1
55682368    1
40160736    1
60040960    1
32354112    1
13752928    1
65186240    1
2738848     1
41756288    1
62105792    1
72760128    1
61997600    1
37790816    1
30041536    1
66174752    1
34207040    1
13554016    1
11111968    1
4337984     1
30311680    1
29973888    1
28990976    1
27421632    1
63383264    1
56819168    1
12134304    1
86708832    1
75103616    1
3950912     1
62697600    1
57708896    1
85254400    1
30172352    1
34720       1
57565760    1
56538944    1
45427200    1
48319264    1
66123008    1
7193088     1
37209088    1
42485632    1
17997504    1
46219936    1
22300992    1
23665824    1
15095136    1
47097792    1
15576288    1
31458112    1
29722784    1
24629024    1
34603968    1
65664032    1
53420640    1
45721536    1
53331488    1
40905536    1
76682144    1
2800672     1
37026752    1
71929984    1
3847424     1
26523392    1
Name: ID, dtype: int64

After combinining the data, we need to drop the duplicate records from the combined dataframe and form a new dataframe , which will be used for visualiing and interpreting the demographic data.
In [93]:


demographicData.head()


Out[93]:

ID
VISIT_ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
CLINIC_CODE
BIRTHDATE
SEX
RACE
DATE_OF_DEATH
VISIT_ACTIVATION_DATE_TIME
DISCHARGE_TYPE
FACILITY
VISIT_STATUS
0
22300992
1.152389e+09
2014-11-25
2014-11-25
0.0
2051-05-24
Male
Black or African American
NaT
2014-11-25
discharge (routine)
H + H Facility
discharged
33
23665824
4.335787e+08
2003-03-10
2003-03-13
0.0
2052-04-14
Female
Hispanic
NaT
2003-03-09
routine
H + H Facility
closed discharged
93
24810016
3.871856e+08
2002-04-09
2002-04-10
230.0
2057-12-04
Female
Black or African American
NaT
2002-04-09
Routine
H + H Facility
discharged
273
26523392
3.305710e+08
2001-03-26
2001-03-26
269.0
2067-12-31
Male
Hispanic
NaT
2001-03-26
routine
H + H Facility
closed discharged
299
27421632
6.117502e+08
2006-07-15
2006-07-16
4.0
2062-04-05
Female
Black or African American
NaT
2006-07-15
discharge (routine)
H + H Facility
closed discharged

Data Visualization:
We have imported an essential visualization package for python. A noteworthy point is that we are considering two factos : 1)RACE 2)SEX , in the same visualization. A single graph can give us a lot of information on the demographics. 
In [20]:


import seaborn as sns
sns.set(style = 'darkgrid')
sns.set(rc={'figure.figsize':(11.7,8.27)})
sns.countplot(x='SEX', hue = 'RACE',data=demographicData , palette = "Set1" )


Out[20]:
<matplotlib.axes._subplots.AxesSubplot at 0x2872902af98>


In [21]:


combineData.isna().sum()


Out[21]:
ID                               0
VISIT_ID                        98
ADMISSION_DATE_TIME             98
DISCHARGE_DATE_TIME            306
CLINIC_CODE                    531
BIRTHDATE                       98
SEX                             98
RACE                            98
DATE_OF_DEATH                 9668
VISIT_ACTIVATION_DATE_TIME      98
DISCHARGE_TYPE                 353
FACILITY                        98
VISIT_STATUS                    98
dtype: int64
In [22]:


deadpatientData = combineData.dropna(subset=['DATE_OF_DEATH'])
deadpatientData.shape


Out[22]:
(430, 13)
In [23]:


deadpatientData.isna().sum()


Out[23]:
ID                              0
VISIT_ID                       98
ADMISSION_DATE_TIME            98
DISCHARGE_DATE_TIME           105
CLINIC_CODE                   102
BIRTHDATE                      98
SEX                            98
RACE                           98
DATE_OF_DEATH                   0
VISIT_ACTIVATION_DATE_TIME     98
DISCHARGE_TYPE                105
FACILITY                       98
VISIT_STATUS                   98
dtype: int64
In [24]:


pd.set_option('display.max_rows', 2000)
deadpatientData


Out[24]:

ID
VISIT_ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
CLINIC_CODE
BIRTHDATE
SEX
RACE
DATE_OF_DEATH
VISIT_ACTIVATION_DATE_TIME
DISCHARGE_TYPE
FACILITY
VISIT_STATUS
393
28438144
2.883912e+08
2000-06-13
2000-07-01
656.0
1988-03-21
Female
Black or African American
2015-07-02
2000-06-13
routine
H + H Facility
discharged
394
28438144
3.171398e+08
2000-12-22
2000-12-29
656.0
1988-03-21
Female
Black or African American
2015-07-02
2000-12-22
routine
H + H Facility
discharged
395
28438144
4.079258e+08
2002-09-03
2002-09-03
7.0
1988-03-21
Female
Black or African American
2015-07-02
2002-09-03
routine
H + H Facility
discharged
396
28438144
4.329317e+08
2003-03-04
2003-03-07
7.0
1988-03-21
Female
Black or African American
2015-07-02
2003-03-04
routine
H + H Facility
discharged
397
28438144
4.639150e+08
2003-10-09
2003-10-14
7.0
1988-03-21
Female
Black or African American
2015-07-02
2003-10-09
routine
H + H Facility
closed discharged
398
28438144
4.663172e+08
2003-10-20
2003-10-27
590.0
1988-03-21
Female
Black or African American
2015-07-02
2003-10-20
routine
H + H Facility
closed discharged
399
28438144
4.664979e+08
2003-10-28
2003-10-28
7.0
1988-03-21
Female
Black or African American
2015-07-02
2003-10-28
routine
H + H Facility
closed discharged
400
28438144
4.671861e+08
2003-11-01
2003-11-07
656.0
1988-03-21
Female
Black or African American
2015-07-02
2003-11-01
discharge (routine)
H + H Facility
closed discharged
401
28438144
4.711685e+08
2003-12-02
2003-12-03
7.0
1988-03-21
Female
Black or African American
2015-07-02
2003-12-02
routine
H + H Facility
closed discharged
402
28438144
4.742204e+08
2003-12-12
2003-12-23
590.0
1988-03-21
Female
Black or African American
2015-07-02
2003-12-12
routine
H + H Facility
closed discharged
403
28438144
4.759061e+08
2003-12-17
2004-01-07
590.0
1988-03-21
Female
Black or African American
2015-07-02
2003-12-17
routine
H + H Facility
closed discharged
404
28438144
4.811387e+08
2004-02-13
NaT
530.0
1988-03-21
Female
Black or African American
2015-07-02
2004-02-13
NaN
H + H Facility
closed cancelled
405
28438144
4.821440e+08
2004-02-12
2004-02-20
590.0
1988-03-21
Female
Black or African American
2015-07-02
2004-02-12
routine
H + H Facility
closed discharged
406
28438144
4.831588e+08
2004-02-25
2004-02-26
590.0
1988-03-21
Female
Black or African American
2015-07-02
2004-02-25
routine
H + H Facility
closed discharged
407
28438144
4.920241e+08
2004-04-26
2004-05-01
530.0
1988-03-21
Female
Black or African American
2015-07-02
2004-04-26
routine
H + H Facility
discharged
408
28438144
4.937546e+08
2004-05-05
2004-05-07
590.0
1988-03-21
Female
Black or African American
2015-07-02
2004-05-05
routine
H + H Facility
closed discharged
409
28438144
4.944903e+08
2004-05-12
2004-05-13
7.0
1988-03-21
Female
Black or African American
2015-07-02
2004-05-12
routine
H + H Facility
closed discharged
410
28438144
5.029076e+08
2004-07-10
2004-07-10
656.0
1988-03-21
Female
Black or African American
2015-07-02
2004-07-10
discharge (routine)
H + H Facility
closed discharged
411
28438144
5.128228e+08
2004-09-21
2004-09-22
7.0
1988-03-21
Female
Black or African American
2015-07-02
2004-09-21
routine
H + H Facility
closed discharged
412
28438144
5.229402e+08
2004-12-01
2004-12-01
7.0
1988-03-21
Female
Black or African American
2015-07-02
2004-12-01
routine
H + H Facility
closed discharged
413
28438144
5.240009e+08
2004-12-08
2004-12-08
7.0
1988-03-21
Female
Black or African American
2015-07-02
2004-12-08
routine
H + H Facility
discharged
414
28438144
5.416398e+08
2005-04-07
2005-04-07
560.0
1988-03-21
Female
Black or African American
2015-07-02
2005-04-07
routine
H + H Facility
closed discharged
415
28438144
5.452698e+08
2005-04-30
2005-05-02
656.0
1988-03-21
Female
Black or African American
2015-07-02
2005-04-30
discharge (routine)
H + H Facility
closed discharged
416
28438144
5.454965e+08
2005-05-02
2005-05-03
656.0
1988-03-21
Female
Black or African American
2015-07-02
2005-05-02
discharge (routine)
H + H Facility
closed discharged
417
28438144
5.716761e+08
2005-10-25
2005-10-25
7.0
1988-03-21
Female
Black or African American
2015-07-02
2005-10-25
routine
H + H Facility
discharged
418
28438144
5.718850e+08
2005-10-26
2005-10-26
7.0
1988-03-21
Female
Black or African American
2015-07-02
2005-10-26
routine
H + H Facility
closed discharged
419
28438144
5.733240e+08
2005-11-02
2005-11-04
354.0
1988-03-21
Female
Black or African American
2015-07-02
2005-11-02
routine
H + H Facility
closed discharged
420
28438144
5.846120e+08
2006-01-26
2006-01-26
7.0
1988-03-21
Female
Black or African American
2015-07-02
2006-01-26
routine
H + H Facility
closed discharged
421
28438144
5.953619e+08
2006-04-03
2006-04-04
530.0
1988-03-21
Female
Black or African American
2015-07-02
2006-04-03
routine
H + H Facility
discharged
422
28438144
6.294809e+08
2006-11-06
2006-11-08
530.0
1988-03-21
Female
Black or African American
2015-07-02
2006-11-06
routine
H + H Facility
discharged
423
28438144
6.303002e+08
2006-11-13
2006-11-16
530.0
1988-03-21
Female
Black or African American
2015-07-02
2006-11-13
routine
H + H Facility
closed discharged
424
28438144
6.314874e+08
2006-11-20
2006-11-21
530.0
1988-03-21
Female
Black or African American
2015-07-02
2006-11-20
routine
H + H Facility
closed discharged
425
28438144
6.434112e+08
2007-02-06
2007-02-09
530.0
1988-03-21
Female
Black or African American
2015-07-02
2007-02-06
routine
H + H Facility
discharged
426
28438144
6.435927e+08
2007-02-07
2007-02-08
7.0
1988-03-21
Female
Black or African American
2015-07-02
2007-02-07
routine
H + H Facility
discharged
427
28438144
6.489755e+08
2007-03-13
2007-03-15
530.0
1988-03-21
Female
Black or African American
2015-07-02
2007-03-13
routine
H + H Facility
discharged
428
28438144
6.514314e+08
2007-03-27
2007-03-27
530.0
1988-03-21
Female
Black or African American
2015-07-02
2007-03-27
routine
H + H Facility
closed discharged
429
28438144
6.609674e+08
2007-05-22
2007-05-22
530.0
1988-03-21
Female
Black or African American
2015-07-02
2007-05-22
routine
H + H Facility
discharged
430
28438144
6.642650e+08
2007-06-12
2007-06-13
530.0
1988-03-21
Female
Black or African American
2015-07-02
2007-06-12
routine
H + H Facility
discharged
431
28438144
6.646395e+08
2007-06-14
2007-06-14
7.0
1988-03-21
Female
Black or African American
2015-07-02
2007-06-14
routine
H + H Facility
discharged
432
28438144
6.662190e+08
2007-06-25
2007-06-25
725.0
1988-03-21
Female
Black or African American
2015-07-02
2007-06-25
routine
H + H Facility
discharged
433
28438144
9.035296e+08
2011-01-14
2011-01-14
884.0
1988-03-21
Female
Black or African American
2015-07-02
2011-01-14
discharge (routine)
H + H Facility
discharged
434
28438144
9.189566e+08
2011-04-02
2011-04-05
884.0
1988-03-21
Female
Black or African American
2015-07-02
2011-04-01
routine
H + H Facility
discharged
435
28438144
9.676198e+08
2011-12-17
2012-01-06
0.0
1988-03-21
Female
Black or African American
2015-07-02
2011-12-16
routine
H + H Facility
discharged
436
28438144
9.713686e+08
2012-01-10
2012-01-10
695.0
1988-03-21
Female
Black or African American
2015-07-02
2012-01-10
routine
H + H Facility
closed discharged
437
28438144
9.718723e+08
2012-01-12
2012-01-12
780.0
1988-03-21
Female
Black or African American
2015-07-02
2012-01-12
routine
H + H Facility
closed discharged
438
28438144
9.729305e+08
2012-01-19
2012-01-19
780.0
1988-03-21
Female
Black or African American
2015-07-02
2012-01-19
routine
H + H Facility
closed discharged
439
28438144
9.738087e+08
2012-01-24
2012-01-25
780.0
1988-03-21
Female
Black or African American
2015-07-02
2012-01-24
routine
H + H Facility
closed discharged
440
28438144
9.778214e+08
2012-02-14
NaT
715.0
1988-03-21
Female
Black or African American
2015-07-02
2012-02-14
NaN
H + H Facility
closed cancelled
441
28438144
9.778484e+08
2012-02-14
2012-02-14
695.0
1988-03-21
Female
Black or African American
2015-07-02
2012-02-14
routine
H + H Facility
closed discharged
442
28438144
9.794155e+08
2012-02-23
2012-03-08
337.0
1988-03-21
Female
Black or African American
2015-07-02
2012-02-23
routine
H + H Facility
discharged
443
28438144
9.828825e+08
2012-03-13
2012-03-19
695.0
1988-03-21
Female
Black or African American
2015-07-02
2012-03-13
routine
H + H Facility
closed discharged
444
28438144
9.836153e+08
2012-03-15
2012-03-16
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-03-15
routine
H + H Facility
closed discharged
445
28438144
9.849073e+08
2012-03-20
2012-03-23
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-03-20
routine
H + H Facility
closed discharged
446
28438144
9.860038e+08
2012-03-27
2012-03-29
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-03-27
routine
H + H Facility
closed discharged
447
28438144
9.863619e+08
2012-03-29
2012-03-30
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-03-29
routine
H + H Facility
closed discharged
448
28438144
9.874184e+08
2012-04-05
2012-04-05
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-04-05
routine
H + H Facility
closed discharged
449
28438144
9.875814e+08
2012-04-04
2012-04-06
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-04-04
routine
H + H Facility
closed discharged
450
28438144
9.884641e+08
2012-04-09
2012-04-12
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-04-09
routine
H + H Facility
closed discharged
451
28438144
9.888021e+08
2012-04-12
2012-04-13
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-04-12
routine
H + H Facility
closed discharged
452
28438144
9.906676e+08
2012-04-20
2012-04-24
667.0
1988-03-21
Female
Black or African American
2015-07-02
2012-04-20
routine
H + H Facility
closed discharged
453
28438144
9.931244e+08
2012-05-08
2012-05-09
695.0
1988-03-21
Female
Black or African American
2015-07-02
2012-05-08
routine
H + H Facility
closed discharged
454
28438144
9.950671e+08
2012-05-18
2012-05-18
0.0
1988-03-21
Female
Black or African American
2015-07-02
2012-05-18
discharge (routine)
H + H Facility
closed discharged
455
28438144
1.011969e+09
2012-08-27
2012-08-31
0.0
1988-03-21
Female
Black or African American
2015-07-02
2012-08-26
routine
H + H Facility
discharged
456
28438144
1.013999e+09
2012-09-07
2012-09-07
230.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-07
routine
H + H Facility
discharged
457
28438144
1.015104e+09
2012-09-13
NaT
480.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-13
NaN
H + H Facility
closed cancelled
458
28438144
1.015121e+09
2012-09-13
NaT
450.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-13
NaN
H + H Facility
closed cancelled
459
28438144
1.015125e+09
2012-09-13
2012-09-14
230.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-13
routine
H + H Facility
closed discharged
460
28438144
1.017294e+09
2012-09-26
2012-09-28
478.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-26
routine
H + H Facility
closed discharged
461
28438144
1.017851e+09
2012-09-28
2012-10-02
1.0
1988-03-21
Female
Black or African American
2015-07-02
2012-09-28
routine
H + H Facility
discharged
462
28438144
1.019288e+09
2012-10-09
2012-10-09
480.0
1988-03-21
Female
Black or African American
2015-07-02
2012-10-09
routine
H + H Facility
discharged
463
28438144
1.021049e+09
2012-10-18
2012-10-19
480.0
1988-03-21
Female
Black or African American
2015-07-02
2012-10-18
routine
H + H Facility
closed discharged
464
28438144
1.021591e+09
2012-10-22
2012-10-22
725.0
1988-03-21
Female
Black or African American
2015-07-02
2012-10-22
routine
H + H Facility
closed discharged
465
28438144
1.025301e+09
2012-11-15
2012-11-15
480.0
1988-03-21
Female
Black or African American
2015-07-02
2012-11-15
routine
H + H Facility
discharged
466
28438144
1.027610e+09
2012-11-28
2012-12-01
1.0
1988-03-21
Female
Black or African American
2015-07-02
2012-11-28
routine
H + H Facility
discharged
467
28438144
1.035306e+09
2013-01-10
2013-01-15
1.0
1988-03-21
Female
Black or African American
2015-07-02
2013-01-10
routine
H + H Facility
discharged
468
28438144
1.037195e+09
2013-01-22
2013-01-23
0.0
1988-03-21
Female
Black or African American
2015-07-02
2013-01-22
discharge (routine)
H + H Facility
discharged
469
28438144
1.037870e+09
2013-01-24
2013-01-25
0.0
1988-03-21
Female
Black or African American
2015-07-02
2013-01-24
discharge (routine)
H + H Facility
discharged
470
28438144
1.037905e+09
2013-01-25
2013-01-26
1.0
1988-03-21
Female
Black or African American
2015-07-02
2013-01-24
routine
H + H Facility
discharged
471
28438144
1.038168e+09
2013-01-27
2013-01-27
0.0
1988-03-21
Female
Black or African American
2015-07-02
2013-01-27
discharge (routine)
H + H Facility
discharged
472
28438144
1.066261e+09
2013-07-02
2013-07-03
884.0
1988-03-21
Female
Black or African American
2015-07-02
2013-07-02
discharge (routine)
H + H Facility
discharged
473
28438144
1.106824e+09
2014-02-28
2014-02-28
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-02-28
left against medical advice
H + H Facility
closed discharged
474
28438144
1.111962e+09
2014-03-28
2014-04-10
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-03-27
routine
H + H Facility
discharged
475
28438144
1.119878e+09
2014-05-13
2014-07-02
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-05-13
routine
H + H Facility
discharged
476
28438144
1.128890e+09
2014-07-29
2014-07-29
NaN
1988-03-21
Female
Black or African American
2015-07-02
2014-07-04
discharge (routine)
H + H Facility
discharged
477
28438144
1.130071e+09
2014-07-12
2014-07-12
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-07-12
discharge (routine)
H + H Facility
discharged
478
28438144
1.130130e+09
2014-07-13
2014-07-18
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-07-13
routine
H + H Facility
discharged
479
28438144
1.145348e+09
2014-10-12
2014-10-13
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-10-11
routine
H + H Facility
discharged
480
28438144
1.145842e+09
2014-10-15
2014-10-21
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-10-15
routine
H + H Facility
discharged
481
28438144
1.147908e+09
2014-10-28
2014-10-30
0.0
1988-03-21
Female
Black or African American
2015-07-02
2014-10-27
routine
H + H Facility
discharged
482
28438144
1.173269e+09
2015-04-08
2015-04-09
0.0
1988-03-21
Female
Black or African American
2015-07-02
2015-04-08
discharge (routine)
H + H Facility
discharged
483
28438144
1.177613e+09
2015-05-05
2015-05-09
0.0
1988-03-21
Female
Black or African American
2015-07-02
2015-05-05
routine
H + H Facility
discharged
484
28438144
1.182498e+09
2015-06-03
2015-06-03
0.0
1988-03-21
Female
Black or African American
2015-07-02
2015-06-03
discharge (routine)
H + H Facility
discharged
485
28438144
1.183865e+09
2015-06-11
2015-06-12
0.0
1988-03-21
Female
Black or African American
2015-07-02
2015-06-11
discharge (routine)
H + H Facility
discharged
486
28438144
1.185303e+09
2015-06-21
2015-07-02
0.0
1988-03-21
Female
Black or African American
2015-07-02
2015-06-21
expired
H + H Facility
discharged
732
30041536
2.820574e+07
1995-01-17
1995-01-17
160.0
2040-11-05
Female
Hispanic
2015-02-25
1995-01-17
routine
H + H Facility
discharged
733
30041536
3.057194e+08
2000-10-05
2000-10-05
780.0
2040-11-05
Female
Hispanic
2015-02-25
2000-10-05
routine
H + H Facility
discharged
734
30041536
3.190318e+08
2001-01-09
2001-01-11
160.0
2040-11-05
Female
Hispanic
2015-02-25
2001-01-09
routine
H + H Facility
discharged
735
30041536
3.496013e+08
2001-07-30
2001-07-31
337.0
2040-11-05
Female
Hispanic
2015-02-25
2001-07-30
routine
H + H Facility
discharged
736
30041536
3.507174e+08
2001-08-07
2001-08-07
160.0
2040-11-05
Female
Hispanic
2015-02-25
2001-08-07
routine
H + H Facility
discharged
737
30041536
3.783332e+08
2002-02-11
2002-02-11
337.0
2040-11-05
Female
Hispanic
2015-02-25
2002-02-11
routine
H + H Facility
closed discharged
738
30041536
3.814651e+08
2002-03-04
2002-03-05
337.0
2040-11-05
Female
Hispanic
2015-02-25
2002-03-04
routine
H + H Facility
discharged
739
30041536
3.849524e+08
2002-03-25
2002-03-27
337.0
2040-11-05
Female
Hispanic
2015-02-25
2002-03-25
routine
H + H Facility
discharged
740
30041536
4.077825e+08
2002-09-03
2002-09-03
160.0
2040-11-05
Female
Hispanic
2015-02-25
2002-09-03
routine
H + H Facility
discharged
741
30041536
4.080665e+08
2002-09-04
2002-09-04
337.0
2040-11-05
Female
Hispanic
2015-02-25
2002-09-04
routine
H + H Facility
discharged
742
30041536
4.278491e+08
2003-01-28
2003-01-28
160.0
2040-11-05
Female
Hispanic
2015-02-25
2003-01-28
routine
H + H Facility
discharged
743
30041536
4.387812e+08
2003-04-14
2003-04-14
510.0
2040-11-05
Female
Hispanic
2015-02-25
2003-04-14
routine
H + H Facility
discharged
744
30041536
4.413851e+08
2003-05-01
2003-05-01
780.0
2040-11-05
Female
Hispanic
2015-02-25
2003-05-01
routine
H + H Facility
discharged
745
30041536
4.424368e+08
2003-05-08
2003-05-08
337.0
2040-11-05
Female
Hispanic
2015-02-25
2003-05-08
routine
H + H Facility
discharged
746
30041536
4.469975e+08
2003-06-10
2003-06-10
160.0
2040-11-05
Female
Hispanic
2015-02-25
2003-06-10
routine
H + H Facility
discharged
747
30041536
4.554890e+08
2003-08-11
2003-08-11
510.0
2040-11-05
Female
Hispanic
2015-02-25
2003-08-11
routine
H + H Facility
closed discharged
748
30041536
4.577361e+08
2003-08-27
2003-08-29
835.0
2040-11-05
Female
Hispanic
2015-02-25
2003-08-27
routine
H + H Facility
closed discharged
749
30041536
4.586927e+08
2003-09-04
2003-09-04
780.0
2040-11-05
Female
Hispanic
2015-02-25
2003-09-04
routine
H + H Facility
closed discharged
750
30041536
4.598069e+08
2003-09-11
2003-09-11
337.0
2040-11-05
Female
Hispanic
2015-02-25
2003-09-11
routine
H + H Facility
discharged
751
30041536
4.618081e+08
2003-09-25
2003-09-30
215.0
2040-11-05
Female
Hispanic
2015-02-25
2003-09-25
routine
H + H Facility
closed discharged
752
30041536
4.642825e+08
2003-10-14
2003-10-17
160.0
2040-11-05
Female
Hispanic
2015-02-25
2003-10-14
routine
H + H Facility
closed discharged
753
30041536
4.653532e+08
2003-10-21
2003-10-21
160.0
2040-11-05
Female
Hispanic
2015-02-25
2003-10-21
routine
H + H Facility
discharged
754
30041536
4.683175e+08
2003-11-12
2003-11-12
500.0
2040-11-05
Female
Hispanic
2015-02-25
2003-11-12
routine
H + H Facility
closed discharged
755
30041536
4.771932e+08
2004-01-16
2004-01-23
770.0
2040-11-05
Female
Hispanic
2015-02-25
2004-01-16
routine
H + H Facility
closed discharged
756
30041536
4.776433e+08
2004-01-21
2004-01-21
337.0
2040-11-05
Female
Hispanic
2015-02-25
2004-01-21
routine
H + H Facility
discharged
757
30041536
4.798054e+08
2004-02-05
2004-02-05
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-02-05
routine
H + H Facility
closed discharged
758
30041536
4.818435e+08
2004-02-19
2004-02-26
NaN
2040-11-05
Female
Hispanic
2015-02-25
2004-02-19
routine
H + H Facility
closed discharged
759
30041536
4.832910e+08
2004-02-27
2004-02-27
337.0
2040-11-05
Female
Hispanic
2015-02-25
2004-02-27
routine
H + H Facility
closed discharged
760
30041536
4.847852e+08
2004-03-09
2004-03-09
215.0
2040-11-05
Female
Hispanic
2015-02-25
2004-03-09
routine
H + H Facility
closed discharged
761
30041536
4.862102e+08
2004-03-17
2004-03-17
906.0
2040-11-05
Female
Hispanic
2015-02-25
2004-03-17
routine
H + H Facility
closed discharged
762
30041536
4.873215e+08
2004-03-25
2004-03-25
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-03-25
routine
H + H Facility
closed discharged
763
30041536
4.911086e+08
2004-04-20
2004-04-20
215.0
2040-11-05
Female
Hispanic
2015-02-25
2004-04-20
routine
H + H Facility
closed discharged
764
30041536
4.968649e+08
2004-05-27
2004-05-27
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-05-27
routine
H + H Facility
closed discharged
765
30041536
4.973202e+08
2004-06-01
2004-06-01
160.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-01
routine
H + H Facility
discharged
766
30041536
4.979272e+08
2004-06-04
2004-06-04
337.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-04
routine
H + H Facility
closed discharged
767
30041536
4.985782e+08
2004-06-09
2004-06-15
906.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-09
routine
H + H Facility
closed discharged
768
30041536
4.987690e+08
2004-06-10
2004-06-10
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-10
routine
H + H Facility
closed discharged
769
30041536
4.991763e+08
2004-06-14
2004-06-14
510.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-14
routine
H + H Facility
discharged
770
30041536
4.995796e+08
2004-06-16
2004-06-16
835.0
2040-11-05
Female
Hispanic
2015-02-25
2004-06-16
routine
H + H Facility
closed discharged
771
30041536
5.071772e+08
2004-08-12
2004-08-12
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-08-12
routine
H + H Facility
closed discharged
772
30041536
5.081435e+08
2004-08-19
2004-08-19
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-08-19
routine
H + H Facility
closed discharged
773
30041536
5.088033e+08
2004-08-24
2004-08-24
337.0
2040-11-05
Female
Hispanic
2015-02-25
2004-08-24
routine
H + H Facility
discharged
774
30041536
5.101420e+08
2004-09-02
2004-09-02
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-09-02
routine
H + H Facility
closed discharged
775
30041536
5.130767e+08
2004-09-23
2004-09-23
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-09-23
routine
H + H Facility
closed discharged
776
30041536
5.149903e+08
2004-10-06
2004-10-06
835.0
2040-11-05
Female
Hispanic
2015-02-25
2004-10-06
routine
H + H Facility
closed discharged
777
30041536
5.165702e+08
2004-10-18
2004-10-18
510.0
2040-11-05
Female
Hispanic
2015-02-25
2004-10-18
routine
H + H Facility
closed discharged
778
30041536
5.209434e+08
2004-11-17
2004-11-17
835.0
2040-11-05
Female
Hispanic
2015-02-25
2004-11-17
routine
H + H Facility
discharged
779
30041536
5.218188e+08
2004-11-23
2004-11-23
160.0
2040-11-05
Female
Hispanic
2015-02-25
2004-11-23
routine
H + H Facility
discharged
780
30041536
5.226812e+08
2004-11-30
2004-11-30
337.0
2040-11-05
Female
Hispanic
2015-02-25
2004-11-30
routine
H + H Facility
discharged
781
30041536
5.257999e+08
2004-12-21
2004-12-21
780.0
2040-11-05
Female
Hispanic
2015-02-25
2004-12-21
routine
H + H Facility
closed discharged
782
30041536
5.368564e+08
2005-03-08
2005-03-08
337.0
2040-11-05
Female
Hispanic
2015-02-25
2005-03-08
routine
H + H Facility
discharged
783
30041536
5.371562e+08
2005-03-10
2005-03-10
780.0
2040-11-05
Female
Hispanic
2015-02-25
2005-03-10
routine
H + H Facility
closed discharged
784
30041536
5.392030e+08
2005-03-23
2005-03-24
835.0
2040-11-05
Female
Hispanic
2015-02-25
2005-03-23
routine
H + H Facility
closed discharged
785
30041536
5.433379e+08
2005-04-19
2005-04-19
160.0
2040-11-05
Female
Hispanic
2015-02-25
2005-04-19
routine
H + H Facility
discharged
786
30041536
5.435577e+08
2005-04-20
2005-04-20
835.0
2040-11-05
Female
Hispanic
2015-02-25
2005-04-20
routine
H + H Facility
closed discharged
787
30041536
5.493574e+08
2005-05-26
2005-05-26
780.0
2040-11-05
Female
Hispanic
2015-02-25
2005-05-26
routine
H + H Facility
closed discharged
788
30041536
5.519922e+08
2005-06-14
2005-06-14
906.0
2040-11-05
Female
Hispanic
2015-02-25
2005-06-14
routine
H + H Facility
closed discharged
789
30041536
5.524178e+08
2005-06-16
2005-06-16
780.0
2040-11-05
Female
Hispanic
2015-02-25
2005-06-16
routine
H + H Facility
closed discharged
790
30041536
5.560033e+08
2005-07-12
2005-07-12
160.0
2040-11-05
Female
Hispanic
2015-02-25
2005-07-12
routine
H + H Facility
closed discharged
791
30041536
5.564009e+08
2005-07-14
2005-07-20
780.0
2040-11-05
Female
Hispanic
2015-02-25
2005-07-14
routine
H + H Facility
closed discharged
792
30041536
5.568630e+08
2005-07-18
2005-07-18
337.0
2040-11-05
Female
Hispanic
2015-02-25
2005-07-18
routine
H + H Facility
discharged
793
30041536
5.601865e+08
2005-08-09
2005-08-09
160.0
2040-11-05
Female
Hispanic
2015-02-25
2005-08-09
routine
H + H Facility
closed discharged
794
30041536
5.676464e+08
2005-09-28
2005-09-28
835.0
2040-11-05
Female
Hispanic
2015-02-25
2005-09-28
routine
H + H Facility
discharged
795
30041536
5.730535e+08
2005-11-03
2005-11-03
780.0
2040-11-05
Female
Hispanic
2015-02-25
2005-11-03
routine
H + H Facility
discharged
796
30041536
5.745882e+08
2005-11-15
2005-11-15
337.0
2040-11-05
Female
Hispanic
2015-02-25
2005-11-15
routine
H + H Facility
discharged
797
30041536
5.756317e+08
2005-11-22
2005-11-22
160.0
2040-11-05
Female
Hispanic
2015-02-25
2005-11-22
routine
H + H Facility
discharged
798
30041536
5.764145e+08
2005-11-29
2005-11-29
337.0
2040-11-05
Female
Hispanic
2015-02-25
2005-11-29
routine
H + H Facility
discharged
799
30041536
5.935444e+08
2006-03-23
2006-03-23
780.0
2040-11-05
Female
Hispanic
2015-02-25
2006-03-23
routine
H + H Facility
closed discharged
800
30041536
5.942430e+08
2006-03-28
2006-03-29
835.0
2040-11-05
Female
Hispanic
2015-02-25
2006-03-28
routine
H + H Facility
discharged
801
30041536
5.942431e+08
2006-03-28
2006-03-28
160.0
2040-11-05
Female
Hispanic
2015-02-25
2006-03-28
routine
H + H Facility
discharged
802
30041536
5.978722e+08
2006-04-19
2006-04-19
160.0
2040-11-05
Female
Hispanic
2015-02-25
2006-04-19
routine
H + H Facility
discharged
803
30041536
5.991782e+08
2006-04-27
2006-04-27
780.0
2040-11-05
Female
Hispanic
2015-02-25
2006-04-27
routine
H + H Facility
closed discharged
804
30041536
5.998903e+08
2006-05-02
2006-05-12
835.0
2040-11-05
Female
Hispanic
2015-02-25
2006-05-02
routine
H + H Facility
closed discharged
805
30041536
6.014634e+08
2006-05-11
2006-05-16
780.0
2040-11-05
Female
Hispanic
2015-02-25
2006-05-11
routine
H + H Facility
closed discharged
806
30041536
6.021322e+08
2006-05-16
2006-05-16
835.0
2040-11-05
Female
Hispanic
2015-02-25
2006-05-16
routine
H + H Facility
discharged
807
30041536
6.120292e+08
2006-07-18
2006-07-18
835.0
2040-11-05
Female
Hispanic
2015-02-25
2006-07-18
routine
H + H Facility
closed discharged
808
30041536
6.185080e+08
2006-08-29
2006-08-30
835.0
2040-11-05
Female
Hispanic
2015-02-25
2006-08-29
routine
H + H Facility
discharged
809
30041536
6.194200e+08
2006-09-05
2006-09-05
160.0
2040-11-05
Female
Hispanic
2015-02-25
2006-09-05
routine
H + H Facility
discharged
810
30041536
6.247275e+08
2006-10-06
2006-10-10
500.0
2040-11-05
Female
Hispanic
2015-02-25
2006-10-06
routine
H + H Facility
closed discharged
811
30041536
6.250249e+08
2006-10-10
2006-10-10
160.0
2040-11-05
Female
Hispanic
2015-02-25
2006-10-10
routine
H + H Facility
closed discharged
812
30041536
6.253463e+08
2006-10-11
2006-10-11
337.0
2040-11-05
Female
Hispanic
2015-02-25
2006-10-11
routine
H + H Facility
closed discharged
813
30041536
6.398706e+08
2007-01-16
2007-01-16
337.0
2040-11-05
Female
Hispanic
2015-02-25
2007-01-16
routine
H + H Facility
discharged
814
30041536
6.421237e+08
2007-01-30
2007-02-01
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-01-30
routine
H + H Facility
discharged
815
30041536
6.507530e+08
2007-03-23
2007-03-27
770.0
2040-11-05
Female
Hispanic
2015-02-25
2007-03-23
routine
H + H Facility
closed discharged
816
30041536
6.520775e+08
2007-03-30
2007-03-30
337.0
2040-11-05
Female
Hispanic
2015-02-25
2007-03-30
routine
H + H Facility
discharged
817
30041536
6.574440e+08
2007-05-02
2007-05-02
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-05-02
routine
H + H Facility
closed discharged
818
30041536
6.589065e+08
2007-05-10
2007-05-10
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-05-10
routine
H + H Facility
discharged
819
30041536
6.629421e+08
2007-06-05
NaT
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-06-05
NaN
H + H Facility
closed cancelled
820
30041536
6.641309e+08
2007-06-12
2007-06-12
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-06-12
routine
H + H Facility
discharged
821
30041536
6.641323e+08
2007-06-12
NaT
725.0
2040-11-05
Female
Hispanic
2015-02-25
2007-06-12
NaN
H + H Facility
closed cancelled
822
30041536
6.690017e+08
2007-07-12
2007-07-12
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-07-12
routine
H + H Facility
closed discharged
823
30041536
6.706872e+08
2007-07-23
2007-07-23
337.0
2040-11-05
Female
Hispanic
2015-02-25
2007-07-23
routine
H + H Facility
discharged
824
30041536
6.749180e+08
2007-08-16
2007-08-17
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-08-16
routine
H + H Facility
discharged
825
30041536
6.816103e+08
2007-09-25
2007-09-25
337.0
2040-11-05
Female
Hispanic
2015-02-25
2007-09-25
routine
H + H Facility
discharged
826
30041536
6.844512e+08
2007-10-11
2007-10-11
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-10-11
routine
H + H Facility
closed discharged
827
30041536
6.852590e+08
2007-10-16
2007-10-16
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-10-16
routine
H + H Facility
discharged
828
30041536
6.883771e+08
2007-11-01
2007-11-01
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-11-01
routine
H + H Facility
closed discharged
829
30041536
6.895181e+08
2007-11-08
2007-11-09
780.0
2040-11-05
Female
Hispanic
2015-02-25
2007-11-08
routine
H + H Facility
closed discharged
830
30041536
6.914409e+08
2007-11-20
2007-11-20
835.0
2040-11-05
Female
Hispanic
2015-02-25
2007-11-20
routine
H + H Facility
discharged
831
30041536
6.964799e+08
2007-12-18
2007-12-18
835.0
2040-11-05
Female
Hispanic
2015-02-25
2007-12-18
routine
H + H Facility
closed discharged
832
30041536
6.964868e+08
2007-12-18
2007-12-18
160.0
2040-11-05
Female
Hispanic
2015-02-25
2007-12-18
routine
H + H Facility
discharged
833
30041536
7.111655e+08
2008-03-10
2008-03-10
337.0
2040-11-05
Female
Hispanic
2015-02-25
2008-03-10
routine
H + H Facility
discharged
834
30041536
7.132952e+08
2008-03-20
2008-03-20
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-03-20
routine
H + H Facility
discharged
835
30041536
7.145611e+08
2008-03-27
2008-03-27
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-03-27
routine
H + H Facility
discharged
836
30041536
7.171851e+08
2008-04-10
2008-04-10
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-04-10
routine
H + H Facility
closed discharged
837
30041536
7.182388e+08
2008-04-16
2008-04-16
835.0
2040-11-05
Female
Hispanic
2015-02-25
2008-04-16
routine
H + H Facility
discharged
838
30041536
7.212491e+08
2008-05-02
2008-05-09
684.0
2040-11-05
Female
Hispanic
2015-02-25
2008-05-02
routine
H + H Facility
closed discharged
839
30041536
7.233349e+08
2008-05-14
2008-05-16
770.0
2040-11-05
Female
Hispanic
2015-02-25
2008-05-14
routine
H + H Facility
closed discharged
840
30041536
7.308387e+08
2008-06-25
2008-06-25
835.0
2040-11-05
Female
Hispanic
2015-02-25
2008-06-25
routine
H + H Facility
closed discharged
841
30041536
7.308670e+08
2008-06-25
2008-06-26
337.0
2040-11-05
Female
Hispanic
2015-02-25
2008-06-25
routine
H + H Facility
discharged
842
30041536
7.395337e+08
2008-08-14
2008-08-14
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-08-14
routine
H + H Facility
closed discharged
843
30041536
7.407474e+08
2008-08-21
2008-08-21
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-08-21
routine
H + H Facility
closed discharged
844
30041536
7.437751e+08
2008-09-09
2008-09-09
160.0
2040-11-05
Female
Hispanic
2015-02-25
2008-09-09
routine
H + H Facility
discharged
845
30041536
7.466175e+08
2008-09-24
2008-10-02
835.0
2040-11-05
Female
Hispanic
2015-02-25
2008-09-24
routine
H + H Facility
closed discharged
846
30041536
7.466201e+08
2008-09-24
2008-09-24
160.0
2040-11-05
Female
Hispanic
2015-02-25
2008-09-24
routine
H + H Facility
discharged
847
30041536
7.477021e+08
2008-09-30
2008-10-01
337.0
2040-11-05
Female
Hispanic
2015-02-25
2008-09-30
routine
H + H Facility
discharged
848
30041536
7.494676e+08
2008-10-09
2008-10-09
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-10-09
routine
H + H Facility
closed discharged
2172
819392
3.930561e+08
2002-05-18
2002-05-24
0.0
2064-08-31
Female
White
2015-07-10
2002-05-18
discharge (routine)
H + H Facility
closed discharged
2173
819392
1.058556e+09
2013-05-20
2013-05-20
0.0
2064-08-31
Female
White
2015-07-10
2013-05-20
discharge (routine)
H + H Facility
discharged
2174
819392
1.059884e+09
2013-05-28
2013-06-03
840.0
2064-08-31
Female
White
2015-07-10
2013-05-28
routine
H + H Facility
discharged
2175
819392
1.063747e+09
2013-06-18
2013-06-18
840.0
2064-08-31
Female
White
2015-07-10
2013-06-18
routine
H + H Facility
discharged
2176
819392
1.081404e+09
2013-10-01
2013-10-01
NaN
2064-08-31
Female
White
2015-07-10
2013-09-30
discharge (routine)
H + H Facility
discharged
2177
819392
1.117065e+09
2014-04-26
2014-04-27
0.0
2064-08-31
Female
White
2015-07-10
2014-04-25
routine
H + H Facility
discharged
2178
819392
1.117861e+09
2014-04-30
2014-05-01
520.0
2064-08-31
Female
White
2015-07-10
2014-04-30
routine
H + H Facility
discharged
2179
819392
1.131307e+09
2014-07-20
2014-07-21
0.0
2064-08-31
Female
White
2015-07-10
2014-07-20
discharge (routine)
H + H Facility
closed discharged
2180
819392
1.131875e+09
2014-07-23
2014-07-24
835.0
2064-08-31
Female
White
2015-07-10
2014-07-23
routine
H + H Facility
closed discharged
2181
819392
1.132034e+09
2014-07-24
2014-07-25
520.0
2064-08-31
Female
White
2015-07-10
2014-07-24
routine
H + H Facility
closed discharged
2182
819392
1.146068e+09
2014-10-16
2014-10-22
0.0
2064-08-31
Female
White
2015-07-10
2014-10-16
routine
H + H Facility
discharged
2183
819392
1.150856e+09
2014-11-14
2014-11-15
0.0
2064-08-31
Female
White
2015-07-10
2014-11-14
discharge (routine)
H + H Facility
discharged
2184
819392
1.164949e+09
2015-03-11
2015-03-12
NaN
2064-08-31
Female
White
2015-07-10
2015-02-17
discharge (routine)
H + H Facility
discharged
2185
819392
1.177430e+09
2015-05-04
2015-05-05
0.0
2064-08-31
Female
White
2015-07-10
2015-05-04
discharge (routine)
H + H Facility
discharged
2186
819392
1.177740e+09
2015-05-06
2015-05-28
0.0
2064-08-31
Female
White
2015-07-10
2015-05-05
routine
H + H Facility
discharged
2187
819392
1.182309e+09
2015-06-02
2015-06-03
0.0
2064-08-31
Female
White
2015-07-10
2015-06-02
discharge (routine)
H + H Facility
discharged
2188
819392
1.188317e+09
2015-07-09
2015-07-10
0.0
2064-08-31
Female
White
2015-07-10
2015-07-09
expired (deceased)
H + H Facility
discharged
3031
30041536
7.519247e+08
2008-10-23
2008-10-23
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-10-23
routine
H + H Facility
closed discharged
3032
30041536
7.615819e+08
2008-12-18
2008-12-18
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-12-18
routine
H + H Facility
closed discharged
3033
30041536
7.623060e+08
2008-12-23
2008-12-23
160.0
2040-11-05
Female
Hispanic
2015-02-25
2008-12-23
routine
H + H Facility
closed discharged
3034
30041536
7.623915e+08
2008-12-23
2008-12-23
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-12-23
routine
H + H Facility
closed discharged
3035
30041536
7.631890e+08
2008-12-30
2008-12-30
780.0
2040-11-05
Female
Hispanic
2015-02-25
2008-12-30
routine
H + H Facility
closed discharged
3036
30041536
7.677604e+08
2009-01-27
2009-01-27
160.0
2040-11-05
Female
Hispanic
2015-02-25
2009-01-27
routine
H + H Facility
discharged
3037
30041536
7.688308e+08
2009-02-02
2009-02-02
337.0
2040-11-05
Female
Hispanic
2015-02-25
2009-02-02
routine
H + H Facility
closed discharged
3038
30041536
7.704391e+08
2009-02-10
2009-02-10
500.0
2040-11-05
Female
Hispanic
2015-02-25
2009-02-10
routine
H + H Facility
closed discharged
3039
30041536
7.772218e+08
2009-03-18
2009-03-18
337.0
2040-11-05
Female
Hispanic
2015-02-25
2009-03-18
routine
H + H Facility
closed discharged
3040
30041536
7.827716e+08
2009-04-16
2009-04-16
780.0
2040-11-05
Female
Hispanic
2015-02-25
2009-04-16
routine
H + H Facility
closed discharged
3041
30041536
7.863386e+08
2009-05-05
2009-05-05
160.0
2040-11-05
Female
Hispanic
2015-02-25
2009-05-05
routine
H + H Facility
discharged
3042
30041536
7.926019e+08
2009-06-05
2009-06-08
337.0
2040-11-05
Female
Hispanic
2015-02-25
2009-06-05
routine
H + H Facility
closed discharged
3043
30041536
7.975812e+08
2009-07-02
2009-07-02
780.0
2040-11-05
Female
Hispanic
2015-02-25
2009-07-02
routine
H + H Facility
discharged
3044
30041536
8.087864e+08
2009-09-03
2009-09-03
780.0
2040-11-05
Female
Hispanic
2015-02-25
2009-09-03
routine
H + H Facility
closed discharged
3045
30041536
8.101326e+08
2009-09-11
2009-09-14
337.0
2040-11-05
Female
Hispanic
2015-02-25
2009-09-11
routine
H + H Facility
discharged
3046
30041536
8.137577e+08
2009-09-30
2009-09-30
150.0
2040-11-05
Female
Hispanic
2015-02-25
2009-09-30
routine
H + H Facility
closed discharged
3047
30041536
8.137709e+08
2009-09-30
2009-09-30
160.0
2040-11-05
Female
Hispanic
2015-02-25
2009-09-30
routine
H + H Facility
closed discharged
3048
30041536
8.196793e+08
2009-10-29
2009-10-29
780.0
2040-11-05
Female
Hispanic
2015-02-25
2009-10-29
routine
H + H Facility
discharged
3049
30041536
8.215256e+08
2009-11-09
2009-11-09
150.0
2040-11-05
Female
Hispanic
2015-02-25
2009-11-09
routine
H + H Facility
closed discharged
3050
30041536
8.245508e+08
2009-11-24
2009-11-24
160.0
2040-11-05
Female
Hispanic
2015-02-25
2009-11-24
routine
H + H Facility
closed discharged
3051
30041536
8.264425e+08
2009-12-04
2009-12-07
337.0
2040-11-05
Female
Hispanic
2015-02-25
2009-12-04
routine
H + H Facility
closed discharged
3052
30041536
8.296714e+08
2009-12-22
2009-12-22
160.0
2040-11-05
Female
Hispanic
2015-02-25
2009-12-22
routine
H + H Facility
discharged
3053
30041536
8.441168e+08
2010-03-11
2010-03-11
780.0
2040-11-05
Female
Hispanic
2015-02-25
2010-03-11
routine
H + H Facility
closed discharged
3054
30041536
8.471472e+08
2010-03-26
2010-03-29
337.0
2040-11-05
Female
Hispanic
2015-02-25
2010-03-26
routine
H + H Facility
closed discharged
3055
30041536
8.491936e+08
2010-04-07
2010-04-07
150.0
2040-11-05
Female
Hispanic
2015-02-25
2010-04-07
routine
H + H Facility
closed discharged
3056
30041536
8.525848e+08
2010-04-23
2010-04-23
150.0
2040-11-05
Female
Hispanic
2015-02-25
2010-04-23
routine
H + H Facility
closed discharged
3057
30041536
8.530453e+08
2010-04-26
2010-04-26
337.0
2040-11-05
Female
Hispanic
2015-02-25
2010-04-26
routine
H + H Facility
closed discharged
3058
30041536
8.559719e+08
2010-05-11
2010-05-11
290.0
2040-11-05
Female
Hispanic
2015-02-25
2010-05-11
routine
H + H Facility
closed discharged
3059
30041536
8.576444e+08
2010-05-19
2010-05-20
835.0
2040-11-05
Female
Hispanic
2015-02-25
2010-05-19
routine
H + H Facility
discharged
3060
30041536
8.604950e+08
2010-06-03
2010-06-03
780.0
2040-11-05
Female
Hispanic
2015-02-25
2010-06-03
routine
H + H Facility
closed discharged
3061
30041536
8.635518e+08
2010-06-18
2010-06-18
150.0
2040-11-05
Female
Hispanic
2015-02-25
2010-06-18
routine
H + H Facility
closed discharged
3062
30041536
8.649057e+08
2010-06-25
2010-06-28
337.0
2040-11-05
Female
Hispanic
2015-02-25
2010-06-25
routine
H + H Facility
closed discharged
3063
30041536
8.668162e+08
2010-07-07
2010-07-07
835.0
2040-11-05
Female
Hispanic
2015-02-25
2010-07-07
routine
H + H Facility
discharged
3064
30041536
8.736283e+08
2010-08-11
2010-08-11
835.0
2040-11-05
Female
Hispanic
2015-02-25
2010-08-11
routine
H + H Facility
discharged
3065
30041536
8.747062e+08
2010-08-17
2010-08-17
160.0
2040-11-05
Female
Hispanic
2015-02-25
2010-08-17
routine
H + H Facility
discharged
3066
30041536
8.765277e+08
2010-08-26
2010-08-30
780.0
2040-11-05
Female
Hispanic
2015-02-25
2010-08-26
routine
H + H Facility
closed discharged
3067
30041536
8.969683e+08
2010-12-10
2010-12-10
337.0
2040-11-05
Female
Hispanic
2015-02-25
2010-12-10
routine
H + H Facility
closed discharged
3068
30041536
9.040135e+08
2011-01-19
2011-01-19
500.0
2040-11-05
Female
Hispanic
2015-02-25
2011-01-19
routine
H + H Facility
closed discharged
3069
30041536
9.184688e+08
2011-03-31
2011-03-31
780.0
2040-11-05
Female
Hispanic
2015-02-25
2011-03-31
routine
H + H Facility
closed discharged
3070
30041536
9.202042e+08
2011-04-08
2011-04-08
337.0
2040-11-05
Female
Hispanic
2015-02-25
2011-04-08
routine
H + H Facility
closed discharged
3071
30041536
9.293551e+08
2011-05-25
2011-05-25
160.0
2040-11-05
Female
Hispanic
2015-02-25
2011-05-25
routine
H + H Facility
discharged
3072
30041536
9.346405e+08
2011-06-22
2011-06-22
835.0
2040-11-05
Female
Hispanic
2015-02-25
2011-06-22
routine
H + H Facility
discharged
3073
30041536
9.370516e+08
2011-07-06
2011-07-06
835.0
2040-11-05
Female
Hispanic
2015-02-25
2011-07-06
routine
H + H Facility
closed discharged
3074
30041536
9.412861e+08
2011-07-28
2011-07-28
780.0
2040-11-05
Female
Hispanic
2015-02-25
2011-07-28
routine
H + H Facility
closed discharged
3075
30041536
9.547152e+08
2011-10-07
2011-10-12
337.0
2040-11-05
Female
Hispanic
2015-02-25
2011-10-07
routine
H + H Facility
closed discharged
3076
30041536
9.630118e+08
2011-11-22
2011-11-28
160.0
2040-11-05
Female
Hispanic
2015-02-25
2011-11-22
routine
H + H Facility
closed discharged
3077
30041536
9.714828e+08
2012-01-11
2012-01-11
835.0
2040-11-05
Female
Hispanic
2015-02-25
2012-01-11
routine
H + H Facility
closed discharged
3078
30041536
9.731587e+08
2012-01-20
2012-01-20
337.0
2040-11-05
Female
Hispanic
2015-02-25
2012-01-20
routine
H + H Facility
discharged
3079
30041536
9.768739e+08
2012-02-09
2012-02-09
780.0
2040-11-05
Female
Hispanic
2015-02-25
2012-02-09
routine
H + H Facility
closed discharged
3080
30041536
9.792403e+08
2012-02-23
NaT
780.0
2040-11-05
Female
Hispanic
2015-02-25
2012-02-23
NaN
H + H Facility
closed cancelled
3081
30041536
9.806150e+08
2012-03-01
2012-03-01
780.0
2040-11-05
Female
Hispanic
2015-02-25
2012-03-01
routine
H + H Facility
closed discharged
3082
30041536
9.829925e+08
2012-03-14
2012-03-14
835.0
2040-11-05
Female
Hispanic
2015-02-25
2012-03-14
routine
H + H Facility
closed discharged
3083
30041536
9.894313e+08
2012-04-18
2012-04-18
835.0
2040-11-05
Female
Hispanic
2015-02-25
2012-04-18
routine
H + H Facility
closed discharged
3084
30041536
9.907305e+08
2012-04-25
2012-04-25
835.0
2040-11-05
Female
Hispanic
2015-02-25
2012-04-25
routine
H + H Facility
closed discharged
3085
30041536
9.924940e+08
2012-05-04
2012-05-04
337.0
2040-11-05
Female
Hispanic
2015-02-25
2012-05-04
routine
H + H Facility
closed discharged
3086
30041536
9.932442e+08
2012-05-09
2012-05-09
835.0
2040-11-05
Female
Hispanic
2015-02-25
2012-05-09
routine
H + H Facility
discharged
3087
30041536
1.005004e+09
2012-07-17
2012-07-18
500.0
2040-11-05
Female
Hispanic
2015-02-25
2012-07-17
routine
H + H Facility
closed discharged
3088
30041536
1.008133e+09
2012-08-03
2012-08-03
500.0
2040-11-05
Female
Hispanic
2015-02-25
2012-08-03
routine
H + H Facility
closed discharged
3089
30041536
1.009325e+09
2012-08-10
2012-08-13
500.0
2040-11-05
Female
Hispanic
2015-02-25
2012-08-10
routine
H + H Facility
closed discharged
3090
30041536
1.016945e+09
2012-09-25
2012-09-25
337.0
2040-11-05
Female
Hispanic
2015-02-25
2012-09-25
routine
H + H Facility
closed discharged
3091
30041536
1.030917e+09
2012-12-17
2012-12-17
337.0
2040-11-05
Female
Hispanic
2015-02-25
2012-12-17
routine
H + H Facility
closed discharged
3092
30041536
1.046058e+09
2013-03-12
2013-03-12
337.0
2040-11-05
Female
Hispanic
2015-02-25
2013-03-12
routine
H + H Facility
discharged
3093
30041536
1.057608e+09
2013-05-15
2013-05-15
835.0
2040-11-05
Female
Hispanic
2015-02-25
2013-05-15
routine
H + H Facility
discharged
3094
30041536
1.063587e+09
2013-06-18
2013-06-18
337.0
2040-11-05
Female
Hispanic
2015-02-25
2013-06-18
routine
H + H Facility
discharged
3095
30041536
1.079009e+09
2013-09-17
2013-09-17
337.0
2040-11-05
Female
Hispanic
2015-02-25
2013-09-17
routine
H + H Facility
discharged
3096
30041536
1.080876e+09
2013-09-26
2013-09-26
337.0
2040-11-05
Female
Hispanic
2015-02-25
2013-09-26
routine
H + H Facility
closed discharged
3097
30041536
1.084308e+09
2013-10-16
2013-10-16
835.0
2040-11-05
Female
Hispanic
2015-02-25
2013-10-16
routine
H + H Facility
discharged
3098
30041536
1.087242e+09
2013-10-31
2013-10-31
780.0
2040-11-05
Female
Hispanic
2015-02-25
2013-10-31
routine
H + H Facility
closed discharged
3099
30041536
1.092621e+09
2013-12-03
2013-12-03
780.0
2040-11-05
Female
Hispanic
2015-02-25
2013-12-03
routine
H + H Facility
closed discharged
3100
30041536
1.093753e+09
2013-12-10
2013-12-10
337.0
2040-11-05
Female
Hispanic
2015-02-25
2013-12-10
routine
H + H Facility
discharged
3101
30041536
1.095105e+09
2013-12-17
2013-12-19
780.0
2040-11-05
Female
Hispanic
2015-02-25
2013-12-17
routine
H + H Facility
closed discharged
3102
30041536
1.097527e+09
2014-01-03
2014-03-31
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-01-03
routine
H + H Facility
discharged
3103
30041536
1.128899e+09
2014-07-04
2014-07-31
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-07-04
routine
H + H Facility
discharged
3104
30041536
1.135916e+09
2014-08-16
2014-08-26
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-08-15
routine
H + H Facility
discharged
3105
30041536
1.139399e+09
2014-09-08
2014-09-08
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-08
discharged to skilled nursing facility
H + H Facility
discharged
3106
30041536
1.139791e+09
2014-09-10
2014-09-11
835.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-10
routine
H + H Facility
closed discharged
3107
30041536
1.140484e+09
2014-09-15
2014-09-16
684.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-15
routine
H + H Facility
closed discharged
3108
30041536
1.141198e+09
2014-09-18
2014-09-18
780.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-18
routine
H + H Facility
closed discharged
3109
30041536
1.142177e+09
2014-09-23
2014-09-25
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-23
discharge (routine)
H + H Facility
discharged
3110
30041536
1.144800e+09
2014-10-08
2014-10-08
835.0
2040-11-05
Female
Hispanic
2015-02-25
2014-10-08
routine
H + H Facility
closed discharged
3111
30041536
1.145191e+09
2014-10-10
2014-10-23
684.0
2040-11-05
Female
Hispanic
2015-02-25
2014-10-10
routine
H + H Facility
closed discharged
3112
30041536
1.145222e+09
2014-10-10
2014-10-10
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-10-10
discharge (routine)
H + H Facility
closed discharged
3113
30041536
1.146203e+09
2014-10-17
2014-10-17
770.0
2040-11-05
Female
Hispanic
2015-02-25
2014-10-17
routine
H + H Facility
closed discharged
3114
30041536
1.147027e+09
2014-10-22
2014-12-18
0.0
2040-11-05
Female
Hispanic
2015-02-25
2014-10-22
routine
H + H Facility
discharged
3115
30041536
1.148247e+09
2014-09-25
2014-09-25
770.0
2040-11-05
Female
Hispanic
2015-02-25
2014-09-25
routine
H + H Facility
closed discharged
3116
30041536
1.159036e+09
2015-01-08
2015-01-16
0.0
2040-11-05
Female
Hispanic
2015-02-25
2015-01-08
routine
H + H Facility
discharged
3117
30041536
1.160941e+09
2015-01-21
2015-02-12
0.0
2040-11-05
Female
Hispanic
2015-02-25
2015-01-21
routine
H + H Facility
discharged
3118
30041536
1.164736e+09
2015-02-16
2015-02-25
0.0
2040-11-05
Female
Hispanic
2015-02-25
2015-02-16
expired
H + H Facility
discharged
3119
30172352
9.326066e+08
2011-06-10
2011-06-13
395.0
2061-09-23
Female
Hispanic
2015-09-17
2011-06-10
Routine
H + H Facility
closed discharged
3120
30172352
1.113490e+09
2014-04-06
2014-04-06
884.0
2061-09-23
Female
Hispanic
2015-09-17
2014-04-06
discharge (routine)
H + H Facility
closed discharged
3121
30172352
1.137385e+09
2014-08-25
2014-08-26
884.0
2061-09-23
Female
Hispanic
2015-09-17
2014-08-25
discharge (routine)
H + H Facility
closed discharged
3122
30172352
1.151525e+09
2014-11-19
2014-11-26
495.0
2061-09-23
Female
Hispanic
2015-09-17
2014-11-19
routine
H + H Facility
closed discharged
3123
30172352
1.156941e+09
2014-12-23
2015-01-06
4.0
2061-09-23
Female
Hispanic
2015-09-17
2014-12-23
routine
H + H Facility
discharged
3124
30172352
1.159171e+09
2015-01-09
2015-01-12
495.0
2061-09-23
Female
Hispanic
2015-09-17
2015-01-09
routine
H + H Facility
discharged
3125
30172352
1.159263e+09
2015-01-10
2015-01-10
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-01-10
discharge (routine)
H + H Facility
discharged
3126
30172352
1.161255e+09
2015-01-22
2015-01-22
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-01-22
left against medical advice
H + H Facility
closed discharged
3127
30172352
1.162919e+09
2015-02-04
2015-02-04
66.0
2061-09-23
Female
Hispanic
2015-09-17
2015-02-04
routine
H + H Facility
discharged
3128
30172352
1.163361e+09
2015-02-06
2015-02-06
66.0
2061-09-23
Female
Hispanic
2015-09-17
2015-02-06
routine
H + H Facility
discharged
3129
30172352
1.165968e+09
2015-02-24
2015-02-24
495.0
2061-09-23
Female
Hispanic
2015-09-17
2015-02-24
routine
H + H Facility
discharged
3130
30172352
1.166089e+09
2015-02-25
2015-03-01
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-02-24
routine
H + H Facility
discharged
3131
30172352
1.192920e+09
2015-08-08
2015-08-08
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-08-08
left against medical advice
H + H Facility
discharged
3132
30172352
1.192957e+09
2015-08-08
2015-08-09
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-08-08
discharge (routine)
H + H Facility
discharged
3133
30172352
1.196306e+09
2015-08-30
2015-09-10
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-08-30
routine
H + H Facility
discharged
3134
30172352
1.198522e+09
2015-09-15
2015-09-17
0.0
2061-09-23
Female
Hispanic
2015-09-17
2015-09-14
expired
H + H Facility
discharged
0
819392
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-10
NaT
NaN
NaN
NaN
1
15150240
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-11-15
NaT
NaN
NaN
NaN
2
28438144
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-02
NaT
NaN
NaN
NaN
3
28653184
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-30
NaT
NaN
NaN
NaN
4
30041536
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-25
NaT
NaN
NaN
NaN
5
30172352
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-17
NaT
NaN
NaN
NaN
6
44109184
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-29
NaT
NaN
NaN
NaN
7
53420640
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-12-17
NaT
NaN
NaN
NaN
8
63383264
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-25
NaT
NaN
NaN
NaN
9
34720
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-23
NaT
NaN
NaN
NaN
10
297472
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-25
NaT
NaN
NaN
NaN
11
2738848
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-14
NaT
NaN
NaN
NaN
12
2800672
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-03
NaT
NaN
NaN
NaN
13
4337984
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-08
NaT
NaN
NaN
NaN
14
4626720
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-15
NaT
NaN
NaN
NaN
15
6223168
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-28
NaT
NaN
NaN
NaN
16
6674528
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-28
NaT
NaN
NaN
NaN
17
7339360
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-18
NaT
NaN
NaN
NaN
18
7453600
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
19
11111968
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-24
NaT
NaN
NaN
NaN
20
12356512
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-28
NaT
NaN
NaN
NaN
21
13554016
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-25
NaT
NaN
NaN
NaN
22
13752928
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-07
NaT
NaN
NaN
NaN
23
14058688
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-04
NaT
NaN
NaN
NaN
24
15181376
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
25
19353600
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-09
NaT
NaN
NaN
NaN
26
19401984
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-19
NaT
NaN
NaN
NaN
27
20039712
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-25
NaT
NaN
NaN
NaN
28
20292384
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-30
NaT
NaN
NaN
NaN
29
20718880
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-13
NaT
NaN
NaN
NaN
30
21542752
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-26
NaT
NaN
NaN
NaN
31
22386336
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2014-12-11
NaT
NaN
NaN
NaN
32
22495648
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-19
NaT
NaN
NaN
NaN
33
24629024
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-19
NaT
NaN
NaN
NaN
34
24663968
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
35
24803520
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-31
NaT
NaN
NaN
NaN
36
25332832
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-28
NaT
NaN
NaN
NaN
37
26154688
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-08-18
NaT
NaN
NaN
NaN
38
28283808
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-08-02
NaT
NaN
NaN
NaN
39
29722784
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-08-31
NaT
NaN
NaN
NaN
40
29973888
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-03-20
NaT
NaN
NaN
NaN
41
30311680
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-20
NaT
NaN
NaN
NaN
42
30972256
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-15
NaT
NaN
NaN
NaN
43
31249568
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-29
NaT
NaN
NaN
NaN
44
31458112
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-01-13
NaT
NaN
NaN
NaN
45
32354112
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-28
NaT
NaN
NaN
NaN
46
32739168
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-05
NaT
NaN
NaN
NaN
47
33104736
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-20
NaT
NaN
NaN
NaN
48
36042944
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-21
NaT
NaN
NaN
NaN
49
37472512
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-04-19
NaT
NaN
NaN
NaN
50
37511488
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-21
NaT
NaN
NaN
NaN
51
41756288
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-13
NaT
NaN
NaN
NaN
52
42370720
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
53
42519456
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-31
NaT
NaN
NaN
NaN
54
44648352
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-26
NaT
NaN
NaN
NaN
55
45427200
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-29
NaT
NaN
NaN
NaN
56
45721536
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-26
NaT
NaN
NaN
NaN
57
46219936
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-05
NaT
NaN
NaN
NaN
58
51375520
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-27
NaT
NaN
NaN
NaN
59
52633504
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-20
NaT
NaN
NaN
NaN
60
53067840
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-01
NaT
NaN
NaN
NaN
61
53331488
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
62
55521760
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-23
NaT
NaN
NaN
NaN
63
55682368
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-21
NaT
NaN
NaN
NaN
64
56538944
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2014-10-03
NaT
NaN
NaN
NaN
65
60000864
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-04-27
NaT
NaN
NaN
NaN
66
60040960
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-02-22
NaT
NaN
NaN
NaN
67
61997600
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-20
NaT
NaN
NaN
NaN
68
64368864
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-11
NaT
NaN
NaN
NaN
69
64906912
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-20
NaT
NaN
NaN
NaN
70
65664032
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-29
NaT
NaN
NaN
NaN
71
66123008
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-01-14
NaT
NaN
NaN
NaN
72
66242400
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-09
NaT
NaN
NaN
NaN
73
67152960
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-27
NaT
NaN
NaN
NaN
74
67378976
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-11-01
NaT
NaN
NaN
NaN
75
69495776
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-30
NaT
NaN
NaN
NaN
76
71635200
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-02
NaT
NaN
NaN
NaN
77
71929984
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-08
NaT
NaN
NaN
NaN
78
71936032
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-21
NaT
NaN
NaN
NaN
79
72760128
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-16
NaT
NaN
NaN
NaN
80
75103616
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-16
NaT
NaN
NaN
NaN
81
76259232
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-06-18
NaT
NaN
NaN
NaN
82
76640704
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-07-28
NaT
NaN
NaN
NaN
83
76682144
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-18
NaT
NaN
NaN
NaN
84
77370048
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-10
NaT
NaN
NaN
NaN
85
77373408
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-08-10
NaT
NaN
NaN
NaN
86
80459680
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-11-01
NaT
NaN
NaN
NaN
87
82681088
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-21
NaT
NaN
NaN
NaN
88
83874784
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-05-20
NaT
NaN
NaN
NaN
89
83918912
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-28
NaT
NaN
NaN
NaN
90
84279776
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-22
NaT
NaN
NaN
NaN
91
84284032
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-16
NaT
NaN
NaN
NaN
92
84789600
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-25
NaT
NaN
NaN
NaN
93
85254400
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2014-10-08
NaT
NaN
NaN
NaN
94
85384320
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2014-12-18
NaT
NaN
NaN
NaN
95
85620416
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-10-20
NaT
NaN
NaN
NaN
96
86567936
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2015-09-24
NaT
NaN
NaN
NaN
97
86708832
NaN
NaT
NaT
NaN
NaT
NaN
NaN
2005-02-09
NaT
NaN
NaN
NaN

Number of Patients who died:
We transformed the data to find out for how many patients the value of "Date_Of_Death" column is not null and then , we calculate the unique records to check how many patients have died.
In [25]:


deadpatientData['ID'].nunique()


Out[25]:
98
In [26]:


deadpatientData['ID'].value_counts()


Out[26]:
30041536    206
28438144     95
819392       18
30172352     17
76682144      1
82681088      1
20292384      1
12356512      1
13554016      1
71929984      1
55682368      1
52633504      1
15150240      1
13752928      1
72760128      1
22386336      1
29722784      1
45721536      1
20718880      1
33104736      1
84284032      1
22495648      1
77373408      1
63383264      1
76259232      1
85620416      1
11111968      1
6674528       1
19353600      1
24629024      1
85384320      1
7453600       1
64906912      1
2800672       1
37472512      1
41756288      1
20039712      1
56538944      1
71936032      1
31249568      1
15181376      1
66123008      1
53067840      1
42370720      1
32739168      1
83918912      1
51375520      1
69495776      1
53420640      1
75103616      1
67378976      1
64368864      1
86708832      1
86567936      1
44648352      1
30972256      1
24663968      1
26154688      1
34720         1
30311680      1
65664032      1
31458112      1
84789600      1
80459680      1
67152960      1
24803520      1
55521760      1
60040960      1
53331488      1
6223168       1
14058688      1
46219936      1
76640704      1
44109184      1
37511488      1
25332832      1
71635200      1
36042944      1
2738848       1
60000864      1
42519456      1
19401984      1
28283808      1
77370048      1
84279776      1
4626720       1
32354112      1
61997600      1
29973888      1
66242400      1
4337984       1
45427200      1
85254400      1
21542752      1
28653184      1
83874784      1
7339360       1
297472        1
Name: ID, dtype: int64
In [27]:


deadpatientData.isna().sum()


Out[27]:
ID                              0
VISIT_ID                       98
ADMISSION_DATE_TIME            98
DISCHARGE_DATE_TIME           105
CLINIC_CODE                   102
BIRTHDATE                      98
SEX                            98
RACE                           98
DATE_OF_DEATH                   0
VISIT_ACTIVATION_DATE_TIME     98
DISCHARGE_TYPE                105
FACILITY                       98
VISIT_STATUS                   98
dtype: int64

Subsetting the Data:
After finding how many patients have died, the next step towards the problem statement is to calculate the number of days between the last visit of the patient and the date of the death.
In [94]:


df = deadpatientData[['ID','ADMISSION_DATE_TIME','DATE_OF_DEATH']]
df.index = range(len(df.index))
df.head()


Out[94]:

ID
ADMISSION_DATE_TIME
DATE_OF_DEATH
0
28438144
2000-06-13
2015-07-02
1
28438144
2000-12-22
2015-07-02
2
28438144
2002-09-03
2015-07-02
3
28438144
2003-03-04
2015-07-02
4
28438144
2003-10-09
2015-07-02

We can easily notice that some of the patient IDs contribute the most to this data. These are reduntant values and we need to get rid of them.
In [29]:


df['ID'].value_counts()


Out[29]:
30041536    206
28438144     95
819392       18
30172352     17
76682144      1
82681088      1
20292384      1
12356512      1
13554016      1
71929984      1
55682368      1
52633504      1
15150240      1
13752928      1
72760128      1
22386336      1
29722784      1
45721536      1
20718880      1
33104736      1
84284032      1
22495648      1
77373408      1
63383264      1
76259232      1
85620416      1
11111968      1
6674528       1
19353600      1
24629024      1
85384320      1
7453600       1
64906912      1
2800672       1
37472512      1
41756288      1
20039712      1
56538944      1
71936032      1
31249568      1
15181376      1
66123008      1
53067840      1
42370720      1
32739168      1
83918912      1
51375520      1
69495776      1
53420640      1
75103616      1
67378976      1
64368864      1
86708832      1
86567936      1
44648352      1
30972256      1
24663968      1
26154688      1
34720         1
30311680      1
65664032      1
31458112      1
84789600      1
80459680      1
67152960      1
24803520      1
55521760      1
60040960      1
53331488      1
6223168       1
14058688      1
46219936      1
76640704      1
44109184      1
37511488      1
25332832      1
71635200      1
36042944      1
2738848       1
60000864      1
42519456      1
19401984      1
28283808      1
77370048      1
84279776      1
4626720       1
32354112      1
61997600      1
29973888      1
66242400      1
4337984       1
45427200      1
85254400      1
21542752      1
28653184      1
83874784      1
7339360       1
297472        1
Name: ID, dtype: int64

Check the duplicate values in the ID column. We will only consider the last visit of the patient because that's what the problem statement requires.
In [30]:


print(df)



           ID ADMISSION_DATE_TIME DATE_OF_DEATH
0    28438144          2000-06-13    2015-07-02
1    28438144          2000-12-22    2015-07-02
2    28438144          2002-09-03    2015-07-02
3    28438144          2003-03-04    2015-07-02
4    28438144          2003-10-09    2015-07-02
5    28438144          2003-10-20    2015-07-02
6    28438144          2003-10-28    2015-07-02
7    28438144          2003-11-01    2015-07-02
8    28438144          2003-12-02    2015-07-02
9    28438144          2003-12-12    2015-07-02
10   28438144          2003-12-17    2015-07-02
11   28438144          2004-02-13    2015-07-02
12   28438144          2004-02-12    2015-07-02
13   28438144          2004-02-25    2015-07-02
14   28438144          2004-04-26    2015-07-02
15   28438144          2004-05-05    2015-07-02
16   28438144          2004-05-12    2015-07-02
17   28438144          2004-07-10    2015-07-02
18   28438144          2004-09-21    2015-07-02
19   28438144          2004-12-01    2015-07-02
20   28438144          2004-12-08    2015-07-02
21   28438144          2005-04-07    2015-07-02
22   28438144          2005-04-30    2015-07-02
23   28438144          2005-05-02    2015-07-02
24   28438144          2005-10-25    2015-07-02
25   28438144          2005-10-26    2015-07-02
26   28438144          2005-11-02    2015-07-02
27   28438144          2006-01-26    2015-07-02
28   28438144          2006-04-03    2015-07-02
29   28438144          2006-11-06    2015-07-02
30   28438144          2006-11-13    2015-07-02
31   28438144          2006-11-20    2015-07-02
32   28438144          2007-02-06    2015-07-02
33   28438144          2007-02-07    2015-07-02
34   28438144          2007-03-13    2015-07-02
35   28438144          2007-03-27    2015-07-02
36   28438144          2007-05-22    2015-07-02
37   28438144          2007-06-12    2015-07-02
38   28438144          2007-06-14    2015-07-02
39   28438144          2007-06-25    2015-07-02
40   28438144          2011-01-14    2015-07-02
41   28438144          2011-04-02    2015-07-02
42   28438144          2011-12-17    2015-07-02
43   28438144          2012-01-10    2015-07-02
44   28438144          2012-01-12    2015-07-02
45   28438144          2012-01-19    2015-07-02
46   28438144          2012-01-24    2015-07-02
47   28438144          2012-02-14    2015-07-02
48   28438144          2012-02-14    2015-07-02
49   28438144          2012-02-23    2015-07-02
50   28438144          2012-03-13    2015-07-02
51   28438144          2012-03-15    2015-07-02
52   28438144          2012-03-20    2015-07-02
53   28438144          2012-03-27    2015-07-02
54   28438144          2012-03-29    2015-07-02
55   28438144          2012-04-05    2015-07-02
56   28438144          2012-04-04    2015-07-02
57   28438144          2012-04-09    2015-07-02
58   28438144          2012-04-12    2015-07-02
59   28438144          2012-04-20    2015-07-02
60   28438144          2012-05-08    2015-07-02
61   28438144          2012-05-18    2015-07-02
62   28438144          2012-08-27    2015-07-02
63   28438144          2012-09-07    2015-07-02
64   28438144          2012-09-13    2015-07-02
65   28438144          2012-09-13    2015-07-02
66   28438144          2012-09-13    2015-07-02
67   28438144          2012-09-26    2015-07-02
68   28438144          2012-09-28    2015-07-02
69   28438144          2012-10-09    2015-07-02
70   28438144          2012-10-18    2015-07-02
71   28438144          2012-10-22    2015-07-02
72   28438144          2012-11-15    2015-07-02
73   28438144          2012-11-28    2015-07-02
74   28438144          2013-01-10    2015-07-02
75   28438144          2013-01-22    2015-07-02
76   28438144          2013-01-24    2015-07-02
77   28438144          2013-01-25    2015-07-02
78   28438144          2013-01-27    2015-07-02
79   28438144          2013-07-02    2015-07-02
80   28438144          2014-02-28    2015-07-02
81   28438144          2014-03-28    2015-07-02
82   28438144          2014-05-13    2015-07-02
83   28438144          2014-07-29    2015-07-02
84   28438144          2014-07-12    2015-07-02
85   28438144          2014-07-13    2015-07-02
86   28438144          2014-10-12    2015-07-02
87   28438144          2014-10-15    2015-07-02
88   28438144          2014-10-28    2015-07-02
89   28438144          2015-04-08    2015-07-02
90   28438144          2015-05-05    2015-07-02
91   28438144          2015-06-03    2015-07-02
92   28438144          2015-06-11    2015-07-02
93   28438144          2015-06-21    2015-07-02
94   30041536          1995-01-17    2015-02-25
95   30041536          2000-10-05    2015-02-25
96   30041536          2001-01-09    2015-02-25
97   30041536          2001-07-30    2015-02-25
98   30041536          2001-08-07    2015-02-25
99   30041536          2002-02-11    2015-02-25
100  30041536          2002-03-04    2015-02-25
101  30041536          2002-03-25    2015-02-25
102  30041536          2002-09-03    2015-02-25
103  30041536          2002-09-04    2015-02-25
104  30041536          2003-01-28    2015-02-25
105  30041536          2003-04-14    2015-02-25
106  30041536          2003-05-01    2015-02-25
107  30041536          2003-05-08    2015-02-25
108  30041536          2003-06-10    2015-02-25
109  30041536          2003-08-11    2015-02-25
110  30041536          2003-08-27    2015-02-25
111  30041536          2003-09-04    2015-02-25
112  30041536          2003-09-11    2015-02-25
113  30041536          2003-09-25    2015-02-25
114  30041536          2003-10-14    2015-02-25
115  30041536          2003-10-21    2015-02-25
116  30041536          2003-11-12    2015-02-25
117  30041536          2004-01-16    2015-02-25
118  30041536          2004-01-21    2015-02-25
119  30041536          2004-02-05    2015-02-25
120  30041536          2004-02-19    2015-02-25
121  30041536          2004-02-27    2015-02-25
122  30041536          2004-03-09    2015-02-25
123  30041536          2004-03-17    2015-02-25
124  30041536          2004-03-25    2015-02-25
125  30041536          2004-04-20    2015-02-25
126  30041536          2004-05-27    2015-02-25
127  30041536          2004-06-01    2015-02-25
128  30041536          2004-06-04    2015-02-25
129  30041536          2004-06-09    2015-02-25
130  30041536          2004-06-10    2015-02-25
131  30041536          2004-06-14    2015-02-25
132  30041536          2004-06-16    2015-02-25
133  30041536          2004-08-12    2015-02-25
134  30041536          2004-08-19    2015-02-25
135  30041536          2004-08-24    2015-02-25
136  30041536          2004-09-02    2015-02-25
137  30041536          2004-09-23    2015-02-25
138  30041536          2004-10-06    2015-02-25
139  30041536          2004-10-18    2015-02-25
140  30041536          2004-11-17    2015-02-25
141  30041536          2004-11-23    2015-02-25
142  30041536          2004-11-30    2015-02-25
143  30041536          2004-12-21    2015-02-25
144  30041536          2005-03-08    2015-02-25
145  30041536          2005-03-10    2015-02-25
146  30041536          2005-03-23    2015-02-25
147  30041536          2005-04-19    2015-02-25
148  30041536          2005-04-20    2015-02-25
149  30041536          2005-05-26    2015-02-25
150  30041536          2005-06-14    2015-02-25
151  30041536          2005-06-16    2015-02-25
152  30041536          2005-07-12    2015-02-25
153  30041536          2005-07-14    2015-02-25
154  30041536          2005-07-18    2015-02-25
155  30041536          2005-08-09    2015-02-25
156  30041536          2005-09-28    2015-02-25
157  30041536          2005-11-03    2015-02-25
158  30041536          2005-11-15    2015-02-25
159  30041536          2005-11-22    2015-02-25
160  30041536          2005-11-29    2015-02-25
161  30041536          2006-03-23    2015-02-25
162  30041536          2006-03-28    2015-02-25
163  30041536          2006-03-28    2015-02-25
164  30041536          2006-04-19    2015-02-25
165  30041536          2006-04-27    2015-02-25
166  30041536          2006-05-02    2015-02-25
167  30041536          2006-05-11    2015-02-25
168  30041536          2006-05-16    2015-02-25
169  30041536          2006-07-18    2015-02-25
170  30041536          2006-08-29    2015-02-25
171  30041536          2006-09-05    2015-02-25
172  30041536          2006-10-06    2015-02-25
173  30041536          2006-10-10    2015-02-25
174  30041536          2006-10-11    2015-02-25
175  30041536          2007-01-16    2015-02-25
176  30041536          2007-01-30    2015-02-25
177  30041536          2007-03-23    2015-02-25
178  30041536          2007-03-30    2015-02-25
179  30041536          2007-05-02    2015-02-25
180  30041536          2007-05-10    2015-02-25
181  30041536          2007-06-05    2015-02-25
182  30041536          2007-06-12    2015-02-25
183  30041536          2007-06-12    2015-02-25
184  30041536          2007-07-12    2015-02-25
185  30041536          2007-07-23    2015-02-25
186  30041536          2007-08-16    2015-02-25
187  30041536          2007-09-25    2015-02-25
188  30041536          2007-10-11    2015-02-25
189  30041536          2007-10-16    2015-02-25
190  30041536          2007-11-01    2015-02-25
191  30041536          2007-11-08    2015-02-25
192  30041536          2007-11-20    2015-02-25
193  30041536          2007-12-18    2015-02-25
194  30041536          2007-12-18    2015-02-25
195  30041536          2008-03-10    2015-02-25
196  30041536          2008-03-20    2015-02-25
197  30041536          2008-03-27    2015-02-25
198  30041536          2008-04-10    2015-02-25
199  30041536          2008-04-16    2015-02-25
200  30041536          2008-05-02    2015-02-25
201  30041536          2008-05-14    2015-02-25
202  30041536          2008-06-25    2015-02-25
203  30041536          2008-06-25    2015-02-25
204  30041536          2008-08-14    2015-02-25
205  30041536          2008-08-21    2015-02-25
206  30041536          2008-09-09    2015-02-25
207  30041536          2008-09-24    2015-02-25
208  30041536          2008-09-24    2015-02-25
209  30041536          2008-09-30    2015-02-25
210  30041536          2008-10-09    2015-02-25
211    819392          2002-05-18    2015-07-10
212    819392          2013-05-20    2015-07-10
213    819392          2013-05-28    2015-07-10
214    819392          2013-06-18    2015-07-10
215    819392          2013-10-01    2015-07-10
216    819392          2014-04-26    2015-07-10
217    819392          2014-04-30    2015-07-10
218    819392          2014-07-20    2015-07-10
219    819392          2014-07-23    2015-07-10
220    819392          2014-07-24    2015-07-10
221    819392          2014-10-16    2015-07-10
222    819392          2014-11-14    2015-07-10
223    819392          2015-03-11    2015-07-10
224    819392          2015-05-04    2015-07-10
225    819392          2015-05-06    2015-07-10
226    819392          2015-06-02    2015-07-10
227    819392          2015-07-09    2015-07-10
228  30041536          2008-10-23    2015-02-25
229  30041536          2008-12-18    2015-02-25
230  30041536          2008-12-23    2015-02-25
231  30041536          2008-12-23    2015-02-25
232  30041536          2008-12-30    2015-02-25
233  30041536          2009-01-27    2015-02-25
234  30041536          2009-02-02    2015-02-25
235  30041536          2009-02-10    2015-02-25
236  30041536          2009-03-18    2015-02-25
237  30041536          2009-04-16    2015-02-25
238  30041536          2009-05-05    2015-02-25
239  30041536          2009-06-05    2015-02-25
240  30041536          2009-07-02    2015-02-25
241  30041536          2009-09-03    2015-02-25
242  30041536          2009-09-11    2015-02-25
243  30041536          2009-09-30    2015-02-25
244  30041536          2009-09-30    2015-02-25
245  30041536          2009-10-29    2015-02-25
246  30041536          2009-11-09    2015-02-25
247  30041536          2009-11-24    2015-02-25
248  30041536          2009-12-04    2015-02-25
249  30041536          2009-12-22    2015-02-25
250  30041536          2010-03-11    2015-02-25
251  30041536          2010-03-26    2015-02-25
252  30041536          2010-04-07    2015-02-25
253  30041536          2010-04-23    2015-02-25
254  30041536          2010-04-26    2015-02-25
255  30041536          2010-05-11    2015-02-25
256  30041536          2010-05-19    2015-02-25
257  30041536          2010-06-03    2015-02-25
258  30041536          2010-06-18    2015-02-25
259  30041536          2010-06-25    2015-02-25
260  30041536          2010-07-07    2015-02-25
261  30041536          2010-08-11    2015-02-25
262  30041536          2010-08-17    2015-02-25
263  30041536          2010-08-26    2015-02-25
264  30041536          2010-12-10    2015-02-25
265  30041536          2011-01-19    2015-02-25
266  30041536          2011-03-31    2015-02-25
267  30041536          2011-04-08    2015-02-25
268  30041536          2011-05-25    2015-02-25
269  30041536          2011-06-22    2015-02-25
270  30041536          2011-07-06    2015-02-25
271  30041536          2011-07-28    2015-02-25
272  30041536          2011-10-07    2015-02-25
273  30041536          2011-11-22    2015-02-25
274  30041536          2012-01-11    2015-02-25
275  30041536          2012-01-20    2015-02-25
276  30041536          2012-02-09    2015-02-25
277  30041536          2012-02-23    2015-02-25
278  30041536          2012-03-01    2015-02-25
279  30041536          2012-03-14    2015-02-25
280  30041536          2012-04-18    2015-02-25
281  30041536          2012-04-25    2015-02-25
282  30041536          2012-05-04    2015-02-25
283  30041536          2012-05-09    2015-02-25
284  30041536          2012-07-17    2015-02-25
285  30041536          2012-08-03    2015-02-25
286  30041536          2012-08-10    2015-02-25
287  30041536          2012-09-25    2015-02-25
288  30041536          2012-12-17    2015-02-25
289  30041536          2013-03-12    2015-02-25
290  30041536          2013-05-15    2015-02-25
291  30041536          2013-06-18    2015-02-25
292  30041536          2013-09-17    2015-02-25
293  30041536          2013-09-26    2015-02-25
294  30041536          2013-10-16    2015-02-25
295  30041536          2013-10-31    2015-02-25
296  30041536          2013-12-03    2015-02-25
297  30041536          2013-12-10    2015-02-25
298  30041536          2013-12-17    2015-02-25
299  30041536          2014-01-03    2015-02-25
300  30041536          2014-07-04    2015-02-25
301  30041536          2014-08-16    2015-02-25
302  30041536          2014-09-08    2015-02-25
303  30041536          2014-09-10    2015-02-25
304  30041536          2014-09-15    2015-02-25
305  30041536          2014-09-18    2015-02-25
306  30041536          2014-09-23    2015-02-25
307  30041536          2014-10-08    2015-02-25
308  30041536          2014-10-10    2015-02-25
309  30041536          2014-10-10    2015-02-25
310  30041536          2014-10-17    2015-02-25
311  30041536          2014-10-22    2015-02-25
312  30041536          2014-09-25    2015-02-25
313  30041536          2015-01-08    2015-02-25
314  30041536          2015-01-21    2015-02-25
315  30041536          2015-02-16    2015-02-25
316  30172352          2011-06-10    2015-09-17
317  30172352          2014-04-06    2015-09-17
318  30172352          2014-08-25    2015-09-17
319  30172352          2014-11-19    2015-09-17
320  30172352          2014-12-23    2015-09-17
321  30172352          2015-01-09    2015-09-17
322  30172352          2015-01-10    2015-09-17
323  30172352          2015-01-22    2015-09-17
324  30172352          2015-02-04    2015-09-17
325  30172352          2015-02-06    2015-09-17
326  30172352          2015-02-24    2015-09-17
327  30172352          2015-02-25    2015-09-17
328  30172352          2015-08-08    2015-09-17
329  30172352          2015-08-08    2015-09-17
330  30172352          2015-08-30    2015-09-17
331  30172352          2015-09-15    2015-09-17
332    819392                 NaT    2015-07-10
333  15150240                 NaT    2015-11-15
334  28438144                 NaT    2015-07-02
335  28653184                 NaT    2015-10-30
336  30041536                 NaT    2015-02-25
337  30172352                 NaT    2015-09-17
338  44109184                 NaT    2015-10-29
339  53420640                 NaT    2015-12-17
340  63383264                 NaT    2015-06-25
341     34720                 NaT    2015-10-23
342    297472                 NaT    2015-09-25
343   2738848                 NaT    2015-07-14
344   2800672                 NaT    2015-05-03
345   4337984                 NaT    2015-07-08
346   4626720                 NaT    2015-10-15
347   6223168                 NaT    2015-02-28
348   6674528                 NaT    2015-09-28
349   7339360                 NaT    2015-09-18
350   7453600                 NaT    2015-10-27
351  11111968                 NaT    2015-07-24
352  12356512                 NaT    2015-10-28
353  13554016                 NaT    2015-10-25
354  13752928                 NaT    2015-05-07
355  14058688                 NaT    2015-02-04
356  15181376                 NaT    2015-10-27
357  19353600                 NaT    2015-02-09
358  19401984                 NaT    2015-05-19
359  20039712                 NaT    2015-06-25
360  20292384                 NaT    2015-09-30
361  20718880                 NaT    2015-10-13
362  21542752                 NaT    2015-10-26
363  22386336                 NaT    2014-12-11
364  22495648                 NaT    2015-10-19
365  24629024                 NaT    2015-10-19
366  24663968                 NaT    2015-10-27
367  24803520                 NaT    2015-10-31
368  25332832                 NaT    2015-09-28
369  26154688                 NaT    2015-08-18
370  28283808                 NaT    2015-08-02
371  29722784                 NaT    2015-08-31
372  29973888                 NaT    2015-03-20
373  30311680                 NaT    2015-10-20
374  30972256                 NaT    2015-10-15
375  31249568                 NaT    2015-07-29
376  31458112                 NaT    2015-01-13
377  32354112                 NaT    2015-10-28
378  32739168                 NaT    2015-10-05
379  33104736                 NaT    2015-10-20
380  36042944                 NaT    2015-09-21
381  37472512                 NaT    2015-04-19
382  37511488                 NaT    2015-10-21
383  41756288                 NaT    2015-02-13
384  42370720                 NaT    2015-10-27
385  42519456                 NaT    2015-10-31
386  44648352                 NaT    2015-09-26
387  45427200                 NaT    2015-10-29
388  45721536                 NaT    2015-05-26
389  46219936                 NaT    2015-02-05
390  51375520                 NaT    2015-06-27
391  52633504                 NaT    2015-10-20
392  53067840                 NaT    2015-10-01
393  53331488                 NaT    2015-10-27
394  55521760                 NaT    2015-07-23
395  55682368                 NaT    2015-10-21
396  56538944                 NaT    2014-10-03
397  60000864                 NaT    2015-04-27
398  60040960                 NaT    2015-02-22
399  61997600                 NaT    2015-09-20
400  64368864                 NaT    2015-05-11
401  64906912                 NaT    2015-09-20
402  65664032                 NaT    2015-06-29
403  66123008                 NaT    2015-01-14
404  66242400                 NaT    2015-06-09
405  67152960                 NaT    2015-10-27
406  67378976                 NaT    2015-11-01
407  69495776                 NaT    2015-10-30
408  71635200                 NaT    2015-07-02
409  71929984                 NaT    2015-06-08
410  71936032                 NaT    2015-06-21
411  72760128                 NaT    2015-09-16
412  75103616                 NaT    2015-05-16
413  76259232                 NaT    2015-06-18
414  76640704                 NaT    2015-07-28
415  76682144                 NaT    2015-10-18
416  77370048                 NaT    2015-09-10
417  77373408                 NaT    2015-08-10
418  80459680                 NaT    2015-11-01
419  82681088                 NaT    2015-10-21
420  83874784                 NaT    2015-05-20
421  83918912                 NaT    2015-10-28
422  84279776                 NaT    2015-10-22
423  84284032                 NaT    2015-10-16
424  84789600                 NaT    2015-10-25
425  85254400                 NaT    2014-10-08
426  85384320                 NaT    2014-12-18
427  85620416                 NaT    2015-10-20
428  86567936                 NaT    2015-09-24
429  86708832                 NaT    2005-02-09
In [34]:


xtrain = df.loc[df['ADMISSION_DATE_TIME'].notnull(), ['ID','DATE_OF_DEATH','ADMISSION_DATE_TIME']]
print(xtrain)



           ID DATE_OF_DEATH ADMISSION_DATE_TIME
0    28438144    2015-07-02          2000-06-13
1    28438144    2015-07-02          2000-12-22
2    28438144    2015-07-02          2002-09-03
3    28438144    2015-07-02          2003-03-04
4    28438144    2015-07-02          2003-10-09
5    28438144    2015-07-02          2003-10-20
6    28438144    2015-07-02          2003-10-28
7    28438144    2015-07-02          2003-11-01
8    28438144    2015-07-02          2003-12-02
9    28438144    2015-07-02          2003-12-12
10   28438144    2015-07-02          2003-12-17
11   28438144    2015-07-02          2004-02-13
12   28438144    2015-07-02          2004-02-12
13   28438144    2015-07-02          2004-02-25
14   28438144    2015-07-02          2004-04-26
15   28438144    2015-07-02          2004-05-05
16   28438144    2015-07-02          2004-05-12
17   28438144    2015-07-02          2004-07-10
18   28438144    2015-07-02          2004-09-21
19   28438144    2015-07-02          2004-12-01
20   28438144    2015-07-02          2004-12-08
21   28438144    2015-07-02          2005-04-07
22   28438144    2015-07-02          2005-04-30
23   28438144    2015-07-02          2005-05-02
24   28438144    2015-07-02          2005-10-25
25   28438144    2015-07-02          2005-10-26
26   28438144    2015-07-02          2005-11-02
27   28438144    2015-07-02          2006-01-26
28   28438144    2015-07-02          2006-04-03
29   28438144    2015-07-02          2006-11-06
30   28438144    2015-07-02          2006-11-13
31   28438144    2015-07-02          2006-11-20
32   28438144    2015-07-02          2007-02-06
33   28438144    2015-07-02          2007-02-07
34   28438144    2015-07-02          2007-03-13
35   28438144    2015-07-02          2007-03-27
36   28438144    2015-07-02          2007-05-22
37   28438144    2015-07-02          2007-06-12
38   28438144    2015-07-02          2007-06-14
39   28438144    2015-07-02          2007-06-25
40   28438144    2015-07-02          2011-01-14
41   28438144    2015-07-02          2011-04-02
42   28438144    2015-07-02          2011-12-17
43   28438144    2015-07-02          2012-01-10
44   28438144    2015-07-02          2012-01-12
45   28438144    2015-07-02          2012-01-19
46   28438144    2015-07-02          2012-01-24
47   28438144    2015-07-02          2012-02-14
48   28438144    2015-07-02          2012-02-14
49   28438144    2015-07-02          2012-02-23
50   28438144    2015-07-02          2012-03-13
51   28438144    2015-07-02          2012-03-15
52   28438144    2015-07-02          2012-03-20
53   28438144    2015-07-02          2012-03-27
54   28438144    2015-07-02          2012-03-29
55   28438144    2015-07-02          2012-04-05
56   28438144    2015-07-02          2012-04-04
57   28438144    2015-07-02          2012-04-09
58   28438144    2015-07-02          2012-04-12
59   28438144    2015-07-02          2012-04-20
60   28438144    2015-07-02          2012-05-08
61   28438144    2015-07-02          2012-05-18
62   28438144    2015-07-02          2012-08-27
63   28438144    2015-07-02          2012-09-07
64   28438144    2015-07-02          2012-09-13
65   28438144    2015-07-02          2012-09-13
66   28438144    2015-07-02          2012-09-13
67   28438144    2015-07-02          2012-09-26
68   28438144    2015-07-02          2012-09-28
69   28438144    2015-07-02          2012-10-09
70   28438144    2015-07-02          2012-10-18
71   28438144    2015-07-02          2012-10-22
72   28438144    2015-07-02          2012-11-15
73   28438144    2015-07-02          2012-11-28
74   28438144    2015-07-02          2013-01-10
75   28438144    2015-07-02          2013-01-22
76   28438144    2015-07-02          2013-01-24
77   28438144    2015-07-02          2013-01-25
78   28438144    2015-07-02          2013-01-27
79   28438144    2015-07-02          2013-07-02
80   28438144    2015-07-02          2014-02-28
81   28438144    2015-07-02          2014-03-28
82   28438144    2015-07-02          2014-05-13
83   28438144    2015-07-02          2014-07-29
84   28438144    2015-07-02          2014-07-12
85   28438144    2015-07-02          2014-07-13
86   28438144    2015-07-02          2014-10-12
87   28438144    2015-07-02          2014-10-15
88   28438144    2015-07-02          2014-10-28
89   28438144    2015-07-02          2015-04-08
90   28438144    2015-07-02          2015-05-05
91   28438144    2015-07-02          2015-06-03
92   28438144    2015-07-02          2015-06-11
93   28438144    2015-07-02          2015-06-21
94   30041536    2015-02-25          1995-01-17
95   30041536    2015-02-25          2000-10-05
96   30041536    2015-02-25          2001-01-09
97   30041536    2015-02-25          2001-07-30
98   30041536    2015-02-25          2001-08-07
99   30041536    2015-02-25          2002-02-11
100  30041536    2015-02-25          2002-03-04
101  30041536    2015-02-25          2002-03-25
102  30041536    2015-02-25          2002-09-03
103  30041536    2015-02-25          2002-09-04
104  30041536    2015-02-25          2003-01-28
105  30041536    2015-02-25          2003-04-14
106  30041536    2015-02-25          2003-05-01
107  30041536    2015-02-25          2003-05-08
108  30041536    2015-02-25          2003-06-10
109  30041536    2015-02-25          2003-08-11
110  30041536    2015-02-25          2003-08-27
111  30041536    2015-02-25          2003-09-04
112  30041536    2015-02-25          2003-09-11
113  30041536    2015-02-25          2003-09-25
114  30041536    2015-02-25          2003-10-14
115  30041536    2015-02-25          2003-10-21
116  30041536    2015-02-25          2003-11-12
117  30041536    2015-02-25          2004-01-16
118  30041536    2015-02-25          2004-01-21
119  30041536    2015-02-25          2004-02-05
120  30041536    2015-02-25          2004-02-19
121  30041536    2015-02-25          2004-02-27
122  30041536    2015-02-25          2004-03-09
123  30041536    2015-02-25          2004-03-17
124  30041536    2015-02-25          2004-03-25
125  30041536    2015-02-25          2004-04-20
126  30041536    2015-02-25          2004-05-27
127  30041536    2015-02-25          2004-06-01
128  30041536    2015-02-25          2004-06-04
129  30041536    2015-02-25          2004-06-09
130  30041536    2015-02-25          2004-06-10
131  30041536    2015-02-25          2004-06-14
132  30041536    2015-02-25          2004-06-16
133  30041536    2015-02-25          2004-08-12
134  30041536    2015-02-25          2004-08-19
135  30041536    2015-02-25          2004-08-24
136  30041536    2015-02-25          2004-09-02
137  30041536    2015-02-25          2004-09-23
138  30041536    2015-02-25          2004-10-06
139  30041536    2015-02-25          2004-10-18
140  30041536    2015-02-25          2004-11-17
141  30041536    2015-02-25          2004-11-23
142  30041536    2015-02-25          2004-11-30
143  30041536    2015-02-25          2004-12-21
144  30041536    2015-02-25          2005-03-08
145  30041536    2015-02-25          2005-03-10
146  30041536    2015-02-25          2005-03-23
147  30041536    2015-02-25          2005-04-19
148  30041536    2015-02-25          2005-04-20
149  30041536    2015-02-25          2005-05-26
150  30041536    2015-02-25          2005-06-14
151  30041536    2015-02-25          2005-06-16
152  30041536    2015-02-25          2005-07-12
153  30041536    2015-02-25          2005-07-14
154  30041536    2015-02-25          2005-07-18
155  30041536    2015-02-25          2005-08-09
156  30041536    2015-02-25          2005-09-28
157  30041536    2015-02-25          2005-11-03
158  30041536    2015-02-25          2005-11-15
159  30041536    2015-02-25          2005-11-22
160  30041536    2015-02-25          2005-11-29
161  30041536    2015-02-25          2006-03-23
162  30041536    2015-02-25          2006-03-28
163  30041536    2015-02-25          2006-03-28
164  30041536    2015-02-25          2006-04-19
165  30041536    2015-02-25          2006-04-27
166  30041536    2015-02-25          2006-05-02
167  30041536    2015-02-25          2006-05-11
168  30041536    2015-02-25          2006-05-16
169  30041536    2015-02-25          2006-07-18
170  30041536    2015-02-25          2006-08-29
171  30041536    2015-02-25          2006-09-05
172  30041536    2015-02-25          2006-10-06
173  30041536    2015-02-25          2006-10-10
174  30041536    2015-02-25          2006-10-11
175  30041536    2015-02-25          2007-01-16
176  30041536    2015-02-25          2007-01-30
177  30041536    2015-02-25          2007-03-23
178  30041536    2015-02-25          2007-03-30
179  30041536    2015-02-25          2007-05-02
180  30041536    2015-02-25          2007-05-10
181  30041536    2015-02-25          2007-06-05
182  30041536    2015-02-25          2007-06-12
183  30041536    2015-02-25          2007-06-12
184  30041536    2015-02-25          2007-07-12
185  30041536    2015-02-25          2007-07-23
186  30041536    2015-02-25          2007-08-16
187  30041536    2015-02-25          2007-09-25
188  30041536    2015-02-25          2007-10-11
189  30041536    2015-02-25          2007-10-16
190  30041536    2015-02-25          2007-11-01
191  30041536    2015-02-25          2007-11-08
192  30041536    2015-02-25          2007-11-20
193  30041536    2015-02-25          2007-12-18
194  30041536    2015-02-25          2007-12-18
195  30041536    2015-02-25          2008-03-10
196  30041536    2015-02-25          2008-03-20
197  30041536    2015-02-25          2008-03-27
198  30041536    2015-02-25          2008-04-10
199  30041536    2015-02-25          2008-04-16
200  30041536    2015-02-25          2008-05-02
201  30041536    2015-02-25          2008-05-14
202  30041536    2015-02-25          2008-06-25
203  30041536    2015-02-25          2008-06-25
204  30041536    2015-02-25          2008-08-14
205  30041536    2015-02-25          2008-08-21
206  30041536    2015-02-25          2008-09-09
207  30041536    2015-02-25          2008-09-24
208  30041536    2015-02-25          2008-09-24
209  30041536    2015-02-25          2008-09-30
210  30041536    2015-02-25          2008-10-09
211    819392    2015-07-10          2002-05-18
212    819392    2015-07-10          2013-05-20
213    819392    2015-07-10          2013-05-28
214    819392    2015-07-10          2013-06-18
215    819392    2015-07-10          2013-10-01
216    819392    2015-07-10          2014-04-26
217    819392    2015-07-10          2014-04-30
218    819392    2015-07-10          2014-07-20
219    819392    2015-07-10          2014-07-23
220    819392    2015-07-10          2014-07-24
221    819392    2015-07-10          2014-10-16
222    819392    2015-07-10          2014-11-14
223    819392    2015-07-10          2015-03-11
224    819392    2015-07-10          2015-05-04
225    819392    2015-07-10          2015-05-06
226    819392    2015-07-10          2015-06-02
227    819392    2015-07-10          2015-07-09
228  30041536    2015-02-25          2008-10-23
229  30041536    2015-02-25          2008-12-18
230  30041536    2015-02-25          2008-12-23
231  30041536    2015-02-25          2008-12-23
232  30041536    2015-02-25          2008-12-30
233  30041536    2015-02-25          2009-01-27
234  30041536    2015-02-25          2009-02-02
235  30041536    2015-02-25          2009-02-10
236  30041536    2015-02-25          2009-03-18
237  30041536    2015-02-25          2009-04-16
238  30041536    2015-02-25          2009-05-05
239  30041536    2015-02-25          2009-06-05
240  30041536    2015-02-25          2009-07-02
241  30041536    2015-02-25          2009-09-03
242  30041536    2015-02-25          2009-09-11
243  30041536    2015-02-25          2009-09-30
244  30041536    2015-02-25          2009-09-30
245  30041536    2015-02-25          2009-10-29
246  30041536    2015-02-25          2009-11-09
247  30041536    2015-02-25          2009-11-24
248  30041536    2015-02-25          2009-12-04
249  30041536    2015-02-25          2009-12-22
250  30041536    2015-02-25          2010-03-11
251  30041536    2015-02-25          2010-03-26
252  30041536    2015-02-25          2010-04-07
253  30041536    2015-02-25          2010-04-23
254  30041536    2015-02-25          2010-04-26
255  30041536    2015-02-25          2010-05-11
256  30041536    2015-02-25          2010-05-19
257  30041536    2015-02-25          2010-06-03
258  30041536    2015-02-25          2010-06-18
259  30041536    2015-02-25          2010-06-25
260  30041536    2015-02-25          2010-07-07
261  30041536    2015-02-25          2010-08-11
262  30041536    2015-02-25          2010-08-17
263  30041536    2015-02-25          2010-08-26
264  30041536    2015-02-25          2010-12-10
265  30041536    2015-02-25          2011-01-19
266  30041536    2015-02-25          2011-03-31
267  30041536    2015-02-25          2011-04-08
268  30041536    2015-02-25          2011-05-25
269  30041536    2015-02-25          2011-06-22
270  30041536    2015-02-25          2011-07-06
271  30041536    2015-02-25          2011-07-28
272  30041536    2015-02-25          2011-10-07
273  30041536    2015-02-25          2011-11-22
274  30041536    2015-02-25          2012-01-11
275  30041536    2015-02-25          2012-01-20
276  30041536    2015-02-25          2012-02-09
277  30041536    2015-02-25          2012-02-23
278  30041536    2015-02-25          2012-03-01
279  30041536    2015-02-25          2012-03-14
280  30041536    2015-02-25          2012-04-18
281  30041536    2015-02-25          2012-04-25
282  30041536    2015-02-25          2012-05-04
283  30041536    2015-02-25          2012-05-09
284  30041536    2015-02-25          2012-07-17
285  30041536    2015-02-25          2012-08-03
286  30041536    2015-02-25          2012-08-10
287  30041536    2015-02-25          2012-09-25
288  30041536    2015-02-25          2012-12-17
289  30041536    2015-02-25          2013-03-12
290  30041536    2015-02-25          2013-05-15
291  30041536    2015-02-25          2013-06-18
292  30041536    2015-02-25          2013-09-17
293  30041536    2015-02-25          2013-09-26
294  30041536    2015-02-25          2013-10-16
295  30041536    2015-02-25          2013-10-31
296  30041536    2015-02-25          2013-12-03
297  30041536    2015-02-25          2013-12-10
298  30041536    2015-02-25          2013-12-17
299  30041536    2015-02-25          2014-01-03
300  30041536    2015-02-25          2014-07-04
301  30041536    2015-02-25          2014-08-16
302  30041536    2015-02-25          2014-09-08
303  30041536    2015-02-25          2014-09-10
304  30041536    2015-02-25          2014-09-15
305  30041536    2015-02-25          2014-09-18
306  30041536    2015-02-25          2014-09-23
307  30041536    2015-02-25          2014-10-08
308  30041536    2015-02-25          2014-10-10
309  30041536    2015-02-25          2014-10-10
310  30041536    2015-02-25          2014-10-17
311  30041536    2015-02-25          2014-10-22
312  30041536    2015-02-25          2014-09-25
313  30041536    2015-02-25          2015-01-08
314  30041536    2015-02-25          2015-01-21
315  30041536    2015-02-25          2015-02-16
316  30172352    2015-09-17          2011-06-10
317  30172352    2015-09-17          2014-04-06
318  30172352    2015-09-17          2014-08-25
319  30172352    2015-09-17          2014-11-19
320  30172352    2015-09-17          2014-12-23
321  30172352    2015-09-17          2015-01-09
322  30172352    2015-09-17          2015-01-10
323  30172352    2015-09-17          2015-01-22
324  30172352    2015-09-17          2015-02-04
325  30172352    2015-09-17          2015-02-06
326  30172352    2015-09-17          2015-02-24
327  30172352    2015-09-17          2015-02-25
328  30172352    2015-09-17          2015-08-08
329  30172352    2015-09-17          2015-08-08
330  30172352    2015-09-17          2015-08-30
331  30172352    2015-09-17          2015-09-15

Drop the NA values from the data. Because, if the "Admission Date" column is empty then we cannot find the difference between the last visit and the "Date_of_death". After dropping such values, the criteria narrows down the search to 4 unique patients.
In [43]:


ytrain = xtrain.drop_duplicates(subset='ID',keep = 'last',inplace=False)
print(ytrain)



           ID DATE_OF_DEATH ADMISSION_DATE_TIME
93   28438144    2015-07-02          2015-06-21
227    819392    2015-07-10          2015-07-09
315  30041536    2015-02-25          2015-02-16
331  30172352    2015-09-17          2015-09-15
In [44]:


ytrain.info()



<class 'pandas.core.frame.DataFrame'>
Int64Index: 4 entries, 93 to 331
Data columns (total 3 columns):
ID                     4 non-null int64
DATE_OF_DEATH          4 non-null datetime64[ns]
ADMISSION_DATE_TIME    4 non-null datetime64[ns]
dtypes: datetime64[ns](2), int64(1)
memory usage: 128.0 bytes
In [45]:


ytrain['Death from Last Visit'] = ytrain['DATE_OF_DEATH'] - ytrain['ADMISSION_DATE_TIME']



C:\Users\dhira\Anaconda3\lib\site-packages\ipykernel_launcher.py:1: SettingWithCopyWarning: 
A value is trying to be set on a copy of a slice from a DataFrame.
Try using .loc[row_indexer,col_indexer] = value instead

See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
  """Entry point for launching an IPython kernel.

Number of days between Last VisitDate and Date_Of_Death
We calculated the difference between 2 datetime objects and added the difference as another column in the dataframe. The resulting column is again a datetime object. Hence, maintaining the uniformity in the dtypes.
In [46]:


print(ytrain)



           ID DATE_OF_DEATH ADMISSION_DATE_TIME Death from Last Visit
93   28438144    2015-07-02          2015-06-21               11 days
227    819392    2015-07-10          2015-07-09                1 days
315  30041536    2015-02-25          2015-02-16                9 days
331  30172352    2015-09-17          2015-09-15                2 days
In [49]:


df1 = combineData[['ID','ADMISSION_DATE_TIME','DISCHARGE_DATE_TIME']]
df1.info()



<class 'pandas.core.frame.DataFrame'>
Int64Index: 10098 entries, 0 to 97
Data columns (total 3 columns):
ID                     10098 non-null int64
ADMISSION_DATE_TIME    10000 non-null datetime64[ns]
DISCHARGE_DATE_TIME    9792 non-null datetime64[ns]
dtypes: datetime64[ns](2), int64(1)
memory usage: 315.6 KB
In [50]:


df1


Out[50]:

ID
ADMISSION_DATE_TIME
DISCHARGE_DATE_TIME
0
22300992
2014-11-25
2014-11-25
1
22300992
2014-12-16
2014-12-18
2
22300992
2014-12-22
2014-12-22
3
22300992
2014-12-23
2014-12-24
4
22300992
2014-12-28
2014-12-28
5
22300992
2015-02-04
2015-02-09
6
22300992
2015-02-18
2015-02-20
7
22300992
2015-02-25
2015-02-25
8
22300992
2015-03-25
2015-03-31
9
22300992
2015-03-25
2015-03-25
10
22300992
2015-04-30
NaT
11
22300992
2015-05-04
2015-05-04
12
22300992
2015-05-06
2015-05-06
13
22300992
2015-05-14
2015-05-15
14
22300992
2015-05-27
2015-05-28
15
22300992
2015-06-15
2015-06-17
16
22300992
2015-06-22
2015-06-25
17
22300992
2015-07-06
2015-07-06
18
22300992
2015-07-15
2015-07-17
19
22300992
2015-07-22
2015-07-22
20
22300992
2015-07-24
2015-07-31
21
22300992
2015-08-04
2015-08-05
22
22300992
2015-08-12
2015-08-13
23
22300992
2015-08-24
2015-08-24
24
22300992
2015-08-31
2015-09-01
25
22300992
2015-09-15
2015-09-15
26
22300992
2015-09-15
2015-09-16
27
22300992
2015-09-22
2015-09-23
28
22300992
2015-09-24
2015-09-25
29
22300992
2015-09-25
2015-09-25
30
22300992
2015-09-28
2015-09-30
31
22300992
2015-10-13
2015-10-13
32
22300992
2015-10-26
2015-10-27
33
23665824
2003-03-10
2003-03-13
34
23665824
2003-03-20
2003-03-20
35
23665824
2003-05-30
2003-05-30
36
23665824
2003-07-21
2003-07-22
37
23665824
2003-08-05
2003-08-05
38
23665824
2003-10-02
NaT
39
23665824
2003-11-13
2003-11-13
40
23665824
2003-11-22
2003-11-22
41
23665824
2003-12-09
2003-12-11
42
23665824
2004-01-15
2004-01-15
43
23665824
2004-03-18
2004-03-19
44
23665824
2004-06-04
2004-06-04
45
23665824
2004-07-15
2004-07-16
46
23665824
2004-10-12
2004-10-12
47
23665824
2005-03-04
2005-03-04
48
23665824
2005-04-11
2005-04-11
49
23665824
2005-04-26
NaT
50
23665824
2005-08-09
2005-08-09
51
23665824
2005-10-24
2005-10-24
52
23665824
2006-02-13
2006-02-13
53
23665824
2006-05-04
2006-05-04
54
23665824
2006-09-05
2006-09-05
55
23665824
2006-09-22
2006-09-22
56
23665824
2006-11-17
2006-11-17
57
23665824
2006-11-24
2006-11-24
58
23665824
2007-06-15
2007-06-15
59
23665824
2007-10-19
2007-10-19
60
23665824
2007-11-28
2007-12-03
61
23665824
2008-06-17
2008-06-17
62
23665824
2012-03-14
2012-03-14
63
23665824
2012-07-09
2012-07-09
64
23665824
2012-12-01
2012-12-01
65
23665824
2014-03-05
2014-03-05
66
23665824
2014-05-22
2014-05-28
67
23665824
2014-08-11
2014-08-11
68
23665824
2014-08-25
2014-08-25
69
23665824
2014-09-28
2014-10-03
70
23665824
2014-11-02
2014-11-13
71
23665824
2014-11-26
2014-11-26
72
23665824
2014-11-29
2014-12-04
73
23665824
2014-12-08
2014-12-11
74
23665824
2014-12-17
2014-12-18
75
23665824
2014-12-18
2014-12-18
76
23665824
2015-01-16
2015-01-21
77
23665824
2015-02-18
2015-02-19
78
23665824
2015-03-04
2015-03-05
79
23665824
2015-03-11
2015-03-17
80
23665824
2015-03-29
2015-03-30
81
23665824
2015-06-02
2015-06-03
82
23665824
2015-06-09
2015-06-11
83
23665824
2015-06-18
2015-06-18
84
23665824
2015-07-16
2015-07-16
85
23665824
2015-07-25
2015-07-25
86
23665824
2015-07-28
2015-08-21
87
23665824
2015-08-26
2015-08-26
88
23665824
2015-08-30
2015-08-30
89
23665824
2015-09-14
2015-09-15
90
23665824
2015-09-16
2015-09-16
91
23665824
2015-09-21
NaT
92
23665824
2015-09-22
2015-09-25
93
24810016
2002-04-09
2002-04-10
94
24810016
2003-05-09
2003-05-09
95
24810016
2003-07-14
2003-07-14
96
24810016
2003-07-23
2003-07-23
97
24810016
2003-08-11
2003-08-11
98
24810016
2003-08-21
2003-08-21
99
24810016
2003-09-11
2003-09-11
100
24810016
2003-10-02
2003-10-02
101
24810016
2003-10-03
2003-10-03
102
24810016
2004-01-08
2004-01-08
103
24810016
2004-01-23
2004-01-23
104
24810016
2004-02-09
2004-02-09
105
24810016
2004-03-10
2004-03-10
106
24810016
2004-03-17
2004-03-17
107
24810016
2004-04-15
2004-04-15
108
24810016
2004-04-22
2004-04-22
109
24810016
2004-05-26
2004-05-26
110
24810016
2004-06-04
2004-06-04
111
24810016
2004-06-30
2004-06-30
112
24810016
2004-08-30
2004-08-30
113
24810016
2004-10-06
2004-10-06
114
24810016
2004-10-21
2004-10-21
115
24810016
2004-12-09
2004-12-09
116
24810016
2005-01-13
2005-01-13
117
24810016
2005-02-09
2005-02-09
118
24810016
2005-03-15
2005-03-15
119
24810016
2005-03-23
2005-03-23
120
24810016
2005-04-20
2005-04-20
121
24810016
2005-04-21
2005-04-21
122
24810016
2005-06-20
2005-06-20
123
24810016
2005-06-29
2005-06-29
124
24810016
2005-08-12
2005-08-12
125
24810016
2005-09-15
2005-09-15
126
24810016
2005-09-22
2005-09-22
127
24810016
2005-10-27
2005-10-27
128
24810016
2005-12-07
2005-12-07
129
24810016
2005-12-08
2005-12-08
130
24810016
2006-01-10
2006-01-10
131
24810016
2006-01-24
2006-01-24
132
24810016
2006-02-09
2006-02-09
133
24810016
2006-02-24
2006-02-24
134
24810016
2006-04-13
2006-04-13
135
24810016
2006-04-19
2006-04-19
136
24810016
2006-06-07
2006-06-07
137
24810016
2006-06-12
2006-06-12
138
24810016
2006-07-10
2006-07-10
139
24810016
2006-07-13
2006-07-13
140
24810016
2006-08-10
2006-08-10
141
24810016
2006-08-14
2006-08-14
142
24810016
2006-08-24
2006-08-24
143
24810016
2006-10-05
2006-10-05
144
24810016
2006-12-11
2006-12-11
145
24810016
2006-12-13
2006-12-13
146
24810016
2007-01-12
2007-01-12
147
24810016
2007-01-19
2007-01-19
148
24810016
2007-02-12
2007-02-12
149
24810016
2007-03-05
2007-03-05
150
24810016
2007-03-28
2007-03-28
151
24810016
2007-04-23
2007-04-23
152
24810016
2007-04-26
2007-04-26
153
24810016
2007-06-08
2007-06-08
154
24810016
2007-06-25
2007-06-25
155
24810016
2007-07-24
2007-07-24
156
24810016
2007-07-25
2007-07-25
157
24810016
2007-10-05
2007-10-05
158
24810016
2007-10-16
2007-10-16
159
24810016
2007-11-13
2007-11-13
160
24810016
2007-12-12
2007-12-12
161
24810016
2008-01-30
2008-01-30
162
24810016
2008-02-05
2008-02-05
163
24810016
2008-03-05
2008-03-05
164
24810016
2008-03-26
2008-03-26
165
24810016
2008-04-14
2008-04-14
166
24810016
2008-04-28
2008-04-28
167
24810016
2008-05-13
2008-05-13
168
24810016
2008-06-09
2008-06-09
169
24810016
2008-07-10
2008-07-10
170
24810016
2008-07-15
2008-07-15
171
24810016
2008-08-07
2008-08-07
172
24810016
2008-09-10
2008-09-10
173
24810016
2008-10-30
2008-10-30
174
24810016
2008-11-18
2008-11-18
175
24810016
2008-12-22
2008-12-22
176
24810016
2009-01-08
2009-01-08
177
24810016
2009-02-11
2009-02-11
178
24810016
2009-04-17
2009-04-17
179
24810016
2009-05-07
2009-05-07
180
24810016
2009-05-18
2009-05-18
181
24810016
2009-06-10
2009-06-10
182
24810016
2009-06-18
2009-06-18
183
24810016
2009-07-23
2009-07-23
184
24810016
2009-08-20
2009-08-20
185
24810016
2009-08-26
2009-08-26
186
24810016
2009-09-18
2009-09-18
187
24810016
2009-09-28
2009-09-28
188
24810016
2009-10-16
2009-10-16
189
24810016
2009-10-28
2009-10-28
190
24810016
2009-11-13
2009-11-13
191
24810016
2009-11-30
2009-11-30
192
24810016
2009-12-16
2009-12-16
193
24810016
2009-12-28
2009-12-28
194
24810016
2010-01-13
2010-01-13
195
24810016
2010-01-27
2010-01-27
196
24810016
2010-02-22
2010-02-22
197
24810016
2010-03-15
2010-03-15
198
24810016
2010-03-22
2010-03-22
199
24810016
2010-04-19
2010-04-19
200
24810016
2010-04-21
2010-04-21
201
24810016
2010-06-02
2010-06-02
202
24810016
2010-06-07
2010-06-07
203
24810016
2010-07-07
2010-07-07
204
24810016
2010-07-08
2010-07-08
205
24810016
2010-08-11
2010-08-11
206
24810016
2010-09-07
2010-09-07
207
24810016
2010-09-08
2010-09-08
208
24810016
2010-10-06
2010-10-06
209
24810016
2010-10-07
2010-10-07
210
24810016
2010-11-08
2010-11-08
211
24810016
2010-12-02
2010-12-02
212
24810016
2011-01-05
2011-01-05
213
24810016
2011-01-31
2011-01-31
214
24810016
2011-02-24
2011-02-24
215
24810016
2011-03-16
2011-03-16
216
24810016
2011-04-13
2011-04-13
217
24810016
2011-04-21
2011-04-21
218
24810016
2011-05-11
2011-05-11
219
24810016
2011-05-23
2011-05-23
220
24810016
2011-06-10
2011-06-10
221
24810016
2011-07-08
2011-07-08
222
24810016
2011-08-16
2011-08-16
223
24810016
2011-08-22
2011-08-22
224
24810016
2011-10-04
2011-10-04
225
24810016
2011-11-02
2011-11-02
226
24810016
2011-12-05
2011-12-05
227
24810016
2012-01-09
2012-01-09
228
24810016
2012-02-09
2012-02-09
229
24810016
2012-04-04
2012-04-04
230
24810016
2012-05-03
2012-05-03
231
24810016
2012-06-06
2012-06-06
232
24810016
2012-07-05
2012-07-05
233
24810016
2012-08-23
2012-08-23
234
24810016
2012-09-24
2012-09-24
235
24810016
2012-12-14
2012-12-14
236
24810016
2013-01-16
2013-01-16
237
24810016
2013-03-04
2013-03-04
238
24810016
2013-03-20
2013-03-20
239
24810016
2013-04-15
2013-04-15
240
24810016
2013-04-24
2013-04-24
241
24810016
2013-05-14
2013-05-14
242
24810016
2013-05-29
2013-05-29
243
24810016
2013-06-11
2013-06-11
244
24810016
2013-07-11
2013-07-11
245
24810016
2013-08-21
2013-08-21
246
24810016
2013-09-18
2013-09-18
247
24810016
2013-11-18
2013-11-18
248
24810016
2013-12-23
2013-12-23
249
24810016
2014-03-25
2014-03-25
250
24810016
2014-04-22
2014-04-22
251
24810016
2014-05-20
2014-05-20
252
24810016
2014-06-04
2014-06-04
253
24810016
2014-06-17
2014-06-17
254
24810016
2014-07-07
2014-07-07
255
24810016
2014-07-18
2014-07-18
256
24810016
2014-08-15
2014-08-15
257
24810016
2014-09-15
2014-09-15
258
24810016
2014-10-08
2014-10-08
259
24810016
2014-10-15
2014-10-15
260
24810016
2015-01-20
2015-01-20
261
24810016
2015-02-25
2015-02-25
262
24810016
2015-03-27
2015-03-27
263
24810016
2015-05-18
2015-05-18
264
24810016
2015-05-19
2015-05-19
265
24810016
2015-06-17
2015-06-17
266
24810016
2015-06-25
2015-06-25
267
24810016
2015-07-15
2015-07-15
268
24810016
2015-07-30
2015-07-30
269
24810016
2015-08-17
2015-08-17
270
24810016
2015-08-27
2015-08-27
271
24810016
2015-09-29
2015-09-29
272
24810016
2015-10-27
2015-10-27
273
26523392
2001-03-26
2001-03-26
274
26523392
2002-02-06
2002-02-08
275
26523392
2002-04-22
2002-04-23
276
26523392
2002-07-22
2002-07-24
277
26523392
2004-02-10
2004-02-12
278
26523392
2004-03-25
2004-03-25
279
26523392
2004-12-21
2004-12-22
280
26523392
2006-01-27
2006-01-27
281
26523392
2006-02-03
2006-02-03
282
26523392
2006-03-07
2006-03-08
283
26523392
2006-05-31
2006-05-31
284
26523392
2006-08-16
2006-08-17
285
26523392
2006-09-20
2006-09-20
286
26523392
2006-09-21
2006-09-21
287
26523392
2006-12-29
2006-12-29
288
26523392
2007-06-11
2007-06-12
289
26523392
2007-10-02
2007-10-10
290
26523392
2007-10-23
2007-10-24
291
26523392
2009-01-02
2009-01-03
292
26523392
2014-09-26
2014-09-26
293
26523392
2014-09-26
2014-09-29
294
26523392
2014-09-29
2014-09-29
295
26523392
2014-10-06
NaT
296
26523392
2014-10-26
2014-10-30
297
26523392
2014-10-30
2014-10-30
298
26523392
2014-11-04
2014-11-12
299
27421632
2006-07-15
2006-07-16
300
27421632
2006-08-17
2006-08-17
301
27421632
2006-08-18
2006-08-18
302
27421632
2006-08-21
2006-08-21
303
27421632
2006-08-31
2006-09-14
304
27421632
2006-09-11
2006-09-20
305
27421632
2006-09-29
2006-10-02
306
27421632
2006-10-10
2006-10-10
307
27421632
2006-10-04
2006-10-17
308
27421632
2006-10-06
2006-10-17
309
27421632
2006-12-06
2006-12-13
310
27421632
2006-12-23
2006-12-23
311
27421632
2007-01-11
2007-01-18
312
27421632
2007-02-15
2007-02-27
313
27421632
2007-02-16
2007-02-27
314
27421632
2007-03-04
2007-03-04
315
27421632
2007-03-16
2007-03-21
316
27421632
2007-03-16
2007-03-19
317
27421632
2007-06-18
2007-06-19
318
27421632
2007-08-03
2007-08-03
319
27421632
2009-07-02
2009-07-03
320
27421632
2009-07-07
2009-07-10
321
27421632
2009-07-10
2009-07-22
322
27421632
2010-01-06
2010-01-07
323
27421632
2010-02-01
2010-02-04
324
27421632
2010-03-02
2010-03-02
325
27421632
2010-05-31
2010-06-01
326
27421632
2010-03-01
2010-07-15
327
27421632
2010-04-01
2010-07-16
328
27421632
2010-05-01
2010-07-16
329
27421632
2010-06-01
2010-09-01
330
27421632
2010-07-01
2010-09-01
331
27421632
2010-08-01
2010-10-01
332
27421632
2010-09-01
2010-12-20
333
27421632
2010-10-01
2011-01-11
334
27421632
2010-11-01
2011-02-02
335
27421632
2011-01-01
2011-03-24
336
27421632
2011-02-01
2011-03-24
337
27421632
2010-12-01
2011-04-26
338
27421632
2011-03-01
2011-04-26
339
27421632
2011-04-27
2011-04-28
340
27421632
2011-04-01
2011-06-17
341
27421632
2011-05-01
2011-06-17
342
27421632
2011-06-01
2011-07-27
343
27421632
2011-07-01
2011-08-17
344
27421632
2011-08-01
2011-09-26
345
27421632
2011-09-01
2011-10-31
346
27421632
2011-12-15
2011-12-15
347
27421632
2011-10-01
2011-12-16
348
27421632
2012-01-26
2012-01-26
349
27421632
2012-04-05
2012-04-05
350
27421632
2011-11-01
2012-02-01
351
27421632
2012-02-22
2012-02-22
352
27421632
2011-12-01
2012-05-23
353
27421632
2012-06-06
2012-06-07
354
27421632
2012-01-01
2012-06-19
355
27421632
2012-02-01
2012-07-19
356
27421632
2012-08-28
2012-08-30
357
27421632
2012-03-01
2012-09-28
358
27421632
2012-04-01
2012-09-28
359
27421632
2012-11-18
2012-11-18
360
27421632
2012-06-01
2012-06-01
361
27421632
2012-05-01
2012-12-05
362
27421632
2013-01-07
2013-02-06
363
27421632
2012-08-01
2013-02-08
364
27421632
2013-02-10
2013-02-10
365
27421632
2012-09-01
2013-02-12
366
27421632
2012-12-01
2013-02-26
367
27421632
2013-04-30
2013-04-30
368
27421632
2013-06-04
2013-06-05
369
27421632
2013-02-01
2013-06-12
370
27421632
2013-07-09
2013-07-10
371
27421632
2013-11-14
2013-11-15
372
27421632
2014-01-23
2014-01-23
373
27421632
2014-02-03
2014-02-04
374
27421632
2014-02-10
2014-02-10
375
27421632
2014-03-04
2014-03-06
376
27421632
2014-03-18
2014-03-21
377
27421632
2014-05-05
2014-05-05
378
27421632
2014-05-16
2014-05-16
379
27421632
2014-06-18
2014-06-18
380
27421632
2014-09-12
2014-09-14
381
27421632
2014-09-22
2014-09-22
382
27421632
2014-10-30
2014-10-31
383
27421632
2014-11-01
2014-11-02
384
27421632
2014-11-25
NaT
385
27421632
2014-12-08
2014-12-08
386
27421632
2014-12-06
2014-12-07
387
27421632
2015-07-20
2015-07-20
388
27421632
2015-07-26
2015-07-29
389
27421632
2015-08-04
2015-08-13
390
27421632
2015-08-28
2015-08-29
391
27421632
2015-10-09
2015-10-09
392
27421632
2015-10-20
2015-10-30
393
28438144
2000-06-13
2000-07-01
394
28438144
2000-12-22
2000-12-29
395
28438144
2002-09-03
2002-09-03
396
28438144
2003-03-04
2003-03-07
397
28438144
2003-10-09
2003-10-14
398
28438144
2003-10-20
2003-10-27
399
28438144
2003-10-28
2003-10-28
400
28438144
2003-11-01
2003-11-07
401
28438144
2003-12-02
2003-12-03
402
28438144
2003-12-12
2003-12-23
403
28438144
2003-12-17
2004-01-07
404
28438144
2004-02-13
NaT
405
28438144
2004-02-12
2004-02-20
406
28438144
2004-02-25
2004-02-26
407
28438144
2004-04-26
2004-05-01
408
28438144
2004-05-05
2004-05-07
409
28438144
2004-05-12
2004-05-13
410
28438144
2004-07-10
2004-07-10
411
28438144
2004-09-21
2004-09-22
412
28438144
2004-12-01
2004-12-01
413
28438144
2004-12-08
2004-12-08
414
28438144
2005-04-07
2005-04-07
415
28438144
2005-04-30
2005-05-02
416
28438144
2005-05-02
2005-05-03
417
28438144
2005-10-25
2005-10-25
418
28438144
2005-10-26
2005-10-26
419
28438144
2005-11-02
2005-11-04
420
28438144
2006-01-26
2006-01-26
421
28438144
2006-04-03
2006-04-04
422
28438144
2006-11-06
2006-11-08
423
28438144
2006-11-13
2006-11-16
424
28438144
2006-11-20
2006-11-21
425
28438144
2007-02-06
2007-02-09
426
28438144
2007-02-07
2007-02-08
427
28438144
2007-03-13
2007-03-15
428
28438144
2007-03-27
2007-03-27
429
28438144
2007-05-22
2007-05-22
430
28438144
2007-06-12
2007-06-13
431
28438144
2007-06-14
2007-06-14
432
28438144
2007-06-25
2007-06-25
433
28438144
2011-01-14
2011-01-14
434
28438144
2011-04-02
2011-04-05
435
28438144
2011-12-17
2012-01-06
436
28438144
2012-01-10
2012-01-10
437
28438144
2012-01-12
2012-01-12
438
28438144
2012-01-19
2012-01-19
439
28438144
2012-01-24
2012-01-25
440
28438144
2012-02-14
NaT
441
28438144
2012-02-14
2012-02-14
442
28438144
2012-02-23
2012-03-08
443
28438144
2012-03-13
2012-03-19
444
28438144
2012-03-15
2012-03-16
445
28438144
2012-03-20
2012-03-23
446
28438144
2012-03-27
2012-03-29
447
28438144
2012-03-29
2012-03-30
448
28438144
2012-04-05
2012-04-05
449
28438144
2012-04-04
2012-04-06
450
28438144
2012-04-09
2012-04-12
451
28438144
2012-04-12
2012-04-13
452
28438144
2012-04-20
2012-04-24
453
28438144
2012-05-08
2012-05-09
454
28438144
2012-05-18
2012-05-18
455
28438144
2012-08-27
2012-08-31
456
28438144
2012-09-07
2012-09-07
457
28438144
2012-09-13
NaT
458
28438144
2012-09-13
NaT
459
28438144
2012-09-13
2012-09-14
460
28438144
2012-09-26
2012-09-28
461
28438144
2012-09-28
2012-10-02
462
28438144
2012-10-09
2012-10-09
463
28438144
2012-10-18
2012-10-19
464
28438144
2012-10-22
2012-10-22
465
28438144
2012-11-15
2012-11-15
466
28438144
2012-11-28
2012-12-01
467
28438144
2013-01-10
2013-01-15
468
28438144
2013-01-22
2013-01-23
469
28438144
2013-01-24
2013-01-25
470
28438144
2013-01-25
2013-01-26
471
28438144
2013-01-27
2013-01-27
472
28438144
2013-07-02
2013-07-03
473
28438144
2014-02-28
2014-02-28
474
28438144
2014-03-28
2014-04-10
475
28438144
2014-05-13
2014-07-02
476
28438144
2014-07-29
2014-07-29
477
28438144
2014-07-12
2014-07-12
478
28438144
2014-07-13
2014-07-18
479
28438144
2014-10-12
2014-10-13
480
28438144
2014-10-15
2014-10-21
481
28438144
2014-10-28
2014-10-30
482
28438144
2015-04-08
2015-04-09
483
28438144
2015-05-05
2015-05-09
484
28438144
2015-06-03
2015-06-03
485
28438144
2015-06-11
2015-06-12
486
28438144
2015-06-21
2015-07-02
487
28653184
2003-04-10
2003-04-12
488
28653184
2003-05-15
2003-05-16
489
28653184
2003-05-16
2003-05-16
490
28653184
2003-05-16
2003-05-19
491
28653184
2003-10-02
2003-10-02
492
28653184
2004-11-08
2004-11-08
493
28653184
2005-01-15
2005-01-16
494
28653184
2005-04-16
2005-04-19
495
28653184
2005-09-19
2005-09-23
496
28653184
2006-01-06
2006-01-07
497
28653184
2006-02-20
2006-02-24
498
28653184
2007-01-05
2007-01-06
499
28653184
2007-04-18
2007-04-20
500
28653184
2007-07-12
2007-07-13
501
28653184
2007-07-24
2007-07-25
502
28653184
2007-08-23
2007-08-24
503
28653184
2007-09-10
2007-09-11
504
28653184
2007-09-15
2007-09-16
505
28653184
2007-09-24
2007-09-25
506
28653184
2007-09-25
2007-09-28
507
28653184
2007-10-07
2007-10-07
508
28653184
2008-01-12
2008-01-13
509
28653184
2008-01-17
2008-01-18
510
28653184
2008-01-21
2008-01-23
511
28653184
2008-03-02
2008-03-02
512
28653184
2008-03-03
2008-03-03
513
28653184
2008-04-04
2008-04-05
514
28653184
2008-04-12
2008-04-18
515
28653184
2008-04-23
2008-04-23
516
28653184
2008-04-23
2008-04-24
517
28653184
2008-06-01
2008-06-02
518
28653184
2008-09-25
2008-10-07
519
28653184
2008-11-16
2008-11-18
520
28653184
2008-12-10
2008-12-10
521
28653184
2008-12-11
2008-12-13
522
28653184
2008-12-24
2008-12-25
523
28653184
2009-01-13
2009-01-14
524
28653184
2009-01-14
2009-01-17
525
28653184
2009-01-21
2009-01-21
526
28653184
2009-10-05
2009-10-07
527
28653184
2009-11-10
2009-11-12
528
28653184
2009-11-23
2009-11-26
529
28653184
2009-11-23
2009-11-25
530
28653184
2010-01-08
2010-01-09
531
28653184
2010-01-14
2010-01-15
532
28653184
2010-03-25
2010-03-26
533
28653184
2010-05-10
2010-05-11
534
28653184
2010-05-11
2010-05-13
535
28653184
2010-06-27
2010-06-28
536
28653184
2010-08-20
2010-08-21
537
28653184
2010-10-09
2010-10-15
538
28653184
2010-10-09
2010-10-10
539
28653184
2010-10-25
2010-11-09
540
28653184
2010-12-30
2010-12-30
541
28653184
2010-12-30
2010-12-31
542
28653184
2011-01-04
2011-01-05
543
28653184
2011-02-01
2011-02-01
544
28653184
2011-02-02
2011-02-02
545
28653184
2011-02-21
2011-02-22
546
28653184
2011-03-05
2011-03-06
547
28653184
2011-04-09
2011-04-10
548
28653184
2011-05-06
2011-05-07
549
28653184
2011-05-29
2011-05-30
550
28653184
2011-08-17
2011-08-18
551
28653184
2011-10-23
2011-10-23
552
28653184
2011-10-23
2011-10-25
553
28653184
2011-10-24
2011-10-25
554
28653184
2011-11-30
2011-12-01
555
28653184
2012-01-09
2012-01-10
556
28653184
2012-01-28
2012-01-28
557
28653184
2012-02-05
2012-02-06
558
28653184
2012-03-26
2012-03-26
559
28653184
2012-06-04
2012-06-04
560
28653184
2012-06-23
2012-06-24
561
28653184
2012-09-02
2012-09-03
562
28653184
2012-11-06
2012-11-07
563
28653184
2012-11-15
2012-11-17
564
28653184
2012-12-04
2012-12-05
565
28653184
2012-12-24
2012-12-25
566
28653184
2013-02-12
2013-02-13
567
28653184
2013-02-17
2013-02-20
568
28653184
2013-02-17
2013-02-22
569
28653184
2013-03-30
2013-03-31
570
28653184
2013-04-03
2013-04-04
571
28653184
2013-04-06
2013-04-06
572
28653184
2013-04-07
2013-04-08
573
28653184
2013-04-24
2013-04-24
574
28653184
2013-04-30
2013-05-01
575
28653184
2013-05-03
2013-05-03
576
28653184
2013-05-12
2013-05-13
577
28653184
2013-05-24
2013-05-25
578
28653184
2013-06-05
2013-06-06
579
28653184
2013-06-07
2013-06-08
580
28653184
2013-06-08
2013-06-09
581
28653184
2013-06-27
2013-06-27
582
28653184
2013-07-12
2013-07-12
583
28653184
2013-07-17
2013-07-18
584
28653184
2013-09-09
2013-09-10
585
28653184
2013-09-17
2013-09-18
586
28653184
2013-09-29
2013-09-30
587
28653184
2013-09-30
2013-10-01
588
28653184
2013-10-10
2013-10-10
589
28653184
2013-11-01
2013-11-02
590
28653184
2013-11-19
2013-11-20
591
28653184
2013-11-20
2013-11-21
592
28653184
2013-11-21
2013-11-22
593
28653184
2013-11-28
2013-11-28
594
28653184
2013-11-29
2013-12-01
595
28653184
2013-12-26
2013-12-27
596
28653184
2014-01-02
2014-01-02
597
28653184
2014-01-03
2014-01-14
598
28653184
2014-01-31
2014-02-01
599
28653184
2014-02-08
2014-02-09
600
28653184
2014-04-05
2014-04-06
601
28653184
2014-04-09
2014-04-09
602
28653184
2014-04-24
2014-04-25
603
28653184
2014-05-25
2014-05-26
604
28653184
2014-06-09
2014-06-10
605
28653184
2014-06-12
2014-06-13
606
28653184
2014-07-22
2014-07-24
607
28653184
2014-07-31
2014-08-01
608
28653184
2014-08-06
2014-08-06
609
28653184
2014-08-07
2014-08-09
610
28653184
2014-08-20
2014-08-21
611
28653184
2014-09-04
2014-09-04
612
28653184
2014-09-20
2014-09-21
613
28653184
2014-09-28
2014-09-28
614
28653184
2014-09-29
2014-09-30
615
28653184
2014-10-03
2014-10-04
616
28653184
2014-10-17
2014-10-17
617
28653184
2014-11-04
2014-11-05
618
28653184
2014-12-01
2014-12-02
619
28653184
2014-12-31
2015-01-01
620
28653184
2015-01-22
2015-01-22
621
28653184
2015-02-05
2015-02-05
622
28653184
2015-02-19
2015-02-20
623
28653184
2015-03-10
2015-03-10
624
28653184
2015-03-16
2015-03-16
625
28653184
2015-03-22
2015-03-22
626
28653184
2015-03-27
2015-03-28
627
28653184
2015-04-30
2015-04-30
628
28653184
2015-05-04
2015-05-05
629
28653184
2015-06-19
2015-06-19
630
28653184
2015-07-20
2015-07-21
631
28653184
2015-08-07
2015-08-07
632
28653184
2015-08-08
2015-08-09
633
28653184
2015-08-28
2015-08-29
634
28653184
2015-09-07
2015-09-07
635
28653184
2015-10-06
2015-10-06
636
28653184
2015-10-16
2015-10-26
637
28990976
2003-06-06
2003-06-11
638
28990976
2003-07-09
2003-07-15
639
28990976
2003-11-17
2003-11-26
640
28990976
2003-11-29
2003-12-02
641
28990976
2004-01-05
2004-01-06
642
28990976
2004-01-19
2004-01-26
643
28990976
2006-02-03
2006-02-07
644
28990976
2006-04-12
2006-04-17
645
28990976
2006-08-20
2006-08-23
646
28990976
2006-08-31
2006-09-06
647
28990976
2007-03-08
2007-03-13
648
28990976
2007-05-10
2007-05-13
649
28990976
2007-05-14
2007-05-14
650
28990976
2007-05-14
2007-05-14
651
28990976
2007-07-30
2007-08-03
652
28990976
2007-10-23
2007-10-28
653
28990976
2007-11-09
2007-11-09
654
28990976
2007-12-17
2007-12-21
655
28990976
2008-10-20
2008-10-27
656
28990976
2008-12-17
2008-12-19
657
28990976
2009-02-16
2009-02-18
658
28990976
2009-02-26
2009-02-27
659
28990976
2009-05-29
2009-06-01
660
28990976
2009-07-03
2009-07-05
661
28990976
2009-07-23
2009-07-23
662
28990976
2011-08-01
2011-08-02
663
28990976
2012-01-16
2012-01-16
664
28990976
2012-01-16
2012-01-17
665
28990976
2012-04-13
2012-04-13
666
28990976
2012-07-21
2012-07-23
667
28990976
2012-07-22
2012-07-30
668
28990976
2012-08-18
NaT
669
28990976
2012-08-18
2012-08-19
670
28990976
2014-02-11
2014-02-11
671
28990976
2014-02-12
2014-02-20
672
28990976
2014-10-25
2014-11-07
673
28990976
2015-01-04
2015-01-14
674
28990976
2015-02-01
2015-02-03
675
28990976
2015-03-29
2015-04-07
676
28990976
2015-04-23
2015-04-26
677
29779232
2001-09-13
2001-10-11
678
29779232
2012-10-06
NaT
679
29779232
2012-10-07
2012-10-19
680
29779232
2012-11-11
2012-11-14
681
29779232
2012-11-11
2012-11-13
682
29779232
2012-11-13
NaT
683
29779232
2012-11-13
2012-11-14
684
29779232
2012-11-15
NaT
685
29779232
2012-11-17
2012-12-06
686
29779232
2012-12-07
NaT
687
29779232
2012-12-07
2012-12-07
688
29779232
2012-12-13
NaT
689
29779232
2012-12-18
2012-12-18
690
29779232
2013-06-06
2013-06-06
691
29779232
2013-06-06
2013-06-07
692
29779232
2013-08-08
2013-08-08
693
29779232
2013-08-08
2013-08-12
694
29779232
2013-08-11
NaT
695
29779232
2013-08-11
2013-08-12
696
29779232
2013-08-12
NaT
697
29779232
2013-08-12
2013-08-14
698
29779232
2013-08-12
NaT
699
29779232
2013-08-12
2013-08-14
700
29779232
2013-08-13
2013-08-13
701
29779232
2013-08-17
2013-08-20
702
29779232
2013-08-17
2013-08-20
703
29779232
2013-08-12
2013-08-12
704
29779232
2013-08-26
2013-08-27
705
29779232
2013-09-01
2013-09-09
706
29779232
2013-09-03
2013-09-12
707
29779232
2013-08-12
2013-09-11
708
29779232
2013-09-16
2013-09-16
709
29779232
2013-09-16
2013-09-18
710
29779232
2013-09-29
2013-09-30
711
29779232
2013-09-29
2013-10-01
712
29779232
2013-10-06
2013-10-13
713
29779232
2013-10-06
2013-10-07
714
29779232
2013-12-01
2013-12-05
715
29779232
2013-12-02
2013-12-12
716
29779232
2014-02-19
2014-02-21
717
29779232
2014-02-20
2014-03-05
718
29779232
2014-03-11
2014-03-13
719
29779232
2014-03-11
2014-03-26
720
29779232
2014-07-23
2014-07-24
721
29779232
2014-07-26
2014-08-07
722
29779232
2014-10-04
2014-10-22
723
29779232
2014-10-25
2014-10-27
724
29779232
2014-11-21
2014-12-02
725
29779232
2014-12-04
2014-12-15
726
29779232
2015-02-06
2015-02-06
727
29779232
2015-05-02
2015-05-11
728
29779232
2015-07-09
2015-07-10
729
29779232
2015-10-21
2015-10-22
730
29779232
2015-10-24
2015-10-27
731
29779232
2015-10-30
2015-10-31
732
30041536
1995-01-17
1995-01-17
733
30041536
2000-10-05
2000-10-05
734
30041536
2001-01-09
2001-01-11
735
30041536
2001-07-30
2001-07-31
736
30041536
2001-08-07
2001-08-07
737
30041536
2002-02-11
2002-02-11
738
30041536
2002-03-04
2002-03-05
739
30041536
2002-03-25
2002-03-27
740
30041536
2002-09-03
2002-09-03
741
30041536
2002-09-04
2002-09-04
742
30041536
2003-01-28
2003-01-28
743
30041536
2003-04-14
2003-04-14
744
30041536
2003-05-01
2003-05-01
745
30041536
2003-05-08
2003-05-08
746
30041536
2003-06-10
2003-06-10
747
30041536
2003-08-11
2003-08-11
748
30041536
2003-08-27
2003-08-29
749
30041536
2003-09-04
2003-09-04
750
30041536
2003-09-11
2003-09-11
751
30041536
2003-09-25
2003-09-30
752
30041536
2003-10-14
2003-10-17
753
30041536
2003-10-21
2003-10-21
754
30041536
2003-11-12
2003-11-12
755
30041536
2004-01-16
2004-01-23
756
30041536
2004-01-21
2004-01-21
757
30041536
2004-02-05
2004-02-05
758
30041536
2004-02-19
2004-02-26
759
30041536
2004-02-27
2004-02-27
760
30041536
2004-03-09
2004-03-09
761
30041536
2004-03-17
2004-03-17
762
30041536
2004-03-25
2004-03-25
763
30041536
2004-04-20
2004-04-20
764
30041536
2004-05-27
2004-05-27
765
30041536
2004-06-01
2004-06-01
766
30041536
2004-06-04
2004-06-04
767
30041536
2004-06-09
2004-06-15
768
30041536
2004-06-10
2004-06-10
769
30041536
2004-06-14
2004-06-14
770
30041536
2004-06-16
2004-06-16
771
30041536
2004-08-12
2004-08-12
772
30041536
2004-08-19
2004-08-19
773
30041536
2004-08-24
2004-08-24
774
30041536
2004-09-02
2004-09-02
775
30041536
2004-09-23
2004-09-23
776
30041536
2004-10-06
2004-10-06
777
30041536
2004-10-18
2004-10-18
778
30041536
2004-11-17
2004-11-17
779
30041536
2004-11-23
2004-11-23
780
30041536
2004-11-30
2004-11-30
781
30041536
2004-12-21
2004-12-21
782
30041536
2005-03-08
2005-03-08
783
30041536
2005-03-10
2005-03-10
784
30041536
2005-03-23
2005-03-24
785
30041536
2005-04-19
2005-04-19
786
30041536
2005-04-20
2005-04-20
787
30041536
2005-05-26
2005-05-26
788
30041536
2005-06-14
2005-06-14
789
30041536
2005-06-16
2005-06-16
790
30041536
2005-07-12
2005-07-12
791
30041536
2005-07-14
2005-07-20
792
30041536
2005-07-18
2005-07-18
793
30041536
2005-08-09
2005-08-09
794
30041536
2005-09-28
2005-09-28
795
30041536
2005-11-03
2005-11-03
796
30041536
2005-11-15
2005-11-15
797
30041536
2005-11-22
2005-11-22
798
30041536
2005-11-29
2005-11-29
799
30041536
2006-03-23
2006-03-23
800
30041536
2006-03-28
2006-03-29
801
30041536
2006-03-28
2006-03-28
802
30041536
2006-04-19
2006-04-19
803
30041536
2006-04-27
2006-04-27
804
30041536
2006-05-02
2006-05-12
805
30041536
2006-05-11
2006-05-16
806
30041536
2006-05-16
2006-05-16
807
30041536
2006-07-18
2006-07-18
808
30041536
2006-08-29
2006-08-30
809
30041536
2006-09-05
2006-09-05
810
30041536
2006-10-06
2006-10-10
811
30041536
2006-10-10
2006-10-10
812
30041536
2006-10-11
2006-10-11
813
30041536
2007-01-16
2007-01-16
814
30041536
2007-01-30
2007-02-01
815
30041536
2007-03-23
2007-03-27
816
30041536
2007-03-30
2007-03-30
817
30041536
2007-05-02
2007-05-02
818
30041536
2007-05-10
2007-05-10
819
30041536
2007-06-05
NaT
820
30041536
2007-06-12
2007-06-12
821
30041536
2007-06-12
NaT
822
30041536
2007-07-12
2007-07-12
823
30041536
2007-07-23
2007-07-23
824
30041536
2007-08-16
2007-08-17
825
30041536
2007-09-25
2007-09-25
826
30041536
2007-10-11
2007-10-11
827
30041536
2007-10-16
2007-10-16
828
30041536
2007-11-01
2007-11-01
829
30041536
2007-11-08
2007-11-09
830
30041536
2007-11-20
2007-11-20
831
30041536
2007-12-18
2007-12-18
832
30041536
2007-12-18
2007-12-18
833
30041536
2008-03-10
2008-03-10
834
30041536
2008-03-20
2008-03-20
835
30041536
2008-03-27
2008-03-27
836
30041536
2008-04-10
2008-04-10
837
30041536
2008-04-16
2008-04-16
838
30041536
2008-05-02
2008-05-09
839
30041536
2008-05-14
2008-05-16
840
30041536
2008-06-25
2008-06-25
841
30041536
2008-06-25
2008-06-26
842
30041536
2008-08-14
2008-08-14
843
30041536
2008-08-21
2008-08-21
844
30041536
2008-09-09
2008-09-09
845
30041536
2008-09-24
2008-10-02
846
30041536
2008-09-24
2008-09-24
847
30041536
2008-09-30
2008-10-01
848
30041536
2008-10-09
2008-10-09
849
38659264
2003-12-30
2003-12-30
850
38659264
2004-04-13
2004-04-13
851
38659264
2006-12-17
2006-12-20
852
38659264
2006-12-28
2006-12-28
853
38659264
2006-12-28
2007-01-03
854
38659264
2008-05-21
2008-05-21
855
38659264
2008-07-03
2008-07-03
856
38659264
2008-10-16
2008-10-16
857
38659264
2009-02-23
2009-02-23
858
38659264
2009-12-04
2009-12-05
859
38659264
2009-12-07
2009-12-07
860
38659264
2009-12-07
2009-12-07
861
38659264
2010-02-09
2010-02-09
862
38659264
2010-07-08
2010-07-09
863
38659264
2010-08-03
2010-08-03
864
38659264
2010-10-25
2010-10-25
865
38659264
2011-01-24
2011-01-25
866
38659264
2011-05-28
2011-05-28
867
38659264
2011-06-09
2011-06-09
868
38659264
2011-06-21
2011-06-21
869
38659264
2011-12-21
2011-12-22
870
38659264
2012-04-02
2012-04-03
871
38659264
2012-05-11
2012-05-11
872
38659264
2012-05-20
2012-05-22
873
38659264
2012-05-21
2012-05-23
874
38659264
2012-07-19
2012-07-20
875
38659264
2012-07-30
2012-07-30
876
38659264
2013-11-30
2013-12-13
877
38659264
2013-12-16
NaT
878
38659264
2013-12-26
2013-12-31
879
38659264
2014-01-14
2014-01-15
880
38659264
2014-02-07
2014-02-07
881
38659264
2014-02-12
2014-02-12
882
38659264
2014-02-19
2014-02-19
883
38659264
2014-09-23
2014-09-24
884
38659264
2015-07-20
2015-07-22
885
38659264
2015-07-28
2015-07-29
886
39541824
2005-01-05
2005-01-07
887
39541824
2007-04-22
2007-04-22
888
39541824
2008-02-18
2008-02-23
889
39541824
2008-05-28
2008-06-03
890
39541824
2008-06-04
2008-06-17
891
39541824
2008-06-24
2008-06-30
892
39541824
2008-06-27
2008-07-08
893
39541824
2009-05-29
2009-05-30
894
39541824
2011-04-10
2011-04-11
895
39541824
2011-04-10
2011-04-11
896
39541824
2011-04-14
2011-04-15
897
39541824
2011-04-19
2011-04-20
898
39541824
2012-01-19
2012-01-20
899
39541824
2013-04-25
2013-04-26
900
39541824
2013-04-26
2013-04-27
901
39541824
2013-04-27
2013-04-28
902
39541824
2013-04-28
2013-04-29
903
39541824
2014-09-24
2014-09-24
904
40160736
2002-11-13
2002-11-13
905
40160736
2003-04-16
2003-04-16
906
40160736
2003-05-16
2003-05-19
907
40160736
2003-05-20
2003-05-23
908
40160736
2003-05-21
2003-05-21
909
40160736
2003-07-09
2003-07-09
910
40160736
2003-07-23
2003-07-23
911
40160736
2003-07-28
2003-07-28
912
40160736
2004-02-04
2004-02-04
913
40160736
2006-10-18
2006-10-19
914
40160736
2006-10-19
NaT
915
40160736
2007-04-12
2007-04-17
916
40160736
2007-04-13
2007-04-13
917
40160736
2007-09-10
2007-09-11
918
40160736
2007-10-03
2007-10-03
919
40160736
2007-10-09
2007-10-09
920
40160736
2008-01-03
2008-01-03
921
40160736
2008-01-11
2008-01-14
922
40160736
2008-06-04
2008-06-06
923
40160736
2008-06-12
2008-06-13
924
40160736
2008-09-22
2008-09-22
925
40160736
2009-01-06
2009-01-06
926
40160736
2009-01-06
2009-01-06
927
40160736
2009-03-02
2009-03-02
928
40160736
2009-04-27
2009-04-27
929
40160736
2009-08-26
2009-08-26
930
40160736
2010-02-01
2010-02-01
931
40160736
2010-02-18
2010-02-18
932
40160736
2010-05-03
2010-05-03
933
40160736
2010-08-02
2010-08-02
934
40160736
2010-10-29
2010-11-01
935
40160736
2010-11-05
2010-11-05
936
40160736
2010-11-05
2010-11-08
937
40160736
2011-02-09
2011-02-09
938
40160736
2011-09-12
2011-09-13
939
40160736
2011-11-29
2011-11-29
940
40160736
2011-12-02
2011-12-02
941
40160736
2012-02-09
2012-02-09
942
40160736
2012-03-07
2012-03-08
943
40160736
2012-10-08
2012-10-08
944
40160736
2012-10-09
2012-10-12
945
40160736
2012-10-11
2012-10-12
946
40160736
2012-11-27
2012-11-27
947
40160736
2013-02-26
2013-02-26
948
40160736
2013-05-03
2013-05-06
949
40160736
2013-07-19
2013-07-19
950
40160736
2013-08-19
2013-08-19
951
40160736
2013-08-01
2013-08-02
952
40160736
2015-05-22
2015-05-22
953
40160736
2015-05-08
2015-06-08
954
40160736
2015-06-03
2015-06-11
955
40160736
2015-05-21
2015-06-12
956
40160736
2015-06-17
2015-06-18
957
40160736
2015-06-19
2015-06-22
958
40160736
2015-06-11
2015-06-22
959
40160736
2015-07-02
2015-07-06
960
40160736
2015-07-06
2015-07-07
961
40160736
2015-07-09
2015-07-10
962
40160736
2015-07-16
2015-07-29
963
40160736
2015-08-04
2015-08-05
964
40160736
2015-08-10
2015-08-11
965
40160736
2015-08-13
2015-08-14
966
40160736
2015-08-14
2015-08-17
967
40160736
2015-08-31
2015-09-01
968
40160736
2015-09-02
2015-09-04
969
40160736
2015-09-03
2015-09-04
970
40160736
2015-09-10
2015-09-22
971
40160736
2015-09-11
2015-09-22
972
40160736
2015-09-21
2015-09-23
973
40160736
2015-09-17
2015-09-23
974
40160736
2015-09-30
2015-09-30
975
40160736
2015-10-01
2015-10-09
976
40160736
2015-10-08
2015-10-16
977
40160736
2015-10-15
2015-10-16
978
40160736
2015-10-14
2015-10-20
979
40160736
2015-10-22
2015-10-28
980
40674368
2010-06-30
2010-07-01
981
40674368
2012-07-07
2012-07-08
982
40674368
2012-07-07
2012-07-09
983
40674368
2012-08-10
2012-08-13
984
40674368
2013-06-09
2013-06-11
985
40674368
2013-06-28
2013-06-29
986
40674368
2013-07-02
2013-07-03
987
40674368
2013-07-03
2013-07-03
988
40674368
2013-07-03
2013-07-06
989
40674368
2013-07-07
2013-07-07
990
40674368
2014-05-18
2014-05-19
991
40674368
2014-05-29
2014-05-30
992
40674368
2014-09-09
2014-09-09
993
40674368
2014-09-09
2014-09-09
994
40674368
2015-02-25
2015-02-26
995
40674368
2015-02-27
2015-03-01
996
40674368
2015-03-10
2015-03-10
997
40674368
2015-04-20
2015-04-21
998
40674368
2015-05-09
2015-05-14
999
40674368
2015-05-14
2015-05-14
...
...
...
...
9098
37790816
2015-04-05
2015-04-06
9099
37790816
2015-04-06
2015-04-07
9100
37790816
2015-04-07
2015-04-08
9101
37790816
2015-04-08
2015-04-08
9102
37790816
2015-04-08
2015-04-09
9103
37790816
2015-07-16
2015-07-16
9104
37790816
2015-07-17
2015-07-17
9105
37790816
2015-07-18
2015-07-18
9106
37790816
2015-07-19
2015-07-20
9107
37790816
2015-07-20
2015-07-20
9108
37790816
2015-10-24
2015-10-24
9109
37790816
2015-10-27
2015-10-28
9110
38058496
2001-07-12
2001-07-12
9111
38058496
2006-11-28
2006-11-30
9112
38058496
2006-11-28
2006-11-29
9113
38058496
2008-11-15
2008-11-16
9114
38058496
2009-07-22
2009-07-23
9115
38058496
2014-07-28
2014-07-29
9116
38058496
2014-07-31
2014-08-01
9117
38058496
2014-08-27
2014-08-28
9118
38058496
2014-08-29
2014-08-30
9119
38058496
2014-09-11
2014-09-12
9120
38058496
2014-09-13
2014-09-14
9121
38058496
2014-09-14
2014-09-15
9122
38058496
2014-09-18
2014-09-19
9123
38058496
2014-09-20
2014-09-21
9124
38058496
2014-10-05
2014-10-06
9125
38058496
2015-02-10
2015-02-11
9126
38058496
2015-02-21
2015-02-22
9127
38058496
2015-07-08
2015-07-09
9128
38058496
2015-08-04
2015-08-05
9129
38058496
2015-08-05
2015-08-06
9130
38058496
2015-08-06
2015-08-07
9131
38058496
2015-08-07
2015-08-08
9132
38058496
2015-08-10
2015-08-11
9133
38058496
2015-09-06
2015-09-06
9134
56819168
2005-08-17
2005-08-18
9135
56819168
2005-10-12
2005-10-12
9136
56819168
2005-10-31
2005-10-31
9137
56819168
2005-11-18
2005-11-18
9138
56819168
2006-02-03
2006-02-03
9139
56819168
2006-02-10
2006-02-13
9140
56819168
2006-05-08
2006-05-09
9141
56819168
2006-08-07
2006-08-09
9142
56819168
2006-12-01
2006-12-07
9143
56819168
2006-12-12
2006-12-12
9144
56819168
2007-02-05
2007-02-07
9145
56819168
2007-04-06
2007-04-06
9146
56819168
2007-04-09
2007-04-09
9147
56819168
2007-04-30
2007-05-01
9148
56819168
2007-05-10
2007-05-10
9149
56819168
2007-07-26
2007-07-31
9150
56819168
2007-07-30
2007-07-30
9151
56819168
2007-08-27
2007-08-27
9152
56819168
2007-09-25
2007-09-25
9153
56819168
2007-10-24
2007-10-24
9154
56819168
2007-12-15
2007-12-15
9155
56819168
2007-12-17
2007-12-17
9156
56819168
2007-12-18
2007-12-18
9157
56819168
2007-12-18
2007-12-18
9158
56819168
2008-01-18
2008-01-22
9159
56819168
2008-01-25
2008-01-29
9160
56819168
2008-01-28
2008-01-28
9161
56819168
2008-02-01
2008-02-12
9162
56819168
2008-02-04
2008-02-04
9163
56819168
2008-02-22
2008-02-25
9164
56819168
2008-02-29
2008-03-03
9165
56819168
2008-03-10
2008-03-10
9166
56819168
2008-03-19
2008-03-19
9167
56819168
2008-04-07
2008-04-07
9168
56819168
2008-05-02
2008-05-05
9169
56819168
2008-06-11
2008-06-11
9170
56819168
2008-06-18
2008-06-18
9171
56819168
2008-06-30
2008-06-30
9172
56819168
2008-09-22
2008-09-22
9173
56819168
2008-09-26
2008-10-01
9174
56819168
2008-10-05
2008-10-06
9175
56819168
2008-10-14
2008-10-17
9176
56819168
2008-11-19
2008-11-20
9177
56819168
2008-11-25
2008-11-25
9178
56819168
2008-12-04
2008-12-05
9179
56819168
2008-12-17
2008-12-26
9180
56819168
2008-12-30
2008-12-31
9181
56819168
2009-01-13
2009-01-15
9182
56819168
2009-01-13
2009-01-14
9183
56819168
2009-03-16
2009-03-17
9184
56819168
2009-03-18
2009-03-20
9185
56819168
2009-04-07
2009-04-07
9186
56819168
2009-04-22
2009-04-22
9187
56819168
2009-04-23
2009-04-23
9188
56819168
2009-05-21
2009-05-21
9189
56819168
2009-05-21
2009-05-26
9190
56819168
2009-05-29
2009-06-02
9191
56819168
2009-06-10
2009-06-10
9192
56819168
2009-06-23
2009-06-23
9193
56819168
2009-06-26
2009-06-30
9194
56819168
2009-07-01
2009-07-02
9195
56819168
2009-08-19
2009-08-20
9196
56819168
2009-08-24
2009-08-24
9197
56819168
2009-09-15
2009-09-16
9198
56819168
2009-09-16
2009-09-16
9199
56819168
2009-09-21
2009-09-22
9200
56819168
2009-09-24
2009-09-25
9201
56819168
2009-09-29
2009-09-29
9202
56819168
2009-09-30
2009-09-30
9203
56819168
2009-10-02
2009-10-07
9204
56819168
2009-10-09
2009-10-09
9205
56819168
2009-10-13
2009-10-15
9206
56819168
2009-10-14
2009-10-15
9207
56819168
2009-10-15
2009-10-16
9208
56819168
2009-10-16
2009-10-19
9209
56819168
2009-10-19
2009-10-20
9210
56819168
2009-10-20
2009-10-20
9211
56819168
2009-10-26
2009-10-29
9212
56819168
2009-10-27
2009-10-27
9213
56819168
2009-10-28
2009-10-28
9214
56819168
2009-11-04
2009-11-04
9215
56819168
2009-11-10
2009-11-10
9216
56819168
2009-11-17
2009-11-19
9217
56819168
2009-11-13
2009-11-19
9218
56819168
2009-12-01
2009-12-02
9219
56819168
2009-11-18
2009-12-03
9220
56819168
2009-11-24
2009-12-03
9221
56819168
2009-12-07
2009-12-07
9222
56819168
2009-11-20
2009-12-08
9223
56819168
2009-12-08
2009-12-08
9224
56819168
2009-12-09
2009-12-11
9225
56819168
2009-12-01
2009-12-10
9226
56819168
2009-12-11
2009-12-11
9227
56819168
2009-12-23
2009-12-23
9228
56819168
2009-12-03
2009-12-30
9229
56819168
2009-12-10
2009-12-31
9230
56819168
2009-12-31
2009-12-31
9231
56819168
2009-12-15
2009-12-31
9232
56819168
2010-01-06
2010-01-06
9233
56819168
2010-01-07
2010-01-07
9234
56819168
2010-01-12
2010-01-13
9235
56819168
2010-01-19
2010-01-19
9236
56819168
2010-01-26
2010-01-26
9237
56819168
2010-02-01
2010-02-04
9238
56819168
2010-01-27
2010-02-11
9239
56819168
2010-02-11
2010-02-11
9240
56819168
2010-02-16
2010-02-16
9241
56819168
2010-02-18
2010-02-18
9242
56819168
2010-02-24
2010-02-25
9243
56819168
2010-02-25
2010-02-25
9244
56819168
2010-03-04
2010-03-04
9245
56819168
2010-03-05
2010-03-05
9246
56819168
2010-03-08
2010-03-08
9247
56819168
2010-03-10
2010-03-10
9248
56819168
2010-03-12
2010-03-15
9249
56819168
2010-03-15
2010-03-15
9250
56819168
2010-03-22
2010-03-22
9251
56819168
2010-03-29
2010-03-29
9252
56819168
2010-03-30
2010-03-30
9253
56819168
2010-03-31
2010-03-31
9254
56819168
2010-04-05
2010-04-05
9255
56819168
2010-04-12
2010-04-12
9256
56819168
2010-04-27
2010-04-27
9257
56819168
2010-05-04
2010-05-04
9258
56819168
2010-05-11
2010-05-11
9259
56819168
2010-05-12
2010-05-12
9260
56819168
2010-05-18
2010-05-18
9261
56819168
2010-05-20
2010-05-21
9262
56819168
2010-05-24
2010-05-24
9263
56819168
2010-05-24
2010-05-24
9264
56819168
2010-05-25
2010-05-25
9265
56819168
2010-05-27
2010-05-28
9266
56819168
2010-05-27
2010-05-28
9267
56819168
2010-05-27
2010-06-01
9268
56819168
2010-06-01
2010-06-03
9269
56819168
2010-06-03
2010-06-04
9270
56819168
2010-06-08
2010-06-08
9271
56819168
2010-06-07
2010-06-08
9272
56819168
2010-06-10
2010-06-10
9273
56819168
2010-06-09
2010-06-10
9274
56819168
2010-06-15
2010-06-16
9275
56819168
2010-06-16
2010-06-16
9276
56819168
2010-06-16
2010-06-16
9277
56819168
2010-06-17
2010-06-18
9278
56819168
2010-06-25
2010-06-25
9279
56819168
2010-06-28
2010-06-28
9280
56819168
2010-05-17
2010-06-29
9281
56819168
2010-06-29
2010-06-29
9282
56819168
2010-07-06
2010-07-06
9283
56819168
2010-07-07
2010-07-07
9284
56819168
2010-07-08
2010-07-09
9285
56819168
2010-07-08
2010-07-08
9286
56819168
2010-07-09
2010-07-09
9287
56819168
2010-07-13
2010-07-13
9288
56819168
2010-07-13
2010-07-13
9289
56819168
2010-07-12
2010-07-14
9290
56819168
2010-07-14
2010-07-15
9291
56819168
2010-07-15
2010-07-16
9292
56819168
2010-07-16
2010-07-16
9293
56819168
2010-07-17
2010-07-17
9294
56819168
2010-07-19
2010-07-20
9295
56819168
2010-07-20
2010-07-20
9296
56819168
2010-07-21
2010-07-22
9297
56819168
2010-07-21
2010-07-22
9298
56819168
2010-07-23
2010-07-23
9299
56819168
2010-08-02
2010-08-03
9300
56819168
2010-08-03
2010-08-03
9301
56819168
2010-08-05
2010-08-06
9302
56819168
2010-08-10
2010-08-10
9303
56819168
2010-08-11
NaT
9304
56819168
2010-08-25
2010-08-25
9305
56819168
2010-09-02
2010-09-02
9306
56819168
2010-09-03
2010-09-07
9307
56819168
2010-09-08
2010-09-13
9308
56819168
2010-09-14
2010-09-17
9309
56819168
2010-09-10
2010-09-14
9310
56819168
2010-09-16
2010-09-17
9311
56819168
2010-09-16
2010-09-16
9312
56819168
2010-09-15
2010-09-21
9313
56819168
2010-09-21
2010-09-21
9314
56819168
2010-09-24
2010-09-27
9315
56819168
2010-09-24
2010-09-27
9316
56819168
2010-09-24
2010-09-27
9317
56819168
2010-09-22
2010-09-27
9318
56819168
2010-09-28
2010-09-28
9319
56819168
2010-09-17
2010-09-28
9320
56819168
2010-10-01
2010-10-05
9321
56819168
2010-10-04
2010-10-04
9322
56819168
2010-09-24
2010-10-04
9323
56819168
2010-09-29
2010-10-05
9324
56819168
2010-10-13
2010-10-13
9325
56819168
2010-10-01
2010-10-13
9326
56819168
2010-10-21
2010-10-21
9327
56819168
2010-10-22
2010-10-22
9328
56819168
2010-10-28
2010-10-28
9329
56819168
2010-10-28
2010-10-28
9330
56819168
2010-11-01
2010-11-01
9331
56819168
2010-11-08
2010-11-08
9332
56819168
2010-11-15
2010-11-15
9333
56819168
2010-11-22
2010-11-22
9334
56819168
2010-11-29
2010-11-29
9335
56819168
2010-12-06
2010-12-06
9336
56819168
2010-12-03
2010-12-07
9337
56819168
2010-12-10
2010-12-14
9338
56819168
2010-12-09
2010-12-10
9339
56819168
2010-12-15
2010-12-15
9340
56819168
2010-12-16
2010-12-17
9341
56819168
2010-12-20
2010-12-21
9342
56819168
2010-12-23
2010-12-23
9343
56819168
2010-12-15
2010-12-23
9344
56819168
2010-12-23
2010-12-30
9345
56819168
2011-01-03
2011-01-03
9346
56819168
2010-12-21
2011-01-04
9347
56819168
2011-01-03
2011-01-04
9348
56819168
2011-01-06
2011-01-06
9349
56819168
2011-01-07
2011-01-07
9350
56819168
2011-01-10
2011-01-10
9351
56819168
2011-01-11
2011-01-12
9352
56819168
2011-01-14
2011-01-14
9353
56819168
2011-01-19
2011-01-20
9354
56819168
2011-01-21
2011-01-21
9355
56819168
2011-01-26
2011-01-26
9356
56819168
2011-01-31
2011-01-31
9357
56819168
2011-02-03
2011-02-03
9358
56819168
2011-02-07
2011-02-07
9359
56819168
2011-02-11
2011-02-11
9360
56819168
2011-02-11
2011-02-11
9361
56819168
2011-02-23
2011-02-25
9362
56819168
2011-03-09
2011-03-09
9363
56819168
2011-03-14
2011-03-14
9364
56819168
2011-03-16
2011-03-16
9365
56819168
2011-03-31
2011-03-31
9366
56819168
2011-04-01
2011-04-01
9367
56819168
2011-04-07
2011-04-07
9368
56819168
2011-04-11
2011-04-11
9369
56819168
2011-04-18
2011-04-18
9370
56819168
2011-04-20
2011-04-20
9371
56819168
2011-04-28
2011-04-28
9372
56819168
2011-05-02
2011-05-02
9373
56819168
2011-05-09
NaT
9374
56819168
2011-05-11
2011-05-11
9375
56819168
2011-05-16
2011-05-16
9376
56819168
2011-05-17
2011-05-17
9377
56819168
2011-05-18
2011-05-19
9378
56819168
2011-05-20
2011-05-20
9379
56819168
2011-05-24
2011-05-24
9380
56819168
2011-05-31
2011-05-31
9381
56819168
2011-06-02
2011-06-02
9382
56819168
2011-06-06
2011-06-06
9383
56819168
2011-06-07
2011-06-07
9384
56819168
2011-06-09
2011-06-09
9385
56819168
2011-06-13
2011-06-17
9386
56819168
2011-06-27
2011-06-27
9387
56819168
2011-06-30
2011-06-30
9388
56819168
2011-07-07
2011-07-08
9389
56819168
2011-07-11
2011-07-11
9390
56819168
2011-07-13
2011-07-13
9391
56819168
2011-07-18
2011-07-18
9392
56819168
2011-07-21
2011-07-21
9393
56819168
2011-07-20
2011-07-21
9394
56819168
2011-07-25
2011-07-25
9395
56819168
2011-07-25
2011-07-26
9396
56819168
2011-07-26
2011-07-26
9397
56819168
2011-08-01
2011-08-01
9398
56819168
2011-08-02
2011-08-02
9399
56819168
2011-08-02
2011-08-02
9400
56819168
2011-08-08
2011-08-08
9401
56819168
2011-08-09
2011-08-11
9402
56819168
2011-08-12
2011-08-16
9403
56819168
2011-08-15
2011-08-15
9404
56819168
2011-08-17
2011-08-17
9405
56819168
2011-08-18
2011-08-19
9406
56819168
2011-08-23
2011-08-25
9407
56819168
2011-08-24
2011-08-24
9408
56819168
2011-08-30
2011-08-30
9409
56819168
2011-08-31
2011-09-01
9410
56819168
2011-09-01
2011-09-01
9411
56819168
2011-09-06
2011-09-06
9412
56819168
2011-09-12
2011-09-12
9413
56819168
2011-09-16
2011-09-16
9414
56819168
2011-09-19
2011-09-19
9415
56819168
2011-09-22
2011-09-26
9416
56819168
2011-09-28
2011-09-28
9417
56819168
2011-09-30
2011-09-30
9418
56819168
2011-10-03
2011-10-05
9419
56819168
2011-10-04
NaT
9420
56819168
2011-10-04
2011-10-06
9421
56819168
2011-10-05
2011-10-05
9422
56819168
2011-10-12
2011-10-12
9423
56819168
2011-10-14
2011-10-14
9424
56819168
2011-10-18
2011-10-18
9425
56819168
2011-10-19
2011-10-19
9426
56819168
2011-10-19
2011-10-19
9427
56819168
2011-10-21
2011-10-21
9428
56819168
2011-10-26
2011-10-26
9429
56819168
2011-10-28
2011-10-28
9430
56819168
2011-10-31
2011-10-31
9431
56819168
2011-11-01
2011-11-01
9432
56819168
2011-11-02
2011-11-02
9433
56819168
2011-11-09
2011-11-09
9434
56819168
2011-11-16
2011-11-17
9435
56819168
2011-11-15
2011-11-16
9436
56819168
2011-11-21
2011-11-21
9437
56819168
2011-11-23
2011-11-29
9438
56819168
2011-11-25
2011-11-25
9439
56819168
2011-11-30
2011-11-30
9440
56819168
2011-11-30
2011-11-30
9441
56819168
2011-12-01
2011-12-02
9442
56819168
2011-12-05
2011-12-05
9443
56819168
2011-12-06
2011-12-06
9444
56819168
2011-12-14
2011-12-14
9445
56819168
2011-12-21
2011-12-21
9446
56819168
2011-12-22
2011-12-27
9447
56819168
2011-12-28
2011-12-28
9448
56819168
2012-01-04
2012-01-04
9449
56819168
2012-01-13
2012-01-19
9450
56819168
2012-01-18
2012-01-18
9451
56819168
2012-01-19
2012-01-19
9452
56819168
2012-01-25
2012-01-25
9453
56819168
2012-01-30
2012-02-06
9454
56819168
2012-02-01
2012-02-01
9455
56819168
2012-02-08
2012-02-08
9456
56819168
2012-02-02
2012-02-09
9457
56819168
2012-02-07
2012-02-13
9458
56819168
2012-02-14
2012-02-15
9459
56819168
2012-02-09
2012-02-16
9460
56819168
2012-02-17
2012-02-17
9461
56819168
2012-02-22
2012-02-22
9462
56819168
2012-02-29
2012-02-29
9463
56819168
2012-03-01
2012-03-01
9464
56819168
2012-01-31
2012-03-01
9465
56819168
2012-02-21
2012-03-02
9466
56819168
2012-03-05
2012-03-05
9467
56819168
2012-02-23
2012-03-06
9468
56819168
2012-03-07
2012-03-07
9469
56819168
2012-02-28
2012-03-08
9470
56819168
2012-03-01
2012-03-08
9471
56819168
2012-03-20
2012-03-21
9472
56819168
2012-03-21
2012-03-21
9473
56819168
2012-03-28
2012-03-28
9474
56819168
2012-03-29
2012-04-03
9475
56819168
2012-04-02
2012-04-03
9476
56819168
2012-04-04
2012-04-04
9477
56819168
2012-04-06
2012-04-06
9478
56819168
2012-04-11
2012-04-11
9479
56819168
2012-04-13
2012-04-13
9480
56819168
2012-04-19
2012-04-19
9481
56819168
2012-04-24
2012-04-24
9482
56819168
2012-05-01
2012-05-01
9483
56819168
2012-05-02
2012-05-02
9484
56819168
2012-05-07
2012-05-09
9485
56819168
2012-05-08
2012-05-08
9486
56819168
2012-05-07
2012-05-11
9487
56819168
2012-05-15
2012-05-15
9488
56819168
2012-05-15
2012-05-15
9489
56819168
2012-05-24
2012-05-24
9490
56819168
2012-05-25
2012-05-25
9491
56819168
2012-05-18
2012-05-25
9492
56819168
2012-05-29
2012-05-29
9493
56819168
2012-05-16
2012-05-30
9494
56819168
2012-05-22
2012-05-30
9495
56819168
2012-05-29
2012-06-04
9496
56819168
2012-06-04
2012-06-04
9497
56819168
2012-06-05
2012-06-05
9498
56819168
2012-06-06
2012-06-08
9499
56819168
2012-06-07
2012-06-07
9500
56819168
2012-06-05
2012-06-08
9501
56819168
2012-06-11
2012-06-11
9502
56819168
2012-06-12
2012-06-12
9503
56819168
2012-06-13
2012-06-15
9504
56819168
2012-06-19
2012-06-19
9505
56819168
2012-06-12
2012-06-19
9506
56819168
2012-06-21
2012-06-26
9507
56819168
2012-06-21
2012-06-22
9508
56819168
2012-06-21
2012-06-21
9509
56819168
2012-06-21
2012-06-27
9510
56819168
2012-06-25
2012-06-25
9511
56819168
2012-06-19
2012-06-25
9512
56819168
2012-06-26
2012-06-26
9513
56819168
2012-06-27
2012-06-29
9514
56819168
2012-07-02
2012-07-05
9515
56819168
2012-07-06
2012-07-10
9516
56819168
2012-07-10
2012-07-10
9517
56819168
2012-07-09
2012-07-10
9518
56819168
2012-07-11
2012-07-12
9519
56819168
2012-07-13
2012-07-20
9520
56819168
2012-07-16
2012-07-20
9521
56819168
2012-07-17
2012-07-17
9522
56819168
2012-07-16
2012-07-18
9523
56819168
2012-07-20
2012-07-26
9524
56819168
2012-07-18
2012-07-20
9525
56819168
2012-07-24
2012-07-24
9526
56819168
2012-07-30
2012-07-30
9527
56819168
2012-07-30
2012-07-30
9528
56819168
2012-07-31
2012-07-31
9529
56819168
2012-07-31
2012-07-31
9530
56819168
2012-08-07
2012-08-07
9531
56819168
2012-08-08
2012-08-10
9532
56819168
2012-08-13
2012-08-13
9533
56819168
2012-08-14
2012-08-14
9534
56819168
2012-08-14
2012-08-15
9535
56819168
2012-08-17
2012-08-17
9536
56819168
2012-08-21
2012-08-21
9537
56819168
2012-07-27
2012-08-21
9538
56819168
2012-08-28
2012-08-28
9539
56819168
2012-09-10
2012-09-10
9540
56819168
2012-09-17
2012-09-17
9541
56819168
2012-09-21
2012-09-21
9542
56819168
2012-09-24
2012-09-27
9543
56819168
2012-10-01
2012-10-01
9544
56819168
2012-10-03
2012-10-04
9545
56819168
2012-10-11
2012-10-11
9546
56819168
2012-10-12
2012-10-12
9547
56819168
2012-10-17
2012-10-17
9548
56819168
2012-10-23
2012-10-23
9549
56819168
2012-10-26
2012-10-31
9550
56819168
2012-11-14
NaT
9551
56819168
2012-11-14
2012-11-14
9552
56819168
2012-11-15
2012-11-23
9553
56819168
2012-11-16
NaT
9554
56819168
2012-11-19
2012-11-20
9555
56819168
2012-11-21
2012-11-21
9556
56819168
2012-11-29
2012-11-30
9557
56819168
2012-12-04
2012-12-04
9558
56819168
2012-12-03
2012-12-04
9559
56819168
2012-12-06
2012-12-06
9560
56819168
2012-12-06
2012-12-10
9561
56819168
2012-12-10
2012-12-11
9562
56819168
2012-12-11
2012-12-11
9563
56819168
2012-12-11
2012-12-11
9564
56819168
2012-12-13
2012-12-13
9565
56819168
2012-12-13
2012-12-14
9566
56819168
2012-12-17
2012-12-17
9567
56819168
2012-12-19
2012-12-20
9568
56819168
2012-12-24
2012-12-27
9569
56819168
2012-12-26
2012-12-27
9570
56819168
2012-12-31
2012-12-31
9571
56819168
2013-01-04
2013-01-04
9572
56819168
2013-01-09
2013-01-09
9573
56819168
2013-01-18
2013-01-18
9574
56819168
2013-01-22
2013-01-23
9575
56819168
2013-02-01
2013-02-05
9576
56819168
2013-02-06
2013-02-06
9577
56819168
2013-02-07
2013-02-11
9578
56819168
2013-02-11
2013-02-12
9579
56819168
2013-02-20
2013-02-20
9580
56819168
2013-02-21
2013-02-26
9581
56819168
2013-02-25
2013-02-27
9582
56819168
2013-02-13
2013-02-28
9583
56819168
2013-02-27
2013-02-28
9584
56819168
2013-02-20
2013-03-01
9585
56819168
2013-03-04
2013-03-05
9586
56819168
2013-03-25
2013-03-28
9587
56819168
2013-03-25
2013-03-26
9588
56819168
2013-04-03
2013-04-03
9589
56819168
2013-04-04
2013-04-04
9590
56819168
2013-04-22
2013-04-26
9591
56819168
2013-04-22
2013-04-23
9592
56819168
2013-05-01
2013-05-01
9593
56819168
2013-02-15
2013-05-06
9594
56819168
2013-05-13
2013-05-13
9595
56819168
2013-05-15
2013-05-15
9596
56819168
2013-05-20
2013-05-20
9597
56819168
2013-05-21
2013-05-21
9598
56819168
2013-05-23
2013-05-28
9599
56819168
2013-05-29
2013-05-29
9600
56819168
2013-06-04
2013-06-06
9601
56819168
2013-06-20
2013-06-21
9602
56819168
2013-06-24
2013-06-25
9603
56819168
2013-06-27
2013-06-27
9604
56819168
2013-07-15
2013-07-19
9605
56819168
2013-07-18
2013-07-19
9606
56819168
2013-07-22
2013-07-22
9607
56819168
2013-07-23
2013-07-23
9608
56819168
2013-07-25
2013-07-25
9609
56819168
2013-07-30
2013-07-30
9610
56819168
2013-07-31
2013-08-01
9611
56819168
2013-08-01
2013-08-02
9612
56819168
2013-08-12
2013-08-12
9613
56819168
2013-08-15
2013-08-15
9614
56819168
2013-09-25
2013-09-25
9615
56819168
2013-10-09
2013-10-16
9616
56819168
2013-10-21
2013-10-23
9617
56819168
2013-10-22
2013-10-22
9618
56819168
2013-10-23
2013-10-23
9619
56819168
2013-11-01
2013-11-01
9620
56819168
2013-11-01
2013-11-04
9621
56819168
2014-01-02
2014-01-03
9622
56819168
2013-12-31
2014-01-06
9623
56819168
2014-01-07
2014-01-08
9624
56819168
2014-01-14
2014-01-15
9625
56819168
2014-01-15
2014-01-21
9626
56819168
2014-01-21
2014-01-22
9627
56819168
2014-01-16
2014-01-22
9628
56819168
2014-01-23
2014-01-24
9629
56819168
2014-01-27
2014-01-27
9630
56819168
2014-01-28
2014-01-29
9631
56819168
2014-01-28
2014-01-29
9632
56819168
2014-02-06
2014-02-08
9633
56819168
2014-02-21
2014-02-24
9634
56819168
2014-03-12
2014-03-12
9635
56819168
2014-04-03
2014-04-03
9636
56819168
2014-04-23
2014-04-24
9637
56819168
2014-04-29
2014-04-30
9638
56819168
2014-05-21
2014-05-21
9639
56819168
2014-05-28
2014-05-28
9640
56819168
2014-06-10
2014-06-12
9641
56819168
2014-06-11
2014-06-11
9642
56819168
2014-06-20
2014-06-23
9643
56819168
2014-06-30
2014-06-30
9644
56819168
2014-07-07
2014-07-07
9645
56819168
2014-07-21
2014-07-21
9646
56819168
2014-07-29
2014-07-30
9647
56819168
2014-08-13
2014-08-13
9648
56819168
2014-08-15
2014-08-15
9649
56819168
2014-08-25
2014-08-25
9650
56819168
2014-09-09
2014-09-09
9651
56819168
2014-09-24
2014-09-24
9652
56819168
2014-09-29
2014-09-29
9653
56819168
2014-10-03
2014-10-03
9654
56819168
2014-10-14
2014-10-14
9655
56819168
2014-11-05
2014-11-06
9656
56819168
2014-11-07
2014-11-10
9657
56819168
2014-11-14
2014-11-14
9658
56819168
2014-11-13
2014-11-14
9659
56819168
2014-11-18
2014-11-18
9660
56819168
2014-11-19
2014-11-20
9661
56819168
2014-11-25
2014-11-25
9662
56819168
2014-12-01
2014-12-01
9663
56819168
2014-12-23
2014-12-23
9664
56819168
2015-01-02
2015-01-02
9665
56819168
2015-01-07
2015-01-07
9666
56819168
2015-01-16
2015-01-20
9667
56819168
2015-01-20
2015-01-20
9668
56819168
2015-02-03
2015-02-03
9669
56819168
2015-02-22
2015-02-23
9670
56819168
2015-02-26
2015-02-27
9671
56819168
2015-02-26
2015-02-26
9672
56819168
2015-03-18
2015-03-18
9673
56819168
2015-07-31
2015-08-03
9674
56819168
2015-08-24
2015-08-25
9675
56819168
2015-10-14
2015-10-15
9676
56819168
2015-10-21
2015-10-21
9677
56819168
2015-10-22
2015-10-23
9678
56819168
2015-10-29
2015-10-29
9679
57565760
2007-04-11
2007-04-24
9680
57565760
2007-05-15
2007-05-17
9681
57565760
2010-01-05
2010-01-05
9682
57565760
2010-04-06
2010-04-07
9683
57565760
2010-07-04
2010-07-04
9684
57565760
2010-07-04
2010-07-04
9685
57565760
2010-07-05
2010-07-06
9686
57565760
2010-07-09
2010-07-10
9687
57565760
2010-07-18
2010-07-18
9688
57565760
2010-07-18
2010-07-21
9689
57565760
2010-08-04
2010-08-04
9690
57565760
2010-08-14
2010-08-16
9691
57565760
2010-08-23
2010-08-24
9692
57565760
2010-08-25
2010-08-26
9693
57565760
2010-09-02
2010-09-02
9694
57565760
2010-09-02
2010-09-03
9695
57565760
2010-09-19
2010-09-20
9696
57565760
2010-09-20
2010-09-21
9697
57565760
2010-09-21
2010-09-22
9698
57565760
2010-09-22
2010-09-27
9699
57565760
2010-09-23
2010-09-24
9700
57565760
2010-09-24
2010-09-28
9701
57565760
2010-09-24
2010-09-25
9702
57565760
2010-09-25
2010-09-27
9703
57565760
2010-10-03
2010-10-04
9704
57565760
2010-10-04
NaT
9705
57565760
2010-10-05
2010-10-08
9706
57565760
2010-10-08
2010-10-09
9707
57565760
2010-10-23
2010-10-24
9708
57565760
2010-11-03
2010-11-04
9709
57565760
2010-11-05
2010-11-06
9710
57565760
2010-11-07
2010-11-08
9711
57565760
2010-11-10
2010-11-12
9712
57565760
2010-11-13
2010-11-14
9713
57565760
2010-11-16
2010-11-19
9714
57565760
2010-11-22
2010-11-22
9715
57565760
2010-11-24
2010-11-24
9716
57565760
2010-12-05
2010-12-09
9717
57565760
2010-12-11
2010-12-14
9718
57565760
2010-12-22
2010-12-23
9719
57565760
2011-01-10
2011-01-14
9720
57565760
2011-01-11
2011-01-14
9721
57565760
2011-01-14
2011-01-14
9722
57565760
2011-02-05
2011-02-05
9723
57565760
2011-02-06
NaT
9724
57565760
2011-02-07
2011-02-08
9725
57565760
2011-02-09
2011-02-10
9726
57565760
2011-02-10
2011-02-10
9727
57565760
2011-02-11
2011-02-13
9728
57565760
2011-02-11
2011-02-11
9729
57565760
2011-02-13
2011-02-13
9730
57565760
2011-02-15
NaT
9731
57565760
2011-02-16
2011-02-16
9732
57565760
2011-02-16
2011-02-20
9733
57565760
2011-03-02
2011-03-03
9734
57565760
2011-03-03
2011-03-04
9735
57565760
2011-03-04
2011-03-05
9736
57565760
2011-03-05
2011-03-05
9737
57565760
2011-03-05
2011-03-06
9738
57565760
2011-03-05
2011-03-08
9739
57565760
2011-03-08
2011-03-09
9740
57565760
2011-03-09
2011-03-11
9741
57565760
2011-03-10
2011-03-11
9742
57565760
2011-03-11
2011-03-12
9743
57565760
2011-03-12
2011-03-13
9744
57565760
2011-04-06
2011-04-06
9745
57565760
2011-05-20
2011-05-20
9746
57565760
2011-05-24
2011-05-25
9747
57565760
2011-07-10
2011-07-11
9748
57565760
2011-07-11
2011-07-12
9749
57565760
2011-07-19
2011-07-20
9750
57565760
2011-07-20
2011-07-21
9751
57565760
2011-08-05
2011-08-05
9752
57565760
2011-08-06
2011-08-07
9753
57565760
2011-08-14
2011-08-14
9754
57565760
2011-09-02
2011-09-03
9755
57565760
2011-09-03
2011-09-04
9756
57565760
2011-09-05
2011-09-05
9757
57565760
2011-10-02
2011-10-03
9758
57565760
2011-11-27
2011-11-28
9759
57565760
2011-12-01
2011-12-02
9760
57565760
2011-12-05
2011-12-06
9761
57565760
2012-03-10
2012-03-11
9762
57565760
2012-03-12
2012-03-12
9763
57565760
2012-03-15
2012-03-15
9764
57565760
2012-03-16
2012-03-16
9765
57565760
2012-03-17
2012-03-18
9766
57565760
2012-03-18
2012-03-18
9767
57565760
2012-03-23
2012-03-24
9768
57565760
2012-03-25
NaT
9769
57565760
2012-03-27
2012-03-28
9770
57565760
2012-03-28
NaT
9771
57565760
2012-03-28
2012-03-28
9772
57565760
2012-03-29
NaT
9773
57565760
2012-04-11
2012-04-12
9774
57565760
2012-04-13
NaT
9775
57565760
2012-04-13
NaT
9776
57565760
2012-04-14
NaT
9777
57565760
2012-04-16
2012-04-17
9778
57565760
2012-04-18
2012-04-18
9779
57565760
2012-04-19
2012-04-20
9780
57565760
2012-04-29
2012-04-30
9781
57565760
2012-05-08
2012-05-08
9782
57565760
2012-05-10
2012-05-10
9783
57565760
2012-05-10
2012-05-11
9784
57565760
2012-05-12
2012-05-12
9785
57565760
2012-05-15
2012-05-15
9786
57565760
2012-05-16
2012-05-16
9787
57565760
2012-05-17
2012-05-17
9788
57565760
2012-05-18
NaT
9789
57565760
2012-05-18
2012-05-18
9790
57565760
2012-05-20
2012-05-20
9791
57565760
2012-05-20
NaT
9792
57565760
2012-05-21
2012-05-22
9793
57565760
2012-06-19
2012-06-19
9794
57565760
2012-06-20
2012-06-20
9795
57565760
2012-06-21
NaT
9796
57565760
2012-06-22
NaT
9797
57565760
2012-06-22
2012-06-23
9798
57565760
2012-06-23
2012-06-24
9799
57565760
2012-06-26
2012-06-26
9800
57565760
2012-06-26
2012-06-27
9801
57565760
2012-06-27
2012-06-28
9802
57565760
2012-07-03
2012-07-04
9803
57565760
2012-07-06
2012-07-07
9804
57565760
2012-07-14
2012-07-14
9805
57565760
2012-07-21
2012-07-22
9806
57565760
2012-07-24
2012-07-24
9807
57565760
2012-07-24
2012-07-25
9808
57565760
2012-07-27
2012-07-28
9809
57565760
2012-07-30
2012-07-31
9810
57565760
2012-07-31
2012-08-01
9811
57565760
2012-08-01
2012-08-03
9812
57565760
2012-08-05
2012-08-06
9813
57565760
2012-08-08
2012-08-09
9814
57565760
2012-08-09
2012-08-09
9815
57565760
2012-08-11
2012-08-12
9816
57565760
2012-08-17
2012-08-17
9817
57565760
2012-08-17
2012-08-18
9818
57565760
2012-08-18
2012-08-19
9819
57565760
2012-08-23
2012-08-24
9820
57565760
2012-08-31
2012-09-01
9821
57565760
2012-09-03
NaT
9822
57565760
2012-09-05
2012-09-06
9823
57565760
2012-09-06
2012-09-07
9824
57565760
2012-09-08
2012-09-09
9825
57565760
2012-10-12
2012-10-13
9826
57565760
2012-10-13
2012-10-13
9827
57565760
2012-10-14
2012-10-15
9828
57565760
2012-10-20
NaT
9829
57565760
2012-10-21
2012-10-22
9830
57565760
2012-10-23
2012-10-24
9831
57565760
2012-10-24
2012-10-25
9832
57565760
2012-10-26
2012-10-26
9833
57565760
2012-11-06
2012-11-06
9834
57565760
2012-11-07
2012-11-07
9835
57565760
2012-11-08
NaT
9836
57565760
2012-11-08
2012-11-09
9837
57565760
2012-11-12
2012-11-12
9838
57565760
2012-11-12
NaT
9839
57565760
2012-11-13
2012-11-14
9840
57565760
2012-11-19
2012-11-20
9841
57565760
2013-01-03
2013-01-04
9842
57565760
2013-01-04
2013-01-05
9843
57565760
2013-01-05
2013-01-05
9844
57565760
2013-01-14
2013-01-14
9845
57565760
2013-03-06
2013-03-07
9846
57565760
2013-03-07
2013-03-07
9847
57565760
2013-03-09
2013-03-10
9848
57565760
2013-04-06
2013-04-06
9849
57565760
2013-04-07
NaT
9850
57565760
2013-04-10
2013-04-11
9851
57565760
2013-04-11
2013-04-12
9852
57565760
2013-04-12
2013-04-13
9853
57565760
2013-04-14
2013-04-14
9854
57565760
2013-04-16
2013-04-16
9855
57565760
2013-04-16
2013-04-17
9856
57565760
2013-04-18
2013-04-21
9857
57565760
2013-04-19
2013-04-20
9858
57565760
2013-04-20
2013-04-21
9859
57565760
2013-04-21
2013-04-22
9860
57565760
2013-04-23
2013-04-24
9861
57565760
2013-04-24
NaT
9862
57565760
2013-04-25
2013-04-26
9863
57565760
2013-04-30
2013-05-01
9864
57565760
2013-05-01
2013-05-02
9865
57565760
2013-05-02
2013-05-03
9866
57565760
2013-05-04
2013-05-04
9867
57565760
2013-05-04
2013-05-05
9868
57565760
2013-05-05
NaT
9869
57565760
2013-08-04
2013-08-05
9870
57565760
2013-10-26
2013-10-28
9871
57565760
2013-11-08
2013-11-08
9872
57565760
2013-11-28
2013-11-29
9873
57565760
2013-12-03
2013-12-04
9874
57565760
2013-12-14
2013-12-14
9875
57565760
2014-01-02
2014-01-03
9876
57565760
2014-02-06
2014-02-07
9877
57565760
2014-02-10
2014-02-11
9878
57565760
2014-02-21
2014-02-21
9879
57565760
2014-02-21
2014-02-21
9880
57565760
2014-02-27
2014-02-28
9881
57565760
2014-03-17
2014-03-17
9882
57565760
2014-03-17
2014-03-17
9883
57565760
2014-03-28
2014-03-28
9884
57565760
2014-04-11
2014-04-11
9885
57565760
2014-04-12
2014-04-13
9886
57565760
2014-04-13
2014-04-14
9887
57565760
2014-04-14
2014-04-15
9888
57565760
2014-05-24
2014-05-24
9889
57565760
2014-06-08
2014-06-08
9890
57565760
2014-06-14
2014-06-18
9891
57565760
2014-06-14
NaT
9892
57565760
2014-12-27
2014-12-27
9893
57565760
2015-01-04
2015-01-04
9894
57565760
2015-04-27
2015-04-27
9895
57565760
2015-06-08
2015-06-11
9896
57565760
2015-06-18
2015-06-19
9897
57565760
2015-06-20
2015-06-23
9898
57565760
2015-08-15
2015-08-16
9899
57565760
2015-08-27
2015-08-28
9900
57565760
2015-09-12
2015-09-12
9901
57565760
2015-09-25
2015-09-26
9902
57565760
2015-10-01
2015-10-02
9903
57565760
2015-10-06
2015-10-07
9904
57565760
2015-10-09
2015-10-10
9905
57565760
2015-10-13
2015-10-13
9906
57565760
2015-10-17
2015-10-17
9907
57565760
2015-10-29
2015-10-30
9908
57565760
2015-10-31
2015-11-01
9909
57631168
2004-01-20
2004-01-20
9910
57631168
2004-01-13
2004-01-22
9911
57631168
2004-02-02
2004-02-05
9912
57631168
2004-03-15
2004-03-16
9913
57631168
2004-03-19
2004-03-19
9914
57631168
2004-04-06
2004-04-08
9915
57631168
2004-04-06
2004-04-06
9916
57631168
2004-04-06
2004-04-08
9917
57631168
2004-04-15
2004-04-15
9918
57631168
2004-04-22
2004-04-29
9919
57631168
2004-05-11
2004-05-14
9920
57631168
2004-05-21
2004-06-25
9921
57631168
2004-07-29
2004-08-13
9922
57631168
2004-08-25
2004-09-10
9923
57631168
2004-09-15
2004-09-15
9924
57631168
2005-04-21
2005-04-21
9925
57631168
2005-04-27
2005-04-27
9926
57631168
2005-05-04
2005-05-04
9927
57631168
2005-12-29
2005-12-29
9928
57631168
2006-03-15
2006-03-16
9929
57631168
2006-03-22
2006-03-24
9930
57631168
2006-05-13
2006-05-14
9931
57631168
2006-06-18
2006-06-19
9932
57631168
2006-06-21
2006-06-22
9933
57631168
2006-07-22
2006-07-22
9934
57631168
2006-07-28
2006-07-29
9935
57631168
2006-07-31
2006-08-01
9936
57631168
2006-12-15
2006-12-28
9937
57631168
2007-01-05
2007-01-09
9938
57631168
2007-02-27
2007-03-01
9939
57631168
2007-02-26
2007-03-01
9940
57631168
2009-10-23
2009-10-29
9941
57631168
2009-10-28
2009-10-29
9942
57631168
2009-11-24
2009-12-09
9943
57631168
2009-12-09
2009-12-09
9944
57631168
2010-01-20
2010-01-20
9945
57631168
2011-03-04
2011-03-04
9946
57631168
2011-09-20
2011-10-06
9947
57631168
2013-08-24
2013-08-24
9948
57631168
2013-08-24
2013-08-26
9949
57631168
2013-09-19
NaT
9950
57631168
2013-09-19
2013-09-20
9951
57631168
2013-12-11
2013-12-13
9952
57631168
2013-12-12
2013-12-20
9953
57631168
2014-01-09
2014-01-14
9954
57631168
2014-01-11
2014-01-21
9955
57631168
2014-09-03
2014-09-09
9956
57631168
2014-11-21
2014-12-01
9957
57631168
2015-07-06
2015-07-09
9958
57631168
2015-07-06
2015-07-09
9959
57631168
2015-07-07
2015-07-10
9960
57631168
2015-07-28
2015-07-28
9961
57631168
2015-07-28
2015-07-28
9962
57631168
2015-07-28
NaT
9963
57631168
2015-08-03
2015-08-03
9964
57631168
2015-08-03
2015-08-26
9965
57631168
2015-09-07
2015-09-07
9966
57708896
2007-08-09
2007-08-09
9967
57708896
2012-02-19
2012-02-19
9968
57708896
2012-11-02
2012-11-03
9969
57708896
2012-11-03
2012-11-03
9970
57708896
2012-11-06
2012-11-06
9971
57708896
2012-11-14
2012-11-14
9972
57708896
2012-11-16
2012-11-16
9973
57708896
2013-10-03
2013-10-04
9974
57708896
2014-10-07
2014-10-07
9975
57708896
2015-04-04
2015-04-04
9976
57708896
2015-04-04
2015-04-04
9977
57708896
2015-09-15
2015-09-16
9978
57708896
2015-10-16
2015-10-16
9979
60754176
2004-07-20
2004-07-21
9980
60754176
2014-06-20
2014-06-20
9981
60754176
2014-08-12
2014-08-14
9982
60754176
2014-09-16
2014-09-16
9983
60754176
2014-10-10
2014-10-14
9984
60754176
2015-01-11
2015-01-12
9985
60754176
2015-02-03
2015-02-08
9986
60754176
2015-03-24
2015-03-25
9987
60754176
2015-06-27
2015-07-07
9988
60754176
2015-08-08
2015-08-12
9989
60754176
2015-08-19
2015-08-21
9990
60754176
2015-09-19
2015-09-20
9991
60754176
2015-11-01
NaT
9992
62105792
2004-04-11
2004-04-15
9993
62105792
2014-10-03
2014-10-04
9994
62105792
2015-09-28
2015-09-29
9995
62697600
2008-10-25
2008-10-26
9996
62697600
2008-10-28
2008-11-07
9997
62697600
2008-10-25
2008-10-30
9998
62697600
2012-01-14
2012-01-14
9999
62697600
2012-01-14
2012-01-18
0
819392
NaT
NaT
1
15150240
NaT
NaT
2
28438144
NaT
NaT
3
28653184
NaT
NaT
4
30041536
NaT
NaT
5
30172352
NaT
NaT
6
44109184
NaT
NaT
7
53420640
NaT
NaT
8
63383264
NaT
NaT
9
34720
NaT
NaT
10
297472
NaT
NaT
11
2738848
NaT
NaT
12
2800672
NaT
NaT
13
4337984
NaT
NaT
14
4626720
NaT
NaT
15
6223168
NaT
NaT
16
6674528
NaT
NaT
17
7339360
NaT
NaT
18
7453600
NaT
NaT
19
11111968
NaT
NaT
20
12356512
NaT
NaT
21
13554016
NaT
NaT
22
13752928
NaT
NaT
23
14058688
NaT
NaT
24
15181376
NaT
NaT
25
19353600
NaT
NaT
26
19401984
NaT
NaT
27
20039712
NaT
NaT
28
20292384
NaT
NaT
29
20718880
NaT
NaT
30
21542752
NaT
NaT
31
22386336
NaT
NaT
32
22495648
NaT
NaT
33
24629024
NaT
NaT
34
24663968
NaT
NaT
35
24803520
NaT
NaT
36
25332832
NaT
NaT
37
26154688
NaT
NaT
38
28283808
NaT
NaT
39
29722784
NaT
NaT
40
29973888
NaT
NaT
41
30311680
NaT
NaT
42
30972256
NaT
NaT
43
31249568
NaT
NaT
44
31458112
NaT
NaT
45
32354112
NaT
NaT
46
32739168
NaT
NaT
47
33104736
NaT
NaT
48
36042944
NaT
NaT
49
37472512
NaT
NaT
50
37511488
NaT
NaT
51
41756288
NaT
NaT
52
42370720
NaT
NaT
53
42519456
NaT
NaT
54
44648352
NaT
NaT
55
45427200
NaT
NaT
56
45721536
NaT
NaT
57
46219936
NaT
NaT
58
51375520
NaT
NaT
59
52633504
NaT
NaT
60
53067840
NaT
NaT
61
53331488
NaT
NaT
62
55521760
NaT
NaT
63
55682368
NaT
NaT
64
56538944
NaT
NaT
65
60000864
NaT
NaT
66
60040960
NaT
NaT
67
61997600
NaT
NaT
68
64368864
NaT
NaT
69
64906912
NaT
NaT
70
65664032
NaT
NaT
71
66123008
NaT
NaT
72
66242400
NaT
NaT
73
67152960
NaT
NaT
74
67378976
NaT
NaT
75
69495776
NaT
NaT
76
71635200
NaT
NaT
77
71929984
NaT
NaT
78
71936032
NaT
NaT
79
72760128
NaT
NaT
80
75103616
NaT
NaT
81
76259232
NaT
NaT
82
76640704
NaT
NaT
83
76682144
NaT
NaT
84
77370048
NaT
NaT
85
77373408
NaT
NaT
86
80459680
NaT
NaT
87
82681088
NaT
NaT
88
83874784
NaT
NaT
89
83918912
NaT
NaT
90
84279776
NaT
NaT
91
84284032
NaT
NaT
92
84789600
NaT
NaT
93
85254400
NaT
NaT
94
85384320
NaT
NaT
95
85620416
NaT
NaT
96
86567936
NaT
NaT
97
86708832
NaT
NaT
10098 rows × 3 columns

Data Extraction:
The next step in the data challenge is to find out the number of days spent by the patient in the hospital. So we keep Admission date and Discharge Date column and calculate the difference to get the total number of days.
In [56]:


df2 = df1.loc[df1['DISCHARGE_DATE_TIME'].notnull(), ['ID','DISCHARGE_DATE_TIME','ADMISSION_DATE_TIME']]
df2


Out[56]:

ID
DISCHARGE_DATE_TIME
ADMISSION_DATE_TIME
0
22300992
2014-11-25
2014-11-25
1
22300992
2014-12-18
2014-12-16
2
22300992
2014-12-22
2014-12-22
3
22300992
2014-12-24
2014-12-23
4
22300992
2014-12-28
2014-12-28
5
22300992
2015-02-09
2015-02-04
6
22300992
2015-02-20
2015-02-18
7
22300992
2015-02-25
2015-02-25
8
22300992
2015-03-31
2015-03-25
9
22300992
2015-03-25
2015-03-25
11
22300992
2015-05-04
2015-05-04
12
22300992
2015-05-06
2015-05-06
13
22300992
2015-05-15
2015-05-14
14
22300992
2015-05-28
2015-05-27
15
22300992
2015-06-17
2015-06-15
16
22300992
2015-06-25
2015-06-22
17
22300992
2015-07-06
2015-07-06
18
22300992
2015-07-17
2015-07-15
19
22300992
2015-07-22
2015-07-22
20
22300992
2015-07-31
2015-07-24
21
22300992
2015-08-05
2015-08-04
22
22300992
2015-08-13
2015-08-12
23
22300992
2015-08-24
2015-08-24
24
22300992
2015-09-01
2015-08-31
25
22300992
2015-09-15
2015-09-15
26
22300992
2015-09-16
2015-09-15
27
22300992
2015-09-23
2015-09-22
28
22300992
2015-09-25
2015-09-24
29
22300992
2015-09-25
2015-09-25
30
22300992
2015-09-30
2015-09-28
31
22300992
2015-10-13
2015-10-13
32
22300992
2015-10-27
2015-10-26
33
23665824
2003-03-13
2003-03-10
34
23665824
2003-03-20
2003-03-20
35
23665824
2003-05-30
2003-05-30
36
23665824
2003-07-22
2003-07-21
37
23665824
2003-08-05
2003-08-05
39
23665824
2003-11-13
2003-11-13
40
23665824
2003-11-22
2003-11-22
41
23665824
2003-12-11
2003-12-09
42
23665824
2004-01-15
2004-01-15
43
23665824
2004-03-19
2004-03-18
44
23665824
2004-06-04
2004-06-04
45
23665824
2004-07-16
2004-07-15
46
23665824
2004-10-12
2004-10-12
47
23665824
2005-03-04
2005-03-04
48
23665824
2005-04-11
2005-04-11
50
23665824
2005-08-09
2005-08-09
51
23665824
2005-10-24
2005-10-24
52
23665824
2006-02-13
2006-02-13
53
23665824
2006-05-04
2006-05-04
54
23665824
2006-09-05
2006-09-05
55
23665824
2006-09-22
2006-09-22
56
23665824
2006-11-17
2006-11-17
57
23665824
2006-11-24
2006-11-24
58
23665824
2007-06-15
2007-06-15
59
23665824
2007-10-19
2007-10-19
60
23665824
2007-12-03
2007-11-28
61
23665824
2008-06-17
2008-06-17
62
23665824
2012-03-14
2012-03-14
63
23665824
2012-07-09
2012-07-09
64
23665824
2012-12-01
2012-12-01
65
23665824
2014-03-05
2014-03-05
66
23665824
2014-05-28
2014-05-22
67
23665824
2014-08-11
2014-08-11
68
23665824
2014-08-25
2014-08-25
69
23665824
2014-10-03
2014-09-28
70
23665824
2014-11-13
2014-11-02
71
23665824
2014-11-26
2014-11-26
72
23665824
2014-12-04
2014-11-29
73
23665824
2014-12-11
2014-12-08
74
23665824
2014-12-18
2014-12-17
75
23665824
2014-12-18
2014-12-18
76
23665824
2015-01-21
2015-01-16
77
23665824
2015-02-19
2015-02-18
78
23665824
2015-03-05
2015-03-04
79
23665824
2015-03-17
2015-03-11
80
23665824
2015-03-30
2015-03-29
81
23665824
2015-06-03
2015-06-02
82
23665824
2015-06-11
2015-06-09
83
23665824
2015-06-18
2015-06-18
84
23665824
2015-07-16
2015-07-16
85
23665824
2015-07-25
2015-07-25
86
23665824
2015-08-21
2015-07-28
87
23665824
2015-08-26
2015-08-26
88
23665824
2015-08-30
2015-08-30
89
23665824
2015-09-15
2015-09-14
90
23665824
2015-09-16
2015-09-16
92
23665824
2015-09-25
2015-09-22
93
24810016
2002-04-10
2002-04-09
94
24810016
2003-05-09
2003-05-09
95
24810016
2003-07-14
2003-07-14
96
24810016
2003-07-23
2003-07-23
97
24810016
2003-08-11
2003-08-11
98
24810016
2003-08-21
2003-08-21
99
24810016
2003-09-11
2003-09-11
100
24810016
2003-10-02
2003-10-02
101
24810016
2003-10-03
2003-10-03
102
24810016
2004-01-08
2004-01-08
103
24810016
2004-01-23
2004-01-23
104
24810016
2004-02-09
2004-02-09
105
24810016
2004-03-10
2004-03-10
106
24810016
2004-03-17
2004-03-17
107
24810016
2004-04-15
2004-04-15
108
24810016
2004-04-22
2004-04-22
109
24810016
2004-05-26
2004-05-26
110
24810016
2004-06-04
2004-06-04
111
24810016
2004-06-30
2004-06-30
112
24810016
2004-08-30
2004-08-30
113
24810016
2004-10-06
2004-10-06
114
24810016
2004-10-21
2004-10-21
115
24810016
2004-12-09
2004-12-09
116
24810016
2005-01-13
2005-01-13
117
24810016
2005-02-09
2005-02-09
118
24810016
2005-03-15
2005-03-15
119
24810016
2005-03-23
2005-03-23
120
24810016
2005-04-20
2005-04-20
121
24810016
2005-04-21
2005-04-21
122
24810016
2005-06-20
2005-06-20
123
24810016
2005-06-29
2005-06-29
124
24810016
2005-08-12
2005-08-12
125
24810016
2005-09-15
2005-09-15
126
24810016
2005-09-22
2005-09-22
127
24810016
2005-10-27
2005-10-27
128
24810016
2005-12-07
2005-12-07
129
24810016
2005-12-08
2005-12-08
130
24810016
2006-01-10
2006-01-10
131
24810016
2006-01-24
2006-01-24
132
24810016
2006-02-09
2006-02-09
133
24810016
2006-02-24
2006-02-24
134
24810016
2006-04-13
2006-04-13
135
24810016
2006-04-19
2006-04-19
136
24810016
2006-06-07
2006-06-07
137
24810016
2006-06-12
2006-06-12
138
24810016
2006-07-10
2006-07-10
139
24810016
2006-07-13
2006-07-13
140
24810016
2006-08-10
2006-08-10
141
24810016
2006-08-14
2006-08-14
142
24810016
2006-08-24
2006-08-24
143
24810016
2006-10-05
2006-10-05
144
24810016
2006-12-11
2006-12-11
145
24810016
2006-12-13
2006-12-13
146
24810016
2007-01-12
2007-01-12
147
24810016
2007-01-19
2007-01-19
148
24810016
2007-02-12
2007-02-12
149
24810016
2007-03-05
2007-03-05
150
24810016
2007-03-28
2007-03-28
151
24810016
2007-04-23
2007-04-23
152
24810016
2007-04-26
2007-04-26
153
24810016
2007-06-08
2007-06-08
154
24810016
2007-06-25
2007-06-25
155
24810016
2007-07-24
2007-07-24
156
24810016
2007-07-25
2007-07-25
157
24810016
2007-10-05
2007-10-05
158
24810016
2007-10-16
2007-10-16
159
24810016
2007-11-13
2007-11-13
160
24810016
2007-12-12
2007-12-12
161
24810016
2008-01-30
2008-01-30
162
24810016
2008-02-05
2008-02-05
163
24810016
2008-03-05
2008-03-05
164
24810016
2008-03-26
2008-03-26
165
24810016
2008-04-14
2008-04-14
166
24810016
2008-04-28
2008-04-28
167
24810016
2008-05-13
2008-05-13
168
24810016
2008-06-09
2008-06-09
169
24810016
2008-07-10
2008-07-10
170
24810016
2008-07-15
2008-07-15
171
24810016
2008-08-07
2008-08-07
172
24810016
2008-09-10
2008-09-10
173
24810016
2008-10-30
2008-10-30
174
24810016
2008-11-18
2008-11-18
175
24810016
2008-12-22
2008-12-22
176
24810016
2009-01-08
2009-01-08
177
24810016
2009-02-11
2009-02-11
178
24810016
2009-04-17
2009-04-17
179
24810016
2009-05-07
2009-05-07
180
24810016
2009-05-18
2009-05-18
181
24810016
2009-06-10
2009-06-10
182
24810016
2009-06-18
2009-06-18
183
24810016
2009-07-23
2009-07-23
184
24810016
2009-08-20
2009-08-20
185
24810016
2009-08-26
2009-08-26
186
24810016
2009-09-18
2009-09-18
187
24810016
2009-09-28
2009-09-28
188
24810016
2009-10-16
2009-10-16
189
24810016
2009-10-28
2009-10-28
190
24810016
2009-11-13
2009-11-13
191
24810016
2009-11-30
2009-11-30
192
24810016
2009-12-16
2009-12-16
193
24810016
2009-12-28
2009-12-28
194
24810016
2010-01-13
2010-01-13
195
24810016
2010-01-27
2010-01-27
196
24810016
2010-02-22
2010-02-22
197
24810016
2010-03-15
2010-03-15
198
24810016
2010-03-22
2010-03-22
199
24810016
2010-04-19
2010-04-19
200
24810016
2010-04-21
2010-04-21
201
24810016
2010-06-02
2010-06-02
202
24810016
2010-06-07
2010-06-07
203
24810016
2010-07-07
2010-07-07
204
24810016
2010-07-08
2010-07-08
205
24810016
2010-08-11
2010-08-11
206
24810016
2010-09-07
2010-09-07
207
24810016
2010-09-08
2010-09-08
208
24810016
2010-10-06
2010-10-06
209
24810016
2010-10-07
2010-10-07
210
24810016
2010-11-08
2010-11-08
211
24810016
2010-12-02
2010-12-02
212
24810016
2011-01-05
2011-01-05
213
24810016
2011-01-31
2011-01-31
214
24810016
2011-02-24
2011-02-24
215
24810016
2011-03-16
2011-03-16
216
24810016
2011-04-13
2011-04-13
217
24810016
2011-04-21
2011-04-21
218
24810016
2011-05-11
2011-05-11
219
24810016
2011-05-23
2011-05-23
220
24810016
2011-06-10
2011-06-10
221
24810016
2011-07-08
2011-07-08
222
24810016
2011-08-16
2011-08-16
223
24810016
2011-08-22
2011-08-22
224
24810016
2011-10-04
2011-10-04
225
24810016
2011-11-02
2011-11-02
226
24810016
2011-12-05
2011-12-05
227
24810016
2012-01-09
2012-01-09
228
24810016
2012-02-09
2012-02-09
229
24810016
2012-04-04
2012-04-04
230
24810016
2012-05-03
2012-05-03
231
24810016
2012-06-06
2012-06-06
232
24810016
2012-07-05
2012-07-05
233
24810016
2012-08-23
2012-08-23
234
24810016
2012-09-24
2012-09-24
235
24810016
2012-12-14
2012-12-14
236
24810016
2013-01-16
2013-01-16
237
24810016
2013-03-04
2013-03-04
238
24810016
2013-03-20
2013-03-20
239
24810016
2013-04-15
2013-04-15
240
24810016
2013-04-24
2013-04-24
241
24810016
2013-05-14
2013-05-14
242
24810016
2013-05-29
2013-05-29
243
24810016
2013-06-11
2013-06-11
244
24810016
2013-07-11
2013-07-11
245
24810016
2013-08-21
2013-08-21
246
24810016
2013-09-18
2013-09-18
247
24810016
2013-11-18
2013-11-18
248
24810016
2013-12-23
2013-12-23
249
24810016
2014-03-25
2014-03-25
250
24810016
2014-04-22
2014-04-22
251
24810016
2014-05-20
2014-05-20
252
24810016
2014-06-04
2014-06-04
253
24810016
2014-06-17
2014-06-17
254
24810016
2014-07-07
2014-07-07
255
24810016
2014-07-18
2014-07-18
256
24810016
2014-08-15
2014-08-15
257
24810016
2014-09-15
2014-09-15
258
24810016
2014-10-08
2014-10-08
259
24810016
2014-10-15
2014-10-15
260
24810016
2015-01-20
2015-01-20
261
24810016
2015-02-25
2015-02-25
262
24810016
2015-03-27
2015-03-27
263
24810016
2015-05-18
2015-05-18
264
24810016
2015-05-19
2015-05-19
265
24810016
2015-06-17
2015-06-17
266
24810016
2015-06-25
2015-06-25
267
24810016
2015-07-15
2015-07-15
268
24810016
2015-07-30
2015-07-30
269
24810016
2015-08-17
2015-08-17
270
24810016
2015-08-27
2015-08-27
271
24810016
2015-09-29
2015-09-29
272
24810016
2015-10-27
2015-10-27
273
26523392
2001-03-26
2001-03-26
274
26523392
2002-02-08
2002-02-06
275
26523392
2002-04-23
2002-04-22
276
26523392
2002-07-24
2002-07-22
277
26523392
2004-02-12
2004-02-10
278
26523392
2004-03-25
2004-03-25
279
26523392
2004-12-22
2004-12-21
280
26523392
2006-01-27
2006-01-27
281
26523392
2006-02-03
2006-02-03
282
26523392
2006-03-08
2006-03-07
283
26523392
2006-05-31
2006-05-31
284
26523392
2006-08-17
2006-08-16
285
26523392
2006-09-20
2006-09-20
286
26523392
2006-09-21
2006-09-21
287
26523392
2006-12-29
2006-12-29
288
26523392
2007-06-12
2007-06-11
289
26523392
2007-10-10
2007-10-02
290
26523392
2007-10-24
2007-10-23
291
26523392
2009-01-03
2009-01-02
292
26523392
2014-09-26
2014-09-26
293
26523392
2014-09-29
2014-09-26
294
26523392
2014-09-29
2014-09-29
296
26523392
2014-10-30
2014-10-26
297
26523392
2014-10-30
2014-10-30
298
26523392
2014-11-12
2014-11-04
299
27421632
2006-07-16
2006-07-15
300
27421632
2006-08-17
2006-08-17
301
27421632
2006-08-18
2006-08-18
302
27421632
2006-08-21
2006-08-21
303
27421632
2006-09-14
2006-08-31
304
27421632
2006-09-20
2006-09-11
305
27421632
2006-10-02
2006-09-29
306
27421632
2006-10-10
2006-10-10
307
27421632
2006-10-17
2006-10-04
308
27421632
2006-10-17
2006-10-06
309
27421632
2006-12-13
2006-12-06
310
27421632
2006-12-23
2006-12-23
311
27421632
2007-01-18
2007-01-11
312
27421632
2007-02-27
2007-02-15
313
27421632
2007-02-27
2007-02-16
314
27421632
2007-03-04
2007-03-04
315
27421632
2007-03-21
2007-03-16
316
27421632
2007-03-19
2007-03-16
317
27421632
2007-06-19
2007-06-18
318
27421632
2007-08-03
2007-08-03
319
27421632
2009-07-03
2009-07-02
320
27421632
2009-07-10
2009-07-07
321
27421632
2009-07-22
2009-07-10
322
27421632
2010-01-07
2010-01-06
323
27421632
2010-02-04
2010-02-01
324
27421632
2010-03-02
2010-03-02
325
27421632
2010-06-01
2010-05-31
326
27421632
2010-07-15
2010-03-01
327
27421632
2010-07-16
2010-04-01
328
27421632
2010-07-16
2010-05-01
329
27421632
2010-09-01
2010-06-01
330
27421632
2010-09-01
2010-07-01
331
27421632
2010-10-01
2010-08-01
332
27421632
2010-12-20
2010-09-01
333
27421632
2011-01-11
2010-10-01
334
27421632
2011-02-02
2010-11-01
335
27421632
2011-03-24
2011-01-01
336
27421632
2011-03-24
2011-02-01
337
27421632
2011-04-26
2010-12-01
338
27421632
2011-04-26
2011-03-01
339
27421632
2011-04-28
2011-04-27
340
27421632
2011-06-17
2011-04-01
341
27421632
2011-06-17
2011-05-01
342
27421632
2011-07-27
2011-06-01
343
27421632
2011-08-17
2011-07-01
344
27421632
2011-09-26
2011-08-01
345
27421632
2011-10-31
2011-09-01
346
27421632
2011-12-15
2011-12-15
347
27421632
2011-12-16
2011-10-01
348
27421632
2012-01-26
2012-01-26
349
27421632
2012-04-05
2012-04-05
350
27421632
2012-02-01
2011-11-01
351
27421632
2012-02-22
2012-02-22
352
27421632
2012-05-23
2011-12-01
353
27421632
2012-06-07
2012-06-06
354
27421632
2012-06-19
2012-01-01
355
27421632
2012-07-19
2012-02-01
356
27421632
2012-08-30
2012-08-28
357
27421632
2012-09-28
2012-03-01
358
27421632
2012-09-28
2012-04-01
359
27421632
2012-11-18
2012-11-18
360
27421632
2012-06-01
2012-06-01
361
27421632
2012-12-05
2012-05-01
362
27421632
2013-02-06
2013-01-07
363
27421632
2013-02-08
2012-08-01
364
27421632
2013-02-10
2013-02-10
365
27421632
2013-02-12
2012-09-01
366
27421632
2013-02-26
2012-12-01
367
27421632
2013-04-30
2013-04-30
368
27421632
2013-06-05
2013-06-04
369
27421632
2013-06-12
2013-02-01
370
27421632
2013-07-10
2013-07-09
371
27421632
2013-11-15
2013-11-14
372
27421632
2014-01-23
2014-01-23
373
27421632
2014-02-04
2014-02-03
374
27421632
2014-02-10
2014-02-10
375
27421632
2014-03-06
2014-03-04
376
27421632
2014-03-21
2014-03-18
377
27421632
2014-05-05
2014-05-05
378
27421632
2014-05-16
2014-05-16
379
27421632
2014-06-18
2014-06-18
380
27421632
2014-09-14
2014-09-12
381
27421632
2014-09-22
2014-09-22
382
27421632
2014-10-31
2014-10-30
383
27421632
2014-11-02
2014-11-01
385
27421632
2014-12-08
2014-12-08
386
27421632
2014-12-07
2014-12-06
387
27421632
2015-07-20
2015-07-20
388
27421632
2015-07-29
2015-07-26
389
27421632
2015-08-13
2015-08-04
390
27421632
2015-08-29
2015-08-28
391
27421632
2015-10-09
2015-10-09
392
27421632
2015-10-30
2015-10-20
393
28438144
2000-07-01
2000-06-13
394
28438144
2000-12-29
2000-12-22
395
28438144
2002-09-03
2002-09-03
396
28438144
2003-03-07
2003-03-04
397
28438144
2003-10-14
2003-10-09
398
28438144
2003-10-27
2003-10-20
399
28438144
2003-10-28
2003-10-28
400
28438144
2003-11-07
2003-11-01
401
28438144
2003-12-03
2003-12-02
402
28438144
2003-12-23
2003-12-12
403
28438144
2004-01-07
2003-12-17
405
28438144
2004-02-20
2004-02-12
406
28438144
2004-02-26
2004-02-25
407
28438144
2004-05-01
2004-04-26
408
28438144
2004-05-07
2004-05-05
409
28438144
2004-05-13
2004-05-12
410
28438144
2004-07-10
2004-07-10
411
28438144
2004-09-22
2004-09-21
412
28438144
2004-12-01
2004-12-01
413
28438144
2004-12-08
2004-12-08
414
28438144
2005-04-07
2005-04-07
415
28438144
2005-05-02
2005-04-30
416
28438144
2005-05-03
2005-05-02
417
28438144
2005-10-25
2005-10-25
418
28438144
2005-10-26
2005-10-26
419
28438144
2005-11-04
2005-11-02
420
28438144
2006-01-26
2006-01-26
421
28438144
2006-04-04
2006-04-03
422
28438144
2006-11-08
2006-11-06
423
28438144
2006-11-16
2006-11-13
424
28438144
2006-11-21
2006-11-20
425
28438144
2007-02-09
2007-02-06
426
28438144
2007-02-08
2007-02-07
427
28438144
2007-03-15
2007-03-13
428
28438144
2007-03-27
2007-03-27
429
28438144
2007-05-22
2007-05-22
430
28438144
2007-06-13
2007-06-12
431
28438144
2007-06-14
2007-06-14
432
28438144
2007-06-25
2007-06-25
433
28438144
2011-01-14
2011-01-14
434
28438144
2011-04-05
2011-04-02
435
28438144
2012-01-06
2011-12-17
436
28438144
2012-01-10
2012-01-10
437
28438144
2012-01-12
2012-01-12
438
28438144
2012-01-19
2012-01-19
439
28438144
2012-01-25
2012-01-24
441
28438144
2012-02-14
2012-02-14
442
28438144
2012-03-08
2012-02-23
443
28438144
2012-03-19
2012-03-13
444
28438144
2012-03-16
2012-03-15
445
28438144
2012-03-23
2012-03-20
446
28438144
2012-03-29
2012-03-27
447
28438144
2012-03-30
2012-03-29
448
28438144
2012-04-05
2012-04-05
449
28438144
2012-04-06
2012-04-04
450
28438144
2012-04-12
2012-04-09
451
28438144
2012-04-13
2012-04-12
452
28438144
2012-04-24
2012-04-20
453
28438144
2012-05-09
2012-05-08
454
28438144
2012-05-18
2012-05-18
455
28438144
2012-08-31
2012-08-27
456
28438144
2012-09-07
2012-09-07
459
28438144
2012-09-14
2012-09-13
460
28438144
2012-09-28
2012-09-26
461
28438144
2012-10-02
2012-09-28
462
28438144
2012-10-09
2012-10-09
463
28438144
2012-10-19
2012-10-18
464
28438144
2012-10-22
2012-10-22
465
28438144
2012-11-15
2012-11-15
466
28438144
2012-12-01
2012-11-28
467
28438144
2013-01-15
2013-01-10
468
28438144
2013-01-23
2013-01-22
469
28438144
2013-01-25
2013-01-24
470
28438144
2013-01-26
2013-01-25
471
28438144
2013-01-27
2013-01-27
472
28438144
2013-07-03
2013-07-02
473
28438144
2014-02-28
2014-02-28
474
28438144
2014-04-10
2014-03-28
475
28438144
2014-07-02
2014-05-13
476
28438144
2014-07-29
2014-07-29
477
28438144
2014-07-12
2014-07-12
478
28438144
2014-07-18
2014-07-13
479
28438144
2014-10-13
2014-10-12
480
28438144
2014-10-21
2014-10-15
481
28438144
2014-10-30
2014-10-28
482
28438144
2015-04-09
2015-04-08
483
28438144
2015-05-09
2015-05-05
484
28438144
2015-06-03
2015-06-03
485
28438144
2015-06-12
2015-06-11
486
28438144
2015-07-02
2015-06-21
487
28653184
2003-04-12
2003-04-10
488
28653184
2003-05-16
2003-05-15
489
28653184
2003-05-16
2003-05-16
490
28653184
2003-05-19
2003-05-16
491
28653184
2003-10-02
2003-10-02
492
28653184
2004-11-08
2004-11-08
493
28653184
2005-01-16
2005-01-15
494
28653184
2005-04-19
2005-04-16
495
28653184
2005-09-23
2005-09-19
496
28653184
2006-01-07
2006-01-06
497
28653184
2006-02-24
2006-02-20
498
28653184
2007-01-06
2007-01-05
499
28653184
2007-04-20
2007-04-18
500
28653184
2007-07-13
2007-07-12
501
28653184
2007-07-25
2007-07-24
502
28653184
2007-08-24
2007-08-23
503
28653184
2007-09-11
2007-09-10
504
28653184
2007-09-16
2007-09-15
505
28653184
2007-09-25
2007-09-24
506
28653184
2007-09-28
2007-09-25
507
28653184
2007-10-07
2007-10-07
508
28653184
2008-01-13
2008-01-12
509
28653184
2008-01-18
2008-01-17
510
28653184
2008-01-23
2008-01-21
511
28653184
2008-03-02
2008-03-02
512
28653184
2008-03-03
2008-03-03
513
28653184
2008-04-05
2008-04-04
514
28653184
2008-04-18
2008-04-12
515
28653184
2008-04-23
2008-04-23
516
28653184
2008-04-24
2008-04-23
517
28653184
2008-06-02
2008-06-01
518
28653184
2008-10-07
2008-09-25
519
28653184
2008-11-18
2008-11-16
520
28653184
2008-12-10
2008-12-10
521
28653184
2008-12-13
2008-12-11
522
28653184
2008-12-25
2008-12-24
523
28653184
2009-01-14
2009-01-13
524
28653184
2009-01-17
2009-01-14
525
28653184
2009-01-21
2009-01-21
526
28653184
2009-10-07
2009-10-05
527
28653184
2009-11-12
2009-11-10
528
28653184
2009-11-26
2009-11-23
529
28653184
2009-11-25
2009-11-23
530
28653184
2010-01-09
2010-01-08
531
28653184
2010-01-15
2010-01-14
532
28653184
2010-03-26
2010-03-25
533
28653184
2010-05-11
2010-05-10
534
28653184
2010-05-13
2010-05-11
535
28653184
2010-06-28
2010-06-27
536
28653184
2010-08-21
2010-08-20
537
28653184
2010-10-15
2010-10-09
538
28653184
2010-10-10
2010-10-09
539
28653184
2010-11-09
2010-10-25
540
28653184
2010-12-30
2010-12-30
541
28653184
2010-12-31
2010-12-30
542
28653184
2011-01-05
2011-01-04
543
28653184
2011-02-01
2011-02-01
544
28653184
2011-02-02
2011-02-02
545
28653184
2011-02-22
2011-02-21
546
28653184
2011-03-06
2011-03-05
547
28653184
2011-04-10
2011-04-09
548
28653184
2011-05-07
2011-05-06
549
28653184
2011-05-30
2011-05-29
550
28653184
2011-08-18
2011-08-17
551
28653184
2011-10-23
2011-10-23
552
28653184
2011-10-25
2011-10-23
553
28653184
2011-10-25
2011-10-24
554
28653184
2011-12-01
2011-11-30
555
28653184
2012-01-10
2012-01-09
556
28653184
2012-01-28
2012-01-28
557
28653184
2012-02-06
2012-02-05
558
28653184
2012-03-26
2012-03-26
559
28653184
2012-06-04
2012-06-04
560
28653184
2012-06-24
2012-06-23
561
28653184
2012-09-03
2012-09-02
562
28653184
2012-11-07
2012-11-06
563
28653184
2012-11-17
2012-11-15
564
28653184
2012-12-05
2012-12-04
565
28653184
2012-12-25
2012-12-24
566
28653184
2013-02-13
2013-02-12
567
28653184
2013-02-20
2013-02-17
568
28653184
2013-02-22
2013-02-17
569
28653184
2013-03-31
2013-03-30
570
28653184
2013-04-04
2013-04-03
571
28653184
2013-04-06
2013-04-06
572
28653184
2013-04-08
2013-04-07
573
28653184
2013-04-24
2013-04-24
574
28653184
2013-05-01
2013-04-30
575
28653184
2013-05-03
2013-05-03
576
28653184
2013-05-13
2013-05-12
577
28653184
2013-05-25
2013-05-24
578
28653184
2013-06-06
2013-06-05
579
28653184
2013-06-08
2013-06-07
580
28653184
2013-06-09
2013-06-08
581
28653184
2013-06-27
2013-06-27
582
28653184
2013-07-12
2013-07-12
583
28653184
2013-07-18
2013-07-17
584
28653184
2013-09-10
2013-09-09
585
28653184
2013-09-18
2013-09-17
586
28653184
2013-09-30
2013-09-29
587
28653184
2013-10-01
2013-09-30
588
28653184
2013-10-10
2013-10-10
589
28653184
2013-11-02
2013-11-01
590
28653184
2013-11-20
2013-11-19
591
28653184
2013-11-21
2013-11-20
592
28653184
2013-11-22
2013-11-21
593
28653184
2013-11-28
2013-11-28
594
28653184
2013-12-01
2013-11-29
595
28653184
2013-12-27
2013-12-26
596
28653184
2014-01-02
2014-01-02
597
28653184
2014-01-14
2014-01-03
598
28653184
2014-02-01
2014-01-31
599
28653184
2014-02-09
2014-02-08
600
28653184
2014-04-06
2014-04-05
601
28653184
2014-04-09
2014-04-09
602
28653184
2014-04-25
2014-04-24
603
28653184
2014-05-26
2014-05-25
604
28653184
2014-06-10
2014-06-09
605
28653184
2014-06-13
2014-06-12
606
28653184
2014-07-24
2014-07-22
607
28653184
2014-08-01
2014-07-31
608
28653184
2014-08-06
2014-08-06
609
28653184
2014-08-09
2014-08-07
610
28653184
2014-08-21
2014-08-20
611
28653184
2014-09-04
2014-09-04
612
28653184
2014-09-21
2014-09-20
613
28653184
2014-09-28
2014-09-28
614
28653184
2014-09-30
2014-09-29
615
28653184
2014-10-04
2014-10-03
616
28653184
2014-10-17
2014-10-17
617
28653184
2014-11-05
2014-11-04
618
28653184
2014-12-02
2014-12-01
619
28653184
2015-01-01
2014-12-31
620
28653184
2015-01-22
2015-01-22
621
28653184
2015-02-05
2015-02-05
622
28653184
2015-02-20
2015-02-19
623
28653184
2015-03-10
2015-03-10
624
28653184
2015-03-16
2015-03-16
625
28653184
2015-03-22
2015-03-22
626
28653184
2015-03-28
2015-03-27
627
28653184
2015-04-30
2015-04-30
628
28653184
2015-05-05
2015-05-04
629
28653184
2015-06-19
2015-06-19
630
28653184
2015-07-21
2015-07-20
631
28653184
2015-08-07
2015-08-07
632
28653184
2015-08-09
2015-08-08
633
28653184
2015-08-29
2015-08-28
634
28653184
2015-09-07
2015-09-07
635
28653184
2015-10-06
2015-10-06
636
28653184
2015-10-26
2015-10-16
637
28990976
2003-06-11
2003-06-06
638
28990976
2003-07-15
2003-07-09
639
28990976
2003-11-26
2003-11-17
640
28990976
2003-12-02
2003-11-29
641
28990976
2004-01-06
2004-01-05
642
28990976
2004-01-26
2004-01-19
643
28990976
2006-02-07
2006-02-03
644
28990976
2006-04-17
2006-04-12
645
28990976
2006-08-23
2006-08-20
646
28990976
2006-09-06
2006-08-31
647
28990976
2007-03-13
2007-03-08
648
28990976
2007-05-13
2007-05-10
649
28990976
2007-05-14
2007-05-14
650
28990976
2007-05-14
2007-05-14
651
28990976
2007-08-03
2007-07-30
652
28990976
2007-10-28
2007-10-23
653
28990976
2007-11-09
2007-11-09
654
28990976
2007-12-21
2007-12-17
655
28990976
2008-10-27
2008-10-20
656
28990976
2008-12-19
2008-12-17
657
28990976
2009-02-18
2009-02-16
658
28990976
2009-02-27
2009-02-26
659
28990976
2009-06-01
2009-05-29
660
28990976
2009-07-05
2009-07-03
661
28990976
2009-07-23
2009-07-23
662
28990976
2011-08-02
2011-08-01
663
28990976
2012-01-16
2012-01-16
664
28990976
2012-01-17
2012-01-16
665
28990976
2012-04-13
2012-04-13
666
28990976
2012-07-23
2012-07-21
667
28990976
2012-07-30
2012-07-22
669
28990976
2012-08-19
2012-08-18
670
28990976
2014-02-11
2014-02-11
671
28990976
2014-02-20
2014-02-12
672
28990976
2014-11-07
2014-10-25
673
28990976
2015-01-14
2015-01-04
674
28990976
2015-02-03
2015-02-01
675
28990976
2015-04-07
2015-03-29
676
28990976
2015-04-26
2015-04-23
677
29779232
2001-10-11
2001-09-13
679
29779232
2012-10-19
2012-10-07
680
29779232
2012-11-14
2012-11-11
681
29779232
2012-11-13
2012-11-11
683
29779232
2012-11-14
2012-11-13
685
29779232
2012-12-06
2012-11-17
687
29779232
2012-12-07
2012-12-07
689
29779232
2012-12-18
2012-12-18
690
29779232
2013-06-06
2013-06-06
691
29779232
2013-06-07
2013-06-06
692
29779232
2013-08-08
2013-08-08
693
29779232
2013-08-12
2013-08-08
695
29779232
2013-08-12
2013-08-11
697
29779232
2013-08-14
2013-08-12
699
29779232
2013-08-14
2013-08-12
700
29779232
2013-08-13
2013-08-13
701
29779232
2013-08-20
2013-08-17
702
29779232
2013-08-20
2013-08-17
703
29779232
2013-08-12
2013-08-12
704
29779232
2013-08-27
2013-08-26
705
29779232
2013-09-09
2013-09-01
706
29779232
2013-09-12
2013-09-03
707
29779232
2013-09-11
2013-08-12
708
29779232
2013-09-16
2013-09-16
709
29779232
2013-09-18
2013-09-16
710
29779232
2013-09-30
2013-09-29
711
29779232
2013-10-01
2013-09-29
712
29779232
2013-10-13
2013-10-06
713
29779232
2013-10-07
2013-10-06
714
29779232
2013-12-05
2013-12-01
715
29779232
2013-12-12
2013-12-02
716
29779232
2014-02-21
2014-02-19
717
29779232
2014-03-05
2014-02-20
718
29779232
2014-03-13
2014-03-11
719
29779232
2014-03-26
2014-03-11
720
29779232
2014-07-24
2014-07-23
721
29779232
2014-08-07
2014-07-26
722
29779232
2014-10-22
2014-10-04
723
29779232
2014-10-27
2014-10-25
724
29779232
2014-12-02
2014-11-21
725
29779232
2014-12-15
2014-12-04
726
29779232
2015-02-06
2015-02-06
727
29779232
2015-05-11
2015-05-02
728
29779232
2015-07-10
2015-07-09
729
29779232
2015-10-22
2015-10-21
730
29779232
2015-10-27
2015-10-24
731
29779232
2015-10-31
2015-10-30
732
30041536
1995-01-17
1995-01-17
733
30041536
2000-10-05
2000-10-05
734
30041536
2001-01-11
2001-01-09
735
30041536
2001-07-31
2001-07-30
736
30041536
2001-08-07
2001-08-07
737
30041536
2002-02-11
2002-02-11
738
30041536
2002-03-05
2002-03-04
739
30041536
2002-03-27
2002-03-25
740
30041536
2002-09-03
2002-09-03
741
30041536
2002-09-04
2002-09-04
742
30041536
2003-01-28
2003-01-28
743
30041536
2003-04-14
2003-04-14
744
30041536
2003-05-01
2003-05-01
745
30041536
2003-05-08
2003-05-08
746
30041536
2003-06-10
2003-06-10
747
30041536
2003-08-11
2003-08-11
748
30041536
2003-08-29
2003-08-27
749
30041536
2003-09-04
2003-09-04
750
30041536
2003-09-11
2003-09-11
751
30041536
2003-09-30
2003-09-25
752
30041536
2003-10-17
2003-10-14
753
30041536
2003-10-21
2003-10-21
754
30041536
2003-11-12
2003-11-12
755
30041536
2004-01-23
2004-01-16
756
30041536
2004-01-21
2004-01-21
757
30041536
2004-02-05
2004-02-05
758
30041536
2004-02-26
2004-02-19
759
30041536
2004-02-27
2004-02-27
760
30041536
2004-03-09
2004-03-09
761
30041536
2004-03-17
2004-03-17
762
30041536
2004-03-25
2004-03-25
763
30041536
2004-04-20
2004-04-20
764
30041536
2004-05-27
2004-05-27
765
30041536
2004-06-01
2004-06-01
766
30041536
2004-06-04
2004-06-04
767
30041536
2004-06-15
2004-06-09
768
30041536
2004-06-10
2004-06-10
769
30041536
2004-06-14
2004-06-14
770
30041536
2004-06-16
2004-06-16
771
30041536
2004-08-12
2004-08-12
772
30041536
2004-08-19
2004-08-19
773
30041536
2004-08-24
2004-08-24
774
30041536
2004-09-02
2004-09-02
775
30041536
2004-09-23
2004-09-23
776
30041536
2004-10-06
2004-10-06
777
30041536
2004-10-18
2004-10-18
778
30041536
2004-11-17
2004-11-17
779
30041536
2004-11-23
2004-11-23
780
30041536
2004-11-30
2004-11-30
781
30041536
2004-12-21
2004-12-21
782
30041536
2005-03-08
2005-03-08
783
30041536
2005-03-10
2005-03-10
784
30041536
2005-03-24
2005-03-23
785
30041536
2005-04-19
2005-04-19
786
30041536
2005-04-20
2005-04-20
787
30041536
2005-05-26
2005-05-26
788
30041536
2005-06-14
2005-06-14
789
30041536
2005-06-16
2005-06-16
790
30041536
2005-07-12
2005-07-12
791
30041536
2005-07-20
2005-07-14
792
30041536
2005-07-18
2005-07-18
793
30041536
2005-08-09
2005-08-09
794
30041536
2005-09-28
2005-09-28
795
30041536
2005-11-03
2005-11-03
796
30041536
2005-11-15
2005-11-15
797
30041536
2005-11-22
2005-11-22
798
30041536
2005-11-29
2005-11-29
799
30041536
2006-03-23
2006-03-23
800
30041536
2006-03-29
2006-03-28
801
30041536
2006-03-28
2006-03-28
802
30041536
2006-04-19
2006-04-19
803
30041536
2006-04-27
2006-04-27
804
30041536
2006-05-12
2006-05-02
805
30041536
2006-05-16
2006-05-11
806
30041536
2006-05-16
2006-05-16
807
30041536
2006-07-18
2006-07-18
808
30041536
2006-08-30
2006-08-29
809
30041536
2006-09-05
2006-09-05
810
30041536
2006-10-10
2006-10-06
811
30041536
2006-10-10
2006-10-10
812
30041536
2006-10-11
2006-10-11
813
30041536
2007-01-16
2007-01-16
814
30041536
2007-02-01
2007-01-30
815
30041536
2007-03-27
2007-03-23
816
30041536
2007-03-30
2007-03-30
817
30041536
2007-05-02
2007-05-02
818
30041536
2007-05-10
2007-05-10
820
30041536
2007-06-12
2007-06-12
822
30041536
2007-07-12
2007-07-12
823
30041536
2007-07-23
2007-07-23
824
30041536
2007-08-17
2007-08-16
825
30041536
2007-09-25
2007-09-25
826
30041536
2007-10-11
2007-10-11
827
30041536
2007-10-16
2007-10-16
828
30041536
2007-11-01
2007-11-01
829
30041536
2007-11-09
2007-11-08
830
30041536
2007-11-20
2007-11-20
831
30041536
2007-12-18
2007-12-18
832
30041536
2007-12-18
2007-12-18
833
30041536
2008-03-10
2008-03-10
834
30041536
2008-03-20
2008-03-20
835
30041536
2008-03-27
2008-03-27
836
30041536
2008-04-10
2008-04-10
837
30041536
2008-04-16
2008-04-16
838
30041536
2008-05-09
2008-05-02
839
30041536
2008-05-16
2008-05-14
840
30041536
2008-06-25
2008-06-25
841
30041536
2008-06-26
2008-06-25
842
30041536
2008-08-14
2008-08-14
843
30041536
2008-08-21
2008-08-21
844
30041536
2008-09-09
2008-09-09
845
30041536
2008-10-02
2008-09-24
846
30041536
2008-09-24
2008-09-24
847
30041536
2008-10-01
2008-09-30
848
30041536
2008-10-09
2008-10-09
849
38659264
2003-12-30
2003-12-30
850
38659264
2004-04-13
2004-04-13
851
38659264
2006-12-20
2006-12-17
852
38659264
2006-12-28
2006-12-28
853
38659264
2007-01-03
2006-12-28
854
38659264
2008-05-21
2008-05-21
855
38659264
2008-07-03
2008-07-03
856
38659264
2008-10-16
2008-10-16
857
38659264
2009-02-23
2009-02-23
858
38659264
2009-12-05
2009-12-04
859
38659264
2009-12-07
2009-12-07
860
38659264
2009-12-07
2009-12-07
861
38659264
2010-02-09
2010-02-09
862
38659264
2010-07-09
2010-07-08
863
38659264
2010-08-03
2010-08-03
864
38659264
2010-10-25
2010-10-25
865
38659264
2011-01-25
2011-01-24
866
38659264
2011-05-28
2011-05-28
867
38659264
2011-06-09
2011-06-09
868
38659264
2011-06-21
2011-06-21
869
38659264
2011-12-22
2011-12-21
870
38659264
2012-04-03
2012-04-02
871
38659264
2012-05-11
2012-05-11
872
38659264
2012-05-22
2012-05-20
873
38659264
2012-05-23
2012-05-21
874
38659264
2012-07-20
2012-07-19
875
38659264
2012-07-30
2012-07-30
876
38659264
2013-12-13
2013-11-30
878
38659264
2013-12-31
2013-12-26
879
38659264
2014-01-15
2014-01-14
880
38659264
2014-02-07
2014-02-07
881
38659264
2014-02-12
2014-02-12
882
38659264
2014-02-19
2014-02-19
883
38659264
2014-09-24
2014-09-23
884
38659264
2015-07-22
2015-07-20
885
38659264
2015-07-29
2015-07-28
886
39541824
2005-01-07
2005-01-05
887
39541824
2007-04-22
2007-04-22
888
39541824
2008-02-23
2008-02-18
889
39541824
2008-06-03
2008-05-28
890
39541824
2008-06-17
2008-06-04
891
39541824
2008-06-30
2008-06-24
892
39541824
2008-07-08
2008-06-27
893
39541824
2009-05-30
2009-05-29
894
39541824
2011-04-11
2011-04-10
895
39541824
2011-04-11
2011-04-10
896
39541824
2011-04-15
2011-04-14
897
39541824
2011-04-20
2011-04-19
898
39541824
2012-01-20
2012-01-19
899
39541824
2013-04-26
2013-04-25
900
39541824
2013-04-27
2013-04-26
901
39541824
2013-04-28
2013-04-27
902
39541824
2013-04-29
2013-04-28
903
39541824
2014-09-24
2014-09-24
904
40160736
2002-11-13
2002-11-13
905
40160736
2003-04-16
2003-04-16
906
40160736
2003-05-19
2003-05-16
907
40160736
2003-05-23
2003-05-20
908
40160736
2003-05-21
2003-05-21
909
40160736
2003-07-09
2003-07-09
910
40160736
2003-07-23
2003-07-23
911
40160736
2003-07-28
2003-07-28
912
40160736
2004-02-04
2004-02-04
913
40160736
2006-10-19
2006-10-18
915
40160736
2007-04-17
2007-04-12
916
40160736
2007-04-13
2007-04-13
917
40160736
2007-09-11
2007-09-10
918
40160736
2007-10-03
2007-10-03
919
40160736
2007-10-09
2007-10-09
920
40160736
2008-01-03
2008-01-03
921
40160736
2008-01-14
2008-01-11
922
40160736
2008-06-06
2008-06-04
923
40160736
2008-06-13
2008-06-12
924
40160736
2008-09-22
2008-09-22
925
40160736
2009-01-06
2009-01-06
926
40160736
2009-01-06
2009-01-06
927
40160736
2009-03-02
2009-03-02
928
40160736
2009-04-27
2009-04-27
929
40160736
2009-08-26
2009-08-26
930
40160736
2010-02-01
2010-02-01
931
40160736
2010-02-18
2010-02-18
932
40160736
2010-05-03
2010-05-03
933
40160736
2010-08-02
2010-08-02
934
40160736
2010-11-01
2010-10-29
935
40160736
2010-11-05
2010-11-05
936
40160736
2010-11-08
2010-11-05
937
40160736
2011-02-09
2011-02-09
938
40160736
2011-09-13
2011-09-12
939
40160736
2011-11-29
2011-11-29
940
40160736
2011-12-02
2011-12-02
941
40160736
2012-02-09
2012-02-09
942
40160736
2012-03-08
2012-03-07
943
40160736
2012-10-08
2012-10-08
944
40160736
2012-10-12
2012-10-09
945
40160736
2012-10-12
2012-10-11
946
40160736
2012-11-27
2012-11-27
947
40160736
2013-02-26
2013-02-26
948
40160736
2013-05-06
2013-05-03
949
40160736
2013-07-19
2013-07-19
950
40160736
2013-08-19
2013-08-19
951
40160736
2013-08-02
2013-08-01
952
40160736
2015-05-22
2015-05-22
953
40160736
2015-06-08
2015-05-08
954
40160736
2015-06-11
2015-06-03
955
40160736
2015-06-12
2015-05-21
956
40160736
2015-06-18
2015-06-17
957
40160736
2015-06-22
2015-06-19
958
40160736
2015-06-22
2015-06-11
959
40160736
2015-07-06
2015-07-02
960
40160736
2015-07-07
2015-07-06
961
40160736
2015-07-10
2015-07-09
962
40160736
2015-07-29
2015-07-16
963
40160736
2015-08-05
2015-08-04
964
40160736
2015-08-11
2015-08-10
965
40160736
2015-08-14
2015-08-13
966
40160736
2015-08-17
2015-08-14
967
40160736
2015-09-01
2015-08-31
968
40160736
2015-09-04
2015-09-02
969
40160736
2015-09-04
2015-09-03
970
40160736
2015-09-22
2015-09-10
971
40160736
2015-09-22
2015-09-11
972
40160736
2015-09-23
2015-09-21
973
40160736
2015-09-23
2015-09-17
974
40160736
2015-09-30
2015-09-30
975
40160736
2015-10-09
2015-10-01
976
40160736
2015-10-16
2015-10-08
977
40160736
2015-10-16
2015-10-15
978
40160736
2015-10-20
2015-10-14
979
40160736
2015-10-28
2015-10-22
980
40674368
2010-07-01
2010-06-30
981
40674368
2012-07-08
2012-07-07
982
40674368
2012-07-09
2012-07-07
983
40674368
2012-08-13
2012-08-10
984
40674368
2013-06-11
2013-06-09
985
40674368
2013-06-29
2013-06-28
986
40674368
2013-07-03
2013-07-02
987
40674368
2013-07-03
2013-07-03
988
40674368
2013-07-06
2013-07-03
989
40674368
2013-07-07
2013-07-07
990
40674368
2014-05-19
2014-05-18
991
40674368
2014-05-30
2014-05-29
992
40674368
2014-09-09
2014-09-09
993
40674368
2014-09-09
2014-09-09
994
40674368
2015-02-26
2015-02-25
995
40674368
2015-03-01
2015-02-27
996
40674368
2015-03-10
2015-03-10
997
40674368
2015-04-21
2015-04-20
998
40674368
2015-05-14
2015-05-09
999
40674368
2015-05-14
2015-05-14
1000
40674368
2015-05-21
2015-05-19
1001
40674368
2015-05-23
2015-05-23
1002
40674368
2015-08-01
2015-08-01
1003
40674368
2015-08-01
2015-08-01
1004
40674368
2015-08-08
2015-08-07
1005
40674368
2015-10-19
2015-10-16
1006
40674368
2015-10-26
2015-10-24
1007
40674368
2015-10-27
2015-10-26
1008
40674368
2015-10-27
2015-10-27
1009
40674368
2015-10-27
2015-10-27
1010
40905536
1998-12-04
1998-12-04
1011
40905536
1998-12-06
1998-12-04
1012
40905536
2004-02-12
2004-02-06
1013
40905536
2004-09-28
2004-09-27
1014
40905536
2004-10-04
2004-10-04
1015
40905536
2004-10-16
2004-10-15
1016
40905536
2004-11-15
2004-11-15
1017
40905536
2004-12-27
2004-12-27
1019
40905536
2004-12-30
2004-12-27
1020
40905536
2005-04-25
2005-04-25
1021
40905536
2005-05-13
2005-05-13
1022
40905536
2005-05-18
2005-05-17
1023
40905536
2005-05-23
2005-05-23
...
...
...
...
8969
37209088
2010-07-08
2010-06-08
8970
37209088
2010-07-09
2010-07-08
8971
37209088
2010-07-30
2010-07-16
8972
37209088
2010-07-30
2010-07-30
8973
37209088
2010-08-11
2010-08-11
8974
37209088
2010-08-12
2010-08-12
8975
37209088
2010-08-12
2010-08-12
8976
37209088
2010-08-27
2010-08-27
8977
37209088
2010-09-08
2010-09-08
8978
37209088
2010-09-10
2010-09-10
8979
37209088
2010-10-12
2010-10-12
8980
37209088
2010-10-14
2010-10-14
8981
37209088
2010-11-10
2010-11-09
8982
37209088
2010-12-09
2010-12-08
8983
37209088
2010-12-17
2010-12-17
8984
37209088
2011-01-11
2011-01-11
8985
37209088
2011-01-14
2011-01-14
8986
37209088
2011-01-31
2011-01-31
8987
37209088
2011-02-16
2011-01-31
8988
37209088
2011-02-27
2011-02-27
8989
37209088
2011-03-14
2011-02-28
8990
37209088
2011-03-16
2011-03-15
8991
37209088
2011-03-24
2011-03-23
8992
37209088
2011-03-24
2011-03-23
8993
37209088
2011-03-30
2011-03-28
8994
37209088
2011-04-01
2011-04-01
8995
37209088
2011-04-11
2011-04-08
8997
37209088
2011-04-21
2011-04-21
8998
37209088
2011-04-21
2011-04-21
8999
37209088
2011-05-04
2011-05-04
9000
37209088
2011-05-06
2011-05-06
9001
37209088
2011-05-18
2011-05-18
9002
37209088
2011-06-02
2011-06-02
9003
37209088
2011-06-06
2011-06-06
9004
37209088
2011-06-15
2011-06-15
9005
37209088
2011-06-29
2011-06-29
9006
37209088
2011-07-06
2011-07-06
9007
37209088
2011-07-21
2011-07-21
9008
37209088
2011-08-04
2011-08-04
9009
37209088
2011-09-07
2011-09-07
9010
37209088
2011-09-07
2011-09-07
9011
37209088
2011-09-27
2011-09-27
9012
37209088
2011-10-05
2011-10-05
9013
37209088
2011-10-18
2011-10-18
9014
37209088
2011-11-23
2011-11-12
9015
37209088
2011-12-05
2011-12-01
9016
37209088
2011-12-12
2011-12-12
9017
37209088
2012-01-06
2012-01-06
9018
37209088
2012-01-09
2012-01-09
9019
37209088
2012-02-06
2012-02-06
9020
37209088
2012-03-05
2012-03-05
9021
37209088
2012-03-19
2012-03-19
9022
37209088
2012-04-04
2012-04-04
9023
37209088
2012-04-18
2012-04-18
9024
37209088
2012-04-25
2012-04-25
9025
37209088
2014-11-05
2014-10-27
9026
37209088
2015-06-01
2015-05-15
9027
37209088
2015-10-31
2015-10-30
9028
37790816
2002-06-19
2002-06-19
9029
37790816
2003-03-07
2003-02-28
9030
37790816
2003-03-12
2003-03-09
9031
37790816
2007-08-19
2007-08-19
9032
37790816
2011-05-06
2011-05-05
9033
37790816
2011-12-27
2011-12-26
9034
37790816
2012-05-09
2012-05-09
9035
37790816
2012-05-28
2012-05-27
9036
37790816
2012-06-08
2012-06-07
9037
37790816
2012-09-18
2012-09-17
9038
37790816
2013-01-13
2013-01-12
9039
37790816
2013-02-06
2013-02-05
9040
37790816
2013-02-06
2013-02-06
9041
37790816
2013-02-06
2013-02-06
9042
37790816
2013-06-05
2013-06-04
9043
37790816
2014-01-22
2014-01-21
9044
37790816
2014-02-15
2014-02-14
9045
37790816
2014-02-18
2014-02-14
9046
37790816
2014-02-14
2014-02-14
9047
37790816
2014-02-16
2014-02-15
9048
37790816
2014-02-21
2014-02-16
9049
37790816
2014-02-22
2014-02-21
9050
37790816
2014-02-23
2014-02-22
9051
37790816
2014-02-28
2014-02-27
9052
37790816
2014-03-01
2014-02-28
9053
37790816
2014-03-02
2014-03-02
9054
37790816
2014-03-13
2014-03-03
9055
37790816
2014-04-18
2014-04-17
9056
37790816
2014-04-20
2014-04-19
9057
37790816
2014-05-02
2014-05-01
9058
37790816
2014-05-07
2014-05-06
9059
37790816
2014-05-07
2014-05-07
9060
37790816
2014-05-08
2014-05-08
9061
37790816
2014-05-09
2014-05-09
9062
37790816
2014-05-16
2014-05-15
9063
37790816
2014-05-17
2014-05-16
9064
37790816
2014-05-18
2014-05-18
9065
37790816
2014-05-18
2014-05-18
9066
37790816
2014-05-20
2014-05-18
9067
37790816
2014-06-01
2014-05-31
9068
37790816
2014-06-03
2014-06-03
9069
37790816
2014-06-04
2014-06-03
9070
37790816
2014-06-17
2014-06-16
9071
37790816
2014-06-21
2014-06-20
9072
37790816
2014-06-24
2014-06-24
9073
37790816
2014-06-27
2014-06-27
9074
37790816
2014-12-08
2014-12-07
9076
37790816
2014-12-12
2014-12-08
9077
37790816
2014-12-13
2014-12-13
9078
37790816
2015-02-16
2015-02-15
9079
37790816
2015-02-18
2015-02-15
9080
37790816
2015-02-20
2015-02-19
9081
37790816
2015-02-20
2015-02-20
9082
37790816
2015-02-21
2015-02-21
9083
37790816
2015-02-22
2015-02-21
9084
37790816
2015-02-24
2015-02-23
9085
37790816
2015-03-18
2015-03-18
9086
37790816
2015-03-19
2015-03-19
9087
37790816
2015-03-19
2015-03-19
9088
37790816
2015-03-23
2015-03-19
9089
37790816
2015-03-24
2015-03-23
9090
37790816
2015-03-24
2015-03-24
9091
37790816
2015-03-27
2015-03-27
9092
37790816
2015-04-03
2015-03-28
9093
37790816
2015-03-29
2015-03-28
9094
37790816
2015-03-31
2015-03-30
9095
37790816
2015-04-02
2015-04-01
9096
37790816
2015-04-04
2015-04-03
9097
37790816
2015-04-05
2015-04-04
9098
37790816
2015-04-06
2015-04-05
9099
37790816
2015-04-07
2015-04-06
9100
37790816
2015-04-08
2015-04-07
9101
37790816
2015-04-08
2015-04-08
9102
37790816
2015-04-09
2015-04-08
9103
37790816
2015-07-16
2015-07-16
9104
37790816
2015-07-17
2015-07-17
9105
37790816
2015-07-18
2015-07-18
9106
37790816
2015-07-20
2015-07-19
9107
37790816
2015-07-20
2015-07-20
9108
37790816
2015-10-24
2015-10-24
9109
37790816
2015-10-28
2015-10-27
9110
38058496
2001-07-12
2001-07-12
9111
38058496
2006-11-30
2006-11-28
9112
38058496
2006-11-29
2006-11-28
9113
38058496
2008-11-16
2008-11-15
9114
38058496
2009-07-23
2009-07-22
9115
38058496
2014-07-29
2014-07-28
9116
38058496
2014-08-01
2014-07-31
9117
38058496
2014-08-28
2014-08-27
9118
38058496
2014-08-30
2014-08-29
9119
38058496
2014-09-12
2014-09-11
9120
38058496
2014-09-14
2014-09-13
9121
38058496
2014-09-15
2014-09-14
9122
38058496
2014-09-19
2014-09-18
9123
38058496
2014-09-21
2014-09-20
9124
38058496
2014-10-06
2014-10-05
9125
38058496
2015-02-11
2015-02-10
9126
38058496
2015-02-22
2015-02-21
9127
38058496
2015-07-09
2015-07-08
9128
38058496
2015-08-05
2015-08-04
9129
38058496
2015-08-06
2015-08-05
9130
38058496
2015-08-07
2015-08-06
9131
38058496
2015-08-08
2015-08-07
9132
38058496
2015-08-11
2015-08-10
9133
38058496
2015-09-06
2015-09-06
9134
56819168
2005-08-18
2005-08-17
9135
56819168
2005-10-12
2005-10-12
9136
56819168
2005-10-31
2005-10-31
9137
56819168
2005-11-18
2005-11-18
9138
56819168
2006-02-03
2006-02-03
9139
56819168
2006-02-13
2006-02-10
9140
56819168
2006-05-09
2006-05-08
9141
56819168
2006-08-09
2006-08-07
9142
56819168
2006-12-07
2006-12-01
9143
56819168
2006-12-12
2006-12-12
9144
56819168
2007-02-07
2007-02-05
9145
56819168
2007-04-06
2007-04-06
9146
56819168
2007-04-09
2007-04-09
9147
56819168
2007-05-01
2007-04-30
9148
56819168
2007-05-10
2007-05-10
9149
56819168
2007-07-31
2007-07-26
9150
56819168
2007-07-30
2007-07-30
9151
56819168
2007-08-27
2007-08-27
9152
56819168
2007-09-25
2007-09-25
9153
56819168
2007-10-24
2007-10-24
9154
56819168
2007-12-15
2007-12-15
9155
56819168
2007-12-17
2007-12-17
9156
56819168
2007-12-18
2007-12-18
9157
56819168
2007-12-18
2007-12-18
9158
56819168
2008-01-22
2008-01-18
9159
56819168
2008-01-29
2008-01-25
9160
56819168
2008-01-28
2008-01-28
9161
56819168
2008-02-12
2008-02-01
9162
56819168
2008-02-04
2008-02-04
9163
56819168
2008-02-25
2008-02-22
9164
56819168
2008-03-03
2008-02-29
9165
56819168
2008-03-10
2008-03-10
9166
56819168
2008-03-19
2008-03-19
9167
56819168
2008-04-07
2008-04-07
9168
56819168
2008-05-05
2008-05-02
9169
56819168
2008-06-11
2008-06-11
9170
56819168
2008-06-18
2008-06-18
9171
56819168
2008-06-30
2008-06-30
9172
56819168
2008-09-22
2008-09-22
9173
56819168
2008-10-01
2008-09-26
9174
56819168
2008-10-06
2008-10-05
9175
56819168
2008-10-17
2008-10-14
9176
56819168
2008-11-20
2008-11-19
9177
56819168
2008-11-25
2008-11-25
9178
56819168
2008-12-05
2008-12-04
9179
56819168
2008-12-26
2008-12-17
9180
56819168
2008-12-31
2008-12-30
9181
56819168
2009-01-15
2009-01-13
9182
56819168
2009-01-14
2009-01-13
9183
56819168
2009-03-17
2009-03-16
9184
56819168
2009-03-20
2009-03-18
9185
56819168
2009-04-07
2009-04-07
9186
56819168
2009-04-22
2009-04-22
9187
56819168
2009-04-23
2009-04-23
9188
56819168
2009-05-21
2009-05-21
9189
56819168
2009-05-26
2009-05-21
9190
56819168
2009-06-02
2009-05-29
9191
56819168
2009-06-10
2009-06-10
9192
56819168
2009-06-23
2009-06-23
9193
56819168
2009-06-30
2009-06-26
9194
56819168
2009-07-02
2009-07-01
9195
56819168
2009-08-20
2009-08-19
9196
56819168
2009-08-24
2009-08-24
9197
56819168
2009-09-16
2009-09-15
9198
56819168
2009-09-16
2009-09-16
9199
56819168
2009-09-22
2009-09-21
9200
56819168
2009-09-25
2009-09-24
9201
56819168
2009-09-29
2009-09-29
9202
56819168
2009-09-30
2009-09-30
9203
56819168
2009-10-07
2009-10-02
9204
56819168
2009-10-09
2009-10-09
9205
56819168
2009-10-15
2009-10-13
9206
56819168
2009-10-15
2009-10-14
9207
56819168
2009-10-16
2009-10-15
9208
56819168
2009-10-19
2009-10-16
9209
56819168
2009-10-20
2009-10-19
9210
56819168
2009-10-20
2009-10-20
9211
56819168
2009-10-29
2009-10-26
9212
56819168
2009-10-27
2009-10-27
9213
56819168
2009-10-28
2009-10-28
9214
56819168
2009-11-04
2009-11-04
9215
56819168
2009-11-10
2009-11-10
9216
56819168
2009-11-19
2009-11-17
9217
56819168
2009-11-19
2009-11-13
9218
56819168
2009-12-02
2009-12-01
9219
56819168
2009-12-03
2009-11-18
9220
56819168
2009-12-03
2009-11-24
9221
56819168
2009-12-07
2009-12-07
9222
56819168
2009-12-08
2009-11-20
9223
56819168
2009-12-08
2009-12-08
9224
56819168
2009-12-11
2009-12-09
9225
56819168
2009-12-10
2009-12-01
9226
56819168
2009-12-11
2009-12-11
9227
56819168
2009-12-23
2009-12-23
9228
56819168
2009-12-30
2009-12-03
9229
56819168
2009-12-31
2009-12-10
9230
56819168
2009-12-31
2009-12-31
9231
56819168
2009-12-31
2009-12-15
9232
56819168
2010-01-06
2010-01-06
9233
56819168
2010-01-07
2010-01-07
9234
56819168
2010-01-13
2010-01-12
9235
56819168
2010-01-19
2010-01-19
9236
56819168
2010-01-26
2010-01-26
9237
56819168
2010-02-04
2010-02-01
9238
56819168
2010-02-11
2010-01-27
9239
56819168
2010-02-11
2010-02-11
9240
56819168
2010-02-16
2010-02-16
9241
56819168
2010-02-18
2010-02-18
9242
56819168
2010-02-25
2010-02-24
9243
56819168
2010-02-25
2010-02-25
9244
56819168
2010-03-04
2010-03-04
9245
56819168
2010-03-05
2010-03-05
9246
56819168
2010-03-08
2010-03-08
9247
56819168
2010-03-10
2010-03-10
9248
56819168
2010-03-15
2010-03-12
9249
56819168
2010-03-15
2010-03-15
9250
56819168
2010-03-22
2010-03-22
9251
56819168
2010-03-29
2010-03-29
9252
56819168
2010-03-30
2010-03-30
9253
56819168
2010-03-31
2010-03-31
9254
56819168
2010-04-05
2010-04-05
9255
56819168
2010-04-12
2010-04-12
9256
56819168
2010-04-27
2010-04-27
9257
56819168
2010-05-04
2010-05-04
9258
56819168
2010-05-11
2010-05-11
9259
56819168
2010-05-12
2010-05-12
9260
56819168
2010-05-18
2010-05-18
9261
56819168
2010-05-21
2010-05-20
9262
56819168
2010-05-24
2010-05-24
9263
56819168
2010-05-24
2010-05-24
9264
56819168
2010-05-25
2010-05-25
9265
56819168
2010-05-28
2010-05-27
9266
56819168
2010-05-28
2010-05-27
9267
56819168
2010-06-01
2010-05-27
9268
56819168
2010-06-03
2010-06-01
9269
56819168
2010-06-04
2010-06-03
9270
56819168
2010-06-08
2010-06-08
9271
56819168
2010-06-08
2010-06-07
9272
56819168
2010-06-10
2010-06-10
9273
56819168
2010-06-10
2010-06-09
9274
56819168
2010-06-16
2010-06-15
9275
56819168
2010-06-16
2010-06-16
9276
56819168
2010-06-16
2010-06-16
9277
56819168
2010-06-18
2010-06-17
9278
56819168
2010-06-25
2010-06-25
9279
56819168
2010-06-28
2010-06-28
9280
56819168
2010-06-29
2010-05-17
9281
56819168
2010-06-29
2010-06-29
9282
56819168
2010-07-06
2010-07-06
9283
56819168
2010-07-07
2010-07-07
9284
56819168
2010-07-09
2010-07-08
9285
56819168
2010-07-08
2010-07-08
9286
56819168
2010-07-09
2010-07-09
9287
56819168
2010-07-13
2010-07-13
9288
56819168
2010-07-13
2010-07-13
9289
56819168
2010-07-14
2010-07-12
9290
56819168
2010-07-15
2010-07-14
9291
56819168
2010-07-16
2010-07-15
9292
56819168
2010-07-16
2010-07-16
9293
56819168
2010-07-17
2010-07-17
9294
56819168
2010-07-20
2010-07-19
9295
56819168
2010-07-20
2010-07-20
9296
56819168
2010-07-22
2010-07-21
9297
56819168
2010-07-22
2010-07-21
9298
56819168
2010-07-23
2010-07-23
9299
56819168
2010-08-03
2010-08-02
9300
56819168
2010-08-03
2010-08-03
9301
56819168
2010-08-06
2010-08-05
9302
56819168
2010-08-10
2010-08-10
9304
56819168
2010-08-25
2010-08-25
9305
56819168
2010-09-02
2010-09-02
9306
56819168
2010-09-07
2010-09-03
9307
56819168
2010-09-13
2010-09-08
9308
56819168
2010-09-17
2010-09-14
9309
56819168
2010-09-14
2010-09-10
9310
56819168
2010-09-17
2010-09-16
9311
56819168
2010-09-16
2010-09-16
9312
56819168
2010-09-21
2010-09-15
9313
56819168
2010-09-21
2010-09-21
9314
56819168
2010-09-27
2010-09-24
9315
56819168
2010-09-27
2010-09-24
9316
56819168
2010-09-27
2010-09-24
9317
56819168
2010-09-27
2010-09-22
9318
56819168
2010-09-28
2010-09-28
9319
56819168
2010-09-28
2010-09-17
9320
56819168
2010-10-05
2010-10-01
9321
56819168
2010-10-04
2010-10-04
9322
56819168
2010-10-04
2010-09-24
9323
56819168
2010-10-05
2010-09-29
9324
56819168
2010-10-13
2010-10-13
9325
56819168
2010-10-13
2010-10-01
9326
56819168
2010-10-21
2010-10-21
9327
56819168
2010-10-22
2010-10-22
9328
56819168
2010-10-28
2010-10-28
9329
56819168
2010-10-28
2010-10-28
9330
56819168
2010-11-01
2010-11-01
9331
56819168
2010-11-08
2010-11-08
9332
56819168
2010-11-15
2010-11-15
9333
56819168
2010-11-22
2010-11-22
9334
56819168
2010-11-29
2010-11-29
9335
56819168
2010-12-06
2010-12-06
9336
56819168
2010-12-07
2010-12-03
9337
56819168
2010-12-14
2010-12-10
9338
56819168
2010-12-10
2010-12-09
9339
56819168
2010-12-15
2010-12-15
9340
56819168
2010-12-17
2010-12-16
9341
56819168
2010-12-21
2010-12-20
9342
56819168
2010-12-23
2010-12-23
9343
56819168
2010-12-23
2010-12-15
9344
56819168
2010-12-30
2010-12-23
9345
56819168
2011-01-03
2011-01-03
9346
56819168
2011-01-04
2010-12-21
9347
56819168
2011-01-04
2011-01-03
9348
56819168
2011-01-06
2011-01-06
9349
56819168
2011-01-07
2011-01-07
9350
56819168
2011-01-10
2011-01-10
9351
56819168
2011-01-12
2011-01-11
9352
56819168
2011-01-14
2011-01-14
9353
56819168
2011-01-20
2011-01-19
9354
56819168
2011-01-21
2011-01-21
9355
56819168
2011-01-26
2011-01-26
9356
56819168
2011-01-31
2011-01-31
9357
56819168
2011-02-03
2011-02-03
9358
56819168
2011-02-07
2011-02-07
9359
56819168
2011-02-11
2011-02-11
9360
56819168
2011-02-11
2011-02-11
9361
56819168
2011-02-25
2011-02-23
9362
56819168
2011-03-09
2011-03-09
9363
56819168
2011-03-14
2011-03-14
9364
56819168
2011-03-16
2011-03-16
9365
56819168
2011-03-31
2011-03-31
9366
56819168
2011-04-01
2011-04-01
9367
56819168
2011-04-07
2011-04-07
9368
56819168
2011-04-11
2011-04-11
9369
56819168
2011-04-18
2011-04-18
9370
56819168
2011-04-20
2011-04-20
9371
56819168
2011-04-28
2011-04-28
9372
56819168
2011-05-02
2011-05-02
9374
56819168
2011-05-11
2011-05-11
9375
56819168
2011-05-16
2011-05-16
9376
56819168
2011-05-17
2011-05-17
9377
56819168
2011-05-19
2011-05-18
9378
56819168
2011-05-20
2011-05-20
9379
56819168
2011-05-24
2011-05-24
9380
56819168
2011-05-31
2011-05-31
9381
56819168
2011-06-02
2011-06-02
9382
56819168
2011-06-06
2011-06-06
9383
56819168
2011-06-07
2011-06-07
9384
56819168
2011-06-09
2011-06-09
9385
56819168
2011-06-17
2011-06-13
9386
56819168
2011-06-27
2011-06-27
9387
56819168
2011-06-30
2011-06-30
9388
56819168
2011-07-08
2011-07-07
9389
56819168
2011-07-11
2011-07-11
9390
56819168
2011-07-13
2011-07-13
9391
56819168
2011-07-18
2011-07-18
9392
56819168
2011-07-21
2011-07-21
9393
56819168
2011-07-21
2011-07-20
9394
56819168
2011-07-25
2011-07-25
9395
56819168
2011-07-26
2011-07-25
9396
56819168
2011-07-26
2011-07-26
9397
56819168
2011-08-01
2011-08-01
9398
56819168
2011-08-02
2011-08-02
9399
56819168
2011-08-02
2011-08-02
9400
56819168
2011-08-08
2011-08-08
9401
56819168
2011-08-11
2011-08-09
9402
56819168
2011-08-16
2011-08-12
9403
56819168
2011-08-15
2011-08-15
9404
56819168
2011-08-17
2011-08-17
9405
56819168
2011-08-19
2011-08-18
9406
56819168
2011-08-25
2011-08-23
9407
56819168
2011-08-24
2011-08-24
9408
56819168
2011-08-30
2011-08-30
9409
56819168
2011-09-01
2011-08-31
9410
56819168
2011-09-01
2011-09-01
9411
56819168
2011-09-06
2011-09-06
9412
56819168
2011-09-12
2011-09-12
9413
56819168
2011-09-16
2011-09-16
9414
56819168
2011-09-19
2011-09-19
9415
56819168
2011-09-26
2011-09-22
9416
56819168
2011-09-28
2011-09-28
9417
56819168
2011-09-30
2011-09-30
9418
56819168
2011-10-05
2011-10-03
9420
56819168
2011-10-06
2011-10-04
9421
56819168
2011-10-05
2011-10-05
9422
56819168
2011-10-12
2011-10-12
9423
56819168
2011-10-14
2011-10-14
9424
56819168
2011-10-18
2011-10-18
9425
56819168
2011-10-19
2011-10-19
9426
56819168
2011-10-19
2011-10-19
9427
56819168
2011-10-21
2011-10-21
9428
56819168
2011-10-26
2011-10-26
9429
56819168
2011-10-28
2011-10-28
9430
56819168
2011-10-31
2011-10-31
9431
56819168
2011-11-01
2011-11-01
9432
56819168
2011-11-02
2011-11-02
9433
56819168
2011-11-09
2011-11-09
9434
56819168
2011-11-17
2011-11-16
9435
56819168
2011-11-16
2011-11-15
9436
56819168
2011-11-21
2011-11-21
9437
56819168
2011-11-29
2011-11-23
9438
56819168
2011-11-25
2011-11-25
9439
56819168
2011-11-30
2011-11-30
9440
56819168
2011-11-30
2011-11-30
9441
56819168
2011-12-02
2011-12-01
9442
56819168
2011-12-05
2011-12-05
9443
56819168
2011-12-06
2011-12-06
9444
56819168
2011-12-14
2011-12-14
9445
56819168
2011-12-21
2011-12-21
9446
56819168
2011-12-27
2011-12-22
9447
56819168
2011-12-28
2011-12-28
9448
56819168
2012-01-04
2012-01-04
9449
56819168
2012-01-19
2012-01-13
9450
56819168
2012-01-18
2012-01-18
9451
56819168
2012-01-19
2012-01-19
9452
56819168
2012-01-25
2012-01-25
9453
56819168
2012-02-06
2012-01-30
9454
56819168
2012-02-01
2012-02-01
9455
56819168
2012-02-08
2012-02-08
9456
56819168
2012-02-09
2012-02-02
9457
56819168
2012-02-13
2012-02-07
9458
56819168
2012-02-15
2012-02-14
9459
56819168
2012-02-16
2012-02-09
9460
56819168
2012-02-17
2012-02-17
9461
56819168
2012-02-22
2012-02-22
9462
56819168
2012-02-29
2012-02-29
9463
56819168
2012-03-01
2012-03-01
9464
56819168
2012-03-01
2012-01-31
9465
56819168
2012-03-02
2012-02-21
9466
56819168
2012-03-05
2012-03-05
9467
56819168
2012-03-06
2012-02-23
9468
56819168
2012-03-07
2012-03-07
9469
56819168
2012-03-08
2012-02-28
9470
56819168
2012-03-08
2012-03-01
9471
56819168
2012-03-21
2012-03-20
9472
56819168
2012-03-21
2012-03-21
9473
56819168
2012-03-28
2012-03-28
9474
56819168
2012-04-03
2012-03-29
9475
56819168
2012-04-03
2012-04-02
9476
56819168
2012-04-04
2012-04-04
9477
56819168
2012-04-06
2012-04-06
9478
56819168
2012-04-11
2012-04-11
9479
56819168
2012-04-13
2012-04-13
9480
56819168
2012-04-19
2012-04-19
9481
56819168
2012-04-24
2012-04-24
9482
56819168
2012-05-01
2012-05-01
9483
56819168
2012-05-02
2012-05-02
9484
56819168
2012-05-09
2012-05-07
9485
56819168
2012-05-08
2012-05-08
9486
56819168
2012-05-11
2012-05-07
9487
56819168
2012-05-15
2012-05-15
9488
56819168
2012-05-15
2012-05-15
9489
56819168
2012-05-24
2012-05-24
9490
56819168
2012-05-25
2012-05-25
9491
56819168
2012-05-25
2012-05-18
9492
56819168
2012-05-29
2012-05-29
9493
56819168
2012-05-30
2012-05-16
9494
56819168
2012-05-30
2012-05-22
9495
56819168
2012-06-04
2012-05-29
9496
56819168
2012-06-04
2012-06-04
9497
56819168
2012-06-05
2012-06-05
9498
56819168
2012-06-08
2012-06-06
9499
56819168
2012-06-07
2012-06-07
9500
56819168
2012-06-08
2012-06-05
9501
56819168
2012-06-11
2012-06-11
9502
56819168
2012-06-12
2012-06-12
9503
56819168
2012-06-15
2012-06-13
9504
56819168
2012-06-19
2012-06-19
9505
56819168
2012-06-19
2012-06-12
9506
56819168
2012-06-26
2012-06-21
9507
56819168
2012-06-22
2012-06-21
9508
56819168
2012-06-21
2012-06-21
9509
56819168
2012-06-27
2012-06-21
9510
56819168
2012-06-25
2012-06-25
9511
56819168
2012-06-25
2012-06-19
9512
56819168
2012-06-26
2012-06-26
9513
56819168
2012-06-29
2012-06-27
9514
56819168
2012-07-05
2012-07-02
9515
56819168
2012-07-10
2012-07-06
9516
56819168
2012-07-10
2012-07-10
9517
56819168
2012-07-10
2012-07-09
9518
56819168
2012-07-12
2012-07-11
9519
56819168
2012-07-20
2012-07-13
9520
56819168
2012-07-20
2012-07-16
9521
56819168
2012-07-17
2012-07-17
9522
56819168
2012-07-18
2012-07-16
9523
56819168
2012-07-26
2012-07-20
9524
56819168
2012-07-20
2012-07-18
9525
56819168
2012-07-24
2012-07-24
9526
56819168
2012-07-30
2012-07-30
9527
56819168
2012-07-30
2012-07-30
9528
56819168
2012-07-31
2012-07-31
9529
56819168
2012-07-31
2012-07-31
9530
56819168
2012-08-07
2012-08-07
9531
56819168
2012-08-10
2012-08-08
9532
56819168
2012-08-13
2012-08-13
9533
56819168
2012-08-14
2012-08-14
9534
56819168
2012-08-15
2012-08-14
9535
56819168
2012-08-17
2012-08-17
9536
56819168
2012-08-21
2012-08-21
9537
56819168
2012-08-21
2012-07-27
9538
56819168
2012-08-28
2012-08-28
9539
56819168
2012-09-10
2012-09-10
9540
56819168
2012-09-17
2012-09-17
9541
56819168
2012-09-21
2012-09-21
9542
56819168
2012-09-27
2012-09-24
9543
56819168
2012-10-01
2012-10-01
9544
56819168
2012-10-04
2012-10-03
9545
56819168
2012-10-11
2012-10-11
9546
56819168
2012-10-12
2012-10-12
9547
56819168
2012-10-17
2012-10-17
9548
56819168
2012-10-23
2012-10-23
9549
56819168
2012-10-31
2012-10-26
9551
56819168
2012-11-14
2012-11-14
9552
56819168
2012-11-23
2012-11-15
9554
56819168
2012-11-20
2012-11-19
9555
56819168
2012-11-21
2012-11-21
9556
56819168
2012-11-30
2012-11-29
9557
56819168
2012-12-04
2012-12-04
9558
56819168
2012-12-04
2012-12-03
9559
56819168
2012-12-06
2012-12-06
9560
56819168
2012-12-10
2012-12-06
9561
56819168
2012-12-11
2012-12-10
9562
56819168
2012-12-11
2012-12-11
9563
56819168
2012-12-11
2012-12-11
9564
56819168
2012-12-13
2012-12-13
9565
56819168
2012-12-14
2012-12-13
9566
56819168
2012-12-17
2012-12-17
9567
56819168
2012-12-20
2012-12-19
9568
56819168
2012-12-27
2012-12-24
9569
56819168
2012-12-27
2012-12-26
9570
56819168
2012-12-31
2012-12-31
9571
56819168
2013-01-04
2013-01-04
9572
56819168
2013-01-09
2013-01-09
9573
56819168
2013-01-18
2013-01-18
9574
56819168
2013-01-23
2013-01-22
9575
56819168
2013-02-05
2013-02-01
9576
56819168
2013-02-06
2013-02-06
9577
56819168
2013-02-11
2013-02-07
9578
56819168
2013-02-12
2013-02-11
9579
56819168
2013-02-20
2013-02-20
9580
56819168
2013-02-26
2013-02-21
9581
56819168
2013-02-27
2013-02-25
9582
56819168
2013-02-28
2013-02-13
9583
56819168
2013-02-28
2013-02-27
9584
56819168
2013-03-01
2013-02-20
9585
56819168
2013-03-05
2013-03-04
9586
56819168
2013-03-28
2013-03-25
9587
56819168
2013-03-26
2013-03-25
9588
56819168
2013-04-03
2013-04-03
9589
56819168
2013-04-04
2013-04-04
9590
56819168
2013-04-26
2013-04-22
9591
56819168
2013-04-23
2013-04-22
9592
56819168
2013-05-01
2013-05-01
9593
56819168
2013-05-06
2013-02-15
9594
56819168
2013-05-13
2013-05-13
9595
56819168
2013-05-15
2013-05-15
9596
56819168
2013-05-20
2013-05-20
9597
56819168
2013-05-21
2013-05-21
9598
56819168
2013-05-28
2013-05-23
9599
56819168
2013-05-29
2013-05-29
9600
56819168
2013-06-06
2013-06-04
9601
56819168
2013-06-21
2013-06-20
9602
56819168
2013-06-25
2013-06-24
9603
56819168
2013-06-27
2013-06-27
9604
56819168
2013-07-19
2013-07-15
9605
56819168
2013-07-19
2013-07-18
9606
56819168
2013-07-22
2013-07-22
9607
56819168
2013-07-23
2013-07-23
9608
56819168
2013-07-25
2013-07-25
9609
56819168
2013-07-30
2013-07-30
9610
56819168
2013-08-01
2013-07-31
9611
56819168
2013-08-02
2013-08-01
9612
56819168
2013-08-12
2013-08-12
9613
56819168
2013-08-15
2013-08-15
9614
56819168
2013-09-25
2013-09-25
9615
56819168
2013-10-16
2013-10-09
9616
56819168
2013-10-23
2013-10-21
9617
56819168
2013-10-22
2013-10-22
9618
56819168
2013-10-23
2013-10-23
9619
56819168
2013-11-01
2013-11-01
9620
56819168
2013-11-04
2013-11-01
9621
56819168
2014-01-03
2014-01-02
9622
56819168
2014-01-06
2013-12-31
9623
56819168
2014-01-08
2014-01-07
9624
56819168
2014-01-15
2014-01-14
9625
56819168
2014-01-21
2014-01-15
9626
56819168
2014-01-22
2014-01-21
9627
56819168
2014-01-22
2014-01-16
9628
56819168
2014-01-24
2014-01-23
9629
56819168
2014-01-27
2014-01-27
9630
56819168
2014-01-29
2014-01-28
9631
56819168
2014-01-29
2014-01-28
9632
56819168
2014-02-08
2014-02-06
9633
56819168
2014-02-24
2014-02-21
9634
56819168
2014-03-12
2014-03-12
9635
56819168
2014-04-03
2014-04-03
9636
56819168
2014-04-24
2014-04-23
9637
56819168
2014-04-30
2014-04-29
9638
56819168
2014-05-21
2014-05-21
9639
56819168
2014-05-28
2014-05-28
9640
56819168
2014-06-12
2014-06-10
9641
56819168
2014-06-11
2014-06-11
9642
56819168
2014-06-23
2014-06-20
9643
56819168
2014-06-30
2014-06-30
9644
56819168
2014-07-07
2014-07-07
9645
56819168
2014-07-21
2014-07-21
9646
56819168
2014-07-30
2014-07-29
9647
56819168
2014-08-13
2014-08-13
9648
56819168
2014-08-15
2014-08-15
9649
56819168
2014-08-25
2014-08-25
9650
56819168
2014-09-09
2014-09-09
9651
56819168
2014-09-24
2014-09-24
9652
56819168
2014-09-29
2014-09-29
9653
56819168
2014-10-03
2014-10-03
9654
56819168
2014-10-14
2014-10-14
9655
56819168
2014-11-06
2014-11-05
9656
56819168
2014-11-10
2014-11-07
9657
56819168
2014-11-14
2014-11-14
9658
56819168
2014-11-14
2014-11-13
9659
56819168
2014-11-18
2014-11-18
9660
56819168
2014-11-20
2014-11-19
9661
56819168
2014-11-25
2014-11-25
9662
56819168
2014-12-01
2014-12-01
9663
56819168
2014-12-23
2014-12-23
9664
56819168
2015-01-02
2015-01-02
9665
56819168
2015-01-07
2015-01-07
9666
56819168
2015-01-20
2015-01-16
9667
56819168
2015-01-20
2015-01-20
9668
56819168
2015-02-03
2015-02-03
9669
56819168
2015-02-23
2015-02-22
9670
56819168
2015-02-27
2015-02-26
9671
56819168
2015-02-26
2015-02-26
9672
56819168
2015-03-18
2015-03-18
9673
56819168
2015-08-03
2015-07-31
9674
56819168
2015-08-25
2015-08-24
9675
56819168
2015-10-15
2015-10-14
9676
56819168
2015-10-21
2015-10-21
9677
56819168
2015-10-23
2015-10-22
9678
56819168
2015-10-29
2015-10-29
9679
57565760
2007-04-24
2007-04-11
9680
57565760
2007-05-17
2007-05-15
9681
57565760
2010-01-05
2010-01-05
9682
57565760
2010-04-07
2010-04-06
9683
57565760
2010-07-04
2010-07-04
9684
57565760
2010-07-04
2010-07-04
9685
57565760
2010-07-06
2010-07-05
9686
57565760
2010-07-10
2010-07-09
9687
57565760
2010-07-18
2010-07-18
9688
57565760
2010-07-21
2010-07-18
9689
57565760
2010-08-04
2010-08-04
9690
57565760
2010-08-16
2010-08-14
9691
57565760
2010-08-24
2010-08-23
9692
57565760
2010-08-26
2010-08-25
9693
57565760
2010-09-02
2010-09-02
9694
57565760
2010-09-03
2010-09-02
9695
57565760
2010-09-20
2010-09-19
9696
57565760
2010-09-21
2010-09-20
9697
57565760
2010-09-22
2010-09-21
9698
57565760
2010-09-27
2010-09-22
9699
57565760
2010-09-24
2010-09-23
9700
57565760
2010-09-28
2010-09-24
9701
57565760
2010-09-25
2010-09-24
9702
57565760
2010-09-27
2010-09-25
9703
57565760
2010-10-04
2010-10-03
9705
57565760
2010-10-08
2010-10-05
9706
57565760
2010-10-09
2010-10-08
9707
57565760
2010-10-24
2010-10-23
9708
57565760
2010-11-04
2010-11-03
9709
57565760
2010-11-06
2010-11-05
9710
57565760
2010-11-08
2010-11-07
9711
57565760
2010-11-12
2010-11-10
9712
57565760
2010-11-14
2010-11-13
9713
57565760
2010-11-19
2010-11-16
9714
57565760
2010-11-22
2010-11-22
9715
57565760
2010-11-24
2010-11-24
9716
57565760
2010-12-09
2010-12-05
9717
57565760
2010-12-14
2010-12-11
9718
57565760
2010-12-23
2010-12-22
9719
57565760
2011-01-14
2011-01-10
9720
57565760
2011-01-14
2011-01-11
9721
57565760
2011-01-14
2011-01-14
9722
57565760
2011-02-05
2011-02-05
9724
57565760
2011-02-08
2011-02-07
9725
57565760
2011-02-10
2011-02-09
9726
57565760
2011-02-10
2011-02-10
9727
57565760
2011-02-13
2011-02-11
9728
57565760
2011-02-11
2011-02-11
9729
57565760
2011-02-13
2011-02-13
9731
57565760
2011-02-16
2011-02-16
9732
57565760
2011-02-20
2011-02-16
9733
57565760
2011-03-03
2011-03-02
9734
57565760
2011-03-04
2011-03-03
9735
57565760
2011-03-05
2011-03-04
9736
57565760
2011-03-05
2011-03-05
9737
57565760
2011-03-06
2011-03-05
9738
57565760
2011-03-08
2011-03-05
9739
57565760
2011-03-09
2011-03-08
9740
57565760
2011-03-11
2011-03-09
9741
57565760
2011-03-11
2011-03-10
9742
57565760
2011-03-12
2011-03-11
9743
57565760
2011-03-13
2011-03-12
9744
57565760
2011-04-06
2011-04-06
9745
57565760
2011-05-20
2011-05-20
9746
57565760
2011-05-25
2011-05-24
9747
57565760
2011-07-11
2011-07-10
9748
57565760
2011-07-12
2011-07-11
9749
57565760
2011-07-20
2011-07-19
9750
57565760
2011-07-21
2011-07-20
9751
57565760
2011-08-05
2011-08-05
9752
57565760
2011-08-07
2011-08-06
9753
57565760
2011-08-14
2011-08-14
9754
57565760
2011-09-03
2011-09-02
9755
57565760
2011-09-04
2011-09-03
9756
57565760
2011-09-05
2011-09-05
9757
57565760
2011-10-03
2011-10-02
9758
57565760
2011-11-28
2011-11-27
9759
57565760
2011-12-02
2011-12-01
9760
57565760
2011-12-06
2011-12-05
9761
57565760
2012-03-11
2012-03-10
9762
57565760
2012-03-12
2012-03-12
9763
57565760
2012-03-15
2012-03-15
9764
57565760
2012-03-16
2012-03-16
9765
57565760
2012-03-18
2012-03-17
9766
57565760
2012-03-18
2012-03-18
9767
57565760
2012-03-24
2012-03-23
9769
57565760
2012-03-28
2012-03-27
9771
57565760
2012-03-28
2012-03-28
9773
57565760
2012-04-12
2012-04-11
9777
57565760
2012-04-17
2012-04-16
9778
57565760
2012-04-18
2012-04-18
9779
57565760
2012-04-20
2012-04-19
9780
57565760
2012-04-30
2012-04-29
9781
57565760
2012-05-08
2012-05-08
9782
57565760
2012-05-10
2012-05-10
9783
57565760
2012-05-11
2012-05-10
9784
57565760
2012-05-12
2012-05-12
9785
57565760
2012-05-15
2012-05-15
9786
57565760
2012-05-16
2012-05-16
9787
57565760
2012-05-17
2012-05-17
9789
57565760
2012-05-18
2012-05-18
9790
57565760
2012-05-20
2012-05-20
9792
57565760
2012-05-22
2012-05-21
9793
57565760
2012-06-19
2012-06-19
9794
57565760
2012-06-20
2012-06-20
9797
57565760
2012-06-23
2012-06-22
9798
57565760
2012-06-24
2012-06-23
9799
57565760
2012-06-26
2012-06-26
9800
57565760
2012-06-27
2012-06-26
9801
57565760
2012-06-28
2012-06-27
9802
57565760
2012-07-04
2012-07-03
9803
57565760
2012-07-07
2012-07-06
9804
57565760
2012-07-14
2012-07-14
9805
57565760
2012-07-22
2012-07-21
9806
57565760
2012-07-24
2012-07-24
9807
57565760
2012-07-25
2012-07-24
9808
57565760
2012-07-28
2012-07-27
9809
57565760
2012-07-31
2012-07-30
9810
57565760
2012-08-01
2012-07-31
9811
57565760
2012-08-03
2012-08-01
9812
57565760
2012-08-06
2012-08-05
9813
57565760
2012-08-09
2012-08-08
9814
57565760
2012-08-09
2012-08-09
9815
57565760
2012-08-12
2012-08-11
9816
57565760
2012-08-17
2012-08-17
9817
57565760
2012-08-18
2012-08-17
9818
57565760
2012-08-19
2012-08-18
9819
57565760
2012-08-24
2012-08-23
9820
57565760
2012-09-01
2012-08-31
9822
57565760
2012-09-06
2012-09-05
9823
57565760
2012-09-07
2012-09-06
9824
57565760
2012-09-09
2012-09-08
9825
57565760
2012-10-13
2012-10-12
9826
57565760
2012-10-13
2012-10-13
9827
57565760
2012-10-15
2012-10-14
9829
57565760
2012-10-22
2012-10-21
9830
57565760
2012-10-24
2012-10-23
9831
57565760
2012-10-25
2012-10-24
9832
57565760
2012-10-26
2012-10-26
9833
57565760
2012-11-06
2012-11-06
9834
57565760
2012-11-07
2012-11-07
9836
57565760
2012-11-09
2012-11-08
9837
57565760
2012-11-12
2012-11-12
9839
57565760
2012-11-14
2012-11-13
9840
57565760
2012-11-20
2012-11-19
9841
57565760
2013-01-04
2013-01-03
9842
57565760
2013-01-05
2013-01-04
9843
57565760
2013-01-05
2013-01-05
9844
57565760
2013-01-14
2013-01-14
9845
57565760
2013-03-07
2013-03-06
9846
57565760
2013-03-07
2013-03-07
9847
57565760
2013-03-10
2013-03-09
9848
57565760
2013-04-06
2013-04-06
9850
57565760
2013-04-11
2013-04-10
9851
57565760
2013-04-12
2013-04-11
9852
57565760
2013-04-13
2013-04-12
9853
57565760
2013-04-14
2013-04-14
9854
57565760
2013-04-16
2013-04-16
9855
57565760
2013-04-17
2013-04-16
9856
57565760
2013-04-21
2013-04-18
9857
57565760
2013-04-20
2013-04-19
9858
57565760
2013-04-21
2013-04-20
9859
57565760
2013-04-22
2013-04-21
9860
57565760
2013-04-24
2013-04-23
9862
57565760
2013-04-26
2013-04-25
9863
57565760
2013-05-01
2013-04-30
9864
57565760
2013-05-02
2013-05-01
9865
57565760
2013-05-03
2013-05-02
9866
57565760
2013-05-04
2013-05-04
9867
57565760
2013-05-05
2013-05-04
9869
57565760
2013-08-05
2013-08-04
9870
57565760
2013-10-28
2013-10-26
9871
57565760
2013-11-08
2013-11-08
9872
57565760
2013-11-29
2013-11-28
9873
57565760
2013-12-04
2013-12-03
9874
57565760
2013-12-14
2013-12-14
9875
57565760
2014-01-03
2014-01-02
9876
57565760
2014-02-07
2014-02-06
9877
57565760
2014-02-11
2014-02-10
9878
57565760
2014-02-21
2014-02-21
9879
57565760
2014-02-21
2014-02-21
9880
57565760
2014-02-28
2014-02-27
9881
57565760
2014-03-17
2014-03-17
9882
57565760
2014-03-17
2014-03-17
9883
57565760
2014-03-28
2014-03-28
9884
57565760
2014-04-11
2014-04-11
9885
57565760
2014-04-13
2014-04-12
9886
57565760
2014-04-14
2014-04-13
9887
57565760
2014-04-15
2014-04-14
9888
57565760
2014-05-24
2014-05-24
9889
57565760
2014-06-08
2014-06-08
9890
57565760
2014-06-18
2014-06-14
9892
57565760
2014-12-27
2014-12-27
9893
57565760
2015-01-04
2015-01-04
9894
57565760
2015-04-27
2015-04-27
9895
57565760
2015-06-11
2015-06-08
9896
57565760
2015-06-19
2015-06-18
9897
57565760
2015-06-23
2015-06-20
9898
57565760
2015-08-16
2015-08-15
9899
57565760
2015-08-28
2015-08-27
9900
57565760
2015-09-12
2015-09-12
9901
57565760
2015-09-26
2015-09-25
9902
57565760
2015-10-02
2015-10-01
9903
57565760
2015-10-07
2015-10-06
9904
57565760
2015-10-10
2015-10-09
9905
57565760
2015-10-13
2015-10-13
9906
57565760
2015-10-17
2015-10-17
9907
57565760
2015-10-30
2015-10-29
9908
57565760
2015-11-01
2015-10-31
9909
57631168
2004-01-20
2004-01-20
9910
57631168
2004-01-22
2004-01-13
9911
57631168
2004-02-05
2004-02-02
9912
57631168
2004-03-16
2004-03-15
9913
57631168
2004-03-19
2004-03-19
9914
57631168
2004-04-08
2004-04-06
9915
57631168
2004-04-06
2004-04-06
9916
57631168
2004-04-08
2004-04-06
9917
57631168
2004-04-15
2004-04-15
9918
57631168
2004-04-29
2004-04-22
9919
57631168
2004-05-14
2004-05-11
9920
57631168
2004-06-25
2004-05-21
9921
57631168
2004-08-13
2004-07-29
9922
57631168
2004-09-10
2004-08-25
9923
57631168
2004-09-15
2004-09-15
9924
57631168
2005-04-21
2005-04-21
9925
57631168
2005-04-27
2005-04-27
9926
57631168
2005-05-04
2005-05-04
9927
57631168
2005-12-29
2005-12-29
9928
57631168
2006-03-16
2006-03-15
9929
57631168
2006-03-24
2006-03-22
9930
57631168
2006-05-14
2006-05-13
9931
57631168
2006-06-19
2006-06-18
9932
57631168
2006-06-22
2006-06-21
9933
57631168
2006-07-22
2006-07-22
9934
57631168
2006-07-29
2006-07-28
9935
57631168
2006-08-01
2006-07-31
9936
57631168
2006-12-28
2006-12-15
9937
57631168
2007-01-09
2007-01-05
9938
57631168
2007-03-01
2007-02-27
9939
57631168
2007-03-01
2007-02-26
9940
57631168
2009-10-29
2009-10-23
9941
57631168
2009-10-29
2009-10-28
9942
57631168
2009-12-09
2009-11-24
9943
57631168
2009-12-09
2009-12-09
9944
57631168
2010-01-20
2010-01-20
9945
57631168
2011-03-04
2011-03-04
9946
57631168
2011-10-06
2011-09-20
9947
57631168
2013-08-24
2013-08-24
9948
57631168
2013-08-26
2013-08-24
9950
57631168
2013-09-20
2013-09-19
9951
57631168
2013-12-13
2013-12-11
9952
57631168
2013-12-20
2013-12-12
9953
57631168
2014-01-14
2014-01-09
9954
57631168
2014-01-21
2014-01-11
9955
57631168
2014-09-09
2014-09-03
9956
57631168
2014-12-01
2014-11-21
9957
57631168
2015-07-09
2015-07-06
9958
57631168
2015-07-09
2015-07-06
9959
57631168
2015-07-10
2015-07-07
9960
57631168
2015-07-28
2015-07-28
9961
57631168
2015-07-28
2015-07-28
9963
57631168
2015-08-03
2015-08-03
9964
57631168
2015-08-26
2015-08-03
9965
57631168
2015-09-07
2015-09-07
9966
57708896
2007-08-09
2007-08-09
9967
57708896
2012-02-19
2012-02-19
9968
57708896
2012-11-03
2012-11-02
9969
57708896
2012-11-03
2012-11-03
9970
57708896
2012-11-06
2012-11-06
9971
57708896
2012-11-14
2012-11-14
9972
57708896
2012-11-16
2012-11-16
9973
57708896
2013-10-04
2013-10-03
9974
57708896
2014-10-07
2014-10-07
9975
57708896
2015-04-04
2015-04-04
9976
57708896
2015-04-04
2015-04-04
9977
57708896
2015-09-16
2015-09-15
9978
57708896
2015-10-16
2015-10-16
9979
60754176
2004-07-21
2004-07-20
9980
60754176
2014-06-20
2014-06-20
9981
60754176
2014-08-14
2014-08-12
9982
60754176
2014-09-16
2014-09-16
9983
60754176
2014-10-14
2014-10-10
9984
60754176
2015-01-12
2015-01-11
9985
60754176
2015-02-08
2015-02-03
9986
60754176
2015-03-25
2015-03-24
9987
60754176
2015-07-07
2015-06-27
9988
60754176
2015-08-12
2015-08-08
9989
60754176
2015-08-21
2015-08-19
9990
60754176
2015-09-20
2015-09-19
9992
62105792
2004-04-15
2004-04-11
9993
62105792
2014-10-04
2014-10-03
9994
62105792
2015-09-29
2015-09-28
9995
62697600
2008-10-26
2008-10-25
9996
62697600
2008-11-07
2008-10-28
9997
62697600
2008-10-30
2008-10-25
9998
62697600
2012-01-14
2012-01-14
9999
62697600
2012-01-18
2012-01-14
9792 rows × 3 columns
In [58]:


df2.isna().sum()


Out[58]:
ID                     0
DISCHARGE_DATE_TIME    0
ADMISSION_DATE_TIME    0
dtype: int64
In [61]:


df2['No. Of Days in Hospital'] = df2['DISCHARGE_DATE_TIME'] - df2['ADMISSION_DATE_TIME']
df2


Out[61]:

ID
DISCHARGE_DATE_TIME
ADMISSION_DATE_TIME
No. Of Days in Hospital
0
22300992
2014-11-25
2014-11-25
0 days
1
22300992
2014-12-18
2014-12-16
2 days
2
22300992
2014-12-22
2014-12-22
0 days
3
22300992
2014-12-24
2014-12-23
1 days
4
22300992
2014-12-28
2014-12-28
0 days
5
22300992
2015-02-09
2015-02-04
5 days
6
22300992
2015-02-20
2015-02-18
2 days
7
22300992
2015-02-25
2015-02-25
0 days
8
22300992
2015-03-31
2015-03-25
6 days
9
22300992
2015-03-25
2015-03-25
0 days
11
22300992
2015-05-04
2015-05-04
0 days
12
22300992
2015-05-06
2015-05-06
0 days
13
22300992
2015-05-15
2015-05-14
1 days
14
22300992
2015-05-28
2015-05-27
1 days
15
22300992
2015-06-17
2015-06-15
2 days
16
22300992
2015-06-25
2015-06-22
3 days
17
22300992
2015-07-06
2015-07-06
0 days
18
22300992
2015-07-17
2015-07-15
2 days
19
22300992
2015-07-22
2015-07-22
0 days
20
22300992
2015-07-31
2015-07-24
7 days
21
22300992
2015-08-05
2015-08-04
1 days
22
22300992
2015-08-13
2015-08-12
1 days
23
22300992
2015-08-24
2015-08-24
0 days
24
22300992
2015-09-01
2015-08-31
1 days
25
22300992
2015-09-15
2015-09-15
0 days
26
22300992
2015-09-16
2015-09-15
1 days
27
22300992
2015-09-23
2015-09-22
1 days
28
22300992
2015-09-25
2015-09-24
1 days
29
22300992
2015-09-25
2015-09-25
0 days
30
22300992
2015-09-30
2015-09-28
2 days
31
22300992
2015-10-13
2015-10-13
0 days
32
22300992
2015-10-27
2015-10-26
1 days
33
23665824
2003-03-13
2003-03-10
3 days
34
23665824
2003-03-20
2003-03-20
0 days
35
23665824
2003-05-30
2003-05-30
0 days
36
23665824
2003-07-22
2003-07-21
1 days
37
23665824
2003-08-05
2003-08-05
0 days
39
23665824
2003-11-13
2003-11-13
0 days
40
23665824
2003-11-22
2003-11-22
0 days
41
23665824
2003-12-11
2003-12-09
2 days
42
23665824
2004-01-15
2004-01-15
0 days
43
23665824
2004-03-19
2004-03-18
1 days
44
23665824
2004-06-04
2004-06-04
0 days
45
23665824
2004-07-16
2004-07-15
1 days
46
23665824
2004-10-12
2004-10-12
0 days
47
23665824
2005-03-04
2005-03-04
0 days
48
23665824
2005-04-11
2005-04-11
0 days
50
23665824
2005-08-09
2005-08-09
0 days
51
23665824
2005-10-24
2005-10-24
0 days
52
23665824
2006-02-13
2006-02-13
0 days
53
23665824
2006-05-04
2006-05-04
0 days
54
23665824
2006-09-05
2006-09-05
0 days
55
23665824
2006-09-22
2006-09-22
0 days
56
23665824
2006-11-17
2006-11-17
0 days
57
23665824
2006-11-24
2006-11-24
0 days
58
23665824
2007-06-15
2007-06-15
0 days
59
23665824
2007-10-19
2007-10-19
0 days
60
23665824
2007-12-03
2007-11-28
5 days
61
23665824
2008-06-17
2008-06-17
0 days
62
23665824
2012-03-14
2012-03-14
0 days
63
23665824
2012-07-09
2012-07-09
0 days
64
23665824
2012-12-01
2012-12-01
0 days
65
23665824
2014-03-05
2014-03-05
0 days
66
23665824
2014-05-28
2014-05-22
6 days
67
23665824
2014-08-11
2014-08-11
0 days
68
23665824
2014-08-25
2014-08-25
0 days
69
23665824
2014-10-03
2014-09-28
5 days
70
23665824
2014-11-13
2014-11-02
11 days
71
23665824
2014-11-26
2014-11-26
0 days
72
23665824
2014-12-04
2014-11-29
5 days
73
23665824
2014-12-11
2014-12-08
3 days
74
23665824
2014-12-18
2014-12-17
1 days
75
23665824
2014-12-18
2014-12-18
0 days
76
23665824
2015-01-21
2015-01-16
5 days
77
23665824
2015-02-19
2015-02-18
1 days
78
23665824
2015-03-05
2015-03-04
1 days
79
23665824
2015-03-17
2015-03-11
6 days
80
23665824
2015-03-30
2015-03-29
1 days
81
23665824
2015-06-03
2015-06-02
1 days
82
23665824
2015-06-11
2015-06-09
2 days
83
23665824
2015-06-18
2015-06-18
0 days
84
23665824
2015-07-16
2015-07-16
0 days
85
23665824
2015-07-25
2015-07-25
0 days
86
23665824
2015-08-21
2015-07-28
24 days
87
23665824
2015-08-26
2015-08-26
0 days
88
23665824
2015-08-30
2015-08-30
0 days
89
23665824
2015-09-15
2015-09-14
1 days
90
23665824
2015-09-16
2015-09-16
0 days
92
23665824
2015-09-25
2015-09-22
3 days
93
24810016
2002-04-10
2002-04-09
1 days
94
24810016
2003-05-09
2003-05-09
0 days
95
24810016
2003-07-14
2003-07-14
0 days
96
24810016
2003-07-23
2003-07-23
0 days
97
24810016
2003-08-11
2003-08-11
0 days
98
24810016
2003-08-21
2003-08-21
0 days
99
24810016
2003-09-11
2003-09-11
0 days
100
24810016
2003-10-02
2003-10-02
0 days
101
24810016
2003-10-03
2003-10-03
0 days
102
24810016
2004-01-08
2004-01-08
0 days
103
24810016
2004-01-23
2004-01-23
0 days
104
24810016
2004-02-09
2004-02-09
0 days
105
24810016
2004-03-10
2004-03-10
0 days
106
24810016
2004-03-17
2004-03-17
0 days
107
24810016
2004-04-15
2004-04-15
0 days
108
24810016
2004-04-22
2004-04-22
0 days
109
24810016
2004-05-26
2004-05-26
0 days
110
24810016
2004-06-04
2004-06-04
0 days
111
24810016
2004-06-30
2004-06-30
0 days
112
24810016
2004-08-30
2004-08-30
0 days
113
24810016
2004-10-06
2004-10-06
0 days
114
24810016
2004-10-21
2004-10-21
0 days
115
24810016
2004-12-09
2004-12-09
0 days
116
24810016
2005-01-13
2005-01-13
0 days
117
24810016
2005-02-09
2005-02-09
0 days
118
24810016
2005-03-15
2005-03-15
0 days
119
24810016
2005-03-23
2005-03-23
0 days
120
24810016
2005-04-20
2005-04-20
0 days
121
24810016
2005-04-21
2005-04-21
0 days
122
24810016
2005-06-20
2005-06-20
0 days
123
24810016
2005-06-29
2005-06-29
0 days
124
24810016
2005-08-12
2005-08-12
0 days
125
24810016
2005-09-15
2005-09-15
0 days
126
24810016
2005-09-22
2005-09-22
0 days
127
24810016
2005-10-27
2005-10-27
0 days
128
24810016
2005-12-07
2005-12-07
0 days
129
24810016
2005-12-08
2005-12-08
0 days
130
24810016
2006-01-10
2006-01-10
0 days
131
24810016
2006-01-24
2006-01-24
0 days
132
24810016
2006-02-09
2006-02-09
0 days
133
24810016
2006-02-24
2006-02-24
0 days
134
24810016
2006-04-13
2006-04-13
0 days
135
24810016
2006-04-19
2006-04-19
0 days
136
24810016
2006-06-07
2006-06-07
0 days
137
24810016
2006-06-12
2006-06-12
0 days
138
24810016
2006-07-10
2006-07-10
0 days
139
24810016
2006-07-13
2006-07-13
0 days
140
24810016
2006-08-10
2006-08-10
0 days
141
24810016
2006-08-14
2006-08-14
0 days
142
24810016
2006-08-24
2006-08-24
0 days
143
24810016
2006-10-05
2006-10-05
0 days
144
24810016
2006-12-11
2006-12-11
0 days
145
24810016
2006-12-13
2006-12-13
0 days
146
24810016
2007-01-12
2007-01-12
0 days
147
24810016
2007-01-19
2007-01-19
0 days
148
24810016
2007-02-12
2007-02-12
0 days
149
24810016
2007-03-05
2007-03-05
0 days
150
24810016
2007-03-28
2007-03-28
0 days
151
24810016
2007-04-23
2007-04-23
0 days
152
24810016
2007-04-26
2007-04-26
0 days
153
24810016
2007-06-08
2007-06-08
0 days
154
24810016
2007-06-25
2007-06-25
0 days
155
24810016
2007-07-24
2007-07-24
0 days
156
24810016
2007-07-25
2007-07-25
0 days
157
24810016
2007-10-05
2007-10-05
0 days
158
24810016
2007-10-16
2007-10-16
0 days
159
24810016
2007-11-13
2007-11-13
0 days
160
24810016
2007-12-12
2007-12-12
0 days
161
24810016
2008-01-30
2008-01-30
0 days
162
24810016
2008-02-05
2008-02-05
0 days
163
24810016
2008-03-05
2008-03-05
0 days
164
24810016
2008-03-26
2008-03-26
0 days
165
24810016
2008-04-14
2008-04-14
0 days
166
24810016
2008-04-28
2008-04-28
0 days
167
24810016
2008-05-13
2008-05-13
0 days
168
24810016
2008-06-09
2008-06-09
0 days
169
24810016
2008-07-10
2008-07-10
0 days
170
24810016
2008-07-15
2008-07-15
0 days
171
24810016
2008-08-07
2008-08-07
0 days
172
24810016
2008-09-10
2008-09-10
0 days
173
24810016
2008-10-30
2008-10-30
0 days
174
24810016
2008-11-18
2008-11-18
0 days
175
24810016
2008-12-22
2008-12-22
0 days
176
24810016
2009-01-08
2009-01-08
0 days
177
24810016
2009-02-11
2009-02-11
0 days
178
24810016
2009-04-17
2009-04-17
0 days
179
24810016
2009-05-07
2009-05-07
0 days
180
24810016
2009-05-18
2009-05-18
0 days
181
24810016
2009-06-10
2009-06-10
0 days
182
24810016
2009-06-18
2009-06-18
0 days
183
24810016
2009-07-23
2009-07-23
0 days
184
24810016
2009-08-20
2009-08-20
0 days
185
24810016
2009-08-26
2009-08-26
0 days
186
24810016
2009-09-18
2009-09-18
0 days
187
24810016
2009-09-28
2009-09-28
0 days
188
24810016
2009-10-16
2009-10-16
0 days
189
24810016
2009-10-28
2009-10-28
0 days
190
24810016
2009-11-13
2009-11-13
0 days
191
24810016
2009-11-30
2009-11-30
0 days
192
24810016
2009-12-16
2009-12-16
0 days
193
24810016
2009-12-28
2009-12-28
0 days
194
24810016
2010-01-13
2010-01-13
0 days
195
24810016
2010-01-27
2010-01-27
0 days
196
24810016
2010-02-22
2010-02-22
0 days
197
24810016
2010-03-15
2010-03-15
0 days
198
24810016
2010-03-22
2010-03-22
0 days
199
24810016
2010-04-19
2010-04-19
0 days
200
24810016
2010-04-21
2010-04-21
0 days
201
24810016
2010-06-02
2010-06-02
0 days
202
24810016
2010-06-07
2010-06-07
0 days
203
24810016
2010-07-07
2010-07-07
0 days
204
24810016
2010-07-08
2010-07-08
0 days
205
24810016
2010-08-11
2010-08-11
0 days
206
24810016
2010-09-07
2010-09-07
0 days
207
24810016
2010-09-08
2010-09-08
0 days
208
24810016
2010-10-06
2010-10-06
0 days
209
24810016
2010-10-07
2010-10-07
0 days
210
24810016
2010-11-08
2010-11-08
0 days
211
24810016
2010-12-02
2010-12-02
0 days
212
24810016
2011-01-05
2011-01-05
0 days
213
24810016
2011-01-31
2011-01-31
0 days
214
24810016
2011-02-24
2011-02-24
0 days
215
24810016
2011-03-16
2011-03-16
0 days
216
24810016
2011-04-13
2011-04-13
0 days
217
24810016
2011-04-21
2011-04-21
0 days
218
24810016
2011-05-11
2011-05-11
0 days
219
24810016
2011-05-23
2011-05-23
0 days
220
24810016
2011-06-10
2011-06-10
0 days
221
24810016
2011-07-08
2011-07-08
0 days
222
24810016
2011-08-16
2011-08-16
0 days
223
24810016
2011-08-22
2011-08-22
0 days
224
24810016
2011-10-04
2011-10-04
0 days
225
24810016
2011-11-02
2011-11-02
0 days
226
24810016
2011-12-05
2011-12-05
0 days
227
24810016
2012-01-09
2012-01-09
0 days
228
24810016
2012-02-09
2012-02-09
0 days
229
24810016
2012-04-04
2012-04-04
0 days
230
24810016
2012-05-03
2012-05-03
0 days
231
24810016
2012-06-06
2012-06-06
0 days
232
24810016
2012-07-05
2012-07-05
0 days
233
24810016
2012-08-23
2012-08-23
0 days
234
24810016
2012-09-24
2012-09-24
0 days
235
24810016
2012-12-14
2012-12-14
0 days
236
24810016
2013-01-16
2013-01-16
0 days
237
24810016
2013-03-04
2013-03-04
0 days
238
24810016
2013-03-20
2013-03-20
0 days
239
24810016
2013-04-15
2013-04-15
0 days
240
24810016
2013-04-24
2013-04-24
0 days
241
24810016
2013-05-14
2013-05-14
0 days
242
24810016
2013-05-29
2013-05-29
0 days
243
24810016
2013-06-11
2013-06-11
0 days
244
24810016
2013-07-11
2013-07-11
0 days
245
24810016
2013-08-21
2013-08-21
0 days
246
24810016
2013-09-18
2013-09-18
0 days
247
24810016
2013-11-18
2013-11-18
0 days
248
24810016
2013-12-23
2013-12-23
0 days
249
24810016
2014-03-25
2014-03-25
0 days
250
24810016
2014-04-22
2014-04-22
0 days
251
24810016
2014-05-20
2014-05-20
0 days
252
24810016
2014-06-04
2014-06-04
0 days
253
24810016
2014-06-17
2014-06-17
0 days
254
24810016
2014-07-07
2014-07-07
0 days
255
24810016
2014-07-18
2014-07-18
0 days
256
24810016
2014-08-15
2014-08-15
0 days
257
24810016
2014-09-15
2014-09-15
0 days
258
24810016
2014-10-08
2014-10-08
0 days
259
24810016
2014-10-15
2014-10-15
0 days
260
24810016
2015-01-20
2015-01-20
0 days
261
24810016
2015-02-25
2015-02-25
0 days
262
24810016
2015-03-27
2015-03-27
0 days
263
24810016
2015-05-18
2015-05-18
0 days
264
24810016
2015-05-19
2015-05-19
0 days
265
24810016
2015-06-17
2015-06-17
0 days
266
24810016
2015-06-25
2015-06-25
0 days
267
24810016
2015-07-15
2015-07-15
0 days
268
24810016
2015-07-30
2015-07-30
0 days
269
24810016
2015-08-17
2015-08-17
0 days
270
24810016
2015-08-27
2015-08-27
0 days
271
24810016
2015-09-29
2015-09-29
0 days
272
24810016
2015-10-27
2015-10-27
0 days
273
26523392
2001-03-26
2001-03-26
0 days
274
26523392
2002-02-08
2002-02-06
2 days
275
26523392
2002-04-23
2002-04-22
1 days
276
26523392
2002-07-24
2002-07-22
2 days
277
26523392
2004-02-12
2004-02-10
2 days
278
26523392
2004-03-25
2004-03-25
0 days
279
26523392
2004-12-22
2004-12-21
1 days
280
26523392
2006-01-27
2006-01-27
0 days
281
26523392
2006-02-03
2006-02-03
0 days
282
26523392
2006-03-08
2006-03-07
1 days
283
26523392
2006-05-31
2006-05-31
0 days
284
26523392
2006-08-17
2006-08-16
1 days
285
26523392
2006-09-20
2006-09-20
0 days
286
26523392
2006-09-21
2006-09-21
0 days
287
26523392
2006-12-29
2006-12-29
0 days
288
26523392
2007-06-12
2007-06-11
1 days
289
26523392
2007-10-10
2007-10-02
8 days
290
26523392
2007-10-24
2007-10-23
1 days
291
26523392
2009-01-03
2009-01-02
1 days
292
26523392
2014-09-26
2014-09-26
0 days
293
26523392
2014-09-29
2014-09-26
3 days
294
26523392
2014-09-29
2014-09-29
0 days
296
26523392
2014-10-30
2014-10-26
4 days
297
26523392
2014-10-30
2014-10-30
0 days
298
26523392
2014-11-12
2014-11-04
8 days
299
27421632
2006-07-16
2006-07-15
1 days
300
27421632
2006-08-17
2006-08-17
0 days
301
27421632
2006-08-18
2006-08-18
0 days
302
27421632
2006-08-21
2006-08-21
0 days
303
27421632
2006-09-14
2006-08-31
14 days
304
27421632
2006-09-20
2006-09-11
9 days
305
27421632
2006-10-02
2006-09-29
3 days
306
27421632
2006-10-10
2006-10-10
0 days
307
27421632
2006-10-17
2006-10-04
13 days
308
27421632
2006-10-17
2006-10-06
11 days
309
27421632
2006-12-13
2006-12-06
7 days
310
27421632
2006-12-23
2006-12-23
0 days
311
27421632
2007-01-18
2007-01-11
7 days
312
27421632
2007-02-27
2007-02-15
12 days
313
27421632
2007-02-27
2007-02-16
11 days
314
27421632
2007-03-04
2007-03-04
0 days
315
27421632
2007-03-21
2007-03-16
5 days
316
27421632
2007-03-19
2007-03-16
3 days
317
27421632
2007-06-19
2007-06-18
1 days
318
27421632
2007-08-03
2007-08-03
0 days
319
27421632
2009-07-03
2009-07-02
1 days
320
27421632
2009-07-10
2009-07-07
3 days
321
27421632
2009-07-22
2009-07-10
12 days
322
27421632
2010-01-07
2010-01-06
1 days
323
27421632
2010-02-04
2010-02-01
3 days
324
27421632
2010-03-02
2010-03-02
0 days
325
27421632
2010-06-01
2010-05-31
1 days
326
27421632
2010-07-15
2010-03-01
136 days
327
27421632
2010-07-16
2010-04-01
106 days
328
27421632
2010-07-16
2010-05-01
76 days
329
27421632
2010-09-01
2010-06-01
92 days
330
27421632
2010-09-01
2010-07-01
62 days
331
27421632
2010-10-01
2010-08-01
61 days
332
27421632
2010-12-20
2010-09-01
110 days
333
27421632
2011-01-11
2010-10-01
102 days
334
27421632
2011-02-02
2010-11-01
93 days
335
27421632
2011-03-24
2011-01-01
82 days
336
27421632
2011-03-24
2011-02-01
51 days
337
27421632
2011-04-26
2010-12-01
146 days
338
27421632
2011-04-26
2011-03-01
56 days
339
27421632
2011-04-28
2011-04-27
1 days
340
27421632
2011-06-17
2011-04-01
77 days
341
27421632
2011-06-17
2011-05-01
47 days
342
27421632
2011-07-27
2011-06-01
56 days
343
27421632
2011-08-17
2011-07-01
47 days
344
27421632
2011-09-26
2011-08-01
56 days
345
27421632
2011-10-31
2011-09-01
60 days
346
27421632
2011-12-15
2011-12-15
0 days
347
27421632
2011-12-16
2011-10-01
76 days
348
27421632
2012-01-26
2012-01-26
0 days
349
27421632
2012-04-05
2012-04-05
0 days
350
27421632
2012-02-01
2011-11-01
92 days
351
27421632
2012-02-22
2012-02-22
0 days
352
27421632
2012-05-23
2011-12-01
174 days
353
27421632
2012-06-07
2012-06-06
1 days
354
27421632
2012-06-19
2012-01-01
170 days
355
27421632
2012-07-19
2012-02-01
169 days
356
27421632
2012-08-30
2012-08-28
2 days
357
27421632
2012-09-28
2012-03-01
211 days
358
27421632
2012-09-28
2012-04-01
180 days
359
27421632
2012-11-18
2012-11-18
0 days
360
27421632
2012-06-01
2012-06-01
0 days
361
27421632
2012-12-05
2012-05-01
218 days
362
27421632
2013-02-06
2013-01-07
30 days
363
27421632
2013-02-08
2012-08-01
191 days
364
27421632
2013-02-10
2013-02-10
0 days
365
27421632
2013-02-12
2012-09-01
164 days
366
27421632
2013-02-26
2012-12-01
87 days
367
27421632
2013-04-30
2013-04-30
0 days
368
27421632
2013-06-05
2013-06-04
1 days
369
27421632
2013-06-12
2013-02-01
131 days
370
27421632
2013-07-10
2013-07-09
1 days
371
27421632
2013-11-15
2013-11-14
1 days
372
27421632
2014-01-23
2014-01-23
0 days
373
27421632
2014-02-04
2014-02-03
1 days
374
27421632
2014-02-10
2014-02-10
0 days
375
27421632
2014-03-06
2014-03-04
2 days
376
27421632
2014-03-21
2014-03-18
3 days
377
27421632
2014-05-05
2014-05-05
0 days
378
27421632
2014-05-16
2014-05-16
0 days
379
27421632
2014-06-18
2014-06-18
0 days
380
27421632
2014-09-14
2014-09-12
2 days
381
27421632
2014-09-22
2014-09-22
0 days
382
27421632
2014-10-31
2014-10-30
1 days
383
27421632
2014-11-02
2014-11-01
1 days
385
27421632
2014-12-08
2014-12-08
0 days
386
27421632
2014-12-07
2014-12-06
1 days
387
27421632
2015-07-20
2015-07-20
0 days
388
27421632
2015-07-29
2015-07-26
3 days
389
27421632
2015-08-13
2015-08-04
9 days
390
27421632
2015-08-29
2015-08-28
1 days
391
27421632
2015-10-09
2015-10-09
0 days
392
27421632
2015-10-30
2015-10-20
10 days
393
28438144
2000-07-01
2000-06-13
18 days
394
28438144
2000-12-29
2000-12-22
7 days
395
28438144
2002-09-03
2002-09-03
0 days
396
28438144
2003-03-07
2003-03-04
3 days
397
28438144
2003-10-14
2003-10-09
5 days
398
28438144
2003-10-27
2003-10-20
7 days
399
28438144
2003-10-28
2003-10-28
0 days
400
28438144
2003-11-07
2003-11-01
6 days
401
28438144
2003-12-03
2003-12-02
1 days
402
28438144
2003-12-23
2003-12-12
11 days
403
28438144
2004-01-07
2003-12-17
21 days
405
28438144
2004-02-20
2004-02-12
8 days
406
28438144
2004-02-26
2004-02-25
1 days
407
28438144
2004-05-01
2004-04-26
5 days
408
28438144
2004-05-07
2004-05-05
2 days
409
28438144
2004-05-13
2004-05-12
1 days
410
28438144
2004-07-10
2004-07-10
0 days
411
28438144
2004-09-22
2004-09-21
1 days
412
28438144
2004-12-01
2004-12-01
0 days
413
28438144
2004-12-08
2004-12-08
0 days
414
28438144
2005-04-07
2005-04-07
0 days
415
28438144
2005-05-02
2005-04-30
2 days
416
28438144
2005-05-03
2005-05-02
1 days
417
28438144
2005-10-25
2005-10-25
0 days
418
28438144
2005-10-26
2005-10-26
0 days
419
28438144
2005-11-04
2005-11-02
2 days
420
28438144
2006-01-26
2006-01-26
0 days
421
28438144
2006-04-04
2006-04-03
1 days
422
28438144
2006-11-08
2006-11-06
2 days
423
28438144
2006-11-16
2006-11-13
3 days
424
28438144
2006-11-21
2006-11-20
1 days
425
28438144
2007-02-09
2007-02-06
3 days
426
28438144
2007-02-08
2007-02-07
1 days
427
28438144
2007-03-15
2007-03-13
2 days
428
28438144
2007-03-27
2007-03-27
0 days
429
28438144
2007-05-22
2007-05-22
0 days
430
28438144
2007-06-13
2007-06-12
1 days
431
28438144
2007-06-14
2007-06-14
0 days
432
28438144
2007-06-25
2007-06-25
0 days
433
28438144
2011-01-14
2011-01-14
0 days
434
28438144
2011-04-05
2011-04-02
3 days
435
28438144
2012-01-06
2011-12-17
20 days
436
28438144
2012-01-10
2012-01-10
0 days
437
28438144
2012-01-12
2012-01-12
0 days
438
28438144
2012-01-19
2012-01-19
0 days
439
28438144
2012-01-25
2012-01-24
1 days
441
28438144
2012-02-14
2012-02-14
0 days
442
28438144
2012-03-08
2012-02-23
14 days
443
28438144
2012-03-19
2012-03-13
6 days
444
28438144
2012-03-16
2012-03-15
1 days
445
28438144
2012-03-23
2012-03-20
3 days
446
28438144
2012-03-29
2012-03-27
2 days
447
28438144
2012-03-30
2012-03-29
1 days
448
28438144
2012-04-05
2012-04-05
0 days
449
28438144
2012-04-06
2012-04-04
2 days
450
28438144
2012-04-12
2012-04-09
3 days
451
28438144
2012-04-13
2012-04-12
1 days
452
28438144
2012-04-24
2012-04-20
4 days
453
28438144
2012-05-09
2012-05-08
1 days
454
28438144
2012-05-18
2012-05-18
0 days
455
28438144
2012-08-31
2012-08-27
4 days
456
28438144
2012-09-07
2012-09-07
0 days
459
28438144
2012-09-14
2012-09-13
1 days
460
28438144
2012-09-28
2012-09-26
2 days
461
28438144
2012-10-02
2012-09-28
4 days
462
28438144
2012-10-09
2012-10-09
0 days
463
28438144
2012-10-19
2012-10-18
1 days
464
28438144
2012-10-22
2012-10-22
0 days
465
28438144
2012-11-15
2012-11-15
0 days
466
28438144
2012-12-01
2012-11-28
3 days
467
28438144
2013-01-15
2013-01-10
5 days
468
28438144
2013-01-23
2013-01-22
1 days
469
28438144
2013-01-25
2013-01-24
1 days
470
28438144
2013-01-26
2013-01-25
1 days
471
28438144
2013-01-27
2013-01-27
0 days
472
28438144
2013-07-03
2013-07-02
1 days
473
28438144
2014-02-28
2014-02-28
0 days
474
28438144
2014-04-10
2014-03-28
13 days
475
28438144
2014-07-02
2014-05-13
50 days
476
28438144
2014-07-29
2014-07-29
0 days
477
28438144
2014-07-12
2014-07-12
0 days
478
28438144
2014-07-18
2014-07-13
5 days
479
28438144
2014-10-13
2014-10-12
1 days
480
28438144
2014-10-21
2014-10-15
6 days
481
28438144
2014-10-30
2014-10-28
2 days
482
28438144
2015-04-09
2015-04-08
1 days
483
28438144
2015-05-09
2015-05-05
4 days
484
28438144
2015-06-03
2015-06-03
0 days
485
28438144
2015-06-12
2015-06-11
1 days
486
28438144
2015-07-02
2015-06-21
11 days
487
28653184
2003-04-12
2003-04-10
2 days
488
28653184
2003-05-16
2003-05-15
1 days
489
28653184
2003-05-16
2003-05-16
0 days
490
28653184
2003-05-19
2003-05-16
3 days
491
28653184
2003-10-02
2003-10-02
0 days
492
28653184
2004-11-08
2004-11-08
0 days
493
28653184
2005-01-16
2005-01-15
1 days
494
28653184
2005-04-19
2005-04-16
3 days
495
28653184
2005-09-23
2005-09-19
4 days
496
28653184
2006-01-07
2006-01-06
1 days
497
28653184
2006-02-24
2006-02-20
4 days
498
28653184
2007-01-06
2007-01-05
1 days
499
28653184
2007-04-20
2007-04-18
2 days
500
28653184
2007-07-13
2007-07-12
1 days
501
28653184
2007-07-25
2007-07-24
1 days
502
28653184
2007-08-24
2007-08-23
1 days
503
28653184
2007-09-11
2007-09-10
1 days
504
28653184
2007-09-16
2007-09-15
1 days
505
28653184
2007-09-25
2007-09-24
1 days
506
28653184
2007-09-28
2007-09-25
3 days
507
28653184
2007-10-07
2007-10-07
0 days
508
28653184
2008-01-13
2008-01-12
1 days
509
28653184
2008-01-18
2008-01-17
1 days
510
28653184
2008-01-23
2008-01-21
2 days
511
28653184
2008-03-02
2008-03-02
0 days
512
28653184
2008-03-03
2008-03-03
0 days
513
28653184
2008-04-05
2008-04-04
1 days
514
28653184
2008-04-18
2008-04-12
6 days
515
28653184
2008-04-23
2008-04-23
0 days
516
28653184
2008-04-24
2008-04-23
1 days
517
28653184
2008-06-02
2008-06-01
1 days
518
28653184
2008-10-07
2008-09-25
12 days
519
28653184
2008-11-18
2008-11-16
2 days
520
28653184
2008-12-10
2008-12-10
0 days
521
28653184
2008-12-13
2008-12-11
2 days
522
28653184
2008-12-25
2008-12-24
1 days
523
28653184
2009-01-14
2009-01-13
1 days
524
28653184
2009-01-17
2009-01-14
3 days
525
28653184
2009-01-21
2009-01-21
0 days
526
28653184
2009-10-07
2009-10-05
2 days
527
28653184
2009-11-12
2009-11-10
2 days
528
28653184
2009-11-26
2009-11-23
3 days
529
28653184
2009-11-25
2009-11-23
2 days
530
28653184
2010-01-09
2010-01-08
1 days
531
28653184
2010-01-15
2010-01-14
1 days
532
28653184
2010-03-26
2010-03-25
1 days
533
28653184
2010-05-11
2010-05-10
1 days
534
28653184
2010-05-13
2010-05-11
2 days
535
28653184
2010-06-28
2010-06-27
1 days
536
28653184
2010-08-21
2010-08-20
1 days
537
28653184
2010-10-15
2010-10-09
6 days
538
28653184
2010-10-10
2010-10-09
1 days
539
28653184
2010-11-09
2010-10-25
15 days
540
28653184
2010-12-30
2010-12-30
0 days
541
28653184
2010-12-31
2010-12-30
1 days
542
28653184
2011-01-05
2011-01-04
1 days
543
28653184
2011-02-01
2011-02-01
0 days
544
28653184
2011-02-02
2011-02-02
0 days
545
28653184
2011-02-22
2011-02-21
1 days
546
28653184
2011-03-06
2011-03-05
1 days
547
28653184
2011-04-10
2011-04-09
1 days
548
28653184
2011-05-07
2011-05-06
1 days
549
28653184
2011-05-30
2011-05-29
1 days
550
28653184
2011-08-18
2011-08-17
1 days
551
28653184
2011-10-23
2011-10-23
0 days
552
28653184
2011-10-25
2011-10-23
2 days
553
28653184
2011-10-25
2011-10-24
1 days
554
28653184
2011-12-01
2011-11-30
1 days
555
28653184
2012-01-10
2012-01-09
1 days
556
28653184
2012-01-28
2012-01-28
0 days
557
28653184
2012-02-06
2012-02-05
1 days
558
28653184
2012-03-26
2012-03-26
0 days
559
28653184
2012-06-04
2012-06-04
0 days
560
28653184
2012-06-24
2012-06-23
1 days
561
28653184
2012-09-03
2012-09-02
1 days
562
28653184
2012-11-07
2012-11-06
1 days
563
28653184
2012-11-17
2012-11-15
2 days
564
28653184
2012-12-05
2012-12-04
1 days
565
28653184
2012-12-25
2012-12-24
1 days
566
28653184
2013-02-13
2013-02-12
1 days
567
28653184
2013-02-20
2013-02-17
3 days
568
28653184
2013-02-22
2013-02-17
5 days
569
28653184
2013-03-31
2013-03-30
1 days
570
28653184
2013-04-04
2013-04-03
1 days
571
28653184
2013-04-06
2013-04-06
0 days
572
28653184
2013-04-08
2013-04-07
1 days
573
28653184
2013-04-24
2013-04-24
0 days
574
28653184
2013-05-01
2013-04-30
1 days
575
28653184
2013-05-03
2013-05-03
0 days
576
28653184
2013-05-13
2013-05-12
1 days
577
28653184
2013-05-25
2013-05-24
1 days
578
28653184
2013-06-06
2013-06-05
1 days
579
28653184
2013-06-08
2013-06-07
1 days
580
28653184
2013-06-09
2013-06-08
1 days
581
28653184
2013-06-27
2013-06-27
0 days
582
28653184
2013-07-12
2013-07-12
0 days
583
28653184
2013-07-18
2013-07-17
1 days
584
28653184
2013-09-10
2013-09-09
1 days
585
28653184
2013-09-18
2013-09-17
1 days
586
28653184
2013-09-30
2013-09-29
1 days
587
28653184
2013-10-01
2013-09-30
1 days
588
28653184
2013-10-10
2013-10-10
0 days
589
28653184
2013-11-02
2013-11-01
1 days
590
28653184
2013-11-20
2013-11-19
1 days
591
28653184
2013-11-21
2013-11-20
1 days
592
28653184
2013-11-22
2013-11-21
1 days
593
28653184
2013-11-28
2013-11-28
0 days
594
28653184
2013-12-01
2013-11-29
2 days
595
28653184
2013-12-27
2013-12-26
1 days
596
28653184
2014-01-02
2014-01-02
0 days
597
28653184
2014-01-14
2014-01-03
11 days
598
28653184
2014-02-01
2014-01-31
1 days
599
28653184
2014-02-09
2014-02-08
1 days
600
28653184
2014-04-06
2014-04-05
1 days
601
28653184
2014-04-09
2014-04-09
0 days
602
28653184
2014-04-25
2014-04-24
1 days
603
28653184
2014-05-26
2014-05-25
1 days
604
28653184
2014-06-10
2014-06-09
1 days
605
28653184
2014-06-13
2014-06-12
1 days
606
28653184
2014-07-24
2014-07-22
2 days
607
28653184
2014-08-01
2014-07-31
1 days
608
28653184
2014-08-06
2014-08-06
0 days
609
28653184
2014-08-09
2014-08-07
2 days
610
28653184
2014-08-21
2014-08-20
1 days
611
28653184
2014-09-04
2014-09-04
0 days
612
28653184
2014-09-21
2014-09-20
1 days
613
28653184
2014-09-28
2014-09-28
0 days
614
28653184
2014-09-30
2014-09-29
1 days
615
28653184
2014-10-04
2014-10-03
1 days
616
28653184
2014-10-17
2014-10-17
0 days
617
28653184
2014-11-05
2014-11-04
1 days
618
28653184
2014-12-02
2014-12-01
1 days
619
28653184
2015-01-01
2014-12-31
1 days
620
28653184
2015-01-22
2015-01-22
0 days
621
28653184
2015-02-05
2015-02-05
0 days
622
28653184
2015-02-20
2015-02-19
1 days
623
28653184
2015-03-10
2015-03-10
0 days
624
28653184
2015-03-16
2015-03-16
0 days
625
28653184
2015-03-22
2015-03-22
0 days
626
28653184
2015-03-28
2015-03-27
1 days
627
28653184
2015-04-30
2015-04-30
0 days
628
28653184
2015-05-05
2015-05-04
1 days
629
28653184
2015-06-19
2015-06-19
0 days
630
28653184
2015-07-21
2015-07-20
1 days
631
28653184
2015-08-07
2015-08-07
0 days
632
28653184
2015-08-09
2015-08-08
1 days
633
28653184
2015-08-29
2015-08-28
1 days
634
28653184
2015-09-07
2015-09-07
0 days
635
28653184
2015-10-06
2015-10-06
0 days
636
28653184
2015-10-26
2015-10-16
10 days
637
28990976
2003-06-11
2003-06-06
5 days
638
28990976
2003-07-15
2003-07-09
6 days
639
28990976
2003-11-26
2003-11-17
9 days
640
28990976
2003-12-02
2003-11-29
3 days
641
28990976
2004-01-06
2004-01-05
1 days
642
28990976
2004-01-26
2004-01-19
7 days
643
28990976
2006-02-07
2006-02-03
4 days
644
28990976
2006-04-17
2006-04-12
5 days
645
28990976
2006-08-23
2006-08-20
3 days
646
28990976
2006-09-06
2006-08-31
6 days
647
28990976
2007-03-13
2007-03-08
5 days
648
28990976
2007-05-13
2007-05-10
3 days
649
28990976
2007-05-14
2007-05-14
0 days
650
28990976
2007-05-14
2007-05-14
0 days
651
28990976
2007-08-03
2007-07-30
4 days
652
28990976
2007-10-28
2007-10-23
5 days
653
28990976
2007-11-09
2007-11-09
0 days
654
28990976
2007-12-21
2007-12-17
4 days
655
28990976
2008-10-27
2008-10-20
7 days
656
28990976
2008-12-19
2008-12-17
2 days
657
28990976
2009-02-18
2009-02-16
2 days
658
28990976
2009-02-27
2009-02-26
1 days
659
28990976
2009-06-01
2009-05-29
3 days
660
28990976
2009-07-05
2009-07-03
2 days
661
28990976
2009-07-23
2009-07-23
0 days
662
28990976
2011-08-02
2011-08-01
1 days
663
28990976
2012-01-16
2012-01-16
0 days
664
28990976
2012-01-17
2012-01-16
1 days
665
28990976
2012-04-13
2012-04-13
0 days
666
28990976
2012-07-23
2012-07-21
2 days
667
28990976
2012-07-30
2012-07-22
8 days
669
28990976
2012-08-19
2012-08-18
1 days
670
28990976
2014-02-11
2014-02-11
0 days
671
28990976
2014-02-20
2014-02-12
8 days
672
28990976
2014-11-07
2014-10-25
13 days
673
28990976
2015-01-14
2015-01-04
10 days
674
28990976
2015-02-03
2015-02-01
2 days
675
28990976
2015-04-07
2015-03-29
9 days
676
28990976
2015-04-26
2015-04-23
3 days
677
29779232
2001-10-11
2001-09-13
28 days
679
29779232
2012-10-19
2012-10-07
12 days
680
29779232
2012-11-14
2012-11-11
3 days
681
29779232
2012-11-13
2012-11-11
2 days
683
29779232
2012-11-14
2012-11-13
1 days
685
29779232
2012-12-06
2012-11-17
19 days
687
29779232
2012-12-07
2012-12-07
0 days
689
29779232
2012-12-18
2012-12-18
0 days
690
29779232
2013-06-06
2013-06-06
0 days
691
29779232
2013-06-07
2013-06-06
1 days
692
29779232
2013-08-08
2013-08-08
0 days
693
29779232
2013-08-12
2013-08-08
4 days
695
29779232
2013-08-12
2013-08-11
1 days
697
29779232
2013-08-14
2013-08-12
2 days
699
29779232
2013-08-14
2013-08-12
2 days
700
29779232
2013-08-13
2013-08-13
0 days
701
29779232
2013-08-20
2013-08-17
3 days
702
29779232
2013-08-20
2013-08-17
3 days
703
29779232
2013-08-12
2013-08-12
0 days
704
29779232
2013-08-27
2013-08-26
1 days
705
29779232
2013-09-09
2013-09-01
8 days
706
29779232
2013-09-12
2013-09-03
9 days
707
29779232
2013-09-11
2013-08-12
30 days
708
29779232
2013-09-16
2013-09-16
0 days
709
29779232
2013-09-18
2013-09-16
2 days
710
29779232
2013-09-30
2013-09-29
1 days
711
29779232
2013-10-01
2013-09-29
2 days
712
29779232
2013-10-13
2013-10-06
7 days
713
29779232
2013-10-07
2013-10-06
1 days
714
29779232
2013-12-05
2013-12-01
4 days
715
29779232
2013-12-12
2013-12-02
10 days
716
29779232
2014-02-21
2014-02-19
2 days
717
29779232
2014-03-05
2014-02-20
13 days
718
29779232
2014-03-13
2014-03-11
2 days
719
29779232
2014-03-26
2014-03-11
15 days
720
29779232
2014-07-24
2014-07-23
1 days
721
29779232
2014-08-07
2014-07-26
12 days
722
29779232
2014-10-22
2014-10-04
18 days
723
29779232
2014-10-27
2014-10-25
2 days
724
29779232
2014-12-02
2014-11-21
11 days
725
29779232
2014-12-15
2014-12-04
11 days
726
29779232
2015-02-06
2015-02-06
0 days
727
29779232
2015-05-11
2015-05-02
9 days
728
29779232
2015-07-10
2015-07-09
1 days
729
29779232
2015-10-22
2015-10-21
1 days
730
29779232
2015-10-27
2015-10-24
3 days
731
29779232
2015-10-31
2015-10-30
1 days
732
30041536
1995-01-17
1995-01-17
0 days
733
30041536
2000-10-05
2000-10-05
0 days
734
30041536
2001-01-11
2001-01-09
2 days
735
30041536
2001-07-31
2001-07-30
1 days
736
30041536
2001-08-07
2001-08-07
0 days
737
30041536
2002-02-11
2002-02-11
0 days
738
30041536
2002-03-05
2002-03-04
1 days
739
30041536
2002-03-27
2002-03-25
2 days
740
30041536
2002-09-03
2002-09-03
0 days
741
30041536
2002-09-04
2002-09-04
0 days
742
30041536
2003-01-28
2003-01-28
0 days
743
30041536
2003-04-14
2003-04-14
0 days
744
30041536
2003-05-01
2003-05-01
0 days
745
30041536
2003-05-08
2003-05-08
0 days
746
30041536
2003-06-10
2003-06-10
0 days
747
30041536
2003-08-11
2003-08-11
0 days
748
30041536
2003-08-29
2003-08-27
2 days
749
30041536
2003-09-04
2003-09-04
0 days
750
30041536
2003-09-11
2003-09-11
0 days
751
30041536
2003-09-30
2003-09-25
5 days
752
30041536
2003-10-17
2003-10-14
3 days
753
30041536
2003-10-21
2003-10-21
0 days
754
30041536
2003-11-12
2003-11-12
0 days
755
30041536
2004-01-23
2004-01-16
7 days
756
30041536
2004-01-21
2004-01-21
0 days
757
30041536
2004-02-05
2004-02-05
0 days
758
30041536
2004-02-26
2004-02-19
7 days
759
30041536
2004-02-27
2004-02-27
0 days
760
30041536
2004-03-09
2004-03-09
0 days
761
30041536
2004-03-17
2004-03-17
0 days
762
30041536
2004-03-25
2004-03-25
0 days
763
30041536
2004-04-20
2004-04-20
0 days
764
30041536
2004-05-27
2004-05-27
0 days
765
30041536
2004-06-01
2004-06-01
0 days
766
30041536
2004-06-04
2004-06-04
0 days
767
30041536
2004-06-15
2004-06-09
6 days
768
30041536
2004-06-10
2004-06-10
0 days
769
30041536
2004-06-14
2004-06-14
0 days
770
30041536
2004-06-16
2004-06-16
0 days
771
30041536
2004-08-12
2004-08-12
0 days
772
30041536
2004-08-19
2004-08-19
0 days
773
30041536
2004-08-24
2004-08-24
0 days
774
30041536
2004-09-02
2004-09-02
0 days
775
30041536
2004-09-23
2004-09-23
0 days
776
30041536
2004-10-06
2004-10-06
0 days
777
30041536
2004-10-18
2004-10-18
0 days
778
30041536
2004-11-17
2004-11-17
0 days
779
30041536
2004-11-23
2004-11-23
0 days
780
30041536
2004-11-30
2004-11-30
0 days
781
30041536
2004-12-21
2004-12-21
0 days
782
30041536
2005-03-08
2005-03-08
0 days
783
30041536
2005-03-10
2005-03-10
0 days
784
30041536
2005-03-24
2005-03-23
1 days
785
30041536
2005-04-19
2005-04-19
0 days
786
30041536
2005-04-20
2005-04-20
0 days
787
30041536
2005-05-26
2005-05-26
0 days
788
30041536
2005-06-14
2005-06-14
0 days
789
30041536
2005-06-16
2005-06-16
0 days
790
30041536
2005-07-12
2005-07-12
0 days
791
30041536
2005-07-20
2005-07-14
6 days
792
30041536
2005-07-18
2005-07-18
0 days
793
30041536
2005-08-09
2005-08-09
0 days
794
30041536
2005-09-28
2005-09-28
0 days
795
30041536
2005-11-03
2005-11-03
0 days
796
30041536
2005-11-15
2005-11-15
0 days
797
30041536
2005-11-22
2005-11-22
0 days
798
30041536
2005-11-29
2005-11-29
0 days
799
30041536
2006-03-23
2006-03-23
0 days
800
30041536
2006-03-29
2006-03-28
1 days
801
30041536
2006-03-28
2006-03-28
0 days
802
30041536
2006-04-19
2006-04-19
0 days
803
30041536
2006-04-27
2006-04-27
0 days
804
30041536
2006-05-12
2006-05-02
10 days
805
30041536
2006-05-16
2006-05-11
5 days
806
30041536
2006-05-16
2006-05-16
0 days
807
30041536
2006-07-18
2006-07-18
0 days
808
30041536
2006-08-30
2006-08-29
1 days
809
30041536
2006-09-05
2006-09-05
0 days
810
30041536
2006-10-10
2006-10-06
4 days
811
30041536
2006-10-10
2006-10-10
0 days
812
30041536
2006-10-11
2006-10-11
0 days
813
30041536
2007-01-16
2007-01-16
0 days
814
30041536
2007-02-01
2007-01-30
2 days
815
30041536
2007-03-27
2007-03-23
4 days
816
30041536
2007-03-30
2007-03-30
0 days
817
30041536
2007-05-02
2007-05-02
0 days
818
30041536
2007-05-10
2007-05-10
0 days
820
30041536
2007-06-12
2007-06-12
0 days
822
30041536
2007-07-12
2007-07-12
0 days
823
30041536
2007-07-23
2007-07-23
0 days
824
30041536
2007-08-17
2007-08-16
1 days
825
30041536
2007-09-25
2007-09-25
0 days
826
30041536
2007-10-11
2007-10-11
0 days
827
30041536
2007-10-16
2007-10-16
0 days
828
30041536
2007-11-01
2007-11-01
0 days
829
30041536
2007-11-09
2007-11-08
1 days
830
30041536
2007-11-20
2007-11-20
0 days
831
30041536
2007-12-18
2007-12-18
0 days
832
30041536
2007-12-18
2007-12-18
0 days
833
30041536
2008-03-10
2008-03-10
0 days
834
30041536
2008-03-20
2008-03-20
0 days
835
30041536
2008-03-27
2008-03-27
0 days
836
30041536
2008-04-10
2008-04-10
0 days
837
30041536
2008-04-16
2008-04-16
0 days
838
30041536
2008-05-09
2008-05-02
7 days
839
30041536
2008-05-16
2008-05-14
2 days
840
30041536
2008-06-25
2008-06-25
0 days
841
30041536
2008-06-26
2008-06-25
1 days
842
30041536
2008-08-14
2008-08-14
0 days
843
30041536
2008-08-21
2008-08-21
0 days
844
30041536
2008-09-09
2008-09-09
0 days
845
30041536
2008-10-02
2008-09-24
8 days
846
30041536
2008-09-24
2008-09-24
0 days
847
30041536
2008-10-01
2008-09-30
1 days
848
30041536
2008-10-09
2008-10-09
0 days
849
38659264
2003-12-30
2003-12-30
0 days
850
38659264
2004-04-13
2004-04-13
0 days
851
38659264
2006-12-20
2006-12-17
3 days
852
38659264
2006-12-28
2006-12-28
0 days
853
38659264
2007-01-03
2006-12-28
6 days
854
38659264
2008-05-21
2008-05-21
0 days
855
38659264
2008-07-03
2008-07-03
0 days
856
38659264
2008-10-16
2008-10-16
0 days
857
38659264
2009-02-23
2009-02-23
0 days
858
38659264
2009-12-05
2009-12-04
1 days
859
38659264
2009-12-07
2009-12-07
0 days
860
38659264
2009-12-07
2009-12-07
0 days
861
38659264
2010-02-09
2010-02-09
0 days
862
38659264
2010-07-09
2010-07-08
1 days
863
38659264
2010-08-03
2010-08-03
0 days
864
38659264
2010-10-25
2010-10-25
0 days
865
38659264
2011-01-25
2011-01-24
1 days
866
38659264
2011-05-28
2011-05-28
0 days
867
38659264
2011-06-09
2011-06-09
0 days
868
38659264
2011-06-21
2011-06-21
0 days
869
38659264
2011-12-22
2011-12-21
1 days
870
38659264
2012-04-03
2012-04-02
1 days
871
38659264
2012-05-11
2012-05-11
0 days
872
38659264
2012-05-22
2012-05-20
2 days
873
38659264
2012-05-23
2012-05-21
2 days
874
38659264
2012-07-20
2012-07-19
1 days
875
38659264
2012-07-30
2012-07-30
0 days
876
38659264
2013-12-13
2013-11-30
13 days
878
38659264
2013-12-31
2013-12-26
5 days
879
38659264
2014-01-15
2014-01-14
1 days
880
38659264
2014-02-07
2014-02-07
0 days
881
38659264
2014-02-12
2014-02-12
0 days
882
38659264
2014-02-19
2014-02-19
0 days
883
38659264
2014-09-24
2014-09-23
1 days
884
38659264
2015-07-22
2015-07-20
2 days
885
38659264
2015-07-29
2015-07-28
1 days
886
39541824
2005-01-07
2005-01-05
2 days
887
39541824
2007-04-22
2007-04-22
0 days
888
39541824
2008-02-23
2008-02-18
5 days
889
39541824
2008-06-03
2008-05-28
6 days
890
39541824
2008-06-17
2008-06-04
13 days
891
39541824
2008-06-30
2008-06-24
6 days
892
39541824
2008-07-08
2008-06-27
11 days
893
39541824
2009-05-30
2009-05-29
1 days
894
39541824
2011-04-11
2011-04-10
1 days
895
39541824
2011-04-11
2011-04-10
1 days
896
39541824
2011-04-15
2011-04-14
1 days
897
39541824
2011-04-20
2011-04-19
1 days
898
39541824
2012-01-20
2012-01-19
1 days
899
39541824
2013-04-26
2013-04-25
1 days
900
39541824
2013-04-27
2013-04-26
1 days
901
39541824
2013-04-28
2013-04-27
1 days
902
39541824
2013-04-29
2013-04-28
1 days
903
39541824
2014-09-24
2014-09-24
0 days
904
40160736
2002-11-13
2002-11-13
0 days
905
40160736
2003-04-16
2003-04-16
0 days
906
40160736
2003-05-19
2003-05-16
3 days
907
40160736
2003-05-23
2003-05-20
3 days
908
40160736
2003-05-21
2003-05-21
0 days
909
40160736
2003-07-09
2003-07-09
0 days
910
40160736
2003-07-23
2003-07-23
0 days
911
40160736
2003-07-28
2003-07-28
0 days
912
40160736
2004-02-04
2004-02-04
0 days
913
40160736
2006-10-19
2006-10-18
1 days
915
40160736
2007-04-17
2007-04-12
5 days
916
40160736
2007-04-13
2007-04-13
0 days
917
40160736
2007-09-11
2007-09-10
1 days
918
40160736
2007-10-03
2007-10-03
0 days
919
40160736
2007-10-09
2007-10-09
0 days
920
40160736
2008-01-03
2008-01-03
0 days
921
40160736
2008-01-14
2008-01-11
3 days
922
40160736
2008-06-06
2008-06-04
2 days
923
40160736
2008-06-13
2008-06-12
1 days
924
40160736
2008-09-22
2008-09-22
0 days
925
40160736
2009-01-06
2009-01-06
0 days
926
40160736
2009-01-06
2009-01-06
0 days
927
40160736
2009-03-02
2009-03-02
0 days
928
40160736
2009-04-27
2009-04-27
0 days
929
40160736
2009-08-26
2009-08-26
0 days
930
40160736
2010-02-01
2010-02-01
0 days
931
40160736
2010-02-18
2010-02-18
0 days
932
40160736
2010-05-03
2010-05-03
0 days
933
40160736
2010-08-02
2010-08-02
0 days
934
40160736
2010-11-01
2010-10-29
3 days
935
40160736
2010-11-05
2010-11-05
0 days
936
40160736
2010-11-08
2010-11-05
3 days
937
40160736
2011-02-09
2011-02-09
0 days
938
40160736
2011-09-13
2011-09-12
1 days
939
40160736
2011-11-29
2011-11-29
0 days
940
40160736
2011-12-02
2011-12-02
0 days
941
40160736
2012-02-09
2012-02-09
0 days
942
40160736
2012-03-08
2012-03-07
1 days
943
40160736
2012-10-08
2012-10-08
0 days
944
40160736
2012-10-12
2012-10-09
3 days
945
40160736
2012-10-12
2012-10-11
1 days
946
40160736
2012-11-27
2012-11-27
0 days
947
40160736
2013-02-26
2013-02-26
0 days
948
40160736
2013-05-06
2013-05-03
3 days
949
40160736
2013-07-19
2013-07-19
0 days
950
40160736
2013-08-19
2013-08-19
0 days
951
40160736
2013-08-02
2013-08-01
1 days
952
40160736
2015-05-22
2015-05-22
0 days
953
40160736
2015-06-08
2015-05-08
31 days
954
40160736
2015-06-11
2015-06-03
8 days
955
40160736
2015-06-12
2015-05-21
22 days
956
40160736
2015-06-18
2015-06-17
1 days
957
40160736
2015-06-22
2015-06-19
3 days
958
40160736
2015-06-22
2015-06-11
11 days
959
40160736
2015-07-06
2015-07-02
4 days
960
40160736
2015-07-07
2015-07-06
1 days
961
40160736
2015-07-10
2015-07-09
1 days
962
40160736
2015-07-29
2015-07-16
13 days
963
40160736
2015-08-05
2015-08-04
1 days
964
40160736
2015-08-11
2015-08-10
1 days
965
40160736
2015-08-14
2015-08-13
1 days
966
40160736
2015-08-17
2015-08-14
3 days
967
40160736
2015-09-01
2015-08-31
1 days
968
40160736
2015-09-04
2015-09-02
2 days
969
40160736
2015-09-04
2015-09-03
1 days
970
40160736
2015-09-22
2015-09-10
12 days
971
40160736
2015-09-22
2015-09-11
11 days
972
40160736
2015-09-23
2015-09-21
2 days
973
40160736
2015-09-23
2015-09-17
6 days
974
40160736
2015-09-30
2015-09-30
0 days
975
40160736
2015-10-09
2015-10-01
8 days
976
40160736
2015-10-16
2015-10-08
8 days
977
40160736
2015-10-16
2015-10-15
1 days
978
40160736
2015-10-20
2015-10-14
6 days
979
40160736
2015-10-28
2015-10-22
6 days
980
40674368
2010-07-01
2010-06-30
1 days
981
40674368
2012-07-08
2012-07-07
1 days
982
40674368
2012-07-09
2012-07-07
2 days
983
40674368
2012-08-13
2012-08-10
3 days
984
40674368
2013-06-11
2013-06-09
2 days
985
40674368
2013-06-29
2013-06-28
1 days
986
40674368
2013-07-03
2013-07-02
1 days
987
40674368
2013-07-03
2013-07-03
0 days
988
40674368
2013-07-06
2013-07-03
3 days
989
40674368
2013-07-07
2013-07-07
0 days
990
40674368
2014-05-19
2014-05-18
1 days
991
40674368
2014-05-30
2014-05-29
1 days
992
40674368
2014-09-09
2014-09-09
0 days
993
40674368
2014-09-09
2014-09-09
0 days
994
40674368
2015-02-26
2015-02-25
1 days
995
40674368
2015-03-01
2015-02-27
2 days
996
40674368
2015-03-10
2015-03-10
0 days
997
40674368
2015-04-21
2015-04-20
1 days
998
40674368
2015-05-14
2015-05-09
5 days
999
40674368
2015-05-14
2015-05-14
0 days
1000
40674368
2015-05-21
2015-05-19
2 days
1001
40674368
2015-05-23
2015-05-23
0 days
1002
40674368
2015-08-01
2015-08-01
0 days
1003
40674368
2015-08-01
2015-08-01
0 days
1004
40674368
2015-08-08
2015-08-07
1 days
1005
40674368
2015-10-19
2015-10-16
3 days
1006
40674368
2015-10-26
2015-10-24
2 days
1007
40674368
2015-10-27
2015-10-26
1 days
1008
40674368
2015-10-27
2015-10-27
0 days
1009
40674368
2015-10-27
2015-10-27
0 days
1010
40905536
1998-12-04
1998-12-04
0 days
1011
40905536
1998-12-06
1998-12-04
2 days
1012
40905536
2004-02-12
2004-02-06
6 days
1013
40905536
2004-09-28
2004-09-27
1 days
1014
40905536
2004-10-04
2004-10-04
0 days
1015
40905536
2004-10-16
2004-10-15
1 days
1016
40905536
2004-11-15
2004-11-15
0 days
1017
40905536
2004-12-27
2004-12-27
0 days
1019
40905536
2004-12-30
2004-12-27
3 days
1020
40905536
2005-04-25
2005-04-25
0 days
1021
40905536
2005-05-13
2005-05-13
0 days
1022
40905536
2005-05-18
2005-05-17
1 days
1023
40905536
2005-05-23
2005-05-23
0 days
...
...
...
...
...
8969
37209088
2010-07-08
2010-06-08
30 days
8970
37209088
2010-07-09
2010-07-08
1 days
8971
37209088
2010-07-30
2010-07-16
14 days
8972
37209088
2010-07-30
2010-07-30
0 days
8973
37209088
2010-08-11
2010-08-11
0 days
8974
37209088
2010-08-12
2010-08-12
0 days
8975
37209088
2010-08-12
2010-08-12
0 days
8976
37209088
2010-08-27
2010-08-27
0 days
8977
37209088
2010-09-08
2010-09-08
0 days
8978
37209088
2010-09-10
2010-09-10
0 days
8979
37209088
2010-10-12
2010-10-12
0 days
8980
37209088
2010-10-14
2010-10-14
0 days
8981
37209088
2010-11-10
2010-11-09
1 days
8982
37209088
2010-12-09
2010-12-08
1 days
8983
37209088
2010-12-17
2010-12-17
0 days
8984
37209088
2011-01-11
2011-01-11
0 days
8985
37209088
2011-01-14
2011-01-14
0 days
8986
37209088
2011-01-31
2011-01-31
0 days
8987
37209088
2011-02-16
2011-01-31
16 days
8988
37209088
2011-02-27
2011-02-27
0 days
8989
37209088
2011-03-14
2011-02-28
14 days
8990
37209088
2011-03-16
2011-03-15
1 days
8991
37209088
2011-03-24
2011-03-23
1 days
8992
37209088
2011-03-24
2011-03-23
1 days
8993
37209088
2011-03-30
2011-03-28
2 days
8994
37209088
2011-04-01
2011-04-01
0 days
8995
37209088
2011-04-11
2011-04-08
3 days
8997
37209088
2011-04-21
2011-04-21
0 days
8998
37209088
2011-04-21
2011-04-21
0 days
8999
37209088
2011-05-04
2011-05-04
0 days
9000
37209088
2011-05-06
2011-05-06
0 days
9001
37209088
2011-05-18
2011-05-18
0 days
9002
37209088
2011-06-02
2011-06-02
0 days
9003
37209088
2011-06-06
2011-06-06
0 days
9004
37209088
2011-06-15
2011-06-15
0 days
9005
37209088
2011-06-29
2011-06-29
0 days
9006
37209088
2011-07-06
2011-07-06
0 days
9007
37209088
2011-07-21
2011-07-21
0 days
9008
37209088
2011-08-04
2011-08-04
0 days
9009
37209088
2011-09-07
2011-09-07
0 days
9010
37209088
2011-09-07
2011-09-07
0 days
9011
37209088
2011-09-27
2011-09-27
0 days
9012
37209088
2011-10-05
2011-10-05
0 days
9013
37209088
2011-10-18
2011-10-18
0 days
9014
37209088
2011-11-23
2011-11-12
11 days
9015
37209088
2011-12-05
2011-12-01
4 days
9016
37209088
2011-12-12
2011-12-12
0 days
9017
37209088
2012-01-06
2012-01-06
0 days
9018
37209088
2012-01-09
2012-01-09
0 days
9019
37209088
2012-02-06
2012-02-06
0 days
9020
37209088
2012-03-05
2012-03-05
0 days
9021
37209088
2012-03-19
2012-03-19
0 days
9022
37209088
2012-04-04
2012-04-04
0 days
9023
37209088
2012-04-18
2012-04-18
0 days
9024
37209088
2012-04-25
2012-04-25
0 days
9025
37209088
2014-11-05
2014-10-27
9 days
9026
37209088
2015-06-01
2015-05-15
17 days
9027
37209088
2015-10-31
2015-10-30
1 days
9028
37790816
2002-06-19
2002-06-19
0 days
9029
37790816
2003-03-07
2003-02-28
7 days
9030
37790816
2003-03-12
2003-03-09
3 days
9031
37790816
2007-08-19
2007-08-19
0 days
9032
37790816
2011-05-06
2011-05-05
1 days
9033
37790816
2011-12-27
2011-12-26
1 days
9034
37790816
2012-05-09
2012-05-09
0 days
9035
37790816
2012-05-28
2012-05-27
1 days
9036
37790816
2012-06-08
2012-06-07
1 days
9037
37790816
2012-09-18
2012-09-17
1 days
9038
37790816
2013-01-13
2013-01-12
1 days
9039
37790816
2013-02-06
2013-02-05
1 days
9040
37790816
2013-02-06
2013-02-06
0 days
9041
37790816
2013-02-06
2013-02-06
0 days
9042
37790816
2013-06-05
2013-06-04
1 days
9043
37790816
2014-01-22
2014-01-21
1 days
9044
37790816
2014-02-15
2014-02-14
1 days
9045
37790816
2014-02-18
2014-02-14
4 days
9046
37790816
2014-02-14
2014-02-14
0 days
9047
37790816
2014-02-16
2014-02-15
1 days
9048
37790816
2014-02-21
2014-02-16
5 days
9049
37790816
2014-02-22
2014-02-21
1 days
9050
37790816
2014-02-23
2014-02-22
1 days
9051
37790816
2014-02-28
2014-02-27
1 days
9052
37790816
2014-03-01
2014-02-28
1 days
9053
37790816
2014-03-02
2014-03-02
0 days
9054
37790816
2014-03-13
2014-03-03
10 days
9055
37790816
2014-04-18
2014-04-17
1 days
9056
37790816
2014-04-20
2014-04-19
1 days
9057
37790816
2014-05-02
2014-05-01
1 days
9058
37790816
2014-05-07
2014-05-06
1 days
9059
37790816
2014-05-07
2014-05-07
0 days
9060
37790816
2014-05-08
2014-05-08
0 days
9061
37790816
2014-05-09
2014-05-09
0 days
9062
37790816
2014-05-16
2014-05-15
1 days
9063
37790816
2014-05-17
2014-05-16
1 days
9064
37790816
2014-05-18
2014-05-18
0 days
9065
37790816
2014-05-18
2014-05-18
0 days
9066
37790816
2014-05-20
2014-05-18
2 days
9067
37790816
2014-06-01
2014-05-31
1 days
9068
37790816
2014-06-03
2014-06-03
0 days
9069
37790816
2014-06-04
2014-06-03
1 days
9070
37790816
2014-06-17
2014-06-16
1 days
9071
37790816
2014-06-21
2014-06-20
1 days
9072
37790816
2014-06-24
2014-06-24
0 days
9073
37790816
2014-06-27
2014-06-27
0 days
9074
37790816
2014-12-08
2014-12-07
1 days
9076
37790816
2014-12-12
2014-12-08
4 days
9077
37790816
2014-12-13
2014-12-13
0 days
9078
37790816
2015-02-16
2015-02-15
1 days
9079
37790816
2015-02-18
2015-02-15
3 days
9080
37790816
2015-02-20
2015-02-19
1 days
9081
37790816
2015-02-20
2015-02-20
0 days
9082
37790816
2015-02-21
2015-02-21
0 days
9083
37790816
2015-02-22
2015-02-21
1 days
9084
37790816
2015-02-24
2015-02-23
1 days
9085
37790816
2015-03-18
2015-03-18
0 days
9086
37790816
2015-03-19
2015-03-19
0 days
9087
37790816
2015-03-19
2015-03-19
0 days
9088
37790816
2015-03-23
2015-03-19
4 days
9089
37790816
2015-03-24
2015-03-23
1 days
9090
37790816
2015-03-24
2015-03-24
0 days
9091
37790816
2015-03-27
2015-03-27
0 days
9092
37790816
2015-04-03
2015-03-28
6 days
9093
37790816
2015-03-29
2015-03-28
1 days
9094
37790816
2015-03-31
2015-03-30
1 days
9095
37790816
2015-04-02
2015-04-01
1 days
9096
37790816
2015-04-04
2015-04-03
1 days
9097
37790816
2015-04-05
2015-04-04
1 days
9098
37790816
2015-04-06
2015-04-05
1 days
9099
37790816
2015-04-07
2015-04-06
1 days
9100
37790816
2015-04-08
2015-04-07
1 days
9101
37790816
2015-04-08
2015-04-08
0 days
9102
37790816
2015-04-09
2015-04-08
1 days
9103
37790816
2015-07-16
2015-07-16
0 days
9104
37790816
2015-07-17
2015-07-17
0 days
9105
37790816
2015-07-18
2015-07-18
0 days
9106
37790816
2015-07-20
2015-07-19
1 days
9107
37790816
2015-07-20
2015-07-20
0 days
9108
37790816
2015-10-24
2015-10-24
0 days
9109
37790816
2015-10-28
2015-10-27
1 days
9110
38058496
2001-07-12
2001-07-12
0 days
9111
38058496
2006-11-30
2006-11-28
2 days
9112
38058496
2006-11-29
2006-11-28
1 days
9113
38058496
2008-11-16
2008-11-15
1 days
9114
38058496
2009-07-23
2009-07-22
1 days
9115
38058496
2014-07-29
2014-07-28
1 days
9116
38058496
2014-08-01
2014-07-31
1 days
9117
38058496
2014-08-28
2014-08-27
1 days
9118
38058496
2014-08-30
2014-08-29
1 days
9119
38058496
2014-09-12
2014-09-11
1 days
9120
38058496
2014-09-14
2014-09-13
1 days
9121
38058496
2014-09-15
2014-09-14
1 days
9122
38058496
2014-09-19
2014-09-18
1 days
9123
38058496
2014-09-21
2014-09-20
1 days
9124
38058496
2014-10-06
2014-10-05
1 days
9125
38058496
2015-02-11
2015-02-10
1 days
9126
38058496
2015-02-22
2015-02-21
1 days
9127
38058496
2015-07-09
2015-07-08
1 days
9128
38058496
2015-08-05
2015-08-04
1 days
9129
38058496
2015-08-06
2015-08-05
1 days
9130
38058496
2015-08-07
2015-08-06
1 days
9131
38058496
2015-08-08
2015-08-07
1 days
9132
38058496
2015-08-11
2015-08-10
1 days
9133
38058496
2015-09-06
2015-09-06
0 days
9134
56819168
2005-08-18
2005-08-17
1 days
9135
56819168
2005-10-12
2005-10-12
0 days
9136
56819168
2005-10-31
2005-10-31
0 days
9137
56819168
2005-11-18
2005-11-18
0 days
9138
56819168
2006-02-03
2006-02-03
0 days
9139
56819168
2006-02-13
2006-02-10
3 days
9140
56819168
2006-05-09
2006-05-08
1 days
9141
56819168
2006-08-09
2006-08-07
2 days
9142
56819168
2006-12-07
2006-12-01
6 days
9143
56819168
2006-12-12
2006-12-12
0 days
9144
56819168
2007-02-07
2007-02-05
2 days
9145
56819168
2007-04-06
2007-04-06
0 days
9146
56819168
2007-04-09
2007-04-09
0 days
9147
56819168
2007-05-01
2007-04-30
1 days
9148
56819168
2007-05-10
2007-05-10
0 days
9149
56819168
2007-07-31
2007-07-26
5 days
9150
56819168
2007-07-30
2007-07-30
0 days
9151
56819168
2007-08-27
2007-08-27
0 days
9152
56819168
2007-09-25
2007-09-25
0 days
9153
56819168
2007-10-24
2007-10-24
0 days
9154
56819168
2007-12-15
2007-12-15
0 days
9155
56819168
2007-12-17
2007-12-17
0 days
9156
56819168
2007-12-18
2007-12-18
0 days
9157
56819168
2007-12-18
2007-12-18
0 days
9158
56819168
2008-01-22
2008-01-18
4 days
9159
56819168
2008-01-29
2008-01-25
4 days
9160
56819168
2008-01-28
2008-01-28
0 days
9161
56819168
2008-02-12
2008-02-01
11 days
9162
56819168
2008-02-04
2008-02-04
0 days
9163
56819168
2008-02-25
2008-02-22
3 days
9164
56819168
2008-03-03
2008-02-29
3 days
9165
56819168
2008-03-10
2008-03-10
0 days
9166
56819168
2008-03-19
2008-03-19
0 days
9167
56819168
2008-04-07
2008-04-07
0 days
9168
56819168
2008-05-05
2008-05-02
3 days
9169
56819168
2008-06-11
2008-06-11
0 days
9170
56819168
2008-06-18
2008-06-18
0 days
9171
56819168
2008-06-30
2008-06-30
0 days
9172
56819168
2008-09-22
2008-09-22
0 days
9173
56819168
2008-10-01
2008-09-26
5 days
9174
56819168
2008-10-06
2008-10-05
1 days
9175
56819168
2008-10-17
2008-10-14
3 days
9176
56819168
2008-11-20
2008-11-19
1 days
9177
56819168
2008-11-25
2008-11-25
0 days
9178
56819168
2008-12-05
2008-12-04
1 days
9179
56819168
2008-12-26
2008-12-17
9 days
9180
56819168
2008-12-31
2008-12-30
1 days
9181
56819168
2009-01-15
2009-01-13
2 days
9182
56819168
2009-01-14
2009-01-13
1 days
9183
56819168
2009-03-17
2009-03-16
1 days
9184
56819168
2009-03-20
2009-03-18
2 days
9185
56819168
2009-04-07
2009-04-07
0 days
9186
56819168
2009-04-22
2009-04-22
0 days
9187
56819168
2009-04-23
2009-04-23
0 days
9188
56819168
2009-05-21
2009-05-21
0 days
9189
56819168
2009-05-26
2009-05-21
5 days
9190
56819168
2009-06-02
2009-05-29
4 days
9191
56819168
2009-06-10
2009-06-10
0 days
9192
56819168
2009-06-23
2009-06-23
0 days
9193
56819168
2009-06-30
2009-06-26
4 days
9194
56819168
2009-07-02
2009-07-01
1 days
9195
56819168
2009-08-20
2009-08-19
1 days
9196
56819168
2009-08-24
2009-08-24
0 days
9197
56819168
2009-09-16
2009-09-15
1 days
9198
56819168
2009-09-16
2009-09-16
0 days
9199
56819168
2009-09-22
2009-09-21
1 days
9200
56819168
2009-09-25
2009-09-24
1 days
9201
56819168
2009-09-29
2009-09-29
0 days
9202
56819168
2009-09-30
2009-09-30
0 days
9203
56819168
2009-10-07
2009-10-02
5 days
9204
56819168
2009-10-09
2009-10-09
0 days
9205
56819168
2009-10-15
2009-10-13
2 days
9206
56819168
2009-10-15
2009-10-14
1 days
9207
56819168
2009-10-16
2009-10-15
1 days
9208
56819168
2009-10-19
2009-10-16
3 days
9209
56819168
2009-10-20
2009-10-19
1 days
9210
56819168
2009-10-20
2009-10-20
0 days
9211
56819168
2009-10-29
2009-10-26
3 days
9212
56819168
2009-10-27
2009-10-27
0 days
9213
56819168
2009-10-28
2009-10-28
0 days
9214
56819168
2009-11-04
2009-11-04
0 days
9215
56819168
2009-11-10
2009-11-10
0 days
9216
56819168
2009-11-19
2009-11-17
2 days
9217
56819168
2009-11-19
2009-11-13
6 days
9218
56819168
2009-12-02
2009-12-01
1 days
9219
56819168
2009-12-03
2009-11-18
15 days
9220
56819168
2009-12-03
2009-11-24
9 days
9221
56819168
2009-12-07
2009-12-07
0 days
9222
56819168
2009-12-08
2009-11-20
18 days
9223
56819168
2009-12-08
2009-12-08
0 days
9224
56819168
2009-12-11
2009-12-09
2 days
9225
56819168
2009-12-10
2009-12-01
9 days
9226
56819168
2009-12-11
2009-12-11
0 days
9227
56819168
2009-12-23
2009-12-23
0 days
9228
56819168
2009-12-30
2009-12-03
27 days
9229
56819168
2009-12-31
2009-12-10
21 days
9230
56819168
2009-12-31
2009-12-31
0 days
9231
56819168
2009-12-31
2009-12-15
16 days
9232
56819168
2010-01-06
2010-01-06
0 days
9233
56819168
2010-01-07
2010-01-07
0 days
9234
56819168
2010-01-13
2010-01-12
1 days
9235
56819168
2010-01-19
2010-01-19
0 days
9236
56819168
2010-01-26
2010-01-26
0 days
9237
56819168
2010-02-04
2010-02-01
3 days
9238
56819168
2010-02-11
2010-01-27
15 days
9239
56819168
2010-02-11
2010-02-11
0 days
9240
56819168
2010-02-16
2010-02-16
0 days
9241
56819168
2010-02-18
2010-02-18
0 days
9242
56819168
2010-02-25
2010-02-24
1 days
9243
56819168
2010-02-25
2010-02-25
0 days
9244
56819168
2010-03-04
2010-03-04
0 days
9245
56819168
2010-03-05
2010-03-05
0 days
9246
56819168
2010-03-08
2010-03-08
0 days
9247
56819168
2010-03-10
2010-03-10
0 days
9248
56819168
2010-03-15
2010-03-12
3 days
9249
56819168
2010-03-15
2010-03-15
0 days
9250
56819168
2010-03-22
2010-03-22
0 days
9251
56819168
2010-03-29
2010-03-29
0 days
9252
56819168
2010-03-30
2010-03-30
0 days
9253
56819168
2010-03-31
2010-03-31
0 days
9254
56819168
2010-04-05
2010-04-05
0 days
9255
56819168
2010-04-12
2010-04-12
0 days
9256
56819168
2010-04-27
2010-04-27
0 days
9257
56819168
2010-05-04
2010-05-04
0 days
9258
56819168
2010-05-11
2010-05-11
0 days
9259
56819168
2010-05-12
2010-05-12
0 days
9260
56819168
2010-05-18
2010-05-18
0 days
9261
56819168
2010-05-21
2010-05-20
1 days
9262
56819168
2010-05-24
2010-05-24
0 days
9263
56819168
2010-05-24
2010-05-24
0 days
9264
56819168
2010-05-25
2010-05-25
0 days
9265
56819168
2010-05-28
2010-05-27
1 days
9266
56819168
2010-05-28
2010-05-27
1 days
9267
56819168
2010-06-01
2010-05-27
5 days
9268
56819168
2010-06-03
2010-06-01
2 days
9269
56819168
2010-06-04
2010-06-03
1 days
9270
56819168
2010-06-08
2010-06-08
0 days
9271
56819168
2010-06-08
2010-06-07
1 days
9272
56819168
2010-06-10
2010-06-10
0 days
9273
56819168
2010-06-10
2010-06-09
1 days
9274
56819168
2010-06-16
2010-06-15
1 days
9275
56819168
2010-06-16
2010-06-16
0 days
9276
56819168
2010-06-16
2010-06-16
0 days
9277
56819168
2010-06-18
2010-06-17
1 days
9278
56819168
2010-06-25
2010-06-25
0 days
9279
56819168
2010-06-28
2010-06-28
0 days
9280
56819168
2010-06-29
2010-05-17
43 days
9281
56819168
2010-06-29
2010-06-29
0 days
9282
56819168
2010-07-06
2010-07-06
0 days
9283
56819168
2010-07-07
2010-07-07
0 days
9284
56819168
2010-07-09
2010-07-08
1 days
9285
56819168
2010-07-08
2010-07-08
0 days
9286
56819168
2010-07-09
2010-07-09
0 days
9287
56819168
2010-07-13
2010-07-13
0 days
9288
56819168
2010-07-13
2010-07-13
0 days
9289
56819168
2010-07-14
2010-07-12
2 days
9290
56819168
2010-07-15
2010-07-14
1 days
9291
56819168
2010-07-16
2010-07-15
1 days
9292
56819168
2010-07-16
2010-07-16
0 days
9293
56819168
2010-07-17
2010-07-17
0 days
9294
56819168
2010-07-20
2010-07-19
1 days
9295
56819168
2010-07-20
2010-07-20
0 days
9296
56819168
2010-07-22
2010-07-21
1 days
9297
56819168
2010-07-22
2010-07-21
1 days
9298
56819168
2010-07-23
2010-07-23
0 days
9299
56819168
2010-08-03
2010-08-02
1 days
9300
56819168
2010-08-03
2010-08-03
0 days
9301
56819168
2010-08-06
2010-08-05
1 days
9302
56819168
2010-08-10
2010-08-10
0 days
9304
56819168
2010-08-25
2010-08-25
0 days
9305
56819168
2010-09-02
2010-09-02
0 days
9306
56819168
2010-09-07
2010-09-03
4 days
9307
56819168
2010-09-13
2010-09-08
5 days
9308
56819168
2010-09-17
2010-09-14
3 days
9309
56819168
2010-09-14
2010-09-10
4 days
9310
56819168
2010-09-17
2010-09-16
1 days
9311
56819168
2010-09-16
2010-09-16
0 days
9312
56819168
2010-09-21
2010-09-15
6 days
9313
56819168
2010-09-21
2010-09-21
0 days
9314
56819168
2010-09-27
2010-09-24
3 days
9315
56819168
2010-09-27
2010-09-24
3 days
9316
56819168
2010-09-27
2010-09-24
3 days
9317
56819168
2010-09-27
2010-09-22
5 days
9318
56819168
2010-09-28
2010-09-28
0 days
9319
56819168
2010-09-28
2010-09-17
11 days
9320
56819168
2010-10-05
2010-10-01
4 days
9321
56819168
2010-10-04
2010-10-04
0 days
9322
56819168
2010-10-04
2010-09-24
10 days
9323
56819168
2010-10-05
2010-09-29
6 days
9324
56819168
2010-10-13
2010-10-13
0 days
9325
56819168
2010-10-13
2010-10-01
12 days
9326
56819168
2010-10-21
2010-10-21
0 days
9327
56819168
2010-10-22
2010-10-22
0 days
9328
56819168
2010-10-28
2010-10-28
0 days
9329
56819168
2010-10-28
2010-10-28
0 days
9330
56819168
2010-11-01
2010-11-01
0 days
9331
56819168
2010-11-08
2010-11-08
0 days
9332
56819168
2010-11-15
2010-11-15
0 days
9333
56819168
2010-11-22
2010-11-22
0 days
9334
56819168
2010-11-29
2010-11-29
0 days
9335
56819168
2010-12-06
2010-12-06
0 days
9336
56819168
2010-12-07
2010-12-03
4 days
9337
56819168
2010-12-14
2010-12-10
4 days
9338
56819168
2010-12-10
2010-12-09
1 days
9339
56819168
2010-12-15
2010-12-15
0 days
9340
56819168
2010-12-17
2010-12-16
1 days
9341
56819168
2010-12-21
2010-12-20
1 days
9342
56819168
2010-12-23
2010-12-23
0 days
9343
56819168
2010-12-23
2010-12-15
8 days
9344
56819168
2010-12-30
2010-12-23
7 days
9345
56819168
2011-01-03
2011-01-03
0 days
9346
56819168
2011-01-04
2010-12-21
14 days
9347
56819168
2011-01-04
2011-01-03
1 days
9348
56819168
2011-01-06
2011-01-06
0 days
9349
56819168
2011-01-07
2011-01-07
0 days
9350
56819168
2011-01-10
2011-01-10
0 days
9351
56819168
2011-01-12
2011-01-11
1 days
9352
56819168
2011-01-14
2011-01-14
0 days
9353
56819168
2011-01-20
2011-01-19
1 days
9354
56819168
2011-01-21
2011-01-21
0 days
9355
56819168
2011-01-26
2011-01-26
0 days
9356
56819168
2011-01-31
2011-01-31
0 days
9357
56819168
2011-02-03
2011-02-03
0 days
9358
56819168
2011-02-07
2011-02-07
0 days
9359
56819168
2011-02-11
2011-02-11
0 days
9360
56819168
2011-02-11
2011-02-11
0 days
9361
56819168
2011-02-25
2011-02-23
2 days
9362
56819168
2011-03-09
2011-03-09
0 days
9363
56819168
2011-03-14
2011-03-14
0 days
9364
56819168
2011-03-16
2011-03-16
0 days
9365
56819168
2011-03-31
2011-03-31
0 days
9366
56819168
2011-04-01
2011-04-01
0 days
9367
56819168
2011-04-07
2011-04-07
0 days
9368
56819168
2011-04-11
2011-04-11
0 days
9369
56819168
2011-04-18
2011-04-18
0 days
9370
56819168
2011-04-20
2011-04-20
0 days
9371
56819168
2011-04-28
2011-04-28
0 days
9372
56819168
2011-05-02
2011-05-02
0 days
9374
56819168
2011-05-11
2011-05-11
0 days
9375
56819168
2011-05-16
2011-05-16
0 days
9376
56819168
2011-05-17
2011-05-17
0 days
9377
56819168
2011-05-19
2011-05-18
1 days
9378
56819168
2011-05-20
2011-05-20
0 days
9379
56819168
2011-05-24
2011-05-24
0 days
9380
56819168
2011-05-31
2011-05-31
0 days
9381
56819168
2011-06-02
2011-06-02
0 days
9382
56819168
2011-06-06
2011-06-06
0 days
9383
56819168
2011-06-07
2011-06-07
0 days
9384
56819168
2011-06-09
2011-06-09
0 days
9385
56819168
2011-06-17
2011-06-13
4 days
9386
56819168
2011-06-27
2011-06-27
0 days
9387
56819168
2011-06-30
2011-06-30
0 days
9388
56819168
2011-07-08
2011-07-07
1 days
9389
56819168
2011-07-11
2011-07-11
0 days
9390
56819168
2011-07-13
2011-07-13
0 days
9391
56819168
2011-07-18
2011-07-18
0 days
9392
56819168
2011-07-21
2011-07-21
0 days
9393
56819168
2011-07-21
2011-07-20
1 days
9394
56819168
2011-07-25
2011-07-25
0 days
9395
56819168
2011-07-26
2011-07-25
1 days
9396
56819168
2011-07-26
2011-07-26
0 days
9397
56819168
2011-08-01
2011-08-01
0 days
9398
56819168
2011-08-02
2011-08-02
0 days
9399
56819168
2011-08-02
2011-08-02
0 days
9400
56819168
2011-08-08
2011-08-08
0 days
9401
56819168
2011-08-11
2011-08-09
2 days
9402
56819168
2011-08-16
2011-08-12
4 days
9403
56819168
2011-08-15
2011-08-15
0 days
9404
56819168
2011-08-17
2011-08-17
0 days
9405
56819168
2011-08-19
2011-08-18
1 days
9406
56819168
2011-08-25
2011-08-23
2 days
9407
56819168
2011-08-24
2011-08-24
0 days
9408
56819168
2011-08-30
2011-08-30
0 days
9409
56819168
2011-09-01
2011-08-31
1 days
9410
56819168
2011-09-01
2011-09-01
0 days
9411
56819168
2011-09-06
2011-09-06
0 days
9412
56819168
2011-09-12
2011-09-12
0 days
9413
56819168
2011-09-16
2011-09-16
0 days
9414
56819168
2011-09-19
2011-09-19
0 days
9415
56819168
2011-09-26
2011-09-22
4 days
9416
56819168
2011-09-28
2011-09-28
0 days
9417
56819168
2011-09-30
2011-09-30
0 days
9418
56819168
2011-10-05
2011-10-03
2 days
9420
56819168
2011-10-06
2011-10-04
2 days
9421
56819168
2011-10-05
2011-10-05
0 days
9422
56819168
2011-10-12
2011-10-12
0 days
9423
56819168
2011-10-14
2011-10-14
0 days
9424
56819168
2011-10-18
2011-10-18
0 days
9425
56819168
2011-10-19
2011-10-19
0 days
9426
56819168
2011-10-19
2011-10-19
0 days
9427
56819168
2011-10-21
2011-10-21
0 days
9428
56819168
2011-10-26
2011-10-26
0 days
9429
56819168
2011-10-28
2011-10-28
0 days
9430
56819168
2011-10-31
2011-10-31
0 days
9431
56819168
2011-11-01
2011-11-01
0 days
9432
56819168
2011-11-02
2011-11-02
0 days
9433
56819168
2011-11-09
2011-11-09
0 days
9434
56819168
2011-11-17
2011-11-16
1 days
9435
56819168
2011-11-16
2011-11-15
1 days
9436
56819168
2011-11-21
2011-11-21
0 days
9437
56819168
2011-11-29
2011-11-23
6 days
9438
56819168
2011-11-25
2011-11-25
0 days
9439
56819168
2011-11-30
2011-11-30
0 days
9440
56819168
2011-11-30
2011-11-30
0 days
9441
56819168
2011-12-02
2011-12-01
1 days
9442
56819168
2011-12-05
2011-12-05
0 days
9443
56819168
2011-12-06
2011-12-06
0 days
9444
56819168
2011-12-14
2011-12-14
0 days
9445
56819168
2011-12-21
2011-12-21
0 days
9446
56819168
2011-12-27
2011-12-22
5 days
9447
56819168
2011-12-28
2011-12-28
0 days
9448
56819168
2012-01-04
2012-01-04
0 days
9449
56819168
2012-01-19
2012-01-13
6 days
9450
56819168
2012-01-18
2012-01-18
0 days
9451
56819168
2012-01-19
2012-01-19
0 days
9452
56819168
2012-01-25
2012-01-25
0 days
9453
56819168
2012-02-06
2012-01-30
7 days
9454
56819168
2012-02-01
2012-02-01
0 days
9455
56819168
2012-02-08
2012-02-08
0 days
9456
56819168
2012-02-09
2012-02-02
7 days
9457
56819168
2012-02-13
2012-02-07
6 days
9458
56819168
2012-02-15
2012-02-14
1 days
9459
56819168
2012-02-16
2012-02-09
7 days
9460
56819168
2012-02-17
2012-02-17
0 days
9461
56819168
2012-02-22
2012-02-22
0 days
9462
56819168
2012-02-29
2012-02-29
0 days
9463
56819168
2012-03-01
2012-03-01
0 days
9464
56819168
2012-03-01
2012-01-31
30 days
9465
56819168
2012-03-02
2012-02-21
10 days
9466
56819168
2012-03-05
2012-03-05
0 days
9467
56819168
2012-03-06
2012-02-23
12 days
9468
56819168
2012-03-07
2012-03-07
0 days
9469
56819168
2012-03-08
2012-02-28
9 days
9470
56819168
2012-03-08
2012-03-01
7 days
9471
56819168
2012-03-21
2012-03-20
1 days
9472
56819168
2012-03-21
2012-03-21
0 days
9473
56819168
2012-03-28
2012-03-28
0 days
9474
56819168
2012-04-03
2012-03-29
5 days
9475
56819168
2012-04-03
2012-04-02
1 days
9476
56819168
2012-04-04
2012-04-04
0 days
9477
56819168
2012-04-06
2012-04-06
0 days
9478
56819168
2012-04-11
2012-04-11
0 days
9479
56819168
2012-04-13
2012-04-13
0 days
9480
56819168
2012-04-19
2012-04-19
0 days
9481
56819168
2012-04-24
2012-04-24
0 days
9482
56819168
2012-05-01
2012-05-01
0 days
9483
56819168
2012-05-02
2012-05-02
0 days
9484
56819168
2012-05-09
2012-05-07
2 days
9485
56819168
2012-05-08
2012-05-08
0 days
9486
56819168
2012-05-11
2012-05-07
4 days
9487
56819168
2012-05-15
2012-05-15
0 days
9488
56819168
2012-05-15
2012-05-15
0 days
9489
56819168
2012-05-24
2012-05-24
0 days
9490
56819168
2012-05-25
2012-05-25
0 days
9491
56819168
2012-05-25
2012-05-18
7 days
9492
56819168
2012-05-29
2012-05-29
0 days
9493
56819168
2012-05-30
2012-05-16
14 days
9494
56819168
2012-05-30
2012-05-22
8 days
9495
56819168
2012-06-04
2012-05-29
6 days
9496
56819168
2012-06-04
2012-06-04
0 days
9497
56819168
2012-06-05
2012-06-05
0 days
9498
56819168
2012-06-08
2012-06-06
2 days
9499
56819168
2012-06-07
2012-06-07
0 days
9500
56819168
2012-06-08
2012-06-05
3 days
9501
56819168
2012-06-11
2012-06-11
0 days
9502
56819168
2012-06-12
2012-06-12
0 days
9503
56819168
2012-06-15
2012-06-13
2 days
9504
56819168
2012-06-19
2012-06-19
0 days
9505
56819168
2012-06-19
2012-06-12
7 days
9506
56819168
2012-06-26
2012-06-21
5 days
9507
56819168
2012-06-22
2012-06-21
1 days
9508
56819168
2012-06-21
2012-06-21
0 days
9509
56819168
2012-06-27
2012-06-21
6 days
9510
56819168
2012-06-25
2012-06-25
0 days
9511
56819168
2012-06-25
2012-06-19
6 days
9512
56819168
2012-06-26
2012-06-26
0 days
9513
56819168
2012-06-29
2012-06-27
2 days
9514
56819168
2012-07-05
2012-07-02
3 days
9515
56819168
2012-07-10
2012-07-06
4 days
9516
56819168
2012-07-10
2012-07-10
0 days
9517
56819168
2012-07-10
2012-07-09
1 days
9518
56819168
2012-07-12
2012-07-11
1 days
9519
56819168
2012-07-20
2012-07-13
7 days
9520
56819168
2012-07-20
2012-07-16
4 days
9521
56819168
2012-07-17
2012-07-17
0 days
9522
56819168
2012-07-18
2012-07-16
2 days
9523
56819168
2012-07-26
2012-07-20
6 days
9524
56819168
2012-07-20
2012-07-18
2 days
9525
56819168
2012-07-24
2012-07-24
0 days
9526
56819168
2012-07-30
2012-07-30
0 days
9527
56819168
2012-07-30
2012-07-30
0 days
9528
56819168
2012-07-31
2012-07-31
0 days
9529
56819168
2012-07-31
2012-07-31
0 days
9530
56819168
2012-08-07
2012-08-07
0 days
9531
56819168
2012-08-10
2012-08-08
2 days
9532
56819168
2012-08-13
2012-08-13
0 days
9533
56819168
2012-08-14
2012-08-14
0 days
9534
56819168
2012-08-15
2012-08-14
1 days
9535
56819168
2012-08-17
2012-08-17
0 days
9536
56819168
2012-08-21
2012-08-21
0 days
9537
56819168
2012-08-21
2012-07-27
25 days
9538
56819168
2012-08-28
2012-08-28
0 days
9539
56819168
2012-09-10
2012-09-10
0 days
9540
56819168
2012-09-17
2012-09-17
0 days
9541
56819168
2012-09-21
2012-09-21
0 days
9542
56819168
2012-09-27
2012-09-24
3 days
9543
56819168
2012-10-01
2012-10-01
0 days
9544
56819168
2012-10-04
2012-10-03
1 days
9545
56819168
2012-10-11
2012-10-11
0 days
9546
56819168
2012-10-12
2012-10-12
0 days
9547
56819168
2012-10-17
2012-10-17
0 days
9548
56819168
2012-10-23
2012-10-23
0 days
9549
56819168
2012-10-31
2012-10-26
5 days
9551
56819168
2012-11-14
2012-11-14
0 days
9552
56819168
2012-11-23
2012-11-15
8 days
9554
56819168
2012-11-20
2012-11-19
1 days
9555
56819168
2012-11-21
2012-11-21
0 days
9556
56819168
2012-11-30
2012-11-29
1 days
9557
56819168
2012-12-04
2012-12-04
0 days
9558
56819168
2012-12-04
2012-12-03
1 days
9559
56819168
2012-12-06
2012-12-06
0 days
9560
56819168
2012-12-10
2012-12-06
4 days
9561
56819168
2012-12-11
2012-12-10
1 days
9562
56819168
2012-12-11
2012-12-11
0 days
9563
56819168
2012-12-11
2012-12-11
0 days
9564
56819168
2012-12-13
2012-12-13
0 days
9565
56819168
2012-12-14
2012-12-13
1 days
9566
56819168
2012-12-17
2012-12-17
0 days
9567
56819168
2012-12-20
2012-12-19
1 days
9568
56819168
2012-12-27
2012-12-24
3 days
9569
56819168
2012-12-27
2012-12-26
1 days
9570
56819168
2012-12-31
2012-12-31
0 days
9571
56819168
2013-01-04
2013-01-04
0 days
9572
56819168
2013-01-09
2013-01-09
0 days
9573
56819168
2013-01-18
2013-01-18
0 days
9574
56819168
2013-01-23
2013-01-22
1 days
9575
56819168
2013-02-05
2013-02-01
4 days
9576
56819168
2013-02-06
2013-02-06
0 days
9577
56819168
2013-02-11
2013-02-07
4 days
9578
56819168
2013-02-12
2013-02-11
1 days
9579
56819168
2013-02-20
2013-02-20
0 days
9580
56819168
2013-02-26
2013-02-21
5 days
9581
56819168
2013-02-27
2013-02-25
2 days
9582
56819168
2013-02-28
2013-02-13
15 days
9583
56819168
2013-02-28
2013-02-27
1 days
9584
56819168
2013-03-01
2013-02-20
9 days
9585
56819168
2013-03-05
2013-03-04
1 days
9586
56819168
2013-03-28
2013-03-25
3 days
9587
56819168
2013-03-26
2013-03-25
1 days
9588
56819168
2013-04-03
2013-04-03
0 days
9589
56819168
2013-04-04
2013-04-04
0 days
9590
56819168
2013-04-26
2013-04-22
4 days
9591
56819168
2013-04-23
2013-04-22
1 days
9592
56819168
2013-05-01
2013-05-01
0 days
9593
56819168
2013-05-06
2013-02-15
80 days
9594
56819168
2013-05-13
2013-05-13
0 days
9595
56819168
2013-05-15
2013-05-15
0 days
9596
56819168
2013-05-20
2013-05-20
0 days
9597
56819168
2013-05-21
2013-05-21
0 days
9598
56819168
2013-05-28
2013-05-23
5 days
9599
56819168
2013-05-29
2013-05-29
0 days
9600
56819168
2013-06-06
2013-06-04
2 days
9601
56819168
2013-06-21
2013-06-20
1 days
9602
56819168
2013-06-25
2013-06-24
1 days
9603
56819168
2013-06-27
2013-06-27
0 days
9604
56819168
2013-07-19
2013-07-15
4 days
9605
56819168
2013-07-19
2013-07-18
1 days
9606
56819168
2013-07-22
2013-07-22
0 days
9607
56819168
2013-07-23
2013-07-23
0 days
9608
56819168
2013-07-25
2013-07-25
0 days
9609
56819168
2013-07-30
2013-07-30
0 days
9610
56819168
2013-08-01
2013-07-31
1 days
9611
56819168
2013-08-02
2013-08-01
1 days
9612
56819168
2013-08-12
2013-08-12
0 days
9613
56819168
2013-08-15
2013-08-15
0 days
9614
56819168
2013-09-25
2013-09-25
0 days
9615
56819168
2013-10-16
2013-10-09
7 days
9616
56819168
2013-10-23
2013-10-21
2 days
9617
56819168
2013-10-22
2013-10-22
0 days
9618
56819168
2013-10-23
2013-10-23
0 days
9619
56819168
2013-11-01
2013-11-01
0 days
9620
56819168
2013-11-04
2013-11-01
3 days
9621
56819168
2014-01-03
2014-01-02
1 days
9622
56819168
2014-01-06
2013-12-31
6 days
9623
56819168
2014-01-08
2014-01-07
1 days
9624
56819168
2014-01-15
2014-01-14
1 days
9625
56819168
2014-01-21
2014-01-15
6 days
9626
56819168
2014-01-22
2014-01-21
1 days
9627
56819168
2014-01-22
2014-01-16
6 days
9628
56819168
2014-01-24
2014-01-23
1 days
9629
56819168
2014-01-27
2014-01-27
0 days
9630
56819168
2014-01-29
2014-01-28
1 days
9631
56819168
2014-01-29
2014-01-28
1 days
9632
56819168
2014-02-08
2014-02-06
2 days
9633
56819168
2014-02-24
2014-02-21
3 days
9634
56819168
2014-03-12
2014-03-12
0 days
9635
56819168
2014-04-03
2014-04-03
0 days
9636
56819168
2014-04-24
2014-04-23
1 days
9637
56819168
2014-04-30
2014-04-29
1 days
9638
56819168
2014-05-21
2014-05-21
0 days
9639
56819168
2014-05-28
2014-05-28
0 days
9640
56819168
2014-06-12
2014-06-10
2 days
9641
56819168
2014-06-11
2014-06-11
0 days
9642
56819168
2014-06-23
2014-06-20
3 days
9643
56819168
2014-06-30
2014-06-30
0 days
9644
56819168
2014-07-07
2014-07-07
0 days
9645
56819168
2014-07-21
2014-07-21
0 days
9646
56819168
2014-07-30
2014-07-29
1 days
9647
56819168
2014-08-13
2014-08-13
0 days
9648
56819168
2014-08-15
2014-08-15
0 days
9649
56819168
2014-08-25
2014-08-25
0 days
9650
56819168
2014-09-09
2014-09-09
0 days
9651
56819168
2014-09-24
2014-09-24
0 days
9652
56819168
2014-09-29
2014-09-29
0 days
9653
56819168
2014-10-03
2014-10-03
0 days
9654
56819168
2014-10-14
2014-10-14
0 days
9655
56819168
2014-11-06
2014-11-05
1 days
9656
56819168
2014-11-10
2014-11-07
3 days
9657
56819168
2014-11-14
2014-11-14
0 days
9658
56819168
2014-11-14
2014-11-13
1 days
9659
56819168
2014-11-18
2014-11-18
0 days
9660
56819168
2014-11-20
2014-11-19
1 days
9661
56819168
2014-11-25
2014-11-25
0 days
9662
56819168
2014-12-01
2014-12-01
0 days
9663
56819168
2014-12-23
2014-12-23
0 days
9664
56819168
2015-01-02
2015-01-02
0 days
9665
56819168
2015-01-07
2015-01-07
0 days
9666
56819168
2015-01-20
2015-01-16
4 days
9667
56819168
2015-01-20
2015-01-20
0 days
9668
56819168
2015-02-03
2015-02-03
0 days
9669
56819168
2015-02-23
2015-02-22
1 days
9670
56819168
2015-02-27
2015-02-26
1 days
9671
56819168
2015-02-26
2015-02-26
0 days
9672
56819168
2015-03-18
2015-03-18
0 days
9673
56819168
2015-08-03
2015-07-31
3 days
9674
56819168
2015-08-25
2015-08-24
1 days
9675
56819168
2015-10-15
2015-10-14
1 days
9676
56819168
2015-10-21
2015-10-21
0 days
9677
56819168
2015-10-23
2015-10-22
1 days
9678
56819168
2015-10-29
2015-10-29
0 days
9679
57565760
2007-04-24
2007-04-11
13 days
9680
57565760
2007-05-17
2007-05-15
2 days
9681
57565760
2010-01-05
2010-01-05
0 days
9682
57565760
2010-04-07
2010-04-06
1 days
9683
57565760
2010-07-04
2010-07-04
0 days
9684
57565760
2010-07-04
2010-07-04
0 days
9685
57565760
2010-07-06
2010-07-05
1 days
9686
57565760
2010-07-10
2010-07-09
1 days
9687
57565760
2010-07-18
2010-07-18
0 days
9688
57565760
2010-07-21
2010-07-18
3 days
9689
57565760
2010-08-04
2010-08-04
0 days
9690
57565760
2010-08-16
2010-08-14
2 days
9691
57565760
2010-08-24
2010-08-23
1 days
9692
57565760
2010-08-26
2010-08-25
1 days
9693
57565760
2010-09-02
2010-09-02
0 days
9694
57565760
2010-09-03
2010-09-02
1 days
9695
57565760
2010-09-20
2010-09-19
1 days
9696
57565760
2010-09-21
2010-09-20
1 days
9697
57565760
2010-09-22
2010-09-21
1 days
9698
57565760
2010-09-27
2010-09-22
5 days
9699
57565760
2010-09-24
2010-09-23
1 days
9700
57565760
2010-09-28
2010-09-24
4 days
9701
57565760
2010-09-25
2010-09-24
1 days
9702
57565760
2010-09-27
2010-09-25
2 days
9703
57565760
2010-10-04
2010-10-03
1 days
9705
57565760
2010-10-08
2010-10-05
3 days
9706
57565760
2010-10-09
2010-10-08
1 days
9707
57565760
2010-10-24
2010-10-23
1 days
9708
57565760
2010-11-04
2010-11-03
1 days
9709
57565760
2010-11-06
2010-11-05
1 days
9710
57565760
2010-11-08
2010-11-07
1 days
9711
57565760
2010-11-12
2010-11-10
2 days
9712
57565760
2010-11-14
2010-11-13
1 days
9713
57565760
2010-11-19
2010-11-16
3 days
9714
57565760
2010-11-22
2010-11-22
0 days
9715
57565760
2010-11-24
2010-11-24
0 days
9716
57565760
2010-12-09
2010-12-05
4 days
9717
57565760
2010-12-14
2010-12-11
3 days
9718
57565760
2010-12-23
2010-12-22
1 days
9719
57565760
2011-01-14
2011-01-10
4 days
9720
57565760
2011-01-14
2011-01-11
3 days
9721
57565760
2011-01-14
2011-01-14
0 days
9722
57565760
2011-02-05
2011-02-05
0 days
9724
57565760
2011-02-08
2011-02-07
1 days
9725
57565760
2011-02-10
2011-02-09
1 days
9726
57565760
2011-02-10
2011-02-10
0 days
9727
57565760
2011-02-13
2011-02-11
2 days
9728
57565760
2011-02-11
2011-02-11
0 days
9729
57565760
2011-02-13
2011-02-13
0 days
9731
57565760
2011-02-16
2011-02-16
0 days
9732
57565760
2011-02-20
2011-02-16
4 days
9733
57565760
2011-03-03
2011-03-02
1 days
9734
57565760
2011-03-04
2011-03-03
1 days
9735
57565760
2011-03-05
2011-03-04
1 days
9736
57565760
2011-03-05
2011-03-05
0 days
9737
57565760
2011-03-06
2011-03-05
1 days
9738
57565760
2011-03-08
2011-03-05
3 days
9739
57565760
2011-03-09
2011-03-08
1 days
9740
57565760
2011-03-11
2011-03-09
2 days
9741
57565760
2011-03-11
2011-03-10
1 days
9742
57565760
2011-03-12
2011-03-11
1 days
9743
57565760
2011-03-13
2011-03-12
1 days
9744
57565760
2011-04-06
2011-04-06
0 days
9745
57565760
2011-05-20
2011-05-20
0 days
9746
57565760
2011-05-25
2011-05-24
1 days
9747
57565760
2011-07-11
2011-07-10
1 days
9748
57565760
2011-07-12
2011-07-11
1 days
9749
57565760
2011-07-20
2011-07-19
1 days
9750
57565760
2011-07-21
2011-07-20
1 days
9751
57565760
2011-08-05
2011-08-05
0 days
9752
57565760
2011-08-07
2011-08-06
1 days
9753
57565760
2011-08-14
2011-08-14
0 days
9754
57565760
2011-09-03
2011-09-02
1 days
9755
57565760
2011-09-04
2011-09-03
1 days
9756
57565760
2011-09-05
2011-09-05
0 days
9757
57565760
2011-10-03
2011-10-02
1 days
9758
57565760
2011-11-28
2011-11-27
1 days
9759
57565760
2011-12-02
2011-12-01
1 days
9760
57565760
2011-12-06
2011-12-05
1 days
9761
57565760
2012-03-11
2012-03-10
1 days
9762
57565760
2012-03-12
2012-03-12
0 days
9763
57565760
2012-03-15
2012-03-15
0 days
9764
57565760
2012-03-16
2012-03-16
0 days
9765
57565760
2012-03-18
2012-03-17
1 days
9766
57565760
2012-03-18
2012-03-18
0 days
9767
57565760
2012-03-24
2012-03-23
1 days
9769
57565760
2012-03-28
2012-03-27
1 days
9771
57565760
2012-03-28
2012-03-28
0 days
9773
57565760
2012-04-12
2012-04-11
1 days
9777
57565760
2012-04-17
2012-04-16
1 days
9778
57565760
2012-04-18
2012-04-18
0 days
9779
57565760
2012-04-20
2012-04-19
1 days
9780
57565760
2012-04-30
2012-04-29
1 days
9781
57565760
2012-05-08
2012-05-08
0 days
9782
57565760
2012-05-10
2012-05-10
0 days
9783
57565760
2012-05-11
2012-05-10
1 days
9784
57565760
2012-05-12
2012-05-12
0 days
9785
57565760
2012-05-15
2012-05-15
0 days
9786
57565760
2012-05-16
2012-05-16
0 days
9787
57565760
2012-05-17
2012-05-17
0 days
9789
57565760
2012-05-18
2012-05-18
0 days
9790
57565760
2012-05-20
2012-05-20
0 days
9792
57565760
2012-05-22
2012-05-21
1 days
9793
57565760
2012-06-19
2012-06-19
0 days
9794
57565760
2012-06-20
2012-06-20
0 days
9797
57565760
2012-06-23
2012-06-22
1 days
9798
57565760
2012-06-24
2012-06-23
1 days
9799
57565760
2012-06-26
2012-06-26
0 days
9800
57565760
2012-06-27
2012-06-26
1 days
9801
57565760
2012-06-28
2012-06-27
1 days
9802
57565760
2012-07-04
2012-07-03
1 days
9803
57565760
2012-07-07
2012-07-06
1 days
9804
57565760
2012-07-14
2012-07-14
0 days
9805
57565760
2012-07-22
2012-07-21
1 days
9806
57565760
2012-07-24
2012-07-24
0 days
9807
57565760
2012-07-25
2012-07-24
1 days
9808
57565760
2012-07-28
2012-07-27
1 days
9809
57565760
2012-07-31
2012-07-30
1 days
9810
57565760
2012-08-01
2012-07-31
1 days
9811
57565760
2012-08-03
2012-08-01
2 days
9812
57565760
2012-08-06
2012-08-05
1 days
9813
57565760
2012-08-09
2012-08-08
1 days
9814
57565760
2012-08-09
2012-08-09
0 days
9815
57565760
2012-08-12
2012-08-11
1 days
9816
57565760
2012-08-17
2012-08-17
0 days
9817
57565760
2012-08-18
2012-08-17
1 days
9818
57565760
2012-08-19
2012-08-18
1 days
9819
57565760
2012-08-24
2012-08-23
1 days
9820
57565760
2012-09-01
2012-08-31
1 days
9822
57565760
2012-09-06
2012-09-05
1 days
9823
57565760
2012-09-07
2012-09-06
1 days
9824
57565760
2012-09-09
2012-09-08
1 days
9825
57565760
2012-10-13
2012-10-12
1 days
9826
57565760
2012-10-13
2012-10-13
0 days
9827
57565760
2012-10-15
2012-10-14
1 days
9829
57565760
2012-10-22
2012-10-21
1 days
9830
57565760
2012-10-24
2012-10-23
1 days
9831
57565760
2012-10-25
2012-10-24
1 days
9832
57565760
2012-10-26
2012-10-26
0 days
9833
57565760
2012-11-06
2012-11-06
0 days
9834
57565760
2012-11-07
2012-11-07
0 days
9836
57565760
2012-11-09
2012-11-08
1 days
9837
57565760
2012-11-12
2012-11-12
0 days
9839
57565760
2012-11-14
2012-11-13
1 days
9840
57565760
2012-11-20
2012-11-19
1 days
9841
57565760
2013-01-04
2013-01-03
1 days
9842
57565760
2013-01-05
2013-01-04
1 days
9843
57565760
2013-01-05
2013-01-05
0 days
9844
57565760
2013-01-14
2013-01-14
0 days
9845
57565760
2013-03-07
2013-03-06
1 days
9846
57565760
2013-03-07
2013-03-07
0 days
9847
57565760
2013-03-10
2013-03-09
1 days
9848
57565760
2013-04-06
2013-04-06
0 days
9850
57565760
2013-04-11
2013-04-10
1 days
9851
57565760
2013-04-12
2013-04-11
1 days
9852
57565760
2013-04-13
2013-04-12
1 days
9853
57565760
2013-04-14
2013-04-14
0 days
9854
57565760
2013-04-16
2013-04-16
0 days
9855
57565760
2013-04-17
2013-04-16
1 days
9856
57565760
2013-04-21
2013-04-18
3 days
9857
57565760
2013-04-20
2013-04-19
1 days
9858
57565760
2013-04-21
2013-04-20
1 days
9859
57565760
2013-04-22
2013-04-21
1 days
9860
57565760
2013-04-24
2013-04-23
1 days
9862
57565760
2013-04-26
2013-04-25
1 days
9863
57565760
2013-05-01
2013-04-30
1 days
9864
57565760
2013-05-02
2013-05-01
1 days
9865
57565760
2013-05-03
2013-05-02
1 days
9866
57565760
2013-05-04
2013-05-04
0 days
9867
57565760
2013-05-05
2013-05-04
1 days
9869
57565760
2013-08-05
2013-08-04
1 days
9870
57565760
2013-10-28
2013-10-26
2 days
9871
57565760
2013-11-08
2013-11-08
0 days
9872
57565760
2013-11-29
2013-11-28
1 days
9873
57565760
2013-12-04
2013-12-03
1 days
9874
57565760
2013-12-14
2013-12-14
0 days
9875
57565760
2014-01-03
2014-01-02
1 days
9876
57565760
2014-02-07
2014-02-06
1 days
9877
57565760
2014-02-11
2014-02-10
1 days
9878
57565760
2014-02-21
2014-02-21
0 days
9879
57565760
2014-02-21
2014-02-21
0 days
9880
57565760
2014-02-28
2014-02-27
1 days
9881
57565760
2014-03-17
2014-03-17
0 days
9882
57565760
2014-03-17
2014-03-17
0 days
9883
57565760
2014-03-28
2014-03-28
0 days
9884
57565760
2014-04-11
2014-04-11
0 days
9885
57565760
2014-04-13
2014-04-12
1 days
9886
57565760
2014-04-14
2014-04-13
1 days
9887
57565760
2014-04-15
2014-04-14
1 days
9888
57565760
2014-05-24
2014-05-24
0 days
9889
57565760
2014-06-08
2014-06-08
0 days
9890
57565760
2014-06-18
2014-06-14
4 days
9892
57565760
2014-12-27
2014-12-27
0 days
9893
57565760
2015-01-04
2015-01-04
0 days
9894
57565760
2015-04-27
2015-04-27
0 days
9895
57565760
2015-06-11
2015-06-08
3 days
9896
57565760
2015-06-19
2015-06-18
1 days
9897
57565760
2015-06-23
2015-06-20
3 days
9898
57565760
2015-08-16
2015-08-15
1 days
9899
57565760
2015-08-28
2015-08-27
1 days
9900
57565760
2015-09-12
2015-09-12
0 days
9901
57565760
2015-09-26
2015-09-25
1 days
9902
57565760
2015-10-02
2015-10-01
1 days
9903
57565760
2015-10-07
2015-10-06
1 days
9904
57565760
2015-10-10
2015-10-09
1 days
9905
57565760
2015-10-13
2015-10-13
0 days
9906
57565760
2015-10-17
2015-10-17
0 days
9907
57565760
2015-10-30
2015-10-29
1 days
9908
57565760
2015-11-01
2015-10-31
1 days
9909
57631168
2004-01-20
2004-01-20
0 days
9910
57631168
2004-01-22
2004-01-13
9 days
9911
57631168
2004-02-05
2004-02-02
3 days
9912
57631168
2004-03-16
2004-03-15
1 days
9913
57631168
2004-03-19
2004-03-19
0 days
9914
57631168
2004-04-08
2004-04-06
2 days
9915
57631168
2004-04-06
2004-04-06
0 days
9916
57631168
2004-04-08
2004-04-06
2 days
9917
57631168
2004-04-15
2004-04-15
0 days
9918
57631168
2004-04-29
2004-04-22
7 days
9919
57631168
2004-05-14
2004-05-11
3 days
9920
57631168
2004-06-25
2004-05-21
35 days
9921
57631168
2004-08-13
2004-07-29
15 days
9922
57631168
2004-09-10
2004-08-25
16 days
9923
57631168
2004-09-15
2004-09-15
0 days
9924
57631168
2005-04-21
2005-04-21
0 days
9925
57631168
2005-04-27
2005-04-27
0 days
9926
57631168
2005-05-04
2005-05-04
0 days
9927
57631168
2005-12-29
2005-12-29
0 days
9928
57631168
2006-03-16
2006-03-15
1 days
9929
57631168
2006-03-24
2006-03-22
2 days
9930
57631168
2006-05-14
2006-05-13
1 days
9931
57631168
2006-06-19
2006-06-18
1 days
9932
57631168
2006-06-22
2006-06-21
1 days
9933
57631168
2006-07-22
2006-07-22
0 days
9934
57631168
2006-07-29
2006-07-28
1 days
9935
57631168
2006-08-01
2006-07-31
1 days
9936
57631168
2006-12-28
2006-12-15
13 days
9937
57631168
2007-01-09
2007-01-05
4 days
9938
57631168
2007-03-01
2007-02-27
2 days
9939
57631168
2007-03-01
2007-02-26
3 days
9940
57631168
2009-10-29
2009-10-23
6 days
9941
57631168
2009-10-29
2009-10-28
1 days
9942
57631168
2009-12-09
2009-11-24
15 days
9943
57631168
2009-12-09
2009-12-09
0 days
9944
57631168
2010-01-20
2010-01-20
0 days
9945
57631168
2011-03-04
2011-03-04
0 days
9946
57631168
2011-10-06
2011-09-20
16 days
9947
57631168
2013-08-24
2013-08-24
0 days
9948
57631168
2013-08-26
2013-08-24
2 days
9950
57631168
2013-09-20
2013-09-19
1 days
9951
57631168
2013-12-13
2013-12-11
2 days
9952
57631168
2013-12-20
2013-12-12
8 days
9953
57631168
2014-01-14
2014-01-09
5 days
9954
57631168
2014-01-21
2014-01-11
10 days
9955
57631168
2014-09-09
2014-09-03
6 days
9956
57631168
2014-12-01
2014-11-21
10 days
9957
57631168
2015-07-09
2015-07-06
3 days
9958
57631168
2015-07-09
2015-07-06
3 days
9959
57631168
2015-07-10
2015-07-07
3 days
9960
57631168
2015-07-28
2015-07-28
0 days
9961
57631168
2015-07-28
2015-07-28
0 days
9963
57631168
2015-08-03
2015-08-03
0 days
9964
57631168
2015-08-26
2015-08-03
23 days
9965
57631168
2015-09-07
2015-09-07
0 days
9966
57708896
2007-08-09
2007-08-09
0 days
9967
57708896
2012-02-19
2012-02-19
0 days
9968
57708896
2012-11-03
2012-11-02
1 days
9969
57708896
2012-11-03
2012-11-03
0 days
9970
57708896
2012-11-06
2012-11-06
0 days
9971
57708896
2012-11-14
2012-11-14
0 days
9972
57708896
2012-11-16
2012-11-16
0 days
9973
57708896
2013-10-04
2013-10-03
1 days
9974
57708896
2014-10-07
2014-10-07
0 days
9975
57708896
2015-04-04
2015-04-04
0 days
9976
57708896
2015-04-04
2015-04-04
0 days
9977
57708896
2015-09-16
2015-09-15
1 days
9978
57708896
2015-10-16
2015-10-16
0 days
9979
60754176
2004-07-21
2004-07-20
1 days
9980
60754176
2014-06-20
2014-06-20
0 days
9981
60754176
2014-08-14
2014-08-12
2 days
9982
60754176
2014-09-16
2014-09-16
0 days
9983
60754176
2014-10-14
2014-10-10
4 days
9984
60754176
2015-01-12
2015-01-11
1 days
9985
60754176
2015-02-08
2015-02-03
5 days
9986
60754176
2015-03-25
2015-03-24
1 days
9987
60754176
2015-07-07
2015-06-27
10 days
9988
60754176
2015-08-12
2015-08-08
4 days
9989
60754176
2015-08-21
2015-08-19
2 days
9990
60754176
2015-09-20
2015-09-19
1 days
9992
62105792
2004-04-15
2004-04-11
4 days
9993
62105792
2014-10-04
2014-10-03
1 days
9994
62105792
2015-09-29
2015-09-28
1 days
9995
62697600
2008-10-26
2008-10-25
1 days
9996
62697600
2008-11-07
2008-10-28
10 days
9997
62697600
2008-10-30
2008-10-25
5 days
9998
62697600
2012-01-14
2012-01-14
0 days
9999
62697600
2012-01-18
2012-01-14
4 days
9792 rows × 4 columns
In [62]:


df2.groupby(['ID']).groups.keys()


Out[62]:
dict_keys([819392, 2205504, 3136000, 3435936, 3847424, 3950912, 7193088, 9075808, 9774688, 10087392, 10709216, 12134304, 12137440, 12613664, 12728352, 13288576, 15095136, 15150240, 15576288, 16293312, 17997504, 18739168, 18837952, 22300992, 23665824, 24810016, 26523392, 27421632, 28438144, 28653184, 28990976, 29779232, 30041536, 30172352, 33221440, 34207040, 34603968, 34861120, 35001792, 35764288, 37026752, 37209088, 37790816, 38058496, 38659264, 39541824, 40160736, 40674368, 40905536, 42485632, 44109184, 46070080, 47097792, 47221440, 47483072, 48319264, 50413440, 50986208, 51996896, 52263456, 52898720, 53420640, 54848192, 55047104, 55738144, 56819168, 57565760, 57631168, 57708896, 60754176, 62105792, 62697600, 63040320, 63383264, 63941248, 64050112, 64927520, 65186240, 66174752, 66704736])

Total number of days spent in the hospital by grouped by unique patients:
Next we calculate the total number of days by patient Ids as specified in the problem statement.
In [65]:


totalDays = df2.groupby('ID')['No. Of Days in Hospital'].sum()
print(totalDays)



ID
819392        50 days
2205504      552 days
3136000        5 days
3435936      650 days
3847424      154 days
3950912      324 days
7193088      120 days
9075808      184 days
9774688      113 days
10087392      88 days
10709216     184 days
12134304     290 days
12137440    1898 days
12613664      76 days
12728352       8 days
13288576      10 days
15095136     271 days
15150240     142 days
15576288      27 days
16293312     214 days
17997504     370 days
18739168     111 days
18837952      93 days
22300992     193 days
23665824      89 days
24810016       1 days
26523392      36 days
27421632    3568 days
28438144     296 days
28653184     201 days
28990976     145 days
29779232     258 days
30041536     365 days
30172352      46 days
33221440     564 days
34207040     231 days
34603968     374 days
34861120     136 days
35001792     837 days
35764288     656 days
37026752   19372 days
37209088     148 days
37790816      90 days
38058496      23 days
38659264      42 days
39541824      53 days
40160736     200 days
40674368      34 days
40905536     370 days
42485632     116 days
44109184      37 days
46070080       2 days
47097792      22 days
47221440     251 days
47483072     453 days
48319264      16 days
50413440     131 days
50986208     105 days
51996896      42 days
52263456    4889 days
52898720     200 days
53420640     886 days
54848192     178 days
55047104     280 days
55738144     570 days
56819168     989 days
57565760     197 days
57631168     237 days
57708896       3 days
60754176      31 days
62105792       6 days
62697600      26 days
63040320     109 days
63383264       5 days
63941248      41 days
64050112      10 days
64927520     191 days
65186240      26 days
66174752       2 days
66704736    1602 days
Name: No. Of Days in Hospital, dtype: timedelta64[ns]

Top 10 Patient IDs in terms of total number of days spent in the hospital:
After finding the total number of days spent by each unique patient in the data, we wrote the function below to calculate the 10 largest numbers depicting the highest number of days spent in the hospital by a ptient.
In [79]:


XX = totalDays.nlargest(n=10,keep='first')
print(XX)



ID
37026752   19372 days
52263456    4889 days
27421632    3568 days
12137440    1898 days
66704736    1602 days
56819168     989 days
53420640     886 days
35001792     837 days
35764288     656 days
3435936      650 days
Name: No. Of Days in Hospital, dtype: timedelta64[ns]

Plot the Top 10 Patient IDs by total number of days spent in the hospital:
Lastly, we plot the top 10 list of the patients to get a quick reference of the number of days and the ID associated with it.
In [92]:


YY = XX.plot(y = XX.iloc[1], kind='bar' , color = "coral" , figsize = (10,7), fontsize = 13);
YY.set_alpha(0.8)
YY.set_title("Top 10 patients with max. number of days", fontsize=18)
YY.set_ylabel("No. of days in thousands",fontsize = 18);
​





Executive Summary:
To summarize the above code, we noticed the following observations:
1) There are no American Indian or Alaskan Native Male Patients in the dataset. 
2) Black of African American contribute the most to the number of patients.
3) Second most contribution to the number of patients comes from Hispanic category.
4) Total Number of Unique Patients is 169.
5) Total Number of dead patients is 98.
6) Most spent days at the hospital are by IDs 37026752, 52263456 , 27421632 etc. The highest number of days by a patient goes beyond 19000.
                                Thank-you.
