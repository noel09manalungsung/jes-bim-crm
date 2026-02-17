# JES BIM CRM — Requirements Document

**Project:** JES BIM Customer Relationship Management System
**Company:** Joseph Engineering Services (JES BIM)
**Version:** 1.0
**Test:** This is a test comment to verify GitHub sync is working.
**Date:** February 17, 2026

---

## 1. Project Overview

JES BIM requires a custom CRM system to manage the early stages of business development — from finding potential clients to qualifying them and converting them into deals. This first version (v1) will serve two core teams: **Marketing** and **Sales**, each with their own dashboard and responsibilities.

The system tracks every lead from initial discovery through email campaigns, qualification, sales nurturing, meetings, and deal conversion (RFQ received).

*Note: The **Estimation Team** module (proposal building, Q&A review, manager approval, negotiation, and project handoff) will be added in a future phase once the Marketing and Sales workflows are fully operational.*

---

## 2. Company Background

JES BIM is a digital engineering consultancy specializing in Building Information Modeling (BIM) and Virtual Design and Construction (VDC) for the Architecture, Engineering, and Construction (AEC) industry. Headquartered in Dubai with teams in India and Germany, the firm serves contractors, consultants, and developers across the Middle East and DACH region.

**Core Services:**
- BIM Modeling & Coordination (Architecture, Structure, MEP, Civil)
- Digital Twins & IoT Integration
- VDC Services (4D Scheduling, 5D Cost Estimation)
- BIM Implementation & Consulting (ISO 19650)
- Recruitment & Staffing for BIM Specialists

---

## 3. Users & Roles

| Role | Team Size | Users | Primary Responsibility |
|------|-----------|-------|----------------------|
| Marketing Team | 3 users | 2 Data Gatherers (source & enrich leads), 1 Campaign Manager (selects leads and uploads to Instantly.ai for cold emailing) | Find leads, run email campaigns, qualify and forward to Sales |
| Sales Team | 3 users | 1 per market: Middle East (UAE & KSA), Dutch (Netherlands), German (Germany) | Contact qualified leads, schedule meetings, convert leads to deals with RFQs |
| Admin | 1–2 users | System administrators | Manage users, oversee all data, system configuration |

*Note: The Estimation Team (2–5 users: estimators and proposal managers) will be added in a future phase.*

**Sales Market Coverage:**
- **Middle East Market** — UAE and KSA (1 Sales rep)
- **Dutch Market** — Netherlands, with more companies being added soon (1 Sales rep)
- **German Market** — Germany, with more companies being added soon (1 Sales rep)

---

## 4. Lead Lifecycle

Every potential client moves through a defined journey (v1 covers Marketing and Sales):

```
Data Gatherers source leads (Apollo.io / LinkedIn CSV) →
Upload to CRM Database (with duplicate check) →
Campaign Manager sees new qualified leads → Selects leads for targeting →
Leads auto-pushed to Instantly.ai via API → Cold email campaign runs →

IF lead replies (via Instantly.ai auto-sync):
  → AI automatically reads email → Extracts useful data (phone numbers, new emails, referred contacts, etc.)
  → AI classifies reply as: Sales Qualified Lead (SQL), Marketing Qualified Lead (MQL), or Spam

  → If Sales Qualified Lead (SQL) → Forwarded to Sales Team immediately
     (Interested in services, forwarded to right person, has BIM team, or "not now but later")
  → If Marketing Qualified Lead (MQL) → Stays with Marketing for nurturing
     ("Not interested", out-of-office replies, or potential future leads)
     → Parked for 6 months → Email re-verified → Re-added to Instantly.ai campaign
  → If Spam → Flagged and archived (auto-replies with no info, nonsense replies)

IF lead does NOT reply after 15 days:
  → Auto-forwarded to Sales Team for outreach →
  Sales Step 1: LinkedIn Outreach (15 days) →
  Sales Step 2: Cold Calling (if no LinkedIn reply) →
  Sales Step 3: Cold Meeting (if no Cold Calling reply — stays for 6 months) →
  After 6 months → Recycled back to Data Gatherers for re-enrichment →
  Re-added to new campaign

IF lead engages at any Sales step:
  → Nurture (Hot/Warm/Cold) → Schedule meetings →
  Lead provides RFQ + project details →
  Lead converted to Deal → [Handed to Estimation Team — future phase]
```

