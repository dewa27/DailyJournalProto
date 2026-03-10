# Product Requirements Document (PRD)
## Daily Journal — Teacher's Daily Report Form

**Document Version:** 1.0  
**Date:** 2026-03-06  
**Prepared by:** Antigravity (AI Coding Assistant)  
**Project Type:** Web Application Prototype  
**Platform:** Browser-based (Responsive — Desktop & Mobile)

---

## 1. Overview

### 1.1 Product Summary

**Daily Journal** is a browser-based daily reporting tool designed for teachers at **Timedoor Academy**. It enables teachers to quickly document and submit a structured academic progress report for each student after every class session.

The tool exists as two separate HTML files optimized for their respective form factors:

| File | Target Platform | Interaction Pattern |
|------|----------------|---------------------|
| `index.html` | Desktop / Tablet | All-at-once sidebar + main panel layout |
| `mobile-index.html` | Mobile (phones) | Step-by-step wizard (3 steps) |

The final output — the **Daily Report** — summarizes which lessons were covered, individual lesson performance scores, active project information, teacher observations, and an AI-generated summary paragraph. The report can be previewed (desktop) and printed as a PDF.

---

## 2. Goals & Objectives

### 2.1 Primary Goals
1. **Speed & Simplicity** — Allow a teacher to fill out a complete daily academic report in under 2 minutes.
2. **Consistency** — Standardize the data format of all reports across the academy.
3. **Professional Output** — Generate a clean, printable A4 PDF report in multiple design themes.
4. **Mobile Accessibility** — Provide an equally functional experience on mobile devices (phones used in-class).

### 2.2 Non-Goals (Current Prototype Scope)
- Backend data persistence (no database / API integration)
- User authentication and role management
- Multi-student batch reporting
- Real AI API integration (AI summary is currently template-generated)

---

## 3. Target Users

| Role | Profile | Primary Use Case |
|------|---------|-----------------|
| **Teacher** | Coding instructor at Timedoor Academy | Fills out and submits student progress after each session |
| **Admin** | Academy coordinator | Reviews and archives submitted daily reports |

---

## 4. Data Model & Configuration

Both the desktop and mobile versions share the same **CONFIG** JavaScript object as their data source. This serves as the single source of truth for all pre-populated fields.

### 4.1 CONFIG Structure

```js
CONFIG = {
  student: { name, id, class, initial },
  teacher:  { name, department, profile (URL) },
  session:  { date, time, course },
  academy:  { name, fullName },
  lessonsCovered:    [ { name, unit, score, checked, objectives[] } ],
  availableLessons:  [ { name, unit, objectives[] } ],
  project:  { enabled, name },
  mediaEvidence:     [ { url, caption } ],
  previewImages:     [ { url, caption, size } ]
}
```

The CONFIG is the intended integration point — in a production system, this object would be populated via an API call (e.g., from a Student Management System or LMS).

---

## 5. Feature Specifications

### 5.1 Desktop View (`index.html`)

The desktop layout uses a **3-column grid** with a sticky left sidebar and a main scrollable content area.

#### 5.1.1 Header
- **App title:** "New Daily Report" with subtitle "Timedoor Academy"
- **Draft Mode badge** — visual indicator that the report is unsaved
- **Back button** (navigates to previous screen — TBD in full integration)
- **User avatar icon** (placeholder; to be linked to teacher profile)

#### 5.1.2 Left Sidebar (Sticky)

**Student Info Card**
- Avatar circle with student initial
- Student full name, Student ID
- Class name (e.g., "27A - Python")
- Teacher name

**Session Details Card**
- Date picker (`<input type="date">`) — pre-filled from CONFIG
- Time picker (`<input type="time">`) — pre-filled from CONFIG
- Course name (read-only text field) — pre-filled from CONFIG

**Available Lessons Panel**
- Lists all lessons from `CONFIG.availableLessons` that have not yet been added to "Lessons Covered"
- Each row shows lesson name, unit name, and an **Add (+) button**
- Clicking Add moves the lesson to the "Lessons Covered" section in the main area
- Successfully added lessons are hidden from this list

#### 5.1.3 Main Content Area (Right — 2/3 width)

**Lessons Covered & Performance Card**
- Header: "Lessons Covered & Performance" with a "Reset Default" button
- Each lesson row (`lesson-row`) contains:
  - Checkbox (checked = lesson is included in report)
  - Lesson name (bold) and unit subtitle
  - Numeric score input field (0–100, no spinners)
  - Trash/delete icon button (removes lesson from covered list, returns it to available)
- Row background highlights green when checkbox is checked
- **Generated Objectives section** below the lessons: auto-populates with bullet points from all checked lessons' `objectives` arrays

