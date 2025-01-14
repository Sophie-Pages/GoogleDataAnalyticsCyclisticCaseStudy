# Google Data Analytics: Cyclistic bike-share analysis case study
## Introduction
The Cyclistic case study is one of the two capstone projects in the [Google Data Analytics Professional Certificate](https://www.coursera.org/learn/google-data-analytics-capstone).

In this documentation, we will go through the following data analysis phases: Ask, Prepare, Process, Analyse, Share and Act. If you want to skip directly to the data visualisations, you can go to: [Cyclistic Case Study Dashboard](https://public.tableau.com/app/profile/sophie.pages/viz/GoogleDataAnalyticsDashboardofCyclisticcasestudy/Dashboard3).

## Case study description
Cyclistic is a fictional bike-share company based in Chicago. In this project, I will take the role of a junior data analyst working in the marketing analytics team.

### Company
Cyclistic launched in 2016 a successful bike-share offering with over 5,800 bicycles and 690 stations across Chicago. The bikes can be taken from one station and returned to any other station in the system.

Until now, Cyclistic’s marketing strategy relied on growing its customers by building general awareness and appealing to broad consumer segments. 

Cyclistic sets itself apart by offering reclining bikes, hand tricycles, and cargo bikes. It makes bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders select the traditional bikes and about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use bikes to commute to work each day.

Cyclistic popularity can also be attributed to the flexibility of its pricing plans: customers can choose between single-ride passes, full-day passes, and annual memberships. Customers who purchase single-ride or full-day passes are referred to as casual riders while customers who purchase annual memberships are called annual members.

To understand the flexibility in pricing, we can take a look at the difference in pricing for classic bikes: 
* Single rides have an initial cost of $1 to unlock the bike and then $0.18 per minute. 
* Day passes have the first 3 hours for free and then $0.18 per minute. 
* Annual memberships have the first 45 minutes for free and then $0.18 per minute.

### Change in marketing strategy
Although the pricing flexibility helps Cyclistic attract customers, Cyclistic’s finance analysts have concluded that annual members are much more profitable than casual riders. 

The director of marketing, Lily Moreno, believes that the company’s future success depends on maximising the number of annual memberships. Rather than creating a marketing campaign that targets new customers, Moreno believes there is a solid opportunity to convert casual riders into members. 

Lily Moreno has set a clear goal for the marketing analytics team: to design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve the recommendations, and they must be backed up with compelling data insights and professional data visualizations.

Before making recommendations, the marketing analytics team needs to better understand how annual members and casual riders differ, why casual riders would buy a membership, and how digital media could affect their marketing tactics. The Cyclistic historical bike trip data will be analysed to identify trends.

## Ask phase
*The Ask phase is the problem-defining phase. It is when you define the problem you’re trying to solve. You also need to make sure you understand who the stakeholders are, and what their expectations are.*

### Information
Lily Moreno has assigned this question for us to answer: how do annual members and casual riders use Cyclistic bikes differently?

### Business task
*A business task corresponds to the question or problem to be solved by data analysis for a business.*

In our case, our business task is to understand how annual members and casual riders use Cyclistic bikes in order to design a new marketing strategy to convert casual riders into annual members.

### Key stakeholders
Our key stakeholders are Lily Moreno -our manager and the director of marketing- and the Cyclistic executive team that will decide whether or not to approve the recommended marketing program.

## Prepare phase
*The Prepare phase is the data collection phase. It is the time to decide what data needs to be collected in order to answer your questions. It is also the time to learn more about your data and to organise it to make it useful.*

### The data
The bike trip data is stored in a S3 bucket and can be collected [here](https://divvy-tripdata.s3.amazonaws.com/index.html). This is public data made available by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement). It is first-party data which means it has been collected by Cyclistic using their own resources. It is external data as it is available publicly.

The data is structured and it is organized into CSV files that go from 2013 to October 2024. This is wide data as every data subject has a single row with multiple columns to hold the different values of its attributes.

We have been requested to use only the last 12 months of data. Those last months are enough to gather relevant information and make recommendations. But, if we or the stakeholders wanted, we could review the data of the last few years to gather even more information about users and trends.

### Limitations of the data
The data has been anonymised which means any Personally Identifiable Information (PII) has been removed to ensure the identities of the users are protected. 

Due to the anonymisation process, we will not be able to know which rides are single-ride or full-day passes. The users who purchased these passes are referred to as casual riders without distinction.

We have no information about the circumstances of the trip: was it for work, leisure, or other? We have as well no information about where the users live and if it is close to the stations. 

Due to those limitations, I recommend to the stakeholders an anonymous survey where we could ask these types of questions and much more:
Are you an annual member? 
 🔲 Yes
 🔲 No, I mostly use full-day passes
 🔲 No, I mostly use single-ride passes
 🔲 No, I use full-day passes and single-ride passes equally

Why do you usually use Cyclistic? 
 🔲 To go to work
 🔲 For leisure
 🔲 To do sport
 🔲 Other

Where is the closest station from your home? 
 🔲 Less than 5 min
 🔲 Between 5 and 15 min
 🔲 More than 15 min

Where is the closest station from your workplace?
 🔲 Less than 5 min
 🔲 Between 5 and 15 min
 🔲 More than 15 min

### Data responsibility
*Before you work with data, you must confirm that it is unbiased and credible. After all, if you start your analysis with unreliable data, you won’t be able to trust your results. - Google Data Analytics Course*

The data doesn’t seem to have any issue regarding bias or credibility as it is Cyclistic that collected the data of all the trips in the city of Chicago. Nevertheless, I need to keep bias and fairness on top of my mind to keep an open mind and not create any bias.

Does the data ROCCC?
R - reliable: it is accurate and unbiased.
O - original: it is original and we know the origin of the data.
C - comprehensive: it is comprehensive but there is about 1/5 of the data that has some missing fields such as start station name, start station id, end station name, and end station id. 
C - current: it is current and I could use the last 12 months of data.
C - cited: it is cited and we know who created it.

### Data transformation
All the data transformation steps I took can be found in my [Jupyter Notebook](https://github.com/Sophie-Pages/GoogleDataAnalyticsCyclisticCaseStudy/blob/main/Jupyter_Notebook/case_study.ipynb). 

Documentation of the data collection and data transformation steps:
* Verify that I can access the downloaded files and create dataframes from them.
* Verify that each dataframe has the same number of columns, column names, and types.
* Import the datraframes into one.
* Remove columns I don’t plan on using.
* Rename some unclear column names.

## Process
*The Process phase is the cleaning phase. It is the time to clean your data to get rid of any possible errors, inaccuracies, or inconsistencies.*

For this project, I cleaned my data using Python in a Jupyter Notebook. 

### Documentation of data cleaning and data manipulation steps
Using my initial to-do list that can be found in my [Jupyter Notebook](https://github.com/Sophie-Pages/GoogleDataAnalyticsCyclisticCaseStudy/blob/main/Jupyter_Notebook/case_study.ipynb), I created a documentation of the data cleaning and data manipulation steps I took:
* Verify bike_type unique values
* Verify membership_type unique values
* Manage the timestamps.
  * Create the columns start_day, start_time, end_day and end_time
  * Add the day of the week
  * Add the month
  * Remove rows where start_time and end_time are the same
  * Remove rows where start_day and end_day are not the same
* Check duplicates
  * Check if there are duplicate rows across all columns
  * Check if there are duplicate rows across specific columns
  * Duplicates found: ride_id should have unique values
  * Understand why there are duplicates and delete the appropriate rows
* Calculate the ride duration and create a column with the ride duration in minutes for easier data visualisations
* Check if any row has a ride_id that doesn't have the right length
* Check the number of null values per column and delete the appropriate rows

* Download my dataframe in CSV to import it into Tableau

## Analyse and Share phase
*The Analyse phase is when you do your data analysis: you organize, format, and aggregate the data to gain insights. Data calculations can also help you explore your data further.*

*The Share phase is show time! It is about showing your summarised results with clear and enticing visuals. This is when you can demonstrate to the stakeholders that you have solved their problem and explain how you got there.*

When working on this case study, I mostly used visualisations to do the analysis phase. Because of this, I will be showing my analysis steps with those visualisations. 

**_Note:_** Be aware that this is an in-depth analysis. If I were to show my analysis results to the stakeholders, I would do a summarised presentation of these steps or share my Dashboard which found in Tableau here: [Cyclistic Case Study Dashboard](https://public.tableau.com/app/profile/sophie.pages/viz/GoogleDataAnalyticsDashboardofCyclisticcasestudy/Dashboard3).

### Overview
After data cleaning and data manipulation, my data has 4084229 rows which means 4084229 trips. About 64% of these trips were done by members. 

The first ride started on 11/01/2023 and the last ride ended on 10/31/2024.

### Average ride time
![image](https://github.com/user-attachments/assets/06c361ce-ecb8-40c1-aa6c-42f4bea03f79)

The average ride time across all riders is 16 minutes. The members had an average of 12 minutes and the casual riders had an average of 22 minutes. The casual riders ride on average almost twice as long as the members.

My assumption is that members are located close to their workplaces and mostly use Cyclistic to go to work and go back home. Casual riders on the other hand use it mostly for leisure or to do sport. I will try to confirm or infirm my assumption with my analysis.

![image](https://github.com/user-attachments/assets/b51ebaa4-4902-4fb8-be9a-bbefac4c2e1f) ![image](https://github.com/user-attachments/assets/25c32f1a-ecf2-4501-bcff-99734d7f873b)


![image](https://github.com/user-attachments/assets/65594f95-5d4e-4c0b-8321-d890486752e2) ![image](https://github.com/user-attachments/assets/17a423c4-084b-4b46-9153-e764e31c5bcd)


![image](https://github.com/user-attachments/assets/1e72d839-bc33-4810-8360-fb287a9dc95e) ![image](https://github.com/user-attachments/assets/bc9b0747-b519-4673-9c4e-30fa529d3dba)


![image](https://github.com/user-attachments/assets/d231e181-de26-4b35-b1f4-d4040d415014) ![image](https://github.com/user-attachments/assets/8f9c85aa-7e4f-4a8c-8a57-4938e5a861ef)



The average ride time for members stays quite steady throughout the hours of the day, weeks, and months. On the other hand, casual riders have a significant growth in ride time between 10 a.m. and 12 p.m., on Saturdays and Sundays, and in the summer months. 

### Frequent stations
![image](https://github.com/user-attachments/assets/c910303d-f644-4076-a6f7-3648289c0c20)

![image](https://github.com/user-attachments/assets/39e1056d-8e31-44ab-813a-8fc9d8320130)

The three most frequent stations are the same if we look at start stations and end stations. For members, those stations are “Kingsbury St & Kinzie St”, “Clinton St & Washington Blvd” and “Clinton St & Madison St”. The three most frequent stations for casual riders are “Streeter Dr & Grand Ave”, “DuSable Lake Shore Dr & Monroe St” and “Michigan Ave & Oak St”. After a quick analysis using a map, I can see that the stations used by members are close to the center of the city, and thus, close to workplaces and commercial areas. The stations that are the most used by casual riders are close to the seaside of the city where parks are.

### Vehicle types
![image](https://github.com/user-attachments/assets/fa65b4b6-5d67-46d3-b75f-f202d4c46684)

![image](https://github.com/user-attachments/assets/7ba855f9-4ced-4d4a-94b0-be602ba3487f)

There are three types of vehicles in the data I collected: electric bike, classic bike, and electric scooter. However, in the information provided to me, Cyclistic is said to use reclining bikes, hand tricycles, and cargo bikes. Either that information is outdated or the assistive options are not clearly identified in the dataset. If the information is missing from our dataset or if it was removed during the anonymisation process, we could add a question asking if the users use our assistive bikes in the anonymous survey I proposed to the stakeholders.

The most used type of vehicle is the classic bike for both members and casual riders, followed by the electric bike, and the electric scooter. 

### Number of rides
![image](https://github.com/user-attachments/assets/fc331242-d19e-4b87-a92b-18cc9b6c6ba7)

Two-thirds of the total number of rides were done by members and one-third by casual riders. Either the members are more diligent and ride more often or there are just more members than casual riders or both. Due to the anonymisation process, we have no way of knowing without doing a survey.  My assumption is that there are more members than casual riders and they are more diligent as well.

#### By hour
![image](https://github.com/user-attachments/assets/d7efc80d-25e5-4f7b-9c05-5357fcea28f0) ![image](https://github.com/user-attachments/assets/bdf9c8db-cdcd-4233-9840-520ea935f5b4)

**_Note:_** For the clarity of the visualisation, the number of rides has been aggregated by hour of the day. For example, all the rides that started between 8 a.m. and 8:59 a.m. will be counted in the 8am peak number. 

If we look at the number of rides per hour of the day for members, we can see that there are two peaks with a first one between 7-9 a.m. and an even bigger one between 4-6 p.m.. For casual riders, however, there is only one peak between 4-6 p.m. We can assume that casual riders use Cyclistic after their work hours to do some activities and visit the parks that we previously mentioned. 

#### By day
![image](https://github.com/user-attachments/assets/891dc196-9659-496d-a346-5df7a5149de6) ![image](https://github.com/user-attachments/assets/f42a2f2f-d7ef-4702-821a-9a4f6e045b58)

The days that have the highest number of rides for members are Wednesday and Thursday whereas it is Saturday and Sunday for casual riders.

The days that have the lowest number of rides for members are Saturday and Sunday whereas it is Monday and Tuesday for casual riders.

#### By month
![image](https://github.com/user-attachments/assets/e1656595-e98b-4bdc-97bf-b0944c354a61) ![image](https://github.com/user-attachments/assets/5c38df4e-200b-4d42-ae68-4d9519128a0d)

The month where we can see the highest number of rides for members is September whereas it is July for casual riders.

The month that has the lowest number of rides for both members and casual riders is January.

#### By season
![image](https://github.com/user-attachments/assets/0e596929-de77-43c9-af46-fa508fcd8dfd) ![image](https://github.com/user-attachments/assets/91bd168c-a1a4-405e-88bb-0b6934fbfa13)

If we take a look at the heat map, we have the highest number of rides in the Summer and the lowest in the Winter for both members and casual riders.

### Summarised results
Let’s go back to the question that was assigned to me: “How do annual members and casual riders use Cyclistic bikes differently?“.

Because we want to convert casual rides into members, we will focus on what makes the casual riders different from the members.

Casual riders:
* Have an average ride time that is almost twice the one of the members.
* Have a peak of average ride time from 10 a.m. to 2 p.m.
* Have the longest ride time and number of rides during the weekend.
* Have their most frequent stations close to the seaside and parks.

Members:
* Have half the average ride time of the casual riders.
* Have a stable average ride time throughout the day.
* Have a peak of number of rides between 7-9 a.m that casual riders don’t have.
* Have the most rides on Wednesdays and Thursdays.
* Have their most frequent stations close to the center of the city, and thus, close to workplaces and commercial areas.

All these observations confirm my first assumption that members mostly use Cyclistic to go to work and go back home while casual riders on the other hand use it mostly for leisure or to do sport.

### Documentation of the visualisations
Not all visualisations have made it to my final dashboard. 

You can find below the full list of visualisations I made during my analysis process:
* Table
  * Total number of rides
  * Average ride time
  * Average ride time by membership type
  * Average ride time by ride type 
  * Most used start stations across all users
  * Most used end stations across all users
  * Most used start stations per membership type
  * Most used end stations per membership type
* Pie chart
  * Number of rides by membership type
  * Number of rides by ride type
* Bar chart
  * Number of rides by ride type
* Line chart
  * Number of rides by hour of the day
  * Number of rides by day of the week 
  * Number of rides by month
  * Average ride time by hour of the day 
  * Average ride time by day of the week
  * Average ride time by month
* Heat map
  * Number of rides by day of the week and by month
  * Number of rides by day of the week and by month per membership type
  * Number of rides by day of the week and by season
  * Number of rides by day of the week and by season per membership type
  * Average ride time by day of the week and by month
  * Average ride time by day of the week and by month per membership type
  * Average ride time by day of the week and by season per membership type 

**_Note:_** The line charts could be replaced by bar charts but I personally find the line charts more clear.

## Act 
*The Act phase is the time to act on your data. This is when you gather everything that has been learned from your data analysis and put it to use. This usually means providing recommendations to the stakeholders to ensure that they can make data-driven decisions.*

### Recommendations
The stakeholders have asked for my top three recommendations based on my analysis.

#### Health benefits
Because casual riders seem to be using Cyclistic mostly for leisure or sport. To appeal to casual riders, I recommend a marketing campaign focusing on the health benefits of doing exercise and riding bikes. To make the casual riders want to switch to a membership, the campaign should clearly communicate the benefits of purchasing a membership. One of the reasons could be health benefits due to the members being more diligent with their rides. 

#### Discount
Because casual riders tend to ride bikes during summer months, I recommend a marketing campaign proposing a discount to start an annual membership during that period. 

#### New membership
Understanding why casual riders have not switched to an annual membership would be ideal in order to create an effective marketing campaign. With the data that was provided, it is not something that I can deduct easily but I can still make an hypothesis. 

I recommend doing the survey that I talked about previously to understand the reasons why they have not switched to an annual membership. My hypothesis is that they want to have the opportunity to use their bikes for longer than 45 minutes. If my hypothesis is verified after doing the survey, I recommend that we create a new membership. We could have the first 3 hours for free like for the day passes or we could lift the limitation only during the weekend.

#### Marketing campaign ideas
As casual riders mostly ride during the weekends, the marketing campaign should target Saturdays and Sundays if they plan to use advertising posters. Those advertising posters should be close to the most frequent stations used by casual riders.

Using social media is a must nowadays. Because casual riders are already using Cyclistic, they could be interested in content relating to bikes, health benefits, sports, best places to use Cyclistic, etc. If Cyclistic grows its social media, casual riders will continue to hear about them throughout the months -thus keeping them interested in using Cyclistic- and see the marketing campaign when it launches.

#### Additional idea
We came to the conclusion that casual riders are active people interested in doing sports or leisure activities. Cylistic could do partnerships with sports events or gyms to continue to appeal to casual riders and as well appeal to those that haven’t heard about them before.






