## Soccer Database
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
So here is a few things we will look into and practice with this public dataset:
- **Comparison of Average Points Per Game for each Team in Home and Away Games**
- **Top Teams in Soccer European Leagues by winning percentage**

## Importing the dataset from Kaggle to see what tables we have in the dataset
It will always start with downloading the dataset as a zip file and you can extract it wherever you want on your computer.

We will be using RStudio, so I uploaded the database in  the lower right panel where it says files. You can see that it is a SQL Database, so we need to install the packages to help us retrieve the data.

We will now load the packages needed so we can retrieve the dataset.

```bash 
# Packages needed to prepare the data
install.packages("ggplot2")  # this is for visualization
install.packages("RSQLite") # this is for retrieving the data
install.packages("dplyr")

# load the packages
library(ggplot2)
library(RSQLite)
library(dplyr)
```
You can get an overview of the columns in the kaggle dataset. So from here we will retrieve the data that we need, make sure you installed and loaded all the packages that we need.

## Connecting to the database to R
```bash 
# connecting the database to R
db <- dbConnect(SQLite(),"../project/database.sqlite")
Country <- dbGetQuery(db,"Select * from Country")
Match <- dbGetQuery(db,"Select * from Match")
League <- dbGetQuery(db,"Select * from League")
Team <- dbGetQuery(db,"Select * from Team")
Player <- dbGetQuery(db,"Select * from Player")
Player_Attributes <- dbGetQuery(db,"Select * from Player_Attributes")
```
The use of **DBConnect(SQLite)** is to let us connect to the data base in RStudio.

The **DBGetQuery()** in the command allows us to write and retrieve the result in t he SQL Lite Data file, it has to be the same query as SQL so it will match the database.

## Checking unique values and columns
We have now connected and added the database in our RStudio with the previous code or function.

In this example we used view for RStudio. You can get an overview as it opens up a new tab for each dataframe or dataset.

The **View()** function in R can be used to invoke a spreadsheet-style data viewer within RStudio. Then we can proceed to data analysis.

```bash 
# checking all the tables
View(Country)
View(Match)
View(League)
View(Team)
View(Player)
View(Player_Attributes)
```

## Let's do some Data Analysis
We will create a new dataset or data table to count the matches of the teams and also do a calculation for the winning percentage. We will use COUNT in this example.
- COUNT(ALL expression) evaluates expression for each row in a group, and returns the number of non null values.

The **FIRST STEP IS TO COUNT THE MATCHES A PARTICULAR TEAM HAS PLAYED AT HOME AND AWAY**

```bash 
# Count the number of matches the particular team played at home
home_match = count(Match,home_team_api_id)
away_match = count(Match,away_team_api_id)

# View the dataset
View(home_match)
View(away_match)
```
Now let's change the column "n" to number of matches.
```bash 
#change names of the column n to number of matches
names(home_match)[names(home_match)=="n"] <- "home_matches_number"
names(away_match)[names(away_match)=="n"] <- "away_matches_number"
```
Then we will combine the data we made so it will be easier to work with.
```bash 
#Combine these two dataframes
new_match_data <- cbind(home_match,away_match)
```
**Column Bind or Cbind** function stands for column-bind. The cbind function is used to combine vectors, matrices and/or data frames by columns.

Then to the next part of the analysis. 

## Creating new columns for the anaylsis
We will add three columns to the new data to organize and calculate the winning percentage.
- Total Matches a particular team has played?
- How many matches the team has won at home and away?
- What is the winning percentage?

```bash 
#Add three new columns,
# a. total matches a particular team has played
# b. how many matches the team has won at home and away
# c. what is the winning percentage. (wins/total_matches * 100)
new_match_data <- new_match_data %>% mutate(
  total_matches = home_matches_number + away_matches_number,
  wins = 0,
  win_percentage = 0,
  country = "",
  team_name = ""
)

for(row1 in rownames(new_match_data))
{
  home_indexes = which(Match$home_team_api_id == new_match_data$home_team_api_id[as.numeric(row1)])
  new_match_data$country[as.numeric(row1)] <- Country$name[Country$id==Match$country_id[as.numeric(home_indexes[1])]]
  new_match_data$team_name[as.numeric(row1)] <- Team$team_long_name[Team$team_api_id==new_match_data$home_team_api_id[as.numeric(row1)]]
}
# the code above also indicates to make a new row which the datatpye is numeric.

# then you can view the data
View(new_match_data)
```

We used the **Mutate** function for this step.

**Mutate** is used to create a variable from a dataset. This package is included in the dyplr package.

After a few hours of researching also what the whole code is called, it is called a loop. There are lots of type of loop but let's just focus on this one first.

Based on a community member in RStudio Discord,
*The first line finds which row of Match has the same team id as that of a given row of new_match_data. The second line adds the country_id of that row from Match to the new_match_data data frame. The third does the same thing for the team name.
Essentially, there are several data frames that contain data on aspects of each team, and each contains a key column that can be matched to  new_match_data$home_team_api_id to link the correct data to each team.*
*This code looks correct, but it could be improved. There's no need for a for loop here, since the functions used are all vectorized (they treat a vector as a single argument, so it's wasteful to pass elements of the vector individually as arguments). Instead of the for loop, it would be better to use the match function or dplyr::inner_join to join the data frames directly (since the code uses the dplyr package)*

So this is the easiest way to understand the code.

We will also drop the unnecessary columns "Home matches and away matches"
```bash
#Drop the unnecessary columns "home_matches_number" and "away_matches_number"
drops_columns <- c("home_matches_number","away_matches_number")
new_match_data <- new_match_data[ , !(names(new_match_data) %in% drops_columns)]
```
Then the following code will just print the notice that unnecessary columns has been dropped and view the new data.
```bash
print("Removed unnecessary columns")
print(head(new_match_data))
```
The **head()** function is used to display the first number of rows in the data frame or in the input data frame.




