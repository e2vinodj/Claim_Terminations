---
title       : 2018 ILTCI Predictive Modelling Workshop - Prework
description : Work on R functions we will use at workshop.

---
## Quick Intro to Data Camp Exercises

```yaml
type: NormalExercise
key: 459aa8cdaa
lang: r
skills: 1
```
This course allows the workshop attendees to apply R programming skills without installing the R application and explore the data used at the workshop for Predictive Analysis modeling for Claim Termination study. 

We strongly encourage you to take other R courses for indepth knowledge. 


`@instructions`
Reminder:
In the window "script.R" you should type R code to solve the exercises. When you hit the 'Submit Answer' button, every line of code is interpreted and executed by R and you will get a message whether or not your code was correct. The output of your R code is shown in the console in the lower right corner. You can hit the 'Hint' button for hints.

You can also execute R commands straight in the console. This is a good way to experiment with the R code, as your submission is not checked for correctness.

Note: All the packages necessary for the exercises will be added to the R sessions in this course for your use.

- Type the following in the script editor below the comment #2: a <- a + 6. 


`@pre_exercise_code`
```{r}
library(data.table)

```

`@sample_code`
```{r}
#1 assign 5 to the varaible 'a'
a <- 5

#2 Add 6 to the varaible 'a'


#3 show the value in 'a'
a
```

`@solution`
```{r}
#1 assign 5 to the varaible 'a'
a <- 5

#2 Add 6 to the varaible 'a'
a <- a + 6

#3 show the value in 'a'
a
```

`@sct`
```{r}
test_error()
success_msg("Good work!")
```


---
## How to use functions from other R packages

```yaml
type: TabExercise
key: f5311b7de5
lang: r
```

`@pre_exercise_code`
```{r}
library(data.table)
library(dplyr)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```
***

```yaml
type: NormalExercise
lang: r
key: 3e0a9f3b88
```

A R package typically includes several functions and sometimes even data sets within it. Some packages are included with the Base R version.  There are several other R packages developed by the community that can be reused by you to perform simple to complex tasks without having to reinvent the wheel. These packages must be downloaded and installed  from CRAN-like repositories or from local files. You can use the function `install.packages("package name in quotes")` to install packages in your system. This is often an onetime task.

To use a function or data from the other packages, we must load the package into the session before calling the function. To load a package use the function 'library(package name without quotes)`. 

To unload a package from the memory, use the function `detach()`.

Type the `?` in front of the function names like `?library()` in the R console to read the R documentation for it. You can do this for any function.


`@instructions`

- Type the following below comment #2: `str_detect(ctr_data$Diagnosis_Category, "Alzheimer")`
  If you don’t have the package `stringr` loaded in your session, you will get an error message - Error: could not find function "str_detect".
 
- Now type the following below comment #1 to load the package `stringr` loaded in your session and submit the code: `library(stringr)`

- Now type the following below comment #3 to unload the package `stringr` from memory: `detach(package:stringr)`


***

```yaml
type: NormalExercise
lang: r
key: ddea187fd4
xp: 50
```

Are you ready to test your knowledge to Load a  R package.

`@instructions`

- Type the R code below comment #1 to load the package `ggplot2` in this session

Note - The `ggplot2` package is already installed but not loaded.
       The ggplot2 package, created by Hadley Wickham, offers a powerful graphics language for creating elegant and complex plots. It is very popular package and allows you to create graphs that represent both univariate and multivariate numerical and categorical data in a straightforward manner. Grouping can be represented by color, symbol, size, and transparency. The creation of trellis plots (i.e., conditioning) is relatively simple. 
 


`@hint`
Use `library()` function to load the package into current R session.


`@sample_code`
```{r}
#1 Load the package ggplot2 into your R session


#2 Calculate the Actual termination rates from the ctr_data
raw_hazard <- ctr_data %>%
    group_by(ClaimDuration,
             Gender) %>%
           summarise(raw_hazard_rate = sum(Terminations)/sum(Exposure))
		   
		   
#3 plot the Actual termination rates from the ctr_data
ggplot() +
  geom_line(data = raw_hazard, 
            aes(x =ClaimDuration , 
                y = raw_hazard_rate, 
                colour = factor(c(Gender))
                )
            ) + 
  ggtitle("Actual termination hazard rates")
```


`@solution`
```{r}
#1 Load the package ggplot2 into your R session
library(ggplot2)

#2 Calculate the Actual termination rates from the ctr_data
raw_hazard <- ctr_data %>%
    group_by(ClaimDuration,
             Gender) %>%
           summarise(raw_hazard_rate = sum(Terminations)/sum(Exposure))
		   
		   
#3 plot the Actual termination rates from the ctr_data
ggplot() +
  geom_line(data = raw_hazard, 
            aes(x =ClaimDuration , 
                y = raw_hazard_rate, 
                colour = factor(c(Gender))
                )
            ) + 
  ggtitle("Actual termination hazard rates")
```

`@sct`
```{r}
test_function("library", args = "ggplot2",
              not_called_msg = "Have you used the function to load the ggplot2 package?",
              args_not_specified = "Have you specified the package name in the function?",
              incorrect_msg = c("Have you provided the correct package name to load ggplot2?"))

test_error()
success_msg("Good work!")
```

---
## Data Exploration using R - Claim Terminations data table

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: 0b40057779
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
              args_not_specified = "Have you specified the table name?",
              incorrect_msg = c("Have you specified the correct table name?"))
              
