# GDP vs Country Income
Nathan Mowat  
June 14, 2016  


# Introduction:
### We will explore the relationship between the GDP ranking of a country to each country's average income. 
### In order to do this, we will be merging a GDP Ranking Table dataset with an Education Statistics data set. In cleaning and merging these two data sets, we will be able to answer the following questions:
#### 1) Once merged, how many of the Country IDs match?
#### 2) After sorting the data frame in ascending order by GDP rank (so United States is last), What is the 13th country in the resulting data frame?
#### 3) What are the average GDP rankings for the "High income: OECD" and "High income: nonOECD" groups?
#### 4) How many countries are Lower middle income but among the 38 nations with the highest GDP?

## We will also practice creating plots and tables:
#### 5) Plot the GDP for all of the countries. Use ggplot2 to color your plot by Income Group.
#### 6) Cut the GDP rankings into 5 separate quantile groups. Make a table versus Income.Group.

## Libraries required
#### install.packages("downloader")
#### install.packages("reshape2")
#### install.packages("doBy")
#### install.packages("ggrepel")




## Part 1: Download the files for this case study.

#### This data is from http://data.worldbank.org/data-catalog/GDP-ranking-table and 
#### http://data.worldbank.org/data-catalog/ed-stats
#### The data in the GDP ranking data set include the variables "Country"", "ranking", "Economy", and "millions of US dollars".

#### The data in the Education Statistics data set include multiple variables about the economic status of the different countries around the world. The data spans from 1976 to 2014.
### Packages needed for Case Study:
*This code will download the packages needed for the analysis and questions.

```r
library(downloader)
library(dplyr)
```

```
## 
## Attaching package: 'dplyr'
```

```
## The following objects are masked from 'package:stats':
## 
##     filter, lag
```

```
## The following objects are masked from 'package:base':
## 
##     intersect, setdiff, setequal, union
```

```r
library(doBy)
library(ggplot2)
library(ggrepel)
```

### Download Data from online:
*This code will download the datasets from data.worldbank.org and store it in local files with a new name.

```r
download("https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FGDP.csv", destfile="GDPRanking.csv")
download("https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2FEDSTATS_Country.csv", destfile="EducationStatistics.csv")

cat("Data downloaded into current working directory")
```

```
## Data downloaded into current working directory
```
## Part 2: Read the data into R and examine it.
*This code reads the csv files into R and starts reading the GDP data on the 6th row. The first 5 rows were NA's.

```r
GDP.Raw <- read.csv("GDPRanking.csv", stringsAsFactors=FALSE, header=FALSE, skip = 5) 
EducStat.Raw <- read.csv("EducationStatistics.csv", stringsAsFactors=FALSE) 
cat("Data read into R")
```

```
## Data read into R
```

### Examine data
#### Use 'str' function to get a feel for the two data sets:

```r
str(GDP.Raw)# Notice the lack of variable names and excessive NA's
```

```
## 'data.frame':	326 obs. of  10 variables:
##  $ V1 : chr  "USA" "CHN" "JPN" "DEU" ...
##  $ V2 : chr  "1" "2" "3" "4" ...
##  $ V3 : logi  NA NA NA NA NA NA ...
##  $ V4 : chr  "United States" "China" "Japan" "Germany" ...
##  $ V5 : chr  " 16,244,600 " " 8,227,103 " " 5,959,718 " " 3,428,131 " ...
##  $ V6 : chr  "" "" "" "" ...
##  $ V7 : logi  NA NA NA NA NA NA ...
##  $ V8 : logi  NA NA NA NA NA NA ...
##  $ V9 : logi  NA NA NA NA NA NA ...
##  $ V10: logi  NA NA NA NA NA NA ...
```

```r
str(EducStat.Raw)
```

