# Admin Panel — Restaurant Dashboard Build Prompt

## CONTEXT

This admin panel already has:
- A login page (Master Admin / Admin User roles)
- Master Admin can manage restaurants and assign them to Admin Users
- Each restaurant card currently has a **"Menu"** button — **RENAME this button to "Dashboard"** everywhere it appears

When the **"Dashboard"** button is clicked, instead of going to the old Menu Management page, it should open a brand-new, fully featured Restaurant Dashboard for that specific restaurant.

> **ACTION REQUIRED FIRST:** Delete the existing Menu Management page/component completely. Start fresh with the new Dashboard described below.

---

## MONGODB STRUCTURE (Digital Menu Database)

The digital menu for each restaurant connects to a MongoDB instance with the following databases and collections. The Dashboard must perform full CRUD operations on all of them.

---

### Database: `barrelborn` (Main Menu DB)

**Menu Item Collections** — one MongoDB collection per food/drink category. Each collection contains MenuItem documents.

**Category collection names:**

```
nibbles, soups, titbits, salads, mangalorean-style, wok, charcoal, continental, pasta,
artisan-pizzas, mini-burger-sliders, entree-(main-course), bao-&-dim-sum,
indian-mains---curries, biryanis-&-rice, dals, breads, asian-mains,
rice-with-curry---thai-&-asian-bowls, rice-&-noodles, desserts, blended-whisky,
blended-scotch-whisky, american-irish-whiskey, single-malt-whisky, vodka, gin, rum,
tequila, cognac-brandy, liqueurs, sparkling-wine, white-wines, rose-wines, red-wines,
dessert-wines, port-wine, signature-mocktails, soft-beverages, craft-beers-on-tap,
draught-beer, pint-beers, classic-cocktails, signature-cocktails, wine-cocktails,
sangria, beer-cocktail, signature-shots, sizzlers
```

**MenuItem Document Schema:**

```json
{
  "_id": "ObjectId",
  "name": "string",
  "description": "string",
  "price": "number OR string (e.g. '30ml: ₹200 / NIP: ₹400 / Bottle: ₹2000')",
  "category": "string (collection name)",
  "isVeg": "boolean",
  "image": "string (URL)",
  "restaurantId": "ObjectId",
  "isAvailable": "boolean",
  "todaysSpecial": "boolean",
  "chefSpecial": "boolean",
  "createdAt": "Date",
  "updatedAt": "Date",
  "preparationTime": "string (optional)",
  "nutritionalContents": "object key-value (optional)",
  "allergens": ["string (optional)"],
  "ingredients": ["string (optional)"]
}
```

**Other collections in `barrelborn`:**
- `cartitems` — `{ menuItemId, quantity, createdAt, updatedAt }`
- `users` — `{ username, password, createdAt, updatedAt }`

---

### Database: `customersdb`

**Collection: `customers`**

```json
{
  "_id": "ObjectId",
  "name": "string",
  "contactNumber": "string (10 digits)",
  "visitCount": "number",
  "lastVisitDate": "Date",
  "createdAt": "Date",
  "updatedAt": "Date"
}
```

---

### Database: `socialsandcontact`

**Collection: `link`** (single document)

```json
{
  "_id": "ObjectId",
  "instagram": "URL",
  "facebook": "URL",
  "youtube": "URL",
  "googleReview": "URL",
  "locate": "Google Maps URL",
  "call": "tel:+91...",
  "whatsapp": "https://wa.me/...",
  "email": "mailto:...",
  "website": "URL"
}
```

---

### Database: `welcomescreen`

**Collection: `welcomescreenui`** (single document)

```json
{
  "_id": "ObjectId",
  "logoUrl": "string (image URL or empty)",
  "buttonText": "string"
}
```

---

### Database: `menupage`

**Collection: `coupons`**

```json
{
  "_id": "ObjectId",
  "code": "string",
  "title": "string",
  "subtitle": "string",
  "description": "string",
  "validity": "string",
  "tag": "string",
  "show": "boolean"
}
```

**Collection: `carousel`**

```json
{
  "_id": "ObjectId",
  "url": "string (image URL)",
  "alt": "string",
  "order": "number",
  "visible": "boolean"
}
```

**Collection: `logo`** (single document)

```json
{
  "_id": "ObjectId",
  "url": "string (image URL)"
}
```

**Collection: `categories`** (top-level menu groupings with nested subcategories)

```json
{
  "_id": "ObjectId",
  "id": "string (slug, e.g. 'food')",
  "title": "string (e.g. 'FOOD')",
  "image": "string",
  "order": "number",
  "visible": "boolean",
  "subcategories": [
    {
      "id": "string",
      "title": "string",
      "image": "string",
      "visible": "boolean",
      "subcategories": []
    }
  ]
}
```

