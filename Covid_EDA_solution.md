# Covid Case EDA using SQL
Below are the solutions regarding the questions that are given before.

## 1. What are percentage of death from the total case per country and what country that has the highest percentage of death ?
### Solution: 
```sql
select location as country, round((cast(max(total_deaths) as real)/cast(max(total_cases) as real)),3)*100 as death_percentage
from CovidDeaths
where continent is not NULL
group by location
order by death_percentage desc
```
