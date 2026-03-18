# HR Recruitment Screener Agent — Concept Note

## Overview
An AI agent that automates the recruitment process — screens CVs/resumes, ranks candidates, conducts initial screening conversations, schedules interviews, and manages the hiring pipeline. Works via WhatsApp (for candidates) and web dashboard (for HR/hiring managers).

## Problem
Hiring in East Africa is painful. Companies receive hundreds of applications for a single position. HR teams spend weeks manually reviewing CVs. Good candidates are missed because screening is inconsistent. Small businesses without HR departments struggle even more — the owner reads every CV between running the business.

## Solution
A configurable recruitment agent that:
- Accepts job descriptions and creates screening criteria automatically
- Receives and parses CVs (PDF, Word, images) from email or WhatsApp
- Scores and ranks candidates against job requirements
- Conducts automated initial screening conversations via WhatsApp/Telegram
- Schedules interviews with qualified candidates
- Manages candidate pipeline (applied → screened → interviewed → offered)
- Generates shortlists with summaries
- Anti-bias features (optional blind screening)

## Architecture
```
┌─────────────────────────────────────────────┐
│         HR Recruitment Screener Agent         │
├─────────────────────────────────────────────┤
│  Candidate Channels  │  Core Engine          │
│  ├─ WhatsApp         │  ├─ LLM Provider      │
│  ├─ Telegram         │  ├─ CV Parser (OCR)    │
│  ├─ Email            │  ├─ Scoring Engine     │
│  └─ Web Portal       │  ├─ Screening Q&A     │
│                      │  ├─ Interview Scheduler│
│  HR Channels         │  └─ Pipeline Manager  │
│  ├─ Web Dashboard    │                       │
│  ├─ WhatsApp         │  Integrations         │
│  └─ Email Reports    │  ├─ Google Calendar    │
│                      │  └─ Email (SMTP)       │
└─────────────────────────────────────────────┘
```

## Tech Stack
- **Runtime:** Node.js
- **LLM:** Any provider (CV analysis, screening conversations, scoring)
- **CV Parsing:** pdf-parse + Tesseract.js (OCR for image CVs)
- **Channels:** Baileys (WhatsApp), Telegraf (Telegram), Express (Web)
- **Database:** SQLite (candidates, jobs, pipeline, scores)
- **Calendar:** Google Calendar integration
- **Email:** Nodemailer (receive applications, send notifications)

## Configuration
```bash
git clone https://github.com/AiStudioUg/hr-recruitment-screener.git
cd hr-recruitment-screener
cp .env.example .env
nano config/settings.yaml
npm install && npm start
```

### config/settings.yaml
```yaml
company:
  name: "TechServe Solutions Ltd"
  industry: "technology"
  location: "Kampala, Uganda"

screening:
  auto_parse_cv: true
  scoring_criteria:
    education: 20        # weight percentage
    experience: 30
    skills_match: 35
    certifications: 15
  passing_score: 60      # minimum score to proceed
  max_shortlist: 10      # max candidates per shortlist

pipeline:
  stages:
    - "Applied"
    - "CV Screened"
    - "Phone Screen"
    - "Interview"
    - "Assessment"
    - "Offer"
    - "Hired"
    - "Rejected"

interviews:
  calendar_integration: "google"  # google | outlook | manual
  default_duration: 30  # minutes
  buffer_between: 15    # minutes between interviews
  available_hours:
    start: "09:00"
    end: "17:00"

communication:
  candidate_language: "en"  # en | sw | lg
  auto_acknowledgment: true
  rejection_after_days: 14  # auto-reject if no action

anti_bias:
  blind_screening: false    # hide name, gender, age, photo
  standardized_scoring: true

llm:
  provider: "openai"
  model: "gpt-4o"
  api_key: "${OPENAI_API_KEY}"
```

## Workflow

### 1. Post a Job
```
HR: New job: Senior Backend Developer. 3+ years experience, 
    Node.js, Python, PostgreSQL. BSc Computer Science preferred.
    Salary 3-5M UGX. Remote ok.

Agent: ✅ Job posted: Senior Backend Developer
       
       Auto-generated screening criteria:
       • Education: BSc CS or related (preferred)
       • Experience: 3+ years backend development
       • Required skills: Node.js, Python, PostgreSQL
       • Nice-to-have: Docker, AWS, CI/CD
       • Location: Remote (Uganda-based preferred)
       
       Screening questions (for WhatsApp interview):
       1. Years of backend experience?
       2. Primary programming languages?
       3. Database experience?
       4. Available start date?
       5. Salary expectations?
       
       Share this application link: https://apply.techserve.ug/sbd-001
       Or forward CVs to: jobs@techserve.ug
       
       Shall I adjust anything?
```