#2 View the top rows from the claim termination data table 'ctr_data'
test_function("head", args = "ctr_data",
              not_called_msg = "Have you used `head()` to see the top records from the ctr_data table?",
              args_not_specified = "Have you specified the table name?",
              incorrect_msg = c("Have you specified the correct table name?"))
              
#3 View the column names in the claim termination data table 'ctr_data'
test_function("names", args = "ctr_data",
              not_called_msg = "Have you used `names()` to see the variable names in the ctr_data table?",
              args_not_specified = "Have you specified the table name?",
              incorrect_msg = c("Have you specified the correct table name?"))
              

test_error()
success_msg("Good work!")

```

---
## Data Exploration using R - Column names

```yaml
type: MultipleChoiceExercise
lang: r
xp: 20
skills: 1
key: dab375c9ff
```


What is the Name of the variable that contains the state code in the `ctr_data` table?.

`@instructions`
- stateabbr
- $stateabbr
- StateAbbr
- $StateAbbr


`@hint`
Use names() or str() or summary() functions.

`@pre_exercise_code`
```{r}

library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)

```


`@sct`
```{r,eval=FALSE}
msg1 = "Incorrect! Check the R restrictions on variable namings"
msg2 = "Incorrect! Check the usage of $ in reference to R table varaiables"
msg3 = "Correct! The R vaiables are case sensitive. The table does not have $ prefix before the variable StateAbbr"
msg4 = "Incorrect! Check the usage of $ in reference to R table varaiables"

test_mc(correct=3, feedback_msgs = c(msg1, msg2, msg3, msg4))
```

---

## Data Exploration using R - Scope of the variables in the data table

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: cc38162081
```

R objects that reside in other R objects can require a lot of typing to access. For example, to refer to a variable x in a dataframe df, one could type df$x. The attach() function helps to reference the database variables by their name alone without using the table name. Using the function will attach the  database to the R search path. This means that the database is searched by R when evaluating a variable. By default the database is attached in position 2 in the search path, immediately after the user's workspace and before all previously attached packages and previously attached databases. However use this function with caution, as it can easily lead to referencing a wrong object with same name.

To remove the database from the search path, use the detach() function.


`@instructions`

- Run the code as-is and see the results.

- Complete the `attach()` function below comment #3 to attach the `ctr_data` data table to the R search path. 
  
- Complete the `detach()` function below the comment #5 to remove the `ctr_data` data table from the R search path..


`@hint`
Use `attach(table_name)` function to attach a table to the R search path.

Use `detach(table_name)` function to remove the table from the R search path.


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
#1 Print the first 50 values in column ClaimType from the ctr_data
ctr_data$ClaimType[1:50]

#2 Try to print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

#3 Complete the function to attach the ctr_data to the search path
attach(  )

#4 Now print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

#5 Complete the function to remove the ctr_data to the search path
detach(   )

#6 Now print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

```


`@solution`
```{r}
#1 Print the first 50 values in column ClaimType from the ctr_data
ctr_data$ClaimType[1:50]

#2 Try to print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

#3 Complete the function to attach the ctr_data to the search path
attach(ctr_data)

#4 Now print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

#5 Complete the function to remove the ctr_data to the search path
detach(ctr_data)

#6 Now print the first 50 values in column ClaimType from the ctr_data
ClaimType[1:50]

```


`@sct`
```{r}
test_function("attach", args = "ctr_data",
              not_called_msg = "Have you used `attach()` to include the ctr_data table?",
              args_not_specified = "Have you specified the table name to attach?",
              incorrect_msg = c("Have you specified the table name correctly to attach?"))

test_function("detach", args = "ctr_data",
              not_called_msg = "Have you used `detach()` to remove the ctr_data table from search path?",
              args_not_specified = "Have you specified the table name to detach?",
              incorrect_msg = c("Have you specified the correct table name to detach?"))

test_error()
success_msg("Good work!")

```
---

## Data Exploration using R - gather insights from data

```yaml
type: NormalExercise
xp: 100
key: 13c07d42c4
```

`@pre_exercise_code`
```{r}
library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```

`@instructions`

Take time to know the data and gain insights into your data.

The table function is a very basic, but essential, function to master while performing interactive data analyses. It simply creates tabular results of categorical variables. However, when combined with the powers of logical expressions in R, you can gain even more insights into your data, including identifying potential problems.

- The code below comment #1 prints the distrubution of Coverage Types:

- The code below comment #2 prints the distrubution of Coverage Types and the Group Indicator:

- Now Type the table function below the comment #3 to print the distrubution of Coverage Types, the Group Indicator and the Gender from table ctr_data:


`@sample_code`
```{r}
#1 View a tabular summary by Coverage type
table(ctr_data$Cov_Type_Bucket)

#2 View a tabular summary by Coverage type and Group Indicator
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator)

#3 View a tabular summary by Coverage type, Group Indicator and Gender


```

`@solution`
```{r}
#1 View a tabular summary by Coverage type
table(ctr_data$Cov_Type_Bucket)

#2 View a tabular summary by Coverage type and Group Indicator
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator)

#3 View a tabular summary by Coverage type, Group Indicator and Gender
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator, ctr_data$Gender )

```

`@hint`
Add Gender as a argument in the table() function

`@sct`
```{r}
#1 View a tabular summary by Coverage type
test_function("table", args = c("x"), index=1, not_called_msg = "You didn't call `table() in the code after comment #1`!")

