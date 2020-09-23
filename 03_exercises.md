---
title: 'Weekly Exercises #3' 
author: "Will Moscato"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---


```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, error=TRUE, message=FALSE, warning=FALSE)
```

```{r libraries}
library(tidyverse)     # for graphing and data cleaning
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
library(ggthemes)      # for even more plotting themes
library(geofacet)      # for special faceting with US map layout
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())       # My favorite ggplot() theme :)
```

```{r data}
#Lisa's garden data
garden_harvest <- read_sheet("https://docs.google.com/spreadsheets/d/1DekSazCzKqPS2jnGhKue7tLxRU3GVL1oxi-4bEM5IWw/edit?usp=sharing") %>% 
  mutate(date = ymd(date))

# Seeds/plants (and other garden supply) costs
supply_costs <- read_sheet("https://docs.google.com/spreadsheets/d/1dPVHwZgR9BxpigbHLnA0U99TtVHHQtUzNB9UR0wvb7o/edit?usp=sharing",
  col_types = "ccccnn")

# Planting dates and locations
plant_date_loc <- read_sheet("https://docs.google.com/spreadsheets/d/11YH0NtXQTncQbUse5wOsTtLSKAiNogjUA21jnX5Pnl4/edit?usp=sharing",
  col_types = "cccnDlc")%>% 
  mutate(date = ymd(date))

# Tidy Tuesday data
kids <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-09-15/kids.csv')
```

## Setting up on GitHub!

