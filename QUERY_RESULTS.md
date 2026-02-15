# Query Results Examples - SRMiggy Food Ordering System

This document shows expected output for each of the 8 analytical queries in the schema.

---

## Query 1: Customer Order History

**Purpose**: View complete order history for a specific customer

**SQL Query**:
```sql
SELECT 
    o.order_id,
    o.order_date,
    v.restaurant_name,
    o.order_status,
    o.final_amount,
    o.customer_rating,
    o.customer_review,
    GROUP_CONCAT(CONCAT(oi.quantity, 'x ', mi.item_name) SEPARATOR ', ') as items_ordered
FROM Orders o
INNER JOIN Vendors v ON o.vendor_id = v.vendor_id
INNER JOIN Order_Items oi ON o.order_id = oi.order_id
INNER JOIN Menu_Items mi ON oi.menu_item_id = mi.menu_item_id
WHERE o.customer_id = 1
GROUP BY o.order_id
ORDER BY o.order_date DESC;
```

**Expected Output**:
```
+----------+---------------------+---------------------+--------------+--------------+-----------------+----------------------------------+---------------------------+
| order_id | order_date          | restaurant_name     | order_status | final_amount | customer_rating | customer_review                  | items_ordered             |
+----------+---------------------+---------------------+--------------+--------------+-----------------+----------------------------------+---------------------------+
|        6 | 2024-02-15 19:00:00 | North Bites         | preparing    | 220.00       | NULL            | NULL                             | 1x Paneer Butter Masala,  |
|          |                     |                     |              |              |                 |                                  | 1x Dal Tadka, 1x Samosa   |
+----------+---------------------+---------------------+--------------+--------------+-----------------+----------------------------------+---------------------------+
|        1 | 2024-02-10 12:30:00 | South Indian Delight| delivered    | 130.00       | 5               | Excellent food and quick delivery| 1x South Indian Meals     |
+----------+---------------------+---------------------+--------------+--------------+-----------------+----------------------------------+---------------------------+
```

---

## Query 2: Vendor Revenue Summary

**Purpose**: Calculate total revenue and order statistics for each vendor

**SQL Query**:
```sql
SELECT 
    v.vendor_id,
    v.restaurant_name,
    v.cuisine_type,
    COUNT(DISTINCT o.order_id) as total_orders,
    COUNT(DISTINCT CASE WHEN o.order_status = 'delivered' THEN o.order_id END) as delivered_orders,
    SUM(CASE WHEN o.order_status = 'delivered' THEN o.final_amount ELSE 0 END) as total_revenue,
    AVG(CASE WHEN o.order_status = 'delivered' THEN o.final_amount END) as avg_order_value,
    v.average_rating
FROM Vendors v
LEFT JOIN Orders o ON v.vendor_id = o.vendor_id
GROUP BY v.vendor_id
ORDER BY total_revenue DESC;
```

**Expected Output**:
```
+-----------+----------------------+---------------+--------------+-------------------+---------------+-----------------+----------------+
| vendor_id | restaurant_name      | cuisine_type  | total_orders | delivered_orders  | total_revenue | avg_order_value | average_rating |
+-----------+----------------------+---------------+--------------+-------------------+---------------+-----------------+----------------+
|         3 | Chinese Wok Express  | Chinese       |            2 |                 1 | 325.00        | 325.00          | 4.70           |
|         2 | North Bites          | North Indian  |            3 |                 1 | 200.00        | 200.00          | 4.20           |
|         1 | South Indian Delight | South Indian  |            3 |                 2 | 295.00        | 147.50          | 4.50           |
+-----------+----------------------+---------------+--------------+-------------------+---------------+-----------------+----------------+
```

**Insights**:
- Chinese Wok Express has the highest average order value
- South Indian Delight has the most orders
- All vendors have good ratings (4.2-4.7)

---

## Query 3: Wallet Transaction Report

**Purpose**: View complete wallet transaction history with running balance

**SQL Query**:
```sql
SELECT 
    wt.wallet_transaction_id,
    wt.transaction_date,
    wt.transaction_type,
    wt.amount,
    wt.balance_after,
    wt.description,
    CASE 
        WHEN wt.reference_order_id IS NOT NULL THEN CONCAT('Order #', wt.reference_order_id)
        ELSE 'N/A'
    END as order_reference
FROM Wallet_Transactions wt
WHERE wt.user_id = 1
ORDER BY wt.transaction_date DESC;
```

