# Field Collections Monitoring Dashboard

An enterprise-grade Streamlit dashboard for collections / field-visit / curing
performance monitoring. Upload an XLSX, XLS, or CSV extract and get automatic
executive KPIs, industry/agent/area/bucket breakdowns, field-result analysis,
payment/PTP tracking, an interactive account grid, and Excel exports.

## 1. Install

```bash
pip install -r requirements.txt
```

(`streamlit-aggrid` is optional — the account detail table falls back to a
plain sortable/filterable `st.dataframe` if it isn't installed.)

## 2. Run

```bash
streamlit run app.py
```

Then open the URL Streamlit prints (usually `http://localhost:8501`).

## 3. Use it

1. Upload your file from the sidebar (**📁 Data Source**).
2. The app auto-detects columns such as `concat`, `OB/TAD`, `Status`,
   `Industry`, `Agent`, `Area`, `Fv_Result`, etc. — header names don't need to
   match exactly (case/spacing-insensitive fuzzy matching). Check the
   **"🔎 Detected column mapping"** expander in the sidebar to confirm.
3. Use the sidebar filters (Status, SubStatus, Industry, Area, Area2, Agent,
   Bucket, dates, Active Status, FV Result, Product Type) to slice the data.
   **Clear all filters** resets everything.
4. Explore the 10 tabs: Executive Summary, Industry, Agent, Field Result,
   Area, Active Status, Bucket, Payment, Account Details, Downloads.
5. Use the **Downloads** tab (or the export button on Account Details) to get
   Excel workbooks of the summary, industry report, area report, agent
   performance, or filtered raw data.

## Notes on methodology

- **All account counts are DISTINCT counts of `concat`.** Duplicate `concat`
  rows are dropped before counting/summing so no account is double-counted.
- `OB/TAD` is coerced to numeric (currency symbols/commas stripped, blanks →
  0) before summing.
- Category breakdowns (Field Result, Active Status, Bucket, Area, Province,
  etc.) are built dynamically from whatever values exist in your data, rather
  than a hard-coded list — so the dashboard adapts automatically if your
  source file uses different labels than the original spec's examples.
- "NCR vs Universal" comparison in the Area tab matches any Area2 value
  containing "NCR" or "Universal" (case-insensitive).
- Data loading and aggregation are cached with `@st.cache_data`, and grouping
  operations are vectorized pandas `groupby`s, so the app comfortably handles
  100k+ row files.

## Deploying on a shared network drive

If you're hosting this the same way as your other DAR tooling (a shared
network path), just copy `app.py` + `requirements.txt` to that share, `pip
install -r requirements.txt` once, and run `streamlit run app.py
--server.address 0.0.0.0 --server.port <port>` so teammates can reach it over
the network.
