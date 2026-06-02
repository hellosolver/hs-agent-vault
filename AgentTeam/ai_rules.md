# HelloSolver: Strict AI Operational Rules (AI Rules)

This document defines the **Strict AI Operational Rules** and **Behavioral Laws** that govern every AI Agent in the HelloSolver development pipeline. All agents must enforce these principles at all times.

---

## 🚫 1. Core Visual Sizing Locks (Tailwind Classes)
*   **Rule:** You are absolutely prohibited from modifying, overriding, or restructuring the locked Tailwind CSS classes, padding, heights, or dimensions of standard UI components defined in `dev_rules.md`.
*   **Locked Elements:** ServiceCard CTAs, ProductCard CTAs, Compact/Normal Nav buttons, and mobile Hero Quick Actions.
*   **Consequence:** Any layout deviation or modification is a visual QA regression **FAIL**.

---

## 🔒 2. Database & API Payload Security
*   **Rule:** Protect MongoDB from account takeovers and malicious payload injection.
*   **Safe Mutation Protocol:** When updating user profiles, active bookings, or catalogs, backend API controllers must explicitly sanitize update payloads by stripping `id` and `uid` fields before executing Mongo updates.
*   **CORS Checks:** CORS rules must strictly match Vite dev (`5173`) and Express API bridge (`5181`) ports. No wildcards (`*`) are allowed in staging/production environment configs.

---

## ⚡ 3. UI Loader & Asynchronous Button Laws
*   **Rule:** Every interactive button triggering backend API requests must display immediate loader feedback and disable itself on click to prevent double submissions.
*   **Finally Block Reset:** The loading state must ALWAYS be cleared inside the `finally` block of the try/catch loop. Leaving spinners spinning indefinitely on API errors is a strict **FAIL**.
*   **Dynamic Translation:** Dropdowns and option text pickers must decode HTML entities (e.g. render "Nearest & Top Rated" instead of raw `&amp;` strings).

---

## 📂 4. Test Data & Zero Placeholder Principle
*   **Rule:** Never use dummy data, stock placeholders, or `lorem ipsum` mock text.
*   **E2E Test Accounts:** All automated and manual testing runs must exclusively use the authorized credentials documented in `realtestuser.txt.example`.
*   **No Random Profiling:** Do not register fake users, mock vendors, or bypass Firebase OTP credentials during testing.

---

## 🔄 5. Multi-Role Context & Cache Cleansing
*   **Rule:** Ensure absolute cache separation when switching dashboard roles.
*   **Cache Flushing:** Toggling between Customer, Vendor, Admin, and Delivery views must completely clear local state caches and context stores to prevent stale or mixed queues from rendering.
*   **TV Sync Legibility:** Duplicate TV displays must mirror live database status transitions in real-time without layout shifts or text overlaps.

---

## 🌐 6. IT Solutions Redirection Gate
*   **Rule:** Local integration of custom B2B enterprise software consulting pages is prohibited.
*   **External Routing:** All B2B IT solutions and consulting links must trigger a clean route-level redirect to the external domain `https://www.hellosolvertech.com/` in a new tab (`target="_blank" rel="noopener noreferrer"`).

---

## 🤖 7. AI Token & Instruction Optimization
*   **Low-Token / High-Precision:** Keep communication direct, factual, and strictly technical. Avoid conversational fluff, hypothetical assumptions, or generic design templates.
*   **Active Context Harvesting:** Before editing code or writing specifications, you must read naye `.ai-control/` files to discover active staging guidelines and environment indexes.
