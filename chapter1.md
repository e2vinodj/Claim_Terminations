---
title       : Explore the workshop data
description : Using R to explore the Claim Termination file going to be used in the workshop.

---
## Workshop Prework

```yaml
type: NormalExercise
key: 97c8cc3611
lang: r
skills: 1
```
This course is to allow the workshop attendees to apply basic R programming skills and obtain an understanding of data used at the workshop for application of Predictive Analysis for Claim Termination study.

`@instructions`

In the window "script.R" you should type R code to solve the exercises. When you hit the 'Submit Answer' button, every line of code is interpreted and executed by R and you will get a message whether or not your code was correct. The output of your R code is shown in the console in the lower right corner. You can hit the 'Hint' button for hints.

You can also execute R commands straight in the console. This is a good way to experiment with R code, as your submission is not checked for correctness.

Note: All the packages needed for the exercises will be added to the R sessions in this course for your use.

- Type the following in the script editor below the second comment: a <- a + 6. 


`@pre_exercise_code`
```{r}
library(data.table)

```

`@sample_code`
```{r}
# assign 5 to the varaible 'a'
a <- 5

# Add 6 to the varaible 'a'


# show the value in 'a'
a
```

`@solution`
```{r}
# assign 5 to the varaible 'a'
a <- 5

# Add 6 to the varaible 'a'
a <- a + 6

# show the value in 'a'
a
```

`@sct`
```{r}
test_error()
success_msg("Good work!")
```



---
## Data Cleaning / Exploration and Feature Engineering

```yaml
type: NormalExercise
lang: r
xp: 100
skills: 1
key: 90828da696

```
In the next few exercises, we will explore the data file that will be used at the the workshop. Claim termination rate (CTR) data has been extracted from the SOA experience database and modified for workshop purposes.The modified data is downloaded here as a R Data table and is named 'ctr_data'.

'data.table' is an R extension of 'data.frame' and allows for faster processing and manipulation of data. Like data frames, it can have columns with different modes (numeric, character, factor, etc.). This is similar to SAS and SPSS datasets. The package data.table must be loaded in the R workspace to use the data tables and related functions. We have already added this package to the session by using function `library(data.table)`.


`@instructions`

- Type the following in the script editor: str(ctr_data). The str function will show the basic structure of the workshop data file.

- Use the `head()` function below the second comment. This will print the top rows from the table. Take time to scan through the data.


`@hint`
Use `str(table_name)` function to view the basic structure in R.

Use `head(table_name)` function to view the top rows from a data frame or data table


`@pre_exercise_code`
```{r}

library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6345/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)

```


`@sample_code`
```{r}
# View the basic structure of the claim termination data table 'ctr_data'


# View the top rows from the claim termination data table 'ctr_data'


```


`@solution`
```{r}
# View the basic structure of the claim termination data table 'ctr_data'
str(ctr_data)

# View the top rows from the claim termination data table 'ctr_data'
head(ctr_data)

```


`@sct`
```{r}

# View the basic structure of the claim termination data table 'ctr_data'
test_function("str", not_called_msg = "You didn't call `str()`!")
              
# View the top rows from the claim termination data table 'ctr_data'
test_function("head", not_called_msg = "You didn't call `head()`!")

test_error()
success_msg("Good work!")

```

---
## Data Cleaning / Exploration

```yaml
type: MultipleChoiceExercise
lang: r
xp: 50
skills: 1
key: ad35373459
```


Name the variable that contains the state code in the table?.

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
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6345/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)

```


`@sct`
```{r,eval=FALSE}
msg1 = "Incorrect! Check the R restrictions on variable references"
msg2 = "Incorrect! Check the usage of $ in reference to R table varaiables"
msg3 = "Correct! The R vaiables are case sensitive. The table does not have $ prefix before the variable StateAbbr"
msg4 = "Incorrect! Check the usage of $ in reference to R table varaiables"

test_mc(correct=3, feedback_msgs = c(msg1, msg2, msg3, msg4))

```

---
## Perform data analysis

```yaml
type: TabExercise
key: 2678bc2bb7
lang: r
xp: 100
```


`@pre_exercise_code`
```{r}
library(data.table)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6345/datasets/ILTCI%20PM%20workshop%20CTR%20data.zip", temp)
unzip(temp,"ILTCI PM workshop CTR data.csv",overwrite=TRUE)
ctr_data  <-fread("ILTCI PM workshop CTR data.csv", sep=",", header=TRUE) 
unlink(temp)
```

***

### Sub Heading 2

```yaml
type: NormalExercise
xp: 100
key: 7b98b4e0fd
```


`@instructions`

Take time to know the data and gain insights into your data.

The table function is a very basic, but essential, function to master while performing interactive data analyses. It simply creates tabular results of categorical variables. However, when combined with the powers of logical expressions in R, you can gain even more insights into your data, including identifying potential problems.

- Type: table(ctr_data$Cov_Type_Bucket) - This will display the distrubution of Coverage Types.

- Type: table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator) - This will display the distrubution of Coverage Types and the Group Indicator.

- Now Type the table function to display the distrubution of Coverage Types, the Group Indicator and the gender


`@sample_code`
```{r}
# View a tabular summary by Coverage type
table(ctr_data$Cov_Type_Bucket)

# View a tabular summary by Coverage type and Group Indicator
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator)

# View a tabular summary by Coverage type, Group Indicator and Gender


```

`@solution`
```{r}
# View a tabular summary by Coverage type
table(ctr_data$Cov_Type_Bucket)

# View a tabular summary by Coverage type and Group Indicator
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator)

# View a tabular summary by Coverage type, Group Indicator and Gender
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator, ctr_data$Gender )

```

`@hint`
Add Gender as a third argument in the table() function

`@sct`
```{r}
# View a tabular summary by Coverage type
test_function("table", not_called_msg = "You didn't call `table()`!")

# View a tabular summary by Coverage type and Group Indicator
test_function("table", not_called_msg = "You didn't call `table()`!")

# View a tabular summary by Coverage type, Group Indicator and Gender
test_function("table", not_called_msg = "You didn't call `table()`!")

test_error()
success_msg("Good work!")

```

***

### Examine a claim

```yaml
type: NormalExercise
xp: 50
key: 32fc0c6062
```

`@instructions`

Running the instruction will display the records from the ctr_data table having value 16430 in the column fake_claim_id. 

The column 'ClaimDuration' shows the claim duration in months. The 'Terminations' column with 1, identifies that the claim has been terminated. Look at the various columns and try to identify the columns that could be used as a Response variable for the claim termination study.

`@sample_code`
```{r}
#View the records for Claim # 16430

ctr_data[fake_claim_id==16430,]

#Create a new data table 'Test_Data' with  the records for Incurred_Year >= 2000

```
