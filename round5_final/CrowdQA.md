# Final Project Proposal: CrowdQA — TA Recitation Confusion Tracker

**Team Name**: CrowdQA  
**Submission Date**: November 13, 2025  
**GitHub Organization**: https://github.com/nets2130-crowdqa-team8
---

## Team Information

### Team Members

| Name | PennKey | Primary Role(s) | Secondary Skills |
|------|---------|----------------|------------------|
| Vedha Avali | vavali | Backend Developer, API Design| Node.js, PostgreSQL |
| Nathan Lee | nzlee | Frontend Developer, UX Design | React, Figma, Data Visualization |
| Leha Choppara | lehac | Dashboard & Visualization | HTML/CSS, Chart.js, React |
| Manasi Gajjalapurna |       | Integration & Testing | Deployment, Copywriting |
___

### Team Skills Inventory

**Skills we have:**
- **Frontend (React, HTML/CSS)**: Nathan, Vedha, Leha  
- **Backend (Node.js, Express)**: Vedha, Nathan  
- **Data Visualization (Chart.js, Recharts)**: Leha, Manasi  
- **Deployment (Render/Vercel)**: Manasi 

**Skills we need to learn/acquire:**
- **Basic Data Aggregation Analytics** – summarize confusion trends – *Leha + Nathan*  

**External resources we might need:**
- AWS for data storage - est. cost <$1 per recitation

### Team Availability for TA Meetings

**Week of [Date]:**

_List all time slots when the ENTIRE team can meet with a TA. Use Eastern Time. Format: Day, Time-Time_

- Monday: [e.g., 2:00 PM - 4:00 PM, 6:00 PM - 8:00 PM]
- Tuesday: [e.g., 10:00 AM - 12:00 PM]
- Wednesday: [e.g., 3:00 PM - 5:00 PM]
- Thursday: [Not available]
- Friday: [e.g., 1:00 PM - 3:00 PM]

**Preferred meeting duration**: [30 min / 45 min / 60 min]

**Meeting format preference**: [In-person / Zoom / Either]

**Primary contact for scheduling**: [Name and email]

https://www.when2meet.com/?33539239-Fh9qr
---

## Project Overview

### Project Connection to Round 4

**Round 4 Decision**: STAYING

**Original idea from**: Professor Lumbroso

**How the idea evolved**: 
The original CrowdQA concept targeted full lectures with live confusion tracking. We’ve decided to narrow our initial scope by focusing on a **simpler TA recitation-focused MVP**. The recitation tool focuses on timestamped confusion logging and post-session summaries that TAs can use to identify sections needing review. In addition to an “I’m confused” button, students have the option of typing in key words to indicate what they are confused on.


### Problem Statement

_Refined from your Round 4 decision_

We have decided to narrow our initial scope as TAs in large introductory CS courses often have limited feedback on which parts of their recitations are confusing to students. CrowdQA allows students to anonymously click when they’re lost, producing a timestamped log of confusion peaks that helps TAs identify and improve unclear sections in future sessions.



### One-Sentence Pitch

*A one-click web tool for TAs/instructors to visualize which moments in their lectures/recitations confused students the most.*


### Target Users

**End Users:** Teaching Assistants and instructors in large lecture or recitation settings will benefit from the data, and students will benefit from providing the data.

**Crowd Workers**: Students attending those sessions who provide real-time “confusion” feedback.  

**Scale**: 10–20 students per recitation; 3–4 TA sessions for MVP testing.

### Project Type

- [ ] Human computation algorithm
- [ ] Social science experiment with the crowd
- [x] Tool for crowdsourcing (requesters or workers)
- [ ] Business idea using crowdsourcing
- [ ] Other: [specify]

---

## System Architecture



| Component | Description | Points | Owner(s) | Dependencies |
|-----------|-------------|--------|----------|--------------|
| Student Interface | Simple click UI for recording confusion events | 2 | Vedha | Session API ready |
| Session Management | Create/join session endpoints | 4 | Nathan | Database schema |
| Dashboard Visualization | Chart of confusion over time + summary stats | 4 | Leha | API endpoints |
| Post-Recitation Summary | Peak detection + auto summary | 3 | Manasi | Dashboard data ready |


**Total Points**: [Sum - should be 15-20]

**Point allocation rationale**: 
_If teaching staff questions your point distribution, explain your reasoning here_

### Detailed Workflow

_Step-by-step description of how your system works from start to finish_

1. **[Step 1]**: [What happens, who/what does it]
2. **[Step 2]**: [What happens, who/what does it]
3. **[Step 3]**: [What happens, who/what does it]
4. **[Step 4]**: [What happens, who/what does it]
5. **[Step 5]**: [What happens, who/what does it]
6. **[Step 6]**: [What happens, who/what does it]
7. **[Step 7]**: [What happens, who/what does it]