*Future phase (Estimation): Deal assigned to Estimation → Estimation analyzes & builds proposal → Q&A review → Estimation Manager approves proposal → Proposal sent to client → Waiting for Approval (client reviewing) → Client negotiation → Win or Loss*

**Statuses by Team:**

Each team uses its own set of statuses relevant to their workflow stage (v1 covers Marketing and Sales):

**Marketing Lead Statuses** (based on cold email campaign engagement):
- Not Contacted — lead uploaded to database but not yet included in any campaign
- Email Sent — cold email campaign sent via Instantly.ai (auto-pushed via API)
- Replied — recipient replied to the email (auto-updated from Instantly.ai) → **AI auto-classifies** the reply
- No Reply — automatic status, applied after 15 days of no response → **auto-forwarded to Sales for outreach**
- Sales Qualified Lead (SQL) — AI classified as high intent → **auto-forwarded to Sales Team**
- Marketing Qualified Lead (MQL) — AI classified as potential future lead → stays with Marketing, **parked for 6 months**, then email re-verified and re-added to a new Instantly.ai campaign
- Spam — AI classified as auto-reply with no info or nonsense → flagged and archived

**Sales Lead Statuses** (based on outreach escalation and proximity to RFQ):
- LinkedIn Outreach — lead received from Marketing (no reply to email), Sales contacts via LinkedIn (15 days)
- Cold Calling — no reply from LinkedIn outreach, escalated to phone calls
- Cold Meeting — no reply from cold calling, lead parked here for up to 6 months before recycling back to Data Gatherers
- Hot — actively engaged, likely to provide RFQ soon
- Warm — interested but not yet ready to commit
- Meeting Scheduled — discovery call or meeting booked
- RFQ Received — lead has submitted an RFQ and project details → **converted to Deal**

*Note: Once a lead provides an RFQ along with their project details, the lead is converted into a **Deal** and handed to the Estimation Team.*

**Sales Outreach Escalation Flow:**
Leads that don't reply to cold emails are automatically forwarded to Sales and follow this escalation path:
1. **LinkedIn Outreach** (15 days) — Sales rep reaches out via LinkedIn
2. **Cold Calling** (if no LinkedIn reply) — Sales rep calls the lead
3. **Cold Meeting** (if no cold calling reply) — lead is parked for up to **6 months**
4. After 6 months in Cold Meeting → **recycled back to Marketing Data Gatherers** for re-enrichment and re-verification, then re-added to a new campaign

At any point during this escalation, if the lead engages (replies, shows interest, requests info), the Sales rep can change the status to Hot, Warm, or Meeting Scheduled and proceed with normal nurturing.

**Estimation Deal Statuses** *(future phase — documented here for reference):*
*New Deal, Analyzing, Estimating, Proposal Draft, Q&A Review, Pending Approval, Approved, Proposal Sent, Waiting for Approval, Negotiating, Won, Lost, On Hold*

---

## 5. Marketing Team Requirements

### 5.1 Lead Sourcing (Data Gatherers)
- **Data Gatherer 1 & 2** are responsible for sourcing leads from external tools (Apollo.io, LinkedIn Sales Navigator)
- They export leads as CSV files from these tools and upload them into the CRM database
- Add new leads manually through a form (for one-off entries)
- Required lead fields: Contact Name, Title, Company Name, Email, Phone, Industry, Company Size
- Optional fields: Company Revenue, Website, LinkedIn Profile URL, Location

