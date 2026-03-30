# Fun with PlyDB: Chat with Olympics 2026 data

Accompanying [blog post](https://www.plydb.com/blog/plydb-fun-olympics-2026/).

---

This repo is a fun experiment in conversational analytics using
[PlyDB](https://www.plydb.com/) and Olympics 2026 data.

_Marketing disclaimer out of the way_: PlyDB is an open-source, universal
database gateway that lets AI agents query across live data in place - no data
movement required. It bridges the gap between your AI and your data, from SQL
databases to flat files: Postgres, MySQL, CSV, Excel, Parquet, and
[more](https://www.plydb.com/docs/data-sources/).

---

## Data sources

| Description            | Source                                                                                               | Local path                                                                                                                                   | License                                                         |
| ---------------------- | ---------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------- |
| 2026 Olympics data     | https://www.kaggle.com/datasets/piterfm/milano-cortina-2026-olympic-winter-games                     | [/data/milano-cortina-2026-olympic-winter-games/](/data/milano-cortina-2026-olympic-winter-games/)                                           | [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/) |
| GDP data               | https://data.worldbank.org/indicator/NY.GDP.MKTP.CD                                                  | [/data/API_NY.GDP.MKTP.CD_DS2_en_csv_v2_32/](/data/API_NY.GDP.MKTP.CD_DS2_en_csv_v2_32/)                                                     | [CC-BY 4.0](https://creativecommons.org/licenses/by/4.0/)       |
| Country lattitude data | https://www.kaggle.com/datasets/paultimothymooney/latitude-and-longitude-for-every-country-and-state | [/data/world_country_and_usa_states_latitude_and_longitude_values.csv](/data/world_country_and_usa_states_latitude_and_longitude_values.csv) | [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |

## Examples

The analysis examples in this repo were written using
[PlyDB](https://www.plydb.com/) +
[Claude Code](https://claude.com/product/claude-code) (Sonnet 4.6 & Opus 4.6).

Claude's full writeups can be found in the [/analysis/](/analysis/) folder.

## What Are Your Best Odds of Winning an Olympic Medal?

Excerpt from Claude's [full writeup](/analysis/best-odds-of-a-medal.md):

---

**TL;DR:** Short Track Speed Skating is the single best bet — 34% of its
112-person field medalled at Milano-Cortina, gold spread across 4 countries, and
relay formats let one athlete stack medals across multiple events. Nordic
Combined is the worst: Norway won every gold event.

Dark horse pick for new entrants: Ski Mountaineering, which debuted in 2026 with
only 36 athletes and a competitive field still taking shape.

---

## GDP vs Medals: Milano-Cortina 2026 Winter Olympics

Excerpt from Claude's [full writeup](/analysis/gdp_vs_medals.md):

---

Norway's entire economy is smaller than what China "spent" per medal. Slovenia —
roughly the size of New Jersey — matched Poland's medal count on one-twelfth the
budget. And the best predictor of winter Olympic success turns out not to be
money — it's how far north you live, and what your grandparents did on weekends.

Three analyses. One conclusion: **you can't buy a ski jump in your DNA.**

---

## Milano-Cortina 2026 Winter Olympics — Analysis & Findings

Excerpt from Claude's [full writeup](/analysis/milano-cortina-2026-findings.md):

---

One man won 6 golds. One country sent 39 athletes and matched Norway
medal-for-medal. And Brazil — Brazil — won alpine skiing gold. Here's what the
data says about the most surprising Winter Olympics in years.

**TL;DR:** Norway dominated with 18 golds from just 80 athletes. The Netherlands
was the most efficient nation on Earth (51% medal rate, 39 athletes). France
obliterated biathlon, winning 6 of 11 events. The US swept both ice hockey
golds. Johannes Klaebo swept all 6 men's cross-country events. And Brazil pulled
off the biggest upset of the Games in the Men's Giant Slalom.

---

## Try it yourself

To chat with this 2026 Olympics data yourself:

1. Download this repository
2. Install PlyDB and configure it with your AI agent
   ([Quickstart Guide](https://www.plydb.com/docs/quickstart/))
3. Tell your AI to query with PlyDB using the pre-configured `plydb-config.json`
   file in this repository.
4. Have fun!

## Chat with other data

Did you know? PlyDB can connect your AI to boring data too!

Whether it's business data in a dusty Excel sheet or a complex DevOps log in S3,
AI can be surprisingly good at making sense of a mess. PlyDB acts as the bridge,
letting your AI query across Postgres, MySQL, CSV, Excel, Parquet, Google
Sheets, and more - locally or in the cloud.

Open source and free.
[Give PlyDB a spin!](https://www.plydb.com/docs/quickstart/)
