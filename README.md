# SRMiggy Online Food Ordering System - MySQL Database Schema

A complete MySQL database schema for a campus-based online food ordering system, designed for DBMS academic project submission.

## 🎯 Project Overview

SRMiggy is a comprehensive food ordering platform for SRM University campus, featuring:
- Multi-role user management (customers, vendors, riders, admins)
- Complete order lifecycle management
- Integrated wallet system
- Delivery slot scheduling
- Payment processing
- Rating and review system

## 📁 Repository Contents

- **`srmiggy_food_ordering_schema.sql`** - Complete MySQL schema with:
  - Database creation
  - 9 normalized tables (3NF compliant)
  - Comprehensive sample data
  - 8+ useful analytical queries
  - Detailed comments explaining design decisions

- **`PROJECT_DOCUMENTATION.md`** - Detailed documentation including:
  - System requirements
  - ER model explanation
  - Normalization proof
  - Table descriptions
  - Relationship mappings
  - Installation guide
  - Use cases and examples

- **`ER_DIAGRAM_GUIDE.md`** - Instructions for generating ER diagrams from the schema

## 🚀 Quick Start

### Prerequisites
- MySQL 5.7 or higher
- MySQL Workbench (optional, for GUI)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Vedanthdamn/SQL.git
   cd SQL
   ```

2. **Import the schema**
   ```bash
   mysql -u root -p < srmiggy_food_ordering_schema.sql
   ```

3. **Verify installation**
   ```bash
   mysql -u root -p
   USE srmiggy_food_ordering;
   SHOW TABLES;
   ```

## 📊 Database Schema

### Tables (9)
1. **Users** - All system users (customers, vendors, riders, admins)
2. **Vendors** - Restaurant information
3. **Menu_Items** - Food items catalog
4. **Orders** - Order management
5. **Order_Items** - Order-menu junction table
6. **Payment_Transactions** - Payment records
7. **Wallet_Transactions** - Wallet transaction history
8. **Delivery_Slots** - Delivery time windows
9. **Riders** - Delivery personnel information

### Key Features
- ✅ **MySQL Compatible** - No PostgreSQL/Supabase features
- ✅ **3NF Normalized** - Eliminates redundancy
- ✅ **INT AUTO_INCREMENT PKs** - Simple and efficient
- ✅ **Foreign Key Constraints** - Referential integrity
- ✅ **Check Constraints** - Data validation
- ✅ **Comprehensive Indexes** - Optimized queries
- ✅ **Sample Data** - Ready for testing
- ✅ **Analytical Queries** - Business intelligence

## 📈 Entity Relationships

```
Users (1) ----< (M) Orders
Users (1) ----< (M) Wallet_Transactions
Users (1) ---- (1) Vendors
Users (1) ---- (1) Riders

Vendors (1) ----< (M) Menu_Items
Vendors (1) ----< (M) Orders

Orders (1) ----< (M) Order_Items
Orders (1) ---- (1) Payment_Transactions
Orders (M) >---- (1) Delivery_Slots
Orders (M) >---- (1) Riders

Menu_Items (1) ----< (M) Order_Items
```

## 💡 Sample Queries Included

1. **Customer Order History** - Complete order tracking
2. **Vendor Revenue Summary** - Sales analytics
3. **Wallet Transaction Report** - Financial tracking
4. **Pending Orders** - Operations dashboard
5. **Most Ordered Items** - Popularity analysis
6. **Rider Performance** - Delivery metrics
7. **Daily Revenue Report** - Financial insights
8. **Customer Wallet Balance** - Balance summary

## 🎓 Academic Submission Ready

This schema is specifically designed for DBMS academic projects:
- ✅ Faculty-approved MySQL syntax only
- ✅ Complete normalization proof (1NF, 2NF, 3NF)
- ✅ Detailed comments for report writing
- ✅ ER diagram can be easily generated
- ✅ Suitable for Chapter 1 & 2 of project report
- ✅ Includes all required entities and relationships
- ✅ Sample data for demonstrations

## 📖 Documentation

For detailed documentation, see:
- [`PROJECT_DOCUMENTATION.md`](PROJECT_DOCUMENTATION.md) - Complete project documentation
- [`ER_DIAGRAM_GUIDE.md`](ER_DIAGRAM_GUIDE.md) - ER diagram generation guide
- SQL file comments - Inline explanations of design decisions

## 🔧 Testing the Schema

Run the included queries to test functionality:

```sql
-- Use the database
USE srmiggy_food_ordering;

-- Test customer order history
SELECT o.order_id, o.order_date, v.restaurant_name, o.final_amount
FROM Orders o
JOIN Vendors v ON o.vendor_id = v.vendor_id
WHERE o.customer_id = 1
ORDER BY o.order_date DESC;

-- Test vendor revenue
SELECT v.restaurant_name, SUM(o.final_amount) as revenue
FROM Vendors v
JOIN Orders o ON v.vendor_id = o.vendor_id
WHERE o.order_status = 'delivered'
GROUP BY v.vendor_id;
```

## 🏗️ System Architecture

### Design Principles
- **Normalization**: 3NF compliance ensures no data redundancy
- **Referential Integrity**: All relationships enforced via foreign keys
- **Data Validation**: Check constraints prevent invalid data
- **Performance**: Strategic indexing for fast queries
- **Scalability**: Design supports growth in users and transactions

### Technology Choices
- **Database**: MySQL (InnoDB engine)
- **Primary Keys**: INT AUTO_INCREMENT
- **Character Set**: UTF8MB4 (supports emojis)
- **Data Types**: Appropriate types for each field (DECIMAL for money, ENUM for fixed options)

## 📝 Project Report Tips

### Chapter 1: Introduction
- Use the project overview from documentation
- Include stakeholder analysis
- List all features and objectives

### Chapter 2: Database Design
- Include the ER diagram (generate using MySQL Workbench)
- Explain each entity and relationship
- Provide normalization proof with examples
- Document all constraints and rules

### Chapter 3: Implementation
- Include SQL schema code
- Show sample data
- Demonstrate queries with results
- Include screenshots of MySQL Workbench

## 🔒 Security Considerations

- Passwords stored as hashed values (never plain text)
- Wallet balance constraints prevent negative values
- Check constraints validate data ranges
- Foreign keys prevent orphaned records
- Transaction support ensures data consistency

## 🚦 Future Enhancements

Potential features for advanced projects:
- Promo codes and discount system
- Real-time order tracking with GPS
- Advanced analytics dashboard
- Mobile app integration
- Notification system (email/SMS)
- Loyalty points and rewards
- Review system with images
- Group ordering and bill splitting

## 📄 License

This project is created for academic purposes. Free to use for educational projects.

## 🤝 Contributing

This is an academic project. If you find issues or have suggestions:
1. Fork the repository
2. Create a feature branch
3. Submit a pull request

## 📧 Contact

For questions or support:
- Create an issue in this repository
- Consult the detailed documentation files
- Refer to SQL comments in the schema file

---

**Note**: This schema is conversion of a Supabase/PostgreSQL project to MySQL for academic submission. All PostgreSQL-specific features have been removed and replaced with MySQL-compatible alternatives.

## ⭐ Star this repo if it helps with your DBMS project!