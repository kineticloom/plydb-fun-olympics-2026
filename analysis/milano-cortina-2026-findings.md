# Milano-Cortina 2026 Winter Olympics — Analysis & Findings

> **One man won 6 golds. One country sent 39 athletes and matched Norway medal-for-medal. And Brazil — Brazil — won alpine skiing gold.** Here's what the data says about the most surprising Winter Olympics in years.

**TL;DR:** Norway dominated with 18 golds from just 80 athletes. The Netherlands was the most efficient nation on Earth (51% medal rate, 39 athletes). France obliterated biathlon, winning 6 of 11 events. The US swept both ice hockey golds. Johannes Klaebo swept all 6 men's cross-country events. And Brazil pulled off the biggest upset of the Games in the Men's Giant Slalom.

---

**Data sources:** `athletes`, `coaches`, `medallists`, `medals`, `schedules`, `teams`, `venues`
**Analysis date:** 2026-03-05

---

## Overview

The Milano-Cortina 2026 Winter Olympics ran from **February 7–22, 2026** — 16 competition days across 15 venues spread across northern Italy. The Games featured:

- **2,916 athletes** from **93 countries** (1,553 male / 1,363 female, ~53/47 split)
- **16 disciplines**, **116 medal events** (709 individual medal entries in the medallists table, since team sports count each athlete separately)
- **29 countries** won at least one medal — just 31% of participating nations
- **64 countries** went home without a single medal

---

## Full Medal Table

| Rank | Country | Gold | Silver | Bronze | Total |
|------|---------|------|--------|--------|-------|
| 1 | Norway | 18 | 12 | 11 | 41 |
| 2 | United States | 12 | 12 | 9 | 33 |
| 3 | Netherlands | 10 | 7 | 3 | 20 |
| 4 | Italy | 10 | 6 | 14 | 30 |
| 5 | Germany | 8 | 10 | 8 | 26 |
| 6 | France | 8 | 9 | 6 | 23 |
| 7 | Sweden | 8 | 6 | 4 | 18 |
| 8 | Switzerland | 6 | 9 | 8 | 23 |
| 9 | Austria | 5 | 8 | 5 | 18 |
| 10 | Japan | 5 | 7 | 12 | 24 |
| 11 | Canada | 5 | 7 | 9 | 21 |
| 12 | China | 5 | 4 | 6 | 15 |
| 13 | Korea | 3 | 4 | 3 | 10 |
| 14 | Australia | 3 | 2 | 1 | 6 |
| 15 | Great Britain | 3 | 1 | 1 | 5 |
| 16 | Czechia | 2 | 2 | 1 | 5 |
| 17 | Slovenia | 2 | 1 | 1 | 4 |
| 18 | Spain | 1 | 0 | 2 | 3 |
| 19 | Brazil | 1 | 0 | 0 | 1 |
| 19 | Kazakhstan | 1 | 0 | 0 | 1 |
| 21 | Poland | 0 | 3 | 1 | 4 |
| 22 | New Zealand | 0 | 2 | 1 | 3 |
| 23 | Finland | 0 | 1 | 5 | 6 |
| 24 | Latvia | 0 | 1 | 1 | 2 |
| 25 | Denmark | 0 | 1 | 0 | 1 |
| 25 | Estonia | 0 | 1 | 0 | 1 |
| 25 | Georgia | 0 | 1 | 0 | 1 |
| 28 | Bulgaria | 0 | 0 | 2 | 2 |
| 29 | Belgium | 0 | 0 | 1 | 1 |

---

## Queries Used

All queries used `plydb query -config plydb-config.json`. Tables referenced by fully-qualified names (e.g. `medals.medals.medals`).

