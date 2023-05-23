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