#2 View a tabular summary by Coverage type and Group Indicator
test_function("table", args = c("x","y"), index=2, not_called_msg = "You didn't call `table() in the code after comment #2`!")

#3 View a tabular summary by Coverage type, Group Indicator and Gender
test_function("table", args = c("x","y","z"), index=3, not_called_msg = "You didn't call `table() in the code after comment #3`!")

test_error()
success_msg("Good work!")

```

---

## Data Exploration using R - Creating new datasets from existing tables

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: 5f8a281c76
```

A new data frame or a data table can be created from an existing data frame or table using the Assignment operator `<-`.



`@instructions`

- Running the code under comment #1, will display the records from the ctrdata table having value 16430 in the column fakeclaim_id.

- Type this code below comment #2 to make a copy of the data in `ctr_data` but only for records having the Claim # 16430: ctr_data1 <- ctr_data[fake_claim_id==16430,]

- Type this code below comment #3 to view the data from the new table `ctr_data1`: View(ctr_data1)
 
- Now Type the code below comment #4 to Create a new data table called 'Test_Data' from table `ctr_data` having Incurred_Year >= 2000: 


`@hint`
Use '<-` to assign the data into a new table from an existing table.

Use "table[selection creiteria,]" to remove the pick only selected records from a table.


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
#1 Print the records from the ctr_data table where fake_claim_id = 16430
ctr_data[fake_claim_id==16430,]

#2 Copy the records into ctr_data1 table from the ctr_data table where fake_claim_id = 16430
ctr_data1 <- ctr_data[fake_claim_id==16430,]

#3 View the records the newly created `ctr_data1` dataset in tabular format (like excel)
View(ctr_data1)

#4 Create a new data table called `Test_Data` from table `ctr_data` where Incurred_Year >= 2000


```

`@solution`
```{r}
#1 Print the records from the ctr_data table where fake_claim_id = 16430
ctr_data[fake_claim_id==16430,]

#2 Copy the records into ctr_data1 table from the ctr_data table where fake_claim_id = 16430
ctr_data1 <- ctr_data[fake_claim_id==16430,]

#3 View the records the newly created `ctr_data1` dataset in tabular format (like excel)
View(ctr_data1)

#4 Create a new data table called `Test_Data` from table `ctr_data` where Incurred_Year >= 2000
Test_Data <- ctr_data[Incurred_Year >= 2000,]

```


`@sct`
```{r}

test_object("ctr_data1")
test_object("Test_Data")
success_msg("Good work!")

```


---

## Data Manipulation - Working with the `dplyr` package

```yaml
type: TabExercise
key: e6a60bf804
lang: r
```

`@pre_exercise_code`
```{r}
library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```
***

```yaml
type: NormalExercise
lang: r
key: d9c7c56d60
xp: 50
```

`dplyr` is one of the many data manipulation tools used for data analysis. It makes data manipulation easier to perform in R. It is fast and works with the data no matter where it's stored, whether in a data frame, a data table or database.

Listed here are some of the functions from dplyr package that is often used for data manipulation. Some of these functions are available in base R, but the dplyr functions are simpler to write and efficient in computation.

	• `select()` allows to subset of variables
	• `filter()` allows to subset of rows
	• `mutate()` allows to add new columns
	• `summarise()` can aggregate each group to a smaller number of summary statistics
	• `arrange()` allows to re-order the rows
	• `%>%` is a pipe that allows to chain the code together

Type the `?` in front of the function names like `?select()` in the R console to read the R documentation for it. You can do this for any function. The package must be loaded into the session.


`@instructions`

- Now type the code below comment #1 to load the package `dplyr` in your session and submit the code:

`@hint`
Use `library()` function to load the package into current R session.


`@sample_code`
```{r}
#1 Load the package dplyr into your R session


```


`@solution`
```{r}
#1 Load the package dplyr into your R session
library(dplyr)

```

`@sct`
```{r}
test_function("library", args = "dplyr",
              not_called_msg = "Have you used the function to load the `dplyr` package?",
              args_not_specified = "Have you specified the package name - dplyr?",
              incorrect_msg = c("Have you specified the correct package name?"))


test_error()
success_msg("Good work!")

```


***

```yaml
type: NormalExercise
lang: r
key: f755deced6
xp: 50
```

## Keeping or Removing columns from tables and data frames

`select()` is a function available in the `dplyr` package. It allows you to keep or remove the variables you list in the statement. The `-` in front of the variable names in the `select` function removes the variable. 

Type `?select()` in the R console to read the R documentation for it.

`@instructions`
- To copy only variables `fake_claim_id`, `Gender`, `Claim_Type` and `Max_Ben_Bucket` from the `ctr_data` table into a new table `temp_ctr_data`, Type the following under comment #1: `temp_ctr_data <- select(ctr_data, fake_claim_id, Gender, Claim_Type, Max_Ben_Bucket)`

- Type the following below comment #2 to list the column names from temp_ctr_data: names(temp_ctr_data)

- Type the following below comment #3, to remove the column `fake_claim_id` from `temp_ctr_data`: select(temp_ctr_data, -fake_claim_id) 

- Now type the code below comment #4, to use the `select` function to copy only columns `TQ_Status`, `Cov_Type_Bucket` and `Terminations` from the `ctr_data` table into a new table called `new_ctr_data`:


`@hint`
Use `select(column names)` function to keep or `select(-column names)` to remove columns from a table or dataframe.


`@sample_code`
```{r}
#1 Keep variables fake_claim_id, Gender, Claim_Type and Max_Ben_Bucket from the 'ctr_data' table into 'temp_ctr_data'


