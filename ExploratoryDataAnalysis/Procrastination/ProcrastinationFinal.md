MSDS Procrastination
================
Lizzy Sterling and Casey Martinez
12/06/2017

We are interested in looking at global procrastination information in order to dtermine how we can help students in the MSDS program at Southern Methodist University. After data collection, we now have a complete set of data which can be found in `Procrastination.csv`. As we discussed, there are a few variables in particular that are worth looking at when conducting analysis. These include age, annual income, and country of origin.

Key Questions
-------------

-   What are the top 15 nations in average procrastination scores? (In terms of DP, AIP, and GP)
-   How many nations, if any, show up in both of these categories?
-   Is there a relationship between Age and Income? If so, what are some potential reasons for this?
-   Is there a relationship between Life Satisfaction and HDI?

### Libraries used

library(XML)
library(rvest)
library(dplyr)
library(ggplot2)
library(ggthemes)
library(plyr)
library(knitr)

``` r
knitr::opts_knit$set(root.dir = '/Users/lizzysterling/Desktop/CaseStudy2/Analysis/Data')
#knitr::opts_knit$set(root.dir = "~/SMU Data Science/MSDS_6306_Doing_Data_Science/CaseStudy2/Analysis/Data")
```

Data Cleaning
-------------

Although we finished data collection, there is still some tidying to do. As you can see, it is a rather large data set, so we need to go back through and make sure that all of the entries are ready for analysis.

``` r
#2A
Procrastination <- read.csv("Procrastination.csv", header = TRUE, stringsAsFactors = FALSE)
dim <- data.frame(nrow(Procrastination),ncol(Procrastination))
colnames(dim) <- c("Number of Rows", "Number of Columns")
kable(dim)
```

|  Number of Rows|  Number of Columns|
|---------------:|------------------:|
|            4264|                 61|

In order to make the data set human-readable, we need to change the column names so that they make more sense. *You can reference the README.md file in the `Data` folder for further descriptions of these columns (columns 15 to 61 in particular)*

``` r
#2B
names(Procrastination) <- c("Age", "Gender", "Kids", "Education", "WorkStatus", "AnnualIncome", "Occupation", "OccupationYears", "OccupationMonths", "CommunitySize", "CountryOfResidence", "MaritalStatus", "NumberSons", "NumberDaughters", "DP1", "DP2", "DP3", "DP4", "DP5", "AIP1", "AIP2", "AIP3", "AIP4", "AIP5", "AIP6", "AIP7", "AIP8", "AIP9", "AIP10", "AIP11", "AIP12", "AIP13", "AIP14", "AIP15", "GP1", "GP2", "GP3", "GP4", "GP5", "GP6", "GP7", "GP8", "GP9", "GP10", "GP11", "GP12", "GP13", "GP14", "GP15", "GP16", "GP17", "GP18", "GP19", "GP20", "SWLS1", "SWLS2", "SWLS3", "SWLS4", "SWLS5", "P1", "P2")
```

Now that we have human-readable column names, let's take a look at the data:

``` r
head(Procrastination)
```

    ##    Age Gender     Kids Education WorkStatus AnnualIncome Occupation
    ## 1 67.5   Male Yes Kids        ma    retired        25000           
    ## 2 45.0   Male Yes Kids       deg  part-time        35000           
    ## 3 19.0 Female  No Kids       dip    student           NA           
    ## 4 37.5   Male Yes Kids        ma  full-time        45000           
    ## 5 28.0 Female  No Kids       deg  full-time        35000           
    ## 6 23.0 Female  No Kids       deg  full-time        15000           
    ##   OccupationYears OccupationMonths CommunitySize CountryOfResidence
    ## 1         9.0e+00                0    Large-City        El Salvador
    ## 2         1.5e-19                0       Village            Bolivia
    ## 3         0.0e+00                0    Large Town             Cyprus
    ## 4         1.4e+01                0    Large Town     Czech Republic
    ## 5         1.0e+00                0       Village     Czech Republic
    ## 6         1.0e+00                0    Small Town     Czech Republic
    ##   MaritalStatus NumberSons NumberDaughters DP1 DP2 DP3 DP4 DP5 AIP1 AIP2
    ## 1      Divorced          0               5   3   1   1   1   1    1    1
    ## 2       Married       Male               1   3   4   3   3   3    3    1
    ## 3        Single          0               0   5   5   2   3   3    5    4
    ## 4       Married          0               1   3   3   3   3   3    2    1
    ## 5        Single          0               0   3   3   2   1   1    1    1
    ## 6        Single          0               0   3   4   3   2   2    2    5
    ##   AIP3 AIP4 AIP5 AIP6 AIP7 AIP8 AIP9 AIP10 AIP11 AIP12 AIP13 AIP14 AIP15
    ## 1    1    1    1    1    1    1    5     1     1     1     1     1     3
    ## 2    4    3    3    4    3    3    3     3     4     2     2     2     4
    ## 3    4    5    5    5    5    4    5     5     4     3     5     4     3
    ## 4    4    3    5    3    4    5    4     5     4     3     4     2     1
    ## 5    3    3    2    2    2    2    1     1     2     1     2     1     2
    ## 6    5    5    5    3    5    4    4     5     3     5     4     5     5
    ##   GP1 GP2 GP3 GP4 GP5 GP6 GP7 GP8 GP9 GP10 GP11 GP12 GP13 GP14 GP15 GP16
    ## 1   1   1   1   1   1   1   1   1   1    1    5    1    1    1    1    1
    ## 2   4   2   2   2   2   2   4   2   4    2    3    4    2    2    3    4
    ## 3   5   2   2   4   3   1   3   2   5    4    5    5    3    4    5    2
    ## 4   4   1   3   3   2   3   4   5   4    1    3    4    3    3    4    4
    ## 5   4   1   2   4   5   2   4   2   4    1    2    3    2    4    3    2
    ## 6   5   5   2   5   4   4   5   4   4    3    4    4    3    4    4    4
    ##   GP17 GP18 GP19 GP20 SWLS1 SWLS2 SWLS3 SWLS4 SWLS5  P1  P2
    ## 1    1    5    1    5     5     5     5     5     5  no  no
    ## 2    3    3    4    4     3     4     4     4     3 yes yes
    ## 3    3    5    5    4     2     2     2     3     4 yes yes
    ## 4    3    4    5    1     2     4     2     2     2 yes yes
    ## 5    3    2    3    4     4     4     4     3     4  no  no
    ## 6    4    4    4    4     3     2     4     4     3 yes yes

We need to change the `Age` column so that they are in whole numbers.

``` r
Procrastination$Age <- as.integer(Procrastination$Age)
```

The other column needs to be converted to factor variables.

``` r
Procrastination$Gender <- as.factor(Procrastination$Gender)

Procrastination$WorkStatus <- as.factor(Procrastination$WorkStatus)
levels(Procrastination$WorkStatus)[levels(Procrastination$WorkStatus) == "0"] <- ""

Procrastination$P1 <- as.factor(Procrastination$P1)
Procrastination$P2 <- as.factor(Procrastination$P2)
levels(Procrastination$P2)[levels(Procrastination$P2) == "0"] <- ""
levels(Procrastination$P2)[levels(Procrastination$P2) == "4"] <- ""
```

We also need to make the values of `OccupationYears` to integers and then determine which years do not makes sense.

``` r
#Question #2C
Procrastination$OccupationYears <- as.integer(Procrastination$OccupationYears)
OccupationYears <- data.frame(unique(Procrastination$OccupationYears))
colnames(OccupationYears) <- "OccupationYears"
kable(OccupationYears)
```

|  OccupationYears|
|----------------:|
|                9|
|                0|
|               14|
|                1|
|                8|
|               NA|
|                2|
|                6|
|                5|
|                3|
|                7|
|               10|
|               30|
|               17|
|                4|
|               12|
|               23|
|               13|
|               28|
|              999|

