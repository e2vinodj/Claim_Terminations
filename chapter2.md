---
title       : Prework2-Cox/ Poisson Modeling
description : Cox/ Poisson Modeling.
---

## Data Cleaning, Exploration and Feature Engineering


```yaml
type: TabExercise
key: 8fc72dc59a
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
key: bfa942ef77
xp: 25
```


`@instructions`
	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

- Read the comments and the code. 

- In this chapter, - First select sections of the code by highlight them. Then click on the "RUN CODE" button to run the Code in small chunks. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as one R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



`@sample_code`
```{r}
# Read the R documentation on the package related functions.

```

***

```yaml
type: NormalExercise
lang: r
key: 2ad2d696ad
xp: 100
```

`@instructions`
- We are exploring the data in the ctr_data table:
	   To understand the data and relationships.
	   To gather insights.


- Read the comments and the code. Run the Code and Observe the results.
	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

- Read the comments and the code. 

- First select sections of the code by highlight them. Then click on the "RUN CODE" button to run the Code in small chunks. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as one R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.


Note: Refer the code in "0100 - Data Explore.r"

`@sample_code`
```{r}
##########################################################################################
# Purpose: To understand how to execute the different ways
#             of viewing data and data summaries in R
##########################################################################################

# 1.0.1 - load the packages
library(dplyr)
library(data.table)

# 1.0.4 - import the data
# Datacamp session has already imported the data into a table name ctr_data

# 1.0.5 - view the data to see what we're working with
names(ctr_data)
str(ctr_data)
glimpse(ctr_data)
class(ctr_data)
head(ctr_data, 10)

# 1.0.6 - view basic data summaries to see if there are any problems with the data
summary(ctr_data)
table(ctr_data$Cov_Type_Bucket)
table(ctr_data$GroupIndicator)
table(ctr_data$Gender)
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator)
table(ctr_data$Cov_Type_Bucket, ctr_data$GroupIndicator, ctr_data$Gender)
table(ctr_data$Diagnosis_Category)

# 1.0.7 - dig into one of the fields, the tax-qualified status; status of unknown is frequent
tq_table <- table(ctr_data$TQ_Status)
tq_table / sum(tq_table)


```



`@sct`
```{r}
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: af5130f061
xp: 100
```


`@instructions`

- Now we are going to visually explore the data in the ctr_data table:  By Plotting graphs	   

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0100 - Data Explore.r"

`@sample_code`
```{r}
# 1.0.8 - graph some of the fields for some basic data integrity and sanity checks
boxplot(ctr_data$ClaimDuration,
        main = "Boxplot of Data by Claim Duration",
        ylab = "Claim Duration"
        )

boxplot(ctr_data$Exposure,
        main = "Boxplot of Data by Exposure",
        ylab = "Exposure"
        )

situs_counts <- table(ctr_data$ClaimType)

barplot(situs_counts,
        main = "Bar Chart of Situs Counts",
        ylab = "Counts"
        )

tq_counts <- table(ctr_data$TQ_Status)

barplot(tq_counts,
        main = "Bar Chart of TQ Status Counts",
        ylab = "Counts")

# 1.0.9 - perform a basic summary of hazard rates, and view the results for reasonability
term_stats <- ctr_data %>%
                    select(ClaimDuration,
                           Exposure,
                           Terminations
                           ) %>%
                    group_by(ClaimDuration
                           ) %>%
                    summarise(SumExposure = sum(Exposure),
                              SumTerminations = sum(Terminations),
                              term_rates = SumTerminations / SumExposure
                            )

#******  Uncomment the below View() and run it to view the records in table term_stats.  ******
#    View(term_stats)

# 1.0.10 - plot the hazard rate summary, and view it for reasonability
attach(term_stats)

plot(y = term_rates[1:120], 
     x = ClaimDuration[1:120],
     xlab = "Claim Duration",
     ylab = "Term Rate",
     type = "l",
     main = "Hazard rates by claim duration",
     col = "blue")

# 1.0.11 - add in a smoothed line to remove some of the noise
lines(smooth(term_rates[1:120]), 
      col = "red")

detach(term_stats)

# 1.0.12 - credibility view - do you have enough termination data to perform an analysis?
cred_stats <- ctr_data %>%
                    select(Gender,
                           ClaimType,
                           IncurredAgeBucket,
                           Terminations
                            ) %>%
                    group_by(Gender,
                             ClaimType,
                             IncurredAgeBucket
                            ) %>%
                    summarise(SumTerminations = sum(Terminations)
                            )

# 1.0.13 - view the results for a judgment call on credibility
#******  Uncomment the below View() and run it.  ******

#    View(cred_stats)

```


`@solution`
```{r}
# 1.0.8 - graph some of the fields for some basic data integrity and sanity checks
boxplot(ctr_data$ClaimDuration,
        main = "Boxplot of Data by Claim Duration",
        ylab = "Claim Duration"
        )

boxplot(ctr_data$Exposure,
        main = "Boxplot of Data by Exposure",
        ylab = "Exposure"
        )

situs_counts <- table(ctr_data$ClaimType)

barplot(situs_counts,
        main = "Bar Chart of Situs Counts",
        ylab = "Counts"
        )

tq_counts <- table(ctr_data$TQ_Status)

barplot(tq_counts,
        main = "Bar Chart of TQ Status Counts",
        ylab = "Counts")

# 1.0.9 - perform a basic summary of hazard rates, and view the results for reasonability
term_stats <- ctr_data %>%
                    select(ClaimDuration,
                           Exposure,
                           Terminations
                           ) %>%
                    group_by(ClaimDuration
                           ) %>%
                    summarise(SumExposure = sum(Exposure),
                              SumTerminations = sum(Terminations),
                              term_rates = SumTerminations / SumExposure
                            )

#******  Uncomment the below View() and run it to view the records in table term_stats.  ******
#    View(term_stats)

# 1.0.10 - plot the hazard rate summary, and view it for reasonability
attach(term_stats)

plot(y = term_rates[1:120], 
     x = ClaimDuration[1:120],
     xlab = "Claim Duration",
     ylab = "Term Rate",
     type = "l",
     main = "Hazard rates by claim duration",
     col = "blue")

# 1.0.11 - add in a smoothed line to remove some of the noise
lines(smooth(term_rates[1:120]), 
      col = "red")

detach(term_stats)

# 1.0.12 - credibility view - do you have enough termination data to perform an analysis?
cred_stats <- ctr_data %>%
                    select(Gender,
                           ClaimType,
                           IncurredAgeBucket,
                           Terminations
                            ) %>%
                    group_by(Gender,
                             ClaimType,
                             IncurredAgeBucket
                            ) %>%
                    summarise(SumTerminations = sum(Terminations)
                            )

# 1.0.13 - view the results for a judgment call on credibility
#******  Uncomment the below View() and run it.  ******

#    View(cred_stats)

```

`@sct`
```{r}
success_msg("Good work!")
```


---

## Examining and Creating Holdout Data

```yaml
type: TabExercise
key: cd957d088d
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
key: 41dc02ec1c
xp: 100
```


`@instructions`

- Now we should examine the data on how to create Training & Validation sample data:
- Split the dataset into a 60% training sample, a 20% in-time validation sample, and
  20% out-of-time testing sample

- Read the comments and the code. Run the Code and Observe the results.
 	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.


Note: Refer the code in "0150 - Separate data into train val test.r"