#2 List the column names in table - temp_ctr_data

   
#3 Remove the column 'fake_claim_id' from table - temp_ctr_data


#4 Keep variables 'TQ_Status', 'Cov_Type_Bucket' and 'Terminations' from the 'ctr_data' table into 'new_ctr_data'


```


`@solution`
```{r}
#1 Keep variables fake_claim_id, Gender, Claim_Type and Max_Ben_Bucket from the 'ctr_data' table into 'temp_ctr_data'
temp_ctr_data <- select(ctr_data, fake_claim_id, Gender, Claim_Type, Max_Ben_Bucket)

#2 List the column names in table - temp_ctr_data
names(temp_ctr_data)  
   
#3 Remove the column 'fake_claim_id' from table - temp_ctr_data
select(temp_ctr_data, -fake_claim_id) 

#4 Keep variables 'TQ_Status', 'Cov_Type_Bucket' and 'Terminations' from the 'ctr_data' table into 'new_ctr_data'
new_ctr_data <- select(ctr_data, TQ_Status, Cov_Type_Bucket, Terminations)

```

`@sct`
```{r}
test_function("select", args = c("ctr_data", "fake_claim_id", "Gender", "Claim_Type", "Max_Ben_Bucket"),
              index=1,
              not_called_msg = "Have you used the function to select the columns?",
              args_not_specified = "Have you specified, all the 4 column names as in the table?",
              incorrect_msg = c("Have you specified the table name correctly in the function?",
                                "Have you specified the column 'fake_claim_id'?",
                                "Have you specified the column 'Gender'?",
                                "Have you specified the column 'Claim_Type'?",
                                "Have you specified the column 'Max_Ben_Bucket'?"
                                ))


test_function("select", args = c("temp_ctr_data", "-fake_claim_id"),
              index=2,
              not_called_msg = "Have you used the function to select the columns?",
              args_not_specified = "Have you specified the column names to keep/remove and are they named as in the table?",
              incorrect_msg = c("Have you specified the table name correctly in the function?",
                                "Have you specified the column 'fake_claim_id' to remove?"
                               ))

test_function("select", args = c("ctr_data", "TQ_Status", "Cov_Type_Bucket", "Terminations"),
              index=3,
              not_called_msg = "Have you used the function to select the columns?",
              args_not_specified = "Have you specified, all the 3 column names as in the table?",
              incorrect_msg = c("Have you specified the table name correctly in the function?",
                                "Have you specified the column 'TQ_Status'?",
                                "Have you specified the column 'Cov_Type_Bucket'?",
                                "Have you specified the column 'Terminations'?"
                               ))
test_object(new_ctr_data)
test_error()
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: bf930e5f6e
xp: 50
```

## dplyr - Selecting rows from tables and data frames

`filter()` is a function available in the `dplyr` package. It allows you to subset by rows. The rows that satisfy the criteria specified in the filter function will be retained and all other rows will be filtered out.

Type `?filter()` in the R console to read the R documentation for it.

`@instructions`
- To pick only the records with Claim Type = "HHC" OR "ALF" from the ctr_data, type this below comment #1:  filter(ctr_data, ClaimType = "HHC" | ClaimType = "ALF")

- Now type the code with filter function below comment #2 to pick only the records where Gender is "Female" and IncurredAgeBucket is "80 to 84" and Max_Ben_Bucket is Not "Unlimited" from the ctr_data table:


`@hint`
Use `filter(column names)` function to select the rows from a table or dataframe. Use `==` to compare equal

`@sample_code`
```{r}
#1 select only the records with Claim Type = "HHC" OR "ALF" from the 'ctr_data' table


#2 Select only the records where Gender is "Female" and IncurredAgeBucket is "80 to 84" and Max_Ben_Bucket is Not "Unlimited" from the ctr_data table

   
```


`@solution`
```{r}
#1 select only the records with Claim Type = "HHC" OR "ALF" from the 'ctr_data' table
filter(ctr_data, ClaimType == "HHC" | ClaimType == "ALF")

#2 Select only the records where Gender is "Female" and IncurredAgeBucket is "80 to 84" and Max_Ben_Bucket is Not "Unlimited" from the ctr_data table
filter(ctr_data, Gender == "Female",  IncurredAgeBucket == "80 to 84", Max_Ben_Bucket != "Unlimited")
   
```

`@sct`
```{r}
test_function("filter", args = c('ctr_data', "..."),
              index=1,
              not_called_msg = "Have you used the function to select the records?",
              args_not_specified = "Have you specified the table and conditions correctly to select the records")

test_function("filter", args = c('ctr_data', "..."),
              index=2,
              not_called_msg = "Have you used the function to select the records?",
              args_not_specified = "Have you specified the table and conditions correctly to select the records")

