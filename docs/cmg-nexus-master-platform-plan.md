# CMG Nexus Master Platform Plan

Date: 2026-04-27

CMG Nexus is the central AI-led enterprise operating system for CMG. It is not
just a CRM and must not become a thin wrapper around another product. Pepperi,
Zoho, Xero, Google Maps, email providers, and fleet tools can inform or
integrate with selected workflows, but CMG Nexus remains the owned operational
control layer.

## 1. Core Vision

CMG Nexus must become the system that catches what people miss.

The platform coordinates:

- Head office
- Warehouse
- Field sales
- Finance
- Purchasing
- Collections
- Distribution and drivers
- Managers
- Documents and emails
- Route activity
- B2B customers

AI is the operational layer, not a decoration. It should:

- Warn users before something becomes a problem.
- Show managers what is waiting, missed, blocked, risky, or overdue.
- Help sales representatives arrive prepared for customers.
- Help warehouse and dispatch teams see what is coming.
- Help finance control expenses, supplier bills, purchases, cash, and approvals.
- Read documents and emails, ask follow-up questions, and create draft actions.
- Report the truth clearly through desktop dashboards and mobile text-first
  summaries.

## 2. Platform Direction

CMG Nexus is the main platform. External systems may be integrated where useful,
but they do not become the center of the business.

Rationale:

- Pepperi is strong for field sales and B2B ordering, but weak for full finance,
  warehouse, cash, distribution, custom AI operations, and executive control.
- Zoho is broad, but still requires heavy configuration and custom workflows.
- CMG needs a business-specific operating system rather than a generic CRM.

## 3. Current State

The live implementation described by the platform brief exists in a separate
parallel environment:

- Original CRM path: `/crm`
- Nexus path: `/nexus`
- Nexus database: separate cloned database
- Existing CRM data copied into the Nexus database
- `/nexus` runs independently from `/crm`

Existing foundations described by the brief:

- Customers, users, roles, invitations, sales reps, and zones
- Tasks, notes, reports, search, audit logs, and Command Center
- Items, warehouse inventory, suppliers, orders, and order lines
- Invoices, payments, credit notes, aged receivables, approval queue, and price
  rules
- Routes, route stops, and live location foundations
- Xero/accounting sync foundations
- AI assistant chat and learning memory
- Notification queue

Security status:

- `/nexus/diag-aging/` was public and has been disabled with `403`.
- Remaining security work includes rotating exposed credentials and API keys,
  locking down FTP and database access, and confirming no other diagnostic
  endpoints or logs are public.

## 4. Platform Owner Model

The Platform Owner is a platform governance role, not a normal administrator.

Platform Owner responsibilities:

- Own all tenants and organizations.
- Manage platform-level users.
- Manage platform developments.
- Apply developments to selected tenants.
- Control release status.
- See all organizations.
- Access platform-level audit and governance.
- Own critical platform settings and integrations.

Current described access:

- Login URL: `/nexus/owner-login`
- Post-login console: `/nexus/platform`
- Role: `platform_owner`

Current Platform Owner console includes:

- Tenant control
- Platform user list
- Platform development registry
- Tenant rollout/application status

Required protections:

- Platform Owner cannot be disabled, deleted, or demoted by tenant admins.
- Sensitive owner actions must be audited.
- Critical owner/admin actions should eventually require re-authentication and
  two-factor authentication.

## 5. Tenant and Organization Model

CMG Nexus follows a Xero-style organization model:

- User accounts are separate from organization membership.
- One user can belong to one or more organizations.
- Roles are scoped per organization.
- Platform Owner can see across all organizations.
- Organization Owner/Admin can manage only their organization.

Existing tenant tables:

- `organizations`
- `organization_memberships`

Current described behavior:

- Existing users were linked to a default organization.
- Invitations belong to an organization.
- User list shows organization membership.
- User profile shows organization membership.
- Header can show and switch the current organization.

Next step:

- Move business data to a tenant-aware structure by adding `organization_id` to
  important tables, backfilling current records, and filtering data by current
  organization.

## 6. Target Architecture: Eight Engines

### 6.1 Identity and Permission Engine

Controls platform owner, organization owner, admin, manager, department user, and
external user access.

Needed capabilities:

- Separate platform-level rights from tenant-level rights.
- Tenant-scoped role assignment.
- Organization Owner role.
- Permission matrix.
- Platform Owner hard protection.
- Audit logs for sensitive actions.
- Critical action re-authentication.
- Two-factor authentication for owner/admin accounts.

### 6.2 Operational Event Engine

Everything important becomes an event.

Example events:

- Overdue invoice
- Missed visit
- Incoming important email
- Uploaded supplier invoice
- Pickup due in 3 days
- Low stock
- Delivery shortage
- Wrong item delivered
- Return requested
- Cash variance
- Route deviation
- Customer complaint
- Minimum price approval required

Core table: `operational_events`

Required event fields:

- Severity
- Owner
- Due date
- SLA
- Status
- Source module
- Related customer, order, invoice, supplier, or user
- AI summary

### 6.3 Work Queue and SLA Engine

Turns operational events into action.

Needed capabilities:

- Role-based queues
- Assignment
- Due dates
- Snooze
- Escalate
- Resolve
- Manager visibility
- SLA breach tracking

### 6.4 Document and Email Intelligence Engine

Reads incoming documents and emails, understands meaning, and creates actions.

Needed capabilities:

- Multi-mailbox connection
- Email classification and importance scoring
- AI response draft suggestions
- Manager preference learning
- PDF/image upload
- OCR and extraction
- Draft purchase, expense, or supplier bill creation
- Follow-up questions
- Link email/document to customer, order, invoice, or supplier

Email categories:

- Order received
- Supplier invoice
- Debt reminder
- Complaint
- Warning
- Pickup instruction
- Delivery instruction
- Payment promise
- Internal escalation
- Pricing issue
- Stock issue
- Legal or urgent risk

### 6.5 Commercial Execution Engine

Makes sales reps professional and informed.

Needed capabilities:

- Customer 360
- Product history
- Last purchased products
- Reorder signals
- Commercial gaps
- Stock-aware selling
- Customer-specific price visibility
- Minimum price approval
- Margin exception alerts
- Promotions
- Quote and order flow
- B2B portal later

Sales representatives must know before each customer visit:

- Customer debt
- Last orders
- Usual products
- Missing products
- Open issues
- Stock availability
- Pricing limits
- Promises made
- Delivery problems

### 6.6 Warehouse and Distribution Engine

Moves from inventory visibility to operational warehouse and delivery control.

Warehouse modules:

- Receiving
- Putaway
- Bin/location control
- Stock counts
- Discrepancy handling
- Inventory reservations
- Pick waves
- Pick tasks
- Packing
- Dispatch staging
- Low stock and replenishment

Distribution modules:

- Runsheets
- Driver assignment
- Delivery stops
- Proof of delivery
- Shortage notes
- Wrong item notes
- Damage notes
- Failed delivery
- Returns
- Return authorization
- Delivery exception workflow

Delivery principle:

- If goods go out incomplete or wrong, the driver or distribution user records
  the exception at delivery time.

### 6.7 Map and Fleet Movement Engine

Provides fleet-level visibility and accountability where useful.

Needed capabilities:

- Live map
- Driver and vehicle assignment
- Field rep tracking
- Route plan versus actual
- Geofence arrival/departure
- Stop duration
- Route replay
- Missed stop detection
- Off-route detection
- Driver scorecards
- Vehicle inspection/checklist
- Mobile text-first driver interface

The map must control movement, exceptions, and accountability, not only show
pins.

### 6.8 AI Insight and Reporting Engine

Continuously shows management what is missed.

Needed capabilities:

- Executive Command Center
- Exception reports
- Desktop charts
- Mobile text summaries
- AI daily brief
- AI role brief
- Risk scoring
- Trend detection
- Owner, action, and deadline on every alert

Desktop experience:

- Charts
- Tables
- Maps
- Drilldowns

Mobile experience:

- Short text-first summaries
- Action buttons

## 7. Required Modules

### Platform Layer

- Platform Owner Console
- Tenant management
- Platform users
- Platform developments
- Tenant rollout
- Platform audit
- Secret/config governance

### Organization Layer

- Organization profile
- Organization users
- Organization roles
- Organization settings
- Xero tenant connection
- Organization data isolation

### CRM Layer

- Customers
- Contacts
- Notes
- Tasks
- Visit cadence
- Customer groups
- Exclusion from reports
- Customer promises
- Complaints/cases

### Sales Layer

- Field sales dashboard
- Customer briefing
- Product history
- Reorder signals
- Commercial gaps
- Order taking
- Quote push
- Minimum price approval
- Promotions
- B2B portal

### Collection Layer

- Aged receivables
- Collection priorities
- Promise to pay
- Broken promise alerts
- Customer risk
- Collector queue
- Sales rep debt visibility
- Excluded company governance

### Finance Layer

- Purchase requests
- Purchase orders
- Supplier bills
- Employee expenses
- Receipt upload
- OCR/AI extraction
- Finance approval
- Accounting export/sync
- Cash in/out ledger
- Cash handover
- Cash count
- Cash variance

### Warehouse Layer

- Items
- Locations
- Inventory levels
- Movements
- Receiving
- Putaway
- Pick/pack
- Dispatch
- Stock count
- Discrepancy workflow

### Distribution Layer

- Runsheets
- Driver stops
- Delivery proof
- Shortages
- Wrong item
- Damage
- Returns
- Failed delivery
- Delivery exception reporting

### Operations Layer

- Operational events
- Work queues
- SLA
- Escalations
- Manager alerts
- Document-driven workflows
- Pickup/deadline warnings

### Email Layer

- Multi-mailbox reader
- AI classification
- Action creation
- Response drafts
- Manager review
- Follow-up tracking

### Map/Fleet Layer

- Live tracking
- Route replay
- Geofence
- Driver/vehicle management
- Field rep location
- Route adherence
- Stop proof

## 8. Phase Plan

### Phase 0: Stabilize and Secure

Goal: make `/nexus` safe and reliable before heavy feature building.

Tasks:

- Rotate credentials and API keys.
- Check all public diagnostic endpoints.
- Restrict FTP and database access.
- Confirm error logs are not public.
- Verify database schema differences.
- Fix known `orders.archived_at` issue if still present.
- Confirm backup process.
- Confirm `/crm` and `/nexus` are fully independent.

Known status:

- `/nexus` parallel environment exists.
- `/nexus/diag-aging/` disabled.
- Platform Owner role exists.
- Platform Owner Console exists.

### Phase 1: Platform and Tenant Control

Goal: make CMG Nexus a true platform.

Tasks:

- Complete Platform Owner Console.
- Add tenant detail page.
- Add tenant status and health.
- Add tenant-scoped user roles.
- Add Organization Owner role.
- Protect Platform Owner from disable/delete/demotion.
- Add owner action audit logs.
- Add development release details.
- Add per-tenant feature flags.
- Add tenant rollout history.

Main tables:

- `organizations`
- `organization_memberships`
- `platform_developments`
- `platform_development_tenants`
- `platform_feature_flags`
- `platform_audit_events`

### Phase 2: Data Tenancy

Goal: make business data organization-aware.

Tasks:

- Add `organization_id` to key tables.
- Backfill current records to the default organization.
- Filter data by current organization.
- Let Platform Owner see across tenants.
- Let tenant users see only their tenant.

Priority tables:

- `customers`
- `invoices`
- `invoice_lines`
- `payments`
- `credit_notes`
- `items`
- `inventory_levels`
- `orders`
- `order_lines`
- `routes`
- `route_stops`
- `tasks`
- `notes`
- `approval_queue`
- Reports/cache tables
- AI events/memories

