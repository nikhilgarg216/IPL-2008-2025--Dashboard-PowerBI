# 🏏 IPL Analysis Dashboard (2008–2025)

Analyzing 18 seasons of Indian Premier League data to uncover batting, bowling, team, and match insights using **Power BI Desktop**, **Power Query**, **DAX**, **Data Modeling**, and **GitHub**.

![Home Page](screenshots/Home.png)

---

## 📌 Table of Contents
- [Overview](#overview)
- [Business Problem](#business-problem)
- [Dataset](#dataset)
- [Tools & Technologies](#tools--technologies)
- [Project Structure](#project-structure)
- [Data Cleaning & Preparation](#data-cleaning--preparation)
- [Data Modeling](#data-modeling)
- [DAX Measures](#dax-measures)
- [Research Questions & Key Findings](#research-questions--key-findings)
- [Dashboard](#dashboard)
- [How to Run This Project](#how-to-run-this-project)
- [Final Recommendations](#final-recommendations)
- [Author & Contact](#author--contact)

---

## Overview

This project explores **18 seasons of IPL cricket data (2008–2025)** covering 1,000+ matches, 10 teams, and 1 billion fans worldwide. An end-to-end Power BI dashboard was built with 3 interactive pages — **Home**, **Insight**, and **Statistics** — providing deep insights into team performance, player statistics, and match trends.

---

## Business Problem

Cricket analytics is a growing field used by franchises, broadcasters, and fans to understand performance trends. This project aims to:

- Identify top-performing batters and bowlers across all 18 seasons
- Determine team dominance and all-time win patterns
- Analyze toss impact on match outcomes
- Track season-wise scoring trends and boundary counts
- Compare player strike rates, averages, and economy rates
- Surface insights for fantasy cricket and strategic decisions

---

## Dataset

| File | Description |
|------|-------------|
| `ball_by_ball_data.csv` | Every ball bowled across all IPL matches (2008–2025) |
| `ipl_matches_data.csv` | Match-level data — toss, result, venue, season |
| `players-data-updated.csv` | Player profiles — batting style, bowling style |
| `teams_data.csv` | Team names, short codes, logo URLs |

- **Seasons covered:** 2008 – 2025 (18 seasons)
- **Total matches:** 1,000+
- **Total teams:** 10 active + 6 historical

---

## Tools & Technologies

| Tool | Usage |
|------|-------|
| **Power BI Desktop** | Dashboard creation and visualization |
| **Power Query** | Data ingestion, cleaning, and transformation |
| **DAX** | Custom measures and calculated columns |
| **Data Modeling** | Table relationships and star schema design |
| **GitHub** | Version control and project sharing |

---

## Project Structure

```
ipl-analysis-dashboard/
│
├── README.md                        # Project documentation
├── .gitignore
│
├── data/                            # Raw CSV data files
│   ├── ball_by_ball_data.csv
│   ├── ipl_matches_data.csv
│   ├── players-data-updated.csv
│   └── teams_data.csv
│
├── dashboard/                       # Power BI project file
│   └── ipl_analysis_dashboard.pbix
│
└── screenshots/                     # Dashboard page screenshots
    ├── Home.png
    ├── Insights.png
    └── Statistics.png
```

---

## Data Cleaning & Preparation

Done inside **Power Query Editor** in Power BI:

- Removed super over deliveries → filtered `is_super_over = FALSE`
- Handled null values in `player_out`, `fielders_involved`, `wicket_kind`
- Replaced null numeric columns with `0`
- Changed data types — `match_date` to Date, boolean columns to True/False
- Renamed columns for readability across all 4 tables
- Merged `players-data-updated` into ball-by-ball on `batter` and `bowler`
- Loaded `teams_data` for logo display using image URLs in visuals

---

## Data Modeling

Relationships built in Power BI Model View:

```
ipl_matches_data
    └── match_id  →  ball_by_ball_data [match_id]    (1 to Many)
    └── season    →  season slicer                    (filter context)

players_data
    └── player_name  →  ball_by_ball_data [batter]   (1 to Many)
    └── player_name  →  ball_by_ball_data [bowler]   (1 to Many)

teams_data
    └── team_name  →  ipl_matches_data [team1/team2] (lookup)
```

- Schema type: **Star Schema**
- Cross-filter direction: **Single** (default), **Both** where needed for slicers

---

## DAX Measures

Key measures created for the dashboard:

```dax
-- Total Runs
Total Runs = SUM(ball_by_ball_data[batter_runs])

-- Total Wickets
Total Wickets =
    COUNTROWS(FILTER(ball_by_ball_data, ball_by_ball_data[is_wicket] = TRUE()))

-- Strike Rate
Strike Rate =
    DIVIDE(SUM(ball_by_ball_data[batter_runs]), COUNT(ball_by_ball_data[ball_number])) * 100

-- Economy Rate
Economy Rate =
    DIVIDE(SUM(ball_by_ball_data[total_runs]), DIVIDE(COUNT(ball_by_ball_data[ball_number]), 6))

-- Batting Average
Batting Average =
    DIVIDE(
        SUM(ball_by_ball_data[batter_runs]),
        CALCULATE(COUNTROWS(ball_by_ball_data), ball_by_ball_data[is_wicket] = TRUE())
    )

-- Player of Match Count
Count of POM =
    COUNTROWS(FILTER(ipl_matches_data,
        ipl_matches_data[player_of_match] = SELECTEDVALUE(ipl_matches_data[player_of_match])))

-- Top Scorer (Card visual)
Top Scorer =
    FIRSTNONBLANK(
        TOPN(1, VALUES(ball_by_ball_data[batter]), CALCULATE(SUM(ball_by_ball_data[batter_runs])), 1), 1)

-- Toss Win & Match Win
Toss Win & Match Win =
    COUNTROWS(FILTER(ipl_matches_data,
        ipl_matches_data[toss_winner] = ipl_matches_data[match_winner]))
```

---

## Research Questions & Key Findings

**1. Which team has won the most matches all-time?**
→ **Mumbai Indians** lead with 153 wins, followed by CSK (142) and KKR (135)

**2. Who is the all-time top run-scorer?**
→ **V Kohli** with 8,671 runs across 18 seasons

**3. Who has taken the most wickets?**
→ **YS Chahal** leads with 229 wickets all-time

**4. Who hits the most sixes?**
→ **CH Gayle** with 359 sixes — the most in IPL history

**5. What is the highest team score ever?**
→ **287** — Sunrisers Hyderabad, 2024

**6. Which city hosts the most IPL matches?**
→ **Mumbai** is the most frequent host city

**7. Who wins the most Player of the Match awards?**
→ **AB de Villiers** leads, followed by CH Gayle and RG Sharma

**8. Does toss decision impact match result?**
→ Teams that win the toss and choose to field have a slight edge in wins

**9. What were the 2025 season highlights?**
→ Champion: **Royal Challengers Bangalore** | Runner-up: **Punjab Kings**
→ Total 6s: 1,296 | Total 4s: 2,251 | Matches played: 74

---

## Dashboard

The Power BI dashboard has **3 interactive pages**:

### 🏠 Home Page
![Home](screenshots/Home.png)
- IPL branding with season selector (2008–2025)
- 2025 Champion display — Royal Challengers Bangalore
- Navigation buttons to Insight and Statistics pages

---

### 📊 Insight Page
![Insight](screenshots/Insights.png)
- **KPI Cards:** Total 6s · Total 4s · Total Matches · Teams · Centuries · Half Centuries · Venues
- Orange Cap & Purple Cap winner with player photos
- Season-wise Points Table with team logos
- Champion and Runner-up cards
- Official Links panel (IPL, Twitter, Facebook, ESPN Cricinfo, CricBuzz)

---

### 📈 Statistics Page
![Statistics](screenshots/Statistics.png)
- **KPI Cards:** Top Scorer · Most Wickets · Most Sixes · Highest Score · Most Matches City
- Batting Avg vs Strike Rate bubble scatter chart (top players)
- All-Time Team Wins horizontal bar chart
- Most Player of the Match Awards bar chart (Top 15)
- Season filter slicer

---

## How to Run This Project

**1. Clone the repository:**
```bash
git clone https://github.com/nikhil3596/ipl-analysis-dashboard.git
```

**2. Open the dashboard:**
- Install [Power BI Desktop](https://powerbi.microsoft.com/desktop/) (free)
- Open `dashboard/ipl_analysis_dashboard.pbix`
- If data source error appears → go to **Transform Data → Data Source Settings** and repoint to your local `/data/` folder

**3. Explore the dashboard:**
- Use the season dropdown to filter by year (2008–2025)
- Click **Insight** or **Statistics** buttons to navigate pages
- Use slicers to filter by team, season, or player

---

## Final Recommendations

- **Franchise Strategy:** Mumbai Indians and CSK show the most consistent win patterns — their toss decisions and batting order strategies are worth studying
- **Fantasy Cricket:** Use the Batting Avg vs Strike Rate scatter chart to pick consistent high-impact players
- **Bowler Picks:** Spinners dominate economy rate stats — prioritize them in middle overs on slower pitches
- **Venue Planning:** Mumbai and Chennai historically produce closer contests — adjust team composition accordingly
- **Rising Teams:** Gujarat Titans and Lucknow Super Giants show rapid improvement since joining in 2022

---

## Author & Contact

**Nikhil Garg**
Frontend Developer | Data Analyst

📧 Email: [nikhilgarg216@gmail.com](mailto:nikhilgarg216@gmail.com)
🔗 LinkedIn: [linkedin.com/in/nikhil3596](https://www.linkedin.com/in/nikhil3596/)
🔗 GitHub: [github.com/nikhil3596](https://github.com/nikhil3596)

---

> ⭐ If you found this project useful, feel free to star the repository!
