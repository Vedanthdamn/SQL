# Quick Start Guide - SRMiggy Food Ordering System

## For Students: Getting Started in 5 Minutes

### Step 1: Import the Schema (Choose one method)

#### Option A: MySQL Command Line
```bash
mysql -u root -p < srmiggy_food_ordering_schema.sql
```
Enter your MySQL password when prompted.

#### Option B: MySQL Workbench
1. Open MySQL Workbench
2. Connect to your server
3. File → Open SQL Script → Select `srmiggy_food_ordering_schema.sql`
4. Click Execute (⚡ lightning icon)

#### Option C: phpMyAdmin
1. Open phpMyAdmin in browser
2. Click "Import" tab
3. Choose file: `srmiggy_food_ordering_schema.sql`
4. Click "Go"

---

### Step 2: Verify Installation

Run this in MySQL:
```sql
USE srmiggy_food_ordering;
SHOW TABLES;
```

You should see 9 tables:
- Users
- Vendors
- Menu_Items
- Delivery_Slots
- Riders
- Orders
- Order_Items
- Payment_Transactions
- Wallet_Transactions

---

### Step 3: Test with Sample Queries

#### View all customers:
```sql
SELECT user_id, full_name, email, wallet_balance 
FROM Users 
WHERE user_role = 'customer';
```

#### View all restaurants:
```sql
SELECT v.restaurant_name, v.cuisine_type, v.average_rating, u.full_name as owner
FROM Vendors v
JOIN Users u ON v.owner_user_id = u.user_id;
```

#### View complete menu:
```sql
SELECT v.restaurant_name, mi.item_name, mi.price, mi.category
FROM Menu_Items mi
JOIN Vendors v ON mi.vendor_id = v.vendor_id
WHERE mi.is_available = TRUE
ORDER BY v.restaurant_name, mi.category;
```

---

### Step 4: Run Advanced Queries

All 8 analytical queries are already in the schema file. Open the file and look for sections like:

```sql
-- QUERY 1: Customer Order History
-- QUERY 2: Vendor Revenue Summary
-- QUERY 3: Wallet Transaction Report
-- QUERY 4: Current Pending Orders
-- QUERY 5: Most Ordered Food Items
-- QUERY 6: Rider Performance Report
-- QUERY 7: Daily Revenue Report
-- QUERY 8: Customer Wallet Balance Summary
```

Copy and execute any query directly!

---

### Step 5: Generate ER Diagram

See `ER_DIAGRAM_GUIDE.md` for detailed instructions.

**Quick method with MySQL Workbench:**
1. Database → Reverse Engineer
2. Select `srmiggy_food_ordering` database
3. Follow wizard → Your ER diagram is ready!
4. Export as PNG for your report

---

## Understanding the Database

### Core Entities and Their Purpose

| Table | Purpose | Key Relationships |
|-------|---------|-------------------|
| **Users** | All system users | Central table, connects to Orders, Vendors, Riders, Wallet |
| **Vendors** | Restaurants | Links to Users (owner), has Menu_Items |
| **Menu_Items** | Food catalog | Belongs to Vendor, appears in Orders |
| **Orders** | Customer orders | Links Customer, Vendor, Rider, Delivery_Slot |
| **Order_Items** | Items in order | Bridge between Orders and Menu_Items (M:N) |
| **Payment_Transactions** | Payment records | One payment per order |
| **Wallet_Transactions** | Wallet history | User's wallet activity log |
| **Delivery_Slots** | Time windows | Available delivery slots |
| **Riders** | Delivery staff | Links to Users, delivers Orders |

### Relationships Explained

```
Customer (User) → places → Order → contains → Menu_Items
Order → paid_by → Payment_Transaction
Order → delivered_in → Delivery_Slot
Order → delivered_by → Rider
User → has → Wallet_Transactions
Vendor → offers → Menu_Items
```

---

## Common Tasks

### 1. Add a New Menu Item
```sql
INSERT INTO Menu_Items (vendor_id, item_name, description, price, category, is_vegetarian)
VALUES (1, 'Filter Coffee', 'South Indian style filter coffee', 30.00, 'Beverages', TRUE);
```

### 2. Place a New Order
```sql
-- Step 1: Create order
INSERT INTO Orders (customer_id, vendor_id, total_amount, final_amount, delivery_address)
VALUES (1, 1, 90.00, 110.00, 'Hostel Block A, Room 301');

-- Step 2: Add items to order
INSERT INTO Order_Items (order_id, menu_item_id, quantity, unit_price, subtotal)
VALUES (LAST_INSERT_ID(), 1, 1, 60.00, 60.00);

-- Step 3: Record payment
INSERT INTO Payment_Transactions (order_id, payment_method, payment_status, amount)
VALUES (LAST_INSERT_ID(), 'wallet', 'completed', 110.00);
```

