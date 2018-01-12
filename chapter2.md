---
title       : Prework2-Cox/ Poisson Modeling
description : Cox/ Poisson Modeling.
---

## Data Cleaning, Exploration and Feature Engineering

```yaml
type: NormalExercise
lang: r
xp: 50
skills: 1
key: 
```
In the next few exercises, we will use the data file that will be used at the the workshop. Claim termination rate (CTR) data has been extracted from the SOA experience database and modified for workshop purposes.The modified data is downloaded here as a R Data table and is named 'ctr_data'.



`@instructions`

- Execute the code in each section and examine the results. Take time to understand each section.


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

```


`@solution`
```{r}

```


`@sct`
```{r}
```

---
