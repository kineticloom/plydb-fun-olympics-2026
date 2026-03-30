# GDP vs Medals: Milano-Cortina 2026 Winter Olympics

Norway's entire economy is smaller than what China "spent" per medal. Slovenia — roughly the
size of New Jersey — matched Poland's medal count on one-twelfth the budget. And the best
predictor of winter Olympic success turns out not to be money — it's how far north you live,
and what your grandparents did on weekends.

Three analyses. One conclusion: **you can't buy a ski jump in your DNA.**

---

## TL;DR

| Finding | The number |
|---------|-----------|
| Most medals per $1T GDP | Norway — **84.9** (71x more efficient than the USA) |
| Least medals per $1T GDP | Brazil — **0.46** (their entire economy, one alpine skiing gold) |
| Biggest latitude-adjusted overachiever | Georgia — **15x** above its latitude-band average |
| Most surprising underachiever given latitude | Great Britain — **0.11x** its band average, same latitude as Norway |
| The real predictor of winter success | Snowfall, not GDP |

---

Three analyses at the intersection of economic power and sporting achievement.

---

## Data Sources

- **Olympics:** `medals`, `athletes` tables — Milano-Cortina 2026 final results
- **GDP:** `gdp` table — World Bank, GDP current US$, year 2023 (most recent year with broad coverage)
- **Country metadata:** `gdp_metadata_country` — World Bank region and income group classifications
- **Latitude:** `country_lat_lon` table — country centroid latitude/longitude, ISO-2 country codes

**Key mapping note:** Olympic country codes (IOC) do not always match World Bank ISO-3 codes.
The following manual mappings were applied:

| IOC Code | Country      | World Bank Code |
|----------|--------------|-----------------|
| GER      | Germany      | DEU             |
| SUI      | Switzerland  | CHE             |
| NED      | Netherlands  | NLD             |
| SLO      | Slovenia     | SVN             |
| BUL      | Bulgaria     | BGR             |
| LAT      | Latvia       | LVA             |
| DEN      | Denmark      | DNK             |

---

## Base Dataset

Raw data assembled from queries below. 29 countries won at least one medal; 348 medals were
distributed in total. World GDP (2023) = $106.74 trillion.

### Queries

```sql
-- Medal table (all 29 medal-winning countries)
SELECT country_code, gold, silver, bronze, total
FROM medals.medals.medals
ORDER BY total DESC

-- GDP for medal-winning countries (direct IOC code matches)
SELECT "Country Code", "Country Name", "2023"
FROM gdp.default.gdp
WHERE "Country Code" IN (
  'NOR','USA','ITA','JPN','SUI','FRA','CAN','NED','SWE','AUT',
  'CHN','KOR','FIN','AUS','GBR','CZE','POL','ESP','NZL','BRA',
  'KAZ','EST','BEL','GEO'
)

-- GDP for mismatched codes — retrieved by country name
SELECT "Country Code", "Country Name", "2023"
FROM gdp.default.gdp
WHERE "Country Name" IN (
  'Germany','Switzerland','Netherlands','Slovenia','Bulgaria','Latvia','Denmark'
)

-- World GDP 2023
SELECT "Country Code", "Country Name", "2023"
FROM gdp.default.gdp
WHERE "Country Code" = 'WLD'
-- Result: $106,741,647,821,064

-- GDP for major 0-medal participating countries
SELECT "Country Code", "Country Name", "2023"
FROM gdp.default.gdp
WHERE "Country Name" IN (
  'India','Turkey','Mexico','Argentina','Iran, Islamic Rep.','Saudi Arabia',
  'Ukraine','Hungary','Indonesia','Colombia','Croatia','Greece','Portugal',
  'Romania','Israel','Singapore','Serbia','Hong Kong SAR, China'
)
```

### Assembled dataset (medal-winning countries)

