📦 Supply Chain Synthetic Data Model

This repository contains a complete synthetic manufacturing and distribution dataset designed to simulate an enterprise-level ERP and planning environment. It models the full flow of customer orders, shipments, materials, plant operations, and inventory positions — ideal for use in Microsoft Fabric, Power BI, Databricks, or Snowflake environments for analytics, digital-twin, and decision-intelligence demonstrations.

🧩 Data Model Overview

The model follows a classic star schema structure with 9 core tables:

Type	Table	Description
Dimension	customer_dim.csv	Master data for customers, partners, and sales channels.
	plant_dim.csv	Manufacturing plants, warehouses, and 3PL locations.
	materials_dim.csv	Product master for finished goods, subassemblies, and raw materials.
	date_dim.csv	Full two-year date calendar with fiscal and time attributes.
Bridge	plant_material_bridge.csv	Defines which materials are produced or stored at each plant.
Fact	fact_orders.csv	Customer orders (open, closed, cancelled) by line.
	fact_shipments.csv	Individual shipment transactions tied to order lines.
	fact_order_shipments.csv	Flattened table combining order + shipment details for reporting.
	fact_inventory.csv	Daily inventory snapshot by plant and material.

erDiagram
    CUSTOMER_DIM ||--o{ FACT_ORDERS : has
    MATERIALS_DIM ||--o{ FACT_ORDERS : contains
    PLANT_DIM ||--o{ FACT_ORDERS : fulfills
    DATE_DIM ||--o{ FACT_ORDERS : references
    FACT_ORDERS ||--o{ FACT_SHIPMENTS : ships
    PLANT_DIM ||--o{ PLANT_MATERIAL_BRIDGE : owns
    MATERIALS_DIM ||--o{ PLANT_MATERIAL_BRIDGE : links
    PLANT_MATERIAL_BRIDGE ||--o{ FACT_INVENTORY : stocks



🔑 Key Relationships
| From Table              | Key                          | To Table                | Key                          | Cardinality |
| ----------------------- | ---------------------------- | ----------------------- | ---------------------------- | ----------- |
| `fact_orders`           | `Customer_Key`               | `customer_dim`          | `Customer_Key`               | Many-to-one |
| `fact_orders`           | `Material_Key`               | `materials_dim`         | `Material_Key`               | Many-to-one |
| `fact_orders`           | `Plant_Key`                  | `plant_dim`             | `Plant_Key`                  | Many-to-one |
| `fact_orders`           | `Order_Entry_Date`           | `date_dim`              | `Date`                       | Many-to-one |
| `fact_shipments`        | `order_line_key`             | `fact_orders`           | `order_line_key`             | One-to-many |
| `plant_material_bridge` | (`Plant_Key`,`Material_Key`) | `materials_dim`         | (`Material_Key`)             | Many-to-one |
| `fact_inventory`        | (`Plant_Key`,`Material_Key`) | `plant_material_bridge` | (`Plant_Key`,`Material_Key`) | Many-to-one |


🧱 Table Summaries
Customer Dimension

50,000 customer location records

Fields include Partner_Name, Sold_To_Name, MCN (Major Customer Number), address, sales channel, and delivery type

Each customer is linked to an MCN and ZIP3 combination

Plant Dimension

35 manufacturing or distribution facilities

Includes headquarters, production plants, suppliers, and 3PL sites

Materials Dimension

10,000 products including finished goods, subassemblies, and raw materials

Contains material type, dimensions, weight, cost, warranty, and associated MCN for customer-specific SKUs

Plant–Material Bridge

Defines which materials are available at each plant

Includes reorder points, safety stock, and service level targets

Fact Orders

75,000 orders (~150,000 lines)

Contains order quantities, promised and requested dates, cancellations, and pricing

Fact Shipments

Shipment-level detail by order line

Tracks shipment date, delivery quantity, method, and carrier

Fact Order Shipments

Flattened version combining orders and shipments for easy reporting

Useful for Power BI modeling and service-level KPIs

Fact Inventory

Current snapshot of on-hand, available, and in-transit stock

Flags quality holds, dock inventory, and last receipt dates

Date Dimension

~900 calendar dates over two years

Includes month, quarter, day name, week number, and period boundaries

📊 Example Use Cases

Order-to-Cash Pipeline Analysis
Track open orders, backlogs, and fulfillment performance.

Shipment Lead Time and On-Time Performance
Calculate promised vs actual delivery metrics.

Inventory Optimization
Evaluate service levels and inventory positioning by plant.

Demand vs Supply Matching
Correlate orders, shipments, and on-hand inventory across the network.

📅 Date Dimension Highlights

The date_dim table includes:

Calendar & fiscal periods (Year, Quarter, YearMonth, etc.)

Day-level details (DayOfWeek, DayName, IsWeekend)

Period boundaries (FirstOfMonth, EndOfMonth, etc.)

| Table                   | Rows     |
| ----------------------- | -------- |
| `customer_dim`          | 50,000   |
| `plant_dim`             | 35       |
| `materials_dim`         | 10,000   |
| `plant_material_bridge` | 210,000  |
| `fact_orders`           | 150,000  |
| `fact_shipments`        | 200,000+ |
| `fact_order_shipments`  | 250,000+ |
| `fact_inventory`        | 210,000  |
| `date_dim`              | ~900     |


🧭 Versioning & Attribution

This dataset is synthetic and non-proprietary.
It was programmatically generated for educational and demonstration purposes and does not contain real customer, product, or operational data.
)
