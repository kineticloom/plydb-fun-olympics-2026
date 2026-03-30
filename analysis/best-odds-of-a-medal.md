# What Are Your Best Odds of Winning an Olympic Medal?
## A Data-Driven Analysis of Milano-Cortina 2026

*One-in-five athletes at the Winter Olympics wins a medal — but that average hides a sport where
a third of competitors podium and another where Norway takes home all the gold. Which discipline
should a rational, ambitious athlete choose if the goal is maximising their shot at standing on
that podium in four years?*

**TL;DR:** Short Track Speed Skating is the single best bet — 34% of its 112-person field
medalled at Milano-Cortina, gold spread across 4 countries, and relay formats let one athlete
stack medals across multiple events. Nordic Combined is the worst: Norway won every gold event.
Dark horse pick for new entrants: Ski Mountaineering, which debuted in 2026 with only 36
athletes and a competitive field still taking shape.

---

**Data source:** Milano-Cortina 2026 Winter Olympics dataset (athletes, medallists, medals tables)
**Analysis date:** 2026-03-05

---

## The Question

If you could choose any discipline to dedicate your life to, which one gives a lay person the
best statistical chance of standing on an Olympic podium? This analysis works through the data
systematically to answer that question.

One caveat upfront: all of this assumes you've already qualified for the Olympics — itself an
enormous hurdle. The analysis focuses on conditional odds: *given you made it to the Games,
what are your chances of winning a medal?*

---

## Thought Process

There are three lenses to evaluate this question:

1. **Medal rate** — what fraction of athletes in each discipline actually win a medal?
   A sport where 1-in-3 competitors medals is structurally very different from one where 1-in-11 does.

2. **Country concentration** — even if the overall medal rate is high, does that medal go to
   athletes from just one or two countries? If Norway wins all the cross-country skiing events,
   your odds as a non-Norwegian are near zero regardless of the headline rate.

3. **Field size** — smaller fields mean fewer athletes to beat, and also mean qualification
   is more attainable in the first place.

The interaction of these three factors — not any one in isolation — produces the true opportunity.

---

## Queries & Raw Data

### 1. Overall baseline: how many athletes medal at all?

```sql
SELECT COUNT(DISTINCT name) as unique_medalists FROM medallists.default.medallists
-- 534 unique athletes won at least one medal

SELECT COUNT(*) as total_athletes FROM athletes.default.athletes
-- 2,916 athletes attended the Games
```

**Base rate: 534 / 2,916 = 18.3%** of athletes at the Games won at least one medal.
But this average conceals enormous variation between disciplines.

---

### 2. Athlete counts per discipline

```sql
SELECT 'Alpine Skiing' as discipline, COUNT(*) as athletes
FROM athletes.default.athletes WHERE events LIKE '%Alpine Skiing%'
UNION ALL SELECT 'Biathlon', COUNT(*) FROM athletes.default.athletes WHERE events LIKE '%Biathlon%'
-- ... (repeated for all 16 disciplines)
ORDER BY athletes DESC
```

| Discipline | Athletes Registered |
|---|---|
| Ice Hockey | 530 |
| Alpine Skiing | 306 |
| Cross-Country Skiing | 296 |
| Freestyle Skiing | 278 |
| Speed Skating | 275 |
| Snowboard | 235 |
| Biathlon | 210 |
| Bobsleigh | 201 |
| Figure Skating | 147 |
| Short Track Speed Skating | 112 |
| Curling | 112 |
| Luge | 105 |
| Ski Jumping | 101 |
| Skeleton | 50 |
| Ski Mountaineering | 36 |
| Nordic Combined | 36 |

---

### 3. Unique medalists per discipline

```sql
SELECT discipline,
       COUNT(DISTINCT name) as unique_medalists,
       COUNT(DISTINCT event_name) as num_events
FROM medallists.default.medallists
GROUP BY discipline ORDER BY unique_medalists DESC
```

| Discipline | Unique Medalists | Events |
|---|---|---|
| Ice Hockey | 144 | 2 |
| Freestyle Skiing | 40 | 15 |
| Short Track Speed Skating | 38 | 9 |
| Biathlon | 36 | 11 |
| Speed Skating | 36 | 14 |
| Curling | 36 | 3 |
| Cross-Country Skiing | 33 | 12 |
| Snowboard | 32 | 11 |
| Figure Skating | 31 | 5 |
| Alpine Skiing | 28 | 10 |
| Bobsleigh | 21 | 4 |
| Luge | 20 | 5 |
| Ski Jumping | 19 | 6 |
| Skeleton | 7 | 3 |
| Ski Mountaineering | 7 | 3 |
| Nordic Combined | 6 | 3 |

---

### 4. Medal rate by discipline (unique medalists / athletes)

Combining the two tables above:

| Discipline | Athletes | Unique Medalists | Medal Rate |
|---|---|---|---|
| Short Track Speed Skating | 112 | 38 | **33.9%** |
| Curling | 112 | 36 | **32.1%** |
| Ice Hockey | 530 | 144 | **27.2%** |
| Figure Skating | 147 | 31 | **21.1%** |
| Ski Mountaineering | 36 | 7 | **19.4%** |
| Luge | 105 | 20 | **19.0%** |
| Ski Jumping | 101 | 19 | **18.8%** |
| Nordic Combined | 36 | 6 | **16.7%** |
| Biathlon | 210 | 36 | **17.1%** |
| Freestyle Skiing | 278 | 40 | **14.4%** |
| Skeleton | 50 | 7 | **14.0%** |
| Snowboard | 235 | 32 | **13.6%** |
| Speed Skating | 275 | 36 | **13.1%** |
| Cross-Country Skiing | 296 | 33 | **11.1%** |
| Bobsleigh | 201 | 21 | **10.4%** |
| Alpine Skiing | 306 | 28 | **9.2%** |

---

### 5. Country concentration — how many countries win gold?

```sql
SELECT discipline,
       COUNT(DISTINCT country_code) as gold_winning_countries,
       COUNT(DISTINCT event_name) as total_gold_events
FROM medallists.default.medallists WHERE medal='GOLD'
GROUP BY discipline ORDER BY gold_winning_countries ASC
```

| Discipline | Gold-Winning Countries | Gold Events |
|---|---|---|
| Nordic Combined | 1 | 3 |
| Ice Hockey | 1 (per gender) | 2 |
| Curling | 2 | 3 |
| Cross-Country Skiing | 2 | 12 |
| Bobsleigh | 2 | 4 |
| Luge | 2 | 5 |
| Skeleton | 2 | 3 |
| Ski Mountaineering | 3 | 3 |
| Biathlon | 4 | 11 |
| Short Track Speed Skating | 4 | 9 |
| Ski Jumping | 4 | 6 |
| Figure Skating | 4 | 5 |
| Alpine Skiing | 5 | 10 |
| Snowboard | 7 | 11 |
| Speed Skating | 7 | 14 |
| Freestyle Skiing | 8 | 15 |

The disciplines where only 1–2 countries won gold across all events (Nordic Combined,
Cross-Country Skiing, Curling, Bobsleigh, Luge, Skeleton) represent monopolies that are
nearly impossible to break into regardless of talent, unless you happen to be from that country.

---

### 6. Gold athletes per event (team event scale)

```sql
SELECT discipline, event_name, COUNT(*) as gold_athletes
FROM medallists.default.medallists WHERE medal='GOLD'
GROUP BY discipline, event_name ORDER BY gold_athletes DESC
```

Top team events by gold recipients:

| Discipline | Event | Gold Athletes |
|---|---|---|
| Ice Hockey | Men | 25 |
| Ice Hockey | Women | 23 |
| Figure Skating | Team Event | 7 |
| Short Track / Luge | Relay / Team Relay | 6 each |
| Short Track | Men's 5000m / Women's 3000m Relay | 5 each |
| Curling | Men / Women | 5 each |

81 of 116 events awarded gold to exactly 1 athlete (individual events). The remaining 35 events
are team/relay events where gold goes to 2–25 athletes per nation.

---

### 7. Distribution of medals per athlete

```sql
SELECT CASE WHEN total_medals=1 THEN '1 medal'
            WHEN total_medals=2 THEN '2 medals'
            WHEN total_medals=3 THEN '3 medals'
            WHEN total_medals>=4 THEN '4+ medals' END as bucket,
       COUNT(*) as athletes
FROM (SELECT name, COUNT(*) as total_medals FROM medallists.default.medallists GROUP BY name)
GROUP BY bucket ORDER BY bucket
```

| Medals Won | Athletes |
|---|---|
| 1 medal | 404 (76%) |
| 2 medals | 96 (18%) |
| 3 medals | 26 (5%) |
| 4+ medals | 8 (1%) |

Three-quarters of medalists won exactly one medal. The extreme outliers (Klaebo 6, Laegreid 5)
are a tiny tail of specialists competing in many events within their discipline.

---

## Analysis

### The medal-rate leaders need a big asterisk

**Short Track (33.9%) and Curling (32.1%)** top the medal rate table, but for different
structural reasons:

- **Short Track** has a high rate because relay events award 5–6 gold medals at once (Mixed Team,
  Men's 5000m, Women's 3000m). The same athlete can medal in relays plus their individual event,
  so 38 unique athletes medal across 9 events. But gold concentration is relatively low (4
  countries), making it genuinely competitive.

- **Curling** awards 5 medals per gold event (entire team), so each of the 3 events produces
  15 medallists. The catch: only 2 countries won gold, making it highly concentrated at the top.

**Ice Hockey (27.2%)** looks great on paper but is the ultimate team-size illusion. Yes, 144
of 530 athletes medal — but a single gold event awards 23–25 medals to one country's entire
roster. Only 3 countries ever reach the podium per gender tournament, and the top teams
(USA, Canada) dominate.

### The "concentration trap"

Several disciplines have attractive medal rates but are essentially locked to specific nations:

