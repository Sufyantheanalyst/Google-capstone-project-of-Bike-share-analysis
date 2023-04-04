# Google-capstone-project-of-Bike-share-analysis

This case study involves carrying out various real-world duties that a typical data analyst would perform daily. The focus will be on addressing business queries for Cyclist, a fictitious company. To accomplish this task, I will be following the six-step approach taught in Google's Professional Certificate, which includes: **ask**, **prepare**, **process**, **analyze**, **share**, and **act**. I believe this methodology provides a comprehensive framework for executing any analytics project while allowing for the flexibility necessary for individual projects. Without further ado, let us commence.

**Indroduction**

As a member of the marketing analyst team at Cyclistic, a bike-share company located in Chicago, I work as a data analyst. The company's director of marketing has expressed that the key to the company's future success is to maximize the number of annual memberships. To achieve this goal, our team's focus is to gain a comprehensive understanding of the distinctive patterns in the usage of Cyclistic bikes by casual riders and annual members. Based on these findings, we plan to create a new marketing strategy that aims to convert casual riders into annual members. However, before we can implement our recommendations, they need to be approved by Cyclistic executives, and therefore, our proposals must be supported by persuasive data insights and professional data visualizations.

The manager's aim is unequivocal: to create marketing tactics that target transforming casual riders into annual members. Nevertheless, to achieve this objective, the marketing analyst team must gain a deeper comprehension of the differences between annual members and casual riders, why casual riders would opt for a membership, and how digital media might influence their marketing approaches. The manager and her team have expressed their interest in scrutinizing the historical bike trip data of Cyclistic to detect any patterns.

**1- Ask**

Having assessed the stakeholder's prerequisites and requirements, the primary business objective is to **discover distinctive trends within the data that distinguish casual riders from members**. By achieving this aim, the marketing team will gain novel insights into our users' actions, which will aid them in formulating more effective strategies.

**2- Prepare**

Our next step involves determining the necessary data required to answer the business queries and devising a plan to obtain it. If the data already exists, we need to ascertain its source. In the case of this project, the data is presented in CSV files, with each file representing the data for a specific month. The fields included in each file are:

**ride_id**:identifier for each trip **rideable_type**: type of bike that was used **started_at**: timestamp for when the trip started **ended_at**: timestamp for when the trip ended **stat_station_name**: the name of the station where the trip started **start_station_id**: the id of the station where the trip started **end_station_name**: the name of the station where the trip ended **end_station_id**: the id of the station where the trip ended **start_lat**: latitude value for where the trip started **start_lng**: longitude value for where the trip started **end_lat**: latitude value for where the trip ended **end_lng**: longitude value for where the trip ended **memeber_casual**: type of user

Using this data, we can distinguish between member users and casual ones and observe the variations in usage throughout the day and across different months. However, due to privacy concerns, we do not have access to more detailed information about the users, which would have been helpful in real-world scenarios.

**Process**

Prior to delving into data analysis and attempting to derive insights, it is crucial to verify its integrity and ensure it is in a usable format. Although Excel is a potential tool for data cleaning, given the size of the dataset, it is preferable to employ a programming language like Python or R. For this particular project, I will be using **R** and its **IDE RStudio**.

## Importing libraries for this project

```{r}
#install.packages("ggplot2")
#install.packages("dplyr")
#install.packages("tidyverse")
```
```{r}
library(tidyverse)
library(dplyr)
library(ggplot2)
```
In my data analysis project, I will utilize various **R** packages to work with our datasets. Firstly, I will load the datasets into **RStudio** using the **readr** package. Then, for data manipulation, I will use the packages **tidyr** and **dplyr**. Lastly, if we want to visualize the data in a graphical format, I can utilize the **ggplot2** package.

After acquiring the necessary libraries, our next step is to retrieve the data from the previous year. The data is divided into 12 CSV tiles, which requires us to initially load each file into its own data frame.

