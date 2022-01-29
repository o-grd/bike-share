<p align="center">
    <img src="https://raw.githubusercontent.com/labwilliam/data_analysis_projects/main/cyclistic_bike_share/scripts/logo.png" alt="bike-share" width="200"/>
</p>
<h1 align="center">Case Study: Cyclistic Bike-Share</h1>

# About the company
Cyclistic is a bike-share company in Chicago, Cyclistic launched in 2016 a sucessfull bike-share offering. Since then, the program grown to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations across Chicago. The bikes can be unlocked from one station and returned to any other station in the system anytime. 

# Questions for the analysis
* How do annual members and casual riders use Cyclistic bikes differently?
* Why would casual riders buy Cyclistic annual memberships?
* How can Cyclistic use digital media to influence casual riders to become members?

# Business Task
My team wants to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, my team will design a new marketing strategy to convert casual riders into annual members.

# R Data Cleaning
I made two versions of this cleaning, in R and Python, in Python you can view in this [link](https://github.com/o-grd/bike-share-python)

Loading packages for exploration
```r
library(tidyverse)
library(lubridate)
library(ggplot2)
```


Collecting the data in dataframes
```r
q1_2019 <- read_csv("Divvy_Trips_2019_Q1.csv")
q2_2019 <- read.csv("Divvy_Trips_2019_Q2.csv")
q3_2019 <- read.csv("Divvy_Trips_2019_Q3.csv")
q4_2019 <- read.csv("Divvy_Trips_2019_Q4.csv")
```

Seeing the column names to combine then into a single file
```r
colnames(q1_2019)
colnames(q2_2019)
colnames(q3_2019)
colnames(q4_2019)
```

Result
```r
> colnames(q1_2019)
[1] "trip_id"           "start_time"        "end_time"          "bikeid"            "tripduration"     
[6] "from_station_id"   "from_station_name" "to_station_id"     "to_station_name"   "usertype"         
[11] "gender"            "birthyear" 

> colnames(q2_2019)
[1] "X01...Rental.Details.Rental.ID" "X01...Rental.Details.Local.Start.Time"            
[3] "X01...Rental.Details.Local.End.Time" "X01...Rental.Details.Bike.ID"                     
[5] "X01...Rental.Details.Duration.In.Seconds.Uncapped" "X03...Rental.Start.Station.ID"                    
[7] "X03...Rental.Start.Station.Name" "X02...Rental.End.Station.ID"                      
[9] "X02...Rental.End.Station.Name" "User.Type"                                        
[11] "Member.Gender" 

> colnames(q3_2019)
[1] "trip_id"           "start_time"        "end_time"          "bikeid"            "tripduration"     
[6] "from_station_id"   "from_station_name" "to_station_id"     "to_station_name"   "usertype"         
[11] "gender"            "birthyear"

> colnames(q4_2019)
[1] "trip_id"           "start_time"        "end_time"          "bikeid"            "tripduration"     
[6] "from_station_id"   "from_station_name" "to_station_id"     "to_station_name"   "usertype"         
[11] "gender"            "birthyear"
```
So we can see **q2_2019** don't have the same name that the others dataframes, so lets change this
```r
(q2_2019 <- rename(q2_2019,
                   trip_id = "X01...Rental.Details.Rental.ID",
                   bikeid = "X01...Rental.Details.Bike.ID",
                   tripduration = "X01...Rental.Details.Duration.In.Seconds.Uncapped",
                   start_time = "X01...Rental.Details.Local.Start.Time",
                   end_time = "X01...Rental.Details.Local.End.Time",
                   from_station_name = "X03...Rental.Start.Station.Name",
                   from_station_id = "X03...Rental.Start.Station.ID",
                   to_station_name = "X02...Rental.End.Station.Name",
                   to_station_id = "X02...Rental.End.Station.ID",
                   usertype = "User.Type",
                   gender = "Member.Gender",
                   birthyear = "X05...Member.Details.Member.Birthday.Year"))
```
Let's inspect the dataframe and look for incongruencies
```r
str(q1_2019)
str(q2_2019)
str(q3_2019)
str(q4_2019)
```
Result
```r
> str(q1_2019)
spec_tbl_df [365,069 x 12] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
 $ trip_id          : num [1:365069] 21742443 21742444 21742445 21742446 21742447 ...
 $ start_time       : POSIXct[1:365069], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" "2019-01-01 00:13:23" "2019-01-01 00:13:45" ...
 $ end_time         : POSIXct[1:365069], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" "2019-01-01 00:27:12" "2019-01-01 00:43:28" ...
 $ bikeid           : num [1:365069] 2167 4386 1524 252 1170 ...
 $ tripduration     : num [1:365069] 390 441 829 1783 364 ...
 $ from_station_id  : num [1:365069] 199 44 15 123 173 98 98 211 150 268 ...
 $ from_station_name: chr [1:365069] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
 $ to_station_id    : num [1:365069] 84 624 644 176 35 49 49 142 148 141 ...
 $ to_station_name  : chr [1:365069] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
 $ usertype         : chr [1:365069] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
 $ gender           : chr [1:365069] "Male" "Female" "Female" "Male" ...
 $ birthyear        : num [1:365069] 1989 1990 1994 1993 1994 ...
```
```r
> str(q2_2019)
'data.frame':	1108163 obs. of  12 variables:
 $ trip_id          : int  22178529 22178530 22178531 22178532 22178533 22178534 22178535 22178536 22178537 22178538 ...
 $ start_time       : chr  "2019-04-01 00:02:22" "2019-04-01 00:03:02" "2019-04-01 00:11:07" "2019-04-01 00:13:01" ...
 $ end_time         : chr  "2019-04-01 00:09:48" "2019-04-01 00:20:30" "2019-04-01 00:15:19" "2019-04-01 00:18:58" ...
 $ bikeid           : int  6251 6226 5649 4151 3270 3123 6418 4513 3280 5534 ...
 $ tripduration     : chr  "446.0" "1,048.0" "252.0" "357.0" ...
 $ from_station_id  : int  81 317 283 26 202 420 503 260 211 211 ...
 $ from_station_name: chr  "Daley Center Plaza" "Wood St & Taylor St" "LaSalle St & Jackson Blvd" "McClurg Ct & Illinois St" ...
 $ to_station_id    : int  56 59 174 133 129 426 500 499 211 211 ...
 $ to_station_name  : chr  "Desplaines St & Kinzie St" "Wabash Ave & Roosevelt Rd" "Canal St & Madison St" "Kingsbury St & Kinzie St" ...
 $ usertype         : chr  "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
 $ gender           : chr  "Male" "Female" "Male" "Male" ...
 $ birthyear        : int  1975 1984 1990 1993 1992 1999 1969 1991 NA NA ...
```
```r
> str(q3_2019)
'data.frame':	1640718 obs. of  12 variables:
 $ trip_id          : int  23479388 23479389 23479390 23479391 23479392 23479393 23479394 23479395 23479396 23479397 ...
 $ start_time       : chr  "2019-07-01 00:00:27" "2019-07-01 00:01:16" "2019-07-01 00:01:48" "2019-07-01 00:02:07" ...
 $ end_time         : chr  "2019-07-01 00:20:41" "2019-07-01 00:18:44" "2019-07-01 00:27:42" "2019-07-01 00:27:10" ...
 $ bikeid           : int  3591 5353 6180 5540 6014 4941 3770 5442 2957 6091 ...
 $ tripduration     : chr  "1,214.0" "1,048.0" "1,554.0" "1,503.0" ...
 $ from_station_id  : int  117 381 313 313 168 300 168 313 43 43 ...
 $ from_station_name: chr  "Wilton Ave & Belmont Ave" "Western Ave & Monroe St" "Lakeview Ave & Fullerton Pkwy" "Lakeview Ave & Fullerton Pkwy" ...
 $ to_station_id    : int  497 203 144 144 62 232 62 144 195 195 ...
 $ to_station_name  : chr  "Kimball Ave & Belmont Ave" "Western Ave & 21st St" "Larrabee St & Webster Ave" "Larrabee St & Webster Ave" ...
 $ usertype         : chr  "Subscriber" "Customer" "Customer" "Customer" ...
 $ gender           : chr  "Male" "" "" "" ...
 $ birthyear        : int  1992 NA NA NA NA 1990 NA NA NA NA ...
```
```r
> str(q4_2019)
'data.frame':	704054 obs. of  12 variables:
 $ trip_id          : int  25223640 25223641 25223642 25223643 25223644 25223645 25223646 25223647 25223648 25223649 ...
 $ start_time       : chr  "2019-10-01 00:01:39" "2019-10-01 00:02:16" "2019-10-01 00:04:32" "2019-10-01 00:04:32" ...
 $ end_time         : chr  "2019-10-01 00:17:20" "2019-10-01 00:06:34" "2019-10-01 00:18:43" "2019-10-01 00:43:43" ...
 $ bikeid           : int  2215 6328 3003 3275 5294 1891 1061 1274 6011 2957 ...
 $ tripduration     : chr  "940.0" "258.0" "850.0" "2,350.0" ...
 $ from_station_id  : int  20 19 84 313 210 156 84 156 156 336 ...
 $ from_station_name: chr  "Sheffield Ave & Kingsbury St" "Throop (Loomis) St & Taylor St" "Milwaukee Ave & Grand Ave" "Lakeview Ave & Fullerton Pkwy" ...
 $ to_station_id    : int  309 241 199 290 382 226 142 463 463 336 ...
 $ to_station_name  : chr  "Leavitt St & Armitage Ave" "Morgan St & Polk St" "Wabash Ave & Grand Ave" "Kedzie Ave & Palmer Ct" ...
 $ usertype         : chr  "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
 $ gender           : chr  "Male" "Male" "Female" "Male" ...
 $ birthyear        : int  1987 1998 1991 1990 1987 1994 1991 1995 1993 NA ...
```

We can see that **q1_2019** have different type of columns, so we can change dataframes columns to character type so they can stack in one dataframe

Code for change:
```r
q1_2019 <- q1_2019 %>%
  mutate(across(everything(), as.character))

q2_2019<- q2_2019 %>%
  mutate(across(everything(), as.character))

q3_2019 <- q3_2019 %>%
  mutate(across(everything(), as.character))

q4_2019 <- q4_2019 %>%
  mutate(across(everything(), as.character))
```
Code for stack:
```r
all_trips <- bind_rows(q1_2019, q2_2019, q3_2019, q4_2019)
```
Let's inspect the dataframe that has been created
```r
colnames(all_trips)
[1] "trip_id"           "start_time"        "end_time"          "bikeid"            "tripduration"     
[6] "from_station_id"   "from_station_name" "to_station_id"     "to_station_name"   "usertype"         
[11] "gender"            "birthyear"

dim(all_trips)
[1] 3818004      12

nrow(all_trips)
[1] 3818004

head(all_trips)
# A tibble: 6 x 12
  trip_id  start_time end_time bikeid tripduration from_station_id from_station_na~ to_station_id to_station_name
  <chr>    <chr>      <chr>    <chr>  <chr>        <chr>           <chr>            <chr>         <chr>          
1 21742443 2019-01-0~ 2019-01~ 2167   390          199             Wabash Ave & Gr~ 84            Milwaukee Ave ~
2 21742444 2019-01-0~ 2019-01~ 4386   441          44              State St & Rand~ 624           Dearborn St & ~
3 21742445 2019-01-0~ 2019-01~ 1524   829          15              Racine Ave & 18~ 644           Western Ave & ~
4 21742446 2019-01-0~ 2019-01~ 252    1783         123             California Ave ~ 176           Clark St & Elm~
5 21742447 2019-01-0~ 2019-01~ 1170   364          173             Mies van der Ro~ 35            Streeter Dr & ~
6 21742448 2019-01-0~ 2019-01~ 2437   216          98              LaSalle St & Wa~ 49            Dearborn St & ~
# ... with 3 more variables: usertype <chr>, gender <chr>, birthyear <chr>
```
Little change in column **usertype** only for better uderstanding and visualization
```r
all_trips <- all_trips %>%
  mutate(usertype = recode(usertype,
                           "Subscriber" = "Member",
                           "Customer" = "Casual"))
```

Check to see if the reassign works
```r
table(all_trips$usertype)
 Casual  Member 
 880637 2937367
```
Adding columns that list the date, month, day and year of each ride, this will allow us to aggregate ride data for each month, day,
or year
```r
all_trips$date <- as.Date(all_trips$start_time)
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
```
Creating a .csv file to use in Tableau
```r
write.csv(all_trips, file = "C:/Users/Grd/Documents/GitHub/bike-share/all_trips.csv")
```
# Tableau Dashboard and Analysis
If you want to see in the Tableau site with a better resolution, just clik [here](https://public.tableau.com/views/CyclisticBike-Share_16433583038420/Dashboard?:language=en-US&publish=yes&:display_count=n&:origin=viz_share_link).

![Dashboard](/Dashboard.png)

## Analysis
As I showed in the dashboard above, the difference in the usage mode is: Members use more for daily and routine activities, while Casuals use it for sightseeing, visiting some tourist places.

So, one way to convince Casuals to become Members is to show them that there is more than one good way to use a bike, which is also very practical and fast to use a bike to get to and from work, as it avoids city traffic in the peak hours, as well as being a more sustainable measure. This can be shown through advertisements on social media with long-time Members and they can talk about the positive points of using the service for so long.