```sql
-- Athlete demographics
SELECT COUNT(*) as total_athletes,
       COUNT(CASE WHEN gender='M' THEN 1 END) as male,
       COUNT(CASE WHEN gender='F' THEN 1 END) as female
FROM athletes.athletes.athletes;
-- 2916 total, 1553 male, 1363 female

-- Full medal table
SELECT country, gold, silver, bronze, total, rank
FROM medals.medals.medals
ORDER BY rank;

-- Gold medals by discipline (counts individual athletes for team events)
SELECT discipline, COUNT(*) as gold_medals
FROM medallists.medallists.medallists
WHERE medal='GOLD'
GROUP BY discipline ORDER BY gold_medals DESC;

-- Top individual medal winners
SELECT name, country, COUNT(*) as total_medals,
       SUM(CASE WHEN medal='GOLD' THEN 1 ELSE 0 END) as gold
FROM medallists.medallists.medallists
GROUP BY name, country
ORDER BY total_medals DESC, gold DESC LIMIT 15;

-- Gold medals per 100 athletes (efficiency)
SELECT m.country, m.gold, a.athletes,
       ROUND(m.gold * 1.0 / a.athletes * 100, 2) as gold_per_100_athletes
FROM medals.medals.medals m
JOIN (SELECT country_code, COUNT(*) as athletes
      FROM athletes.athletes.athletes GROUP BY country_code) a
  ON m.country_code = a.country_code
WHERE m.gold > 0
ORDER BY gold_per_100_athletes DESC LIMIT 15;

-- Medal rate per athlete
SELECT m.country, (m.gold + m.silver + m.bronze) as medals, a.athletes,
       ROUND((m.gold + m.silver + m.bronze) * 1.0 / a.athletes * 100, 1) as medal_rate
FROM medals.medals.medals m
JOIN (SELECT country_code, COUNT(*) as athletes
      FROM athletes.athletes.athletes GROUP BY country_code) a
  ON m.country_code = a.country_code
ORDER BY medal_rate DESC LIMIT 12;
```

---

## Key Findings & Insights

### 1. Norway Is Untouchable in Nordic Sports

Norway won **18 gold medals** — the most of any nation — with only **80 athletes** (the 13th-largest delegation). Their gold-per-athlete rate of **22.5 per 100** is second only to the Netherlands.

Their dominance was concentrated in the snow endurance disciplines:

**Cross-Country Skiing** (Norway won 7 of 12 gold events):
- Men's 10km Skiathlon, 10km Free, 50km Classic, Sprint, Team Sprint, Relay — Norway swept all 6 men's events
- Women's relay also went to Norway; the remaining 5 women's events went to Sweden

**Nordic Combined** (Norway won all 3 events — a complete sweep)

**Biathlon** (Norway won 3 of 11 golds): Men's 15km Mass Start, Men's 20km Individual, Women's 7.5km Sprint

**Ski Jumping**: Women's LH and NH Individual both went to Norway (Anna Odine Stroem, 3 medals total including 2 golds)

---

### 2. The Netherlands: Most Efficient Gold Nation

The Netherlands sent just **39 athletes** but won **10 gold medals** — a rate of **25.6 golds per 100 athletes**, the best of any nation. Their total medal haul of 20 places them 3rd on the gold table.

The secret: total domination of the ice oval disciplines.

**Short Track Speed Skating** (Netherlands won 5 of 9 gold events):
- Men's 1000m, 1500m, 5000m Relay; Women's 1000m, 500m
- Jens van 't Wout: 4 medals, 3 gold

**Speed Skating** (Netherlands won 5 of 14 gold events):
- Women's 1000m, 1500m, 500m, Mass Start; Men's Mass Start

---

### 3. France Obliterated Biathlon

France won **6 of 11 biathlon gold events** — a level of dominance that is extraordinary for a single nation in a multi-event discipline. Their biathlon haul alone would rank them 8th in the overall gold table.