`@sample_code`
```{r}
##########################################################################################
# Purpose: Split the dataset into a 60% training sample, a 20% in-time validation sample, and a 
# 20% out-of-time testing sample. 
##########################################################################################

# 1.5.1 - load the packages and read in data
library(data.table)
library(dplyr)
library(ggplot2)
library(plotly)
library(lubridate)

# 1.0.4 - import the data
# Datacamp session has already imported the data into a table name ctr_data

# 1.5.2 - Add a current date variable so we can create an out-of-time holdout
# Assume that claims were incurred at the middle of year, and are bounded between 2000 and 2011 
ctr_data <- ctr_data %>%
  mutate(incurred_date = ymd(paste(Incurred_Year, "-07-01", sep="")),
         study_start = incurred_date %m+% months(start_duration -1),
         study_start_date = if_else(study_start < ymd("2000-01-01"),
                                ymd("2000-01-01"),
                                if_else(study_start > ymd("2011-12-31"),
                                        ymd("2011-01-01"),
                                        study_start
                                        )
                                ),
         current_date_raw = study_start_date %m+% months(ClaimDuration - start_duration),
         current_date = if_else(current_date_raw > ymd("2011-12-31"),
                                ymd("2011-12-31"),
                                current_date_raw 
                                )                          
         ) %>% 
  select(-study_start, - current_date_raw) # remove extra variables

# Let's check out the hazard rates by current year
ctr_data %>%
  mutate(year = year(current_date))%>%
  group_by(year) %>%
  summarise(prior_rate = sum(Terminations)/sum(Exposure),
            terms = sum(Terminations)
            )

# It looks like 2011 is higher than usual
# Let's see what happens when we drop exposures greater than 2011-06-30
ctr_data %>%
  filter( current_date < ymd("2011-07-01")) %>%
  mutate(year = year(current_date))%>%
  group_by(year) %>%
  summarise(prior_rate = sum(Terminations)/sum(Exposure),
            terms = sum(Terminations)
            )

# Those rates are a bit more reasonable for workshop purposes 
# Let's drop claims greater than 2011-06-30
ctr_data <- ctr_data %>%
  filter( current_date < ymd("2011-07-01"))

```


`@solution`
```{r}
##########################################################################################
# Purpose: Split the dataset into a 60% training sample, a 20% in-time validation sample, and a 
# 20% out-of-time testing sample. 
##########################################################################################

# 1.5.1 - load the packages and read in data
library(data.table)
library(dplyr)
library(ggplot2)
library(plotly)
library(lubridate)

# 1.0.4 - import the data
# Datacamp session has already imported the data into a table name ctr_data

# 1.5.2 - Add a current date variable so we can create an out-of-time holdout
# Assume that claims were incurred at the middle of year, and are bounded between 2000 and 2011 
ctr_data <- ctr_data %>%
  mutate(incurred_date = ymd(paste(Incurred_Year, "-07-01", sep="")),
         study_start = incurred_date %m+% months(start_duration -1),
         study_start_date = if_else(study_start < ymd("2000-01-01"),
                                ymd("2000-01-01"),
                                if_else(study_start > ymd("2011-12-31"),
                                        ymd("2011-01-01"),
                                        study_start
                                        )
                                ),
         current_date_raw = study_start_date %m+% months(ClaimDuration - start_duration),
         current_date = if_else(current_date_raw > ymd("2011-12-31"),
                                ymd("2011-12-31"),
                                current_date_raw 
                                )                          
         ) %>% 
  select(-study_start, - current_date_raw) # remove extra variables

# Let's check out the hazard rates by current year
ctr_data %>%
  mutate(year = year(current_date))%>%
  group_by(year) %>%
  summarise(prior_rate = sum(Terminations)/sum(Exposure),
            terms = sum(Terminations)
            )

# It looks like 2011 is higher than usual
# Let's see what happens when we drop exposures greater than 2011-06-30
ctr_data %>%
  filter( current_date < ymd("2011-07-01")) %>%
  mutate(year = year(current_date))%>%
  group_by(year) %>%
  summarise(prior_rate = sum(Terminations)/sum(Exposure),
            terms = sum(Terminations)
            )

# Those rates are a bit more reasonable for workshop purposes 
# Let's drop claims greater than 2011-06-30
ctr_data <- ctr_data %>%
  filter( current_date < ymd("2011-07-01"))

```

`@sct`
```{r}
success_msg("Good work!")
```

***

```yaml
type: NormalExercise
lang: r
key: 1960ff7df1
xp: 100
```


`@instructions`

- Now we are going to split the data using the insights we obtained from our previous exercises.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0150 - Separate data into train val test.r"

`@sample_code`
```{r}
# 1.5.3 - Now we can split the data
# Find the date the corresponds to the 80th percentile for the out-of-time holdout
(test_cut_date <- quantile(ctr_data$current_date, .80, type = 1))

# Grab all the distinct claim IDs in the data and tag on random numbers for sampling
# Do this at the claim level, as We do not want to split a claim across the training and validation data
distinct_claim_ids <- ctr_data %>% 
                            distinct(fake_claim_id)

# Use a seed to make results reproducible
set.seed(123)
rand_claim_ids <- data.table(distinct_claim_ids, 
                             RandNum = runif(nrow(distinct_claim_ids), 0, 1)
                             )

# Join random number onto data
ctr_data <- left_join(ctr_data, 
                      rand_claim_ids, 
                      by="fake_claim_id"
                      ) 

# Tag on sample flags
ctr_data <- ctr_data %>% 
  mutate(Sample = ifelse(current_date > test_cut_date,
                         "testing", # 20%
                         ifelse(RandNum > 3/4, 
                                "validation", # 60% = 80% * (3/4)
                                "training" # 20%
                                )
                         )
         ) %>%
  select(-RandNum)

# 1.5.4 - Now let's check out the distribution of our samples by year using barplot()
?barplot() # Use ? before function to pull up help window

# added in color field to the data
# check out list of colors here Www.stat.columbia.edu/~tzheng/files/Rcolor.pdf
ctr_data_color <- ctr_data %>%
  mutate(SampleColor = ifelse(Sample == "validation",
                              "orange", 
                              ifelse(Sample == "training",
                                     "blue4", 
                                     "chartreuse4"
                                     )
                              )
         )



# Store year and color field in a table
date_plottable <- table(ctr_data_color$SampleColor, 
                        year(ctr_data_color$current_date)
                        )

# Now let's plot this data in the Plots window
barplot(date_plottable,
        col = rownames(date_plottable),
        las = 2,
        main = "Exposure over time",
        xlab="Year",
        legend.text=c("Training", "Testing", "Validation"),
        args.legend = list(x = "topleft")
        )

# Do a quick summary of the data
ctr_data %>% 
  group_by(Sample) %>% 
  summarise(Expo_Dist = sum(Exposure)/sum(ctr_data$Exposure),
            Exposures = sum(Exposure), 
            Terminations = sum(Terminations),
            Hazard_rate = sum(Terminations)/sum(Exposure)
            )                                                         

# Plot actual termination rates
raw_hazard <- ctr_data %>%
  group_by(ClaimDuration,
           Sample) %>%
  summarise(raw_hazard_rate = sum(Terminations)/sum(Exposure))

ggplot() + 
  geom_line(data = raw_hazard, 
            aes(x =ClaimDuration , 
                y = raw_hazard_rate, 
                colour = factor(c(Sample))
                )
            )+ 
  ggtitle("Actual termination hazard rates")

# That plot is difficult to analyze because of the scale
# Let's Wrap the graph in ggplotly() to make it interactive in the Viewer window that can zoom in
ggplotly(ggplot() +
           geom_line(data = raw_hazard, 
                     aes(x =ClaimDuration , 
                         y = raw_hazard_rate, 
                         colour = factor(c(Sample))
                         )
                     )+
           ggtitle("Actual termination hazard rates")
          )

# Terminations rates fluctuate after 100 and even more so after 150
# Investigate and understand reason for spikes
# For purposes of workshop, we'll chalk up to limited exposure in the tail
# Care will be needed when analyzing the model in the tail and 
#   judgement is needed in how to use/extrapolate where there is limited data

```


