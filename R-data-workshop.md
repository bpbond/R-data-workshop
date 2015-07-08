Working with data in R
========================================================
author: Ben Bond-Lamberty
date: June 2015

A workshop covering reproducibility, tools, importing, manipulation and aggregation, piping, handling errors, robustness, speed, and common use cases.




The next three hours of your life
========================================================

- Introduction and reproducibility (25 minutes)
- Getting data into R (25 m)
- Cleaning data (20 m)
- Reshaping data (30 m)
- Summarizing data (45 m)
- Robustness and speed (30 m)

**Breadth more than depth**

Three hours, 50% lecture and 50% working examples and problems.

Feedback: <a href="mailto:bondlamberty@pnnl">Email</a>  [Twitter](https://twitter.com/BenBondLamberty)


Focus of this workshop
========================================================

<img src="images/pipeline.png" width="1000" />

In a typical data pipeline:
- *Raw data* can come from many sources 
- *Processing* - cleaning, modifying, reshaping, QC
- *Summarizing* - merging with other data, groupwise summaries
- *Products* include output data, plots, statistical analyses


Is R the right tool for the job?
========================================================

>R has simple and obvious appeal. Through R, you can sift through complex data sets, manipulate data through sophisticated modeling functions, and create sleek graphics to represent the numbers, in just a few lines of code...
>
>R’s greatest asset is the vibrant ecosystem has developed around it: The R community is constantly adding new packages and features to its already rich function sets. It’s estimated that more than 2 million people use R...
>
>-- [The 9 Best Languages For Crunching Data](http://www.fastcompany.com/3030716/the-9-best-languages-for-crunching-data)


Is R the right tool for the job?
========================================================

It might not be. R has limitations and weaknesses:
- nontrivial learning curve; quirks; inconsistent syntax
- documentation patchy and terse
- package quality varies
- generally operates in-memory only
- not particularly fast

Alternatives to address some of these issues include C/C++ (speed), Python (intuitive, flexible, scalable), Julia (fast, expressive, arcane), Java (scalable), Hadoop (very large data).


Things you should know
========================================================
type: section



Things you should know: basics
========================================================

This workshop assumes you understand the basics of using R:

- What R is
- How to start and quit it
- How to get help:
+ `?read.table`
+ `help(package='dplyr')`


Things you should know: basics
========================================================

- The idea of *objects*, *functions*, *assignment*, and *comments*

```r
x <- 10 + 2 # `x` is an object
sum(x) # `sum` is a function
```

>To understand computations in R, two slogans are helpful:
>- Everything that exists is an object.
>- Everything that happens is a function call.
>
>-- John Chambers


Things you should know: data types
========================================================

- The *boolean* (`TRUE`, `FALSE`) data type
- The *vector* data type

```r
v <- 1:5 + 1
v
```

```
[1] 2 3 4 5 6
```

```r
v * 2
```

```
[1]  4  6  8 10 12
```

***


```r
# note that R is 1-, not 0-, based
v[2]  
```

```
[1] 3
```

```r
length(v)
```

```
[1] 5
```


Things you should know: data frames
========================================================

- The idea of a *data frame* (tightly coupled vectors)

```r
head(cars)  # a built-in dataset
```

```
  speed dist
1     4    2
2     4   10
3     7    4
4     7   22
5     8   16
6     9   10
```
Data frames are the fundamental (in the sense of most frequently used) data type in R.


Things you should know: data frames
========================================================

- How to get information about a data frame

```r
nrow(cars)
```

```
[1] 50
```

```r
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


Things you should know: data frames
========================================================

- Basic data frame indexing

```r
cars[1,]
```

```
  speed dist
1     4    2
```

```r
cars[c(1, 3, 4),]
```

```
  speed dist
1     4    2
3     7    4
4     7   22
```

***


```r
# Rows and columns can be specified by number or name
cars[3, "dist"]
```

```
[1] 4
```

```r
cars$dist[3]
```

```
[1] 4
```


Gotcha #1: partial matching
========================================================
type: alert

R has *partial matching* for the $ operator.


```r
d <- data.frame(xdsfjk=1:3)
d$x
```

```
[1] 1 2 3
```

In particular, you need to be careful using any column `x`, or checking for `is.null(x)`, if another column exists whose name begins with the same pattern.

This applies to both data frames and lists.


Things you should know: control flow
========================================================

- Most common control flow uses `if...then...else` and `for`

```r
if(sum(1:4) == 10) {
  print("right!")
} else {
  print("wrong!")
}
```

```
[1] "right!"
```

```r
for(i in 1:4) { cat(i) }
```

```
1234
```


Things you should know: scripts
========================================================

The difference between a *script* (stored program) and *command line* (immediate response).

In general, you want to use scripts, which provide *persistence* and *reproducibility*.


```r
source("myscript.R")
```

***

<img src="images/mayan_script.gif" />



Things you should know: packages
========================================================

- *Packages* are pieces of software that can be optionally loaded and used. The package system is one of R's enormous strengths: there are thousands, written for all kinds of tasks and needs.


```r
library(ggplot2)
qplot(speed, dist, data=cars)
```

***

![plot of chunk unnamed-chunk-13](R-data-workshop-figure/unnamed-chunk-13-1.png) 


Quiz: Basics
========================================================
type: prompt
incremental: true


```r
x <- -2:2
x ^ 2  # what does this print?
```

```
[1] 4 1 0 1 4
```

```r
y <- x < 0  # what type is y?
y
```

```
[1]  TRUE  TRUE FALSE FALSE FALSE
```

***


```r
x[-1] # ?
```

```
[1] -1  0  1  2
```

```r
data.frame(x, y)
```

```
   x     y
1 -2  TRUE
2 -1  TRUE
3  0 FALSE
4  1 FALSE
5  2 FALSE
```


Reproducibility
========================================================
type: section


Reproducibility
========================================================

We are in the era of 'big data', but even if you work with 'little data' you have to acquire some skills to deal with those data.

**Most fundamentally, your results have to be reproducible.**

**Reproducible by yourself, and others.**

**At any time in the future.**


You can't reproduce
========================================================
...what doesn't exist.
- Gozilla ate my computer
+ backup
+ ideally *continuous*
- Godzilla ate my office
+ cloud

***

<img src="images/godzilla.jpg" width="400" />


You can't reproduce
========================================================

...what you've lost. What if you need access to a file as it existed 1, 10, or 100, or 1000 days ago?
- Incremental backups (minimum)
- Version control (better). A *repository* holds files and tracks changes

***

<img src="images/tardis.jpg" width="400" />


Version control
========================================================

**Git** and **GitHub** are the most popular (and free) version control tools for use with R, and many other languages:
- version control
- sharing code with collaborators
- issue tracking
- social coding

***

<img src="images/github.png" width="400" />

**JGCRI has a paid GitHub account: private repositories.**


Packages and reproducibility
========================================================

Using *packages* raises an interesting problem though, as the packages may themselves
change over time (version 1.0, 1.1, ...). How can you guarantee that your script's
behavior and results won't change?

There are a number of solutions to this problem, but my favorite lightweight one is the [checkpoint](http://cran.r-project.org/web/packages/checkpoint/index.html) package.


```r
library(checkpoint)
checkpoint("2015-06-20")
```

This will automatically load and install your script's packages *exactly as they existed on a particular date in the past*.


Code style and clarity
========================================================

Use a clear, consistent code style. Many examples are available.

These two code snippets do the same thing. Which is easier to read? 

```r
finaldata <- merge(process(read.csv( "rawdata.csv")), otherdata)
```


```r
# Read data, process and merge it, and plot
  read.csv("rawdata.csv") %>%
  process() %>%
  merge(otherdata) ->
finaldata
```


Reproducible research example
========================================================

A typical project/paper directory for me:
```
0-download.R
1-process_data.R
2-analyze_data.R
3-make_graphs.R
logs/
output/
rawdata/
```

This directory is generated by my [default script](https://github.com/bpbond/R_analysis_script). It is backed up both *locally* and *remotely*, and under *version control*.


Reproducible research example
========================================================

- Sequentially numbered R scripts (0-download.R, 1-process_data.R, ...)
- Each script depends on the previous one
- Each has a discrete, logical function
- Each produces a *log file* including date/time, what R version, etc.
- This analytical chain starts with raw data
- ...and ends with figures and tables for ms
+ *or the ms itself!* This presentation, for example, was generated directly from an R file


Getting data into R
========================================================
type: section


Things to install beforehand
========================================================
If you're doing the exercises and problems, you should have installed these
packages beforehand:
- `dplyr` - fast, flexible tool for working with data frames
- `reshape2` - reshaping data

(These are part of what's known as the *HadleyVerse*.) We'll also use this data package:
- `babynames` - names provided to the SSA 1880-2013

These can all be installed using R's `install.packages` command.


Getting data into R
========================================================

By far the most common way to bring data into R is via the `read.table` family of functions. In particular, `read.csv` reads a comma separated value file.

```r
d <- read.table("mydata.csv", 
                sep=",", 
                header=TRUE)
d <- read.csv("mydata.csv") # same thing
```


Getting data into R
========================================================

Common and useful options to `read.table` and its brethren include:

Option        | Effect
------------- | -------------
skip=*n*      | Skip the first *n* lines of the file
nrows=*n*     | Read *n* rows of data
sep=*s*       | Look for character *s* separating data
comment.char=*c*  | Lines beginning with *c* are ignored
header=TRUE  | Look for a header giving column names
check.names=TRUE | Check that column names are syntactically correct


Gotcha #2: stringsAsFactors
========================================================
type: alert

The behavior of `read.table` leads to an **extremely common** bug for beginners in R.

By default, `read.table` changes strings to `factors`: a number encoding categories (e.g. 0, 1, 2 instead of "Apple", "Banana", "Grape"). This can lead to hard-to-diagnose problems later (for example, when merging data) if you're not expecting it.

The solution:

```r
d <- read.csv("mydata.csv",
              stringsAsFactors=FALSE)
```


Getting data into R
========================================================

`read.table` can also read from data you've copied from another program, from a string, from a compressed file, or from an URL.

Let's use this ability to read in a dataset, from Pew Research's [Religious Landscape Survey](http://www.pewforum.org/religious-landscape-study/). These particular data examine the relationship between income and religious affiliation.

***

<img src="images/income.gif" width="400" />


Exercise: Reading data into R
========================================================
type: prompt
incremental: true

Data are at: http://stat405.had.co.nz/data/pew.txt


```r
pew <- read.???(...)
pew
summary(pew)
```

Don't forget to look at the `read.table` help page!


```r
pew <- read.table(
  file = "http://stat405.had.co.nz/data/pew.txt",
  header = TRUE,
  stringsAsFactors = FALSE,
  check.names = FALSE
)
```




Alternatives for getting data into R
========================================================

The `read.table` functions can be slow and, at their heart, read only from flat text files. But there are *many* other options.
- A number of packages read Excel files directly. In particular the new `readxl` is fast, stable, and flexible
- `data.table::fread` reads text files extremely quickly, as does the new `readr` package
- The `foreign` package provides import facilities for Stat, SAS, Minitab, etc.
- Relational databases via ` RMySQL` and others
- Many specialized packages (e.g. `ncdf4` for netCDF, `XML::readHTMLTable` for extracting tables from HTML)


Cleaning data
========================================================
type: section


Examining data frames
========================================================

In most cases, after you've imported your data, it's in a `data.frame`. The `summary` and `str` functions are useful at this point:

```r
str(cars); summary(cars)
```

```
'data.frame':	50 obs. of  2 variables:
 $ speed: num  4 4 7 7 8 9 10 10 10 11 ...
 $ dist : num  2 10 4 22 16 10 18 26 34 17 ...
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

Examining data frames
========================================================


```r
nrow(cars); ncol(cars)
```

```
[1] 50
```

```
[1] 2
```

```r
dim(cars)
```

```
[1] 50  2
```

***


```r
head(cars)
```

```
  speed dist
1     4    2
2     4   10
3     7    4
4     7   22
5     8   16
6     9   10
```

```r
tail(cars)
```

```
   speed dist
45    23   54
46    24   70
47    24   92
48    24   93
49    24  120
50    25   85
```


Examining data frames
========================================================


```r
names(cars)
```

```
[1] "speed" "dist" 
```

```r
unique(cars$speed)
```

```
 [1]  4  7  8  9 10 11 12 13 14 15 16 17 18 19 20 22 23 24 25
```

***


```r
range(cars$dist)
```

```
[1]   2 120
```


Examining data frames
========================================================

We already used `ggplot2` to plot the `cars` dataset. Can also do this using `base` graphics built into R:


```r
plot(cars$speed, cars$dist)
```

![plot of chunk unnamed-chunk-29](R-data-workshop-figure/unnamed-chunk-29-1.png) 


Subsetting data frames
========================================================

There are (at least) two separate ways to subset data in base R.


```r
cars[cars$speed < 8,]
```

```
  speed dist
1     4    2
2     4   10
3     7    4
4     7   22
```

***


```r
subset(cars, speed < 8)
```

```
  speed dist
1     4    2
2     4   10
3     7    4
4     7   22
```
There can be subtle differences but you can generally consider these to be equivalent.


Subsetting data frames
========================================================

Data frames are indexed by row *first* and column *second* (in the more general case of multidimensional arrays, the other dimensions follow).

```r
cars[1:3, ]
```

```
  speed dist
1     4    2
2     4   10
3     7    4
```

***

Negative notation excludes!


```r
cars[2, -1]
```

```
[1] 10
```

```r
cars[2, -1, drop=FALSE]
```

```
  dist
2   10
```

**Gotcha #2½**: R generally simplifies objects when it can...see `drop()`


Examining data frames
========================================================

For very large data frames, you might want to take a random sample of the rows to be able to plot or get a sense of things.

```r
library(babynames) # has 1.8 million rows
babynames[sample(nrow(babynames), 3), ]
```

```
Source: local data frame [3 x 5]

  year sex    name  n         prop
1 2005   M  Khyren  7 3.294310e-06
2 1978   M Brently  7 4.096655e-06
3 1927   F     Leo 58 4.691467e-05
```
This uses the extremely useful `sample()` function to randomly sample from a vector.


Subsetting data frames
========================================================

Finding and removing duplicates. Note the use of `which`.

```r
any(duplicated(cars$dist))
```

```
[1] TRUE
```

```r
dupes <- which(duplicated(cars$dist))
print(dupes)
```

```
 [1]  6 15 16 17 18 20 24 25 29 30 36 37 39 42 45
```

```r
cars_nodupes <- cars[-dupes, ]
```


Exercise: Examining data frames
========================================================
type: prompt


```r
library(babynames)
summary(babynames)
```

How many rows and columns are there in the `babynames` dataset?

What name is in row #12345?

How many unique baby names are there?

Make a new data frame with a random 1% of the original rows.

How many 19th century rows are there?


Exercise: Examining data frames
========================================================
type: prompt
incremental: true


```r
cat(dim(babynames), 
    as.character(babynames[12345, "name"]), 
    length(unique(babynames$name)))
```

```
1792091 5 Baxter 92600
```

```r
s <- babynames[sample(1:nrow(babynames), 
                      0.01 * nrow(babynames)),]

sum(babynames$year < 1900) # faster and more memory-efficient than nrow(subset(...))
```

```
[1] 52265
```


Cleaning data
========================================================

Usually, the first thing you'd like to do after importing data is *clean* it.

- change column types
- computing on columns
- splitting columns
- combining columns
- dealing with `NA` values


Changing column types
========================================================

Sometimes, the column classes assigned by `read.table` aren't correct. You can change these:

```r
d$x <- as.numeric(d$x)
d$z <- as.Date(d$z)
```
But first, understand *why* `read.table` did what it did.If you think a column is numeric, but `read.table` converts it to character, that means it encountered a non-numeric entry. Investigate. 

```r
subset(d, is.na(as.numeric(x))) # examine NAs
```


Computing on columns
========================================================

This can be simple...

```r
d <- data.frame(x=1:3)
d$y <- d$x * 2
d$z <- cumsum(d$y)
d$four <- ifelse(d$y == 4, "four", "not four") 
d
```

```
  x y  z     four
1 1 2  2 not four
2 2 4  6     four
3 3 6 12 not four
```

R provides a set of high performance functions for many of these tasks: `cumsum`, `colMeans`, `colSums`, `rowMeans`, `rowSums`, etc.


Computing on columns
========================================================

...but not always. A rolling mean for window size *n*, for example.

* naive (slow) loop

```r
for(i in seq_along(x)) {
# calculate mean...
```
* `filter` (note its `sides` argument)

```r
filter(x, rep(1/n, n), sides=2)
```

* package-specific solutions, e.g.

```r
zoo::rollmean(x, n, align='center')
```


Gotcha #3: sequences and for()
========================================================
type: alert
incremental: true

In the previous example, we used `seq_along(x)` instead of `1:length(x)` (or `nrow`). Why?

Because if `x` has zero rows or elements...


```r
d <- data.frame()  # 0 rows
for(i in 1:nrow(d)) print(i)
```

```
[1] 1
[1] 0
```

```r
for(i in seq_along(d))    # correct: no output
  print(i) 
```


Exercise: Computing on columns
========================================================
type: prompt
incremental: true

One recent problem I had involved a data frame with multiplexer valve numbers; in the experiment, the multiplexer was automatically switching between valves.

Whenever the valve number changes, we want to assign a new sample number.

```r
# analyzer is switching between valves 1, 2, and 3
vnums <- c(1, 1, 2, 3, 3, 3, 1, 2, 2, 3)
# there are 6 samples here: (1, 1), (2), (3, 3, 3), (1), (2, 2), (3)

# There are ≥ two solutions
```


Exercise: Computing on columns
========================================================
type: prompt
incremental: true


```r
# Works, but slow
samplenums <- rep(1, length(vnums))
s <- 1
for(i in seq_along(vnums)[-1]) {
  if(vnums[i] != vnums[i-1])
    s <- s + 1
  samplenums[i] <- s
}
samplenums
```

```
 [1] 1 1 2 3 3 3 4 5 5 6
```


```r
# Vectorised: fast and elegant
newvalve <- c(TRUE,
              vnums[-length(vnums)] != vnums[-1])
cumsum(newvalve)
```

```
 [1] 1 1 2 3 3 3 4 5 5 6
```


Exercise: Computing on columns - time
========================================================
type: prompt



This has big consequences!

For a data frame with 1,100,000 rows:

In R, `for` loops are rarely the fastest way to do something (although they may be the clearest).

***

![plot of chunk unnamed-chunk-49](R-data-workshop-figure/unnamed-chunk-49-1.png) 


Combining columns
========================================================

Combining columns is generally easy.

```r
d <- data.frame(x=1:3, y=4:6)
d$z <- with(d, paste("pasted", x, "and", y))  # note use of `with` here
d
```

```
  x y              z
1 1 4 pasted 1 and 4
2 2 5 pasted 2 and 5
3 3 6 pasted 3 and 6
```


Splitting columns
========================================================

This is sometimes a bit trickier. Perfectly possible in base R, but since we'll be using the `reshape2` package later anyway, let's use `reshape2::colsplit`.

```r
d <- data.frame(x=paste("hi", 1:3))
library(reshape2)
split <- colsplit(d$x, " ", c("var1", "var2"))
cbind(d, split)
```

```
     x var1 var2
1 hi 1   hi    1
2 hi 2   hi    2
3 hi 3   hi    3
```


Understanding and dealing with NA
========================================================

One of R's real strengths is that missing values are a first-class data type: `NA`.


```r
x <- c(1, 2, 3, NA)
is.na(x) # returns c(F, F, F, T)
any(is.na(x)) # returns TRUE
which(is.na(x)) # returns ...?
```

Like `NaN` and `Inf`, generally `NA` 'poisons' operations, so it must be handled.


```r
sum(x) # NA
sum(x, na.rm=TRUE) # 6
na.omit(data.frame(x))  # remove rows with NA
```


Dealing with dates
========================================================

R has a `Date` class representing calendar dates, and an `as.Date` function for converting to Dates. The `lubridate` package is often useful (and easier) for these cases:

```r
library(lubridate)
x <- c("09-01-01", "09-01-02")
ymd(x)   # there's also dmy and mdy!
```

```
[1] "2009-01-01 UTC" "2009-01-02 UTC"
```

Once data are in `Date` format, the time interval between them can be computed simply by subtraction. See also `?difftime`


Quiz: Cleaning Data
========================================================
type: prompt
incremental: true


```r
x <- -2:2
y <- 4/x 
y  # prints...?
```

```
[1]  -2  -4 Inf   4   2
```

```r
y <- y[is.finite(y)]
y  # prints...?
```

```
[1] -2 -4  4  2
```

***


```r
is.numeric(NA)
```

```
[1] FALSE
```

```r
is.numeric(Inf)
```

```
[1] TRUE
```

```r
is.infinite(Inf)
```

```
[1] TRUE
```


Reshaping data
========================================================
type: section


History lesson
========================================================

<img src="images/history.png" width="850" />


A tale of two pipelines
========================================================

<img src="images/pipeline_detail.png" width="850" />


Reshaping data
========================================================

We often need data in a particular form for working with it.

If you're used to spreadsheets, it's more common to use data in *wide format*: a subject's repeated responses are in a single row.

For data processing in R, *long format* is almost always better: each row is one time point per subject.

Variables are one of two types: *id* variables, which are typically assigned; and *measure* variables, which are measured (observations). But it's not always obvious
- What is a variable?
- What is a unit of observation?
- Which data should go in each row?


Reshaping the Pew data
========================================================


```r
pew[1:5, 1:4]
```

```
            religion <$10k $10-20k $20-30k
1           Agnostic    27      34      60
2            Atheist    12      27      37
3           Buddhist    27      21      30
4           Catholic   418     617     732
5 Don’t know/refused    15      14      15
```

These data are *wide* - multiple observations per row.

Which columns are *measure* variables, and which *id* variables?


Reshaping the Pew data
========================================================


```r
library(reshape2)
pew_long <- melt(pew, id.var="religion")
head(pew_long)
```

```
            religion variable value
1           Agnostic    <$10k    27
2            Atheist    <$10k    12
3           Buddhist    <$10k    27
4           Catholic    <$10k   418
5 Don’t know/refused    <$10k    15
6   Evangelical Prot    <$10k   575
```

These data are now *long*.


Reshaping the Pew data
========================================================

Once our data are *long*, we can cast into another form:


```r
pew_original <- dcast(pew_long, 
                      religion ~ variable)
all.equal(pew_original, pew)
```

```
[1] TRUE
```

We've just transformed it back into its original form!


```
           religion <$10k $10-20k $20-30k
           Agnostic    27      34      60
            Atheist    12      27      37
           Buddhist    27      21      30
           Catholic   418     617     732
 Don’t know/refused    15      14      15
```


Reshaping the Pew data
========================================================

Casting in the `reshape2` package can also involve data summarizing.


```r
dcast(pew_long, religion ~ ., mean)
```

```
                  religion     .
1                 Agnostic  82.6
2                  Atheist  51.5
3                 Buddhist  41.1
4                 Catholic 805.4
5       Don’t know/refused  27.2
6         Evangelical Prot 947.2
7                    Hindu  25.7
8  Historically Black Prot 199.5
9        Jehovah's Witness  21.5
10                  Jewish  68.2
11           Mainline Prot 747.0
12                  Mormon  58.1
13                  Muslim  11.6
14                Orthodox  36.3
15         Other Christian  12.9
16            Other Faiths  44.9
17   Other World Religions   4.2
18            Unaffiliated 370.7
```


Exercise: Reshaping data
========================================================
type: prompt

The built-in (and very famous) `iris` dataset gives the measurements (in cm) of the variables sepal length and width and petal length and width, respectively, for 50 flowers from each of 3 species of iris. The species are *Iris setosa*, *versicolor*, and *virginica*.


```
  Sepal.Length Sepal.Width Species
1          5.1         3.5  setosa
2          4.9         3.0  setosa
3          4.7         3.2  setosa
4          4.6         3.1  setosa
5          5.0         3.6  setosa
```

`iris` has 150 rows and 5 columns (two columns are not shown above). Is it *wide* or *long*? Why?


Exercise: Reshaping data
========================================================
type: prompt

1. What variables are *id* in the `iris` data? What are the *measured* variable(s)?

2. From a data manipulation aspect, what is particularly inconvenient about the `iris` structure (look at the column names)?

3. Try reshaping the `iris` data: melt it into long format, then make it wide in at least four different forms.

4. Why does `dcast(iris_long, variable ~ Species)` generate a warning?

5. Melt the `pew` data into a `pew_long` dataframe.


Exercise: Reshaping data
========================================================
type: prompt

`iris` is *wide*: multiple observations of different individuals per row.

1. `iris$Species` is an *id* variable; the others are *measured*.
2. The `iris` column names conflate two *measured* variables: plant part (sepal or petal) and axis measured (width or length). We would probably want to split these labels into separate columns.
3. Melt with `id.var = 'Species'`. Possible reshapes using `dcast` include `Species ~ variable`, `variable ~ Species`, `variable ~ .`, `Species ~ .`.
4. Because there's more than one entry for each combination of `variable` and `Species`, and we haven't supplied an *aggregation function*.
5. `pew_long <- melt(pew, id.var="religion")`

Merging datasets
========================================================

Often, as we clean and reshape data, we want to merge different datasets together. The built-in `merge` command does this well.

Let's say we have a data frame containing information on how pretty each of the `iris` species is:

```
     Species    pretty
1     setosa      ugly
2 versicolor        ok
3  virginica beautiful
```


Merging datasets
========================================================

`merge` looks for names in common between two data frames, and uses these to merge. Various options allow us to control the merge behavior (what happens to unmatched rows, what columns to use, etc).

```r
merge(iris, howpretty)
```

```
       Species Sepal.Length    pretty
1       setosa          5.1      ugly
2       setosa          4.9      ugly
50      setosa          5.0      ugly
100 versicolor          5.7        ok
150  virginica          5.9 beautiful
```

The `data.table` and `dplyr` packages feature more varied, faster database-style join operations.


Summarizing and operating on data
========================================================
type: section


Summarizing and operating on data
========================================================

Thinking back to the typical data pipeline, we often want to summarize data by groups as an intermediate or final step. For example, for each subgroup we might want to:

* Compute mean, max, min, etc. (`n`->1)
* Compute rolling mean and other window functions (`n`->`n`)
* Fit models and extract their parameters, goodness of fit, etc.

Specific examples:

* `cars`: for each speed, what's the farthest distance traveled?
* `iris`: how many samples were taken from each species?
* `babynames`: what's the most common name over time?


Split-apply-combine
========================================================

These are generally known as *split-apply-combine* problems.

<img src="images/splitapply.png" width="650" />

From https://ramnathv.github.io/pycon2014-r/explore/sac.html


The apply family
========================================================

Traditionally the *apply* family of functions was R's solution. Unfortunately they have inconsistent and confusing syntax, middling performance, and functional quirks.

Function      | Description
------------- | ------------
base::apply   |  Apply Functions Over Array Margins
base::by      |  Apply a Function to a Data Frame Split by Factors
base::eapply  |  Apply a Function Over Values in an Environment
**base::lapply**  |  **Apply a Function over a List or Vector**
base::mapply  |  Apply a Function to Multiple List or Vector Arguments
base::rapply  |  Recursively Apply a Function to a List
base::tapply  |  Apply a Function Over a Ragged Array

https://nsaunders.wordpress.com/2010/08/20/a-brief-introduction-to-apply-in-r/ provides a simple, readable summary of these.


aggregate
========================================================

R also has a built-in `aggregate` function. It's not particularly fast or flexible, and confusingly has a number of different forms. 

It can however be useful for simple operations:

```r
aggregate(dist ~ speed, data=cars, FUN=max)
```

```
   speed dist
1      4   10
2      7   22
3      8   16
4      9   10
5     10   34
6     11   28
7     12   28
8     13   46
9     14   80
10    15   54
11    16   40
12    17   50
13    18   84
14    19   68
15    20   64
16    22   66
17    23   54
18    24  120
19    25   85
```


plyr
========================================================

The `plyr` package revolutionized the handling of split-apply-combine 
problems in R, providing a consistent naming scheme and interface, allowing concise code, good performance, and lots of flexibility.

Input      | Output: Array | Output: Data frame | Ouptut: List | Discard output
---------- | ------------- | ------------------ | ------------ | --------------
Array      | `aaply`       | `adply`            | `alply`      | `a_ply`
Data frame | `daply`       | **`ddply`**        | `dlply`      | `d_ply`
List       | `laply`       | `ldply`            | `llply`      | `l_ply`

(Most of `dplyr` is equivalent to `ddply` + various functions.)


plyr
========================================================

<img src="images/plyr_split_apply_combine.png" width="750" />

Wickham (2011), The Split-Apply-Combine Strategy for Data Analysis. *J. Stat. Software* 40(1):1-29, http://www.jstatsoft.org/v40/i01/paper.


dplyr
========================================================

The newer `dplyr` package makes a different tradeoff: it specializes in data frames, recognizing that most people use them most of the time, and is extremely fast.

`dplyr` also allows you to work with remote, out-of-memory databases, using exactly the same tools, because dplyr will translate your R code into the appropriate SQL.

In other words, `dplyr` abstracts away *how* your data is stored.


Aside: operation pipelines in R
========================================================

`dplyr` *imports*, and its examples make heavy use of, the [magrittr](https://github.com/smbache/magrittr) package, which changes R syntax (remember, every operation is a function) to introduce a **pipe** operator `%>%`. This 
* structures sequences of data operations left-to-right (as opposed to inside-out)
* avoids nested function calls
* minimizes the need for local variables
* makes it easy to add steps anywhere in a sequence of operations

Not everyone is a fan of piping, and there are situations where it's not appropriate; but we'll stick to `dplyr` convention and use it frequently.


Operation pipelines in R
========================================================

Standard R notation:

```r
x <- read_my_data(f)
y <- process_data(clean_data(x), otherdata)
z <- summarize_data(y)
```

Notation using a `magrittr` pipe:

```r
z <- read_my_data(f) %>%
  clean_data() %>%
  process_data(otherdata) %>%
  summarize_data()
```


Operation pipelines in R
========================================================

Basic `magrittr` piping summary:

* `x %>% f` is equivalent to `f(x)`
* `x %>% f(y)` is equivalent to `f(x, y)`
* `x %>% f %>% g %>% h` is equivalent to `h(g(f(x)))`
* `x %>% f(y, .)` is equivalent to `f(y, x)`

Remember, `magrittr` is independent of `dplyr` - you can use pipes anywhere useful.


Verbs
========================================================

`dplyr` provides functions for each basic *verb* of data manipulation. These tend to have analogues in base R, but use a consistent, compact syntax, and are very high performance.

* `filter()` - subset rows; like `base::subset()`
* `arrange()` - reorder rows; is a wrapper for `order()`
* `select()` - select columns
* `mutate()` - add new columns; like `base::transform()
* `summarise()` - like `aggregate` or `plyr::ddply`


Grouping
========================================================

`dplyr` verbs become particularly powerful when used in conjunction with *groups* we define in the dataset. The `group_by` function converts an existing data frame into a grouped `tbl`.


```r
group_by(babynames, year, sex)
```

```
Source: local data frame [1,792,091 x 5]
Groups: year, sex

   year sex      name    n       prop
1  1880   F      Mary 7065 0.07238359
2  1880   F      Anna 2604 0.02667896
3  1880   F      Emma 2003 0.02052149
4  1880   F Elizabeth 1939 0.01986579
5  1880   F    Minnie 1746 0.01788843
6  1880   F  Margaret 1578 0.01616720
7  1880   F       Ida 1472 0.01508119
8  1880   F     Alice 1414 0.01448696
9  1880   F    Bertha 1320 0.01352390
10 1880   F     Sarah 1288 0.01319605
..  ... ...       ...  ...        ...
```


Summarizing iris
========================================================


```r
iris %>% 
  group_by(Species) %>% 
  summarise(msl = mean(Sepal.Length))
```

```
Source: local data frame [3 x 2]

     Species   msl
1     setosa 5.006
2 versicolor 5.936
3  virginica 6.588
```


Summarizing iris
========================================================

We can apply (multiple) functions across (multiple) columns.

```r
iris %>% 
  group_by(Species) %>% 
  summarise_each(funs(mean, sd), 
                 Petal.Width, Sepal.Length) %>%
  names()
```

```
[1] "Species"           "Petal.Width_mean"  "Sepal.Length_mean"
[4] "Petal.Width_sd"    "Sepal.Length_sd"  
```


Summarizing babynames
========================================================

Note that each summary operation peels off one grouping layer. What does this calculate?

```r
babynames %>%
  group_by(year, sex) %>% 
  summarise(prop = max(prop), 
            name = name[which.max(prop)])
```

```
Source: local data frame [268 x 4]
Groups: year

   year sex       prop name
1  1880   F 0.07238359 Mary
2  1880   M 0.08154561 John
3  1881   F 0.06998999 Mary
4  1881   M 0.08098075 John
5  1882   F 0.07042473 Mary
6  1882   M 0.07831552 John
7  1883   F 0.06673052 Mary
8  1883   M 0.07907113 John
9  1884   F 0.06698985 Mary
10 1884   M 0.07648564 John
..  ... ...        ...  ...
```


Summarizing babynames
========================================================



<img src="images/babynames.png" width="850" />

https://en.wikipedia.org/wiki/Linda_(1946_song)


Summarizing babynames
========================================================

Time to summarize the 1.8 million row `babynames` using base R, `plyr`, and `dplyr`.

In general `dplyr` is ~10x faster than `plyr`, which in turn is ~10x faster than base R.

Base R also tends to require many more lines of code.

***

![plot of chunk unnamed-chunk-74](R-data-workshop-figure/unnamed-chunk-74-1.png) 


Useful summary functions
========================================================

`dplyr` provides some useful summarizing functions in addition to those provided by base R:

* `n()` - number of observations in current group
* `n_distinct(x)` - number of unique values in `x`
* `first(x)`, `last(x)`, `nth(x, n)` - extract particular elements from a group. These all have an `order_by` parameter to specify an ordering


Window functions
========================================================

`dplyr` *window functions* take `n` values and return `n` values. This is useful for computing lags, ranks, etc. For example, the popularity of "Mary":


```r
babynames %>% 
  group_by(year, sex) %>% 
  mutate(rank = dense_rank(desc(prop))) %>%
  filter(name == "Mary")
```



<img src="images/babynames_rank.png" width="950" />


More advanced summary operations
========================================================

Sometimes we want to perform a significantly more complicated summary operation, like fitting linear models to each group.

```r
mods <- rawdata %>%
  group_by(samplenum) %>%
  do(co2mod = lm(CO2_dry ~ time, data = .))

modsummary <- mods %>%
  summarise(
    R2_CO2 = summary(co2mod)$r.squared,
    m_CO2 = coef(co2mod)[2])
```

This fits a model to each group defined by `samplenum`, and then extracts the slope (`m_CO2`) and R2 for each model into a data frame.


Exercise: Summarizing data
========================================================
type: prompt

1. Use `dplyr` to calculate the total number of respondents, for each religion, in the `pew_long` data.

2. Use `dplyr` and the `babynames` data to calculate the 5th most popular name for girls in each year.


Exercise: Summarizing data
========================================================
type: prompt


```r
pew_long %>% 
  group_by(religion) %>% 
  summarise(sum(value))
```

```
Source: local data frame [18 x 2]

                  religion sum(value)
1                 Agnostic        826
2                  Atheist        515
3                 Buddhist        411
4                 Catholic       8054
5       Don’t know/refused        272
6         Evangelical Prot       9472
7                    Hindu        257
8  Historically Black Prot       1995
9        Jehovah's Witness        215
10                  Jewish        682
11           Mainline Prot       7470
12                  Mormon        581
13                  Muslim        116
14                Orthodox        363
15         Other Christian        129
16            Other Faiths        449
17   Other World Religions         42
18            Unaffiliated       3707
```


Exercise: Summarizing data
========================================================
type: prompt


```r
babynames %>% 
  filter(sex == 'F') %>% 
  group_by(year) %>% 
  summarise(fifth = nth(name, 5, 
                      order_by = desc(prop)))
```

```
Source: local data frame [134 x 2]

   year    fifth
1  1880   Minnie
2  1881 Margaret
3  1882   Minnie
4  1883   Minnie
5  1884   Minnie
6  1885 Margaret
7  1886   Minnie
8  1887 Margaret
9  1888 Margaret
10 1889     Emma
..  ...      ...
```


Robustness and performance
========================================================
type: section


What's robust code?
========================================================

Robust code handles bad inputs and unexpected/error condition gracefully. This is sometimes called *defensive programming*.

At the beginning of a function or piece of code, *sanity checks* using the built-in function `stopifnot` can be useful:

```r
stopifnot(is.numeric(x))
```

The `assertthat` package provides more sophisticated assertion checking and is very useful.

```r
library(assertthat)
assert_that(is.writeable("images/"))
```


Gotcha #4: infinity
========================================================
type: alert
incremental: true

Unlike in many other programming languages, division by zero does not generate an error:

```r
x <- 1/0
x
```

```
[1] Inf
```

...but this also means you might not catch a problem. Test for this using `is.finite(x)`.


try
========================================================

Some operations can generate code-terminating errors. You might want to *catch* - handle yourself, and recover from - these using `try`:

```r
x <- try(stop()) #  stop() generates an error
is.numeric(x); 
```

```
[1] FALSE
```

```r
assertthat::is.error(x)
```

```
[1] TRUE
```

There's also the more powerful `tryCatch`, which has the single worst help page in R.


Unit testing
========================================================

If you have a serious piece of code that you will maintain over time, strongly consider **unit testing**.

Test code automatically tests each part of your program, so you don't accidentally introduce bugs to previously-working code. See the `testthat` package for more details.

***

<img src="images/testing.jpg" />


Performance
========================================================

*By design*, R  is not a fast language: it's intended to make data analysis and statistics easier for people. 

For most purposes, most of the time, it's fast enough.

But not always.

***

<img src="images/stock-illustration-39884236-man-asleep-in-front-of-the-computer.jpg" />


Understand what's slow, and why
========================================================

The simplest way to figure out what's slow is `system.time()`, which provides timing for an expression or code block.

```r
system.time(Sys.sleep(3))
```

```
   user  system elapsed 
  0.000   0.000   3.005 
```

```r
system.time(sqrt(1:1e6))
```

```
   user  system elapsed 
  0.006   0.003   0.008 
```

The `microbenchmark` package provides much more precise timing and can be very useful.


Understand what's slow, and why
========================================================

A more sophisticated way to approach this is by *profiling* your code. R has a built-in profiler (and, of course, there are packages that provide more functionality).

```r
f <- function() { for(i in 1:1e6) sqrt(i) }
g <- function() { for(i in 1:1e6) i }
h <- function() { f() + g() }
h()
```

After using `Rprof`...


```r
       total.time total.pct self.time self.pct
"h"          0.28    100.00      0.00     0.00
"f"          0.24     85.71      0.18    64.29
"sqrt"       0.06     21.43      0.06    21.43
"g"          0.04     14.29      0.04    14.29
```


Speeding up your code
========================================================

>Premature optimization is the root of all evil. 
>
>--Donald Knuth

1. Look for existing solutions
2. Do less work
3. Vectorise
4. Parallelise
5. Avoid copies
6. Byte code compiler
7. Move outside of R

Most of this list is from http://adv-r.had.co.nz/Profiling.html.


Vectorise
========================================================

Certain operations in R are, as has already been noted, heavily *vectorised*. Take advantage of them! In particular, loops don't *always* have to be slow, but sometimes they really are.

```r
system.time(for(i in 1:1e6) sqrt(i))
```

```
   user  system elapsed 
  0.203   0.001   0.206 
```

```r
system.time(sqrt(1:1e6))
```

```
   user  system elapsed 
  0.004   0.000   0.004 
```

Some functions are known to be particularly slow, e.g. `ifelse`.


Internal functions
========================================================

Be aware that sometimes there are considerably faster *internal* versions of functions. These are usually noted on the help page.


```r
library(microbenchmark)
microbenchmark(
  "pmax"     = pmax(1:5),
  "pmax.int" = pmax.int(1:5)
)
```

```
Unit: nanoseconds
     expr  min   lq    mean median     uq   max neval
     pmax 3994 4329 6676.94 4538.0 4998.5 72367   100
 pmax.int  461  488  970.36  543.5  665.5 18678   100
```


Gotcha #5: sequential rbind operations
========================================================
type: alert
incremental: true

It's fairly common to need to read in chunks of data, process them in some way, and build up a larger single data set:

```r
d_final <- data.frame()
for(f in files_to_process) {
  d <- read.csv(f)
  
  # do something
  
  d_final <- rbind(d_final, d)
}
```

This will quickly become **very** slow, as R has to re-allocate memory for the growing `d_final` again and again.


Gotcha #5: sequential rbind operations
========================================================
type: alert

A much better solution is to pre-allocate the final data frame (if you know in advance how big it will be), or use a temporary file:

```r
tf <- tempfile()
for(f in files_to_process) {
  d <- read.csv(f)
  
  # do something
  
  first <- !file.exists(tf)
  write.table(d, file=tf, sep=",", append=!first, 
              col.names=first)
}
d_final <- read.csv(tf)
```


The byte code compiler
========================================================

R ships with a byte code compiler that can drastically speed up some kinds of code.

```r
library(compiler)
f <- function() {
  for(i in 1:1e6) sqrt(i) }
fc <- cmpfun(f)
microbenchmark("f"=f(), "fc"=fc())
```

```
Unit: milliseconds
 expr      min       lq     mean   median       uq      max neval
    f 129.9580 137.5874 142.4279 140.8487 143.8499 216.2845   100
   fc 158.2409 164.4974 172.2762 168.4660 172.7314 238.9767   100
```


Moving away from R
========================================================

* R now has good support (the built-in `parallel` package) for basic parallelization. See also the `foreach` package, which allows you to connect arbitrary backends to your code.

* Faster, experimental versions of R are available - e.g. `pqr` (Pretty Quick R).

* The extremely popular `Rcpp` package offers seamless integration between R and C++.


Last thoughts
========================================================

>The best thing about R is that it was written by statisticians. The worst thing about R is that it was written by statisticians.
>
>-- Bow Cowgill

All the source code for this presentation is available at https://github.com/bpbond/R-data-workshop


Resources
========================================================
type: section


Resources
========================================================

* [CRAN](http://cran.r-project.org) - The Comprehensive R Archive Network. Ground zero for R.
* [GitHub](https://github.com/JGCRI) - The JGCRI organization page on GitHub.
* [RStudio](http://www.rstudio.com) - the integrated development environment for R. Makes many things hugely easier.
* [Advanced R](http://adv-r.had.co.nz) - the companion website for “Advanced R”, a book in Chapman & Hall’s R Series. Detailed, in depth look at many of the issues covered here.


Resources
========================================================

R has many contributed *packages* across a wide variety of scientific fields. Almost anything you want to do will have packages to support it.

[CRAN](http://cran.r-project.org) also provides "Task Views". For example:

***

- Bayesian
- Clinical Trials
- Differential Equations
- Finance
- Genetics
- HPC
- Meta-analysis
- Optimization
- [**Reproducible Research**](http://cran.r-project.org/web/views/ReproducibleResearch.html)
- Spatial Statistics
- Time Series


Removed slides
========================================================
type: section


Things you should know: data types
========================================================

- The *factor* data type

```r
# 'letters' and 'LETTERS' are built-in
summary(letters)
```

```
   Length     Class      Mode 
       26 character character 
```

```r
summary(as.factor(letters))
```

```
a b c d e f g h i j k l m n o p q r s t u v w x y z 
1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 1 
```


Reshaping the Pew data
========================================================

Can have more than one `id` variable in your rows or columns.


```r
dcast(pew_long, religion + variable ~ ., mean)
```

```
                   religion           variable    .
1                  Agnostic              <$10k   27
2                  Agnostic            $10-20k   34
3                  Agnostic            $20-30k   60
4                  Agnostic            $30-40k   81
5                  Agnostic            $40-50k   76
6                  Agnostic            $50-75k  137
7                  Agnostic           $75-100k  122
8                  Agnostic          $100-150k  109
9                  Agnostic              >150k   84
10                 Agnostic Don't know/refused   96
11                  Atheist              <$10k   12
12                  Atheist            $10-20k   27
13                  Atheist            $20-30k   37
14                  Atheist            $30-40k   52
15                  Atheist            $40-50k   35
16                  Atheist            $50-75k   70
17                  Atheist           $75-100k   73
18                  Atheist          $100-150k   59
19                  Atheist              >150k   74
20                  Atheist Don't know/refused   76
21                 Buddhist              <$10k   27
22                 Buddhist            $10-20k   21
23                 Buddhist            $20-30k   30
24                 Buddhist            $30-40k   34
25                 Buddhist            $40-50k   33
26                 Buddhist            $50-75k   58
27                 Buddhist           $75-100k   62
28                 Buddhist          $100-150k   39
29                 Buddhist              >150k   53
30                 Buddhist Don't know/refused   54
31                 Catholic              <$10k  418
32                 Catholic            $10-20k  617
33                 Catholic            $20-30k  732
34                 Catholic            $30-40k  670
35                 Catholic            $40-50k  638
36                 Catholic            $50-75k 1116
37                 Catholic           $75-100k  949
38                 Catholic          $100-150k  792
39                 Catholic              >150k  633
40                 Catholic Don't know/refused 1489
41       Don’t know/refused              <$10k   15
42       Don’t know/refused            $10-20k   14
43       Don’t know/refused            $20-30k   15
44       Don’t know/refused            $30-40k   11
45       Don’t know/refused            $40-50k   10
46       Don’t know/refused            $50-75k   35
47       Don’t know/refused           $75-100k   21
48       Don’t know/refused          $100-150k   17
49       Don’t know/refused              >150k   18
50       Don’t know/refused Don't know/refused  116
51         Evangelical Prot              <$10k  575
52         Evangelical Prot            $10-20k  869
53         Evangelical Prot            $20-30k 1064
54         Evangelical Prot            $30-40k  982
55         Evangelical Prot            $40-50k  881
56         Evangelical Prot            $50-75k 1486
57         Evangelical Prot           $75-100k  949
58         Evangelical Prot          $100-150k  723
59         Evangelical Prot              >150k  414
60         Evangelical Prot Don't know/refused 1529
61                    Hindu              <$10k    1
62                    Hindu            $10-20k    9
63                    Hindu            $20-30k    7
64                    Hindu            $30-40k    9
65                    Hindu            $40-50k   11
66                    Hindu            $50-75k   34
67                    Hindu           $75-100k   47
68                    Hindu          $100-150k   48
69                    Hindu              >150k   54
70                    Hindu Don't know/refused   37
71  Historically Black Prot              <$10k  228
72  Historically Black Prot            $10-20k  244
73  Historically Black Prot            $20-30k  236
74  Historically Black Prot            $30-40k  238
75  Historically Black Prot            $40-50k  197
76  Historically Black Prot            $50-75k  223
77  Historically Black Prot           $75-100k  131
78  Historically Black Prot          $100-150k   81
79  Historically Black Prot              >150k   78
80  Historically Black Prot Don't know/refused  339
81        Jehovah's Witness              <$10k   20
82        Jehovah's Witness            $10-20k   27
83        Jehovah's Witness            $20-30k   24
84        Jehovah's Witness            $30-40k   24
85        Jehovah's Witness            $40-50k   21
86        Jehovah's Witness            $50-75k   30
87        Jehovah's Witness           $75-100k   15
88        Jehovah's Witness          $100-150k   11
89        Jehovah's Witness              >150k    6
90        Jehovah's Witness Don't know/refused   37
91                   Jewish              <$10k   19
92                   Jewish            $10-20k   19
93                   Jewish            $20-30k   25
94                   Jewish            $30-40k   25
95                   Jewish            $40-50k   30
96                   Jewish            $50-75k   95
97                   Jewish           $75-100k   69
98                   Jewish          $100-150k   87
99                   Jewish              >150k  151
100                  Jewish Don't know/refused  162
101           Mainline Prot              <$10k  289
102           Mainline Prot            $10-20k  495
103           Mainline Prot            $20-30k  619
104           Mainline Prot            $30-40k  655
105           Mainline Prot            $40-50k  651
106           Mainline Prot            $50-75k 1107
107           Mainline Prot           $75-100k  939
108           Mainline Prot          $100-150k  753
109           Mainline Prot              >150k  634
110           Mainline Prot Don't know/refused 1328
111                  Mormon              <$10k   29
112                  Mormon            $10-20k   40
113                  Mormon            $20-30k   48
114                  Mormon            $30-40k   51
115                  Mormon            $40-50k   56
116                  Mormon            $50-75k  112
117                  Mormon           $75-100k   85
118                  Mormon          $100-150k   49
119                  Mormon              >150k   42
120                  Mormon Don't know/refused   69
121                  Muslim              <$10k    6
122                  Muslim            $10-20k    7
123                  Muslim            $20-30k    9
124                  Muslim            $30-40k   10
125                  Muslim            $40-50k    9
126                  Muslim            $50-75k   23
127                  Muslim           $75-100k   16
128                  Muslim          $100-150k    8
129                  Muslim              >150k    6
130                  Muslim Don't know/refused   22
131                Orthodox              <$10k   13
132                Orthodox            $10-20k   17
133                Orthodox            $20-30k   23
134                Orthodox            $30-40k   32
135                Orthodox            $40-50k   32
136                Orthodox            $50-75k   47
137                Orthodox           $75-100k   38
138                Orthodox          $100-150k   42
139                Orthodox              >150k   46
140                Orthodox Don't know/refused   73
141         Other Christian              <$10k    9
142         Other Christian            $10-20k    7
143         Other Christian            $20-30k   11
144         Other Christian            $30-40k   13
145         Other Christian            $40-50k   13
146         Other Christian            $50-75k   14
147         Other Christian           $75-100k   18
148         Other Christian          $100-150k   14
149         Other Christian              >150k   12
150         Other Christian Don't know/refused   18
151            Other Faiths              <$10k   20
152            Other Faiths            $10-20k   33
153            Other Faiths            $20-30k   40
154            Other Faiths            $30-40k   46
155            Other Faiths            $40-50k   49
156            Other Faiths            $50-75k   63
157            Other Faiths           $75-100k   46
158            Other Faiths          $100-150k   40
159            Other Faiths              >150k   41
160            Other Faiths Don't know/refused   71
161   Other World Religions              <$10k    5
162   Other World Religions            $10-20k    2
163   Other World Religions            $20-30k    3
164   Other World Religions            $30-40k    4
165   Other World Religions            $40-50k    2
166   Other World Religions            $50-75k    7
167   Other World Religions           $75-100k    3
168   Other World Religions          $100-150k    4
169   Other World Religions              >150k    4
170   Other World Religions Don't know/refused    8
171            Unaffiliated              <$10k  217
172            Unaffiliated            $10-20k  299
173            Unaffiliated            $20-30k  374
174            Unaffiliated            $30-40k  365
175            Unaffiliated            $40-50k  341
176            Unaffiliated            $50-75k  528
177            Unaffiliated           $75-100k  407
178            Unaffiliated          $100-150k  321
179            Unaffiliated              >150k  258
180            Unaffiliated Don't know/refused  597
```
