# HelloSolver Standard Operating Procedures (SOP) Handbook

This document compiles the official, mandatory **Standard Operating Procedures (SOPs)** for the HelloSolver codebase. All builders (Developer, DevOps) and validators (CLI, GUI, Human Testers) must strictly adhere to these protocols.

---

## 🛡️ SOP 1: Button Actions & UI Loader Integrity

To prevent duplicate database mutations, infinite spinner freezes, and visual layout shifts, follow these guidelines:

### 1. The Try/Catch/Finally Loader Loop
Every button that triggers an asynchronous backend API request must immediately toggle an active loading state and explicitly disable itself to prevent double-clicks:
```javascript
const handleAction = async () => {
  try {
    setLoading(true);
    await apiCall();
    showToast("Action completed.");
  } catch (error) {
    showToast(error.message || "An error occurred.");
  } finally {
    setLoading(false); // ALWAYS clear loading in the finally block
  }
};
```
*Mandatory Rule:* The `loading` state must be reset inside a `finally` block to ensure that even on API failures, the UI never freezes or leaves the button permanently stuck.

### 2. Option Text Entity Decoding
All select pickers, dropdowns, and option lists must naturally decode HTML characters. Never render raw escaped entities to the user:
*   *Incorrect:* `Nearest &amp; Top Rated`
*   *Correct:* `Nearest & Top Rated`

---

## 🔑 SOP 2: Booking & Token Integrity Safeguards

To prevent database clutter, concurrent write overrides, and duplicate queue slots:

### 1. Single Active Token Safeguard
Before creating or inserting a new booking or token, both the frontend client and the backend API controller must explicitly check if the customer already holds an active token with the same vendor:
```javascript
const activeToken = await db.collection('bookings').findOne({
  userId: customerId,
  vendorId: vendorId,
  status: { $in: ['pending', 'confirmed', 'in_service'] }
});

if (activeToken) {
  throw new Error("You already have an active booking with this vendor.");
}
```
If an active booking exists, block the transaction immediately.

### 2. Transient Write Retries
Under heavy concurrent load, database operations can fail due to write locks or transient database connection lags. All transaction-critical database writes must implement a retry loop (up to 3 attempts with exponential backoff) before throwing a failure.

---

## 📡 SOP 3: Notification & FCM Token Pruning

To protect the alert delivery system and ensure push notification reliability:

### 1. Stale Token Pruning (Silent Alert Prevention)
*   **The Issue:** Stale or inactive registration tokens stored in the database cause notification failure loops, slow down queue dispatches, and trigger silent alerts.
*   **The Solution:** When a push notification dispatch returns an inactive or unregistered device error from the Firebase Cloud Messaging (FCM) gateway, immediately prune that stale token from the user profile database.
*   **Audit Logging:** Every notification trigger must write an audit record to the `notification_triggers` collection in MongoDB for backend verification.

---

## 🔄 SOP 4: Realtime Synchronization & Context Cleansing

To maintain consistent state representation across multiple concurrent roles:

### 1. Role-Switching Cache Purging
*   **The Issue:** When switching between Customer, Vendor, Admin, and Delivery views in the dashboard, stale context caches can leak private queue states.
*   **The Solution:** Every role-switch action must explicitly purge local state stores, context providers, and data fetch hooks, ensuring only fresh, authorized, role-appropriate queues are rendered.

### 2. TV Display Synchronicity
*   The **TV Queue Display** must sync live status transitions immediately upon database mutations without requiring a manual refresh. Spacing and component sizes on the TV dashboard must be optimized for dense, long-distance readability.
