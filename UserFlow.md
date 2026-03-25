# QueueLess Health — End-to-End User Flow

This document maps the complete user flow of the QueueLess Health application for UI/UX design.
It covers all key personas, screen transitions, decision branches, and edge flows.

## 1) Personas

- Patient (tech-enabled)
- Patient (non-tech / assisted)
- Front Desk Staff
- Doctor
- Admin / Operations

---

## 2) Global App Entry Points

### A. Patient Self Entry
- Mobile web app / kiosk opens.
- Language selection shown first.
- Patient chooses:
  - New registration
  - Returning patient

### B. Assisted Entry (Front Desk)
- Staff dashboard opens.
- Staff selects “Create New Token”.
- Staff enters details for patient or scans handwritten slip.

### C. Doctor Entry
- Doctor login.
- Queue dashboard for assigned department.
- Pending + in-consultation + completed tabs.

### D. Admin Entry
- Admin login.
- OPD operations dashboard (queue load, emergency flags, staff activity, AI logs).

---

## 3) Patient Journey (Primary)

## 3.1 Start & Registration

### Screen: Welcome / Language
- Inputs: language choice (Hindi/English/regional).
- Output: localized UI.

### Screen: Identity Step
- Options:
  - Mobile number + OTP
  - Existing Patient ID
  - “Continue as assisted patient” (kiosk with staff)
- Decision:
  - Existing match found → show patient confirmation card.
  - No match → go to new profile creation.

### Screen: Patient Profile
- Required fields: name, age, gender, phone.
- Optional: address, guardian, previous hospital ID.
- Duplicate-check runs in background (phone + demographics).
- Decision:
  - Possible duplicate found → confirm merge or create new.

## 3.2 Symptom Intake

### Screen: Intake Method Selection
- Options:
  - Type symptoms
  - Voice input
  - Upload/scan handwritten slip
  - Staff enters from paper

### Screen: Symptom Capture
- Patient enters chief complaint + duration + severity.
- Multi-language input accepted.
- Original text preserved; translated text created for workflow.

### AI Step: Triage Suggestion
- AI outputs:
  - Suggested department
  - Urgency level (normal / priority / critical)
  - Confidence score
- Decision:
  - Low confidence → staff review required.
  - Critical urgency → immediate priority route.

## 3.3 Token & Queue

### Screen: Token Generated
- Shows token number, department, estimated wait time.
- QR code generated.
- Notification sent via SMS/WhatsApp (if available).

### Screen: Queue Status Tracker
- Live status:
  - Waiting
  - Called
  - In consultation
  - Completed
- Patient can monitor via link or display board.

## 3.4 Consultation & Post-Consultation

### During Consultation
- Patient is marked “In consultation” by doctor/staff.

### After Consultation
- Status becomes “Completed”.
- Patient receives:
  - Visit summary
  - Prescription record (image + digitized text when available)
  - Follow-up instructions

### Screen: History & Records
- Patient can view visit timeline.
- Can open simplified AI explanation of doctor notes in local language.

---

## 4) Non-Tech Patient Assisted Journey

## 4.1 Front Desk Intake

### Screen: Staff Quick Entry
- Staff enters minimal patient details in <30 sec target.
- Language assistance enabled.

### Screen: Slip Scan / Upload
- Staff scans handwritten slip (single or bulk).
- OCR extracts text + confidence.
- Manual correction UI appears for uncertain fields.

### Screen: Verification
- Staff verifies profile + symptoms.
- Submits to triage.

## 4.2 Token Handoff
- Printed token or verbal token number provided.
- Optional printed OPD card with QR.

## 4.3 Queue Assistance
- Staff can search patient by name/phone/token.
- Staff can reprint token/OPD card.

---

## 5) Doctor Journey

## 5.1 Queue View

### Screen: Doctor Dashboard
- Sections:
  - Waiting patients
  - Priority/Critical patients (pinned at top)
  - Completed consultations

### Patient Card Data
- Token
- Basic demographics
- Symptom summary
- AI suggested department/urgency/confidence
- Intake source (typed/voice/handwritten/staff)

