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
- [ggplot for this analysis](#ggplot-for-this-analysis)
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
- **Which league has the most of Average Points Per Game for each Team in Home and Away Games**
- **Top Teams in Soccer European Leagues, England Leauge, Spain and more by winning percentage**

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

## Creating additional analysis for the winning percentage and won matches
We will also use the new match data for this additional analysis, we will add the winning percentage and the won matches. We will create the home indexes and away indexes.

We will do the similar functions in the previous analysis.

```bash
for(id in rownames(new_match_data))
{
    #win_count stores the number of wins if the current team has scored more goals than the opponent team.
    win_count = 0
    #Find all the records in main "Match" table which match the current team id
    home_indexes = which(Match$home_team_api_id == new_match_data$home_team_api_id[as.numeric(id)])
    away_indexes = which(Match$away_team_api_id == new_match_data$away_team_api_id[as.numeric(id)])
    for(i in home_indexes)
    {
        if(Match$home_team_goal[i]>Match$away_team_goal[i])
        {
            win_count = win_count +1
        }
    }
    for(i in away_indexes)
    {
        if(Match$away_team_goal[i]>Match$home_team_goal[i])
        {
            win_count = win_count + 1
        }
    }
    new_match_data$wins[as.numeric(id)] <- win_count
    new_match_data$win_percentage[as.numeric(id)] <- as.double(win_count/new_match_data$total_matches[as.numeric(id)]*100)
}

# the code also indicates if the home team goal is highter than away that will count as 1 and vice versa for away then view the data
View(new_match_data)
```
I also researched about this code and it is called Loop and Iteration.

A **loop** is defined as a segment of code that executes multiple times. 
**Iteration** refers to the process in which the code segment is executed once. One iteration refers to 1-time execution of a loop. A loop can undergo many iterations.

Then we will drop the unwanted columns like before.

```bash
#Drop Away_team_id column and change home_team_api_id columns name to team_id
drop_columns <- c("away_team_api_id")
new_match_data <- new_match_data[ , !names(new_match_data) %in% drop_columns]
names(new_match_data)[names(new_match_data)=="home_team_api_id"]<-"team_id"
```
Then we will sort out the data by renaming it sorted data.
```bash
#Sort the teams based on the winning percentage
sorted_data <- new_match_data[order(-new_match_data$win_percentage),]
print(sorted_data[0:10,])

# View the data
View(sorted_data)
```

## ggplot Visualizations
We will start with plotting all the visualizations for this dataset. A visual for each leagues.

**European League**
```bash
ggplot(data=sorted_data[0:10,], aes(x=team_name,y=win_percentage)) +
  geom_bar(stat="identity", fill="steelblue")+labs(x="Team",y="Winning percentage")+
  ggtitle("Top 10 teams in all European Leagues")+theme(axis.text.x = element_text(angle = 90, hjust = 1))
```
![](https://github.com/TacoBadger/Soccer-Database/blob/main/Visuals/Top%2010%20Teams%20in%20all%20European%20Leagues.png?raw=true)

The visual shows that **FC Barcelona** is the top team in European league based on winning percentage with other teams.
 
**England League**
```bash
#Find Best teams in England's England Premier League
english_team <- sorted_data[sorted_data$country=="England",]
england <- english_team[order(-english_team$win_percentage),][1:10,]
ggplot(data=england,aes(x=team_name,y=win_percentage))+
geom_bar(stat="identity",fill="turquoise")+labs(x="Team",y="Winning Percentage")+
ggtitle("Top 10 Teams in England Premier League (EPL)")+theme(axis.text.x = element_text(angle = 90, hjust = 1))
```
![](https://github.com/TacoBadger/Soccer-Database/blob/main/Visuals/English%20League.png?raw=true)

The visual shows that **Manchester United** is the top team in England league based on winning percentage with other teams.

**Spain Liga BBVA League**
```bash
#Find the top teams from spain league 
spain_teams <- sorted_data[sorted_data$country=="Spain",]
spain <- spain_teams[order(-spain_teams$win_percentage),][1:10,]
ggplot(data=spain,aes(x=team_name,y=win_percentage))+
  geom_bar(stat="identity",fill="darkseagreen")+labs(x="Team",y="Winning Percentage")+
  ggtitle("Top 10 teams in Spain LIGA BBVA League")+theme(axis.text.x = element_text(angle = 90, hjust = 1))
```
![](https://github.com/TacoBadger/Soccer-Database/blob/main/Visuals/Spain%20League.png?raw=true)

The visual shows that **FC Barcelona** is the top team in Spain league based on winning percentage with other teams.

**Germany League**
```bash
#Find top teams in Germany's l Bundesliga League
german_team <- sorted_data[sorted_data$country=="Germany",]
germany <- german_team[order(-german_team$win_percentage),][1:10,]
ggplot(data=germany,aes(x=team_name,y=win_percentage))+
  geom_bar(stat='identity',fill="firebrick ")+labs(x="Team",y="Winning Percentage")+
  ggtitle("Top 10 Teams in Germany's l Bundesliga League")+theme(axis.text.x = element_text(angle = 90, hjust = 1))
```
![](https://github.com/TacoBadger/Soccer-Database/blob/main/Visuals/Germany%20League.png?raw=true)

The visual shows that **FC Bayern Munich** is the top team in Spain league based on winning percentage with other teams.

Let us move on to the next analysis.

## Comparison of Average Points Per Game
Now we will move to comparison of Average Points per game for each Team in Home and Away Games. I made a new RScript file to plot and analyze this data.

Since we have the database in the project already we will just select the tables that we need.
```bash
league <- tbl_df(dbGetQuery(db,"SELECT * FROM League"))
team   <- tbl_df(dbGetQuery(db,"SELECT * FROM Team"))
match  <- tbl_df(dbGetQuery(db,"SELECT * FROM Match"))
```
Then we sort to only the data that we need for this analysis.
```bash
league <- select(league, id, name, country_id) %>% rename(league_id = id, league_name = name)
team   <- select(team, team_api_id, team_long_name, team_short_name)
match  <- select(match, league_id, home_team_api_id, away_team_api_id, home_team_goal, away_team_goal)
```

## Calculating the points for each match
Then we calculate points scored for each match and we will call it match points.

```bash
# Calculate the points scored for each match -> match_points
match_points <- match %>%
  mutate(home_team_points = if_else((home_team_goal > away_team_goal),3,if_else((home_team_goal == away_team_goal),1,0))) %>%
  mutate(away_team_points = if_else((home_team_goal > away_team_goal),0,if_else((home_team_goal == away_team_goal),1,3)))
 
 # this code is similar to getting the winning percentage on the previous analysis.
```
Then we proceed to calculating the average points for home and away.
```bash
# Calculate average home team points per game
home_points <- match_points %>%
  select(league_id, team_api_id = home_team_api_id, home_team_points) %>%
  group_by(league_id, team_api_id) %>%
  summarize(avg_home_ppg = mean(home_team_points))
  
# Calculate average away team points per game
away_points <- match_points %>%
  select(league_id, team_api_id = away_team_api_id, away_team_points) %>%
  group_by(league_id, team_api_id) %>%
  summarize(avg_away_ppg = mean(away_team_points))
```

Then we will start combining the data.
```bash
# Combine the data for the average home and away team points per game
all_points <- left_join(home_points, away_points, by = c("league_id", "team_api_id"))

# Add the average points scored per game (home and away)
# It's OK to take a simple average of the home and away averages as each team plays the same number of home and away games
all_points <- all_points %>%
  mutate(avg_ppg = (avg_home_ppg + avg_away_ppg)/2)
  
# Add the details of the league and the team to each record so that their names can be displayed on the plots
all_points <- left_join(all_points, league, by = "league_id")
all_points <- left_join(all_points, team, by = "team_api_id")
```

In the example above we used the LEFT JOIN, SUMMARIZE AND MEAN.

A **left join** in R is a merge operation between two data frames where the merge returns all of the rows from one table (the left side) and any matching rows from the second table. A left join in R will NOT return values of the second table which do not already exist in the first table.

**Summarize** As its name implies, the summarize function reduces a data frame to a summary of just one vector or value. Many times, these summaries are calculated by grouping observations using a factor or categorical variables first.

**Mean** function in R -mean()  calculates the arithmetic mean. mean() function calculates arithmetic mean of vector with NA values  and arithmetic mean of column in data frame. mean of a group can also calculated using mean() function in R by providing it inside the aggregate function. with mean() function we can also perform row wise mean using dplyr package and also column wise mean lets see an example of each.

- mean of the list of vector elements with NA values
- mean of a particular column of the dataframe in R
- Mean of multiple columns of a dataframe in R
- column wise mean of the dataframe using mean() function
- mean of the group in R dataframe using aggregate() and dplyr package
- Row wise mean of the dataframe in R using mean() function

## ggplot for this analysis
Then now we can start plotting our ggplot visualization.
```bash
ggplot(all_points, aes(x = avg_home_ppg, y = avg_away_ppg, color = league_name)) +
  geom_point() +
  xlim(0,3) +
  ylim(0,3) +
  labs(title = "Comparison of Average Points Per Game (PPG) for each Team\nin Home and Away Games",
       x = "Average Home PPG",
       y = "Average Away PPG") +
  theme(plot.title = element_text(hjust = 0.5))
```
![](https://github.com/TacoBadger/Soccer-Database/blob/main/Visuals/Comparison%20of%20Average%20Points%20per%20Game%20for%20each%20Team%20in%20Home%20and%20Away%20games.png?raw=true)

The visual shows **Switzerland Superleague** has the highest average points per game for all the teams in both away and home matches. 

## Key Findings
**The top teams in this leagues are:**
- **European League** FC Barcelona and Real Madrid CF tied with SL Benifica
- **England League**  Manchester United and Chelsea
- **Spain League** FC Barcelona and Real Madrid CF
- **German League** FC Bayern Munich and Borussia Dortmund

And for the League that has the most Average Points per game is the **Switzerland Superleague** for both away and home matches.

## Explore our Tableau Public
- [@TacoBadger](https://public.tableau.com/app/profile/taco.badger)

## Explore our Notebook
- [@TacoBadger](https://www.kaggle.com/code/cryptocosy/soccer-database)

## What is Next?
We have finished our practice! This dataset was the most difficult out of all the dataset I worked with.
- I could have also improved my analysis by adding more relationships or correlations of different values.
- I could also practice more types of ggplot regarding the additional analysis I could've made.
- I can also need to learn more advanced functions and commands for the next practice.

Thank you for reading my kernel or repositories!