_Continue as needed..._

### Human vs. Automated Tasks

| Task | Performed By | Justification |
|------|---------------|---------------|
| Clicking “I’m Confused” | Human | Students best judge their confusion |
| Aggregating timestamps | Automated | Simple arithmetic, easily handled by backend |
| Summarizing peaks | Automated | Basic math thresholding |
| Reviewing results | Human | TAs interpret context and teaching improvements |

---

## Quality Control Module

### QC Strategy Overview

Since confusion signals are anonymous and may contain noise (spam clicks), QC will focus on **rate limiting and outlier filtering** rather than worker evaluation.  

We’ll ensure each user’s clicks are spaced reasonably apart and that massive sudden spikes (bot-like) are filtered out before visualization.


### Specific QC Mechanisms

**Primary:** Rate-limiting + outlier detection  
**Implementation details**:
- Input format: Receives clicks from students.
- Processing: Checks rates between clicks from the same user in a specific session 
- Output format: Returns a filtered dataset containing only validated clicks.
- Threshold for acceptance: Discard clicks that occur less than 3 seconds after a previous click

**Additional mechanisms**:
- [ ] Gold standard questions
  - _How many? How distributed? Pass/fail criteria?_
- [ ] Majority voting across multiple workers
  - _How many workers per task? Tie-breaking?_
- [ ] Attention checks
  - _What types? How often?_
- [ ] Reputation system
  - _How do workers build reputation?_
- [ ] Statistical outlier detection
  - _What metrics? How handled?_
- [ ] Other: [specify and explain]

### QC Module Code Plan

**Location:** `backend/src/qc/filter.js`  

**Key functions/classes**:
1. `filterRapidClicks(sessionId)` – removes clicks spaced <3s apart.  

**Input data format**: 
```json
[{ "timestamp": 12.4, "session_id": "C123", "user_id": "anon1" }]
```

**Output data format**:
```
[{ "timestamp": 12.4, "valid": true }]
```

**Sample scenario**:

A student clicks 5 times within 2 seconds, and 4 clicks discarded; only one kept. Aggregated chart reflects valid collective confusion.

---

## Aggregation Module

### Aggregation Strategy Overview

After the QC module filters invalid or spam clicks, the Aggregation Module processes the validated data to generate interpretable summaries for TAs.  
It groups student "I'm Confused" clicks into **1–5 minute bins** depending on lecture length, counts the number of unique users per bin, and highlights confusion peaks — moments when many students clicked within the same time window.
This approach transforms raw timestamp data into a concise visual representation of class-wide confusion over time.  
—

### Aggregation Method

**Primary method:** Interval-based click aggregation with peak detection  

**Implementation details**:
- **Input Format:**  
  The module receives filtered, validated click data (output from the QC module):  
  ```
  [
    { "session_id": "C123", "timestamp": 62.4, "valid": true, "user_id": "anon1" },
    { "session_id": "C123", "timestamp": 73.7, "valid": true, "user_id": "anon2" },
    { "session_id": "C123", "timestamp": 312.2, "valid": true, "user_id": "anon3" }
  ]

- Processing: [What does it do?]
1. Group by session: Aggregate all valid clicks belonging to the same session_id.
2. Bin timestamps: divide each session timeline into 1-5-minute bins based on user configuration
3. Count unique users per bin: Ensures that repeated clicks by the same user don’t inflate counts.
4. Calculate statistics: Compute mean and standard deviation of click counts across bins, Define “peak” as any bin where count >= mean + 1.2σ.
5. Normalize data: Convert raw counts into percentage of participants who clicked in that interval for scalability across class sizes.
6. Store output: Save aggregated data and peak markers to the database for quick dashboard retrieval.
- Output format: 
[
  { "interval": "0–5 min", "count": 4, "percent": 16, "isPeak": false },
  { "interval": "5–10 min", "count": 15, "percent": 60, "isPeak": true },
  { "interval": "10–15 min", "count": 6, "percent": 24, "isPeak": false }
]
- Handling edge cases: Low participation: If fewer than 5 total valid clicks exist, the system marks the session as “insufficient data” to avoid misleading results; Single-user peaks: Bins with clicks from only one unique user are ignored in peak detection

**Why this method**:
- Interpretability: Instructors can easily relate time intervals to specific parts of their recitation.
- Simplicity: uses basic descriptive statistics (no complex models) ensuring real-time computation.
- Robustness: Binning smooths noise and isolates meaningful confusion spikes.
- Scalability: Works equally well for 10 students or 200; normalized percentages allow fair comparison.



### Aggregation Module Code Plan

**Location in repo**: backend/src/aggregation/aggregate.js

**Key functions/classes**:
1. aggregateClicks(sessionId) – Groups validated clicks into bins and returns counts per interval
2. computePeaks(aggregatedData) – Calculates mean/σ, marks high-density bins as peaks
3. normalizeCounts(aggregatedData, totalUsers) – Converts counts to percentage of class participation
4. storeAggregationResults(sessionId, results) – Saves results to DB for dashboard access

**Input data format**:
```
[
  { "session_id": "C123", "timestamp": 85.5, "user_id": "anon2", "valid": true },
  { "session_id": "C123", "timestamp": 112.2, "user_id": "anon1", "valid": true },
  { "session_id": "C123", "timestamp": 304.0, "user_id": "anon3", "valid": true }
]

