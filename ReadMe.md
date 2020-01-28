Working with Data
================

A tutorial for analysing and processing data using simple-to-use R tools

# Table of contents

  - [Getting started](#Getting-started)
      - [Installing R](#Installing-R)
      - [Installing packages for this
        tutorial](#Installing-packages-for-this-tutorial)
  - [Importing data into R](#Importing-data-into-R)
      - [Retrieving path to your data
        file](#Retrieving-path-to-your-data-file)
      - [Importing delimited text
        files](#importing-delimited-text-files)(\#Importing-delimited-text-files
      - [Importing excel files](#Importing-excel-files)
  - [Transforming dataframes](#Transforming-dataframes)
      - [The pipe (%\>%)](#The-pipe-\(%%3E%\))
      - [Filter data](#Filter-data)
      - [Transmute data](#Transmute-data)
      - [Reformating columns](##Reformating-columns)
      - [Spread/gather](#Spreadgather)

## Getting started

### Installing R

To begin this tutorial, you need to have R installed on your computer.
You may download the latest R version so from
[CRAN](https://cloud.r-project.org/).

Optionally, you may enhance your R experience by utilizing Rstudio, an
easy to use software to run and write R codes. Rstudio can be downloaded
from [here](https://rstudio.com/products/rstudio/).

### Installing packages for this tutorial

R contain a plethora of effecient and simple tools for data analysis. To
install all the packages covered in this tutorial, open R in your
terminal or from Rstudio and run the following:

``` r
install.packages("devtools")
devtools::install_github("fursham-h/wwgd")
```

## Getting data into R

Most biology data are structured as tables. It contain one or more
column variables and an infinite number of rows listing the values of
each variable. A data table can be saved as one of these file types;

  - generic text file: *.txt*
  - tab-separated values file: *.tsv*
  - comma-separated values file: *.csv*
  - excel file: *.xls or .xlsx*.

To work with these data files in R, we need to import it and convert
into an R-friendly dataframe. Here we will describe how to get your data
into R.

### Retrieving path to your data file

Before importing your data into R, you need to know the “file path” of
your file. You can think of file path as an address in the file system,
pointing to where your it is located on your computer system. For
example, a file named “testfile.txt” that is located on your user
desktop would have a file path that would look something like this:

    /Users/USERNAME/Desktop/testfile.txt

Retrieving your file path differs between operating systems. You may
refer to these tips on how to copy file paths for
[Mac](http://osxdaily.com/2015/11/05/copy-file-path-name-text-mac-os-x-finder/)
and
[Windows](https://www.pcworld.com/article/251406/windows-tips-copy-a-file-path-show-or-hide-extensions.html).
Once your file path have been copied, you may paste it into a variable
in R as such:

``` r
file_path <- "/PATH/TO/YOUR/FILE.txt"
```

A simpler and interactive way of retrieving a file path is possible
using R’s `file.choose` function. Try:

``` r
file_path <- file.choose()
```

For this part of the tutorial, we will retrieve the path of a sample
*.txt* to be imported into
R:

``` r
sample_file_path <- system.file("extdata", "expression_1.txt", package = "wwgd")

# inspect path
sample_file_path
```

    ## [1] "/Library/Frameworks/R.framework/Versions/3.6/Resources/library/wwgd/extdata/expression_1.txt"

### Importing delimited text files

To import *.txt*, *.csv* or *.tsv* files into R as a dataframe, I
recommend using the `vroom` data import tool from
[*vroom*](https://github.com/r-lib/vroom) package. This highly popular
tool has plenty of handy features, including guessing of your file
delimeter, guessing of your column type, importing of multiple files and
most importantly, it is FAST. Simply run:

``` r
library(vroom)
sample_file <- vroom(sample_file_path)
```

We can quickly inspect the dataframe using built-in R functions:

``` r
# view first 6 rows of dataframe
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
# get number of rows found in dataframe
nrow(sample_file)
```

    ## [1] 16

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

As seen above, our sample *expression\_1.txt* file contain 2 named
columns; “transcript\_id” which contain character names of transcripts
and “TpM” containing expression values. By default, `vroom` will assign
the first row of the input table as column names and the remaining rows
as values. If your data do not contain column names, you may prevent
`vroom` from misreading your data by setting the **col\_names** argument
to FALSE as such:

``` r
vroom(sample_file_path, col_names = FALSE)
```

    ## # A tibble: 17 x 2
    ##    X1                 X2   
    ##    <chr>              <chr>
    ##  1 transcript_id      TpM  
    ##  2 ENSMUST00000172282 47.4 
    ##  3 ENSMUST00000168988 1.8  
    ##  4 ENSMUST00000165704 30.3 
    ##  5 ENSMUST00000165153 10.3 
    ##  6 ENSMUST00000099958 33   
    ##  7 ENSMUST00000057343 43.7 
    ##  8 ENSMUST00000171599 30.5 
    ##  9 ENSMUST00000168216 30.5 
    ## 10 ENSMUST00000169483 38.6 
    ## 11 ENSMUST00000095457 36.9 
    ## 12 ENSMUST00000168683 7.8  
    ## 13 ENSMUST00000169091 48.7 
    ## 14 ENSMUST00000171216 39.4 
    ## 15 ENSMUST00000165724 17   
    ## 16 ENSMUST00000164385 4.2  
    ## 17 ENSMUST00000169580 21.9

For more information on how to use *vroom*, refer to
[*vroom*](https://vroom.r-lib.org/articles/vroom.html)’s getting started
page.

### Importing excel files

To import excel files, I recommend using the `read_excel` function from
the [*readxl*](https://readxl.tidyverse.org/) package. Here’s how to
import an excel version of *expression\_1.txt* file into R:

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

By default, `read_excel` will import the first sheet of your excel file.
It is possible to import other sheets by providing additional arguments
to this function. Check out *readxl*’s overview page
[here](https://readxl.tidyverse.org/).

## Transforming dataframes

After importing of data table into R’s dataframe, you would want to
filter/reshape/transmute your dataframe to better understand your data.
Here we will be using simple tools from the popular *tidyverse* package
to transform your data

``` r
#load tidyverse
library(tidyverse)
```

### The pipe (%\>%)

<https://www.datacamp.com/community/tutorials/pipe-r-tutorial>

### Filter data

### Transmute data

### Reformating columns

### Spread/gather