test_error()
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: 2667dadeae
xp: 50
```

## dplyr - Adding new variables to tables and data frames

`mutate()` allows us to create new column(s) or modify values of existing columns. Often the new columns are created from evaluating the values in existing columns. `mutate()` is a function available in the `dplyr` package.

Type `?mutate()` in the R console to read the R documentation for it.

`@instructions`
- The code below comment #1 shows how to create a new column 'ClmDurBucket ' in the ctr_data: 

- The code below comment #4 shows how to create a new column 'Confined_Sample' from another new column 'Confined_Ind', created all in the same `mutate()` function: 

- Now type the code using mutate() function below comment #7, to create a new column `BP_Type` in ctr_data table. Populate it as "Lifetime" when the corresponding record has Max_Ben_Bucket = "Unlimited" else populate it with "Non-Life":

- The code below comment #8 allows to print the value in BP_Type for the first 50 records


`@hint`
Use `mutate(table name, columns...)` function to add new columns or make changes to the value of a column in a table or  dataframe.


`@sample_code`
```{r}
#1 How to create a new column 'ClmDurBucket ' in the 'ctr_data' table
mutate(ctr_data, ClmDurBucket = case_when(ClaimDuration <= 3 ~ "Mos1-3",
                                                          ClaimDuration > 3 & ClaimDuration <= 12 ~ "Mo4-12",
                                                          ClaimDuration > 12 & ClaimDuration <= 72 ~ "Yrs2-6",
                                                          ClaimDuration > 72 ~ "Yrs7+ " ))

														  
#2 See the structure of newly added column from 'ctr_data' table
str(ctr_data)

#3 See the top records from 'ctr_data' table
head(ctr_data(50))

#4 How to create a new column from another newly created column created in same statement
mutate(ctr_data, Confined_Ind = case_when(
								  ClaimType = "NH" | ClaimType = "ALF" ~ "Confined",
								  ClaimType = "HHC" ~ "Non-Confined",
								  ~ "Unknown" ), 
				 Confined_Sample =  if_else(Confined_Ind == "Confined",  "Y",  "N" )
								  )

  
#5 See the structure of newly added columns from 'ctr_data' table
str(ctr_data)

#6 See the top records from 'ctr_data' table
head(ctr_data(50))

#7 Create a new column `BP_Type` in ctr_data table using mutate function. Populate it as "Lifetime" when the corresponding record has Max_Ben_Bucket = "Unlimited" else populate it with "Non-Life".


#8 Print the values in `BP_Type` of the first 50 records from 'ctr_data' table
ctr_data[1:50,]$BP_Type

```


`@solution`
```{r}
#1 How to create a new column 'ClmDurBucket ' in the 'ctr_data' table
mutate(ctr_data, ClmDurBucket = case_when(ClaimDuration <= 3 ~ "Mos1-3",
                                                          ClaimDuration > 3 & ClaimDuration <= 12 ~ "Mo4-12",
                                                          ClaimDuration > 12 & ClaimDuration <= 72 ~ "Yrs2-6",
                                                          ClaimDuration > 72 ~ "Yrs7+ " ))

														  
#2 See the structure of newly added column from 'ctr_data' table
str(ctr_data)

#3 See the top records from 'ctr_data' table
head(ctr_data(50))

#4 How to create a new column from another newly created column created in same statement
mutate(ctr_data, Confined_Ind = case_when(
								  ClaimType = "NH" | ClaimType = "ALF" ~ "Confined",
								  ClaimType = "HHC" ~ "Non-Confined",
								  ~ "Unknown" ), 
				 Confined_Sample =  if_else(Confined_Ind == "Confined",  "Y",  "N" )
								  )

  
#5 See the structure of newly added columns from 'ctr_data' table
str(ctr_data)

#6 See the top records from 'ctr_data' table
head(ctr_data(50))

#7 Create a new column `BP_Type` in ctr_data table using mutate function. Populate it as "Lifetime" when the corresponding record has Max_Ben_Bucket = "Unlimited" else populate it with "Non-Life".
mutate(ctr_data, BP_Type  = if_else(Max_Ben_Bucket == "Unlimited",  "Lifetime",  "Non-Life" ))

#8 Print the values in `BP_Type` of the first 50 records from 'ctr_data' table
ctr_data[1:50,]$BP_Type

```

`@sct`
```{r}
test_function("mutate",
              index=3,
              not_called_msg = "Have you used the mutate function to modify the records?")


test_output_contains("ctr_data[1:50,]$BP_Type",
                     times = 1)
test_error()
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: b4e6d5f13e
xp: 50
```

## dplyr - Simpler Coding using pipe

Imagine when you have to execute a series of tasks, dependent on the results from previous steps. You could create a complex code, one harder to understand and maintain latter. Or break the code into several lines and end up with clutter in the workspace. The R Pipe, notated as `%>%` will allow you to construct readable and uncluttered statements .

The `%>%` takes the object or results from its left and passes it as the first argument to the function on its right.


`@instructions`
- The code below comment #1 Copies only records where Gender is "Female" and Max_Ben_Bucket <> "Unlimited" and then Select only the listed columns: 

- The code below comment #2 does the same thing as the R code in #1 but uses the Pipe `%>%` to write a simpler code:
  You will notice that the table names are not specified in the functions `filter` and `select`. A table name is expected as the first argument in both these functions but it is missing now. No worries! Here the pipe `%>%` passes the table ctr_data (left hand side of the pipe) as the first argument to the function filter (on right hand side of the pipe). Then the results from the filter, the table with only rows having Gender = "Female" and Max_Ben_Bucket != "Unlimited" is passed as the first argument to the `select()` function. The select function will pick the columns between fake_claim_id and ClaimType  ie., fake_claim_id, GroupIndicator, Gender, IncurredAgeBucket, Incurred_Year, ClaimType; and the column Max_Ben_Bucket and move the ending results to the table `ctr_data_temp2`. 

- Now Compare the results and structure of the ctr_data_temp1 & ctr_data_temp2 table. Use the `str()`, `head()`, `table()` functions from previous exercises. Note that the ctr_data remains unchanged:
  
`@sample_code`
```{r}
#1 copy from ctr_data to ctr_data_temp1. Copy only records where Gender is "Female" and Max_Ben_Bucket <> "Unlimited". Select only the listed columns.
ctr_data_temp1 <- filter(ctr_data, Gender == "Female",  Max_Ben_Bucket != "Unlimited")
ctr_data_temp1 <- select(ctr_data_temp1, fake_claim_id:ClaimType, Max_Ben_Bucket )
			  
