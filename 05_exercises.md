---
title: 'Weekly Exercises #5'
author: "Will Orser"
output: 
  html_document:
    keep_md: TRUE
    toc: TRUE
    toc_float: TRUE
    df_print: paged
    code_download: true
---





```r
library(tidyverse)     # for data cleaning and plotting
library(googlesheets4) # for reading googlesheet data
library(lubridate)     # for date manipulation
library(openintro)     # for the abbr2state() function
library(palmerpenguins)# for Palmer penguin data
library(maps)          # for map data
library(ggmap)         # for mapping points on maps
library(gplots)        # for col2hex() function
library(RColorBrewer)  # for color palettes
library(sf)            # for working with spatial data
library(leaflet)       # for highly customizable mapping
library(ggthemes)      # for more themes (including theme_map())
library(plotly)        # for the ggplotly() - basic interactivity
library(gganimate)     # for adding animation layers to ggplots
library(transformr)    # for "tweening" (gganimate)
library(shiny)         # for creating interactive apps
library(ggimage)
gs4_deauth()           # To not have to authorize each time you knit.
theme_set(theme_minimal())
```


```r
# SNCF Train data
small_trains <- read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2019/2019-02-26/small_trains.csv") 

# Lisa's garden data
garden_harvest <- read_sheet("https://docs.google.com/spreadsheets/d/1DekSazCzKqPS2jnGhKue7tLxRU3GVL1oxi-4bEM5IWw/edit?usp=sharing") %>% 
  mutate(date = ymd(date))

# Lisa's Mallorca cycling data
mallorca_bike_day7 <- read_csv("https://www.dropbox.com/s/zc6jan4ltmjtvy0/mallorca_bike_day7.csv?dl=1") %>% 
  select(1:4, speed)

# Heather Lendway's Ironman 70.3 Pan Am championships Panama data
panama_swim <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_swim_20160131.csv")

panama_bike <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_bike_20160131.csv")

panama_run <- read_csv("https://raw.githubusercontent.com/llendway/gps-data/master/data/panama_run_20160131.csv")

#COVID-19 data from the New York Times
covid19 <- read_csv("https://raw.githubusercontent.com/nytimes/covid-19-data/master/us-states.csv")
```

## Put your homework on GitHub!

