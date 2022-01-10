# Analyzing Employee Exit Surveys

We'll be taking a look at, and analyzing, employee exits surveys from two educational institutions:
* Department of Education, Training and Employment (DETE) 
* Technical and Further Education (TAFE)

We'll be working with two datasets (DETE, TAFE). We'll be working to combine the two datasets. 

## The main questions
* Are employees who only worked for the institutes for a short period of time resigning due to some kind of dissatisfaction? What about employees who have been there longer?
* Are younger employees resigning due to some kind of dissatisfaction? What about older employees?


## Initial Observations

### DETE Survey
* The DETE survey has a good handful of `bool` questions that should be easier to work with. Some of them might integrate well with the TAFE survey.  
* All our dates with have to be standardized across both datasets.  
* It seems like some of the "reason" columns could be combined.
* There are a lot of string objects that can become simple boolean answers.

### TAFE Survey
* This is a mess. The `Contributing Factors` can probably be merged.  
* The Institute Views could also be merged I bet. As well as Work Unit Views, Instruction Info, and Workplace. These are all basically boolean questions.  
* Age needs to be an int.  
* Length of service could potentially be categorized as short/long tenure.  

### DQ
* The dete_survey dataframe contains 'Not Stated' values that indicate values are missing, but they aren't represented as NaN.  * Both the dete_survey and tafe_survey dataframes contain many columns that we don't need to complete our analysis.  
* Each dataframe contains many of the same columns, but the column names are different.  
*  There are multiple columns/answers that indicate an employee resigned because they were dissatisfied.

## Cleaning

### Column Drops
DQ says to drop all these columns. Now I felt like they could be merged, or at least taken into account on some level. Columns like Professional Development, Opportunities for promotion, Staff morale, Workplace issue, Physical environment ALL seem like very relevant reasons to move on from a job. 

However, the DETE data for these columns appear to be specific abbreviations. I'm having a hard time tracking down what those abbreviations are code for. So I guess that's why we're dropping them. 

The TAFE dropped columns are a whole bunch of agree/disagree questions. These could have a value of 1-5 if we thought the questions would be helpful. But maybe it's just too much to deal with for a beginner? Some of the other columns are straight up Y/N and some are just bools. I'm not really sure.

### Column Renaming
I am renaming some of these columns to more easily identify similarities between questions. I am also adding `_` to make things a little more Pythonic as well as setting to lowercase for consistency.

### Separation Type
One of the quesitons we are trying to answer revolves around the reasons for resignation. Let's isolate resignations only.  
* DETE has three different types of resignation answers (Resignation-Other reasons, Resignation-Other employer, Resignation-Move overseas/interstate). We'll merge those all into one.  
* TAFE has just a simple "Resignation" respose.  


## Verifying Data

### Years of Service

#### dete_resignations
* Let's extract the year for `cease_date` so that we have consistency.  
* We'll also convert those values to `float` since all the start_date data is in year-only float format.  

#### tafe_resignations  

* The tafe cease dates appear to already be in `float` format.  
* Instead of a start date we have `institue_service` that approximates the years of service.  


#### Start and Cease dates

* We'll use the TAFE years of service to approximate long and short term employees.  
* Since TAFE has start and cease dates we'll isolate rows where we can actually extract a value for years of service.  
* From what I can tell there will only be 273 rows that we can actually use.  
* Vectorized operations will only work on non-null values so our new column will have NaN value for any rows that do not contain both a start and cease year.  


### Dissatisfied or Not

We're going to create a new column based on the contributing factors for resignation. 

TAFE has just two options and both use the term "dissatisfaction"
* `Contributing Factors. Dissatisfaction`
* `Contributing Factors. Job Dissatisfaction`  

DETE has a large handful that could fall under the category of "Dissatisfied".
* `job_dissatisfaction`
* `dissatisfaction_with_the_department`
* `physical_work_environment`
* `lack_of_recognition`
* `lack_of_job_security`
* `work_location`
* `employment_conditions`
* `work_life_balance`
* `workload`

Dete doesn't appear to have any NaN values which is really helpful.  



## Merging Dataframes

* Let's start by adding a new column named `institute` so that we can keep track of both datasets.  
* We'll concatenate along the `rows` axis which will easily combine all the `columns` in common.  
* Let's also drop all columns with less than 500 non null values.  



#### We're going to use four categories to sort our employees.

* New: Less than 3 years at a company
* Experienced: 3-6 years at a company
* Established: 7-10 years at a company
* Veteran: 11 or more years at a company