**Active Project Card**
- Toggle switch to enable/disable an active project
- Text input for project name (disabled/greyed until toggled on)
- Example placeholder: "e.g. Bridge Building"

**Media Evidence Card**
- Displays up to 5 media thumbnails (Max 5 rule)
- Each thumbnail has a hover overlay with a remove (×) button
- Camera icon button to add more photos

**Area for Improvements**
- Free-text `<textarea>` for teacher observations (4 rows)
- Placeholder text: "Student showed great initiative during..."

**Final Notes (Generated Report)**
- `<textarea>` (5 rows, italic styled)
- **Generate Report button** — constructs a structured summary text from:
  - All checked lesson names + scores
  - Active project (if enabled)
  - Teacher observations text
- Visual ring animation on the textarea on generation

#### 5.1.4 Sticky Footer Action Bar
- **Save Draft** button — saves current state (UI placeholder in prototype)
- **Preview** button — opens the Preview Modal
- **Submit Report** button (primary CTA, green, with paper-plane icon)

#### 5.1.5 Preview Modal

Triggered by the "Preview" button. Full-screen overlay with backdrop blur.

**Modal Header:**
- Title: "Report Preview"
- **Design selector dropdown** — switch between:
  - `Classic Layout` (Design 1)
  - `Modern Layout` (Design 2)
- Close button (×)

**Modal Content — Design 1 (Classic Layout):**
- Dark green header with Academy name + decorative icon
- 2-column photo grid (featured images from CONFIG)
- Student info strip (Name, ID, Date & Time, Course)
- Main body:
  - Learning Objectives list
  - Project Name section (conditional — only if project is active)
  - Lesson Performance table (Lesson Module | Score)
  - Teacher Notes section (yellow blockquote style)
- Footer: Report ID, teacher name + avatar

**Modal Content — Design 2 (Modern Layout):**
- Gradient green header with Academy name, report date, student info
- Featured image (large, adaptive to portrait/landscape)
- Learning Objectives card (green gradient background, checkmark icons)
- Project Focus card (amber gradient, conditional)
- Performance Summary table (gradient green header, pill-style scores)
- Teacher Observations card (amber accent)
- Footer: Academy branding, teacher name + avatar

**Modal Footer:**
- Close button
- **Print PDF** button — uses `window.print()` on the active design template

---

### 5.2 Mobile View (`mobile-index.html`)

The mobile version uses a **3-step wizard** pattern optimized for small touchscreens.

#### 5.2.1 Header (Sticky)
- App title: "Daily Journal" with "Draft" badge
- Student avatar (initial), full name, Student ID, class name
- Session date and time displayed inline

#### 5.2.2 Step Progress Bar (Sticky, below header)
- 3-step indicator showing: **Lessons → Details → Review**
- Active step = filled green circle
- Completed step = green circle with checkmark icon
- Upcoming step = grey circle with number
- Progress lines between steps fill green as steps are completed

---

#### Step 1 — Lessons & Photo

**Section header:** "Covered Lessons" with sub-description.

**Add Button (top-right):** Opens the **Select Lessons Modal** (full-screen).

**Selected Lessons List:**
- Empty state: dashed card with book icon + "No Lessons Selected / Tap Add to get started"
- Each selected lesson card shows:
  - Lesson name + remove (×) button
  - **Quick-score buttons:** 60 / 75 / 85 / 100
  - Manual score number input (large, 2-digit display)
  - `/100` label

**Photo Upload Area:**
- Dashed border area — tap to trigger file picker
- Accepts image files via `<input type="file" accept="image/*">`
- Preview: shows uploaded image with a "Remove Photo" button
- Placeholder: camera icon + "Add Evidence Photo" + "Choose Photo" button

**Lesson Objectives Panel:**
- Green gradient card showing aggregated objectives from all selected lessons
- Auto-updates when lessons are added/removed

**Fixed Bottom CTA:** "Continue →" button (full width, gradient green)

---

#### Step 1 — Select Lessons Modal (Full-Screen Overlay)

- Green header: "Covered Lessons" + close (×) button
- **Search input** with magnifier icon — filters lessons by name in real-time
- **Selected count badge** — shows "X lessons selected"
- Scrollable list of all lessons:
  - Each card shows a checkbox (green when selected), lesson name, and objective count
  - Tapping a card toggles selection
- **Footer:** Cancel (reverts to pre-modal state) + Confirm Selection buttons

---

#### Step 2 — Details

**Section header:** "Add Details — Project and observations"

**Active Project Toggle:**
- Label + native checkbox toggle
- Project name input (disabled until toggled)
- Placeholder: "e.g., Snake Game Development"