**Expected Output**:
```
+------------------------+---------------------+------------------+--------+---------------+------------------+-----------------+
| wallet_transaction_id  | transaction_date    | transaction_type | amount | balance_after | description      | order_reference |
+------------------------+---------------------+------------------+--------+---------------+------------------+-----------------+
|                      4 | 2024-02-15 19:02:00 | debit            | 220.00 | 400.00        | Payment for order| Order #6        |
|                      3 | 2024-02-14 15:00:00 | credit           | 250.00 | 620.00        | Wallet recharge  | N/A             |
|                      2 | 2024-02-10 12:31:00 | debit            | 130.00 | 370.00        | Payment for order| Order #1        |
|                      1 | 2024-02-01 10:00:00 | credit           | 500.00 | 500.00        | Wallet recharge  | N/A             |
+------------------------+---------------------+------------------+--------+---------------+------------------+-----------------+
```

**Insights**:
- Customer started with ₹500 recharge
- Spent ₹130 on first order
- Recharged ₹250 more
- Current balance: ₹400

---

## Query 4: Pending Orders (Operations Dashboard)

**Purpose**: View all orders that are not yet delivered or cancelled

**SQL Query**:
```sql
SELECT 
    o.order_id,
    o.order_date,
    u.full_name as customer_name,
    v.restaurant_name,
    o.order_status,
    o.final_amount,
    ds.slot_name as delivery_slot,
    TIMESTAMPDIFF(MINUTE, o.order_date, NOW()) as minutes_since_order
FROM Orders o
INNER JOIN Users u ON o.customer_id = u.user_id
INNER JOIN Vendors v ON o.vendor_id = v.vendor_id
LEFT JOIN Delivery_Slots ds ON o.delivery_slot_id = ds.slot_id
WHERE o.order_status NOT IN ('delivered', 'cancelled')
ORDER BY o.order_date ASC;
```

**Expected Output**:
```
+----------+---------------------+---------------+----------------------+-------------------+--------------+---------------+---------------------+
| order_id | order_date          | customer_name | restaurant_name      | order_status      | final_amount | delivery_slot | minutes_since_order |
+----------+---------------------+---------------+----------------------+-------------------+--------------+---------------+---------------------+
|        5 | 2024-02-15 18:30:00 | Vikram Singh  | Chinese Wok Express  | out_for_delivery  | 270.00       | Dinner        | 35                  |
|        6 | 2024-02-15 19:00:00 | Rahul Sharma  | North Bites          | preparing         | 220.00       | Dinner        | 5                   |
|        7 | 2024-02-15 19:15:00 | Priya Kumar   | South Indian Delight | confirmed         | 120.00       | Dinner        | 0                   |
+----------+---------------------+---------------+----------------------+-------------------+--------------+---------------+---------------------+
```

**Use Case**: Kitchen staff can see which orders to prepare, delivery team knows which orders to deliver.

---

## Query 5: Most Ordered Food Items

**Purpose**: Identify top-selling menu items across all vendors

**SQL Query**:
```sql
SELECT 
    mi.menu_item_id,
    mi.item_name,
    v.restaurant_name,
    mi.category,
    mi.price,
    COUNT(oi.order_item_id) as times_ordered,
    SUM(oi.quantity) as total_quantity_sold,
    SUM(oi.subtotal) as total_revenue
FROM Menu_Items mi
INNER JOIN Vendors v ON mi.vendor_id = v.vendor_id
INNER JOIN Order_Items oi ON mi.menu_item_id = oi.menu_item_id
INNER JOIN Orders o ON oi.order_id = o.order_id
WHERE o.order_status = 'delivered'
GROUP BY mi.menu_item_id
ORDER BY times_ordered DESC, total_quantity_sold DESC
LIMIT 10;
```

**Expected Output**:
```
+--------------+----------------------+----------------------+-------------+--------+---------------+---------------------+---------------+
| menu_item_id | item_name            | restaurant_name      | category    | price  | times_ordered | total_quantity_sold | total_revenue |
+--------------+----------------------+----------------------+-------------+--------+---------------+---------------------+---------------+
|            1 | Masala Dosa          | South Indian Delight | Breakfast   | 60.00  | 2             | 2                   | 120.00        |
|            7 | Paneer Butter Masala | North Bites          | Main Course | 150.00 | 1             | 1                   | 150.00        |
|           14 | Chicken Fried Rice   | Chinese Wok Express  | Rice        | 130.00 | 1             | 1                   | 130.00        |
|           16 | Chilli Chicken       | Chinese Wok Express  | Starter     | 180.00 | 1             | 1                   | 180.00        |
|            9 | Butter Naan          | North Bites          | Bread       | 25.00  | 1             | 2                   | 50.00         |
+--------------+----------------------+----------------------+-------------+--------+---------------+---------------------+---------------+
```