Go [here](https://github.com/llendway/github_for_collaboration/blob/master/github_for_collaboration.md) or to previous homework to remind yourself how to get set up. 

Once your repository is created, you should always open your **project** rather than just opening an .Rmd file. You can do that by either clicking on the .Rproj file in your repository folder on your computer. Or, by going to the upper right hand corner in R Studio and clicking the arrow next to where it says Project: (None). You should see your project come up in that list if you've used it recently. You could also go to File --> Open Project and navigate to your .Rproj file. 

## Instructions

* Put your name at the top of the document. 

* **For ALL graphs, you should include appropriate labels.** 

* Feel free to change the default theme, which I currently have set to `theme_minimal()`. 

* Use good coding practice. Read the short sections on good code with [pipes](https://style.tidyverse.org/pipes.html) and [ggplot2](https://style.tidyverse.org/ggplot2.html). **This is part of your grade!**

* **NEW!!** With animated graphs, add `eval=FALSE` to the code chunk that creates the animation and saves it using `anim_save()`. Add another code chunk to reread the gif back into the file. See the [tutorial](https://animation-and-interactivity-in-r.netlify.app/) for help. 

* When you are finished with ALL the exercises, uncomment the options at the top so your document looks nicer. Don't do it before then, or else you might miss some important warnings and messages.

## Warm-up exercises from tutorial

  1. Choose 2 graphs you have created for ANY assignment in this class and add interactivity using the `ggplotly()` function.
  

```r
perfect_garden_graph_v3 <- garden_harvest %>%
  #Filter dataset to only include tomatoes
  filter(vegetable == "tomatoes") %>% 
  #Create a new variable that gives weight in pounds
  mutate(weight_in_lbs = weight/453.592) %>%
  #Calculate the total weight harvested for each date in pounds (in case a variety was harvested multiple times on the same day) 
  group_by(variety, date) %>% 
  summarize(total_days_weight_lbs = sum(weight_in_lbs)) %>% 
  #Pipe into ggplot and create a point graph. Facet by variety of tomato. Reorder the varieties so that varieties are graphed in order of mean(total_days_weight_lbs). Add color, labels, and a theme. This week, I changed the theme to "linedraw", which makes the data points easier to measure visually. Add in connecting lines to more clearly show the variations in weight over time.
  ggplot(aes(x = date, y = total_days_weight_lbs)) +
  geom_point(color = "red") + 
  geom_line() +
  facet_wrap(~fct_reorder(variety, total_days_weight_lbs, mean)) + 
  labs(title = "The Weight of Tomato Varieties Harvested Over Time", x = "Date", y = "Weight (lbs)") +
  theme_linedraw()

ggplotly(perfect_garden_graph_v3)
```

<!--html_preserve--><div id="htmlwidget-69505d562061f0ef8b10" style="width:960px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-69505d562061f0ef8b10">{"x":{"data":[{"x":[18454,18464,18467,18468,18471,18472,18473,18474,18475,18476,18478,18479,18481,18482,18483,18485,18487,18488,18490,18491,18492,18493,18494,18495,18497,18498,18499,18500,18503,18504,18508,18509,18511,18515,18520,18524,18530],"y":[0.0529109860844107,0.189597700135805,0.0683433570256971,0.23369018853948,0.288805799044075,0.0881849768073511,0.200620822236724,0.220462442018378,0.370376902590875,0.224871690858745,0.260145681581686,0.47840349917988,0.643750330693663,0.178574578034886,0.141095962891762,0.665796574895501,0.92814688089737,0.277782676943156,1.05160584842766,0.961216247200127,0.299828921144994,0.994285613502884,1.0868798391506,0.584225471348701,0.961216247200127,0.165346831513783,1.11553995661299,1.80558740013051,0.837757279669835,1.83645214201309,2.49343021922785,0.97444399372123,1.35584401841302,1.12215382987354,0.568793100407415,2.33249263655444,1.80558740013051],"text":["date: 2020-07-11<br />total_days_weight_lbs: 0.05291099","date: 2020-07-21<br />total_days_weight_lbs: 0.18959770","date: 2020-07-24<br />total_days_weight_lbs: 0.06834336","date: 2020-07-25<br />total_days_weight_lbs: 0.23369019","date: 2020-07-28<br />total_days_weight_lbs: 0.28880580","date: 2020-07-29<br />total_days_weight_lbs: 0.08818498","date: 2020-07-30<br />total_days_weight_lbs: 0.20062082","date: 2020-07-31<br />total_days_weight_lbs: 0.22046244","date: 2020-08-01<br />total_days_weight_lbs: 0.37037690","date: 2020-08-02<br />total_days_weight_lbs: 0.22487169","date: 2020-08-04<br />total_days_weight_lbs: 0.26014568","date: 2020-08-05<br />total_days_weight_lbs: 0.47840350","date: 2020-08-07<br />total_days_weight_lbs: 0.64375033","date: 2020-08-08<br />total_days_weight_lbs: 0.17857458","date: 2020-08-09<br />total_days_weight_lbs: 0.14109596","date: 2020-08-11<br />total_days_weight_lbs: 0.66579657","date: 2020-08-13<br />total_days_weight_lbs: 0.92814688","date: 2020-08-14<br />total_days_weight_lbs: 0.27778268","date: 2020-08-16<br />total_days_weight_lbs: 1.05160585","date: 2020-08-17<br />total_days_weight_lbs: 0.96121625","date: 2020-08-18<br />total_days_weight_lbs: 0.29982892","date: 2020-08-19<br />total_days_weight_lbs: 0.99428561","date: 2020-08-20<br />total_days_weight_lbs: 1.08687984","date: 2020-08-21<br />total_days_weight_lbs: 0.58422547","date: 2020-08-23<br />total_days_weight_lbs: 0.96121625","date: 2020-08-24<br />total_days_weight_lbs: 0.16534683","date: 2020-08-25<br />total_days_weight_lbs: 1.11553996","date: 2020-08-26<br />total_days_weight_lbs: 1.80558740","date: 2020-08-29<br />total_days_weight_lbs: 0.83775728","date: 2020-08-30<br />total_days_weight_lbs: 1.83645214","date: 2020-09-03<br />total_days_weight_lbs: 2.49343022","date: 2020-09-04<br />total_days_weight_lbs: 0.97444399","date: 2020-09-06<br />total_days_weight_lbs: 1.35584402","date: 2020-09-10<br />total_days_weight_lbs: 1.12215383","date: 2020-09-15<br />total_days_weight_lbs: 0.56879310","date: 2020-09-19<br />total_days_weight_lbs: 2.33249264","date: 2020-09-25<br />total_days_weight_lbs: 1.80558740"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18464,18467,18469,18472,18475,18476,18478,18479,18481,18482,18483,18485,18487,18488,18490,18491,18492,18494,18495,18497,18499,18504,18511,18523,18526,18530],"y":[0.747367678442301,0.308647418825729,0.326284414187199,0.337307536288118,0.959011622779943,0.343921409548669,0.853189650611122,1.24120354856347,0.791460166845976,1.24340817298365,0.394627771212896,0.67681969699642,0.731935307501014,1.56748796275067,1.19711106015979,0.802483288946895,0.597453217869804,1.39332263355615,0.727526058660647,0.593043969029436,1.3867087602956,0.341716785128486,1.56528333833048,2.31926489003333,1.57410183601122,1.05160584842766],"text":["date: 2020-07-21<br />total_days_weight_lbs: 0.74736768","date: 2020-07-24<br />total_days_weight_lbs: 0.30864742","date: 2020-07-26<br />total_days_weight_lbs: 0.32628441","date: 2020-07-29<br />total_days_weight_lbs: 0.33730754","date: 2020-08-01<br />total_days_weight_lbs: 0.95901162","date: 2020-08-02<br />total_days_weight_lbs: 0.34392141","date: 2020-08-04<br />total_days_weight_lbs: 0.85318965","date: 2020-08-05<br />total_days_weight_lbs: 1.24120355","date: 2020-08-07<br />total_days_weight_lbs: 0.79146017","date: 2020-08-08<br />total_days_weight_lbs: 1.24340817","date: 2020-08-09<br />total_days_weight_lbs: 0.39462777","date: 2020-08-11<br />total_days_weight_lbs: 0.67681970","date: 2020-08-13<br />total_days_weight_lbs: 0.73193531","date: 2020-08-14<br />total_days_weight_lbs: 1.56748796","date: 2020-08-16<br />total_days_weight_lbs: 1.19711106","date: 2020-08-17<br />total_days_weight_lbs: 0.80248329","date: 2020-08-18<br />total_days_weight_lbs: 0.59745322","date: 2020-08-20<br />total_days_weight_lbs: 1.39332263","date: 2020-08-21<br />total_days_weight_lbs: 0.72752606","date: 2020-08-23<br />total_days_weight_lbs: 0.59304397","date: 2020-08-25<br />total_days_weight_lbs: 1.38670876","date: 2020-08-30<br />total_days_weight_lbs: 0.34171679","date: 2020-09-06<br />total_days_weight_lbs: 1.56528334","date: 2020-09-18<br />total_days_weight_lbs: 2.31926489","date: 2020-09-21<br />total_days_weight_lbs: 1.57410184","date: 2020-09-25<br />total_days_weight_lbs: 1.05160585"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x2","yaxis":"y","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18475,18478,18479,18481,18485,18491,18493,18494,18497,18499,18500,18504,18506,18509,18523],"y":[0.705479814458809,0.509268241062453,0.639341081853296,0.784846293585425,0.480608123600063,0.672410448156052,0.738549180761566,1.06483359494876,0.983262491401965,1.92022786998007,2.2972186458315,1.01412723328454,0.392423146792712,0.947988500679024,1.57410183601122],"text":["date: 2020-08-01<br />total_days_weight_lbs: 0.70547981","date: 2020-08-04<br />total_days_weight_lbs: 0.50926824","date: 2020-08-05<br />total_days_weight_lbs: 0.63934108","date: 2020-08-07<br />total_days_weight_lbs: 0.78484629","date: 2020-08-11<br />total_days_weight_lbs: 0.48060812","date: 2020-08-17<br />total_days_weight_lbs: 0.67241045","date: 2020-08-19<br />total_days_weight_lbs: 0.73854918","date: 2020-08-20<br />total_days_weight_lbs: 1.06483359","date: 2020-08-23<br />total_days_weight_lbs: 0.98326249","date: 2020-08-25<br />total_days_weight_lbs: 1.92022787","date: 2020-08-26<br />total_days_weight_lbs: 2.29721865","date: 2020-08-30<br />total_days_weight_lbs: 1.01412723","date: 2020-09-01<br />total_days_weight_lbs: 0.39242315","date: 2020-09-04<br />total_days_weight_lbs: 0.94798850","date: 2020-09-18<br />total_days_weight_lbs: 1.57410184"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x3","yaxis":"y","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18467,18472,18474,18475,18480,18483,18484,18485,18493,18495,18500,18504,18506],"y":[0.544542231785393,0.529109860844107,0.67681969699642,1.36466251609376,0.668001199315685,0.679024321416604,0.476198874759696,1.76810878498739,1.92243249440025,3.52960369671423,1.30734228116898,1.32057002769008,0.443129508456939],"text":["date: 2020-07-24<br />total_days_weight_lbs: 0.54454223","date: 2020-07-29<br />total_days_weight_lbs: 0.52910986","date: 2020-07-31<br />total_days_weight_lbs: 0.67681970","date: 2020-08-01<br />total_days_weight_lbs: 1.36466252","date: 2020-08-06<br />total_days_weight_lbs: 0.66800120","date: 2020-08-09<br />total_days_weight_lbs: 0.67902432","date: 2020-08-10<br />total_days_weight_lbs: 0.47619887","date: 2020-08-11<br />total_days_weight_lbs: 1.76810878","date: 2020-08-19<br />total_days_weight_lbs: 1.92243249","date: 2020-08-21<br />total_days_weight_lbs: 3.52960370","date: 2020-08-26<br />total_days_weight_lbs: 1.30734228","date: 2020-08-30<br />total_days_weight_lbs: 1.32057003","date: 2020-09-01<br />total_days_weight_lbs: 0.44312951"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x4","yaxis":"y","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18471,18481,18482,18483,18484,18488,18494,18499,18504,18506,18509,18515],"y":[0.69445669235789,1.23899892414328,0.405650893313815,1.30293303232861,0.53131448526429,0.399037020053264,0.79366479126616,1.27427291486622,1.6578775639782,1.70417467680206,2.59704756697649,1.66228681281857],"text":["date: 2020-07-28<br />total_days_weight_lbs: 0.69445669","date: 2020-08-07<br />total_days_weight_lbs: 1.23899892","date: 2020-08-08<br />total_days_weight_lbs: 0.40565089","date: 2020-08-09<br />total_days_weight_lbs: 1.30293303","date: 2020-08-10<br />total_days_weight_lbs: 0.53131449","date: 2020-08-14<br />total_days_weight_lbs: 0.39903702","date: 2020-08-20<br />total_days_weight_lbs: 0.79366479","date: 2020-08-25<br />total_days_weight_lbs: 1.27427291","date: 2020-08-30<br />total_days_weight_lbs: 1.65787756","date: 2020-09-01<br />total_days_weight_lbs: 1.70417468","date: 2020-09-04<br />total_days_weight_lbs: 2.59704757","date: 2020-09-10<br />total_days_weight_lbs: 1.66228681"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y2","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18464,18471,18479,18480,18482,18487,18488,18490,18493,18494,18495,18499,18500,18503,18508,18511,18524,18530],"y":[0.302033545565178,0.447538757297307,0.491631245700982,0.67681969699642,0.357149156069772,0.9105098855359,0.586430095768885,0.87523589481296,0.582020846928517,0.75839080054322,1.85629376179474,2.18919204924249,1.09569833683134,2.27737702604984,2.78884989153248,3.63542566888305,0.670205823735868,1.45284749290111],"text":["date: 2020-07-21<br />total_days_weight_lbs: 0.30203355","date: 2020-07-28<br />total_days_weight_lbs: 0.44753876","date: 2020-08-05<br />total_days_weight_lbs: 0.49163125","date: 2020-08-06<br />total_days_weight_lbs: 0.67681970","date: 2020-08-08<br />total_days_weight_lbs: 0.35714916","date: 2020-08-13<br />total_days_weight_lbs: 0.91050989","date: 2020-08-14<br />total_days_weight_lbs: 0.58643010","date: 2020-08-16<br />total_days_weight_lbs: 0.87523589","date: 2020-08-19<br />total_days_weight_lbs: 0.58202085","date: 2020-08-20<br />total_days_weight_lbs: 0.75839080","date: 2020-08-21<br />total_days_weight_lbs: 1.85629376","date: 2020-08-25<br />total_days_weight_lbs: 2.18919205","date: 2020-08-26<br />total_days_weight_lbs: 1.09569834","date: 2020-08-29<br />total_days_weight_lbs: 2.27737703","date: 2020-09-03<br />total_days_weight_lbs: 2.78884989","date: 2020-09-06<br />total_days_weight_lbs: 3.63542567","date: 2020-09-19<br />total_days_weight_lbs: 0.67020582","date: 2020-09-25<br />total_days_weight_lbs: 1.45284749"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x2","yaxis":"y2","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18475,18476,18480,18485,18490,18492,18493,18495,18497,18500,18530],"y":[0.961216247200127,1.8893631280975,0.866417397132225,0.791460166845976,0.641545706273479,0.698865941198258,1.27647753928641,3.39071235824265,3.46787421294908,0.476198874759696,0.520291363163372],"text":["date: 2020-08-01<br />total_days_weight_lbs: 0.96121625","date: 2020-08-02<br />total_days_weight_lbs: 1.88936313","date: 2020-08-06<br />total_days_weight_lbs: 0.86641740","date: 2020-08-11<br />total_days_weight_lbs: 0.79146017","date: 2020-08-16<br />total_days_weight_lbs: 0.64154571","date: 2020-08-18<br />total_days_weight_lbs: 0.69886594","date: 2020-08-19<br />total_days_weight_lbs: 1.27647754","date: 2020-08-21<br />total_days_weight_lbs: 3.39071236","date: 2020-08-23<br />total_days_weight_lbs: 3.46787421","date: 2020-08-26<br />total_days_weight_lbs: 0.47619887","date: 2020-09-25<br />total_days_weight_lbs: 0.52029136"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x3","yaxis":"y2","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18471,18474,18476,18481,18488,18490,18492,18495,18497,18499,18500,18504,18506,18515,18523,18530],"y":[1.34702552073229,1.39552725797633,0.740753805181749,0.51367748990282,0.524700612003739,1.32057002769008,0.231485564119297,0.535723734104658,1.76590416056721,0.253531808321134,1.4682798638424,1.89818162577823,1.77472265824794,1.48591685920387,3.59574242931974,4.01903031799503],"text":["date: 2020-07-28<br />total_days_weight_lbs: 1.34702552","date: 2020-07-31<br />total_days_weight_lbs: 1.39552726","date: 2020-08-02<br />total_days_weight_lbs: 0.74075381","date: 2020-08-07<br />total_days_weight_lbs: 0.51367749","date: 2020-08-14<br />total_days_weight_lbs: 0.52470061","date: 2020-08-16<br />total_days_weight_lbs: 1.32057003","date: 2020-08-18<br />total_days_weight_lbs: 0.23148556","date: 2020-08-21<br />total_days_weight_lbs: 0.53572373","date: 2020-08-23<br />total_days_weight_lbs: 1.76590416","date: 2020-08-25<br />total_days_weight_lbs: 0.25353181","date: 2020-08-26<br />total_days_weight_lbs: 1.46827986","date: 2020-08-30<br />total_days_weight_lbs: 1.89818163","date: 2020-09-01<br />total_days_weight_lbs: 1.77472266","date: 2020-09-10<br />total_days_weight_lbs: 1.48591686","date: 2020-09-18<br />total_days_weight_lbs: 3.59574243","date: 2020-09-25<br />total_days_weight_lbs: 4.01903032"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x4","yaxis":"y2","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18467,18471,18472,18474,18476,18477,18481,18483,18485,18487,18490,18491,18493,18494,18500,18504,18509,18515,18523,18530],"y":[0.485017372440431,0.687842819097339,0.97444399372123,0.639341081853296,0.465175752658777,0.679024321416604,2.30383251909205,2.42949611104252,0.780437044745057,1.60276195347361,0.524700612003739,1.68433305702041,1.35584401841302,0.507063616642269,0.681228945836787,0.866417397132225,6.39120619411277,3.06883719289582,1.47709836152313,2.7315296566077],"text":["date: 2020-07-24<br />total_days_weight_lbs: 0.48501737","date: 2020-07-28<br />total_days_weight_lbs: 0.68784282","date: 2020-07-29<br />total_days_weight_lbs: 0.97444399","date: 2020-07-31<br />total_days_weight_lbs: 0.63934108","date: 2020-08-02<br />total_days_weight_lbs: 0.46517575","date: 2020-08-03<br />total_days_weight_lbs: 0.67902432","date: 2020-08-07<br />total_days_weight_lbs: 2.30383252","date: 2020-08-09<br />total_days_weight_lbs: 2.42949611","date: 2020-08-11<br />total_days_weight_lbs: 0.78043704","date: 2020-08-13<br />total_days_weight_lbs: 1.60276195","date: 2020-08-16<br />total_days_weight_lbs: 0.52470061","date: 2020-08-17<br />total_days_weight_lbs: 1.68433306","date: 2020-08-19<br />total_days_weight_lbs: 1.35584402","date: 2020-08-20<br />total_days_weight_lbs: 0.50706362","date: 2020-08-26<br />total_days_weight_lbs: 0.68122895","date: 2020-08-30<br />total_days_weight_lbs: 0.86641740","date: 2020-09-04<br />total_days_weight_lbs: 6.39120619","date: 2020-09-10<br />total_days_weight_lbs: 3.06883719","date: 2020-09-18<br />total_days_weight_lbs: 1.47709836","date: 2020-09-25<br />total_days_weight_lbs: 2.73152966"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x","yaxis":"y3","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18478,18479,18483,18485,18488,18490,18491,18492,18493,18494,18495,18499,18500,18503,18504,18506,18508,18509,18511,18515,18520,18522,18524,18526,18530],"y":[0.160937582673416,0.147709836152313,0.119049718689924,0.352739907229404,1.08026596589005,0.723116809820279,0.674615072576236,0.326284414187199,0.674615072576236,0.734139931921198,1.23899892414328,1.07585671704968,0.575406973667966,2.87262561949946,1.81220127339107,4.30563149261892,1.3448208963121,2.72050653450678,5.24039224677684,0.9105098855359,1.59835270463324,0.467380377078961,6.4683680488192,0.209439319917459,4.36074710312351],"text":["date: 2020-08-04<br />total_days_weight_lbs: 0.16093758","date: 2020-08-05<br />total_days_weight_lbs: 0.14770984","date: 2020-08-09<br />total_days_weight_lbs: 0.11904972","date: 2020-08-11<br />total_days_weight_lbs: 0.35273991","date: 2020-08-14<br />total_days_weight_lbs: 1.08026597","date: 2020-08-16<br />total_days_weight_lbs: 0.72311681","date: 2020-08-17<br />total_days_weight_lbs: 0.67461507","date: 2020-08-18<br />total_days_weight_lbs: 0.32628441","date: 2020-08-19<br />total_days_weight_lbs: 0.67461507","date: 2020-08-20<br />total_days_weight_lbs: 0.73413993","date: 2020-08-21<br />total_days_weight_lbs: 1.23899892","date: 2020-08-25<br />total_days_weight_lbs: 1.07585672","date: 2020-08-26<br />total_days_weight_lbs: 0.57540697","date: 2020-08-29<br />total_days_weight_lbs: 2.87262562","date: 2020-08-30<br />total_days_weight_lbs: 1.81220127","date: 2020-09-01<br />total_days_weight_lbs: 4.30563149","date: 2020-09-03<br />total_days_weight_lbs: 1.34482090","date: 2020-09-04<br />total_days_weight_lbs: 2.72050653","date: 2020-09-06<br />total_days_weight_lbs: 5.24039225","date: 2020-09-10<br />total_days_weight_lbs: 0.91050989","date: 2020-09-15<br />total_days_weight_lbs: 1.59835270","date: 2020-09-17<br />total_days_weight_lbs: 0.46738038","date: 2020-09-19<br />total_days_weight_lbs: 6.46836805","date: 2020-09-21<br />total_days_weight_lbs: 0.20943932","date: 2020-09-25<br />total_days_weight_lbs: 4.36074710"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x2","yaxis":"y3","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18470,18478,18479,18481,18492,18497,18499,18503,18504,18506,18509,18515,18530],"y":[1.76590416056721,0.747367678442301,1.72181167216353,0.802483288946895,1.34041164747174,1.55205559180938,2.26194465510856,2.4184729889416,1.29852378348825,2.6499585530609,0.562179227146863,0.696661316778074,4.42247658688866],"text":["date: 2020-07-27<br />total_days_weight_lbs: 1.76590416","date: 2020-08-04<br />total_days_weight_lbs: 0.74736768","date: 2020-08-05<br />total_days_weight_lbs: 1.72181167","date: 2020-08-07<br />total_days_weight_lbs: 0.80248329","date: 2020-08-18<br />total_days_weight_lbs: 1.34041165","date: 2020-08-23<br />total_days_weight_lbs: 1.55205559","date: 2020-08-25<br />total_days_weight_lbs: 2.26194466","date: 2020-08-29<br />total_days_weight_lbs: 2.41847299","date: 2020-08-30<br />total_days_weight_lbs: 1.29852378","date: 2020-09-01<br />total_days_weight_lbs: 2.64995855","date: 2020-09-04<br />total_days_weight_lbs: 0.56217923","date: 2020-09-10<br />total_days_weight_lbs: 0.69666132","date: 2020-09-25<br />total_days_weight_lbs: 4.42247659"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x3","yaxis":"y3","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null},{"x":[18468,18472,18474,18475,18476,18479,18480,18485,18487,18488,18490,18491,18493,18494,18495,18497,18499,18500,18504,18506,18509,18511,18515,18523,18530],"y":[1.02074110654509,0.46076650381841,0.434311010776204,0.213848568757826,1.12215382987354,0.842166528510203,0.385809273532161,1.11553995661299,1.41536887775799,1.15742782059648,1.23458967530292,1.29631915906806,2.19801054692323,2.22667066438562,0.943579251838657,3.39953085592338,3.08647418825729,4.1579216564666,6.4683680488192,3.38850773382247,4.76198874759696,2.90349036138204,1.52560009876717,0.502654367801901,6.03626166246318],"text":["date: 2020-07-25<br />total_days_weight_lbs: 1.02074111","date: 2020-07-29<br />total_days_weight_lbs: 0.46076650","date: 2020-07-31<br />total_days_weight_lbs: 0.43431101","date: 2020-08-01<br />total_days_weight_lbs: 0.21384857","date: 2020-08-02<br />total_days_weight_lbs: 1.12215383","date: 2020-08-05<br />total_days_weight_lbs: 0.84216653","date: 2020-08-06<br />total_days_weight_lbs: 0.38580927","date: 2020-08-11<br />total_days_weight_lbs: 1.11553996","date: 2020-08-13<br />total_days_weight_lbs: 1.41536888","date: 2020-08-14<br />total_days_weight_lbs: 1.15742782","date: 2020-08-16<br />total_days_weight_lbs: 1.23458968","date: 2020-08-17<br />total_days_weight_lbs: 1.29631916","date: 2020-08-19<br />total_days_weight_lbs: 2.19801055","date: 2020-08-20<br />total_days_weight_lbs: 2.22667066","date: 2020-08-21<br />total_days_weight_lbs: 0.94357925","date: 2020-08-23<br />total_days_weight_lbs: 3.39953086","date: 2020-08-25<br />total_days_weight_lbs: 3.08647419","date: 2020-08-26<br />total_days_weight_lbs: 4.15792166","date: 2020-08-30<br />total_days_weight_lbs: 6.46836805","date: 2020-09-01<br />total_days_weight_lbs: 3.38850773","date: 2020-09-04<br />total_days_weight_lbs: 4.76198875","date: 2020-09-06<br />total_days_weight_lbs: 2.90349036","date: 2020-09-10<br />total_days_weight_lbs: 1.52560010","date: 2020-09-18<br />total_days_weight_lbs: 0.50265437","date: 2020-09-25<br />total_days_weight_lbs: 6.03626166"],"type":"scatter","mode":"markers+lines","marker":{"autocolorscale":false,"color":"rgba(255,0,0,1)","opacity":1,"size":5.66929133858268,"symbol":"circle","line":{"width":1.88976377952756,"color":"rgba(255,0,0,1)"}},"hoveron":"points","showlegend":false,"xaxis":"x4","yaxis":"y3","hoverinfo":"text","line":{"width":1.88976377952756,"color":"rgba(0,0,0,1)","dash":"solid"},"frame":null}],"layout":{"margin":{"t":59.8356164383562,"r":7.30593607305936,"b":44.5662100456621,"l":31.4155251141553},"plot_bgcolor":"rgba(255,255,255,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"The Weight of Tomato Varieties Harvested Over Time","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,0.242389649923896],"automargin":true,"type":"linear","autorange":false,"range":[18450.2,18533.8],"tickmode":"array","ticktext":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"tickvals":[18458,18475,18489,18506,18520],"categoryorder":"array","categoryarray":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"y3","title":"","hoverformat":".2f"},"annotations":[{"text":"Date","x":0.5,"y":-0.0471841704718417,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"top","annotationType":"axis"},{"text":"Weight (lbs)","x":-0.0175038051750381,"y":0.5,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-90,"xanchor":"right","yanchor":"center","annotationType":"axis"},{"text":"grape","x":0.121194824961948,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Bonny Best","x":0.375,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Brandywine","x":0.625,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Cherokee Purple","x":0.878805175038052,"y":1,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Jet Star","x":0.121194824961948,"y":0.627092846270929,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Big Beef","x":0.375,"y":0.627092846270929,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Black Krim","x":0.625,"y":0.627092846270929,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Old German","x":0.878805175038052,"y":0.627092846270929,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Better Boy","x":0.121194824961948,"y":0.293759512937595,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"volunteers","x":0.375,"y":0.293759512937595,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Mortgage Lifter","x":0.625,"y":0.293759512937595,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"},{"text":"Amish Paste","x":0.878805175038052,"y":0.293759512937595,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(255,255,255,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"bottom"}],"yaxis":{"domain":[0.706240487062405,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.267861867052329,6.78914090195594],"tickmode":"array","ticktext":["0","2","4","6"],"tickvals":[0,2,4,6],"categoryorder":"array","categoryarray":["0","2","4","6"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0.706240487062405,"y1":1},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0,"y1":23.37899543379,"yanchor":1,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0.706240487062405,"y1":1},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0,"y1":23.37899543379,"yanchor":1,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0.706240487062405,"y1":1},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0,"y1":23.37899543379,"yanchor":1,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0.706240487062405,"y1":1},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0,"y1":23.37899543379,"yanchor":1,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0.372907153729072,"y1":0.627092846270929},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.627092846270929,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0.372907153729072,"y1":0.627092846270929},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.627092846270929,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0.372907153729072,"y1":0.627092846270929},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.627092846270929,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0.372907153729072,"y1":0.627092846270929},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0,"y1":23.37899543379,"yanchor":0.627092846270929,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0,"y1":0.293759512937595},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0,"x1":0.242389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.293759512937595,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0,"y1":0.293759512937595},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.257610350076104,"x1":0.492389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.293759512937595,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0,"y1":0.293759512937595},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.507610350076104,"x1":0.742389649923896,"y0":0,"y1":23.37899543379,"yanchor":0.293759512937595,"ysizemode":"pixel"},{"type":"rect","fillcolor":"transparent","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0,"y1":0.293759512937595},{"type":"rect","fillcolor":"rgba(0,0,0,1)","line":{"color":"rgba(0,0,0,1)","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","x0":0.757610350076104,"x1":1,"y0":0,"y1":23.37899543379,"yanchor":0.293759512937595,"ysizemode":"pixel"}],"xaxis2":{"type":"linear","autorange":false,"range":[18450.2,18533.8],"tickmode":"array","ticktext":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"tickvals":[18458,18475,18489,18506,18520],"categoryorder":"array","categoryarray":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.257610350076104,0.492389649923896],"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"y3","title":"","hoverformat":".2f"},"xaxis3":{"type":"linear","autorange":false,"range":[18450.2,18533.8],"tickmode":"array","ticktext":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"tickvals":[18458,18475,18489,18506,18520],"categoryorder":"array","categoryarray":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.507610350076104,0.742389649923896],"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"y3","title":"","hoverformat":".2f"},"xaxis4":{"type":"linear","autorange":false,"range":[18450.2,18533.8],"tickmode":"array","ticktext":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"tickvals":[18458,18475,18489,18506,18520],"categoryorder":"array","categoryarray":["Jul 15","Aug 01","Aug 15","Sep 01","Sep 15"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.757610350076104,1],"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"y3","title":"","hoverformat":".2f"},"yaxis2":{"type":"linear","autorange":false,"range":[-0.267861867052329,6.78914090195594],"tickmode":"array","ticktext":["0","2","4","6"],"tickvals":[0,2,4,6],"categoryorder":"array","categoryarray":["0","2","4","6"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.372907153729072,0.627092846270929],"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"yaxis3":{"type":"linear","autorange":false,"range":[-0.267861867052329,6.78914090195594],"tickmode":"array","ticktext":["0","2","4","6"],"tickvals":[0,2,4,6],"categoryorder":"array","categoryarray":["0","2","4","6"],"nticks":null,"ticks":"outside","tickcolor":"rgba(0,0,0,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0,0.293759512937595],"gridcolor":"rgba(0,0,0,1)","gridwidth":0.066417600664176,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"showlegend":false,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"d91a5dfa3a91":{"x":{},"y":{},"type":"scatter"},"d91a571b35ca":{"x":{},"y":{}}},"cur_data":"d91a5dfa3a91","visdat":{"d91a5dfa3a91":["function (y) ","x"],"d91a571b35ca":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->



```r
science <- read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-06-09/science.csv')

#Look at only the first listed occupation for each scientist in the dataset; create a new dataset called new_occupation
science %>% 
  separate(occupation_s, sep = ";", into = "first_occupation", extra = "drop") -> new_occupation


#Count the number of scientists in each occupation to help identify the top five most common occupations
new_occupation %>% 
  count(first_occupation) 
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["first_occupation"],"name":[1],"type":["chr"],"align":["left"]},{"label":["n"],"name":[2],"type":["int"],"align":["right"]}],"data":[{"1":"Acoustician","2":"1"},{"1":"Almanac author","2":"1"},{"1":"Anthropologist","2":"1"},{"1":"Astronomer","2":"1"},{"1":"Astrophysicist","2":"1"},{"1":"Atmospheric scientist","2":"1"},{"1":"Automotive engineer","2":"1"},{"1":"Bacteriologist","2":"1"},{"1":"Biochemist[citation needed]","2":"1"},{"1":"Bioengineer","2":"1"},{"1":"Biophysicist","2":"1"},{"1":"Botanical researcher","2":"1"},{"1":"Cancer research and surgeon","2":"1"},{"1":"Chemical engineer","2":"1"},{"1":"Chemist","2":"9"},{"1":"Civil engineer","2":"1"},{"1":"Computer engineer","2":"2"},{"1":"Computer scientist","2":"6"},{"1":"Dentist","2":"1"},{"1":"Economist","2":"2"},{"1":"Electrical Engineer","2":"1"},{"1":"Electrician","2":"1"},{"1":"Engineer","2":"3"},{"1":"Environmental chemist","2":"1"},{"1":"Evolutionary biologist","2":"1"},{"1":"Farmer","2":"1"},{"1":"Gastroenterologist","2":"1"},{"1":"Geneticist","2":"1"},{"1":"Geochemist","2":"1"},{"1":"Inventor","2":"25"},{"1":"Inventor[citation needed]","2":"1"},{"1":"Linguist","2":"2"},{"1":"Mathematician","2":"8"},{"1":"Medical researcher","2":"1"},{"1":"Microbiologist","2":"1"},{"1":"Neurobiologist","2":"1"},{"1":"Neurosurgeon","2":"1"},{"1":"Nuclear engineer","2":"1"},{"1":"Nurse","2":"1"},{"1":"Ophthalmologist","2":"1"},{"1":"Orthodontist","2":"1"},{"1":"Pediatric neurosurgeon","2":"1"},{"1":"Physical therapist","2":"1"},{"1":"Physician","2":"1"},{"1":"Physicist","2":"6"},{"1":"Professor","2":"1"},{"1":"Psychologist","2":"3"},{"1":"Research engineer","2":"1"},{"1":"Researcher","2":"1"},{"1":"Roboticist","2":"1"},{"1":"Scientist","2":"2"},{"1":"Scientist and researcher","2":"1"},{"1":"Surgeon","2":"3"},{"1":"Surgical technician","2":"1"},{"1":"Theoretical physicist","2":"1"},{"1":"Transplant surgeon","2":"1"},{"1":"Woods Hole Marine Biology Institute biologist","2":"1"},{"1":"Zoologist","2":"2"},{"1":"ZoologistexplorerAnthropologist","2":"1"},{"1":"NA","2":"1"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>

```r
#Filter out only the Top Five Most Common Occupations; create a new dataset called topfive_first_occupations
topfive_first_occupations <- filter(new_occupation, first_occupation %in% c("Inventor", "Chemist", "Mathematician", "Computer scientist", "Physicist"))

#Create a new variable from birth called birth_decade
floor_decade = function(value){ return(value - value %% 10) }
topfive_first_occupations$birth_decade <- floor_decade(topfive_first_occupations$birth)

#Plot birth decade vs. occupation in a bar graph; facet so rows show occupation
african_american_scientist_occupations <- topfive_first_occupations %>% 
  ggplot(aes(x = birth_decade)) +
  geom_bar(fill = "blue") +
  facet_grid(rows = vars(first_occupation), space = "free") +
  labs(title = "Birth Decade vs. Occupation Frequency for African American Scientists", x = "Birth Decade", y = "Count") +
  theme_dark()

ggplotly(african_american_scientist_occupations)
```

<!--html_preserve--><div id="htmlwidget-5b3554f477870fd6096c" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-5b3554f477870fd6096c">{"x":{"data":[{"orientation":"v","width":[9,9,9,9,9],"base":[0,0,0,0,0],"x":[1870,1880,1890,1910,1920],"y":[1,1,3,3,1],"text":["count: 1<br />birth_decade: 1870","count: 1<br />birth_decade: 1880","count: 3<br />birth_decade: 1890","count: 3<br />birth_decade: 1910","count: 1<br />birth_decade: 1920"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,0,255,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y","hoverinfo":"text","frame":null},{"orientation":"v","width":[9,9,9,9],"base":[0,0,0,0],"x":[1930,1940,1950,1960],"y":[1,1,2,1],"text":["count: 1<br />birth_decade: 1930","count: 1<br />birth_decade: 1940","count: 2<br />birth_decade: 1950","count: 1<br />birth_decade: 1960"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,0,255,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y2","hoverinfo":"text","frame":null},{"orientation":"v","width":[9,9,9,9,9,9,9,9,9,9,9,9,9],"base":[0,0,0,0,0,0,0,0,0,0,0,0,0],"x":[1790,1800,1810,1820,1830,1840,1850,1860,1870,1890,1920,1950,1970],"y":[1,2,1,1,2,2,3,1,1,3,2,1,1],"text":["count: 1<br />birth_decade: 1790","count: 2<br />birth_decade: 1800","count: 1<br />birth_decade: 1810","count: 1<br />birth_decade: 1820","count: 2<br />birth_decade: 1830","count: 2<br />birth_decade: 1840","count: 3<br />birth_decade: 1850","count: 1<br />birth_decade: 1860","count: 1<br />birth_decade: 1870","count: 3<br />birth_decade: 1890","count: 2<br />birth_decade: 1920","count: 1<br />birth_decade: 1950","count: 1<br />birth_decade: 1970"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,0,255,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y3","hoverinfo":"text","frame":null},{"orientation":"v","width":[9,9,9,9],"base":[0,0,0,0],"x":[1910,1920,1930,1940],"y":[2,2,1,3],"text":["count: 2<br />birth_decade: 1910","count: 2<br />birth_decade: 1920","count: 1<br />birth_decade: 1930","count: 3<br />birth_decade: 1940"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,0,255,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y4","hoverinfo":"text","frame":null},{"orientation":"v","width":[9,9,9,9,9],"base":[0,0,0,0,0],"x":[1850,1910,1930,1940,1960],"y":[1,1,1,2,1],"text":["count: 1<br />birth_decade: 1850","count: 1<br />birth_decade: 1910","count: 1<br />birth_decade: 1930","count: 2<br />birth_decade: 1940","count: 1<br />birth_decade: 1960"],"type":"bar","marker":{"autocolorscale":false,"color":"rgba(0,0,255,1)","line":{"width":1.88976377952756,"color":"transparent"}},"showlegend":false,"xaxis":"x","yaxis":"y5","hoverinfo":"text","frame":null}],"layout":{"margin":{"t":55.4520547945205,"r":18.9954337899543,"b":40.1826484018265,"l":31.4155251141553},"plot_bgcolor":"rgba(127,127,127,1)","paper_bgcolor":"rgba(255,255,255,1)","font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"title":{"text":"Birth Decade vs. Occupation Frequency for African American Scientists","font":{"color":"rgba(0,0,0,1)","family":"","size":17.5342465753425},"x":0,"xref":"paper"},"xaxis":{"domain":[0,1],"automargin":true,"type":"linear","autorange":false,"range":[1776.05,1983.95],"tickmode":"array","ticktext":["1800","1850","1900","1950"],"tickvals":[1800,1850,1900,1950],"categoryorder":"array","categoryarray":["1800","1850","1900","1950"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"y5","title":"","hoverformat":".2f"},"annotations":[{"text":"Birth Decade","x":0.5,"y":-0.0471841704718417,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-0,"xanchor":"center","yanchor":"top","annotationType":"axis"},{"text":"Count","x":-0.0250054359643401,"y":0.5,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(0,0,0,1)","family":"","size":14.6118721461187},"xref":"paper","yref":"paper","textangle":-90,"xanchor":"right","yanchor":"center","annotationType":"axis"},{"text":"Chemist","x":1,"y":0.907610350076103,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(229,229,229,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":90,"xanchor":"left","yanchor":"middle"},{"text":"Computer scientist","x":1,"y":0.7,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(229,229,229,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":90,"xanchor":"left","yanchor":"middle"},{"text":"Inventor","x":1,"y":0.5,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(229,229,229,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":90,"xanchor":"left","yanchor":"middle"},{"text":"Mathematician","x":1,"y":0.3,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(229,229,229,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":90,"xanchor":"left","yanchor":"middle"},{"text":"Physicist","x":1,"y":0.0923896499238965,"showarrow":false,"ax":0,"ay":0,"font":{"color":"rgba(229,229,229,1)","family":"","size":11.689497716895},"xref":"paper","yref":"paper","textangle":90,"xanchor":"left","yanchor":"middle"}],"yaxis":{"domain":[0.815220700152207,1],"automargin":true,"type":"linear","autorange":false,"range":[-0.15,3.15],"tickmode":"array","ticktext":["0","1","2","3"],"tickvals":[0,1,2,3],"categoryorder":"array","categoryarray":["0","1","2","3"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"shapes":[{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0.815220700152207,"y1":1},{"type":"rect","fillcolor":"rgba(38,38,38,1)","line":{"color":"transparent","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","y0":0.815220700152207,"y1":1,"x0":0,"x1":23.37899543379,"xanchor":1,"xsizemode":"pixel"},{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0.615220700152207,"y1":0.784779299847793},{"type":"rect","fillcolor":"rgba(38,38,38,1)","line":{"color":"transparent","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","y0":0.615220700152207,"y1":0.784779299847793,"x0":0,"x1":23.37899543379,"xanchor":1,"xsizemode":"pixel"},{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0.415220700152207,"y1":0.584779299847793},{"type":"rect","fillcolor":"rgba(38,38,38,1)","line":{"color":"transparent","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","y0":0.415220700152207,"y1":0.584779299847793,"x0":0,"x1":23.37899543379,"xanchor":1,"xsizemode":"pixel"},{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0.215220700152207,"y1":0.384779299847793},{"type":"rect","fillcolor":"rgba(38,38,38,1)","line":{"color":"transparent","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","y0":0.215220700152207,"y1":0.384779299847793,"x0":0,"x1":23.37899543379,"xanchor":1,"xsizemode":"pixel"},{"type":"rect","fillcolor":null,"line":{"color":null,"width":0,"linetype":[]},"yref":"paper","xref":"paper","x0":0,"x1":1,"y0":0,"y1":0.184779299847793},{"type":"rect","fillcolor":"rgba(38,38,38,1)","line":{"color":"transparent","width":0.66417600664176,"linetype":"solid"},"yref":"paper","xref":"paper","y0":0,"y1":0.184779299847793,"x0":0,"x1":23.37899543379,"xanchor":1,"xsizemode":"pixel"}],"yaxis2":{"type":"linear","autorange":false,"range":[-0.15,3.15],"tickmode":"array","ticktext":["0","1","2","3"],"tickvals":[0,1,2,3],"categoryorder":"array","categoryarray":["0","1","2","3"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.615220700152207,0.784779299847793],"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"yaxis3":{"type":"linear","autorange":false,"range":[-0.15,3.15],"tickmode":"array","ticktext":["0","1","2","3"],"tickvals":[0,1,2,3],"categoryorder":"array","categoryarray":["0","1","2","3"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.415220700152207,0.584779299847793],"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"yaxis4":{"type":"linear","autorange":false,"range":[-0.15,3.15],"tickmode":"array","ticktext":["0","1","2","3"],"tickvals":[0,1,2,3],"categoryorder":"array","categoryarray":["0","1","2","3"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0.215220700152207,0.384779299847793],"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"yaxis5":{"type":"linear","autorange":false,"range":[-0.15,3.15],"tickmode":"array","ticktext":["0","1","2","3"],"tickvals":[0,1,2,3],"categoryorder":"array","categoryarray":["0","1","2","3"],"nticks":null,"ticks":"outside","tickcolor":"rgba(51,51,51,1)","ticklen":3.65296803652968,"tickwidth":0.33208800332088,"showticklabels":true,"tickfont":{"color":"rgba(77,77,77,1)","family":"","size":11.689497716895},"tickangle":-0,"showline":false,"linecolor":null,"linewidth":0,"showgrid":true,"domain":[0,0.184779299847793],"gridcolor":"rgba(107,107,107,1)","gridwidth":0.33208800332088,"zeroline":false,"anchor":"x","title":"","hoverformat":".2f"},"showlegend":false,"legend":{"bgcolor":"rgba(255,255,255,1)","bordercolor":"transparent","borderwidth":1.88976377952756,"font":{"color":"rgba(0,0,0,1)","family":"","size":11.689497716895}},"hovermode":"closest","barmode":"relative"},"config":{"doubleClick":"reset","showSendToCloud":false},"source":"A","attrs":{"d91a51570073":{"x":{},"type":"bar"}},"cur_data":"d91a51570073","visdat":{"d91a51570073":["function (y) ","x"]},"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->


  2. Use animation to tell an interesting story with the `small_trains` dataset that contains data from the SNCF (National Society of French Railways). These are Tidy Tuesday data! Read more about it [here](https://github.com/rfordatascience/tidytuesday/tree/master/data/2019/2019-02-26).


```r
small_trains %>% 
  filter(departure_station == "PARIS EST", arrival_station == "NANCY", year == 2018) %>% 
  #arrange(year, month)
  ggplot(aes(x = month, y = total_num_trips)) +
  geom_line(color = "darkred") +
  labs(title = "Total Number of Trips per Month from PARIS EST to NANCY in 2018", x = "Month", y = "Trips", subtitle = "Month: {frame_along}") +
  transition_reveal(month)
```



```r
anim_save("Trains.gif")
```



```r
knitr::include_graphics("Trains.gif")
```

![](Trains.gif)<!-- -->



## Garden data

  3. In this exercise, you will create a stacked area plot that reveals itself over time (see the `geom_area()` examples [here](https://ggplot2.tidyverse.org/reference/position_stack.html)). You will look at cumulative harvest of tomato varieties over time. You should do the following:
  * From the `garden_harvest` data, filter the data to the tomatoes and find the *daily* harvest in pounds for each variety.
  * Then, for each variety, find the cumulative harvest in pounds.
  * Use the data you just made to create a static cumulative harvest area plot, with the areas filled with different colors for each vegetable and arranged (HINT: `fct_reorder()`) from most to least harvested (most on the bottom).  
  * Add animation to reveal the plot over date.
  

```r
garden_harvest %>% 
  filter(vegetable == "tomatoes") %>% 
  complete(variety, date = seq.Date(min(date), max(date), by="day")) %>% 
  select(-c(vegetable, units)) %>% 
  mutate(weight = replace_na(weight, 0)) %>% 
  group_by(variety, date) %>% 
  summarize(daily_harvest_lb = sum(weight) * 0.00220462) %>% 
  mutate(cumsum_daily_harvest_lb = cumsum(daily_harvest_lb)) %>%   
  select(-daily_harvest_lb) %>% 
  ggplot(aes(x = date, y = cumsum_daily_harvest_lb, fill = variety)) +
  geom_area(position = position_stack()) +
  labs(title = "Cumulative Harvest of Tomato Varieties Over Time", x = "Date", y = "Pounds", fill = "Variety", subtitle = "Date: {frame_along}") +
  transition_reveal(date) 
```



```r
anim_save("CumulativeHarvest.gif")
```



```r
knitr::include_graphics("CumulativeHarvest.gif")
```

![](CumulativeHarvest.gif)<!-- -->


## Maps, animation, and movement!

  4. Map my `mallorca_bike_day7` bike ride using animation! 
  Requirements:
  * Plot on a map using `ggmap`.  
  * Show "current" location with a red point. 
  * Show path up until the current point.  
  * Color the path according to elevation.
  * Show the time in the subtitle.  
  * CHALLENGE: use the `ggimage` package and `geom_image` to add a bike image instead of a red point. You can use [this](https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png) image. See [here](https://goodekat.github.io/presentations/2019-isugg-gganimate-spooky/slides.html#35) for an example. 
  * Add something of your own! And comment on if you prefer this to the static map and why or why not.


```r
bike_image_link <- "https://raw.githubusercontent.com/llendway/animation_and_interactivity/master/bike.png"

mallorca_bike_day7 <- mallorca_bike_day7 %>%
  mutate(image = bike_image_link)

mallorca_map <- get_stamenmap(
    bbox = c(left = 2.28, bottom = 39.41, right = 3.03, top = 39.8), 
    maptype = "terrain",
    zoom = 11)

ggmap(mallorca_map) +
  geom_path(data = mallorca_bike_day7, 
            aes(x = lon, y = lat, color = ele),
            size = .5) +
  labs(title = "Mallorca Bike Ride",
      subtitle = "Time: {frame_along}") +
  geom_image(data = mallorca_bike_day7,
            aes(x = lon, y = lat, image = bike_image_link), 
            size = 0.075) +
  transition_reveal(time) +
  scale_color_viridis_c(option = "magma") +
  theme_map() +
  theme(legend.background = element_blank())
```



```r
anim_save("MallorcaBikeRide.gif")
```



```r
knitr::include_graphics("MallorcaBikeRide.gif")
```

![](MallorcaBikeRide.gif)<!-- -->


I prefer the animated map to the static map because it allows you to see the direction Lisa takes on her ride. The static map only shows the path, but not the direction it was traveled. 

  
  5. In this exercise, you get to meet my sister, Heather! She is a proud Mac grad, currently works as a Data Scientist at 3M where she uses R everyday, and for a few years (while still holding a full-time job) she was a pro triathlete. You are going to map one of her races. The data from each discipline of the Ironman 70.3 Pan Am championships, Panama is in a separate file - `panama_swim`, `panama_bike`, and `panama_run`. Create a similar map to the one you created with my cycling data. You will need to make some small changes: 1. combine the files (HINT: `bind_rows()`, 2. make the leading dot a different color depending on the event (for an extra challenge, make it a different image using `geom_image()!), 3. CHALLENGE (optional): color by speed, which you will need to compute on your own from the data. You can read Heather's race report [here](https://heatherlendway.com/2016/02/10/ironman-70-3-pan-american-championships-panama-race-report/). She is also in the Macalester Athletics [Hall of Fame](https://athletics.macalester.edu/honors/hall-of-fame/heather-lendway/184) and still has records at the pool. 
  


