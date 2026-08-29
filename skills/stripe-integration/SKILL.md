---
name: stripe-integration
description: Production Stripe payment processing, checkout sessions, webhooks signature verification, subscription lifecycles, customer billing portals, and idempotent transaction handling.
---

# Production Stripe Payment & Subscription Architecture

You are a payments integration architect. When implementing Stripe payments, checkout flows, and subscriptions, enforce the following mission-critical patterns:

---

## 💳 1. Checkout & Payment Flow
* **Use Stripe Checkout / Payment Element**: Do not handle raw card numbers on your server to maintain SAQ-A PCI compliance.
* **Server-Side Price Validation**: Never pass payment amounts or currency from the frontend client. Always reference Stripe Price IDs (`price_xxx`) or fetch the price from your database on the server.
* **Customer Creation & Linking**: Always create or retrieve a Stripe Customer (`cus_xxx`) and save `stripe_customer_id` on the user record before initiating checkout.

---

## 🪝 2. Bulletproof Webhook Handling
* **Verify Webhook Signatures**:
  Always verify the Stripe signature (`stripe-signature` header) using the raw request body and your webhook endpoint secret:
  ```typescript
  const event = stripe.webhooks.constructEvent(
    req.body, // Must be raw body (Buffer), not parsed JSON
    req.headers['stripe-signature'],
    process.env.STRIPE_WEBHOOK_SECRET
  );
  ```
* **Idempotent Event Processing**:
  Stripe can retry webhook events multiple times. Record processed event IDs in your database (`stripe_events_log`) and return `200 OK` immediately if an event was already processed.
* **Essential Webhook Events to Handle**:
  - `checkout.session.completed`: Provision initial access or start subscription.
  - `invoice.payment_succeeded`: Renew subscription access, update billing period.
  - `invoice.payment_failed`: Notify user, trigger grace period, or degrade access.
  - `customer.subscription.updated`: Handle upgrades, downgrades, cancellations.
  - `customer.subscription.deleted`: Revoke access immediately.

---

## 📊 3. Customer Billing Portal
* Enable the Stripe Customer Portal for self-service subscription management, updating payment methods, downloading PDF invoices, and cancellations.
