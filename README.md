# MentorHub
## SQL Q&A Pet Project

This repository contains a pet project dedicated to answering product manager questions using SQL queries.

### Project Description
MentorHub is a service that helps mentors and cops find each other. Meetings take place on the service site. You can schedule a meeting in your account after authorization on the site.
In this pet project, we aim to provide insights and answers to various queries frequently asked by product managers. The project involves analyzing data related to mentors and mentees interactions on our platform using SQL queries.

### Structure of MentorHub database
Database includes this tables:

1. domain — mentoring directory

   id || Unique direction identifier

   name || Direction name
   
2. region — directory of user regions

   id || Region unique identifier

   name || Region name
   
3. users — user table.

   user_id || Unique user ID

   reg_date || User registration date

   role || User role: mentor or mentee

   region_id || User region ID
   
4. sessions — session table.

   session_id || Unique session ID

   session_date_time || Date and time of the session

   mentor_id || Mentor ID

   mentee_id || Mentee ID

   session_status || Session status: finished or cancelled

   mentor_domain_id || Mentoring direction ID

### Product manager questions
The following product manager questions are included in this project:
1. Quantity of Mentors and Mentees Interacting Monthly
2. How many mentors and mentees are there on the platform who have not participated in any meetings yet? 
Take into account users who have never assigned themselves meetings. 
Why are they not participating in meetings? What hypotheses can be tested?/
3. How many average successful sessions per week does each mentor have for each month? 
How does the frequency of meetings per week change from month to month?
Identify the top 5 mentors with the highest number of sessions in the last full month.
4. How much time on average passes between mentor meetings for a single mentor?
5. How many sessions for each mentoring direction are usually cancelled per month? 
How does the percentage of cancelled sessions change monthly?
6. Determine the day of the week with the most meetings in the last full month.
Identify the busiest day of the week for each mentoring direction. 
Display the direction type, day of the week, and number of meetings in the results. 
