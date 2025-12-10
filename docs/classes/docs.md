# CLASS DIAGRAM DOCUMENTATION

## OVERVIEW

The class diagram represents the object-oriented design of the electronics webshop system. It contains 17 classes with their attributes, methods, and relationships organized by functional area.

## CLASS ORGANIZATION

The classes are organized into 6 functional groups:

| Group | Classes | Count |
|-------|---------|-------|
| User Management | User, UserAddress | 2 |
| Product Catalog | Category, Brand, Product, ProductImage, ProductReview, Wishlist | 6 |
| Shopping | ShoppingCart, CartItem | 2 |
| Orders | Order, OrderItem, Return, Promotion | 4 |
| Support | ContactForm, Notification, PromoUsage | 3 |
| **Total** | | **17** |

## CLASS SPECIFICATIONS

### User Management

#### 1. User
**Purpose**: Authenticate users and manage user profile information

**Attributes** (11):
- user_id: int [Primary Key]
- email: string [Unique, Required]
- username: string [Unique, Required]
- password_hash: string [Required]
- first_name: string
- last_name: string
- phone: string
- account_status: enum [active | inactive | suspended]
- user_type: enum [customer | admin | staff]
- created_at: timestamp
- updated_at: timestamp

**Methods** (7):
- register(): void
- login(email: string, password: string): boolean
- updateProfile(data: object): void
- getAddresses(): List[UserAddress]
- getOrders(): List[Order]
- getWishlist(): List[Product]
- getNotifications(): List[Notification]

**Relationships**:
- Composes: UserAddress (1:many)
- Associates: Order (1:many)
- Associates: ProductReview (1:many)
- Associates: Wishlist (1:many)
- Composes: ShoppingCart (1:many)
- Associates: Return (1:many)
- Associates: ContactForm (1:many)
- Associates: Notification (1:many)

---

#### 2. UserAddress
**Purpose**: Store multiple delivery and billing addresses for users

**Attributes** (8):
- address_id: int [Primary Key]
- user_id: int [Foreign Key]
- address_type: enum [billing | shipping]
- street_address: string [Required]
- postal_code: string [Required]
- city: string [Required]
- country: string [Required]
- is_default: boolean

**Methods** (2):
- updateAddress(data: object): void
- delete(): void

**Relationships**:
- Owned by: User

---

### Product Catalog

#### 3. Category
**Purpose**: Organize products into hierarchical categories

**Attributes** (4):
- category_id: int [Primary Key]
- category_name: string [Unique, Required]
- description: string
- is_active: boolean

**Methods** (3):
- getProducts(): List[Product]
- activate(): void
- deactivate(): void

**Relationships**:
- Contains: Product (1:many)

---

#### 4. Brand
**Purpose**: Represent product manufacturers and brands

**Attributes** (3):
- brand_id: int [Primary Key]
- brand_name: string [Unique, Required]
- is_active: boolean

**Methods** (1):
- getProducts(): List[Product]

**Relationships**:
- Manufactures: Product (1:many)

---

#### 5. Product
**Purpose**: Core product entity representing items for sale

**Attributes** (15):
- product_id: int [Primary Key]
- product_name: string [Required]
- sku: string [Unique, Required]
- description: string
- category_id: int [Foreign Key, Required]
- brand_id: int [Foreign Key]
- price_base: decimal [Required]
- discount_percentage: int
- stock_quantity: int [Required]
- low_stock_threshold: int
- is_active: boolean
- average_rating: decimal
- total_reviews: int
- created_at: timestamp
- updated_at: timestamp

**Methods** (6):
- getPrice(): decimal
- getDiscount(): decimal
- updateStock(quantity: int): void
- getImages(): List[ProductImage]
- getReviews(): List[ProductReview]
- getRating(): decimal

**Relationships**:
- Belongs to: Category
- Belongs to: Brand
- Composes: ProductImage (1:many)
- Receives: ProductReview (1:many)
- Referenced in: Wishlist (1:many)
- Referenced in: CartItem (1:many)
- Referenced in: OrderItem (1:many)

---

#### 6. ProductImage
**Purpose**: Store product gallery images

**Attributes** (4):
- image_id: int [Primary Key]
- product_id: int [Foreign Key, Required]
- image_url: string [Required]
- is_primary: boolean