### Phase 3: Operational Event and Work Queue

Goal: build the system that catches missed work.

Tasks:

- Create `operational_events`.
- Create `work_queue_items`.
- Create SLA and escalation rules.
- Convert receivables, visits, approvals, low stock, and delivery issues into
  events.
- Upgrade Manager Command Center.
- Build role dashboards.

### Phase 4: Finance, Purchasing, Expense, Cash

Goal: control money flows.

Tasks:

- Purchase request module.
- Purchase order module.
- Supplier bill upload and extraction.
- Expense upload and extraction.
- Finance approval workbench.
- Cash in/out ledger.
- Cash count and handover.
- Cash variance alerts.
- Xero export/sync queue.

### Phase 5: Email and Document Intelligence

Goal: make AI read incoming business communications.

Tasks:

- Connect multiple mailboxes.
- Classify emails.
- Detect urgent/risky emails.
- Ask managers how to respond.
- Learn response style.
- Draft response options.
- Convert emails into tasks/events/orders/expenses/complaints.
- Connect document upload to operational workflows.

### Phase 6: Warehouse and Distribution

Goal: create serious warehouse and delivery control.

Tasks:

- Receiving
- Putaway
- Picking
- Packing
- Dispatch staging
- Runsheets
- Delivery exceptions
- Returns
- Proof of delivery
- Warehouse manager board
- Pickup 3-day warning workflow

### Phase 7: Map/Fleet and Field Control

Goal: create real movement visibility.

Tasks:

- Live driver/rep map
- Route adherence
- Geofence proof
- Route replay
- Missed stop alerts
- Driver scorecards
- Vehicle records
- Vehicle inspections
- Mobile field text-first interface

### Phase 8: B2B Portal and External Users

Goal: expose controlled self-service after internal truth is reliable.

Tasks:

- B2B customer login
- Customer catalog
- Customer order history
- Reorder suggestions
- Account balance/statement
- Order status
- Delivery status
- Customer documents

Rule:

- B2B comes after internal stock, price, order, delivery, and receivable controls
  are reliable.

### Phase 9: Advanced AI Reporting

Goal: turn data into executive intelligence.

Tasks:

- AI daily brief
- AI role brief
- Sales risk
- Collection risk
- Warehouse risk
- Cash risk
- Delivery risk
- Supplier risk
- Margin risk
- Executive chart dashboard
- Mobile text summary
- AI recommended next action

## 9. Immediate Development Order

1. Finish Platform Owner Console:
   - Tenant detail page
   - Tenant users
   - Tenant feature flags
   - Platform audit
2. Build tenant data isolation:
   - Add `organization_id`
   - Backfill data
   - Filter queries
3. Protect Platform Owner:
   - Cannot delete
   - Cannot suspend
   - Cannot remove platform owner role
   - Critical action audit
   - Two-factor authentication later
4. Build Operational Event Engine:
   - Events
   - Work queues
   - SLA
   - Escalations
5. Upgrade Command Center:
   - Missed work
   - Overdue work
   - Blocked work
   - Risk by department
   - Owner, action, and deadline

## 10. Definition of Success

CMG Nexus is successful when:

- Management sees what is being missed without asking people.
- Sales reps are fully informed before visiting customers.
- Warehouse knows what is coming before it becomes urgent.
- Finance controls purchase, expense, cash, and supplier bill flows.
- Distribution records delivery problems at the moment they happen.
- Emails and documents become structured actions.
- AI explains what matters, who owns it, and what should happen next.
- Tenants are cleanly separated.
- Platform Owner controls development and rollout.
- Desktop shows rich dashboards and charts.
- Mobile shows short, text-first operational actions.

## 11. Guiding Principle

Do not build a lazy system.

Build a system that helps good people see what they missed, gives managers the
truth early, and makes every department look professional.

