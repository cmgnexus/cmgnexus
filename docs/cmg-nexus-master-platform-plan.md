# CMG Nexus Master Platform Plan

Date: 2026-04-27

CMG Nexus is the central AI-led enterprise operating system for CMG. It is not
just a CRM and must not become a thin wrapper around another product. The system
will be built from scratch with PHP and MySQL as the first production stack, with
an API-first structure so it can later support a mobile app.

Pepperi, Zoho, Xero, Google Maps, email providers, and fleet tools can inform or
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

## 3. Build Baseline

CMG Nexus is a new build. Do not treat any previous CRM, prototype, copied
database, route, diagnostic page, or partial module as completed work.

Baseline decisions:

- Build from zero using PHP and MySQL.
- Design the first version as a secure web application.
- Keep the backend API-first so a mobile app can be added later without
  rewriting business logic.
- Use server-rendered admin and operations screens where they are faster to ship,
  but keep business actions behind clear service/API boundaries.
- Model tenancy, permissions, audit, events, and operational queues from the
  beginning instead of adding them after the CRM is built.
- Treat external services as integrations, not as the system of record.

Initial technical foundations to create:

- PHP application structure
- MySQL schema and migrations
- Environment configuration and secret handling
- Authentication and session handling
- Role and permission framework
- Organization/tenant model
- Audit logging
- Queue-ready background job structure
- File/document upload storage boundary
- API routes for future app access
- Basic admin UI layout
- Automated database backup and restore procedure

Security baseline:

- No public diagnostic endpoints.
- No committed credentials, tokens, database passwords, FTP credentials, or API
  keys.
- Locked-down database and file access.
- Centralized error handling that does not expose sensitive details.
- Audit logs for sensitive user, tenant, role, and platform actions.
- Owner/admin accounts prepared for future two-factor authentication.

## 4. Technical Direction

CMG Nexus will be built from zero as a PHP and MySQL platform.

Primary technical direction:

- Backend: PHP.
- Database: MySQL.
- First interface: responsive web platform for desktop and mobile browsers.
- Future interface: mobile app built on top of the same platform API.
- Architecture style: API-first so operational workflows, dashboards, and the
  later mobile app use consistent business rules.
- Data model: tenant-aware from the first migration, not added later as a patch.
- AI integration: service layer that can classify, summarize, score risk, draft
  actions, and create operational events without owning the source of truth.
- External integrations: optional connectors for accounting, maps, email, OCR,
  and fleet tools where they add value.

Implementation principles:

- Start with clean schemas, migrations, and seed data.
- Keep business rules in server-side services, not scattered across templates.
- Use role and tenant checks in every query path that touches business data.
- Design endpoints so mobile app screens can be added without rewriting core
  logic.
- Keep audit logs for sensitive platform, tenant, finance, and user actions from
  the beginning.

## 5. Platform Owner Model

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

Target access:

- Login URL: `/owner-login` or another dedicated platform-owner route.
- Post-login console: `/platform`.
- Role: `platform_owner`

Initial Platform Owner console should include:

- Tenant control
- Platform user list
- Platform development registry
- Tenant rollout/application status

Required protections:

- Platform Owner cannot be disabled, deleted, or demoted by tenant admins.
- Sensitive owner actions must be audited.
- Critical owner/admin actions should eventually require re-authentication and
  two-factor authentication.

## 6. Tenant and Organization Model

CMG Nexus follows a Xero-style organization model:

- User accounts are separate from organization membership.
- One user can belong to one or more organizations.
- Roles are scoped per organization.
- Platform Owner can see across all organizations.
- Organization Owner/Admin can manage only their organization.

Initial tenant tables to create:

- `organizations`
- `organization_memberships`

Target behavior:

- Every user account is independent from organization membership.
- Invitations belong to an organization.
- User lists show organization membership and tenant-scoped roles.
- User profiles show organization membership.
- The header can show and switch the current organization when a user belongs to
  more than one organization.

Data-tenancy rule:

- Business tables are created with `organization_id` from the first migration
  unless they are explicitly platform-global tables.

## 7. Target Architecture: Eight Engines

### 7.1 Identity and Permission Engine

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

### 7.2 Operational Event Engine

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

### 7.3 Work Queue and SLA Engine

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

### 7.4 Document and Email Intelligence Engine

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

### 7.5 Commercial Execution Engine

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

### 7.6 Warehouse and Distribution Engine

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

### 7.7 Map and Fleet Movement Engine

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

### 7.8 AI Insight and Reporting Engine

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

## 8. Required Modules

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

## 9. Phase Plan

### Phase 0: Foundation and Security Baseline

Goal: create the secure PHP/MySQL foundation before business modules are built.

Tasks:

- Create the PHP project structure.
- Create MySQL migration and seed workflow.
- Add environment configuration without committed secrets.
- Implement authentication, sessions, password hashing, and logout.
- Create the initial responsive web layout.
- Add API route structure for future mobile app use.
- Add centralized authorization middleware.
- Add centralized error handling that hides sensitive details.
- Add audit logging for sensitive actions.
- Add backup and restore procedure for MySQL.
- Confirm there are no public diagnostic endpoints from the beginning.
- Document deployment, filesystem permissions, database access, and secret
  rotation rules.

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

## 10. Immediate Development Order

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

## 11. Definition of Success

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

## 12. Guiding Principle

Do not build a lazy system.

Build a system that helps good people see what they missed, gives managers the
truth early, and makes every department look professional.

