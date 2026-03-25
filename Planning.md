# Product Definition

## Table of Contents

- [Product Definition](#product-definition)
- [1. Patient User Stories (Enhanced)](#1-patient-user-stories-enhanced)
	- [Intake (Hybrid Support)](#intake-hybrid-support)
	- [Records & Reports](#records--reports)
- [2. Doctor User Stories (Hybrid — Very Important)](#2-doctor-user-stories-hybrid--very-important)
	- [Writing Workflow (Key Innovation)](#writing-workflow-key-innovation)
	- [Patient Handling](#patient-handling)
- [3. Admin / Staff User Stories](#3-admin--staff-user-stories)
	- [Front Desk (Critical in India)](#front-desk-critical-in-india)
	- [Operations](#operations)
- [4. System User Stories (Real-World Ready)](#4-system-user-stories-real-world-ready)
- [5. AI User Stories (Core Differentiator)](#5-ai-user-stories-core-differentiator)

- Hybrid AI-powered OPD Management System
- Works with paper + digital
- Supports low-tech + high-tech patients
- Helps doctors write normally but digitize seamlessly
- Provides AI-assisted triage + documentation

## 1. Patient User Stories (Enhanced)

### Intake (Hybrid Support)

#### US-P1
As a patient, I can either fill a digital form OR submit a handwritten slip.

**DoD**
- Option: Form / Upload / Kiosk / Staff entry
- OCR works for handwritten slips
- Staff override allowed if OCR fails
- Input stored as raw + structured data

#### US-P2
As a patient, I should be able to use my local language.

**DoD**
- Supports Hindi + regional languages
- Auto translation to English for system
- Original text preserved

#### US-P3
As a patient, I should get a token without standing in line.

**DoD**
- Token generated instantly after submission
- SMS / WhatsApp / screen display
- QR code included

### Records & Reports

#### US-P4
As a patient, I should view my OPD history.

**DoD**
- Visit timeline visible
- Includes doctor notes (digitized)
- Secure access

#### US-P5
As a patient, I should receive simplified explanations.

**DoD**
- AI converts doctor notes → simple language
- Multi-language output
- Example: “Gastritis” → “stomach irritation”

## 2. Doctor User Stories (Hybrid — Very Important)

### Writing Workflow (Key Innovation)

#### US-D1
As a doctor, I should be able to write on paper as usual.

**DoD**
- No forced digital typing
- Paper workflow unchanged

#### US-D2
As a doctor, I should be able to digitize my handwritten notes easily.

**DoD**
- Option 1: Scan/upload prescription
- Option 2: Assistant uploads
- Option 3: AI auto-digitizes handwriting
- Editable before saving

#### US-D3
As a doctor, I should optionally use structured digital entry.

**DoD**
- Quick templates (fever, gastric, etc.)
- Voice-to-text supported
- Minimal clicks UI

### Patient Handling

#### US-D4
As a doctor, I should see AI-suggested department/reason.

**DoD**
- Shows symptoms summary
- AI confidence score
- Override option

#### US-D5
As a doctor, I should mark consultation complete.

**DoD**
- Status updated
- Timestamp stored
- Patient notified

#### US-D6
As a doctor, I should track accountability.

**DoD**
- Every patient mapped to doctor
- Logs maintained
- No patient left unmarked

## 3. Admin / Staff User Stories

### Front Desk (Critical in India)

#### US-A1
As staff, I should enter data for non-tech patients.

**DoD**
- Simple UI for quick entry
- Language assistance
- Fast (<30 sec per patient)

#### US-A2
As staff, I should scan paper slips.

**DoD**
- Bulk scan support
- Auto OCR + tagging
- Manual correction option

### Operations

#### US-A3
As admin, I should manage OPD flow.

**DoD**
- Control queue limits
- Emergency prioritization
- Department load balancing

#### US-A4
As admin, I should audit system activity.

**DoD**
- Logs for:
	- AI decisions
	- Doctor actions
	- Edits
- Exportable reports

## 4. System User Stories (Real-World Ready)

#### US-S1
As a system, I should support offline-first mode.

**DoD**
- Data cached locally
- Sync when internet returns
- Conflict resolution handled

#### US-S2
As a system, I should merge duplicate patients.

**DoD**
- Match via phone/name/age
- AI-assisted matching
- Manual confirmation

#### US-S3
As a system, I should generate OPD card (digital + printable).

**DoD**
- Printable format (hospital compatible)
- QR code
- Works without app

#### US-S4
As a system, I should maintain audit trail.

**DoD**
- Every action logged
- Immutable history
- Admin access

## 5. AI User Stories (Core Differentiator)

#### US-AI1
As AI, I should classify symptoms → department.

**DoD**
- Input: text/image/voice
- Output: department + confidence
- Accuracy benchmark (>80%)

#### US-AI2
As AI, I should digitize handwritten prescriptions.

**DoD**
- OCR + medical correction
- Editable output
- Confidence score

#### US-AI3
As AI, I should detect urgency.

**DoD**
- Flag critical cases
- Alert admin/doctor
- Priority queueing

#### US-AI4
As AI, I should explain medical info simply.

**DoD**
- Converts doctor notes → layman terms
- Multi-language output

#### US-AI5
As AI, I should assist doctor (not replace).

**DoD**
- Suggestions only
- Doctor override always available