Before starting your assignment, you need to get yourself set up on GitHub and make sure GitHub is connected to R Studio. To do that, you should read the instruction (through the "Cloning a repo" section) and watch the video [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md). Then, do the following (if you get stuck on a step, don't worry, I will help! You can always get started on the homework and we can figure out the GitHub piece later):

* Create a repository on GitHub, giving it a nice name so you know it is for the 3rd weekly exercise assignment (follow the instructions in the document/video).  
* Copy the repo name so you can clone it to your computer. In R Studio, go to file --> New project --> Version control --> Git and follow the instructions from the document/video.  
* Download the code from this document and save it in the repository folder/project on your computer.  
* In R Studio, you should then see the .Rmd file in the upper right corner in the Git tab (along with the .Rproj file and probably .gitignore).  
* Check all the boxes of the files in the Git tab and choose commit.  
* In the commit window, write a commit message, something like "Initial upload" would be appropriate, and commit the files.  
* Either click the green up arrow in the commit window or close the commit window and click the green up arrow in the Git tab to push your changes to GitHub.  
* Refresh your GitHub page (online) and make sure the new documents have been pushed out.  
* Back in R Studio, knit the .Rmd file. When you do that, you should have two (as long as you didn't make any changes to the .Rmd file, in which case you might have three) files show up in the Git tab - an .html file and an .md file. The .md file is something we haven't seen before and is here because I included `keep_md: TRUE` in the YAML heading. The .md file is a markdown (NOT R Markdown) file that is an interim step to creating the html file. They are displayed fairly nicely in GitHub, so we want to keep it and look at it there. Click the boxes next to these two files, commit changes (remember to include a commit message), and push them (green up arrow).  
* As you work through your homework, save and commit often, push changes occasionally (maybe after you feel finished with an exercise?), and go check to see what the .md file looks like on GitHub.  
* If you have issues, let me know! This is new to many of you and may not be intuitive at first. But, I promise, you'll get the hang of it! 



## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.


## Warm-up exercises with garden data

These exercises will reiterate what you learned in the "Expanding the data wrangling toolkit" tutorial. If you haven't gone through the tutorial yet, you should do that first.

  1. Summarize the `garden_harvest` data to find the total harvest weight in pounds for each vegetable and day of week. Display the results so that the vegetables are rows but the days of the week are columns.

```{r}
garden_harvest %>% 
  mutate(day_of_week = wday(date, label = TRUE)) %>% 
  group_by(day_of_week, vegetable) %>% 
  summarise(weight_lbs = sum(0.00220462*weight)) %>% 
  pivot_wider(id_cols = vegetable,
              names_from = day_of_week,
              values_from = weight_lbs)
```

  2. Summarize the `garden_harvest` data to find the total harvest in pound for each vegetable variety and then try adding the `plot` variable from the `plant_date_loc` table. This will not turn out perfectly. What is the problem? How might you fix it?

```{r}
garden_harvest %>% 
  group_by(vegetable, variety) %>% 
  summarise(weight_lbs = sum(weight * 0.00220462)) %>% 
  left_join(plant_date_loc, by = c("vegetable", "variety"))
```


  3. I would like to understand how much money I "saved" by gardening, for each vegetable type. Describe how I could use the `garden_harvest` and `supply_cost` datasets, along with data from somewhere like [this](https://products.wholefoodsmarket.com/search?sort=relevance&store=10542) to answer this question. You can answer this in words, referencing various join functions. You don't need R code but could provide some if it's helpful.
  
  You could answer this by summing the total cost it took to harvest the vegetables on your own and compare that to the price for the same amount of each vegetable from a store like whole foods.

  4. Subset the data to tomatoes. Reorder the tomato varieties from smallest to largest first harvest date. Create a barplot of total harvest in pounds for each variety, in the new order.

```{r}
garden_harvest %>%
  filter(vegetable == "tomatoes") %>% 
  mutate(variety2 = fct_reorder(variety, date, min)) %>% 
  group_by(variety2) %>% 
  summarise(date, weight_lbs = sum(weight *0.00220462)) %>% 
  ggplot()+
  geom_bar(aes(y = variety2)) +
  ggtitle("Total Harvest by Variety") +
  xlab("Count") +
  ylab("Variety")
```

  5. In the `garden_harvest` data, create two new variables: one that makes the varieties lowercase and another that finds the length of the variety name. Arrange the data by vegetable and length of variety name (smallest to largest), with one row for each vegetable variety. HINT: use `str_to_lower()`, `str_length()`, and `distinct()`.
  
```{r}
garden_harvest %>% 
  mutate(lowercase = str_to_lower(variety)) %>% 
  mutate(variety_length = str_length(lowercase)) %>% 
  distinct(vegetable, variety, variety_length) %>% 
  arrange(variety_length)
```

  6. In the `garden_harvest` data, find all distinct vegetable varieties that have "er" or "ar" in their name. HINT: `str_detect()` with an "or" statement (use the | for "or") and `distinct()`.

```{r}
garden_harvest %>%   
  mutate(hasEAR = str_detect(variety, "er") | str_detect(variety, "ar")) %>% 
  distinct(variety, hasEAR)
```


## Bicycle-Use Patterns

In this activity, you'll examine some factors that may influence the use of bicycles in a bike-renting program.  The data come from Washington, DC and cover the last quarter of 2014.

<center>

![A typical Capital Bikeshare station. This one is at Florida and California, next to Pleasant Pops.](https://www.macalester.edu/~dshuman1/data/112/bike_station.jpg){300px}


![One of the vans used to redistribute bicycles to different stations.](https://www.macalester.edu/~dshuman1/data/112/bike_van.jpg){300px}

</center>

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usualy, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`.

```{r cache=TRUE}
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

**NOTE:** The `Trips` data table is a random subset of 10,000 trips from the full quarterly data. Start with this small data table to develop your analysis commands. **When you have this working well, you should access the full data set of more than 600,000 events by removing `-Small` from the name of the `data_site`.**

### Temporal patterns

It's natural to expect that bikes are rented more at some times of day, some days of the week, some months of the year than others. The variable `sdate` gives the time (including the date) that the rental started. Make the following plots and interpret them:

  7. A density plot, which is a smoothed out histogram, of the events versus `sdate`. Use `geom_density()`.
  
```{r}
Trips %>% 
  ggplot()+
  geom_density(aes(x = sdate)) +
  ggtitle("Events by Date") +
  xlab("Date") +
  ylab("Density")
```
  
  8. A density plot of the events versus time of day.  You can use `mutate()` with `lubridate`'s  `hour()` and `minute()` functions to extract the hour of the day and minute within the hour from `sdate`. Hint: A minute is 1/60 of an hour, so create a variable where 3:30 is 3.5 and 3:45 is 3.75.
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60) %>% 
  ggplot()+
  geom_density(aes(x = time)) +
  ggtitle("Density by Time of Day") +
  xlab("Time") +
  ylab("Density")
```
  
  9. A bar graph of the events versus day of the week. Put day on the y-axis.
  
```{r}
Trips %>% 
  mutate(dayofweek = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_bar(aes(y = dayofweek)) +
  ggtitle("Events By Day of Week") +
  xlab("Count") +
  ylab("Day of the Week")
```
  
  10. Facet your graph from exercise 8. by day of the week. Is there a pattern?
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time))+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week") +
  xlab("Time") +
  ylab("Density")
```
  On the weekend more people are going in the middle of the day, whereas on the weekdays it is only in the morning and the evenings.
  
  
The variable `client` describes whether the renter is a regular user (level `Registered`) or has not joined the bike-rental organization (`Causal`). The next set of exercises investigate whether these two different categories of users show different rental behavior and how `client` interacts with the patterns you found in the previous exercises. Repeat the graphic from Exercise \@ref(exr:exr-temp) (d) with the following changes:

  11. Change the graph from exercise 10 to set the `fill` aesthetic for `geom_density()` to the `client` variable. You should also set `alpha = .5` for transparency and `color=NA` to suppress the outline of the density function.
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = client), alpha = 0.5, color = NA)+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week by Client") +
  xlab("Time") +
  ylab("Density")
```

  12. Change the previous graph by adding the argument `position = position_stack()` to `geom_density()`. In your opinion, is this better or worse in terms of telling a story? What are the advantages/disadvantages of each?
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = client),  alpha = 0.5, color = NA, position = position_stack())+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week by Client") +
  xlab("Time") +
  ylab("Density")
```
  I think this allows you to distinguish between the two categories better, but it hides the bottom of the plot that is put behind, so you could miss part of the story.
  
  
  13. Add a new variable to the dataset called `weekend` which will be "weekend" if the day is Saturday or Sunday and  "weekday" otherwise (HINT: use the `ifelse()` function and the `wday()` function from `lubridate`). Then, update the graph from the previous problem by faceting on the new `weekend` variable. 
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  mutate(weekend = ifelse(weekday %in% c("Sat", "Sun"), "weekend", "weekday")) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = client), alpha = 0.5, color = NA) +
  facet_wrap(~weekend) +
  ggtitle("Events by Time by Day by Client by Weekend vs Weekday") +
  xlab("Time") +
  ylab("Density")
```
  
  14. Change the graph from the previous problem to facet on `client` and fill with `weekday`. What information does this graph tell you that the previous didn't? Is one graph better than the other?
  
```{r}
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  mutate(weekend = ifelse(weekday %in% c("Sat", "Sun"), "weekend", "weekday")) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = weekend, alpha = 0.5), color = NA) +
  facet_wrap(~client) +
   ggtitle("Events by Time by Day by Client by Weekend vs Weekday") +
  xlab("Time") +
  ylab("Density")
