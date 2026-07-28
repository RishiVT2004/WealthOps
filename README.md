# WealthOps: B2B SaaS for Financial Advisors

## The Elevator Pitch
**WealthOps** is a B2B SaaS workflow platform for Independent Financial Advisors (IFAs). It automates the two most manual aspects of wealth management: rules-based portfolio generation (aligned with SEBI RIA non-discretionary compliance) and automated client fee billing with payment reconciliation (EIPP).

---

## 1. Core Feature Summary

### The Portfolio Engine (Asset Allocation)
* **Risk-Adjusted Generation:** Takes a client's age, risk tolerance, and time horizon to generate a mathematical asset allocation split (e.g., Equity, Debt, Gold).
* **Fund Mapping:** Maps the asset classes to specific, low-cost Indian mutual fund categories (e.g., Flexi-Cap, BAF, Nifty 50 Index).

### The Audit Layer & Friction Calculator (Quality Control)
* **Automated Surveillance:** Monitors the recommended funds for structural changes, such as an AMC raising a fund's expense ratio above a set threshold (e.g., 0.07%).
* **The Friction Calculator:** Before alerting an advisor to switch funds, the system calculates the financial friction (Exit Loads + STCG/LTCG taxes) to ensure the switch is mathematically profitable for the client.

### The EIPP Engine (Order-to-Cash Automation)
* **Automated Invoicing:** Calculates the advisor's AUM-based fee or monthly retainer and generates an electronic invoice.
* **Automated Reconciliation:** Matches incoming payments to open invoices. 
* **Dispute/Short-Pay Resolution:** Flags underpayments as "Exceptions" and triggers automated dunning (collection) workflows.

### B2B Workflows & Dashboards (The User Experience)
* **Maker-Checker Approvals:** Because IFAs operate under non-discretionary rules, the advisor (Maker) recommends a portfolio change, and the system sends an automated WhatsApp/Email link to the client (Checker) for final execution approval.
* **Advisory Dashboard:** A unified view of Total Assets Under Management, Unpaid Invoices, and Client Portfolio Exceptions.

---

## 2. Core Technology Stack

* **Python (FastAPI):** The core backend. Handles the business logic, the Friction Calculator math, and invoice generation. (FastAPI is chosen for its speed and auto-generated API documentation).
* **PostgreSQL (SQL):** The transactional database. Stores strict, relational data: Advisors, Clients, Invoices, and Payment ledgers.
* **MongoDB (NoSQL):** The document database. Stores heavy, unstructured data: daily mutual fund NAVs, expense ratio histories, and immutable audit logs of client trade approvals.
* **n8n:** The automation and orchestration engine. Replaces heavy Python integration code for sending emails, WhatsApp messages, and routing payment webhooks.
* **Metabase:** The presentation and BI layer. Plugs directly into PostgreSQL to generate the visual dashboards for the advisor.
* **Figma / Draw.io:** For building the high-fidelity UI mockups and drawing the B2B system architecture diagrams for the PM portfolio.

---

## 3. Layer-by-Layer Task Breakdown

### Python / FastAPI Layer
* Write the API endpoint that accepts client risk data and returns a JSON portfolio split.
* Code the `FrictionCalculator` function (input: current fund, proposed fund, capital gains tax rate; output: boolean `True/False` on whether to switch).
* Write the fee calculation script that runs monthly to generate invoice amounts.

### Database Layer (PostgreSQL & MongoDB)
* **SQL:** Design the relational schema. Create tables for `Advisor_Profile`, `Client_Profile`, `Client_Holdings`, and `Invoices` (with status fields like OPEN, CLOSED, SHORT_PAY).
* **NoSQL:** Set up a MongoDB collection to ingest and store raw JSON payloads from public Mutual Fund APIs (for the Audit Layer).

### Automation Layer (n8n)
* Build a webhook listener that triggers when an invoice is created in Postgres, formatting and sending an email to the client.
* Build the **Payment Matcher workflow**: Listens for a mock bank payment, searches Postgres for a matching Invoice ID, and updates the SQL status to CLOSED or SHORT_PAY.
* Build the **Maker-Checker workflow**: Sends a portfolio rebalance approval link to the client.

### Presentation Layer (Metabase)
* Connect Metabase to the Postgres database.
* Write the SQL queries inside Metabase to generate the Total AUM pie chart and the Outstanding Invoices bar chart.

---

## 4. Implementation Roadmap

### Phase 1: The Core Engine (Data & Logic) | Weeks 1-3
Start by designing the PostgreSQL database schema. Once the tables are set, write the FastAPI endpoints for the Portfolio Engine. You should be able to send an API request with client details via Postman and get a mathematically correct portfolio split back.

### Phase 2: The EIPP Reconciliation Layer | Weeks 4-5
Add the billing logic to FastAPI. Generate invoices and store them in Postgres. Then, build the core n8n workflow that mimics incoming payments and updates the SQL invoice statuses to handle matching and short-pays.

### Phase 3: The Audit Layer (NoSQL & Monitoring) | Weeks 6-8
Integrate a free Mutual Fund API to pull real-world expense ratios into MongoDB. Write the Python Friction Calculator and set up an n8n cron job to run daily checks for "Exceptions" (expense ratio spikes or asset drift).

### Phase 4: Dashboards & The PM Portfolio | Weeks 9-10
Connect Metabase to Postgres and build the Advisor Dashboard. Finally, write the Product Requirements Document (PRD), map out the architecture in Draw.io, and define the B2B Go-To-Market strategy for MBA/PM interviews.
