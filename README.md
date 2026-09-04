# Farm Attachment Depot

A professional dropship storefront and attachment finder for farm equipment, loaders, and skid steer attachments.

## 📁 Project Files

### Storefronts
- **`farm_attachment_depot_launch_ready.html`** - ⭐ **Recommended** - Full-featured storefront with:
  - Shopping cart with local storage persistence
  - Product filtering and search
  - Checkout panel
  - Custom quote requests
  - Attachment finder tool
  - Responsive mobile design

- **`farm_attachment_depot_dropship_storefront.html`** - Basic storefront version with:
  - Product grid and filtering
  - Attachment finder
  - Business model explanation
  - Custom quote form

### Tools & Finders
- **`hayspear_attachment_finder.html`** - Standalone attachment finder that routes customers to the right product category based on:
  - Machine/hookup type (John Deere, Case, CAT, Skid Steer, etc.)
  - Job type (bale moving, conversions, grapples, augers, etc.)
  - Links to live Hayspear catalog categories

## 🚀 Getting Started

### View Locally
1. Download any `.html` file
2. Open in your web browser (no server needed)
3. The storefronts work offline with local browser storage

### Deploy to GitHub Pages
1. Go to repository Settings → Pages
2. Select "Deploy from a branch"
3. Choose branch: `main`, folder: `/ (root)`
4. Your site will be live at: `https://rodneyb1986-rgb.github.io/Farm/farm_attachment_depot_launch_ready.html`

## 🛠️ Features

### Launch-Ready Storefront
✅ Product catalog with 8 demo attachments  
✅ Category filtering (Adapters, Bale Spears, Brackets, Augers)  
✅ Live search functionality  
✅ Shopping cart (persists across sessions)  
✅ Quantity adjustment (+ / -)  
✅ Checkout form collection  
✅ Order summary logging  
✅ Mobile responsive  
✅ Custom color scheme (green & cream)  
✅ Attachment finder tool  
✅ Business model explainer  
✅ Custom quote capture form  

### Attachment Finder
✅ 10 machine/hookup types  
✅ 7 job categories  
✅ Dynamic routing to Hayspear catalog  
✅ Custom fabrication option  
✅ Professional dark theme  

## 💳 Payment Integration (Ready to Connect)

The storefront captures order data. To accept payments, connect:
- **Stripe** - for credit card processing
- **Shopify** - for full ecommerce platform
- **WooCommerce** - for WordPress integration
- **Your own API** - custom backend

Order data is logged to browser console in this format:
```javascript
{
  orderNumber: "FAD-12345678",
  customer: { name, email, address },
  items: [{ id, qty }, ...]
}
```

## 🎨 Customization

### Colors (Edit CSS variables)
```css
:root {
  --g: #234a2d;           /* Primary green */
  --g2: #16351f;          /* Dark green */
  --cream: #f4f1e8;       /* Background */
  --ink: #172019;         /* Text */
  --gold: #c3913e;        /* Accent */
}
```

### Product Data
Edit the `data` array in the script section to add/modify products:
```javascript
const data = [
  {
    id: 1,
    type: 'adapter',
    name: 'Product Name',
    price: 2399,
    icon: '🔩',
    url: 'https://...',
    desc: 'Description'
  },
  // Add more products...
];
```

### Machine Types & Jobs
Update the `<select>` dropdowns in HTML to modify finder options.

## 📊 Business Model Explained

This is a **dropship/reseller storefront** template:
1. **You** host the storefront and set retail prices
2. **Customer** buys from you at your price
3. **Supplier** (Hayspear) fulfills the order
4. **You** keep the margin

Example:
- Supplier cost: $1,000
- Your retail price: $1,299
- Your gross margin: $299

## 📱 Mobile Responsive

All storefronts adapt to:
- 📺 Desktop (4-column grid)
- 💻 Tablet (2-column grid, 900px breakpoint)
- 📱 Mobile (1-column, 560px breakpoint)

Navigation hides on mobile; menu items become links.

## 🔗 External Links

- [Hayspear Catalog](https://hayspear.com/) - Live supplier catalog
- [Hayspear Contact](https://hayspear.com/contact-us/) - For custom quotes

## ⚠️ Before Going Live

- [ ] Negotiate reseller/dropship agreement with supplier
- [ ] Secure written approval for product resale
- [ ] Set your retail prices and margins
- [ ] Connect payment processor (Stripe, Shopify, WooCommerce)
- [ ] Set up order routing to supplier
- [ ] Implement customer support workflow
- [ ] Add your branding (logo, colors, fonts)
- [ ] Populate with real product data and images
- [ ] Set up SSL/HTTPS
- [ ] Configure shipping calculator
- [ ] Add terms of service and privacy policy

## 🚀 Next Steps

1. **Host it**: Deploy to GitHub Pages or your web server
2. **Customize**: Update colors, products, and branding
3. **Connect payment**: Integrate Stripe or Shopify
4. **Add backend**: Create order management system
5. **Go live**: Announce to customers and start selling

---

**Built with:** HTML, CSS, JavaScript (no dependencies)  
**License:** Open source - customize freely  
**Last updated:** 2026-09-04
