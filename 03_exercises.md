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





```r
library(tidyverse)     # for graphing and data cleaning
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
library(ggthemes)      # for even more plotting themes
library(geofacet)      # for special faceting with US map layout
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())       # My favorite ggplot() theme :)
```


```r
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


```r
garden_harvest %>% 
  mutate(day_of_week = wday(date, label = TRUE)) %>% 
  group_by(day_of_week, vegetable) %>% 
  summarise(weight_lbs = sum(0.00220462*weight)) %>% 
  pivot_wider(id_cols = vegetable,
              names_from = day_of_week,
              values_from = weight_lbs)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Sun"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Mon"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["Tue"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Wed"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["Thu"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["Fri"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["Sat"],"name":[8],"type":["dbl"],"align":["right"]}],"data":[{"1":"beans","2":"1.52780166","3":"6.5080382","4":"4.38719380","5":"4.08295624","6":"3.39291018","7":"1.52559704","8":"4.70906832"},{"1":"beets","2":"0.32187452","3":"0.6724091","4":"0.15873264","5":"0.18298346","6":"11.89172028","7":"0.02425082","8":"0.37919464"},{"1":"corn","2":"1.45725382","3":"0.7583893","4":"0.72752460","5":"5.30211110","6":"NA","7":"3.44802568","8":"1.31615814"},{"1":"cucumbers","2":"3.10410496","3":"4.7752069","4":"10.04645334","5":"5.30652034","6":"3.30693000","7":"7.42956940","8":"9.64080326"},{"1":"kale","2":"0.38139926","3":"1.7659006","4":"0.28219136","5":"0.61729360","6":"NA","7":"NA","8":"1.49032312"},{"1":"lettuce","2":"1.15963012","3":"2.4581513","4":"0.91712192","5":"1.14860702","6":"2.45153744","7":"1.80117454","8":"1.10671924"},{"1":"onions","2":"0.26014516","3":"0.5092672","4":"0.70768302","5":"NA","6":"0.60186126","7":"0.07275246","8":"1.34040896"},{"1":"peas","2":"2.05691046","3":"4.6341112","4":"2.06793356","5":"1.08026380","6":"3.39731942","7":"0.93696350","8":"2.85277828"},{"1":"radish","2":"0.08157094","3":"0.1962112","4":"0.09479866","5":"NA","6":"0.14770954","7":"0.19400656","8":"0.23148510"},{"1":"spinach","2":"0.48722102","3":"0.1477095","4":"0.49603950","5":"0.21384814","6":"0.23368972","7":"0.19621118","8":"0.26014516"},{"1":"strawberries","2":"0.08157094","3":"0.4784025","4":"NA","5":"NA","6":"0.08818480","7":"0.48722102","8":"0.16975574"},{"1":"Swiss chard","2":"0.73634308","3":"1.0736499","4":"0.07054784","5":"0.71429688","6":"2.23107544","7":"0.56438272","8":"NA"},{"1":"tomatoes","2":"63.68485794","3":"9.7091465","4":"48.75076206","5":"31.55913530","6":"34.51773534","7":"58.67596130","8":"25.94396816"},{"1":"zucchini","2":"12.23564100","3":"12.1959578","4":"16.46851140","5":"2.04147812","6":"5.74964896","7":"18.72163304","8":"3.41495638"},{"1":"basil","2":"NA","3":"0.0661386","4":"0.11023100","5":"NA","6":"0.02645544","7":"0.46737944","8":"0.41005932"},{"1":"broccoli","2":"NA","3":"0.8201186","4":"NA","5":"0.70768302","6":"NA","7":"NA","8":"NA"},{"1":"carrots","2":"NA","3":"0.8708249","4":"0.35273920","5":"NA","6":"2.67420406","7":"2.13848140","8":"2.33028334"},{"1":"hot peppers","2":"NA","3":"1.2588380","4":"0.14109568","5":"0.06834322","6":"NA","7":"NA","8":"NA"},{"1":"jalapeño","2":"NA","3":"0.4343101","4":"0.54895038","5":"0.09479866","6":"0.22487124","7":"1.29411194","8":"0.93916812"},{"1":"peppers","2":"NA","3":"0.2535313","4":"1.44402610","5":"0.94798660","6":"0.70988764","7":"0.14991416","8":"1.38229674"},{"1":"potatoes","2":"NA","3":"0.9700328","4":"NA","5":"4.57017726","6":"1.66669272","7":"NA","8":"2.15611836"},{"1":"raspberries","2":"NA","3":"0.1300726","4":"0.33510224","5":"NA","6":"0.28880522","7":"0.57099658","8":"0.53351804"},{"1":"cilantro","2":"NA","3":"NA","4":"0.00440924","5":"NA","6":"NA","7":"0.07275246","8":"0.03747854"},{"1":"edamame","2":"NA","3":"NA","4":"1.40213832","5":"NA","6":"NA","7":"NA","8":"4.68922674"},{"1":"pumpkins","2":"NA","3":"NA","4":"31.85675900","5":"NA","6":"NA","7":"NA","8":"92.68883866"},{"1":"squash","2":"NA","3":"NA","4":"18.46810174","5":"NA","6":"NA","7":"NA","8":"56.22221924"},{"1":"chives","2":"NA","3":"NA","4":"NA","5":"0.01763696","6":"NA","7":"NA","8":"NA"},{"1":"kohlrabi","2":"NA","3":"NA","4":"NA","5":"NA","6":"0.42108242","7":"NA","8":"NA"},{"1":"asparagus","2":"NA","3":"NA","4":"NA","5":"NA","6":"NA","7":"NA","8":"0.04409240"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  2. Summarize the `garden_harvest` data to find the total harvest in pound for each vegetable variety and then try adding the `plot` variable from the `plant_date_loc` table. This will not turn out perfectly. What is the problem? How might you fix it?


```r
garden_harvest %>% 
  group_by(vegetable, variety) %>% 
  summarise(weight_lbs = sum(weight * 0.00220462)) %>% 
  left_join(plant_date_loc, by = c("vegetable", "variety"))
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["weight_lbs"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["plot"],"name":[4],"type":["chr"],"align":["left"]},{"label":["number_seeds_planted"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["date"],"name":[6],"type":["date"],"align":["right"]},{"label":["number_seeds_exact"],"name":[7],"type":["lgl"],"align":["right"]},{"label":["notes"],"name":[8],"type":["chr"],"align":["left"]}],"data":[{"1":"asparagus","2":"asparagus","3":"0.04409240","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"basil","2":"Isle of Naxos","3":"1.08026380","4":"potB","5":"40","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"beans","2":"Bush Bush Slender","3":"21.92274128","4":"M","5":"30","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"beans","2":"Bush Bush Slender","3":"21.92274128","4":"D","5":"10","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"beans","2":"Chinese Red Noodle","3":"0.78484472","4":"K","5":"5","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"beans","2":"Chinese Red Noodle","3":"0.78484472","4":"L","5":"5","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"beans","2":"Classic Slenderette","3":"3.42597948","4":"E","5":"29","6":"2020-06-20","7":"TRUE","8":"NA"},{"1":"beets","2":"Gourmet Golden","3":"7.02171470","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"beets","2":"leaves","3":"0.22266662","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"beets","2":"Sweet Merlin","3":"6.38678414","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"broccoli","2":"Main Crop Bravado","3":"0.70768302","4":"D","5":"7","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"broccoli","2":"Main Crop Bravado","3":"0.70768302","4":"I","5":"7","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"broccoli","2":"Yod Fah","3":"0.82011864","4":"P","5":"25","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"carrots","2":"Bolero","3":"3.99477144","4":"H","5":"50","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"Bolero","3":"3.99477144","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"carrots","2":"Dragon","3":"2.15832298","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"Dragon","3":"2.15832298","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"carrots","2":"greens","3":"0.37258078","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"carrots","2":"King Midas","3":"1.84085770","4":"H","5":"50","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"King Midas","3":"1.84085770","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"chives","2":"perrenial","3":"0.01763696","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"cilantro","2":"cilantro","3":"0.11464024","4":"potD","5":"15","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"cilantro","2":"cilantro","3":"0.11464024","4":"E","5":"20","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"corn","2":"Dorinny Sweet","3":"11.40670388","4":"A","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"corn","2":"Golden Bantam","3":"1.60275874","4":"B","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"cucumbers","2":"pickling","3":"43.60958822","4":"L","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"edamame","2":"edamame","3":"6.09136506","4":"O","5":"25","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"hot peppers","2":"thai","3":"0.14770954","4":"potB","5":"1","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"hot peppers","2":"variety","3":"1.32056738","4":"potC","5":"6","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"jalapeño","2":"giant","3":"3.53621048","4":"L","5":"4","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"kale","2":"Heirloom Lacinto","3":"4.53710796","4":"P","5":"30","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"kale","2":"Heirloom Lacinto","3":"4.53710796","4":"front","5":"30","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"kohlrabi","2":"Crispy Colors Duo","3":"0.42108242","4":"front","5":"10","6":"2020-05-20","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Farmer's Market Blend","3":"3.80296950","4":"C","5":"60","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Farmer's Market Blend","3":"3.80296950","4":"L","5":"60","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Lettuce Mixture","3":"4.19539186","4":"G","5":"200","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"lettuce","2":"mustard greens","3":"0.05070626","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"lettuce","2":"reseed","3":"0.09920790","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"lettuce","2":"Tatsoi","3":"2.89466606","4":"P","5":"25","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"onions","2":"Delicious Duo","3":"0.58422430","4":"P","5":"25","6":"2020-04-26","7":"FALSE","8":"NA"},{"1":"onions","2":"Long Keeping Rainbow","3":"2.90789378","4":"H","5":"40","6":"2020-04-26","7":"FALSE","8":"NA"},{"1":"peas","2":"Magnolia Blossom","3":"7.45822946","4":"B","5":"24","6":"2020-04-19","7":"TRUE","8":"NA"},{"1":"peas","2":"Super Sugar Snap","3":"9.56805080","4":"A","5":"22","6":"2020-04-19","7":"TRUE","8":"NA"},{"1":"peppers","2":"green","3":"1.80337916","4":"K","5":"12","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"green","3":"1.80337916","4":"O","5":"5","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.08426338","4":"potA","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.08426338","4":"potA","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.08426338","4":"potD","5":"1","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"potatoes","2":"purple","3":"2.35894340","4":"D","5":"5","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"Russet","3":"1.38670598","4":"D","5":"8","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"yellow","3":"5.61737176","4":"I","5":"10","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"yellow","3":"5.61737176","4":"I","5":"8","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"Cinderella's Carraige","3":"32.87308882","4":"B","5":"3","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"New England Sugar","3":"35.40178796","4":"K","5":"4","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"saved","3":"56.27072088","4":"B","5":"8","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"C","5":"20","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"G","5":"30","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"H","5":"15","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"raspberries","2":"perrenial","3":"1.85849466","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"spinach","2":"Catalina","3":"2.03486426","4":"H","5":"50","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"spinach","2":"Catalina","3":"2.03486426","4":"E","5":"100","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"squash","2":"Blue (saved)","3":"35.09093654","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Blue (saved)","3":"35.09093654","4":"B","5":"8","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"delicata","3":"9.81055900","4":"K","5":"8","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"11.80574010","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"11.80574010","4":"B","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"11.80574010","4":"side","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Waltham Butternut","3":"17.98308534","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Waltham Butternut","3":"17.98308534","4":"K","5":"6","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"strawberries","2":"perrenial","3":"1.30513504","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"Swiss chard","2":"Neon Glow","3":"5.39029590","4":"M","5":"25","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"tomatoes","2":"Amish Paste","3":"46.26174608","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Amish Paste","3":"46.26174608","4":"N","5":"2","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Better Boy","3":"27.60404702","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Better Boy","3":"27.60404702","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Big Beef","3":"20.50076138","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Black Krim","3":"14.46010258","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Bonny Best","3":"21.44654336","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Brandywine","3":"14.72465698","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Cherokee Purple","3":"15.22951496","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"grape","3":"26.05860840","4":"O","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Jet Star","3":"14.26168678","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Mortgage Lifter","3":"17.81773884","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"died"},{"1":"tomatoes","2":"Mortgage Lifter","3":"17.81773884","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Old German","3":"18.85170562","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"35.62445458","4":"N","5":"1","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"35.62445458","4":"J","5":"1","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"35.62445458","4":"front","5":"5","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"35.62445458","4":"O","5":"2","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"zucchini","2":"Romanesco","3":"70.82782674","4":"D","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


  3. I would like to understand how much money I "saved" by gardening, for each vegetable type. Describe how I could use the `garden_harvest` and `supply_cost` datasets, along with data from somewhere like [this](https://products.wholefoodsmarket.com/search?sort=relevance&store=10542) to answer this question. You can answer this in words, referencing various join functions. You don't need R code but could provide some if it's helpful.
  
  You could answer this by summing the total cost it took to harvest the vegetables on your own and compare that to the price for the same amount of each vegetable from a store like whole foods.

  4. Subset the data to tomatoes. Reorder the tomato varieties from smallest to largest first harvest date. Create a barplot of total harvest in pounds for each variety, in the new order.


```r
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

![](03_exercises_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

  5. In the `garden_harvest` data, create two new variables: one that makes the varieties lowercase and another that finds the length of the variety name. Arrange the data by vegetable and length of variety name (smallest to largest), with one row for each vegetable variety. HINT: use `str_to_lower()`, `str_length()`, and `distinct()`.
  

```r
garden_harvest %>% 
  mutate(lowercase = str_to_lower(variety)) %>% 
  mutate(variety_length = str_length(lowercase)) %>% 
  distinct(vegetable, variety, variety_length) %>% 
  arrange(variety_length)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["variety_length"],"name":[3],"type":["int"],"align":["right"]}],"data":[{"1":"hot peppers","2":"thai","3":"4"},{"1":"tomatoes","2":"grape","3":"5"},{"1":"jalapeño","2":"giant","3":"5"},{"1":"peppers","2":"green","3":"5"},{"1":"pumpkins","2":"saved","3":"5"},{"1":"lettuce","2":"reseed","3":"6"},{"1":"beets","2":"leaves","3":"6"},{"1":"lettuce","2":"Tatsoi","3":"6"},{"1":"carrots","2":"Dragon","3":"6"},{"1":"carrots","2":"Bolero","3":"6"},{"1":"potatoes","2":"purple","3":"6"},{"1":"potatoes","2":"yellow","3":"6"},{"1":"carrots","2":"greens","3":"6"},{"1":"potatoes","2":"Russet","3":"6"},{"1":"hot peppers","2":"variety","3":"7"},{"1":"peppers","2":"variety","3":"7"},{"1":"broccoli","2":"Yod Fah","3":"7"},{"1":"edamame","2":"edamame","3":"7"},{"1":"spinach","2":"Catalina","3":"8"},{"1":"cilantro","2":"cilantro","3":"8"},{"1":"cucumbers","2":"pickling","3":"8"},{"1":"tomatoes","2":"Big Beef","3":"8"},{"1":"tomatoes","2":"Jet Star","3":"8"},{"1":"squash","2":"delicata","3":"8"},{"1":"squash","2":"Red Kuri","3":"8"},{"1":"chives","2":"perrenial","3":"9"},{"1":"strawberries","2":"perrenial","3":"9"},{"1":"asparagus","2":"asparagus","3":"9"},{"1":"Swiss chard","2":"Neon Glow","3":"9"},{"1":"raspberries","2":"perrenial","3":"9"},{"1":"zucchini","2":"Romanesco","3":"9"},{"1":"tomatoes","2":"Bonny Best","3":"10"},{"1":"carrots","2":"King Midas","3":"10"},{"1":"tomatoes","2":"Better Boy","3":"10"},{"1":"tomatoes","2":"Old German","3":"10"},{"1":"tomatoes","2":"Brandywine","3":"10"},{"1":"tomatoes","2":"Black Krim","3":"10"},{"1":"tomatoes","2":"volunteers","3":"10"},{"1":"tomatoes","2":"Amish Paste","3":"11"},{"1":"beets","2":"Sweet Merlin","3":"12"},{"1":"squash","2":"Blue (saved)","3":"12"},{"1":"basil","2":"Isle of Naxos","3":"13"},{"1":"onions","2":"Delicious Duo","3":"13"},{"1":"corn","2":"Dorinny Sweet","3":"13"},{"1":"corn","2":"Golden Bantam","3":"13"},{"1":"lettuce","2":"mustard greens","3":"14"},{"1":"beets","2":"Gourmet Golden","3":"14"},{"1":"lettuce","2":"Lettuce Mixture","3":"15"},{"1":"tomatoes","2":"Cherokee Purple","3":"15"},{"1":"tomatoes","2":"Mortgage Lifter","3":"15"},{"1":"radish","2":"Garden Party Mix","3":"16"},{"1":"kale","2":"Heirloom Lacinto","3":"16"},{"1":"peas","2":"Magnolia Blossom","3":"16"},{"1":"peas","2":"Super Sugar Snap","3":"16"},{"1":"beans","2":"Bush Bush Slender","3":"17"},{"1":"broccoli","2":"Main Crop Bravado","3":"17"},{"1":"kohlrabi","2":"Crispy Colors Duo","3":"17"},{"1":"squash","2":"Waltham Butternut","3":"17"},{"1":"pumpkins","2":"New England Sugar","3":"17"},{"1":"beans","2":"Chinese Red Noodle","3":"18"},{"1":"beans","2":"Classic Slenderette","3":"19"},{"1":"onions","2":"Long Keeping Rainbow","3":"20"},{"1":"lettuce","2":"Farmer's Market Blend","3":"21"},{"1":"pumpkins","2":"Cinderella's Carraige","3":"21"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  6. In the `garden_harvest` data, find all distinct vegetable varieties that have "er" or "ar" in their name. HINT: `str_detect()` with an "or" statement (use the | for "or") and `distinct()`.


```r
garden_harvest %>%   
  mutate(hasEAR = str_detect(variety, "er") | str_detect(variety, "ar")) %>% 
  distinct(variety, hasEAR)
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["variety"],"name":[1],"type":["chr"],"align":["left"]},{"label":["hasEAR"],"name":[2],"type":["lgl"],"align":["right"]}],"data":[{"1":"reseed","2":"FALSE"},{"1":"Garden Party Mix","2":"TRUE"},{"1":"Farmer's Market Blend","2":"TRUE"},{"1":"Catalina","2":"FALSE"},{"1":"leaves","2":"FALSE"},{"1":"Heirloom Lacinto","2":"FALSE"},{"1":"Magnolia Blossom","2":"FALSE"},{"1":"Super Sugar Snap","2":"TRUE"},{"1":"perrenial","2":"TRUE"},{"1":"Tatsoi","2":"FALSE"},{"1":"asparagus","2":"TRUE"},{"1":"Neon Glow","2":"FALSE"},{"1":"cilantro","2":"FALSE"},{"1":"Isle of Naxos","2":"FALSE"},{"1":"mustard greens","2":"TRUE"},{"1":"Romanesco","2":"FALSE"},{"1":"Bush Bush Slender","2":"TRUE"},{"1":"Gourmet Golden","2":"FALSE"},{"1":"Sweet Merlin","2":"TRUE"},{"1":"pickling","2":"FALSE"},{"1":"grape","2":"FALSE"},{"1":"Delicious Duo","2":"FALSE"},{"1":"giant","2":"FALSE"},{"1":"thai","2":"FALSE"},{"1":"variety","2":"TRUE"},{"1":"Long Keeping Rainbow","2":"FALSE"},{"1":"Big Beef","2":"FALSE"},{"1":"Bonny Best","2":"FALSE"},{"1":"Lettuce Mixture","2":"FALSE"},{"1":"King Midas","2":"FALSE"},{"1":"Cherokee Purple","2":"TRUE"},{"1":"Better Boy","2":"TRUE"},{"1":"Dragon","2":"FALSE"},{"1":"Amish Paste","2":"FALSE"},{"1":"Mortgage Lifter","2":"TRUE"},{"1":"Yod Fah","2":"FALSE"},{"1":"Old German","2":"TRUE"},{"1":"Jet Star","2":"TRUE"},{"1":"Bolero","2":"TRUE"},{"1":"Brandywine","2":"FALSE"},{"1":"Black Krim","2":"FALSE"},{"1":"volunteers","2":"TRUE"},{"1":"green","2":"FALSE"},{"1":"Classic Slenderette","2":"TRUE"},{"1":"purple","2":"FALSE"},{"1":"yellow","2":"FALSE"},{"1":"Chinese Red Noodle","2":"FALSE"},{"1":"edamame","2":"FALSE"},{"1":"Dorinny Sweet","2":"FALSE"},{"1":"Golden Bantam","2":"FALSE"},{"1":"greens","2":"FALSE"},{"1":"saved","2":"FALSE"},{"1":"Blue (saved)","2":"FALSE"},{"1":"Cinderella's Carraige","2":"TRUE"},{"1":"Main Crop Bravado","2":"FALSE"},{"1":"Russet","2":"FALSE"},{"1":"Crispy Colors Duo","2":"FALSE"},{"1":"delicata","2":"FALSE"},{"1":"Waltham Butternut","2":"TRUE"},{"1":"Red Kuri","2":"FALSE"},{"1":"New England Sugar","2":"TRUE"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


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


```r
data_site <- 
  "https://www.macalester.edu/~dshuman1/data/112/2014-Q4-Trips-History-Data.rds" 
Trips <- readRDS(gzcon(url(data_site)))
Stations<-read_csv("http://www.macalester.edu/~dshuman1/data/112/DC-Stations.csv")
```

**NOTE:** The `Trips` data table is a random subset of 10,000 trips from the full quarterly data. Start with this small data table to develop your analysis commands. **When you have this working well, you should access the full data set of more than 600,000 events by removing `-Small` from the name of the `data_site`.**

### Temporal patterns

It's natural to expect that bikes are rented more at some times of day, some days of the week, some months of the year than others. The variable `sdate` gives the time (including the date) that the rental started. Make the following plots and interpret them:

  7. A density plot, which is a smoothed out histogram, of the events versus `sdate`. Use `geom_density()`.
  

```r
Trips %>% 
  ggplot()+
  geom_density(aes(x = sdate)) +
  ggtitle("Events by Date") +
  xlab("Date") +
  ylab("Density")
```

![](03_exercises_files/figure-html/unnamed-chunk-7-1.png)<!-- -->
  
  8. A density plot of the events versus time of day.  You can use `mutate()` with `lubridate`'s  `hour()` and `minute()` functions to extract the hour of the day and minute within the hour from `sdate`. Hint: A minute is 1/60 of an hour, so create a variable where 3:30 is 3.5 and 3:45 is 3.75.
  

```r
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60) %>% 
  ggplot()+
  geom_density(aes(x = time)) +
  ggtitle("Density by Time of Day") +
  xlab("Time") +
  ylab("Density")
```

![](03_exercises_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
  
  9. A bar graph of the events versus day of the week. Put day on the y-axis.
  

```r
Trips %>% 
  mutate(dayofweek = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_bar(aes(y = dayofweek)) +
  ggtitle("Events By Day of Week") +
  xlab("Count") +
  ylab("Day of the Week")
```

![](03_exercises_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
  
  10. Facet your graph from exercise 8. by day of the week. Is there a pattern?
  

```r
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time))+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week") +
  xlab("Time") +
  ylab("Density")
```

![](03_exercises_files/figure-html/unnamed-chunk-10-1.png)<!-- -->
  On the weekend more people are going in the middle of the day, whereas on the weekdays it is only in the morning and the evenings.
  
  
The variable `client` describes whether the renter is a regular user (level `Registered`) or has not joined the bike-rental organization (`Causal`). The next set of exercises investigate whether these two different categories of users show different rental behavior and how `client` interacts with the patterns you found in the previous exercises. Repeat the graphic from Exercise \@ref(exr:exr-temp) (d) with the following changes:

  11. Change the graph from exercise 10 to set the `fill` aesthetic for `geom_density()` to the `client` variable. You should also set `alpha = .5` for transparency and `color=NA` to suppress the outline of the density function.
  

```r
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = client), alpha = 0.5, color = NA)+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week by Client") +
  xlab("Time") +
  ylab("Density")
```

![](03_exercises_files/figure-html/unnamed-chunk-11-1.png)<!-- -->

  12. Change the previous graph by adding the argument `position = position_stack()` to `geom_density()`. In your opinion, is this better or worse in terms of telling a story? What are the advantages/disadvantages of each?
  

```r
Trips %>% 
  mutate(minute = minute(sdate), hour = hour(sdate), time = hour + minute/60, weekday = wday(sdate, label = TRUE)) %>% 
  ggplot()+
  geom_density(aes(x = time, fill = client),  alpha = 0.5, color = NA, position = position_stack())+
  facet_wrap(~weekday) +
  ggtitle("Events by Time by Day of the Week by Client") +
  xlab("Time") +
  ylab("Density")
```

![](03_exercises_files/figure-html/unnamed-chunk-12-1.png)<!-- -->
  I think this allows you to distinguish between the two categories better, but it hides the bottom of the plot that is put behind, so you could miss part of the story.
  
  
  13. Add a new variable to the dataset called `weekend` which will be "weekend" if the day is Saturday or Sunday and  "weekday" otherwise (HINT: use the `ifelse()` function and the `wday()` function from `lubridate`). Then, update the graph from the previous problem by faceting on the new `weekend` variable. 
  

```r
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

![](03_exercises_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
  
  14. Change the graph from the previous problem to facet on `client` and fill with `weekday`. What information does this graph tell you that the previous didn't? Is one graph better than the other?
  

```r
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

![](03_exercises_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
  Generally these graphs tell us the same information, but in the second one it is easier to see that the casual people make up more of the density in the middle of the day, especially on the weekdays.
  
  
### Spatial patterns

  15. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. We will improve this plot next week when we learn about maps!
  

```r
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

![](03_exercises_files/figure-html/unnamed-chunk-15-1.png)<!-- -->
  
  16. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? (Again, we'll improve this next week when we learn about maps).
  

```r
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

![](03_exercises_files/figure-html/unnamed-chunk-16-1.png)<!-- -->
  
  Jefferson & 14th, Lincoln Memorial, Washington & Independence, 4th & Constitution, 1st & Constitution, and Smithsonian / Jefferson Dr & 12th St SW have the highest percentage of casual clients.
  
### Spatiotemporal patterns

  17. Make a table with the ten station-date combinations (e.g., 14th & V St., 2014-10-14) with the highest number of departures, sorted from most departures to fewest. Save this to a new dataset and print out the dataset. Hint: `as_date(sdate)` converts `sdate` from date-time format to date format. 
  

```r
station_date_frequency <- Trips %>% 
  mutate(date = as.Date(sdate)) %>% 
  group_by(sstation, date) %>%
  summarise(total = n()) %>% 
  arrange(desc(total)) %>% 
  filter(total >= 322)
```
  
  18. Use a join operation to make a table with only those trips whose departures match those top ten station-date combinations from the previous part.
  

```r
station_date_frequency %>% 
  mutate(sdate = as.Date(date)) %>%
  left_join(Trips, by = "sdate")
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["sstation.x"],"name":[1],"type":["chr"],"align":["left"]},{"label":["date"],"name":[2],"type":["date"],"align":["right"]},{"label":["total"],"name":[3],"type":["int"],"align":["right"]},{"label":["sdate"],"name":[4],"type":["S3: POSIXct"],"align":["right"]},{"label":["duration"],"name":[5],"type":["chr"],"align":["left"]},{"label":["sstation.y"],"name":[6],"type":["chr"],"align":["left"]},{"label":["edate"],"name":[7],"type":["S3: POSIXct"],"align":["right"]},{"label":["estation"],"name":[8],"type":["chr"],"align":["left"]},{"label":["bikeno"],"name":[9],"type":["chr"],"align":["left"]},{"label":["client"],"name":[10],"type":["chr"],"align":["left"]}],"data":[{"1":"Lincoln Memorial","2":"2014-10-25","3":"386","4":"2014-10-25","5":"0h 5m 31s","6":"10th & Florida Ave NW","7":"2014-10-25 00:06:00","8":"Georgia Ave and Fairmont St NW","9":"W21314","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-25","3":"386","4":"2014-10-25","5":"0h 7m 40s","6":"3rd & H St NE","7":"2014-10-25 00:08:00","8":"Gallaudet / 8th St & Florida Ave NE","9":"W00982","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 4m 25s","6":"16th & Harvard St NW","7":"2014-10-18 00:04:00","8":"Park Rd & Holmead Pl NW","9":"W20793","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 11m 45s","6":"3000 Connecticut Ave NW / National Zoo","7":"2014-10-18 00:12:00","8":"Idaho Ave & Newark St NW [on 2nd District patio]","9":"W20247","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 19m 57s","6":"14th St & Spring Rd NW","7":"2014-10-18 00:20:00","8":"15th & P St NW","9":"W21208","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 27m 45s","6":"USDA / 12th & Independence Ave SW","7":"2014-10-18 00:28:00","8":"Massachusetts Ave & Dupont Circle NW","9":"W20331","10":"Casual"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 8m 36s","6":"14th & R St NW","7":"2014-10-18 00:09:00","8":"New Jersey Ave & R St NW","9":"W20341","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-18","3":"354","4":"2014-10-18","5":"0h 8m 32s","6":"14th & R St NW","7":"2014-10-18 00:09:00","8":"New Jersey Ave & R St NW","9":"W20278","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-26","3":"349","4":"2014-10-26","5":"0h 11m 50s","6":"15th & P St NW","7":"2014-10-26 00:11:00","8":"14th & Harvard St NW","9":"W01331","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-26","3":"349","4":"2014-10-26","5":"0h 21m 49s","6":"Court House Metro / 15th & N Uhle St","7":"2014-10-26 00:22:00","8":"S Arlington Mill Dr & Campbell Ave","9":"W00816","10":"Casual"},{"1":"Lincoln Memorial","2":"2014-10-26","3":"349","4":"2014-10-26","5":"0h 12m 38s","6":"5th & K St NW","7":"2014-10-26 00:12:00","8":"4th & E St SW","9":"W21628","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-26","3":"349","4":"2014-10-26","5":"0h 6m 0s","6":"13th & D St NE","7":"2014-10-26 00:06:00","8":"19th & East Capitol St SE","9":"W00067","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-26","3":"349","4":"2014-10-26","5":"0h 17m 41s","6":"21st & M St NW","7":"2014-10-26 00:18:00","8":"3rd & H St NE","9":"W00736","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-27","3":"345","4":"2014-10-27","5":"0h 21m 53s","6":"Thomas Circle","7":"2014-10-27 00:22:00","8":"5th & K St NW","9":"W20088","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-04","3":"337","4":"2014-10-04","5":"0h 6m 29s","6":"1st & M St NE","7":"2014-10-04 00:06:00","8":"Florida Ave & R St NW","9":"W00484","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-04","3":"337","4":"2014-10-04","5":"0h 8m 20s","6":"12th & U St NW","7":"2014-10-04 00:08:00","8":"1st & Rhode Island Ave NW","9":"W20663","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-02","3":"334","4":"2014-10-02","5":"0h 7m 24s","6":"14th & R St NW","7":"2014-10-02 00:07:00","8":"Convention Center / 7th & M St NW","9":"W20024","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-02","3":"334","4":"2014-10-02","5":"0h 4m 7s","6":"M St & Delaware Ave NE","7":"2014-10-02 00:04:00","8":"Eckington Pl & Q St NE","9":"W01186","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-02","3":"334","4":"2014-10-02","5":"0h 9m 14s","6":"14th & V St NW","7":"2014-10-02 00:09:00","8":"3rd & Elm St NW","9":"W20823","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-02","3":"334","4":"2014-10-02","5":"0h 9m 15s","6":"Ward Circle / American University","7":"2014-10-02 00:10:00","8":"Connecticut Ave & Tilden St NW","9":"W20228","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-28","3":"333","4":"2014-10-28","5":"0h 10m 8s","6":"M St & Pennsylvania Ave NW","7":"2014-10-28 00:10:00","8":"34th St & Wisconsin Ave NW","9":"W21146","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-28","3":"333","4":"2014-10-28","5":"0h 7m 47s","6":"Minnesota Ave Metro/DOES","7":"2014-10-28 00:08:00","8":"Nannie Helen Burroughs Ave & 49th St NE","9":"W20464","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-28","3":"333","4":"2014-10-28","5":"0h 14m 46s","6":"1st & M St NE","7":"2014-10-28 00:15:00","8":"19th & East Capitol St SE","9":"W01243","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-12","3":"328","4":"2014-10-12","5":"0h 7m 7s","6":"Columbia Rd & Belmont St NW","7":"2014-10-12 00:07:00","8":"Massachusetts Ave & Dupont Circle NW","9":"W01387","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-12","3":"328","4":"2014-10-12","5":"0h 8m 7s","6":"7th & F St NW / National Portrait Gallery","7":"2014-10-12 00:08:00","8":"3rd & H St NE","9":"W21103","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-12","3":"328","4":"2014-10-12","5":"0h 18m 9s","6":"6th & H St NE","7":"2014-10-12 00:18:00","8":"Convention Center / 7th & M St NW","9":"W20678","10":"Registered"},{"1":"Lincoln Memorial","2":"2014-10-12","3":"328","4":"2014-10-12","5":"0h 10m 42s","6":"14th St & Spring Rd NW","7":"2014-10-12 00:11:00","8":"Massachusetts Ave & Dupont Circle NW","9":"W01275","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-09","3":"327","4":"2014-10-09","5":"NA","6":"NA","7":"<NA>","8":"NA","9":"NA","10":"NA"},{"1":"Columbus Circle / Union Station","2":"2014-10-08","3":"322","4":"2014-10-08","5":"0h 17m 33s","6":"7th & T St NW","7":"2014-10-08 00:17:00","8":"Neal St & Trinidad Ave NE","9":"W01467","10":"Registered"},{"1":"Columbus Circle / Union Station","2":"2014-10-08","3":"322","4":"2014-10-08","5":"0h 14m 34s","6":"Thomas Circle","7":"2014-10-08 00:15:00","8":"15th & P St NW","9":"W00251","10":"Registered"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  
  19. Build on the code from the previous problem (ie. copy that code below and then %>% into the next step.) and group the trips by client type and day of the week (use the name, not the number). Find the proportion of trips by day within each client type (ie. the proportions for all 7 days within each client type add up to 1). Display your results so day of week is a column and there is a column for each client type. Interpret your results.
  
  

```r
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

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["dayofweek"],"name":[1],"type":["ord"],"align":["right"]},{"label":["Casual"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Registered"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["NA"],"name":[4],"type":["dbl"],"align":["right"]}],"data":[{"1":"Sun","2":"0.1111111","3":"0.8888889","4":"NA"},{"1":"Mon","2":"NA","3":"1.0000000","4":"NA"},{"1":"Tue","2":"NA","3":"1.0000000","4":"NA"},{"1":"Wed","2":"NA","3":"1.0000000","4":"NA"},{"1":"Thu","2":"NA","3":"0.8000000","4":"0.2"},{"1":"Sat","2":"0.1000000","3":"0.9000000","4":"NA"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

Casual riders don't tend to ride on weekdays and overall registered riders take up a larger proportion. 

**DID YOU REMEMBER TO GO BACK AND CHANGE THIS SET OF EXERCISES TO THE LARGER DATASET? IF NOT, DO THAT NOW.**

## GitHub link

  20. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 03_exercises.Rmd, provide a link to the 03_exercises.md file, which is the one that will be most readable on GitHub.

## Challenge problem! 

This problem uses the data from the Tidy Tuesday competition this week, `kids`. If you need to refresh your memory on the data, read about it [here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-15/readme.md). 

  21. In this exercise, you are going to try to replicate the graph below, created by Georgios Karamanis. I'm sure you can find the exact code on GitHub somewhere, but **DON'T DO THAT!** You will only be graded for putting an effort into this problem. So, give it a try and see how far you can get without doing too much googling. HINT: use `facet_geo()`. The graphic won't load below since it came from a location on my computer. So, you'll have to reference the original html on the moodle page to see it.
  




```r
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

![](03_exercises_files/figure-html/unnamed-chunk-20-1.png)<!-- -->


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