```r
total_trail <- panama_swim %>%
  bind_rows(list(panama_run, panama_bike)) 

panama_map <- get_stamenmap(
  bbox = c(left = -79.56, bottom = 8.88, right = -79.41, top = 9.001),
  maptype = "terrain",
  zoom = 13)

ggmap(panama_map) +
  geom_point(data = total_trail, 
             aes(x = lon, y = lat, color = event, shape = event),
             size = 2) +
  geom_path(data = total_trail,
            aes(x = lon, y = lat, color = event),
            alpha = 0.8, size = 0.5) +
  labs(title = "Ironman 70.3 Pan Am Championship",
       subtitle = "Time: {frame_along}") +
  scale_color_viridis_d(option = "magma") +
  theme_map() +
  theme(legend.background = element_blank()) +
  transition_reveal(time)
```



```r
anim_save("ironmanpanam.gif")
```
  


```r
knitr::include_graphics("ironmanpanam.gif")
```

![](ironmanpanam.gif)<!-- -->

  
## COVID-19 data

  6. In this exercise, you are going to replicate many of the features in [this](https://aatishb.com/covidtrends/?region=US) visualization by Aitish Bhatia but include all US states. Requirements:
 * Create a new variable that computes the number of new cases in the past week (HINT: use the `lag()` function you've used in a previous set of exercises). Replace missing values with 0's using `replace_na()`. 
  * Filter the data to omit rows where the cumulative case counts are less than 20. 
  * Create a static plot with cumulative cases on the x-axis and new cases in the past 7 days on the y-axis. Connect the points for each state over time. HINTS: use `geom_path()` and add a `group` aesthetic.  Put the x and y axis on the log scale and make the tick labels look nice - `scales::comma` is one option. This plot will look pretty ugly as is. 
  * Animate the plot to reveal the pattern by date. Display the date as the subtitle. Add a leading point to each state's line (`geom_point()`) and add the state name as a label (`geom_text()` - you should look at the `check_overlap` argument). 
  * Use the `animate()` function to have 200 frames in your animation and make it 30 seconds long. 
  * Comment on what you observe.
  

```r
covid19 %>% 
  group_by(state) %>% 
  mutate(lag7 = lag(cases, 7, order_by = date)) %>%
  replace_na(list(lag7 = 0)) %>%
  mutate(new_cases_past_week = cases - lag7) %>% 
  filter(cases >= 20) %>% 
  
  ggplot(aes(x = cases, y = new_cases_past_week, group = state)) +
  geom_point() +
  geom_path(color = "lightgray") +
  geom_text(aes(label = state), check_overlap = TRUE) +
  scale_x_log10(labels = scales::comma) +
  scale_y_log10(labels = scales::comma) +
  labs(title = "Trajectory of US COVID-19 Confirmed Cases", x = "Total Confirmed Cases", y = "New Confirmed Cases (in the Past Week)", subtitle = "Date: {frame_along}") +
  theme(legend.position = "none") +
  transition_reveal(date) -> covid19trajectory_gganim

  animate(covid19trajectory_gganim, nframes = 200, duration = 30)
```


```r
anim_save("COVID19Trajectory.gif")
```



```r
knitr::include_graphics("COVID19Trajectory.gif")
```

![](COVID19Trajectory.gif)<!-- -->


The trajectory of confirmed COVID-19 cases increases sharply for nearly all states initially (especially for some states, such as New York, California, and Florida). Then, the number of new confirmed cases in the past week drops off for most states once they reach about 10,000 total confirmed cases(although the point of drop off varies for each state).
  
  
  7. In this exercise you will animate a map of the US, showing how cumulative COVID-19 cases per 10,000 residents has changed over time. This is similar to exercises 11 & 12 from the previous exercises, with the added animation! So, in the end, you should have something like the static map you made there, but animated over all the days. Put date in the subtitle. Comment on what you see.
  


```r
census_pop_est_2018 <- read_csv("https://www.dropbox.com/s/6txwv3b4ng7pepe/us_census_2018_state_pop_est.csv?dl=1") %>% 
  separate(state, into = c("dot","state"), extra = "merge") %>% 
  select(-dot) %>% 
  mutate(state = str_to_lower(state))
```
  

```r
covid19_population <-
  covid19 %>% 
  mutate(state = str_to_lower(state)) %>%
  left_join(census_pop_est_2018,
            by = "state") %>% 
  group_by(state, est_pop_2018, date) %>%
  summarize(cumulative_cases = max(cases)) %>%
  mutate(cases_per_10000 = (cumulative_cases/est_pop_2018)*10000)

states_map <- map_data("state")

covid_map <- covid19_population %>% 
  mutate(state = str_to_lower(state), weekday = wday(date, label=TRUE)) %>%
  filter(weekday == "Fri") %>%
  ggplot() +
  geom_map(map = states_map,
           aes(map_id = state, fill = cases_per_10000, group = date)) +
  expand_limits(x = states_map$long, y = states_map$lat) + 
  labs(title = "Cumulative COVID-19 cases per 10,000 people in the United States", fill = "Cases/10,000") +
  theme(legend.background = element_blank()) + 
  theme_map() +
  scale_fill_viridis_c() +
  transition_states(date, transition_length = 0) +
  labs(subtitle = "Date: {next_state}")

animate(covid_map, duration = 30)
```


```r
anim_save("COVID19Map.gif")
```


```r
knitr::include_graphics("COVID19Map.gif")
```

![](COVID19Map.gif)<!-- -->

From this graphic, I can see that states in the same geographic region of the country tend to show similar increases in the number of confirmed cases per 10,000. For instance, the southeastern states all seem to shift from blue to green to yellow together, indicating an increase in the number of cases per 10,000 in each of these states. 
  
  
## GitHub link

  9. Below, provide a link to your GitHub page with this set of Weekly Exercises. Specifically, if the name of the file is 05_exercises.Rmd, provide a link to the 05_exercises.md file, which is the one that will be most readable on GitHub. If that file isn't very readable, then provide a link to your main GitHub page.
  

  


**DID YOU REMEMBER TO UNCOMMENT THE OPTIONS AT THE TOP?**
