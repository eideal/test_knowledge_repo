---
title: Pulse Key Driver Analysis
authors:
- Eric O'Rourke
- wesley_wisdom
tags:
- knowledge
- pulse
- kda
created_at: 2017-02-08 00:00:00
updated_at: 2017-02-08 18:02:59.402030
tldr: We found that work you enjoy and autonomy are drivers for attrition.
thumbnail: images/unnamed-chunk-1-1.png
---

**Contents**

[TOC]

### Executive Summary

Here is the summary of the work we've done. 

### 2016_Q3 Results

Here is some code to make a plot:


```r
plot(density(runif(100)), lwd=2, main="100 uniforms")
abline(h=0, v=0)
```

![plot of chunk unnamed-chunk-1](images/unnamed-chunk-1-1.png)


### Key Takeaways

What we learned is:

 - There should be one plot display call in each R block. The add_knowledge script currently gets confused if there are multiple plots in a given block:


```r
  ### My Header (treated as R comment, as it is in a code block)
  library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.3.2
```

```r
  test_data <- data.frame(y1 = rnorm(100), y2 = runif(100), x = 1:100)
  ggplot(test_data, aes(y = y1, x = x)) + geom_line()
```

![plot of chunk unnamed-chunk-2](images/unnamed-chunk-2-1.png)

```r
  ggplot(test_data, aes(y = y2, x = x)) + geom_line()
```

![plot of chunk unnamed-chunk-2](images/unnamed-chunk-2-2.png)

 - Do not put markdown headers in R blocks. The knitr code will interpret the header #'s as R comments. This means that they will either be rendered properly as R comments within the block, or left out entirely if echo=FALSE

### Appendix

Here is some more detail if you're interested:
