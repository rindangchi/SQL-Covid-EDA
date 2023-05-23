# Covid Case EDA using SQL
Below are the solutions regarding the questions that are given before.

### 1. What are percentage of death from the total case per country and what country that has the highest percentage of death ?
#### Solution: 
```sql
select location as country, round((cast(max(total_deaths) as real)/cast(max(total_cases) as real)),3)*100 as death_percentage
from CovidDeaths
where continent is not NULL
group by location
order by death_percentage desc
```

<img width="283" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/88eaf287-fefa-496b-8183-8da746608e5e">

Vanuatu is the country with the highest date percentage calculated from the total cases per country

### 2. For each continent Which country has the highest death percentage based on total cases ?
#### Solution: 
```sql
select continent, location as country, death_percentage
from
	(select continent, location, row_number () over (PARTITION by continent order by death_percentage DESC) as rn, death_percentage
	FROM
	(select continent, location, round((cast(max(total_deaths) as real)/cast(max(total_cases) as real)),3)*100 as death_percentage
	from CovidDeaths
	where continent is not NULL
group by location)
)
where rn = 1
order by death_percentage desc
```
<img width="283" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/13b216ca-d894-4c86-a3dc-5a349dd6aa37">

### 3. For each continent which country has the highest infection percentage based on population ?
#### Solution:
```sql
select continent, location as country, infection_percentage
from
	(select continent, location, row_number () over (PARTITION by continent order by infection_percentage DESC) as rn, infection_percentage
	FROM
	(select continent, location, round((cast(max(total_cases) as real)/cast(max(population) as real)),3)*100 as infection_percentage
	from CovidDeaths
	where continent is not NULL
group by location)
)
where rn = 1
order by infection_percentage desc
```
<img width="283" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/b4285b18-d1d3-4df0-9ea6-897eaeadb631">

### 4. What is the number of cases and death on each continent ?
#### Solution:
```sql
select continent, sum(case_per_country) case_per_continent, sum(deaths_per_country) deaths_per_continent
from
(select continent, location, max(total_cases) case_per_country, max(total_deaths) deaths_per_country
from CovidDeaths
where continent is not null
group by location)
group by continent
order by 2 desc
```
<img width="307" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/4bc08a51-6a8f-4cb4-b1c9-6b67bc2e8953">

### 5. What is percentage of death from total cases on each continent ?
#### Solution:
```sql
select continent, round((cast(deaths_per_continent as real)/cast(case_per_continent as real)),4)*100 as death_percentage_per_continent
from
(select continent, sum(case_per_country) case_per_continent, sum(deaths_per_country) deaths_per_continent
from
(select continent, location, max(total_cases) case_per_country, max(total_deaths) deaths_per_country
from CovidDeaths
where continent is not null
group by location)
group by continent)
order by death_percentage_per_continent desc
```
<img width="283" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/1ae2193d-95ee-4cea-8e54-8f22abbc8c65">

### 6. What is the number of average new case per day by country ?
#### Solution:
```sql
select location as country, round(avg(new_cases),0) as average_new_cases
from CovidDeaths
where continent is not null
group by location
order by average_new_cases desc
```
<img width="287" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/8644494f-ddb8-4e60-a3da-dfd171aff929">

### 7. What is the highest new case in each country in 2020 and 2021 ?
#### Solution:
```sql
select country,
sum(case when Year = '2020' then max_cases end) as '2020',
sum(case when Year = '2021' then max_cases end) as '2021'
FROM
(
select country, strftime('%Y',SomeDate) as Year, max(new_cases) max_cases
FROM
(select location as country, (substr(date, 7, 4) || '-' || substr(date, 4, 2) || '-' || substr(date, 1, 2)) AS SomeDate, new_cases
from CovidDeaths
where continent is not NULL)
group by 1,2)
group by country
```
<img width="283" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/ef1f809d-93ec-4e94-87b9-cf86e82c6014">

### 8. What is the month when the highest new case happened in each country in 2020 and 2021 ?
#### Solution:
```sql
select country, strftime('%Y',SomeDate) as Year, strftime('%m',SomeDate) as Month, max(new_cases) max_cases
FROM
(select location as country, (substr(date, 7, 4) || '-' || substr(date, 4, 2) || '-' || substr(date, 1, 2)) AS SomeDate, new_cases
from CovidDeaths
where continent is not NULL)
group by 1, 2
```
<img width="310" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/3a6f772c-c678-4a6f-8674-672716190a29">

### 9. What is percentage of population that has received at least one Covid Vaccine ?
#### Solution:
```sql
select a.location as country, round(cast(a.total_people_vaccinated as real)/cast(b.total_population as real),6)*100 as percentage_of_vaccinated_people
from
(
(select iso_code, location, max(people_vaccinated) as total_people_vaccinated 
from CovidVaccinations
where continent is not NULL
group by location) a
join
(select iso_code, location, max(population) as total_population
from CovidDeaths
where continent is not NULL
group by location) b
on a.iso_code = b.iso_code)
```
<img width="365" alt="image" src="https://github.com/rindangchi/SQL-Covid-EDA/assets/10241058/381d1d7d-c737-476f-9288-2a531d0782f8">
