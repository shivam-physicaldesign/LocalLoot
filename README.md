**LocalLoot — Neighbourhood Footfall Network**



A hyper-local, pay-for-footfall discount platform connecting neighborhood shopkeepers, shoppers, and counter staff. Built for resilience against poor connectivity, natural language inputs, and multi-tenant shop isolation.



\* Live Deployed App: **https://shivam-physicaldesign.github.io/LocalLoot/**

\* Repository: https:**//github.com/shivam-physicaldesign/LocalLoot**



\---



\## **1. How to Run the App**



\### Option A: Open the Deployed Link

Open the live deployment link in any desktop or mobile browser:

https://shivam-physicaldesign.github.io/LocalLoot/



\### Option B: Run Locally

1\. Clone the repository:

&#x20;  git clone https://github.com/shivam-physicaldesign/LocalLoot.git

&#x20;  cd LocalLoot



2\. Open index.html directly in any web browser:

&#x20;  \* macOS: open index.html

&#x20;  \* Windows: double-click index.html or run start index.html

&#x20;  \* Linux: xdg-open index.html



No external dependencies, build steps, or backend servers are required. Everything runs directly in the browser using Tailwind CSS, Tesseract.js, QRCode.js, and HTML5-QRCode via CDN.



\---

\## **2. How the Platform Works (End-to-End Flow)**



LocalLoot models the complete lifecycle of neighbourhood commerce across three distinct user roles:



\### 🛍️ **Shopper Flow**

1\. **\*\*Browse Deals\*\***: The customer views live discounts nearby and filters by store (e.g., \*Satyam Jewelry's\* or \*Glow Cosmetic\*).

2\. **\*\*Free Claim\*\***: Tapping "Claim" generates an unambiguous 6-character coupon badge (e.g., `A4P8W1`) along with a camera-scannable QR code. Claims cost the customer nothing and deduct 0 points from the merchant.

3\. **\*\*Wallet Storage\*\***: Saved coupons stay in the shopper's wallet with active/redeemed statuses.



\### 🏪 **Shopkeeper Flow**

1\. **\*\*Prepaid Footfall Balance\*\***: Merchants preload points (e.g., 200–500 pts). Points are consumed only when walk-in customers convert in the physical shop.

2\. **\*\*Drafting Promotions\*\***: Shopkeepers can type natural language text (e.g., `flat 20% off all gold ornaments till diwali, min bill 2500`) or photograph physical display signboards using in-browser OCR.

3\. **\*\*Review \& Activation\*\***: Parsed details (discount, minimum order, validity) must be confirmed in a preview modal before saving as draft or activating live.

4\. **\*\*Footfall Statements\*\***: A monthly ledger tracks daily customer redemptions and points spent.



\### 🖥️ Store Counter (POS Desk) Flow

1\. **\*\*Customer Verification\*\***: The cashier types the customer's 6-character code or scans their QR badge with the device camera.

2\. **\*\*Idempotent Deduction\*\***: Verifying the code deducts exactly 10 points from the shop's balance, grants the discount, and marks the coupon redeemed with an audit timestamp.

3\. **\*\*Double-Spend Prevention\*\***: Rapid taps or re-entering used codes are immediately rejected by an in-flight debounce lock and ledger check.



\---



\## **3. Architectural Decisions and Key Assumptions**





|**Requirement / Challenge**|**Operational Solution**|**Technical Implementation \& Decision**|
|-|-|-|
|**\*\*Pay-For-Footfall Model\*\***|Deduct points only upon in-store redemption. Browsing, viewing, and claiming offers are 100% free.|Deducts exactly 10 points per counter scan. Low-balance stores are prevented from redeeming once balance hits 0.|
|**\*\*Flaky POS Network\*\***|Counter staff repeatedly tap the "Redeem" button when experiencing connection lag.|Two-tier idempotency: in-flight memory latch (isRedeeming) deduplicates clicks, backed by an audit ledger.|
|**\*\*Multi-Tenant Shop Boundary\*\***|Store owners and counter desks must not see, activate, or alter another store's promotions.|Complete data isolation: \*Satyam Jewelry's\* coupons are rejected if submitted at \*Glow Cosmetic's\* counter terminal.|
|**\*\*Messy Input \& Signboard OCR\*\***| Shopkeepers type casual fragments or take photos of physical promo boards.|Client-side OCR via Tesseract.js combined with regex parsing and a mandatory confirmation modal before drafting.|
|**\*\*Coupon Code Usability\*\***|Codes must be easy to read and pronounce out loud over noisy retail counters.|Unambiguous 6-character alphanumeric codes omitting lookalikes (`0`, `O`, `1`, `I`), with generated scannable QR tags.|
| **\*\*Date Standard \& Expiry\*\***|Dates must avoid regional month/day misinterpretations across Indian retail contexts.|Standardized strictly to `DD/MM/YYYY`. Expired deals automatically disable claim buttons and fail counter redemption.|
|**\*\*Store Decommissioning\*\***|Permanently closing a store must not leave broken promotions or orphaned claims in customer wallets.|Store deletion triggers a cascading cleanup: active offers are removed, counters close, and claims mark store inactive.|







\---



\## **4. AI Tools Used and Implementation**



\* Model Used: Google Gemini

\* UI Architecture and Theming: Designed the responsive layout and dynamic diagonal watermark overlay using Tailwind CSS.

\* Regex NLP Parser: Formulated pattern expressions for extracting messy promotion values, spending thresholds, and expiry dates.

\* Concurrency Safeguards: Structured the UI debounce latch and audit logging ledger to prevent duplicate payouts during connectivity drops.



\---



\## **5. Next Steps (With Another Week)**



1\. Atomic PostgreSQL Database: Replace browser localStorage with PostgreSQL and use SELECT FOR UPDATE row-level locks for transaction safety.

2\. Server-Side Multimodal Vision Model: Upgrade from client-side Tesseract.js to a multimodal vision API for complex, handwritten regional scripts.

3\. PWA and Offline Queueing: Use Service Workers and IndexedDB to validate cryptographically signed offline coupons and sync when back online.

4\. Geolocation Radius: Integrate browser Geolocation API with spatial queries to sort promotions strictly within 500m of the shopper.