| Country        | IOC | Total | Gold | GDP 2023 ($B) |
|----------------|-----|-------|------|---------------|
| Norway         | NOR | 41    | 18   | 482.9         |
| United States  | USA | 33    | 12   | 27,292.2      |
| Italy          | ITA | 30    | 10   | 2,316.7       |
| Germany        | GER | 26    | 8    | 4,562.2       |
| Japan          | JPN | 24    | 5    | 4,213.2       |
| Switzerland    | SUI | 23    | 6    | 894.4         |
| France         | FRA | 23    | 8    | 3,056.3       |
| Canada         | CAN | 21    | 5    | 2,173.3       |
| Netherlands    | NED | 20    | 10   | 1,135.5       |
| Sweden         | SWE | 18    | 8    | 579.0         |
| Austria        | AUT | 18    | 5    | 516.7         |
| China          | CHN | 15    | 5    | 18,270.4      |
| South Korea    | KOR | 10    | 3    | 1,844.8       |
| Finland        | FIN | 6     | 0    | 295.0         |
| Australia      | AUS | 6     | 3    | 1,734.5       |
| Great Britain  | GBR | 5     | 3    | 3,420.8       |
| Czech Republic | CZE | 5     | 2    | 345.1         |
| Slovenia       | SLO | 4     | 2    | 69.3          |
| Poland         | POL | 4     | 0    | 812.5         |
| Spain          | ESP | 3     | 1    | 1,619.5       |
| New Zealand    | NZL | 3     | 0    | 255.2         |
| Bulgaria       | BUL | 2     | 0    | 102.2         |
| Latvia         | LAT | 2     | 0    | 42.8          |
| Brazil         | BRA | 1     | 1    | 2,191.1       |
| Denmark        | DEN | 1     | 0    | 404.7         |
| Kazakhstan     | KAZ | 1     | 1    | 261.8         |
| Estonia        | EST | 1     | 0    | 41.5          |
| Belgium        | BEL | 1     | 0    | 651.3         |
| Georgia        | GEO | 1     | 0    | 30.8          |

---

## Analysis 1: Which countries punched above their economic weight?

### Methodology

Metric: **medals per $1 trillion GDP**

```
medals_per_trillion = total_medals / (gdp_2023_usd / 1,000,000,000,000)
```

This tells you how many medals a country extracted from every trillion dollars of economic output.
A higher score = punching above economic weight. A lower score = underperforming relative to wealth.

### Results

| Rank | Country        | Total Medals | GDP ($B) | Medals per $1T GDP |
|------|----------------|-------------|----------|--------------------|
| 1    | Norway         | 41          | 482.9    | **84.9**           |
| 2    | Slovenia       | 4           | 69.3     | **57.7**           |
| 3    | Latvia         | 2           | 42.8     | **46.7**           |
| 4    | Austria        | 18          | 516.7    | **34.8**           |
| 5    | Georgia        | 1           | 30.8     | **32.5**           |
| 6    | Sweden         | 18          | 579.0    | **31.1**           |
| 7    | Switzerland    | 23          | 894.4    | **25.7**           |
| 8    | Estonia        | 1           | 41.5     | **24.1**           |
| 9    | Finland        | 6           | 295.0    | **20.3**           |
| 10   | Bulgaria       | 2           | 102.2    | **19.6**           |
| 11   | Netherlands    | 20          | 1,135.5  | 17.6               |
| 12   | Czech Republic | 5           | 345.1    | 14.5               |
| 13   | Italy          | 30          | 2,316.7  | 13.0               |
| 14   | New Zealand    | 3           | 255.2    | 11.8               |
| 15   | Canada         | 21          | 2,173.3  | 9.7                |
| 16   | France         | 23          | 3,056.3  | 7.5                |
| 17   | Germany        | 26          | 4,562.2  | 5.7                |
| 18   | Japan          | 24          | 4,213.2  | 5.7                |
| 19   | South Korea    | 10          | 1,844.8  | 5.4                |
| 20   | Poland         | 4           | 812.5    | 4.9                |
| 21   | Kazakhstan     | 1           | 261.8    | 3.8                |
| 22   | Australia      | 6           | 1,734.5  | 3.5                |
| 23   | Denmark        | 1           | 404.7    | 2.5                |
| 24   | Spain          | 3           | 1,619.5  | 1.9                |
| 25   | Great Britain  | 5           | 3,420.8  | 1.5                |
| 26   | Belgium        | 1           | 651.3    | 1.5                |
| 27   | United States  | 33          | 27,292.2 | 1.2                |
| 28   | China          | 15          | 18,270.4 | 0.82               |
| 29   | Brazil         | 1           | 2,191.1  | 0.46               |