**Collection: `callwaiter`** (single document)

```json
{
  "_id": "ObjectId",
  "called": "boolean"
}
```

---

### Database: `hamburger`

**Collection: `reservation`**

```json
{
  "_id": "ObjectId",
  "name": "string",
  "phone": "string",
  "date": "string",
  "timeSlot": "string",
  "guests": "string",
  "occasion": "string (optional)",
  "createdAt": "Date"
}
```

**Collection: `paymentdetails`** (single document)

```json
{
  "_id": "ObjectId",
  "upiId": "string"
}
```

**Collection: `restaurantinfo`** (single document — each field is an info entry object)

```json
{
  "_id": "ObjectId",
  "location":  { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "locate" },
  "contact":   { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "call" },
  "hours":     { "name": "string", "subtext": "string", "show": "boolean" },
  "instagram": { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "instagram" },
  "facebook":  { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "facebook" },
  "youtube":   { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "youtube" },
  "whatsapp":  { "name": "string", "subtext": "string", "show": "boolean", "linkKey": "whatsapp" }
}
```

---

### Database: `smartpicks`

**Collection: `smartpickscategorie`**

```json
{
  "_id": "ObjectId",
  "label": "string",
  "key": "string",
  "icon": "string (emoji or icon name)",
  "tagline": "string",
  "order": "number",
  "isVisible": "boolean"
}
```

---

## DASHBOARD REQUIREMENTS

Build a full-featured, beautiful Restaurant Dashboard that replaces the old Menu Management page.

---

### Layout

- **Left sidebar** with collapsible navigation, icons (lucide-react) alongside text labels
- **Top header** showing the restaurant name, logo, and a back button to return to the main admin panel
- Clean, modern design with a consistent blue/white color theme matching the existing admin panel
- Fully responsive layout

---

### Sidebar Sections

| # | Section | Icon | Description |
|---|---------|------|-------------|
| 1 | Overview | `LayoutDashboard` | Stats cards: total menu items, customers, reservations, active coupons, categories |
| 2 | Menu Items | `UtensilsCrossed` | Full CRUD across all category collections |
| 3 | Categories | `LayoutGrid` | Tree view of top-level categories + subcategories, toggle visibility, reorder |
| 4 | Smart Picks | `Sparkles` | List, toggle visibility, edit label/icon/tagline, reorder |
| 5 | Carousel | `Images` | Image list with preview, add/edit/delete/reorder/toggle visible |
| 6 | Coupons | `Tag` | Full CRUD + toggle show/hide |
| 7 | Customers | `Users` | Paginated table, search, date filter, sort, export CSV |
| 8 | Reservations | `CalendarCheck` | Table, date filter, export CSV |
| 9 | Social Links | `Share2` | Editable form for all social/contact links |
| 10 | Welcome Screen | `Monitor` | Edit logoUrl (with preview) and buttonText |
| 11 | Restaurant Info | `Info` | Edit each info entry (name, subtext, show toggle, linkKey) |
| 12 | Payment Settings | `CreditCard` | Edit UPI ID |
| 13 | Logo | `ImageIcon` | Preview current logo + update URL |
| 14 | Call Waiter | `Bell` | Show called status + reset button |

---

### Section Details

#### 2. Menu Items
- List all items with search bar, filter by category (dropdown), filter toggles for isVeg / isAvailable / todaysSpecial / chefSpecial
- Show item image preview, name, description, price, category, veg badge
- Inline toggle switches for `isAvailable`, `todaysSpecial`, `chefSpecial`
- Add new item form: name, description, price, category dropdown (all 49 categories), isVeg toggle, image URL, isAvailable, todaysSpecial, chefSpecial, preparationTime, allergens (comma-separated), ingredients (comma-separated), nutritionalContents (key-value pairs)
- Edit existing item (same form pre-populated)
- Delete item with confirmation dialog
- Bulk import via JSON file upload

#### 3. Categories
- Render top-level categories as expandable cards showing their subcategories
- Toggle `visible` for both top-level categories and subcategories
- Reorder top-level categories with up/down buttons (updates `order` field)
- Edit category title and image URL
- Add / delete subcategories within a category

#### 4. Smart Picks
- List all smart picks categories
- Toggle `isVisible` with a switch
- Reorder with up/down buttons (updates `order` field)
- Edit label, icon, tagline inline or via modal

#### 5. Carousel
- Grid of carousel images with thumbnail preview
- Add new image (URL + alt text + order)
- Edit alt text and order
- Toggle `visible`
- Delete with confirmation

