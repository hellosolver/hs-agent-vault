# UI/UX & Coding Standards (Visual & Design Rules)

This document establishes the unified **Visual & Design Standards** for all applications. All developer and tester agents must strictly adhere to these visual dimensions, loader patterns, and spacing systems to guarantee a highly responsive, uniform, and premium user experience.

---

## 📐 1. Spacing & Spacing Grid System

To maintain visual rhythm and eliminate arbitrary layouts, all margins, paddings, and alignment dimensions must conform to a strict 4px/8px-based grid hierarchy:

- **Compact Spacing (4px / 8px):** Used for item-internal details, small component paddings, and utility gaps.
- **Normal Spacing (12px / 16px):** Standard padding for dialog containers, list items, card paddings, and form controls.
- **Generous Spacing (24px / 32px):** Container-level margins, hero section dividers, and structural spacing.

*Coding Standard:* Under Tailwind CSS, use exclusively standard grid classes (e.g., `p-1` (4px), `p-2` (8px), `p-3` (12px), `p-4` (16px), `p-6` (24px), `p-8` (32px)). Custom arbitrary spacing (e.g. `p-[17px]`) is strictly prohibited.

---

## 🔒 2. Component Dimension Sizing Locks

To prevent layout shifts and display overflows under dynamic localized text, the following standard UI components are strictly locked to these dimensions:

### A. Primary Action Cards (ServiceCard, ProductCard, CatalogCard)
- **Minimum Card Height:** `min-h-[220px]` (standard catalog items must maintain vertical consistency).
- **CTA Button Padding:** `py-2.5 px-4` (minimum touch targets must be maintained).
- **Typography Sizing:** Titles must use `text-base font-semibold`, subtitles `text-sm text-gray-500`.

### B. Navigation & Control Bars
- **Navigation Bar Height:** Locked to `h-16` (64px) on desktop, and `h-14` (56px) on mobile viewports.
- **Mobile Bottom Navigation Grid:** Standard bottom bars must center key actionable buttons. Touch targets must be a minimum of `44px` in width and height.

---

## 🔄 3. Dynamic UI Loaders & Button Mutators

To protect backend state integrity and prevent double transaction triggers, all mutation/API buttons must strictly implement the standard loader pattern:

1. **Immediate State Change:** Disable the button and display an active loader icon/spinner immediately upon click.
2. **Deterministic Reset:** The active state must be reset inside a deterministic `finally` block to ensure buttons never remain permanently disabled.
3. **Spinner Centering:** Spacing and spinner alignment must preserve original button heights.

---

## 📱 4. Mobile-First Layout Hierarchy

All pages must stack elements naturally on mobile viewports:
- **Responsive Wrappers:** Grid structures must use responsive columns (e.g., `grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3` or `flex flex-col md:flex-row`).
- **Zero Horizontal Scroll:** Horizontal overflows on standard pages are an absolute visual QA **FAIL**.
- **Touch Target Padding:** Interactive fields, input boxes, and buttons must ensure a minimum touch-action boundary of `44px`.

---

## 🌐 5. Dropdown & Select Picker Decoding

Option lists, dropdown selectors, and pickers must cleanly decode HTML characters.
- **The Protocol:** Always render fully decoded character entities (e.g., use standard characters like `&` rather than raw escaped strings like `&amp;`).
