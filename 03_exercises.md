---
title: 'Weekly Exercises #3'
author: "Anna Leidner"
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
library(gardenR)       # for Lisa's garden data
library(lubridate)     # for date manipulation
library(ggthemes)      # for even more plotting themes
library(geofacet)      # for special faceting with US map layout
library(janitor)
theme_set(theme_minimal())       # My favorite ggplot() theme :)
```


```r
# Lisa's garden data
data("garden_harvest")

# Seeds/plants (and other garden supply) costs
data("garden_spending")

# Planting dates and locations
data("garden_planting")

# Tidy Tuesday dog breed data
breed_traits <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/breed_traits.csv')
trait_description <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/trait_description.csv')
breed_rank_all <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2022/2022-02-01/breed_rank.csv')

# Tidy Tuesday data for challenge problem
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

  1. Summarize the `garden_harvest` data to find the total harvest weight in pounds for each vegetable and day of week (HINT: use the `wday()` function from `lubridate`). Display the results so that the vegetables are rows but the days of the week are columns.


```r
garden_harvest %>% 
  mutate(day = wday(date, label = TRUE)) %>%
  group_by(vegetable, day) %>% #"for each" = group by
  mutate(wt_lbs = weight * 0.00220462) %>%
  summarize(total_weight = sum(wt_lbs)) %>%
  pivot_wider(names_from = day,
              values_from = total_weight) %>%
  mutate_all(~replace(., is.na(.), 0))
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Sat"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["Mon"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["Tue"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["Thu"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["Fri"],"name":[6],"type":["dbl"],"align":["right"]},{"label":["Sun"],"name":[7],"type":["dbl"],"align":["right"]},{"label":["Wed"],"name":[8],"type":["dbl"],"align":["right"]}],"data":[{"1":"apple","2":"0.34392072","3":"0.0000000","4":"0.00000000","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"asparagus","2":"0.04409240","3":"0.0000000","4":"0.00000000","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"basil","2":"0.41005932","3":"0.0661386","4":"0.11023100","5":"0.02645544","6":"0.46737944","7":"0.00000000","8":"0.00000000"},{"1":"beans","2":"4.70906832","3":"6.5080382","4":"4.38719380","5":"3.39291018","6":"1.52559704","7":"1.91361016","8":"4.08295624"},{"1":"beets","2":"0.37919464","3":"0.6724091","4":"0.15873264","5":"11.89172028","6":"0.02425082","7":"0.32187452","8":"0.18298346"},{"1":"broccoli","2":"0.00000000","3":"0.8201186","4":"0.00000000","5":"0.00000000","6":"0.16534650","7":"1.25883802","8":"0.70768302"},{"1":"carrots","2":"2.33028334","3":"0.8708249","4":"0.35273920","5":"2.67420406","6":"2.13848140","7":"2.93655384","8":"5.56225626"},{"1":"chives","2":"0.00000000","3":"0.0000000","4":"0.00000000","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.01763696"},{"1":"cilantro","2":"0.03747854","3":"0.0000000","4":"0.00440924","5":"0.00000000","6":"0.07275246","7":"0.00000000","8":"0.00000000"},{"1":"corn","2":"1.31615814","3":"0.7583893","4":"0.72752460","5":"0.00000000","6":"3.44802568","7":"1.45725382","8":"5.30211110"},{"1":"cucumbers","2":"9.64080326","3":"4.7752069","4":"10.04645334","5":"3.30693000","6":"7.42956940","7":"3.10410496","8":"5.30652034"},{"1":"edamame","2":"4.68922674","3":"0.0000000","4":"1.40213832","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"hot peppers","2":"0.00000000","3":"1.2588380","4":"0.14109568","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.06834322"},{"1":"jalapeño","2":"1.50796008","3":"5.5534378","4":"0.54895038","5":"0.22487124","6":"1.29411194","7":"0.26234978","8":"0.48060716"},{"1":"kale","2":"1.49032312","3":"2.0679336","4":"0.28219136","5":"0.27998674","6":"0.38139926","7":"0.82673250","8":"0.61729360"},{"1":"kohlrabi","2":"0.00000000","3":"0.0000000","4":"0.00000000","5":"0.42108242","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"lettuce","2":"1.31615814","3":"2.4581513","4":"0.91712192","5":"2.45153744","6":"1.80117454","7":"1.46607230","8":"1.18608556"},{"1":"onions","2":"1.91361016","3":"0.5092672","4":"0.70768302","5":"0.60186126","6":"0.07275246","7":"0.26014516","8":"0.00000000"},{"1":"peas","2":"2.85277828","3":"4.6341112","4":"2.06793356","5":"3.39731942","6":"0.93696350","7":"2.05691046","8":"1.08026380"},{"1":"peppers","2":"1.38229674","3":"2.5264945","4":"1.44402610","5":"0.70988764","6":"0.33510224","7":"0.50265336","8":"2.44271896"},{"1":"potatoes","2":"2.80207202","3":"0.9700328","4":"0.00000000","5":"11.85203712","6":"3.74124014","7":"0.00000000","8":"4.57017726"},{"1":"pumpkins","2":"92.68883866","3":"30.1195184","4":"31.85675900","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"radish","2":"0.23148510","3":"0.1962112","4":"0.09479866","5":"0.14770954","6":"0.19400656","7":"0.08157094","8":"0.00000000"},{"1":"raspberries","2":"0.53351804","3":"0.1300726","4":"0.33510224","5":"0.28880522","6":"0.57099658","7":"0.00000000","8":"0.00000000"},{"1":"rutabaga","2":"6.89825598","3":"0.0000000","4":"0.00000000","5":"0.00000000","6":"3.57809826","7":"19.26396956","8":"0.00000000"},{"1":"spinach","2":"0.26014516","3":"0.1477095","4":"0.49603950","5":"0.23368972","6":"0.19621118","7":"0.48722102","8":"0.21384814"},{"1":"squash","2":"56.22221924","3":"24.3345956","4":"18.46810174","5":"0.00000000","6":"0.00000000","7":"0.00000000","8":"0.00000000"},{"1":"strawberries","2":"0.16975574","3":"0.4784025","4":"0.00000000","5":"0.08818480","6":"0.48722102","7":"0.08157094","8":"0.00000000"},{"1":"Swiss chard","2":"0.73413846","3":"1.0736499","4":"0.07054784","5":"2.23107544","6":"0.61729360","7":"1.24781492","8":"0.90830344"},{"1":"tomatoes","2":"35.12621046","3":"11.4926841","4":"48.75076206","5":"34.51773534","6":"85.07628580","7":"75.60964752","8":"58.26590198"},{"1":"zucchini","2":"3.41495638","3":"12.1959578","4":"16.46851140","5":"34.63017096","6":"18.72163304","7":"12.23564100","8":"2.04147812"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  2. Summarize the `garden_harvest` data to find the total harvest in pound for each vegetable variety and then try adding the plot from the `garden_planting` table. This will not turn out perfectly. What is the problem? How might you fix it?


```r
garden_harvest %>%
  group_by(vegetable, variety) %>%
  mutate(wt_lbs = weight * 0.00220462) %>%
  summarize(total_wt = sum(wt_lbs)) %>%
  left_join(garden_planting, 
            by = c("vegetable", "variety")) 
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["total_wt"],"name":[3],"type":["dbl"],"align":["right"]},{"label":["plot"],"name":[4],"type":["chr"],"align":["left"]},{"label":["number_seeds_planted"],"name":[5],"type":["dbl"],"align":["right"]},{"label":["date"],"name":[6],"type":["date"],"align":["right"]},{"label":["number_seeds_exact"],"name":[7],"type":["lgl"],"align":["right"]},{"label":["notes"],"name":[8],"type":["chr"],"align":["left"]}],"data":[{"1":"apple","2":"unknown","3":"0.34392072","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"asparagus","2":"asparagus","3":"0.04409240","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"basil","2":"Isle of Naxos","3":"1.08026380","4":"potB","5":"40","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"beans","2":"Bush Bush Slender","3":"22.12997556","4":"M","5":"30","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"beans","2":"Bush Bush Slender","3":"22.12997556","4":"D","5":"10","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"beans","2":"Chinese Red Noodle","3":"0.78484472","4":"K","5":"5","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"beans","2":"Chinese Red Noodle","3":"0.78484472","4":"L","5":"5","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"beans","2":"Classic Slenderette","3":"3.60455370","4":"E","5":"29","6":"2020-06-20","7":"TRUE","8":"NA"},{"1":"beets","2":"Gourmet Golden","3":"7.02171470","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"beets","2":"leaves","3":"0.22266662","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"beets","2":"Sweet Merlin","3":"6.38678414","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"broccoli","2":"Main Crop Bravado","3":"2.13186754","4":"D","5":"7","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"broccoli","2":"Main Crop Bravado","3":"2.13186754","4":"I","5":"7","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"broccoli","2":"Yod Fah","3":"0.82011864","4":"P","5":"25","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"carrots","2":"Bolero","3":"8.29157582","4":"H","5":"50","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"Bolero","3":"8.29157582","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"carrots","2":"Dragon","3":"4.10500244","4":"H","5":"40","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"Dragon","3":"4.10500244","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"carrots","2":"greens","3":"0.37258078","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"carrots","2":"King Midas","3":"4.09618396","4":"H","5":"50","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"carrots","2":"King Midas","3":"4.09618396","4":"L","5":"50","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"chives","2":"perrenial","3":"0.01763696","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"cilantro","2":"cilantro","3":"0.11464024","4":"potD","5":"15","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"cilantro","2":"cilantro","3":"0.11464024","4":"E","5":"20","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"corn","2":"Dorinny Sweet","3":"11.40670388","4":"A","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"corn","2":"Golden Bantam","3":"1.60275874","4":"B","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"cucumbers","2":"pickling","3":"43.60958822","4":"L","5":"20","6":"2020-05-25","7":"FALSE","8":"NA"},{"1":"edamame","2":"edamame","3":"6.09136506","4":"O","5":"25","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"hot peppers","2":"thai","3":"0.14770954","4":"potB","5":"1","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"hot peppers","2":"variety","3":"1.32056738","4":"potC","5":"6","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"jalapeño","2":"giant","3":"9.87228836","4":"L","5":"4","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"kale","2":"Heirloom Lacinto","3":"5.94586014","4":"P","5":"30","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"kale","2":"Heirloom Lacinto","3":"5.94586014","4":"front","5":"30","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"kohlrabi","2":"Crispy Colors Duo","3":"0.42108242","4":"front","5":"10","6":"2020-05-20","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Farmer's Market Blend","3":"3.80296950","4":"C","5":"60","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Farmer's Market Blend","3":"3.80296950","4":"L","5":"60","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"lettuce","2":"Lettuce Mixture","3":"4.74875148","4":"G","5":"200","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"lettuce","2":"mustard greens","3":"0.05070626","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"lettuce","2":"reseed","3":"0.09920790","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"lettuce","2":"Tatsoi","3":"2.89466606","4":"P","5":"25","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"onions","2":"Delicious Duo","3":"0.75398004","4":"P","5":"25","6":"2020-04-26","7":"FALSE","8":"NA"},{"1":"onions","2":"Long Keeping Rainbow","3":"3.31133924","4":"H","5":"40","6":"2020-04-26","7":"FALSE","8":"NA"},{"1":"peas","2":"Magnolia Blossom","3":"7.45822946","4":"B","5":"24","6":"2020-04-19","7":"TRUE","8":"NA"},{"1":"peas","2":"Super Sugar Snap","3":"9.56805080","4":"A","5":"22","6":"2020-04-19","7":"TRUE","8":"NA"},{"1":"peppers","2":"green","3":"5.69232884","4":"K","5":"12","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"green","3":"5.69232884","4":"O","5":"5","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.65085072","4":"potA","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.65085072","4":"potA","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"peppers","2":"variety","3":"3.65085072","4":"potD","5":"1","6":"2020-05-21","7":"TRUE","8":"NA"},{"1":"potatoes","2":"purple","3":"3.00930630","4":"D","5":"5","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"red","3":"4.43349082","4":"I","5":"3","6":"2020-05-22","7":"FALSE","8":"NA"},{"1":"potatoes","2":"Russet","3":"9.09185288","4":"D","5":"8","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"yellow","3":"7.40090934","4":"I","5":"10","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"potatoes","2":"yellow","3":"7.40090934","4":"I","5":"8","6":"2020-05-22","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"Cinderella's Carraige","3":"32.87308882","4":"B","5":"3","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"New England Sugar","3":"44.85960776","4":"K","5":"4","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"pumpkins","2":"saved","3":"76.93241952","4":"B","5":"8","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"C","5":"20","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"G","5":"30","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"radish","2":"Garden Party Mix","3":"0.94578198","4":"H","5":"15","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"raspberries","2":"perrenial","3":"1.85849466","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"rutabaga","2":"Improved Helenor","3":"29.74032380","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"spinach","2":"Catalina","3":"2.03486426","4":"H","5":"50","6":"2020-05-16","7":"FALSE","8":"NA"},{"1":"spinach","2":"Catalina","3":"2.03486426","4":"E","5":"100","6":"2020-06-20","7":"FALSE","8":"NA"},{"1":"squash","2":"Blue (saved)","3":"41.52401770","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Blue (saved)","3":"41.52401770","4":"B","5":"8","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"delicata","3":"10.49840044","4":"K","5":"8","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"22.73183682","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"22.73183682","4":"B","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Red Kuri","3":"22.73183682","4":"side","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Waltham Butternut","3":"24.27066158","4":"A","5":"4","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"squash","2":"Waltham Butternut","3":"24.27066158","4":"K","5":"6","6":"2020-05-25","7":"TRUE","8":"NA"},{"1":"strawberries","2":"perrenial","3":"1.30513504","4":"NA","5":"NA","6":"<NA>","7":"NA","8":"NA"},{"1":"Swiss chard","2":"Neon Glow","3":"6.88282364","4":"M","5":"25","6":"2020-05-02","7":"FALSE","8":"NA"},{"1":"tomatoes","2":"Amish Paste","3":"65.67342518","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Amish Paste","3":"65.67342518","4":"N","5":"2","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Better Boy","3":"34.00846812","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Better Boy","3":"34.00846812","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Big Beef","3":"24.99377694","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Black Krim","3":"15.80712540","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Bonny Best","3":"24.92322910","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Brandywine","3":"15.64618814","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Cherokee Purple","3":"15.71232674","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"grape","3":"32.39468628","4":"O","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Jet Star","3":"15.02448530","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Mortgage Lifter","3":"26.32536742","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"died"},{"1":"tomatoes","2":"Mortgage Lifter","3":"26.32536742","4":"N","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"Old German","3":"26.71778978","4":"J","5":"1","6":"2020-05-20","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"51.61235882","4":"N","5":"1","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"51.61235882","4":"J","5":"1","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"51.61235882","4":"front","5":"5","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"tomatoes","2":"volunteers","3":"51.61235882","4":"O","5":"2","6":"2020-06-03","7":"TRUE","8":"NA"},{"1":"zucchini","2":"Romanesco","3":"99.70834874","4":"D","5":"3","6":"2020-05-21","7":"TRUE","8":"NA"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
When planting, the same variety of some vegetables were planted in multiple different planter boxes so the “plot” data doesn’t match up perfectly. Instead, there could be a separate row for each vegetable and variety by where it was plotted. 

  3. I would like to understand how much money I "saved" by gardening, for each vegetable type. Describe how I could use the `garden_harvest` and `garden_spending` datasets, along with data from somewhere like [this](https://products.wholefoodsmarket.com/search?sort=relevance&store=10542) to answer this question. You can answer this in words, referencing various join functions. You don't need R code but could provide some if it's helpful.
  
You could use the `garden_spending data` to find the total amount spent to grow/harvest each vegetable. Then, using `garden_harvest` you could find the total weight in grams for each vegetable. You could then combine those datasets using left_join. Using data found at a produce price site, you could then find how much each vegetable type costs per unit or pound. Somewhere along the way, the units would likely all have to be converted to grams or kg or lbs. Then, combining the newfound data with the new dataset we created, you could multiply the total weight of the vegetable type harvested by the price per unit. Finally, you would have to subtract how much you spent planting and harvesting that vegetable to find how much money was saved for each vegetable type.


```r
garden_spending_tot <- garden_spending %>%
  group_by(vegetable) %>%
  mutate(total_spending = sum(price_with_tax)) %>%
  select(vegetable, total_spending) %>%
  slice(1)

garden_harvest %>%
  group_by(vegetable) %>%
  mutate(total_weight_g = sum(weight)) %>%
  select(vegetable, total_weight_g ) %>%
  slice(1) %>%
  left_join(garden_spending_tot, 
            by = c("vegetable"))
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["total_weight_g"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["total_spending"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"apple","2":"156","3":"NA"},{"1":"asparagus","2":"20","3":"NA"},{"1":"basil","2":"490","3":"3.505938"},{"1":"beans","2":"12029","3":"9.471425"},{"1":"beets","2":"6183","3":"6.666675"},{"1":"broccoli","2":"1339","3":"6.677463"},{"1":"carrots","2":"7650","3":"9.741113"},{"1":"chives","2":"8","3":"NA"},{"1":"cilantro","2":"52","3":"3.505938"},{"1":"corn","2":"5901","3":"7.281563"},{"1":"cucumbers","2":"19781","3":"3.009713"},{"1":"edamame","2":"2763","3":"3.441213"},{"1":"hot peppers","2":"666","3":"0.000000"},{"1":"jalapeño","2":"4478","3":"5.000000"},{"1":"kale","2":"2697","3":"3.009713"},{"1":"kohlrabi","2":"191","3":"3.549088"},{"1":"lettuce","2":"5260","3":"10.021588"},{"1":"onions","2":"1844","3":"6.774550"},{"1":"peas","2":"7723","3":"6.235175"},{"1":"peppers","2":"4238","3":"1.666667"},{"1":"potatoes","2":"10857","3":"0.000000"},{"1":"pumpkins","2":"70155","3":"9.158588"},{"1":"radish","2":"429","3":"3.225463"},{"1":"raspberries","2":"843","3":"NA"},{"1":"rutabaga","2":"13490","3":"3.225463"},{"1":"spinach","2":"923","3":"3.225463"},{"1":"squash","2":"44917","3":"7.281563"},{"1":"strawberries","2":"592","3":"NA"},{"1":"Swiss chard","2":"3122","3":"NA"},{"1":"tomatoes","2":"158231","3":"23.333488"},{"1":"zucchini","2":"45227","3":"3.225463"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>
  4. Subset the data to tomatoes. Reorder the tomato varieties from smallest to largest first harvest date. Create a barplot of total harvest in pounds for each variety, in the new order.CHALLENGE: add the date near the end of the bar. (This is probably not a super useful graph because it's difficult to read. This is more an exercise in using some of the functions you just learned.)


```r
garden_harvest %>%
  filter(vegetable =="tomatoes") %>%
  group_by(variety) %>%
  slice(1) %>%
  mutate(weight_lb = weight * 0.00220462) %>%
  ggplot(aes(x = weight_lb, y = fct_reorder(variety, date), fill = variety)) +
    geom_col() +
  labs(title = "Tomato Varieties first harvest weight", 
       y = "Tomato Variety", 
       x = "Weight(lbs)") +
  theme_classic() +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

  5. In the `garden_harvest` data, create two new variables: one that makes the varieties lowercase and another that finds the length of the variety name. Arrange the data by vegetable and length of variety name (smallest to largest), with one row for each vegetable variety. HINT: use `str_to_lower()`, `str_length()`, and `distinct()`.
  

```r
garden_harvest %>%
  mutate(low_variety = str_to_lower(variety)) %>%
  mutate(length_variety = str_length(variety)) %>%
  distinct(low_variety, vegetable, .keep_all = TRUE) %>%
  arrange(-desc(length_variety))
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["date"],"name":[3],"type":["date"],"align":["right"]},{"label":["weight"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["units"],"name":[5],"type":["chr"],"align":["left"]},{"label":["low_variety"],"name":[6],"type":["chr"],"align":["left"]},{"label":["length_variety"],"name":[7],"type":["int"],"align":["right"]}],"data":[{"1":"potatoes","2":"red","3":"2020-10-15","4":"1718","5":"grams","6":"red","7":"3"},{"1":"hot peppers","2":"thai","3":"2020-07-20","4":"12","5":"grams","6":"thai","7":"4"},{"1":"tomatoes","2":"grape","3":"2020-07-11","4":"24","5":"grams","6":"grape","7":"5"},{"1":"jalapeño","2":"giant","3":"2020-07-17","4":"20","5":"grams","6":"giant","7":"5"},{"1":"peppers","2":"green","3":"2020-08-04","4":"81","5":"grams","6":"green","7":"5"},{"1":"pumpkins","2":"saved","3":"2020-09-01","4":"4758","5":"grams","6":"saved","7":"5"},{"1":"lettuce","2":"reseed","3":"2020-06-06","4":"20","5":"grams","6":"reseed","7":"6"},{"1":"beets","2":"leaves","3":"2020-06-11","4":"8","5":"grams","6":"leaves","7":"6"},{"1":"lettuce","2":"Tatsoi","3":"2020-06-20","4":"18","5":"grams","6":"tatsoi","7":"6"},{"1":"carrots","2":"Dragon","3":"2020-07-24","4":"80","5":"grams","6":"dragon","7":"6"},{"1":"carrots","2":"Bolero","3":"2020-07-30","4":"116","5":"grams","6":"bolero","7":"6"},{"1":"potatoes","2":"purple","3":"2020-08-06","4":"317","5":"grams","6":"purple","7":"6"},{"1":"potatoes","2":"yellow","3":"2020-08-06","4":"439","5":"grams","6":"yellow","7":"6"},{"1":"carrots","2":"greens","3":"2020-08-29","4":"169","5":"grams","6":"greens","7":"6"},{"1":"potatoes","2":"Russet","3":"2020-09-16","4":"629","5":"grams","6":"russet","7":"6"},{"1":"hot peppers","2":"variety","3":"2020-07-20","4":"559","5":"grams","6":"variety","7":"7"},{"1":"peppers","2":"variety","3":"2020-07-24","4":"68","5":"grams","6":"variety","7":"7"},{"1":"broccoli","2":"Yod Fah","3":"2020-07-27","4":"372","5":"grams","6":"yod fah","7":"7"},{"1":"edamame","2":"edamame","3":"2020-08-11","4":"109","5":"grams","6":"edamame","7":"7"},{"1":"apple","2":"unknown","3":"2020-09-26","4":"156","5":"grams","6":"unknown","7":"7"},{"1":"spinach","2":"Catalina","3":"2020-06-11","4":"9","5":"grams","6":"catalina","7":"8"},{"1":"cilantro","2":"cilantro","3":"2020-06-23","4":"2","5":"grams","6":"cilantro","7":"8"},{"1":"cucumbers","2":"pickling","3":"2020-07-08","4":"181","5":"grams","6":"pickling","7":"8"},{"1":"tomatoes","2":"Big Beef","3":"2020-07-21","4":"137","5":"grams","6":"big beef","7":"8"},{"1":"tomatoes","2":"Jet Star","3":"2020-07-28","4":"315","5":"grams","6":"jet star","7":"8"},{"1":"squash","2":"delicata","3":"2020-09-19","4":"307","5":"grams","6":"delicata","7":"8"},{"1":"squash","2":"Red Kuri","3":"2020-09-19","4":"1178","5":"grams","6":"red kuri","7":"8"},{"1":"chives","2":"perrenial","3":"2020-06-17","4":"8","5":"grams","6":"perrenial","7":"9"},{"1":"strawberries","2":"perrenial","3":"2020-06-18","4":"40","5":"grams","6":"perrenial","7":"9"},{"1":"asparagus","2":"asparagus","3":"2020-06-20","4":"20","5":"grams","6":"asparagus","7":"9"},{"1":"Swiss chard","2":"Neon Glow","3":"2020-06-21","4":"19","5":"grams","6":"neon glow","7":"9"},{"1":"raspberries","2":"perrenial","3":"2020-06-29","4":"30","5":"grams","6":"perrenial","7":"9"},{"1":"zucchini","2":"Romanesco","3":"2020-07-06","4":"175","5":"grams","6":"romanesco","7":"9"},{"1":"tomatoes","2":"Bonny Best","3":"2020-07-21","4":"339","5":"grams","6":"bonny best","7":"10"},{"1":"carrots","2":"King Midas","3":"2020-07-23","4":"56","5":"grams","6":"king midas","7":"10"},{"1":"tomatoes","2":"Better Boy","3":"2020-07-24","4":"220","5":"grams","6":"better boy","7":"10"},{"1":"tomatoes","2":"Old German","3":"2020-07-28","4":"611","5":"grams","6":"old german","7":"10"},{"1":"tomatoes","2":"Brandywine","3":"2020-08-01","4":"320","5":"grams","6":"brandywine","7":"10"},{"1":"tomatoes","2":"Black Krim","3":"2020-08-01","4":"436","5":"grams","6":"black krim","7":"10"},{"1":"tomatoes","2":"volunteers","3":"2020-08-04","4":"73","5":"grams","6":"volunteers","7":"10"},{"1":"tomatoes","2":"Amish Paste","3":"2020-07-25","4":"463","5":"grams","6":"amish paste","7":"11"},{"1":"beets","2":"Sweet Merlin","3":"2020-07-07","4":"10","5":"grams","6":"sweet merlin","7":"12"},{"1":"squash","2":"Blue (saved)","3":"2020-09-01","4":"3227","5":"grams","6":"blue (saved)","7":"12"},{"1":"basil","2":"Isle of Naxos","3":"2020-06-23","4":"5","5":"grams","6":"isle of naxos","7":"13"},{"1":"onions","2":"Delicious Duo","3":"2020-07-16","4":"50","5":"grams","6":"delicious duo","7":"13"},{"1":"corn","2":"Dorinny Sweet","3":"2020-08-11","4":"330","5":"grams","6":"dorinny sweet","7":"13"},{"1":"corn","2":"Golden Bantam","3":"2020-08-15","4":"383","5":"grams","6":"golden bantam","7":"13"},{"1":"lettuce","2":"mustard greens","3":"2020-06-29","4":"23","5":"grams","6":"mustard greens","7":"14"},{"1":"beets","2":"Gourmet Golden","3":"2020-07-07","4":"62","5":"grams","6":"gourmet golden","7":"14"},{"1":"lettuce","2":"Lettuce Mixture","3":"2020-07-22","4":"23","5":"grams","6":"lettuce mixture","7":"15"},{"1":"tomatoes","2":"Cherokee Purple","3":"2020-07-24","4":"247","5":"grams","6":"cherokee purple","7":"15"},{"1":"tomatoes","2":"Mortgage Lifter","3":"2020-07-27","4":"801","5":"grams","6":"mortgage lifter","7":"15"},{"1":"radish","2":"Garden Party Mix","3":"2020-06-06","4":"36","5":"grams","6":"garden party mix","7":"16"},{"1":"kale","2":"Heirloom Lacinto","3":"2020-06-13","4":"10","5":"grams","6":"heirloom lacinto","7":"16"},{"1":"peas","2":"Magnolia Blossom","3":"2020-06-17","4":"8","5":"grams","6":"magnolia blossom","7":"16"},{"1":"peas","2":"Super Sugar Snap","3":"2020-06-17","4":"121","5":"grams","6":"super sugar snap","7":"16"},{"1":"rutabaga","2":"Improved Helenor","3":"2020-10-16","4":"883","5":"grams","6":"improved helenor","7":"16"},{"1":"beans","2":"Bush Bush Slender","3":"2020-07-06","4":"235","5":"grams","6":"bush bush slender","7":"17"},{"1":"broccoli","2":"Main Crop Bravado","3":"2020-09-09","4":"102","5":"grams","6":"main crop bravado","7":"17"},{"1":"kohlrabi","2":"Crispy Colors Duo","3":"2020-09-17","4":"191","5":"grams","6":"crispy colors duo","7":"17"},{"1":"squash","2":"Waltham Butternut","3":"2020-09-19","4":"1834","5":"grams","6":"waltham butternut","7":"17"},{"1":"pumpkins","2":"New England Sugar","3":"2020-09-19","4":"1109","5":"grams","6":"new england sugar","7":"17"},{"1":"beans","2":"Chinese Red Noodle","3":"2020-08-08","4":"108","5":"grams","6":"chinese red noodle","7":"18"},{"1":"beans","2":"Classic Slenderette","3":"2020-08-05","4":"41","5":"grams","6":"classic slenderette","7":"19"},{"1":"onions","2":"Long Keeping Rainbow","3":"2020-07-20","4":"102","5":"grams","6":"long keeping rainbow","7":"20"},{"1":"lettuce","2":"Farmer's Market Blend","3":"2020-06-11","4":"12","5":"grams","6":"farmer's market blend","7":"21"},{"1":"pumpkins","2":"Cinderella's Carraige","3":"2020-09-01","4":"7350","5":"grams","6":"cinderella's carraige","7":"21"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

  6. In the `garden_harvest` data, find all distinct vegetable varieties that have "er" or "ar" in their name. HINT: `str_detect()` with an "or" statement (use the | for "or") and `distinct()`.


```r
garden_harvest %>%
  mutate(low_variety = str_to_lower(variety)) %>%
  arrange(vegetable) %>%
  group_by(variety) %>%
  filter(row_number()==1) %>%
  distinct(low_variety, vegetable, .keep_all = TRUE) %>%
  mutate(is_er = str_detect(low_variety, "er")) %>%
  mutate(is_ar = str_detect(low_variety, "ar"))
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["vegetable"],"name":[1],"type":["chr"],"align":["left"]},{"label":["variety"],"name":[2],"type":["chr"],"align":["left"]},{"label":["date"],"name":[3],"type":["date"],"align":["right"]},{"label":["weight"],"name":[4],"type":["dbl"],"align":["right"]},{"label":["units"],"name":[5],"type":["chr"],"align":["left"]},{"label":["low_variety"],"name":[6],"type":["chr"],"align":["left"]},{"label":["is_er"],"name":[7],"type":["lgl"],"align":["right"]},{"label":["is_ar"],"name":[8],"type":["lgl"],"align":["right"]}],"data":[{"1":"apple","2":"unknown","3":"2020-09-26","4":"156","5":"grams","6":"unknown","7":"FALSE","8":"FALSE"},{"1":"asparagus","2":"asparagus","3":"2020-06-20","4":"20","5":"grams","6":"asparagus","7":"FALSE","8":"TRUE"},{"1":"basil","2":"Isle of Naxos","3":"2020-06-23","4":"5","5":"grams","6":"isle of naxos","7":"FALSE","8":"FALSE"},{"1":"beans","2":"Bush Bush Slender","3":"2020-07-06","4":"235","5":"grams","6":"bush bush slender","7":"TRUE","8":"FALSE"},{"1":"beans","2":"Classic Slenderette","3":"2020-08-05","4":"41","5":"grams","6":"classic slenderette","7":"TRUE","8":"FALSE"},{"1":"beans","2":"Chinese Red Noodle","3":"2020-08-08","4":"108","5":"grams","6":"chinese red noodle","7":"FALSE","8":"FALSE"},{"1":"beets","2":"leaves","3":"2020-06-11","4":"8","5":"grams","6":"leaves","7":"FALSE","8":"FALSE"},{"1":"beets","2":"Gourmet Golden","3":"2020-07-07","4":"62","5":"grams","6":"gourmet golden","7":"FALSE","8":"FALSE"},{"1":"beets","2":"Sweet Merlin","3":"2020-07-07","4":"10","5":"grams","6":"sweet merlin","7":"TRUE","8":"FALSE"},{"1":"broccoli","2":"Yod Fah","3":"2020-07-27","4":"372","5":"grams","6":"yod fah","7":"FALSE","8":"FALSE"},{"1":"broccoli","2":"Main Crop Bravado","3":"2020-09-09","4":"102","5":"grams","6":"main crop bravado","7":"FALSE","8":"FALSE"},{"1":"carrots","2":"King Midas","3":"2020-07-23","4":"56","5":"grams","6":"king midas","7":"FALSE","8":"FALSE"},{"1":"carrots","2":"Dragon","3":"2020-07-24","4":"80","5":"grams","6":"dragon","7":"FALSE","8":"FALSE"},{"1":"carrots","2":"Bolero","3":"2020-07-30","4":"116","5":"grams","6":"bolero","7":"TRUE","8":"FALSE"},{"1":"carrots","2":"greens","3":"2020-08-29","4":"169","5":"grams","6":"greens","7":"FALSE","8":"FALSE"},{"1":"chives","2":"perrenial","3":"2020-06-17","4":"8","5":"grams","6":"perrenial","7":"TRUE","8":"FALSE"},{"1":"cilantro","2":"cilantro","3":"2020-06-23","4":"2","5":"grams","6":"cilantro","7":"FALSE","8":"FALSE"},{"1":"corn","2":"Dorinny Sweet","3":"2020-08-11","4":"330","5":"grams","6":"dorinny sweet","7":"FALSE","8":"FALSE"},{"1":"corn","2":"Golden Bantam","3":"2020-08-15","4":"383","5":"grams","6":"golden bantam","7":"FALSE","8":"FALSE"},{"1":"cucumbers","2":"pickling","3":"2020-07-08","4":"181","5":"grams","6":"pickling","7":"FALSE","8":"FALSE"},{"1":"edamame","2":"edamame","3":"2020-08-11","4":"109","5":"grams","6":"edamame","7":"FALSE","8":"FALSE"},{"1":"hot peppers","2":"thai","3":"2020-07-20","4":"12","5":"grams","6":"thai","7":"FALSE","8":"FALSE"},{"1":"hot peppers","2":"variety","3":"2020-07-20","4":"559","5":"grams","6":"variety","7":"FALSE","8":"TRUE"},{"1":"jalapeño","2":"giant","3":"2020-07-17","4":"20","5":"grams","6":"giant","7":"FALSE","8":"FALSE"},{"1":"kale","2":"Heirloom Lacinto","3":"2020-06-13","4":"10","5":"grams","6":"heirloom lacinto","7":"FALSE","8":"FALSE"},{"1":"kohlrabi","2":"Crispy Colors Duo","3":"2020-09-17","4":"191","5":"grams","6":"crispy colors duo","7":"FALSE","8":"FALSE"},{"1":"lettuce","2":"reseed","3":"2020-06-06","4":"20","5":"grams","6":"reseed","7":"FALSE","8":"FALSE"},{"1":"lettuce","2":"Farmer's Market Blend","3":"2020-06-11","4":"12","5":"grams","6":"farmer's market blend","7":"TRUE","8":"TRUE"},{"1":"lettuce","2":"Tatsoi","3":"2020-06-20","4":"18","5":"grams","6":"tatsoi","7":"FALSE","8":"FALSE"},{"1":"lettuce","2":"mustard greens","3":"2020-06-29","4":"23","5":"grams","6":"mustard greens","7":"FALSE","8":"TRUE"},{"1":"lettuce","2":"Lettuce Mixture","3":"2020-07-22","4":"23","5":"grams","6":"lettuce mixture","7":"FALSE","8":"FALSE"},{"1":"onions","2":"Delicious Duo","3":"2020-07-16","4":"50","5":"grams","6":"delicious duo","7":"FALSE","8":"FALSE"},{"1":"onions","2":"Long Keeping Rainbow","3":"2020-07-20","4":"102","5":"grams","6":"long keeping rainbow","7":"FALSE","8":"FALSE"},{"1":"peas","2":"Magnolia Blossom","3":"2020-06-17","4":"8","5":"grams","6":"magnolia blossom","7":"FALSE","8":"FALSE"},{"1":"peas","2":"Super Sugar Snap","3":"2020-06-17","4":"121","5":"grams","6":"super sugar snap","7":"TRUE","8":"TRUE"},{"1":"peppers","2":"green","3":"2020-08-04","4":"81","5":"grams","6":"green","7":"FALSE","8":"FALSE"},{"1":"potatoes","2":"purple","3":"2020-08-06","4":"317","5":"grams","6":"purple","7":"FALSE","8":"FALSE"},{"1":"potatoes","2":"yellow","3":"2020-08-06","4":"439","5":"grams","6":"yellow","7":"FALSE","8":"FALSE"},{"1":"potatoes","2":"Russet","3":"2020-09-16","4":"629","5":"grams","6":"russet","7":"FALSE","8":"FALSE"},{"1":"potatoes","2":"red","3":"2020-10-15","4":"1718","5":"grams","6":"red","7":"FALSE","8":"FALSE"},{"1":"pumpkins","2":"saved","3":"2020-09-01","4":"4758","5":"grams","6":"saved","7":"FALSE","8":"FALSE"},{"1":"pumpkins","2":"Cinderella's Carraige","3":"2020-09-01","4":"7350","5":"grams","6":"cinderella's carraige","7":"TRUE","8":"TRUE"},{"1":"pumpkins","2":"New England Sugar","3":"2020-09-19","4":"1109","5":"grams","6":"new england sugar","7":"FALSE","8":"TRUE"},{"1":"radish","2":"Garden Party Mix","3":"2020-06-06","4":"36","5":"grams","6":"garden party mix","7":"FALSE","8":"TRUE"},{"1":"rutabaga","2":"Improved Helenor","3":"2020-10-16","4":"883","5":"grams","6":"improved helenor","7":"FALSE","8":"FALSE"},{"1":"spinach","2":"Catalina","3":"2020-06-11","4":"9","5":"grams","6":"catalina","7":"FALSE","8":"FALSE"},{"1":"squash","2":"Blue (saved)","3":"2020-09-01","4":"3227","5":"grams","6":"blue (saved)","7":"FALSE","8":"FALSE"},{"1":"squash","2":"delicata","3":"2020-09-19","4":"307","5":"grams","6":"delicata","7":"FALSE","8":"FALSE"},{"1":"squash","2":"Waltham Butternut","3":"2020-09-19","4":"1834","5":"grams","6":"waltham butternut","7":"TRUE","8":"FALSE"},{"1":"squash","2":"Red Kuri","3":"2020-09-19","4":"1178","5":"grams","6":"red kuri","7":"FALSE","8":"FALSE"},{"1":"Swiss chard","2":"Neon Glow","3":"2020-06-21","4":"19","5":"grams","6":"neon glow","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"grape","3":"2020-07-11","4":"24","5":"grams","6":"grape","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"Big Beef","3":"2020-07-21","4":"137","5":"grams","6":"big beef","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"Bonny Best","3":"2020-07-21","4":"339","5":"grams","6":"bonny best","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"Cherokee Purple","3":"2020-07-24","4":"247","5":"grams","6":"cherokee purple","7":"TRUE","8":"FALSE"},{"1":"tomatoes","2":"Better Boy","3":"2020-07-24","4":"220","5":"grams","6":"better boy","7":"TRUE","8":"FALSE"},{"1":"tomatoes","2":"Amish Paste","3":"2020-07-25","4":"463","5":"grams","6":"amish paste","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"Mortgage Lifter","3":"2020-07-27","4":"801","5":"grams","6":"mortgage lifter","7":"TRUE","8":"FALSE"},{"1":"tomatoes","2":"Old German","3":"2020-07-28","4":"611","5":"grams","6":"old german","7":"TRUE","8":"FALSE"},{"1":"tomatoes","2":"Jet Star","3":"2020-07-28","4":"315","5":"grams","6":"jet star","7":"FALSE","8":"TRUE"},{"1":"tomatoes","2":"Brandywine","3":"2020-08-01","4":"320","5":"grams","6":"brandywine","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"Black Krim","3":"2020-08-01","4":"436","5":"grams","6":"black krim","7":"FALSE","8":"FALSE"},{"1":"tomatoes","2":"volunteers","3":"2020-08-04","4":"73","5":"grams","6":"volunteers","7":"TRUE","8":"FALSE"},{"1":"zucchini","2":"Romanesco","3":"2020-07-06","4":"175","5":"grams","6":"romanesco","7":"FALSE","8":"FALSE"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


## Bicycle-Use Patterns

In this activity, you'll examine some factors that may influence the use of bicycles in a bike-renting program.  The data come from Washington, DC and cover the last quarter of 2014.

<center>

![A typical Capital Bikeshare station. This one is at Florida and California, next to Pleasant Pops.](https://www.macalester.edu/~dshuman1/data/112/bike_station.jpg){width="30%"}


![One of the vans used to redistribute bicycles to different stations.](https://www.macalester.edu/~dshuman1/data/112/bike_van.jpg){width="30%"}

</center>

Two data tables are available:

- `Trips` contains records of individual rentals
- `Stations` gives the locations of the bike rental stations

Here is the code to read in the data. We do this a little differently than usual, which is why it is included here rather than at the top of this file. To avoid repeatedly re-reading the files, start the data import chunk with `{r cache = TRUE}` rather than the usual `{r}`.


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
  ggplot(aes(x = sdate)) +
  geom_density() +
  labs(title = "Density of Bike Rentals Between October and January", 
       x = "Month", 
       y = "Density of Bike Rentals") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times")) 
```

![](03_exercises_files/figure-html/unnamed-chunk-8-1.png)<!-- -->
  
  8. A density plot of the events versus time of day.  You can use `mutate()` with `lubridate`'s  `hour()` and `minute()` functions to extract the hour of the day and minute within the hour from `sdate`. Hint: A minute is 1/60 of an hour, so create a variable where 3:30 is 3.5 and 3:45 is 3.75.
  

```r
Trips %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>%
  ggplot(aes(x = t_day)) +
  geom_density() +
  labs(title = "Density of Bike Rentals Throughout 24 Hours", 
       x = "Time of Day (hr)", 
       y = "Density of Bike Rentals") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times")) 
```

![](03_exercises_files/figure-html/unnamed-chunk-9-1.png)<!-- -->
  
  9. A bar graph of the events versus day of the week. Put day on the y-axis.
  

```r
  Trips %>%
  mutate(day = wday(sdate, label = TRUE)) %>%
  #mutate(day = weekdays(Trips$sdate)) %>%
  ggplot(aes(y = day, fill = day)) +
  geom_bar() +
  labs(title = "Events by day of the Week", 
       x = "Number of Events", 
       y = "Day of the Week")
```

![](03_exercises_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

```r
  theme_classic()
```

```
## List of 93
##  $ line                      :List of 6
##   ..$ colour       : chr "black"
##   ..$ size         : num 0.5
##   ..$ linetype     : num 1
##   ..$ lineend      : chr "butt"
##   ..$ arrow        : logi FALSE
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_line" "element"
##  $ rect                      :List of 5
##   ..$ fill         : chr "white"
##   ..$ colour       : chr "black"
##   ..$ size         : num 0.5
##   ..$ linetype     : num 1
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_rect" "element"
##  $ text                      :List of 11
##   ..$ family       : chr ""
##   ..$ face         : chr "plain"
##   ..$ colour       : chr "black"
##   ..$ size         : num 11
##   ..$ hjust        : num 0.5
##   ..$ vjust        : num 0.5
##   ..$ angle        : num 0
##   ..$ lineheight   : num 0.9
##   ..$ margin       : 'margin' num [1:4] 0points 0points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : logi FALSE
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ title                     : NULL
##  $ aspect.ratio              : NULL
##  $ axis.title                : NULL
##  $ axis.title.x              :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 1
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 2.75points 0points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.title.x.top          :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 0
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 2.75points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.title.x.bottom       : NULL
##  $ axis.title.y              :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 1
##   ..$ angle        : num 90
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 2.75points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.title.y.left         : NULL
##  $ axis.title.y.right        :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 0
##   ..$ angle        : num -90
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 0points 2.75points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.text                 :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : chr "grey30"
##   ..$ size         : 'rel' num 0.8
##   ..$ hjust        : NULL
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.text.x               :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 1
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 2.2points 0points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.text.x.top           :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : num 0
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 2.2points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.text.x.bottom        : NULL
##  $ axis.text.y               :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : num 1
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 2.2points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.text.y.left          : NULL
##  $ axis.text.y.right         :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : num 0
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 0points 2.2points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ axis.ticks                :List of 6
##   ..$ colour       : chr "grey20"
##   ..$ size         : NULL
##   ..$ linetype     : NULL
##   ..$ lineend      : NULL
##   ..$ arrow        : logi FALSE
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_line" "element"
##  $ axis.ticks.x              : NULL
##  $ axis.ticks.x.top          : NULL
##  $ axis.ticks.x.bottom       : NULL
##  $ axis.ticks.y              : NULL
##  $ axis.ticks.y.left         : NULL
##  $ axis.ticks.y.right        : NULL
##  $ axis.ticks.length         : 'simpleUnit' num 2.75points
##   ..- attr(*, "unit")= int 8
##  $ axis.ticks.length.x       : NULL
##  $ axis.ticks.length.x.top   : NULL
##  $ axis.ticks.length.x.bottom: NULL
##  $ axis.ticks.length.y       : NULL
##  $ axis.ticks.length.y.left  : NULL
##  $ axis.ticks.length.y.right : NULL
##  $ axis.line                 :List of 6
##   ..$ colour       : chr "black"
##   ..$ size         : 'rel' num 1
##   ..$ linetype     : NULL
##   ..$ lineend      : NULL
##   ..$ arrow        : logi FALSE
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_line" "element"
##  $ axis.line.x               : NULL
##  $ axis.line.x.top           : NULL
##  $ axis.line.x.bottom        : NULL
##  $ axis.line.y               : NULL
##  $ axis.line.y.left          : NULL
##  $ axis.line.y.right         : NULL
##  $ legend.background         :List of 5
##   ..$ fill         : NULL
##   ..$ colour       : logi NA
##   ..$ size         : NULL
##   ..$ linetype     : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_rect" "element"
##  $ legend.margin             : 'margin' num [1:4] 5.5points 5.5points 5.5points 5.5points
##   ..- attr(*, "unit")= int 8
##  $ legend.spacing            : 'simpleUnit' num 11points
##   ..- attr(*, "unit")= int 8
##  $ legend.spacing.x          : NULL
##  $ legend.spacing.y          : NULL
##  $ legend.key                : list()
##   ..- attr(*, "class")= chr [1:2] "element_blank" "element"
##  $ legend.key.size           : 'simpleUnit' num 1.2lines
##   ..- attr(*, "unit")= int 3
##  $ legend.key.height         : NULL
##  $ legend.key.width          : NULL
##  $ legend.text               :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : 'rel' num 0.8
##   ..$ hjust        : NULL
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ legend.text.align         : NULL
##  $ legend.title              :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : num 0
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ legend.title.align        : NULL
##  $ legend.position           : chr "right"
##  $ legend.direction          : NULL
##  $ legend.justification      : chr "center"
##  $ legend.box                : NULL
##  $ legend.box.just           : NULL
##  $ legend.box.margin         : 'margin' num [1:4] 0cm 0cm 0cm 0cm
##   ..- attr(*, "unit")= int 1
##  $ legend.box.background     : list()
##   ..- attr(*, "class")= chr [1:2] "element_blank" "element"
##  $ legend.box.spacing        : 'simpleUnit' num 11points
##   ..- attr(*, "unit")= int 8
##  $ panel.background          :List of 5
##   ..$ fill         : chr "white"
##   ..$ colour       : logi NA
##   ..$ size         : NULL
##   ..$ linetype     : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_rect" "element"
##  $ panel.border              : list()
##   ..- attr(*, "class")= chr [1:2] "element_blank" "element"
##  $ panel.spacing             : 'simpleUnit' num 5.5points
##   ..- attr(*, "unit")= int 8
##  $ panel.spacing.x           : NULL
##  $ panel.spacing.y           : NULL
##  $ panel.grid                :List of 6
##   ..$ colour       : chr "grey92"
##   ..$ size         : NULL
##   ..$ linetype     : NULL
##   ..$ lineend      : NULL
##   ..$ arrow        : logi FALSE
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_line" "element"
##  $ panel.grid.major          : list()
##   ..- attr(*, "class")= chr [1:2] "element_blank" "element"
##  $ panel.grid.minor          : list()
##   ..- attr(*, "class")= chr [1:2] "element_blank" "element"
##  $ panel.grid.major.x        : NULL
##  $ panel.grid.major.y        : NULL
##  $ panel.grid.minor.x        : NULL
##  $ panel.grid.minor.y        : NULL
##  $ panel.ontop               : logi FALSE
##  $ plot.background           :List of 5
##   ..$ fill         : NULL
##   ..$ colour       : chr "white"
##   ..$ size         : NULL
##   ..$ linetype     : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_rect" "element"
##  $ plot.title                :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : 'rel' num 1.2
##   ..$ hjust        : num 0
##   ..$ vjust        : num 1
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 5.5points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ plot.title.position       : chr "panel"
##  $ plot.subtitle             :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : num 0
##   ..$ vjust        : num 1
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 0points 0points 5.5points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ plot.caption              :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : 'rel' num 0.8
##   ..$ hjust        : num 1
##   ..$ vjust        : num 1
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 5.5points 0points 0points 0points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ plot.caption.position     : chr "panel"
##  $ plot.tag                  :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : 'rel' num 1.2
##   ..$ hjust        : num 0.5
##   ..$ vjust        : num 0.5
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ plot.tag.position         : chr "topleft"
##  $ plot.margin               : 'margin' num [1:4] 5.5points 5.5points 5.5points 5.5points
##   ..- attr(*, "unit")= int 8
##  $ strip.background          :List of 5
##   ..$ fill         : chr "white"
##   ..$ colour       : chr "black"
##   ..$ size         : 'rel' num 2
##   ..$ linetype     : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_rect" "element"
##  $ strip.background.x        : NULL
##  $ strip.background.y        : NULL
##  $ strip.placement           : chr "inside"
##  $ strip.text                :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : chr "grey10"
##   ..$ size         : 'rel' num 0.8
##   ..$ hjust        : NULL
##   ..$ vjust        : NULL
##   ..$ angle        : NULL
##   ..$ lineheight   : NULL
##   ..$ margin       : 'margin' num [1:4] 4.4points 4.4points 4.4points 4.4points
##   .. ..- attr(*, "unit")= int 8
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ strip.text.x              : NULL
##  $ strip.text.y              :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : NULL
##   ..$ angle        : num -90
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  $ strip.switch.pad.grid     : 'simpleUnit' num 2.75points
##   ..- attr(*, "unit")= int 8
##  $ strip.switch.pad.wrap     : 'simpleUnit' num 2.75points
##   ..- attr(*, "unit")= int 8
##  $ strip.text.y.left         :List of 11
##   ..$ family       : NULL
##   ..$ face         : NULL
##   ..$ colour       : NULL
##   ..$ size         : NULL
##   ..$ hjust        : NULL
##   ..$ vjust        : NULL
##   ..$ angle        : num 90
##   ..$ lineheight   : NULL
##   ..$ margin       : NULL
##   ..$ debug        : NULL
##   ..$ inherit.blank: logi TRUE
##   ..- attr(*, "class")= chr [1:2] "element_text" "element"
##  - attr(*, "class")= chr [1:2] "theme" "gg"
##  - attr(*, "complete")= logi TRUE
##  - attr(*, "validate")= logi TRUE
```

```r
# c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", 
#     "Friday", "Saturday")[as.POSIXlt(Trips$sdate)$wday + 1]
```
  
  10. Facet your graph from exercise 8. by day of the week. Is there a pattern?
  

```r
Trips %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>% 
  #mutate(day = weekdays(Trips$sdate)) %>%
  mutate(day = wday(sdate, label = TRUE)) %>%
  ggplot(aes(x = t_day)) +
  geom_density() +
  facet_wrap(vars(day)) +
  labs(title = "Events during the Week", 
       x = "Time of Day", 
       y = "Event density") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-11-1.png)<!-- -->
There is a pattern of this faceted graph. On weekdays, Mon-Friday, we see the density increases during the two peaks: when workers are traveling to work, and then increasing again when workers are traveling home from work. On weekends, density of events gradually increases during the day and then decreases as night approaches. 

The variable `client` describes whether the renter is a regular user (level `Registered`) or has not joined the bike-rental organization (`Causal`). The next set of exercises investigate whether these two different categories of users show different rental behavior and how `client` interacts with the patterns you found in the previous exercises. 

  11. Change the graph from exercise 10 to set the `fill` aesthetic for `geom_density()` to the `client` variable. You should also set `alpha = .5` for transparency and `color=NA` to suppress the outline of the density function.
  

```r
Trips %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>% 
  mutate(day = weekdays(Trips$sdate)) %>%
  ggplot(aes(x = t_day, fill = client)) +
  geom_density(alpha = 0.5, color = NA) +
  facet_wrap(vars(day)) +
  labs(title = "Events during the day", 
       x = "Time of Day (hrs)", 
       y = "Event density") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-12-1.png)<!-- -->

  12. Change the previous graph by adding the argument `position = position_stack()` to `geom_density()`. In your opinion, is this better or worse in terms of telling a story? What are the advantages/disadvantages of each?
  

```r
Trips %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>% 
  mutate(day = weekdays(Trips$sdate)) %>%
  ggplot(aes(x = t_day, fill = client)) +
  geom_density(alpha = 0.5, color = NA, position = position_stack()) +
  facet_wrap(vars(day)) +
  labs(title = "Events during the day by day of the week", 
       x = "Time of Day (hrs)", 
       y = "Event density") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-13-1.png)<!-- -->
Personally, I find the first graph in question 11 to be much easier to read and interpret and is better for story telling. The first graph is better for distinctly seeing the separate activity of Casual and Registered riders throughout each day of the week. The second graph is better if you want to see the cumulative total density of both casual and registered riders throughout each day of the week. 


  13. In this graph, go back to using the regular density plot (without `position = position_stack()`). Add a new variable to the dataset called `weekend` which will be "weekend" if the day is Saturday or Sunday and  "weekday" otherwise (HINT: use the `ifelse()` function and the `wday()` function from `lubridate`). Then, update the graph from the previous problem by faceting on the new `weekend` variable. 
  

```r
Trips %>%
  mutate(day = wday(sdate, label = TRUE)) %>%
  mutate(weekend = ifelse(day == c("Sat", "Sun"), "weekend", "weekday")) %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>%
  ggplot(aes(x = t_day, 
             fill = client)) +
  geom_density(alpha = 0.5, 
               color = NA) +
  facet_wrap(~weekend) +
  labs(title = "Events during the weekday vs weekend", 
       x = "Time of Day (hrs)", 
       y = "Event density") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-14-1.png)<!-- -->
  
  14. Change the graph from the previous problem to facet on `client` and fill with `weekday`. What information does this graph tell you that the previous didn't? Is one graph better than the other?
  

```r
Trips %>%
  mutate(day = wday(sdate, label = TRUE)) %>%
  mutate(weekend = ifelse(day == c("Sat", "Sun"), "weekend", "weekday")) %>%
  mutate(hr = hour(sdate), mn = minute(sdate)) %>%
  mutate(t_day = hr+(mn/60)) %>%
  ggplot(aes(x = t_day, 
             fill = weekend)) +
  geom_density(alpha = 0.5, 
               color = NA) +
  facet_wrap(~client) +
  labs(title = "Events during the day by day of the week", 
       x = "Time of Day (hrs)", 
       y = "Event density") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-15-1.png)<!-- -->
The first graph shows the bike usage by both casual and registered riders on weekdays vs weekends. It focuses on the comparison between casual and registered riders based on what day it is. The second graph shows the bike usage on weekdays vs weekends by both casual and registered riders. It focuses on the comparison between weekdays vs weekends by day of the week. As both graphs are very similar, I think they are equally useful for slightly different comparisons/questions.

### Spatial patterns

  15. Use the latitude and longitude variables in `Stations` to make a visualization of the total number of departures from each station in the `Trips` data. Use either color or size to show the variation in number of departures. We will improve this plot next week when we learn about maps!
  

```r
Trips %>%
  left_join(Stations, 
            by = c("sstation" = "name")) %>%
  group_by(lat, long) %>%
  summarize(n = n(), 
            prop_casual = mean(client == "Casual")) %>%
  ggplot(aes(x = long, y = lat, color = n)) + 
  geom_point(alpha = 0.8, shape = 17) +
  labs(title = "Total Number of Departures by Station Location", 
       x = "Latitude", 
       y =  "Longitude") +
    theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-16-1.png)<!-- -->
  
  16. Only 14.4% of the trips in our data are carried out by casual users. Create a plot that shows which area(s) have stations with a much higher percentage of departures by casual users. What patterns do you notice? (Again, we'll improve this next week when we learn about maps).
  

```r
Trips %>%
  left_join(Stations, 
            by = c("sstation" = "name")) %>%
  group_by(lat, long) %>%
  summarize(n = n(), 
            prop_casual = mean(client == "Casual")) %>% 
  ggplot(aes(x = long, y = lat, color = prop_casual)) + 
  geom_point(alpha = 0.8, shape = 17) +
  labs(title = "Total Number of Departures by Station Location", 
       x = "Latitude", 
       y =  "Longitude") +
    theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-17-1.png)<!-- -->
Much of the departure locations are concentrated in a specific area, likely the center of the city. More of the departure locations that are father away from that central location are registered riders who are likely commuting into the more dense area. However, it is still a little bit hard to read the data when the points are so densely placed. 

**DID YOU REMEMBER TO GO BACK AND CHANGE THIS SET OF EXERCISES TO THE LARGER DATASET? IF NOT, DO THAT NOW.**

## Dogs!

In this section, we'll use the data from 2022-02-01 Tidy Tuesday. If you didn't use that data or need a little refresher on it, see the [website](https://github.com/rfordatascience/tidytuesday/blob/master/data/2022/2022-02-01/readme.md).

  17. The final product of this exercise will be a graph that has breed on the y-axis and the sum of the numeric ratings in the `breed_traits` dataset on the x-axis, with a dot for each rating. First, create a new dataset called `breed_traits_total` that has two variables -- `Breed` and `total_rating`. The `total_rating` variable is the sum of the numeric ratings in the `breed_traits` dataset (we'll use this dataset again in the next problem). Then, create the graph just described. Omit Breeds with a `total_rating` of 0 and order the Breeds from highest to lowest ranked. You may want to adjust the `fig.height` and `fig.width` arguments inside the code chunk options (eg. `{r, fig.height=8, fig.width=4}`) so you can see things more clearly - check this after you knit the file to assure it looks like what you expected.


```r
breed_traits_total <- breed_traits %>%
  mutate(Breed = str_squish(Breed)) %>%
  #select(newBreed, Year, Rank)
  select(-c(`Coat Type`:`Coat Length`)) %>%
  pivot_longer(cols = -Breed, 
               names_to = "Category", 
               values_to = "Rankings") %>%
  group_by(Breed) %>%
  mutate(total_rating = sum(Rankings)) %>%
  #mutate(total_rating = tot_rating > 0) %>%
  select(Breed, total_rating) %>%
  filter(row_number()==1) %>%
  arrange(desc(total_rating)) %>%
  head(194)

breed_traits_total %>% 
  arrange(desc(total_rating)) %>%
  ggplot(aes(x = total_rating,
             y = fct_reorder(Breed, total_rating))) +
  geom_point() +
  labs(title = "Total Rating of Dog Breeds", 
       y = "Dog Breed", 
       x = "Total Rating") +
  theme_clean() +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-18-1.png)<!-- -->

  18. The final product of this exercise will be a graph with the top-20 dogs in total ratings (from previous problem) on the y-axis, year on the x-axis, and points colored by each breed's ranking for that year (from the `breed_rank_all` dataset). The points within each breed will be connected by a line, and the breeds should be arranged from the highest median rank to lowest median rank ("highest" is actually the smallest numer, eg. 1 = best). After you're finished, think of AT LEAST one thing you could you do to make this graph better. HINTS: 1. Start with the `breed_rank_all` dataset and pivot it so year is a variable. 2. Use the `separate()` function to get year alone, and there's an extra argument in that function that can make it numeric. 3. For both datasets used, you'll need to `str_squish()` Breed before joining. 
  

```r
breed_rank_all %>%
  pivot_longer(col = starts_with("20"), 
               names_to = "Year",
               values_to = "Rank") %>%
  separate(Year, 
           into = c("Year", "words"), 
           remove = FALSE, 
           convert = TRUE) %>%
  mutate(Breed = str_squish(Breed)) %>%
  select(Breed, Year, Rank)  #Breed, Year, and their ranking that year. 2013-2020
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Breed"],"name":[1],"type":["chr"],"align":["left"]},{"label":["Year"],"name":[2],"type":["int"],"align":["right"]},{"label":["Rank"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"Retrievers (Labrador)","2":"2013","3":"1"},{"1":"Retrievers (Labrador)","2":"2014","3":"1"},{"1":"Retrievers (Labrador)","2":"2015","3":"1"},{"1":"Retrievers (Labrador)","2":"2016","3":"1"},{"1":"Retrievers (Labrador)","2":"2017","3":"1"},{"1":"Retrievers (Labrador)","2":"2018","3":"1"},{"1":"Retrievers (Labrador)","2":"2019","3":"1"},{"1":"Retrievers (Labrador)","2":"2020","3":"1"},{"1":"French Bulldogs","2":"2013","3":"11"},{"1":"French Bulldogs","2":"2014","3":"9"},{"1":"French Bulldogs","2":"2015","3":"6"},{"1":"French Bulldogs","2":"2016","3":"6"},{"1":"French Bulldogs","2":"2017","3":"4"},{"1":"French Bulldogs","2":"2018","3":"4"},{"1":"French Bulldogs","2":"2019","3":"4"},{"1":"French Bulldogs","2":"2020","3":"2"},{"1":"German Shepherd Dogs","2":"2013","3":"2"},{"1":"German Shepherd Dogs","2":"2014","3":"2"},{"1":"German Shepherd Dogs","2":"2015","3":"2"},{"1":"German Shepherd Dogs","2":"2016","3":"2"},{"1":"German Shepherd Dogs","2":"2017","3":"2"},{"1":"German Shepherd Dogs","2":"2018","3":"2"},{"1":"German Shepherd Dogs","2":"2019","3":"2"},{"1":"German Shepherd Dogs","2":"2020","3":"3"},{"1":"Retrievers (Golden)","2":"2013","3":"3"},{"1":"Retrievers (Golden)","2":"2014","3":"3"},{"1":"Retrievers (Golden)","2":"2015","3":"3"},{"1":"Retrievers (Golden)","2":"2016","3":"3"},{"1":"Retrievers (Golden)","2":"2017","3":"3"},{"1":"Retrievers (Golden)","2":"2018","3":"3"},{"1":"Retrievers (Golden)","2":"2019","3":"3"},{"1":"Retrievers (Golden)","2":"2020","3":"4"},{"1":"Bulldogs","2":"2013","3":"5"},{"1":"Bulldogs","2":"2014","3":"4"},{"1":"Bulldogs","2":"2015","3":"4"},{"1":"Bulldogs","2":"2016","3":"4"},{"1":"Bulldogs","2":"2017","3":"5"},{"1":"Bulldogs","2":"2018","3":"5"},{"1":"Bulldogs","2":"2019","3":"5"},{"1":"Bulldogs","2":"2020","3":"5"},{"1":"Poodles","2":"2013","3":"8"},{"1":"Poodles","2":"2014","3":"7"},{"1":"Poodles","2":"2015","3":"8"},{"1":"Poodles","2":"2016","3":"7"},{"1":"Poodles","2":"2017","3":"7"},{"1":"Poodles","2":"2018","3":"7"},{"1":"Poodles","2":"2019","3":"6"},{"1":"Poodles","2":"2020","3":"6"},{"1":"Beagles","2":"2013","3":"4"},{"1":"Beagles","2":"2014","3":"5"},{"1":"Beagles","2":"2015","3":"5"},{"1":"Beagles","2":"2016","3":"5"},{"1":"Beagles","2":"2017","3":"6"},{"1":"Beagles","2":"2018","3":"6"},{"1":"Beagles","2":"2019","3":"7"},{"1":"Beagles","2":"2020","3":"7"},{"1":"Rottweilers","2":"2013","3":"9"},{"1":"Rottweilers","2":"2014","3":"10"},{"1":"Rottweilers","2":"2015","3":"9"},{"1":"Rottweilers","2":"2016","3":"8"},{"1":"Rottweilers","2":"2017","3":"8"},{"1":"Rottweilers","2":"2018","3":"8"},{"1":"Rottweilers","2":"2019","3":"8"},{"1":"Rottweilers","2":"2020","3":"8"},{"1":"Pointers (German Shorthaired)","2":"2013","3":"13"},{"1":"Pointers (German Shorthaired)","2":"2014","3":"12"},{"1":"Pointers (German Shorthaired)","2":"2015","3":"11"},{"1":"Pointers (German Shorthaired)","2":"2016","3":"11"},{"1":"Pointers (German Shorthaired)","2":"2017","3":"10"},{"1":"Pointers (German Shorthaired)","2":"2018","3":"9"},{"1":"Pointers (German Shorthaired)","2":"2019","3":"9"},{"1":"Pointers (German Shorthaired)","2":"2020","3":"9"},{"1":"Dachshunds","2":"2013","3":"10"},{"1":"Dachshunds","2":"2014","3":"11"},{"1":"Dachshunds","2":"2015","3":"13"},{"1":"Dachshunds","2":"2016","3":"13"},{"1":"Dachshunds","2":"2017","3":"13"},{"1":"Dachshunds","2":"2018","3":"12"},{"1":"Dachshunds","2":"2019","3":"11"},{"1":"Dachshunds","2":"2020","3":"10"},{"1":"Pembroke Welsh Corgis","2":"2013","3":"24"},{"1":"Pembroke Welsh Corgis","2":"2014","3":"22"},{"1":"Pembroke Welsh Corgis","2":"2015","3":"20"},{"1":"Pembroke Welsh Corgis","2":"2016","3":"18"},{"1":"Pembroke Welsh Corgis","2":"2017","3":"15"},{"1":"Pembroke Welsh Corgis","2":"2018","3":"13"},{"1":"Pembroke Welsh Corgis","2":"2019","3":"10"},{"1":"Pembroke Welsh Corgis","2":"2020","3":"11"},{"1":"Australian Shepherds","2":"2013","3":"20"},{"1":"Australian Shepherds","2":"2014","3":"18"},{"1":"Australian Shepherds","2":"2015","3":"17"},{"1":"Australian Shepherds","2":"2016","3":"16"},{"1":"Australian Shepherds","2":"2017","3":"17"},{"1":"Australian Shepherds","2":"2018","3":"15"},{"1":"Australian Shepherds","2":"2019","3":"13"},{"1":"Australian Shepherds","2":"2020","3":"12"},{"1":"Yorkshire Terriers","2":"2013","3":"6"},{"1":"Yorkshire Terriers","2":"2014","3":"6"},{"1":"Yorkshire Terriers","2":"2015","3":"7"},{"1":"Yorkshire Terriers","2":"2016","3":"9"},{"1":"Yorkshire Terriers","2":"2017","3":"9"},{"1":"Yorkshire Terriers","2":"2018","3":"10"},{"1":"Yorkshire Terriers","2":"2019","3":"12"},{"1":"Yorkshire Terriers","2":"2020","3":"13"},{"1":"Boxers","2":"2013","3":"7"},{"1":"Boxers","2":"2014","3":"8"},{"1":"Boxers","2":"2015","3":"10"},{"1":"Boxers","2":"2016","3":"10"},{"1":"Boxers","2":"2017","3":"11"},{"1":"Boxers","2":"2018","3":"11"},{"1":"Boxers","2":"2019","3":"14"},{"1":"Boxers","2":"2020","3":"14"},{"1":"Great Danes","2":"2013","3":"16"},{"1":"Great Danes","2":"2014","3":"15"},{"1":"Great Danes","2":"2015","3":"15"},{"1":"Great Danes","2":"2016","3":"14"},{"1":"Great Danes","2":"2017","3":"14"},{"1":"Great Danes","2":"2018","3":"16"},{"1":"Great Danes","2":"2019","3":"17"},{"1":"Great Danes","2":"2020","3":"15"},{"1":"Siberian Huskies","2":"2013","3":"14"},{"1":"Siberian Huskies","2":"2014","3":"13"},{"1":"Siberian Huskies","2":"2015","3":"12"},{"1":"Siberian Huskies","2":"2016","3":"12"},{"1":"Siberian Huskies","2":"2017","3":"12"},{"1":"Siberian Huskies","2":"2018","3":"14"},{"1":"Siberian Huskies","2":"2019","3":"15"},{"1":"Siberian Huskies","2":"2020","3":"16"},{"1":"Cavalier King Charles Spaniels","2":"2013","3":"18"},{"1":"Cavalier King Charles Spaniels","2":"2014","3":"19"},{"1":"Cavalier King Charles Spaniels","2":"2015","3":"18"},{"1":"Cavalier King Charles Spaniels","2":"2016","3":"19"},{"1":"Cavalier King Charles Spaniels","2":"2017","3":"19"},{"1":"Cavalier King Charles Spaniels","2":"2018","3":"18"},{"1":"Cavalier King Charles Spaniels","2":"2019","3":"16"},{"1":"Cavalier King Charles Spaniels","2":"2020","3":"17"},{"1":"Doberman Pinschers","2":"2013","3":"12"},{"1":"Doberman Pinschers","2":"2014","3":"14"},{"1":"Doberman Pinschers","2":"2015","3":"14"},{"1":"Doberman Pinschers","2":"2016","3":"15"},{"1":"Doberman Pinschers","2":"2017","3":"16"},{"1":"Doberman Pinschers","2":"2018","3":"17"},{"1":"Doberman Pinschers","2":"2019","3":"19"},{"1":"Doberman Pinschers","2":"2020","3":"18"},{"1":"Miniature Schnauzers","2":"2013","3":"17"},{"1":"Miniature Schnauzers","2":"2014","3":"16"},{"1":"Miniature Schnauzers","2":"2015","3":"16"},{"1":"Miniature Schnauzers","2":"2016","3":"17"},{"1":"Miniature Schnauzers","2":"2017","3":"18"},{"1":"Miniature Schnauzers","2":"2018","3":"19"},{"1":"Miniature Schnauzers","2":"2019","3":"18"},{"1":"Miniature Schnauzers","2":"2020","3":"19"},{"1":"Shih Tzu","2":"2013","3":"15"},{"1":"Shih Tzu","2":"2014","3":"17"},{"1":"Shih Tzu","2":"2015","3":"19"},{"1":"Shih Tzu","2":"2016","3":"20"},{"1":"Shih Tzu","2":"2017","3":"20"},{"1":"Shih Tzu","2":"2018","3":"20"},{"1":"Shih Tzu","2":"2019","3":"20"},{"1":"Shih Tzu","2":"2020","3":"20"},{"1":"Boston Terriers","2":"2013","3":"23"},{"1":"Boston Terriers","2":"2014","3":"23"},{"1":"Boston Terriers","2":"2015","3":"22"},{"1":"Boston Terriers","2":"2016","3":"21"},{"1":"Boston Terriers","2":"2017","3":"21"},{"1":"Boston Terriers","2":"2018","3":"21"},{"1":"Boston Terriers","2":"2019","3":"21"},{"1":"Boston Terriers","2":"2020","3":"21"},{"1":"Bernese Mountain Dogs","2":"2013","3":"32"},{"1":"Bernese Mountain Dogs","2":"2014","3":"32"},{"1":"Bernese Mountain Dogs","2":"2015","3":"29"},{"1":"Bernese Mountain Dogs","2":"2016","3":"27"},{"1":"Bernese Mountain Dogs","2":"2017","3":"25"},{"1":"Bernese Mountain Dogs","2":"2018","3":"22"},{"1":"Bernese Mountain Dogs","2":"2019","3":"23"},{"1":"Bernese Mountain Dogs","2":"2020","3":"22"},{"1":"Pomeranians","2":"2013","3":"19"},{"1":"Pomeranians","2":"2014","3":"20"},{"1":"Pomeranians","2":"2015","3":"21"},{"1":"Pomeranians","2":"2016","3":"22"},{"1":"Pomeranians","2":"2017","3":"22"},{"1":"Pomeranians","2":"2018","3":"23"},{"1":"Pomeranians","2":"2019","3":"24"},{"1":"Pomeranians","2":"2020","3":"23"},{"1":"Havanese","2":"2013","3":"25"},{"1":"Havanese","2":"2014","3":"25"},{"1":"Havanese","2":"2015","3":"24"},{"1":"Havanese","2":"2016","3":"23"},{"1":"Havanese","2":"2017","3":"23"},{"1":"Havanese","2":"2018","3":"24"},{"1":"Havanese","2":"2019","3":"22"},{"1":"Havanese","2":"2020","3":"24"},{"1":"Cane Corso","2":"2013","3":"50"},{"1":"Cane Corso","2":"2014","3":"48"},{"1":"Cane Corso","2":"2015","3":"35"},{"1":"Cane Corso","2":"2016","3":"40"},{"1":"Cane Corso","2":"2017","3":"37"},{"1":"Cane Corso","2":"2018","3":"32"},{"1":"Cane Corso","2":"2019","3":"30"},{"1":"Cane Corso","2":"2020","3":"25"},{"1":"Spaniels (English Springer)","2":"2013","3":"28"},{"1":"Spaniels (English Springer)","2":"2014","3":"28"},{"1":"Spaniels (English Springer)","2":"2015","3":"27"},{"1":"Spaniels (English Springer)","2":"2016","3":"26"},{"1":"Spaniels (English Springer)","2":"2017","3":"27"},{"1":"Spaniels (English Springer)","2":"2018","3":"27"},{"1":"Spaniels (English Springer)","2":"2019","3":"27"},{"1":"Spaniels (English Springer)","2":"2020","3":"26"},{"1":"Shetland Sheepdogs","2":"2013","3":"21"},{"1":"Shetland Sheepdogs","2":"2014","3":"21"},{"1":"Shetland Sheepdogs","2":"2015","3":"23"},{"1":"Shetland Sheepdogs","2":"2016","3":"24"},{"1":"Shetland Sheepdogs","2":"2017","3":"24"},{"1":"Shetland Sheepdogs","2":"2018","3":"25"},{"1":"Shetland Sheepdogs","2":"2019","3":"25"},{"1":"Shetland Sheepdogs","2":"2020","3":"27"},{"1":"Brittanys","2":"2013","3":"30"},{"1":"Brittanys","2":"2014","3":"27"},{"1":"Brittanys","2":"2015","3":"26"},{"1":"Brittanys","2":"2016","3":"25"},{"1":"Brittanys","2":"2017","3":"26"},{"1":"Brittanys","2":"2018","3":"26"},{"1":"Brittanys","2":"2019","3":"26"},{"1":"Brittanys","2":"2020","3":"28"},{"1":"Pugs","2":"2013","3":"31"},{"1":"Pugs","2":"2014","3":"33"},{"1":"Pugs","2":"2015","3":"33"},{"1":"Pugs","2":"2016","3":"32"},{"1":"Pugs","2":"2017","3":"31"},{"1":"Pugs","2":"2018","3":"28"},{"1":"Pugs","2":"2019","3":"31"},{"1":"Pugs","2":"2020","3":"29"},{"1":"Spaniels (Cocker)","2":"2013","3":"29"},{"1":"Spaniels (Cocker)","2":"2014","3":"30"},{"1":"Spaniels (Cocker)","2":"2015","3":"30"},{"1":"Spaniels (Cocker)","2":"2016","3":"29"},{"1":"Spaniels (Cocker)","2":"2017","3":"29"},{"1":"Spaniels (Cocker)","2":"2018","3":"30"},{"1":"Spaniels (Cocker)","2":"2019","3":"28"},{"1":"Spaniels (Cocker)","2":"2020","3":"30"},{"1":"Miniature American Shepherds","2":"2013","3":"NA"},{"1":"Miniature American Shepherds","2":"2014","3":"NA"},{"1":"Miniature American Shepherds","2":"2015","3":"NA"},{"1":"Miniature American Shepherds","2":"2016","3":"NA"},{"1":"Miniature American Shepherds","2":"2017","3":"NA"},{"1":"Miniature American Shepherds","2":"2018","3":"NA"},{"1":"Miniature American Shepherds","2":"2019","3":"NA"},{"1":"Miniature American Shepherds","2":"2020","3":"31"},{"1":"Border Collies","2":"2013","3":"44"},{"1":"Border Collies","2":"2014","3":"40"},{"1":"Border Collies","2":"2015","3":"38"},{"1":"Border Collies","2":"2016","3":"38"},{"1":"Border Collies","2":"2017","3":"38"},{"1":"Border Collies","2":"2018","3":"35"},{"1":"Border Collies","2":"2019","3":"33"},{"1":"Border Collies","2":"2020","3":"32"},{"1":"Mastiffs","2":"2013","3":"26"},{"1":"Mastiffs","2":"2014","3":"26"},{"1":"Mastiffs","2":"2015","3":"25"},{"1":"Mastiffs","2":"2016","3":"28"},{"1":"Mastiffs","2":"2017","3":"28"},{"1":"Mastiffs","2":"2018","3":"29"},{"1":"Mastiffs","2":"2019","3":"32"},{"1":"Mastiffs","2":"2020","3":"33"},{"1":"Chihuahuas","2":"2013","3":"22"},{"1":"Chihuahuas","2":"2014","3":"24"},{"1":"Chihuahuas","2":"2015","3":"28"},{"1":"Chihuahuas","2":"2016","3":"30"},{"1":"Chihuahuas","2":"2017","3":"32"},{"1":"Chihuahuas","2":"2018","3":"33"},{"1":"Chihuahuas","2":"2019","3":"35"},{"1":"Chihuahuas","2":"2020","3":"34"},{"1":"Vizslas","2":"2013","3":"34"},{"1":"Vizslas","2":"2014","3":"34"},{"1":"Vizslas","2":"2015","3":"32"},{"1":"Vizslas","2":"2016","3":"31"},{"1":"Vizslas","2":"2017","3":"30"},{"1":"Vizslas","2":"2018","3":"31"},{"1":"Vizslas","2":"2019","3":"34"},{"1":"Vizslas","2":"2020","3":"35"},{"1":"Basset Hounds","2":"2013","3":"42"},{"1":"Basset Hounds","2":"2014","3":"42"},{"1":"Basset Hounds","2":"2015","3":"39"},{"1":"Basset Hounds","2":"2016","3":"39"},{"1":"Basset Hounds","2":"2017","3":"39"},{"1":"Basset Hounds","2":"2018","3":"39"},{"1":"Basset Hounds","2":"2019","3":"37"},{"1":"Basset Hounds","2":"2020","3":"36"},{"1":"Belgian Malinois","2":"2013","3":"60"},{"1":"Belgian Malinois","2":"2014","3":"60"},{"1":"Belgian Malinois","2":"2015","3":"51"},{"1":"Belgian Malinois","2":"2016","3":"47"},{"1":"Belgian Malinois","2":"2017","3":"44"},{"1":"Belgian Malinois","2":"2018","3":"43"},{"1":"Belgian Malinois","2":"2019","3":"41"},{"1":"Belgian Malinois","2":"2020","3":"37"},{"1":"Maltese","2":"2013","3":"27"},{"1":"Maltese","2":"2014","3":"29"},{"1":"Maltese","2":"2015","3":"31"},{"1":"Maltese","2":"2016","3":"33"},{"1":"Maltese","2":"2017","3":"33"},{"1":"Maltese","2":"2018","3":"37"},{"1":"Maltese","2":"2019","3":"36"},{"1":"Maltese","2":"2020","3":"38"},{"1":"Weimaraners","2":"2013","3":"33"},{"1":"Weimaraners","2":"2014","3":"35"},{"1":"Weimaraners","2":"2015","3":"34"},{"1":"Weimaraners","2":"2016","3":"34"},{"1":"Weimaraners","2":"2017","3":"34"},{"1":"Weimaraners","2":"2018","3":"36"},{"1":"Weimaraners","2":"2019","3":"39"},{"1":"Weimaraners","2":"2020","3":"39"},{"1":"Collies","2":"2013","3":"35"},{"1":"Collies","2":"2014","3":"36"},{"1":"Collies","2":"2015","3":"36"},{"1":"Collies","2":"2016","3":"37"},{"1":"Collies","2":"2017","3":"40"},{"1":"Collies","2":"2018","3":"38"},{"1":"Collies","2":"2019","3":"38"},{"1":"Collies","2":"2020","3":"40"},{"1":"Newfoundlands","2":"2013","3":"37"},{"1":"Newfoundlands","2":"2014","3":"37"},{"1":"Newfoundlands","2":"2015","3":"37"},{"1":"Newfoundlands","2":"2016","3":"35"},{"1":"Newfoundlands","2":"2017","3":"36"},{"1":"Newfoundlands","2":"2018","3":"40"},{"1":"Newfoundlands","2":"2019","3":"40"},{"1":"Newfoundlands","2":"2020","3":"41"},{"1":"Rhodesian Ridgebacks","2":"2013","3":"39"},{"1":"Rhodesian Ridgebacks","2":"2014","3":"39"},{"1":"Rhodesian Ridgebacks","2":"2015","3":"40"},{"1":"Rhodesian Ridgebacks","2":"2016","3":"42"},{"1":"Rhodesian Ridgebacks","2":"2017","3":"41"},{"1":"Rhodesian Ridgebacks","2":"2018","3":"41"},{"1":"Rhodesian Ridgebacks","2":"2019","3":"42"},{"1":"Rhodesian Ridgebacks","2":"2020","3":"42"},{"1":"Shiba Inu","2":"2013","3":"46"},{"1":"Shiba Inu","2":"2014","3":"47"},{"1":"Shiba Inu","2":"2015","3":"45"},{"1":"Shiba Inu","2":"2016","3":"44"},{"1":"Shiba Inu","2":"2017","3":"45"},{"1":"Shiba Inu","2":"2018","3":"44"},{"1":"Shiba Inu","2":"2019","3":"45"},{"1":"Shiba Inu","2":"2020","3":"43"},{"1":"West Highland White Terriers","2":"2013","3":"36"},{"1":"West Highland White Terriers","2":"2014","3":"38"},{"1":"West Highland White Terriers","2":"2015","3":"41"},{"1":"West Highland White Terriers","2":"2016","3":"41"},{"1":"West Highland White Terriers","2":"2017","3":"42"},{"1":"West Highland White Terriers","2":"2018","3":"42"},{"1":"West Highland White Terriers","2":"2019","3":"44"},{"1":"West Highland White Terriers","2":"2020","3":"44"},{"1":"Bichons Frises","2":"2013","3":"40"},{"1":"Bichons Frises","2":"2014","3":"44"},{"1":"Bichons Frises","2":"2015","3":"44"},{"1":"Bichons Frises","2":"2016","3":"45"},{"1":"Bichons Frises","2":"2017","3":"46"},{"1":"Bichons Frises","2":"2018","3":"46"},{"1":"Bichons Frises","2":"2019","3":"43"},{"1":"Bichons Frises","2":"2020","3":"45"},{"1":"Bloodhounds","2":"2013","3":"48"},{"1":"Bloodhounds","2":"2014","3":"50"},{"1":"Bloodhounds","2":"2015","3":"49"},{"1":"Bloodhounds","2":"2016","3":"52"},{"1":"Bloodhounds","2":"2017","3":"50"},{"1":"Bloodhounds","2":"2018","3":"49"},{"1":"Bloodhounds","2":"2019","3":"51"},{"1":"Bloodhounds","2":"2020","3":"46"},{"1":"Spaniels (English Cocker)","2":"2013","3":"62"},{"1":"Spaniels (English Cocker)","2":"2014","3":"62"},{"1":"Spaniels (English Cocker)","2":"2015","3":"60"},{"1":"Spaniels (English Cocker)","2":"2016","3":"56"},{"1":"Spaniels (English Cocker)","2":"2017","3":"52"},{"1":"Spaniels (English Cocker)","2":"2018","3":"52"},{"1":"Spaniels (English Cocker)","2":"2019","3":"50"},{"1":"Spaniels (English Cocker)","2":"2020","3":"47"},{"1":"Akitas","2":"2013","3":"45"},{"1":"Akitas","2":"2014","3":"46"},{"1":"Akitas","2":"2015","3":"46"},{"1":"Akitas","2":"2016","3":"46"},{"1":"Akitas","2":"2017","3":"47"},{"1":"Akitas","2":"2018","3":"47"},{"1":"Akitas","2":"2019","3":"47"},{"1":"Akitas","2":"2020","3":"48"},{"1":"Portuguese Water Dogs","2":"2013","3":"49"},{"1":"Portuguese Water Dogs","2":"2014","3":"52"},{"1":"Portuguese Water Dogs","2":"2015","3":"52"},{"1":"Portuguese Water Dogs","2":"2016","3":"51"},{"1":"Portuguese Water Dogs","2":"2017","3":"54"},{"1":"Portuguese Water Dogs","2":"2018","3":"50"},{"1":"Portuguese Water Dogs","2":"2019","3":"49"},{"1":"Portuguese Water Dogs","2":"2020","3":"49"},{"1":"Retrievers (Chesapeake Bay)","2":"2013","3":"43"},{"1":"Retrievers (Chesapeake Bay)","2":"2014","3":"41"},{"1":"Retrievers (Chesapeake Bay)","2":"2015","3":"42"},{"1":"Retrievers (Chesapeake Bay)","2":"2016","3":"43"},{"1":"Retrievers (Chesapeake Bay)","2":"2017","3":"43"},{"1":"Retrievers (Chesapeake Bay)","2":"2018","3":"45"},{"1":"Retrievers (Chesapeake Bay)","2":"2019","3":"46"},{"1":"Retrievers (Chesapeake Bay)","2":"2020","3":"50"},{"1":"Dalmatians","2":"2013","3":"64"},{"1":"Dalmatians","2":"2014","3":"66"},{"1":"Dalmatians","2":"2015","3":"62"},{"1":"Dalmatians","2":"2016","3":"62"},{"1":"Dalmatians","2":"2017","3":"63"},{"1":"Dalmatians","2":"2018","3":"56"},{"1":"Dalmatians","2":"2019","3":"60"},{"1":"Dalmatians","2":"2020","3":"51"},{"1":"St. Bernards","2":"2013","3":"47"},{"1":"St. Bernards","2":"2014","3":"51"},{"1":"St. Bernards","2":"2015","3":"50"},{"1":"St. Bernards","2":"2016","3":"49"},{"1":"St. Bernards","2":"2017","3":"48"},{"1":"St. Bernards","2":"2018","3":"48"},{"1":"St. Bernards","2":"2019","3":"48"},{"1":"St. Bernards","2":"2020","3":"52"},{"1":"Papillons","2":"2013","3":"38"},{"1":"Papillons","2":"2014","3":"43"},{"1":"Papillons","2":"2015","3":"48"},{"1":"Papillons","2":"2016","3":"53"},{"1":"Papillons","2":"2017","3":"53"},{"1":"Papillons","2":"2018","3":"54"},{"1":"Papillons","2":"2019","3":"53"},{"1":"Papillons","2":"2020","3":"53"},{"1":"Australian Cattle Dogs","2":"2013","3":"58"},{"1":"Australian Cattle Dogs","2":"2014","3":"55"},{"1":"Australian Cattle Dogs","2":"2015","3":"56"},{"1":"Australian Cattle Dogs","2":"2016","3":"54"},{"1":"Australian Cattle Dogs","2":"2017","3":"56"},{"1":"Australian Cattle Dogs","2":"2018","3":"55"},{"1":"Australian Cattle Dogs","2":"2019","3":"55"},{"1":"Australian Cattle Dogs","2":"2020","3":"54"},{"1":"Bullmastiffs","2":"2013","3":"41"},{"1":"Bullmastiffs","2":"2014","3":"45"},{"1":"Bullmastiffs","2":"2015","3":"43"},{"1":"Bullmastiffs","2":"2016","3":"48"},{"1":"Bullmastiffs","2":"2017","3":"51"},{"1":"Bullmastiffs","2":"2018","3":"51"},{"1":"Bullmastiffs","2":"2019","3":"52"},{"1":"Bullmastiffs","2":"2020","3":"55"},{"1":"Samoyeds","2":"2013","3":"67"},{"1":"Samoyeds","2":"2014","3":"68"},{"1":"Samoyeds","2":"2015","3":"61"},{"1":"Samoyeds","2":"2016","3":"65"},{"1":"Samoyeds","2":"2017","3":"57"},{"1":"Samoyeds","2":"2018","3":"59"},{"1":"Samoyeds","2":"2019","3":"59"},{"1":"Samoyeds","2":"2020","3":"56"},{"1":"Scottish Terriers","2":"2013","3":"55"},{"1":"Scottish Terriers","2":"2014","3":"59"},{"1":"Scottish Terriers","2":"2015","3":"58"},{"1":"Scottish Terriers","2":"2016","3":"58"},{"1":"Scottish Terriers","2":"2017","3":"58"},{"1":"Scottish Terriers","2":"2018","3":"57"},{"1":"Scottish Terriers","2":"2019","3":"57"},{"1":"Scottish Terriers","2":"2020","3":"57"},{"1":"Soft Coated Wheaten Terriers","2":"2013","3":"51"},{"1":"Soft Coated Wheaten Terriers","2":"2014","3":"49"},{"1":"Soft Coated Wheaten Terriers","2":"2015","3":"47"},{"1":"Soft Coated Wheaten Terriers","2":"2016","3":"50"},{"1":"Soft Coated Wheaten Terriers","2":"2017","3":"49"},{"1":"Soft Coated Wheaten Terriers","2":"2018","3":"53"},{"1":"Soft Coated Wheaten Terriers","2":"2019","3":"54"},{"1":"Soft Coated Wheaten Terriers","2":"2020","3":"58"},{"1":"Whippets","2":"2013","3":"59"},{"1":"Whippets","2":"2014","3":"56"},{"1":"Whippets","2":"2015","3":"57"},{"1":"Whippets","2":"2016","3":"60"},{"1":"Whippets","2":"2017","3":"61"},{"1":"Whippets","2":"2018","3":"61"},{"1":"Whippets","2":"2019","3":"58"},{"1":"Whippets","2":"2020","3":"59"},{"1":"Pointers (German Wirehaired)","2":"2013","3":"71"},{"1":"Pointers (German Wirehaired)","2":"2014","3":"71"},{"1":"Pointers (German Wirehaired)","2":"2015","3":"68"},{"1":"Pointers (German Wirehaired)","2":"2016","3":"64"},{"1":"Pointers (German Wirehaired)","2":"2017","3":"62"},{"1":"Pointers (German Wirehaired)","2":"2018","3":"63"},{"1":"Pointers (German Wirehaired)","2":"2019","3":"64"},{"1":"Pointers (German Wirehaired)","2":"2020","3":"60"},{"1":"Chinese Shar-Pei","2":"2013","3":"54"},{"1":"Chinese Shar-Pei","2":"2014","3":"58"},{"1":"Chinese Shar-Pei","2":"2015","3":"59"},{"1":"Chinese Shar-Pei","2":"2016","3":"61"},{"1":"Chinese Shar-Pei","2":"2017","3":"64"},{"1":"Chinese Shar-Pei","2":"2018","3":"64"},{"1":"Chinese Shar-Pei","2":"2019","3":"66"},{"1":"Chinese Shar-Pei","2":"2020","3":"61"},{"1":"Airedale Terriers","2":"2013","3":"56"},{"1":"Airedale Terriers","2":"2014","3":"57"},{"1":"Airedale Terriers","2":"2015","3":"53"},{"1":"Airedale Terriers","2":"2016","3":"55"},{"1":"Airedale Terriers","2":"2017","3":"55"},{"1":"Airedale Terriers","2":"2018","3":"60"},{"1":"Airedale Terriers","2":"2019","3":"61"},{"1":"Airedale Terriers","2":"2020","3":"62"},{"1":"Wirehaired Pointing Griffons","2":"2013","3":"80"},{"1":"Wirehaired Pointing Griffons","2":"2014","3":"76"},{"1":"Wirehaired Pointing Griffons","2":"2015","3":"66"},{"1":"Wirehaired Pointing Griffons","2":"2016","3":"66"},{"1":"Wirehaired Pointing Griffons","2":"2017","3":"65"},{"1":"Wirehaired Pointing Griffons","2":"2018","3":"65"},{"1":"Wirehaired Pointing Griffons","2":"2019","3":"63"},{"1":"Wirehaired Pointing Griffons","2":"2020","3":"63"},{"1":"Bull Terriers","2":"2013","3":"52"},{"1":"Bull Terriers","2":"2014","3":"53"},{"1":"Bull Terriers","2":"2015","3":"55"},{"1":"Bull Terriers","2":"2016","3":"57"},{"1":"Bull Terriers","2":"2017","3":"60"},{"1":"Bull Terriers","2":"2018","3":"62"},{"1":"Bull Terriers","2":"2019","3":"62"},{"1":"Bull Terriers","2":"2020","3":"64"},{"1":"Alaskan Malamutes","2":"2013","3":"57"},{"1":"Alaskan Malamutes","2":"2014","3":"54"},{"1":"Alaskan Malamutes","2":"2015","3":"54"},{"1":"Alaskan Malamutes","2":"2016","3":"59"},{"1":"Alaskan Malamutes","2":"2017","3":"59"},{"1":"Alaskan Malamutes","2":"2018","3":"58"},{"1":"Alaskan Malamutes","2":"2019","3":"65"},{"1":"Alaskan Malamutes","2":"2020","3":"65"},{"1":"Cardigan Welsh Corgis","2":"2013","3":"75"},{"1":"Cardigan Welsh Corgis","2":"2014","3":"78"},{"1":"Cardigan Welsh Corgis","2":"2015","3":"76"},{"1":"Cardigan Welsh Corgis","2":"2016","3":"69"},{"1":"Cardigan Welsh Corgis","2":"2017","3":"68"},{"1":"Cardigan Welsh Corgis","2":"2018","3":"68"},{"1":"Cardigan Welsh Corgis","2":"2019","3":"67"},{"1":"Cardigan Welsh Corgis","2":"2020","3":"66"},{"1":"Giant Schnauzers","2":"2013","3":"83"},{"1":"Giant Schnauzers","2":"2014","3":"83"},{"1":"Giant Schnauzers","2":"2015","3":"81"},{"1":"Giant Schnauzers","2":"2016","3":"79"},{"1":"Giant Schnauzers","2":"2017","3":"80"},{"1":"Giant Schnauzers","2":"2018","3":"78"},{"1":"Giant Schnauzers","2":"2019","3":"72"},{"1":"Giant Schnauzers","2":"2020","3":"67"},{"1":"Old English Sheepdogs","2":"2013","3":"78"},{"1":"Old English Sheepdogs","2":"2014","3":"77"},{"1":"Old English Sheepdogs","2":"2015","3":"74"},{"1":"Old English Sheepdogs","2":"2016","3":"75"},{"1":"Old English Sheepdogs","2":"2017","3":"70"},{"1":"Old English Sheepdogs","2":"2018","3":"72"},{"1":"Old English Sheepdogs","2":"2019","3":"71"},{"1":"Old English Sheepdogs","2":"2020","3":"68"},{"1":"Italian Greyhounds","2":"2013","3":"66"},{"1":"Italian Greyhounds","2":"2014","3":"74"},{"1":"Italian Greyhounds","2":"2015","3":"71"},{"1":"Italian Greyhounds","2":"2016","3":"72"},{"1":"Italian Greyhounds","2":"2017","3":"74"},{"1":"Italian Greyhounds","2":"2018","3":"73"},{"1":"Italian Greyhounds","2":"2019","3":"68"},{"1":"Italian Greyhounds","2":"2020","3":"69"},{"1":"Great Pyrenees","2":"2013","3":"69"},{"1":"Great Pyrenees","2":"2014","3":"75"},{"1":"Great Pyrenees","2":"2015","3":"67"},{"1":"Great Pyrenees","2":"2016","3":"67"},{"1":"Great Pyrenees","2":"2017","3":"66"},{"1":"Great Pyrenees","2":"2018","3":"66"},{"1":"Great Pyrenees","2":"2019","3":"70"},{"1":"Great Pyrenees","2":"2020","3":"70"},{"1":"Dogues de Bordeaux","2":"2013","3":"65"},{"1":"Dogues de Bordeaux","2":"2014","3":"63"},{"1":"Dogues de Bordeaux","2":"2015","3":"63"},{"1":"Dogues de Bordeaux","2":"2016","3":"63"},{"1":"Dogues de Bordeaux","2":"2017","3":"67"},{"1":"Dogues de Bordeaux","2":"2018","3":"67"},{"1":"Dogues de Bordeaux","2":"2019","3":"69"},{"1":"Dogues de Bordeaux","2":"2020","3":"71"},{"1":"Russell Terriers","2":"2013","3":"102"},{"1":"Russell Terriers","2":"2014","3":"105"},{"1":"Russell Terriers","2":"2015","3":"104"},{"1":"Russell Terriers","2":"2016","3":"90"},{"1":"Russell Terriers","2":"2017","3":"78"},{"1":"Russell Terriers","2":"2018","3":"82"},{"1":"Russell Terriers","2":"2019","3":"76"},{"1":"Russell Terriers","2":"2020","3":"72"},{"1":"Cairn Terriers","2":"2013","3":"61"},{"1":"Cairn Terriers","2":"2014","3":"69"},{"1":"Cairn Terriers","2":"2015","3":"70"},{"1":"Cairn Terriers","2":"2016","3":"70"},{"1":"Cairn Terriers","2":"2017","3":"69"},{"1":"Cairn Terriers","2":"2018","3":"69"},{"1":"Cairn Terriers","2":"2019","3":"73"},{"1":"Cairn Terriers","2":"2020","3":"73"},{"1":"Irish Wolfhounds","2":"2013","3":"73"},{"1":"Irish Wolfhounds","2":"2014","3":"72"},{"1":"Irish Wolfhounds","2":"2015","3":"69"},{"1":"Irish Wolfhounds","2":"2016","3":"73"},{"1":"Irish Wolfhounds","2":"2017","3":"73"},{"1":"Irish Wolfhounds","2":"2018","3":"76"},{"1":"Irish Wolfhounds","2":"2019","3":"77"},{"1":"Irish Wolfhounds","2":"2020","3":"74"},{"1":"Setters (Irish)","2":"2013","3":"72"},{"1":"Setters (Irish)","2":"2014","3":"73"},{"1":"Setters (Irish)","2":"2015","3":"72"},{"1":"Setters (Irish)","2":"2016","3":"76"},{"1":"Setters (Irish)","2":"2017","3":"72"},{"1":"Setters (Irish)","2":"2018","3":"77"},{"1":"Setters (Irish)","2":"2019","3":"80"},{"1":"Setters (Irish)","2":"2020","3":"75"},{"1":"Greater Swiss Mountain Dogs","2":"2013","3":"74"},{"1":"Greater Swiss Mountain Dogs","2":"2014","3":"80"},{"1":"Greater Swiss Mountain Dogs","2":"2015","3":"78"},{"1":"Greater Swiss Mountain Dogs","2":"2016","3":"78"},{"1":"Greater Swiss Mountain Dogs","2":"2017","3":"75"},{"1":"Greater Swiss Mountain Dogs","2":"2018","3":"74"},{"1":"Greater Swiss Mountain Dogs","2":"2019","3":"74"},{"1":"Greater Swiss Mountain Dogs","2":"2020","3":"76"},{"1":"Miniature Pinschers","2":"2013","3":"53"},{"1":"Miniature Pinschers","2":"2014","3":"61"},{"1":"Miniature Pinschers","2":"2015","3":"64"},{"1":"Miniature Pinschers","2":"2016","3":"68"},{"1":"Miniature Pinschers","2":"2017","3":"71"},{"1":"Miniature Pinschers","2":"2018","3":"70"},{"1":"Miniature Pinschers","2":"2019","3":"75"},{"1":"Miniature Pinschers","2":"2020","3":"77"},{"1":"Lhasa Apsos","2":"2013","3":"63"},{"1":"Lhasa Apsos","2":"2014","3":"67"},{"1":"Lhasa Apsos","2":"2015","3":"65"},{"1":"Lhasa Apsos","2":"2016","3":"71"},{"1":"Lhasa Apsos","2":"2017","3":"77"},{"1":"Lhasa Apsos","2":"2018","3":"71"},{"1":"Lhasa Apsos","2":"2019","3":"79"},{"1":"Lhasa Apsos","2":"2020","3":"78"},{"1":"Chinese Crested","2":"2013","3":"68"},{"1":"Chinese Crested","2":"2014","3":"65"},{"1":"Chinese Crested","2":"2015","3":"75"},{"1":"Chinese Crested","2":"2016","3":"77"},{"1":"Chinese Crested","2":"2017","3":"79"},{"1":"Chinese Crested","2":"2018","3":"79"},{"1":"Chinese Crested","2":"2019","3":"81"},{"1":"Chinese Crested","2":"2020","3":"79"},{"1":"Coton de Tulear","2":"2013","3":"NA"},{"1":"Coton de Tulear","2":"2014","3":"NA"},{"1":"Coton de Tulear","2":"2015","3":"NA"},{"1":"Coton de Tulear","2":"2016","3":"NA"},{"1":"Coton de Tulear","2":"2017","3":"NA"},{"1":"Coton de Tulear","2":"2018","3":"NA"},{"1":"Coton de Tulear","2":"2019","3":"NA"},{"1":"Coton de Tulear","2":"2020","3":"80"},{"1":"Staffordshire Bull Terriers","2":"2013","3":"79"},{"1":"Staffordshire Bull Terriers","2":"2014","3":"79"},{"1":"Staffordshire Bull Terriers","2":"2015","3":"79"},{"1":"Staffordshire Bull Terriers","2":"2016","3":"82"},{"1":"Staffordshire Bull Terriers","2":"2017","3":"82"},{"1":"Staffordshire Bull Terriers","2":"2018","3":"80"},{"1":"Staffordshire Bull Terriers","2":"2019","3":"83"},{"1":"Staffordshire Bull Terriers","2":"2020","3":"81"},{"1":"American Staffordshire Terriers","2":"2013","3":"76"},{"1":"American Staffordshire Terriers","2":"2014","3":"84"},{"1":"American Staffordshire Terriers","2":"2015","3":"77"},{"1":"American Staffordshire Terriers","2":"2016","3":"81"},{"1":"American Staffordshire Terriers","2":"2017","3":"83"},{"1":"American Staffordshire Terriers","2":"2018","3":"85"},{"1":"American Staffordshire Terriers","2":"2019","3":"86"},{"1":"American Staffordshire Terriers","2":"2020","3":"82"},{"1":"Rat Terriers","2":"2013","3":"NA"},{"1":"Rat Terriers","2":"2014","3":"NA"},{"1":"Rat Terriers","2":"2015","3":"NA"},{"1":"Rat Terriers","2":"2016","3":"NA"},{"1":"Rat Terriers","2":"2017","3":"NA"},{"1":"Rat Terriers","2":"2018","3":"NA"},{"1":"Rat Terriers","2":"2019","3":"NA"},{"1":"Rat Terriers","2":"2020","3":"83"},{"1":"Chow Chows","2":"2013","3":"70"},{"1":"Chow Chows","2":"2014","3":"70"},{"1":"Chow Chows","2":"2015","3":"73"},{"1":"Chow Chows","2":"2016","3":"74"},{"1":"Chow Chows","2":"2017","3":"76"},{"1":"Chow Chows","2":"2018","3":"75"},{"1":"Chow Chows","2":"2019","3":"78"},{"1":"Chow Chows","2":"2020","3":"84"},{"1":"Anatolian Shepherd Dogs","2":"2013","3":"93"},{"1":"Anatolian Shepherd Dogs","2":"2014","3":"94"},{"1":"Anatolian Shepherd Dogs","2":"2015","3":"92"},{"1":"Anatolian Shepherd Dogs","2":"2016","3":"84"},{"1":"Anatolian Shepherd Dogs","2":"2017","3":"86"},{"1":"Anatolian Shepherd Dogs","2":"2018","3":"90"},{"1":"Anatolian Shepherd Dogs","2":"2019","3":"96"},{"1":"Anatolian Shepherd Dogs","2":"2020","3":"85"},{"1":"Basenjis","2":"2013","3":"85"},{"1":"Basenjis","2":"2014","3":"86"},{"1":"Basenjis","2":"2015","3":"87"},{"1":"Basenjis","2":"2016","3":"88"},{"1":"Basenjis","2":"2017","3":"84"},{"1":"Basenjis","2":"2018","3":"87"},{"1":"Basenjis","2":"2019","3":"88"},{"1":"Basenjis","2":"2020","3":"86"},{"1":"Spaniels (Boykin)","2":"2013","3":"121"},{"1":"Spaniels (Boykin)","2":"2014","3":"108"},{"1":"Spaniels (Boykin)","2":"2015","3":"107"},{"1":"Spaniels (Boykin)","2":"2016","3":"110"},{"1":"Spaniels (Boykin)","2":"2017","3":"98"},{"1":"Spaniels (Boykin)","2":"2018","3":"100"},{"1":"Spaniels (Boykin)","2":"2019","3":"90"},{"1":"Spaniels (Boykin)","2":"2020","3":"87"},{"1":"Lagotti Romagnoli","2":"2013","3":"NA"},{"1":"Lagotti Romagnoli","2":"2014","3":"NA"},{"1":"Lagotti Romagnoli","2":"2015","3":"NA"},{"1":"Lagotti Romagnoli","2":"2016","3":"NA"},{"1":"Lagotti Romagnoli","2":"2017","3":"NA"},{"1":"Lagotti Romagnoli","2":"2018","3":"NA"},{"1":"Lagotti Romagnoli","2":"2019","3":"NA"},{"1":"Lagotti Romagnoli","2":"2020","3":"88"},{"1":"Brussels Griffons","2":"2013","3":"84"},{"1":"Brussels Griffons","2":"2014","3":"91"},{"1":"Brussels Griffons","2":"2015","3":"95"},{"1":"Brussels Griffons","2":"2016","3":"97"},{"1":"Brussels Griffons","2":"2017","3":"94"},{"1":"Brussels Griffons","2":"2018","3":"98"},{"1":"Brussels Griffons","2":"2019","3":"98"},{"1":"Brussels Griffons","2":"2020","3":"89"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2013","3":"97"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2014","3":"99"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2015","3":"99"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2016","3":"87"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2017","3":"89"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2018","3":"83"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2019","3":"87"},{"1":"Retrievers (Nova Scotia Duck Tolling)","2":"2020","3":"90"},{"1":"Norwegian Elkhounds","2":"2013","3":"103"},{"1":"Norwegian Elkhounds","2":"2014","3":"103"},{"1":"Norwegian Elkhounds","2":"2015","3":"88"},{"1":"Norwegian Elkhounds","2":"2016","3":"94"},{"1":"Norwegian Elkhounds","2":"2017","3":"91"},{"1":"Norwegian Elkhounds","2":"2018","3":"97"},{"1":"Norwegian Elkhounds","2":"2019","3":"94"},{"1":"Norwegian Elkhounds","2":"2020","3":"91"},{"1":"Standard Schnauzers","2":"2013","3":"90"},{"1":"Standard Schnauzers","2":"2014","3":"90"},{"1":"Standard Schnauzers","2":"2015","3":"91"},{"1":"Standard Schnauzers","2":"2016","3":"85"},{"1":"Standard Schnauzers","2":"2017","3":"90"},{"1":"Standard Schnauzers","2":"2018","3":"89"},{"1":"Standard Schnauzers","2":"2019","3":"99"},{"1":"Standard Schnauzers","2":"2020","3":"92"},{"1":"Dogo Argentinos","2":"2013","3":"NA"},{"1":"Dogo Argentinos","2":"2014","3":"NA"},{"1":"Dogo Argentinos","2":"2015","3":"NA"},{"1":"Dogo Argentinos","2":"2016","3":"NA"},{"1":"Dogo Argentinos","2":"2017","3":"NA"},{"1":"Dogo Argentinos","2":"2018","3":"NA"},{"1":"Dogo Argentinos","2":"2019","3":"NA"},{"1":"Dogo Argentinos","2":"2020","3":"93"},{"1":"Bouviers des Flandres","2":"2013","3":"82"},{"1":"Bouviers des Flandres","2":"2014","3":"81"},{"1":"Bouviers des Flandres","2":"2015","3":"83"},{"1":"Bouviers des Flandres","2":"2016","3":"83"},{"1":"Bouviers des Flandres","2":"2017","3":"85"},{"1":"Bouviers des Flandres","2":"2018","3":"84"},{"1":"Bouviers des Flandres","2":"2019","3":"93"},{"1":"Bouviers des Flandres","2":"2020","3":"94"},{"1":"Pekingese","2":"2013","3":"77"},{"1":"Pekingese","2":"2014","3":"82"},{"1":"Pekingese","2":"2015","3":"80"},{"1":"Pekingese","2":"2016","3":"93"},{"1":"Pekingese","2":"2017","3":"88"},{"1":"Pekingese","2":"2018","3":"92"},{"1":"Pekingese","2":"2019","3":"84"},{"1":"Pekingese","2":"2020","3":"95"},{"1":"Keeshonden","2":"2013","3":"86"},{"1":"Keeshonden","2":"2014","3":"87"},{"1":"Keeshonden","2":"2015","3":"84"},{"1":"Keeshonden","2":"2016","3":"92"},{"1":"Keeshonden","2":"2017","3":"87"},{"1":"Keeshonden","2":"2018","3":"95"},{"1":"Keeshonden","2":"2019","3":"89"},{"1":"Keeshonden","2":"2020","3":"96"},{"1":"Border Terriers","2":"2013","3":"81"},{"1":"Border Terriers","2":"2014","3":"85"},{"1":"Border Terriers","2":"2015","3":"82"},{"1":"Border Terriers","2":"2016","3":"86"},{"1":"Border Terriers","2":"2017","3":"92"},{"1":"Border Terriers","2":"2018","3":"88"},{"1":"Border Terriers","2":"2019","3":"85"},{"1":"Border Terriers","2":"2020","3":"97"},{"1":"Leonbergers","2":"2013","3":"98"},{"1":"Leonbergers","2":"2014","3":"104"},{"1":"Leonbergers","2":"2015","3":"93"},{"1":"Leonbergers","2":"2016","3":"95"},{"1":"Leonbergers","2":"2017","3":"100"},{"1":"Leonbergers","2":"2018","3":"93"},{"1":"Leonbergers","2":"2019","3":"97"},{"1":"Leonbergers","2":"2020","3":"98"},{"1":"Tibetan Terriers","2":"2013","3":"88"},{"1":"Tibetan Terriers","2":"2014","3":"88"},{"1":"Tibetan Terriers","2":"2015","3":"90"},{"1":"Tibetan Terriers","2":"2016","3":"91"},{"1":"Tibetan Terriers","2":"2017","3":"101"},{"1":"Tibetan Terriers","2":"2018","3":"96"},{"1":"Tibetan Terriers","2":"2019","3":"103"},{"1":"Tibetan Terriers","2":"2020","3":"99"},{"1":"Neapolitan Mastiffs","2":"2013","3":"111"},{"1":"Neapolitan Mastiffs","2":"2014","3":"114"},{"1":"Neapolitan Mastiffs","2":"2015","3":"106"},{"1":"Neapolitan Mastiffs","2":"2016","3":"99"},{"1":"Neapolitan Mastiffs","2":"2017","3":"107"},{"1":"Neapolitan Mastiffs","2":"2018","3":"102"},{"1":"Neapolitan Mastiffs","2":"2019","3":"102"},{"1":"Neapolitan Mastiffs","2":"2020","3":"100"},{"1":"Setters (English)","2":"2013","3":"91"},{"1":"Setters (English)","2":"2014","3":"89"},{"1":"Setters (English)","2":"2015","3":"96"},{"1":"Setters (English)","2":"2016","3":"102"},{"1":"Setters (English)","2":"2017","3":"95"},{"1":"Setters (English)","2":"2018","3":"94"},{"1":"Setters (English)","2":"2019","3":"100"},{"1":"Setters (English)","2":"2020","3":"101"},{"1":"Retrievers (Flat-Coated)","2":"2013","3":"94"},{"1":"Retrievers (Flat-Coated)","2":"2014","3":"92"},{"1":"Retrievers (Flat-Coated)","2":"2015","3":"86"},{"1":"Retrievers (Flat-Coated)","2":"2016","3":"89"},{"1":"Retrievers (Flat-Coated)","2":"2017","3":"96"},{"1":"Retrievers (Flat-Coated)","2":"2018","3":"91"},{"1":"Retrievers (Flat-Coated)","2":"2019","3":"106"},{"1":"Retrievers (Flat-Coated)","2":"2020","3":"102"},{"1":"Borzois","2":"2013","3":"99"},{"1":"Borzois","2":"2014","3":"102"},{"1":"Borzois","2":"2015","3":"89"},{"1":"Borzois","2":"2016","3":"98"},{"1":"Borzois","2":"2017","3":"102"},{"1":"Borzois","2":"2018","3":"103"},{"1":"Borzois","2":"2019","3":"107"},{"1":"Borzois","2":"2020","3":"103"},{"1":"Fox Terriers (Wire)","2":"2013","3":"96"},{"1":"Fox Terriers (Wire)","2":"2014","3":"95"},{"1":"Fox Terriers (Wire)","2":"2015","3":"94"},{"1":"Fox Terriers (Wire)","2":"2016","3":"101"},{"1":"Fox Terriers (Wire)","2":"2017","3":"99"},{"1":"Fox Terriers (Wire)","2":"2018","3":"101"},{"1":"Fox Terriers (Wire)","2":"2019","3":"101"},{"1":"Fox Terriers (Wire)","2":"2020","3":"104"},{"1":"Miniature Bull Terriers","2":"2013","3":"125"},{"1":"Miniature Bull Terriers","2":"2014","3":"129"},{"1":"Miniature Bull Terriers","2":"2015","3":"121"},{"1":"Miniature Bull Terriers","2":"2016","3":"120"},{"1":"Miniature Bull Terriers","2":"2017","3":"115"},{"1":"Miniature Bull Terriers","2":"2018","3":"110"},{"1":"Miniature Bull Terriers","2":"2019","3":"114"},{"1":"Miniature Bull Terriers","2":"2020","3":"105"},{"1":"Belgian Tervuren","2":"2013","3":"108"},{"1":"Belgian Tervuren","2":"2014","3":"110"},{"1":"Belgian Tervuren","2":"2015","3":"98"},{"1":"Belgian Tervuren","2":"2016","3":"107"},{"1":"Belgian Tervuren","2":"2017","3":"103"},{"1":"Belgian Tervuren","2":"2018","3":"106"},{"1":"Belgian Tervuren","2":"2019","3":"105"},{"1":"Belgian Tervuren","2":"2020","3":"106"},{"1":"Setters (Gordon)","2":"2013","3":"105"},{"1":"Setters (Gordon)","2":"2014","3":"100"},{"1":"Setters (Gordon)","2":"2015","3":"105"},{"1":"Setters (Gordon)","2":"2016","3":"104"},{"1":"Setters (Gordon)","2":"2017","3":"104"},{"1":"Setters (Gordon)","2":"2018","3":"115"},{"1":"Setters (Gordon)","2":"2019","3":"115"},{"1":"Setters (Gordon)","2":"2020","3":"107"},{"1":"Silky Terriers","2":"2013","3":"92"},{"1":"Silky Terriers","2":"2014","3":"101"},{"1":"Silky Terriers","2":"2015","3":"102"},{"1":"Silky Terriers","2":"2016","3":"100"},{"1":"Silky Terriers","2":"2017","3":"106"},{"1":"Silky Terriers","2":"2018","3":"112"},{"1":"Silky Terriers","2":"2019","3":"111"},{"1":"Silky Terriers","2":"2020","3":"108"},{"1":"Norwich Terriers","2":"2013","3":"89"},{"1":"Norwich Terriers","2":"2014","3":"97"},{"1":"Norwich Terriers","2":"2015","3":"103"},{"1":"Norwich Terriers","2":"2016","3":"103"},{"1":"Norwich Terriers","2":"2017","3":"105"},{"1":"Norwich Terriers","2":"2018","3":"108"},{"1":"Norwich Terriers","2":"2019","3":"104"},{"1":"Norwich Terriers","2":"2020","3":"109"},{"1":"Spinoni Italiani","2":"2013","3":"117"},{"1":"Spinoni Italiani","2":"2014","3":"118"},{"1":"Spinoni Italiani","2":"2015","3":"112"},{"1":"Spinoni Italiani","2":"2016","3":"105"},{"1":"Spinoni Italiani","2":"2017","3":"111"},{"1":"Spinoni Italiani","2":"2018","3":"109"},{"1":"Spinoni Italiani","2":"2019","3":"118"},{"1":"Spinoni Italiani","2":"2020","3":"110"},{"1":"Japanese Chin","2":"2013","3":"87"},{"1":"Japanese Chin","2":"2014","3":"93"},{"1":"Japanese Chin","2":"2015","3":"97"},{"1":"Japanese Chin","2":"2016","3":"108"},{"1":"Japanese Chin","2":"2017","3":"108"},{"1":"Japanese Chin","2":"2018","3":"104"},{"1":"Japanese Chin","2":"2019","3":"110"},{"1":"Japanese Chin","2":"2020","3":"111"},{"1":"Welsh Terriers","2":"2013","3":"104"},{"1":"Welsh Terriers","2":"2014","3":"106"},{"1":"Welsh Terriers","2":"2015","3":"108"},{"1":"Welsh Terriers","2":"2016","3":"111"},{"1":"Welsh Terriers","2":"2017","3":"109"},{"1":"Welsh Terriers","2":"2018","3":"107"},{"1":"Welsh Terriers","2":"2019","3":"112"},{"1":"Welsh Terriers","2":"2020","3":"112"},{"1":"Toy Fox Terriers","2":"2013","3":"107"},{"1":"Toy Fox Terriers","2":"2014","3":"115"},{"1":"Toy Fox Terriers","2":"2015","3":"110"},{"1":"Toy Fox Terriers","2":"2016","3":"116"},{"1":"Toy Fox Terriers","2":"2017","3":"112"},{"1":"Toy Fox Terriers","2":"2018","3":"111"},{"1":"Toy Fox Terriers","2":"2019","3":"109"},{"1":"Toy Fox Terriers","2":"2020","3":"113"},{"1":"Schipperkes","2":"2013","3":"109"},{"1":"Schipperkes","2":"2014","3":"109"},{"1":"Schipperkes","2":"2015","3":"109"},{"1":"Schipperkes","2":"2016","3":"112"},{"1":"Schipperkes","2":"2017","3":"110"},{"1":"Schipperkes","2":"2018","3":"105"},{"1":"Schipperkes","2":"2019","3":"108"},{"1":"Schipperkes","2":"2020","3":"114"},{"1":"Parson Russell Terriers","2":"2013","3":"100"},{"1":"Parson Russell Terriers","2":"2014","3":"116"},{"1":"Parson Russell Terriers","2":"2015","3":"111"},{"1":"Parson Russell Terriers","2":"2016","3":"109"},{"1":"Parson Russell Terriers","2":"2017","3":"119"},{"1":"Parson Russell Terriers","2":"2018","3":"117"},{"1":"Parson Russell Terriers","2":"2019","3":"120"},{"1":"Parson Russell Terriers","2":"2020","3":"115"},{"1":"Pointers","2":"2013","3":"114"},{"1":"Pointers","2":"2014","3":"119"},{"1":"Pointers","2":"2015","3":"114"},{"1":"Pointers","2":"2016","3":"117"},{"1":"Pointers","2":"2017","3":"113"},{"1":"Pointers","2":"2018","3":"114"},{"1":"Pointers","2":"2019","3":"117"},{"1":"Pointers","2":"2020","3":"116"},{"1":"Belgian Sheepdogs","2":"2013","3":"119"},{"1":"Belgian Sheepdogs","2":"2014","3":"123"},{"1":"Belgian Sheepdogs","2":"2015","3":"120"},{"1":"Belgian Sheepdogs","2":"2016","3":"129"},{"1":"Belgian Sheepdogs","2":"2017","3":"120"},{"1":"Belgian Sheepdogs","2":"2018","3":"125"},{"1":"Belgian Sheepdogs","2":"2019","3":"128"},{"1":"Belgian Sheepdogs","2":"2020","3":"117"},{"1":"Tibetan Spaniels","2":"2013","3":"106"},{"1":"Tibetan Spaniels","2":"2014","3":"122"},{"1":"Tibetan Spaniels","2":"2015","3":"115"},{"1":"Tibetan Spaniels","2":"2016","3":"118"},{"1":"Tibetan Spaniels","2":"2017","3":"121"},{"1":"Tibetan Spaniels","2":"2018","3":"119"},{"1":"Tibetan Spaniels","2":"2019","3":"119"},{"1":"Tibetan Spaniels","2":"2020","3":"118"},{"1":"American Eskimo Dogs","2":"2013","3":"110"},{"1":"American Eskimo Dogs","2":"2014","3":"120"},{"1":"American Eskimo Dogs","2":"2015","3":"118"},{"1":"American Eskimo Dogs","2":"2016","3":"122"},{"1":"American Eskimo Dogs","2":"2017","3":"118"},{"1":"American Eskimo Dogs","2":"2018","3":"122"},{"1":"American Eskimo Dogs","2":"2019","3":"122"},{"1":"American Eskimo Dogs","2":"2020","3":"119"},{"1":"Irish Terriers","2":"2013","3":"123"},{"1":"Irish Terriers","2":"2014","3":"125"},{"1":"Irish Terriers","2":"2015","3":"113"},{"1":"Irish Terriers","2":"2016","3":"115"},{"1":"Irish Terriers","2":"2017","3":"114"},{"1":"Irish Terriers","2":"2018","3":"116"},{"1":"Irish Terriers","2":"2019","3":"121"},{"1":"Irish Terriers","2":"2020","3":"120"},{"1":"Beaucerons","2":"2013","3":"152"},{"1":"Beaucerons","2":"2014","3":"145"},{"1":"Beaucerons","2":"2015","3":"142"},{"1":"Beaucerons","2":"2016","3":"140"},{"1":"Beaucerons","2":"2017","3":"141"},{"1":"Beaucerons","2":"2018","3":"124"},{"1":"Beaucerons","2":"2019","3":"123"},{"1":"Beaucerons","2":"2020","3":"121"},{"1":"Afghan Hounds","2":"2013","3":"95"},{"1":"Afghan Hounds","2":"2014","3":"98"},{"1":"Afghan Hounds","2":"2015","3":"100"},{"1":"Afghan Hounds","2":"2016","3":"113"},{"1":"Afghan Hounds","2":"2017","3":"93"},{"1":"Afghan Hounds","2":"2018","3":"113"},{"1":"Afghan Hounds","2":"2019","3":"113"},{"1":"Afghan Hounds","2":"2020","3":"122"},{"1":"Boerboels","2":"2013","3":"NA"},{"1":"Boerboels","2":"2014","3":"NA"},{"1":"Boerboels","2":"2015","3":"NA"},{"1":"Boerboels","2":"2016","3":"NA"},{"1":"Boerboels","2":"2017","3":"NA"},{"1":"Boerboels","2":"2018","3":"NA"},{"1":"Boerboels","2":"2019","3":"NA"},{"1":"Boerboels","2":"2020","3":"123"},{"1":"Fox Terriers (Smooth)","2":"2013","3":"116"},{"1":"Fox Terriers (Smooth)","2":"2014","3":"124"},{"1":"Fox Terriers (Smooth)","2":"2015","3":"122"},{"1":"Fox Terriers (Smooth)","2":"2016","3":"124"},{"1":"Fox Terriers (Smooth)","2":"2017","3":"122"},{"1":"Fox Terriers (Smooth)","2":"2018","3":"123"},{"1":"Fox Terriers (Smooth)","2":"2019","3":"124"},{"1":"Fox Terriers (Smooth)","2":"2020","3":"124"},{"1":"Bearded Collies","2":"2013","3":"112"},{"1":"Bearded Collies","2":"2014","3":"121"},{"1":"Bearded Collies","2":"2015","3":"119"},{"1":"Bearded Collies","2":"2016","3":"126"},{"1":"Bearded Collies","2":"2017","3":"125"},{"1":"Bearded Collies","2":"2018","3":"127"},{"1":"Bearded Collies","2":"2019","3":"138"},{"1":"Bearded Collies","2":"2020","3":"125"},{"1":"Black Russian Terriers","2":"2013","3":"118"},{"1":"Black Russian Terriers","2":"2014","3":"127"},{"1":"Black Russian Terriers","2":"2015","3":"116"},{"1":"Black Russian Terriers","2":"2016","3":"119"},{"1":"Black Russian Terriers","2":"2017","3":"116"},{"1":"Black Russian Terriers","2":"2018","3":"118"},{"1":"Black Russian Terriers","2":"2019","3":"116"},{"1":"Black Russian Terriers","2":"2020","3":"126"},{"1":"Black and Tan Coonhounds","2":"2013","3":"113"},{"1":"Black and Tan Coonhounds","2":"2014","3":"126"},{"1":"Black and Tan Coonhounds","2":"2015","3":"127"},{"1":"Black and Tan Coonhounds","2":"2016","3":"128"},{"1":"Black and Tan Coonhounds","2":"2017","3":"130"},{"1":"Black and Tan Coonhounds","2":"2018","3":"138"},{"1":"Black and Tan Coonhounds","2":"2019","3":"143"},{"1":"Black and Tan Coonhounds","2":"2020","3":"127"},{"1":"Spaniels (Welsh Springer)","2":"2013","3":"124"},{"1":"Spaniels (Welsh Springer)","2":"2014","3":"133"},{"1":"Spaniels (Welsh Springer)","2":"2015","3":"128"},{"1":"Spaniels (Welsh Springer)","2":"2016","3":"123"},{"1":"Spaniels (Welsh Springer)","2":"2017","3":"133"},{"1":"Spaniels (Welsh Springer)","2":"2018","3":"128"},{"1":"Spaniels (Welsh Springer)","2":"2019","3":"132"},{"1":"Spaniels (Welsh Springer)","2":"2020","3":"128"},{"1":"American Hairless Terriers","2":"2013","3":"NA"},{"1":"American Hairless Terriers","2":"2014","3":"NA"},{"1":"American Hairless Terriers","2":"2015","3":"NA"},{"1":"American Hairless Terriers","2":"2016","3":"NA"},{"1":"American Hairless Terriers","2":"2017","3":"NA"},{"1":"American Hairless Terriers","2":"2018","3":"NA"},{"1":"American Hairless Terriers","2":"2019","3":"NA"},{"1":"American Hairless Terriers","2":"2020","3":"129"},{"1":"Norfolk Terriers","2":"2013","3":"129"},{"1":"Norfolk Terriers","2":"2014","3":"131"},{"1":"Norfolk Terriers","2":"2015","3":"131"},{"1":"Norfolk Terriers","2":"2016","3":"134"},{"1":"Norfolk Terriers","2":"2017","3":"128"},{"1":"Norfolk Terriers","2":"2018","3":"126"},{"1":"Norfolk Terriers","2":"2019","3":"133"},{"1":"Norfolk Terriers","2":"2020","3":"130"},{"1":"Xoloitzcuintli","2":"2013","3":"139"},{"1":"Xoloitzcuintli","2":"2014","3":"142"},{"1":"Xoloitzcuintli","2":"2015","3":"135"},{"1":"Xoloitzcuintli","2":"2016","3":"139"},{"1":"Xoloitzcuintli","2":"2017","3":"143"},{"1":"Xoloitzcuintli","2":"2018","3":"140"},{"1":"Xoloitzcuintli","2":"2019","3":"139"},{"1":"Xoloitzcuintli","2":"2020","3":"131"},{"1":"Manchester Terriers","2":"2013","3":"120"},{"1":"Manchester Terriers","2":"2014","3":"136"},{"1":"Manchester Terriers","2":"2015","3":"137"},{"1":"Manchester Terriers","2":"2016","3":"133"},{"1":"Manchester Terriers","2":"2017","3":"135"},{"1":"Manchester Terriers","2":"2018","3":"133"},{"1":"Manchester Terriers","2":"2019","3":"142"},{"1":"Manchester Terriers","2":"2020","3":"132"},{"1":"Kerry Blue Terriers","2":"2013","3":"126"},{"1":"Kerry Blue Terriers","2":"2014","3":"128"},{"1":"Kerry Blue Terriers","2":"2015","3":"124"},{"1":"Kerry Blue Terriers","2":"2016","3":"127"},{"1":"Kerry Blue Terriers","2":"2017","3":"129"},{"1":"Kerry Blue Terriers","2":"2018","3":"129"},{"1":"Kerry Blue Terriers","2":"2019","3":"135"},{"1":"Kerry Blue Terriers","2":"2020","3":"133"},{"1":"Australian Terriers","2":"2013","3":"122"},{"1":"Australian Terriers","2":"2014","3":"139"},{"1":"Australian Terriers","2":"2015","3":"125"},{"1":"Australian Terriers","2":"2016","3":"136"},{"1":"Australian Terriers","2":"2017","3":"137"},{"1":"Australian Terriers","2":"2018","3":"139"},{"1":"Australian Terriers","2":"2019","3":"144"},{"1":"Australian Terriers","2":"2020","3":"134"},{"1":"Spaniels (Clumber)","2":"2013","3":"131"},{"1":"Spaniels (Clumber)","2":"2014","3":"143"},{"1":"Spaniels (Clumber)","2":"2015","3":"134"},{"1":"Spaniels (Clumber)","2":"2016","3":"144"},{"1":"Spaniels (Clumber)","2":"2017","3":"140"},{"1":"Spaniels (Clumber)","2":"2018","3":"143"},{"1":"Spaniels (Clumber)","2":"2019","3":"136"},{"1":"Spaniels (Clumber)","2":"2020","3":"135"},{"1":"Lakeland Terriers","2":"2013","3":"134"},{"1":"Lakeland Terriers","2":"2014","3":"149"},{"1":"Lakeland Terriers","2":"2015","3":"141"},{"1":"Lakeland Terriers","2":"2016","3":"145"},{"1":"Lakeland Terriers","2":"2017","3":"138"},{"1":"Lakeland Terriers","2":"2018","3":"147"},{"1":"Lakeland Terriers","2":"2019","3":"150"},{"1":"Lakeland Terriers","2":"2020","3":"136"},{"1":"Bluetick Coonhounds","2":"2013","3":"128"},{"1":"Bluetick Coonhounds","2":"2014","3":"130"},{"1":"Bluetick Coonhounds","2":"2015","3":"123"},{"1":"Bluetick Coonhounds","2":"2016","3":"121"},{"1":"Bluetick Coonhounds","2":"2017","3":"132"},{"1":"Bluetick Coonhounds","2":"2018","3":"130"},{"1":"Bluetick Coonhounds","2":"2019","3":"140"},{"1":"Bluetick Coonhounds","2":"2020","3":"137"},{"1":"English Toy Spaniels","2":"2013","3":"135"},{"1":"English Toy Spaniels","2":"2014","3":"138"},{"1":"English Toy Spaniels","2":"2015","3":"129"},{"1":"English Toy Spaniels","2":"2016","3":"130"},{"1":"English Toy Spaniels","2":"2017","3":"134"},{"1":"English Toy Spaniels","2":"2018","3":"135"},{"1":"English Toy Spaniels","2":"2019","3":"141"},{"1":"English Toy Spaniels","2":"2020","3":"138"},{"1":"German Pinschers","2":"2013","3":"130"},{"1":"German Pinschers","2":"2014","3":"141"},{"1":"German Pinschers","2":"2015","3":"138"},{"1":"German Pinschers","2":"2016","3":"146"},{"1":"German Pinschers","2":"2017","3":"136"},{"1":"German Pinschers","2":"2018","3":"134"},{"1":"German Pinschers","2":"2019","3":"125"},{"1":"German Pinschers","2":"2020","3":"139"},{"1":"Tibetan Mastiffs","2":"2013","3":"132"},{"1":"Tibetan Mastiffs","2":"2014","3":"135"},{"1":"Tibetan Mastiffs","2":"2015","3":"133"},{"1":"Tibetan Mastiffs","2":"2016","3":"135"},{"1":"Tibetan Mastiffs","2":"2017","3":"153"},{"1":"Tibetan Mastiffs","2":"2018","3":"131"},{"1":"Tibetan Mastiffs","2":"2019","3":"130"},{"1":"Tibetan Mastiffs","2":"2020","3":"140"},{"1":"Bedlington Terriers","2":"2013","3":"137"},{"1":"Bedlington Terriers","2":"2014","3":"140"},{"1":"Bedlington Terriers","2":"2015","3":"146"},{"1":"Bedlington Terriers","2":"2016","3":"138"},{"1":"Bedlington Terriers","2":"2017","3":"151"},{"1":"Bedlington Terriers","2":"2018","3":"141"},{"1":"Bedlington Terriers","2":"2019","3":"153"},{"1":"Bedlington Terriers","2":"2020","3":"141"},{"1":"Greyhounds","2":"2013","3":"148"},{"1":"Greyhounds","2":"2014","3":"147"},{"1":"Greyhounds","2":"2015","3":"147"},{"1":"Greyhounds","2":"2016","3":"151"},{"1":"Greyhounds","2":"2017","3":"156"},{"1":"Greyhounds","2":"2018","3":"145"},{"1":"Greyhounds","2":"2019","3":"164"},{"1":"Greyhounds","2":"2020","3":"142"},{"1":"Pulik","2":"2013","3":"136"},{"1":"Pulik","2":"2014","3":"151"},{"1":"Pulik","2":"2015","3":"154"},{"1":"Pulik","2":"2016","3":"159"},{"1":"Pulik","2":"2017","3":"142"},{"1":"Pulik","2":"2018","3":"160"},{"1":"Pulik","2":"2019","3":"NA"},{"1":"Pulik","2":"2020","3":"143"},{"1":"Salukis","2":"2013","3":"115"},{"1":"Salukis","2":"2014","3":"134"},{"1":"Salukis","2":"2015","3":"132"},{"1":"Salukis","2":"2016","3":"125"},{"1":"Salukis","2":"2017","3":"123"},{"1":"Salukis","2":"2018","3":"120"},{"1":"Salukis","2":"2019","3":"127"},{"1":"Salukis","2":"2020","3":"144"},{"1":"Barbets","2":"2013","3":"NA"},{"1":"Barbets","2":"2014","3":"NA"},{"1":"Barbets","2":"2015","3":"NA"},{"1":"Barbets","2":"2016","3":"NA"},{"1":"Barbets","2":"2017","3":"NA"},{"1":"Barbets","2":"2018","3":"NA"},{"1":"Barbets","2":"2019","3":"NA"},{"1":"Barbets","2":"2020","3":"145"},{"1":"Redbone Coonhounds","2":"2013","3":"133"},{"1":"Redbone Coonhounds","2":"2014","3":"148"},{"1":"Redbone Coonhounds","2":"2015","3":"139"},{"1":"Redbone Coonhounds","2":"2016","3":"143"},{"1":"Redbone Coonhounds","2":"2017","3":"139"},{"1":"Redbone Coonhounds","2":"2018","3":"142"},{"1":"Redbone Coonhounds","2":"2019","3":"145"},{"1":"Redbone Coonhounds","2":"2020","3":"146"},{"1":"Swedish Vallhunds","2":"2013","3":"147"},{"1":"Swedish Vallhunds","2":"2014","3":"158"},{"1":"Swedish Vallhunds","2":"2015","3":"155"},{"1":"Swedish Vallhunds","2":"2016","3":"163"},{"1":"Swedish Vallhunds","2":"2017","3":"149"},{"1":"Swedish Vallhunds","2":"2018","3":"169"},{"1":"Swedish Vallhunds","2":"2019","3":"161"},{"1":"Swedish Vallhunds","2":"2020","3":"147"},{"1":"Sealyham Terriers","2":"2013","3":"158"},{"1":"Sealyham Terriers","2":"2014","3":"169"},{"1":"Sealyham Terriers","2":"2015","3":"158"},{"1":"Sealyham Terriers","2":"2016","3":"155"},{"1":"Sealyham Terriers","2":"2017","3":"150"},{"1":"Sealyham Terriers","2":"2018","3":"164"},{"1":"Sealyham Terriers","2":"2019","3":"156"},{"1":"Sealyham Terriers","2":"2020","3":"148"},{"1":"Spanish Water Dogs","2":"2013","3":"NA"},{"1":"Spanish Water Dogs","2":"2014","3":"NA"},{"1":"Spanish Water Dogs","2":"2015","3":"NA"},{"1":"Spanish Water Dogs","2":"2016","3":"NA"},{"1":"Spanish Water Dogs","2":"2017","3":"NA"},{"1":"Spanish Water Dogs","2":"2018","3":"NA"},{"1":"Spanish Water Dogs","2":"2019","3":"NA"},{"1":"Spanish Water Dogs","2":"2020","3":"149"},{"1":"Briards","2":"2013","3":"127"},{"1":"Briards","2":"2014","3":"132"},{"1":"Briards","2":"2015","3":"130"},{"1":"Briards","2":"2016","3":"132"},{"1":"Briards","2":"2017","3":"127"},{"1":"Briards","2":"2018","3":"132"},{"1":"Briards","2":"2019","3":"137"},{"1":"Briards","2":"2020","3":"150"},{"1":"Berger Picards","2":"2013","3":"NA"},{"1":"Berger Picards","2":"2014","3":"NA"},{"1":"Berger Picards","2":"2015","3":"NA"},{"1":"Berger Picards","2":"2016","3":"NA"},{"1":"Berger Picards","2":"2017","3":"NA"},{"1":"Berger Picards","2":"2018","3":"NA"},{"1":"Berger Picards","2":"2019","3":"NA"},{"1":"Berger Picards","2":"2020","3":"151"},{"1":"Entlebucher Mountain Dogs","2":"2013","3":"155"},{"1":"Entlebucher Mountain Dogs","2":"2014","3":"153"},{"1":"Entlebucher Mountain Dogs","2":"2015","3":"159"},{"1":"Entlebucher Mountain Dogs","2":"2016","3":"161"},{"1":"Entlebucher Mountain Dogs","2":"2017","3":"152"},{"1":"Entlebucher Mountain Dogs","2":"2018","3":"157"},{"1":"Entlebucher Mountain Dogs","2":"2019","3":"169"},{"1":"Entlebucher Mountain Dogs","2":"2020","3":"152"},{"1":"Treeing Walker Coonhounds","2":"2013","3":"101"},{"1":"Treeing Walker Coonhounds","2":"2014","3":"112"},{"1":"Treeing Walker Coonhounds","2":"2015","3":"117"},{"1":"Treeing Walker Coonhounds","2":"2016","3":"137"},{"1":"Treeing Walker Coonhounds","2":"2017","3":"131"},{"1":"Treeing Walker Coonhounds","2":"2018","3":"137"},{"1":"Treeing Walker Coonhounds","2":"2019","3":"131"},{"1":"Treeing Walker Coonhounds","2":"2020","3":"153"},{"1":"Icelandic Sheepdogs","2":"2013","3":"142"},{"1":"Icelandic Sheepdogs","2":"2014","3":"146"},{"1":"Icelandic Sheepdogs","2":"2015","3":"140"},{"1":"Icelandic Sheepdogs","2":"2016","3":"153"},{"1":"Icelandic Sheepdogs","2":"2017","3":"144"},{"1":"Icelandic Sheepdogs","2":"2018","3":"155"},{"1":"Icelandic Sheepdogs","2":"2019","3":"134"},{"1":"Icelandic Sheepdogs","2":"2020","3":"154"},{"1":"Wirehaired Vizslas","2":"2013","3":"NA"},{"1":"Wirehaired Vizslas","2":"2014","3":"NA"},{"1":"Wirehaired Vizslas","2":"2015","3":"NA"},{"1":"Wirehaired Vizslas","2":"2016","3":"NA"},{"1":"Wirehaired Vizslas","2":"2017","3":"NA"},{"1":"Wirehaired Vizslas","2":"2018","3":"NA"},{"1":"Wirehaired Vizslas","2":"2019","3":"NA"},{"1":"Wirehaired Vizslas","2":"2020","3":"155"},{"1":"Pumik","2":"2013","3":"NA"},{"1":"Pumik","2":"2014","3":"NA"},{"1":"Pumik","2":"2015","3":"NA"},{"1":"Pumik","2":"2016","3":"NA"},{"1":"Pumik","2":"2017","3":"NA"},{"1":"Pumik","2":"2018","3":"NA"},{"1":"Pumik","2":"2019","3":"NA"},{"1":"Pumik","2":"2020","3":"156"},{"1":"Portuguese Podengo Pequenos","2":"2013","3":"153"},{"1":"Portuguese Podengo Pequenos","2":"2014","3":"166"},{"1":"Portuguese Podengo Pequenos","2":"2015","3":"153"},{"1":"Portuguese Podengo Pequenos","2":"2016","3":"160"},{"1":"Portuguese Podengo Pequenos","2":"2017","3":"159"},{"1":"Portuguese Podengo Pequenos","2":"2018","3":"154"},{"1":"Portuguese Podengo Pequenos","2":"2019","3":"149"},{"1":"Portuguese Podengo Pequenos","2":"2020","3":"157"},{"1":"Spaniels (American Water)","2":"2013","3":"144"},{"1":"Spaniels (American Water)","2":"2014","3":"160"},{"1":"Spaniels (American Water)","2":"2015","3":"157"},{"1":"Spaniels (American Water)","2":"2016","3":"156"},{"1":"Spaniels (American Water)","2":"2017","3":"167"},{"1":"Spaniels (American Water)","2":"2018","3":"166"},{"1":"Spaniels (American Water)","2":"2019","3":"166"},{"1":"Spaniels (American Water)","2":"2020","3":"158"},{"1":"Retrievers (Curly-Coated)","2":"2013","3":"163"},{"1":"Retrievers (Curly-Coated)","2":"2014","3":"163"},{"1":"Retrievers (Curly-Coated)","2":"2015","3":"151"},{"1":"Retrievers (Curly-Coated)","2":"2016","3":"164"},{"1":"Retrievers (Curly-Coated)","2":"2017","3":"171"},{"1":"Retrievers (Curly-Coated)","2":"2018","3":"162"},{"1":"Retrievers (Curly-Coated)","2":"2019","3":"165"},{"1":"Retrievers (Curly-Coated)","2":"2020","3":"159"},{"1":"Spaniels (Field)","2":"2013","3":"140"},{"1":"Spaniels (Field)","2":"2014","3":"137"},{"1":"Spaniels (Field)","2":"2015","3":"145"},{"1":"Spaniels (Field)","2":"2016","3":"147"},{"1":"Spaniels (Field)","2":"2017","3":"145"},{"1":"Spaniels (Field)","2":"2018","3":"149"},{"1":"Spaniels (Field)","2":"2019","3":"155"},{"1":"Spaniels (Field)","2":"2020","3":"160"},{"1":"Lowchen","2":"2013","3":"154"},{"1":"Lowchen","2":"2014","3":"165"},{"1":"Lowchen","2":"2015","3":"161"},{"1":"Lowchen","2":"2016","3":"171"},{"1":"Lowchen","2":"2017","3":"169"},{"1":"Lowchen","2":"2018","3":"168"},{"1":"Lowchen","2":"2019","3":"160"},{"1":"Lowchen","2":"2020","3":"161"},{"1":"Nederlandse Kooikerhondjes","2":"2013","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2014","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2015","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2016","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2017","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2018","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2019","3":"NA"},{"1":"Nederlandse Kooikerhondjes","2":"2020","3":"162"},{"1":"Affenpinschers","2":"2013","3":"143"},{"1":"Affenpinschers","2":"2014","3":"144"},{"1":"Affenpinschers","2":"2015","3":"136"},{"1":"Affenpinschers","2":"2016","3":"149"},{"1":"Affenpinschers","2":"2017","3":"147"},{"1":"Affenpinschers","2":"2018","3":"148"},{"1":"Affenpinschers","2":"2019","3":"152"},{"1":"Affenpinschers","2":"2020","3":"163"},{"1":"Petits Bassets Griffons Vendeens","2":"2013","3":"138"},{"1":"Petits Bassets Griffons Vendeens","2":"2014","3":"150"},{"1":"Petits Bassets Griffons Vendeens","2":"2015","3":"143"},{"1":"Petits Bassets Griffons Vendeens","2":"2016","3":"148"},{"1":"Petits Bassets Griffons Vendeens","2":"2017","3":"160"},{"1":"Petits Bassets Griffons Vendeens","2":"2018","3":"156"},{"1":"Petits Bassets Griffons Vendeens","2":"2019","3":"154"},{"1":"Petits Bassets Griffons Vendeens","2":"2020","3":"164"},{"1":"Finnish Lapphunds","2":"2013","3":"171"},{"1":"Finnish Lapphunds","2":"2014","3":"173"},{"1":"Finnish Lapphunds","2":"2015","3":"169"},{"1":"Finnish Lapphunds","2":"2016","3":"173"},{"1":"Finnish Lapphunds","2":"2017","3":"173"},{"1":"Finnish Lapphunds","2":"2018","3":"161"},{"1":"Finnish Lapphunds","2":"2019","3":"167"},{"1":"Finnish Lapphunds","2":"2020","3":"165"},{"1":"Scottish Deerhounds","2":"2013","3":"165"},{"1":"Scottish Deerhounds","2":"2014","3":"157"},{"1":"Scottish Deerhounds","2":"2015","3":"152"},{"1":"Scottish Deerhounds","2":"2016","3":"154"},{"1":"Scottish Deerhounds","2":"2017","3":"168"},{"1":"Scottish Deerhounds","2":"2018","3":"158"},{"1":"Scottish Deerhounds","2":"2019","3":"151"},{"1":"Scottish Deerhounds","2":"2020","3":"166"},{"1":"Plott Hounds","2":"2013","3":"NA"},{"1":"Plott Hounds","2":"2014","3":"NA"},{"1":"Plott Hounds","2":"2015","3":"NA"},{"1":"Plott Hounds","2":"2016","3":"NA"},{"1":"Plott Hounds","2":"2017","3":"NA"},{"1":"Plott Hounds","2":"2018","3":"NA"},{"1":"Plott Hounds","2":"2019","3":"NA"},{"1":"Plott Hounds","2":"2020","3":"167"},{"1":"Norwegian Buhunds","2":"2013","3":"166"},{"1":"Norwegian Buhunds","2":"2014","3":"172"},{"1":"Norwegian Buhunds","2":"2015","3":"165"},{"1":"Norwegian Buhunds","2":"2016","3":"174"},{"1":"Norwegian Buhunds","2":"2017","3":"170"},{"1":"Norwegian Buhunds","2":"2018","3":"165"},{"1":"Norwegian Buhunds","2":"2019","3":"171"},{"1":"Norwegian Buhunds","2":"2020","3":"168"},{"1":"Glen of Imaal Terriers","2":"2013","3":"167"},{"1":"Glen of Imaal Terriers","2":"2014","3":"162"},{"1":"Glen of Imaal Terriers","2":"2015","3":"168"},{"1":"Glen of Imaal Terriers","2":"2016","3":"180"},{"1":"Glen of Imaal Terriers","2":"2017","3":"158"},{"1":"Glen of Imaal Terriers","2":"2018","3":"174"},{"1":"Glen of Imaal Terriers","2":"2019","3":"170"},{"1":"Glen of Imaal Terriers","2":"2020","3":"169"},{"1":"Setters (Irish Red and White)","2":"2013","3":"145"},{"1":"Setters (Irish Red and White)","2":"2014","3":"155"},{"1":"Setters (Irish Red and White)","2":"2015","3":"148"},{"1":"Setters (Irish Red and White)","2":"2016","3":"142"},{"1":"Setters (Irish Red and White)","2":"2017","3":"154"},{"1":"Setters (Irish Red and White)","2":"2018","3":"146"},{"1":"Setters (Irish Red and White)","2":"2019","3":"157"},{"1":"Setters (Irish Red and White)","2":"2020","3":"170"},{"1":"Ibizan Hounds","2":"2013","3":"151"},{"1":"Ibizan Hounds","2":"2014","3":"159"},{"1":"Ibizan Hounds","2":"2015","3":"160"},{"1":"Ibizan Hounds","2":"2016","3":"152"},{"1":"Ibizan Hounds","2":"2017","3":"165"},{"1":"Ibizan Hounds","2":"2018","3":"152"},{"1":"Ibizan Hounds","2":"2019","3":"173"},{"1":"Ibizan Hounds","2":"2020","3":"171"},{"1":"Spaniels (Sussex)","2":"2013","3":"162"},{"1":"Spaniels (Sussex)","2":"2014","3":"175"},{"1":"Spaniels (Sussex)","2":"2015","3":"173"},{"1":"Spaniels (Sussex)","2":"2016","3":"172"},{"1":"Spaniels (Sussex)","2":"2017","3":"161"},{"1":"Spaniels (Sussex)","2":"2018","3":"180"},{"1":"Spaniels (Sussex)","2":"2019","3":"178"},{"1":"Spaniels (Sussex)","2":"2020","3":"172"},{"1":"Bergamasco Sheepdogs","2":"2013","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2014","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2015","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2016","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2017","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2018","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2019","3":"NA"},{"1":"Bergamasco Sheepdogs","2":"2020","3":"173"},{"1":"Spaniels (Irish Water)","2":"2013","3":"141"},{"1":"Spaniels (Irish Water)","2":"2014","3":"152"},{"1":"Spaniels (Irish Water)","2":"2015","3":"144"},{"1":"Spaniels (Irish Water)","2":"2016","3":"150"},{"1":"Spaniels (Irish Water)","2":"2017","3":"164"},{"1":"Spaniels (Irish Water)","2":"2018","3":"159"},{"1":"Spaniels (Irish Water)","2":"2019","3":"172"},{"1":"Spaniels (Irish Water)","2":"2020","3":"174"},{"1":"Polish Lowland Sheepdogs","2":"2013","3":"157"},{"1":"Polish Lowland Sheepdogs","2":"2014","3":"170"},{"1":"Polish Lowland Sheepdogs","2":"2015","3":"164"},{"1":"Polish Lowland Sheepdogs","2":"2016","3":"169"},{"1":"Polish Lowland Sheepdogs","2":"2017","3":"176"},{"1":"Polish Lowland Sheepdogs","2":"2018","3":"170"},{"1":"Polish Lowland Sheepdogs","2":"2019","3":"175"},{"1":"Polish Lowland Sheepdogs","2":"2020","3":"175"},{"1":"Otterhounds","2":"2013","3":"172"},{"1":"Otterhounds","2":"2014","3":"179"},{"1":"Otterhounds","2":"2015","3":"180"},{"1":"Otterhounds","2":"2016","3":"166"},{"1":"Otterhounds","2":"2017","3":"186"},{"1":"Otterhounds","2":"2018","3":"182"},{"1":"Otterhounds","2":"2019","3":"174"},{"1":"Otterhounds","2":"2020","3":"176"},{"1":"Kuvaszok","2":"2013","3":"150"},{"1":"Kuvaszok","2":"2014","3":"161"},{"1":"Kuvaszok","2":"2015","3":"150"},{"1":"Kuvaszok","2":"2016","3":"165"},{"1":"Kuvaszok","2":"2017","3":"157"},{"1":"Kuvaszok","2":"2018","3":"163"},{"1":"Kuvaszok","2":"2019","3":"168"},{"1":"Kuvaszok","2":"2020","3":"177"},{"1":"Komondorok","2":"2013","3":"159"},{"1":"Komondorok","2":"2014","3":"168"},{"1":"Komondorok","2":"2015","3":"163"},{"1":"Komondorok","2":"2016","3":"177"},{"1":"Komondorok","2":"2017","3":"163"},{"1":"Komondorok","2":"2018","3":"173"},{"1":"Komondorok","2":"2019","3":"183"},{"1":"Komondorok","2":"2020","3":"178"},{"1":"Cirnechi dell Etna","2":"2013","3":"NA"},{"1":"Cirnechi dell Etna","2":"2014","3":"NA"},{"1":"Cirnechi dell Etna","2":"2015","3":"NA"},{"1":"Cirnechi dell Etna","2":"2016","3":"NA"},{"1":"Cirnechi dell Etna","2":"2017","3":"NA"},{"1":"Cirnechi dell Etna","2":"2018","3":"NA"},{"1":"Cirnechi dell Etna","2":"2019","3":"NA"},{"1":"Cirnechi dell Etna","2":"2020","3":"179"},{"1":"Pharaoh Hounds","2":"2013","3":"160"},{"1":"Pharaoh Hounds","2":"2014","3":"164"},{"1":"Pharaoh Hounds","2":"2015","3":"171"},{"1":"Pharaoh Hounds","2":"2016","3":"168"},{"1":"Pharaoh Hounds","2":"2017","3":"174"},{"1":"Pharaoh Hounds","2":"2018","3":"172"},{"1":"Pharaoh Hounds","2":"2019","3":"163"},{"1":"Pharaoh Hounds","2":"2020","3":"180"},{"1":"Dandie Dinmont Terriers","2":"2013","3":"168"},{"1":"Dandie Dinmont Terriers","2":"2014","3":"167"},{"1":"Dandie Dinmont Terriers","2":"2015","3":"172"},{"1":"Dandie Dinmont Terriers","2":"2016","3":"167"},{"1":"Dandie Dinmont Terriers","2":"2017","3":"182"},{"1":"Dandie Dinmont Terriers","2":"2018","3":"176"},{"1":"Dandie Dinmont Terriers","2":"2019","3":"176"},{"1":"Dandie Dinmont Terriers","2":"2020","3":"181"},{"1":"Pyrenean Shepherds","2":"2013","3":"169"},{"1":"Pyrenean Shepherds","2":"2014","3":"178"},{"1":"Pyrenean Shepherds","2":"2015","3":"176"},{"1":"Pyrenean Shepherds","2":"2016","3":"176"},{"1":"Pyrenean Shepherds","2":"2017","3":"180"},{"1":"Pyrenean Shepherds","2":"2018","3":"181"},{"1":"Pyrenean Shepherds","2":"2019","3":"184"},{"1":"Pyrenean Shepherds","2":"2020","3":"182"},{"1":"Skye Terriers","2":"2013","3":"161"},{"1":"Skye Terriers","2":"2014","3":"177"},{"1":"Skye Terriers","2":"2015","3":"175"},{"1":"Skye Terriers","2":"2016","3":"178"},{"1":"Skye Terriers","2":"2017","3":"172"},{"1":"Skye Terriers","2":"2018","3":"178"},{"1":"Skye Terriers","2":"2019","3":"189"},{"1":"Skye Terriers","2":"2020","3":"183"},{"1":"Canaan Dogs","2":"2013","3":"164"},{"1":"Canaan Dogs","2":"2014","3":"176"},{"1":"Canaan Dogs","2":"2015","3":"170"},{"1":"Canaan Dogs","2":"2016","3":"181"},{"1":"Canaan Dogs","2":"2017","3":"175"},{"1":"Canaan Dogs","2":"2018","3":"179"},{"1":"Canaan Dogs","2":"2019","3":"181"},{"1":"Canaan Dogs","2":"2020","3":"184"},{"1":"American English Coonhounds","2":"2013","3":"146"},{"1":"American English Coonhounds","2":"2014","3":"156"},{"1":"American English Coonhounds","2":"2015","3":"166"},{"1":"American English Coonhounds","2":"2016","3":"170"},{"1":"American English Coonhounds","2":"2017","3":"178"},{"1":"American English Coonhounds","2":"2018","3":"175"},{"1":"American English Coonhounds","2":"2019","3":"177"},{"1":"American English Coonhounds","2":"2020","3":"185"},{"1":"Chinooks","2":"2013","3":"156"},{"1":"Chinooks","2":"2014","3":"171"},{"1":"Chinooks","2":"2015","3":"177"},{"1":"Chinooks","2":"2016","3":"175"},{"1":"Chinooks","2":"2017","3":"179"},{"1":"Chinooks","2":"2018","3":"190"},{"1":"Chinooks","2":"2019","3":"186"},{"1":"Chinooks","2":"2020","3":"186"},{"1":"Finnish Spitz","2":"2013","3":"170"},{"1":"Finnish Spitz","2":"2014","3":"174"},{"1":"Finnish Spitz","2":"2015","3":"178"},{"1":"Finnish Spitz","2":"2016","3":"179"},{"1":"Finnish Spitz","2":"2017","3":"177"},{"1":"Finnish Spitz","2":"2018","3":"184"},{"1":"Finnish Spitz","2":"2019","3":"185"},{"1":"Finnish Spitz","2":"2020","3":"187"},{"1":"Grand Basset Griffon Vendeens","2":"2013","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2014","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2015","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2016","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2017","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2018","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2019","3":"NA"},{"1":"Grand Basset Griffon Vendeens","2":"2020","3":"188"},{"1":"Sloughis","2":"2013","3":"NA"},{"1":"Sloughis","2":"2014","3":"NA"},{"1":"Sloughis","2":"2015","3":"NA"},{"1":"Sloughis","2":"2016","3":"NA"},{"1":"Sloughis","2":"2017","3":"NA"},{"1":"Sloughis","2":"2018","3":"NA"},{"1":"Sloughis","2":"2019","3":"NA"},{"1":"Sloughis","2":"2020","3":"189"},{"1":"Harriers","2":"2013","3":"173"},{"1":"Harriers","2":"2014","3":"181"},{"1":"Harriers","2":"2015","3":"183"},{"1":"Harriers","2":"2016","3":"186"},{"1":"Harriers","2":"2017","3":"183"},{"1":"Harriers","2":"2018","3":"189"},{"1":"Harriers","2":"2019","3":"188"},{"1":"Harriers","2":"2020","3":"190"},{"1":"Cesky Terriers","2":"2013","3":"174"},{"1":"Cesky Terriers","2":"2014","3":"182"},{"1":"Cesky Terriers","2":"2015","3":"179"},{"1":"Cesky Terriers","2":"2016","3":"182"},{"1":"Cesky Terriers","2":"2017","3":"185"},{"1":"Cesky Terriers","2":"2018","3":"185"},{"1":"Cesky Terriers","2":"2019","3":"190"},{"1":"Cesky Terriers","2":"2020","3":"191"},{"1":"American Foxhounds","2":"2013","3":"176"},{"1":"American Foxhounds","2":"2014","3":"180"},{"1":"American Foxhounds","2":"2015","3":"181"},{"1":"American Foxhounds","2":"2016","3":"189"},{"1":"American Foxhounds","2":"2017","3":"187"},{"1":"American Foxhounds","2":"2018","3":"186"},{"1":"American Foxhounds","2":"2019","3":"191"},{"1":"American Foxhounds","2":"2020","3":"192"},{"1":"Azawakhs","2":"2013","3":"NA"},{"1":"Azawakhs","2":"2014","3":"NA"},{"1":"Azawakhs","2":"2015","3":"NA"},{"1":"Azawakhs","2":"2016","3":"NA"},{"1":"Azawakhs","2":"2017","3":"NA"},{"1":"Azawakhs","2":"2018","3":"NA"},{"1":"Azawakhs","2":"2019","3":"NA"},{"1":"Azawakhs","2":"2020","3":"193"},{"1":"English Foxhounds","2":"2013","3":"177"},{"1":"English Foxhounds","2":"2014","3":"183"},{"1":"English Foxhounds","2":"2015","3":"184"},{"1":"English Foxhounds","2":"2016","3":"187"},{"1":"English Foxhounds","2":"2017","3":"189"},{"1":"English Foxhounds","2":"2018","3":"188"},{"1":"English Foxhounds","2":"2019","3":"195"},{"1":"English Foxhounds","2":"2020","3":"194"},{"1":"Norwegian Lundehunds","2":"2013","3":"175"},{"1":"Norwegian Lundehunds","2":"2014","3":"184"},{"1":"Norwegian Lundehunds","2":"2015","3":"182"},{"1":"Norwegian Lundehunds","2":"2016","3":"188"},{"1":"Norwegian Lundehunds","2":"2017","3":"190"},{"1":"Norwegian Lundehunds","2":"2018","3":"191"},{"1":"Norwegian Lundehunds","2":"2019","3":"194"},{"1":"Norwegian Lundehunds","2":"2020","3":"195"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

```r
breed_rank_all %>%
  left_join(breed_traits_total, 
            by = "Breed") %>%
  arrange(desc(total_rating)) %>%
  select(Breed, `2013 Rank`, `2014 Rank`, `2015 Rank`, `2016 Rank`, `2017 Rank`, `2018 Rank`, `2019 Rank`, `2020 Rank`, total_rating) %>%
  head(20) %>%#top 20 dogs in total ratings 
  pivot_longer(col = starts_with("20"), 
               names_to = "Year", 
               values_to = "Rank") %>%
  separate(Year, 
           into = c("Year", "words"), 
           remove = FALSE, 
           convert = TRUE) %>%
  select(Breed, total_rating, Rank, Year) %>%
  mutate(Rank = as.integer(Rank)) %>%
  group_by(Breed) %>%
  mutate(median_rank = median((Rank))) %>%  #finds median Rank by Breed
  arrange(-desc(median_rank)) %>% #ordered with best median Rank (1) at the top.
  ggplot(aes(x = Year, 
             y = fct_reorder(Breed, -median_rank), 
             color = Rank)) +
  geom_point() +
  geom_line() +
  labs(title = "Rankings of Dog Breeds by Year", 
       x = "Year", 
       y = "Dog Breeds") +
  theme(plot.background = element_rect(fill = "snow1"),
        text = element_text(family = "Times"))
```

![](03_exercises_files/figure-html/unnamed-chunk-19-1.png)<!-- -->
One thing that would make this graph better would be if Miniature American Shepherds, which have NA values for every year except 2020, was removed because of their NA values because otherwise that breed occurs at the top of the ranking list, showing somewhat misleading results. 

  19. Create your own! Requirements: use a `join` or `pivot` function (or both, if you'd like), a `str_XXX()` function, and a `fct_XXX()` function to create a graph using any of the dog datasets. One suggestion is to try to improve the graph you created for the Tidy Tuesday assignment. If you want an extra challenge, find a way to use the dog images in the `breed_rank_all` file - check out the `ggimage` library and [this resource](https://wilkelab.org/ggtext/) for putting images as labels.
  

```r
top_10 <- breed_rank_all %>% 
  slice(1:10) %>%
  select(Breed, `2016 Rank`:`2020 Rank`) 
  
new_top_10 <- top_10 %>% 
  pivot_longer(ends_with("Rank"), #using pivot_longer to make dataset longer. Reduces columns (turns years into individual row values). Years as variables/columns turns into values of year variable. 
               names_to = "year", 
               values_to = "rank") %>% 
  mutate(year = str_remove(year, " Rank")) %>% #Str function removes Rank from year values. 
  mutate(year = as.numeric(year)) %>% #turns string for year into numeric data. 
  group_by(Breed) 

new_top_10
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["Breed"],"name":[1],"type":["chr"],"align":["left"]},{"label":["year"],"name":[2],"type":["dbl"],"align":["right"]},{"label":["rank"],"name":[3],"type":["dbl"],"align":["right"]}],"data":[{"1":"Retrievers (Labrador)","2":"2016","3":"1"},{"1":"Retrievers (Labrador)","2":"2017","3":"1"},{"1":"Retrievers (Labrador)","2":"2018","3":"1"},{"1":"Retrievers (Labrador)","2":"2019","3":"1"},{"1":"Retrievers (Labrador)","2":"2020","3":"1"},{"1":"French Bulldogs","2":"2016","3":"6"},{"1":"French Bulldogs","2":"2017","3":"4"},{"1":"French Bulldogs","2":"2018","3":"4"},{"1":"French Bulldogs","2":"2019","3":"4"},{"1":"French Bulldogs","2":"2020","3":"2"},{"1":"German Shepherd Dogs","2":"2016","3":"2"},{"1":"German Shepherd Dogs","2":"2017","3":"2"},{"1":"German Shepherd Dogs","2":"2018","3":"2"},{"1":"German Shepherd Dogs","2":"2019","3":"2"},{"1":"German Shepherd Dogs","2":"2020","3":"3"},{"1":"Retrievers (Golden)","2":"2016","3":"3"},{"1":"Retrievers (Golden)","2":"2017","3":"3"},{"1":"Retrievers (Golden)","2":"2018","3":"3"},{"1":"Retrievers (Golden)","2":"2019","3":"3"},{"1":"Retrievers (Golden)","2":"2020","3":"4"},{"1":"Bulldogs","2":"2016","3":"4"},{"1":"Bulldogs","2":"2017","3":"5"},{"1":"Bulldogs","2":"2018","3":"5"},{"1":"Bulldogs","2":"2019","3":"5"},{"1":"Bulldogs","2":"2020","3":"5"},{"1":"Poodles","2":"2016","3":"7"},{"1":"Poodles","2":"2017","3":"7"},{"1":"Poodles","2":"2018","3":"7"},{"1":"Poodles","2":"2019","3":"6"},{"1":"Poodles","2":"2020","3":"6"},{"1":"Beagles","2":"2016","3":"5"},{"1":"Beagles","2":"2017","3":"6"},{"1":"Beagles","2":"2018","3":"6"},{"1":"Beagles","2":"2019","3":"7"},{"1":"Beagles","2":"2020","3":"7"},{"1":"Rottweilers","2":"2016","3":"8"},{"1":"Rottweilers","2":"2017","3":"8"},{"1":"Rottweilers","2":"2018","3":"8"},{"1":"Rottweilers","2":"2019","3":"8"},{"1":"Rottweilers","2":"2020","3":"8"},{"1":"Pointers (German Shorthaired)","2":"2016","3":"11"},{"1":"Pointers (German Shorthaired)","2":"2017","3":"10"},{"1":"Pointers (German Shorthaired)","2":"2018","3":"9"},{"1":"Pointers (German Shorthaired)","2":"2019","3":"9"},{"1":"Pointers (German Shorthaired)","2":"2020","3":"9"},{"1":"Dachshunds","2":"2016","3":"13"},{"1":"Dachshunds","2":"2017","3":"13"},{"1":"Dachshunds","2":"2018","3":"12"},{"1":"Dachshunds","2":"2019","3":"11"},{"1":"Dachshunds","2":"2020","3":"10"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

```r
ggplot(new_top_10, aes(x = year, y = rank, group = Breed)) +
  geom_line(aes(color = fct_reorder2(Breed, year, rank), alpha = 1), size = 2) +
  geom_point(aes(color = fct_reorder2(Breed, year, rank), alpha = 1), size = 4) +
  #scale_y_continuous(breaks = 1:nrow(new_top_10)) +
  scale_y_reverse(breaks = 1:nrow(new_top_10)) + #above reverses order on graph, but still in wrong order on legend...
  geom_label(data = new_top_10 %>% filter(year == "2020"), aes(label = Breed, x = 2019.5), size = 2, hjust = 0.5, fontface = "bold") +
  theme(legend.position = "none") +
  labs(title = "Current Top 10 Dog Breeds Since 2016", 
       x = "Year", 
       y = "Ranking", 
       color = "Breeds")
```

![](03_exercises_files/figure-html/unnamed-chunk-20-1.png)<!-- -->

```r
#Would've liked the labels to be on the right, but when I tried that they were cut off. 
```

```r
# #Draft
# top_10 <- breed_rank_all %>% 
#   slice(1:10) %>%
#   select(Breed, `2016 Rank`:`2020 Rank`) 
#   
# new_top_10 <- top_10 %>% 
#   pivot_longer(ends_with("Rank"), #using pivot_longer to make dataset longer. Reduces columns (turns years into individual row values). Years as variables/columns turns into values of year variable. 
#                names_to = "year", 
#                values_to = "rank") %>% 
#   mutate(year = str_remove(year, " Rank") %>% #Str function removes Rank from year values. 
#   as.numeric()) %>% #turns string for year into numeric data. 
#   group_by(Breed) %>%
#   arrange(rank)
# 
# new_top_10 
# 
# 
# new_top_10 %>%
#   ggplot(aes(x = year, y = rank, color = fct_reorder2(Breed, year, rank))) +
#   geom_line() +
#   geom_point() +
#   #scale_y_continuous(breaks = 1:nrow(new_top_10)) +
#   scale_y_reverse(breaks = 1:nrow(new_top_10)) + #above reverses order on graph, but still in wrong order on legend...
#   #scale_fill_continuous(trans = 'reverse') +
#   #scale_fill_brewer(guide = guide_legend(reverse = TRUE)) +
#   #guides(color = guide_colorbar(reverse = TRUE)) +
#   labs(title = "Current Top 10 Dog Breeds Since 2016", 
#        x = "Year", 
#        y = "Ranking", 
#        color = "Breeds")
# #I reordered the breeds in the color legend using fct reorder2, but then reversed the scale to put the 1 ranking at the top, but it doesn't change the order of the legend colors, causing them to still be in reverse order.
```

## GitHub link

  20. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 03_exercises.Rmd, provide a link to the 03_exercises.md file, which is the one that will be most readable on GitHub.

[Link to Exercise 3](https://github.com/aleidner6/AnnaLeidner_test_repo/blob/main/03_exercises.md).

## Challenge problem! 

This problem uses the data from the Tidy Tuesday competition this week, `kids`. If you need to refresh your memory on the data, read about it [here](https://github.com/rfordatascience/tidytuesday/blob/master/data/2020/2020-09-15/readme.md). 

  21. In this exercise, you are going to try to replicate the graph below, created by Georgios Karamanis. I'm sure you can find the exact code on GitHub somewhere, but **DON'T DO THAT!** You will only be graded for putting an effort into this problem. So, give it a try and see how far you can get without doing too much googling. HINT: use `facet_geo()`. The graphic won't load below since it came from a location on my computer. So, you'll have to reference the original html on the moodle page to see it.
  

**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