Gold event wins:
- Men's 10km Sprint, Men's 4x7.5km Relay, Mixed Relay
- Women's 12.5km Mass Start, Women's 15km Individual, Women's 4x6km Relay
- (Norway took Men's 15km Mass Start, Men's 20km Individual, and Women's 7.5km Sprint; Sweden took Men's 12.5km Pursuit; Italy took Women's 10km Pursuit)

Star athletes:
- **Quentin Fillon Maillet**: 4 medals, 3 gold
- **Julia Simon**: 4 medals, 3 gold
- **Lou Jeanmonnot**: 4 medals, 2 gold
- **Oceane Michelon**: 3 medals, 2 gold
- **Eric Perrot**: 3 medals, 2 gold

---

### 4. USA Swept Ice Hockey (Both Genders)

The United States won **both** the men's and women's ice hockey gold medals — the only nation to achieve this double. This accounts for 48 of the 709 individual medal entries in the dataset (25-man men's squad + 23-woman women's squad, all receiving gold).

Ice Hockey results:
- Men: 🥇 USA, 🥈 Canada, 🥉 Finland
- Women: 🥇 USA, 🥈 Canada, 🥉 Switzerland

Canada finished silver in both tournaments — the second-best result from a single country across both ice hockey events.

---

### 5. Johannes Hoesflot Klaebo: Best Individual Performance

**Johannes Hoesflot Klaebo** (Norway, Cross-Country Skiing) won **6 gold medals** — the most of any individual at these Games. He swept all 6 men's cross-country events:
- 10km Skiathlon, 10km Free, Sprint Classic, Team Sprint, 4x7.5km Relay, 50km Mass Start Classic

This makes him one of the most decorated single-Games performers in Winter Olympics history.

Other top individual performers:
| Athlete | Country | Discipline | Total Medals | Gold |
|---------|---------|-----------|-------------|------|
| KLAEBO Johannes Hoesflot | Norway | Cross-Country Skiing | 6 | 6 |
| LAEGREID Sturla Holm | Norway | Biathlon | 5 | 0 |
| van 'T WOUT Jens | Netherlands | Short Track | 4 | 3 |
| FILLON MAILLET Quentin | France | Biathlon | 4 | 3 |
| SIMON Julia | France | Biathlon | 4 | 3 |
| JEANMONNOT Lou | France | Biathlon | 4 | 2 |
| ANDERSSON Ebba | Sweden | Cross-Country Skiing | 4 | 1 |
| SARAULT Courtney | Canada | Short Track | 4 | 0 |
| OFTEBRO Jens Luraas | Norway | Nordic Combined | 3 | 3 |
| von ALLMEN Franjo | Switzerland | Alpine Skiing | 3 | 3 |

Note: Laegreid's 5 medals with 0 gold is a notable stat — he was the consummate silver/bronze finisher.

---

### 6. Switzerland Dominated Alpine Skiing

Switzerland won **4 of 10 alpine skiing gold events** — more than any other nation in the marquee mountain discipline:
- Men's Downhill, Slalom, Super-G, Team Combined

Franjo von Allmen was the individual star with 3 golds (Downhill, Super-G, Team Combined). Marco Odermatt added 3 medals. This was Switzerland's strongest alpine Games in recent memory.

The US won Women's Downhill and Slalom. Italy won Women's Giant Slalom and Super-G on home snow. Austria won Women's Team Combined.

**Brazil's shock gold**: Brazil won the Men's Giant Slalom — a stunning result for a country not traditionally associated with alpine skiing, and the only Latin American gold medal of the Games.

---

### 7. Germany Swept Sliding Sports

Germany had a near-total stranglehold on the Cortina Sliding Centre:

**Luge**: Germany won 3 of 5 luge events (Men's Singles, Women's Singles, Team Relay). Italy took both Doubles events (Men's and Women's).
**Bobsleigh**: Germany won 3 of 4 bobsleigh events (2-man, 2-woman, 4-man). USA won Women's Monobob.

Combined, Germany's 6 sliding golds account for 6 of their 8 total gold medals.

---

### 8. Italy as Host Nation — Bronze-Heavy but Strong

Italy (the host) finished **4th on the gold table** with 10 golds, but their medal distribution is interesting: **14 bronze medals** is the most of any nation, giving them 30 total (3rd behind Norway's 41 and USA's 33).

Italy's gold sources:
- Short Track Speed Skating: 6 individual gold medallists (Mixed Team Relay win)
- Speed Skating: 5 individual gold medallists (Team Pursuit, Women's 3000m & 5000m)
- Luge: Men's Doubles & Women's Doubles (4 individual gold medallists)
- Alpine Skiing: Women's Giant Slalom & Super-G (2 event wins, multiple athletes)
- Biathlon: Women's 10km Pursuit
- Freestyle Skiing: Men's Ski Cross

Italy sent the 3rd-largest delegation (196 athletes) and converted them at a 15.3% medal rate.

---

### 9. Japan Dominated Snowboard

Japan won **4 of 11 snowboard gold events**:
- Men's Big Air, Men's Halfpipe, Women's Big Air, Women's Slopestyle

Japan's 5 golds, 7 silvers, and 12 bronzes (24 total) reflects broad depth — they accumulated many medals across figure skating, snowboard, and speed skating. Their 12 bronze medals is second only to Italy's 14.

---

### 10. Great Britain's Skeleton Surprise

Great Britain won **2 of 3 skeleton gold events** (Men and Mixed Team), a discipline in which they have a historically strong programme. Combined with a snowboard gold (Mixed Team Snowboard Cross), they claimed 5 medals total — a strong return for a 55-athlete delegation (5.45 golds per 100 athletes, 6th best rate).

---

### 11. A Cross-Country Skiing Divide: Norway vs Sweden

The 12 cross-country skiing gold events were shared exclusively between Norway and Sweden:
- **Norway**: All 6 men's events + Women's Relay = **7 golds**
- **Sweden**: Women's Skiathlon, Free, 50km Classic, Sprint, Team Sprint = **5 golds** (plus Sweden's Ebba Andersson with 4 medals, 1 gold)

No other country won a single cross-country skiing gold. Frida Karlsson (Sweden) and Ebba Andersson were standout performers on the women's side.

---

### 12. Medal Efficiency: Small Delegations Punching Above Their Weight

Countries with the best total medal rate per athlete sent:

| Country | Medals | Athletes | Medal Rate |
|---------|--------|----------|------------|
| Netherlands | 20 | 39 | 51.3% |
| Norway | 41 | 80 | 51.2% |
| Japan | 24 | 121 | 19.8% |
| New Zealand | 3 | 17 | 17.6% |
| Sweden | 18 | 110 | 16.4% |
| Austria | 18 | 117 | 15.4% |
| Italy | 30 | 196 | 15.3% |

The Netherlands and Norway are in a completely different class at ~51% — roughly 1 in 2 athletes came home with a medal.

---

### 13. Disciplines Won by a Single Nation

Several disciplines were monopolised by one country for gold:

| Discipline | Dominant Country | Gold Events Won | Total Events |
|-----------|-----------------|-----------------|--------------|
| Nordic Combined | Norway | 3 | 3 (complete sweep) |
| Bobsleigh | Germany | 3 | 4 |
| Biathlon | France | 6 | 11 |
| Ice Hockey | United States | 2 | 2 (complete sweep) |

Norway's Nordic Combined sweep is particularly striking — they won every single event in the discipline.

---

### 14. The Games at a Glance: Scale and Spread

**Venues**: 15 venues across the region. The Cortina Sliding Centre (luge/bobsleigh/skeleton) hosted the most sessions (294), followed by the Milano Ice Skating Arena (162) and the Cortina Curling Stadium (multiple sheets, 400+ sessions combined).

**Competition schedule**: 16 days, Feb 7–22. Up to 9 gold events decided on the busiest days (Feb 10, 12, 15, 18, 21).

**Coaching**: Switzerland, Canada, and Sweden each had the largest coaching staffs (5 coaches each in the dataset).

**Flag bearers**: Italy, as host, had 6 flag bearers across the opening and closing ceremonies — the most of any country.

---

## Summary

Milano-Cortina 2026 produced several compelling stories:

- **Norway** was the undisputed dominant nation, sweeping Nordic Combined and winning nearly every cross-country skiing event contested.
- **The Netherlands** was the most efficient team — 39 athletes, 10 golds, powered by ice oval expertise.
- **France** mounted a biathlon blitz, winning 6 of 11 gold events and producing a roster of multi-medal athletes.
- **The US** made history by winning both ice hockey tournaments, boosting them to 2nd on the gold table.
- **Klaebo** (Norway) delivered the single greatest individual performance of the Games with 6 golds in cross-country skiing.
- The host nation **Italy** delivered 10 golds and 30 total medals, with particular strength in short track, speed skating, and luge.
- **Brazil's** Men's Giant Slalom gold was the Games' biggest upset, the only Latin American gold of the Winter Olympics.
