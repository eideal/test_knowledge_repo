---
title: Personality Research Project
authors:
- Boris Yanovsky
tags:
- plotly
- personality
created_at: 2017-03-07 00:00:00
updated_at: 2017-03-07 09:31:55.207851
tldr: Boris presented on Rmd with plotly at our team meeting
---
**Contents**

[TOC]



Let's pretend we were lucky enough to collect personality data from 2,800 willing participants measuring the **Big Five** traits with 25 items. In actuality, this data comes loaded in the R package `psych` (along with many other interesting datasets), but let's pretend like we collected it for this vignette. For more information on the **Big Five** personality traits, see @goldberg.

Our colleague just shared the data with us, so let's take a look at what we have and become familiar with the dataset before jumping into the research project.

## Initial Data Exploration

Here we will load up the package `psych`, which has many useful functions that we'll be using. The package also contains our data, so we will load that as well.


```r
library(psych)
data(bfi)
```

Probably the first thing you would want to do is figure out what kind of data we have! It would help to know the variables included and maybe even some descriptives. Let's run a function that will compute this for us. We will keep every bit of code that we run in this document so that our colleagues can see exactly what we did, and are able to re-run and replicate our analyses.


```r
describe(bfi, fast = TRUE)
```

```
##           vars    n  mean    sd min max range   se
## A1           1 2784  2.41  1.41   1   6     5 0.03
## A2           2 2773  4.80  1.17   1   6     5 0.02
## A3           3 2774  4.60  1.30   1   6     5 0.02
## A4           4 2781  4.70  1.48   1   6     5 0.03
## A5           5 2784  4.56  1.26   1   6     5 0.02
## C1           6 2779  4.50  1.24   1   6     5 0.02
## C2           7 2776  4.37  1.32   1   6     5 0.03
## C3           8 2780  4.30  1.29   1   6     5 0.02
## C4           9 2774  2.55  1.38   1   6     5 0.03
## C5          10 2784  3.30  1.63   1   6     5 0.03
## E1          11 2777  2.97  1.63   1   6     5 0.03
## E2          12 2784  3.14  1.61   1   6     5 0.03
## E3          13 2775  4.00  1.35   1   6     5 0.03
## E4          14 2791  4.42  1.46   1   6     5 0.03
## E5          15 2779  4.42  1.33   1   6     5 0.03
## N1          16 2778  2.93  1.57   1   6     5 0.03
## N2          17 2779  3.51  1.53   1   6     5 0.03
## N3          18 2789  3.22  1.60   1   6     5 0.03
## N4          19 2764  3.19  1.57   1   6     5 0.03
## N5          20 2771  2.97  1.62   1   6     5 0.03
## O1          21 2778  4.82  1.13   1   6     5 0.02
## O2          22 2800  2.71  1.57   1   6     5 0.03
## O3          23 2772  4.44  1.22   1   6     5 0.02
## O4          24 2786  4.89  1.22   1   6     5 0.02
## O5          25 2780  2.49  1.33   1   6     5 0.03
## gender      26 2800  1.67  0.47   1   2     1 0.01
## education   27 2577  3.19  1.11   1   5     4 0.02
## age         28 2800 28.78 11.13   3  86    83 0.21
```

This shows us the variable list along with some descriptives. We can see the 25 **Big Five** items, plus a few demographics as well. After referring to our data dictionary, we can see that items *A1-A5* measure *Agreeableness*, *C1-C5* measure *Conscientiousness*, *E1-E5* measure *Extraversion*, *N1-N5* measure *Neuroticism*, and *O1-O5* are *Openness* items.


```r
library(plotly)
r = cor(bfi[,1:25], use="complete.obs")
plot_ly(z = r, x=row.names(r), y=row.names(r), colorscale = "Hot", type = "heatmap")
```

```
## Error in loadNamespace(name): there is no package called 'webshot'
```

