# Data Documentation

This directory contains the dataset used by the Kasparro Agentic Facebook Performance Analyst.

## Dataset Overview

[cite_start]The file `sample_fb_ads.csv` is a **synthetic dataset** representing Facebook Ads performance for an eCommerce brand. It combines quantitative performance metrics (Spend, ROAS, CTR) with qualitative creative data (Creative Message, Type).

## Schema

The dataset includes the following columns used by the **Data Agent** and **Insight Agent**:

| Column Name | Type | Description |
| :--- | :--- | :--- |
| `date` | Date | Daily aggregation date (YYYY-MM-DD). |
| `campaign_name` | String | Name of the high-level campaign. |
| `adset_name` | String | Name of the specific ad set. |
| `creative_type` | String | Format of the ad (e.g., Image, Video, Carousel). |
| `creative_message` | String | **Key Input**: The actual ad copy/headline used. Used for generating new creatives. |
| `audience_type` | String | Target audience (e.g., Broad, Interest-based, Lookalike). |
| `platform` | String | Placement platform (e.g., Facebook, Instagram). |
| `country` | String | Target country code (e.g., US, UK). |
| `spend` | Float | Total amount spent in USD. |
| `impressions` | Integer | Total number of times the ad was shown. |
| `clicks` | Integer | Total number of clicks. |
| `ctr` | Float | Click-Through Rate (Clicks / Impressions). |
| `purchases` | Integer | Total conversion events. |
| `revenue` | Float | Total value generated in USD. |
| `roas` | Float | Return on Ad Spend (Revenue / Spend). |

## Usage & Notes
- The DataAgent canonicalizes noisy `campaign_name` values using fuzzy matching. Keep the original raw names in place; DataAgent will produce canonical IDs in the summary.
- Creative messages in the dataset are used as **anchor examples** to generate grounded creative recommendations — avoid replacing them unless necessary.
- If you want to use a full dataset, update `config/config.yaml` → `data_csv` to point to the new file path.
- For reproducibility during evaluation, keep the sample CSV in `data/` and do not remove it from the repository.

## Size and privacy
- The sample file is synthetic and safe to include in a public repo.
- For real production or sensitive datasets, do NOT commit raw data — instead provide a small synthetic sample and instructions for how to mount or upload the real data.

## Troubleshooting
- If the pipeline reports "CSV not found", check `config/config.yaml` → `data_csv` and ensure the relative path points to this file.
- If prompts complain about lack of `creative_message` anchors, ensure the CSV includes non-empty `creative_message` values for the campaigns you want creatives for.