We are going to change all of the 999 entries to NAs, because this is likely a mistake.

``` r
#Question 2ci
Procrastination[grep("999", Procrastination$OccupationYears), "OccupationYears"] <- ""
```

Now looking at the `NumberSons` column, we noticed that some of the entries are `Male` and `Female` rather than integer values.

``` r
NumberSons <- data.frame(unique(Procrastination$NumberSons))
colnames(NumberSons) <- "Number of Sons"
kable(NumberSons)
```

| Number of Sons |
|:---------------|
| 0              |
| Male           |
| Female         |
| 4              |
| 3              |
| 10             |
| 8              |
| 5              |
|                |

We know that in this column, `Male`= 1 Son and `Female` = 2 Sons. Therefore, we need to change each Male and Female entry to their corresponding numbers so that they can be counted correctly.

``` r
Procrastination[grep("^Male.*", Procrastination$NumberSons),"NumberSons"] <- rep(1, length(Procrastination[grep("^Male.*", Procrastination$NumberSons),"NumberSons"]))

Procrastination[grep("^Female.*", Procrastination$NumberSons),"NumberSons"] <- rep(2, length(Procrastination[grep("^Female.*", Procrastination$NumberSons),"NumberSons"]))
```

In our `CountryOfResidence` column, we only want values that are really countries.

``` r
Country <- data.frame(unique(Procrastination$CountryOfResidence))
colnames(Country) <- "Countries"
kable(head(sort(Country$Countries), n=10), col.names = "Countries")
```

| Countries   |
|:------------|
|             |
| 0           |
| Afghanistan |
| Albania     |
| Algeria     |
| Andorra     |
| Antigua     |
| Argentina   |
| Australia   |
| Austria     |

Because 0 is not a country of residence, we need to change it to a NA.

``` r
Procrastination[grep("0", Procrastination$CountryOfResidence), "CountryOfResidence"] <- ""
```

Looking at `Occupation`, we want to get rid of values that arent really jobs, as well as combine like-jobs together.
\* Due to the lenght of observation inputs, we decided to save them into a separate csv file, `Occupations.csv`, which can be found in the data section of our GitHub repository \*

``` r
Occupation <- data.frame(unique(Procrastination$Occupation))
write.csv(Occupation, "Occupation.csv",row.names = FALSE)
colnames(Occupation) <- "Occupation"
kable(head(sort(Occupation$Occupation), n=10), col.names = "Occupations")
```

| Occupations            |
|:-----------------------|
|                        |
| Accountant             |
| acupuncturist          |
| admin assist           |
| anthropologist         |
| Assistant              |
| Attorney-self employed |
| bookseller             |
| Communications         |
| consultant             |

First, we notice that `please specify` and `0` are not occupations, we can get rid of them.

``` r
Procrastination[grep("0", Procrastination$Occupation), "Occupation"] <- ""
Procrastination[grep("please specify", Procrastination$Occupation), "Occupation"] <- ""
```

Because this was a fill in the blank type question, there are many answers that could be grouped with others. We can group these occupations together so that we can get accurate counts. Some examples include entries that include `Teachers` and entries that include `Computer`.

``` r
Procrastination[(grep("teacher", Procrastination$Occupation)), "Occupation"] <- "Teacher"
Procrastination[(grep("Computer", Procrastination$Occupation)), "Occupation"] <- "Computers"
```

Now that all of the columns are clean, we need to make sure they are of the correct data types.

``` r
kable(sapply(Procrastination, class), col.names="Class Type")
```