`@solution`
```{r}
# 1.5.3 - Now we can split the data
# Find the date the corresponds to the 80th percentile for the out-of-time holdout
(test_cut_date <- quantile(ctr_data$current_date, .80, type = 1))

# Grab all the distinct claim IDs in the data and tag on random numbers for sampling
# Do this at the claim level, as We do not want to split a claim across the training and validation data
distinct_claim_ids <- ctr_data %>% 
                            distinct(fake_claim_id)

# Use a seed to make results reproducible
set.seed(123)
rand_claim_ids <- data.table(distinct_claim_ids, 
                             RandNum = runif(nrow(distinct_claim_ids), 0, 1)
                             )

# Join random number onto data
ctr_data <- left_join(ctr_data, 
                      rand_claim_ids, 
                      by="fake_claim_id"
                      ) 

# Tag on sample flags
ctr_data <- ctr_data %>% 
  mutate(Sample = ifelse(current_date > test_cut_date,
                         "testing", # 20%
                         ifelse(RandNum > 3/4, 
                                "validation", # 60% = 80% * (3/4)
                                "training" # 20%
                                )
                         )
         ) %>%
  select(-RandNum)

# 1.5.4 - Now let's check out the distribution of our samples by year using barplot()
?barplot() # Use ? before function to pull up help window

# added in color field to the data
# check out list of colors here Www.stat.columbia.edu/~tzheng/files/Rcolor.pdf
ctr_data_color <- ctr_data %>%
  mutate(SampleColor = ifelse(Sample == "validation",
                              "orange", 
                              ifelse(Sample == "training",
                                     "blue4", 
                                     "chartreuse4"
                                     )
                              )
         )



# Store year and color field in a table
date_plottable <- table(ctr_data_color$SampleColor, 
                        year(ctr_data_color$current_date)
                        )

# Now let's plot this data in the Plots window
barplot(date_plottable,
        col = rownames(date_plottable),
        las = 2,
        main = "Exposure over time",
        xlab="Year",
        legend.text=c("Training", "Testing", "Validation"),
        args.legend = list(x = "topleft")
        )

# Do a quick summary of the data
ctr_data %>% 
  group_by(Sample) %>% 
  summarise(Expo_Dist = sum(Exposure)/sum(ctr_data$Exposure),
            Exposures = sum(Exposure), 
            Terminations = sum(Terminations),
            Hazard_rate = sum(Terminations)/sum(Exposure)
            )                                                         

# Plot actual termination rates
raw_hazard <- ctr_data %>%
  group_by(ClaimDuration,
           Sample) %>%
  summarise(raw_hazard_rate = sum(Terminations)/sum(Exposure))

ggplot() + 
  geom_line(data = raw_hazard, 
            aes(x =ClaimDuration , 
                y = raw_hazard_rate, 
                colour = factor(c(Sample))
                )
            )+ 
  ggtitle("Actual termination hazard rates")

# That plot is difficult to analyze because of the scale
# Let's Wrap the graph in ggplotly() to make it interactive in the Viewer window that can zoom in
ggplotly(ggplot() +
           geom_line(data = raw_hazard, 
                     aes(x =ClaimDuration , 
                         y = raw_hazard_rate, 
                         colour = factor(c(Sample))
                         )
                     )+
           ggtitle("Actual termination hazard rates")
          )

# Terminations rates fluctuate after 100 and even more so after 150
# Investigate and understand reason for spikes
# For purposes of workshop, we'll chalk up to limited exposure in the tail
# Care will be needed when analyzing the model in the tail and 
#   judgement is needed in how to use/extrapolate where there is limited data

```

`@sct`
```{r}
success_msg("Good work!")
```



---

## Visualize Kaplan-Meier Survival curves and Create Nelson-Aalen views

```yaml
type: TabExercise
key: 3e74e4f1bd
lang: r
```

`@pre_exercise_code`
```{r}
library(data.table)
library(dplyr)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ctr_data.RData", temp)
unzip(temp,"ctr_data.RData",overwrite=TRUE)
load("ctr_data.RData")
unlink(temp)

```
***

```yaml
type: NormalExercise
lang: r
key: 34d0fa9a0c
xp: 150
```


`@instructions`

- Now let us produce and visualize Kaplan-Meier survival curves.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0200 - KM-NA.r"

`@sample_code`
```{r}
##########################################################################################
# Purpose: To be able to produce and visualize Kaplan-Meier survival curves
#             and to produce simplified Nelson-Aalen test views
##########################################################################################

# 2.0.1 - load the packages
library(survival)
library(stringr)
library(broom)
library(dplyr)

# 2.0.4 - import table - The data produced earlier in chapter-2 is loaded into this session and ready for use. Table name - `ctr_data`

# 2.0.5 - recode ClaimDuration as a numeric variable, which is needed in the next data manipulation step
ctr_data$ClaimDuration.n <- as.numeric(ctr_data$ClaimDuration)

# 2.0.6 - format beginning and end times for use in K-M analysis, and for later use in the Cox regression
ctr_data_trn <- ctr_data %>%
                        mutate(beg_dur_ph = case_when(.$start_duration == .$end_duration ~
                                                        .$ClaimDuration.n - 1,
                                                      .$ClaimDuration == .$start_duration ~
                                                        .$ClaimDuration.n - .$Exposure,
                                                      .$ClaimDuration == .$end_duration ~
                                                        .$ClaimDuration.n - 1,
                                                      (.$ClaimDuration > .$start_duration) & (.$ClaimDuration < .$end_duration) ~
                                                        .$ClaimDuration.n - 1
                                                      ),
                                end_dur_ph = beg_dur_ph + .$Exposure
                               ) %>%
                        filter(GroupIndicator == "Ind",
                               Cov_Type_Bucket == "Comprehensive",
                               Sample == "training"
                              )

# 2.0.7 - filter the survival data to include only females for base level characteristics,
#         and another set to include both genders
ctr_data_trn_f <- ctr_data_trn %>%
                        filter(Gender == "Female",
                               IncurredAgeBucket == "80 to 84",
                               Max_Ben_Bucket != "Unlimited",
                               ClaimType == "HHC",
                               Diagnosis_Category != (str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental")
                               )

ctr_data_trn_b <- ctr_data_trn %>%
                        filter(IncurredAgeBucket == "80 to 84",
                               Max_Ben_Bucket != "Unlimited",
                               ClaimType == "HHC",
                               Diagnosis_Category != (str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental")
                               )

# 2.0.8 - use ctr_data_trn_f as the prefix for the next step
attach(ctr_data_trn_f)

# 2.0.9 - create a survival object for the female gender, which is needed to produce the K-M curve later
surv_object_f <- Surv(time = beg_dur_ph,
                      time2 = end_dur_ph,
                      event = Terminations,
                      type = "counting"
                      )

# 2.0.10 - remove association
detach(ctr_data_trn_f)

# 2.0.11 - use ctr_data_trn_b as the prefix for the two gender view
attach(ctr_data_trn_b)

# 2.0.12 - create survival object for both genders, which is needed to produce the K-M curves later
surv_object_b <- Surv(time = beg_dur_ph,
                      time2 = end_dur_ph,
                      event = Terminations,
                      type = "counting"
                      )

# 2.0.13 - remove association
detach(ctr_data_trn_b)

# 2.0.14 - calculate the K-M curves with the prior survival objects
surv_fit_f <- survfit(surv_object_f ~ 1
                      )

surv_fit_b <- survfit(surv_object_b ~ Gender,
                      data = ctr_data_trn_b
                      )

# 2.0.15 - plot the K-M survival curve, female only, then both males and females - are they reasonable?
plot(surv_fit_f,
        ylim=c(0,1),
        xlab="Months",
        ylab="Proportion not Terminated",
        col=c("black", "red", "blue")
     )
legend('topright', c("Females", "Lower 95%CI", "Upper 95%CI"), col=c("black", "red", "blue"), lty=1:3, cex=0.8)

plot(surv_fit_b,
       ylim=c(0,1),
       xlab="Months",
       ylab="Proportion not Terminated",
       col=c("red", "blue")
     )
legend('topright', c("Females", "Males"), col=c("red", "blue"), lty=1:3, cex=0.8)

```


