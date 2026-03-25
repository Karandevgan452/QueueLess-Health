# QueueLess Health — Full Implementation Plan

This document is the end-to-end implementation blueprint for QueueLess Health.
It is based on:
- `Planning.md` (user stories + DoD)
- `UserFlow.md` (detailed journey and state flow)
- Shared UI references in this repository (`image.png`, `image copy.png`, `image copy 2.png`, `image copy 3.png`, `image copy 4.png`, `image copy 5.png`)

---

## 1. Product Goal

Build a single-server Node.js application where:
- Express hosts backend APIs
- React frontend is built and served by the same Express app
- OPD intake supports digital + paper/hybrid workflows
- AI assists triage, OCR, urgency detection, and simplification
- Staff and doctors operate through controlled role-based modules

Primary outcome: reduce OPD queue time while preserving existing hospital habits (paper + assisted intake).

---

## 2. Non-Negotiable Business Rules (Mandatory)

## 2.1 Patient account creation rules

Patients **cannot** sign up using Google/social auth.

Patients are created only through OPD workflow:
1. OPD enrollment / check-up form fill (self or assisted), or
2. Handwritten slip submission + OCR/staff entry

Default behavior for a new patient:
- Patient must request OPD card/profile by completing form/slip intake
- If patient profile already exists, system links to existing profile
- If not found, profile is created during OPD intake

## 2.2 Staff and doctor onboarding rules

- Doctors and staff are added separately by admin/super-admin
- No public self-registration for doctor/staff roles
- Role assignment is explicit (`doctor`, `staff`, `admin`)
- Audit record required for every user creation/update

## 2.3 Access model

- Patient access: OTP-based (mobile + OTP) or hospital-provided identifier
- Staff/Doctor/Admin access: credential-based login with RBAC
- No external OAuth provider in MVP (including Google)

---

## 3. UI Reference Map (Design Replication Inputs)

Use these images as canonical visual reference:

1. `image.png` — Language selection
2. `image copy.png` — Returning patient OTP + registration cards
3. `image copy 2.png` — Patient profile form layout
4. `image copy 3.png` — Symptom intake method selection
5. `image copy 4.png` — Symptom capture + triage panel
6. `image copy 5.png` — Registration confirmed/token card + actions

Design implementation guidance:
- Keep visual hierarchy and layout structure aligned with these references
- Maintain clean clinical style (light backgrounds, readable cards, minimal clutter)
- Preserve stepwise journey: language → identity → profile → intake method → symptom capture → token

---

## 4. System Architecture (Single-Server Deployment)

## 4.1 Runtime architecture

- Node.js runtime
- Express app with two responsibilities:
  1. REST API layer (`/api/*`)
  2. Static serving of React build (`/`, `/app/*`)

Recommended monorepo structure:

- `/server` Express backend
- `/client` React frontend
- `/shared` shared schema/types
- `/docs` planning and architecture docs

Production serving pattern:
1. Build React app (`client/build`)
2. Express serves static assets
3. Express fallback route returns `index.html` for SPA paths

## 4.2 Data and storage

Recommended baseline:
- PostgreSQL for transactional data
- Redis for queue cache/session/OTP throttling
- Object storage (S3-compatible/minio/local) for scanned slips and uploaded prescriptions

## 4.3 AI services integration

AI modules exposed through service layer:
- OCR + handwriting extraction
- Symptom-to-department triage
- Urgency scoring
- Medical text simplification

All AI outputs must include:
- confidence
- model version
- `isOverridden` flag
- reviewer identity when edited

---

## 5. Roles, Modules, and Navigation

## 5.1 Patient module

Main routes:
- `/patient/language`
- `/patient/identity`
- `/patient/profile`
- `/patient/intake-method`
- `/patient/symptoms`
- `/patient/token`
- `/patient/history`

Capabilities:
- Local language experience
- OTP identity verification
- OPD enrollment / check-up form
- Slip upload (if self-assisted)
- Token and queue tracking
- Visit history and simplified notes

## 5.2 Staff module

Main routes:
- `/staff/intake`
- `/staff/scan-ocr`
- `/staff/queue-desk`
- `/staff/patient-search`

Capabilities:
- Fast assisted entry (<30 sec target)
- Bulk scan and OCR correction
- Token generation/reprint
- Queue assistance and no-show handling

## 5.3 Doctor module