Overall, it looks like the within scale correlations are pretty high and items in the same scale seems more correlated with each other than they do with other scales. Except for the Openness scale. Let's take a closer look by getting some scale performance  information through a reliability analysis.

In addition to the output we've seen above, you can also get r to print values for you inline with your text. The coefficient alpha values below are computed inline so as not to break up the flow of the document. Alpha value for *Agreeableness* is 0.7030184, *Conscientiousness* alpha is 0.726735, *Extraversion* is 0.7617328, *Neuroticism* alpha is 0.8139629, and *Openness* is 0.6001725.

These items don't appear to be performing as well as we'd hoped, but this is the data we have so we will try to make the best of it. *Neuroticism* does meet the conventional cutoff of $a>.80$ and this is one of the personality dimensions we're most interested in studying.

## Our Research Project

For the current project, we've been curious about investigating differences in personality between men and women. Of particular interest is how men and women differ on the dimension of *Neuroticism*. To perform some basic analyses and get a better understanding of the relationship that gender has with the personality sub-dimension of *Neuroticism*, we will perform the following steps and share the results with our colleagues:

    1. Compute scale score.
    2. Examine means.
    3. Perform inferrential statistics.
    4. Plot the data for some further exploring.

With each step, we can make notes for our colleagues (and ourselves!) to provide interpretations, commentary, and notes on what we are doing.

### 1. Scale Score for Neuroticism

For this analysis, we decide to simply average all the items in the scale to create our scale score. Below we compute a new variable called `neur` and then print the descriptives for the dataset again, which now includes our new scale score.


```r
bfi$neur = rowMeans(bfi[,c("N1","N2","N3","N4","N5")], na.rm = T)
describe(bfi, fast = TRUE)
```

```
##           vars    n  mean    sd min max range   se
## A1           1 2784  2.41  1.41   1   6     5 0.03
## A2           2 2773  4.80  1.17   1   6     5 0.02
## A3           3 2774  4.60  1.30   1   6     5 0.02
## A4           4 2781  4.70  1.48   1   6     5 0.03
## A5           5 2784  4.56  1.26   1   6     5 0.02
## C1           6 2779  4.50  1.24   1   6     5 0.02
## C2           7 2776  4.37  1.32   1   6     5 0.03
## C3           8 2780  4.30  1.29   1   6     5 0.02
## C4           9 2774  2.55  1.38   1   6     5 0.03
## C5          10 2784  3.30  1.63   1   6     5 0.03
## E1          11 2777  2.97  1.63   1   6     5 0.03
## E2          12 2784  3.14  1.61   1   6     5 0.03
## E3          13 2775  4.00  1.35   1   6     5 0.03
## E4          14 2791  4.42  1.46   1   6     5 0.03
## E5          15 2779  4.42  1.33   1   6     5 0.03
## N1          16 2778  2.93  1.57   1   6     5 0.03
## N2          17 2779  3.51  1.53   1   6     5 0.03
## N3          18 2789  3.22  1.60   1   6     5 0.03
## N4          19 2764  3.19  1.57   1   6     5 0.03
## N5          20 2771  2.97  1.62   1   6     5 0.03
## O1          21 2778  4.82  1.13   1   6     5 0.02
## O2          22 2800  2.71  1.57   1   6     5 0.03
## O3          23 2772  4.44  1.22   1   6     5 0.02
## O4          24 2786  4.89  1.22   1   6     5 0.02
## O5          25 2780  2.49  1.33   1   6     5 0.03
## gender      26 2800  1.67  0.47   1   2     1 0.01
## education   27 2577  3.19  1.11   1   5     4 0.02
## age         28 2800 28.78 11.13   3  86    83 0.21
## neur        29 2800  3.16  1.20   1   6     5 0.02
```

Now we have our *Neuroticism* scale score. It has an average of 3.1622679 and a standard deviation of 1.1963314.

### 2. Compare Group Means

Now that we have our scale computed, let's see the average score for men and women. The `describeBy` function in the `psych` package can compute descriptives across a grouping variable.


