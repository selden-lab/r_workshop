---
title: "R Markdown Demo"
author: "Professor Becca Selden"
date: "6/10/2020"
output: 
  html_document:
    theme: flatly
    toc: TRUE
    toc_float: TRUE
    keep_md: yes
---
## Objective
To get more familiar with R, Rmarkdown, and the tidyverse

## Initial Setup
The top portion of this is the metadata. Fill this out everytime! Give it a title, you are the author, include the date, and choose the output. Here I choose the html output, using a special theme "flatly" and include a floating table of contents (toc) that automatically generates based on the headers I provide below.


```r
knitr::opts_chunk$set(
  echo = TRUE,
  comment= NA,
  warning = FALSE,
  fig.path=here::here("Rmd.Figures/"))
```

This first time, I'm saying include=TRUE for this to be included in the RMarkdown. In future RMarkdown files, I'll say include=F to supress this from being shown in RMarkdown.

I also name my chunk "setup". This both helps with debuging if something fails, and can also be used to automatically name figures based on their code chunk (see below)

Here I also specify the options for how the Markdown should be "knit".  
- echo=TRUE unless othewise specified, show the RCode that made something  
- comment=NA remove the hash marks that would otherwise show up in the left hand side of the code output  
- warning=FALSE don't show any warnings that result from running any code  
- fig.path="place_for_Figures" this will save figures that were generated while rendering the html. They will be named based on the name of the chunk.


## Packages 
Since R is open source many people have developed packages to accomplish specific tasks or analyses. We will take advantage of that by installing them. 

```r
install.packages("here")
install.packages("tidyverse")
```

Once you have installed a package you don't have re-run that (so I put eval=F).

When we want to use a package we use the function `library(packagename)`. 

```r
library(here)
```

```
here() starts at /Users/rs5/Documents/WellesleyResearch/r_workshop
```

```r
library(tidyverse)
```

```
── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
✔ dplyr     1.1.4     ✔ readr     2.1.5
✔ forcats   1.0.0     ✔ stringr   1.5.1
✔ ggplot2   3.4.4     ✔ tibble    3.2.1
✔ lubridate 1.9.3     ✔ tidyr     1.3.1
✔ purrr     1.0.2     
```

```
── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
✖ dplyr::filter() masks stats::filter()
✖ dplyr::lag()    masks stats::lag()
ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

Sometimes two packages have the same name for a function. Whichever one is loaded second will supersede the first, and you will be warned when you load the second package that it is "masking" a function. However, we can specify which one we want to use using `package_name::function_name`.

You can see that `dplyr` within the tidyverse package has a function called `filter()` as does the `stats` package within baseR. If we want to use `filter()` from the stats packages you can say `stats::filter()`


## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:


```r
?cars
summary(cars)
```

```
     speed           dist       
 Min.   : 4.0   Min.   :  2.00  
 1st Qu.:12.0   1st Qu.: 26.00  
 Median :15.0   Median : 36.00  
 Mean   :15.4   Mean   : 42.98  
 3rd Qu.:19.0   3rd Qu.: 56.00  
 Max.   :25.0   Max.   :120.00  
```


In this case, we are requesting that R provide summary statistics for a built in dataset `cars` using the function `summary()`


Let's take a look at the `chickwts` dataset that comes with baseR

```r
?chickwts
?plot
```

```
Help on topic 'plot' was found in the following packages:

  Package               Library
  base                  /Library/Frameworks/R.framework/Resources/library
  graphics              /Library/Frameworks/R.framework/Versions/4.3-arm64/Resources/library


Using the first match ...
```

```r
?ggplot

summary(chickwts)
```

```
     weight             feed   
 Min.   :108.0   casein   :12  
 1st Qu.:204.5   horsebean:10  
 Median :258.0   linseed  :12  
 Mean   :261.3   meatmeal :11  
 3rd Qu.:323.5   soybean  :14  
 Max.   :423.0   sunflower:12  
```



What are the first few lines of the dataset?

```r
head(chickwts)
```

```
  weight      feed
