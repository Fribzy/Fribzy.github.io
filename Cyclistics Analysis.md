Cyclistics Analysis
Logan Frisby
2025-01-05
Upload data and install packages into R
Install packages tidyverse and conflicted to help manage the data and to the conflicts that may arise in the data
install.packages("tidyverse")
## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.4'
## (as 'lib' is unspecified)
install.packages("conflicted")
## Installing package into '/cloud/lib/x86_64-pc-linux-gnu-library/4.4'
## (as 'lib' is unspecified)
Load the packages so that you can use them and the functions that come with them.
library("tidyverse")
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.4     ✔ readr     2.1.5
## ✔ forcats   1.0.0     ✔ stringr   1.5.1
## ✔ ggplot2   3.5.1     ✔ tibble    3.2.1
## ✔ lubridate 1.9.4     ✔ tidyr     1.3.1
## ✔ purrr     1.0.2     
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ dplyr::filter() masks stats::filter()
## ✖ dplyr::lag()    masks stats::lag()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
library("conflicted")
Upload the Divvy datasets (csv files)
q1_2019 <- read_csv("Divvy_Trips_2019_Q1.csv")
## Rows: 365069 Columns: 12
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (4): from_station_name, to_station_name, usertype, gender
## dbl  (5): trip_id, bikeid, from_station_id, to_station_id, birthyear
## num  (1): tripduration
## dttm (2): start_time, end_time
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
q1_2020 <- read_csv("Divvy_Trips_2020_Q1.csv")
## Rows: 426887 Columns: 13
## ── Column specification ────────────────────────────────────────────────────────
## Delimiter: ","
## chr  (5): ride_id, rideable_type, start_station_name, end_station_name, memb...
## dbl  (6): start_station_id, end_station_id, start_lat, start_lng, end_lat, e...
## dttm (2): started_at, ended_at
## 
## ℹ Use `spec()` to retrieve the full column specification for this data.
## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.
Sort the data and combine the dataframes into a single dataaframe
Compare the file names
colnames(q1_2019)
##  [1] "trip_id"           "start_time"        "end_time"         
##  [4] "bikeid"            "tripduration"      "from_station_id"  
##  [7] "from_station_name" "to_station_id"     "to_station_name"  
## [10] "usertype"          "gender"            "birthyear"
colnames(q1_2020)
##  [1] "ride_id"            "rideable_type"      "started_at"        
##  [4] "ended_at"           "start_station_name" "start_station_id"  
##  [7] "end_station_name"   "end_station_id"     "start_lat"         
## [10] "start_lng"          "end_lat"            "end_lng"           
## [13] "member_casual"
Rename columns so that they are consistant with each other. To make joining the dataframes easier
(q1_2019 <- rename(q1_2019
                   ,ride_id = trip_id
                   ,rideable_type = bikeid
                   ,started_at = start_time
                   ,ended_at = end_time
                   ,start_station_name = from_station_name
                   ,start_station_id = from_station_id
                   ,end_station_name = to_station_name
                   ,end_station_id = to_station_id
                   ,member_casual = usertype
))
## # A tibble: 365,069 × 12
##     ride_id started_at          ended_at            rideable_type tripduration
##       <dbl> <dttm>              <dttm>                      <dbl>        <dbl>
##  1 21742443 2019-01-01 00:04:37 2019-01-01 00:11:07          2167          390
##  2 21742444 2019-01-01 00:08:13 2019-01-01 00:15:34          4386          441
##  3 21742445 2019-01-01 00:13:23 2019-01-01 00:27:12          1524          829
##  4 21742446 2019-01-01 00:13:45 2019-01-01 00:43:28           252         1783
##  5 21742447 2019-01-01 00:14:52 2019-01-01 00:20:56          1170          364
##  6 21742448 2019-01-01 00:15:33 2019-01-01 00:19:09          2437          216
##  7 21742449 2019-01-01 00:16:06 2019-01-01 00:19:03          2708          177
##  8 21742450 2019-01-01 00:18:41 2019-01-01 00:20:21          2796          100
##  9 21742451 2019-01-01 00:18:43 2019-01-01 00:47:30          6205         1727
## 10 21742452 2019-01-01 00:19:18 2019-01-01 00:24:54          3939          336
## # ℹ 365,059 more rows
## # ℹ 7 more variables: start_station_id <dbl>, start_station_name <chr>,
## #   end_station_id <dbl>, end_station_name <chr>, member_casual <chr>,
## #   gender <chr>, birthyear <dbl>
Inspect the dataframes for inconsitencies with the data and formatting
str(q1_2019)
## spc_tbl_ [365,069 × 12] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ ride_id           : num [1:365069] 21742443 21742444 21742445 21742446 21742447 ...
##  $ started_at        : POSIXct[1:365069], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" ...
##  $ ended_at          : POSIXct[1:365069], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" ...
##  $ rideable_type     : num [1:365069] 2167 4386 1524 252 1170 ...
##  $ tripduration      : num [1:365069] 390 441 829 1783 364 ...
##  $ start_station_id  : num [1:365069] 199 44 15 123 173 98 98 211 150 268 ...
##  $ start_station_name: chr [1:365069] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
##  $ end_station_id    : num [1:365069] 84 624 644 176 35 49 49 142 148 141 ...
##  $ end_station_name  : chr [1:365069] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
##  $ member_casual     : chr [1:365069] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
##  $ gender            : chr [1:365069] "Male" "Female" "Female" "Male" ...
##  $ birthyear         : num [1:365069] 1989 1990 1994 1993 1994 ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   trip_id = col_double(),
##   ..   start_time = col_datetime(format = ""),
##   ..   end_time = col_datetime(format = ""),
##   ..   bikeid = col_double(),
##   ..   tripduration = col_number(),
##   ..   from_station_id = col_double(),
##   ..   from_station_name = col_character(),
##   ..   to_station_id = col_double(),
##   ..   to_station_name = col_character(),
##   ..   usertype = col_character(),
##   ..   gender = col_character(),
##   ..   birthyear = col_double()
##   .. )
##  - attr(*, "problems")=<externalptr>
str(q1_2020)
## spc_tbl_ [426,887 × 13] (S3: spec_tbl_df/tbl_df/tbl/data.frame)
##  $ ride_id           : chr [1:426887] "EACB19130B0CDA4A" "8FED874C809DC021" "789F3C21E472CA96" "C9A388DAC6ABF313" ...
##  $ rideable_type     : chr [1:426887] "docked_bike" "docked_bike" "docked_bike" "docked_bike" ...
##  $ started_at        : POSIXct[1:426887], format: "2020-01-21 20:06:59" "2020-01-30 14:22:39" ...
##  $ ended_at          : POSIXct[1:426887], format: "2020-01-21 20:14:30" "2020-01-30 14:26:22" ...
##  $ start_station_name: chr [1:426887] "Western Ave & Leland Ave" "Clark St & Montrose Ave" "Broadway & Belmont Ave" "Clark St & Randolph St" ...
##  $ start_station_id  : num [1:426887] 239 234 296 51 66 212 96 96 212 38 ...
##  $ end_station_name  : chr [1:426887] "Clark St & Leland Ave" "Southport Ave & Irving Park Rd" "Wilton Ave & Belmont Ave" "Fairbanks Ct & Grand Ave" ...
##  $ end_station_id    : num [1:426887] 326 318 117 24 212 96 212 212 96 100 ...
##  $ start_lat         : num [1:426887] 42 42 41.9 41.9 41.9 ...
##  $ start_lng         : num [1:426887] -87.7 -87.7 -87.6 -87.6 -87.6 ...
##  $ end_lat           : num [1:426887] 42 42 41.9 41.9 41.9 ...
##  $ end_lng           : num [1:426887] -87.7 -87.7 -87.7 -87.6 -87.6 ...
##  $ member_casual     : chr [1:426887] "member" "member" "member" "member" ...
##  - attr(*, "spec")=
##   .. cols(
##   ..   ride_id = col_character(),
##   ..   rideable_type = col_character(),
##   ..   started_at = col_datetime(format = ""),
##   ..   ended_at = col_datetime(format = ""),
##   ..   start_station_name = col_character(),
##   ..   start_station_id = col_double(),
##   ..   end_station_name = col_character(),
##   ..   end_station_id = col_double(),
##   ..   start_lat = col_double(),
##   ..   start_lng = col_double(),
##   ..   end_lat = col_double(),
##   ..   end_lng = col_double(),
##   ..   member_casual = col_character()
##   .. )
##  - attr(*, "problems")=<externalptr>
Convert ride_id and rideable_type from number vectors into character vectors
q1_2019 <- mutate(q1_2019, ride_id = as.character(ride_id)
                  ,rideable_type = as.character(rideable_type))