|                    | Class Type |
|--------------------|:-----------|
| Age                | integer    |
| Gender             | factor     |
| Kids               | character  |
| Education          | character  |
| WorkStatus         | factor     |
| AnnualIncome       | integer    |
| Occupation         | character  |
| OccupationYears    | character  |
| OccupationMonths   | integer    |
| CommunitySize      | character  |
| CountryOfResidence | character  |
| MaritalStatus      | character  |
| NumberSons         | character  |
| NumberDaughters    | integer    |
| DP1                | integer    |
| DP2                | integer    |
| DP3                | integer    |
| DP4                | integer    |
| DP5                | integer    |
| AIP1               | integer    |
| AIP2               | integer    |
| AIP3               | integer    |
| AIP4               | integer    |
| AIP5               | integer    |
| AIP6               | integer    |
| AIP7               | integer    |
| AIP8               | integer    |
| AIP9               | integer    |
| AIP10              | integer    |
| AIP11              | integer    |
| AIP12              | integer    |
| AIP13              | integer    |
| AIP14              | integer    |
| AIP15              | integer    |
| GP1                | integer    |
| GP2                | integer    |
| GP3                | integer    |
| GP4                | integer    |
| GP5                | integer    |
| GP6                | integer    |
| GP7                | integer    |
| GP8                | integer    |
| GP9                | integer    |
| GP10               | integer    |
| GP11               | integer    |
| GP12               | integer    |
| GP13               | integer    |
| GP14               | integer    |
| GP15               | integer    |
| GP16               | integer    |
| GP17               | integer    |
| GP18               | integer    |
| GP19               | integer    |
| GP20               | integer    |
| SWLS1              | integer    |
| SWLS2              | integer    |
| SWLS3              | integer    |
| SWLS4              | integer    |
| SWLS5              | integer    |
| P1                 | factor     |
| P2                 | factor     |

We want to change both the `OccupationYears` and `NumberSons` into numeric values.

``` r
Procrastination$OccupationYears <- as.numeric(Procrastination$OccupationYears)
Procrastination$NumberSons <- as.integer(Procrastination$NumberSons)
```

Although all of this is interesting, the procrastination scales are what we are really interested in, so we are going to take the means of each row to create mean scores for each individual. These new rows are `DPmean`, `AIPmean`, `GPmean`, and `SWLSmean`.

``` r
Procrastination$DPmean <- rowMeans(Procrastination[15:19],na.rm = TRUE)
Procrastination$AIPmean <- rowMeans(Procrastination[20:34],na.rm = TRUE)
Procrastination$GPmean <- rowMeans(Procrastination[35:54],na.rm = TRUE)
Procrastination$SWLSmean <- rowMeans(Procrastination[55:59],na.rm = TRUE)
```

### Scraping the HDI Data

Now that `Procrastination.csv` is tidy, we noticed that there are many countries included in the data set. We thought, wouldn't it be interesting to see how the Human Development Index of each country relates to its respective rate of procrastination.
To do this, we took data found on this website <https://en.wikipedia.org/wiki/List_of_countries_by_Human_Development_Index#Complete_list_of_countries>.

``` r
#3A
link = "https://en.wikipedia.org/wiki/List_of_countries_by_Human_Development_Index#Complete_list_of_countries"
doc <- read_html(link) 
node <- html_nodes(doc,"table")
tables <- html_table(node, fill = TRUE)
```

From this data, we needed to create data frames from the tables collected.

``` r
df1 <- as.data.frame(tables[4]) #Very High Human Development1
df2 <- as.data.frame(tables[5]) #Very High Human Development2
df3 <- as.data.frame(tables[7]) #High Human Development1
df4 <- as.data.frame(tables[8]) #High Human Development2
df5 <- as.data.frame(tables[10]) #Medium Human Development1
df6 <- as.data.frame(tables[11]) #Medium Human Development2
df7 <- as.data.frame(tables[13]) #Low Human Development1
df8 <- as.data.frame(tables[14]) #Low Human Development2
```

