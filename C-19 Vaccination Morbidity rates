-- In the dataset Location and continent have values that overlap. Example: Africa is considered as a location and a continent 

SELECT* FROM PortfolioProjects.coviddeath
WHERE continent is not NULL;

 SELECT location, dates, total_cases, new_cases, total_deaths
 FROM PortfolioProjects.coviddeath
 order by 1,2;
 
 -- Case fatality risk ( showing the likelihood of dying if you contract covid) by country. Focusing on the United States 
 
 SELECT location, dates, total_cases, total_deaths,(total_deaths/total_cases)*100 
AS deathpercentage
FROM PortfolioProjects.coviddeath
WHERE location LIKE '%states%';
 
-- Morbidity rate ( showing the percentage of population that contracted covid in the U.S

 SELECT location, dates, total_cases, population,(total_cases/population)*100 
AS Percentpopulationinfected
FROM PortfolioProjects.coviddeath
WHERE location LIKE '%states%';

-- Incidence rate, specifically looking at countries with the highest rates of infection conpared to population

SELECT location, MAX(total_cases) as Highestinfectioncount , population,max((total_cases/population))*100 
AS Percentpopulationinfected
FROM PortfolioProjects.coviddeath
-- WHERE location LIKE '%states%'
GROUP BY location, population
order by Percentpopulationinfected desc;

-- Country with the highest death counts. Using the not null clause to get rid of overlaps in locations/continents 

SELECT location, MAX(total_deaths) as TotalDeathCount
FROM PortfolioProjects.coviddeath
 WHERE continent is not null
GROUP BY location
order by TotalDeathCount desc;

SELECT location, MAX(total_deaths) as TotalDeathCount
FROM PortfolioProjects.coviddeath
 WHERE continent is not null
GROUP BY location
order by TotalDeathCount desc;

-- Global Death percentage 
SELECT sum(new_cases) as total_cases ,sum(new_deaths) as total_deaths , sum(new_deaths)/sum(new_cases) *100
AS deathpercentage
FROM PortfolioProjects.coviddeath
-- WHERE location LIKE '%states%' 
where continent is not null
order by 1,2;

-- Global vaccination 
-- joining covid deaths and vaccinations table 
SELECT coviddeath.continent, coviddeath.location , coviddeath.dates, coviddeath.population, covidvac.new_vaccinations
, sum(covidvac.new_vaccinations) OVER (partition by coviddeath.location order by coviddeath.location,coviddeath.dates )
as rollingpeoplevaccinated
FROM PortfolioProjects.coviddeath 
join PortfolioProjects.covidvac
on coviddeath.location = covidvac.location
and coviddeath.dates = covidvac.dates
where coviddeath.continent is not null
-- order by 2,3
;



-- USE CTE 

WITH PopVsVac(continent, location, dates, population, new_vaccinations, RollingPeopleVaccinated)
as 
(
SELECT *, coviddeath.continent, coviddeath.location , coviddeath.dates, coviddeath.population, covidvac.new_vaccinations
, sum(covidvac.new_vaccinations) OVER (partition by coviddeath.location order by coviddeath.location,coviddeath.dates )
as RollingPeopleVaccinated 
FROM PortfolioProjects.coviddeath 
join PortfolioProjects.covidvac
on coviddeath.location = covidvac.location
and coviddeath.dates = covidvac.dates
where coviddeath.continent is not null
-- order by 2,3
)
select * , ( RollingPeopleVaccinated / population)*100
FROM popvsvac

-- Temp TABLE

-- DROP Table if exists #PercentPopulationVaccinated

CREATE Table #PercentPopulationVaccinated
(
continent nvarchar(250) ,
location nvarchar(250)
dates datetime,
population numeric,
new_vaccinations numeric,
RollingPeopleVaccinated numeric
)

Insert into
Select * coviddeath.continent, coviddeath.location, coviddeath.dates, coviddeath.population, covidvac.new_vaccinations
, SUM(covidvac.new_vaccinations) OVER (Partition by coviddeath.location Order by coviddeath.location,
coviddeath.Dates) as RollingPeopleVaccinated
, (RollingPeopleVaccinated/population)*100
From coviddeaths 
Join covidvaccinations
  on coviddeath.location = covidvac.Location
  and coviddeath.date = covidvac.date
where coviddeath.continent is not null
--order by 2,3
)
Select *, (RollingPeopleVaccinated/Population)*100
From #PercentPopulationVaccinated

-- Creating View to store data for later visualizations

CREATE view 'PopvsVac' as 

Create View #PercentPopulationVaccinated as
Select * coviddeath.continent, coviddeath.location, coviddeath.dates, coviddeath.population, covidvac.new_vaccinations
, SUM(covidvac.new_vaccinations) OVER (Partition by coviddeath.location Order by coviddeath.location,
coviddeath.Date) as RollingPeopleVaccinated
, (RollingPeopleVaccinated/population)*100
From coviddeath 
Join covidvac
  on coviddeath.location = covidvac.location
  and coviddeath.dates = covidvac.dates
where coviddeath.continent is not null
