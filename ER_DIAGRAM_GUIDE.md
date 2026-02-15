# ER Diagram Generation Guide for SRMiggy Food Ordering System

This guide explains how to generate Entity-Relationship (ER) diagrams from the SRMiggy MySQL schema for your DBMS project report.

## 📊 Table of Contents
1. [Using MySQL Workbench (Recommended)](#method-1-mysql-workbench-recommended)
2. [Using Online Tools](#method-2-online-tools)
3. [Using dbdiagram.io](#method-3-dbdiagramio)
4. [Manual Drawing](#method-4-manual-drawing)
5. [ER Diagram Requirements](#er-diagram-requirements)

---

## Method 1: MySQL Workbench (Recommended)

MySQL Workbench can automatically generate ER diagrams from your database schema.

### Steps:

1. **Import the Schema**
   ```bash
   # First, create the database
   mysql -u root -p < srmiggy_food_ordering_schema.sql
   ```

2. **Open MySQL Workbench**
   - Launch MySQL Workbench
   - Connect to your MySQL server

3. **Reverse Engineer the Database**
   - Go to **Database** → **Reverse Engineer**
   - Select your connection
   - Click **Next**
   - Select the `srmiggy_food_ordering` database
   - Click **Next** through the wizard
   - Click **Execute**

4. **View the ER Diagram**
   - The ER diagram will be automatically generated
   - You'll see all tables with their columns and relationships

5. **Customize the Diagram**
   - **Rearrange tables**: Drag tables to organize layout
   - **Show/hide details**: Right-click on tables to toggle visibility
   - **Adjust colors**: Use the Properties panel to customize colors
   - **Add notes**: Add text boxes to explain relationships

6. **Export the Diagram**
   - Go to **File** → **Export**
   - Choose format:
     - **PNG**: For Word documents (recommended for reports)
     - **PDF**: High quality for printing
     - **SVG**: For scalable graphics
   
   Recommended settings for report:
   - Format: PNG
   - Resolution: 300 DPI
   - Size: A4 or Letter

### Tips for Better Diagrams in Workbench:
- **Zoom**: Use Ctrl+Mouse Wheel to zoom in/out
- **Grid**: Enable snap-to-grid for alignment (View → Grid)
- **Layers**: Group related tables together
- **Labels**: Add relationship labels to explain connections

---

## Method 2: Online Tools

Several online tools can generate ER diagrams from SQL or MySQL dumps.

### Option A: dbdocs.io
Website: https://dbdocs.io/

**Steps:**
1. Visit dbdocs.io
2. Sign up for a free account
3. Create a new project
4. Paste the DBML format (see conversion below)
5. View and export the diagram

### Option B: QuickDBD
Website: https://www.quickdatabasediagrams.com/

**Steps:**
1. Visit QuickDBD
2. Define tables in their syntax format
3. Click **Export** → **Export Diagram**
4. Choose PNG or PDF format

### Option C: SchemaSpy
Website: http://schemaspy.org/

**Steps:**
1. Download SchemaSpy JAR
2. Run against your database:
   ```bash
   java -jar schemaspy.jar -t mysql -db srmiggy_food_ordering \
        -u root -p password -o output_directory
   ```
3. Open `output_directory/index.html` to view diagrams

---

## Method 3: dbdiagram.io

dbdiagram.io is an excellent free tool for creating ER diagrams.

### Steps:

1. **Visit** https://dbdiagram.io/

2. **Paste the following DBML code:**

```dbml
// SRMiggy Food Ordering System ER Diagram

Table Users {
  user_id int [pk, increment]
  full_name varchar(100)
  email varchar(100) [unique]
  phone_number varchar(15)
  password_hash varchar(255)
  user_role enum
  wallet_balance decimal(10,2)
  address text
  registration_date timestamp
  is_active boolean
}

Table Vendors {
  vendor_id int [pk, increment]
  owner_user_id int [ref: > Users.user_id]
  restaurant_name varchar(150)
  description text
  cuisine_type varchar(100)
  opening_time time
  closing_time time
  average_rating decimal(3,2)
  total_ratings int
  is_active boolean
}

Table Menu_Items {
  menu_item_id int [pk, increment]
  vendor_id int [ref: > Vendors.vendor_id]
  item_name varchar(150)
  description text
  price decimal(10,2)
  category varchar(50)
  is_vegetarian boolean
  is_available boolean
  preparation_time int
  calories int
}

Table Delivery_Slots {
  slot_id int [pk, increment]
  slot_name varchar(50)
  start_time time
  end_time time
  is_active boolean
  max_orders int
}

Table Riders {
  rider_id int [pk, increment]
  user_id int [ref: > Users.user_id]
  vehicle_type varchar(50)
  vehicle_number varchar(20)
  license_number varchar(50)
  is_available boolean
  total_deliveries int
  average_rating decimal(3,2)
  total_ratings int
}

Table Orders {
  order_id int [pk, increment]
  customer_id int [ref: > Users.user_id]
  vendor_id int [ref: > Vendors.vendor_id]
  delivery_slot_id int [ref: > Delivery_Slots.slot_id]
  rider_id int [ref: > Riders.rider_id]
  order_status enum
  total_amount decimal(10,2)
  delivery_fee decimal(10,2)
  discount_amount decimal(10,2)
  final_amount decimal(10,2)
  delivery_address text
  order_date timestamp
  customer_rating int
}

Table Order_Items {
  order_item_id int [pk, increment]
  order_id int [ref: > Orders.order_id]
  menu_item_id int [ref: > Menu_Items.menu_item_id]
  quantity int
  unit_price decimal(10,2)
  subtotal decimal(10,2)
}

Table Payment_Transactions {
  transaction_id int [pk, increment]
  order_id int [unique, ref: - Orders.order_id]
  payment_method enum
  payment_status enum
  amount decimal(10,2)
  transaction_date timestamp
}

Table Wallet_Transactions {
  wallet_transaction_id int [pk, increment]
  user_id int [ref: > Users.user_id]
  transaction_type enum
  amount decimal(10,2)
  balance_after decimal(10,2)
  description varchar(255)
  reference_order_id int [ref: > Orders.order_id]
  transaction_date timestamp
}
```

3. **Export the Diagram**
   - Click **Export** (top right)
   - Choose format: PNG, PDF, or SVG
   - Use in your report

---

## Method 4: Manual Drawing

For complete control, draw the ER diagram manually.

### Recommended Tools:
1. **draw.io (diagrams.net)** - Free, web-based
   - Website: https://app.diagrams.net/
   - Templates for ER diagrams available
   - Export to PNG, PDF, SVG

2. **Lucidchart** - Professional tool
   - Website: https://www.lucidchart.com/
   - Free for students
   - ER diagram templates included

3. **Microsoft Visio** - Industry standard
   - Part of Microsoft Office
   - Professional templates
   - Best for detailed diagrams

4. **PowerPoint or Google Slides** - Simple option
   - Use shapes and connectors
   - Good for basic diagrams
   - Easy to edit and share

### Manual Drawing Steps:

1. **Create Entities (Rectangles)**
   - Draw a rectangle for each table
   - Write table name at the top
   - List primary key attributes (underlined)
   - List other important attributes

2. **Add Relationships (Lines/Diamonds)**
   - Draw lines between related entities
   - Add diamonds for relationships (optional)
   - Label relationships (e.g., "places", "contains")

3. **Indicate Cardinality**
   - Use crow's foot notation:
     - `|` = One
     - `>` = Many
     - `O` = Zero (optional)
   - Examples:
     - `Users |----< Orders` = One user to many orders
     - `Orders >----< Menu_Items` = Many-to-many

4. **Add Attributes**
   - List key attributes in each entity
   - Underline primary keys
   - Mark foreign keys with (FK)

---

## ER Diagram Requirements

### Must Include:

1. **All 9 Entities**
   - Users
   - Vendors
   - Menu_Items
   - Orders
   - Order_Items
   - Payment_Transactions
   - Wallet_Transactions
   - Delivery_Slots
   - Riders

2. **All Relationships**
   - Users → Orders (1:M)
   - Users → Wallet_Transactions (1:M)
   - Users ← Vendors (1:1)
   - Users ← Riders (1:1)
   - Vendors → Menu_Items (1:M)
   - Vendors → Orders (1:M)
   - Orders → Order_Items (1:M)
   - Orders ← Payment_Transactions (1:1)
   - Orders → Delivery_Slots (M:1)
   - Orders → Riders (M:1)
   - Menu_Items → Order_Items (1:M)

3. **Key Attributes**
   - Primary keys (underlined)
   - Important foreign keys
   - Key business attributes

4. **Cardinality Notation**
   - One-to-One (1:1)
   - One-to-Many (1:M)
   - Many-to-Many (M:N)

### Suggested Layout:

```
                     Users (Central)
                        |
        +---------------+---------------+
        |               |               |
     Vendors         Orders          Riders
        |               |               |
   Menu_Items      Order_Items   Delivery_Slots
        |               |
        +-------+-------+
                |
        Payment_Transactions
                |
        Wallet_Transactions
```

---

## ER Diagram Symbols Reference

### Chen Notation:
- **Rectangle**: Entity
- **Diamond**: Relationship
- **Oval**: Attribute
- **Underline**: Primary Key
- **Double Oval**: Multi-valued attribute
- **Dashed Oval**: Derived attribute

### Crow's Foot Notation (Modern):
- **Rectangle**: Entity
- **Line**: Relationship
- **|**: One (exactly one)
- **O**: Zero (optional)
- **<**: Many
- **Crow's foot**: Many (looks like bird foot)

Example:
```
Users ||----O{ Orders
(One user has zero or many orders)

Orders ||----|| Payment_Transactions  
(One order has exactly one payment)

Orders }O----|| Vendors
(Many orders belong to one vendor)
```

---

## Tips for Academic Reports

### 1. Diagram Quality
- **Resolution**: At least 300 DPI for print
- **Size**: Full page or half page (not too small)
- **Clarity**: All text must be readable
- **Colors**: Use colors to group related entities

### 2. Labeling
- Add title: "ER Diagram for SRMiggy Food Ordering System"
- Add legend explaining notation used
- Label all relationships with verb phrases
- Include cardinality on all relationships

### 3. Explanation Text
After the diagram, include:
- Brief description of each entity
- Explanation of relationships
- Justification for design decisions
- Note about normalization (3NF)

### 4. Multiple Views (Optional)
Consider creating:
1. **Conceptual ER Diagram**: High-level entities only
2. **Logical ER Diagram**: Full detail with all attributes
3. **Physical Schema Diagram**: With data types and constraints

### Example Caption:
> *Figure 2.1: Entity-Relationship Diagram for SRMiggy Food Ordering System showing 9 entities and their relationships. The diagram follows Crow's Foot notation where single line indicates 'one' and crow's foot indicates 'many'. All relationships maintain referential integrity through foreign key constraints.*

---

## Verification Checklist

Before submitting your ER diagram, verify:

- [ ] All 9 entities are present
- [ ] All relationships are shown
- [ ] Cardinality is marked on all relationships
- [ ] Primary keys are clearly indicated
- [ ] Important foreign keys are shown
- [ ] Diagram is properly labeled
- [ ] Legend/notation key is included
- [ ] Image quality is suitable for printing
- [ ] Diagram matches the SQL schema
- [ ] All entity names match table names

---

## Common Mistakes to Avoid

1. **Missing Relationships**: Don't forget optional relationships (rider_id in Orders)
2. **Wrong Cardinality**: Verify 1:1, 1:M, M:N correctly
3. **Poor Layout**: Avoid crossing lines when possible
4. **Too Much Detail**: Don't list every attribute in the diagram
5. **No Legend**: Always include notation explanation
6. **Low Quality**: Ensure diagram is clear and readable

---

## Example Description for Report

### Sample Text to Include with ER Diagram:

> **2.2 Entity-Relationship Model**
>
> The SRMiggy Food Ordering System database consists of 9 entities with clearly defined relationships:
>
> **Core Entities:**
> - **Users**: Central entity representing all system users (customers, vendors, riders, and admins). Uses a role-based design to handle multiple user types in a single table.
> - **Orders**: Represents customer orders with complete lifecycle tracking from placement to delivery.
>
> **Supporting Entities:**
> - **Vendors**: Restaurant information linked to user accounts
> - **Menu_Items**: Food catalog managed by vendors
> - **Order_Items**: Junction table implementing the many-to-many relationship between Orders and Menu_Items
> - **Payment_Transactions**: Payment records with 1:1 relationship to Orders
> - **Wallet_Transactions**: Maintains complete audit trail of wallet activities
> - **Delivery_Slots**: Time window management for delivery scheduling
> - **Riders**: Delivery personnel information and performance tracking
>
> **Key Relationships:**
> 1. A customer (User) can place multiple Orders (1:M)
> 2. Each Order belongs to exactly one Vendor (M:1)
> 3. Orders and Menu_Items have a many-to-many relationship through Order_Items
> 4. Each Order has exactly one Payment_Transaction (1:1)
> 5. Riders can be assigned to multiple Orders (1:M)
>
> The design ensures referential integrity through foreign key constraints and maintains data consistency across all operations.

---

## Additional Resources

### Learning ER Diagrams:
- [ER Diagram Tutorial by Lucidchart](https://www.lucidchart.com/pages/er-diagrams)
- [Database Design Course on Coursera](https://www.coursera.org/courses?query=database%20design)
- [ER Diagram Symbols Guide](https://www.smartdraw.com/entity-relationship-diagram/)

### Tools Documentation:
- [MySQL Workbench Manual](https://dev.mysql.com/doc/workbench/en/)
- [dbdiagram.io Documentation](https://dbdiagram.io/docs)
- [draw.io Examples](https://www.diagrams.net/example-diagrams)

---

## Questions?

If you have questions about generating ER diagrams:
1. Review the PROJECT_DOCUMENTATION.md for entity details
2. Check the SQL schema file comments
3. Refer to your DBMS textbook chapter on ER modeling
4. Consult with your project guide/faculty

---

**Good luck with your DBMS project! 🎓**