1    179 horsebean
2    160 horsebean
3    136 horsebean
4    227 horsebean
5    217 horsebean
6    168 horsebean
```

What are the names of the columns?

```r
names(chickwts)
```

```
[1] "weight" "feed"  
```

Getting the structure of an object

```r
str(chickwts)
```

```
'data.frame':	71 obs. of  2 variables:
 $ weight: num  179 160 136 227 217 168 108 124 143 140 ...
 $ feed  : Factor w/ 6 levels "casein","horsebean",..: 2 2 2 2 2 2 2 2 2 2 ...
```
We see that the object `chickwts` is a `data.frame` with two variables (columns), and 71 rows (obs.). The column weight is a numeric variable, and feed is a factor with 6 different levels.

## Including Plots

You can also embed plots, and control the dimensions with `fig.width` and `fig.height`. Here I specify the figure will have a width of 5 and a height of 3 inches. Use `fig.cap` to include a figure caption in the html output of the RMarkdown.

For ggplot, you first specify the dataset you want to use for the plot with `ggplot(data)` then specify the geometry you want it to plot in `+geom_something()` where you specify the aesthetics with the argument `aes(x,y)`


```r
ggplot(data=chickwts) + geom_boxplot(aes(x=feed, y=weight, fill=feed))
```

![Figure 1. Chick weights on different feed types after 6 weeks](/Users/rs5/Documents/WellesleyResearch/r_workshop/Rmd.Figures/plot_chick-1.png)



Save the resulting plot as a standalone figure

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot

## Manipulating data

Let's manipulate the data using the tidyverse

#### Add a column `mutate()`
Create a new column for individual chick ID

```r
chickwts <- chickwts %>%
  mutate(chick_id=seq(1:length(weight)),
         dummy_variable=2+1)
chickwts
```

```
   weight      feed chick_id dummy_variable
1     179 horsebean        1              3
2     160 horsebean        2              3
3     136 horsebean        3              3
4     227 horsebean        4              3
5     217 horsebean        5              3
6     168 horsebean        6              3
7     108 horsebean        7              3
8     124 horsebean        8              3
9     143 horsebean        9              3
10    140 horsebean       10              3
11    309   linseed       11              3
12    229   linseed       12              3
13    181   linseed       13              3
14    141   linseed       14              3
15    260   linseed       15              3
16    203   linseed       16              3
17    148   linseed       17              3
18    169   linseed       18              3
19    213   linseed       19              3
20    257   linseed       20              3
21    244   linseed       21              3
22    271   linseed       22              3
23    243   soybean       23              3
24    230   soybean       24              3
25    248   soybean       25              3
26    327   soybean       26              3
27    329   soybean       27              3
28    250   soybean       28              3
29    193   soybean       29              3
30    271   soybean       30              3
31    316   soybean       31              3
32    267   soybean       32              3
33    199   soybean       33              3
34    171   soybean       34              3
35    158   soybean       35              3
36    248   soybean       36              3
37    423 sunflower       37              3
38    340 sunflower       38              3
39    392 sunflower       39              3
40    339 sunflower       40              3
41    341 sunflower       41              3
42    226 sunflower       42              3
43    320 sunflower       43              3
44    295 sunflower       44              3
45    334 sunflower       45              3
46    322 sunflower       46              3
47    297 sunflower       47              3
48    318 sunflower       48              3
49    325  meatmeal       49              3
50    257  meatmeal       50              3
51    303  meatmeal       51              3
52    315  meatmeal       52              3
53    380  meatmeal       53              3
54    153  meatmeal       54              3
55    263  meatmeal       55              3
56    242  meatmeal       56              3
57    206  meatmeal       57              3
58    344  meatmeal       58              3
59    258  meatmeal       59              3
60    368    casein       60              3
61    390    casein       61              3
62    379    casein       62              3
63    260    casein       63              3
64    404    casein       64              3
65    318    casein       65              3
66    352    casein       66              3
67    359    casein       67              3
68    216    casein       68              3
69    222    casein       69              3
70    283    casein       70              3
71    332    casein       71              3
```

#### Filter data `filter()`
Subset data only to the feed types that are animal based

```r
chickwts_animal <- chickwts %>%
  filter(feed %in% c("casein", "meatmeal"))

chickwts_soy <- chickwts %>%
  filter(feed =="soybean")

