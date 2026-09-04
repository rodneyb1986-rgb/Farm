# Backend Setup Guide

This guide shows how to create a backend server to handle orders, payments, and notifications.

## 🚀 Option 1: Vercel (Recommended for Beginners)

### Step 1: Create Vercel Account
1. Go to [vercel.com](https://vercel.com)
2. Sign up with GitHub
3. Connect your Farm repository

### Step 2: Create API Route
Create `api/create-order.js`:

```javascript
import Stripe from 'stripe';

const stripe = new Stripe(process.env.STRIPE_SECRET_KEY);

export default async function handler(req, res) {
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    const { items, customer } = req.body;

    // Create Stripe checkout session
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: items.map(item => ({
        price_data: {
          currency: 'usd',
          product_data: {
            name: `Farm Attachment #${item.id}`,
          },
          unit_amount: item.price * 100,
        },
        quantity: item.qty,
      })),
      mode: 'payment',
      success_url: `${process.env.DOMAIN}/success?session_id={CHECKOUT_SESSION_ID}`,
      cancel_url: `${process.env.DOMAIN}/cancelled`,
      customer_email: customer.email,
    });

    // Save order to database or email
    await saveOrder({
      stripeSessionId: session.id,
      customer,
      items,
      timestamp: new Date(),
    });

    return res.status(200).json({ sessionId: session.id });
  } catch (error) {
    console.error('Error:', error);
    return res.status(500).json({ error: error.message });
  }
}

async function saveOrder(order) {
  // Connect to database or send email
  console.log('Order saved:', order);
}
```

### Step 3: Set Environment Variables
1. In Vercel dashboard, go to Settings → Environment Variables
2. Add:
   - `STRIPE_SECRET_KEY` = sk_live_...
   - `DOMAIN` = https://yourdomain.com

### Step 4: Deploy
```bash
vercel
```

Your API will be available at:
```
https://your-project.vercel.app/api/create-order
```

---

## 🚀 Option 2: Firebase (Google Cloud)

### Step 1: Create Firebase Project
1. Go to [firebase.google.com](https://firebase.google.com)
2. Create new project
3. Enable Firestore Database
4. Create web app
5. Copy config credentials

### Step 2: Save Orders to Firestore

Add to your HTML:
```html
<script src="https://www.gstatic.com/firebasejs/10.0.0/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/10.0.0/firebase-firestore.js"></script>

<script>
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};

const app = firebase.initializeApp(firebaseConfig);
const db = firebase.firestore(app);

async function saveOrderToFirebase(order) {
  try {
    await db.collection('orders').add({
      orderNumber: order.orderNumber,
      customer: order.customer,
      items: order.items,
      timestamp: new Date(),
      status: 'pending',
    });
    console.log('Order saved to Firebase');
  } catch (error) {
    console.error('Error saving order:', error);
  }
}
</script>
```

### Step 3: Update Checkout
```javascript
async function placeOrder() {
  const name = document.getElementById('cname').value.trim();
  const email = document.getElementById('cemail').value.trim();
  const address = document.getElementById('caddress').value.trim();
  
  if (!name || !email || !address) {
    alert('Please complete your name, email, and shipping address.');
    return;
  }
  
  const order = {
    orderNumber: 'FAD-' + Date.now().toString().slice(-8),
    customer: { name, email, address },
    items: cart
  };
  
  await saveOrderToFirebase(order);
  alert('Order saved! Your order #: ' + order.orderNumber);
}
```

---

## 🚀 Option 3: Node.js + Express (Advanced)

### Step 1: Set Up Project

```bash
mkdir farm-api
cd farm-api
npm init -y
npm install express stripe dotenv cors body-parser
```

### Step 2: Create Server (`server.js`)

```javascript
const express = require('express');
const stripe = require('stripe')(process.env.STRIPE_SECRET_KEY);
const cors = require('cors');
const bodyParser = require('body-parser');

const app = express();
app.use(cors());
app.use(bodyParser.json());

// Create checkout session
app.post('/api/create-order', async (req, res) => {
  const { items, customer } = req.body;

  try {
    const session = await stripe.checkout.sessions.create({
      payment_method_types: ['card'],
      line_items: items.map(item => ({
        price_data: {
          currency: 'usd',
          product_data: { name: `Attachment #${item.id}` },
          unit_amount: item.price * 100,
        },
        quantity: item.qty,
      })),
      mode: 'payment',
      success_url: 'https://yourdomain.com/success',
      cancel_url: 'https://yourdomain.com/cancel',
      customer_email: customer.email,
    });

    // Save order to database
    const order = {
      orderNumber: 'FAD-' + Date.now().toString().slice(-8),
      stripeSessionId: session.id,
      customer,
      items,
      status: 'pending',
      createdAt: new Date(),
    };
    
    await saveOrderToDatabase(order);

    res.json({ sessionId: session.id });
  } catch (error) {
    console.error('Error:', error);
    res.status(400).json({ error: error.message });
  }
});

// Webhook for payment confirmation
app.post('/webhook', express.raw({type: 'application/json'}), (req, res) => {
  const sig = req.headers['stripe-signature'];
  
  try {
    const event = stripe.webhooks.constructEvent(
      req.body,
      sig,
      process.env.STRIPE_WEBHOOK_SECRET
    );

    if (event.type === 'checkout.session.completed') {
      const session = event.data.object;
      
      // Update order status to 'paid'
      updateOrderStatus(session.id, 'paid');
      
      // Send order to supplier
      sendToSupplier(session);
      
      // Send confirmation email to customer
      sendConfirmationEmail(session.customer_email);
    }

    res.json({received: true});
  } catch (error) {
    res.status(400).send(`Webhook Error: ${error.message}`);
  }
});