```
## 'data.frame':	234 obs. of  31 variables:
##  $ CountryCode                                      : chr  "ABW" "ADO" "AFG" "AGO" ...
##  $ Long.Name                                        : chr  "Aruba" "Principality of Andorra" "Islamic State of Afghanistan" "People's Republic of Angola" ...
##  $ Income.Group                                     : chr  "High income: nonOECD" "High income: nonOECD" "Low income" "Lower middle income" ...
##  $ Region                                           : chr  "Latin America & Caribbean" "Europe & Central Asia" "South Asia" "Sub-Saharan Africa" ...
##  $ Lending.category                                 : chr  "" "" "IDA" "IDA" ...
##  $ Other.groups                                     : chr  "" "" "HIPC" "" ...
##  $ Currency.Unit                                    : chr  "Aruban florin" "Euro" "Afghan afghani" "Angolan kwanza" ...
##  $ Latest.population.census                         : chr  "2000" "Register based" "1979" "1970" ...
##  $ Latest.household.survey                          : chr  "" "" "MICS, 2003" "MICS, 2001, MIS, 2006/07" ...
##  $ Special.Notes                                    : chr  "" "" "Fiscal year end: March 20; reporting period for national accounts data: FY." "" ...
##  $ National.accounts.base.year                      : chr  "1995" "" "2002/2003" "1997" ...
##  $ National.accounts.reference.year                 : int  NA NA NA NA 1996 NA NA 1996 NA NA ...
##  $ System.of.National.Accounts                      : int  NA NA NA NA 1993 NA 1993 1993 NA NA ...
##  $ SNA.price.valuation                              : chr  "" "" "VAB" "VAP" ...
##  $ Alternative.conversion.factor                    : chr  "" "" "" "1991-96" ...
##  $ PPP.survey.year                                  : int  NA NA NA 2005 2005 NA 2005 2005 NA NA ...
##  $ Balance.of.Payments.Manual.in.use                : chr  "" "" "" "BPM5" ...
##  $ External.debt.Reporting.status                   : chr  "" "" "Actual" "Actual" ...
##  $ System.of.trade                                  : chr  "Special" "General" "General" "Special" ...
##  $ Government.Accounting.concept                    : chr  "" "" "Consolidated" "" ...
##  $ IMF.data.dissemination.standard                  : chr  "" "" "GDDS" "GDDS" ...
##  $ Source.of.most.recent.Income.and.expenditure.data: chr  "" "" "" "IHS, 2000" ...
##  $ Vital.registration.complete                      : chr  "" "Yes" "" "" ...
##  $ Latest.agricultural.census                       : chr  "" "" "" "1964-65" ...
##  $ Latest.industrial.data                           : int  NA NA NA NA 2005 NA 2001 NA NA NA ...
##  $ Latest.trade.data                                : int  2008 2006 2008 1991 2008 2008 2008 2008 NA 2007 ...
##  $ Latest.water.withdrawal.data                     : int  NA NA 2000 2000 2000 2005 2000 2000 NA 1990 ...
##  $ X2.alpha.code                                    : chr  "AW" "AD" "AF" "AO" ...
##  $ WB.2.code                                        : chr  "AW" "AD" "AF" "AO" ...
##  $ Table.Name                                       : chr  "Aruba" "Andorra" "Afghanistan" "Angola" ...
##  $ Short.Name                                       : chr  "Aruba" "Andorra" "Afghanistan" "Angola" ...
```

*This code will look at the last 10 observations for each variable

```r
tail(GDP.Raw, 10) #Notice all the NA's that fill the data set. We will eliminate these.
```

```
##     V1 V2 V3 V4 V5 V6 V7 V8 V9 V10
## 317       NA          NA NA NA  NA
## 318       NA          NA NA NA  NA
## 319       NA          NA NA NA  NA
## 320       NA          NA NA NA  NA
## 321       NA          NA NA NA  NA
## 322       NA          NA NA NA  NA
## 323       NA          NA NA NA  NA
## 324       NA          NA NA NA  NA
## 325       NA          NA NA NA  NA
## 326       NA          NA NA NA  NA
```

```r
tail(EducStat.Raw, 10)
```