#Remove the chickwts_soy
rm(chickwts_soy)
#rm(list=ls()) #remove all things in global environment
```

#### Rename columns
I can change the column name this way

```r
chickwts <- chickwts %>%
  rename(chick_num = chick_id)
```

#### Re-assigning values within a column
I can use `ifelse()` to reclassify values within a column

```r
chickwts <- chickwts %>%
  mutate(size_class=ifelse(weight > 250, "Large", "Small"))
chickwts
```

```
   weight      feed chick_num dummy_variable size_class
1     179 horsebean         1              3      Small
2     160 horsebean         2              3      Small
3     136 horsebean         3              3      Small
4     227 horsebean         4              3      Small
5     217 horsebean         5              3      Small
6     168 horsebean         6              3      Small
7     108 horsebean         7              3      Small
8     124 horsebean         8              3      Small
9     143 horsebean         9              3      Small
10    140 horsebean        10              3      Small
11    309   linseed        11              3      Large
12    229   linseed        12              3      Small
13    181   linseed        13              3      Small
14    141   linseed        14              3      Small
15    260   linseed        15              3      Large
16    203   linseed        16              3      Small
17    148   linseed        17              3      Small
18    169   linseed        18              3      Small
19    213   linseed        19              3      Small
20    257   linseed        20              3      Large
21    244   linseed        21              3      Small
22    271   linseed        22              3      Large
23    243   soybean        23              3      Small
24    230   soybean        24              3      Small
25    248   soybean        25              3      Small
26    327   soybean        26              3      Large
27    329   soybean        27              3      Large
28    250   soybean        28              3      Small
29    193   soybean        29              3      Small
30    271   soybean        30              3      Large
31    316   soybean        31              3      Large
32    267   soybean        32              3      Large
33    199   soybean        33              3      Small
34    171   soybean        34              3      Small
35    158   soybean        35              3      Small
36    248   soybean        36              3      Small
37    423 sunflower        37              3      Large
38    340 sunflower        38              3      Large
39    392 sunflower        39              3      Large
40    339 sunflower        40              3      Large
41    341 sunflower        41              3      Large
42    226 sunflower        42              3      Small
43    320 sunflower        43              3      Large
44    295 sunflower        44              3      Large
45    334 sunflower        45              3      Large
46    322 sunflower        46              3      Large
47    297 sunflower        47              3      Large
48    318 sunflower        48              3      Large
49    325  meatmeal        49              3      Large
50    257  meatmeal        50              3      Large
51    303  meatmeal        51              3      Large
52    315  meatmeal        52              3      Large
53    380  meatmeal        53              3      Large
54    153  meatmeal        54              3      Small
55    263  meatmeal        55              3      Large
56    242  meatmeal        56              3      Small
57    206  meatmeal        57              3      Small
58    344  meatmeal        58              3      Large
59    258  meatmeal        59              3      Large
60    368    casein        60              3      Large
61    390    casein        61              3      Large
62    379    casein        62              3      Large
63    260    casein        63              3      Large
64    404    casein        64              3      Large
65    318    casein        65              3      Large
66    352    casein        66              3      Large
67    359    casein        67              3      Large
68    216    casein        68              3      Small
69    222    casein        69              3      Small
70    283    casein        70              3      Large
71    332    casein        71              3      Large
```


#### Summarize `summarize()` with `group_by()`

```r
chickwts_summ <- chickwts %>%
  group_by(feed) %>%
  summarize(mean_wt=mean(weight), #mean weight on feed type
            sd_wt=sd(weight), #standard deviation of feed type
            n_chicks=n()) #number of chicks raised on each data

chickwts_summ
```

```
# A tibble: 6 × 4
  feed      mean_wt sd_wt n_chicks
  <fct>       <dbl> <dbl>    <int>