// Helper functions
async function saveOrderToDatabase(order) {
  // Connect to MongoDB, PostgreSQL, etc.
  console.log('Order saved:', order);
}

async function updateOrderStatus(sessionId, status) {
  // Update database
  console.log(`Order ${sessionId} status updated to ${status}`);
}

async function sendToSupplier(session) {
  // Send order to Hayspear or supplier system
  console.log('Sending order to supplier:', session);
}

async function sendConfirmationEmail(email) {
  // Send email via SendGrid, AWS SES, etc.
  console.log(`Confirmation email sent to ${email}`);
}

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

### Step 3: Create `.env` File

```
STRIPE_SECRET_KEY=sk_live_abc123...
STRIPE_WEBHOOK_SECRET=whsec_abc123...
DOMAIN=https://yourdomain.com
PORT=3000
NODE_ENV=production
```

**⚠️ Never commit `.env` to GitHub!**

### Step 4: Deploy to Heroku

```bash
# Install Heroku CLI first
heroku login
heroku create farm-depot-api
git push heroku main
heroku config:set STRIPE_SECRET_KEY=sk_live_...
```

Your API is now live at: `https://farm-depot-api.herokuapp.com/api/create-order`

---

## 📊 Database Schema

### Orders Collection (MongoDB/Firestore)

```json
{
  "orderId": "FAD-12345678",
  "customerId": "cust_abc123",
  "customer": {
    "name": "John Farmer",
    "email": "john@farm.com",
    "phone": "555-1234",
    "address": "123 Farm Road",
    "city": "Rural, ST 12345"
  },
  "items": [
    {
      "id": 1,
      "name": "Great Bend 660 QA → Skid Steer",
      "qty": 1,
      "price": 2399,
      "supplierId": "hayspear_001"
    }
  ],
  "subtotal": 2399,
  "shipping": 0,
  "tax": 0,
  "total": 2399,
  "status": "paid",
  "payment": {
    "provider": "stripe",
    "sessionId": "cs_live_...",
    "transactionId": "pi_...",
    "paidAt": "2026-09-04T12:00:00Z"
  },
  "fulfillment": {
    "supplier": "Hayspear",
    "supplierOrderId": "HSP-123",
    "trackingNumber": "1Z999AA10123456784",
    "estimatedDelivery": "2026-09-15",
    "status": "pending"
  },
  "createdAt": "2026-09-04T11:30:00Z",
  "updatedAt": "2026-09-04T12:00:00Z"
}
```

---

## 💌 Email Notifications

### Using SendGrid

```bash
npm install @sendgrid/mail
```

```javascript
const sgMail = require('@sendgrid/mail');
sgMail.setApiKey(process.env.SENDGRID_API_KEY);

async function sendOrderConfirmation(order) {
  await sgMail.send({
    to: order.customer.email,
    from: 'orders@farmattachmentdepot.com',
    subject: `Order Confirmed: ${order.orderId}`,
    html: `
      <h2>Order Confirmed!</h2>
      <p>Thank you for your order.</p>
      <p><strong>Order #:</strong> ${order.orderId}</p>
      <p><strong>Total:</strong> $${order.total}</p>
      <p>Your order will be shipped within 2-3 business days.</p>
      <p><a href="https://track.hayspear.com/${order.fulfillment.trackingNumber}">
        Track your shipment
      </a></p>
    `,
  });
}
```

### Using AWS SES

```javascript
const AWS = require('aws-sdk');
const ses = new AWS.SES({ region: 'us-east-1' });

async function sendOrderConfirmation(order) {
  await ses.sendEmail({
    Source: 'orders@farmattachmentdepot.com',
    Destination: { ToAddresses: [order.customer.email] },
    Message: {
      Subject: { Data: `Order Confirmed: ${order.orderId}` },
      Body: { Html: { Data: emailHTML } }
    }
  }).promise();
}
```

---

## 🧪 Testing

### Stripe Test Cards

| Card Number | Result |
|------------|--------|
| 4242 4242 4242 4242 | Success |
| 4000 0000 0000 0002 | Decline |
| 4000 0025 0000 3155 | 3D Secure required |

**Expiry**: Any future date  
**CVC**: Any 3 digits

### Test Webhook

```bash
stripe listen --forward-to localhost:3000/webhook
```

---

## 🔍 Monitoring & Logging

### Sentry (Error Tracking)

```bash
npm install @sentry/node
```

```javascript
const Sentry = require("@sentry/node");

Sentry.init({ dsn: process.env.SENTRY_DSN });

app.use(Sentry.Handlers.errorHandler());
```

### Datadog (Performance Monitoring)

```bash
npm install dd-trace
```

```javascript
require('dd-trace').init();
```

---

## 🔐 Security Best Practices

- ✅ Never expose API keys in code
- ✅ Use HTTPS only (no HTTP)
- ✅ Validate all inputs
- ✅ Sanitize data before storing
- ✅ Use rate limiting on endpoints
- ✅ Enable CORS only for trusted domains
- ✅ Hash sensitive data
- ✅ Keep dependencies updated
- ✅ Use environment variables
- ✅ Enable logging for audits

---

## 📞 Next Steps

1. Choose your backend option (Vercel/Firebase/Node.js)
2. Set up payment processing (Stripe recommended)
3. Configure order notifications (SendGrid/Zapier)
4. Deploy and test with test credentials
5. Go live with production keys

See `DEPLOYMENT.md` for hosting instructions.
