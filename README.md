Zena's Amazing Athleisure Catalog

A simple Streamlit web app that displays an online clothing catalog backed by a Snowflake database. Users pick a sweatsuit color or style from a dropdown, and the app shows the matching product's image, price, available sizes, and a short upsell description.

Features:

Interactive product catalog built with Streamlit
Live data pulled from a Snowflake table (catalog_for_website)
Dropdown (selectbox) to choose a sweatsuit color or style
Dynamic product view: image, price, available sizes, and an upsell blurb
Credentials managed securely through Streamlit secrets


Tech Stack:

Python
Streamlit – UI and app framework
Snowflake (snowflake-connector-python) – data source
pandas – data handling


Project Structure:

streamlit_app_clothing/
├── clothing_app.py     # Main Streamlit application
└── requirements.txt    # Python dependencies

How It Works:

The app connects to Snowflake using credentials stored in Streamlit secrets.
It queries the distinct color_or_style values from the catalog_for_website table and loads them into a pandas DataFrame.
Those values populate a dropdown (st.selectbox).
When a user selects an option, the app queries the row for that item and displays:

The product image (direct_url)
Price
Available sizes
Upsell product description


Prerequisites:

Python 3.8+
A Snowflake account with a catalog_for_website table containing at least these columns:

color_or_style
direct_url (image URL)
price
size_list
upsell_product_desc

Setup:

Clone the repository:
bash   git clone https://github.com/sakura-57/streamlit_app_clothing.git
   cd streamlit_app_clothing

Install dependencies:

bash   
pip install -r requirements.txt

Configure Snowflake credentials:
Create a .streamlit/secrets.toml file in the project root:


toml
[snowflake]
   account   = "your_account_identifier"
   user      = "your_username"
   password  = "your_password"
   warehouse = "your_warehouse"
   database  = "your_database"
   schema    = "your_schema"
   role      = "your_role"

Do not commit secrets.toml to version control. Add .streamlit/secrets.toml to your .gitignore.

Running the App:

bashstreamlit run clothing_app.py

The app will open in your browser at http://localhost:8501.

Notes & Possible Improvements:

The product lookup query is built by string concatenation with the selected value. Since the value comes from a fixed dropdown this is low-risk, but using parameterized queries would be safer and is good practice.
Consider caching the catalog query with @st.cache_data to avoid re-querying Snowflake on every interaction.
The Snowflake connection/cursor could be closed (or managed with a context manager) when the app finishes its work.

License:

Add a license of your choice (e.g., MIT) if you intend to share or open-source this project.
