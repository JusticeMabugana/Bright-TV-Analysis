---USER PROFILES EDA
select *
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`
limit 10;

--viewing the headers
SELECT * FROM `workspace`.`default`.`bright_tv_dataset_1_user_profiles`
limit 10;

-- viewing the number of unique UserIDs
select distinct count(userid) from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

--sorting IDs in ascending and descending order to see the first and last user ID
select userid 
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`
order by userid desc;

select userid 
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`
order by userid asc;

-- viewing the unique genders on the list
select distinct gender
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

-- viewing the unique races on the list
select distinct Race
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

-- finding the lowest and highest age
select min(age) as min_age, max(age) as max_age 
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

-- finding the unique values in provinces
select distinct province
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

--categorising into different age groups
Select *, 
case when Age < 18 then '01. Minor (0-17)'
when Age between 18 and 35 then '02. Young Adult (18-35)'
when Age between 36 and 55 then '03. Adult (36-55)'
when Age > 55 then '04. Senior (55+)'
end as Age_Group
FROM `workspace`.`default`.`bright_tv_dataset_1_user_profiles`
order by userid asc;

create temporary view user_profile_summary1 as
select userid, age,
case
when gender ='other' then 'Unspecified'
when gender is null then 'Unspecified'
else gender
end as Gender,
case
when race ='other' then 'Unspecified'
when race is null then 'Unspecified'
else race
end as Race,
case 
when province ='other' then 'Unspecified'
when province is null then 'Unspecified'
else province
end as Province,
case 
when Age < 18 then '01. Minor (0-17)'
when Age between 18 and 35 then '02. Young Adult (18-35)'
when Age between 36 and 55 then '03. Adult (36-55)'
when Age > 55 then '04. Senior (55+)'
end as Age_Group
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

select *
from user_profile_summary1;


---VIEWERSHIP EDA
select *
from `workspace`.`default`.`bright_tv_dataset_1_viewership`
limit 10;

--viewing columns in the viewership table
SELECT * 
FROM `workspace`.`default`.`bright_tv_dataset_1_viewership`
limit 10;

--finding the unique values in channel column
select distinct channel
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

--converting record2 to a timestamp format and extracting the record date and time
select to_timestamp(recorddate2, 'M/d/yyyy H:mm') as NewRecordDate,
to_date (newrecorddate) as RecordDate,
to_char (newrecorddate, 'HH:mm') as recordtime
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

--ealierst and latest record date
select 
min(to_date(to_timestamp(recorddate2, 'M/d/yyyy H:mm'))) as EalierstRecordDate,
max(to_date(to_timestamp(recorddate2, 'M/d/yyyy H:mm'))) as LatestRecordDate
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

--record time timebuckets
select recorddate2,to_char (to_timestamp(recorddate2, 'M/d/yyyy H:mm'), 'HH:mm') as recordtime,
case 
when recordtime between '00:00' and '05:00' then '01. Midnight'
when recordtime between '05:00' and '12:00' then '02. Morning'
when recordtime between '12:00' and '18:00' then '03. Afternoon'
when recordtime between '18:00' and '21:00' then '04. Evening'
else '05. Night'
end as RecordTimeBucket
from `workspace`.`default`.`bright_tv_dataset_1_viewership`; 


--max and min viewing duration
select min(`duration 2`) as min_duration, max(`duration 2`) as max_duration
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

--viewing duration time buckets
select `duration 2`, 
case
  when `duration 2` = '0:00:00' then "Not watched"
  when `duration 2` between '0:00:00' and '0:15:00' then "01. Short span (0-15 mins)"
  when `duration 2` between '0:15:01' and '0:30:00' then "02. Medium span (15-30 mins)"
  when `duration 2` between '0:30:01' and '1:00:00' then "03. Long span (30 mins-1 hour)"
  when `duration 2` between '1:00:01' and '2:00:00' then "04. Very Long span (1-2 hours)"
  else "05. Binge watching (over 2 hours)"
end as ViewingSpan
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

select USERID, CHANNEL,`duration 2`,
case
  when `duration 2` = '0:00:00' then "Not watched"
  when `duration 2` between '0:00:00' and '0:15:00' then "01. Short span (0-15 mins)"
  when `duration 2` between '0:15:01' and '0:30:00' then "02. Medium span (15-30 mins)"
  when `duration 2` between '0:30:01' and '1:00:00' then "03. Long span (30 mins-1 hour)"
  when `duration 2` between '1:00:01' and '2:00:00' then "04. Very Long span (1-2 hours)"
  else "05. Binge watching (over 2 hours)"
end as ViewingSpan,
recorddate2,to_char (to_timestamp(recorddate2, 'M/d/yyyy H:mm'), 'HH:mm') as recordtime,TO_DATE(to_timestamp(recorddate2, 'M/d/yyyy H:mm')) as NewRecordDate,
case 
when recordtime between '00:00' and '05:00' then '01. Midnight (00:00-05:00)'
when recordtime between '05:00' and '12:00' then '02. Morning (05:00-12:00)'
when recordtime between '12:00' and '18:00' then '03. Afternoon (12:00-18:00)'
when recordtime between '18:00' and '21:00' then '04. Evening (18:00-21:00)'
else '05. Night (21:00-00:00)'
end as RecordTimeBucket
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;


CREATE TEMPORARY VIEW viewership_summary as 
select USERID, CHANNEL,`duration 2`,
case
  when `duration 2` = '0:00:00' then "Not watched"
  when `duration 2` between '0:00:00' and '0:15:00' then '01. Short span (0-15 mins)'
  when `duration 2` between '0:15:01' and '0:30:00' then '02. Medium span (15-30 mins)'
  when `duration 2` between '0:30:01' and '1:00:00' then '03. Long span (30 mins-1 hour)'
  when `duration 2` between '1:00:01' and '2:00:00' then '04. Very Long span (1-2 hours)'
  else "05. Binge watching (over 2 hours)"
end as ViewingSpan,
recorddate2,to_char (to_timestamp(recorddate2, 'M/d/yyyy H:mm'), 'HH:mm') as recordtime,TO_DATE(to_timestamp(recorddate2, 'M/d/yyyy H:mm')) as NewRecordDate, dayname(NewRecordDate) as DayName,
case 
when recordtime between '00:00' and '05:00' then '01. Midnight (00:00-05:00)'
when recordtime between '05:00' and '12:00' then '02. Morning (05:00-12:00)'
when recordtime between '12:00' and '18:00' then '03. Afternoon (12:00-18:00)'
when recordtime between '18:00' and '21:00' then '04. Evening (18:00-21:00)'
else '05. Night (21:00-00:00)'
end as RecordTimeBucket
from `workspace`.`default`.`bright_tv_dataset_1_viewership`;

SELECT * FROM viewership_summary;

create temporary view user_profile_summary as
select userid, age,
case
when gender ='other' then 'Unspecified'
when gender is null then 'Unspecified'
when gender ='None' then 'Unspecified'
else gender
end as Gender,
case
when race ='other' then 'Unspecified'
when race ='None' then 'Unspecified'
when race is null then 'Unspecified'
else race
end as Race,
case 
when province ='other' then 'Unspecified'
when province ='None' then 'Unspecified'
when province is null then 'Unspecified'
else province
end as Province,
case 
when Age = 0 then 'Unspecified'
when age between 1 and 18 then '01. Minor (1-17)'
when Age between 18 and 35 then '02. Young Adult (18-35)'
when Age between 36 and 55 then '03. Adult (36-55)'
when Age > 55 then '04. Senior (55+)'
else 'Unspecified'
end as Age_Group
from `workspace`.`default`.`bright_tv_dataset_1_user_profiles`;

select *
from user_profile_summary;

SELECT A.*, B.*
FROM viewership_summary AS A
LEFT JOIN user_profile_summary AS B ON A.USERID = B.USERID; 
