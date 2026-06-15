# Zena's Amazing Athleisure Catalog

A simple interactive web app built with **Streamlit** that displays a clothing catalog stored in a **Snowflake** database. Users pick a sweatsuit color or style from a dropdown, and the app shows the product image, price, available sizes, and a recommended (upsell) product.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Database Setup](#database-setup)
- [Configuration](#configuration)
- [Installation](#installation)
- [Running the App](#running-the-app)
- [How It Works](#how-it-works)
- [Possible Improvements](#possible-improvements)

---

## Overview

This is a lightweight catalog front-end. All product data lives in a Snowflake table called `catalog_for_website`. The Streamlit app queries that table at runtime, lets the user select a product variant, and renders the matching details on the page. It's a good example of connecting a Python web UI directly to a cloud data warehouse.

---

## Features

- Interactive dropdown (select box) to choose a sweatsuit color or style
- Live product image loaded from a URL stored in the database
- Displays price, available sizes, and an upsell product description
- Reads catalog data directly from Snowflake on each interaction
- Credentials kept out of the code using Streamlit's secrets management

---

## Tech Stack

| Component | Purpose |
|-----------|---------|
| **Python** | Core language |
| **Streamlit** | Web UI framework |
| **Snowflake** | Cloud data warehouse storing the catalog |
| **snowflake-connector-python** | Python client for querying Snowflake |
| **pandas** | Holds query results in a DataFrame |

---

## Project Structure

```
streamlit_app_clothing/
├── clothing_app.py      # Main Streamlit application
└── requirements.txt     # Python dependencies
```

---

## Prerequisites

- Python 3.8 or higher
- A Snowflake account with access to a database/schema/warehouse
- A `catalog_for_website` table populated with product data (see below)

---

## Database Setup

The app expects a Snowflake table named `catalog_for_website` with the following columns:

| Column | Description |
|--------|-------------|
| `color_or_style` | The product variant shown in the dropdown |
| `direct_url` | URL of the product image |
| `price` | Product price |
| `size_list` | Available sizes |
| `upsell_product_desc` | Text describing a recommended product |

Example schema:

```sql
CREATE TABLE catalog_for_website (
    color_or_style       STRING,
    direct_url           STRING,
    price                STRING,
    size_list            STRING,
    upsell_product_desc  STRING
);
```

---

## Configuration

This app uses **Streamlit secrets** to connect to Snowflake. Create a file at `.streamlit/secrets.toml` in the project root:

```toml
[snowflake]
account   = "your_account_identifier"
user      = "your_username"
password  = "your_password"
warehouse = "your_warehouse"
database  = "your_database"
schema    = "your_schema"
```

> **Note:** Never commit `secrets.toml` to version control. Add `.streamlit/secrets.toml` to your `.gitignore`.

---

## Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/sakura-57/streamlit_app_clothing.git
   cd streamlit_app_clothing
   ```

2. (Recommended) Create and activate a virtual environment:

   ```bash
   python -m venv venv
   source venv/bin/activate      # On Windows: venv\Scripts\activate
   ```

3. Install dependencies:

   ```bash
   pip install streamlit pandas snowflake-connector-python
   ```

   *(The included `requirements.txt` lists `snowflake-connector-python`; make sure `streamlit` and `pandas` are also installed.)*

---

## Running the App

```bash
streamlit run clothing_app.py
```

Streamlit will start a local server (default: `http://localhost:8501`) and open the app in your browser.

---

## How It Works

1. **Connect** to Snowflake using credentials from `st.secrets["snowflake"]`.
2. **Query** all available variants: `SELECT color_or_style FROM catalog_for_website`.
3. **Load** the results into a pandas DataFrame and extract the values into a list.
4. **Display** a select box so the user can pick a color or style.
5. **Re-query** the table for the selected variant to fetch its image URL, price, sizes, and upsell text.
6. **Render** the product image (with a caption), price, available sizes, and upsell description on the page.

---

## Possible Improvements

- **Use parameterized queries:** the second query is built with string concatenation, which is vulnerable to SQL injection. Use bind parameters instead (e.g. `my_cur.execute(query, (option,))`).
- **Cache the connection / queries** with `@st.cache_resource` and `@st.cache_data` to avoid re-running on every interaction.
- **Add error handling** for missing data, failed connections, or broken image URLs.
- **Pin dependency versions** in `requirements.txt` for reproducible builds, and add `streamlit` and `pandas` explicitly.

---

## License

No license is currently specified. Add one (e.g. MIT) if you intend others to reuse this code.