## 5.2 Consultation Workflow

### Step 1: Start Consultation
- Doctor clicks “Start”.
- System sets status = in_consultation.
- Timestamp logged.

### Step 2: Record Clinical Notes
- Options for doctor:
  - Write on paper as usual
  - Upload/scan handwritten note/prescription
  - Use digital template
  - Use voice-to-text

### AI Step: Digitization Assist
- Handwritten notes are OCR-processed.
- Medical correction + structured extraction suggested.
- Doctor reviews and edits before save.

### Step 3: Finalize Visit
- Doctor sets diagnosis / advice / medication / follow-up.
- Clicks “Complete Consultation”.
- Status = completed.
- Patient notified.

## 5.3 Accountability & Safety
- Every action linked to doctor identity.
- No patient can remain in ambiguous state (waiting/in consultation/completed required).
- Override actions are always available to doctor.

---

## 6) Admin / Operations Journey

## 6.1 Live OPD Control

### Screen: Operations Dashboard
- Metrics:
  - Total waiting
  - Department-wise queue length
  - Average wait time
  - Critical cases count

### Actions
- Set queue limits
- Re-route overflow to another department
- Mark emergency priority
- Assign/reassign doctor load

## 6.2 Audit & Governance

### Screen: Activity Logs
- Tracks:
  - AI triage decisions
  - Doctor updates
  - Staff edits/corrections
  - Status changes with timestamps

### Actions
- Filter by date/department/user
- Export audit reports

---

## 7) Core System State Flow

All patient visits move through strict states:

1. Registered
2. Intake Completed
3. Triage Assigned
4. Token Issued
5. Waiting
6. In Consultation
7. Completed
8. Archived to History

Allowed exception states:
- On Hold
- Re-routed Department
- Cancelled / No-show

Design note: every state change should be visible in timeline and log.

---

## 8) AI-Assisted Decision Points

1. Symptom classification to department
2. Urgency detection (critical flag)
3. OCR for handwritten intake/prescriptions
4. Medical note simplification for patients
5. Duplicate patient detection

Design note: each AI output should display confidence + “edited/overridden by human” indicator.

---

## 9) Failure and Edge Flows

## 9.1 OCR Failure
- Show low-confidence warning.
- Route to manual correction screen.
- Save both original image and corrected text.

## 9.2 Duplicate Suspected
- Prompt merge decision.
- Show side-by-side patient summaries.
- Require confirmation before merge.

## 9.3 Offline Mode
- Intake and token actions stored locally.
- UI badge: “Offline — Sync Pending”.
- On reconnect, automatic sync + conflict review.

## 9.4 No-Show / Drop-Off
- Staff or doctor marks no-show.
- Patient can be recalled or closed.

## 9.5 Department Misroute
- Doctor/admin can reassign department.
- Token/queue re-positioning happens automatically.

---

## 10) UX Deliverables Checklist for Design

Create designs for these key screens:

1. Language + Welcome
2. Patient identity (new/returning)
3. Patient profile form
4. Intake method selector
5. Symptom entry (text/voice/upload)
6. OCR correction screen (staff)
7. Token generated + QR
8. Queue tracker (patient view)
9. Staff quick-entry dashboard
10. Doctor queue dashboard
11. Doctor consultation workspace (paper upload + digital edit)
12. Consultation completion summary
13. Admin operations dashboard
14. Audit logs and reports
15. Patient history and simplified notes view

---

## 11) Suggested Navigation Structure

### Patient App
- Home
- Registration
- Intake
- Token/Queue
- History
- Profile

### Staff App
- Quick Intake
- Scan/OCR
- Queue Desk
- Search Patient

### Doctor App
- Queue
- Consultation
- Completed Visits

### Admin App
- Operations
- Department Control
- Audit Logs
- Reports

---

## 12) End-to-End Summary (One-Line)

Patient registers (self or assisted) → symptoms captured (text/voice/paper) → AI triage + urgency → token + queue tracking → doctor consultation (paper or digital) → AI digitization + doctor validation → consultation complete → records and simplified summary available to patient.