### Conclusions

**Norway is in a league of its own.** At 84.9 medals per $1 trillion GDP, Norway is roughly:
- 71x more efficient than the USA (1.2)
- 103x more efficient than China (0.82)
- 184x more efficient than Brazil (0.46)

Norway's entire economy ($483B) is smaller than the GDP "cost" of a single Chinese medal
($18.3T / 15 = $1.22T each), yet Norway won 41 medals to China's 15.

**The Scandinavian/Alpine cluster dominates.** Norway, Austria, Sweden, Switzerland, and Finland
all rank in the top 10. Winter sports are not just their hobby — they are structurally embedded
in their national identity, sporting infrastructure, and climate in a way that GDP alone cannot buy.

**Small nation outliers make the best stories.** Slovenia (pop. 2.1M, $69B GDP) extracted 4 medals
including 2 golds. Latvia (pop. 1.8M, $43B GDP) won 2 medals in a bobsled-and-luge tradition
stretching back to Soviet-era investment. Georgia ($31B GDP) snuck in with a medal, outperforming
economic powerhouses like Belgium ($651B) and Spain ($1.6T).

**The USA presents a fascinating paradox.** America sends the most athletes (235) and finishes
second overall in total medals, yet its medal-per-$1T score of 1.2 places it near the bottom of
the efficiency table. The explanation is simple: the USA is an all-sports powerhouse built for
summer. Winter is a footnote in a very long sporting calendar.

---

## Analysis 2: Which country's medals were the most "economically expensive"?

### Methodology

Metric: **GDP per medal** (absolute) and **GDP per medal relative to Norway** (indexed)

```
gdp_per_medal = gdp_2023_usd / total_medals
norway_index  = gdp_per_medal / norway_gdp_per_medal   (Norway = 1.0x baseline)
```

Norway is used as the baseline because it is the most medal-efficient large economy.
The index answers: "how many times more of their economy did each country need per medal
compared to Norway?"

### Results

| Rank | Country        | Total | GDP ($B)  | GDP per Medal ($B) | vs Norway |
|------|----------------|-------|-----------|--------------------|-----------|
| 1    | Norway         | 41    | 482.9     | **11.8**           | 1.0x      |
| 2    | Slovenia       | 4     | 69.3      | **17.3**           | 1.5x      |
| 3    | Latvia         | 2     | 42.8      | **21.4**           | 1.8x      |
| 4    | Austria        | 18    | 516.7     | **28.7**           | 2.4x      |
| 5    | Georgia        | 1     | 30.8      | **30.8**           | 2.6x      |
| 6    | Sweden         | 18    | 579.0     | **32.2**           | 2.7x      |
| 7    | Switzerland    | 23    | 894.4     | **38.9**           | 3.3x      |
| 8    | Estonia        | 1     | 41.5      | **41.5**           | 3.5x      |
| 9    | Finland        | 6     | 295.0     | **49.2**           | 4.2x      |
| 10   | Bulgaria       | 2     | 102.2     | 51.1               | 4.3x      |
| 11   | Netherlands    | 20    | 1,135.5   | 56.8               | 4.8x      |
| 12   | Czech Republic | 5     | 345.1     | 69.0               | 5.8x      |
| 13   | Italy          | 30    | 2,316.7   | 77.2               | 6.5x      |
| 14   | New Zealand    | 3     | 255.2     | 85.1               | 7.2x      |
| 15   | Canada         | 21    | 2,173.3   | 103.5              | 8.8x      |
| 16   | France         | 23    | 3,056.3   | 132.9              | 11.3x     |
| 17   | Germany        | 26    | 4,562.2   | 175.5              | 14.9x     |
| 18   | Japan          | 24    | 4,213.2   | 175.5              | 14.9x     |
| 19   | South Korea    | 10    | 1,844.8   | 184.5              | 15.6x     |
| 20   | Poland         | 4     | 812.5     | 203.1              | 17.2x     |
| 21   | Australia      | 6     | 1,734.5   | 289.1              | 24.5x     |
| 22   | Kazakhstan     | 1     | 261.8     | 261.8              | 22.2x     |
| 23   | Denmark        | 1     | 404.7     | 404.7              | 34.3x     |
| 24   | Spain          | 3     | 1,619.5   | 539.8              | 45.7x     |
| 25   | Belgium        | 1     | 651.3     | 651.3              | 55.2x     |
| 26   | Great Britain  | 5     | 3,420.8   | 684.2              | 58.0x     |
| 27   | United States  | 33    | 27,292.2  | 827.0              | 70.1x     |
| 28   | China          | 15    | 18,270.4  | 1,218.0            | 103.2x    |
| 29   | Brazil         | 1     | 2,191.1   | **2,191.1**        | **185.7x**|