``` r
#3B
df1 <- df1[-1,3:4]
colnames(df1) <- c('Country','HDI')
df1$Category <- "Very High"

df2 <- df2[-1,3:4]
colnames(df2) <- c('Country','HDI')
df2$Category <- "Very High"

df3 <- df3[-1,3:4]
colnames(df3) <- c('Country','HDI')
df3$Category <- "High"

df4 <- df4[-1,3:4]
colnames(df4) <- c('Country','HDI')
df4$Category <- "High"

df5 <- df5[-1,3:4]
colnames(df5) <- c('Country','HDI')
df5$Category <- "Medium"

df6 <- df6[-1,3:4]
colnames(df6) <- c('Country','HDI')
df6$Category <- "Medium"

df7 <- df7[-1,3:4]
colnames(df7) <- c('Country','HDI')
df7$Category <- "Low"

df8 <- df8[-1,3:4]
colnames(df8) <- c('Country','HDI')
df8$Category <- "Low"
```

Now that we have the data organized, we can combine them together and create a new csv `HumanDevelopmentIndex.csv`

``` r
df <- rbind(df1,df2,df3,df4,df5,df6,df7,df8)
write.csv(df, "HumanDevelopmentIndex.csv",row.names = FALSE)
```

Analysis
--------

Now that the scraped data is saved into a file in the data folder, we need to combine it with our original data into a new variable `Pro`.

``` r
#3C
HDI <- read.csv("HumanDevelopmentIndex.csv", header=TRUE)
Pro <- merge(x = Procrastination,y = HDI, by.x = 'CountryOfResidence', by.y = "Country")
```

### Adult Analysis

From here-on-out, we only want to look at adult observations, so we are going to remove participants who are under 18. This new variable is `ProAdult` and this is the set we are going to be using for the rest of the analysis.

``` r
#4A
ProAdult <- Pro[Pro$Age > 18,]
```

The variables that we are most interested in are age, income, HDI, and the procrastination scale scores. We started by getting their summary statistics.

``` r
#4B
row <- c("Minimum", "First Quartile", "Median", "Mean", "3rd Quartile", "Maximum", "NA's")
col <- c("Statistics for Age", "Statistics for Annual Income", "Statistics for Human Development Index", "Statistics for Decisional Procrastination", "Statistics for Generalized Procastination", "Statistics for Adult Procrastination", "Statistics for Satisfaction with Life Scale")

AgeSummary <- summary(ProAdult$Age)
IncomeSummary <- summary(ProAdult$AnnualIncome)
HDISummary <- summary(ProAdult$HDI)
DPSummary <- summary(ProAdult$DPmean)
GPSummary <- summary(ProAdult$GPmean)
AIPSummary <- summary(ProAdult$AIPmean)
SWLSSummary <- summary(ProAdult$SWLSmean)

m <- cbind(AgeSummary, HDISummary, DPSummary, GPSummary, AIPSummary, SWLSSummary)
mat <- matrix(m, nrow = 7, ncol = 7, dimnames = list(row,col))
kable(mat, row.names=TRUE, digits = c(0,2,4,4,4,4,4))
```

|                |  Statistics for Age|  Statistics for Annual Income|  Statistics for Human Development Index|  Statistics for Decisional Procrastination|  Statistics for Generalized Procastination|  Statistics for Adult Procrastination|  Statistics for Satisfaction with Life Scale|
|----------------|-------------------:|-----------------------------:|---------------------------------------:|------------------------------------------:|------------------------------------------:|-------------------------------------:|--------------------------------------------:|
| Minimum        |                  19|                          0.48|                                  1.0000|                                     1.0000|                                     1.0000|                                1.0000|                                      19.0000|
| First Quartile |                  28|                          0.92|                                  2.4000|                                     2.8000|                                     2.4000|                                2.4000|                                      28.0000|
| Median         |                  37|                          0.92|                                  3.0000|                                     3.2500|                                     2.9333|                                3.0000|                                      37.0000|
| Mean           |                  38|                          0.91|                                  3.0487|                                     3.2382|                                     2.9640|                                3.0433|                                      38.2242|
| 3rd Quartile   |                  45|                          0.92|                                  3.8000|                                     3.7500|                                     3.5333|                                3.8000|                                      45.0000|
| Maximum        |                  80|                          0.95|                                  5.0000|                                     5.0000|                                     5.0000|                                5.0000|                                      80.0000|
| NA's           |                   6|                          6.00|                                  6.0000|                                     6.0000|                                     6.0000|                                6.0000|                                       6.0000|