`@solution`
```{r}
##########################################################################################
# Purpose: To be able to produce and visualize Kaplan-Meier survival curves
#             and to produce simplified Nelson-Aalen test views
##########################################################################################

# 2.0.1 - load the packages
library(survival)
library(stringr)
library(broom)
library(dplyr)

# 2.0.4 - import table - The data produced earlier in chapter-2 is loaded into this session and ready for use. Table name - `ctr_data`

# 2.0.5 - recode ClaimDuration as a numeric variable, which is needed in the next data manipulation step
ctr_data$ClaimDuration.n <- as.numeric(ctr_data$ClaimDuration)

# 2.0.6 - format beginning and end times for use in K-M analysis, and for later use in the Cox regression
ctr_data_trn <- ctr_data %>%
                        mutate(beg_dur_ph = case_when(.$start_duration == .$end_duration ~
                                                        .$ClaimDuration.n - 1,
                                                      .$ClaimDuration == .$start_duration ~
                                                        .$ClaimDuration.n - .$Exposure,
                                                      .$ClaimDuration == .$end_duration ~
                                                        .$ClaimDuration.n - 1,
                                                      (.$ClaimDuration > .$start_duration) & (.$ClaimDuration < .$end_duration) ~
                                                        .$ClaimDuration.n - 1
                                                      ),
                                end_dur_ph = beg_dur_ph + .$Exposure
                               ) %>%
                        filter(GroupIndicator == "Ind",
                               Cov_Type_Bucket == "Comprehensive",
                               Sample == "training"
                              )

# 2.0.7 - filter the survival data to include only females for base level characteristics,
#         and another set to include both genders
ctr_data_trn_f <- ctr_data_trn %>%
                        filter(Gender == "Female",
                               IncurredAgeBucket == "80 to 84",
                               Max_Ben_Bucket != "Unlimited",
                               ClaimType == "HHC",
                               Diagnosis_Category != (str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental")
                               )

ctr_data_trn_b <- ctr_data_trn %>%
                        filter(IncurredAgeBucket == "80 to 84",
                               Max_Ben_Bucket != "Unlimited",
                               ClaimType == "HHC",
                               Diagnosis_Category != (str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental")
                               )

# 2.0.8 - use ctr_data_trn_f as the prefix for the next step
attach(ctr_data_trn_f)

# 2.0.9 - create a survival object for the female gender, which is needed to produce the K-M curve later
surv_object_f <- Surv(time = beg_dur_ph,
                      time2 = end_dur_ph,
                      event = Terminations,
                      type = "counting"
                      )

# 2.0.10 - remove association
detach(ctr_data_trn_f)

# 2.0.11 - use ctr_data_trn_b as the prefix for the two gender view
attach(ctr_data_trn_b)

# 2.0.12 - create survival object for both genders, which is needed to produce the K-M curves later
surv_object_b <- Surv(time = beg_dur_ph,
                      time2 = end_dur_ph,
                      event = Terminations,
                      type = "counting"
                      )

# 2.0.13 - remove association
detach(ctr_data_trn_b)

# 2.0.14 - calculate the K-M curves with the prior survival objects
surv_fit_f <- survfit(surv_object_f ~ 1
                      )

surv_fit_b <- survfit(surv_object_b ~ Gender,
                      data = ctr_data_trn_b
                      )

# 2.0.15 - plot the K-M survival curve, female only, then both males and females - are they reasonable?
plot(surv_fit_f,
        ylim=c(0,1),
        xlab="Months",
        ylab="Proportion not Terminated",
        col=c("black", "red", "blue")
     )
legend('topright', c("Females", "Lower 95%CI", "Upper 95%CI"), col=c("black", "red", "blue"), lty=1:3, cex=0.8)

plot(surv_fit_b,
       ylim=c(0,1),
       xlab="Months",
       ylab="Proportion not Terminated",
       col=c("red", "blue")
     )
legend('topright', c("Females", "Males"), col=c("red", "blue"), lty=1:3, cex=0.8)

```

`@sct`
```{r}
success_msg("Good work!")
```



***

```yaml
type: NormalExercise
lang: r
key: f15554e113
xp: 150
```


`@instructions`

- Now let us generate a simplified Nelson-Aalen test curve for each of the survival objects created in last exercise - Female only and Both males and females.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.- First select sections of the code by highlight them. Then click on the "RUN CODE" button to run the Code in small chunks. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as one R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0200 - KM-NA.r"

`@sample_code`
```{r}
# 2.0.16 - generate a simplified Nelson-Aalen test curve for each of the 2 objects - the ppt gives you an idea
#           of what you should be looking for

surv_na_f <- survfit(surv_object_f ~ 1,
                     type = "fh"
                     )

surv_na_b <- survfit(surv_object_b ~ Gender,
                      data = ctr_data_trn_b
                     )

# 2.0.17- retrieve the data for plotting the simplified Nelson-Aalen and related test results
surv_na_f2 <- tidy(surv_na_f$time)
surv_na_f2$surv <- tidy(surv_na_f$surv)
surv_na_f2$nela <- -log(surv_na_f2$surv$x)
surv_na_f2$haz <- log(surv_na_f2$nela)

surv_na_b2 <- tidy(surv_na_b$time)
surv_na_b2$surv <- tidy(surv_na_b$surv)
surv_na_b2$nela <- -log(surv_na_b2$surv$x)
surv_na_b2$haz <- log(surv_na_b2$nela)

# 2.0.18 - plot the test results for females and both genders, simplified Nelson-Aalen and log(-log) plots
#           are the CHFs linear?  are the log(-log) plots parallel?
plot(surv_na_f2$x,
     surv_na_f2$nela,
     xlab="Months",
     ylab="-log(survival curve)",
     col=c("red"),
     main = "Cumulative Hazard Function (CHF) for Females"
     )
legend('topright', c("Females"), col=c("red"), lty=1:3, cex=0.8)

plot(surv_na_b2$x,
     surv_na_b2$nela,
     xlab="Months",
     ylab="-log(survival curve)",
     main = "Cumulative Hazard Function (CHF), Males and Females"
     )

plot(surv_na_f2$x,
     surv_na_f2$haz,
     xlab="Months",
     ylab="log(-log(survival curve))",
     col=c("red"),
     main = "-log of CHF for Females"
     )
legend('topright', c("Females"), col=c("red"), lty=1:3, cex=0.8)

plot(surv_na_b2$x,
     surv_na_b2$haz,
     xlab="Months",
     ylab="log(-log(survival curve))",
     main = "-log of CHF for Males and Females"
     )

```


