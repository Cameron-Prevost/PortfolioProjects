# PortfolioProjects
select location, date, total_cases, new_cases, total_deaths, population
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
order by 1,2
-- Looking at Total Cases vs Total Deaths

select location, date, total_cases, new_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
where location like States
order by 1,2

-- Looking at Total Cases vs Total Deaths
select location, date, total_cases, new_cases, total_deaths, (total_deaths/total_cases)*100 as DeathPercentage
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
where location = 'United States'
order by 1,2


-- Show what percentage of population got Covid
select location, date,population,  total_cases, new_cases, (total_cases/Population)*100 as PercentPopulationInfected
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
where location = 'United States'
order by 1,2

-- Looking at countries with highest infection rate compared to population
select location, population,  MAX(total_cases) as HighestInfectionCount, MAX ((total_cases/Population))*100 as PercentPopulationInfected
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
--where location = 'United States'
group by location, population
order by PercentPopulationInfected desc

-- Showing Countries with Highest Death Count per Population
select location,MAX(cast(total_deaths as int)) as TotalDeathCount
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
--where location = 'United States'
where continent is not null
group by location
order by TotalDeathCount desc

-- Break Down by Continent
select location,MAX(cast(total_deaths as int)) as TotalDeathCount
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
--where location = 'United States'
where continent is null
group by location
order by TotalDeathCount desc

-- Global Numbers
select date, SUM(new_cases) as total_cases,SUM(new_deaths)as total_deaths, SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
--where location = 'United States'
where continent is not null
group by date
order by 1,2

-- Global Numbers
select SUM(new_cases) as total_cases,SUM(new_deaths)as total_deaths, SUM(new_deaths)/SUM(new_cases)*100 as DeathPercentage
from `portfolio-project-cp1.covid_deaths.covid_deaths_table`
--where location = 'United States'
where continent is not null
group by date
order by 1,2

Select *
from `portfolio-project-cp1.covid_deaths.covid_deaths_table` dea
join `portfolio-project-cp1.covid_vaccinations.covid_vaccinations_table` vac
on dea.location = vac.location
and dea.date = vac.date

--Looking at Total Population vs Vaccinations
Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
,Sum(vac.new_vaccinations) over (partition by dea.location order by dea.location, dea.date) as RollingVaccinated
--, (RollingVaccinated/population)*100
from `portfolio-project-cp1.covid_deaths.covid_deaths_table` dea
join `portfolio-project-cp1.covid_vaccinations.covid_vaccinations_table` vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
order by 2,3

-- CTE
With PopvsVac
as
(
    Select dea.continent, dea.location, dea.date, dea.population, vac.new_vaccinations
,Sum(vac.new_vaccinations) over (partition by dea.location order by dea.location, dea.date) as RollingVaccinated
--, (RollingVaccinated/population)*100
from `portfolio-project-cp1.covid_deaths.covid_deaths_table` dea
join `portfolio-project-cp1.covid_vaccinations.covid_vaccinations_table` vac
on dea.location = vac.location
and dea.date = vac.date
where dea.continent is not null
--order by 2,3
)
select *, (RollingVaccinated/population)*100
from PopvsVac
