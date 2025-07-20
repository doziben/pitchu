## 💳 Billing Layer

**Goal:**  
Track, limit, and charge users based on their call usage — measured in minutes or hours. Prevent abuse while offering flexibility to upgrade.

---

### 🔩 Core Responsibilities

1.  **Call Time Tracking**

    - Log exact **duration of each call** (in seconds).
    - Save to `calls.duration_seconds`.
    - Aggregate usage by user per billing cycle (monthly or hourly).

2.  **Usage Quota**

    - Every plan (Free, Pro, etc.) has:

      - A **max monthly quota** (e.g., 30 mins, 3 hours).
      - Optional **per-call duration limit**.

    - Store current quota and consumption in `user_billing` table:

      ts

      CopyEdit

      `{
  user_id, plan: "free" | "pro" | "enterprise", quota_minutes: 180, used_minutes: 45, renews_at: "2025-08-01T00:00:00Z" }`

3.  **Real-time Quota Enforcement**

    - Before starting a call:

      - Check `used_minutes < quota_minutes`.

    - During call:

      - Auto-end or block new audio processing if limit is exceeded.

    - Graceful error handling with notification.

4.  **Plan Management**

    - User can upgrade/downgrade plans.
    - Plans defined in `plans` table:

      ts

      CopyEdit

      `{ name: "Pro", price: 9.99, quota_minutes: 180 }`

    - Stripe handles billing and webhooks.

5.  **Stripe Integration**

    - Use Stripe Checkout or Customer Portal.
    - Webhooks:

      - `invoice.paid` → increase `quota_minutes`
      - `customer.subscription.deleted` → revert to free plan
      - `checkout.session.completed` → upgrade plan + billing data

    - Store Stripe `customer_id`, `subscription_id` in Supabase `user_billing`.

6.  **Usage UI**

    - Show current usage in dashboard:

      yaml

      CopyEdit

      `Used:  45  mins  /  180  mins  this  month  Next reset:  Aug  1,  2025`

    - Warn when close to limit.

---

### 🛡️ Abuse Protection

- Only allow one concurrent call per user.
- Enforce per-minute billing granularity (round up).
- Auto-end call at hard limit (e.g., 15 min on Free plan).
- Optional: throttle API if exceeding quota.

---

### 💡 Optional Features

- **Team Plans / Shared Quota** (future)
- **Pay-as-you-go credits**
- **Referral rewards → free minutes**
- **Free trial minutes on sign-up**
- **Email reminders before quota runs out**