`@solution`
```{r}
# 2.0.16 - generate a simplified Nelson-Aalen test curve for each of the 2 objects - the ppt gives you an idea
#           of what you should be looking for

surv_na_f <- survfit(surv_object_f ~ 1,
                     type = "fh"
                     )

surv_na_b <- survfit(surv_object_b ~ Gender,
                      data = ctr_data_trn_b
                     )

# 2.0.17- retrieve the data for plotting the simplified Nelson-Aalen and related test results
surv_na_f2 <- tidy(surv_na_f$time)
surv_na_f2$surv <- tidy(surv_na_f$surv)
surv_na_f2$nela <- -log(surv_na_f2$surv$x)
surv_na_f2$haz <- log(surv_na_f2$nela)

surv_na_b2 <- tidy(surv_na_b$time)
surv_na_b2$surv <- tidy(surv_na_b$surv)
surv_na_b2$nela <- -log(surv_na_b2$surv$x)
surv_na_b2$haz <- log(surv_na_b2$nela)

# 2.0.18 - plot the test results for females and both genders, simplified Nelson-Aalen and log(-log) plots
#           are the CHFs linear?  are the log(-log) plots parallel?
plot(surv_na_f2$x,
     surv_na_f2$nela,
     xlab="Months",
     ylab="-log(survival curve)",
     col=c("red"),
     main = "Cumulative Hazard Function (CHF) for Females"
     )
legend('topright', c("Females"), col=c("red"), lty=1:3, cex=0.8)

plot(surv_na_b2$x,
     surv_na_b2$nela,
     xlab="Months",
     ylab="-log(survival curve)",
     main = "Cumulative Hazard Function (CHF), Males and Females"
     )

plot(surv_na_f2$x,
     surv_na_f2$haz,
     xlab="Months",
     ylab="log(-log(survival curve))",
     col=c("red"),
     main = "-log of CHF for Females"
     )
legend('topright', c("Females"), col=c("red"), lty=1:3, cex=0.8)

plot(surv_na_b2$x,
     surv_na_b2$haz,
     xlab="Months",
     ylab="log(-log(survival curve))",
     main = "-log of CHF for Males and Females"
     )

```

`@sct`
```{r}
success_msg("Good work!")
```


---

## Run a Cox proportional hazard model

```yaml
type: NormalExercise
lang: r
key: ff7dc84def
xp: 150
```


`@instructions`

- Now let us produce and visualize Kaplan-Meier survival curves. The ctr_data_trn (training) table created in earlier exercise is imported and loaded into this session.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Feel free to type R codes in the Console to test or perform other tasks.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0220 - CoxPH.R"

`@pre_exercise_code`
```{r}
library(data.table)
library(dplyr)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ctr_data_trn.RData", temp)
unzip(temp,"ctr_data_trn.RData",overwrite=TRUE)
load("ctr_data_trn.RData")
unlink(temp)

```

`@sample_code`
```{r}
##########################################################################################
# Purpose: To be able to run a Cox proportional hazard model, and to be able to
#             review and interpret the basic results
##########################################################################################

# 2.2.1 - load the packages
library(survival)
library(broom)
library(dplyr)
library(stringr)

# 2.2.4 - the ctr_data_trn (training) table created in earlier exercise is already imported and loaded into this session.

# 2.2.5 - recode the data into ctr_data_ph so it is in a cleaner format for the regression
ctr_data_ph <- ctr_data_trn %>%
                          select(GroupIndicator,
                                 Gender,
                                 IncurredAgeBucket,
                                 Incurred_Year,
                                 ClaimType,
                                 Cov_Type_Bucket,
                                 Exposure,
                                 Terminations,
                                 ClaimDuration,
                                 ClaimDuration.n,
                                 Diagnosis_Category,
                                 TQ_Status,
                                 Infl_Rider_Bucket,
                                 Max_Ben_Bucket,
                                 beg_dur_ph,
                                 end_dur_ph,
                                 Region,
                                 Sample
                                  ) %>%
                          mutate(ClmDurBucket = case_when(.$ClaimDuration <= 3 ~ "Mos1-3",
                                                          .$ClaimDuration > 3 & .$ClaimDuration <= 12 ~ "Mo4-12",
                                                          .$ClaimDuration > 12 & .$ClaimDuration <= 72 ~ "Yrs2-6",
                                                          .$ClaimDuration > 72 ~ "Yrs7+ "
                                                          ),
                                 Diagnosis2   = if_else(str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental",
                                                         "Mental",
                                                         "Non-Mental"
                                                          ),
                                 BP2          = if_else(Max_Ben_Bucket == "Unlimited",
                                                         "Lifetime",
                                                         "Non-Life"
                                                          )
                                  ) %>%
                          filter(ClaimType != "Unk"
                                  ) %>%
                          mutate(LnExposure = log(Exposure)
                                 )

# 2.2.6 - recode factor variables so that the base level characteristics can be more easily defined
ctr_data_ph$Gender.f <- as.factor(ctr_data_ph$Gender)
ctr_data_ph$ClaimType.f <- as.factor(ctr_data_ph$ClaimType)
ctr_data_ph$Diagnosis2.f <- as.factor(ctr_data_ph$Diagnosis2)
ctr_data_ph$Diagnosis_Category.f <- as.factor(ctr_data_ph$Diagnosis_Category)
ctr_data_ph$BP2.f <- as.factor(ctr_data_ph$BP2)
ctr_data_ph$IncurredAgeBucket.f <- as.factor(ctr_data_ph$IncurredAgeBucket)
ctr_data_ph$ClmDurBucket.f <- as.factor(ctr_data_ph$ClmDurBucket)
ctr_data_ph$Incurred_Year.f <- as.factor(ctr_data_ph$Incurred_Year)
ctr_data_ph$Region.f <- as.factor(ctr_data_ph$Region)
ctr_data_ph$TQ_Status.f <- as.factor(ctr_data_ph$TQ_Status)
ctr_data_ph$Infl_Rider_Bucket.f <- as.factor(ctr_data_ph$Infl_Rider_Bucket)
ctr_data_ph$Max_Ben_Bucket.f <- as.factor(ctr_data_ph$Max_Ben_Bucket)

# 2.2.7 - use ph data as the prefix
attach(ctr_data_ph)

# 2.2.8 - run the Cox ph regression and explicitly state the base level characteristics
results_ph <- coxph(formula = Surv(time = beg_dur_ph,
                                   time2 = end_dur_ph,
                                   event = Terminations,
                                   type = "counting"
                                  )
                                  ~ relevel(Gender.f, "Female") +
                                    relevel(IncurredAgeBucket.f,"80 to 84") +
                                    relevel(BP2.f,"Non-Life") +
                                    relevel(ClaimType.f,"HHC") +
                                    relevel(Diagnosis2.f,"Non-Mental"
                                    ),
                                  data=ctr_data_ph,
                                  ties = "efron"
                                  )

# 2.2.9 - detach the dataset
detach(ctr_data_ph)

# 2.2.10 - view the results - does everything look reasonable? 
results_ph

# 2.2.11 - store coefficients in data-frame with tidy() from the broom package, which
#           cleans up the results a little, and view the results
coef_ph <- tidy(results_ph) %>% 
                mutate(coef_exp = exp(estimate),
                       chisq_stat = statistic ** 2)

# uncomment the below line to View the results in 'coef_ph'
# View(coef_ph)

# 2.2.12 - plot the baseline survival function - does it look as expected? remember you looked
#             at this raw survival curve in the K-M segment earlier
plot(survfit(results_ph,
             newdata = data.frame(Gender.f = "Female",
                                  IncurredAgeBucket.f = "80 to 84",
                                  BP2.f = "Non-Life",
                                  ClaimType.f = "HHC",
                                  Diagnosis2.f = "Non-Mental"
                                  )
              ),
              ylim=c(0,1),
              xlab="Months",
              ylab="Proportion not Terminated",
              main = "Base Level Survival Curve",
              col=c("black", "red", "blue")
      )
legend('topright', c("Base Level", "Lower 95%CI", "Upper 95%CI"), col=c("black", "red", "blue"), lty=1:3, cex=0.8)

```