**Insights**: 
- Masala Dosa is the most frequently ordered item
- Chilli Chicken has highest revenue per order
- Butter Naan ordered in multiple quantities

---

## Query 6: Rider Performance Report

**Purpose**: Evaluate rider performance metrics

**SQL Query**:
```sql
SELECT 
    r.rider_id,
    u.full_name as rider_name,
    r.vehicle_type,
    r.is_available,
    r.total_deliveries,
    COUNT(DISTINCT o.order_id) as orders_in_system,
    COUNT(DISTINCT CASE WHEN o.order_status = 'delivered' THEN o.order_id END) as completed_deliveries,
    r.average_rating,
    AVG(CASE 
        WHEN o.order_status = 'delivered' AND o.confirmed_at IS NOT NULL AND o.delivered_at IS NOT NULL 
        THEN TIMESTAMPDIFF(MINUTE, o.confirmed_at, o.delivered_at) 
    END) as avg_delivery_time_minutes
FROM Riders r
INNER JOIN Users u ON r.user_id = u.user_id
LEFT JOIN Orders o ON r.rider_id = o.rider_id
GROUP BY r.rider_id
ORDER BY r.average_rating DESC;
```

**Expected Output**:
```
+----------+--------------+---------------+--------------+-------------------+------------------+----------------------+----------------+---------------------------+
| rider_id | rider_name   | vehicle_type  | is_available | total_deliveries  | orders_in_system | completed_deliveries | average_rating | avg_delivery_time_minutes |
+----------+--------------+---------------+--------------+-------------------+------------------+----------------------+----------------+---------------------------+
|        3 | Ravi Kumar   | Scooter       | 1            | 290               | 1                | 1                    | 4.80           | 30.00                     |
|        1 | Suresh Kumar | Motorcycle    | 1            | 450               | 3                | 2                    | 4.60           | 37.50                     |
|        2 | Ganesh Babu  | Motorcycle    | 1            | 380               | 2                | 1                    | 4.40           | 40.00                     |
+----------+--------------+---------------+--------------+-------------------+------------------+----------------------+----------------+---------------------------+
```

**Insights**:
- Ravi Kumar has best rating (4.8) and fastest avg delivery time (30 min)
- Suresh Kumar has most deliveries (450 total)
- All riders are currently available for orders

---

## Query 7: Daily Revenue Report

**Purpose**: Track daily sales performance

**SQL Query**:
```sql
SELECT 
    DATE(o.order_date) as order_date,
    COUNT(DISTINCT o.order_id) as total_orders,
    COUNT(DISTINCT CASE WHEN o.order_status = 'delivered' THEN o.order_id END) as delivered_orders,
    SUM(CASE WHEN o.order_status = 'delivered' THEN o.final_amount ELSE 0 END) as total_revenue,
    AVG(CASE WHEN o.order_status = 'delivered' THEN o.final_amount END) as avg_order_value
FROM Orders o
GROUP BY DATE(o.order_date)
ORDER BY order_date DESC;
```

**Expected Output**:
```
+------------+--------------+-------------------+---------------+-----------------+
| order_date | total_orders | delivered_orders  | total_revenue | avg_order_value |
+------------+--------------+-------------------+---------------+-----------------+
| 2024-02-15 |            3 |                 0 | 0.00          | NULL            |
| 2024-02-14 |            1 |                 0 | 0.00          | NULL            |
| 2024-02-13 |            1 |                 1 | 165.00        | 165.00          |
| 2024-02-12 |            1 |                 1 | 200.00        | 200.00          |
| 2024-02-11 |            1 |                 1 | 325.00        | 325.00          |
| 2024-02-10 |            1 |                 1 | 130.00        | 130.00          |
+------------+--------------+-------------------+---------------+-----------------+
```

**Insights**:
- Feb 11 had highest revenue (₹325)
- Feb 15 has 3 pending orders (not yet delivered)
- Average order value ranges from ₹130-325

---

## Query 8: Customer Wallet Balance Summary

**Purpose**: View all customers with their current wallet balances

