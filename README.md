# covid19
Real-world application of covid19 cases.

## Code:
​/*

​

Covid 19 Data Exploration

​

*/

--select location, date, total_cases, new_cases, total_deaths, population

--From PortfolioProject.dbo.Covid_deaths as COV19D

--where continent is not null

--order by 1,2

​

--  looking aat the total cases vs total deaths in germany

--  percentage of confirmed Covid19 cases (people who got tested)

​

select location, date, total_cases, new_cases, population, (total_cases/population)*100 as population_infected

From PortfolioProject.dbo.Covid_deaths as COV19D

where location like '%germany%'

order by 1,2

​

-- finding the highest infection rate at countries based on population

​

select location, Max(total_cases) as highest_infection_count, population, Max((total_cases/population))*100 as Precentage_of_population_infected

From PortfolioProject.dbo.Covid_deaths as COV19D

group by location, population

order by Precentage_of_population_infected desc

​

-- finding the highest death rate at countries based on population

​

select location, Max(cast(total_deaths as int)) as highest_death_count

From PortfolioProject.dbo.Covid_deaths as COV19D

where continent is not null

group by location

order by highest_death_count desc

​

-- finding the total cases rate at countries from 2020 to mid-2021 based on population

​

--select location, as date, sum(total_cases) as total_infection_count, population, sum((total_cases/population))*100 as Precentage_of_total_infections

--From PortfolioProject.dbo.Covid_deaths as COV19D

--group by location, population

--order by Precentage_of_total_infections desc

​

--Select *

--from PortfolioProject.dbo.Covid_Vaccination as COV19V

--order by 3,4

--showing contintents with the highest death count per populations

​

select continent, Max(cast(total_deaths as int)) as highest_death_count

From PortfolioProject.dbo.Covid_deaths as COV19D

where continent is not null

group by continent

order by highest_death_count desc

​

-- Global Numbers

​

select /*date,*/ sum(new_cases) as total_cases, sum(cast(new_deaths as int)) as total_deaths ,sum(cast(new_deaths as int))/sum(new_cases)*100 as DeathPercentage

From PortfolioProject.dbo.Covid_deaths as COV19D

where continent is not null and total_cases is not null and total_deaths is not null

--group by date

order by 1,2

​

--

​

select COV19D.continent, COV19D.location, COV19D.date, COV19D.population, Covid19V.new_vaccinations,

sum(cast(Covid19V.new_vaccinations as int)) over (partition by cov19d.location order by cov19d.location, cov19d.date) as the_sum_of_people_vaccinatd

--(the_sum_of_people_vaccinated/population)*100 build an ECT

From PortfolioProject.dbo.Covid_deaths COV19D

join portfolioProject.dbo.Covid_Vaccination Covid19V

on COV19D.location = covid19V.location

and COV19D.date = covid19V.date

where cov19d.continent is not null and Covid19V.new_vaccinations is not null

order by 2,3

​

-- I need ECT to find the percentage of people getting vacccinated compared with the population

​

with PopVsVac (continent, location, date, population,new_vacccination, the_sum_of_people_vaccinatd)

as

(

select COV19D.continent, COV19D.location, COV19D.date, COV19D.population, Covid19V.new_vaccinations,

sum(cast(Covid19V.new_vaccinations as int)) over (partition by cov19d.location order by cov19d.location, cov19d.date) as the_sum_of_people_vaccinatd

From PortfolioProject.dbo.Covid_deaths COV19D

join portfolioProject.dbo.Covid_Vaccination Covid19V

on COV19D.location = covid19V.location

and COV19D.date = covid19V.date

where cov19d.continent is not null

--order by 2,3

)

​

select *, (the_sum_of_people_vaccinatd/population)*100 as percentage_of_total_people_vaccinated

from PopVsVac

order by percentage_of_total_people_vaccinated

​

drop table if exists #percentpopulationvaccinated

create table #percentpopulationvaccinated

(

Continent nvarchar(255),

location nvarchar(255),

date datetime,

population numeric,

New_vaccinations numeric,

the_sum_of_people_vaccinatd numeric

)

​

insert into #percentpopulationvaccinated

select COV19D.continent, COV19D.location, COV19D.date, COV19D.population, Covid19V.new_vaccinations,

sum(cast(Covid19V.new_vaccinations as int)) over

(partition by cov19d.location order by cov19d.location, cov19d.date) as the_sum_of_people_vaccinatd

From PortfolioProject.dbo.Covid_deaths COV19D

join portfolioProject.dbo.Covid_Vaccination Covid19V

on COV19D.location = covid19V.location

and COV19D.date = covid19V.date

where cov19d.continent is not null

--order by 2,3

​

select *, (the_sum_of_people_vaccinatd/population)*100 as percentage_of_total_people_vaccinated

from #percentpopulationvaccinated

​

create view percentpopulationvaccinated as

select COV19D.continent, COV19D.location, COV19D.date, COV19D.population, Covid19V.new_vaccinations,

sum(cast(Covid19V.new_vaccinations as int)) over

(partition by cov19d.location order by cov19d.location, cov19d.date) as the_sum_of_people_vaccinatd

From PortfolioProject.dbo.Covid_deaths COV19D

join portfolioProject.dbo.Covid_Vaccination Covid19V

on COV19D.location = covid19V.location

and COV19D.date = covid19V.date

where cov19d.continent is not null

--order by 2,3

​

select *

from percentpopulationvaccinated

where the_sum_of_people_vaccinatd is not null

## Findings: 
https://public.tableau.com/app/profile/yousif.aldossary/viz/Covid-19worldwideimpacct/Covid-19GlobalImpact