`@solution`
```{r}
##########################################################################################
# Purpose: To be able to run a Cox proportional hazard model, and to be able to
#             review and interpret the basic results
##########################################################################################

# 2.2.1 - load the packages
library(survival)
library(broom)
library(dplyr)
library(stringr)

# 2.2.4 - the ctr_data_trn (training) table created in earlier exercise is already imported and loaded into this session.

# 2.2.5 - recode the data into ctr_data_ph so it is in a cleaner format for the regression
ctr_data_ph <- ctr_data_trn %>%
                          select(GroupIndicator,
                                 Gender,
                                 IncurredAgeBucket,
                                 Incurred_Year,
                                 ClaimType,
                                 Cov_Type_Bucket,
                                 Exposure,
                                 Terminations,
                                 ClaimDuration,
                                 ClaimDuration.n,
                                 Diagnosis_Category,
                                 TQ_Status,
                                 Infl_Rider_Bucket,
                                 Max_Ben_Bucket,
                                 beg_dur_ph,
                                 end_dur_ph,
                                 Region,
                                 Sample
                                  ) %>%
                          mutate(ClmDurBucket = case_when(.$ClaimDuration <= 3 ~ "Mos1-3",
                                                          .$ClaimDuration > 3 & .$ClaimDuration <= 12 ~ "Mo4-12",
                                                          .$ClaimDuration > 12 & .$ClaimDuration <= 72 ~ "Yrs2-6",
                                                          .$ClaimDuration > 72 ~ "Yrs7+ "
                                                          ),
                                 Diagnosis2   = if_else(str_detect(Diagnosis_Category, "Alzheimer") | Diagnosis_Category == "Mental",
                                                         "Mental",
                                                         "Non-Mental"
                                                          ),
                                 BP2          = if_else(Max_Ben_Bucket == "Unlimited",
                                                         "Lifetime",
                                                         "Non-Life"
                                                          )
                                  ) %>%
                          filter(ClaimType != "Unk"
                                  ) %>%
                          mutate(LnExposure = log(Exposure)
                                 )

# 2.2.6 - recode factor variables so that the base level characteristics can be more easily defined
ctr_data_ph$Gender.f <- as.factor(ctr_data_ph$Gender)
ctr_data_ph$ClaimType.f <- as.factor(ctr_data_ph$ClaimType)
ctr_data_ph$Diagnosis2.f <- as.factor(ctr_data_ph$Diagnosis2)
ctr_data_ph$Diagnosis_Category.f <- as.factor(ctr_data_ph$Diagnosis_Category)
ctr_data_ph$BP2.f <- as.factor(ctr_data_ph$BP2)
ctr_data_ph$IncurredAgeBucket.f <- as.factor(ctr_data_ph$IncurredAgeBucket)
ctr_data_ph$ClmDurBucket.f <- as.factor(ctr_data_ph$ClmDurBucket)
ctr_data_ph$Incurred_Year.f <- as.factor(ctr_data_ph$Incurred_Year)
ctr_data_ph$Region.f <- as.factor(ctr_data_ph$Region)
ctr_data_ph$TQ_Status.f <- as.factor(ctr_data_ph$TQ_Status)
ctr_data_ph$Infl_Rider_Bucket.f <- as.factor(ctr_data_ph$Infl_Rider_Bucket)
ctr_data_ph$Max_Ben_Bucket.f <- as.factor(ctr_data_ph$Max_Ben_Bucket)

# 2.2.7 - use ph data as the prefix
attach(ctr_data_ph)

# 2.2.8 - run the Cox ph regression and explicitly state the base level characteristics
results_ph <- coxph(formula = Surv(time = beg_dur_ph,
                                   time2 = end_dur_ph,
                                   event = Terminations,
                                   type = "counting"
                                  )
                                  ~ relevel(Gender.f, "Female") +
                                    relevel(IncurredAgeBucket.f,"80 to 84") +
                                    relevel(BP2.f,"Non-Life") +
                                    relevel(ClaimType.f,"HHC") +
                                    relevel(Diagnosis2.f,"Non-Mental"
                                    ),
                                  data=ctr_data_ph,
                                  ties = "efron"
                                  )

# 2.2.9 - detach the dataset
detach(ctr_data_ph)

# 2.2.10 - view the results - does everything look reasonable? 
results_ph

# 2.2.11 - store coefficients in data-frame with tidy() from the broom package, which
#           cleans up the results a little, and view the results
coef_ph <- tidy(results_ph) %>% 
                mutate(coef_exp = exp(estimate),
                       chisq_stat = statistic ** 2)

# uncomment the below line to View the results in 'coef_ph'
# View(coef_ph)

# 2.2.12 - plot the baseline survival function - does it look as expected? remember you looked
#             at this raw survival curve in the K-M segment earlier
plot(survfit(results_ph,
             newdata = data.frame(Gender.f = "Female",
                                  IncurredAgeBucket.f = "80 to 84",
                                  BP2.f = "Non-Life",
                                  ClaimType.f = "HHC",
                                  Diagnosis2.f = "Non-Mental"
                                  )
              ),
              ylim=c(0,1),
              xlab="Months",
              ylab="Proportion not Terminated",
              main = "Base Level Survival Curve",
              col=c("black", "red", "blue")
      )
legend('topright', c("Base Level", "Lower 95%CI", "Upper 95%CI"), col=c("black", "red", "blue"), lty=1:3, cex=0.8)

```

`@sct`
```{r}
success_msg("Good work!")
```


---

## Run a piece-wise Cox proportional hazard model

```yaml
type: NormalExercise
lang: r
key: d5c96a8e76
xp: 150
```


`@instructions`

- Now run a piece-wise Cox proportional hazard model, to be able to review and interpret the basic results, and to show how it is equivalent to a Poisson GLM.

- The ctr_data_ph table from the earlier exercise is imported and loaded into this session.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0230 - PweCoxPH.R"

`@pre_exercise_code`
```{r}
library(data.table)
library(dplyr)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ctr_data_ph.RData", temp)
unzip(temp,"ctr_data_ph.RData",overwrite=TRUE)
load("ctr_data_ph.RData")
unlink(temp)

```

`@sample_code`
```{r}
##########################################################################################
# Purpose: To be able to run a piece-wise Cox proportional hazard model, to be able to
#             review and interpret the basic results, and to show how it is equivalent
#             to a Poisson GLM
##########################################################################################

# 2.3.1 - load the packages
library(eha)

# 2.3.4 - the ctr_data_ph table from the earlier exercise is imported and loaded into this session

# 2.3.5 - use ph data as the prefix
attach(ctr_data_ph)

# 2.3.6 - run the Cox piece-wise regression, it takes about a minute to run
#           note the cutpoints
results_pwe <- phreg(formula = Surv(time = beg_dur_ph,
                                   time2 = end_dur_ph,
                                   event = Terminations,
                                   type = "counting"
                                  )
                                  ~ relevel(Gender.f, "Female") +
                                    relevel(IncurredAgeBucket.f,"80 to 84") +
                                    relevel(BP2.f,"Non-Life") +
                                    relevel(ClaimType.f,"HHC") +
                                    relevel(Diagnosis2.f,"Non-Mental"
                                    ),
                                  data=ctr_data_ph,
                                  dist="pch",
                                  cut = c(3, 12, 72) # Cutpoints to approximate survival curve
                                  )

# 2.3.7 - detach the dataset
detach(ctr_data_ph)

# 2.3.8 - view the results - do they look reasonable?
results_pwe

plot(results_pwe)
results_pwe$hazards

# 2.3.9 - store coefficients in data-frame with tidy() from the broom package
#           this cleans up the formatting a little better
coef_pwe <- tidy(results_pwe$coefficients)

# uncomment the below line and run the code to view - coef_pwe
# View(coef_pwe)

# 2.3.10 - compute the Poisson glm with reduced factors and review results
#            we're jumping ahead a tiny bit on the variable selection so that
#            we can dig into the Poisson GLM after this segment
results_ps <- glm(Terminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3"),
                  data = ctr_data_ph, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.3.11 - to compare to phreg results, we need to exponentiate the Poisson parameters 
coef_ps_clmdur <- exp(results_ps$coefficients[1])
coef_ps_clmdur[2] <- exp(results_ps$coefficients[1]+results_ps$coefficients[14])
coef_ps_clmdur[3] <- exp(results_ps$coefficients[1]+results_ps$coefficients[15])
coef_ps_clmdur[4] <- exp(results_ps$coefficients[1]+results_ps$coefficients[16])

# 2.3.12 - show claim duration equivalence - are they the same?
results_pwe$hazards
coef_ps_clmdur

# 2.3.13 - show coefficient equivalence - are they the same?
coef_pwe

```