```
##     CountryCode                        Long.Name        Income.Group
## 225         VNM    Socialist Republic of Vietnam Lower middle income
## 226         VUT              Republic of Vanuatu Lower middle income
## 227         WBG               West Bank and Gaza Lower middle income
## 228         WLD                            World                    
## 229         WSM                            Samoa Lower middle income
## 230         YEM                Republic of Yemen Lower middle income
## 231         ZAF         Republic of South Africa Upper middle income
## 232         ZAR Democratic Republic of the Congo          Low income
## 233         ZMB               Republic of Zambia          Low income
## 234         ZWE             Republic of Zimbabwe          Low income
##                         Region Lending.category Other.groups
## 225        East Asia & Pacific            Blend             
## 226        East Asia & Pacific              IDA             
## 227 Middle East & North Africa                              
## 228                                                         
## 229        East Asia & Pacific              IDA             
## 230 Middle East & North Africa              IDA             
## 231         Sub-Saharan Africa             IBRD             
## 232         Sub-Saharan Africa              IDA         HIPC
## 233         Sub-Saharan Africa              IDA         HIPC
## 234         Sub-Saharan Africa            Blend             
##          Currency.Unit Latest.population.census Latest.household.survey
## 225    Vietnamese dong                     2009              MICS, 2006
## 226       Vanuatu vatu                     2009                        
## 227 Israeli new shekel                     2007            PAPFAM, 2006
## 228                                                                    
## 229        Samoan tala                     2006                        
## 230        Yemeni rial                     2004              MICS, 2006
## 231 South African rand                     2001               DHS, 2003
## 232    Congolese franc                     1984                DHS 2007
## 233     Zambian kwacha                     2000               DHS, 2007
## 234    Zimbabwe dollar                     2002            DHS, 2005/06
##                                                                   Special.Notes
## 225                                                                            
## 226                                                                            
## 227                                                                            
## 228                                                            World aggregate.
## 229                                                                            
## 230                                                                            
## 231 Fiscal year end: March 31; reporting period for national accounts data: CY.
## 232                                                                            
## 233                                                                            
## 234  Fiscal year end: June 30; reporting period for national accounts data: CY.
##     National.accounts.base.year National.accounts.reference.year
## 225                        1994                               NA
## 226                        1983                               NA
## 227                        1997                               NA
## 228                                                           NA
## 229                        2002                               NA
## 230                        1990                               NA
## 231                        2000                               NA
## 232                        1987                               NA
## 233                        1994                               NA
## 234                        1990                               NA
##     System.of.National.Accounts SNA.price.valuation
## 225                        1993                 VAP
## 226                          NA                 VAP
## 227                          NA                 VAB
## 228                          NA                    
## 229                          NA                 VAB
## 230                          NA                 VAP
## 231                        1993                 VAB
## 232                        1993                 VAB
## 233                          NA                 VAB
## 234                          NA                 VAB
##     Alternative.conversion.factor PPP.survey.year
## 225                          1991            2005
## 226                                            NA
## 227                                            NA
## 228                                            NA
## 229                                            NA
## 230                       1990-96            2005
## 231                                          2005
## 232                       1999-01            2005
## 233                       1990-92            2005
## 234                    1991, 1998            2005
##     Balance.of.Payments.Manual.in.use External.debt.Reporting.status
## 225                              BPM4                       Estimate
## 226                              BPM5                       Estimate
## 227                                                                 
## 228                                                                 
## 229                              BPM5                    Preliminary
## 230                              BPM5                         Actual
## 231                              BPM5                    Preliminary
## 232                              BPM5                       Estimate
## 233                              BPM5                    Preliminary
## 234                              BPM5                         Actual
##     System.of.trade Government.Accounting.concept
## 225         General                  Consolidated
## 226                                  Consolidated
## 227                                     Budgetary
## 228                                              
## 229         General                              
## 230         General                     Budgetary
## 231         General                  Consolidated
## 232         Special                  Consolidated
## 233         General                     Budgetary
## 234         General                  Consolidated
##     IMF.data.dissemination.standard
## 225                            GDDS
## 226                            GDDS
## 227                            GDDS
## 228                                
## 229                                
## 230                            GDDS
## 231                            SDDS
## 232                            GDDS
## 233                            GDDS
## 234                            GDDS
##     Source.of.most.recent.Income.and.expenditure.data
## 225                                         IHS, 2006
## 226                                                  
## 227                                                  
## 228                                                  
## 229                                                  
## 230                                       ES/BS, 2005
## 231                                       ES/BS, 2000
## 232                                    1-2-3, 2005-06
## 233                                      IHS, 2004-05
## 234                                                  
##     Vital.registration.complete Latest.agricultural.census
## 225                                                   2001
## 226                                                       
## 227                                                   1971
## 228                                                       
## 229                                                   1999
## 230                                                   2002
## 231                                                   2000
## 232                                                   1990
## 233                                                   1990
## 234                                                   1960
##     Latest.industrial.data Latest.trade.data Latest.water.withdrawal.data
## 225                   1999              2008                         2000
## 226                     NA              2007                           NA
## 227                     NA                NA                           NA
## 228                     NA                NA                           NA
## 229                     NA              2008                           NA
## 230                   2005              2008                         2000
## 231                   2005              2008                         2000
## 232                     NA              1986                         2000
## 233                     NA              2008                         2000
## 234                   1995              2008                         2002
##     X2.alpha.code WB.2.code         Table.Name         Short.Name
## 225            VN        VN            Vietnam            Vietnam
## 226            VU        VU            Vanuatu            Vanuatu
## 227            PS        GZ West Bank and Gaza West Bank and Gaza
## 228                                      World              World
## 229            WS        WS              Samoa              Samoa
## 230            YE        RY        Yemen, Rep.              Yemen
## 231            ZA        ZA       South Africa       South Africa
## 232            CD        ZR   Congo, Dem. Rep.    Dem. Rep. Congo
## 233            ZM        ZM             Zambia             Zambia
## 234            ZW        ZW           Zimbabwe           Zimbabwe
```

