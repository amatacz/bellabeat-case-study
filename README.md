# Bellabeat Fitness Case Study


### Capstone project of Google Data Analytics [Certificate](https://www.credly.com/badges/15e1da36-eef4-4abe-ad87-7e541b5717e8)

Author: *Aleksandra Matacz*<br><br>
Date: *May 10, 2022*



# Project Overwiev 👁‍🗨💬

### Bellabeat 
is the high-tech wellness company. Sando Mur and Urška Sršen created smart-devices to monitor biometric and lifestyle data that help women better understand how their bodies work and make healthier choices. Bellabeat devices not only support healthy lifestyle but also are beautiful jewellery designed by artist and co-founder of Bellabeat - Urška.

## Ask ❓
Key business task and questions for the analysis:
Gain more insight into behavior of non-Bellabeat smart-devices users to find new opportunities to develop device or improve marketing strategy.

To address key business task my analyze will answer questions below:
* What are some trends in smart device usage?
* How could these trends apply to Bellabeat customers?
* How could these trends help influence Bellabeat marketing strategy?

## Prepare 💻⚙️
Used data sources:
[FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit) (author Mobius) from Kaggle data set. Data license is [CC0: Public Domain](https://wiki.creativecommons.org/wiki/CC0_use_for_data), which means there is no copyright. Data can be copied, modified and distributed.

Installing and loading packages
```
install.packages('tidyverse')
install.packages('lubridate')
install.packages('janitor')
install.packages('reshape')
```

```
library(tidyverse)
library(lubridate)
library(janitor)
library(reshape)
```

Loading datasets
```
activity <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
calories <- read_csv("../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
sleep <- read_csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv')
intensity <- read_csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv')
weight <- read_csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv')
```

# Process (Data Cleaning) 🔧
## Tools used:
R for data cleaning, Tableau for visualization

## Data inspecting
I have imported 5 datasets: acitivty, calories, intensity, sleep and weight. Each dataset contains data collected by wellness smart-devices. View first 10 rows of each dataset to get familiar with structure of table, variable names convention, data types set in columns and looking for NA values.

***Activity* dataset**

Activity dataset contains observations about total steps, distance (with distinction between intensity of training), minutes of training (with distinction between intensity as well), includes also calories burned by users in each day.

```
head(activity,10)
str(activity)

sum(is.na.data.frame(activity))
```
Formatting datatype in column *ActivityDate* (from *char* to *datetime*)
```
activity$ActivityDate <- as.POSIXct(activity$ActivityDate, format = '%m/%d/%Y', tz=Sys.timezone())
```

***Calories* dataset**

Calories dataset contains observation about calories burned by users in each day.
```head(calories, 10)
str(calories)

sum(is.na(calories))
```
Formatting datatype in column *ActivityDay* (from *char* to *datetime*)

```
calories$ActivityDay <- as.POSIXct(calories$ActivityDay, format = '%m/%d/%Y', tz=Sys.timezone())
```

***Intensity* dataset**

In this dataset observation about intensity of each training are stored. Minutes and kilometers of training.

```
head(intensity)
str(intensity)
sum(is.na(intensity))
```

Formating *ActivityDay* type (from *char* to *datetime*)

```
intensity$ActivityDay <- as.POSIXct(intensity$ActivityDay, format = '%m/%d/%Y', tz=Sys.timezone())
```

***Sleep* dataset**

This dataset contains information about sleep duration and and minutes spend in bed.

```
head(sleep)
str(sleep)
sum(is.na(sleep))
```
Formatting *SleepDay* type (from *char* to *datetime*)

```
sleep$SleepDay <- as.POSIXct(sleep$SleepDay, format = '%m/%d/%Y', tz=Sys.timezone())
```

***Weight* dataset**

Weight dataset stores information about weight, bmi and fat percentage of each user.

```
head(weight)
str(weight)
sum(is.na(weight))
```

As in weight data set there are 65 NA values, I have take into consideration if this amount of missing values can be removed, replaced etc.

Formatting *Date* type (from *char* to *datetime*)

```
weight$Date <- as.POSIXct(weight$Date, format = '%m/%d/%Y %I:%M:%S %p', tz=Sys.timezone())
```

## Exploring and summarizing data
Counting unique users in each dataset

```
n_distinct(activity$Id)
n_distinct(calories$Id)
n_distinct(intensity$Id)
n_distinct(sleep$Id)
n_distinct(weight$Id)
```

There is 33 participants in activity, calories and intensity datasets. 24 in sleep and only 8 participants in weight dataset. 8 is not significant to make any recomendations based on this amount of data and due to this, there is no need to analyze how to address 65 missing values of this dataset.

Searching for duplicates in each dataset

```
sum(duplicated(activity))
sum(duplicated(calories))
sum(duplicated(intensity))
sum(duplicated(sleep))
sum(duplicated(weight))
```

Cleaning duplicates
```
activity <- activity %>% 
  distinct() 

calories <- calories %>% 
  distinct()

intensity <- intensity %>%
    distinct()

sleep <- sleep %>% 
  distinct()

weight <- weight %>%
    distinct()
```
Verification
```
sum(duplicated(activity))
sum(duplicated(calories))
sum(duplicated(intensity))
sum(duplicated(sleep))
sum(duplicated(weight))
```

Column names cleaning to get consistency in variables and renaming column in activity data
```
activity <- clean_names(activity)
calories <- clean_names(calories)
intensity <- clean_names(intensity)
sleep <- clean_names(sleep)
weight <- clean_names(weight)

activity <- activity %>% 
  dplyr::rename(activity_day = activity_date)
sleep <- sleep %>%
    dplyr::rename(activity_day = sleep_day)
```
Summary of each dataset
```
activity %>% 
  select(id, total_steps, total_distance, sedentary_minutes) %>% 
  summary()

calories %>% 
  select( calories) %>% 
  summary()

intensity %>%
    select(sedentary_minutes, lightly_active_minutes, fairly_active_minutes, very_active_minutes) %>%
    summary()

sleep %>% 
  select(total_sleep_records, total_minutes_asleep, total_time_in_bed) %>% 
  summary()
```
![image](https://user-images.githubusercontent.com/73778853/167697172-80ce0755-b45c-438f-9a12-d4926f3d73f0.png)
![image](https://user-images.githubusercontent.com/73778853/167697222-f476e2b4-53da-4c79-b7af-d8fd18a43d33.png)

Some interesing findings about each dataset summary:

* average total steps - 7868. Not enough to maintain good health. According to WHO people who make more than 8000 steps per day have 51% lower risk of dying by any cause compared with those who took 4,000 per day. This trend continued with higher step counts, as participants who took 12,000 steps per day had a 65% lower risk of dying than those who took 4,000.
* sedentary minutes are majority of tracked time (in compare to lightly active, fairly active and very active minutes) - average 1026 miutes -> over 17 hours. Definately this amount should be lowered.
* on average participiants sleep 1 to 7 hours.


## Analyze🧐✍️
At first, I have analyzed and categorized users by frequency of usage. Datasets contain information collected in two months April-May 2016.
That is why I set that 20 or more logged trainings qualifiy user as very active, between 20 and 10 trainings as active user, less than 10 - occasional user.
```
activity_by_id <- activity %>% 
    group_by(id) %>%
    count(id)

activity_by_id <- activity_by_id %>% mutate(user_category = case_when(
        n >= 25 ~ 'very_active_user',
        n >= 10 ~ 'active_user',
        n < 10 ~ 'occasional_user'))

activity <- merge(x=activity, y=activity_by_id[ , c("id", "n", "user_category")], by='id')
```


```
options(repr.plot.width=10, repr.plot.height=10)

ggplot(data=activity)+
    geom_boxplot(aes(x=user_category, y=calories, fill=user_category), alpha=0.7)+
        theme_light(base_size=15)+
        guides(x =  guide_axis(angle = 25)) +
        labs(x="User activity", y="Calories burned", title="User activity and burned calories")+
        scale_fill_manual(values=c("very_active_user"="red","active_user"="orange","occasional_user"="yellow"))
```

![image](https://user-images.githubusercontent.com/73778853/167698113-b56cedc5-cf75-4bf7-bbd6-aca538233946.png)

Chart above shows that most active users burn on average more calories than other users. This is what could be expected - more recorded trainings result with more calories burned.
*Bellabeat can consider implementing challenge-like feature, ranking dependent on amount of trainings or training days strike. Fun competition can be motivation to increase users activity.*

Next, I analyzed distribution of total steps recorded. Right amount of steps made during daily life can help to maintain good health. Nowadays more and more people have sedentary live style, works in front of computers and it is getting harder to reach steps goal. This is why devices that helps to monitor steps amount will be more and more popular.

```
options(repr.plot.width=10, repr.plot.height=10)

ggplot(data=activity, aes(x=total_steps))+
    geom_histogram(bins = 20, color='darkblue', fill='blue', alpha=0.7)+
    geom_vline(data=activity, aes(xintercept=mean(total_steps), color='red'), size=1)+
    theme_light(base_size=15) +
    labs(color = "Average amount of steps - 7638", x= "Total steps", title="Total steps recorded in activities")
```    

![image](https://user-images.githubusercontent.com/73778853/167700287-ca708a51-536d-4299-886e-fc10d2926b75.png)

We can observe that the majority of observations lay between 0 an 10000 steps per day. But distribution is slightly right-skewed, which means most of the records are lower than average (7638 steps per day). Each participant should pay attention to make at least 8000 steps per day.
**Bellabeat can consider implementing feature where user can set steps goal and get alerted how many steps have been made and how many left to reach daily goal, in chosen interval (f.eg every two hours).**

Next analyze reveals if users that are making more steps during the day are burning more calories.

```
round(cor(activity$calories, activity$total_steps, method="pearson"),2)
```
0.59

```
options(repr.plot.widht=10, repr.plot.height=10)

ggplot(data=activity, aes(x=calories, y=total_steps, color=calories))+
    geom_point()+
    geom_smooth(method = 'loess', formula = y ~ x)+
    theme_light(base_size=15)+
    scale_color_gradient(low='#ADFF2F', high='#FF0000')+
    labs(x='Calories burned', y='Total steps recorded', title='Correlation between total steps amount and calories burned')
 ```
 
 ![image](https://user-images.githubusercontent.com/73778853/167700526-5e0ac937-c945-4df6-abbf-ebae9e51c2fd.png)
 
Corelation between burned calories and total steps equals 0.59. Which means that there is a moderate linear dependency between these two variables.
We can conclude, that users burning calories during various kinds of acitvities - not only by walkich/jogging. Could be pilates, joga, gym activities.
**Bellabeat can consider extension number of available activities that can be recorded as training.**

To verify if more active day (more minutes spent on activities) is correlated with more minutes spent on sleeping I merged activity and sleep datasets and created new column activity_minutes (sum of every activity minutes recorded on particular day).

```
activity_sleep_merged <- merge(activity, sleep, by=c('id', 'activity_day')) %>%
    mutate(activity_minutes=very_active_minutes+fairly_active_minutes+lightly_active_minutes)
head(activity_sleep_merged)
```

```
round(cor(activity_sleep_merged$total_minutes_asleep, activity_sleep_merged$activity_minutes, method="pearson"),2)
```
-0.07
```
options(repr.plot.widht=10, repr.plot.height=10)

ggplot(data=activity_sleep_merged, aes(x=total_minutes_asleep, y=activity_minutes, color=total_minutes_asleep))+
    geom_point()+
    geom_smooth(method = 'loess', formula = y ~ x)+
    theme_light(base_size=15)+
    scale_color_gradient(low='#E0FFFF', high='#191970')+
    labs(x='Total minutes asleep', y='Activity minutes', title='Correlation between minutes asleep and total activity minutes')
 ```
 
 ![image](https://user-images.githubusercontent.com/73778853/167700803-66b4ef6a-95db-4eb5-b6d3-7494b5b92e04.png)

Graph above shows that there is no correlation between minutes spent on activities and time asleep. Correlation value -0.07 is not significant.
Right amount of high quality sleep is significant factor of health, to improve this area of users life, **Bellabeat can consider implementing feature that lets user track amount of sleep, quality of it and set sleeping schedule defined by user.**

# Summary 🧾 📊
Bellabeat supports women in maintainig healthy lifestyle. Regular analyzing trends among smart-devices users is crucial to be up to date to follow company mission and answer users needs in best possible way. Nowadays smart devices gain more and more popularity - busy schedules and healthy habits don't have to be mutually exclusive. Bellabeat is like a friend who keeps you motivated, on track and is stylish.

* Majority of users do some light activity to stay healthy and in shape, Bellabeat can encourage them to train more frequently or more efficiently by implementing challenge-like feature, ranking dependent on amount of trainings or training days strike. Fun competition can be motivation to increase users activity.
* Average user does 7638 steps per day - it is a bit too low. WHO recommends taking between 8000-12000 stes per day. Bellabeat can consider implementing feature where user can set daily steps goal and get alerted how many steps have been made and how many left to reach goal, in chosen interval (f.eg every two hours).
* Uers burning calories during various kinds of acitvities - not only by walkich/jogging. Could be pilates, joga, gym activities, swimming. Extension of number available activities that can be recorded as training will improve user-experience and encourage users to record more trainings.
* To take care about user wellness Bellabeat can consider implementing feature that lets user track amount of sleep, quality of it and set sleeping schedule defined by user. Right amount of high quality sleep is significant factor of health, to improve this area of users life.

This is my first project using R. I would appreciate any comments and recommendations for improvement!
