# EdTech_Course_Analytics
# 📊 EdTech Course Analytics — Power BI Dashboard

An end-to-end **Power BI** project that cleans, transforms, and analyses a dataset of online recorded-lecture courses (sourced from Kaggle) to help an EdTech startup identify growth opportunities, in-demand skills, and viewer engagement patterns — with a strong **category-wise** analytical lens.

---

## 📌 Problem Statement

> *You are a data analyst working with an EdTech startup that wants to grow its offerings in recorded lectures. The company has collected data from various EdTech websites but needs expert analysis to uncover valuable insights and inform strategic decision-making — with category being the primary lens of analysis.*

---

## 🎯 Business Objectives

The dashboard was built to answer the following client questions:

1. **Course type distribution across categories** — and count of courses by category & sub-category, to help decide which course types to launch where.
2. **Average views** by category, sub-category, and language.
3. **Most in-demand skills** per category (word-cloud based).
4. **Language distribution** across all courses.
5. **Language preference by viewer engagement**, restricted to the **top 5 categories** (by average views).
6. **Subtitle availability vs. number of views** — does subtitle count drive engagement?
7. **Top 3 instructors per category/sub-category** based on rating (static visual for outreach).
8. **Course duration vs. views**, with duration standardized to hours (1 month = 60 hrs, flexible schedule = capped at 200 hrs).
9. **Skill variety vs. viewership** per category/sub-category.

---

## 🗂️ Dataset

- **Source:** Kaggle (online course listings — ~45 raw columns)
- **Key fields used:** `Course ID`, `Title`, `Category`, `Sub-category`, `Course Type`, `Language`, `Subtitle Languages`, `Skills`, `Instructors`, `Rating`, `Number of Viewers`, `Duration`, `Site`

---

## 🛠️ Tools & Tech Stack

- **Power BI Desktop** — data modeling, DAX, visuals
- **Power Query Editor (M Language)** — data cleaning & transformation
- **Power BI Service** — publishing & web sharing
- **DAX** — `RANKX`, conditional measures, aggregations

---

## 🧹 Data Cleaning & Transformation

Performed in Power Query using the **Data Quality view** (View tab) to assess completeness before cleaning:

- Removed error rows, duplicate rows, and fully blank rows
- Dropped columns that were 100% empty (`Program Type`, `Courses`, `Level`, `URL`)
- Removed blank values from the `Category` column (core dimension — cannot tolerate nulls)
- Extracted numeric rating values from text (e.g., `"4.9 stars"` → `4.9`) using **Text Before Delimiter**, converted to Decimal
- **Skills column:** removed extra spaces so multi-word skills (e.g., `Data Analysis`, `Data Cleaning`) aren't miscounted as a single common word (`Data`) by the Word Cloud visual
- **Subtitle Language column:** removed the literal word "Subtitles", then used `Text.Split()` combined with `List.Count` (via a custom column) to count the number of subtitle languages per row
- **Instructors column:** since a single row could contain multiple comma-separated instructor names, the column was:
  1. Duplicated into a separate `Teachers` table (to avoid corrupting view/purchase metrics)
  2. Split by delimiter (`,`) using **Text Before Delimiter** anchored from the *end* of the string, to remove the trailing comma
  3. Split into multiple columns, then **unpivoted** to convert multiple columns into multiple rows (one instructor per row)
- **Course Duration:** standardized into a single unit (hours) via a custom M column with nested conditional logic:
  - `X months` → `X × 60` hours
  - `X hours` → kept as-is
  - `X minutes` → `X / 60` hours
  - `Flexible` schedule → capped at `200` hours
- **Skill count per course:** derived using `Text.Length(Skills) - Text.Length(Text.Replace(Skills, ",", ""))` to count commas (→ number of skills)

---

## 📈 Dashboard Features

| Visual | Purpose |
|---|---|
| Category Slicer | Global filter driving all visuals |
| Stacked Bar Chart (with Ribbons) | Course type distribution per category |
| Drillable Bar Chart | Average views by sub-category → language (drill up/down) |
| Word Cloud | Most in-demand skills per category (with stop-words support) |
| Donut Chart | Language distribution of courses |
| Matrix + `RANKX` measure | Top 5 categories by average views → language breakdown |
| Line Chart | Subtitle count vs. average views |
| Table + `RANKX` measure (static) | Top 3 instructors per category/sub-category by rating |
| Line Chart | Course duration (hrs) vs. average views |
| Matrix | Average skill count vs. average duration per category/sub-category |

### Key DAX Pattern Used (Top-N Ranking)

Used repeatedly (top categories, top instructors) — rank an entity, then conditionally show only top-N:

```dax
Rank Category by Avg Views =
RANKX(
    ALL(Table[Category]),
    CALCULATE(AVERAGE(Table[Number of Views]))
)

Top 5 Avg Views =
IF(
    [Rank Category by Avg Views] <= 5,
    CALCULATE(AVERAGE(Table[Number of Views])),
    BLANK()
)
```

---

## 💡 Key Insights

- **Computer Science** and **Data Science** dominate in both course count and average views.
- Skills with **cleaned spacing** (e.g., `Data Analysis`, `Data Visualization`) surface as genuinely distinct in-demand skills — unlike raw skill text, which over-weighted generic words like "Data" or "Management".
- Courses with **subtitle count > 15** tend to show noticeably higher average viewership.
- There is a **general positive trend** between course duration and average views, though not a strict linear relationship.
- **English** is the most prominent language across the catalog, followed by Spanish and Japanese in select categories.
- Categories with a **higher average skill count** per course also tend to have **longer average course durations**.

---

## 🌐 Publishing & Sharing

- Dashboard was published via **Power BI Service** (Workspace → Publish to Web) for view-only stakeholder access without login.
- Insight snapshots were exported to **PowerPoint** (via Power BI Service → Export → PowerPoint) for client-facing presentations, combining visuals with written analysis.

---

## 🚀 How to Use

1. Clone this repo
2. Open `EdTech_Course_Analytics.pbix` in **Power BI Desktop**
3. Refresh the data source if using an updated dataset
4. Use the **Category** and **Sub-category** slicers to explore insights interactively

---

## 📁 Repository Structure

```
├── EdTech_Course_Analytics.pbix     # Main Power BI file
├── data/
│   └── raw_course_data.csv          # Source dataset (Kaggle)
├── screenshots/                     # Dashboard preview images
└── README.md
```

---

## 👤 Author

**Ajay Verma**

---

## 📄 License

This project is open-sourced for educational purposes. Dataset credit belongs to the original Kaggle source.