**Notes Textarea:**
- Large free-text field (6 rows)
- Placeholder: "Write notes about student's performance..."

**Generate AI Summary Button:**
- Full-width, gradient purple, with robot icon
- On click: simulates a 2-second loading state ("Generating..." with pulse animation)
- Generates a templated summary paragraph based on:
  - Student name
  - Number of selected lessons + average score
  - Top-performing lesson name + score
  - Active project (if any)
  - Teacher notes (if any)
- On success: button text changes to "Summary Generated!" with checkmark, reverts after 3 seconds

**AI Summary Card:**
- Displays the generated text
- Status label: "Not generated" → "Generating..." → "Generated ✓"

**Fixed Bottom Navigation:** Back ← and Continue → buttons (split 50/50)

---

#### Step 3 — Review & Submit

**Section header:** "Review Report — Check before submitting"

**Preview Card:**
- Green gradient card header with Student name + date
- Conditional photo section (only visible if photo was uploaded)
- **Learning Objectives** list
- **Active Project** section (conditional)
- **Lesson Performance** list: each lesson shows name + score
- **AI Summary** section (conditional — only if generated)
- **Teacher Observations** section (conditional — only if notes entered)
- Footer strip: Teacher name + Report number

**Action Buttons:**
- "Save as Draft" — shows "Saved!" confirmation for 2 seconds
- "Submit Report" — triggers `confirm()` dialog → on confirm, disables button and shows "Submitted!" state

**Fixed Bottom:** "← Back to Edit" button

---

## 6. User Flows

### 6.1 Desktop Flow
```
Open Page → Pre-filled sidebar data visible →
  [Optional] Add/remove lessons from "Available Lessons" →
  [Optional] Click lessons to check/uncheck →
  Enter scores for checked lessons →
  [Optional] Enable Active Project + enter name →
  [Optional] Add media evidence photos →
  [Optional] Write teacher observations →
  Click "Generate Report" → Review Final Notes text →
  Click "Preview" → Set design (Classic/Modern) → Review →
  [Optional] Print PDF →
  Click "Submit Report"
```

### 6.2 Mobile Flow
```
Open Page → Header shows student + date info →
  [Step 1] Tap "Add" → Select lessons in modal → Confirm →
  Score each lesson →
  Upload evidence photo (optional) →
  Tap "Continue" →
  [Step 2] Toggle Active Project (optional) →
  Write teacher notes (optional) →
  Generate AI Summary →
  Tap "Continue" →
  [Step 3] Review full report preview →
  Tap "Save Draft" or "Submit Report"
```

---

## 7. UI / Design System

### 7.1 Color Palette

| Token | Hex | Usage |
|-------|-----|-------|
| `primary` | `#059669` | Primary actions, checked states, active elements |
| `primaryDark` | `#047857` | Hover states, header backgrounds |
| Emerald-50 | `#ecfdf5` | Card backgrounds on selected state |
| Amber-50 | `#fffbeb` | Project section backgrounds |
| Purple-500 | `#8b5cf6` | AI Generate button (mobile) |

### 7.2 Typography
- Font stack: system sans-serif (via TailwindCSS default)
- Font Awesome 6.0.0 for all iconography (CDN)

### 7.3 Component Patterns
- **Cards:** `bg-white rounded-2xl shadow-sm border border-gray-100 p-6`
- **Inputs:** `bg-gray-50 border border-gray-200 rounded-lg/xl px-3 py-2 focus:ring-2 focus:ring-primary`
- **Primary Button:** `bg-primary text-white rounded-xl shadow-lg hover:bg-primaryDark`
- **Lesson Row (desktop):** Highlights green (`bg-emerald-50`, `border-primary`) when checked
- **Score Pill (mobile modal design 2):** `bg-emerald-100 text-emerald-700 rounded-full`

### 7.4 Responsive Layout
- Desktop uses **CSS Grid** (`lg:grid-cols-3`), sidebar sticky
- Mobile uses **full-width stacked layout** with step-by-step navigation
- A4 print format is enforced via inline styles (`width: 210mm; max-height: 297mm`) on the preview templates

---

## 8. Interaction Behaviors

