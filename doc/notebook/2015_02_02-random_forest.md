# Random Forest Modeling


**Purpose:** We want to model the number of tumors based on the microbiome using
regression via random forests


### Setup


```r
library(randomForest, quietly=TRUE)
```

```
## randomForest 4.6-10
## Type rfNews() to see new features/changes/bug fixes.
```

```r
library(knitr, quietly=TRUE)
sessionInfo()

set.seed(6201976)
source("code/rf_baseline_analysis.R")
```

```
## R version 3.1.2 (2014-10-31)
## Platform: x86_64-unknown-linux-gnu (64-bit)
## 
## locale:
##  [1] LC_CTYPE=en_US.UTF-8       LC_NUMERIC=C              
##  [3] LC_TIME=en_US.UTF-8        LC_COLLATE=en_US.UTF-8    
##  [5] LC_MONETARY=en_US.UTF-8    LC_MESSAGES=en_US.UTF-8   
##  [7] LC_PAPER=en_US.UTF-8       LC_NAME=C                 
##  [9] LC_ADDRESS=C               LC_TELEPHONE=C            
## [11] LC_MEASUREMENT=en_US.UTF-8 LC_IDENTIFICATION=C       
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] randomForest_4.6-10 knitr_1.8          
## 
## loaded via a namespace (and not attached):
## [1] evaluate_0.5.5 formatR_1.0    stringr_0.6.2  tools_3.1.2
```


```r
opts_chunk$set(dev = c("png", "pdf"))
opts_chunk$set(results = "hold")
opts_chunk$set(fig.show = "hold")
opts_chunk$set(warning = FALSE)
opts_chunk$set(fig.align = "center")
opts_chunk$set(echo = FALSE)
opts_chunk$set(cache = FALSE)
```

Let's get the relative abundance of the OTUs in our samples at baseline (Day 0).
To do this, we will read in the shared file and convert the numbers to relative
abundances and get the tumor count data. Using the sample names we will also
extract the treatment, mouse eartag number, and the day of the experiment.




### Find the best forest

One question is how best to filter the OTU data. Let's see what happens when we
alter the minimum average relative abundance across all samples:

<img src="results/figures/filter_test-1.png" title="plot of chunk filter_test" alt="plot of chunk filter_test" style="display: block; margin: auto;" />

From this plot, it appears that the best threshold would be
0 (Rsq=0.52) and this would
give us 645 OTUs. Let's press on and make sure we're
using a sufficient number of trees with our threshold:

<img src="results/figures/ntrees_test-1.png" title="plot of chunk ntrees_test" alt="plot of chunk ntrees_test" style="display: block; margin: auto;" />

Looks like the model has stabilized at 10<sup>4</sup> trees. Let's see the
importance plots:

<img src="results/figures/importance_plot-1.png" title="plot of chunk importance_plot" alt="plot of chunk importance_plot" style="display: block; margin: auto;" />

Inspection of the Gini coefficient plot (right side of above plot) suggests that
there are three OTUs that stick out as being highly informative, followed by an
additional three OTUs that are also informative.


### Simplify the model

Let's sort the OTUs by their Gini coefficient and build models where we add each
OTU in a stepwise manner.


<img src="results/figures/simplify_model-1.png" title="plot of chunk simplify_model" alt="plot of chunk simplify_model" style="display: block; margin: auto;" />

Thus, we see that the model does pretty well with just a few parameters.


### Fit data to model

Let's compare the number of predicted tumors to the number of observed tumors
based on the full model.

<img src="results/figures/model_fit-1.png" title="plot of chunk model_fit" alt="plot of chunk model_fit" style="display: block; margin: auto;" />

Pretty sweet!


### Plot the top six features with tumor counts

Let's generate a plot with the number of tumors on the y-axis and the OTU's
relative abundance on the x-axis:


```
## Error in rownames(sorted_importance): object 'sorted_importance' not found
```

These plots highlight a few important results:
* Anaeroplasma, Porphyromonadaceae, and Prevotella increase with tumor count
* Enterobacteriaceae, Ureaplasma, and Lactobacillus decrease with tumor count
