---
title       : Prework2-Cox/ Poisson Modeling
description : Cox/ Poisson Modeling.
---

## Data Cleaning, Exploration and Feature Engineering


```yaml
type: TabExercise
key: 
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
key: 
xp: 25
```


`@instructions`
	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

- Read the comments and the code. Run the Code and Observe the results.

Press the button 'Submit Answers' to go to next exercise.

`@sample_code`
```{r}
# Read the R documentation on the package related functions.

```

***

```yaml
type: NormalExercise
lang: r
key: 
xp: 100
```

`@instructions`
- We are exploring the data in the ctr_data table:
	   To understand the data and relationships.
	   To gather insights.


- Read the comments and the code. Run the Code and Observe the results.
	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

Press the button 'Submit Answers' to go to next exercise.

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


`@solution`
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
key: 
xp: 100
```


`@instructions`

- Now we are going to visually explore the data in the ctr_data table:  By Plotting graphs	   

- Read the comments and the code. Run the Code and Observe the results.
	   

Press the button 'Submit Answers' to go to next exercise.

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

View(term_stats)

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
View(cred_stats)

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

View(term_stats)

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
View(cred_stats)

```

`@sct`
```{r}
success_msg("Good work!")
```


---

## Examining and Creating Holdout Data

```yaml
type: TabExercise
key: 
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
key: 
xp: 100
```


`@instructions`

- Now we should examine the data on how to create Training & Validation sample data:
- Split the dataset into a 60% training sample, a 20% in-time validation sample, and
  20% out-of-time testing sample

- Read the comments and the code. Run the Code and Observe the results.
 	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

Press the button 'Submit Answers' to go to next exercise.

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
key: 
xp: 100
```


`@instructions`

- Now we are going to split the data using the insights we obtained from our previous exercises.

- Read the comments and the code. Run the Code and Observe the results.
	   
Type the `?` in front of the function name like `?library()` in the R console to read the R documentation. You can do this for any function.

Press the button 'Submit Answers' to go to next exercise.

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