We are most interested in age and income, so we graphed them to see their distributions.

From the age distribution, you can see that there were more younger participants than older participants. Although the distribution is somewhat normal, there is definitely a stronger tendency towards the younger individuals.

``` r
ggplot(ProAdult, aes(Age)) +
  geom_histogram() + 
  labs(x="Age",y="Frequency") + 
  ggtitle("Age of Procrastinators") +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 6 rows containing non-finite values (stat_bin).

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-27-1.png)

From the income histogram, you can see that there is a right skew with the majority of individuals earning less than $50,000 a year.

``` r
ggplot(ProAdult, aes(AnnualIncome)) +
  geom_histogram() + 
  labs(x="Income",y="Frequency") + 
  ggtitle("Annual Income of Adult Procrastinators") +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5))
```

    ## `stat_bin()` using `bins = 30`. Pick better value with `binwidth`.

    ## Warning: Removed 348 rows containing non-finite values (stat_bin).

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-28-1.png)

In addition to these variables, we are going to look at gender, work status, and occupation.
In terms of Gender, more females than males took the survey. For the most part, the responders to this survey have full-time jobs, with the most jobs reported being `Teacher`.

``` r
#Question 4C

q4c.gender <- count(ProAdult,'Gender')
q4c.gender2 <- q4c.gender[order(q4c.gender$freq, decreasing = TRUE),]
kable(q4c.gender2,row.names=FALSE)
```

| Gender |  freq|
|:-------|-----:|
| Female |  2194|
| Male   |  1645|
| NA     |     6|
|        |     5|

``` r
q4c.work <- count(ProAdult,'WorkStatus')
q4c.work2 <- q4c.work[order(q4c.work$freq, decreasing = TRUE),]
kable(q4c.work2,row.names=FALSE)
```

| WorkStatus |  freq|
|:-----------|-----:|
| full-time  |  2188|
| student    |   779|
| part-time  |   452|
| unemployed |   240|
| retired    |   161|
|            |    24|
| NA         |     6|

``` r
q4c.occupation <- count(ProAdult,'Occupation')
q4c.occupation2 <- head(q4c.occupation[order(q4c.occupation$freq, decreasing = TRUE),],n=15)
kable(q4c.occupation2,row.names=FALSE)
```

| Occupation         |  freq|
|:-------------------|-----:|
|                    |  2499|
| Teacher            |    81|
| college professor  |    43|
| engineer           |    31|
| manager            |    31|
| Attorney           |    30|
| retired            |    27|
| Editor             |    21|
| Marketing          |    20|
| attorney           |    19|
| writer             |    19|
| Unemployed         |    18|
| houswife           |    16|
| Software Developer |    16|
| Doctor; Physician  |    14|

We are going to look at how many people per country took our survey.
The most respondants live in the United States of America, with Canada and the United Kingdom coming in as far second and third.

``` r
#Question 4D

q4d <- count(ProAdult,'CountryOfResidence')
q4d2 <- head(q4d[order(q4d$freq, decreasing = TRUE),],n=15)
kable(q4d2,row.names=FALSE)
```

| CountryOfResidence |  freq|
|:-------------------|-----:|
| United States      |  2785|
| Canada             |   243|
| United Kingdom     |   177|
| Australia          |    99|
| India              |    78|
| Italy              |    62|
| Germany            |    36|
| Brazil             |    20|
| Ireland            |    19|
| Netherlands        |    18|
| Sweden             |    15|
| Norway             |    14|
| France             |    13|
| Japan              |    13|
| Spain              |    13|

We thought it would be interesting to know how many people have an accurate view of whether or not they are a procrastinator. We did this by finding out how many people who reported to yes, I am a procrastinator also reported yes, others think I am a procrastinator and vice versa.
We found that people were more likely to think they are a procrastinator and have other people think the same than vice versa.

``` r
#Question 4e