*This code gives the dimensions for each dataset.

```r
dim(GDP.Raw) # We can see that the two data sets are different dimensions.
```

```
## [1] 326  10
```

```r
dim(EducStat.Raw)
```

```
## [1] 234  31
```




## Part 3: Time to Clean the Data.

### Give the GDP dataset variables descriptive names
*This code will rename the column headers to better variable names

```r
colnames(GDP.Raw) <- c("CountryCode","Ranking", " " ,"Long.Name", "GDP" )
```

### Delete the empty columns with NA's in the GDP.Raw data set
*This code will delete the blank columns inside the data. We will store this as a subset of the original data.

```r
myvars <- c("CountryCode","Ranking", "Long.Name", "GDP")
GDP.subset <- GDP.Raw[myvars]
```

### Changed the variable "Ranking" to numeric from character
#### We will eliminate the 136 NA's after the merge.
*This code will change the "Ranking" variable from a character to numeric. This allows us to do calculations and take averages later on. This code also tells us how many NA's are in the variable "Ranking"

```r
GDP.subset$Ranking <- as.numeric(GDP.subset$Ranking) # Changed "Ranking" to numeric from character
```

```
## Warning: NAs introduced by coercion
```

```r
count(GDP.subset[!complete.cases(GDP.subset$Ranking),]) #Counts the missing data in the "Ranking" Variable. 136 missing data.
```

```
## Source: local data frame [1 x 1]
## 
##       n
##   (int)
## 1   136
```

```r
summary(GDP.subset$Ranking) # Also shows 136 NA's
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    1.00   48.25   95.50   95.49  142.80  190.00     136
```

## Part 4 : Merge Data and clean some more
*This code will merge the two data sets using the variable "Countrycode" as the link-column that is common in both.

```r
CleanData1 <- merge(GDP.subset,EducStat.Raw, by = "CountryCode", all=FALSE) #Merges the data
```

#### Change GDP variable from character to numeric in order to calculate averages.
*This code changes the GDP variable from character to numeric. It will also remove the commas that cause the variable to read in incorrectly.

```r
CleanData1$GDP <- as.numeric(gsub(",","",CleanData1$GDP)) # Changed "GDP" to numeric from character and eliminate the Commas
```

