# Product Requirements Document (PRD)
## EduFlow — Course Management System
### Group 12

---

| Field            | Detail                              |
|------------------|-------------------------------------|
| **Product Name** | EduFlow                             |
| **Version**      | 1.0.0                               |
| **Group**        | Group 12                            |
| **Document Type**| Product Requirements Document (PRD) |
| **Status**       | Final                               |
| **Date**         | April 2025                          |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Goals & Objectives](#3-goals--objectives)
4. [Stakeholders & Users](#4-stakeholders--users)
5. [Scope](#5-scope)
6. [Functional Requirements](#6-functional-requirements)
7. [Non-Functional Requirements](#7-non-functional-requirements)
8. [Data Models](#8-data-models)
9. [User Stories](#9-user-stories)
10. [Tech Stack](#10-tech-stack)
11. [System Architecture](#11-system-architecture)
12. [UI & UX Requirements](#12-ui--ux-requirements)
13. [Reporting & Analytics](#13-reporting--analytics)
14. [Constraints & Assumptions](#14-constraints--assumptions)
15. [Acceptance Criteria](#15-acceptance-criteria)
16. [Glossary](#16-glossary)

---

## 1. Executive Summary

EduFlow is a fully browser-based, offline-capable **Course Management System (CMS)** designed for educational institutions to manage courses, track student enrollments, and monitor learning progress — all without requiring a backend server or internet connection.

The system supports two distinct roles:

- **Students** — Discover courses, self-enroll, track lesson-by-lesson progress, and earn certificates on completion.
- **Administrators** — Perform full CRUD operations on courses, manage enrollment records, and access visual analytics and exportable reports.

All data is persisted using the browser's `localStorage` API, making EduFlow completely self-contained in a single HTML file with no external dependencies beyond Google Fonts.

---

## 2. Problem Statement

Educational institutions commonly face the following operational challenges:

- **Fragmented tools** — Course information is stored across spreadsheets, emails, and disconnected systems.
- **No unified student portal** — Students have no single interface to discover, enroll in, and track their learning progress.
- **Manual reporting** — Enrollment statistics and completion rates require significant manual effort to compile.
- **Deployment friction** — Most LMS platforms require server infrastructure, IT setup, and licensing costs.

EduFlow addresses all four challenges with a zero-setup, single-file solution that runs entirely in any modern browser.

---

## 3. Goals & Objectives

### Primary Goals

| # | Goal | Metric |
|---|------|--------|
| G1 | Enable students to self-enroll in courses | Enrollment recorded in < 1 click |
| G2 | Allow lesson-level progress tracking | Per-lesson checkboxes with localStorage persistence |
| G3 | Provide admin CRUD for courses | Create, Read, Update, Delete with form validation |
| G4 | Deliver enrollment statistics visually | Bar chart + donut chart rendered via Canvas API |
| G5 | Support data export | CSV export for enrollments; HTML report for analytics |

### Secondary Goals

- Auto-generate certificates upon 100% course completion
- Provide a student leaderboard based on lessons completed
- Support course filtering by category and keyword search
- Allow admins to toggle course status between Active and Draft

---

## 4. Stakeholders & Users

### Primary Users

#### 4.1 Student

A learner enrolled at the institution who uses EduFlow to:

- Browse the course catalog
- Enroll in courses of interest
- Complete lessons and track their own progress
- View and download certificates upon completion

**Needs:** Ease of use, progress visibility, motivation through progress bars and certificates.

#### 4.2 Administrator

A faculty member or institution staff responsible for:

- Managing the course catalog (add, edit, delete)
- Monitoring all student enrollments
- Generating and exporting reports for institutional decision-making

**Needs:** Reliable CRUD operations, accurate statistics, exportable data.

### Secondary Stakeholders

| Stakeholder      | Interest                                      |
|------------------|-----------------------------------------------|
| Institution Head | Overview of course performance & completions  |
| Faculty          | Visibility into student engagement per course |
| IT Department    | Zero-maintenance, no-server deployment        |

---

## 5. Scope

### In Scope

- Single-file HTML/CSS/JS application (no backend required)
- Student role: browse, enroll, track, certify
- Admin role: course CRUD, enrollment view, analytics, CSV/HTML export
- Local data persistence via `localStorage`
- Visual analytics: bar chart, donut chart (Canvas API)
- Role switching within the UI (Student / Admin toggle)

### Out of Scope

- User authentication and password management
- Multi-device data synchronization
- Email notifications
- Video hosting or streaming
- Payment gateway integration
- Mobile-native application
- Backend API or database integration

---

## 6. Functional Requirements

### 6.1 Student Features

#### FR-S01: Course Catalog

- The system SHALL display all courses with status `active` in a responsive card grid.
- Each course card SHALL show: thumbnail, title, category, instructor name, total duration, and lesson count.
- Students SHALL be able to filter courses by category using chip-style filter buttons.
- Students SHALL be able to search courses by title, instructor name, or description using a global search input.
- Enrolled courses SHALL display a visual "Enrolled" badge on the course card.

#### FR-S02: Course Enrollment

- A student SHALL be able to enroll in any active course in one click.
- The system SHALL prevent duplicate enrollments and display an informational toast if the student is already enrolled.
- On enrollment, the system SHALL create an `Enrollment` record with `status: 'active'` and persist it to `localStorage`.
- The sidebar navigation badge SHALL update to reflect the current count of active enrollments.

#### FR-S03: Course Detail View

- Clicking any course card SHALL navigate to a detailed course view.
- The detail view SHALL display: title, description, instructor, category, duration, lesson count, total enrolled count, and status.
- For enrolled students, a progress bar showing percentage of lessons completed SHALL be displayed.
- All lessons SHALL be listed with their type icon (video / reading / quiz), title, and duration.
- Unenrolled students SHALL see a lock indicator on lessons and a prominent "Enroll Now" button.

#### FR-S04: Lesson Completion Tracking

- For enrolled students, each lesson SHALL have a circular checkbox to mark it complete or incomplete.
- Checking a lesson SHALL create a `Progress` record in `localStorage`.
- Unchecking a lesson SHALL remove the corresponding `Progress` record.
- The progress bar and percentage SHALL update in real time after each toggle.

#### FR-S05: Course Completion & Certification

- When a student completes ALL lessons in a course, the system SHALL automatically:
  - Update the `Enrollment` record `status` to `'completed'` and set `completedAt` to the current timestamp.
  - Display a Certificate of Completion card within the course detail view showing the student name, course title, and completion date.
  - Show a success toast notification: "🏆 Course completed! Certificate earned!"

#### FR-S06: Student Dashboard

- The dashboard SHALL display four KPI stat cards:
  - Total Enrolled Courses
  - Completed Courses
  - Courses In Progress
  - Overall Progress Percentage
- A "Continue Learning" section SHALL show the 3 most recently enrolled courses with progress bars.
- A "Recommended for You" section SHALL show active courses the student has not yet enrolled in (up to 3).

#### FR-S07: My Progress View

- SHALL display all enrolled courses sorted by completion percentage (descending).
- Each row SHALL show: course thumbnail, title, category, completed lessons vs total, and percentage bar.
- Completed courses SHALL display the completion date in green.
- Four aggregate KPIs SHALL be shown: Total Enrolled, Completed, Lessons Done, Estimated Hours Learned.

---

### 6.2 Admin Features

#### FR-A01: Admin Overview Dashboard

- SHALL display four global KPI stat cards: Total Courses, Total Students, Total Enrollments, Completion Rate.
- SHALL render a bar chart of enrollments per course using the Canvas API.
- SHALL render a donut chart of enrollment distribution by category using the Canvas API.
- SHALL display a summary table of all courses sorted by enrollment count, including completion rate and status.

#### FR-A02: Course Management (CRUD)

**Create:**
- Admin SHALL be able to open a modal form to create a new course.
- Required fields: Title, Instructor, Description, Category, Status, Thumbnail icon.
- Admin SHALL be able to add one or more lessons inside the modal, each with: title, type (video/reading/quiz), duration (minutes).
- On save, the system SHALL validate all required fields and display an error toast if any are missing.
- A new `Course` record SHALL be persisted to `localStorage`.

**Read:**
- Admin SHALL see a table of all courses (active and draft) with: thumbnail, title, instructor, category, lesson count, enrollment count, status, and action buttons.

**Update:**
- Admin SHALL be able to edit any course via an Edit (✏️) button that opens the same modal pre-filled with existing data.
- Lessons within a course SHALL be editable (title, type, duration) and new lessons can be added or existing ones removed.
- Changes SHALL be persisted to `localStorage` on save.

**Delete:**
- Admin SHALL be able to delete a course via a Delete (🗑) button.
- A confirmation modal SHALL appear warning that all related enrollments and progress data will also be deleted.
- On confirmation, the `Course`, all related `Enrollment`, and all related `Progress` records SHALL be permanently removed from `localStorage`.

#### FR-A03: Enrollment Management

- Admin SHALL see a table of ALL enrollments across all students and courses.
- Each row SHALL display: student avatar/name, course thumbnail/title, enrolled date, status (active/completed/dropped), and progress percentage.
- Admin SHALL be able to export the full enrollment table as a `.csv` file.

#### FR-A04: Reports & Analytics

- SHALL display the same four KPI stat cards as the admin dashboard.
- SHALL render both the bar chart (enrollments per course) and donut chart (category distribution).
- SHALL display a "Category Performance" table with: category name, course count, total enrollments, total completions, and completion rate with a progress bar.
- SHALL display a "Student Leaderboard" table ranked by lessons completed, showing enrolled count, completed count, and lessons done.
- Admin SHALL be able to export a formatted HTML report file (`.html`) containing a summary, enrollment table, and course details.
- Admin SHALL be able to export enrollment data as a CSV file with headers: Student Name, Email, Course Title, Category, Enrolled On, Status, Completed On.

---

## 7. Non-Functional Requirements

### 7.1 Performance

| Requirement | Specification |
|-------------|---------------|
| View render time | All views SHALL render in < 500ms on any modern browser |
| Chart render time | Canvas charts SHALL render in < 200ms |
| LocalStorage read/write | Operations SHALL complete in < 50ms |

### 7.2 Usability

- The interface SHALL use a role-based sidebar navigation (Student / Admin) with no login required.
- Role switching SHALL be instantaneous and preserve context.
- All destructive actions (delete course) SHALL require a confirmation modal before execution.
- Toast notifications SHALL appear for all user actions (success, error, info) and auto-dismiss after 3 seconds.
- Empty states SHALL be shown with clear guidance when no data is available (e.g., no courses enrolled).

### 7.3 Accessibility

- All interactive elements SHALL have visible focus states.
- ARIA labels SHALL be applied to icon-only buttons.
- Colour SHALL NOT be the sole indicator of state (text labels accompany all status badges and progress indicators).
- Font sizes SHALL not drop below 11px for readability.

### 7.4 Compatibility

| Browser | Minimum Version |
|---------|-----------------|
| Google Chrome | 90+ |
| Mozilla Firefox | 88+ |
| Microsoft Edge | 90+ |
| Safari | 14+ |

### 7.5 Reliability & Data Integrity

- All `localStorage` writes SHALL happen synchronously to prevent data loss on page close.
- Deleting a course SHALL cascade to remove all associated enrollment and progress records atomically.
- Completion detection SHALL be idempotent — marking an already-completed course complete again SHALL NOT create duplicate records.

### 7.6 Portability

- The entire application SHALL be a **single `.html` file** with no external JavaScript dependencies.
- The application SHALL function completely offline once the Google Fonts stylesheet is cached.
- The file SHALL be openable by double-clicking on any operating system without any install step.

---

## 8. Data Models

All data is stored in `localStorage` as JSON strings under namespaced keys.

### 8.1 Course

```typescript
interface Course {
  id: string;             // UUID (random 8-char alphanumeric)
  title: string;          // Course title
  description: string;    // Full course description
  instructor: string;     // Instructor full name
  category: CategoryType; // One of six defined categories
  duration: number;       // Total duration in hours (computed)
  thumbnail: string;      // Emoji character used as thumbnail
  color: string;          // Hex accent colour for card theming
  lessons: Lesson[];      // Ordered array of lesson objects
  status: 'active' | 'draft'; // Only 'active' courses appear in the catalog
  createdAt: string;      // ISO 8601 timestamp
}

type CategoryType = 'Technology' | 'Business' | 'Design' | 'Science' | 'Arts' | 'Mathematics';
```

**Storage key:** `ef_courses`

### 8.2 Lesson

```typescript
interface Lesson {
  id: string;                        // UUID
  title: string;                     // Lesson title
  duration: number;                  // Duration in minutes
  type: 'video' | 'reading' | 'quiz'; // Content type
}
```

Lessons are stored as a nested array within each `Course` object.

### 8.3 User

```typescript
interface User {
  id: string;                  // UUID
  name: string;                // Full name
  email: string;               // Email address
  role: 'student' | 'admin';   // Role determines which views are accessible
  avatar: string;              // Two-letter initials for avatar display
  joinedAt: string;            // ISO 8601 timestamp
}
```

**Storage key:** `ef_users`
**Current user key:** `ef_current_user` (stores the active user's `id`)

### 8.4 Enrollment

```typescript
interface Enrollment {
  id: string;                                    // UUID
  userId: string;                                // FK → User.id
  courseId: string;                              // FK → Course.id
  enrolledAt: string;                            // ISO 8601 timestamp
  completedAt: string | null;                    // ISO 8601 timestamp or null
  status: 'active' | 'completed' | 'dropped';   // Enrollment lifecycle status
}
```

**Storage key:** `ef_enrollments`

### 8.5 Progress

```typescript
interface Progress {
  userId: string;       // FK → User.id
  courseId: string;     // FK → Course.id
  lessonId: string;     // FK → Lesson.id
  completedAt: string;  // ISO 8601 timestamp
}
```

**Storage key:** `ef_progress`

> **Note:** A `Progress` record is created when a lesson is marked complete and deleted when it is unchecked. This enables toggling.

### 8.6 Entity Relationship Summary

```
User (1) ──────────────< Enrollment (M)
Course (1) ────────────< Enrollment (M)
Course (1) ────────────< Lesson (M)
User (1) ──────────────< Progress (M)
Course (1) ─────────────< Progress (M)
Lesson (1) ─────────────< Progress (M)
```

---

## 9. User Stories

### Student Stories

| ID    | User Story | Priority |
|-------|------------|----------|
| US-01 | As a student, I want to browse the course catalog so that I can discover courses relevant to me. | Must Have |
| US-02 | As a student, I want to search and filter courses by category so that I can find specific topics quickly. | Must Have |
| US-03 | As a student, I want to enroll in a course in one click so that I can start learning immediately. | Must Have |
| US-04 | As a student, I want to see my progress on each course so that I know how far along I am. | Must Have |
| US-05 | As a student, I want to mark individual lessons as complete so that my progress is tracked accurately. | Must Have |
| US-06 | As a student, I want to receive a certificate when I complete all lessons so that I have proof of my achievement. | Should Have |
| US-07 | As a student, I want a personal dashboard showing my overall learning stats so that I stay motivated. | Should Have |
| US-08 | As a student, I want to see all my enrolled courses in one view so that I can manage my learning plan. | Must Have |

### Admin Stories

| ID    | User Story | Priority |
|-------|------------|----------|
| US-09 | As an admin, I want to create new courses with lessons so that students have content to enroll in. | Must Have |
| US-10 | As an admin, I want to edit existing courses so that I can keep content up to date. | Must Have |
| US-11 | As an admin, I want to delete courses so that outdated content is removed from the system. | Must Have |
| US-12 | As an admin, I want to see all enrollments across all students so that I can monitor engagement. | Must Have |
| US-13 | As an admin, I want visual charts of enrollment statistics so that I can understand data at a glance. | Should Have |
| US-14 | As an admin, I want to export enrollment data as CSV so that I can share it with stakeholders. | Should Have |
| US-15 | As an admin, I want to generate an HTML analytics report so that I can present findings. | Could Have |
| US-16 | As an admin, I want to set courses as Draft or Active so that unfinished content is hidden from students. | Should Have |
| US-17 | As an admin, I want to see a student leaderboard so that I can recognize top performers. | Could Have |

---

## 10. Tech Stack

### Core Technologies

| Layer | Technology | Justification |
|-------|------------|---------------|
| Markup | HTML5 | Semantic, single-file architecture; no build step required |
| Styling | CSS3 | Custom Properties (design tokens), Grid, Flexbox, Keyframe Animations |
| Logic | JavaScript ES6+ | Classes, arrow functions, destructuring, template literals |
| Type System | TypeScript-style JSDoc | Type safety documentation without a build step |
| Data Persistence | Browser `localStorage` API | Fully client-side, no backend required, persists across page refreshes |
| Charts | Canvas API (native) | Zero external dependency charting; bar chart + donut chart |
| Typography | Google Fonts | Playfair Display (headings) + DM Sans (body) + DM Mono (code/numbers) |

### Design Decisions

- **Single-file architecture:** The entire application — HTML, CSS, and JavaScript — lives in one `.html` file. This maximises portability and eliminates any deployment complexity.
- **No external JS libraries:** No jQuery, no React, no Chart.js. All UI rendering is done via template literals injected into `innerHTML`, and all charts are hand-drawn using the Canvas API.
- **localStorage over IndexedDB:** `localStorage` is synchronous, universally supported, and sufficient for the data volumes expected in a single-institution CMS. IndexedDB would be chosen for larger-scale deployments.
- **Role switching vs. authentication:** Since this is a local-only system, a simple role toggle replaces authentication. This keeps the UX frictionless while still demonstrating role-based access control patterns.

---

## 11. System Architecture

### 11.1 Application Layers

```
┌──────────────────────────────────────────────────────────────┐
│                     PRESENTATION LAYER                       │
│  HTML Templates (injected via innerHTML)                     │
│  CSS3 Styles (Custom Properties, Grid, Animations)           │
│  Canvas API Charts (Bar, Donut)                              │
└───────────────────────────┬──────────────────────────────────┘
                            │
┌───────────────────────────▼──────────────────────────────────┐
│                     APPLICATION LAYER                        │
│  State Object  – getters/setters for all entities            │
│  View Router   – maps view IDs to render functions           │
│  Event Handlers – onclick, oninput, form validation          │
│  Chart Renderer – drawBarChart(), drawDonutChart()           │
│  Export Helpers – exportEnrollmentsCSV(), exportReportHTML() │
└───────────────────────────┬──────────────────────────────────┘
                            │
┌───────────────────────────▼──────────────────────────────────┐
│                     DATA LAYER                               │
│  localStorage                                                │
│  ├── ef_courses      (Course[])                              │
│  ├── ef_users        (User[])                                │
│  ├── ef_enrollments  (Enrollment[])                          │
│  ├── ef_progress     (Progress[])                            │
│  └── ef_current_user (string — active user ID)               │
└──────────────────────────────────────────────────────────────┘
```

### 11.2 Navigation Architecture

```
Role: Student                          Role: Admin
│                                      │
├── Dashboard (/)                      ├── Overview (/admin-dashboard)
├── Course Catalog (/catalog)          ├── Manage Courses (/admin-courses)
├── My Courses (/my-courses)           ├── Enrollments (/admin-enrollments)
├── My Progress (/progress)            └── Reports (/admin-reports)
└── Course Detail (/course-detail)
```

### 11.3 Data Flow: Enrollment

```
Student clicks "Enroll Now"
        │
        ▼
enrollCourse(courseId) called
        │
        ▼
Check for existing Enrollment record in localStorage
        │
  ┌─────┴─────────────────┐
  │ Found                 │ Not Found
  ▼                       ▼
Show "Already enrolled"  Create new Enrollment {
toast                      id, userId, courseId,
                           enrolledAt, status:'active'
                         }
                           │
                           ▼
                         Save to localStorage
                           │
                           ▼
                         Update sidebar badge count
                           │
                           ▼
                         Re-render Course Detail view
```

---

## 12. UI & UX Requirements

### 12.1 Design System

| Token | Value |
|-------|-------|
| Background base | `#0d0f14` |
| Surface background | `#13161e` |
| Card background | `#161b26` |
| Primary accent | `#f5c842` (Gold) |
| Success colour | `#2dd4bf` (Emerald) |
| Danger colour | `#f43f5e` (Rose) |
| Info colour | `#a78bfa` (Violet) |
| Body font | DM Sans |
| Heading font | Playfair Display |
| Mono font | DM Mono |
| Border radius | 12px (cards), 8px (inputs/buttons) |
| Transition speed | 200ms cubic-bezier |

### 12.2 Layout

- Fixed **260px sidebar** on the left for navigation and role switching.
- Sticky **64px top bar** for page title, search, and contextual action buttons.
- Scrollable content area with **28px padding** on all sides.
- **Responsive grid** for course cards using `auto-fill` with a 280px minimum column width.

### 12.3 Interaction Patterns

- **Toast Notifications:** Success (green left border), Error (red), Info (gold). Auto-dismiss at 3 seconds.
- **Modal Dialogs:** Backdrop blur with spring-animation entrance. Dismissible by clicking the overlay or the close button.
- **Navigation:** SPA-style view switching (no page reload). Active nav item highlighted in gold.
- **Empty States:** All list/grid views show a descriptive empty state with an icon, heading, and call-to-action button when no data exists.
- **Confirmation Modals:** All irreversible actions (course deletion) require explicit confirmation in a modal before execution.

---

## 13. Reporting & Analytics

### 13.1 Visual Reports (Admin)

| Chart | Type | Data | Location |
|-------|------|------|----------|
| Enrollments per Course | Vertical Bar (Canvas) | Course name vs. enrollment count | Admin Dashboard, Admin Reports |
| Category Distribution | Donut (Canvas) | Category vs. enrollment count | Admin Dashboard, Admin Reports |

All charts are rendered using the native browser **Canvas API** with:
- HiDPI support via `devicePixelRatio` scaling
- Dynamic sizing responsive to the parent container width
- Custom colour palette per category
- Legends for the donut chart rendered in the DOM

### 13.2 Tabular Reports

| Report | Columns | Location |
|--------|---------|----------|
| Course Summary | Title, Category, Enrolled, Completed, Completion Rate, Status | Admin Dashboard |
| Category Performance | Category, Courses, Enrollments, Completions, Rate | Admin Reports |
| Student Leaderboard | Rank, Name, Enrolled, Completed, Lessons Done | Admin Reports |
| Full Enrollment Table | Student, Course, Enrolled On, Status, Progress % | Admin Enrollments |

### 13.3 Exports

| Export Type | Format | Content | Trigger |
|-------------|--------|---------|---------|
| Enrollment CSV | `.csv` | All enrollment records with student and course metadata | "Export CSV" button on Enrollments and Reports pages |
| Analytics Report | `.html` | Summary stats, course enrollment table, formatted for printing | "Export Report" button on Reports page |

CSV column headers: `Student Name, Email, Course Title, Category, Enrolled On, Status, Completed On`

---

## 14. Constraints & Assumptions

### Constraints

| ID | Constraint |
|----|-----------|
| C1 | No backend server — all data must be stored client-side in `localStorage` |
| C2 | Single HTML file — no separate CSS, JS, or TypeScript files |
| C3 | No external JavaScript libraries (no React, Vue, jQuery, Chart.js, etc.) |
| C4 | No user authentication — role access is determined by the role toggle |
| C5 | `localStorage` has a ~5MB limit per origin; large datasets may require pruning |

### Assumptions

| ID | Assumption |
|----|-----------|
| A1 | The system will be used by a single institution with a small-to-medium student population |
| A2 | Users have access to a modern browser (Chrome 90+, Firefox 88+, Edge 90+, Safari 14+) |
| A3 | Seed data will be generated on first load if `localStorage` is empty |
| A4 | The distinction between Student and Admin roles is handled via the UI toggle without passwords |
| A5 | Google Fonts will be loaded once and cached by the browser for offline use thereafter |

---

## 15. Acceptance Criteria

### Student Role

| AC ID | Criteria | Pass Condition |
|-------|----------|----------------|
| AC-01 | Catalog displays only active courses | Draft courses are hidden from the student catalog view |
| AC-02 | Search filters course cards in real time | Typing in the search box immediately updates displayed cards |
| AC-03 | Enrollment creates a persistent record | After enrolling, refresh the page — enrollment is retained |
| AC-04 | Lesson toggle updates progress bar | Checking a lesson increments the progress bar percentage immediately |
| AC-05 | 100% completion triggers certificate | Completing the final lesson shows a certificate card and success toast |
| AC-06 | Dashboard stats are accurate | KPI numbers match the actual count of enrollment and progress records |

### Admin Role

| AC ID | Criteria | Pass Condition |
|-------|----------|----------------|
| AC-07 | Create course saves to catalog | New course appears in both admin course table and student catalog |
| AC-08 | Edit course updates existing record | Changes to title/instructor/lessons are reflected immediately after save |
| AC-09 | Delete course cascades | After deletion, related enrollments and progress records are also removed |
| AC-10 | Charts render correctly | Bar and donut charts display accurate enrollment data |
| AC-11 | CSV export downloads valid file | Exported CSV opens correctly in spreadsheet software |
| AC-12 | Draft course hidden from students | Course with status "Draft" does not appear in the student catalog |

---

## 16. Glossary

| Term | Definition |
|------|-----------|
| **CMS** | Course Management System — software for creating, delivering, and tracking educational content |
| **Enrollment** | The act of a student registering for a course, creating an Enrollment record |
| **Progress** | A record indicating that a student has completed a specific lesson within a course |
| **Completion** | The state of an Enrollment where all lessons have been marked complete |
| **Certificate** | A visual confirmation displayed within the app when a student completes a course |
| **Seed Data** | Pre-populated sample data automatically inserted into `localStorage` on first application load |
| **localStorage** | A browser API that stores key-value string data locally on the user's device, persistent across page refreshes |
| **Canvas API** | A native browser API for drawing 2D graphics programmatically |
| **LMS** | Learning Management System — a broader category of software of which EduFlow is a simplified example |
| **DXA** | Document Cross-Reference Absolute — unit used in DOCX format (not applicable here but included for completeness) |
| **UUID** | Universally Unique Identifier — a randomly generated string used as a record's primary key |
| **CRUD** | Create, Read, Update, Delete — the four basic operations for persistent data management |
| **KPI** | Key Performance Indicator — a measurable value used to evaluate success |
| **Toast** | A brief, non-blocking notification message that appears and auto-dismisses |
| **SPA** | Single Page Application — an app that dynamically updates content without full page reloads |

---

*End of Document*

---
**Group 12 | EduFlow Course Management System | PRD v1.0.0 | April 2025**
