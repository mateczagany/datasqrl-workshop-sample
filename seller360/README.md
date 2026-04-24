# Seller 360

Seller 360 is a real-time analytics pipeline for sellers on the ecommerce platform. It ingests order events from the ecommerce catalog, flattens nested item arrays, and computes per-seller analytics exposed through a GraphQL/REST/MCP API.

## Features

- **OrderItems**: Real-time flattened view of all order line items per seller, ordered most-recent first. Enables seller dashboards to display individual orders and drill into product detail.
- **ProductsSoldByDay**: Daily tumbling-window aggregation of order items per seller per product with order count, total revenue, and total freight value. Enables tracking of per-product sales trends over time.

## Data Sources

Sources are imported from the shared ecommerce catalog at `catalog/ecommerce-sources`:
- `orders-{environment}.sqrl`: Streaming `OrderEvents` with nested `items` array (product_id, seller_id, price, freight_value, etc.)
- `masterdata-{environment}.sqrl`: CDC streams for `Sellers`, `Products`, `Customers`, `Geolocation`

## API

The GraphQL API exposes two queries, both requiring `seller_id`:

### `OrderItems(seller_id, limit, offset)`
Returns flattened order line items for the seller, most recent first.

### `ProductsSoldByDay(seller_id, limit, offset)`
Returns daily product sales aggregates for the seller: most recent day first, then ordered by `product_id`.

REST and MCP endpoints are generated automatically.

## Environments

| Environment | Package File                  | Notes |
|-------------|-------------------------------|-------|
| test | `seller360-test-package.json` | Uses filesystem test data (`ecommerce-test-data=dev`) |
| local | `seller360-prod-package.json` | Uses Kafka for live event ingestion |