**CSV Import — Duplicate Detection & Update Logic:**
- During bulk CSV upload, the system checks each lead's email against existing records in the database
- If a matching email is found (duplicate), the system **does not create a new record** — instead, it **updates the existing record** with the latest data from the CSV (e.g., last verified date, company name, title, phone, etc.)
- If no match is found, a new lead record is created
- After import, the system shows a summary: number of new leads created, number of existing leads updated, and any rows that failed validation

### 5.2 Lead Verification & Enrichment
- Mark leads as verified or unverified
- Track which data fields are missing and need enrichment
- Record the source of enrichment (Apollo, LinkedIn, manual research)
- Show verification status per lead (email verified, phone verified, company verified)

### 5.3 Campaign Management (Campaign Manager)
- Once leads are uploaded to the database by Data Gatherers, new qualified leads for campaigns become visible to the **Campaign Manager**
- The Campaign Manager reviews available leads and **selects which leads to target** for the next campaign
- Selected leads are **automatically pushed to Instantly.ai via API** — no manual export/upload needed
- Create and name email campaigns in the CRM
- Track engagement per lead per campaign (auto-synced from Instantly.ai):
  - Email sent date
  - Reply received (yes/no + content summary)
  - Bounce detected (yes/no + reason)
- Replies from Instantly.ai are **automatically synced back to the CRM database**
- Each reply is **automatically processed by AI** which:
  1. Reads the email content and extracts useful data (phone numbers, new email addresses, referred contacts, names, departments)
  2. Updates the lead record with any newly extracted information
  3. Classifies the reply as **Sales Qualified Lead (SQL)**, **Marketing Qualified Lead (MQL)**, or **Spam** (see Section 5.4 for classification rules)
  4. Routes the lead accordingly (SQL → Sales, MQL → Marketing nurture, Spam → archived)
- View campaign performance summary (reply rate, bounce rate, SQL/MQL/Spam breakdown)

### 5.4 AI Reply Classification & Lead Qualification

When a reply is received from Instantly.ai, AI automatically reads the email, extracts useful data, and classifies the lead into one of three categories:

**Sales Qualified Lead (SQL)** — forwarded to Sales Team immediately:
- a. Reply shows any interest in JES BIM's services
- b. Reply says they are not the right person but forwards/refers to the right department or contact person
- c. Reply indicates they have an internal BIM team, an external BIM team, or work with a third-party BIM company (i.e., they are confirmed BIM users)
- d. Reply says they don't need it at the moment (implies potential future need)

**Marketing Qualified Lead (MQL)** — stays with Marketing for nurturing:
- a. Reply says "Not interested" (explicit rejection, but potential for future outreach)
- b. Out-of-office reply that provides useful details (e.g., alternative contact, return date)
- c. Out-of-office reply indicating they are coming back soon
- d. Any reply that suggests potential as a future lead

**Spam** — flagged and archived:
- a. Auto-replies that don't contain any contact information or useful details
- b. Replies that don't make any sense or are irrelevant

**AI Data Extraction:** For every reply (SQL, MQL, or Spam), the AI extracts and saves any useful data found in the email, including: new phone numbers, additional email addresses, referred contact names and roles, department information, and company details. This data is automatically added to the lead record.

**Routing rules:**
- SQL leads are **auto-forwarded to the Sales Team** with all extracted data and classification notes
- MQL leads are **parked for 6 months**. After 6 months, their email addresses are **re-verified** (via Reoon/Neverbounce or manual check), and if still valid, they are **re-added to a new Instantly.ai campaign**
- Spam leads are **archived** and excluded from future campaigns
- Leads with **no reply after 15 days** are **auto-forwarded to the Sales Team** for outreach (LinkedIn → Cold Calling → Cold Meeting escalation)
- The Campaign Manager can **override** any AI classification manually if needed

### 5.5 Marketing Metrics Dashboard
*Note: Dashboard/analytics will be implemented in a later phase.*

---

## 6. Sales Team Requirements