1 casein       324.  64.4       12
2 horsebean    160.  38.6       10
3 linseed      219.  52.2       12
4 meatmeal     277.  64.9       11
5 soybean      246.  54.1       14
6 sunflower    329.  48.8       12
```

## Overlaying graphs of data from two datasets

With ggplot we can do this pretty easily by specifying the dataset not in the ggplot call, but in the specific call for the geometry. 
For example we can plot the raw data from chickwts as points, then overlay the mean and standard deviation from the summary dataset as a point with errorbar.

```r
ggplot()+
  geom_point(data=chickwts, 
             aes(x=feed, y=weight, pch=size_class))+ #use pch=sizeclass to plot a different symbol for whether the chick is large or small
  geom_point(data=chickwts_summ, 
             aes(x=feed, y=mean_wt), 
             col="red", pch=1, cex=4)+ #here I specify color is red, type of symbol is pch=1, and make it bigger than the raw data points by expanding it cex=4
  geom_errorbar(data=chickwts_summ, 
                aes(x=feed, ymin=mean_wt - sd_wt, ymax=mean_wt+sd_wt),
                width=0.2, col="red") #make it red, and make the width more narrow than the default
```

![](/Users/rs5/Documents/WellesleyResearch/r_workshop/Rmd.Figures/overlay graphs-1.png)<!-- -->

In this one I also didn't specify the height or width for the figure in the html. Compare with the first graph

## Merging Data

For this we'll use the datasets within the tidyverse on a very small subset of rock bandmembers `band_members` and their instruments `band_instruments`


```r
band_members
```

```
# A tibble: 3 × 2
  name  band   
  <chr> <chr>  
1 Mick  Stones 
2 John  Beatles
3 Paul  Beatles
```

```r
band_instruments
```

```
# A tibble: 3 × 2
  name  plays 
  <chr> <chr> 
1 John  guitar
2 Paul  bass  
3 Keith guitar
```

```r
band_instruments2
```

```
# A tibble: 3 × 2
  artist plays 
  <chr>  <chr> 
1 John   guitar
2 Paul   bass  
3 Keith  guitar
```

We can see that `band_members` includes Mick Jagger from the Rolling Stones and two members of the Beatles (John and Paul). We also see that `band_instruments` gives the instrument played by those two Beatles, plus a different member of the Stones. `band_instruments2` has the same data but uses `artist` as the column name rather than `name` 

The generic pseudocode for these merges is
`new_df <- join(df1, df2)`
First, let's discuss the difference between the types of joins.
- `inner_join(df1, df2)` only keeps the rows that are present in both df1 and df2.  
- `left_join(df1, df2)` keeps all the rows in df1 (the L one in the argument), even if they don't appear in df2, but only keep the ones in df2 that are in df1.  
- `right_join(df1, df2)` keeps all the rows in df2 (the R one in the argument), even if they don't appear in df1, but only keep the ones in df1 that are in df2.  
- `full_join(df1, df2)` keeps all rows in both df1 and df2.  

Let's try these different types of joins:

```r
inner_join(band_members, band_instruments)
```

```
Joining with `by = join_by(name)`
```

```
# A tibble: 2 × 3
  name  band    plays 
  <chr> <chr>   <chr> 
1 John  Beatles guitar
2 Paul  Beatles bass  
```


```r
left_join(band_members, band_instruments)
```

```
Joining with `by = join_by(name)`
```

```
# A tibble: 3 × 3
  name  band    plays 
  <chr> <chr>   <chr> 
1 Mick  Stones  <NA>  
2 John  Beatles guitar
3 Paul  Beatles bass  
```


```r
right_join(band_members, band_instruments)
```

```
Joining with `by = join_by(name)`
```

```
# A tibble: 3 × 3
  name  band    plays 
  <chr> <chr>   <chr> 
1 John  Beatles guitar
2 Paul  Beatles bass  
3 Keith <NA>    guitar
```


```r
full_join(band_members, band_instruments)
```

```
Joining with `by = join_by(name)`
```

```
# A tibble: 4 × 3
  name  band    plays 
  <chr> <chr>   <chr> 
1 Mick  Stones  <NA>  
2 John  Beatles guitar
3 Paul  Beatles bass  
4 Keith <NA>    guitar
```

How do I merge tables that don't share a column name?
Answer: use the by argument. The one that is kept is the `name`

```r
inner_join(band_members, band_instruments2, by=c("name"="artist"))
```

```
# A tibble: 2 × 3
  name  band    plays 
  <chr> <chr>   <chr> 
1 John  Beatles guitar
2 Paul  Beatles bass  
```



