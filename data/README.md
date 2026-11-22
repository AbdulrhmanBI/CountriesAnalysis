# Data

Raw source files used by the report. Both are plain CSV and are kept exactly as received.

Point the `DataFolder` parameter in Power BI Desktop to the folder that contains them  
(**Home → Transform data → Manage parameters → `DataFolder`**).

> The file name must stay `Country Data Comparison.csv` (with spaces) – that is the name the Power Query step looks for.

> **Source / licence:** the column names follow the CIA World Factbook country-profile fields and the figures look like an older Factbook edition. State the exact source, vintage and licence here before making the repository public.

## Files

| File | Delimiter | Encoding | Line endings |
|------|-----------|----------|--------------|
| `Country Data Comparison.csv` | `;` | ASCII (read as Windows-1252) | LF |
| `Country_Continent.csv` | `,` | UTF-8 with BOM | CRLF |

---

## `Country Data Comparison.csv`

### Layout
* **Line 1** – header: `Country` + 44 indicator columns (45 columns in total).
* **Line 2** – a *type row*: `String` for `Country`, `double` for every indicator.
* **Lines 3 onward** – 262 country / territory rows, plus one aggregate row, `#European Union`.
* Values are plain numbers: `.` as the decimal separator, no thousands separators, no currency symbols or units. **Missing values are empty cells.**
* Units are in the column name where the source gives one (e.g. `kWh`, `bbl/day`, `cu m`, `%`). Monetary columns (`GDP`, `Exports`, `Imports`, `Debt - external`, …) carry no unit in the name – the report formats them as dollars (`$`).
* The type row and the `#European Union` row are not countries; Power Query removes both (see below).

### Columns

"Non-empty" is the number of the 262 countries that have a value.

| # | Column | Declared type | Non-empty |
|---|--------|---------------|-----------|
| 1 | `Country` | String | 262 |
| 2 | `Area(sq km)` | double | 262 |
| 3 | `Birth rate(births/1000 population)` | double | 224 |
| 4 | `Current account balance` | double | 149 |
| 5 | `Death rate(deaths/1000 population)` | double | 224 |
| 6 | `Debt - external` | double | 201 |
| 7 | `Electricity - consumption(kWh)` | double | 214 |
| 8 | `Electricity - production(kWh)` | double | 212 |
| 9 | `Exports` | double | 223 |
| 10 | `GDP` | double | 229 |
| 11 | `GDP - per capita` | double | 229 |
| 12 | `GDP - real growth rate(%)` | double | 211 |
| 13 | `HIV/AIDS - adult prevalence rate(%)` | double | 168 |
| 14 | `HIV/AIDS - deaths` | double | 148 |
| 15 | `HIV/AIDS - people living with HIV/AIDS` | double | 164 |
| 16 | `Highways(km)` | double | 229 |
| 17 | `Imports` | double | 223 |
| 18 | `Industrial production growth rate(%)` | double | 163 |
| 19 | `Infant mortality rate(deaths/1000 live births)` | double | 224 |
| 20 | `Inflation rate (consumer prices)(%)` | double | 221 |
| 21 | `Internet hosts` | double | 190 |
| 22 | `Internet users` | double | 215 |
| 23 | `Investment (gross fixed)(% of GDP)` | double | 145 |
| 24 | `Labor force` | double | 213 |
| 25 | `Life expectancy at birth(years)` | double | 224 |
| 26 | `Military expenditures - dollar figure` | double | 170 |
| 27 | `Military expenditures - percent of GDP(%)` | double | 165 |
| 28 | `Natural gas - consumption(cu m)` | double | 107 |
| 29 | `Natural gas - exports(cu m)` | double | 107 |
| 30 | `Natural gas - imports(cu m)` | double | 107 |
| 31 | `Natural gas - production(cu m)` | double | 106 |
| 32 | `Natural gas - proved reserves(cu m)` | double | 96 |
| 33 | `Oil - consumption(bbl/day)` | double | 211 |
| 34 | `Oil - exports(bbl/day)` | double | 57 |
| 35 | `Oil - imports(bbl/day)` | double | 45 |
| 36 | `Oil - production(bbl/day)` | double | 211 |
| 37 | `Oil - proved reserves(bbl)` | double | 96 |
| 38 | `Population` | double | 237 |
| 39 | `Public debt(% of GDP)` | double | 108 |
| 40 | `Railways(km)` | double | 133 |
| 41 | `Reserves of foreign exchange & gold` | double | 147 |
| 42 | `Telephones - main lines in use` | double | 231 |
| 43 | `Telephones - mobile cellular` | double | 227 |
| 44 | `Total fertility rate(children born/woman)` | double | 224 |
| 45 | `Unemployment rate(%)` | double | 191 |

---

## `Country_Continent.csv`

Lookup table with a header and one row per country (262 rows).

| Column | Description |
|--------|-------------|
| `Country` | Country name – matches `Country Data Comparison.csv` exactly (all 262 names found in both files). |
| `Continent` | One of: Africa, Asia, Europe, North America, Oceania, South America, Antarctica. |

---

## What Power BI does with the raw data

This section describes the report's transformations, not the files themselves.

* **Cleaning** – promotes the header, drops rows whose `Country` starts with `String` or `#`, and sets data types (whole number, decimal, currency).
* **Renames** – `Area(sq km)` → `Area sq km`, `GDP - per capita` → `GDP per Capita`, `GDP - real growth rate(%)` → `GDP Growth %`, `Inflation rate (consumer prices)(%)` → `Inflation %`, `Unemployment rate(%)` → `Unemployment %`, `Life expectancy at birth(years)` → `Life Expectancy`, `Public debt(% of GDP)` → `Public Debt % GDP`.
* **Calculated columns** – `Trade Balance` = `Exports` − `Imports`; `Population Density` = `Population` ÷ `Area sq km` (null when area is 0).
* **Split into model tables** – `Population`, `Economy` and `Energy` each take a subset of the columns (plus `Country`); `CountryData Unpivot` is the same data in long format joined to `Country_Continent.csv`. See the main [README](../README.md#-data-model).
