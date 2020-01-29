Working with Data
================

A tutorial for analysing and processing data using simple-to-use R tools

Table of contents
=================

-   [Getting started](#Getting-started)
    -   [Installing R](#Installing-R)
    -   [Installing packages for this tutorial](#Installing-packages-for-this-tutorial)
-   [Importing data into R](#Importing-data-into-R)
    -   [Retrieving path to your data file](#Retrieving-path-to-your-data-file)
    -   [Importing delimited text files](#Importing-delimited-text-files)
    -   [Importing excel files](#Importing-excel-files)
-   [Accessing data in dataframes](#Accessing-data-in-dataframes)
    -   [Viewing contents of dataframe](#Viewing-contents-of-dataframe)
    -   [Checking column/row names and lengths](#Checking-columnrow-names-and-lengths)
    -   [Subsetting dataframe](#Subsetting-dataframe)
-   [Transforming dataframes](#Transforming-dataframes)
    -   [The pipe (%&gt;%)](#The-pipe-(%%3E%))
    -   [Filter data](#Filter-data)
    -   [Transmute data](#Transmute-data)
    -   [Reformating columns](##Reformating-columns)
    -   [Spread/gather](#Spreadgather)

Getting started
---------------

### Installing R

To begin this tutorial, you need to have R installed on your computer. You may download the latest R version so from [CRAN](https://cloud.r-project.org/).

Optionally, you may enhance your R experience by utilizing Rstudio, an easy to use software to run and write R codes. Rstudio can be downloaded from [here](https://rstudio.com/products/rstudio/).

### Installing packages for this tutorial

R contain a plethora of effecient and simple tools for data analysis. To install all the packages covered in this tutorial, open R in your terminal or from Rstudio and run the following:

``` r
install.packages("devtools")
devtools::install_github("fursham-h/wwgd")
```

Getting data into R
-------------------

Most biology data are structured as tables. It contain one or more column variables and an infinite number of rows listing the values of each variable. A data table can be saved as one of these file types;

-   generic text file: *.txt*
-   tab-separated values file: *.tsv*
-   comma-separated values file: *.csv*
-   excel file: *.xls or .xlsx*.

To work with these data files in R, we need to import it and convert into an R-friendly dataframe. Here we will describe how to get your data into R.

### Retrieving path to your data file

Before importing your data into R, you need to know the "file path" of your file. You can think of file path as an address in the file system, pointing to where your it is located on your computer system. For example, a file named "testfile.txt" that is located on your user desktop would have a file path that would look something like this:

    /Users/USERNAME/Desktop/testfile.txt

Retrieving your file path differs between operating systems. You may refer to these tips on how to copy file paths for [Mac](http://osxdaily.com/2015/11/05/copy-file-path-name-text-mac-os-x-finder/) and [Windows](https://www.pcworld.com/article/251406/windows-tips-copy-a-file-path-show-or-hide-extensions.html). Once your file path have been copied, you may paste it into a variable in R as such:

``` r
file_path <- "/PATH/TO/YOUR/FILE.txt"
```

A simpler and interactive way of retrieving a file path is possible using R's `file.choose` function. Try:

``` r
file_path <- file.choose()
```

For this part of the tutorial, we will retrieve the path of a sample *.txt* to be imported into R:

``` r
sample_file_path <- system.file("extdata", "expression_1.txt", package = "wwgd")

# inspect path
sample_file_path
```

    ## [1] "/usr/local/lib/R/site-library/wwgd/extdata/expression_1.txt"

### Importing delimited text files

To import *.txt*, *.csv* or *.tsv* files into R as a dataframe, I recommend using the `vroom` data import tool from [*vroom*](https://github.com/r-lib/vroom) package. This highly popular tool has plenty of handy features, including guessing of your file delimeter, guessing of your column type, importing of multiple files and most importantly, it is FAST. Simply run:

``` r
library(vroom)
sample_file <- vroom(sample_file_path)
```

Our dataframe is assigned to a variable named **sample\_file**. We can quickly inspect the dataframe using built-in `summary` function:

``` r
# get overview of the variables in the dataframe
summary(sample_file)
```

    ##  transcript_id           TpM       
    ##  Length:16          Min.   : 1.80  
    ##  Class :character   1st Qu.:15.32  
    ##  Mode  :character   Median :30.50  
    ##                     Mean   :27.62  
    ##                     3rd Qu.:38.80  
    ##                     Max.   :48.70

As seen above, our sample *expression\_1.txt* file contain 2 named columns; "transcript\_id" which contain character names of transcripts and "TpM" containing expression values. By default, `vroom` will assign the first row of the input table as column names and the remaining rows as values. If your data do not contain column names, you may prevent `vroom` from misreading your data by setting the **col\_names** argument to FALSE as such:

``` r
sample_file <- vroom(sample_file_path, col_names = FALSE)
```

For more information on how to use *vroom*, refer to [*vroom*](https://vroom.r-lib.org/articles/vroom.html)'s getting started page.

### Importing excel files

To import excel files, I recommend using the `read_excel` function from the [*readxl*](https://readxl.tidyverse.org/) package. Here's how to import an excel version of *expression\_1.txt* file into R:

``` r
library(readxl)

# get filepath
sample_file_path <- sample_file_path <- system.file("extdata", "expression_1.xlsx", package = "wwgd")

# import
sample_file <- read_excel(sample_file_path)

# inspect file summary
summary(sample_file)
```

    ##  transcript_id           TpM       
    ##  Length:16          Min.   : 1.80  
    ##  Class :character   1st Qu.:15.32  
    ##  Mode  :character   Median :30.50  
    ##                     Mean   :27.62  
    ##                     3rd Qu.:38.80  
    ##                     Max.   :48.70

By default, `read_excel` will import the first sheet of your excel file. It is possible to import other sheets by providing additional arguments to this function. Check out *readxl*'s overview page [here](https://readxl.tidyverse.org/).

Accessing data in dataframes
----------------------------

Now that your data is in R, we can start to inspect the dataframe and extract information from it. We will be using the **sample\_file** dataframe that was loaded [above](#Importing-excel-files).

### Viewing contents of dataframe

Your dataframe can be displayed by typing out the variable name that it was assigned to into R console:

``` r
sample_file
```

    ## # A tibble: 16 x 2
    ##    transcript_id        TpM
    ##    <chr>              <dbl>
    ##  1 ENSMUST00000172282  47.4
    ##  2 ENSMUST00000168988   1.8
    ##  3 ENSMUST00000165704  30.3
    ##  4 ENSMUST00000165153  10.3
    ##  5 ENSMUST00000099958  33  
    ##  6 ENSMUST00000057343  43.7
    ##  7 ENSMUST00000171599  30.5
    ##  8 ENSMUST00000168216  30.5
    ##  9 ENSMUST00000169483  38.6
    ## 10 ENSMUST00000095457  36.9
    ## 11 ENSMUST00000168683   7.8
    ## 12 ENSMUST00000169091  48.7
    ## 13 ENSMUST00000171216  39.4
    ## 14 ENSMUST00000165724  17  
    ## 15 ENSMUST00000164385   4.2
    ## 16 ENSMUST00000169580  21.9

In Rstudio, you may display an interactive dataframe by using the `View()` function:

``` r
View(sample_file)
```

You may display the top few or bottom few rows of your dataframe:

``` r
# Display top 6 (default) rows
head(sample_file)
```

    ## # A tibble: 6 x 2
    ##   transcript_id        TpM
    ##   <chr>              <dbl>
    ## 1 ENSMUST00000172282  47.4
    ## 2 ENSMUST00000168988   1.8
    ## 3 ENSMUST00000165704  30.3
    ## 4 ENSMUST00000165153  10.3
    ## 5 ENSMUST00000099958  33  
    ## 6 ENSMUST00000057343  43.7

``` r
# Display top 6 (default) rows
tail(sample_file)
```

    ## # A tibble: 6 x 2
    ##   transcript_id        TpM
    ##   <chr>              <dbl>
    ## 1 ENSMUST00000168683   7.8
    ## 2 ENSMUST00000169091  48.7
    ## 3 ENSMUST00000171216  39.4
    ## 4 ENSMUST00000165724  17  
    ## 5 ENSMUST00000164385   4.2
    ## 6 ENSMUST00000169580  21.9

### Checking column/row names and lengths

Column variable names can be easily viewed using the `names()` function:

``` r
names(sample_file)
```

    ## [1] "transcript_id" "TpM"

If the rows of your dataframe are named, you may view it using the `rownames()` function:

``` r
rownames(sample_file)
```

    ##  [1] "1"  "2"  "3"  "4"  "5"  "6"  "7"  "8"  "9"  "10" "11" "12" "13" "14" "15"
    ## [16] "16"

The dimensions of your dataframe can be inspected using several base functions:

``` r
# get row vs column dimension
dim(sample_file)
```

    ## [1] 16  2

``` r
# get number of rows
nrow(sample_file)
```

    ## [1] 16

``` r
# get number of columns
length(sample_file)
```

    ## [1] 2

### Subsetting dataframe

Subsetting is the process of retrieving certain parts of a dataframe. The columns and rows of a dataframe can be subsetted by specifying the column/row number that you wish to view. This number is also termed as "index" and in R, the first row/column has an index value of 1. The index is to be printed within square brackets infront of the dataframe variable name (e.g. `sample_file[1]`).

Let's see what happens when we print out the above example:

``` r
sample_file[1]
```

    ## # A tibble: 16 x 1
    ##    transcript_id     
    ##    <chr>             
    ##  1 ENSMUST00000172282
    ##  2 ENSMUST00000168988
    ##  3 ENSMUST00000165704
    ##  4 ENSMUST00000165153
    ##  5 ENSMUST00000099958
    ##  6 ENSMUST00000057343
    ##  7 ENSMUST00000171599
    ##  8 ENSMUST00000168216
    ##  9 ENSMUST00000169483
    ## 10 ENSMUST00000095457
    ## 11 ENSMUST00000168683
    ## 12 ENSMUST00000169091
    ## 13 ENSMUST00000171216
    ## 14 ENSMUST00000165724
    ## 15 ENSMUST00000164385
    ## 16 ENSMUST00000169580

The above code selects and prints the entire first column of our sample dataframe. You may create new dataframes by assigning the subsetted dataframe to a new variable:

``` r
sample_subset1 <- sample_file[1]
head(sample_subset1)
```

    ## # A tibble: 6 x 1
    ##   transcript_id     
    ##   <chr>             
    ## 1 ENSMUST00000172282
    ## 2 ENSMUST00000168988
    ## 3 ENSMUST00000165704
    ## 4 ENSMUST00000165153
    ## 5 ENSMUST00000099958
    ## 6 ENSMUST00000057343

How then do we select certain rows from our dataframe? For this, we have to type out the desired row-index, followed by a comma:

``` r
sample_file[1,]
```

    ## # A tibble: 1 x 2
    ##   transcript_id        TpM
    ##   <chr>              <dbl>
    ## 1 ENSMUST00000172282  47.4

Technically, the above code means that you are subsetting the dataframe **sample\_file** by \[**row-index , column-index**\]. If the row or column index is not stated, R will select the entire length of the row/column. Therefore, the code below yields the same output as `sample_file[1]`:

``` r
sample_file[,1]
```

    ## # A tibble: 16 x 1
    ##    transcript_id     
    ##    <chr>             
    ##  1 ENSMUST00000172282
    ##  2 ENSMUST00000168988
    ##  3 ENSMUST00000165704
    ##  4 ENSMUST00000165153
    ##  5 ENSMUST00000099958
    ##  6 ENSMUST00000057343
    ##  7 ENSMUST00000171599
    ##  8 ENSMUST00000168216
    ##  9 ENSMUST00000169483
    ## 10 ENSMUST00000095457
    ## 11 ENSMUST00000168683
    ## 12 ENSMUST00000169091
    ## 13 ENSMUST00000171216
    ## 14 ENSMUST00000165724
    ## 15 ENSMUST00000164385
    ## 16 ENSMUST00000169580

``` r
# 'identical' function checks if object A and B is identical
identical(sample_file[1], sample_file[,1])
```

    ## [1] TRUE

And specific values can be printed by providing both row,column indices:

``` r
sample_file[1,1]
```

    ## # A tibble: 1 x 1
    ##   transcript_id     
    ##   <chr>             
    ## 1 ENSMUST00000172282

Multiple rows can be returned by providing a range of indices. One way is to specify the start and end of the indices, separated by a ":" :

``` r
sample_file[1:6,]
```

    ## # A tibble: 6 x 2
    ##   transcript_id        TpM
    ##   <chr>              <dbl>
    ## 1 ENSMUST00000172282  47.4
    ## 2 ENSMUST00000168988   1.8
    ## 3 ENSMUST00000165704  30.3
    ## 4 ENSMUST00000165153  10.3
    ## 5 ENSMUST00000099958  33  
    ## 6 ENSMUST00000057343  43.7

``` r
# this is identical to head()
identical(head(sample_file), sample_file[1:6,])
```

    ## [1] TRUE

Another way of printing out multiple rows is by providing a list of row indices. In R, a list is an object which contains elements of different types of values. It is constructed by typing out the values separated by a comma, within a `c()` function. For instance:

``` r
# make a list with 4 integers
c(1,3,5,7)
```

    ## [1] 1 3 5 7

This list can be written within the square brackets to subset our dataframe:

``` r
sample_file[c(1,3,5,7),]
```

    ## # A tibble: 4 x 2
    ##   transcript_id        TpM
    ##   <chr>              <dbl>
    ## 1 ENSMUST00000172282  47.4
    ## 2 ENSMUST00000165704  30.3
    ## 3 ENSMUST00000099958  33  
    ## 4 ENSMUST00000171599  30.5

Transforming dataframes
-----------------------

After importing of data table into R's dataframe, you would want to filter/reshape/transmute your dataframe to better understand your data. Here we will be using simple tools from the popular *tidyverse* package to transform your data

``` r
#load tidyverse
library(tidyverse)
```

### The pipe (%&gt;%)

The `%>%` operator is a highly utilized tool in R. It allows users to write unclutterd and logical nested codes. For instance, if we would like sequentially `head()` the first 6 rows of **sample\_file**, and print out its row names, we have to:

``` r
rownames(head(sample_file))
```

    ## [1] "1" "2" "3" "4" "5" "6"

The above nested code has to be read from the inner-most function outwards. This may get complicated when you are trying to write multiple complex functions in one line. With `%>%`, the above code can be cleanly written as:

``` r
sample_file %>% head() %>% rownames()
```

    ## [1] "1" "2" "3" "4" "5" "6"

Intuitively, you will read the code from left to right and immediately you will understand that our **sample\_file** went through 2 functions. In essence, the `%>%` operator will assign the output of the function on its left to the first argument of the function on the right. Therefore, `sample_file %>% head()` will indicate that you are grabbing **sample\_file** on the left-side of `%>%` to the first argument of the `head()` function on the right. In the next part of the code (`head() %>% rownames()`), the output of the `head()` function is set as the first argument of the `rownames()` function , this printing out the row names of the first 6 rows of our dataframe.

I highly recommend adopting the `%>%` operator in all your R code and we will continuously utilize this operator in this tutorial. For details on `%>%`, check out this [tutorial](https://www.datacamp.com/community/tutorials/pipe-r-tutorial).

### Filter data

### Transmute data

### Reformating columns

### Spread/gather