Main routes:
- `/doctor/queue`
- `/doctor/consult/:visitId`
- `/doctor/completed`

Capabilities:
- Queue with priority markers
- Paper-first consultation support
- Upload handwriting and AI digitization
- Template/voice structured entry
- Complete consultation and notify patient

## 5.4 Admin module

Main routes:
- `/admin/operations`
- `/admin/department-control`
- `/admin/audit`
- `/admin/reports`
- `/admin/users`

Capabilities:
- Queue/load balancing controls
- Emergency prioritization
- User provisioning (staff/doctor)
- Audit exports

---

## 6. End-to-End Workflow Logic

## 6.1 State machine for visits

States:
1. `registered`
2. `intake_completed`
3. `triage_assigned`
4. `token_issued`
5. `waiting`
6. `in_consultation`
7. `completed`
8. `archived`

Exception states:
- `on_hold`
- `rerouted`
- `cancelled`
- `no_show`

Rules:
- Every transition writes audit event
- Status timeline shown in UI
- No visit can remain without terminal/active state owner

## 6.2 Patient creation decision flow

On identity step:
- Check patient by phone + demographics
- If found: link existing profile
- If not found: create profile only after OPD form/slip completion

Disallowed flow:
- Creating patient profile from standalone social login

## 6.3 Doctor/staff user creation flow

- Admin creates user in `/admin/users`
- System sends initial activation credential flow
- First login requires password reset + MFA/OTP (optional by environment)
- User role immutable except admin override with audit

---

## 7. Data Model (Core Entities)

## 7.1 Users
- `id`
- `role` (`admin|doctor|staff`)
- `name`
- `phone`
- `email` (optional)
- `isActive`
- `createdBy`
- timestamps

## 7.2 Patients
- `id`
- `hospitalCardId` (nullable until generated)
- `name`, `age`, `gender`, `phone`
- `address`, `guardianName`
- `preferredLanguage`
- `isMerged`, `mergedIntoPatientId`
- timestamps

## 7.3 Visits / OPD Encounters
- `id`
- `patientId`
- `departmentId`
- `tokenNumber`
- `status`
- `urgency`
- `intakeSource` (`form|voice|slip|staff`)
- `symptomRaw`, `symptomNormalized`
- `assignedDoctorId`
- timestamps

## 7.4 Documents
- `id`
- `visitId`
- `type` (`opd_slip|prescription|report`)
- `storageUrl`
- `ocrText`
- `ocrConfidence`
- `verifiedBy`

## 7.5 AI Events
- `id`
- `visitId`
- `taskType` (`triage|urgency|ocr|simplify`)
- `inputRef`
- `outputJson`
- `confidence`
- `modelVersion`
- `isOverridden`
- `overriddenBy`

## 7.6 Audit Logs
- `id`
- `actorType` (`system|user`)
- `actorId`
- `entityType`
- `entityId`
- `action`
- `beforeJson`
- `afterJson`
- timestamp

---

## 8. API Surface (MVP)

## 8.1 Auth
- `POST /api/auth/patient/request-otp`
- `POST /api/auth/patient/verify-otp`
- `POST /api/auth/staff/login`
- `POST /api/auth/doctor/login`
- `POST /api/auth/admin/login`

## 8.2 Patient + intake
- `POST /api/patients/lookup`
- `POST /api/patients/create-from-opd`
- `POST /api/visits/create`
- `POST /api/visits/:id/intake`
- `POST /api/visits/:id/triage`
- `POST /api/visits/:id/token`

## 8.3 Documents/OCR
- `POST /api/documents/upload`
- `POST /api/documents/:id/ocr`
- `PATCH /api/documents/:id/verify`

## 8.4 Doctor operations
- `GET /api/doctor/queue`
- `POST /api/visits/:id/start-consultation`
- `PATCH /api/visits/:id/clinical-notes`
- `POST /api/visits/:id/complete`

## 8.5 Admin operations
- `GET /api/admin/dashboard`
- `POST /api/admin/queue/reassign`
- `POST /api/admin/queue/priority`
- `GET /api/admin/audit`
- `POST /api/admin/users`

---

## 9. Feature-to-Story Traceability