| Discipline | Medal Rate | Reality Check |
|---|---|---|
| Nordic Combined | 16.7% | Norway won all 3 gold events. Non-Norwegians have essentially 0 gold chance. |
| Cross-Country Skiing | 11.1% | Only Norway and Sweden shared all 12 golds. Two countries for 296 athletes. |
| Luge | 19.0% | Germany + Italy shared all 5 golds. Only 2 gold countries. |
| Bobsleigh | 10.4% | Germany + USA shared all 4 golds. |

These disciplines are traps for the lay person: even if you make the Olympics, your chance of
a gold is near-zero unless you're from the right country.

### The genuinely open disciplines

Measured by gold-winning countries relative to events, these disciplines spread medals most broadly:

| Discipline | Medal Rate | Gold Countries | Assessment |
|---|---|---|---|
| **Freestyle Skiing** | 14.4% | 8 | Most open field; 15 events; most accessible for diverse nationalities |
| **Snowboard** | 13.6% | 7 | Similar openness; growing globally; 11 events |
| **Speed Skating** | 13.1% | 7 | Historically Dutch/Korean/Japanese; spreading |
| **Biathlon** | 17.1% | 4 | France + Norway dominant, but 4 countries win gold across 11 events |
| **Short Track** | 33.9% | 4 | Highest medal rate + 4 gold countries = best combination |

### The small-field sleepers

Two disciplines deserve special attention for their tiny fields:

- **Skeleton**: Only 50 athletes at the entire Games. 7 medalists = 14%. The field is small
  because it's a niche sport requiring access to purpose-built ice tracks. Hard to break in, but
  once you're competitive, the pool is tiny.

- **Ski Mountaineering**: Debuted at Milano-Cortina 2026. Only 36 athletes. 7 medalists = 19.4%.
  As a new Olympic discipline, the competitive landscape is still forming — an early mover
  advantage that won't last.

---

## Recommendations

### If you could choose a discipline from scratch, here's the ranking:

**Tier 1 — Best realistic odds**

1. **Short Track Speed Skating** — Highest medal rate among sports with genuine country diversity
   (4 gold-winning countries). Team relays allow a single athlete to accumulate medals across
   individual + relay events. 33.9% of the 112-person field medalled.

2. **Biathlon** — A genuinely open discipline (4 gold countries, 11 events). The 17.1% medal
   rate is solid, and France's 2026 dominance was exceptional rather than historical lock-in.
   The shooting element means peak aerobic fitness alone isn't sufficient — a different pathway
   than pure endurance.

**Tier 2 — Good odds with caveats**

3. **Freestyle Skiing** — Most open field by country diversity (8 gold countries, 15 events).
   Medal rate (14.4%) is below average, but the breadth of nations winning means your nationality
   is less of a barrier. Growing globally.

4. **Snowboard** — Similar profile to freestyle: 7 gold countries, 11 events, 13.6% medal rate.
   Youth-oriented, accessible in many countries with mountains, growing.

5. **Curling** — High medal rate (32%) and a team format where making the squad means everyone
   medals. But only 2 countries won gold, so unless you're from a dominant curling nation, your
   gold ceiling is limited. Silver/bronze is more attainable given broader country spread.

**Tier 3 — Small field, narrow opening**

6. **Skeleton** — 50-person field is the smallest realistic entry point. Hard to get ice-track
   access, but the pool is genuinely tiny.

7. **Ski Mountaineering** — Brand new Olympic discipline, 36 athletes, still forming. Get in
   early.

**Avoid (for a lay person)**

- **Nordic Combined**: Norway won all 3 golds. Enough said.
- **Cross-Country Skiing**: Norway + Sweden share all 12 golds across 296 athletes. Brutal.
- **Alpine Skiing**: Lowest medal rate of any discipline (9.2%). 306 athletes, 28 medalists.
  Expensive to pursue, long training path, highly competitive.
- **Ice Hockey**: 530 athletes competing for 6 medals per gender. Even within medal contention,
  you need to make a top-3 national team.

---

## Summary

| | Best | Worst |
|---|---|---|
| **Highest medal rate** | Short Track (33.9%), Curling (32.1%) | Alpine Skiing (9.2%), Bobsleigh (10.4%) |
| **Most open to any country** | Freestyle Skiing (8 gold nations), Snowboard (7) | Nordic Combined (1), Cross-Country (2) |
| **Smallest field to beat** | Ski Mountaineering (36), Skeleton (50) | Ice Hockey (530), Alpine Skiing (306) |
| **Best combination overall** | **Short Track Speed Skating** | **Nordic Combined** |

The single clearest recommendation: **Short Track Speed Skating**. Highest medal rate in the
dataset (34%), a relay-heavy format that multiplies individual medal opportunities, genuinely
spread across 4 gold-winning countries, and a field of just 112 athletes. If you're going to
dedicate a sporting career to maximising your Olympic podium probability, the ice oval is where
the math points.

The dark horse pick for the ambitious newcomer: **Ski Mountaineering**. Debuted in 2026. Only
36 athletes. The competitive field is still forming, and early specialisation in a new Olympic
discipline has historically paid off before the sport matures and attracts deeper competition.