```{r}
dt1 <- read.csv("~/Professional career/Bike_share project/202101-divvy-tripdata.csv")
dt2 <- read.csv("~/Professional career/Bike_share project/202102-divvy-tripdata.csv")
dt3 <- read.csv("~/Professional career/Bike_share project/202103-divvy-tripdata.csv")
dt4 <- read.csv("~/Professional career/Bike_share project/202104-divvy-tripdata.csv")
dt5 <- read.csv("~/Professional career/Bike_share project/202105-divvy-tripdata.csv")
dt6 <- read.csv("~/Professional career/Bike_share project/202106-divvy-tripdata.csv")
dt7 <- read.csv("~/Professional career/Bike_share project/202107-divvy-tripdata.csv")
dt8 <- read.csv("~/Professional career/Bike_share project/202108-divvy-tripdata.csv")
dt9 <- read.csv("~/Professional career/Bike_share project/202109-divvy-tripdata.csv")
dt10 <- read.csv("~/Professional career/Bike_share project/202110-divvy-tripdata.csv")
dt11 <- read.csv("~/Professional career/Bike_share project/202111-divvy-tripdata.csv")
dt12 <- read.csv("~/Professional career/Bike_share project/202112-divvy-tripdata.csv")
```
It's advisable to examine each data frame individually and familiarize ourselves with the type of data we will be dealing with, and check for any potential data issues before combining them. By utilizing the **View** and **glimpse** function, we can access a tabular graphical user interface to gain insight into the data.

```{r}
view(dt1)             

glimpse(dt1)          

```

Let’s merge all our data frames now by using **rbind** function

```{r}
trip_data <- rbind(dt1,dt2,dt3,dt4,dt5,dt6,dt7,dt8,dt9,dt10,dt11,dt12)
```

Given our intention to obtain specific values from the **rideable_type** and **member_casual** columns, we should examine the unique values present in both columns.

```{r}
unique(trip_data$rideable_type)
```

```{r}
unique(trip_data$member_casual)
```

**Process**

Lets view the column names present in dataset by using **colnames** function

```{r}
colnames(trip_data)                
```
```{r}
#view no of rows
nrow(trip_data)
```

Ensuring data integrity involves checking for uniqueness in the trip ID column to avoid having any duplicate values. To achieve this, the **distinct** function can be utilized to eliminate any duplicate entries in the column.

```{r}
trip_data <- trip_data%>%
  distinct(ride_id,.keep_all = TRUE)    
  ```
   let’s check how many NA values there is in the station columns:
  
  ```{r}
  sum(is.na(trip_data$start_station_name))
  sum(is.na(trip_data$end_station_name))
  sum(is.na(trip_data$start_station_id))
  sum(is.na(trip_data$end_station_id))
  ```
  Removing NA values
  ```{r}
  trip_data <- na.omit(trip_data)
   ```
  
  View first six rows of dataset
  
  ```{r}
  head(trip_data)
  ```
  View column names and data type
  
   ```{r}
   str(trip_data)
   ```
   Summary of data
   
    ```{r}
    summary(trip_data)
    ```
    
    Consistency in date format of started_at & ended_at
    
