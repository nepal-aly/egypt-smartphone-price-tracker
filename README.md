# Egypt Smartphone Price Tracker

An end-to-end ETL pipeline that collects smartphone prices from Egyptian e-commerce websites, cleans and standardizes the data, and loads it into a SQL database for price comparison and tracking over time.

## Project Goal

The same phone is sold on several websites at different prices, and each website writes the product name differently. I built this pipeline to:

- Collect smartphone listings from multiple stores every day.
- Match the same phone across different stores.
- Keep a daily price history to track price changes and discounts.
- Produce a ready-to-use Excel report for comparison.

## Pipeline

| Stage | Notebook | Description | Status |
|---|---|---|---|
| Extract | `01_extract_jumia.ipynb` | Scrape all smartphones from Jumia Egypt | Done |
| Extract | `02_extract_noon.ipynb` | Scrape all smartphones from noon Egypt | In progress |
| Extract | `03_extract_btech.ipynb` | Scrape all smartphones from B.TECH | Planned |
| Transform | `04_transform.ipynb` | Clean prices, extract brand / storage / RAM, remove duplicates | Planned |
| Load | `05_load_sql.ipynb` | Load clean data into a SQLite database | Planned |
| Report | `06_report.ipynb` | SQL analysis and final Excel report | Planned |

## Data Source: Jumia Egypt

- **Coverage:** 1,848 smartphones across 47 pages (first run: 2026-10-24).
- **Method:** Embedded JSON scraping. Each category page contains the full product data as JSON inside `window.__INITIAL_STATE__`, so I read it directly instead of parsing HTML cards. This is more accurate and does not break when the page design changes.
- **Reliability:** Each page is retried up to 3 times on timeouts or empty responses, and any page that still fails is logged instead of stopping the run.

### Responsible Scraping

I followed the rules in Jumia's `robots.txt`:

- The User-Agent clearly identifies the scraper and links to this repository.
- A delay between requests keeps the rate far below the allowed 200 requests per minute.
- Only public category pages are used. Disallowed paths such as `/mobapi/` are not accessed.
- No personal data is collected, only public product information.

## Extracted Columns

| Column | Description |
|---|---|
| `site` | Source website |
| `sku` | Product ID on the website |
| `product_name` | Full product name |
| `brand` | Brand name |
| `category` | Sub-category (Android Phones, iPhones, ...) |
| `price` | Current price |
| `old_price` | Price before discount |
| `discount` | Discount percentage |
| `rating` | Average customer rating |
| `reviews_count` | Number of ratings |
| `is_official_store` | Sold by an official brand store |
| `is_express` | Eligible for express delivery |
| `is_sponsored` | Sponsored listing |
| `seller_id` | Seller ID |
| `url` | Product page link |
| `image_url` | Product image link |
| `page` | Page number in the category |
| `scraped_at` | Date and time of the scrape |

## Project Structure

```
egypt-smartphone-price-tracker/
├── 01_extract_jumia.ipynb
├── data/
│   ├── raw/          # Raw data as scraped, one file per day
│   └── clean/        # Cleaned data
├── database/         # SQLite database
├── output/           # Final Excel reports
├── requirements.txt
└── README.md
```

## Tech Stack

- Python
- requests, BeautifulSoup
- pandas
- SQLite
- Jupyter Notebook

## How to Run

```bash
git clone https://github.com/nepal-aly/egypt-smartphone-price-tracker.git
cd egypt-smartphone-price-tracker
pip install -r requirements.txt
jupyter notebook
```

Then open `01_extract_jumia.ipynb` and run all cells. A full run takes about 15 to 20 minutes, and the output is saved in `data/raw/` with today's date in the file name.

## Author

**Nepal Adel Aly**

- GitHub: [nepal-aly](https://github.com/nepal-aly)
- LinkedIn: [nepal-aly-data](https://www.linkedin.com/in/nepal-aly-data)