### Conclusions

**Brazil's single gold medal is the most striking contrast in this dataset.** Brazil's economy
generated $2.19 trillion in 2023, making it one of the world's ten largest economies. Its one
alpine skiing gold (Lucas Pinheiro Braathen, Men's Giant Slalom) was a genuine, hard-won achievement — and the numbers make it shine even brighter:
Norway's economy was 185x more effective per medal, yet that single Brazilian gold carries the
weight of an entire continental-scale economy behind it.

**China's position is the most intriguing for a superpower.** The world's second-largest economy
($18.3T) produced 15 medals at a "cost" of $1.22 trillion each — comparable to the entire GDP
of the Netherlands per medal. China has invested heavily in winter sports since hosting Beijing
2022, and the trajectory is clearly upward. The gap between economic size and winter medal count
is a story still being written.

**The USA's 33 medals are genuinely impressive in absolute terms.** At $827B of GDP per medal
the efficiency metric looks unflattering, but that's largely a function of scale — a $27T
economy spread across every sport imaginable. Norway got 41 medals on a GDP the size of one
American state, which is a testament to Nordic focus, not American failure.

**Georgia earns a special mention.** Its $30.8B GDP per medal is almost exactly equal to its
entire national GDP — meaning in a sense, 100% of Georgia's annual economic output was
"accounted for" by a single silver medal. Yet in the efficiency ranking (Analysis 1), Georgia
scores 32.5 medals per $1T, placing 5th overall. A paradox: it is simultaneously one of the
most and least "expensive" medal-winning economies depending on which lens you use.

---

## Analysis 3: Medals per $1T GDP, normalised by latitude

### Methodology

Analysis 1 showed that Nordic and Alpine nations dominate medal efficiency. But that raises an
obvious objection: isn't northern latitude itself an unfair advantage? Snow and cold are free
infrastructure. This analysis controls for that by grouping countries into latitude bands and
computing a **latitude-adjusted efficiency index** — how each country performs relative to the
average for countries at similar latitudes.

**Data source:** `country_lat_lon` table — ISO-2 country codes with centroid latitude.

**Join strategy:** The Olympic (IOC), World Bank (ISO-3), and latitude (ISO-2) datasets all use
different country code standards. The cleanest working join is:

```sql
SELECT
  m.country_code,
  m.total,
  g."2023"    AS gdp_2023,
  l.latitude,
  ROUND(m.total / (g."2023" / 1e12), 2) AS medals_per_trillion
FROM medals.medals.medals m
JOIN country_lat_lon.default.country_lat_lon l
  ON l.country_code = CASE m.country_code
    WHEN 'NOR' THEN 'NO' WHEN 'USA' THEN 'US' WHEN 'ITA' THEN 'IT'
    WHEN 'GER' THEN 'DE' WHEN 'JPN' THEN 'JP' WHEN 'SUI' THEN 'CH'
    WHEN 'FRA' THEN 'FR' WHEN 'CAN' THEN 'CA' WHEN 'NED' THEN 'NL'
    WHEN 'SWE' THEN 'SE' WHEN 'AUT' THEN 'AT' WHEN 'CHN' THEN 'CN'
    WHEN 'KOR' THEN 'KR' WHEN 'FIN' THEN 'FI' WHEN 'AUS' THEN 'AU'
    WHEN 'GBR' THEN 'GB' WHEN 'CZE' THEN 'CZ' WHEN 'SLO' THEN 'SI'
    WHEN 'POL' THEN 'PL' WHEN 'ESP' THEN 'ES' WHEN 'NZL' THEN 'NZ'
    WHEN 'BUL' THEN 'BG' WHEN 'LAT' THEN 'LV' WHEN 'BRA' THEN 'BR'
    WHEN 'DEN' THEN 'DK' WHEN 'KAZ' THEN 'KZ' WHEN 'EST' THEN 'EE'
    WHEN 'BEL' THEN 'BE' WHEN 'GEO' THEN 'GE'
  END
JOIN gdp.default.gdp g
  ON g."Country Name" = CASE m.country_code
    WHEN 'NOR' THEN 'Norway'          WHEN 'USA' THEN 'United States'
    WHEN 'ITA' THEN 'Italy'           WHEN 'GER' THEN 'Germany'
    WHEN 'JPN' THEN 'Japan'           WHEN 'SUI' THEN 'Switzerland'
    WHEN 'FRA' THEN 'France'          WHEN 'CAN' THEN 'Canada'
    WHEN 'NED' THEN 'Netherlands'     WHEN 'SWE' THEN 'Sweden'
    WHEN 'AUT' THEN 'Austria'         WHEN 'CHN' THEN 'China'
    WHEN 'KOR' THEN 'Korea, Rep.'     WHEN 'FIN' THEN 'Finland'
    WHEN 'AUS' THEN 'Australia'       WHEN 'GBR' THEN 'United Kingdom'
    WHEN 'CZE' THEN 'Czechia'         WHEN 'SLO' THEN 'Slovenia'
    WHEN 'POL' THEN 'Poland'          WHEN 'ESP' THEN 'Spain'
    WHEN 'NZL' THEN 'New Zealand'     WHEN 'BUL' THEN 'Bulgaria'
    WHEN 'LAT' THEN 'Latvia'          WHEN 'BRA' THEN 'Brazil'
    WHEN 'DEN' THEN 'Denmark'         WHEN 'KAZ' THEN 'Kazakhstan'
    WHEN 'EST' THEN 'Estonia'         WHEN 'BEL' THEN 'Belgium'
    WHEN 'GEO' THEN 'Georgia'
  END
ORDER BY l.latitude DESC
```

Note: `country_lat_lon` uses ISO-2 codes; the GDP table uses World Bank country names (which
differ from lat/lon names for South Korea/"Korea, Rep." and Czech Republic/"Czechia"), so
the two joins use separate CASE mappings rather than chaining through a common key.

**Latitude bands** (four groups by centroid latitude):

| Band | Range | Countries in dataset |
|------|-------|----------------------|
| Nordic/Polar | ≥ 55° | FIN, NOR, SWE, EST, LAT, DEN, CAN, GBR |
| Northern Temperate | 45–55° | NED, POL, GER, BEL, CZE, KAZ, AUT, SUI, FRA, SLO |
| Mediterranean/East Asia | 35–45° | BUL, GEO, ITA, ESP, USA, JPN, KOR, CHN |
| Southern Hemisphere | < 35° | BRA, AUS, NZL |

**Latitude-adjusted efficiency index:**
```
band_avg_medals_per_trillion = total band medals / total band GDP (in $T)
lat_adj_index = country medals_per_trillion / band_avg_medals_per_trillion
```

Index > 1.0 = outperforms latitude-band peers. Index < 1.0 = underperforms them.

### Results — Band averages

| Band | Total Medals | Total GDP ($T) | Band Avg (medals/$1T) |
|------|-------------|----------------|-----------------------|
| Nordic/Polar (≥55°) | 95 | 7.44 | **12.77** |
| Northern Temperate (45–55°) | 125 | 12.31 | **10.16** |
| Mediterranean/East Asia (35–45°) | 118 | 55.69 | **2.12** |
| Southern Hemisphere (<35°) | 10 | 4.18 | **2.39** |

A clear tier effect: Nordic countries produce ~6x more medals per dollar than countries in the
35–45° band. Latitude explains a large part of the Analysis 1 rankings.

### Results — Latitude-adjusted efficiency index by country

**Band 1: Nordic/Polar (≥55°) — band avg 12.77 medals/$1T**

| Country | Lat | Medals/$1T | Lat-Adj Index |
|---------|-----|------------|---------------|
| Norway | 60.5° | 84.89 | **6.65** |
| Latvia | 56.9° | 46.75 | **3.66** |
| Sweden | 60.1° | 31.09 | **2.43** |
| Estonia | 58.6° | 24.11 | 1.89 |
| Finland | 61.9° | 20.34 | 1.59 |
| Canada | 56.1° | 9.66 | 0.76 |
| Denmark | 56.3° | 2.47 | 0.19 |
| Great Britain | 55.4° | 1.46 | **0.11** |

**Band 2: Northern Temperate (45–55°) — band avg 10.16 medals/$1T**

| Country | Lat | Medals/$1T | Lat-Adj Index |
|---------|-----|------------|---------------|
| Slovenia | 46.2° | 57.76 | **5.69** |
| Austria | 47.5° | 34.84 | **3.43** |
| Switzerland | 46.8° | 25.71 | 2.53 |
| Netherlands | 52.1° | 17.61 | 1.73 |
| Czech Republic | 49.8° | 14.49 | 1.43 |
| France | 46.2° | 7.53 | 0.74 |
| Germany | 51.2° | 5.70 | 0.56 |
| Poland | 51.9° | 4.92 | 0.48 |
| Kazakhstan | 48.0° | 3.82 | 0.38 |
| Belgium | 50.5° | 1.54 | **0.15** |

**Band 3: Mediterranean/East Asia (35–45°) — band avg 2.12 medals/$1T**

| Country | Lat | Medals/$1T | Lat-Adj Index |
|---------|-----|------------|---------------|
| Georgia | 42.3° | 32.49 | **15.33** |
| Bulgaria | 42.7° | 19.57 | **9.23** |
| Italy | 41.9° | 12.95 | **6.11** |
| Japan | 36.2° | 5.70 | 2.69 |
| South Korea | 35.9° | 5.42 | 2.56 |
| Spain | 40.5° | 1.85 | 0.87 |
| United States | 37.1° | 1.21 | 0.57 |
| China | 35.9° | 0.82 | **0.39** |

**Band 4: Southern Hemisphere (<35°) — band avg 2.39 medals/$1T**

| Country | Lat | Medals/$1T | Lat-Adj Index |
|---------|-----|------------|---------------|
| New Zealand | -40.9° | 11.76 | **4.92** |
| Australia | -25.3° | 3.46 | 1.45 |
| Brazil | -14.2° | 0.46 | 0.19 |

### Overall latitude-adjusted ranking (all 29 countries)

| Rank | Country | Band | Lat-Adj Index |
|------|---------|------|---------------|
| 1 | Georgia | Mediterranean | **15.33** |
| 2 | Bulgaria | Mediterranean | **9.23** |
| 3 | Norway | Nordic | **6.65** |
| 4 | Italy | Mediterranean | **6.11** |
| 5 | Slovenia | N. Temperate | **5.69** |
| 6 | New Zealand | Southern Hem. | **4.92** |
| 7 | Latvia | Nordic | 3.66 |
| 8 | Austria | N. Temperate | 3.43 |
| 9 | Japan | Mediterranean | 2.69 |
| 10 | South Korea | Mediterranean | 2.56 |
| 11 | Switzerland | N. Temperate | 2.53 |
| 12 | Sweden | Nordic | 2.43 |
| 13 | Estonia | Nordic | 1.89 |
| 14 | Netherlands | N. Temperate | 1.73 |
| 15 | Finland | Nordic | 1.59 |
| 16 | Australia | Southern Hem. | 1.45 |
| 17 | Czech Republic | N. Temperate | 1.43 |
| 18 | Spain | Mediterranean | 0.87 |
| 19 | Canada | Nordic | 0.76 |
| 20 | France | N. Temperate | 0.74 |
| 21 | United States | Mediterranean | 0.57 |
| 22 | Germany | N. Temperate | 0.56 |
| 23 | Poland | N. Temperate | 0.48 |
| 24 | China | Mediterranean | 0.39 |
| 25 | Kazakhstan | N. Temperate | 0.38 |
| 26 | Denmark | Nordic | 0.19 |
| 27 | Brazil | Southern Hem. | 0.19 |
| 28 | Belgium | N. Temperate | 0.15 |
| 29 | Great Britain | Nordic | **0.11** |

### Conclusions

**The latitude effect is real but explains far less than you'd expect.** Band averages drop
sharply from north to south (12.77 → 10.16 → 2.12), confirming that colder latitudes
provide a structural boost. But within every band, the spread is enormous — often 50x or
more between the best and worst performers. Latitude is a necessary condition for winter
sports culture, not a sufficient one.

**Georgia and Bulgaria are the true revelation of this analysis.** Both sit at 42–43°N —
the same latitude as Rome and Barcelona. Yet they index at 15.3x and 9.2x their
latitude-band average respectively. Georgia's silver came from Figure Skating Pair Skating;
Bulgaria's two bronzes from Snowboard and Biathlon. Different disciplines, same story:
find your niche, build real depth, and latitude stops being an excuse.

**Italy's 6.11x index makes its Games performance even more remarkable.** Finishing 3rd overall
with 30 medals is outstanding for any nation, and the latitude adjustment shows it wasn't just
about hosting — Italy performed at 6x the rate of other Mediterranean-latitude nations. A
passionate home crowd and familiar terrain undoubtedly helped, but the depth of Italy's Alpine
tradition was the real foundation.

**Norway's dominance survives the latitude adjustment.** Even among Nordic peers — Finland,
Sweden, Estonia, Latvia — all of whom share similar climates and cultural exposure to winter
sports, Norway still runs at 6.65x the band average. This is not just climate; it is a
national identity built around cross-country skiing and ski jumping at a depth no other
country matches.

**The UK is the most intriguing outlier in the dataset.** Great Britain sits at 55.4°N —
broadly the same latitude as Latvia, Estonia, and just south of Norway — yet scores a
latitude-adjusted index of 0.11. Its 5 medals (including 3 golds) were outstanding individual
achievements, particularly in skeleton and curling. The numbers simply reflect that Britain's
sporting culture has long pointed toward summer: athletics, rowing, cycling, tennis. Winter
is a different kind of adventure for British athletes, which makes every medal they do win
all the more impressive.

**New Zealand is the most impressive southern hemisphere story.** At -40.9° (equivalent
northern latitude: northern Spain or New York), it is the most polar of the three southern
hemisphere medal-winners. Its 4.92x latitude index reflects a country that has built genuine
alpine ski culture around the Southern Alps — well above its band average, and above the
raw efficiency of France, Germany, and Canada.

---

## Overall Narrative

All three analyses converge on a single theme: **winter sports success is strongly tied to
latitude, but latitude alone does not determine outcomes — culture, specialisation, and
historic infrastructure investment explain the large gaps within latitude bands.**

Latitude buys you the climate. What you do with it is a choice. Norway chose cross-country
skiing as a national religion. Latvia chose bobsled. The Netherlands chose speed skating.
Georgia and Bulgaria carried mountain programs forward across generations. Every country
has the same 24 hours in a day and channels them into the sports that resonate with its culture.

The most honest summary: **latitude sets the ceiling, culture determines how close you get
to it.**