**Methods** (2):
- setPrimary(): void
- delete(): void

**Relationships**:
- Owned by: Product

---

#### 7. ProductReview
**Purpose**: Manage customer reviews and ratings for products

**Attributes** (7):
- review_id: int [Primary Key]
- product_id: int [Foreign Key, Required]
- user_id: int [Foreign Key, Required]
- rating: int [Required, Range: 1-5]
- review_text: string
- is_verified_purchase: boolean
- created_at: timestamp

**Methods** (3):
- submitReview(rating: int, text: string): void
- isValid(): boolean
- getProduct(): Product

**Relationships**:
- Reviews: Product
- Written by: User

---

#### 8. Wishlist
**Purpose**: Allow users to bookmark products for later purchase

**Attributes** (4):
- wishlist_id: int [Primary Key]
- user_id: int [Foreign Key, Required]
- product_id: int [Foreign Key, Required]
- added_at: timestamp

**Methods** (2):
- addToWishlist(): void
- removeFromWishlist(): void

**Relationships**:
- Created by: User
- References: Product

---

### Shopping

#### 9. ShoppingCart
**Purpose**: Manage shopping cart for users (registered or guest)

**Attributes** (5):
- cart_id: int [Primary Key]
- user_id: int [Foreign Key, Optional]
- session_id: string [For guest checkout]
- created_at: timestamp
- updated_at: timestamp

**Methods** (7):
- addItem(product: Product, quantity: int): void
- removeItem(product: Product): void
- updateQuantity(product: Product, quantity: int): void
- getTotal(): decimal
- getItems(): List[CartItem]
- clear(): void
- convertToOrder(): Order

**Relationships**:
- Owned by: User (optional)
- Composes: CartItem (1:many)

---

#### 10. CartItem
**Purpose**: Represent individual items within a shopping cart

**Attributes** (6):
- cart_item_id: int [Primary Key]
- cart_id: int [Foreign Key, Required]
- product_id: int [Foreign Key, Required]
- quantity: int [Required]
- price_at_time: decimal [Required]
- added_at: timestamp

**Methods** (3):
- getSubtotal(): decimal
- updateQuantity(quantity: int): void
- delete(): void

**Relationships**:
- Owned by: ShoppingCart
- References: Product

---

### Orders

#### 11. Order
**Purpose**: Represent customer orders with complete lifecycle management

**Attributes** (16):
- order_id: int [Primary Key]
- user_id: int [Foreign Key, Optional]
- order_number: string [Unique, Required]
- order_status: enum [pending | confirmed | shipped | delivered | cancelled]
- payment_status: enum [pending | completed | failed]
- subtotal: decimal [Required]
- discount_amount: decimal
- tax_amount: decimal
- total_amount: decimal [Required]
- customer_email: string [Required]
- customer_phone: string
- notes: string
- order_date: timestamp
- confirmed_at: datetime
- shipped_at: datetime
- delivered_at: datetime

**Methods** (7):
- calculateTotal(): decimal
- confirmOrder(): void
- shipOrder(): void
- deliverOrder(): void
- cancelOrder(): void
- getItems(): List[OrderItem]
- createReturn(): Return

**Relationships**:
- Placed by: User (optional)
- Composes: OrderItem (1:many)
- Has: Return (1:many)
- Uses: PromoUsage (1:many)

---

#### 12. OrderItem
**Purpose**: Represent individual items within an order

**Attributes** (7):
- order_item_id: int [Primary Key]
- order_id: int [Foreign Key, Required]
- product_id: int [Foreign Key, Required]
- product_name: string [Required, snapshot]
- quantity: int [Required]
- unit_price: decimal [Required]
- total_price: decimal [Required]

**Methods** (1):
- getSubtotal(): decimal

**Relationships**:
- Owned by: Order
- References: Product

---

#### 13. Return
**Purpose**: Handle product return requests and processing

**Attributes** (7):
- return_id: int [Primary Key]
- order_id: int [Foreign Key, Required]
- user_id: int [Foreign Key, Required]
- return_status: enum [requested | approved | rejected | completed]
- reason: string [Required]
- requested_at: timestamp
- approved_at: datetime

