# Indiana District 9 US House Election Data

These datasets are for exploring voting patterns in Indiana's 9th Congressional District in US House general elections from 2016 through 2024.

The best file to start with is:

`data/in09_house_precinct_summary.csv`

Each row is one precinct in one election year.

## Main Files

### `data/in09_house_precinct_summary.csv`

One row per precinct per election year.

- `year`: election year.
- `state`: always `Indiana`.
- `district`: always `9`.
- `office`: always `US House`.
- `county_name`: county containing the precinct.
- `county_fips`: county identifier used by the Census.
- `jurisdiction`: election jurisdiction name when available.
- `precinct`: precinct name from the election return file.
- `dem_candidate`: Democratic candidate.
- `rep_candidate`: Republican candidate.
- `lib_candidate`: Libertarian candidate, if there was one.
- `dem_votes`: votes for the Democratic candidate.
- `rep_votes`: votes for the Republican candidate.
- `lib_votes`: votes for the Libertarian candidate.
- `other_votes`: other candidate votes kept in this cleaned file. This is currently `0` because write-ins and ballot-accounting rows were removed.
- `total_votes`: Democratic + Republican + Libertarian votes.
- `dem_share`: Democratic share of `total_votes`.
- `rep_share`: Republican share of `total_votes`.
- `lib_share`: Libertarian share of `total_votes`.
- `two_party_dem_share`: Democratic share among only Democratic and Republican votes.
- `rep_margin`: Republican votes minus Democratic votes.
- `rep_margin_share`: `rep_margin` divided by Democratic + Republican votes.
- `winner_party`: party with more votes in that precinct.

Example:

```r
library(tidyverse)

precincts <- read_csv("data/in09_house_precinct_summary.csv")

precincts |>
  group_by(year) |>
  summarize(
    total_votes = sum(total_votes),
    mean_two_party_dem_share = mean(two_party_dem_share, na.rm = TRUE),
    median_two_party_dem_share = median(two_party_dem_share, na.rm = TRUE)
  )
```

### `data/in09_house_precinct_candidate.csv`

One row per candidate, precinct, and election year. Use this if you want to make plots grouped by candidate or party.

- `candidate`: candidate name.
- `party`: candidate party.
- `party_abbr`: `D`, `R`, or `L`.
- `votes`: candidate votes in that precinct.
- `precinct_total_votes`: total D/R/L votes in that precinct.
- `candidate_vote_share`: candidate votes divided by `precinct_total_votes`.
- `modes_used`: voting mode rows used from the source file.
- `used_total_row`: `yes` if the source had a `TOTAL` row for that precinct/candidate.

### `data/in09_house_county_summary.csv`

One row per county per election year. This is useful for comparing counties instead of individual precincts.

### `data/in09_house_district_summary.csv`

One row per election year. This is useful for overall election trends.

## Turnout And Registration

### `data/in09_house_precinct_summary_with_county_turnout.csv`

This is the precinct summary plus county-level turnout and registration variables.

- `county_registered_voters`: registered voters in the county.
- `county_voters_voting`: voters who voted in the county.
- `county_turnout_pct_reported`: turnout percentage printed in the Indiana Secretary of State report.
- `county_turnout_rate`: `county_voters_voting / county_registered_voters`.
- `county_election_day_votes`: county election-day votes.
- `county_absentee_votes`: county absentee votes. Depending on the year, this may include mail and in-person absentee/early voting.
- `county_absentee_share_reported`: absentee percentage printed in the state report.
- `county_absentee_share`: `county_absentee_votes / county_voters_voting`.
- `county_source`: source report URL.
- `districts_in_county`: congressional districts represented in that county in the MIT US House file for that year.
- `county_split_in_year`: `yes` if the county appears in more than one congressional district that year.

Important: turnout variables are county-level. Every precinct in the same county and year has the same county turnout values.

## Demographics

### `data/in09_county_demographics_acs2024.csv`

One row per county represented in the 2024 District 9 election data. These are whole-county demographics, not precinct demographics and not district-part-only demographics.

- `total_population`: county population.
- `median_age`: median age.
- `white_alone`, `black_alone`, `american_indian_alaska_native`, `asian_alone`, `native_hawaiian_pacific_islander`, `other_race_alone`, `two_or_more_races`: race counts.
- `hispanic_or_latino`: Hispanic or Latino residents of any race.
- `white_share`, `black_share`, `asian_share`, `hispanic_share`: selected demographic shares.
- `population_25_plus`: residents age 25 and older.
- `high_school_or_higher`: residents age 25+ with at least a high school credential.
- `high_school_or_higher_share`: high school or higher share among residents age 25+.
- `bachelors_or_higher`: residents age 25+ with a bachelor's degree or higher.
- `bachelors_or_higher_share`: bachelor's degree or higher share among residents age 25+.
- `median_household_income`: median household income in dollars.
- `labor_force`: people in the labor force.
- `unemployed`: unemployed people in the labor force.
- `unemployment_rate`: `unemployed / labor_force`.
- `poverty_universe`: people for whom poverty status is determined.
- `poverty_count`: people below the poverty line.
- `poverty_rate`: `poverty_count / poverty_universe`.
- `occupied_housing_units`: occupied housing units.
- `owner_occupied_housing_units`: owner-occupied housing units.
- `owner_occupied_share`: `owner_occupied_housing_units / occupied_housing_units`.
- `source`: Census Reporter profile URL.

### `data/in09_2024_county_summary_with_turnout_and_demographics.csv`

One row per 2024 District 9 county. This combines election results, turnout/registration, county split status, and 2024 county demographics.

## A Careful Interpretation Note

County demographics describe whole counties. If only part of a county is in District 9, the county demographics may not perfectly describe District 9 voters in that county.

In the 2024 data, Bartholomew County is split between Districts 6 and 9.

Across all years in this dataset, split counties include:

- 2016: Crawford, Morgan, Scott
- 2018: Crawford, Morgan, Scott
- 2020: Crawford
- 2022: Bartholomew
- 2024: Bartholomew