### 3. Check Customer Order History
```sql
SELECT o.order_id, o.order_date, v.restaurant_name, o.order_status, o.final_amount
FROM Orders o
JOIN Vendors v ON o.vendor_id = v.vendor_id
WHERE o.customer_id = 1
ORDER BY o.order_date DESC;
```

### 4. Calculate Vendor Revenue
```sql
SELECT 
    v.restaurant_name,
    COUNT(o.order_id) as total_orders,
    SUM(o.final_amount) as total_revenue
FROM Vendors v
LEFT JOIN Orders o ON v.vendor_id = o.vendor_id
WHERE o.order_status = 'delivered'
GROUP BY v.vendor_id
ORDER BY total_revenue DESC;
```

### 5. View Wallet Balance
```sql
SELECT 
    u.full_name,
    u.wallet_balance,
    COUNT(wt.wallet_transaction_id) as total_transactions
FROM Users u
LEFT JOIN Wallet_Transactions wt ON u.user_id = wt.user_id
WHERE u.user_role = 'customer'
GROUP BY u.user_id
ORDER BY u.wallet_balance DESC;
```

---

## For Your Project Report

### Chapter 1: Introduction
- **Project Title**: SRMiggy Online Food Ordering System
- **Purpose**: Campus food ordering platform
- **Stakeholders**: Customers, Vendors, Riders, Admins
- **Features**: List from README.md

### Chapter 2: Database Design
- **ER Diagram**: Generate using MySQL Workbench
- **Tables**: All 9 tables with descriptions
- **Relationships**: Explain each relationship with cardinality
- **Normalization**: Mention 3NF compliance

### Chapter 3: Implementation
- **SQL Code**: Include table creation statements
- **Sample Data**: Show INSERT statements
- **Queries**: Include 2-3 key queries with results

### Chapter 4: Screenshots
- MySQL Workbench with schema
- ER Diagram
- Query results
- Sample data in tables

### Chapter 5: Conclusion
- Achievement summary
- Challenges faced
- Future enhancements

---

## Troubleshooting

### Problem: Can't connect to MySQL
**Solution**: 
```bash
sudo service mysql start
mysql -u root -p
```

### Problem: Access denied error
**Solution**: Reset MySQL password or use:
```bash
sudo mysql
```

### Problem: Foreign key constraint fails
**Solution**: Import schema in clean database:
```sql
DROP DATABASE IF EXISTS srmiggy_food_ordering;
CREATE DATABASE srmiggy_food_ordering;
```
Then re-import schema.

### Problem: Character encoding issues
**Solution**: Ensure UTF8MB4 support:
```sql
SET NAMES utf8mb4;
SET CHARACTER SET utf8mb4;
```

---

## Testing Checklist

Before submitting your project, verify:

- [ ] All 9 tables created successfully
- [ ] Sample data imported (check with SELECT * FROM Users)
- [ ] Foreign keys working (check with SHOW CREATE TABLE Orders)
- [ ] All 8 queries execute without errors
- [ ] ER diagram generated and exported
- [ ] Documentation reviewed
- [ ] Screenshots taken
- [ ] Report written
- [ ] Code formatted and commented

---

## Quick Reference: Table Sizes

| Table | Approx Rows | Purpose |
|-------|-------------|---------|
| Users | 12 | Sample users (customers, vendors, riders, admin) |
| Vendors | 3 | Sample restaurants |
| Menu_Items | 18 | Sample food items (6 per vendor) |
| Delivery_Slots | 6 | Time windows for delivery |
| Riders | 3 | Sample delivery personnel |
| Orders | 8 | Sample orders (completed, active, cancelled) |
| Order_Items | 15 | Items in orders |
| Payment_Transactions | 8 | One per order |
| Wallet_Transactions | 13 | User wallet history |

---

## Need More Help?

1. **Schema Details**: Open `srmiggy_food_ordering_schema.sql` - it has extensive comments
2. **Full Documentation**: Read `PROJECT_DOCUMENTATION.md`
3. **ER Diagram**: See `ER_DIAGRAM_GUIDE.md`
4. **README**: Check `README.md` for overview

---

## Quick Command Reference

```bash
# Start MySQL
sudo service mysql start

# Login to MySQL
mysql -u root -p

# Import schema
mysql -u root -p < srmiggy_food_ordering_schema.sql

# Backup database
mysqldump -u root -p srmiggy_food_ordering > backup.sql

# Restore database
mysql -u root -p srmiggy_food_ordering < backup.sql

# Validate schema
./validate_schema.sh
```

---

**Good luck with your DBMS project! 🎓✨**

If everything works, give this repo a ⭐ star!