#2 Same task as the above code but using the pipe `%>%`
ctr_data_temp2 <- ctr_data %>%
                filter(Gender == "Female",  Max_Ben_Bucket != "Unlimited")  %>%
                select(fake_claim_id:ClaimType, Max_Ben_Bucket)

#3 See the top records from 'ctr_data_temp1' table
head(ctr_data_temp1(50))

#4 See the top records from 'ctr_data_temp2' table
head(ctr_data_temp2(50))

#5 See the basic structure of 'ctr_data_temp1' table
str(ctr_data_temp1(50))

#5 See the basic structure of 'ctr_data_temp2' table
str(ctr_data_temp2(50))

```


`@solution`
```{r}
#1 copy from ctr_data to ctr_data_temp1. Copy only records where Gender is "Female" and Max_Ben_Bucket <> "Unlimited". Select only the listed columns.
ctr_data_temp1 <- filter(ctr_data, Gender == "Female",  Max_Ben_Bucket != "Unlimited")
ctr_data_temp1 <- select(ctr_data_temp1, fake_claim_id:ClaimType, Max_Ben_Bucket )
			  
#2 Same task as the above code but using the pipe `%>%`
ctr_data_temp2 <- ctr_data %>%
                filter(Gender == "Female",  Max_Ben_Bucket != "Unlimited")  %>%
                select(fake_claim_id:ClaimType, Max_Ben_Bucket)

#3 See the top records from 'ctr_data_temp1' table
head(ctr_data_temp1(50))

#4 See the top records from 'ctr_data_temp2' table
head(ctr_data_temp2(50))

#5 See the basic structure of 'ctr_data_temp1' table
str(ctr_data_temp1(50))

#5 See the basic structure of 'ctr_data_temp2' table
str(ctr_data_temp2(50))

```

`@sct`
```{r}
test_error()
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: 8f4c9f87b5
xp: 50
```

## dplyr - Summarize & Group By

`summarise()` is typically used along with the `group_by()`, which collapses each group into a single-row summary of that group. `group_by()` takes as arguments the column names that contain the categorical variables for which we want to calculate the summary statistics.

Type `?summarise()` and `?group_by()` in the R console to read the R documentation for it.

`@instructions`
- The code below comment #1 shows how to group by the column 'Incurred_Year' and Summarize the Exposure and Terminations: 
  Note: Notice the usage of pipe

- Now type the code using `summarize()` and `group_by()` function below comment #2, to group by Gender and IncurredAgeBucket and Summarize the Exposure and Terminations:
  Note: Use same calculations as in the above code.


`@sample_code`
```{r}
#1 From the 'ctr_data' table - Group by the column 'Incurred_Year' and Summarize the Exposure and Terminations 
ctr_data %>%
    group_by(Incurred_Year) %>%
    summarise(Exposure = sum(Terminations)/sum(Exposure),
              Terms = sum(Terminations))

														  
#2 From the 'ctr_data' table - Group by 'Gender' and 'IncurredAgeBucket' and Summarize the Exposure and Terminations 





```


`@solution`
```{r}
#1 From the 'ctr_data' table - Group by the column 'Incurred_Year' and Summarize the Exposure and Terminations 
ctr_data %>%
	group_by(Incurred_Year) %>%
	summarise(Exposure = sum(Terminations)/sum(Exposure),
			  Terms = sum(Terminations))

														  
#2 From the 'ctr_data' table - Group by 'Gender' and 'IncurredAgeBucket' and Summarize the Exposure and Terminations as above
ctr_data %>%
	group_by(Gender, IncurredAgeBucket) %>%
	summarise(Exposure = sum(Terminations)/sum(Exposure),
			  Terms = sum(Terminations))

```

`@sct`
```{r}
test_function("group_by", args = c("Gender", "IncurredAgeBucket"),
              index=2,
              not_called_msg = "Have you used the function to Group the records?",
              args_not_specified = "Have you specified the columns to group by? ",
              incorrect_msg = c("Have you specified Gender to group by?",
                               "Have you specified the IncurredAgeBucket to group by? "))


test_error()
success_msg("Good work!")
```

---
## Data Analysis - Random Numbers

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: 66853dd6c4
```
Random Numbers
==============
A pseudo random number generator is an algorithm based on a starting point called "seed".  To perform an exact replication of your program, specify the seed using the function set.seed(). The argument of set.seed has to be an integer.

The Uniform distributions in R.
==============================
The function dunif() gives the density, punif() gives the distribution function, qunif() gives the quantile function, and runif() generates random deviates.  