```
  Generally these graphs tell us the same information, but in the second one it is easier to see that the casual people make up more of the density in the middle of the day, especially on the weekdays.
  
  
### Spatial patterns

  15. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. We will improve this plot next week when we learn about maps!
  
```{r}
Trips %>% 
  mutate(name = sstation) %>% 
  left_join(Stations) %>% 
  group_by(name, lat, long) %>% 
  summarise(n = n()) %>% 
  ggplot()+
  geom_point(aes(x= lat, y = long, color = n)) +
  ggtitle("Total Departures by Station") +
  xlab("Latitude") +
  ylab("Longitude")
```
  
  16. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? (Again, we'll improve this next week when we learn about maps).
  
```{r}
Trips %>% 
  mutate(name = sstation) %>% 
  left_join(Stations) %>% 
  group_by(name, lat, long) %>% 
  filter(client == "Casual") %>% 
  summarise(count = (n()/nrow(Stations))*100) %>% 
  ggplot() +
  geom_point(aes(x = lat, y = long, color = count)) +
  ggtitle("Total Departures by Station for Casual Users") +
  xlab("Latitude") +
  ylab("Longitude")
```
  
  Jefferson & 14th, Lincoln Memorial, Washington & Independence, 4th & Constitution, 1st & Constitution, and Smithsonian / Jefferson Dr & 12th St SW have the highest percentage of casual clients.
  
### Spatiotemporal patterns

  17. Make a table with the ten station-date combinations (e.g., 14th & V St., 2014-10-14) with the highest number of departures, sorted from most departures to fewest. Save this to a new dataset and print out the dataset. Hint: `as_date(sdate)` converts `sdate` from date-time format to date format. 
  
```{r}
station_date_frequency <- Trips %>% 
  mutate(date = as.Date(sdate)) %>% 
  group_by(sstation, date) %>%
  summarise(total = n()) %>% 
  arrange(desc(total)) %>% 
  filter(total >= 322)