### 6.1 Sales Inbox (Leads from Marketing)
Sales receives leads from Marketing through two channels:
- **Sales Qualified Leads (SQL):** Leads that replied to campaigns and were AI-classified as sales-ready (interested, referred contact, confirmed BIM user, or "not now"). These arrive with AI-extracted data (phone numbers, new contacts, department info) and classification notes
- **No-Reply Leads:** Leads that did not reply to cold email campaigns after 15 days (auto-forwarded)
- Each lead arrives with all collected data, campaign engagement history, and AI classification (if applicable)
- SQL leads can be directly nurtured (Hot/Warm/Meeting); No-Reply leads enter the outreach escalation flow starting with LinkedIn Outreach

### 6.2 Outreach Escalation Flow
Sales follows a structured escalation path for leads that didn't reply to cold emails:

**Step 1 — LinkedIn Outreach (15 days)**
- Sales rep reaches out to the lead via LinkedIn
- Log LinkedIn message details and track responses
- If the lead responds → move to Hot/Warm/Meeting Scheduled
- If no response after 15 days → escalate to Cold Calling

**Step 2 — Cold Calling**
- Sales rep calls the lead directly
- Log call details, outcomes, and notes
- If the lead responds → move to Hot/Warm/Meeting Scheduled
- If no response → escalate to Cold Meeting

**Step 3 — Cold Meeting**
- Lead is parked in Cold Meeting status for up to **6 months**
- Periodic check-ins may be attempted
- After 6 months with no engagement → **recycled back to Marketing Data Gatherers** for re-enrichment and re-campaign

### 6.3 Engaged Leads View
- See all leads that responded during any outreach step (LinkedIn, cold call, or meeting)
- View each lead's full history: source, enrichment data, campaign engagement, outreach log
- Filter leads by temperature (Hot/Warm), market, assigned rep, and date
- Search leads by name, company, or email

### 6.4 Lead Contact & Nurturing
- Log all outreach activities: LinkedIn messages, phone calls, emails
- Record call/meeting outcomes and notes
- Set follow-up reminders with dates
- Track response status per lead
- Update lead temperature: **Hot** or **Warm**

### 6.5 Meeting Management
- Schedule meetings with leads (discovery calls, demos, follow-ups)
- Record meeting details: date/time, duration, type, location/link
- Add internal and external attendees
- Write meeting notes and action items
- Track meeting status: Scheduled, Completed, Rescheduled, Cancelled

### 6.6 Lead Status Updates
- Update lead status as they progress through outreach or engagement
- Add notes with each status change

### 6.7 Lead-to-Deal Conversion & RFQ Handoff
- When a lead provides an RFQ along with project details, the **lead is converted into a Deal**
- The Deal record includes:
  - Organization (company) name
  - Contact person(s) associated with the deal
  - RFQ details, scope, and project requirements
  - All meeting notes and prior interaction history
- In v1, converted Deals are stored in the system and marked as ready for Estimation
- *Future phase: Assign the Deal to a specific Estimation Team member for proposal building*

### 6.8 Sales Metrics Dashboard
*Note: Dashboard/analytics will be implemented in a later phase.*

---

## 7. Admin Requirements

The Admin role has full visibility across all teams and is responsible for system configuration. The Admin does not manage leads or deals directly but controls the rules and settings that govern how the CRM behaves.

### 7.1 Admin Settings — Configurable Due Dates

The system automatically assigns due dates to activities based on the lead temperature or deal status. The Admin can configure these defaults separately for **Sales Leads** and **Estimation Deals** from the Settings panel.

**A) Sales Lead Due Dates (by temperature):**

These control how quickly Sales reps must follow up based on lead temperature.

| Lead Temperature | Default Due Date | Description |
|-----------------|-----------------|-------------|
| Hot | 3 days | High priority — must be acted on quickly |
| Warm | 7 days | Moderate priority — follow-up within a week |
| Cold | 14 days | Lower priority — longer follow-up window |

