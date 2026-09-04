# Deployment Guide

## 🚀 Deploy to GitHub Pages (Free)

### Step 1: Enable GitHub Pages
1. Go to your repository: https://github.com/rodneyb1986-rgb/Farm
2. Click **Settings** (top right)
3. Scroll to **Pages** (left sidebar)
4. Under "Source", select:
   - Branch: `main`
   - Folder: `/ (root)`
5. Click **Save**

### Step 2: Access Your Site
Your storefront will be live at:
```
https://rodneyb1986-rgb.github.io/Farm/farm_attachment_depot_launch_ready.html
```

### Step 3: Create a Landing Page (Optional)
Create an `index.html` in the root to make it easier:
```html
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="refresh" content="0; url=farm_attachment_depot_launch_ready.html">
  <title>Farm Attachment Depot</title>
</head>
<body>
  Redirecting to storefront...
</body>
</html>
```

Then your site is simply:
```
https://rodneyb1986-rgb.github.io/Farm/
```

---

## 🌐 Deploy to Netlify (Recommended - More Features)

### Step 1: Connect Repository
1. Go to [netlify.com](https://netlify.com)
2. Click **Add new site** → **Import an existing project**
3. Select GitHub and authorize
4. Choose `rodneyb1986-rgb/Farm`

### Step 2: Configure Build
- **Base directory:** (leave blank)
- **Build command:** (leave blank)
- **Publish directory:** . (current directory)
- Click **Deploy site**

### Step 3: Custom Domain (Optional)
1. In Netlify dashboard, click **Domain settings**
2. Add your custom domain
3. Follow DNS instructions

Your site will be live in minutes at a Netlify URL, then your custom domain.

**Benefits over GitHub Pages:**
- Form submissions support
- Serverless functions
- Environment variables
- Better analytics
- CDN edge caching

---

## 🌍 Deploy to Your Own Server

### Using a VPS (DigitalOcean, Linode, AWS)
1. SSH into your server
2. Clone the repository:
   ```bash
   git clone https://github.com/rodneyb1986-rgb/Farm.git
   ```
3. Set up a web server (Nginx/Apache)
4. Configure SSL with Let's Encrypt
5. Point domain to your server

### Using Docker
```dockerfile
FROM nginx:alpine
COPY farm_attachment_depot_launch_ready.html /usr/share/nginx/html/index.html
EXPOSE 80
```

Deploy to container service (Docker Hub, GitHub Container Registry).

---

## 💳 Connect Payment Processing

### Option 1: Stripe (Recommended)

**1. Sign up**: [stripe.com](https://stripe.com)

**2. Get API keys**: 
   - Go to Developers → API Keys
   - Copy "Publishable Key" and "Secret Key"

**3. Install Stripe.js** in your HTML:
```html
<script src="https://js.stripe.com/v3/"></script>
```

**4. Update checkout function**:
```javascript
async function checkout() {
  if (!cart.length) {
    alert('Add an attachment to your cart first.');
    return;
  }
  
  const response = await fetch('/api/create-checkout-session', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
      items: cart,
      customer: {
        name: document.getElementById('cname').value,
        email: document.getElementById('cemail').value,
        address: document.getElementById('caddress').value,
      },
    }),
  });
  
  const { sessionId } = await response.json();
  
  // Redirect to Stripe checkout
  const stripe = Stripe('pk_live_YOUR_PUBLIC_KEY');
  stripe.redirectToCheckout({ sessionId });
}
```

**5. Backend needed** - See `BACKEND_SETUP.md`

---

### Option 2: Shopify Store

**1. Create Shopify store**: [shopify.com](https://shopify.com)

**2. Set up products** in Shopify admin:
   - Products → Add product
   - Set name, price, images, description

**3. Embed checkout** in your storefront:
```html
<a href="https://your-store.myshopify.com/cart/add/12345?quantity=1">
  Add to cart
</a>
```

Or use Shopify Buy Button:
```html
<div id='shopify-app-b12345'></div>
<script src='https://cdn.shopify.com/s/javascripts/buy_button_v2.js'></script>
<script>
ShopifyBuy.onReady(function(client) {
  // Configure...
});
</script>
```

---

### Option 3: WooCommerce (WordPress)

**1. Install WordPress** on your hosting

**2. Install WooCommerce plugin**:
   - Plugins → Add new → Search "WooCommerce" → Install

**3. Configure shop**:
   - WooCommerce → Settings → Products/Payments

**4. Import products** via CSV or manually

---

## 📧 Connect Order Notifications

### Email via Formspree (Simple)

1. Go to [formspree.io](https://formspree.io)
2. Create new form
3. Get the form endpoint
4. Update HTML form action:
```html
<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST">
  <input name="name" placeholder="Name" required>
  <input name="email" type="email" placeholder="Email" required>
  <textarea name="message" placeholder="Order details"></textarea>
  <button type="submit">Send Order</button>
</form>
```

---

### Email via SendGrid API

1. Sign up at [sendgrid.com](https://sendgrid.com)
2. Create API key
3. Use backend to send emails (see `BACKEND_SETUP.md`)

---

### Email via Zapier (No-Code)

1. Connect Formspree → Zapier
2. Create automation:
   - **Trigger**: Form submission
   - **Action**: Send email notification
3. Set up Slack alerts (optional)

---

## 🔒 Enable HTTPS

| Platform | HTTPS |
|----------|-------|
| GitHub Pages | ✅ Automatic |
| Netlify | ✅ Automatic |
| Custom server | Use Let's Encrypt (free) |

**For Let's Encrypt on your server**:
```bash
sudo certbot certonly --standalone -d yourdomain.com
```

---

## 📊 Add Analytics

### Google Analytics

1. Go to [analytics.google.com](https://analytics.google.com)
2. Create property for your domain
3. Get Measurement ID (G-XXXXXXXXXX)
4. Add to HTML `<head>`:

```html
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

### Track Add to Cart
```javascript
function add(id) {
  const product = data.find(x => x.id === id);
  gtag('event', 'add_to_cart', {
    value: product.price,
    currency: 'USD',
    items: [{ item_name: product.name, price: product.price }]
  });
  // ... rest of add function
}
```

---

## 🛡️ Security Checklist

- [ ] HTTPS enabled (green lock icon)
- [ ] Privacy policy on website
- [ ] Terms of service on website
- [ ] Contact form uses HTTPS only
- [ ] No sensitive API keys in HTML
- [ ] Input validation on forms
- [ ] Rate limiting on API endpoints
- [ ] CORS headers configured
- [ ] No logs with customer data
- [ ] Regular security updates

---

## 🔧 Environment Variables

Never commit sensitive keys to GitHub!

### GitHub Actions Secret (for CI/CD)
1. Go to Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Add variables (STRIPE_KEY, SENDGRID_KEY, etc.)

### On Netlify
1. Go to Site settings → Build & deploy → Environment
2. Add your secrets there

---

## 🐛 Troubleshooting

| Issue | Solution |
|-------|----------|
| 404 Not Found on GitHub Pages | Ensure file names are correct; wait 1-2 minutes for rebuild |
| Cart doesn't save | Check browser allows localStorage; clear cookies |
| Payment not processing | Verify API keys; check HTTPS enabled; test with Stripe test cards |
| Form not submitting | Check internet connection; verify form action URL; check console errors (F12) |
| Slow page load | Enable CDN (Netlify/Cloudflare); compress images; minify CSS/JS |

---

## 📞 Support

- **GitHub Issues**: Create an issue in your repo
- **Stripe Support**: [stripe.com/support](https://stripe.com/support)
- **Netlify Support**: [netlify.com/support](https://netlify.com/support)
- **Hayspear Contact**: [hayspear.com/contact-us](https://hayspear.com/contact-us)

---

**Next Step**: See `BACKEND_SETUP.md` for server-side order handling and payment processing.
