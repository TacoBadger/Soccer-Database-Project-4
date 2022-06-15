# Soccer Database
![](https://raw.githubusercontent.com/TacoBadger/Soccer-Database/e01e9cb3438da6373dd5f4424438c4143b1eaaa4/banner.png)

## Analysis of the Soccer Database in Kaggle
Worked with the dataset to find relationships and the best teams per league. This is a public data made available through Kaggle. Please do take note that this document is for practice purposes only and it does not include any marketing of the titles, names or the whole dataset involved.

## Dataset
We will manually upload all the dataset used but when it passes the limit of the size, we will always link it below.
- [European Soccer Database](https://www.kaggle.com/datasets/hugomathien/soccer)

## Author
- [@TacoBadger](https://github.com/TacoBadger)

## Methods
- [Motivation](#motivation)
- [Looking for Trends and Patterns](#looking-for-trends-and-patterns)
- [Important Definitions](#important-definitions)
- [Importing the dataset from Kaggle to see what tables we have in the dataset](#importing-the-dataset-from-kaggle-to-see-what-tables-we-have-in-the-dataset)
- [Checking unique values and columns](#checking-unique-values-and-columns)
- [Let's do some Data Analysis](#lets-do-some-data-analysis)
- [ggplot Visualizations](#ggplot-visualizations)
- [Comparison of Average Points Per Game](#comparison-of-average-points-per-game)
- [Key Findings](#key-findings)
- [Explore our Tableau Public](#explore-our-tableau-public)
- [Explore our Notebook](#explore-our-notebook)
- [What is Next?](#what-is-next)

## Motivation
For this public dataset we will practice using [RStudio](https://rstudio.cloud/). We will be practicing more functions/commands and the ggplot for visualization.

**RStudio** is an integrated development environment for R, a programming language for statistical computing and graphics. It is available in two formats: RStudio Desktop is a regular desktop application while RStudio Server runs on a remote server and allows accessing RStudio using a web browser.

## Important Definitions
**RStudio** is an integrated development environment for R, a programming language for statistical computing and graphics. It is available in two formats: RStudio Desktop is a regular desktop application while RStudio Server runs on a remote server and allows accessing RStudio using a web browser.

We will perform the following queries:
- sorting and filtering
- creating data frames or new data tables
- common queries
- combining tables
- mutate function
- loops

**Mutate** function is used to create a new variable from a data set. In order to use the function, we need to install the dplyr package, which is an add-on to R that includes a host of cool functions for selecting, filtering, grouping, and arranging data.

A **loop** is defined as a segment of code that executes multiple times. Iteration refers to the process in which the code segment is executed once. One iteration refers to 1-time execution of a loop. A loop can undergo many iterations.

## Looking for Trends and Patterns
So here a few things we will look into:
- **Comparison of Average Points Per Game for each Team in Home and Away Games**
- **Top Teams in Soccer Leagues by winning percentage**

## Importing the dataset from Kaggle to see what tables we have in the dataset
It will always start with downloading the dataset as a zip file and you can extract it wherever you want on your computer.
We will be using RStudio, so I uploaded the database in  the lower right panel where it says files.
You can see that it is a SQL Database, so we need to install the packages to help us retrieve the data.