**Methods** (5):
- requestReturn(): void
- approveReturn(): void
- rejectReturn(): void
- completeReturn(): void
- getOrder(): Order

**Relationships**:
- Related to: Order
- Initiated by: User

---

#### 14. Promotion
**Purpose**: Define and manage discount codes and promotional campaigns

**Attributes** (10):
- promotion_id: int [Primary Key]
- promotion_name: string [Required]
- code: string [Unique]
- discount_percentage: decimal [Required]
- start_date: datetime [Required]
- end_date: datetime [Required]
- max_uses: int
- current_uses: int
- is_active: boolean
- created_at: timestamp

**Methods** (5):
- isValid(): boolean
- isExpired(): boolean
- canUse(): boolean
- apply(order: Order): decimal
- increment(): void

**Relationships**:
- Used in: PromoUsage (1:many)

---

### Support

#### 15. ContactForm
**Purpose**: Handle customer support inquiries and tickets

**Attributes** (8):
- contact_id: int [Primary Key]
- user_id: int [Foreign Key, Optional]
- email: string [Required]
- name: string [Required]
- subject: string [Required]
- message: string [Required]
- status: enum [new | replied | closed]
- submitted_at: timestamp

**Methods** (4):
- submitForm(data: object): void
- replyToForm(response: string): void
- closeTicket(): void
- getUser(): User

**Relationships**:
- Submitted by: User (optional)

---

#### 16. Notification
**Purpose**: Send and manage user notifications

**Attributes** (6):
- notification_id: int [Primary Key]
- user_id: int [Foreign Key, Required]
- notification_type: enum [order_update | review_reply | promotion | system]
- message: string [Required]
- is_read: boolean
- created_at: timestamp

**Methods** (3):
- markAsRead(): void
- send(): void
- delete(): void

**Relationships**:
- Sent to: User

---

#### 17. PromoUsage
**Purpose**: Track promotion application to orders

**Attributes** (4):
- usage_id: int [Primary Key]
- promotion_id: int [Foreign Key, Required]
- order_id: int [Foreign Key, Required]
- used_at: timestamp

**Methods** (1):
- getDiscount(): decimal

**Relationships**:
- Applied in: Order
- Applied from: Promotion

## RELATIONSHIP SUMMARY

### Composition (Strong Ownership - 1:many)
Parent class owns child exclusively. Child cannot exist without parent.

- User → UserAddress
- User → ShoppingCart
- Product → ProductImage
- ShoppingCart → CartItem
- Order → OrderItem

### Association (Weak Coupling - 1:many)
Objects are connected but can exist independently.

- User → Order
- User → ProductReview
- User → Wishlist
- User → Return
- User → ContactForm
- User → Notification
- Category → Product
- Brand → Product
- Product → ProductReview
- Product → Wishlist
- Product → CartItem
- Product → OrderItem
- Order → Return
- Order → PromoUsage
- Promotion → PromoUsage

**Total Relationships**: 20

## DESIGN CONVENTIONS

### Attributes
- **Private visibility**: All attributes are internal to the class
- **Format**: name: type [constraints]
- **Data types**: int, string, decimal, boolean, enum, datetime, timestamp
- **Constraints**: Primary Key, Foreign Key, Unique, Required, Range

### Methods
- **Public visibility**: All methods are accessible externally
- **Format**: name(parameters): returnType
- **Parameters**: Shown with types for clarity
- **Return types**: void, primitive types, or object/list types

### Relationships
- **Composition**: Strong ownership, denoted by filled diamond (⬊)
- **Association**: Weak coupling, denoted by open arrow (→)
- **Cardinality**: 1:many relationships only in this design

## STATISTICS

- **Total Classes**: 17
- **Total Attributes**: 125
- **Total Methods**: 62
- **Total Relationships**: 20
- **Composition Relationships**: 5
- **Association Relationships**: 15

## DESIGN PRINCIPLES

1. **Single Responsibility**: Each class has a single, well-defined purpose
2. **Encapsulation**: Attributes are private, methods are public
3. **Composition Over Inheritance**: Uses composition for relationships
4. **Separation of Concerns**: Classes grouped by functional area
5. **Business Logic**: Methods contain domain-specific operations