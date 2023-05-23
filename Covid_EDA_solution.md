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