#### 6. Coupons
- Card/table list of all coupons
- Toggle `show` with a switch directly from the list
- Add coupon modal: code, title, subtitle, description, validity, tag, show
- Edit coupon
- Delete with confirmation

#### 7. Customers
- Paginated table (10 per page): name, contactNumber, visitCount, lastVisitDate, createdAt
- Search by name or phone number
- Date range filter (from / to)
- Sort by visitCount, name, lastVisitDate (ascending/descending)
- Export visible/filtered results as CSV

#### 8. Reservations
- Full table: name, phone, date, timeSlot, guests, occasion, createdAt
- Filter by date
- Export as CSV

#### 9. Social Links
- Single form with fields: Instagram URL, Facebook URL, YouTube URL, Google Review URL, Google Maps URL, Phone (`tel:`), WhatsApp (`https://wa.me/`), Email (`mailto:`), Website URL
- Save button with success toast

#### 10. Welcome Screen
- Image preview of current `logoUrl`
- Input to update `logoUrl`
- Input to update `buttonText`
- Save button

#### 11. Restaurant Info
- For each field (location, contact, hours, instagram, facebook, youtube, whatsapp):
  - Input for `name`
  - Input for `subtext`
  - Toggle switch for `show`
  - Input for `linkKey` (where applicable)
- Save all changes button

#### 12. Payment Settings
- Input field for `upiId`
- Save button with success toast

#### 13. Logo
- Show current logo image preview
- Input for new logo URL
- Save button

#### 14. Call Waiter
- Display current `called` status (true/false) with a status badge
- "Reset" button to set `called: false` with confirmation

---

### API Endpoints to Implement / Connect

All endpoints use the restaurant's digital menu base URL (configurable per restaurant record).

| Method | Endpoint | Purpose |
|--------|----------|---------|
| GET | `/api/menu-items` | Fetch all menu items |
| GET | `/api/menu-items/category/:category` | Fetch items by category |
| POST | `/api/menu-items` | Create new menu item |
| PATCH | `/api/menu-items/:id` | Update menu item flags |
| DELETE | `/api/menu-items/:id` | Delete menu item |
| GET | `/api/menu-categories` | Get category tree |
| PATCH | `/api/menu-categories/:id` | Update category |
| GET | `/api/smart-picks-categories` | List smart picks |
| PATCH | `/api/smart-picks-categories/:key/visibility` | Toggle smart pick visibility |
| GET | `/api/carousel` | List carousel images |
| POST | `/api/carousel` | Add carousel image |
| PATCH | `/api/carousel/:id` | Update carousel image |
| DELETE | `/api/carousel/:id` | Delete carousel image |
| GET | `/api/coupons` | List all coupons |
| POST | `/api/coupons` | Create coupon |
| PATCH | `/api/coupons/:id` | Update coupon |
| DELETE | `/api/coupons/:id` | Delete coupon |
| GET | `/api/customers` | Paginated customers (Auth: `Bearer admin123`) |
| GET | `/api/reservations` | All reservations (Auth: `Bearer admin123`) |
| GET | `/api/social-links` | Get social links |
| PATCH | `/api/social-links` | Update social links |
| GET | `/api/welcome-screen-ui` | Get welcome screen UI |
| PATCH | `/api/welcome-screen-ui` | Update welcome screen UI |
| GET | `/api/restaurant-info` | Get restaurant info |
| PATCH | `/api/restaurant-info` | Update restaurant info |
| GET | `/api/payment-details` | Get payment details |
| PATCH | `/api/payment-details` | Update UPI ID |
| GET | `/api/logo` | Get logo |
| PATCH | `/api/logo` | Update logo URL |
| GET | `/api/call-waiter` | Get call waiter status |
| PATCH | `/api/call-waiter` | Update call waiter status |

---

### Technical Requirements

- **UI Library:** Tailwind CSS + shadcn/ui components — Card, Button, Input, Badge, Switch, Table, Dialog, Form, Select, Tabs, Skeleton
- **Icons:** `lucide-react` throughout
- **Data Fetching:** `@tanstack/react-query` for all API calls, with proper cache key invalidation after every mutation
- **Forms:** `react-hook-form` with `zodResolver` for validation
- **Notifications:** Toast notifications for all create / update / delete operations
- **Confirmations:** Confirmation dialogs for all destructive (delete / reset) actions
- **Loading States:** Skeleton loaders for all data-fetching sections
- **Empty States:** Helpful empty state messages when collections have no data
- **Design:** Blue/white professional color scheme matching the existing admin panel, collapsible sidebar with icons + labels, top header with restaurant name and back button