```
## Warning: NAs introduced by coercion
```

```r
str(CleanData1) # Notice that "Ranking" and "GDP" are now numeric
```

```
## 'data.frame':	224 obs. of  34 variables:
##  $ CountryCode                                      : chr  "ABW" "ADO" "AFG" "AGO" ...
##  $ Ranking                                          : num  161 NA 105 60 125 32 26 133 NA 172 ...
##  $ Long.Name.x                                      : chr  "Aruba" "Andorra" "Afghanistan" "Angola" ...
##  $ GDP                                              : num  2584 NA 20497 114147 12648 ...
##  $ Long.Name.y                                      : chr  "Aruba" "Principality of Andorra" "Islamic State of Afghanistan" "People's Republic of Angola" ...
##  $ Income.Group                                     : chr  "High income: nonOECD" "High income: nonOECD" "Low income" "Lower middle income" ...
##  $ Region                                           : chr  "Latin America & Caribbean" "Europe & Central Asia" "South Asia" "Sub-Saharan Africa" ...
##  $ Lending.category                                 : chr  "" "" "IDA" "IDA" ...
##  $ Other.groups                                     : chr  "" "" "HIPC" "" ...
##  $ Currency.Unit                                    : chr  "Aruban florin" "Euro" "Afghan afghani" "Angolan kwanza" ...
##  $ Latest.population.census                         : chr  "2000" "Register based" "1979" "1970" ...
##  $ Latest.household.survey                          : chr  "" "" "MICS, 2003" "MICS, 2001, MIS, 2006/07" ...
##  $ Special.Notes                                    : chr  "" "" "Fiscal year end: March 20; reporting period for national accounts data: FY." "" ...
##  $ National.accounts.base.year                      : chr  "1995" "" "2002/2003" "1997" ...
##  $ National.accounts.reference.year                 : int  NA NA NA NA 1996 NA NA 1996 NA NA ...
##  $ System.of.National.Accounts                      : int  NA NA NA NA 1993 NA 1993 1993 NA NA ...
##  $ SNA.price.valuation                              : chr  "" "" "VAB" "VAP" ...
##  $ Alternative.conversion.factor                    : chr  "" "" "" "1991-96" ...
##  $ PPP.survey.year                                  : int  NA NA NA 2005 2005 NA 2005 2005 NA NA ...
##  $ Balance.of.Payments.Manual.in.use                : chr  "" "" "" "BPM5" ...
##  $ External.debt.Reporting.status                   : chr  "" "" "Actual" "Actual" ...
##  $ System.of.trade                                  : chr  "Special" "General" "General" "Special" ...
##  $ Government.Accounting.concept                    : chr  "" "" "Consolidated" "" ...
##  $ IMF.data.dissemination.standard                  : chr  "" "" "GDDS" "GDDS" ...
##  $ Source.of.most.recent.Income.and.expenditure.data: chr  "" "" "" "IHS, 2000" ...
##  $ Vital.registration.complete                      : chr  "" "Yes" "" "" ...
##  $ Latest.agricultural.census                       : chr  "" "" "" "1964-65" ...
##  $ Latest.industrial.data                           : int  NA NA NA NA 2005 NA 2001 NA NA NA ...
##  $ Latest.trade.data                                : int  2008 2006 2008 1991 2008 2008 2008 2008 NA 2007 ...
##  $ Latest.water.withdrawal.data                     : int  NA NA 2000 2000 2000 2005 2000 2000 NA 1990 ...
##  $ X2.alpha.code                                    : chr  "AW" "AD" "AF" "AO" ...
##  $ WB.2.code                                        : chr  "AW" "AD" "AF" "AO" ...
##  $ Table.Name                                       : chr  "Aruba" "Andorra" "Afghanistan" "Angola" ...
##  $ Short.Name                                       : chr  "Aruba" "Andorra" "Afghanistan" "Angola" ...
```


```r
CleanData2 <- CleanData1[order(-CleanData1$Ranking),] # Ordered the data frame by ascending rank. United States is last now.
```