**SQL Query**:
```sql
SELECT 
    u.user_id,
    u.full_name,
    u.email,
    u.wallet_balance,
    COUNT(DISTINCT wt.wallet_transaction_id) as total_transactions,
    SUM(CASE WHEN wt.transaction_type = 'credit' THEN wt.amount ELSE 0 END) as total_credits,
    SUM(CASE WHEN wt.transaction_type = 'debit' THEN wt.amount ELSE 0 END) as total_debits,
    COUNT(DISTINCT o.order_id) as total_orders
FROM Users u
LEFT JOIN Wallet_Transactions wt ON u.user_id = wt.user_id
LEFT JOIN Orders o ON u.user_id = o.customer_id
WHERE u.user_role = 'customer'
GROUP BY u.user_id
ORDER BY u.wallet_balance DESC;
```

**Expected Output**:
```
+---------+---------------+---------------------------+----------------+--------------------+---------------+--------------+--------------+
| user_id | full_name     | email                     | wallet_balance | total_transactions | total_credits | total_debits | total_orders |
+---------+---------------+---------------------------+----------------+--------------------+---------------+--------------+--------------+
|       4 | Sneha Reddy   | sneha.reddy@srmist.edu.in | 1000.00        | 1                  | 1000.00       | 0.00         | 1            |
|       2 | Priya Kumar   | priya.kumar@srmist.edu.in | 880.00         | 2                  | 1000.00       | 120.00       | 2            |
|       1 | Rahul Sharma  | rahul.sharma@srmist.edu.in| 400.00         | 4                  | 750.00        | 350.00       | 2            |
|       3 | Amit Patel    | amit.patel@srmist.edu.in  | 300.00         | 3                  | 500.00        | 380.00       | 2            |
|       5 | Vikram Singh  | vikram.singh@srmist.edu.in| 230.00         | 2                  | 500.00        | 270.00       | 1            |
+---------+---------------+---------------------------+----------------+--------------------+---------------+--------------+--------------+
```

**Insights**:
- Sneha Reddy has highest balance (₹1000) - just recharged, no orders yet
- Rahul Sharma is most active (4 transactions, 2 orders)
- All customers maintain positive wallet balances

---

## Using These Results in Your Report

### Tips for Including Query Results:

1. **Format as Tables**: Use proper table formatting in Word/LaTeX
2. **Add Captions**: "Table 3.1: Customer Order History Query Results"
3. **Explain Insights**: After each result, explain what it shows
4. **Compare Results**: Show before and after for updates
5. **Use Screenshots**: MySQL Workbench query result screenshots look professional

### Example Report Section:

```
3.4 Query Implementation

3.4.1 Customer Order History Query

This query retrieves the complete order history for a specific customer, 
including order details, vendor information, and items ordered.

[Include SQL code here]

Figure 3.1 shows the output when executed for customer_id = 1. The results
indicate that the customer has placed 2 orders, one delivered (rated 5 stars)
and one currently being prepared.

[Include table/screenshot here]

Analysis: The query successfully demonstrates a complex multi-table JOIN
operation combining Orders, Vendors, Order_Items, and Menu_Items tables.
The GROUP_CONCAT function aggregates items into a readable format.
```

---

## Testing All Queries

Run this quick test to verify all queries work:

```sql
USE srmiggy_food_ordering;

-- Test each query
SELECT "Testing Query 1: Customer Order History" as Test;
-- [Paste Query 1 here]

SELECT "Testing Query 2: Vendor Revenue Summary" as Test;
-- [Paste Query 2 here]

-- ... continue for all 8 queries
```

All queries should execute without errors and return data similar to the examples above.

---

## Additional Analysis Queries

### Top Customers by Spending
```sql
SELECT 
    u.full_name,
    COUNT(o.order_id) as total_orders,
    SUM(o.final_amount) as total_spent
FROM Users u
JOIN Orders o ON u.user_id = o.customer_id
WHERE o.order_status = 'delivered'
GROUP BY u.user_id
ORDER BY total_spent DESC
LIMIT 5;
```

### Most Popular Cuisine
```sql
SELECT 
    v.cuisine_type,
    COUNT(o.order_id) as orders,
    SUM(o.final_amount) as revenue
FROM Vendors v
JOIN Orders o ON v.vendor_id = o.vendor_id
WHERE o.order_status = 'delivered'
GROUP BY v.cuisine_type
ORDER BY orders DESC;
```

### Peak Order Hours
```sql
SELECT 
    HOUR(order_date) as hour,
    COUNT(*) as order_count
FROM Orders
GROUP BY HOUR(order_date)
ORDER BY order_count DESC;
```

---

**Note**: Actual results may vary slightly based on the current timestamp when queries are run (especially for "minutes_since_order" and similar time-based calculations).

---

For more information, see:
- `srmiggy_food_ordering_schema.sql` - Full query code
- `PROJECT_DOCUMENTATION.md` - Query explanations
- `QUICK_START.md` - How to run queries
