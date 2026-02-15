# Kadû Admin Dashboard — UI/UX Design Specification

> **Version:** 1.0  
> **Date:** February 15, 2026  
> **For:** UI/UX Designer  
> **Platform:** Desktop-first, Mobile-friendly

---

## Executive Summary

This document outlines all features needed for the Kadû Admin Dashboard. The admin dashboard supports two types of admin users who manage the platform, approve users, handle disputes, and configure platform settings.

---

## Admin Roles

Kadû has **two admin role levels**:

1. **SUPPORT** — Can manage waitlist, view users, handle support cases, communicate with users
2. **SUPER_ADMIN** — Full access including pricing config, promos, platform policies, metrics, and all support features

---

## Design System Guidelines

### Platform Preferences

- **Layout:** Desktop-first, Mobile-friendly (responsive design)
- **Dark Mode:** Not required for MVP
- **Real-time Updates:** Backend concern (not UI designer's focus)
- **Admin Notifications:** YES — bell icon for new signups, cases, disputes
- **Bulk Actions:** YES — multi-select for approve/reject multiple users

### Color Palette

- **Primary Brand:** Kadû Blue (`#132E69`)
- **Success/Approved:** Green (`#10B981` recommended)
- **Warning/Pending:** Yellow/Orange (`#F59E0B` recommended)
- **Danger/Rejected:** Red (`#EF4444` recommended)
- **Info:** Light Blue (`#3B82F6` recommended)
- **Neutral/Gray:** For backgrounds and text

### Badge System

#### Role Badges

- **Customer:** Blue badge
- **Contractor:** Purple/Teal badge
- **Partner:** Orange badge
- **Support:** Gray badge
- **Super Admin:** Dark blue/black badge

#### Status Badges

- **Pending:** Yellow/orange with icon
- **Approved:** Green with checkmark
- **Rejected:** Red with X icon
- **Suspended:** Dark red with lock icon
- **Active:** Green with pulse indicator

#### Trust Tier Badges (for contractors/customers)

- **NEW_USER:** Gray badge
- **ESTABLISHED:** Blue badge
- **TRUSTED:** Gold/green badge with star icon

---

## Admin Authentication (Implemented)

**Different from user auth!** Admins use **2-factor authentication (OTP + Password)**

### Login Flow

**Step 1:** Admin enters phone number  
→ Endpoint: `POST /auth/otp/request`

**Step 2:** Admin enters OTP code (6 digits)  
→ Endpoint: `POST /auth/otp/verify`  
→ Receives `adminVerifyToken` (expires in 5 minutes)

**Step 3:** Admin enters password  
→ Endpoint: `POST /auth/admin/verify`  
→ Receives session tokens and is logged in

### Password Requirements

- Minimum 12 characters
- 1 uppercase letter
- 1 lowercase letter
- 1 number
- 1 special character
- Expires after 90 days
- Cannot reuse last 5 passwords

### UI Screens Needed

1. **Admin Login Page (3-step flow)**
   - Phone number input with Liberia flag (+231 prefix)
   - OTP input (6-digit code entry)
   - Password input with show/hide toggle
   - "Forgot password?" link
   - Error handling for each step

2. **Password Change Page**
   - Current password field
   - New password field with strength indicator
   - Confirm new password field
   - Password requirements checklist (visible)
   - Submit button

3. **Password Expiring Soon Notification**
   - Banner/toast notification: "Your password expires in X days"
   - Link to change password page

---

## Phase 1: Must-Have Features (Launch MVP)

---

## 1. Waitlist Management ⭐ PRIORITY 1

Since users can't access the platform without approval, this is the **first feature** admins will use daily.

### A. Waitlist List View

**Endpoint:** `GET /admin/waitlist`

#### Purpose

Shows all people who signed up and are waiting for approval. Admins can filter, search, and take bulk actions.

#### Filters & Controls

**Top Bar:**

- **Role Filter (Dropdown):** All Roles | Customer | Contractor | Partner
- **Status Filter (Dropdown):** Pending | Approved | Rejected | Suspended
- **Search Bar:** Placeholder "Search by name, phone, or business..."
- **Bulk Actions (when items selected):**
  - "Approve Selected (X)" button (green)
  - "Reject Selected (X)" button (red)
  - "Deselect All" link
- **Refresh Button:** Manual refresh icon
- **Export Button:** Download CSV of current view

**Pagination Controls:**

- Items per page selector: 10 | 20 | 50 | 100
- Page navigation: ← 1 2 3 ... 10 →
- "Showing 21-40 of 237 entries"

#### Table/Card Layout

**Option 1: Table View (Recommended for Desktop)**

| **Select** | **Photo** | **Name**    | **Phone**     | **Email**         | **Role**      | **Status** | **Submitted** | **Actions**                      |
| ---------- | --------- | ----------- | ------------- | ----------------- | ------------- | ---------- | ------------- | -------------------------------- |
| ☐          | [Avatar]  | John Doe    | +231770123456 | john@example.com  | 🟦 Customer   | 🟡 Pending | 2 days ago    | 👁️ View · ✅ Approve · ❌ Reject |
| ☐          | [Avatar]  | Sarah K.    | +231880234567 | sarah@example.com | 🟣 Contractor | 🟡 Pending | 5 hours ago   | 👁️ View · ✅ Approve · ❌ Reject |
| ☐          | [Avatar]  | Mike's Shop | +231990345678 | mike@shop.com     | 🟠 Partner    | 🟡 Pending | 1 day ago     | 👁️ View · ✅ Approve · ❌ Reject |

**Checkbox in Header:** Select/deselect all on current page

**Option 2: Card View (Recommended for Mobile)**

```
┌─────────────────────────────────────────┐
│ ☐  [Avatar]  John Doe                   │
│              +231770123456               │
│              john@example.com            │
│                                          │
│   🟦 Customer  🟡 Pending  📅 2 days ago │
│                                          │
│   [View Details] [Approve] [Reject]     │
└─────────────────────────────────────────┘
```

#### Sort Options

- Default: Oldest pending first (FIFO — first in, first out)
- Other options: Newest first, Name (A-Z), Role

#### Empty States

- **No pending users:** "All caught up! No pending approvals." with illustration
- **No search results:** "No users found matching '[search term]'"

---

### B. Waitlist Statistics Dashboard

**Endpoint:** `GET /admin/waitlist/stats`

#### Purpose

Quick overview of waitlist status counts. Should be visible on main dashboard.

#### Layout

**Top Row: Pending Approvals (Highlighted)**

```
┌──────────────────────┬──────────────────────┬──────────────────────┐
│  👥 Customers        │  🔧 Contractors      │  🏪 Partners         │
│  42 Pending          │  18 Pending          │  7 Pending           │
│  🟡 Awaiting Action  │  🟡 Awaiting Action  │  🟡 Awaiting Action  │
└──────────────────────┴──────────────────────┴──────────────────────┘
```

**Statistics Cards:**

```
┌─────────────────────────────────────────────────────────────┐
│  📊 WAITLIST OVERVIEW                                       │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Total Pending: 67                                          │
│  ├─ Customers: 42                                           │
│  ├─ Contractors: 18                                         │
│  └─ Partners: 7                                             │
│                                                             │
│  ───────────────────────────────────────────────────────    │
│                                                             │
│  Total Approved: 1,245                                      │
│  ├─ Customers: 876                                          │
│  ├─ Contractors: 312                                        │
│  └─ Partners: 57                                            │
│                                                             │
│  ───────────────────────────────────────────────────────    │
│                                                             │
│  Total Rejected: 89                                         │
│  ├─ Customers: 45                                           │
│  ├─ Contractors: 38                                         │
│  └─ Partners: 6                                             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Visualization Ideas:**

- Pie chart: Pending by role
- Bar chart: Approved vs Rejected by role
- Timeline: Signups over time (last 7/30 days)

---

### C. Single User Detail View

**Endpoint:** `GET /admin/waitlist/:id`

#### Purpose

Full profile of a single waitlist applicant with all details and approval actions.

#### Layout Structure

**Header Section:**

```
┌─────────────────────────────────────────────────────────────┐
│  [← Back to Waitlist]                                       │
│                                                             │
│  [Profile Photo]  John Doe                    🟡 PENDING   │
│  (80x80)          +231770123456                             │
│                   john@example.com ✅ Verified              │
│                                                             │
│                   🟦 CUSTOMER                               │
│                                                             │
│  [✅ Approve User]  [❌ Reject User]  [📞 Contact]          │
└─────────────────────────────────────────────────────────────┘
```

#### Identity Information Tab

```
┌─────────────────────────────────────────┐
│  📋 IDENTITY INFORMATION                │
├─────────────────────────────────────────┤
│  Full Name:         John Doe            │
│  Phone Number:      +231770123456       │
│  Email:             john@example.com    │
│  Email Verified:    ✅ Yes              │
│  Profile Photo:     ✅ Uploaded         │
│  Trust Tier:        🔘 NEW_USER         │
└─────────────────────────────────────────┘
```

#### Role Application Details Tab

**For CUSTOMERS:**

```
┌─────────────────────────────────────────┐
│  🛠️ CUSTOMER APPLICATION                │
├─────────────────────────────────────────┤
│  Pain Points Selected:                  │
│  • Hard to find reliable contractors    │
│  • Price uncertainty                    │
│                                         │
│  Interested Categories:                 │
│  • Home Cleaning                        │
│  • Plumbing                             │
│  • Electrical Work                      │
│                                         │
│  Onboarding Progress: 100%              │
└─────────────────────────────────────────┘
```

**For CONTRACTORS:**

```
┌─────────────────────────────────────────┐
│  🔧 CONTRACTOR APPLICATION              │
├─────────────────────────────────────────┤
│  Pain Points Selected:                  │
│  • Finding steady work                  │
│  • Payment delays                       │
│                                         │
│  Skills/Services:                       │
│  • Plumbing                             │
│  • AC Installation & Repair             │
│  • General Handyman                     │
│                                         │
│  Service Location:                      │
│  📍 Monrovia, Liberia                   │
│  [View on map]                          │
│                                         │
│  Onboarding Progress: 100%              │
└─────────────────────────────────────────┘
```

**For PARTNERS:**

```
┌─────────────────────────────────────────┐
│  🏪 PARTNER APPLICATION                 │
├─────────────────────────────────────────┤
│  Business Name: Mike's Hardware         │
│  Business Type: Hardware Store          │
│                                         │
│  Location:                              │
│  📍 123 Main St, Monrovia, Liberia      │
│  [View on map]                          │
│                                         │
│  Hours of Operation:                    │
│  Mon-Fri: 8:00 AM - 6:00 PM            │
│  Sat: 9:00 AM - 4:00 PM                │
│  Sun: Closed                            │
│                                         │
│  Product Categories:                    │
│  • Plumbing supplies                    │
│  • Electrical supplies                  │
│  • Paint & hardware                     │
│                                         │
│  Onboarding Progress: 100%              │
└─────────────────────────────────────────┘
```

#### Status Timeline Tab

```
┌─────────────────────────────────────────┐
│  ⏱️ STATUS TIMELINE                     │
├─────────────────────────────────────────┤
│  🟢 Signed Up                           │
│     Feb 13, 2026 at 2:34 PM            │
│                                         │
│  🟡 Pending Approval                    │
│     Current status                      │
│                                         │
│  [If approved:]                         │
│  🟢 Approved                            │
│     Feb 15, 2026 at 10:15 AM           │
│     By: Admin Sarah Johnson            │
│                                         │
│  [If rejected:]                         │
│  🔴 Rejected                            │
│     Feb 15, 2026 at 10:15 AM           │
│     By: Admin Sarah Johnson            │
│     Reason: Incomplete business info    │
└─────────────────────────────────────────┘
```

#### Action Buttons Behavior

**Approve Button (Green, Prominent):**

- Click → Confirmation modal appears
- Modal: "Are you sure you want to approve John Doe as Customer?"
- Buttons: [Cancel] [Yes, Approve]
- On success: Success toast + page updates → "✅ User approved successfully"
- If already approved: Error toast → "⚠️ This user is already approved"

**Reject Button (Red, Secondary):**

- Click → Modal with form appears
- Modal title: "Reject Application"
- Required field: "Reason for rejection" (textarea, min 10 characters)
- Placeholder: "Please provide a clear reason for rejection..."
- Character counter: 0/500
- Buttons: [Cancel] [Confirm Rejection]
- On success: Success toast + page updates → "✅ User rejected"
- If already rejected: Error toast → "⚠️ This user is already rejected"

**Contact Button (Blue, Secondary):**

- Opens messaging/calling modal (see Communication feature below)

---

### D. Bulk Approve/Reject

#### When Users Selected

- Checkbox selection persists across pages
- Selected count badge appears: "3 users selected"
- Action bar appears at top or bottom:

```
┌─────────────────────────────────────────────────────────────┐
│  📋 3 users selected                                        │
│                                                             │
│  [✅ Approve All (3)]  [❌ Reject All (3)]  [Clear]         │
└─────────────────────────────────────────────────────────────┘
```

#### Bulk Approve Flow

1. Click "Approve All (X)"
2. Confirmation modal:
   ```
   ┌─────────────────────────────────────┐
   │  Approve Multiple Users             │
   ├─────────────────────────────────────┤
   │  You are about to approve:          │
   │                                     │
   │  • John Doe (Customer)              │
   │  • Sarah K. (Contractor)            │
   │  • Mike's Shop (Partner)            │
   │                                     │
   │  [Cancel]  [Approve All]            │
   └─────────────────────────────────────┘
   ```
3. Processing indicator: "Approving 3 users..."
4. Success: "✅ 3 users approved successfully"
5. If any fail: "✅ 2 approved, ⚠️ 1 failed: [Name] - already approved"

#### Bulk Reject Flow

1. Click "Reject All (X)"
2. Modal with rejection reason (applies to all):
   ```
   ┌─────────────────────────────────────┐
   │  Reject Multiple Users              │
   ├─────────────────────────────────────┤
   │  You are about to reject:           │
   │                                     │
   │  • John Doe (Customer)              │
   │  • Sarah K. (Contractor)            │
   │                                     │
   │  Reason (applies to all):           │
   │  [Textarea]                         │
   │                                     │
   │  [Cancel]  [Reject All]             │
   └─────────────────────────────────────┘
   ```
3. Processing + success/error handling similar to bulk approve

---

## 2. Admin Dashboard Home (Implemented)

**Purpose:** Overview of platform health and quick access to key functions

### Layout

**Top Stats Row:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 👥 Users     │ 💼 Active    │ 🔔 Pending   │ ⚠️ Cases     │
│              │    Jobs      │    Approvals │    Open      │
│ 1,332        │ 42           │ 67           │ 8            │
│ +12 today    │ +5 today     │ ⚠️ Action    │ 🔴 2 urgent  │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**Quick Actions:**

- "View Pending Waitlist" button
- "View Open Cases" button
- "User Management" button
- "Platform Metrics" button

**Recent Activity Feed:**

- Last 10 admin actions
- Last 10 user signups
- Last 10 cases opened

**Admin Notifications (Bell Icon):**

- Badge count of unread notifications
- Dropdown list:
  - New user signups
  - Cases opened
  - Disputes filed
  - System alerts
- Mark as read functionality

---

## 3. Communication Center (NEW FEATURE) ⭐

**Purpose:** Allow admins/support to message or call users directly from the dashboard

**Endpoints (TO BE IMPLEMENTED):**

- `POST /admin/messages/send` — Send message to user
- `GET /admin/messages/:identityId` — View message history with user
- `POST /admin/calls/initiate` — Initiate call to user
- `GET /admin/communications/history` — View all admin communications

### A. Messaging Interface

**Access Points:**

- From user detail page: "📞 Contact" button
- From user list: "💬" icon in actions column
- From case detail page: "Message Customer/Contractor" button

**Message Modal:**

```
┌─────────────────────────────────────────────────────────────┐
│  💬 Message: John Doe (+231770123456)                       │
├─────────────────────────────────────────────────────────────┤
│  Message History:                                           │
│  ┌─────────────────────────────────────────────────────┐   │
│  │ [Admin Sarah] 2 days ago:                           │   │
│  │ "Hi John, we need additional information..."        │   │
│  │                                                      │   │
│  │ [John Doe] 1 day ago:                               │   │
│  │ "Sure, here's the info you requested..."           │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  New Message:                                               │
│  [Textarea - type your message here]                        │
│                                                             │
│  Quick Templates: ▾                                         │
│  • Request additional information                           │
│  • Approval confirmation                                    │
│  • Rejection explanation                                    │
│                                                             │
│  [📎 Attach File]  [Cancel]  [Send Message]                │
└─────────────────────────────────────────────────────────────┘
```

**Features:**

- Message history with timestamps
- Quick message templates
- File attachments
- SMS fallback indicator (if user offline)
- Read receipts

### B. Call Interface

**Call Modal:**

```
┌─────────────────────────────────────────┐
│  📞 Call: John Doe                      │
├─────────────────────────────────────────┤
│  Phone: +231770123456                   │
│                                         │
│  Call Options:                          │
│  ○ Direct phone call                    │
│     (Uses your device phone)            │
│                                         │
│  ○ In-app call                          │
│     (VoIP - if implemented)             │
│                                         │
│  Note for call log (optional):          │
│  [Textarea]                             │
│                                         │
│  [Cancel]  [Initiate Call]              │
└─────────────────────────────────────────┘
```

**Call Log Entry (Auto-created):**

- Who called whom
- Date/time
- Duration (if tracked)
- Admin notes
- Call outcome (reached/voicemail/no answer)

### C. Communication History

**View all admin communications with a specific user:**

```
┌─────────────────────────────────────────────────────────────┐
│  Communication History: John Doe                            │
├─────────────────────────────────────────────────────────────┤
│  📞 Call - Feb 15, 2026 10:30 AM                           │
│     By: Admin Sarah                                         │
│     Duration: 5 min 32 sec                                  │
│     Note: Discussed missing documents                       │
│                                                             │
│  💬 Message - Feb 14, 2026 3:45 PM                         │
│     By: Admin John                                          │
│     "Please upload your business license..."                │
│     Status: ✅ Read                                         │
│                                                             │
│  📞 Call - Feb 13, 2026 2:15 PM                            │
│     By: Admin Sarah                                         │
│     Status: No answer                                       │
└─────────────────────────────────────────────────────────────┘
```

---

## Phase 2: Post-Launch Essentials

---

## 4. Document & Identity Verification (TO BE IMPLEMENTED)

**Purpose:** Verify user-uploaded documents (IDs, business licenses, certifications) to grant "Verified" badges

**Endpoints (TO BE IMPLEMENTED):**

- `GET /admin/documents` — List all pending document verifications
- `GET /admin/documents/:id` — View single document
- `POST /admin/documents/:id/approve` — Approve document
- `POST /admin/documents/:id/reject` — Reject document with reason
- `POST /admin/documents/:id/request-reupload` — Request new upload

### Document Types by Role

**All Roles:**

- Government ID (passport, national ID, driver's license)

**Contractors:**

- Trade certifications (plumber license, electrician certification, etc.)
- Professional credentials
- Insurance certificates (optional)

**Partners:**

- Business license/registration
- Tax ID documents
- Proof of address (business location)

**Customers:**

- Generally optional unless high-value transactions
- Corporate customers may need business docs

### A. Document Verification Queue

**Purpose:** Central place to review all uploaded documents awaiting verification

**Top Stats:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 📄 Pending   │ ✅ Approved   │ ❌ Rejected   │ 🔄 Reupload  │
│    Docs      │    Today     │    Today     │    Requested │
│ 34           │ 12           │ 3            │ 5            │
│ -2 vs avg    │ +4 ↑         │ -1 ↓         │ +2 ↑         │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**Filters:**

- Document Type: All | Government ID | Business License | Trade Certification | Other
- User Role: All | Customer | Contractor | Partner
- Status: Pending | Approved | Rejected | Reupload Requested
- Urgency: All | High Value Users | First-Time Upload | Resubmission
- Submitted: Today | Last 7 days | Last 30 days | Custom range

**Table View:**

| **Select** | **User**    | **Role**      | **Document Type** | **Submitted** | **Preview** | **Status** | **Actions**                                  |
| ---------- | ----------- | ------------- | ----------------- | ------------- | ----------- | ---------- | -------------------------------------------- |
| ☐          | John Doe    | 🟦 Customer   | Government ID     | 2 hours ago   | [👁️ View]   | 🟡 Pending | ✅ Approve · ❌ Reject · 🔄 Request Reupload |
| ☐          | Sarah K.    | 🟣 Contractor | Plumber License   | 5 hours ago   | [👁️ View]   | 🟡 Pending | ✅ Approve · ❌ Reject · 🔄 Request Reupload |
| ☐          | Mike's Shop | 🟠 Partner    | Business License  | 1 day ago     | [👁️ View]   | 🟡 Pending | ✅ Approve · ❌ Reject · 🔄 Request Reupload |

**Bulk Actions:**

- Select multiple documents
- Bulk approve (with confirmation)
- Bulk reject (with reason)

---

### B. Document Detail View

**Purpose:** Review single document with full details and verification actions

**Layout:**

```
┌─────────────────────────────────────────────────────────────┐
│  [← Back to Queue]                                          │
│                                                             │
│  📄 GOVERNMENT ID VERIFICATION                              │
│  Submitted by: John Doe (+231770123456)                     │
│  Role: 🟦 Customer                                          │
│  Submitted: Feb 15, 2026 at 2:34 PM                         │
│  Status: 🟡 PENDING REVIEW                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  USER INFORMATION                                           │
├─────────────────────────────────────────────────────────────┤
│  [Profile Photo]  John Doe                                  │
│                   +231770123456                             │
│                   john@example.com ✅ Verified              │
│                   Member since: Feb 10, 2026                │
│                   Trust Tier: 🔘 NEW_USER                   │
│                                                             │
│  [View Full Profile] [Message User]                         │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  DOCUMENT VIEWER                                            │
├─────────────────────────────────────────────────────────────┤
│  Document Type: Government ID (Passport)                    │
│  File Name: passport_john_doe.jpg                           │
│  File Size: 2.4 MB                                          │
│  Uploaded: Feb 15, 2026 at 2:34 PM                          │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                     │   │
│  │         [DOCUMENT IMAGE DISPLAYED HERE]            │   │
│  │                                                     │   │
│  │         With zoom controls: [−] [+] [⛶ Fullscreen] │   │
│  │                                                     │   │
│  │         [🔍 Zoom In] [🔍 Zoom Out] [↻ Rotate]      │   │
│  │                                                     │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  [📥 Download Original]                                     │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  DOCUMENT DETAILS (USER PROVIDED)                           │
├─────────────────────────────────────────────────────────────┤
│  Document Number: P1234567                                  │
│  Full Name: John Doe                                        │
│  Date of Birth: Jan 15, 1990                                │
│  Issue Date: Feb 1, 2025                                    │
│  Expiry Date: Feb 1, 2035                                   │
│  Issuing Country: Liberia                                   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  VERIFICATION CHECKLIST                                     │
├─────────────────────────────────────────────────────────────┤
│  ☐ Document is clear and legible                           │
│  ☐ All corners/edges visible                               │
│  ☐ Name matches profile                                    │
│  ☐ Document is not expired                                 │
│  ☐ No signs of tampering/editing                           │
│  ☐ Photo matches profile photo (if applicable)             │
│                                                             │
│  [Auto-populate notes from checklist]                       │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  ADMIN NOTES (INTERNAL)                                     │
├─────────────────────────────────────────────────────────────┤
│  [Textarea for admin notes]                                 │
│  e.g., "Document clear, all details match"                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  VERIFICATION HISTORY                                       │
├─────────────────────────────────────────────────────────────┤
│  📅 Feb 15, 2026 2:34 PM                                    │
│     Document uploaded by user                               │
│                                                             │
│  [If resubmission:]                                         │
│  📅 Feb 14, 2026 10:00 AM                                   │
│     Rejected by Admin Sarah                                 │
│     Reason: "Image too blurry, please upload clearer photo" │
│                                                             │
│  📅 Feb 13, 2026 3:00 PM                                    │
│     Initial upload by user                                  │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│  ACTIONS                                                    │
├─────────────────────────────────────────────────────────────┤
│  [✅ Approve Document]  [❌ Reject Document]                │
│  [🔄 Request Reupload]                                      │
└─────────────────────────────────────────────────────────────┘
```

---

### C. Verification Actions

**1. Approve Document**

**Flow:**

- Click "Approve Document"
- Modal appears:

```
┌─────────────────────────────────────────┐
│  Approve Document                       │
├─────────────────────────────────────────┤
│  You are approving:                     │
│  • Government ID                        │
│  • For: John Doe (Customer)             │
│                                         │
│  ☑ Grant "Verified" badge               │
│  ☐ Notify user via email                │
│  ☐ Notify user via SMS                  │
│                                         │
│  [Cancel]  [Approve]                    │
└─────────────────────────────────────────┘
```

- On approval:
  - Document status → APPROVED
  - User gets "Verified" badge (if applicable)
  - Notification sent
  - Admin action logged

**2. Reject Document**

**Flow:**

- Click "Reject Document"
- Modal with reason (required):

```
┌─────────────────────────────────────────┐
│  Reject Document                        │
├─────────────────────────────────────────┤
│  Reason for rejection:                  │
│  [Dropdown with common reasons]         │
│  • Image too blurry                     │
│  • Document expired                     │
│  • Name doesn't match profile           │
│  • Incomplete/partial document          │
│  • Suspected forgery                    │
│  • Other (specify below)                │
│                                         │
│  Additional details (required):         │
│  [Textarea, min 20 chars]               │
│                                         │
│  ☑ Allow user to reupload               │
│  ☑ Notify user via email                │
│                                         │
│  [Cancel]  [Reject]                     │
└─────────────────────────────────────────┘
```

- On rejection:
  - Document status → REJECTED
  - User notified with reason
  - Document marked for reupload (if allowed)
  - Admin action logged

**3. Request Reupload**

**Flow:**

- Click "Request Reupload"
- Modal:

```
┌─────────────────────────────────────────┐
│  Request Reupload                       │
├─────────────────────────────────────────┤
│  What's wrong with this document?       │
│  [Dropdown with common issues]          │
│  • Image too dark/blurry                │
│  • All corners not visible              │
│  • Glare/reflection obscures text       │
│  • Wrong document type uploaded         │
│  • Other (specify below)                │
│                                         │
│  Instructions for user:                 │
│  [Textarea]                             │
│  e.g., "Please upload a clearer photo   │
│  with all 4 corners visible"            │
│                                         │
│  ☑ Notify user via email                │
│  ☑ Notify user via SMS                  │
│                                         │
│  [Cancel]  [Request Reupload]           │
└─────────────────────────────────────────┘
```

- On request:
  - Document status → REUPLOAD_REQUESTED
  - User notified with instructions
  - Original document retained for reference
  - Admin action logged

---

### D. Verified Badge System

**When documents are approved, users get badges:**

**Customer Verified Badge:**

- ✅ Government ID verified
- Shows on profile and in job postings

**Contractor "Verified Pro" Badge:**

- ✅ Government ID verified
- ✅ Trade certification verified (plumber license, electrician cert, etc.)
- Shows prominently on contractor profile
- Increases trust and visibility in job offers

**Partner "Verified Business" Badge:**

- ✅ Business license verified
- ✅ Government ID (owner) verified
- Shows on shop listings

**Badge Display:**

```
┌─────────────────────────────────────────┐
│  [Photo] Sarah K. ✅ VERIFIED PRO       │
│          Plumber                        │
│          Licensed & Certified           │
│          ⭐ 4.8 (45 reviews)             │
└─────────────────────────────────────────┘
```

---

### E. Document Management Tab (in User Profile)

When viewing a user's profile, admins should see a "Documents" tab:

```
┌─────────────────────────────────────────────────────────────┐
│  📄 DOCUMENTS                                               │
├─────────────────────────────────────────────────────────────┤
│  Government ID:                                             │
│  [Preview] Passport - P1234567                              │
│  Status: ✅ Approved (Feb 15, 2026 by Admin Sarah)          │
│  [View] [Request New Upload]                                │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  Trade License (Plumber):                                   │
│  [Preview] License #PL-98765                                │
│  Status: ✅ Approved (Feb 14, 2026 by Admin John)           │
│  Expires: Dec 31, 2026 ⚠️ Expiring soon                     │
│  [View] [Request Renewal]                                   │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  📎 Upload Additional Document (Admin)                      │
│  [+ Add Note About Required Document]                       │
└─────────────────────────────────────────────────────────────┘
```

---

## 5. User Management (TO BE IMPLEMENTED)

**Purpose:** Manage all approved users, view profiles, apply security actions

**Endpoints:**

- `GET /admin/users` — List all users
- `GET /admin/users/:id` — View user details
- `POST /admin/users/:id/restrict` — Ban/suspend user
- `POST /admin/users/:id/unrestrict` — Unban user
- `POST /admin/users/:id/security/invalidate-sessions` — Force logout
- `POST /admin/users/:id/security/force-reverify` — Require phone re-verification
- `POST /admin/users/:id/security/unlock-login` — Unlock if locked

### A. User List View

**Filters:**

- Role: All | Customer | Contractor | Partner
- Status: All | Active | Suspended | Banned
- Trust Tier: All | New User | Established | Trusted
- Registration Date: Last 7 days | Last 30 days | Custom range

**Search:** Name, phone, email

**Table Columns:**

- Profile photo
- Name
- Phone
- Email
- Role badges (can have multiple)
- Trust tier badge
- Status
- Jobs completed (for contractors)
- Member since
- Actions: View | Restrict | Message

### B. User Detail Page

**Tabs:**

1. **Profile Information**
   - All identity details
   - Trust tier with progress bar
   - Email/phone verification status
   - Profile readiness checklist

2. **Activity History**
   - Jobs posted/completed
   - Withdrawals
   - Cases/disputes
   - Reviews received
   - Last login

3. **Financial Summary**
   - Total earned/spent
   - Wallet balance (if contractor/partner)
   - Pending payouts
   - Transaction history link

4. **Security Events**
   - Phone changes
   - Session invalidations
   - Login attempts
   - Risk flags

5. **Admin Actions**
   - History of admin actions on this user
   - Notes from support team

**Action Buttons:**

- Ban/Suspend user
- Force logout all sessions
- Require phone re-verification
- Unlock login
- Message user
- Call user

---

## 5. Jobs Management (TO BE IMPLEMENTED)

**Purpose:** Monitor all jobs, view details, intervene if needed

**Endpoints:**

- `GET /admin/jobs` — List all jobs
- `GET /admin/jobs/:id` — View job details (including full financial breakdown)

### A. Jobs List View

**Filters:**

- Status: All | Posted | Active | Completed | Disputed | Canceled
- Category: All | Home Cleaning | Plumbing | etc.
- Date Range: Today | This Week | This Month | Custom
- Value Range: $0-$50 | $50-$200 | $200+ | Custom

**Search:** Job title, customer name, contractor name

**Table Columns:**

- Job ID
- Title
- Customer name
- Contractor name (if assigned)
- Category
- Status badge
- Job value
- Created date
- Actions: View Details

**Stats Cards:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 🆕 Posted    │ ⚙️ Active     │ ✅ Completed  │ ⚠️ Disputed  │
│ 23 jobs      │ 42 jobs      │ 1,456 jobs   │ 3 jobs       │
│ +5 today     │ +8 today     │ +67 today    │ 🔴 Urgent    │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

### B. Job Detail View (ENHANCED WITH FINANCIAL BREAKDOWN) ⭐

**Purpose:** See EVERYTHING about a job including who was paid what, all fees, payment methods, etc.

#### Header

```
┌─────────────────────────────────────────────────────────────┐
│  Job #JOB-2024-001234                   🟢 COMPLETED         │
│  Home Cleaning - Deep Clean                                 │
│  Created: Feb 10, 2026                                      │
└─────────────────────────────────────────────────────────────┘
```

#### Tabs

**1. Job Details Tab**

- Title & description
- Category & subcategory
- Location (map + address)
- Schedule (date/time)
- Meeting point text
- Contact person info
- Attachments

**2. People Involved Tab**

```
┌─────────────────────────────────────────┐
│  👤 CUSTOMER                            │
│  [Photo] John Doe                       │
│  +231770123456                          │
│  john@example.com                       │
│  Trust Tier: 🟢 ESTABLISHED             │
│  [View Profile] [Message]               │
├─────────────────────────────────────────┤
│  🔧 CONTRACTOR                          │
│  [Photo] Sarah K.                       │
│  +231880234567                          │
│  sarah@example.com                      │
│  Trust Tier: 🟢 TRUSTED                 │
│  [View Profile] [Message]               │
└─────────────────────────────────────────┘
```

**3. Financial Breakdown Tab** ⭐ **NEW - DETAILED**

This is critical for transparency and support case resolution.

```
┌─────────────────────────────────────────────────────────────┐
│  💰 FINANCIAL BREAKDOWN                                     │
├─────────────────────────────────────────────────────────────┤
│  JOB VALUE & FEES:                                          │
│  ─────────────────────────────────────────────────────      │
│  Labor Amount:                           $100.00            │
│  Materials:                              $45.50             │
│  ─────────────                                              │
│  Subtotal:                               $145.50            │
│                                                             │
│  CUSTOMER FEES:                                             │
│  ─────────────────────────────────────────────────────      │
│  Service Fee (Customer):                 $9.25              │
│    ↳ Rate: 9.25% (925 bp)                                  │
│    ↳ Base: $100.00 × 925 bp                                │
│  Processing Fee:                         $4.22              │
│    ↳ Stripe fee: 2.9% + $0.30                              │
│  ─────────────                                              │
│  Total Charged to Customer:              $159.97            │
│                                                             │
│  PAYMENT METHOD:                                            │
│  ─────────────────────────────────────────────────────      │
│  💳 Visa •••• 4242                                          │
│  Charged: Feb 10, 2026 at 3:45 PM                          │
│  Transaction ID: ch_1ABC2DEF3GHI4JKL                        │
│  Status: ✅ Succeeded                                       │
│                                                             │
│  CONTRACTOR PAYOUT:                                         │
│  ─────────────────────────────────────────────────────      │
│  Labor Amount:                           $100.00            │
│  Service Fee (Contractor):               -$5.00             │
│    ↳ Rate: 5% (500 bp)                                     │
│  ─────────────                                              │
│  Net Payout (Labor):                     $95.00             │
│                                                             │
│  Materials Payout:                       $38.38             │
│    ↳ Materials cost to partner: $38.38                     │
│    ↳ Materials margin (platform): $7.12                    │
│  ─────────────                                              │
│  Total Contractor Payout:                $133.38            │
│                                                             │
│  PAYOUT DETAILS:                                            │
│  ─────────────────────────────────────────────────────      │
│  Method: 📱 MTN Mobile Money                                │
│  Phone: +231880234567                                       │
│  Paid: Feb 12, 2026 at 10:00 AM                            │
│  Payout ID: po_1XYZ2ABC3DEF4GHI                            │
│  Status: ✅ Paid                                            │
│                                                             │
│  PARTNER PAYOUT (Materials):                                │
│  ─────────────────────────────────────────────────────      │
│  Partner: Mike's Hardware                                   │
│  Materials Cost:                         $38.38             │
│  Processing Fee (Partner):               -$0.38             │
│    ↳ MTN MoMo fee: 1% (partner pays)                       │
│  ─────────────                                              │
│  Net Partner Payout:                     $38.00             │
│  Status: ✅ Paid - Feb 13, 2026                             │
│                                                             │
│  PLATFORM REVENUE BREAKDOWN:                                │
│  ─────────────────────────────────────────────────────      │
│  Customer Service Fee:                   $9.25              │
│  Contractor Service Fee:                 $5.00              │
│  Materials Margin:                       $7.12              │
│  ─────────────                                              │
│  Gross Platform Revenue:                 $21.37             │
│                                                             │
│  PLATFORM COSTS:                                            │
│  Processing Fee (paid to Stripe):        -$4.22            │
│  Net Platform Revenue:                   $17.15             │
│                                                             │
│  PRICING CONFIG USED:                                       │
│  ─────────────────────────────────────────────────────      │
│  Version: v2.3                                              │
│  Activated: Feb 1, 2026                                     │
│  [View Full Config]                                         │
│                                                             │
│  PROMO CODE APPLIED:                                        │
│  ─────────────────────────────────────────────────────      │
│  Code: FIRST10                                              │
│  Type: 10% off customer service fee                         │
│  Discount Applied: -$1.03                                   │
│  [View Promo Details]                                       │
└─────────────────────────────────────────────────────────────┘
```

**Export Options:**

- PDF invoice (customer view)
- PDF invoice (contractor view)
- Full financial breakdown (CSV)
- Audit report (PDF with all transaction IDs)

**4. Timeline Tab**

- Job posted → Offers received → Contractor assigned → Start code verified → Work submitted → Approved → Paid out
- Each step with timestamp and actor
- Admin actions logged

**5. Work & Evidence Tab**

- Work submissions (photos, videos, notes)
- Customer approval/revision requests
- Start code verification status

**6. Messages Tab**

- Job thread messages between customer and contractor
- System messages

**7. Cases/Disputes Tab**

- Any cases opened for this job
- Link to case detail

---

## 6. Money & Finance Metrics (ENHANCED) ⭐

**Purpose:** Financial dashboard showing ALL money flows and fee breakdowns

**Endpoints:**

- `GET /admin/metrics/finance` — Overall financial metrics
- `GET /admin/metrics/fees` — Fee breakdown by type
- `GET /admin/metrics/revenue` — Revenue breakdown

### Financial Dashboard Layout

**Top Summary Cards:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 💰 GMV       │ 📊 Total Rev │ 💳 Customer  │ 🔧 Contractor│
│              │              │    Fees      │    Fees      │
│ $45,890      │ $4,234       │ $2,543       │ $1,215       │
│ +$2,340 ↑    │ +$234 ↑      │ +$145 ↑      │ +$67 ↑       │
└──────────────┴──────────────┴──────────────┴──────────────┘

┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 🏪 Materials │ 💸 Processing│ 🏦 Withdrawals│ 💲 Net Rev   │
│    Margin    │    Fees Paid │    Processed │              │
│ $476         │ -$1,234      │ $38,450      │ $2,890       │
│ +$45 ↑       │ -$89 ↑       │ +$1,890 ↑    │ +$145 ↑      │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**Definitions Display (Tooltip/Help):**

- **GMV (Gross Merchandise Volume):** Total value of all transactions (job value + materials)
- **Total Revenue:** All fees collected by platform (before costs)
- **Customer Fees:** Service fees paid by customers
- **Contractor Fees:** Service fees deducted from contractor payouts
- **Materials Margin:** Markup on materials sold through partners
- **Processing Fees Paid:** Fees paid to Stripe/MoMo (platform cost)
- **Net Revenue:** Total revenue minus processing fees

### Fee Breakdown Section

```
┌─────────────────────────────────────────────────────────────┐
│  📊 FEE BREAKDOWN (Last 30 Days)                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  Customer Service Fees:          $2,543.20                  │
│    ├─ Tier 1 ($0-$100):          $1,234.50  (48.6%)       │
│    ├─ Tier 2 ($100-$500):        $890.70   (35.0%)        │
│    └─ Tier 3 ($500+):            $418.00   (16.4%)        │
│                                                             │
│  Contractor Service Fees:        $1,215.80                  │
│    ├─ Tier 1 ($0-$100):          $567.30   (46.7%)        │
│    ├─ Tier 2 ($100-$500):        $498.50   (41.0%)        │
│    └─ Tier 3 ($500+):            $150.00   (12.3%)        │
│                                                             │
│  Withdrawal Fees:                $89.50                     │
│    ├─ Contractor withdrawals:    $67.20                    │
│    └─ Partner withdrawals:       $22.30                    │
│                                                             │
│  Materials Margin:               $476.30                    │
│    ├─ Average markup %:          15.2%                     │
│    └─ Jobs with materials:       89 (34% of jobs)         │
│                                                             │
│  Processing Fees (Cost):         -$1,234.60                │
│    ├─ Stripe (cards):            -$1,120.40  (90.7%)      │
│    └─ MTN MoMo:                  -$114.20   (9.3%)        │
│                                                             │
│  ────────────────────────────────────────────              │
│  Gross Revenue:                  $4,324.80                  │
│  Processing Costs:               -$1,234.60                │
│  Net Platform Revenue:           $3,090.20                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Charts

1. **Revenue Over Time (Line Chart)**
   - X-axis: Dates
   - Y-axis: Revenue
   - Multiple lines:
     - Gross revenue (green)
     - Net revenue (blue)
     - Processing costs (red, negative)

2. **Fee Type Distribution (Pie Chart)**
   - Customer service fees
   - Contractor service fees
   - Materials margin
   - Withdrawal fees

3. **Transaction Volume (Bar Chart)**
   - Jobs completed per day/week
   - GMV per day/week
   - Color-coded by tier

### Filters

- Date Range: Today | Week | Month | Quarter | Year | Custom
- Fee Type: All | Customer | Contractor | Materials | Withdrawal
- Job Tier: All | Tier 1 | Tier 2 | Tier 3

### Export Options

- PDF report
- CSV download (detailed transactions)
- Excel with charts

---

## 7. Cases & Disputes (TO BE IMPLEMENTED)

**Purpose:** Handle support cases, disputes, no-shows, etc.

**Endpoints:**

- `GET /admin/cases` — List all cases
- `GET /admin/cases/:id` — Case details
- `POST /admin/cases/:id/request-info` — Request more info
- `POST /admin/cases/:id/resolve` — Resolve case

### Case Types

- No-show
- Dispute (work quality)
- Payment dispute
- Abuse/harassment
- Off-platform attempt
- Chargeback
- Scope blocked

### A. Cases List View

**Filters:**

- Status: Open | In Review | Resolved | Closed
- Type: All | No-show | Dispute | etc.
- Priority: All | High | Medium | Low
- Date Range

**Table Columns:**

- Case ID
- Type badge
- Job ID (linked)
- Customer name
- Contractor name
- Priority badge
- Status
- Created date
- Assigned to (support agent)
- Actions: View | Resolve

**Priority Badges:**

- 🔴 High: Chargebacks, abuse reports
- 🟡 Medium: Disputes, no-shows
- 🟢 Low: General inquiries

### B. Case Detail View

**Header:**

```
┌─────────────────────────────────────────────────────────────┐
│  Case #CASE-2024-5678              🔴 HIGH PRIORITY         │
│  Type: Work Quality Dispute        🟡 IN REVIEW             │
│  Created: Feb 14, 2026                                      │
│  Assigned to: Admin Sarah                                   │
└─────────────────────────────────────────────────────────────┘
```

**Tabs:**

#### **1. Case Details Tab**

- Case type and description
- Job reference (linked to full job details)
- Parties involved (customer + contractor)
- Created date/time
- Current status

#### **2. Evidence Timeline Tab** ⭐ **COMPREHENSIVE**

**Purpose:** Chronological view of ALL evidence, media, and events

See full detailed Evidence Timeline section below (Section 6C) for complete layout.

#### **3. Photo & Video Gallery Tab** ⭐

See full detailed Gallery section below (Section 6D) for complete layout.

#### **4. Message Logs Tab** ⭐

See full detailed Message Logs section below (Section 6E) for complete layout.

#### **5. Location Tracking Tab** ⭐ (Limited - Liberia Context)

See full detailed Location section below (Section 6F) for complete layout.

#### **6. Resolution Actions Tab**

- Approve full refund
- Approve partial refund (with amount)
- Dismiss case
- Request more info (from customer/contractor)
- Escalate to super admin

#### **7. Admin Collaboration Tab** ⭐ **NEW**

See full detailed Admin Collaboration section below (Section 6G) for complete layout.

**Action Buttons:**

- Message customer
- Message contractor
- Resolve case (opens resolution modal)
- Request more info (opens form)
- Download evidence bundle
- Export case report

---

### C. Evidence Timeline (Comprehensive View)

**Purpose:** Chronological view of ALL evidence, media, messages, and events

```
┌─────────────────────────────────────────────────────────────┐
│  📸 EVIDENCE TIMELINE                                       │
│  Showing all events, photos, videos, and messages           │
│                                                             │
│  [📥 Download All as ZIP]  [Filter: 📷 Photos | 🎥 Videos | │
│   💬 Messages | 📍 Events]                                  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 10, 2026 - 10:00 AM                                 │
│  🟢 Job Posted by Customer (John Doe)                       │
│     "Deep cleaning needed for 3-bedroom house"              │
│     📍 Location: Monrovia, Liberia                          │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 10, 2026 - 2:30 PM                                  │
│  🟢 Contractor Assigned (Sarah K.)                          │
│     Start code generated: 123456                            │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 9:00 AM                                  │
│  💬 Message from Customer                                   │
│     "Will you be on time? I have another appointment"       │
│     [View Full Conversation]                                │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 9:15 AM                                  │
│  💬 Message from Contractor                                 │
│     "Yes, I'll be there at 10am sharp"                      │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 10:15 AM                                 │
│  ✅ Start Code Verified                                     │
│     Code: 123456 ✓                                          │
│     📍 Contractor location: 6.3156°N, 10.8074°W             │
│        (Approx. 50m from meeting point)                     │
│     [View on Map]                                           │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 2:45 PM                                  │
│  📸 Work Submission by Contractor (Version 1)               │
│     Notes: "Job completed, all rooms cleaned"               │
│                                                             │
│     Photos (4):                                             │
│     [🖼️ Thumbnail] [🖼️ Thumbnail] [🖼️ Thumbnail] [🖼️ Thumbnail] │
│     [Click to view gallery]                                 │
│                                                             │
│     🎥 Video (1):                                           │
│     [▶️ Play: room_tour.mp4 - 2:30 duration]                │
│                                                             │
│     🏷️ Tagged by Admin Sarah: "Quality looks acceptable"   │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 4:00 PM                                  │
│  ❌ Revision Requested by Customer                          │
│     Reason: "Bathroom not cleaned properly"                 │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 4:05 PM                                  │
│  📸 Evidence from Customer                                  │
│     Photos (3):                                             │
│     [🖼️ Thumbnail] [🖼️ Thumbnail] [🖼️ Thumbnail]             │
│     Caption: "Bathroom still dirty, see photos"             │
│                                                             │
│     🏷️ Tagged by Admin Sarah: "Customer photos show issue" │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 6:00 PM                                  │
│  📸 Work Resubmission by Contractor (Version 2)             │
│     Notes: "Bathroom recleaned"                             │
│     Photos (2):                                             │
│     [🖼️ Thumbnail] [🖼️ Thumbnail]                             │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 7:00 PM                                  │
│  ⚠️ Dispute Opened by Customer                              │
│     Reason: "Work still not satisfactory"                   │
│     Amount disputed: $100.00                                │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 7:30 PM                                  │
│  💬 Message from Contractor (in dispute)                    │
│     "I cleaned everything as requested, not sure what..."   │
│     ⚠️ Flagged: Off-platform attempt detected               │
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                                             │
│  [Load Earlier Events] [Jump to Date]                       │
└─────────────────────────────────────────────────────────────┘
```

**Evidence Timeline Features:**

- Chronological order (newest first or oldest first toggle)
- Filter by type: Photos, Videos, Messages, Events, Location pins
- Each piece of evidence shows:
  - Who submitted it (customer/contractor/system)
  - Timestamp
  - Type indicator (photo, video, message, event)
  - Admin tags/comments attached to specific evidence
- Click any photo → Opens in fullscreen gallery view
- Click any video → Opens video player
- Click location → Opens map view

---

### D. Photo & Video Gallery

**Purpose:** Grid view of ALL media for easy browsing

```
┌─────────────────────────────────────────────────────────────┐
│  📸 PHOTO & VIDEO GALLERY                                   │
│                                                             │
│  Filter: [All Media ▾] [Customer Only] [Contractor Only]    │
│  Sort: [Latest First ▾] [Oldest First] [By Submitter]      │
│                                                             │
│  [📥 Download All as ZIP]                                   │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  CUSTOMER EVIDENCE (3 photos)                               │
│  Submitted: Feb 11, 2026 at 4:05 PM                         │
│  Caption: "Bathroom still dirty, see photos"                │
│                                                             │
│  [🖼️ Image 1]  [🖼️ Image 2]  [🖼️ Image 3]                  │
│  [Fullscreen]  [Fullscreen]  [Fullscreen]                  │
│                                                             │
│  💬 Admin Comment (Sarah):                                  │
│     "These photos clearly show the issue - bathroom not     │
│      properly cleaned. Customer complaint valid."           │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  CONTRACTOR WORK SUBMISSION #1 (4 photos, 1 video)          │
│  Submitted: Feb 11, 2026 at 2:45 PM                         │
│  Notes: "Job completed, all rooms cleaned"                  │
│                                                             │
│  [🖼️ Image 1]  [🖼️ Image 2]  [🖼️ Image 3]  [🖼️ Image 4]   │
│  [Fullscreen]  [Fullscreen]  [Fullscreen]  [Fullscreen]    │
│                                                             │
│  [▶️ Video: room_tour.mp4 - 2:30]                           │
│  [Play in fullscreen]                                       │
│                                                             │
│  💬 Admin Comment (Sarah):                                  │
│     [Add comment about this submission...]                  │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  CONTRACTOR WORK SUBMISSION #2 (2 photos)                   │
│  Submitted: Feb 11, 2026 at 6:00 PM                         │
│  Notes: "Bathroom recleaned"                                │
│                                                             │
│  [🖼️ Image 1]  [🖼️ Image 2]                                 │
│  [Fullscreen]  [Fullscreen]                                 │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Fullscreen Gallery View:**

- Click any image → Opens lightbox
- Navigation: Previous/Next arrows
- Zoom controls: Zoom in/out, fit to screen
- Download button
- Image metadata shown:
  - Submitted by: Customer/Contractor name
  - Date/time
  - File size
  - Original filename
- Add admin comment directly on image
- Side panel shows all admin comments for this media

**Video Player:**

- Full controls (play, pause, seek, volume)
- Speed controls (0.5×, 1×, 1.5×, 2×)
- Frame-by-frame stepping (for detailed inspection)
- Screenshot capture button
- Download video button

---

### E. Message Logs

**Purpose:** Complete conversation history between parties

```
┌─────────────────────────────────────────────────────────────┐
│  💬 MESSAGE LOGS                                            │
│                                                             │
│  View: [○ Job Period Only ◉ Full History]                  │
│  Show: [☑ Customer ☑ Contractor ☐ System Messages]         │
│                                                             │
│  [📄 Export as PDF] [🔍 Search in messages]                 │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 10, 2026 - 2:35 PM                                  │
│  💬 Customer (John Doe):                                    │
│     "Hi, is tomorrow morning good for you?"                 │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 10, 2026 - 3:00 PM                                  │
│  💬 Contractor (Sarah K.):                                  │
│     "Yes, I can start at 10am. I'll bring all supplies."    │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 9:00 AM                                  │
│  💬 Customer (John Doe):                                    │
│     "Will you be on time? I have another appointment"       │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 9:15 AM                                  │
│  💬 Contractor (Sarah K.):                                  │
│     "Yes, I'll be there at 10am sharp"                      │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 10:15 AM                                 │
│  🤖 System Message:                                         │
│     "Start code verified. Job now in progress."             │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 4:10 PM                                  │
│  💬 Customer (John Doe):                                    │
│     "The bathroom is still dirty, please come back"         │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 4:15 PM                                  │
│  💬 Contractor (Sarah K.):                                  │
│     "Ok, I'll come back to fix it"                          │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 11, 2026 - 7:35 PM                                  │
│  💬 Contractor (Sarah K.):                                  │
│     "I already fixed everything. Text me on WhatsApp        │
│      at +231-XXX-XXXX if there are more issues"             │
│     ⚠️ FLAGGED: Off-platform contact attempt                │
│  ────────────────────────────────────────────────────       │
│  💬 Admin Note (attached to this message):                  │
│     This message was flagged for attempting to move         │
│     communication off-platform. Contractor advised.         │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  [Load Earlier Messages]                                    │
└─────────────────────────────────────────────────────────────┘
```

**Message Log Features:**

- Toggle between "Job Period Only" (job posted → completed/disputed) vs "Full History"
- Highlight flagged messages (off-platform attempts, inappropriate content)
- Search/filter messages by keyword
- Export entire conversation as PDF (with timestamps, sender names)
- Admin comments can be attached to specific messages
- System messages color-coded differently

**PDF Export Format:**

```
═══════════════════════════════════════════════
KADÛ MESSAGE LOG EXPORT
═══════════════════════════════════════════════
Case ID: CASE-2024-5678
Job ID: JOB-2024-001234
Exported by: Admin Sarah
Export Date: Feb 12, 2026

Parties:
- Customer: John Doe (+231770123456)
- Contractor: Sarah K. (+231880234567)

═══════════════════════════════════════════════

[Feb 10, 2026 - 2:35 PM] Customer (John Doe):
Hi, is tomorrow morning good for you?

[Feb 10, 2026 - 3:00 PM] Contractor (Sarah K.):
Yes, I can start at 10am. I'll bring all supplies.

...

[End of Messages]

═══════════════════════════════════════════════
This export is for internal Kadû administrative
use only. Confidential.
═══════════════════════════════════════════════
```

---

### F. Location Tracking (Limited - Liberia Context)

**Purpose:** Show location data when available (start code verification point)

**Note:** Not continuous tracking due to Liberia's network limitations. Only captures location at key moments.

```
┌─────────────────────────────────────────────────────────────┐
│  📍 LOCATION DATA                                           │
│                                                             │
│  ⚠️ Location data may be limited due to network conditions  │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  🗺️ MAP VIEW                                                │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                                                     │   │
│  │         [Interactive Map Displayed Here]           │   │
│  │                                                     │   │
│  │  📍 Meeting Point (Customer specified)             │   │
│  │     6.3158°N, 10.8072°W                            │   │
│  │                                                     │   │
│  │  ✅ Contractor Location (Start Code Verified)      │   │
│  │     6.3156°N, 10.8074°W                            │   │
│  │     Distance from meeting point: ~50 meters        │   │
│  │     Time: Feb 11, 2026 at 10:15 AM                 │   │
│  │                                                     │   │
│  │  [Zoom In] [Zoom Out] [Center Map]                 │   │
│  └─────────────────────────────────────────────────────┘   │
│                                                             │
│  LOCATION EVENTS:                                           │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 11, 2026 - 10:15 AM                                 │
│  ✅ Start Code Verified                                     │
│     Contractor: Sarah K.                                    │
│     Location: 6.3156°N, 10.8074°W                           │
│     Accuracy: ~20 meters                                    │
│     Distance from meeting point: 50 meters                  │
│     Status: ✅ Within acceptable range                      │
│                                                             │
│  💬 Admin Comment:                                          │
│     "Location confirms contractor was at the site"          │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                                             │
│  [If no location data available:]                          │
│  ⚠️ No location data captured                               │
│     Possible reasons:                                       │
│     • Network connectivity issues                           │
│     • Location services disabled on device                  │
│     • Start code entered while offline                      │
│                                                             │
│     Note: Lack of location data does not invalidate        │
│     start code verification in low-connectivity areas.      │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Location Tab Features:**

- Interactive map showing:
  - Meeting point (pin 📍)
  - Contractor location when start code verified (pin ✅)
  - Distance calculation between points
- Accuracy indicator (GPS accuracy radius)
- Timestamp of location capture
- Admin can add notes about location verification
- Contextual warnings if location unavailable (network issues)
- NOT used as sole proof (start code is primary proof)

---

### G. Admin Collaboration

**Purpose:** Internal admin notes and collaboration on cases

```
┌─────────────────────────────────────────────────────────────┐
│  👥 ADMIN COLLABORATION                                     │
│                                                             │
│  Case assigned to: Admin Sarah                              │
│  [Reassign Case]                                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  💬 INTERNAL ADMIN COMMENTS (Not visible to users)          │
│                                                             │
│  [Add Comment]                                              │
│  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│  📅 Feb 12, 2026 - 9:00 AM                                  │
│  💬 Admin Sarah:                                            │
│     "Reviewed all evidence. Customer photos clearly show    │
│      bathroom not cleaned properly. Contractor resubmitted  │
│      but issue persists. Recommending partial refund."      │
│                                                             │
│  📌 Tagged: Customer photos, Work submission #1             │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 12, 2026 - 10:30 AM                                 │
│  💬 Admin John:                                             │
│     "Agreed. Customer is a NEW_USER with first job. Let's   │
│      approve 50% refund and warn contractor about quality   │
│      standards. No penalty this time but flagged."          │
│                                                             │
│  ────────────────────────────────────────────────────       │
│  📅 Feb 12, 2026 - 11:00 AM                                 │
│  💬 Admin Sarah:                                            │
│     "Resolved. Approved $50 refund. Sent warnings to both   │
│      parties. Updated contractor's professionalism score."  │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  📎 You can tag specific evidence, messages, or timeline    │
│     events in your comments. Other admins will see your     │
│     notes when reviewing this case.                         │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Admin Collaboration Features:**

- All admins can view all admin comments
- Tag specific evidence/messages in comments
- Reassign case to another admin
- @mention other admins for review (optional)
- Comments timestamped and attributed
- Edit history for all comments

---

### H. Evidence Download & Export

**Download All Evidence as ZIP:**

```
case-2024-5678-evidence.zip
├── manifest.json (metadata: who, what, when)
├── customer_evidence/
│   ├── photo_1_bathroom_dirty.jpg
│   ├── photo_2_bathroom_dirty.jpg
│   └── photo_3_bathroom_dirty.jpg
├── contractor_submissions/
│   ├── submission_1/
│   │   ├── photo_1_living_room.jpg
│   │   ├── photo_2_kitchen.jpg
│   │   ├── photo_3_bedroom.jpg
│   │   ├── photo_4_bathroom.jpg
│   │   └── video_room_tour.mp4
│   └── submission_2/
│       ├── photo_1_bathroom_recleaned.jpg
│       └── photo_2_bathroom_recleaned.jpg
├── message_logs.pdf (full conversation export)
├── timeline.pdf (chronological event list)
└── admin_notes.txt (internal admin comments)
```

**Comprehensive Case Report (PDF):**

- Case summary
- Parties involved
- Timeline of events
- All evidence (photos embedded)
- Message logs
- Admin decision and reasoning
- For legal/compliance purposes

---

## Phase 3: Advanced Admin Tools

---

## 8. Pricing Configuration (TO BE IMPLEMENTED)

**For SUPER_ADMIN only**

**Purpose:** Manage platform fee structures

**Endpoints:**

- `GET /admin/pricing-configs`
- `POST /admin/pricing-configs`
- `POST /admin/pricing-configs/:id/activate`
- `GET /admin/pricing-configs/:id`

### Fee Tier Structure

Fees are tiered based on job value. Example:

| Tier | Amount Range | Cust Rate (bp) | Cust Fixed (¢) | Ctr Rate (bp) | Ctr Fixed (¢) |
| ---- | ------------ | -------------- | -------------- | ------------- | ------------- |
| 1    | $0-$100      | 925 (9.25%)    | 0              | 500 (5%)      | 0             |
| 2    | $100-$500    | 750 (7.5%)     | 0              | 400 (4%)      | 0             |
| 3    | $500+        | 600 (6%)       | 0              | 300 (3%)      | 0             |

**Note:** All rates are in **basis points (bp)** where 100 bp = 1%

### A. Config List View

```
┌─────────────────────────────────────────────────────────────┐
│  Current Active Config:                    ✅ v2.3          │
│  Activated: Feb 1, 2026                                     │
│  [View Details]                                             │
├─────────────────────────────────────────────────────────────┤
│  Version History:                                           │
│                                                             │
│  v2.3 - ✅ Active - Feb 1, 2026                             │
│  v2.2 - Archived - Jan 15, 2026                            │
│  v2.1 - Archived - Jan 1, 2026                             │
│  v2.0 - Archived - Dec 1, 2025                             │
│                                                             │
│  [+ Create New Config]                                      │
└─────────────────────────────────────────────────────────────┘
```

### B. Create/Edit Config Form

**Sections:**

1. Customer Service Fee Tiers
2. Contractor Service Fee Tiers
3. Withdrawal Fees
4. Materials Markup Rules
5. Processing Fee Pass-through

**Form includes:**

- Tier amount ranges
- Rate inputs (in basis points)
- Fixed fee inputs (in cents)
- Preview calculator: "Test with job value: $[input]"
- Shows calculated fees

**Actions:**

- Save as draft
- Activate (replaces current active config)
- ⚠️ Warning: "Activating affects new quotes only. Historic jobs unchanged."

---

## 9. Review Moderation

**Purpose:** Manage inappropriate or disputed reviews

**Access:** SUPPORT and SUPER_ADMIN

**Endpoints:**

- `GET /admin/reviews` (list all reviews with filters)
- `GET /admin/reviews/:id` (view single review with full context)
- `POST /admin/reviews/:id/hide` (hide inappropriate review)
- `POST /admin/reviews/:id/unhide` (restore hidden review)

### A. Review Moderation List

**View all reviews across platform with filters**

```
┌─────────────────────────────────────────────────────────────┐
│  ⭐ REVIEW MODERATION                                        │
│                                                             │
│  Filters:                                                   │
│  Status: [All ▾] [Visible] [Hidden]                         │
│  Role: [All ▾] [Customer] [Contractor]                      │
│  Rating: [All ▾] [1★] [2★] [3★] [4★] [5★]                  │
│  Flagged: [○ All  ◉ Flagged Only]                           │
│  Search: [Search by job ID, user name, review text...]      │
│                                                             │
│  [Export to CSV]                                            │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ┌─ Review #1234 ────────────────────────────────────────┐ │
│  │ ⚠️ FLAGGED (Inappropriate language detected)           │ │
│  │                                                         │ │
│  │ Job: #JOB-2024-5678 (House Cleaning)                   │ │
│  │ Reviewer: John Doe (Customer)                          │ │
│  │ About: Sarah K. (Contractor)                           │ │
│  │ Rating: ⭐⭐ (2/5)                                       │ │
│  │ Posted: Feb 12, 2026 at 3:45 PM                        │ │
│  │ Status: 👁️ Visible                                      │ │
│  │                                                         │ │
│  │ Review Text:                                            │ │
│  │ "Terrible service, she was late and did a horrible     │ │
│  │  job. Don't hire her!!!"                                │ │
│  │                                                         │ │
│  │ [View Full Context] [🚫 Hide Review] [Dismiss Flag]    │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                             │
│  ┌─ Review #1233 ────────────────────────────────────────┐ │
│  │ Job: #JOB-2024-5677 (Plumbing Repair)                  │ │
│  │ Reviewer: Mike T. (Contractor)                         │ │
│  │ About: Jane D. (Customer)                              │ │
│  │ Rating: ⭐⭐⭐⭐⭐ (5/5)                                   │ │
│  │ Posted: Feb 12, 2026 at 1:20 PM                        │ │
│  │ Status: 👁️ Visible                                      │ │
│  │                                                         │ │
│  │ Review Text:                                            │ │
│  │ "Great customer, clear instructions, paid on time!"    │ │
│  │                                                         │ │
│  │ [View Full Context]                                    │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                             │
│  ┌─ Review #1232 ────────────────────────────────────────┐ │
│  │ 🔒 HIDDEN (by Admin Sarah on Feb 11, 2026)             │ │
│  │                                                         │ │
│  │ Job: #JOB-2024-5676 (Electrical Work)                  │ │
│  │ Reviewer: Tom H. (Customer)                            │ │
│  │ About: Alex M. (Contractor)                            │ │
│  │ Rating: ⭐ (1/5)                                         │ │
│  │ Posted: Feb 11, 2026 at 5:00 PM                        │ │
│  │ Status: 🔒 Hidden                                       │ │
│  │                                                         │ │
│  │ Hidden Reason: "Contains personal attacks and          │ │
│  │ unverified accusations. Violates review policy."       │ │
│  │                                                         │ │
│  │ [View Full Context] [✅ Unhide Review]                  │ │
│  └─────────────────────────────────────────────────────────┘ │
│                                                             │
│  [Load More Reviews]                                        │
└─────────────────────────────────────────────────────────────┘
```

**Table Columns:**

- Review ID
- Job reference (linked)
- Reviewer name + role
- Reviewed user name + role
- Rating (⭐)
- Status (Visible/Hidden)
- Flagged? (⚠️ if flagged)
- Date posted
- Actions: View | Hide/Unhide

**Bulk Actions:**

- Select multiple reviews
- Hide selected
- Unhide selected

---

### B. Review Detail View (Full Context)

**Purpose:** See complete context before moderating

```
┌─────────────────────────────────────────────────────────────┐
│  ⭐ REVIEW #1234 - FULL CONTEXT                             │
│                                                             │
│  Status: 👁️ VISIBLE  ⚠️ FLAGGED                              │
│                                                             │
│  [🚫 Hide Review] [Dismiss Flag] [Message Reviewer]         │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  📋 JOB INFORMATION                                         │
│  Job ID: #JOB-2024-5678                                     │
│  Category: House Cleaning                                   │
│  Status: COMPLETED                                          │
│  Completed on: Feb 12, 2026                                 │
│  Job value: $100.00                                         │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  👤 REVIEWER (Customer)                                     │
│  Name: John Doe                                             │
│  User ID: #USER-1234                                        │
│  Trust Tier: NEW_USER                                       │
│  Total jobs: 2                                              │
│  Total reviews written: 2                                   │
│  Avg rating given: 2.5 ⭐                                    │
│  [View Full Profile]                                        │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  👷 REVIEWED USER (Contractor)                              │
│  Name: Sarah K.                                             │
│  User ID: #USER-5678                                        │
│  Trust Tier: ESTABLISHED                                    │
│  Total jobs: 47                                             │
│  Avg rating received: 4.6 ⭐ (from 42 reviews)              │
│  [View Full Profile]                                        │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  ⭐ REVIEW CONTENT                                          │
│  Rating: ⭐⭐ (2/5)                                          │
│  Posted: Feb 12, 2026 at 3:45 PM                            │
│  (2 hours after job completion)                             │
│                                                             │
│  Review Text:                                               │
│  "Terrible service, she was late and did a horrible        │
│   job. Don't hire her!!!"                                   │
│                                                             │
│  ⚠️ Auto-Flag Reason:                                        │
│  Inappropriate language detected: "horrible"                │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  🔍 JOB TIMELINE (for context)                              │
│  • Job posted: Feb 10, 2026 at 9:00 AM                     │
│  • Contractor accepted: Feb 10, 2026 at 11:30 AM            │
│  • Start code verified: Feb 11, 2026 at 10:15 AM            │
│    (15 minutes late - scheduled for 10:00 AM)               │
│  • Work submitted: Feb 11, 2026 at 2:45 PM                  │
│  • Revision requested: Feb 11, 2026 at 4:00 PM              │
│  • Work resubmitted: Feb 11, 2026 at 6:00 PM                │
│  • Customer approved: Feb 11, 2026 at 7:15 PM               │
│  • Review posted: Feb 12, 2026 at 3:45 PM                   │
│                                                             │
│  💬 MESSAGE LOGS:                                           │
│  [View full message history between parties]                │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  📸 WORK SUBMISSION EVIDENCE:                               │
│  [View photos submitted by contractor]                      │
│                                                             │
│  ────────────────────────────────────────────────────       │
│                                                             │
│  💬 CONTRACTOR RESPONSE (Optional - if enabled)             │
│  None yet.                                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Admin Actions:**

- **Hide Review** - Opens modal:

  ```
  ┌────────────────────────────────────────┐
  │ 🚫 HIDE REVIEW                         │
  ├────────────────────────────────────────┤
  │ Reason (required):                     │
  │ ◉ Inappropriate language               │
  │ ○ Personal attack                      │
  │ ○ Unverified accusation                │
  │ ○ Off-topic content                    │
  │ ○ Spam or promotional                  │
  │ ○ Other (specify):                     │
  │   [_________________________]          │
  │                                        │
  │ Internal note:                         │
  │ [Text area for admin notes...]         │
  │                                        │
  │ ⚠️ Hiding this review will:            │
  │ • Remove it from public display        │
  │ • Not notify the reviewer              │
  │ • Keep it in admin audit logs          │
  │ • Update reviewed user's avg rating    │
  │   (excluding this review)              │
  │                                        │
  │ [Cancel] [Confirm Hide Review]         │
  └────────────────────────────────────────┘
  ```

- **Unhide Review** - Opens confirmation:

  ```
  ┌────────────────────────────────────────┐
  │ ✅ UNHIDE REVIEW                       │
  ├────────────────────────────────────────┤
  │ Reason for unhiding (optional):        │
  │ [Text area...]                         │
  │                                        │
  │ This review will be restored to public │
  │ display and included in rating calcs.  │
  │                                        │
  │ [Cancel] [Confirm Unhide]              │
  └────────────────────────────────────────┘
  ```

- **Dismiss Flag** - If flagged by automated system, admin can dismiss if not policy violation

- **Message Reviewer** - Opens communication interface to explain action or request clarification

---

### C. Review Moderation Stats

**Dashboard widget showing:**

```
┌─────────────────────────────────────────────────────────────┐
│  📊 REVIEW MODERATION STATS (Last 30 Days)                  │
├─────────────────────────────────────────────────────────────┤
│  Total Reviews Posted: 342                                  │
│  Auto-Flagged Reviews: 12 (3.5%)                            │
│  Hidden Reviews: 5 (1.5%)                                   │
│  Unhidden (Restored): 1                                     │
│                                                             │
│  Common Hide Reasons:                                       │
│  • Inappropriate language: 3                                │
│  • Personal attacks: 2                                      │
│                                                             │
│  Avg Time to Moderate Flagged Review: 4.2 hours            │
└─────────────────────────────────────────────────────────────┘
```

---

### D. Impact on User Profiles

**When review is hidden:**

- Removed from public display on user's profile
- Excluded from average rating calculation
- Still visible to admins in moderation logs
- No notification sent to reviewer (avoid confrontation)
- Reviewed user's rating recalculated in real-time

**When review is unhidden:**

- Restored to public display
- Re-included in average rating calculation
- No notification sent

---

## 10. Promo Codes (TO BE IMPLEMENTED)

**For SUPER_ADMIN only**

**Endpoints:**

- `POST /admin/promos`
- `GET /admin/promos`
- `PATCH /admin/promos/:id`
- `POST /admin/promos/:id/enable`
- `POST /admin/promos/:id/disable`
- `GET /admin/promos/:id/redemptions`

### Promo Types

- PERCENT_OFF_SERVICE_FEE (e.g., 20% off)
- WAIVE_SERVICE_FEE (free service fee)
- PROCESSING_FEE_SUBSIDY (platform pays processing fee)

### A. Promo List View

**Table:**

- Promo code
- Type
- Discount amount
- Status (Active/Disabled)
- Usage count / limit
- Expiry date
- Actions: Edit | Enable/Disable | View Redemptions

**Actions:**

- [+ Create New Promo]
- Bulk enable/disable

### B. Create/Edit Promo Form

```
┌─────────────────────────────────────────────────────────────┐
│  CREATE PROMO CODE                                          │
├─────────────────────────────────────────────────────────────┤
│  Promo Code: [FIRST10____________] (auto-generate option)   │
│                                                             │
│  Type: [◉ PERCENT_OFF_SERVICE_FEE ▾]                        │
│                                                             │
│  Discount Amount: [10]%                                     │
│                                                             │
│  Eligibility Rules:                                         │
│  ☑ First job only                                           │
│  ☐ Specific roles: [ ] Customer  [ ] Contractor            │
│  ☐ Country: [Liberia ▾]                                     │
│  ☐ Min job value: $[____]                                   │
│  ☐ Max job value: $[____]                                   │
│                                                             │
│  Usage Limits:                                              │
│  Global Usage Cap: [1000] uses                              │
│  Per User Cap: [1] use                                      │
│                                                             │
│  Valid Dates:                                               │
│  Start Date: [📅 Feb 15, 2026]                              │
│  End Date: [📅 Mar 15, 2026]                                │
│                                                             │
│  [Cancel]  [Save as Draft]  [Create & Enable]               │
└─────────────────────────────────────────────────────────────┘
```

### C. Redemption Report

Shows who used the promo:

- User name
- Job ID
- Discount amount
- Date redeemed

**Export:** CSV

---

## 10. Platform Policies (Implemented)

**For SUPER_ADMIN only**

**Endpoint:** `GET /admin/policies`, `PATCH /admin/policies/:key`

**Purpose:** Configure business rules without code deploys

### Policy Categories

**1. Email Policies**

```
┌─────────────────────────────────────────┐
│  📧 EMAIL REQUIREMENTS                  │
├─────────────────────────────────────────┤
│  Customer:                              │
│  ☑ Email required at signup             │
│  ☑ Must verify before posting jobs      │
│                                         │
│  Contractor:                            │
│  ☐ Email required at signup             │
│  ☐ Must verify before withdrawing       │
│                                         │
│  Partner:                               │
│  ☑ Email required at signup             │
│  ☑ Must verify before using platform    │
│                                         │
│  [Save Changes]                         │
└─────────────────────────────────────────┘
```

**2. Security Policies**

```
┌─────────────────────────────────────────┐
│  🔒 SECURITY SETTINGS                   │
├─────────────────────────────────────────┤
│  Withdrawal freeze after phone change:  │
│  [7] days                               │
│                                         │
│  Email verification token expiry:       │
│  [7] days                               │
│                                         │
│  [Save Changes]                         │
└─────────────────────────────────────────┘
```

**3. Trust Tier Thresholds**

```
┌─────────────────────────────────────────┐
│  🏆 TRUST TIER THRESHOLDS               │
├─────────────────────────────────────────┤
│  ESTABLISHED tier requires:             │
│  [3] completed jobs                     │
│  [7] days account age                   │
│                                         │
│  TRUSTED tier requires:                 │
│  [10] completed jobs                    │
│  [30] days account age                  │
│                                         │
│  [Save Changes]                         │
└─────────────────────────────────────────┘
```

---

## 11. Risk & Fraud Dashboard (Partially Implemented)

**Endpoints:**

- `GET /admin/risk/events`
- `GET /admin/risk/summary`

### A. Risk Dashboard

**Top Stats:**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 🚨 High Risk │ ⚠️ Flagged    │ 🔍 Under     │ ✅ Resolved  │
│    Users     │    Events    │    Review    │              │
│ 12           │ 45           │ 8            │ 234          │
│ +2 today     │ +7 today     │ -1 today     │ +12 today    │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**Risk Event Types:**

- Off-platform contact attempts
- Frequent cancellations
- Frequent disputes
- Chargeback involvement
- Suspicious patterns

### B. Risk Events List

**Filters:**

- Event type
- Severity: High | Medium | Low
- User role
- Date range

**Table:**

- Event ID
- User name (linked)
- Event type
- Severity badge
- Description
- Date
- Actions: View User | Dismiss | Flag for Review

### C. User Risk Profile

When viewing a user, show risk section:

```
┌─────────────────────────────────────────┐
│  🛡️ RISK PROFILE                        │
├─────────────────────────────────────────┤
│  Reliability Score: 85/100  🟢          │
│  Professionalism Score: 92/100  🟢      │
│                                         │
│  Risk Flags: 2                          │
│  ├─ Off-platform attempt - Feb 12       │
│  └─ Canceled job - Feb 8                │
│                                         │
│  [View Full History]                    │
└─────────────────────────────────────────┘
```

---

## Phase 4: Analytics & Growth

---

## 12. Metrics Dashboard (TO BE IMPLEMENTED)

**For SUPER_ADMIN only**

**Endpoint:** `GET /admin/metrics/overview`

### Dashboard Sections

**1. Platform Overview**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 👥 Total     │ 📈 Active    │ 🆕 Signups   │ 📊 Growth    │
│    Users     │    (DAU)     │    Today     │    Rate      │
│ 1,332        │ 245          │ 12           │ +15%         │
│ All roles    │ 18.4% rate   │ +2 vs avg    │ vs last week │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**User breakdown by role:**

- Customers: X (Y%)
- Contractors: X (Y%)
- Partners: X (Y%)

**2. Job Metrics**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ 📋 Posted    │ ✅ Completed  │ ⚙️ Active     │ 💰 Avg Value │
│    Today     │    Today     │    Now       │              │
│ 23           │ 67           │ 42           │ $127.50      │
│ +5 vs avg    │ +12 vs avg   │ +8 vs avg    │ +$15 vs avg  │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

**3. Financial Metrics**

- GMV (with breakdown)
- Revenue (all fee types)
- Charts showing trends

**4. Quality Metrics**

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│ ⭐ Avg Rating│ ⚠️ Dispute    │ 🚫 Cancel    │ 👻 No-Show   │
│              │    Rate      │    Rate      │    Rate      │
│ 4.7 / 5.0    │ 2.1%         │ 3.5%         │ 1.2%         │
│ +0.1 vs avg  │ -0.3% ↓      │ -0.2% ↓      │ -0.1% ↓      │
└──────────────┴──────────────┴──────────────┴──────────────┘
```

### Charts

1. **Jobs Over Time (Line Chart)**
   - Posted vs Completed
   - 7 days / 30 days / 90 days

2. **Revenue Over Time (Bar Chart)**
   - Daily/Weekly/Monthly bars
   - Stacked by fee type

3. **User Growth (Line Chart)**
   - New users per day/week
   - By role (3 lines)

4. **Completion Rate (Line Chart)**
   - Jobs completed / Jobs posted ratio over time

### Filters

- Date range selectors
- Role filters
- Export to PDF/CSV

---

## 13. Audit Logs (Implemented - Backend)

**Purpose:** Compliance and tracking of all admin actions

**Endpoint:** `GET /admin/audit-logs`

### Audit Log Viewer

**Filters:**

- Admin (who performed action)
- Action type: approve_profile | reject_profile | ban_user | etc.
- Target type: identity | role_profile | job | case
- Date range

**Table:**

- Timestamp
- Admin name
- Action
- Target (type + ID, linked)
- IP address
- User agent
- Details (expandable)

**Expandable Row:**

```
┌─────────────────────────────────────────────────────────────┐
│  Action Details:                                            │
│  ───────────────────────────────────────────────────────    │
│  Before:                                                    │
│  {                                                          │
│    "status": "PENDING",                                     │
│    "approvedAt": null                                       │
│  }                                                          │
│                                                             │
│  After:                                                     │
│  {                                                          │
│    "status": "APPROVED",                                    │
│    "approvedAt": "2026-02-15T10:30:00Z",                   │
│    "approvedByAdminId": "admin-uuid-123"                   │
│  }                                                          │
│                                                             │
│  Notes: "User provided all required documents"              │
└─────────────────────────────────────────────────────────────┘
```

**Export:**

- CSV download
- PDF report for compliance

---

## 14. Message Moderation (TO BE IMPLEMENTED)

**Purpose:** Review and moderate inappropriate messages

**Endpoints:**

- `POST /admin/threads/:id/lock`
- `POST /admin/threads/:id/unlock`
- `POST /admin/messages/:id/tombstone`

### Features

**Flagged Messages Queue:**

- Auto-flagged by content filters
- User reports
- Severity: High | Medium | Low

**Message Viewer:**

- Full conversation thread
- Highlighted flagged content
- User profiles (both parties)

**Actions:**

- Tombstone message (hide content, keep record)
- Lock thread (prevent further messages)
- Warn user (send system message)
- Ban user (if severe)

---

## 15. Referral Partners (TO BE IMPLEMENTED - Feature Flagged)

**For SUPER_ADMIN only**

**Purpose:** Manage hotel/Airbnb host referral codes with commission tracking

**Endpoints:**

- `POST /admin/referral-partners`
- `GET /admin/referral-partners`
- `PATCH /admin/referral-partners/:id`
- `POST /admin/referral-partners/:id/disable`
- `GET /admin/referral-partners/:id/accruals`

### A. Partner List

**Table:**

- Partner name
- Referral code
- Commission rate
- Total referred users
- Total commission earned
- Status (Active/Disabled)
- Actions: Edit | View Accruals | Disable

### B. Create/Edit Partner

```
┌─────────────────────────────────────────┐
│  CREATE REFERRAL PARTNER                │
├─────────────────────────────────────────┤
│  Partner Name: [____________]           │
│  Organization: [____________]           │
│                                         │
│  Referral Code: [HOTEL10___] (auto)    │
│                                         │
│  Commission Rate: [5]%                  │
│    (% of platform revenue from          │
│     referred users)                     │
│                                         │
│  Attribution Window: [90] days          │
│                                         │
│  [Cancel]  [Create Partner]             │
└─────────────────────────────────────────┘
```

### C. Commission Accruals

Shows earnings from referred users:

- User referred
- Date joined
- Jobs completed
- Platform revenue from user
- Commission earned
- Status: Pending | Cleared | Paid

**Payout management:**

- Batch payouts
- Export statements

---

## Navigation Structure

### Top Navigation Bar

```
┌─────────────────────────────────────────────────────────────┐
│  [Kadû Logo]  Dashboard | Waitlist | Users | Jobs | Cases  │
│                                                             │
│                          [🔔 5] [Admin: Sarah ▾] [Logout]   │
└─────────────────────────────────────────────────────────────┘
```

### Sidebar Navigation (Alternative)

```
┌─────────────────┐
│ [Kadû Logo]     │
│                 │
│ 🏠 Dashboard    │
│ 👥 Waitlist     │ ← Priority
│ 👤 Users        │
│ 💼 Jobs         │
│ 📋 Cases        │
│ 💰 Finance      │
│ 🛡️ Risk         │
│ ───────────     │
│ ⚙️ Settings     │
│ 📊 Metrics      │ ← Super Admin
│ 💸 Pricing      │ ← Super Admin
│ 🎟️ Promos       │ ← Super Admin
│ 📜 Audit        │
│                 │
└─────────────────┘
```

### Breadcrumbs

Every page should have breadcrumbs:

```
Dashboard > Waitlist > Customer Details > John Doe
```

---

## Responsive Design Notes

### Desktop (1920px - 1280px)

- Full table views
- Sidebar navigation
- Multi-column layouts
- All filters visible

### Tablet (1024px - 768px)

- Collapsible sidebar
- Tables convert to scrollable horizontal
- Some filters in dropdown menus
- Stats cards in 2 columns

### Mobile (< 768px)

- Hamburger menu
- Card views replace tables
- Single column layouts
- Bottom navigation for quick actions
- Filters behind "Filter" button
- Simplified charts

---

## Notification System

### Bell Icon (Top Right)

**Badge:** Shows count of unread notifications

**Dropdown:**

```
┌─────────────────────────────────────────┐
│  🔔 NOTIFICATIONS (5 unread)            │
├─────────────────────────────────────────┤
│  🔴 New case opened                     │
│     Dispute on Job #1234                │
│     2 minutes ago                       │
│  ───────────────                        │
│  🟡 New waitlist signup                 │
│     John Doe (Customer)                 │
│     5 minutes ago                       │
│  ───────────────                        │
│  🟢 Case resolved                       │
│     Job #5678 dispute closed            │
│     1 hour ago                          │
│  ───────────────                        │
│  [Mark All Read]  [View All]            │
└─────────────────────────────────────────┘
```

**Notification Types:**

- New waitlist signup (🟡 yellow)
- Case opened (🔴 red)
- Dispute filed (🔴 red)
- Chargeback (🔴 red, critical)
- System alert (⚪ gray)
- Case resolved (🟢 green)

**Actions:**

- Click notification → navigate to relevant page
- Mark as read
- Mark all read
- Notification settings (enable/disable by type)

---

## Loading States & Error Handling

### Loading States

- Skeleton screens for tables/lists
- Spinner for actions (approve/reject)
- Progress bars for bulk operations
- "Loading..." text for simple elements

### Error States

- Toast notifications for errors:
  - Red for critical errors
  - Orange for warnings
  - Blue for info messages
  - Green for success
- Inline validation errors (forms)
- Empty states with helpful messages
- Network error banner at top

### Success Feedback

- Toast notifications
- Checkmark animations
- Updated counts/badges
- Message: "✅ Action completed successfully"

---

## Accessibility (a11y)

### Requirements

- WCAG 2.1 AA compliance
- Keyboard navigation (Tab, Enter, Esc)
- Screen reader support (ARIA labels)
- Focus indicators (visible outlines)
- Color contrast ratios (4.5:1 minimum for text)
- Alt text for images
- Form labels properly associated
- Skip navigation links

---

## Security Indicators

### Admin Session

- Show logged-in admin name
- Session timeout warning (5 min before expiry)
- Auto-logout on inactivity (30 min)
- "Last login" display

### Sensitive Actions

- Re-confirm critical actions (ban user, approve payout)
- Show "Are you sure?" modals
- Log all state-changing actions

---

## Performance Considerations

### Optimization

- Paginate long lists (max 100 items)
- Lazy load images
- Debounce search inputs (300ms)
- Cache common queries
- Optimize images (WebP format)
- Progressive loading (show critical data first)

### Data Refresh

- Auto-refresh stats every 30 seconds (optional toggle)
- Manual refresh button always available
- "Last updated" timestamp shown

---

## Future Enhancements (Post-MVP)

### Phase 5+

- Advanced reporting (custom date ranges, export schedules)
- Admin mobile app
- Bulk operations (mass updates)
- Saved filters/views
- Admin collaboration (assign cases to specific admins)
- Push notifications for admins
- Two-way messaging with file attachments
- Video evidence review
- AI-assisted case resolution suggestions
- Fraud detection ML models
- Advanced analytics (cohort analysis, retention, LTV)

---

## Technical Notes for Developers

### API Integration

- All endpoints use RESTful conventions
- Authentication: JWT tokens in Authorization header
- Error responses follow API_CONVENTIONS.md
- Pagination: `?page=1&limit=20`
- Filters: Query parameters
- Sorting: `?sort=createdAt&order=desc`

### State Management

- Use React Context or Redux for global state
- Cache frequently accessed data
- Optimistic updates for better UX
- Rollback on errors

### Data Fetching

- Use React Query or SWR for data fetching
- Handle loading/error/success states
- Implement retry logic
- Show stale data while refetching

---

## Glossary for Non-Technical Designer

### Technical Terms Explained

- **Endpoint:** A URL that the frontend calls to get or send data (e.g., `GET /admin/waitlist`)
- **Idempotent:** Calling the same action multiple times has the same effect (safe to retry)
- **Basis Points (bp):** 1 bp = 0.01%. Used for precise fee calculations. 100 bp = 1%
- **GMV:** Gross Merchandise Volume - total transaction value
- **UUID:** Unique identifier (looks like: `abc123-def456-ghi789`)
- **E.164 format:** International phone number format (e.g., +231770123456)
- **Modal:** Popup window overlaying the main screen
- **Toast:** Small notification that appears temporarily (usually top-right)
- **Badge:** Small label/pill showing status or count
- **Dropdown:** Click to reveal options menu
- **Tab:** Switch between different views without leaving the page
- **Pagination:** Break long lists into pages (Page 1, 2, 3...)
- **Filter:** Narrow down results by criteria
- **Sort:** Change the order of items (A-Z, date, etc.)
- **Breadcrumb:** Navigation path showing where you are (Home > Users > John)

---

## Questions for Designer?

If anything is unclear or you need more details on:

- Specific layouts
- Interaction patterns
- Component specifications
- Iconography
- Typography requirements
- Animation/transitions

Please reach out for clarification!

---

**END OF DOCUMENT**

Version 1.0 | February 15, 2026
