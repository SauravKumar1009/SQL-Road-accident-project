# SQL-Road-accident-project

-- Question 1
-- How many accidents have occured in urban area versus rural area?
-- Method_1
select
Area,
COUNT(Area) as [No of accidents]
from accident
group by Area;

-- Method_2
select (select COUNT(Area) from accident where Area = 'Urban') as [Urban Accidents]
,(select COUNT(Area) from accident where Area = 'Rural') as [Rural Accidents]

-- Question 2
-- Which day of the week has the highest number of accidents?
-- Method_1
select top 5 * from accident;
select top 1 
Day,
COUNT(*) as [No of accidents]
from accident
group by Day
order by [No of accidents] desc;

-- Method_2 ( If there is no Day column in the accident table )
select top 1
datename(weekday,date) as [DayName],
COUNT(*) as [No of accidents]
from accident
group by datename(weekday,date)
order by [No of accidents] desc;

-- Question 3
-- What is the average age of vehicles involved in accidents based on their type?
select  
VehicleType
,AVG(isnull(AgeVehicle,0)) as [Average Age of Vehicle]
from vehicle
group by VehicleType
order by [Average Age of Vehicle] desc;

-- Question 4
-- Can we identify any trends in accidents based on the age of vehicles involved?
select  
isnull(AgeVehicle,0) as [Age of Vehicle]
,COUNT(AccidentIndex) as [No of accidents]
from vehicle
group by AgeVehicle 
order by [No of accidents] desc;

-- Question 5
-- Are there any specific weather conditions that contribute to severe accidents?
-- Severe accidents are accidents which belongs to 'Fatal' and 'Serious' severity.
-- Method_1
select distinct
WeatherConditions
,Severity
,COUNT(Severity) as [Count of accidents]
from accident
where Severity = 'Fatal' or Severity = 'Serious'
group by WeatherConditions,Severity;

--Method_2
with cte as
(
select 
Severity
,WeatherConditions
,COUNT(Severity) as [No of accidents]
from accident
group by Severity,WeatherConditions)
select * from 
(select * from cte) c
pivot( sum([No of accidents]) for Severity in ([Fatal],[Serious])) a

-- Question 6
-- Do accidents often involve impacts on the left hand side of vehicles?
select Lefthand,COUNT(*) as [No of accidents]
from vehicle
group by LeftHand;

-- Question 7
-- Are there any relationships between journey purposes and the severity of accidents?
select a.Severity,v.JourneyPurpose,COUNT(*) as [No of accidents]
from accident a inner join vehicle v
on a.AccidentIndex = v.AccidentIndex
group by a.Severity,v.JourneyPurpose
order by [No of accidents];

-- Question 8
/* Create a stored procedure to calculate the average age of vehicle involved in accidents,considering light conditions
   and point of impact as two variable/inputs : */
   -- light condition = 'Daylight',
   -- Point of impact = 'Offside'

create procedure spAverageageVehicle
(
@Lightcondition varchar(50),
@Pointofimpact varchar(50)
)
as 
begin 
select AVG(AgeVehicle) as [Average age of Vehicle] 
from vehicle v inner join accident a
on v.AccidentIndex = a.AccidentIndex
where a.LightConditions = @Lightcondition
and v.PointImpact = @Pointofimpact
end
   
execute spAverageageVehicle 'Daylight','Offside';


