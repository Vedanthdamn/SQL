# SRMiggy Online Food Ordering System - DBMS Project Documentation

## Table of Contents
1. [Project Overview](#project-overview)
2. [System Requirements](#system-requirements)
3. [Database Design](#database-design)
4. [Entity-Relationship (ER) Model](#entity-relationship-er-model)
5. [Normalization](#normalization)
6. [Table Descriptions](#table-descriptions)
7. [Relationships and Constraints](#relationships-and-constraints)
8. [Sample Queries and Use Cases](#sample-queries-and-use-cases)
9. [Installation and Usage](#installation-and-usage)

---

## Project Overview

### Purpose
SRMiggy is a campus-based online food ordering system designed for SRM University. The system enables students to order food from various campus restaurants, track their orders, and make payments through digital wallets or online payment methods.

### Key Features
- **User Management**: Support for customers, vendors, riders, and administrators
- **Menu Management**: Vendors can manage their food items with prices and availability
- **Order Processing**: Complete order lifecycle from placement to delivery
- **Payment System**: Multiple payment methods including wallet, UPI, cards, and net banking
- **Wallet Management**: Built-in wallet system with transaction history
- **Delivery Management**: Time slot-based delivery with rider assignment
- **Rating System**: Customer feedback for vendors and riders

### Stakeholders
1. **Customers (Students)**: Browse menus, place orders, make payments, track deliveries
2. **Vendors (Restaurant Owners)**: Manage menu items, view orders, track revenue
3. **Riders (Delivery Personnel)**: Accept and deliver orders, earn from deliveries
4. **Administrators**: Oversee system operations, manage users and vendors

---

## System Requirements

### Functional Requirements
1. User registration and authentication
2. Browse and search food items by vendor or cuisine
3. Add items to cart and place orders
4. Multiple payment methods support
5. Real-time order status tracking
6. Delivery slot selection
7. Rider assignment and management
8. Wallet recharge and transaction history
9. Rating and review system
10. Order history and analytics

### Non-Functional Requirements
1. **Performance**: Support concurrent users during peak hours
2. **Security**: Secure password storage, transaction security
3. **Reliability**: Data consistency and backup mechanisms
4. **Scalability**: Handle growing number of users and vendors
5. **Usability**: Intuitive interface for all user types

---

## Database Design

### Design Philosophy
The database is designed following **Third Normal Form (3NF)** principles to ensure:
- **Data Integrity**: No data redundancy or anomalies
- **Consistency**: All constraints properly enforced
- **Maintainability**: Easy to modify and extend
- **Performance**: Proper indexing for fast queries

### Technology Stack
- **Database Management System**: MySQL 5.7+
- **Storage Engine**: InnoDB (for transaction support and foreign keys)
- **Character Set**: UTF8MB4 (for international characters and emojis)

### Key Design Decisions

#### 1. Primary Keys
- All tables use **INT AUTO_INCREMENT** for primary keys
- Simple, efficient, and compatible with all MySQL versions
- Better performance than UUID for joins and indexing

#### 2. Foreign Keys
- All relationships enforced with foreign key constraints
- Appropriate ON DELETE and ON UPDATE actions:
  - **CASCADE**: Child records deleted with parent (e.g., Order_Items with Orders)
  - **RESTRICT**: Prevention of parent deletion if children exist (e.g., Orders with Users)
  - **SET NULL**: Relationship becomes optional (e.g., Riders with Orders)

#### 3. Data Types
- **VARCHAR**: For text fields with reasonable limits
- **TEXT**: For unlimited text (descriptions, addresses, reviews)
- **DECIMAL(10,2)**: For monetary values (prevents floating-point errors)
- **ENUM**: For fields with fixed options (status, roles, payment methods)
- **TIMESTAMP**: For date-time fields (automatic timezone handling)
- **BOOLEAN**: For true/false flags

#### 4. Constraints
- **NOT NULL**: Required fields
- **UNIQUE**: Email addresses, order-payment relationship
- **CHECK**: Value range validation (ratings, amounts, times)
- **DEFAULT**: Sensible defaults for optional fields

#### 5. Indexes
- Primary keys automatically indexed
- Foreign keys indexed for join performance
- Additional indexes on frequently queried fields (email, status, date)

---

## Entity-Relationship (ER) Model

### Entities

#### 1. Users
- **Attributes**: user_id (PK), full_name, email, phone_number, password_hash, user_role, wallet_balance, address, timestamps
- **Description**: Central entity representing all system users
- **Constraints**: Unique email, positive wallet balance

#### 2. Vendors
- **Attributes**: vendor_id (PK), owner_user_id (FK), restaurant_name, description, cuisine_type, opening_time, closing_time, ratings, images, timestamps
- **Description**: Restaurants operating on campus
- **Constraints**: Valid operating hours, rating range 0-5

#### 3. Menu_Items
- **Attributes**: menu_item_id (PK), vendor_id (FK), item_name, description, price, category, dietary_flags, availability, preparation_time, calories, timestamps
- **Description**: Food items offered by vendors
- **Constraints**: Positive price, valid preparation time

#### 4. Orders
- **Attributes**: order_id (PK), customer_id (FK), vendor_id (FK), delivery_slot_id (FK), rider_id (FK), order_status, amounts, delivery_address, timestamps, ratings
- **Description**: Customer orders with complete lifecycle tracking
- **Constraints**: Non-negative amounts, rating range 1-5

#### 5. Order_Items
- **Attributes**: order_item_id (PK), order_id (FK), menu_item_id (FK), quantity, unit_price, subtotal, special_requests
- **Description**: Junction table for order-menu item relationship
- **Constraints**: Positive quantity and prices

#### 6. Payment_Transactions
- **Attributes**: transaction_id (PK), order_id (FK), payment_method, payment_status, amount, timestamps, gateway_reference
- **Description**: Payment records for orders
- **Constraints**: Unique order_id (1:1 relationship), positive amount

#### 7. Wallet_Transactions
- **Attributes**: wallet_transaction_id (PK), user_id (FK), transaction_type, amount, balance_after, description, reference_order_id (FK), timestamps
- **Description**: Wallet transaction history
- **Constraints**: Positive amount, non-negative balance

#### 8. Delivery_Slots
- **Attributes**: slot_id (PK), slot_name, start_time, end_time, is_active, max_orders
- **Description**: Available delivery time windows
- **Constraints**: Start time before end time

#### 9. Riders
- **Attributes**: rider_id (PK), user_id (FK), vehicle_type, vehicle_number, license_number, availability, location, delivery_stats, ratings, timestamps
- **Description**: Delivery personnel information
- **Constraints**: Rating range 0-5, non-negative deliveries

### Relationships

#### One-to-Many (1:M)

1. **Users → Orders**
   - One customer can place many orders
   - Foreign Key: Orders.customer_id → Users.user_id
   - Cardinality: Mandatory on order side (every order must have a customer)

2. **Vendors → Menu_Items**
   - One vendor can have many menu items
   - Foreign Key: Menu_Items.vendor_id → Vendors.vendor_id
   - Cardinality: Mandatory on menu item side

3. **Vendors → Orders**
   - One vendor receives many orders
   - Foreign Key: Orders.vendor_id → Vendors.vendor_id
   - Cardinality: Mandatory on order side

4. **Users → Wallet_Transactions**
   - One user has many wallet transactions
   - Foreign Key: Wallet_Transactions.user_id → Users.user_id
   - Cardinality: Mandatory on transaction side

5. **Delivery_Slots → Orders**
   - One slot can have many orders
   - Foreign Key: Orders.delivery_slot_id → Delivery_Slots.slot_id
   - Cardinality: Optional on order side (can be null initially)

6. **Riders → Orders**
   - One rider delivers many orders
   - Foreign Key: Orders.rider_id → Riders.rider_id
   - Cardinality: Optional (order may not have rider assigned yet)

7. **Orders → Order_Items**
   - One order contains many items
   - Foreign Key: Order_Items.order_id → Orders.order_id
   - Cardinality: Mandatory on order item side

#### One-to-One (1:1)

1. **Orders ↔ Payment_Transactions**
   - Each order has exactly one payment
   - Foreign Key: Payment_Transactions.order_id → Orders.order_id (UNIQUE)
   - Cardinality: Mandatory on both sides

2. **Users ↔ Vendors**
   - Each vendor account linked to one user
   - Foreign Key: Vendors.owner_user_id → Users.user_id
   - Cardinality: Optional (user may not be a vendor)

3. **Users ↔ Riders**
   - Each rider account linked to one user
   - Foreign Key: Riders.user_id → Users.user_id
   - Cardinality: Optional (user may not be a rider)

#### Many-to-Many (M:N)

1. **Orders ↔ Menu_Items** (through Order_Items)
   - One order can have many menu items
   - One menu item can be in many orders
   - Junction Table: Order_Items
   - Foreign Keys: 
     - Order_Items.order_id → Orders.order_id
     - Order_Items.menu_item_id → Menu_Items.menu_item_id

---

## Normalization

### First Normal Form (1NF)
**Definition**: A relation is in 1NF if all attributes contain only atomic values.

**Compliance**:
- ✅ All tables have primary keys
- ✅ No multi-valued attributes
- ✅ No repeating groups
- ✅ Each cell contains single value

**Example**: 
- Instead of storing "items_ordered" as a comma-separated list in Orders, we use Order_Items junction table
- Phone numbers stored as single values, not multiple numbers in one field

### Second Normal Form (2NF)
**Definition**: A relation is in 2NF if it is in 1NF and all non-key attributes are fully dependent on the primary key.

**Compliance**:
- ✅ All tables are in 1NF
- ✅ No partial dependencies
- ✅ All non-key attributes depend on the entire primary key

**Example**:
- In Order_Items table, quantity and unit_price depend on both order_id AND menu_item_id
- We use a surrogate key (order_item_id) to simplify, but business key is still (order_id, menu_item_id)

### Third Normal Form (3NF)
**Definition**: A relation is in 3NF if it is in 2NF and no transitive dependencies exist.

**Compliance**:
- ✅ All tables are in 2NF
- ✅ No transitive dependencies
- ✅ All non-key attributes depend directly on the primary key only

**Examples**:

1. **Vendor Separation**
   - ❌ Bad: Store restaurant_name, cuisine_type in Users table
   - ✅ Good: Separate Vendors table with owner_user_id
   - Reason: Restaurant info depends on vendor, not directly on user

2. **Menu Items**
   - ❌ Bad: Store vendor_name in Menu_Items
   - ✅ Good: Store only vendor_id
   - Reason: Vendor name depends on vendor_id, not menu_item_id (transitive)

3. **Payment Transactions**
   - ❌ Bad: Store all payment info in Orders table
   - ✅ Good: Separate Payment_Transactions table
   - Reason: Payment details depend on payment, not order (though 1:1 relationship)

4. **Wallet Transactions**
   - ❌ Bad: Store transaction history as JSON in Users table
   - ✅ Good: Separate Wallet_Transactions table
   - Reason: Each transaction is independent, not dependent on user directly

### Denormalization Considerations
While the schema is in 3NF, we maintain some calculated fields for performance:
- `Users.wallet_balance`: Updated via transactions (alternative: calculate from Wallet_Transactions)
- `Vendors.average_rating`: Cached rating (alternative: calculate from Orders)
- `Order_Items.subtotal`: Calculated field (quantity × unit_price)

**Justification**: These are read-heavy fields, and denormalization improves query performance significantly. Data integrity maintained through application logic and triggers (if needed).

---

## Table Descriptions

### 1. Users Table
**Purpose**: Central authentication and profile management for all users.

**Key Fields**:
- `user_role`: ENUM distinguishes user types (customer, vendor, rider, admin)
- `wallet_balance`: Current balance for quick access
- `password_hash`: Encrypted password (never plain text)

**Usage**:
- Customer registration and login
- Vendor account management
- Rider profile tracking
- Admin access control

**Indexes**:
- `idx_email`: Fast login lookup
- `idx_user_role`: Filter users by role
- `idx_is_active`: Exclude inactive accounts

---

### 2. Vendors Table
**Purpose**: Restaurant profiles and operational information.

**Key Fields**:
- `owner_user_id`: Links to Users table (1:1)
- `opening_time`, `closing_time`: Operating hours
- `average_rating`: Performance metric
- `is_active`: Enable/disable vendor

**Usage**:
- Display restaurant list
- Check operating hours
- Filter by cuisine type
- Calculate ratings

**Business Rules**:
- Vendor must have associated user with role='vendor'
- Rating range: 0.00 to 5.00
- Must have valid operating hours (opening < closing)

---

### 3. Menu_Items Table
**Purpose**: Food items catalog with pricing and availability.

**Key Fields**:
- `vendor_id`: Associates item with restaurant
- `is_vegetarian`: Dietary filter
- `is_available`: Real-time stock management
- `preparation_time`: Delivery estimation

**Usage**:
- Display menu to customers
- Search by category or dietary preference
- Calculate order preparation time
- Track popular items

**Business Rules**:
- Price must be positive
- Item belongs to exactly one vendor
- Availability can change dynamically

---

### 4. Orders Table
**Purpose**: Order lifecycle management from placement to delivery.

**Key Fields**:
- `order_status`: Tracks order progress (7 states)
- `final_amount`: Total after discounts and delivery
- `customer_rating`: Post-delivery feedback
- Multiple timestamp fields for lifecycle tracking

**Usage**:
- Order placement and tracking
- Revenue calculation
- Performance metrics
- Customer service

**Business Rules**:
- Order must have customer, vendor
- Status progression: pending → confirmed → preparing → ready → out_for_delivery → delivered
- Can be cancelled at any stage before delivery
- Rating only after delivery (1-5 scale)

---

### 5. Order_Items Table
**Purpose**: Bridge table for order-menu item many-to-many relationship.

**Key Fields**:
- `unit_price`: Captures price at order time (important!)
- `quantity`: Number of items ordered
- `subtotal`: Calculated (quantity × unit_price)

**Usage**:
- Order composition
- Revenue calculation per item
- Historical pricing analysis

**Business Rules**:
- Quantity must be positive
- Unit price captured at order time (may differ from current menu price)
- Subtotal = quantity × unit_price

---

### 6. Payment_Transactions Table
**Purpose**: Payment record keeping and transaction management.

**Key Fields**:
- `payment_method`: How customer paid
- `payment_status`: Transaction state
- `payment_gateway_ref`: External system reference

**Usage**:
- Payment verification
- Refund processing
- Financial reporting
- Reconciliation

**Business Rules**:
- One payment per order (UNIQUE constraint)
- Amount must be positive
- Status transitions: pending → completed/failed
- Refunded status for cancellations

---

### 7. Wallet_Transactions Table
**Purpose**: Complete audit trail of wallet activities.

**Key Fields**:
- `transaction_type`: credit/debit/refund
- `balance_after`: Running balance snapshot
- `reference_order_id`: Links to order if applicable

**Usage**:
- Wallet balance reconciliation
- Transaction history display
- Refund tracking
- Financial auditing

**Business Rules**:
- Amount always positive (type indicates direction)
- Balance_after must be non-negative
- Credit: increases balance (recharge)
- Debit: decreases balance (order payment)
- Refund: increases balance (cancelled order)

---

### 8. Delivery_Slots Table
**Purpose**: Time window management for delivery scheduling.

**Key Fields**:
- `start_time`, `end_time`: Slot boundaries
- `max_orders`: Capacity management
- `is_active`: Enable/disable slots

**Usage**:
- Customer slot selection
- Delivery capacity planning
- Rider workload distribution

**Business Rules**:
- Start time < end time
- Max orders limit per slot
- Typical slots: Breakfast, Lunch, Evening, Dinner

---

### 9. Riders Table
**Purpose**: Delivery personnel information and performance tracking.

**Key Fields**:
- `user_id`: Links to Users table (1:1)
- `vehicle_type`, `vehicle_number`: Logistics
- `is_available`: Real-time availability
- `total_deliveries`: Performance metric

**Usage**:
- Rider assignment
- Performance tracking
- Availability checking
- Rating management

**Business Rules**:
- Rider must have associated user with role='rider'
- Rating range: 0.00 to 5.00
- Only available riders can be assigned

---

## Relationships and Constraints

### Referential Integrity

#### CASCADE (Delete child records automatically)
```sql
-- When a user is deleted, delete their vendors
Vendors.owner_user_id → Users.user_id (ON DELETE CASCADE)

-- When a vendor is deleted, delete their menu items
Menu_Items.vendor_id → Vendors.vendor_id (ON DELETE CASCADE)

-- When an order is deleted, delete its items
Order_Items.order_id → Orders.order_id (ON DELETE CASCADE)

-- When an order is deleted, delete its payment
Payment_Transactions.order_id → Orders.order_id (ON DELETE CASCADE)

-- When a user is deleted, delete their wallet transactions
Wallet_Transactions.user_id → Users.user_id (ON DELETE CASCADE)
```

#### RESTRICT (Prevent deletion if children exist)
```sql
-- Cannot delete a customer who has orders
Orders.customer_id → Users.user_id (ON DELETE RESTRICT)

-- Cannot delete a vendor who has orders
Orders.vendor_id → Vendors.vendor_id (ON DELETE RESTRICT)

-- Cannot delete a menu item that's in orders
Order_Items.menu_item_id → Menu_Items.menu_item_id (ON DELETE RESTRICT)
```

#### SET NULL (Make relationship optional)
```sql
-- If delivery slot deleted, orders keep working
Orders.delivery_slot_id → Delivery_Slots.slot_id (ON DELETE SET NULL)

-- If rider removed, orders can be reassigned
Orders.rider_id → Riders.rider_id (ON DELETE SET NULL)

-- If order deleted, wallet transaction keeps reference
Wallet_Transactions.reference_order_id → Orders.order_id (ON DELETE SET NULL)
```

### Check Constraints

```sql
-- Wallet balance cannot be negative
Users: CHECK (wallet_balance >= 0)

-- Ratings between 0 and 5
Vendors: CHECK (average_rating >= 0 AND average_rating <= 5)
Riders: CHECK (average_rating >= 0 AND average_rating <= 5)
Orders: CHECK (customer_rating >= 1 AND customer_rating <= 5)

-- Prices must be positive
Menu_Items: CHECK (price > 0)
Order_Items: CHECK (unit_price > 0 AND subtotal > 0)

-- Amounts must be non-negative
Orders: CHECK (total_amount >= 0 AND final_amount >= 0)
Payment_Transactions: CHECK (amount > 0)

-- Valid time ranges
Delivery_Slots: CHECK (start_time < end_time)
```

---

## Sample Queries and Use Cases

### Use Case 1: Customer Places Order
**Scenario**: Rahul wants to order food from South Indian Delight

```sql
-- Step 1: Browse available menu items
SELECT mi.*, v.restaurant_name, v.average_rating
FROM Menu_Items mi
JOIN Vendors v ON mi.vendor_id = v.vendor_id
WHERE mi.is_available = TRUE 
  AND v.is_active = TRUE
  AND v.vendor_id = 1;

-- Step 2: Create order
INSERT INTO Orders (customer_id, vendor_id, delivery_slot_id, total_amount, final_amount, delivery_address)
VALUES (1, 1, 3, 120.00, 130.00, 'Hostel Block A, Room 301');

-- Step 3: Add order items
INSERT INTO Order_Items (order_id, menu_item_id, quantity, unit_price, subtotal)
VALUES (LAST_INSERT_ID(), 6, 1, 120.00, 120.00);

-- Step 4: Create payment
INSERT INTO Payment_Transactions (order_id, payment_method, payment_status, amount)
VALUES (LAST_INSERT_ID(), 'wallet', 'completed', 130.00);

-- Step 5: Deduct from wallet and record transaction
UPDATE Users SET wallet_balance = wallet_balance - 130.00 WHERE user_id = 1;
INSERT INTO Wallet_Transactions (user_id, transaction_type, amount, balance_after, reference_order_id)
VALUES (1, 'debit', 130.00, (SELECT wallet_balance FROM Users WHERE user_id = 1), LAST_INSERT_ID());
```

### Use Case 2: Vendor Views Daily Orders
**Scenario**: Vendor wants to see today's orders

```sql
SELECT 
    o.order_id,
    o.order_date,
    u.full_name as customer_name,
    u.phone_number,
    o.order_status,
    o.final_amount,
    GROUP_CONCAT(CONCAT(oi.quantity, 'x ', mi.item_name) SEPARATOR ', ') as items
FROM Orders o
JOIN Users u ON o.customer_id = u.user_id
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Menu_Items mi ON oi.menu_item_id = mi.menu_item_id
WHERE o.vendor_id = 1 
  AND DATE(o.order_date) = CURDATE()
GROUP BY o.order_id, o.order_date, u.full_name, u.phone_number, o.order_status, o.final_amount
ORDER BY o.order_date DESC;
```

### Use Case 3: Admin Monitors System Performance
**Scenario**: Admin checks overall system metrics

```sql
-- Overall statistics
SELECT 
    (SELECT COUNT(*) FROM Users WHERE user_role = 'customer') as total_customers,
    (SELECT COUNT(*) FROM Vendors WHERE is_active = TRUE) as active_vendors,
    (SELECT COUNT(*) FROM Riders WHERE is_available = TRUE) as available_riders,
    (SELECT COUNT(*) FROM Orders WHERE DATE(order_date) = CURDATE()) as today_orders,
    (SELECT SUM(final_amount) FROM Orders WHERE order_status = 'delivered' AND DATE(order_date) = CURDATE()) as today_revenue;
```

### Use Case 4: Customer Recharges Wallet
**Scenario**: Customer adds money to wallet

```sql
-- Step 1: Update wallet balance
UPDATE Users 
SET wallet_balance = wallet_balance + 500.00 
WHERE user_id = 1;

-- Step 2: Record transaction
INSERT INTO Wallet_Transactions (user_id, transaction_type, amount, balance_after, description, payment_method)
VALUES (1, 'credit', 500.00, (SELECT wallet_balance FROM Users WHERE user_id = 1), 'Wallet recharge', 'upi');
```

### Use Case 5: Assign Rider to Order
**Scenario**: System assigns available rider to pending order

```sql
-- Find available rider
SELECT rider_id FROM Riders 
WHERE is_available = TRUE 
ORDER BY total_deliveries ASC 
LIMIT 1;

-- Assign rider and update order status
UPDATE Orders 
SET rider_id = 1, order_status = 'out_for_delivery' 
WHERE order_id = 5;

-- Update rider availability (optional)
UPDATE Riders 
SET is_available = FALSE 
WHERE rider_id = 1;
```

---

## Installation and Usage

### Prerequisites
- MySQL 5.7 or higher
- MySQL client or workbench
- Sufficient permissions to create databases

### Installation Steps

#### Method 1: MySQL Command Line
```bash
# Login to MySQL
mysql -u root -p

# Execute the schema file
source /path/to/srmiggy_food_ordering_schema.sql

# Verify installation
USE srmiggy_food_ordering;
SHOW TABLES;
```

#### Method 2: MySQL Workbench
1. Open MySQL Workbench
2. Connect to your MySQL server
3. Go to File → Open SQL Script
4. Select `srmiggy_food_ordering_schema.sql`
5. Click Execute (⚡ icon)
6. Verify in Navigator panel

#### Method 3: Command Line Direct
```bash
mysql -u root -p < srmiggy_food_ordering_schema.sql
```

### Verification Queries

```sql
-- Check all tables created
USE srmiggy_food_ordering;
SHOW TABLES;

-- Verify sample data
SELECT COUNT(*) as user_count FROM Users;
SELECT COUNT(*) as vendor_count FROM Vendors;
SELECT COUNT(*) as menu_items FROM Menu_Items;
SELECT COUNT(*) as orders FROM Orders;

-- Test a complex query
SELECT v.restaurant_name, COUNT(o.order_id) as total_orders
FROM Vendors v
LEFT JOIN Orders o ON v.vendor_id = o.vendor_id
GROUP BY v.vendor_id, v.restaurant_name;
```

### Testing the Schema

```sql
-- Test Order Creation Flow
START TRANSACTION;

-- Create test order
INSERT INTO Orders (customer_id, vendor_id, total_amount, final_amount, delivery_address)
VALUES (1, 1, 100.00, 100.00, 'Test Address');

SET @new_order_id = LAST_INSERT_ID();

-- Add order items
INSERT INTO Order_Items (order_id, menu_item_id, quantity, unit_price, subtotal)
VALUES (@new_order_id, 1, 2, 60.00, 120.00);

-- Create payment
INSERT INTO Payment_Transactions (order_id, payment_method, payment_status, amount)
VALUES (@new_order_id, 'wallet', 'pending', 100.00);

-- Verify
SELECT * FROM Orders WHERE order_id = @new_order_id;
SELECT * FROM Order_Items WHERE order_id = @new_order_id;
SELECT * FROM Payment_Transactions WHERE order_id = @new_order_id;

-- Rollback (if testing)
ROLLBACK;
```

### Common Operations

#### 1. Add New Menu Item
```sql
INSERT INTO Menu_Items (vendor_id, item_name, description, price, category, is_vegetarian)
VALUES (1, 'New Item', 'Description', 99.00, 'Category', TRUE);
```

#### 2. Update Order Status
```sql
UPDATE Orders 
SET order_status = 'confirmed', confirmed_at = NOW() 
WHERE order_id = 1;
```

#### 3. Get Customer Order History
```sql
SELECT o.*, v.restaurant_name
FROM Orders o
JOIN Vendors v ON o.vendor_id = v.vendor_id
WHERE o.customer_id = 1
ORDER BY o.order_date DESC;
```

#### 4. Calculate Vendor Revenue
```sql
SELECT 
    v.restaurant_name,
    SUM(o.final_amount) as total_revenue,
    COUNT(o.order_id) as order_count
FROM Vendors v
JOIN Orders o ON v.vendor_id = o.vendor_id
WHERE o.order_status = 'delivered'
GROUP BY v.vendor_id, v.restaurant_name;
```

---

## Appendix

### ER Diagram Guidelines
When creating an ER diagram for this schema:

1. **Entities**: Draw rectangles for all 9 tables
2. **Attributes**: List key attributes inside entities
3. **Primary Keys**: Underline primary key attributes
4. **Relationships**: Use diamonds or crow's foot notation
5. **Cardinality**: Mark 1, M, or N on relationship lines
6. **Weak Entities**: None in this schema (all have independent keys)

### Report Writing Tips

#### Chapter 1: Introduction
- System overview
- Objectives and scope
- Stakeholder identification
- Features list
- Technology stack

#### Chapter 2: Database Design
- ER diagram with explanation
- Entity descriptions
- Relationship descriptions
- Attribute data types
- Constraint explanation
- Normalization proof (1NF, 2NF, 3NF)

#### Chapter 3: Implementation
- SQL schema code
- Sample data
- Query examples
- Testing results

#### Chapter 4: Conclusion
- Achievement summary
- Future enhancements
- Lessons learned

### Future Enhancements
1. **Promo Codes**: Discount management system
2. **Loyalty Points**: Reward system for frequent customers
3. **Real-time Tracking**: GPS integration for order tracking
4. **Analytics Dashboard**: Business intelligence reports
5. **Notifications**: Email/SMS alerts for order updates
6. **Reviews**: Detailed review system with images
7. **Favorites**: Customer favorite items and restaurants
8. **Scheduled Orders**: Pre-order for future delivery
9. **Group Orders**: Split bills and shared carts
10. **Vendor Analytics**: Detailed sales reports and insights

---

## Contact and Support
For questions or issues with this database schema, please refer to the SQL comments in the schema file or consult your DBMS instructor.

**Author**: DBMS Project Team  
**Date**: February 2024  
**Version**: 1.0  
**License**: Academic Use Only