| Interaction | Behavior |
|-------------|----------|
| Add Lesson (desktop) | Lesson card animate-in (fade + slide from top), item hidden from Available list |
| Remove Lesson (desktop) | Animate-out (fade + slide left), item reappears in Available list |
| Check/Uncheck Lesson | Row background transitions to green/white |
| Project Toggle ON | Input field enables, opacity transitions to full |
| Project Toggle OFF | Input clears, disables, fades out |
| Generate Report (desktop) | Auto-fills Final Notes textarea with green ring flash |
| Preview button | Modal overlays page with `backdrop-blur-sm`, body scroll locked |
| Design Selector (modal) | Switches active design template, all others hidden |
| Print PDF | `window.print()` called on report HTML; restores body after |
| Mobile Step Navigation | Active step content shown, others hidden via CSS class toggle |
| Mobile AI Generate | 2s fake loading state with pulse animation, then summary renders |
| Mobile Submit | `confirm()` dialog; on OK, button disables and changes to "Submitted!" |
| Mobile lesson search | Real-time filter on lesson name in full-screen modal |

---

## 9. Report Templates (Output Formats)

### 9.1 Classic Layout (Design 1)
- **Style:** Corporate, minimal, tabular
- Header: Solid dark green with Academy name
- Photo: 2-column equal-width grid
- Content: Left-aligned text, standard HTML table for scores
- Notes: Yellow left-bordered blockquote
- Footer: Small grey bar with Report ID + teacher avatar

### 9.2 Modern Layout (Design 2)
- **Style:** Contemporary, card-based, colorful
- Header: Gradient green with floating info chips
- Photo: Single large featured image (adaptive orientation)
- Objectives: Green gradient card with checkmark icons
- Project: Amber card (conditional)
- Performance: Gradient header table, rounded pill score badges
- Notes: Amber left-bordered card
- Footer: Gradient bar with teacher avatar

---

## 10. Technical Specifications

| Attribute | Value |
|-----------|-------|
| **Technology** | Vanilla HTML5 + JavaScript (ES6+) |
| **CSS Framework** | TailwindCSS v3 (CDN `cdn.tailwindcss.com`) |
| **Icon Library** | Font Awesome 6.0.0 (CDN) |
| **Rendering** | Client-side only, no build step needed |
| **File Size** | `index.html` ~66 KB, `mobile-index.html` ~47 KB |
| **State Management** | In-memory JavaScript object (`CONFIG`) + DOM state |
| **Data Persistence** | None (prototype; no localStorage or API) |
| **Print** | `window.print()` with DOM swap technique |
| **Photo Upload** | FileReader API → base64 Data URL |
| **AI Summary** | Template-based string generation (no external AI API) |
| **Browser Support** | Modern browsers (Chrome, Firefox, Safari, Edge) |

---

## 11. Known Limitations & Future Improvements

### 11.1 Current Limitations (Prototype)
- **No data persistence** — refreshing the page resets all form data
- **CONFIG is hardcoded** — must be manually edited in HTML; no admin interface
- **AI summary is template-based** — uses simple string interpolation, not a real LLM
- **Print uses DOM swap** — may cause brief flash; could be replaced with a dedicated print stylesheet
- **No validation** — user can submit an empty or incomplete form without warnings
- **Media evidence is cosmetic** — desktop upload area shows a sample image but file input is not fully wired
- **Mobile and desktop are disconnected** — no shared state between the two files

### 11.2 Recommended Future Features (Roadmap)

| Priority | Feature | Description |
|----------|---------|-------------|
| High | **Backend Integration** | API to fetch student/session data into CONFIG and POST submitted reports |
| High | **Data Validation** | Require at least one lesson before allowing submission |
| High | **Real AI Integration** | Connect to OpenAI / Gemini API for intelligent summary generation |
| High | **Responsive Unification** | Merge desktop + mobile into a single responsive file |
| Medium | **LocalStorage Draft** | Auto-save form state to prevent data loss on refresh |
| Medium | **Signature Field** | Digital signature for teacher on the PDF report |
| Medium | **Multiple Report Themes** | Additional PDF design templates |
| Medium | **Report History** | View list of previously submitted reports |
| Low | **Dark Mode** | Dark theme for the form editor |
| Low | **Multi-Language** | Support for Bahasa Indonesia (student data uses Indonesian locale) |

---

## 12. Open Questions

1. **Authentication:** How will teachers log in? Will CONFIG data be pre-loaded server-side or fetched client-side?
2. **Submit destination:** Where does the submitted report go — email, LMS database, Notion, Google Sheets?
3. **Media Evidence:** Should photos be uploaded to a storage service (e.g., S3, Firebase Storage)?
4. **Report ID Format:** Should `#RPT-YYYY-MM-DD-<studentID>` be generated server-side?
5. **Multi-lesson session:** Is there a maximum number of lessons that can be reported per session?
6. **Score validation:** Should scores be optional or mandatory for checked lessons?
7. **Mobile Photo:** Should the mobile view support multiple photos (currently only 1)?

---

*End of PRD v1.0*