Type the `?` in front of the function names like `?dunif()` in the R console to read the R documentation.


`@instructions`

- The code under comment #1, sets the random seed with the argument  4567:

- Now add the missing arguments in function `runif()` under comment #2 to Populate a list of 100 random numbers between 0 & 1:

- The code under comment #3 prints the values in the list :


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
#1 sets the random seed with the argument  4567
set.seed(4567)

#2 Populate a list with 100 uniform random numbers between 0 & 1
My_List <- runif(  ,  , )

#3 Print the values in the list
My_List

```

`@hint`
Use `runif(n, min = , max = )` function to generate n uniform random numbers.


`@solution`
```{r}
#1 sets the random seed with the argument  4567
set.seed(4567)

#2 Populate a list with 100 uniform random numbers between 0 & 1
My_List <- runif(100,0,1)

#3 Print the values in the list
My_List

```


`@sct`
```{r}

#2 Populate a list with 100 uniform random numbers between 0 & 1
test_function("runif", args = c("n","mi","mx"),
              not_called_msg = "Have you used `runif()` to generate the uniform random numbers?",
              args_not_specified = "Have you specified the correct arguments?",
              incorrect_msg = c("Have you specified the total number of random numbers?",
                                "Have you specified the lower limit of the random number?",
                               "Have you specified the upper limit of the random number?"))
              
test_error()
success_msg("Good work!")

```
---

## Data Analysis - Factors

```yaml
type: TabExercise
key: 8d8ffa7d97
lang: r
```

`@pre_exercise_code`
```{r}
library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```

***

```yaml
type: NormalExercise
lang: r
key: 603a7e832f
xp: 50
```

Factors:
Conceptually, factors are variables in R which take on a limited number of different values; such variables are often referred to as categorical variables. One of the most important uses of factors is in statistical modeling; since categorical variables enter into statistical models differently than continuous variables, storing data as factors insures that the modeling functions will treat such data correctly. 

Factors in R are stored as a vector of integer values with a corresponding set of character values to use when the factor is displayed. The factor function is used to create a factor. The only required argument to factor is a vector of values which will be returned as a vector of factor values. Both numeric and character variables can be made into factors, but a factor's levels will always be character values. You can see the possible levels for a factor through the levels command. 
To change the order in which the levels will be displayed from their default sorted order, the levels= argument can be given a vector of all the possible values of the variable in the order you desire. If the ordering should also be used when performing comparisons, use the optional ordered=TRUE argument. In this case, the factor is known as an ordered factor. 

The levels of a factor are used when displaying the factor's values. You can change these levels at the time you create a factor by passing a vector with the new values through the labels= argument. Note that this actually changes the internal levels of the factor, and to change the labels of a factor after it has been created, the assignment form of the levels function is used. To illustrate this point, consider a factor taking on integer values which we want to display as roman numerals. 

From <https://www.stat.berkeley.edu/classes/s133/factors.html> 

Factors in R come in two varieties: ordered and unordered, e.g., {small, medium, large} and {pen, brush, pencil}. For most analyses, it will not matter whether a factor is ordered or unordered. If the factor is ordered, then the specific order of the levels matters (small < medium < large). If the factor is unordered, then the levels will still appear in some order, but the specific order of the levels matters only for convenience (pen, pencil, brush) – it will determine, for example, how output will be printed, or the arrangement of items on a graph.

Another way to change the order is to use relevel() to make a particular level first in the list. (This will not work for ordered factors.)

From <http://www.cookbook-r.com/Manipulating_data/Changing_the_order_of_levels_of_a_factor/> 


`@instructions`
Type the `?` in front of the function names like `?factor()` in the R console to read the R documentation for it. You can do this for any function. The package must be loaded into the session.



`@sample_code`
```{r}
#1 read the R documentation on function factor
?factor()

#2 read the R documentation on function level
?level()

#2 read the R documentation on function relevel
?relevel()

```

***

```yaml
type: NormalExercise
lang: r
xp: 200
skills: 1
key: '5163299628'
```

`@instructions`

- From the code under comment #1 - View the data types of the variables `Gender` & `Max_Ben_Bucket` from table ctr_data: Using function `str()`

- The code under comment #2 - creates a new variable `Gender.f` in ctr_data table to copy the values in `Gender` but as a factor: Using function `factor()`

- The code under comment #3 - To View the order of levels in `Gender.f` in ctr_data table. The Levels are ordered by default based on values in Gender: Using function `level()`

- The code under comment #4 - creates a new variable `Gender.f2` to store the values same as in `Gender` but as a factor. The order is explicitly specified: Using function `factor()`

- The code under comment #5 - To View the order of levels in `Gender.f2` and compare it with the levels `Gender.f` created in #2: Using function `level()`

- The code under comment #6 - Modifies an existing variable `Max_Ben_Bucket` in ctr_data table from data type - character to a factor: Using function `as.factor()`

- The code under comment #7 - To View the order of levels in `Max_Ben_Bucket` in ctr_data table. The Levels are ordered by default based on values in `Max_Ben_Bucket`: Using function `level()`

- The code under comment #8 - To make the value "Unlimited" in `Max_Ben_Bucket` in ctr_data table as the base value (level 1): Using function `relevel()`

- Add code under comment #9 to make a new variable `ClaimType.f` as a factor using the `factor()` function and populate the value from `ClaimType` in ctr_data table. Do not make the new column, `ClaimType.f` as a column in table ctr_data. Order the factors in the order "HHC", "NF", "ALF", "Unk":

- Add code under comment #10 to View the order of levels in `ClaimType.f`:

- Complete the function under comment #11 to change the base level of variable `ClaimType.f` to "NF":

- Add code under comment #12 to View the order of levels in `ClaimType.f`:


`@sample_code`
```{r}
#1 View the data types of the variables Gender & Max_Ben_Bucket in table ctr_data
str(ctr_data)