**How it works:**
- When a Sales rep updates a lead's temperature to Hot, Warm, or Cold, the system automatically calculates the due date for the next follow-up activity based on the Admin's configured values
- The Admin can change these defaults at any time (e.g., change Hot from 3 days to 2 days)
- Changes apply to newly created activities going forward (existing due dates are not retroactively changed)

*Note: Estimation Deal due dates (by deal status) will be added in a future phase when the Estimation module is built.*

### 7.2 Admin Settings — General Configuration

- Manage user accounts: create, deactivate, and assign roles
- Configure market regions (Middle East, Dutch, German) and assign Sales reps to markets
- Set default values for dropdowns and form fields
- View system-wide activity logs
- Export data (leads, deals) for reporting purposes

### 7.3 Admin Settings — Status Configuration

- View and manage the list of statuses for each team (Marketing, Sales)
- Enable or disable specific statuses as workflows evolve
- Configure the "No Reply" auto-forward timer for Marketing (default: 15 days) — leads with no email reply are auto-forwarded to Sales for outreach
- Configure the **LinkedIn Outreach timer** (default: 15 days) — how long Sales has to get a LinkedIn reply before escalating to Cold Calling
- Configure the **Cold Meeting recycling timer** (default: 6 months) — leads in Cold Meeting are returned to Marketing Data Gatherers for re-enrichment and re-campaign
- Configure the **Not Interested recycling timer** (default: 6 months) — "Not Interested" leads are re-verified and re-added to a new Instantly.ai campaign after this period

---

## 8. Shared / System-Wide Requirements

### 8.1 Authentication & Access
- Secure login with email and password
- Each user assigned a role: Marketing, Sales, or Admin
- Role-based dashboards — each team sees only their relevant data
- Data flows between teams but each team cannot modify another team's records inappropriately

### 8.2 Data Visibility Rules
| Data | Marketing | Sales |
|------|-----------|-------|
| All leads | ✅ Full access | ✅ Qualified/assigned only |
| Campaign engagement | ✅ Full access | ✅ View only |
| Lead interactions | ✅ Own entries | ✅ Full access |
| Meetings | ✅ View only | ✅ Full access |
| Deals (converted leads) | ❌ | ✅ Create & view |

### 8.3 Notifications & Alerts
*Note: Notifications and alerts will be implemented in a later phase. For v1, team handoffs are visible through the Sales Inbox (new leads from Marketing).*

### 8.4 Activity History
- Log all significant actions (lead created, status changed, proposal sent, etc.)
- Show activity timeline per lead
- Track who performed each action and when

### 8.5 Search & Filtering
- Global search across leads, companies, and contacts
- Filter by: status, date range, assigned user, priority, source
- Sort by any column in list views

### 8.6 Data Import
- CSV import for bulk lead upload
- Field mapping during import (match CSV columns to CRM fields)
- Duplicate detection on import (by email)
- Import validation and error reporting

---

## 9. Integration Points

**Currently Integrated:**

| External Tool | Purpose | Integration Type |
|--------------|---------|-----------------|
| Instantly.ai | Cold email campaigns | **Two-way API integration**: CRM auto-pushes selected leads to Instantly.ai for campaigns; Instantly.ai auto-syncs replies and engagement data back to CRM, updating lead statuses |

**Planned / Under Consideration:**

| External Tool | Purpose | Integration Type |
|--------------|---------|-----------------|
| Reoon | Email verification | API integration — verify email addresses before/during campaigns |
| Neverbounce | Email verification | API integration — bulk email validation and cleaning |

**Not Integrated (Manual Workflow):**

| External Tool | Purpose | How It's Used |
|--------------|---------|--------------|
| Apollo.io | Lead sourcing | Export leads as CSV, then import into CRM |
| LinkedIn Sales Navigator | Lead sourcing | Export leads as CSV, then import into CRM |
| Email (Gmail/Outlook) | Communication | Log interactions manually in CRM |
| Calendar (Google/Outlook) | Meeting scheduling | Enter meeting details manually in CRM |