Combine individual quarter’s dataframes into a single dataframe
all_trips <- bind_rows(q1_2019, q1_2020)#, q3_2019)#, q4_2019, q1_2020)
Remove columns lat, long, birthyear, gender and “tripduration” from the dataframe because they become obsolete in 2020
all_trips <- all_trips %>% 
select(-c(start_lat, start_lng, end_lat, end_lng, birthyear, gender, "tripduration"))
Clean and add data to prepare for analysis
Inspect the new table that was created from the two dataframes
colnames(all_trips) ### Names of the columns
## [1] "ride_id"            "started_at"         "ended_at"          
## [4] "rideable_type"      "start_station_id"   "start_station_name"
## [7] "end_station_id"     "end_station_name"   "member_casual"
nrow(all_trips) ### The number of rows in the dataframe
## [1] 791956
dim(all_trips) ### Dimensions of the new dataframe
## [1] 791956      9
head(all_trips) ### First six rows of the dataframe
## # A tibble: 6 × 9
##   ride_id started_at          ended_at            rideable_type start_station_id
##   <chr>   <dttm>              <dttm>              <chr>                    <dbl>
## 1 217424… 2019-01-01 00:04:37 2019-01-01 00:11:07 2167                       199
## 2 217424… 2019-01-01 00:08:13 2019-01-01 00:15:34 4386                        44
## 3 217424… 2019-01-01 00:13:23 2019-01-01 00:27:12 1524                        15
## 4 217424… 2019-01-01 00:13:45 2019-01-01 00:43:28 252                        123
## 5 217424… 2019-01-01 00:14:52 2019-01-01 00:20:56 1170                       173
## 6 217424… 2019-01-01 00:15:33 2019-01-01 00:19:09 2437                        98
## # ℹ 4 more variables: start_station_name <chr>, end_station_id <dbl>,
## #   end_station_name <chr>, member_casual <chr>
str(all_trips) ### Names of columns and associated data types 
## tibble [791,956 × 9] (S3: tbl_df/tbl/data.frame)
##  $ ride_id           : chr [1:791956] "21742443" "21742444" "21742445" "21742446" ...
##  $ started_at        : POSIXct[1:791956], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" ...
##  $ ended_at          : POSIXct[1:791956], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" ...
##  $ rideable_type     : chr [1:791956] "2167" "4386" "1524" "252" ...
##  $ start_station_id  : num [1:791956] 199 44 15 123 173 98 98 211 150 268 ...
##  $ start_station_name: chr [1:791956] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
##  $ end_station_id    : num [1:791956] 84 624 644 176 35 49 49 142 148 141 ...
##  $ end_station_name  : chr [1:791956] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
##  $ member_casual     : chr [1:791956] "Subscriber" "Subscriber" "Subscriber" "Subscriber" ...
summary(all_trips) ### Summary of the data
##    ride_id            started_at                    
##  Length:791956      Min.   :2019-01-01 00:04:37.00  
##  Class :character   1st Qu.:2019-02-28 17:04:04.75  
##  Mode  :character   Median :2020-01-07 12:48:50.50  
##                     Mean   :2019-09-01 11:58:08.35  
##                     3rd Qu.:2020-02-19 19:31:54.75  
##                     Max.   :2020-03-31 23:51:34.00  
##                                                     
##     ended_at                      rideable_type      start_station_id
##  Min.   :2019-01-01 00:11:07.00   Length:791956      Min.   :  2.0   
##  1st Qu.:2019-02-28 17:15:58.75   Class :character   1st Qu.: 77.0   
##  Median :2020-01-07 13:02:50.00   Mode  :character   Median :174.0   
##  Mean   :2019-09-01 12:17:52.17                      Mean   :204.4   
##  3rd Qu.:2020-02-19 19:51:54.50                      3rd Qu.:291.0   
##  Max.   :2020-05-19 20:10:34.00                      Max.   :675.0   
##                                                                      
##  start_station_name end_station_id  end_station_name   member_casual     
##  Length:791956      Min.   :  2.0   Length:791956      Length:791956     
##  Class :character   1st Qu.: 77.0   Class :character   Class :character  
##  Mode  :character   Median :174.0   Mode  :character   Mode  :character  
##                     Mean   :204.4                                        
##                     3rd Qu.:291.0                                        
##                     Max.   :675.0                                        
##                     NA's   :1
View how many observations fall under the usertype and what they are.
table(all_trips$member_casual)
## 
##     casual   Customer     member Subscriber 
##      48480      23163     378407     341906
Change the variables “Subscriber” to the variable “member”, “Customer” to “casual”.
This ensures that there is only one name for the same variable.
all_trips$member_casual[all_trips$member_casual== "Subscriber"]<-"member"
all_trips$member_casual[all_trips$member_casual== "Customer"]<-"casual"
Add new columns day_of_week and ride_length to the dataframe. This will allow us to aggregate data for day of the week and that length of each ride.
all_trips$date <- as.Date(all_trips$started_at) #The default format is yyyy-mm-dd
all_trips$month <- format(as.Date(all_trips$date), "%m")
all_trips$day <- format(as.Date(all_trips$date), "%d")
all_trips$year <- format(as.Date(all_trips$date), "%Y")
all_trips$day_of_week <- format(as.Date(all_trips$date), "%A")
all_trips$ride_length <- difftime(all_trips$ended_at, all_trips$started_at, units="mins")
Reformat ride_length data from a character vector to a numeric vector so we can run calculations of the ride_length data.
all_trips$ride_length<- as.numeric(as.character(all_trips$ride_length))
Reinspect the structure of the columns
str(all_trips)
## tibble [791,956 × 15] (S3: tbl_df/tbl/data.frame)
##  $ ride_id           : chr [1:791956] "21742443" "21742444" "21742445" "21742446" ...
##  $ started_at        : POSIXct[1:791956], format: "2019-01-01 00:04:37" "2019-01-01 00:08:13" ...
##  $ ended_at          : POSIXct[1:791956], format: "2019-01-01 00:11:07" "2019-01-01 00:15:34" ...
##  $ rideable_type     : chr [1:791956] "2167" "4386" "1524" "252" ...
##  $ start_station_id  : num [1:791956] 199 44 15 123 173 98 98 211 150 268 ...
##  $ start_station_name: chr [1:791956] "Wabash Ave & Grand Ave" "State St & Randolph St" "Racine Ave & 18th St" "California Ave & Milwaukee Ave" ...
##  $ end_station_id    : num [1:791956] 84 624 644 176 35 49 49 142 148 141 ...
##  $ end_station_name  : chr [1:791956] "Milwaukee Ave & Grand Ave" "Dearborn St & Van Buren St (*)" "Western Ave & Fillmore St (*)" "Clark St & Elm St" ...
##  $ member_casual     : chr [1:791956] "member" "member" "member" "member" ...
##  $ date              : Date[1:791956], format: "2019-01-01" "2019-01-01" ...
##  $ month             : chr [1:791956] "01" "01" "01" "01" ...
##  $ day               : chr [1:791956] "01" "01" "01" "01" ...
##  $ year              : chr [1:791956] "2019" "2019" "2019" "2019" ...
##  $ day_of_week       : chr [1:791956] "Tuesday" "Tuesday" "Tuesday" "Tuesday" ...
##  $ ride_length       : num [1:791956] 6.5 7.35 13.82 29.72 6.07 ...
Remove the bad data, by getting rid of the rides where the ride length is negative or where there is no starting station.
Remove the duplicate data based on the ride_id
The bikes have most likely been taken out of commission or the data collected was bad.
all_trips_V2 <-subset(all_trips, ride_length > 0, start_station_id>0)
all_trips_V2$ride_id <- distinct(all_trips_V2,ride_id)
Conduct Analysis
Descriptive Analysis on ride_length (all figures are in mins)
mean(all_trips_V2$ride_length) ### Average length of each ride (total ride length/rides)
## [1] 19.7355
median(all_trips_V2$ride_length) ### The midpoint number in the ascending array of ride lengths
## [1] 8.95
max(all_trips_V2$ride_length) ### The longest ride
## [1] 177200.4
min(all_trips_V2$ride_length) ### The shortest ride
## [1] 0.01666667
Summary of the four lines of code condensed
summary(all_trips_V2$ride_length)
##      Min.   1st Qu.    Median      Mean   3rd Qu.      Max. 
##      0.02      5.47      8.95     19.74     15.17 177200.37
Compare the mean, median, max and min between the members and the casual users.
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual, FUN = mean)
##   all_trips_V2$member_casual all_trips_V2$ride_length
## 1                     casual                 85.09146
## 2                     member                 13.25419
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual, FUN = median)
##   all_trips_V2$member_casual all_trips_V2$ride_length
## 1                     casual                22.066667
## 2                     member                 8.466667
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual, FUN = max)
##   all_trips_V2$member_casual all_trips_V2$ride_length
## 1                     casual                 177200.4
## 2                     member                 101607.1
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual, FUN = min)
##   all_trips_V2$member_casual all_trips_V2$ride_length
## 1                     casual               0.01666667
## 2                     member               0.01666667
Compare the average ride time by each day for members and casual users.
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual + all_trips_V2$day_of_week,
          FUN = mean)