`@solution`
```{r}
##########################################################################################
# Purpose: To be able to run a piece-wise Cox proportional hazard model, to be able to
#             review and interpret the basic results, and to show how it is equivalent
#             to a Poisson GLM
##########################################################################################

# 2.3.1 - load the packages
library(eha)

# 2.3.4 - the ctr_data_ph table from the earlier exercise is imported and loaded into this session

# 2.3.5 - use ph data as the prefix
attach(ctr_data_ph)

# 2.3.6 - run the Cox piece-wise regression, it takes about a minute to run
#           note the cutpoints
results_pwe <- phreg(formula = Surv(time = beg_dur_ph,
                                   time2 = end_dur_ph,
                                   event = Terminations,
                                   type = "counting"
                                  )
                                  ~ relevel(Gender.f, "Female") +
                                    relevel(IncurredAgeBucket.f,"80 to 84") +
                                    relevel(BP2.f,"Non-Life") +
                                    relevel(ClaimType.f,"HHC") +
                                    relevel(Diagnosis2.f,"Non-Mental"
                                    ),
                                  data=ctr_data_ph,
                                  dist="pch",
                                  cut = c(3, 12, 72) # Cutpoints to approximate survival curve
                                  )

# 2.3.7 - detach the dataset
detach(ctr_data_ph)

# 2.3.8 - view the results - do they look reasonable?
results_pwe

plot(results_pwe)
results_pwe$hazards

# 2.3.9 - store coefficients in data-frame with tidy() from the broom package
#           this cleans up the formatting a little better
coef_pwe <- tidy(results_pwe$coefficients)

# uncomment the below line and run the code to view - coef_pwe
# View(coef_pwe)

# 2.3.10 - compute the Poisson glm with reduced factors and review results
#            we're jumping ahead a tiny bit on the variable selection so that
#            we can dig into the Poisson GLM after this segment
results_ps <- glm(Terminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3"),
                  data = ctr_data_ph, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.3.11 - to compare to phreg results, we need to exponentiate the Poisson parameters 
coef_ps_clmdur <- exp(results_ps$coefficients[1])
coef_ps_clmdur[2] <- exp(results_ps$coefficients[1]+results_ps$coefficients[14])
coef_ps_clmdur[3] <- exp(results_ps$coefficients[1]+results_ps$coefficients[15])
coef_ps_clmdur[4] <- exp(results_ps$coefficients[1]+results_ps$coefficients[16])

# 2.3.12 - show claim duration equivalence - are they the same?
results_pwe$hazards
coef_ps_clmdur

# 2.3.13 - show coefficient equivalence - are they the same?
coef_pwe

```

`@sct`
```{r}
success_msg("Good work!")
```


---

## A Poisson GLM model

```yaml
type: NormalExercise
lang: r
key: a25503af4c
xp: 150
```


`@instructions`

- Now let us run a Poisson GLM and explore the data a little more, to review and interpret the basic results, and to understand the model's shortcomings

- The ctr_data_ph table from previous exercise is loaded into this session.

- First select small chunks of the code by highlighting them. Then click on the "RUN CODE" button to run the Code by small sections. Observe the results printed in the console and then proceed to next section of the code. Make sure you are selecting the complete R statement, as a R statement can span multiple lines.

- Press the 'Submit Answers' button, after you have run the complete code and is ready to go to the next exercise.



Note: Refer the code in "0240 - PoissonGLM Explore.r"

`@pre_exercise_code`
```{r}
library(data.table)
library(dplyr)

temp <- tempfile()
download.file("http://s3.amazonaws.com/assets.datacamp.com/production/course_6490/datasets/ctr_data_ph.RData", temp)
unzip(temp,"ctr_data_ph.RData",overwrite=TRUE)
load("ctr_data_ph.RData")
unlink(temp)

```

`@sample_code`
```{r}
######################################################################################################
# Purpose: To be able to run a Poisson GLM and explore the data a little more, to be able
#               to review and interpret the basic results, and to understand the model's shortcomings
######################################################################################################

# 2.4.1 - load the packages
library(dplyr)
library(stringr)
library(broom)

# 2.4.4 - the ctr_data_ph table from previous exercise is loaded into this session

#convert ctr_data_ph to a tbl_df format for easier viewing
ctr_data_ph <- tbl_df(ctr_data_ph)

# 2.4.5 - view the data structure
str(ctr_data_ph)

# 2.4.6 - aggregate the ph data into ctr_data_ps; this wil speed up the regression significantly,
#           since there are fewer records in the consolidated data
ctr_data_ps <- ctr_data_ph %>%
                          group_by(Cov_Type_Bucket,
                                   Gender.f, 
                                   IncurredAgeBucket.f, 
                                   Incurred_Year, 
                                   Incurred_Year.f,
                                   ClaimType.f,
                                   ClmDurBucket.f,
                                   Diagnosis2.f,
                                   BP2.f,
                                   Sample,
                                   Diagnosis_Category.f, 
                                   TQ_Status.f,          
                                   Infl_Rider_Bucket.f,  
                                   Max_Ben_Bucket.f,     
                                   Region.f        
                                   ) %>%
                          summarise(SumExposure = sum(Exposure), 
                                    SumTerminations = sum(Terminations)
                                    ) %>% data.frame

# 2.4.7 - re-calc the lnexposure column, and a new incurred year variable
#           this needs to be done because these drivers are not additive
ctr_data_ps <- mutate(ctr_data_ps, 
                    LnExposure = log(SumExposure), 
                    LnIncYr = log(Incurred_Year - 1990)
                    )

# 2.4.8 - attach the table name
attach(ctr_data_ps)

# 2.4.9 - compute a preliminary poisson glm and review results
#           which variables would you keep?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f,"Female")
                  + relevel(ClaimType.f,"HHC")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + relevel(Region.f, "West") +
                  + relevel(Diagnosis_Category.f, "Unknown") +
                  + relevel(TQ_Status.f, "U") +
                  + relevel(Infl_Rider_Bucket.f, "Unknown") +
                  + relevel(Max_Ben_Bucket.f, "Unlimited"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

coef_ps <- tidy(results_ps) %>% 
                        mutate(coef_exp = exp(estimate),
                               chisq_stat = statistic ** 2)

summary(results_ps)

# 2.4.10 - re-aggregate the data with reduced factors into ctr_data_ps
#           we removed some variables, so we can even further consolidate
#           the data for quicker downstream processing
ctr_data_ps <- ctr_data_ps %>%
                        select(Cov_Type_Bucket,
                               Gender.f, 
                               IncurredAgeBucket.f, 
                               Incurred_Year,
                               Incurred_Year.f,
                               ClaimType.f, 
                               ClmDurBucket.f, 
                               Diagnosis2.f, 
                               BP2.f,
                               Sample,
                               SumExposure,
                               SumTerminations
                              ) %>%
                        group_by(Cov_Type_Bucket,
                                 Gender.f, 
                                 IncurredAgeBucket.f, 
                                 Incurred_Year,
                                 Incurred_Year.f,
                                 ClaimType.f, 
                                 ClmDurBucket.f,
                                 Diagnosis2.f, 
                                 BP2.f,
                                 Sample
                                ) %>%
                        summarise(SumExposure = sum(SumExposure), 
                                  SumTerminations = sum(SumTerminations)
                                  ) %>% data.frame

# 2.4.11 - re-calc the lnexposure column, and a new incurred year variable (non-additive)
ctr_data_ps <- mutate(ctr_data_ps, 
                      LnExposure = log(SumExposure), 
                      LnIncYr = log(Incurred_Year - 1990)
                      ) %>% data.frame()

# 2.4.12 - compute the poisson glm with incurred year and review results
#           what should we do with the incurred year variable?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + relevel(Incurred_Year.f,"2009"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.13 - compute the poisson glm with fitted incurred year and review results
#           how did we do on the incurred year variable?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + LnIncYr,
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.14 - review the parameter correlation matrix; should we think about adding
#           some cross-terms as a result of this review?
corresult <- summary(results_ps,
                     correlation = TRUE
                     )
corrmat <- corresult$correlation

# **** uncomment the below line to view corrmat ****
# View(corrmat)

# 2.4.15 - run the final model with interactions and review results
#           are the new interaction terms worth keeping?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + LnIncYr * relevel(IncurredAgeBucket.f,"80 to 84")
                  + LnIncYr * relevel(ClmDurBucket.f,"Mos1-3"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.16 - display the dispersion parameter - this is always "1" for a regular Poisson
#           we will be relaxing this parameter a little later
summary.glm(results_ps)$dispersion

# 2.4.17 - Type I analysis, which tells you the sequential explanatory impact of
#         adding the driver variables - order matters!
anova(results_ps)

# 2.4.18 - Type III analysis, which tells you the explanatory impact of removing each variable (one at a time)
#         while keeping all the others - order does not matter
drop1(results_ps,~.,test="Chisq")

```