- US-P1, US-A1, US-A2 → intake method + assisted entry + OCR correction
- US-P2 → multilingual UI and translation pipeline
- US-P3 → token generation + queue tracker + notifications
- US-P4, US-P5 → history timeline + simplification service
- US-D1, US-D2, US-D3 → paper-first + upload + digital templates + voice
- US-D4, US-AI1, US-AI3 → triage suggestion + urgency + confidence + override
- US-D5, US-D6, US-S4 → completion workflow + accountability + immutable logs
- US-A3, US-A4 → operations dashboard + audit/report exports
- US-S1, US-S2, US-S3 → offline sync + duplicate merge + OPD card generation
- US-AI2, US-AI4, US-AI5 → OCR digitization + simplification + assist-not-replace policy

---

## 10. Frontend Implementation Plan (React)

## 10.1 UX implementation sequence

1. Language selection screen (match `image.png`)
2. Identity/OTP + returning/new cards (match `image copy.png`)
3. Patient profile form (match `image copy 2.png`)
4. Intake method selector (match `image copy 3.png`)
5. Symptom capture + triage panel (match `image copy 4.png`)
6. Registration/token confirmation (match `image copy 5.png`)

## 10.2 Component strategy

- Shared shell (header, top progress, role-aware nav)
- Form components with validation + translation-ready labels
- Reusable cards for token, queue stepper, patient summary
- OCR correction editor with confidence highlighting
- Doctor consultation panel with upload + structured notes

## 10.3 State management

- React Query (server state)
- Context/Zustand for local wizard state
- Optimistic queue updates with rollback on conflict

---

## 11. Backend Implementation Plan (Express)

## 11.1 Service layering

- Controllers: request/response shaping
- Services: domain logic (triage flow, token logic, consultation)
- Repositories: DB operations
- Integrations: AI provider, SMS/WhatsApp gateway, storage adapter

## 11.2 Queue logic

- Department-level queue buckets
- Priority insertion for critical patients
- Token sequencing with collision-safe generator

## 11.3 Validation and policy checks

Critical guards:
- deny patient social signup
- enforce `create-from-opd` workflow
- restrict doctor/staff creation to admin endpoints

---

## 12. Security, Compliance, and Audit

- RBAC on every API route
- PHI-sensitive fields encrypted at rest (where applicable)
- Signed URLs for document fetch
- Immutable audit logging
- Rate limiting on OTP endpoints
- PII-safe application logging (no raw symptoms in standard logs)

---

## 13. Offline-First and Sync Strategy

- Front desk client caches intake payloads locally when offline
- Queue actions stored as local event queue
- On reconnect: sync oldest-first with idempotency keys
- Conflict policy: admin/staff reconciliation UI for duplicates/status collisions

---

## 14. Notifications and Communication

- Token issued: SMS/WhatsApp with number + link + QR reference
- Called for consultation: optional notification
- Consultation completed: summary available in patient history

---

## 15. Delivery Phases

## Phase 1 (Foundation)
- Monorepo setup
- Auth + RBAC skeleton
- Patient identity + profile + intake
- Token generation and waiting queue

## Phase 2 (Clinical Flow)
- Doctor queue + consultation + completion
- Document upload + OCR correction UI
- Basic audit trail

## Phase 3 (AI + Operations)
- Triage/urgency integration
- Simplified notes
- Admin operations dashboard + exports

## Phase 4 (Hardening)
- Offline sync
- Duplicate merge workflow
- performance tuning + observability

---

## 16. Acceptance Criteria Checklist

Implementation is complete when:

1. Patient cannot create account via Google/social provider
2. New patient profiles are created only via OPD form/slip workflow
3. Doctor and staff users can only be created by admin
4. End-to-end journey from language screen to token generation works
5. Doctor can complete consultation with paper or digital entry
6. Audit trail captures key actions (AI + user edits + status transitions)
7. Queue state machine is enforced and visible
8. UI aligns with shared design reference screens

---

## 17. Risks and Mitigations

- OCR quality variance → confidence thresholds + mandatory manual correction path
- Duplicate patient errors → merge confirmation UI + reversible merge log
- Queue bottlenecks at peak → department balancing + emergency override controls
- AI overreach risk → advisory-only outputs + visible confidence + human override required

---

## 18. Immediate Next Build Order (Actionable)

1. Create base Express + React single-server scaffold
2. Implement auth and role separation (patient OTP vs staff/doctor/admin credentials)
3. Build patient intake wizard matching six reference screens
4. Add visit state machine + token queue engine
5. Build doctor consultation workflow
6. Add staff OCR correction and admin dashboard
7. Integrate AI services and final audit/reporting

