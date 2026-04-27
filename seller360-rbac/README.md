# Seller 360

Seller 360 is a real-time analytics pipeline for sellers on the ecommerce platform. It ingests order events from the ecommerce catalog, flattens nested item arrays, and computes per-seller analytics exposed through a GraphQL/REST/MCP API.

The implementation uses JWT based authorization to restrict data access and ensure sellers can only view their data. This data API can be directly exposed to end-users to support seller analytics.

Otherwise, this implementation is nearly identical to [seller360](../seller360/README.md).

## JWT Authentication

The public API endpoints read `seller_id` from the JWT claim `auth.seller_id`. Callers do not pass `seller_id`; it is derived from the bearer token and used to restrict each response to the authenticated seller.

The production package is configured for HS256 JWT validation and reads the base64 encoded key buffer from `SELLER360_RBAC_JWT_SECRET_BASE64`:

```bash
export SELLER360_RBAC_JWT_SECRET_BASE64='<base64-encoded-hs256-secret>'
```

For local testing, this workshop uses these values:

```text
Algorithm: HS256
Issuer: seller360-rbac
Audience: seller360-api
Secret: seller360-rbac-jwt-secret-change-me-before-production
```

The test package stores the secret as this base64 encoded key buffer:

```text
c2VsbGVyMzYwLXJiYWMtand0LXNlY3JldC1jaGFuZ2UtbWUtYmVmb3JlLXByb2R1Y3Rpb24=
```

To generate a token with [jwt.io](https://jwt.io):

1. Set the algorithm to `HS256`.
2. Use this payload, replacing `seller_id` with the seller you want to authorize:

```json
{
  "iss": "seller360-rbac",
  "aud": ["seller360-api"],
  "seller_id": "3442f8959a84dea7ee197c632cb2df15",
  "exp": 9999999999
}
```

3. In the Verify Signature section, enter this secret:

```text
seller360-rbac-jwt-secret-change-me-before-production
```

4. Copy the encoded JWT and send it in the `Authorization` header. For example, call `SalesByState` through GraphQL without passing `seller_id`:

```bash
curl -X POST 'http://localhost:8888/v1/graphql' \
  -H 'Content-Type: application/graphql' \
  -H 'Authorization: Bearer <JWT_FROM_JWT_IO>' \
  -d '
query {
  SalesByState {
    seller_id
    customer_state
    num_orders
    total_price
    total_freight_value
  }
}'
```

Example token for seller `3442f8959a84dea7ee197c632cb2df15`:

```text
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzZWxsZXIzNjAtcmJhYyIsImF1ZCI6WyJzZWxsZXIzNjAtYXBpIl0sInNlbGxlcl9pZCI6IjM0NDJmODk1OWE4NGRlYTdlZTE5N2M2MzJjYjJkZjE1IiwiZXhwIjo5OTk5OTk5OTk5fQ.mQkEDg4mdyXbv4W6MtHLxmFMECPS7Y3Q0p0xBGkqi2g
```

Set `SELLER360_RBAC_JWT_SECRET_BASE64` to your own base64 encoded secret before using the production package outside the workshop.
