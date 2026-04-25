# Seller 360

Seller 360 is a real-time analytics pipeline for sellers on the ecommerce platform. It ingests order events from the ecommerce catalog, flattens nested item arrays, and computes per-seller analytics exposed through a GraphQL/REST/MCP API.

The implementation uses JWT based authorization to restrict data access and ensure sellers can only view their data. This data API can be directly exposed to end-users to support seller analytics.

Otherwise, this implementation is nearly identical to [seller360](../seller360/README.md).