## Relationships

- `orders.platform` links to `platforms.platform`
- `orders.area` links to `areas.area` (pickup orders have `area = Pickup`, which is not in `areas.csv`)
- `order_items.order_id` links to `orders.order_id`
- `order_items.dish` links to `menu.dish`

## Quick Start

```python
import pandas as pd

# 1. Load all five files
time_cols = ["order_datetime", "preparing_at", "ready_at", "dispatched_at", "completed_at"]
orders = pd.read_csv("orders.csv", parse_dates=time_cols)
items = pd.read_csv("order_items.csv")
menu = pd.read_csv("menu.csv")
platforms = pd.read_csv("platforms.csv")
areas = pd.read_csv("areas.csv")

# 2. Keep delivered orders only
delivered = orders[orders["status"] == "Delivered"].copy()

# 3. Net revenue by platform
print(delivered.groupby("platform")["net_revenue"].sum().sort_values(ascending=False))

# 4. Average completion time (minutes) by area
delivered["total_minutes"] = (delivered["completed_at"] - delivered["order_datetime"]).dt.total_seconds() / 60
print(delivered.groupby("area")["total_minutes"].mean().round(1).sort_values())

# 5. Join order items with menu details
dishes = items.merge(menu[["dish", "prep_minutes", "popularity"]], on="dish", how="left")
print(dishes.groupby("dish")["line_total"].sum().sort_values(ascending=False).head(5))

# 6. Join orders with platform and area details
full = (orders.merge(platforms[["platform", "commission_rate"]], on="platform", how="left")
              .merge(areas[["area", "distance_km"]], on="area", how="left"))
print(full[["order_id", "platform", "commission_rate", "area", "distance_km"]].head())
```
