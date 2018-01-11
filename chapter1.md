---
title       : Explore the workshop data
description : Using R to explore the Claim Termination file going to be used in the workshop.

---
## Data Exploration using R - Claim Terminations data table

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: e15e1c6c6c
```
In the next few exercises, we will explore the data file that will be used at the the workshop. Claim termination rate (CTR) data has been extracted from the SOA experience database and modified for workshop purposes.The modified data is downloaded here as a R Data table and is named 'ctr_data'.

'data.table' is an R extension of 'data.frame' and allows for faster processing and manipulation of data. Like data frames, it can have columns with different modes (numeric, character, factor, etc.). This is similar to SAS and SPSS datasets. The package data.table must be loaded in the R workspace to use the data tables and related functions. We have already added this package to the session by using function `library(data.table)`.


`@instructions`

- Type the following below comment #1: `str(ctr_data)` 
  The str function will show the basic structure of the workshop data file.

- Complete the `head()` function below the comment #2 to print the top rows from the `ctr_data` table. Take time to scan through the resulting data.

- Complete the `names()` function below the comment #3 to display the column names in the `ctr_data` table.



`@hint`
Use `str(table_name)` function to see the basic structure in R.

Use `head(table_name)` function to view the top rows from a data frame or data table.

Use `names(table_name)` function to see the names of columns in a data frame or data table.


`@pre_exercise_code`
```{r}

library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```

`@sample_code`
```{r}
#1 View the basic structure of the claim termination data table 'ctr_data'


#2 View the top rows from the claim termination data table 'ctr_data'


#3 View the column names in the claim termination data table 'ctr_data'


```


`@solution`
```{r}
#1 View the basic structure of the claim termination data table 'ctr_data'
str(ctr_data)

#2 View the top rows from the claim termination data table 'ctr_data'
head(ctr_data)

#3 View the column names in the claim termination data table 'ctr_data'
names(ctr_data)

```


`@sct`
```{r}

#1 View the basic structure of the claim termination data table 'ctr_data'
test_function("str", args = "ctr_data",
              not_called_msg = "Have you used `str()` to see the structure of the ctr_data table?",
              args_not_specified = "Have you specified the correct table name?",
              incorrect_msg = c("Have you used `str()` to include the ctr_data table? ",
                               "Have you specified the correct table name?"))
              
#2 View the top rows from the claim termination data table 'ctr_data'
test_function("head", args = "ctr_data",
              not_called_msg = "Have you used `head()` to see the top records from the ctr_data table?",
              args_not_specified = "Have you specified the correct table name?",
              incorrect_msg = c("Have you used `head()` to see the top records from the ctr_data table? ",
                               "Have you specified the correct table name?"))
              
#3 View the column names in the claim termination data table 'ctr_data'
test_function("names", args = "ctr_data",
              not_called_msg = "Have you used `names()` to see the variable names in the ctr_data table?",
              args_not_specified = "Have you specified the correct table name?",
              incorrect_msg = c("Have you used `names()` to see the variable names in the ctr_data table? ",
                               "Have you specified the correct table name?"))
              

test_function("names", not_called_msg = "You didn't call `head()`!")

test_error()
success_msg("Good work!")

```