```
  
  18. Use a join operation to make a table with only those trips whose departures match those top ten station-date combinations from the previous part.
  
```{r}
station_date_frequency %>% 
  mutate(sdate = as.Date(date)) %>%
  left_join(Trips, by = "sdate")
```

  
  19. Build on the code from the previous problem (ie. copy that code below and then %>% into the next step.) and group the trips by client type and day of the week (use the name, not the number). Find the proportion of trips by day within each client type (ie. the proportions for all 7 days within each client type add up to 1). Display your results so day of week is a column and there is a column for each client type. Interpret your results.
  
  
```{r}
station_date_frequency %>% 
  mutate(sdate = as.Date(date)) %>%
  left_join(Trips, by = "sdate") %>% 
  mutate(dayofweek = wday(date, label = TRUE)) %>% 
  group_by(dayofweek, client) %>% 
  summarise(total = n()) %>% 
  mutate(total_prop = total/sum(total)) %>% 
  pivot_wider(id_cols = dayofweek,
              names_from = client,
              values_from = total_prop)
```

Casual riders don't tend to ride on weekdays and overall registered riders take up a larger proportion. 

**DID YOU REMEMBER TO GO BACK AND CHANGE THIS SET OF EXERCISES TO THE LARGER DATASET? IF NOT, DO THAT NOW.**

## GitHub link

  20. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 03_exercises.Rmd, provide a link to the 03_exercises.md file, which is the one that will be most readable on GitHub.

## Challenge problem! 

This problem uses the data from the Tidy Tuesday competition this week, `kids`. If you need to refresh your memory on the data, read about it [here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-15/readme.md). 

  21. In this exercise, you are going to try to replicate the graph below, created by Georgios Karamanis. I'm sure you can find the exact code on GitHub somewhere, but **DON'T DO THAT!** You will only be graded for putting an effort into this problem. So, give it a try and see how far you can get without doing too much googling. HINT: use `facet_geo()`. The graphic won't load below since it came from a location on my computer. So, you'll have to reference the original html on the moodle page to see it.
  

[](https://github.com/willmoscato/03_exercises)

```{r}
kids %>%
  filter(variable == "lib") %>% 
  ggplot(aes(x = year, y = inf_adj_perchild)) +
  geom_line(color = "white", size = 0.5) +
  theme(legend.position = "dodge") +
  theme_void() + 
  theme(plot.background = element_rect(fill = "lightsteelblue4")) +
  facet_geo(~state, grid = "us_state_grid3", label = "name") +
  labs(title = "Change in public spending on libraries", subtitle = "Dollars spent per child, adjusted for inflation") +
  theme(plot.title = element_text(hjust = 0.5, size = 15))
```


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
