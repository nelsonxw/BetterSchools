# TEA Student Data Analysis

### Team Members:

* Aditi Sharma
* Christopher Lucas
* Nish Garg
* Bill Wilson
* Nelson Wang

### Objectives:
Analyze educational data from TEA (Texas Education Agency) to discover relationships between students performance on STAAR (State of Texas Assessments of Academic Readiness) tests and student poverty status, campus geographic locations and teacher compensations.

### Dataset:
Data downloads from the Texas Academic Performance Report issued by Texas Education Agency for the school years 2013 to 2016. (https://rptsvr1.tea.texas.gov/perfreport/tapr/2017/download/DownloadData.html). 

### Tools used:
Pandas, Numpy, Matplotlib

### Major Steps:
1. Download raw data from TEA website in dat format.
2. Used Pandas to process the data and extract student performance data.
    + Imported data as dataframe into Pandas.
    + Replaced invalid data or masked data as NaN and dropped.
    + Created a column header mapping table and used it to replace column header codes with header descriptions.
    + Used Pandas melt function to change the data from wide table format to long table format, which made it easier for filtering data.
    + Created a partial string search function to allow filtering data by string search.
    + Parsed the data from the dataframe and extracted student performance data.
    + Validated data and dropped any school that has data masked by TEA  
    #### Code Snippets:  
    ``` Jupyter Notebook
    # import dependencies
import pandas as pd
import numpy as np
# define which school year to extract performance data
year = '2016'
# read raw data file as dataframe 
df_campus = pd.read_csv('CAMPSTAAR2_year%s.dat' %year)
df_campus.head()
CAMPUS	CA00AR01016D	CA00AR01S16N	CB00AR01016D	CB00AR01S16N	CW00AR01016D	CW00AR01S16N	CH00AR01016D	CH00AR01S16N	CI00AR01016D	...	CE00A003A17R	CS00A003017D	CS00A003A17N	CS00A003A17R	CR00A003017D	CR00A003A17N	CR00A003A17R	CL00A003017D	CL00A003A17N	CL00A003A17R
0	1902001	108	78	-1	-1	88	70	-1	-1	.	...	-1	-1	-1	-1	-1	-1	-1	0	.	.
1	1902041	116	100	-1	-1	92	80	12	9	.	...	10	-1	-1	-1	-1	-1	-1	0	.	.
2	1902103	115	104	-1	-1	92	88	13	9	.	...	18	38	7	18	-1	-1	-1	-1	-1	-1
3	1903001	207	173	10	6	173	147	15	13	.	...	6	-1	-1	-1	-1	-1	-1	0	.	.
4	1903041	284	224	20	9	226	184	22	16	.	...	13	101	7	7	286	7	2	0	.	.
5 rows × 1513 columns

# do initial data cleansing by replacing '.' or '-1' (masked data) with NaN
df_campus.replace('-1',np.nan,inplace=True)
df_campus.replace('.',np.nan,inplace=True)
# quick data exploration shows it is a big and wide table
df_campus.shape
(8757, 1513)
# the column headers are coded and not user friendly for further exploration
df_campus.head()
CAMPUS	CA00AR01016D	CA00AR01S16N	CB00AR01016D	CB00AR01S16N	CW00AR01016D	CW00AR01S16N	CH00AR01016D	CH00AR01S16N	CI00AR01016D	...	CE00A003A17R	CS00A003017D	CS00A003A17N	CS00A003A17R	CR00A003017D	CR00A003A17N	CR00A003A17R	CL00A003017D	CL00A003A17N	CL00A003A17R
0	1902001	108	78	NaN	NaN	88	70	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
1	1902041	116	100	NaN	NaN	92	80	12	9	NaN	...	10	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
2	1902103	115	104	NaN	NaN	92	88	13	9	NaN	...	18	38	7	18	NaN	NaN	NaN	NaN	NaN	NaN
3	1903001	207	173	10	6	173	147	15	13	NaN	...	6	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
4	1903041	284	224	20	9	226	184	22	16	NaN	...	13	101	7	7	286	7	2	0	NaN	NaN
5 rows × 1513 columns

# read 3 indiviudal header mapping tables and combine them into one mapping table
df_header1 = pd.read_csv('header_mapping_1_%s.csv' %year)
df_header2 = pd.read_csv('header_mapping_2_%s.csv' %year)
df_header3 = pd.read_csv('header_mapping_3_%s.csv' %year)
df_header = pd.concat([df_header1,df_header2,df_header3],ignore_index=True)
df_header.head()
NAME	LABEL
0	CB00A001S16N	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American All Tests Numerator
1	CB00A001S16R	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American All Tests Rate
2	CB00AM01S16N	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American Mathematics Numerator
3	CB00AM01S16R	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American Mathematics Rate
4	CB00AR01S16N	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American Reading/ELA Numerator
# create a dictionary for the column header mapping table, and use it to rename the column headers
header_dict = {}
for i in range(len(df_header)):
    header_dict[df_header.loc[i,'NAME']] = df_header.loc[i,'LABEL']    
df_campus = df_campus.rename(columns = header_dict)
df_campus.head()
CAMPUS	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students Reading/ELA Numerator	Campus 2016 Index 1: Summed Grades 3-11, African American Reading/ELA Performance Denominator	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, African American Reading/ELA Numerator	Campus 2016 Index 1: Summed Grades 3-11, White Reading/ELA Performance Denominator	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, White Reading/ELA Numerator	Campus 2016 Index 1: Summed Grades 3-11, Hispanic Reading/ELA Performance Denominator	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, Hispanic Reading/ELA Numerator	Campus 2016 Index 1: Summed Grades 3-11, American Indian Reading/ELA Performance Denominator	...	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed Econ Disadv All Subjects Rate	Index 3 Campus 2017 Performance: Grades 3-11, Summed Special Ed All Subjects Denominator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed Special Ed All Subjects Numerator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed Special Ed All Subjects Rate	Index 3 Campus 2017 Performance: Grades 3-11, Summed At Risk All Subjects Denominator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed At Risk All Subjects Numerator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed At Risk All Subjects Rate	Index 3 Campus 2017 Performance: Grades 3-11, Summed ELL All Subjects Denominator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed ELL All Subjects Numerator	Index 3 Campus 2017 Performance, Masters Grade Level: Grades 3-11, Summed ELL All Subjects Rate
0	1902001	108	78	NaN	NaN	88	70	NaN	NaN	NaN	...	NaN	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
1	1902041	116	100	NaN	NaN	92	80	12	9	NaN	...	10	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
2	1902103	115	104	NaN	NaN	92	88	13	9	NaN	...	18	38	7	18	NaN	NaN	NaN	NaN	NaN	NaN
3	1903001	207	173	10	6	173	147	15	13	NaN	...	6	NaN	NaN	NaN	NaN	NaN	NaN	0	NaN	NaN
4	1903041	284	224	20	9	226	184	22	16	NaN	...	13	101	7	7	286	7	2	0	NaN	NaN
5 rows × 1513 columns

# use melt function to transform dataset from wide table to long table so that it will be easier to set filter on the fields
df_campus = df_campus.melt(id_vars='CAMPUS', var_name='Category', value_name='Value')
print(df_campus.shape)
df_campus.head()
(13240584, 3)
CAMPUS	Category	Value
0	1902001	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	108
1	1902041	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	116
2	1902103	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	115
3	1903001	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	207
4	1903041	Campus 2016 Index 1: Summed Grades 3-11, All Students Reading/ELA Performance Denominator	284
# create a function 'contain' to do partial string search (case insensitive) which is used in filtering data fields
def contain(string,target):
    return string.lower() in target.lower()
# select data for the year requested
df_campus = df_campus[df_campus['Category'].apply(lambda x: contain(year,x))]
# get the total student count by campus and remove NaN
df_student_count = df_campus[df_campus['Category'].apply(
    lambda x: contain('All Students All Tests Performance Denominator',x))].dropna()
print(df_student_count.shape)
df_student_count.head()
(7990, 3)
CAMPUS	Category	Value
1225980	1902001	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	256
1225981	1902041	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	337
1225982	1902103	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	303
1225983	1903001	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	484
1225984	1903041	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	854
# get the student count for those passing the exam by campus and remove NaN
df_pass_count = df_campus[df_campus['Category'].apply(
    lambda x: contain('All Students All Tests Numerator',x))].dropna()
print(df_pass_count.shape)
df_pass_count.head()
(7990, 3)
CAMPUS	Category	Value
1234737	1902001	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	201
1234738	1902041	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	276
1234739	1902103	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	264
1234740	1903001	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	428
1234741	1903041	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	657
# get the student pass rate by campus and remove NaN
df_pass_rate = df_campus[df_campus['Category'].apply(
    lambda x: contain('All Students All Tests Rate',x))].dropna()
print(df_pass_rate.shape)
df_pass_rate.head()
(7761, 3)
CAMPUS	Category	Value
1471176	1902001	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	79
1471177	1902041	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	82
1471178	1902103	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	87
1471179	1903001	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	88
1471180	1903041	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	77
# merge dataframes to include all data elements
df_performance = df_student_count.merge(df_pass_count,on='CAMPUS',how='outer').merge(df_pass_rate,on='CAMPUS',how='outer')
print(df_performance.shape)
df_performance.head()
(7990, 7)
CAMPUS	Category_x	Value_x	Category_y	Value_y	Category	Value
0	1902001	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	256	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	201	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	79
1	1902041	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	337	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	276	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	82
2	1902103	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	303	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	264	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	87
3	1903001	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	484	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	428	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	88
4	1903041	Campus 2016 Index 1: Summed Grades 3-11, All Students All Tests Performance Denominator	854	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Numerator	657	Campus 2016 Index 1: Index 1 (Phase1 Level2 & PM_ELL), Summed Grades 3-11, All Students All Tests Rate	77
# select columns and rename column headers, add a column for the school year requested
df_performance = df_performance[['CAMPUS','Value_x','Value_y','Value']].rename(
    columns = {'CAMPUS':'campus_id','Value_x':'student_count','Value_y':'pass_count','Value':'pct_pass'})
df_performance['year'] = year
print(df_performance.shape)
df_performance.head()
(7990, 5)
campus_id	student_count	pass_count	pct_pass	year
0	1902001	256	201	79	2016
1	1902041	337	276	82	2016
2	1902103	303	264	87	2016
3	1903001	484	428	88	2016
4	1903041	854	657	77	2016
# note there are 7990 schools but only 7761 schools have reported with pass rate.
# the delta represents the schools with small number of students and pass rate data are masked by TEA
print(df_performance['campus_id'].count())
print(df_performance['pct_pass'].count())
7990
7761
# remove schools without pass rate
df_performance = df_performance.dropna()
df_performance.shape
(7761, 5)
# save data to csv file
df_performance.to_csv('campus_performance_%s.csv' %year,index=False)
    ```
	
	