q4e <- data.frame(ProAdult$P1,ProAdult$P2)
colnames(q4e) <- c('P1','P2')
q4e2 <- sapply(q4e,function(x){
                                ifelse(q4e$P1 == 'yes' & q4e$P2 == 'yes', 'Both Agree',
                                ifelse(q4e$P1 == 'no' & q4e$P2 == 'no', 'Both Disagree',
                                ''))
                              })
q4e2 <- as.data.frame(q4e2[,1])
q4e3 <- count(q4e2)
#colnames(q4e3) <- c('Match','Freq')

kable(q4e3,row.names = FALSE,col.names = c('Match','Frequency'))
```

| Match         |  Frequency|
|:--------------|----------:|
|               |       1133|
| Both Agree    |       2253|
| Both Disagree |        458|
| NA            |          6|

After this, we are very interested in which nations have the highest reports of procrastination.

``` r
#5B
df3 <- aggregate(DPmean ~ CountryOfResidence + Category, data = ProAdult, mean)
df3 <- head(df3[order(df3$DPmean, decreasing = TRUE),], n=15)

ggplot(data = df3, aes(y = DPmean,x = reorder(CountryOfResidence, DPmean))) + 
  geom_col(aes(fill = Category)) +
  coord_flip() +
  labs(x = 'Country',y = 'DP Mean',title = 'Top 15 Nations Average Procrastination Scores') +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5)) + 
  theme(axis.text.x = element_text(size=7))
```

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-32-1.png)

``` r
#5C
df4 <- aggregate(GPmean ~ CountryOfResidence + Category, data = ProAdult, mean)
df4 <- head(df4[order(df4$GPmean, decreasing = TRUE),], n=15)

ggplot(data = df4, aes(y = GPmean,x = reorder(CountryOfResidence, GPmean))) + 
  geom_col(aes(fill = Category)) +
  coord_flip() +
  labs(x = 'Country',y = 'DP Mean',title = 'Top 15 Nations Average Procrastination Scores') +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5)) + 
  theme(axis.text.x = element_text(size=7))
```

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-33-1.png) We found that Qatar, Panama, Sri Lanka, Austria, Ecuador, Slovenia, and Portugal all are included in the top procrastination scores when looking at both the Decisional Procrastination and General Procrastination Scales. This tells us that these are the countries that are most likely to procrastinate, based on these two scales.

We want to look at how age and income are related to see if there is a pattern. You can see from the graph that there is definitely a linear trend. Although there is significance (p-value &lt; .00001), only about 19.7% of the variance in income can be explained by age (r-square = .1996). From these statistics, we know that there is a relationship, but annual income does not depend on age alone.

``` r
#Question 5d
ggplot(ProAdult, aes(Age, log(AnnualIncome))) + 
  geom_point(aes(color = Gender),stat = "identity") +
  labs(x = 'Age',y = 'log of Income',title = 'Relationship Between Age and Income') +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5)) +
  geom_smooth(method = 'lm')
