# 🥬 Fresh Market Dashboard: Power BI built with HTML, CSS and JavaScript

A five-page sales dashboard for a fictional grocery business. Every page is **one DAX measure that returns a complete HTML page** (HTML + CSS + JavaScript), shown in a Power BI visual that can run scripts.

There are no Power BI slicers and no external libraries. The charts, filters, drill-down and map are all hand-written SVG and vanilla JavaScript.

<img width="1375" height="732" alt="image" src="https://github.com/user-attachments/assets/e0fd055e-aa73-487a-8537-5e5450caf00e" />
<img width="1304" height="730" alt="image" src="https://github.com/user-attachments/assets/ff4322d7-0dc8-45a1-aa5b-db803d57148d" />
<img width="1302" height="729" alt="image" src="https://github.com/user-attachments/assets/e768cded-ef6b-41d2-b705-2fa3785e4cf5" />
<img width="1299" height="729" alt="image" src="https://github.com/user-attachments/assets/f57f6e5b-c3e2-438f-af43-df5dcbc4f3c8" />
<img width="1301" height="730" alt="image" src="https://github.com/user-attachments/assets/377b57c2-2273-4f97-ad55-bcf66f6b37a4" />
<img width="1302" height="734" alt="image" src="https://github.com/user-attachments/assets/50be489a-610f-4ee5-800d-1d3f3b4ccc6e" />


## ✨ Features

| | |
|---|---|
| **JavaScript filter system** | Slide-in filter panel with a blurred veil, multi-select chips (Year, Region, Category), and **staged filters**: nothing changes until you click *Apply*. Active filters appear as removable chips, and a Reset button clears everything. |
| **Cross-filtering** | Click any bar, slice, point, tile, node, map bubble or state to filter the rest of the page. |
| **Drill down / drill up / next level** | On the Overview line chart: Year → Quarter → Month. |
| **Interactive vector map** | US state outlines with city bubbles, state shading, zoom, pan and click-to-filter. The geography is embedded in the code, so it **needs no internet connection**. |
| **Tooltips everywhere** | Every chart element, card, table cell and icon has a tooltip. |
| **Animation** | Count-up numbers, growing bars, drawn lines, staggered card entrances, shine effects. |
| **Light / dark mode** | Pure CSS toggle with a shared set of theme variables. |
| **Fixed 1280×720 stage** | The layout scales to fit whatever size the visual is. |

---

## 📄 Pages

| Page | File | What's on it |
|---|---|---|
| **Landing page** | `Landing_Page_FreshMarket_v2.dax` | Animated KPI cards (sales, orders, profit, margin, products), brand header, light/dark switch |
| **Sales Overview** | `FreshMarket_Overview_Dashboard_v5.dax` | KPIs; sales by year (with drill), by ship mode (donut), top 5 products, top 10 customers |
| **Customer Performance** | `FreshMarket_Customer_Performance_v2.dax` | Top-customer cards with generated SVG avatars, segment/city/country tags, orders/quantity/profit, progress bar, sparkline, country flag reveal, Sales/Profit/Orders switch, pagination |
| **Product Performance** | `FreshMarket_Product_Performance.dax` | Treemap, sales + profit combo chart, Category > Sub-Category > Product decomposition tree, interactive city map |
| **Category Performance** | `FreshMarket_Category_Performance.dax` | Profit waterfall, summary table and Category × Year matrix with expand/collapse (sub-categories, quarters), sortable columns, in-cell SVG icons, bars, rings and sparklines |

---

## 🧱 How it works

1. A DAX measure builds **text variables** from your model: dictionaries (years, regions, categories, …) and one compact row per order line, written as indexes plus sales and profit in cents.
2. The measure then returns a **single HTML string** with the page's CSS, markup and JavaScript, with that data embedded.
3. The visual renders the HTML. The JavaScript parses the embedded data, applies filters and selections in memory, and redraws the charts as SVG.

Because the data lives inside the page, interactions like staged filters, custom drill-down and the map are possible without any Power BI slicers.

---

## ✅ Requirements

- **Power BI Desktop**
- **A visual that renders HTML and allows JavaScript.** The default *HTML Content* visual removes `<script>` tags, so these pages need a visual that runs scripts. Check the visual's description before use.
- A data model with the tables and columns below.

### Expected data model

| Table | Columns used |
|---|---|
| `'Sales Fact'` | `Order ID`, `Total Sales`, `Total Profit`, `Quantity`, `Ship Mode` |
| `'Product Dim'` | `Category`, `Sub-Category`, `Product Name` |
| `'Customer Dim'` | `Customer Name`, `Region`, `City`, `State`, `Country`, `Segment` |
| `'Date Dim'` | `Order Date` |

The landing page also uses these measures:

```dax
T_Sales = SUM('Sales Fact'[Total Sales])
Total orders = DISTINCTCOUNT('Sales Fact'[Order ID])
Total Profit = SUM('Sales Fact'[Total Profit])
Profit Margin = DIVIDE([Total Profit], [T_Sales])
Total product with names = COUNTA('Product Dim'[Product Name])
```

All dimension tables must be related to `'Sales Fact'`. If your tables or columns are named differently, find and replace them at the top of each `.dax` file.

---

## 🚀 Setup

1. Add a new **measure** in Power BI (any table).
2. Paste the full contents of one `.dax` file as the measure's formula.
3. Add your script-capable HTML visual to a page and drop the measure into it.
4. Resize the visual to fill the page (the layout is 16:9 and scales to fit).
5. Repeat for each page. Use transparent Power BI buttons with *Page navigation* over the "Explore" buttons if you want navigation between pages.

> Tip: turn off *Visual header* icons (Format visual → General → Header icons) so they don't overlap the filter buttons.

---

## ⚠️ Notes and limitations

- **Data size:** the whole dataset is embedded as text, with one row per order line. This works well for a few thousand order lines. For much larger data, aggregate further before embedding.
- **Map locations:** the source data has no latitude or longitude, so the map uses a built-in list of the 1,800 largest US cities. Cities not in the list are placed near their state's centre and marked *(approximate location)* in the tooltip. If you have real coordinates, they can replace the lookup.
- **Drill levels:** drilling into a quarter or month changes the line chart only. It does not filter the other charts.
- **Fonts:** the pages use the system font stack, with no web fonts, to stay fully offline.
- **Years:** group by `'Date Dim'[Order Date]` inside `SUMMARIZE` and compute year, quarter and month from it. Using `CALCULATE(MAX(date))` returns the wrong year when the date table doesn't filter from the fact table.

---

## 📁 Repository structure

```
├── Landing_Page_FreshMarket_v2.dax
├── FreshMarket_Overview_Dashboard_v5.dax
├── FreshMarket_Customer_Performance_v2.dax
├── FreshMarket_Product_Performance.dax
├── FreshMarket_Category_Performance.dax
├── FreshMarket_Dashboard_Prompts.txt      # the prompts used to build each page
├── screenshots/
└── README.md
```

---


## 👤 Author

**Your name**: [LinkedIn](https://www.linkedin.com/) · [GitHub](https://github.com/)
