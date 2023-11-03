Business Intelligence Project
================
<Champions>
\<08/10/2023\>

- [Student Details](#student-details)
- [Setup Chunk](#setup-chunk)
- [Step 1 .Install and load all the
  packages](#step-1-install-and-load-all-the-packages)
- [Step 2. Load the dataset](#step-2-load-the-dataset)
- [Step 3. The Resamples Function](#step-3-the-resamples-function)
  - [3.a.Train the models](#3atrain-the-models)
  - [3.b. Call the `resamples`
    Function](#3b-call-the-resamples-function)
- [STEP 4. Display the Results](#step-4-display-the-results)
  - [1. Table Summary](#1-table-summary)
  - [2. Box and Whisker Plot](#2-box-and-whisker-plot)
  - [3. Dot Plots](#3-dot-plots)
  - [4. Scatter Plot Matrix](#4-scatter-plot-matrix)
  - [5. Pairwise xyPlots](#5-pairwise-xyplots)
    - [or](#or)
  - [6. Statistical Significance
    Tests](#6-statistical-significance-tests)
    - [Upper Diagonal](#upper-diagonal)
    - [Lower Diagonal](#lower-diagonal)

# Student Details

<table style="width:99%;">
<colgroup>
<col style="width: 65%" />
<col style="width: 33%" />
</colgroup>
<tbody>
<tr class="odd">
<td><strong>Student ID Number</strong></td>
<td><p>134111</p>
<p>133996</p>
<p>126761</p>
<p>135859</p>
<p>127707</p></td>
</tr>
<tr class="even">
<td><strong>Student Name</strong></td>
<td><p>Juma Immaculate Haayo</p>
<p>Trevor Ngugi</p>
<p>Virginia Wanjiru</p>
<p>Pauline Wang’ombe</p>
<p>Clarice Gitonga</p></td>
</tr>
<tr class="odd">
<td><strong>BBIT 4.2 Group</strong></td>
<td>B</td>
</tr>
<tr class="even">
<td><strong>BI Project Group Name/ID (if applicable)</strong></td>
<td>Champions</td>
</tr>
</tbody>
</table>

# Setup Chunk

**Note:** the following KnitR options have been set as the global
defaults: <BR>
`knitr::opts_chunk$set(echo = TRUE, warning = FALSE, eval = TRUE, collapse = FALSE, tidy = TRUE)`.

More KnitR options are documented here
<https://bookdown.org/yihui/rmarkdown-cookbook/chunk-options.html> and
here <https://yihui.org/knitr/options/>.

# Step 1 .Install and load all the packages

We installed all the packages that will enable us execute this lab.

``` r
## mlbench ----
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: mlbench

``` r
## caret ----
if (require("caret")) {
  require("caret")
} else {
  install.packages("caret", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: caret

    ## Loading required package: ggplot2

    ## Loading required package: lattice

``` r
## kernlab ----
if (require("kernlab")) {
  require("kernlab")
} else {
  install.packages("kernlab", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: kernlab

    ## 
    ## Attaching package: 'kernlab'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     alpha

``` r
## randomForest ----
if (require("randomForest")) {
  require("randomForest")
} else {
  install.packages("randomForest", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}
```

    ## Loading required package: randomForest

    ## randomForest 4.7-1.1

    ## Type rfNews() to see new features/changes/bug fixes.

    ## 
    ## Attaching package: 'randomForest'

    ## The following object is masked from 'package:ggplot2':
    ## 
    ##     margin

# Step 2. Load the dataset

We loaded the Glass dataset which uses the mlbench package

``` r
if (require("mlbench")) {
  require("mlbench")
} else {
  install.packages("mlbench", dependencies = TRUE,
                   repos = "https://cloud.r-project.org")
}

data("Glass")
```

# Step 3. The Resamples Function

The “resamples()” function checks that the models are comparable and
that they used the same training scheme (“train_control” configuration).
To do this, after the models are trained, they are added to a list and
we pass this list of models as an argument to the resamples() function
in R.

## 3.a.Train the models

We train the following models, all of which are using 10-fold repeated
cross validation with 3 repeats: LDA CART KNN SVM Random Forest

``` r
train_control <- trainControl(method = "repeatedcv", number = 10, repeats = 3)

### LDA ----
set.seed(7)
type_model_lda <- train(Type ~ ., data = Glass,
                            method = "lda", trControl = train_control)

### CART ----
set.seed(7)
type_model_cart <- train(Type ~ ., data = Glass,
                             method = "rpart", trControl = train_control)

### KNN ----
set.seed(7)
type_model_knn <- train(Type ~ ., data = Glass,
                            method = "knn", trControl = train_control)

### SVM ----
set.seed(7)
type_model_svm <- train(Type ~ ., data = Glass,
                            method = "svmRadial", trControl = train_control)

### Random Forest ----
set.seed(7)
type_model_rf <- train(Type ~ ., data = Glass,
                           method = "rf", trControl = train_control)
```

## 3.b. Call the `resamples` Function

We then create a list of the model results and pass the list as an
argument to the `resamples` function.

``` r
results <- resamples(list(LDA = type_model_lda, CART = type_model_cart,
                          KNN = type_model_knn, SVM = type_model_svm,
                          RF = type_model_rf))
```

# STEP 4. Display the Results

## 1. Table Summary

It creates a table with one model per row and its corresponding
evaluation metrics displayed per column

``` r
summary(results)
```

    ## 
    ## Call:
    ## summary.resamples(object = results)
    ## 
    ## Models: LDA, CART, KNN, SVM, RF 
    ## Number of resamples: 30 
    ## 
    ## Accuracy 
    ##           Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
    ## LDA  0.3636364 0.5454545 0.6277056 0.6212071 0.6916667 0.9047619    0
    ## CART 0.4545455 0.6190476 0.6363636 0.6493475 0.7142857 0.8181818    0
    ## KNN  0.3809524 0.6190476 0.6742424 0.6672596 0.7443182 0.9047619    0
    ## SVM  0.4761905 0.6363636 0.6666667 0.6783581 0.7142857 0.8571429    0
    ## RF   0.6521739 0.7727273 0.8095238 0.7995313 0.8420455 0.9090909    0
    ## 
    ## Kappa 
    ##           Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
    ## LDA  0.1561644 0.3659318 0.4813037 0.4706998 0.5636423 0.8636364    0
    ## CART 0.1975684 0.4421330 0.4962492 0.4978139 0.5775276 0.7341390    0
    ## KNN  0.1574074 0.4631516 0.5446795 0.5395413 0.6337050 0.8707692    0
    ## SVM  0.2300000 0.4743722 0.5234895 0.5362700 0.5971656 0.7980769    0
    ## RF   0.5183246 0.6820494 0.7345965 0.7215597 0.7735402 0.8770950    0

## 2. Box and Whisker Plot

Is useful for visually observing the spread of the estimated accuracies
for different algorithms and how they relate.

``` r
scales <- list(x = list(relation = "free"), y = list(relation = "free"))
bwplot(results, scales = scales)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%206th%20Code%20Chunk-1.png)<!-- -->

## 3. Dot Plots

They show both the mean estimated accuracy as well as the 95% confidence
interval (e.g. the range in which 95% of observed scores fell).

``` r
scales <- list(x = list(relation = "free"), y = list(relation = "free"))
bwplot(results, scales = scales)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%207th%20Code%20Chunk-1.png)<!-- -->

## 4. Scatter Plot Matrix

This is useful when considering whether the predictions from two
different algorithms are correlated. If weakly correlated, then they are
good candidates for being combined in an ensemble prediction.

``` r
splom(results)
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%208th%20Code%20Chunk-1.png)<!-- -->

## 5. Pairwise xyPlots

You can zoom in on one pairwise comparison of the accuracy of
trial-folds for two models using an xyplot.

xyplot plots to compare models

``` r
xyplot(results, models = c("LDA", "SVM"))
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%209th%20Code%20Chunk-1.png)<!-- -->

### or

``` r
xyplot(results, models = c("SVM", "CART"))
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%2010th%20Code%20Chunk-1.png)<!-- -->

## 6. Statistical Significance Tests

Calculates the significance of the differences between the metric
distributions of the various models.

### Upper Diagonal

Shows the estimated difference between the distributions

### Lower Diagonal

Contains p-values of the null hypothesis. The null hypothesis is a claim
that “the distributions are the same”. A lower p-value is better (more
significant).

``` r
xyplot(results, models = c("SVM", "CART"))
```

![](Lab8-Submission-ModelPerformanceComparison_files/figure-gfm/Your%2011th%20Code%20Chunk-1.png)<!-- -->
