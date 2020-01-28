Working with Data
================

A tutorial for analysing and processing genomic data using simple-to-use R tools

Table of contents
=================

-   [Getting started](#Getting-started)
-   [Installing R](#Installing-R)
-   [Installing packages for this tutorial](#Installing-packages-for-this-tutorial)
-   [Importing data into R](#Importing-data-into-R)
-   [Transforming dataframes](#Transforming-dataframes)
-   [The pipe (%&gt;%)](#The-pipe-(%%3E%))
-   [Filter data](#Filter-data)
-   [Transform data](#Transform-data)
-   [Reformating columns](##Reformating-columns)
-   [Spread/gather](#Spreadgather)

Getting started
---------------

### Installing R

To begin this tutorial, you need to have R installed on your computer. You may download the latest R version so from [CRAN](https://cloud.r-project.org/).

Optionally, you may enhance your R experience by utilizing Rstudio, an easy to use Integrated Development Environment (IDE) while running R code. Rstudio can be downloaded from [here](https://rstudio.com/products/rstudio/).

### Installing packages for this tutorial

R contain a plethora of effecient and simple tools for data analysis. To install all the packages covered in this tutorial, open R in your terminal or from Rstudio and run the following:

``` r
install.packages("devtools")
devtools::install_github("fursham-h/wwgd")
```

Importing data into R
---------------------

Most biology data are structured as a table. It contain one or more columns describing the variables and infinite number of rows listing the values of each variable. Values for each row are seperated by a delimiter symbol; typically a *comma* (,) or a *tab* (\\t).

Transforming dataframes
-----------------------

### The pipe (%&gt;%)

### Filter data

### Transform data

### Reformating columns

### Spread/gather