```

    ## Warning: Removed 348 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 348 rows containing missing values (geom_point).

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-34-1.png)

``` r
Q5dlm <- lm(log(AnnualIncome) ~ Age, data = ProAdult)
summary(Q5dlm)
```

    ## 
    ## Call:
    ## lm(formula = log(AnnualIncome) ~ Age, data = ProAdult)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2.63845 -0.66928  0.02287  0.58310  2.29448 
    ## 
    ## Coefficients:
    ##             Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 9.443094   0.041935  225.18   <2e-16 ***
    ## Age         0.030071   0.001027   29.28   <2e-16 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.8198 on 3500 degrees of freedom
    ##   (348 observations deleted due to missingness)
    ## Multiple R-squared:  0.1968, Adjusted R-squared:  0.1966 
    ## F-statistic: 857.5 on 1 and 3500 DF,  p-value: < 2.2e-16

We then would like to see if there is a relationship between Life Satisfaction and HDI. From the graph, you can see that there is a linear trend, but it does not seem very strong. Looking at the statistics, we find that this relationship is linear (p-value=.0113), but only .14% of the variance in HDI can be attributed to satisfaction with life. From this, we know that there are definitely more and better variables that explain HDI, although there is some relationship between the two.

``` r
#5E
ggplot(ProAdult, aes(SWLSmean, HDI)) + 
  geom_point() +
  labs(x = 'HDI',y = 'Mean SWLS',title = 'Relationship Between Life Satesfaction Score and Human Development Index') +
  theme_economist() +
  theme(plot.title = element_text(hjust = 0.5)) +
  geom_smooth(method = 'lm')
```

    ## Warning: Removed 6 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 6 rows containing missing values (geom_point).

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-35-1.png)

``` r
Q5elm <- lm(HDI ~ SWLSmean, data = ProAdult)
summary(Q5elm)
```

    ## 
    ## Call:
    ## lm(formula = HDI ~ SWLSmean, data = ProAdult)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -0.42598  0.01170  0.01359  0.01596  0.04780 
    ## 
    ## Coefficients:
    ##              Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept) 0.8983525  0.0029855 300.901   <2e-16 ***
    ## SWLSmean    0.0023687  0.0009346   2.534   0.0113 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.05624 on 3842 degrees of freedom
    ##   (6 observations deleted due to missingness)
    ## Multiple R-squared:  0.001669,   Adjusted R-squared:  0.001409 
    ## F-statistic: 6.423 on 1 and 3842 DF,  p-value: 0.0113

That being said, it is very interesting to look at the barchart below which shows that the category `Very High`, which correspondes to HDI, is significantly greater in terms of Life Satisfaction score. But from looking at the summary statistics, we can see that the overall significance is not very high (p-value = .1248) and we cannot assume that any of the categories effects life satisfaction differently than the others.

``` r
ggplot(ProAdult, aes(Category,SWLSmean)) + 
  geom_bar(aes(fill=Category), stat="identity") +
  theme_economist()
```

    ## Warning: Removed 6 rows containing missing values (position_stack).

![](ProcrastinationFinal_files/figure-markdown_github-ascii_identifiers/unnamed-chunk-36-1.png)

``` r
Q5elm <- lm(SWLSmean ~ Category, data = ProAdult)
summary(Q5elm)
```

    ## 
    ## Call:
    ## lm(formula = SWLSmean ~ Category, data = ProAdult)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -2.05063 -0.65063 -0.05063  0.74937  2.07500 
    ## 
    ## Coefficients:
    ##                    Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)        2.925000   0.103445  28.276   <2e-16 ***
    ## CategoryLow       -0.675000   0.496105  -1.361    0.174    
    ## CategoryMedium     0.004825   0.137700   0.035    0.972    
    ## CategoryVery High  0.125632   0.104689   1.200    0.230    
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 0.9704 on 3840 degrees of freedom
    ##   (6 observations deleted due to missingness)
    ## Multiple R-squared:  0.001495,   Adjusted R-squared:  0.0007144 
    ## F-statistic: 1.916 on 3 and 3840 DF,  p-value: 0.1248

``` r
#6A - `HumanDevelopmentIndex.csv`
#6B - `Procrastination2.csv`
#6C
```

Conclusion
----------

From these results, Dr. Engels is hoping to change the marketing of the program towards individuals who are less likely to procrastinate or towards countries that are less likely to procrastinate. That being said, everyone has a tendency to procrastinate, so it may be wise to determine a way to focus on helping people not procrastinate rather than targeting non-procrastinators.

Although this client for this analysis was the MSDS program at Southern Methodist University, both the data set and the analysis are global and can be applied to a variety of businesses.