*Note: v1 integrates with Instantly.ai for campaign management. Email verification tools (Reoon, Neverbounce) may be integrated to support the lead enrichment and recycling workflows. Other tools use CSV import or manual logging, with direct API integrations possible in future versions.*

---

## 10. Technical Requirements

### 10.1 Platform
- Web-based application accessible via modern browsers (Chrome, Edge, Safari, Firefox)
- Responsive design for desktop use (primary) and tablet (secondary)
- No mobile app required for initial version

### 10.2 Technology Stack
- **Frontend:** React with Tailwind CSS
- **Backend & Database:** Supabase (PostgreSQL database, authentication, real-time updates)
- **Hosting:** Deployable to Vercel or Netlify

### 10.3 Performance
- Pages should load within 2 seconds
- Support up to 20 concurrent users
- Handle up to 10,000 leads without performance issues

### 10.4 Security
- Password-protected access
- Role-based data access enforced at the database level
- Encrypted data in transit (HTTPS)
- No sensitive data exposed in URLs

### 10.5 Data
- All data stored in Supabase PostgreSQL
- Real-time updates when data changes (e.g., new lead assigned, status updated)
- Data backup handled by Supabase

---

## 11. Future Enhancements (Out of Scope for v1)

**Phase 2 — Estimation Team Module:**
- Estimation Team dashboard with Deal Inbox, Requirement Analysis, Proposal Builder
- Man-month estimation and project cost/value tracking
- Proposal revisions & version tracking with file uploads
- Q&A review and Estimation Manager approval workflow
- Proposal delivery, client negotiation tracking (Waiting for Approval status)
- Project handoff on deal win
- Estimation Deal due dates (configurable by Admin)
- Estimation metrics dashboard

**Phase 3 — Dashboards & Notifications:**
- Marketing metrics dashboard (lead sourcing stats, campaign performance, qualification rates)
- Sales metrics dashboard (pipeline by status, conversion rates, time-to-RFQ)
- In-app notifications and alerts (new leads in inbox, new deals assigned)
- Reminder system for follow-ups and meetings

**Phase 4+ — Integrations & Advanced Features:**
- Direct Apollo.io API integration (auto-import leads)
- Direct Instantly.ai API integration (auto-sync campaign engagement)
- Google Calendar / Outlook Calendar sync
- Email sending directly from CRM
- Automated lead scoring with AI
- Client portal for proposal review
- Mobile app
- Advanced reporting with export to PDF/Excel
- Workflow automation (auto-assign leads, auto-reminders)

---

## 12. Success Criteria (v1)

The CRM v1 will be considered successful when:

1. Data Gatherers can import leads via CSV (with duplicate detection & update logic) and enrich/verify lead data
2. Campaign Manager can see qualified leads, select targets, and auto-push them to Instantly.ai via API
3. Instantly.ai replies auto-sync back to the CRM and update lead statuses
4. AI automatically reads replies, extracts useful data (phone, emails, contacts), and classifies as SQL, MQL, or Spam
5. SQL leads are auto-forwarded to Sales with extracted data; MQL leads are parked for 6-month recycling; Spam is archived
6. Leads with no email reply after 15 days are auto-forwarded to Sales for outreach
7. Sales can follow the escalation flow: LinkedIn Outreach → Cold Calling → Cold Meeting, with status tracking at each step
8. Engaged leads can be nurtured (Hot/Warm), meetings scheduled, and converted to Deals when RFQs are received
9. Leads in Cold Meeting for 6 months are recycled back to Data Gatherers for re-enrichment
10. MQL leads are re-verified and re-added to campaigns after 6 months
11. Admin can configure timers (no-reply, LinkedIn outreach, Cold Meeting recycling, MQL recycling), manage users/markets, and adjust statuses
12. All users can log in with their role and see only what's relevant to them