```{r} 
start<-as.POSIXlt(trip_data$started_at, tz = "","%m/%d/%Y %H:%M")
start<- data.frame(start)
start<- (start[1:nrow(na.omit(start)),])
start<- data.frame(start)

start1<-as.POSIXlt(trip_data$started_at, tz = "","%Y-%m-%d %H:%M")
start1<- data.frame(start1)
start1<- (start1[(nrow(start)+1):nrow(start1),])
start1<-data.frame(start1)

names(start)<- "start"
names(start1)<- "start"
start_time<- (rbind(start,start1))
trip_data$started_at <- NULL
trip_data['started_at']<- start_time

end<-(as.POSIXlt(trip_data$ended_at, tz = "","%m/%d/%Y %H:%M"))
end<- data.frame(end)
end<- (end[1:nrow(na.omit(end)),])
end<-data.frame(end)

end1<-(as.POSIXlt(trip_data$ended_at, tz = "","%Y-%m-%d %H:%M"))
end1<- data.frame(end1)
end1<- (end1[(nrow(end)+1):nrow(end1),])
end1<-data.frame(end1)

names(end)<- "end"
names(end1)<- "end"
end_time<- (rbind(end,end1))
trip_data$ended_at<- NULL
trip_data['ended_at']<- end_time
```
 Once we have carried out the data transformation process, we can ensure the quality of our data. However, we can further improve our dataset by creating calculated fields.
 
 Adding **weekday** column at which each trip started and Creating **momth** column in which each trip started.
 
 ```{r}
 trip_data$started_at <- as.Date(trip_data$started_at, format = "%Y-%m-%d %H:%M:%S")
trip_data$ended_at <- as.Date(trip_data$ended_at, format = "%Y-%m-%d %H:%M:%S")
trip_data$weekday <- weekdays(trip_data$started_at, abbreviate = FALSE)
trip_data$month <- format(trip_data$started_at, "%m")
 ```
 
 **Analyze**
 
 Important tasks: 
 i- Organize and format data 
 ii- To make data useful and accesible, aggregate it. 
 iii- Identify trends and calculations 
 iv- Calculations
 
 #member_casual
 
 It is important to comprehend the descriptive statistics of the entire dataset to enable comparison between member and casual riders when grouping them.
 
 ```{r}
 trip_data%>% group_by(member_casual)%>% summarise(n=n())%>%
  mutate(percent = n*100/sum(n))
  ```
  ```{r}
  ggplot(data = trip_data, mapping = aes(x = member_casual, fill = member_casual)) +
  geom_bar() +
  scale_fill_manual(values = c("blue", "red")) +
  labs(title = "Member Vs Casual")
  ```
  #member_casual
  
  ![member_casual](https://github.com/Sufyantheanalyst/member-casual/blob/main/compare%20member-casual%20users.jpeg)
  
  ```{r}
  trip_data %>%
  group_by(rideable_type) %>%
  summarise(n = n()) %>%
  mutate(percent = n * 100 / sum(n))
  ```
  ```{r}
  ggplot(data = trip_data, mapping = aes(x = rideable_type, fill = rideable_type)) +
  geom_bar() +
  scale_fill_manual(values = c("red", "green", "blue", "purple")) +
  labs(title = "Bike Types used")
  ```
  
  #Bikes types used
  
  ![bike types](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/bike%20types%20used.jpeg)
  
  ```{r}
  member_type<-trip_data%>% group_by(member_casual,rideable_type) %>% summarise(n=n())%>%  mutate(percent = n*100/sum(n))
  ```
  
  #choice of bike by riders
  
  ```{r}
  ggplot(data = as.data.frame(member_type),mapping= aes(x= member_casual, y=n, fill =rideable_type)) +geom_bar(stat = 'identity')+scale_fill_manual(values = c("red", "green", "blue", "purple")) + labs(title="Choice of Bike by Riders")
  ```
  ![choice of bikes](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/choice%20of%20bike%20by%20riders.jpeg)
  
  i- classical bike is most used than electrical

ii- Docked one is least useable

iii- Electrical bikes are almost equally liked by both

#weektable

```{r}
trip_data$weekday <- factor(trip_data$weekday, levels = c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"))

weektable <- trip_data %>%
  group_by(weekday) %>%
  summarise(n = n()) %>%
  mutate(percent = n * 100 / sum(n))
  ```
  
  #types of bike with riders
  
  ```{r}
  rideable_type<-trip_data%>% group_by(rideable_type,member_casual) %>% summarise(n=n())%>% 
  mutate(percent = n*100/sum(n))
  ```
  ```{r}
  ggplot(data = as.data.frame(rideable_type),mapping= aes(x= rideable_type, y = n, fill =member_casual)) +geom_bar(stat = 'identity') + labs(title="Bike Type with rider")
  ```
  
  ![types of bikes with riders](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/bike%20type%20with%20rider.jpeg)
  
  #usage per day
  
  ```{r}
  ggplot(data = trip_data, mapping = aes(x = weekday, fill = member_casual)) +
  geom_bar() +
  facet_wrap(~member_casual) +
  theme(axis.text.x = element_text(angle = 60, hjust = 1)) +
  scale_fill_manual(values = c("blue", "red"))
  ```
  ![user per weekday](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/member%20and%20casual%20user%20per%20weekday.jpeg)
  
  #Each day usage of bikes
  
  ```{r}
  trip_data$weekday<- factor(trip_data$weekday, levels= c("Monday", "Tuesday","Wednesday","Thursday","Friday","Saturday","Sunday"))
 weektable<- trip_data%>% group_by(weekday)%>% summarise(n=n())%>% mutate(percent = n*100/sum(n))
 
 weektable
 ```
 ```{r}
 ggplot(data = trip_data, aes(x = weekday, fill = weekday)) +
     geom_bar() +
     scale_fill_viridis_d() +
     labs(title = "Usage per day")
  ```
   
  ![usage of bike eachday](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/usage%20each%20day.jpeg)
  
  #summarize
  
  ```{r}
  trip_data %>% 
  group_by(member_casual) %>% 
  summarise(n = n()) %>% 
  mutate(percent = n * 100 / sum(n))
  ```
 #casual riders trend of using bikes
 
 ```{r}
 casual_riders<-trip_data%>% filter(member_casual == 'casual')%>%group_by(weekday,rideable_type)%>% summarise(n=n())%>% mutate(percent = n*100/sum(n))
 ```
 
 ```{r}
 ggplot(data = casual_riders, aes(x = weekday, y = n, fill = rideable_type)) + 
  geom_bar(stat = 'identity') +
  scale_fill_manual(values = c("electric_bike" = "blue", "classic_bike" = "red", "docked_bike" = "green"))
 ```
 
 ![casual riders](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/bike%20usage%20by%20casual%20riders.jpeg)
 
 #members trend of using bikes
 
 ```{r}
 members<-trip_data%>% filter(member_casual == 'member')%>%group_by(weekday,rideable_type)%>% summarise(n=n())%>% mutate(percent = n*100/sum(n))
 ```
 
 ```{r}
 ggplot(data = members, aes(x = weekday, y = n, fill = rideable_type)) + 
     geom_bar(stat = 'identity') +
    scale_fill_manual(values = c("electric_bike" = "blue", "classic_bike" = "red", "docked_bike" = "green"))
 ```
  ![members trend of using bikes](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/bike%20usage%20by%20members.jpeg)
  
 #Top starting stations used by member and casual riders
 
  ```{r}
  top_start_stations <- trip_data %>% 
     group_by(start_station_name, member_casual) %>% 
     summarise(n = n()) %>% 
     arrange(member_casual, desc(n)) %>% 
     group_by(member_casual) %>% 
     top_n(10, n)
  ```
  
  ```{r}
  ggplot(top_start_stations, aes(x = reorder(start_station_name, -n), y = n, fill = member_casual)) +
     geom_col(position = "dodge") +
     scale_fill_manual(values = c("red", "blue")) +
     labs(title = "Top 10 Starting Stations for Member and Casual Riders",
          x = "Starting Station",
          y = "Number of Rides") +
     theme(axis.text.x = element_text(angle = 45, hjust = 1))
  ```
  ![top satarting stations](https://github.com/Sufyantheanalyst/top-10-stations/blob/main/top%2010%20starting%20stations%20for%20member%20and%20casusal%20riders.jpeg)
 
  ```{r}
  ss <- trip_data %>%
     filter(start_station_name != "") %>%
     group_by(start_station_name, member_casual) %>%
     summarise(n = n()) %>%
     arrange(desc(n))
  ```
  ```{r}
  p<- ggplot(data =as.data.frame(ss), aes(x = start_station_name, y=n,fill=member_casual))+
     geom_bar(stat = 'identity')+facet_wrap(~ member_casual, ncol =1)+theme(axis.text.x = element_blank(),legend.position="none")

dat_text <- data.frame(
  label = c("Streeter Dr & Grand Ave", "DuSable Lake Shore Dr & Monroe St", "Millennium Park","Clark St & Elm St","Kingsbury St & Kinzie St","Wells St & Concord Ln" ),
  member_casual = c("casual", "casual", "casual", "member", "member", "member"),
  x = c("Streeter Dr & Grand Ave", "DuSable Lake Shore Dr & Monroe St", "Millennium Park","Clark St & Elm St","Kingsbury St & Kinzie St","Wells St & Concord Ln"),
  y = c(60000, 37000, 35000, 30000, 30000, 30000),
  hjust = c(0, 0, 1, 0.5, 0.5, 0.5),
  angle = c(0, 0, 0, 0, 0, 0)
)


p + geom_text(data = dat_text, mapping = aes(x = x, y = y, label = label, hjust = hjust, angle = angle, color = member_casual), size = 2.5) +
     scale_color_manual(values = c("member" = "red", "casual" = "blue"))
```

![top stations name](https://github.com/Sufyantheanalyst/Bike-share-project-plots/blob/main/top%20starting%20station.jpeg)


i- casual users start and end rides from same station.

ii- Lakeshore drive is place where bussiest stations located which is used by casual riders.

#Suggestions

There are several marketing strategies that could be implemented to encourage casual riders to become annual members:

i- Offer occasional membership discounts to casual riders, particularly during the summer and on weekends.

ii- Increase the rental price of bikes on weekends, especially for classic and electric bikes, which are preferred more by casual users. This may encourage them to consider purchasing an annual membership instead.

iii- Place banners or advertisements offering special discounts at Lake Shore Drive, specifically targeting casual users, with the hope of encouraging them to become members.

Thanks for spending your time to read, please give your valuable feedback

 
 
  
  



 
 
  
   