#2 Creates a variable `Gender.f` in ctr_data table from column Gender and store the values as a factor  
ctr_data$Gender.f <- factor(ctr_data$Gender, ordered=TRUE)

#3 View the order of levels in `Gender.f` in ctr_data table
levels(ctr_data$Gender.f )

#4 Create a new factor variable `Gender.f2` to store the values same as in "Gender". The order is explicitly specified
Gender.f2 <- factor(ctr_data$Gender, labels= c("Male","Female"), ordered=TRUE)

#5 View the order of levels in `Gender.f2` created in above step and compare it with the levels `Gender.f` from code #2
levels(ctr_data$Gender.f2)

#6 Change the existing character variable "Max_Ben_Bucket" in ctr_data table to a factor data type
ctr_data$Max_Ben_Bucket <- as.factor(ctr_data$Max_Ben_Bucket)

#7 View the order of levels in "Max_Ben_Bucket" in ctr_data table. 
levels(ctr_data$Max_Ben_Bucket)

#8 Make the value "Unlimited" in `Max_Ben_Bucket` in ctr_data table as the base value
ctr_data$Max_Ben_Bucket <- relevel(ctr_data$Max_Ben_Bucket, "Unlimited")

#9 Make a new variable `ClaimType.f` as a factor and populate the value from `ClaimType` in ctr_data table. 
# Do Not add `ClaimType.f` as a column in the table ctr_data. Order the factors in the order "HHC", "NF", "ALF", "Unk"


#10 View the order of levels in `ClaimType.f`
levels( )

#11 Change the base level of variable `ClaimType.f` to "NF"


```

`@hint`
#9 Use the function `factor(variable, labels= , ordered=)`. 
#10 View the order of levels - levels(variable)
#11 Change the base level of relevel(variable, base-value)


`@solution`
```{r}
#1 View the data types of the variables Gender & Max_Ben_Bucket in table ctr_data
str(ctr_data)

#2 Creates a variable `Gender.f` in ctr_data table from column Gender and store the values as a factor  
ctr_data$Gender.f <- factor(ctr_data$Gender, ordered=TRUE)

#3 View the order of levels in `Gender.f` in ctr_data table
levels(ctr_data$Gender.f )

#4 Create a new factor variable `Gender.f2` to store the values same as in "Gender". The order is explicitly specified
Gender.f2 <- factor(ctr_data$Gender, labels= c("Male","Female"), ordered=TRUE)

#5 View the order of levels in `Gender.f2` created in above step and compare it with the levels `Gender.f` from code #2
levels(ctr_data$Gender.f2)

#6 Change the existing character variable "Max_Ben_Bucket" in ctr_data table to a factor data type
ctr_data$Max_Ben_Bucket <- as.factor(ctr_data$Max_Ben_Bucket)

#7 View the order of levels in "Max_Ben_Bucket" in ctr_data table. 
levels(ctr_data$Max_Ben_Bucket)

#8 Make the value "Unlimited" in `Max_Ben_Bucket` in ctr_data table as the base value
ctr_data$Max_Ben_Bucket <- relevel(ctr_data$Max_Ben_Bucket, "Unlimited")

#9 Make a new variable `ClaimType.f` as a factor and populate the value from `ClaimType` in ctr_data table. 
# Do Not add `ClaimType.f` as a column in the table ctr_data. Order the factors in the order "HHC", "NF", "ALF", "Unk"
ClaimType.f  <- factor(ctr_data$ClaimType, labels= c("HHC", "NF", "ALF", "Unk"))

#10 View the order of levels in `ClaimType.f`
levels(ClaimType.f)

#11 Change the base level of variable `ClaimType.f` to "NF"
relevel(ClaimType.f, "NF")

```


`@sct`
```{r}

#9 Make a new variable `ClaimType.f` as a factor and populate the value from `ClaimType` in ctr_data table. 
# Do Not add `ClaimType.f` as a column in the table ctr_data. Order the factors in the order "HHC", "NF", "ALF", "Unk"
test_function("factor", index = 3,
              not_called_msg = "Have you used `factor()` to create the factor variable?")

							   
#10 View the order of levels in `ClaimType.f`
test_function("levels", args = "ClaimTypef", index=4,
              not_called_msg = "Have you used `level()` function to view the levels in the variable?",
              args_not_specified = "Have you specified the correct variable name in the argument?",
              incorrect_msg = c("Have you specified the variable name in the argument?"))
							   
#11 Change the base level of variable `ClaimType.f` to "NF"
test_function("relevel", args = c("ClaimTypef","NFvalue"), index=2,
              not_called_msg = "Have you used `relevel()` to change the levels in a factor variable?",
              args_not_specified = "Have you specified the variable name and the base level value?",
              incorrect_msg = c("Have you used factor variable name to change the level?",
                               "Have you specified the base level value?"))
     
test_error()
success_msg("Good work!")

```