`@solution`
```{r}
######################################################################################################
# Purpose: To be able to run a Poisson GLM and explore the data a little more, to be able
#               to review and interpret the basic results, and to understand the model's shortcomings
######################################################################################################

# 2.4.1 - load the packages
library(dplyr)
library(stringr)
library(broom)

# 2.4.4 - the ctr_data_ph table from previous exercise is loaded into this session

#convert ctr_data_ph to a tbl_df format for easier viewing
ctr_data_ph <- tbl_df(ctr_data_ph)

# 2.4.5 - view the data structure
str(ctr_data_ph)

# 2.4.6 - aggregate the ph data into ctr_data_ps; this wil speed up the regression significantly,
#           since there are fewer records in the consolidated data
ctr_data_ps <- ctr_data_ph %>%
                          group_by(Cov_Type_Bucket,
                                   Gender.f, 
                                   IncurredAgeBucket.f, 
                                   Incurred_Year, 
                                   Incurred_Year.f,
                                   ClaimType.f,
                                   ClmDurBucket.f,
                                   Diagnosis2.f,
                                   BP2.f,
                                   Sample,
                                   Diagnosis_Category.f, 
                                   TQ_Status.f,          
                                   Infl_Rider_Bucket.f,  
                                   Max_Ben_Bucket.f,     
                                   Region.f        
                                   ) %>%
                          summarise(SumExposure = sum(Exposure), 
                                    SumTerminations = sum(Terminations)
                                    ) %>% data.frame

# 2.4.7 - re-calc the lnexposure column, and a new incurred year variable
#           this needs to be done because these drivers are not additive
ctr_data_ps <- mutate(ctr_data_ps, 
                    LnExposure = log(SumExposure), 
                    LnIncYr = log(Incurred_Year - 1990)
                    )

# 2.4.8 - attach the table name
attach(ctr_data_ps)

# 2.4.9 - compute a preliminary poisson glm and review results
#           which variables would you keep?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f,"Female")
                  + relevel(ClaimType.f,"HHC")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + relevel(Region.f, "West") +
                  + relevel(Diagnosis_Category.f, "Unknown") +
                  + relevel(TQ_Status.f, "U") +
                  + relevel(Infl_Rider_Bucket.f, "Unknown") +
                  + relevel(Max_Ben_Bucket.f, "Unlimited"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

coef_ps <- tidy(results_ps) %>% 
                        mutate(coef_exp = exp(estimate),
                               chisq_stat = statistic ** 2)

summary(results_ps)

# 2.4.10 - re-aggregate the data with reduced factors into ctr_data_ps
#           we removed some variables, so we can even further consolidate
#           the data for quicker downstream processing
ctr_data_ps <- ctr_data_ps %>%
                        select(Cov_Type_Bucket,
                               Gender.f, 
                               IncurredAgeBucket.f, 
                               Incurred_Year,
                               Incurred_Year.f,
                               ClaimType.f, 
                               ClmDurBucket.f, 
                               Diagnosis2.f, 
                               BP2.f,
                               Sample,
                               SumExposure,
                               SumTerminations
                              ) %>%
                        group_by(Cov_Type_Bucket,
                                 Gender.f, 
                                 IncurredAgeBucket.f, 
                                 Incurred_Year,
                                 Incurred_Year.f,
                                 ClaimType.f, 
                                 ClmDurBucket.f,
                                 Diagnosis2.f, 
                                 BP2.f,
                                 Sample
                                ) %>%
                        summarise(SumExposure = sum(SumExposure), 
                                  SumTerminations = sum(SumTerminations)
                                  ) %>% data.frame

# 2.4.11 - re-calc the lnexposure column, and a new incurred year variable (non-additive)
ctr_data_ps <- mutate(ctr_data_ps, 
                      LnExposure = log(SumExposure), 
                      LnIncYr = log(Incurred_Year - 1990)
                      ) %>% data.frame()

# 2.4.12 - compute the poisson glm with incurred year and review results
#           what should we do with the incurred year variable?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + relevel(Incurred_Year.f,"2009"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.13 - compute the poisson glm with fitted incurred year and review results
#           how did we do on the incurred year variable?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + relevel(IncurredAgeBucket.f,"80 to 84")
                  + relevel(ClmDurBucket.f,"Mos1-3")
                  + LnIncYr,
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.14 - review the parameter correlation matrix; should we think about adding
#           some cross-terms as a result of this review?
corresult <- summary(results_ps,
                     correlation = TRUE
                     )
corrmat <- corresult$correlation

# **** uncomment the below line to view corrmat ****
# View(corrmat)

# 2.4.15 - run the final model with interactions and review results
#           are the new interaction terms worth keeping?
results_ps <- glm(SumTerminations ~ 
                    relevel(Gender.f, "Female") 
                  + relevel(ClaimType.f,"HHC")
                  + relevel(Diagnosis2.f,"Non-Mental")
                  + relevel(BP2.f,"Non-Life")
                  + LnIncYr * relevel(IncurredAgeBucket.f,"80 to 84")
                  + LnIncYr * relevel(ClmDurBucket.f,"Mos1-3"),
                  data = ctr_data_ps, 
                  family = poisson, 
                  offset = LnExposure
                  )

summary(results_ps)

# 2.4.16 - display the dispersion parameter - this is always "1" for a regular Poisson
#           we will be relaxing this parameter a little later
summary.glm(results_ps)$dispersion

# 2.4.17 - Type I analysis, which tells you the sequential explanatory impact of
#         adding the driver variables - order matters!
anova(results_ps)

# 2.4.18 - Type III analysis, which tells you the explanatory impact of removing each variable (one at a time)
#         while keeping all the others - order does not matter
drop1(results_ps,~.,test="Chisq")

```

`@sct`
```{r}
success_msg("Good work!")
```