```r
describeBy(bfi$neur, bfi$gender, fast = T)
```

```
## $`1`
##    vars   n mean   sd min max range   se
## X1    1 919 2.95 1.14   1   6     5 0.04
## 
## $`2`
##    vars    n mean   sd min max range   se
## X1    1 1881 3.27 1.21   1   6     5 0.03
## 
## attr(,"call")
## by.default(data = x, INDICES = group, FUN = describe, type = type, 
##     fast = ..1)
```

In our dataset, males are coded as 1 and females as 2. The means certainly are different, but let's see if this is significant and get some results down on paper to share with others.

### 3. Statistical Analysis

Using a simple t-test for this analysis, we compare group mean differences between males and females on the *Neuroticism* **Big Five** personality trait. 


```r
test1 = t.test(neur~gender, data=bfi)
test1
```

```
## 
## 	Welch Two Sample t-test
## 
## data:  neur by gender
## t = -6.7273, df = 1912.9, p-value = 2.276e-11
## alternative hypothesis: true difference in means is not equal to 0
## 95 percent confidence interval:
##  -0.4075341 -0.2235531
## sample estimates:
## mean in group 1 mean in group 2 
##        2.950290        3.265834
```

Our results show that males ($M=2.95$) rate themselves signifcantly lower on this trait than do females ($M=3.27$).

### 4. Some Further Exploring

Well we've achieved the Holy Grail of research and found statistical significance! Men have significantly lower *Neuroticism* trait levels than do women. But why stop here? This by itself isn't all that interesting to us and we want to find out more about these gender differences.

We decide to visually examine mean group differences on all the other **Big Five** traits between men and women. To do so, we must first compute the scale scores for the remaining scales. After consulting our data dictionary, we learn that some items are negatively worded in the remaining scales and must be reverse scored. Forgetting to reverse score items is an easy mistake to make so documenting your work in Markdown is a great way to have others check your work.


```r
bfi[,c("A1","A2","A3","A4","A5")] = reverse.code(c(-1,1,1,1,1), bfi[,c("A1","A2","A3","A4","A5")])
bfi[,c("C1","C2","C3","C4","C5")] = reverse.code(c(1,1,1,-1,-1), bfi[,c("C1","C2","C3","C4","C5")])
bfi[,c("E1","E2","E3","E4","E5")] = reverse.code(c(-1,-1,1,1,1), bfi[,c("E1","E2","E3","E4","E5")])
bfi[,c("O1","O2","O3","O4","O5")] = reverse.code(c(1,-1,1,1,-1), bfi[,c("O1","O2","O3","O4","O5")])
```

Now that the necessary items have been reverse scored, let's compute the remaining scale scores.


```r
bfi$agree = rowMeans(bfi[,c("A1","A2","A3","A4","A5")], na.rm = T)
bfi$con = rowMeans(bfi[,c("C1","C2","C3","C4","C5")], na.rm = T)
bfi$extra = rowMeans(bfi[,c("E1","E2","E3","E4","E5")], na.rm = T)
bfi$open = rowMeans(bfi[,c("O1","O2","O3","O4","O5")], na.rm = T)
```

Before we can plot the data to see group differences, we need to do a little data massaging and restructure our dataset. All we are doing here is restructuring the data from a "wide" to "long" format.


```r
library(reshape2)
for_graph = melt(bfi[,c("agree", "con", "extra", "open", "neur", "gender")], id.vars=c("gender"))
```

Alright, now let's take a look at these group differences by running some boxplots.


```r
plot_ly(for_graph, x = for_graph$variable, y = for_graph$value, color = as.factor(for_graph$gender), type = "box") %>%
  layout(boxmode = "group")
```

```
## Error in loadNamespace(name): there is no package called 'webshot'
```

Here we can see a little bit of a broader picture of some personality differences between males and females. After all this hard work, we are now ready to share our results with our colleagues. We can simply send them this Markdown file with the data, which includes the code and our notes, to get their input into this project.
