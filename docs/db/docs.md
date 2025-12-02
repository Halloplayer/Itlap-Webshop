# WEBSHOP DATABASE ER/UML DIAGRAM
## Streamlined Version - 17 Tables

---

## TABLE OVERVIEW

### Core User Management
**users** - User accounts and authentication<br>
**user_addresses** - Multiple shipping/billing addresses

### Product Catalog
**categories** - Product categories<br>
**brands** - Product brands/manufacturers<br>
**products** - Main product catalog<br>
**product_images** - Product gallery images

### Customer Engagement
**product_reviews** - Customer ratings and reviews<br>
**wishlist** - Bookmarked products

### Shopping & Checkout
**shopping_carts** - Active shopping carts<br>
**cart_items** - Items in cart

### Orders & Fulfillment
**orders** - Customer orders<br>
**order_items** - Products in orders<br>
**returns** - Return requests

### Promotions
**promotions** - Discount codes and campaigns<br>
**promo_usage** - Track promotion usage

### Support & Communication
**contact_form** - Support inquiries<br>
**notifications** - User notifications

## Summary
**Total: 17 tables** (down from 39)
**Covers all core functionality** needed for electronics e-commerce

---

## KEY RELATIONSHIPS

### Users (Central Hub)
- users → user_addresses (1:*)
- users → orders (1:*)
- users → product_reviews (1:*)
- users → wishlist (1:*)
- users → shopping_carts (1:*)
- users → contact_form (1:*)
- users → notifications (1:*)
- users → returns (1:*)

### Products
- categories → products (1:*)
- brands → products (1:*)
- products → product_images (1:*)
- products → product_reviews (1:*)
- products → wishlist (1:*)
- products → cart_items (1:*)
- products → order_items (1:*)

### Orders
- orders → order_items (1:*)
- orders → returns (1:*)
- orders → promo_usage (1:*)

### Shopping
- shopping_carts → cart_items (1:*)
- cart_items → products (1:*)

### Other
- promotions → promo_usage (1:*)

---

## IMPORT INSTRUCTIONS

### For MySQL/MariaDB
1. Open MySQL Workbench
2. File → Open SQL Script
3. Select: webshop_database_schema_streamlined.sql
4. Execute
5. Done - schema created

### For PlantUML
1. Visual Studio Code:
   - Install PlantUML extension
   - Open: webshop_er_diagram_streamlined.puml
   - Right-click → Preview Diagram

2. Online at plantuml.com:
   - Copy .puml content
   - Paste into editor
   - View diagram

3. Draw.io / Lucidchart:
   - Import .puml file
   - Auto-generates diagram

---

## TABLE DEFINITIONS

### Users
- user_id (PK)
- email (UQ)
- username (UQ)
- password_hash
- first_name, last_name, phone
- account_status (active/inactive/suspended)
- user_type (customer/admin/staff)

### User_addresses
- address_id (PK)
- user_id (FK)
- address_type (billing/shipping)
- street_address, postal_code, city, country
- is_default

### Categories
- category_id (PK)
- category_name (UQ)
- description, is_active

### Brands
- brand_id (PK)
- brand_name (UQ)
- is_active

### Products
- product_id (PK)
- product_name
- sku (UQ)
- description
- category_id (FK)
- brand_id (FK)
- price_base
- discount_percentage
- stock_quantity
- low_stock_threshold
- is_active
- average_rating
- total_reviews

### Product_images
- image_id (PK)
- product_id (FK)
- image_url
- is_primary

### Product_reviews
- review_id (PK)
- product_id (FK)
- user_id (FK)
- rating (1-5)
- review_text
- is_verified_purchase
- created_at

### Wishlist
- wishlist_id (PK)
- user_id (FK)
- product_id (FK)
- added_at

### Shopping_carts
- cart_id (PK)
- user_id (FK) [optional for guest checkout]
- session_id [for guest users]
- created_at, updated_at

### Cart_items
- cart_item_id (PK)
- cart_id (FK)
- product_id (FK)
- quantity
- price_at_time

### Orders
- order_id (PK)
- user_id (FK) [optional for guest orders]
- order_number (UQ)
- order_status (pending/confirmed/shipped/delivered/cancelled)
- payment_status (pending/completed/failed)
- subtotal, discount_amount, tax_amount
- total_amount
- customer_email, customer_phone
- notes, order_date, confirmed_at, shipped_at, delivered_at

### Order_items
- order_item_id (PK)
- order_id (FK)
- product_id (FK)
- product_name [snapshot]
- quantity, unit_price, total_price

### Returns
- return_id (PK)
- order_id (FK)
- user_id (FK)
- return_status (requested/approved/rejected/completed)
- reason, requested_at, approved_at

### Promotions
- promotion_id (PK)
- promotion_name
- code (UQ) [promo code]
- discount_percentage
- start_date, end_date
- max_uses, current_uses
- is_active

### Promo_usage
- usage_id (PK)
- promotion_id (FK)
- order_id (FK)
- used_at

### Contact_form
- contact_id (PK)
- user_id (FK) [optional]
- email, name
- subject, message
- status (new/replied/closed)
- submitted_at

### Notifications
- notification_id (PK)
- user_id (FK)
- notification_type (order_update/review_reply/promotion/system)
- message
- is_read
- created_at

---

## DATABASE STATISTICS

- **Total Tables**: 17
- **Total Columns**: 128
- **Total Foreign Keys**: 20
- **Total Indexes**: 40+
- **Supported Users**: 1000s to millions
- **Order History**: Complete tracking
- **Analytics**: Review counts, ratings, product performance

---

## KEY FEATURES SUPPORTED

✓ User registration & authentication<br>
✓ Multiple shipping addresses<br>
✓ Product catalog with categories & brands<br>
✓ Product images gallery<br>
✓ 5-star review system with verified purchase badges<br>
✓ Wishlist<br>
✓ Shopping cart (guest + registered)<br>
✓ Complete order management<br>
✓ Order returns & refunds<br>
✓ Discount codes & promotions<br>
✓ Real-time stock level tracking<br>
✓ Customer support ticket system<br>
✓ Notifications<br>
✓ No payment/shipping infrastructure (simplified)

---

## SCALABILITY

For small to medium sites (100k-1M orders):
- This schema handles perfectly
- No partitioning needed initially
- Standard indexing sufficient

Future optimization:
- Add order/transaction archive tables
- Partition page_visits by date (if added)
- Cache product ratings/counts

---

## SECURITY NOTES

- Passwords stored as hashes only
- Email/username uniqueness enforced
- Foreign keys prevent orphaned records
- Timestamps on all audit events
- User types (customer/admin/staff) for role-based access

---

END OF DOCUMENTATION