##    all_trips_V2$member_casual all_trips_V2$day_of_week all_trips_V2$ride_length
## 1                      casual                   Friday                 95.60958
## 2                      member                   Friday                 13.27890
## 3                      casual                   Monday                 66.16125
## 4                      member                   Monday                 13.70519
## 5                      casual                 Saturday                 82.51285
## 6                      member                 Saturday                 16.23455
## 7                      casual                   Sunday                 84.35507
## 8                      member                   Sunday                 16.21564
## 9                      casual                 Thursday                129.55377
## 10                     member                 Thursday                 11.78682
## 11                     casual                  Tuesday                 69.73016
## 12                     member                  Tuesday                 12.82403
## 13                     casual                Wednesday                 68.66810
## 14                     member                Wednesday                 11.86630
Reorder the days of the week so they are in correct order
all_trips_V2$day_of_week <- ordered(all_trips_V2$day_of_week, levels=c("Sunday", "Monday",
"Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))
Now we rerun the average ride time by each day of the week for members and casual users.
aggregate(all_trips_V2$ride_length ~ all_trips_V2$member_casual + all_trips_V2$day_of_week,
          FUN = mean)
##    all_trips_V2$member_casual all_trips_V2$day_of_week all_trips_V2$ride_length
## 1                      casual                   Sunday                 84.35507
## 2                      member                   Sunday                 16.21564
## 3                      casual                   Monday                 66.16125
## 4                      member                   Monday                 13.70519
## 5                      casual                  Tuesday                 69.73016
## 6                      member                  Tuesday                 12.82403
## 7                      casual                Wednesday                 68.66810
## 8                      member                Wednesday                 11.86630
## 9                      casual                 Thursday                129.55377
## 10                     member                 Thursday                 11.78682
## 11                     casual                   Friday                 95.60958
## 12                     member                   Friday                 13.27890
## 13                     casual                 Saturday                 82.51285
## 14                     member                 Saturday                 16.23455
Analyze ridership data by type and weekday
all_trips_V2 %>% 
  mutate(weekday = wday(started_at,label=TRUE)) %>% ### Creates weekday field using the function wday()
  group_by(member_casual, weekday) %>% ### Groups the data by usertype and weekday
  summarise(number_of_rides=n() ### Calculates the number of rides and average ride duration
    ,average_duration=mean(ride_length)) %>%  ### Calculates the average duration of the rides
  arrange(member_casual,weekday) ### Sorts the data
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.
## # A tibble: 14 × 4
## # Groups:   member_casual [2]
##    member_casual weekday number_of_rides average_duration
##    <chr>         <ord>             <int>            <dbl>
##  1 casual        Sun               18652             84.4
##  2 casual        Mon                6694             66.2
##  3 casual        Tue                7972             69.7
##  4 casual        Wed                8363             68.7
##  5 casual        Thu                7771            130. 
##  6 casual        Fri                8508             95.6
##  7 casual        Sat               13473             82.5
##  8 member        Sun               60197             16.2
##  9 member        Mon              110430             13.7
## 10 member        Tue              127974             12.8
## 11 member        Wed              121903             11.9
## 12 member        Thu              125228             11.8
## 13 member        Fri              115168             13.3
## 14 member        Sat               59413             16.2
Create a visual for the number of rides by rider type
all_trips_V2 %>% 
  mutate(weekday=wday(started_at,label=TRUE)) %>% 
  group_by(member_casual,weekday) %>% 
  summarise(number_of_rides = n()
          ,average_duration = mean(ride_length)) %>% 
  arrange(member_casual, weekday) %>% 
  ggplot(mapping=aes(x=weekday,y=number_of_rides,fill=member_casual))+
  geom_col(position="dodge")+
  labs(title="Number of Rides", subtitle="Per User Type", x= "Day of the Week", y= "Number of Rides", fill ="User Type" )
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


Create a visual for average duration
all_trips_V2 %>% 
  mutate(weekday=wday(started_at,label=TRUE)) %>% 
  group_by(member_casual,weekday) %>% 
  summarise(number_of_rides=n()
            ,average_duration=mean(ride_length)) %>% 
  arrange(member_casual,weekday) %>% 
  ggplot(mapping=aes(x=weekday,y=average_duration,fill = member_casual))+
  geom_col(position = "dodge")+
  labs(title="Average Trip Duration", subtitle= "Per User Type",x= "Day of the Week", y= "Average Duration (mins)", fill= "User Type")
## `summarise()` has grouped output by 'member_casual'. You can override using the
## `.groups` argument.


#Create a .csv file that can be visualized in Excel, Tableau or another presentation software

counts <- aggregate(all_trips_V2$ride_length~all_trips_V2$member_casual+
all_trips_V2$day_of_week,FUN=mean)
write.csv(counts,file='avg_ride_length.csv')