```

**Output data format**:
```
[
  { "interval": "0–5 min", "count": 3, "percent": 12, "isPeak": false },
  { "interval": "5–10 min", "count": 8, "percent": 32, "isPeak": true },
  { "interval": "10–15 min", "count": 5, "percent": 20, "isPeak": false }
]
```

**Sample scenario**:
_Walk through a concrete example of your aggregation module in action_

60 minute recitation session, 20 students participating, 5-minute bins → 12 total bins. Assume that QC removes invalid clicks and afterwards, 180 valid clicks remain.  The aggregation result says that the average # of clicks for a bin is 4, with a standard deviation of 2. The three bins of 15-20, 30-35, and 50-55 minutes exceed the threshold and are marked as confusion peaks.

### Integration: QC ↔ Aggregation

**How do these modules interact?**
[Describe the relationship. Does QC happen before aggregation? After? Iteratively? Do they share data structures?]

**Data flow diagram** (if different from main flow diagram):
[Location or description]

---

## User Interface & Mockups

### Interfaces Required

_You need mockups for ALL user-facing interfaces_

**For Crowd Workers:**
- [x] Task interface / click page (StudentView)
- [x] Instructions page (JoinSession intro)
- [ ] Training/qualification interface (if applicable)

**For End Users:**
- [x] Main dashboard (TA Dashboard)
- [x] Results display (TA Summary Report)
- [x] Configuration/input screen (Session creation)

**For Administrators (your team):**
- [ ] Dashboard/monitoring
- [ ] Data management interface

### Mockup Details

**Mockup location:** `docs/mockups/` (Final Figma prototype link pending)

**For each interface, describe**:

#### Interface 1: StudentView
- **User type:** Crowd worker (student)
- **Purpose:** Allows students to anonymously indicate confusion during a 1-hour recitation session.
- **Key elements:**
  - Prominent "I'm Confused" button (centered, large, single click)
  - Text feedback box (optional short note, e.g., "Didn’t understand example 2")
  - Real-time feedback count (“You’ve clicked 2 times”)
  - Session code displayed in header
- **Mockup file:** `docs/mockups/student_view.png`
- **Notes:** 
  - Minimal distraction design — single primary action.  
  - Large hit area for mobile usability.  
  - Button click triggers a POST `/click` event with session ID and timestamp.  


#### Interface 2: JoinSession
- **User type:** Crowd worker (student)
- **Purpose:** Entry page to join an active session using a 4-digit code announced by the TA.
- **Key elements:**
  - Input field: “Enter 4-digit session code”
  - “Join” button to access the main confusion interface
  - Error message if code invalid or expired
  - Display of current recitation name once joined
- **Mockup file:** `docs/mockups/join_session.png`
- **Notes:**
  - Ensures only valid sessions receive confusion data.
  - Session codes generated dynamically by TA dashboard.
  - May include a QR code for quick join in later versions.

---
#### Interface 3: TA Dashboard (Live Monitoring)
- **User type:** End user (TA)
- **Purpose:** Displays aggregated confusion events throughout the 60-minute recitation.
- **Key elements:**
  - Line chart (Y-axis: # of clicks, X-axis: time in minutes)
  - Highlighted red bins for confusion peaks (≥ mean + 1.2σ)
  - Numeric summary of participation (total clicks, unique students)
  - Manual “Refresh” button for MVP (no WebSocket dependency)
  - Optional toggle to show student notes under peaks
- **Mockup file:** `docs/mockups/ta_dashboard_live.png`
- **Notes:**
  - Built with Recharts or Chart.js for real-time visual updates.
  - Aggregation done in 5-minute bins for a 1-hour recitation.
  - Designed for clarity under low-light classroom conditions (dark mode option).

---

#### Interface 4: TA Summary Report
- **User type:** End user (TA)
- **Purpose:** Post-session view summarizing where students were most confused.
- **Key elements:**
  - Table of 5-minute intervals (e.g., “10–15 min: 28% confused”)
  - Red-highlighted rows for peak confusion bins
  - Simple histogram or bar chart visualization
  - Button: “Download Summary (CSV)” (optional stretch)
- **Mockup file:** `docs/mockups/ta_summary_report.png`
- **Notes:**
  - Supports retrospective analysis for teaching improvement.
  - Optional integration with Canvas or Google Sheets for recordkeeping.
  - Allows exporting confusion data to CSV for further analysis.

---

#### Interface 5: SessionCreator (optional)
- **User type:** End user (TA)
- **Purpose:** Allows TA to create a new recitation session before class.
- **Key elements:**
  - Form fields: Course Name, Recitation Date, Duration, TA Name
  - Button: “Generate Session Code”
  - Output: Display of session code and shareable join link
- **Mockup file:** `docs/mockups/session_creator.png`
- **Notes:**
  - Automatically stores metadata in the database.
  - Used by TAs before recitation begins; not visible to students.

---



**Crowd participants:**  
Students in university recitation sections act as crowd workers, providing live feedback on comprehension.

**Task description:**  
During the 60-minute recitation, students click the **“I’m Confused”** button whenever they feel uncertain about a topic.  
Each click is timestamped and sent anonymously to the backend for aggregation and visualization.

**Task instructions (displayed on JoinSession page):**
**Example task flow:**
1. TA creates a new session → announces 4-digit session code.
2. Students visit the app → enter code → see “I’m Confused” button.
3. During minute 18 (example problem explanation), 10 students click the button.
4. TA dashboard shows a spike in confusion for the 15–20 min bin.
5. After class, TA reviews the summary to identify challenging concepts.

**Example task**:
[Show workers exactly what one complete task looks like]

**Estimated time per task:** Continuous participation over 60 minutes (each click takes <2 seconds).  

**Payment per task:** None — intrinsic motivation (students help improve their own learning experience).  

**Number of tasks per session:** One session (20–40 student participants, 100–300 total clicks).  

**Qualifications required:** Must be enrolled in the recitation or have access to the shared session code.  

---

## Technical Stack

### Technologies

**Frontend**: React (Vite + TailwindCSS)  
Used to build a responsive, minimal web interface for both students (crowd workers) and TAs (end users).  Recharts or Chart.js will handle visualizations in the TA dashboard.  

**Backend**: Node.js with Express.js  
Handles API routes for session management, confusion click logging, and data aggregation.  
Implements REST endpoints such as:
- `POST /click` → log confusion event
- `GET /summary/:session_id` → retrieve aggregated data
- `POST /session` → create a new session

**Database**: PostgreSQL  
Stores session metadata, click timestamps, and aggregation summaries.  
Schema includes:
- `sessions` table (id, code, instructor, start_time, duration)
- `clicks` table (session_id, user_id, timestamp, valid)
- `summaries` table (session_id, interval_start, count, is_peak)

**Crowdsourcing Platform**: Custom class deployment  
Students in recitation sections act as the “crowd.”  
Recruitment handled internally via session codes announced by TAs.

**ML/AI Tools** (if applicable): None for MVP

**Hosting/Deployment**: AWS

**Other tools**:  
- **GitHub**: version control and team collaboration  
- **Postman**: for API testing  
- **Figma**: for mockup design  

### Repository Structure

**Current structure**:
```
your-repo/
├── README.md
├── docs/
│   ├── flow-diagram.pdf
│   ├── mockups/
│   └── ...
├── src/
│   ├── qc/
│   ├── aggregation/
│   └── ...
├── data/
│   ├── raw/
│   ├── sample-qc-input/
│   ├── sample-qc-output/
│   ├── sample-agg-input/
│   └── sample-agg-output/
└── ...
```

**Explain any deviations**: [If your structure differs, explain why]

---

## Data Management

### Input Data

**Source**: Live clickstream data from students in CIS 1200 and similar recitation sections using the CrowdQA web app.

**Format**: Stored in PostgreSQL tables as rows. Each click is a record in the clicks table with fields: 
id (primary key)
session_id
user_id (pseudonymous anon identifier, not a PennKey)
timestamp (seconds since session start)
created_at (server time)
valid (boolean, set by QC module)

**Sample data location**: `data/raw/` 

**Sample data description**:
We have not trialled the app and collected data yet, but this is an example of a set of data points we would like to have.

data/raw/session_C123.json: Clickstream for a 30 minute recitation with about 15 students and bursts of confusion around specific minutes.

**How much data do you need?**
- For testing/development: 3 minimum, ideally more.
- For your final demo/analysis: 4-6. We will run the app in our own recitations, but it is key that we have datapoints where the app is run independently of us.

**Data collection plan**:
We will run the app during our own recitations, and ask TAs and professors to run it during their recitation/lecture as well.

### QC Module Data

**Input location**: `data/sample-qc-input/`

**Input format**:

[
  { "session_id": "C123", "user_id": "u1", "timestamp": 12.4, "created_at": "2025-02-18T15:02:04Z" },
  { "session_id": "C123", "user_id": "u2", "timestamp": 18.9, "created_at": "2025-02-18T15:02:10Z" }
]

**Output location**: `data/sample-qc-output/`

**Output format**:

The QC module output adds a valid flag for each click after applying the 3 second rate limiting rule per user per session.

[
  { "session_id": "C123", "user_id": "u1", "timestamp": 10.0, "valid": true },
  { "session_id": "C123", "user_id": "u1", "timestamp": 11.5, "valid": false },
  { "session_id": "C123", "user_id": "u2", "timestamp": 12.2, "valid": true },
  { "session_id": "C123", "user_id": "u1", "timestamp": 13.0, "valid": false }
]

**Sample scenario documentation**:
A README.md in data/sample-qc-input/ and data/sample-qc-output/ will explain:
The scenario being simulated (for example a 10 minute mini recitation with one spammy user).
The QC rules that were applied (for example discard clicks that occur less than 3 seconds after a previous click from the same user in the same session).
How to run the QC module on the sample input and where to expect the output.

### Aggregation Module Data

**Input location**: `data/sample-agg-input/`

**Input format**: 

[
  { "session_id": "C123", "user_id": "u1", "timestamp": 62.4, "valid": true },
  { "session_id": "C123", "user_id": "u2", "timestamp": 73.7, "valid": true },
  { "session_id": "C123", "user_id": "u1", "timestamp": 75.1, "valid": false },
  { "session_id": "C123", "user_id": "u3", "timestamp": 312.2, "valid": true }
]

**Output location**: `data/sample-agg-output/`

**Output format**:
Aggregated statistics per time interval, including counts, percentages, and peak flags.

[
  { "interval_start": 0, "interval_end": 300, "count": 4, "percent": 20, "isPeak": false },
  { "interval_start": 300, "interval_end": 600, "count": 12, "percent": 60, "isPeak": true },
  { "interval_start": 600, "interval_end": 900, "count": 4, "percent": 20, "isPeak": false }
]

**Sample scenario documentation**:
A README.md in data/sample-agg-input/ and data/sample-agg-output/ will:
Describe the simulated recitation length and number of students.
Explain how many clicks occur in each interval and why certain bins are considered peaks (for example bins where count is at least mean plus 1.2 standard deviations).
Show how to run aggregateClicks and computePeaks on the sample QC output to reproduce the aggregated results.

### Data Dependencies

**Does your QC module output feed into your aggregation module?**
Yes. The aggregation module only operates on clicks that have already passed through the QC module. The QC output is the direct input to aggregation.

Specifically: The QC module produces a list of click events with valid flags. The aggregation module filters to valid = true events, groups them into time bins, counts unique users per bin, and computes peaks and percentages.

**Data flow between modules**:
Raw collection -> QC stage -> Aggregation stage -> Dashboard consumption.

Raw collection
The frontend sends each “I am confused” click to the backend via POST /click.
The backend stores raw click rows in the clicks table.
QC stage
The QC module (for example filterRapidClicks(sessionId)) reads raw clicks for a given session.
It applies rate limiting and marks each click as valid or invalid.
Results are written back either by updating the valid column in the clicks table or by writing a filtered view into a separate QC output structure.
In the repository, this corresponds to raw JSON files in data/sample-qc-input/ and cleaned JSON files in data/sample-qc-output/.
Aggregation stage
The aggregation module (aggregateClicks, computePeaks, normalizeCounts) reads only valid = true clicks for a session.
It groups timestamps into 1 to 5 minute bins, counts unique users, computes mean and standard deviation of bin counts, flags peaks, and converts counts to percentages.
Aggregated results are written to the summaries table in the database for fast retrieval by the TA dashboard.
In the repository, this corresponds to input files in data/sample-agg-input/ (which mirror QC output) and aggregated results in data/sample-agg-output/.
Dashboard consumption
The TA dashboard calls GET /summary/:session_id.

The backend returns the aggregated data structure (intervals, counts, percentages, peak flags) that the frontend turns into charts and tables.

---

## Crowd Recruitment & Management

### Recruitment Strategy

**Where will workers come from?**
- Workers will primarily come from class volunteers, specifically targeting students in relevant classes such as computer science, data science, and business. We will also use social media (e.g., Penn’s student groups on Facebook, Instagram, and LinkedIn) to recruit students.

**How will you reach them?**
- We will reach students via class announcements, direct emails, and social media outreach. Additionally, we will use platforms like Slack channels and university-specific discussion boards to engage students in the project.

**When will you recruit?**
- Recruitment will begin in Week 1 (early February) to give enough time for students to sign up and become familiar with the system before we start testing in Week 2. Recruitment will continue through Week 2 to fill any gaps and maintain engagement throughout the MVP phase.

### Worker Incentives

**Compensation model**:  
- Payment per task: $0.50 per "I’m Confused" click submission  
- Estimated time per task: 2 minutes per session  
- Effective hourly rate: $15/hour  

**Or alternative incentive**:  
- In addition to monetary compensation, students will receive course credit where applicable or extra credit for participating in the pilot test.

**Justification**:  
- The payment per task provides an immediate, small incentive that is simple and clear for students. It’s a small but effective amount that encourages students to participate without overwhelming the budget, while still recognizing their time. Course credit also motivates students who need or prefer academic incentives.

### Scale Requirements

**For MVP/Demo**:  
- Minimum workers needed: 50 students (to ensure a wide range of confusion clicks and test interactions)  
- Minimum tasks completed: 500 confusion clicks across all users  
- Timeline: By the end of Week 2 (February 18th)

**For Full Analysis**:  
- Target workers: 200 students (to get more comprehensive data and ensure variety in feedback)  
- Target tasks: 2,000 confusion clicks across all users  
- Timeline: By the end of Week 4 (February 25th)

### Backup Plan

**If recruitment fails or is insufficient**:  
- [ ] Use MTurk/paid workers (budget: $100)  
- [ X ] Simplify task to require fewer workers (reduce the target number of clicks required per student)  
- [ ] Use simulated/synthetic data (create mock confusion click data for testing the dashboard functionality)  
- [ ] Other: Use early adopters (students who express interest in participating in testing) for initial feedback

---

## Project Milestones & Timeline

### Week-by-Week Plan

**Week 1 (Dates: February 5 - February 11)**  
- Milestone: Finalize recruitment, finalize tech stack, and confirm project scope  
- Tasks:  
  - [ ] Post recruitment announcements in relevant Slack channels and class forums - [Vedha]  
  - [ ] Set up cloud infrastructure for the backend - [Nathan]  
  - [ ] Design and finalize mockups for the student and instructor interfaces - [Leha]  
- Deliverable: Recruitment completed, mockups ready, cloud infrastructure set up

**Week 2 (Dates: February 12 - February 18)**  
- Milestone: Build and test the MVP, focusing on core functionalities (confusion button, session code entry)  
- Tasks:  
  - [ ] Implement confusion button and session code functionality - [Vedha]  
  - [ ] Set up backend API and database for storing confusion click data - [Nathan]  
  - [ ] Conduct first round of testing and gather initial feedback - [Leha]  
- Deliverable: MVP ready for first testing with live data, basic session management functionality working

**Week 3 (Dates: February 19 - February 25)**  
- Milestone: Polish the MVP, integrate the instructor dashboard and data aggregation  
- Tasks:  
  - [ ] Implement instructor dashboard with data aggregation and visualization - [Manasi]  
  - [ ] Complete student interface testing and finalize confusion data submission - [Vedha]  
  - [ ] Prepare post-class summary email system - [Leha]  
- Deliverable: MVP tested with instructor feedback, confusion data aggregation functional, system polished for full pilot testing

**Week 4 (Dates: February 26 - March 4)**  
- Milestone: Final pilot testing, system optimization, and project wrap-up  
- Tasks:  
  - [ ] Conduct final pilot test with all features integrated (live session with confusion data) - [Vedha]  
  - [ ] Optimize system performance and debug any issues - [Nathan]  
  - [ ] Prepare final project presentation and documentation - [Manasi]  
- Deliverable: Final tested and fully integrated MVP, complete documentation

### Critical Path

**Blocking dependencies** (what MUST be done before other work can proceed):  
1. [Tech Stack Selection] must be done before [Backend Setup]  
2. [Student Interface Design] must be completed before [User Testing]  

**Parallel work** (what can be done simultaneously):  
- [Vedha] can work on [Student Interface Design] while [Nathan] works on [Backend API Setup]  
- [Leha] can work on [Instructor Dashboard] while [Manasi] works on [Post-Class Summary Email System]

**Integration points** (when pieces must come together):  
- [February 18th]: All core features (confusion button, backend API, session management) must be integrated for testing  
- [February 25th]: Instructor dashboard and data aggregation features must be integrated for final testing before pilot sessions

## Risk Management

### Technical Risks

**Risk 1**: Data aggregation inaccuracies due to incorrect timestamp alignment between confusion clicks and session data  
- **Likelihood**: Medium  
- **Impact**: High  
- **Mitigation**: Implement robust timestamp handling and testing, ensure synchronization between student confusion timestamps and the aggregated data model.  
- **Backup plan**: If timestamp issues persist, implement manual data correction tools for instructors to adjust the data and improve the aggregation logic.

**Risk 2**: Backend API or database issues causing delays in storing or retrieving confusion data  
- **Likelihood**: Medium  
- **Impact**: Medium  
- **Mitigation**: Optimize database schema and API performance for quick data handling, set up error handling and retries.  
- **Backup plan**: If issues persist, switch to a simpler database (e.g., SQLite for quicker testing) and troubleshoot performance bottlenecks, or use batch processing for data aggregation.

### Crowd-Related Risks

**Risk 1**: Low student engagement in using the confusion button  
- **Likelihood**: Medium  
- **Impact**: High  
- **Mitigation**: Run a "fake door" test to gauge interest before development, incentivize students with small rewards, and ensure usability with a user-friendly interface.  
- **Backup plan**: If student engagement is still low, manually recruit students for pilot sessions and emphasize user education and ease of use.

**Risk 2**: Students submitting too many "I’m Confused" clicks in quick succession (spamming)  
- **Likelihood**: Medium  
- **Impact**: Medium  
- **Mitigation**: Implement rate limiting and cooldown timers on the confusion button to prevent over-clicking.  
- **Backup plan**: If spam persists, consider adding CAPTCHA or authentication to ensure only active students can submit confusion clicks.



### Resource Risks

**Risk 1**: Budget overrun for cloud storage or email service (e.g., SendGrid for summary emails)  
- **Likelihood**: Low  
- **Impact**: Medium  
- **Mitigation**: Use free tiers or low-cost cloud storage services (e.g., Firebase, AWS S3) and email providers (e.g., SendGrid free plan).  
- **Backup plan**: If costs exceed the budget, explore additional funding options from university or reduce scope (e.g., limit email summaries to specific sessions).


---

## Evaluation Plan

### What You'll Measure


**Primary metrics**:
1. **Student Engagement**: Measured by the number of students who actively use the "I'm Confused" button per session, with a target of at least 50% of students per session.  
2. **Data Accuracy**: Measured by the correctness of timestamp alignment and aggregated confusion data, with a target accuracy of 95%.  
3. **Instructor Dashboard Usage**: Measured by the number of instructors who actively use the dashboard to analyze confusion peaks, with a target of 80% instructor usage in pilot tests.

**Secondary metrics**:
1. **System Performance**: Measured by response times for data submission and retrieval from the backend, with a target of sub-2 second latency.  
2. **Student Satisfaction**: Measured by post-session surveys, aiming for at least 70% positive feedback on the tool's usability.

### Analysis Approach

**What questions will your analysis answer?**
1. How effective is the "I'm Confused" button in identifying moments of confusion for students?
2. How accurately do instructors identify confusion peaks using the dashboard?
3. What improvements can be made to the user experience to increase engagement?

**What comparisons will you make?**
- [ ] Compare crowd vs. expert performance
- [ ] Compare crowd vs. automated baseline
- [ ] Compare different QC methods
- [ ] Compare different aggregation methods
- [ ] Analyze cost/quality tradeoffs
- [X ] Other: We will compare data against other recitation sections of the same course, data against different courses (ie. “advanced” vs. “intermediate” courses), data of same recitation sections on different dates/lessons.

**Data you'll collect for analysis**:
- **Confusion Click Data**: To analyze student engagement and confusion moment identification.  
- **Instructor Dashboard Usage Data**: To evaluate the usefulness and usage rate of the dashboard.  
- **Survey Responses**: To assess student and instructor satisfaction.


**Analysis methods**:
- Statistical analysis (e.g., t-tests, correlation analysis) to compare engagement and satisfaction between sessions.
- Visualizations (e.g., line charts, bar charts) to show confusion peaks and student interactions over time.

---

## Ethical Considerations

### Worker Treatment

**Fair compensation**: Since this is a volunteer-based academic project, no financial compensation is provided. However, students will benefit from the ability to influence class sessions and participate in improving teaching practices.

**Informed consent**: Students will be informed that their clicks are recorded and aggregated anonymously for academic purposes.

**Rejection policy**: Work will not be rejected unless it involves inappropriate behavior (e.g., spamming or deliberate misuse of the system). There will be safeguards (rate-limiting) to prevent such behavior.

### Data Ethics

**Privacy**: Student data will be anonymized, and no personally identifiable information will be collected or stored. Confusion data will only include timestamp and session information.

**Consent**: Students will be asked for consent through a prompt when they first enter the session, ensuring they are aware of the data collection process.

**Data storage**: Data will be stored securely in a cloud database (e.g., PostgreSQL) with access restricted to authorized project members.

### Potential Harms

**Could your project be misused?**: The system could be misused if students flood the session with unnecessary confusion clicks. However, this risk is mitigated by rate limiting and other spam prevention mechanisms.

**Could it cause harm?**: There is minimal risk of harm, but if the system fails to provide useful data, instructors may be misled about areas of confusion. Mitigation involves quality control and manual review processes.

**Mitigation**: Ensuring that confusion data is aggregated correctly and that the tool provides actionable insights for instructors will reduce the chance of harm.

---

## Documentation Standards

### Code Documentation

**Each module must include**:
- Docstrings for all functions/classes
- README in module directory
- Example usage
- Input/output format specifications

**Current documentation status**:
- [ X ] QC module: [Fully documented]
- [ X ] Aggregation module: [Fully documented
- [ ] Other modules: [List status]

### Repository README

**Your main README.md must include**:
- [x] Project overview and goals  
- [x] Setup instructions  
- [x] How to run the system  
- [x] Where to find QC and aggregation code  
- [x] Data format specifications  
- [x] Team member contacts  
- [x] License information

### Ongoing Documentation

**How will you keep documentation current?**
Documentation will be updated each week during team meetings as new features are implemented and code evolves. A version-controlled approach will be used to track changes, and a shared documentation file will be maintained.

---

## Questions for Teaching Staff

### Technical Questions

### Technical Questions

1. Are there any best practices for ensuring accurate timestamp synchronization between student clicks and session data?
2. What platforms/tools would you recommend for implementing rate limiting and spam prevention in a web app?
3. How can we ensure the scalability of the system as more students and instructors use the tool?

### Scope Questions

1. Is implementing the email summary feature necessary for the MVP, or can it be considered a stretch goal?
2. Given the current timeline, is it realistic to complete the MVP with a fully functional instructor dashboard and confusion tracking?
3. Is it acceptable to limit the number of students per session for the MVP, or should we aim for full-scale sessions?

### Resource Questions

1. Can we get access to a university server or cloud hosting credits to minimize costs for deployment and data storage?
2. Do you have any recommendations for recruitment platforms to encourage students to use the "I'm Confused" feature in pilot sessions?

### Other Concerns

Are there any ethical concerns we might be missing regarding data privacy, given the student participation?

---

## Commitment

**We commit to**:
- [ X ] Building a working prototype with functional QC and aggregation modules
- [ X ] Creating comprehensive documentation in our GitHub repository
- [ X ] Recruiting and managing a real crowd (or simulated crowd)
- [ X ] Collecting sufficient data for meaningful analysis
- [ X ] Meeting project milestones on schedule
- [ X ] Communicating proactively if we encounter blockers
- [ X ] Treating crowd workers ethically and fairly

**Team signatures**:

- Vedha Avali, 11/13/2025
- Nathan Lee, 11/13/2025
- Leha Choppara, 11/13/2025
- Manasi Gajjalapurna, 11/13/2025

---

## Submission Checklist

This submission **is a working document**. You may not have finalized all version (of the flow diagram, the sample data, etc.), which is **acceptable**.

Before submitting this proposal, verify you have:

- [ X ] Completed all sections of this template
- [ X ] Provided team availability for TA meetings
- [ X ] Listed team skills and learning needs
- [ X ] Included point values for all components (total 15-20)
- [ X ] Described detailed implementation timeline
- [ X ] Identified risks and mitigation strategies
- [ X ] Had all team members review and sign

Then:

- [ X ] Set up GitHub repository with required directory structure
- [ X ] Prepared questions for teaching staff
- [ X ] Created flow diagram showing QC and aggregation modules
- [ X ] Created mockups for all user-facing interfaces
- [ X ] Added sample input/output data for QC module
- [ X ] Added sample input/output data for aggregation module

**Submission method**:
- **You are able to make multiple successive submission to iterate, complete this proposal.**
- Pull request to `ideation-fall-2025` repository, in `round5_final` folder
- Should be in the root of your GitHub organization

**Submission deadline**: Thursday, Nov. 13 at 11:59PM ET
