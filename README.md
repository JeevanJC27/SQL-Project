# 🎨 SQL Case Study: Art & Museum Data Analysis

This project showcases my end-to-end data wrangling and analysis skills using **Python, MySQL, and SQL**. I worked with real-world art and museum datasets to clean, structure, and extract meaningful insights for stakeholders.

---

## 🔍 Problem Statement

| Objective |
|-----------|
| The goal was to ensure **data quality and consistency** across multiple CSV datasets (e.g., artists, museums, paintings, sales), and then build **insightful queries** to support strategic decisions related to art pricing, museum performance, and artist impact. |

---

## 🧹 Data Preparation & Cleaning (Python + Pandas)

| Task | Description |
|------|-------------|
| Load & Connect | Connected 8 CSV files and imported them into MySQL using `SQLAlchemy`. |
| Missing Values | Identified and removed nulls from `image_link`, `canvas_size`, `product_size`. |
| Data Deduplication | Wrote SQL procedures to remove duplicate entries across key tables using `ROW_NUMBER()`. |
| Type Handling | Standardized inconsistent data types (e.g., `size_id` in `product_size`) using `pandas.to_numeric()`. |
| Invalid Entries | Corrected misspellings and invalid data (e.g., "Thusday" → "Thursday", numeric city names). |

---

## 🧠 Business Questions Solved (SQL)

| # | Key Questions Answered |
|---|--------------------------|
| 1️⃣ | Which paintings are not assigned to any museum? |
| 2️⃣ | Are there museums with no artwork on display? |
| 3️⃣ | How many paintings have a sale price higher than regular price? |
| 4️⃣ | Which canvas sizes are most and least used? |
| 5️⃣ | What are the top 5 most popular museums and artists? |
| 6️⃣ | Which museum is open the longest during the day? |
| 7️⃣ | Who are the artists with paintings across multiple countries? |
| 8️⃣ | Where are the most museums concentrated by country/city? |
| 9️⃣ | Who painted the most expensive and least expensive paintings? |
| 🔟 | What are the most popular styles and subjects (e.g., Portraits outside the USA)? |

---

## 📊 Tools & Technologies

| Category | Tools Used |
|---------|-------------|
| Programming | Python, Pandas |
| Database | MySQL, SQLAlchemy |
| Data Cleaning | Pandas (`dropna()`, `to_numeric()`), SQL |
| Data Analysis | SQL (CTEs, joins, window functions, procedures) |

---

## 💡 Impact & Value

| Outcome |
|---------|
| This project highlighted the **importance of data integrity** and **efficient querying** in making informed decisions. From improving museum operations to analyzing pricing trends and artist reach, the findings were designed to support data-driven strategy in the art & culture domain. |

---

## 🔗 Project Links

| Type | URL |
|------|-----|
| 📂 Project Repo | [GitHub - SQL Project](https://github.com/JeevanJC27/SQL-Project) |
| 📊 Maven Analytics Portfolio | [Case Study on Famous Paintings](https://mavenanalytics.io/project/24812) |

---
