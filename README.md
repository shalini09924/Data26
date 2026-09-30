# Dubai Restaurant Orders Dataset

A sample dataset of food orders for a Dubai-based restaurant selling through delivery aggregators, its own direct channels, and phone. It covers **30 days of orders (2026-08-30 to 2026-09-28)** across 10 delivery areas, and is suited to analysis of revenue, platform commissions, delivery performance, and menu popularity.

## Files

| File | Description | Rows |
|---|---|---|
| `orders.csv` | One row per order | 6,336 |
| `order_items.csv` | One row per dish in an order | 18,253 |
| `menu.csv` | Menu reference | 56 |
| `platforms.csv` | Sales channels and commission rates | 7 |
| `areas.csv` | Delivery areas and distances | 10 |

## Data Dictionary

### orders.csv
| Column | Description |
|---|---|
| `order_id` | Unique order ID (e.g. `ORD-100001`). Primary key |
| `order_datetime` | When the order was placed |
| `channel_type` | `Aggregator`, `Direct`, or `Phone` |
| `platform` | Talabat, Deliveroo, Careem, Noon Food, App, Website, Phone |
| `order_type` | `Delivery` or `Pickup` |
| `area` | Delivery area (`Pickup` for pickup orders) |
| `status` | `Delivered` or `Cancelled` |
| `gross_amount` | Order value in AED |
| `commission_amount` | Platform commission in AED |
| `net_revenue` | `gross_amount - commission_amount` |
| `preparing_at` | Time the kitchen started preparing |
| `ready_at` | Time the order was ready |
| `dispatched_at` | Time the order left for delivery (empty for pickup) |
| `completed_at` | Time the order was completed |

Missing timestamps are expected: cancelled orders have no `ready_at` or `completed_at`, and pickup orders have no `dispatched_at`.

### order_items.csv
| Column | Description |
|---|---|
| `order_id` | Links to `orders.order_id` |
| `cuisine` | Arabic, Indian, Italian, Chinese, Continental |
| `category` | Starter, Main, Side, Dessert, Beverage |
| `dish` | Dish name (matches `menu.dish`) |
| `quantity` | Units ordered |
| `unit_price` | Price per unit in AED |
| `line_total` | `quantity * unit_price` |

### menu.csv
| Column | Description |
|---|---|
| `dish_id` | Unique dish ID (e.g. `AR01`). Primary key |
| `dish` | Dish name |
| `cuisine` | Cuisine type |
| `category` | Menu category |
| `price_aed` | Menu price in AED |
| `prep_minutes` | Typical preparation time |
| `popularity` | Popularity score (higher = more popular) |

### platforms.csv
| Column | Description |
|---|---|
| `platform` | Platform name. Primary key |
| `channel_type` | Aggregator, Direct, or Phone |
| `commission_rate` | Commission as a fraction of gross (0.25 = 25%) |
| `order_share` | Share of total orders from this platform |
| `pickup_share` | Share of this platform's orders that are pickups |

### areas.csv
| Column | Description |
|---|---|
| `area` | Delivery area name. Primary key |
| `distance_km` | Distance from the restaurant in km |
| `order_weight` | Relative share of orders from this area |

## Relationships

- `orders.platform` links to `platforms.platform`
- `orders.area` links to `areas.area`
- `order_items.order_id` links to `orders.order_id`
- `order_items.dish` links to `menu.dish`

## Quick Start

```python
import pandas as pd

orders = pd.read_csv("orders.csv", parse_dates=["order_datetime"])
items = pd.read_csv("order_items.csv")
menu = pd.read_csv("menu.csv")
platforms = pd.read_csv("platforms.csv")
areas = pd.read_csv("areas.csv")

# Net revenue by platform (delivered orders only)
delivered = orders[orders["status"] == "Delivered"]
print(delivered.groupby("platform")["net_revenue"].sum().sort_values(ascending=False))
```

## Questions You Can Explore

- Which platform gives the best net revenue after commission?
- How long does an order take from placement to completion, by area and platform?
- What is the cancellation rate by platform?
- Which dishes and cuisines drive the most revenue?
- Which hours of the day are busiest?

## Notes

- All monetary values are in AED.
- Timestamps use the format `YYYY-MM-DD HH:MM:SS`.

   ## Live Dashboard
   [View the dashboard](https://shalini09924.github.io/Data26/dashboard.html)
