# SWEN466 Assignment 1 – Short Note

## a. `customer_id` vs `customer_unique_id`

The `customers` table contains two identifier columns that both look like customer keys, but they serve different purposes. `customer_id` is a **per-order anonymisation key**: each time a person places a new order they receive a fresh `customer_id`, which is why there are 99 441 distinct values matching the total row count exactly. `customer_unique_id`, by contrast, identifies the **same real-world person across multiple orders**; the dataset contains only 96 096 distinct values, revealing that at least 3 345 individuals have placed more than one order. To count how many customers the business has actually served, use `COUNT(DISTINCT customer_unique_id)`, because summing `customer_id` would overcount repeat buyers.

---

## b. Junction table

`order_items` is the junction table. A many-to-many relationship exists between `orders` and `products` (one order can contain many products, and one product can appear in many orders) and a second many-to-many exists between `orders` and `sellers` (one order may involve several sellers, and one seller fulfils many orders). The `order_items` table resolves both relationships by introducing a composite primary key `(order_id, order_item_id)` and carrying two foreign keys — `product_id` and `seller_id` — alongside the price and freight attributes that belong to each specific line item.

---

## c. Strict vs. permissive cardinality

**Stricter than the diagram — `products → order_items`.**  
The ERD shows `products ||--o{ order_items` (zero-or-many, with a circle) to express that a product *could* exist in the catalogue without ever being ordered. However, the data shows `min = 1`: every product in `raw.products` appears in at least one `order_items` row. The circle on the diagram is a design-time allowance that the actual snapshot does not exercise.

**Circle justified by the data — `orders → order_items`.**  
The ERD shows `orders ||--o{ order_items` (zero-or-many). The data confirms `min = 0`: 775 orders have no matching rows in `order_items` at all. These are likely cancelled or pending orders that were recorded before any item was attached. The circle is therefore directly supported by real data, not just a theoretical possibility.