### 2. Candidate Applies (WhatsApp)
```
Candidate: Hi, I'd like to apply for the developer position
Agent: Welcome! I'm the hiring assistant at TechServe Solutions. 👋
       
       Please send your CV (PDF, Word, or photo) and I'll 
       review it for the Senior Backend Developer role.

Candidate: [sends CV.pdf]
Agent: Thank you! I've received your CV. Let me review it...
       
       ✅ CV parsed successfully. I have a few quick questions:
       
       1. How many years of backend development experience 
          do you have?

Candidate: 4 years
Agent: 2. What programming languages do you primarily use?

Candidate: Node.js and Python, also some Java
Agent: 3. What databases have you worked with?

Candidate: PostgreSQL, MongoDB, and MySQL
Agent: 4. When would you be available to start?

Candidate: 2 weeks notice
Agent: 5. What are your salary expectations (monthly, UGX)?

Candidate: Around 4 million
Agent: Thank you, James! 🎉
       
       Your application has been submitted for the Senior 
       Backend Developer position at TechServe Solutions.
       
       We'll review your profile and get back to you within 
       5 business days. If shortlisted, we'll schedule an 
       interview.
       
       Good luck! 🤞
```

### 3. HR Reviews (Dashboard/WhatsApp)
```
Agent: 📊 Recruitment Update — Senior Backend Developer
       
       Applications: 45
       Screened: 38
       Qualified (score 60+): 12
       
       🏆 Top 5 Candidates:
       1. James Okello — Score: 92/100
          4 yrs exp, Node.js/Python/PostgreSQL ✅✅✅
       2. Sarah Nambi — Score: 87/100
          5 yrs exp, Python/PostgreSQL, MSc CS ✅✅
       3. Peter Kato — Score: 85/100
          3 yrs exp, Node.js/Python/MongoDB ✅✅
       4. Grace Atim — Score: 78/100
          3 yrs exp, Node.js/PostgreSQL, Docker ✅
       5. David Ssali — Score: 75/100
          4 yrs exp, Python/Java/PostgreSQL ✅
       
       Schedule interviews with top candidates?
```

## Browser Extension / Web Dashboard
- Job posting form
- Candidate pipeline (kanban board)
- CV viewer with AI-highlighted relevant sections
- Score breakdown per candidate
- Interview calendar
- Team collaboration (multiple reviewers)
- Export shortlists to PDF/Excel

## File Structure
```
hr-recruitment-screener/
├── README.md
├── LICENSE
├── .env.example
├── package.json
├── config/
│   ├── settings.yaml.example
│   └── templates/
│       ├── job-post.hbs
│       ├── acknowledgment.hbs
│       ├── interview-invite.hbs
│       └── rejection.hbs
├── src/
│   ├── index.js
│   ├── agent/
│   │   ├── core.js
│   │   ├── screening.js       # Screening conversation
│   │   └── notifications.js   # Candidate/HR notifications
│   ├── cv/
│   │   ├── parser.js          # PDF/Word/Image parsing
│   │   ├── ocr.js             # Tesseract OCR for image CVs
│   │   └── scorer.js          # AI-powered scoring
│   ├── pipeline/
│   │   ├── manager.js         # Pipeline stage management
│   │   ├── shortlist.js       # Shortlist generation
│   │   └── scheduler.js       # Interview scheduling
│   ├── channels/
│   │   ├── whatsapp.js        # Candidate channel
│   │   ├── telegram.js
│   │   ├── email.js           # Receive CVs via email
│   │   └── web.js             # HR dashboard
│   ├── db/
│   │   └── sqlite.js
│   └── utils/
├── dashboard/
│   ├── index.html
│   ├── pipeline.js            # Kanban board
│   └── analytics.js
├── docs/
│   ├── setup.md
│   ├── posting-jobs.md
│   ├── screening-flow.md
│   ├── scoring.md
│   ├── anti-bias.md
│   └── interview-scheduling.md
└── tests/
```

## Success Metrics
- 80% reduction in time spent screening CVs
- Consistent scoring across all candidates
- Candidate response rate > 70% (WhatsApp screening)
- Time-to-shortlist < 48 hours (vs 2+ weeks manual)
- Setup time < 30 minutes per job posting

## Target Users
- Small/medium companies hiring frequently
- HR departments overwhelmed with applications
- Recruitment agencies
- Startups without dedicated HR
- NGOs and international organizations hiring locally