### Created tidy data set by "Ranking"

```r
FinalCleanData <- CleanData2[!is.na(CleanData2$Ranking),] # Made new data set that doesn't have the na's in the Ranking column, is now Tidy Data.
```
Question 1) We can see that 189 Country ID's matched in the merge.

```r
dim(FinalCleanData)
```

```
## [1] 189  34
```
Question 2) The 13th country in ascending order is St. Kitts and Nevis

```r
head(FinalCleanData$Long.Name.x, 13)
```

```
##  [1] "Tuvalu"                         "Kiribati"                      
##  [3] "Marshall Islands"               "Palau"                         
##  [5] "São Tomé and Principe"          "Micronesia, Fed. Sts."         
##  [7] "Tonga"                          "Dominica"                      
##  [9] "Comoros"                        "Samoa"                         
## [11] "St. Vincent and the Grenadines" "Grenada"                       
## [13] "St. Kitts and Nevis"
```

# Find the average GDP Ranking by Income group. 
Question 3) The average GDP ranking by Income.Group

```r
summaryBy(Ranking~Income.Group,data=FinalCleanData, FUN=mean) 
```

```
##           Income.Group Ranking.mean
## 1 High income: nonOECD     91.91304
## 2    High income: OECD     32.96667
## 3           Low income    133.72973
## 4  Lower middle income    107.70370
## 5  Upper middle income     92.13333
```

# Plot the GDP for all countries and color by Income Group.
Skipping to Question 5) Plotted GDP for all 189 countries and colored them by their income group.

```r
ggplot(data = FinalCleanData, 
      aes(x = Long.Name.x, 
           y = GDP, 
           color = Income.Group)) +
      geom_point() +
      scale_x_discrete(name = "Country") +
      scale_y_discrete(name = "GDP in US Dollars")
```

![](GDP_vs_CountryIncome_CaseStudy_1_files/figure-html/unnamed-chunk-17-1.png)<!-- -->

*Looking at the plot of GDP to Country, it is easy to see that the High income: OECD populate the higher GDP range and the lower income groups group near the bottom at the low GDP range.
      
# Cut GDP Ranking into 5 separate quantile groups. 
Question 6.1) Separate GDP Ranking into 5 quantile groups.

```r
FinalCleanData$GDP.Quantiles <- with(FinalCleanData, factor(
  findInterval( Ranking, c(-Inf,
                       quantile(Ranking, probs=c(0.2, .4, .6, .8)), Inf)), 
  labels=c("Q1","Q2","Q3","Q4", "Q5")
))
```

Question 6.2) Make a table of the 5 GDP quantile groups versus Income group.
#Make a Table versus Income.Group

```r
GDP.Table <- table(FinalCleanData$Income.Group, FinalCleanData$GDP.Quantiles)
GDP.Table
```

```
##                       
##                        Q1 Q2 Q3 Q4 Q5
##   High income: nonOECD  4  5  8  4  2
##   High income: OECD    18 10  1  1  0
##   Low income            0  1  9 16 11
##   Lower middle income   5 13 11  9 16
##   Upper middle income  11  9  8  8  9
```

*Looking at this table, we can see that as the income increases, the higher the quantile range that the country falls into.

# How many countries are Lower middle income but among the 38 nations with the highest GDP?
Back to question 4) There are 5 countries that are considered lower middle income, but are among the 38 nations with the highest GDP.

```r
Lower.Middle.Income <- sum(FinalCleanData$Income.Group == "Lower middle income" & FinalCleanData$Ranking <= 38)
Lower.Middle.Income
```

```
## [1] 5
```

#Conclusion:
### We have taken (2) data sets,(GDP.csv and EDSTATS_Country.csv), cleaned them and merged them. We cleaned the data sets by the GDP Ranking variable. We removed columns and rows that contained NA's, we reordered the data in ascending order by GDP Rank, and we gave the variables proper names. 
### We discovered a relationship between GDP Ranking and Income Group by plotting them and making a table. The higher the average country income, the higher the average GDP Rank.
