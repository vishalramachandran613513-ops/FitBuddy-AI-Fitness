# 💪 FitBuddy – AI Fitness Plan Generator using Gemini Models

FitBuddy is an AI-powered fitness and nutrition planning web application built with **FastAPI**, **Google Gemini Models** (`gemini-1.5-pro` & `gemini-1.5-flash`), **SQLAlchemy + SQLite**, and **Jinja2 templating**.

FitBuddy generates structured 7-day personalized workout plans, delivers complementary goal-specific nutrition and recovery tips, adapts routines dynamically based on user feedback, and provides an administrative overview dashboard.

---

## 📋 Table of Contents
1. [Project Overview](#project-overview)
2. [Technical Architecture](#technical-architecture)
3. [Project Workflow & Epics](#project-workflow--epics)
4. [Folder Structure](#folder-structure)
5. [Prerequisites](#prerequisites)
6. [Installation & Setup](#installation--setup)
7. [Environment Variables](#environment-variables)
8. [Running the Application](#running-the-application)
9. [API & Web Endpoints Reference](#api--web-endpoints-reference)
10. [Running Automated Tests](#running-automated-tests)
11. [Docker Deployment](#docker-deployment)
12. [Troubleshooting & FAQs](#troubleshooting--faqs)

---

## 🎯 Project Overview

FitBuddy addresses key fitness challenges by eliminating guesswork in training and diet:
- **Tailored 7-Day Workout Routines:** Generated according to the user's specific fitness goal (e.g., weight loss, muscle gain, flexibility) and workout intensity (Low, Medium, High).
- **Practical Nutrition & Recovery Tips:** Concise, actionable dietary advice generated via lightweight Gemini Flash to optimize recovery and fuel progress.
- **Dynamic Feedback Loop:** Users can submit feedback (e.g., "Add more cardio", "Include rest days", "Swap squats with lunges") to iteratively revise their routine.
- **Coach / Admin Dashboard:** Centralized view of all registered users, their parameters, and side-by-side comparison of original vs. updated workout plans.

---

## 🏛️ Technical Architecture

```
User (Browser) / API Client
          │
          ▼
 FastAPI Backend (app/main.py & app/routes.py)
 ┌──────────────────────┬───────────────────────────┬────────────────────────┐
 │                      │                           │                        │
 ▼                      ▼                           ▼                        ▼
Jinja2 Web Views    REST API Endpoints      Google Gemini AI Layer     SQLite Database
• index.html        • /generate-plan        • gemini-1.5-pro (Workouts) • users table
• result.html       • /generate-workout/..  • gemini-1.5-flash (Tips)   • plans table
• all_users.html    • /nutrition-tip        • updated_plan (Revisions)
```

- **Backend Framework:** FastAPI (Python 3.10+)
- **Server:** Uvicorn ASGI
- **AI / LLM Engine:** Google Gemini API (`google-generativeai`)
  - **Gemini 1.5 Pro:** Day-by-day structured workout routines and feedback-based plan revisions.
  - **Gemini 1.5 Flash:** Fast, context-tailored nutrition and recovery tips.
- **Database & ORM:** SQLite (`fitbuddy.db`) with SQLAlchemy ORM.
- **Templating & UI:** Jinja2 with HTML5, CSS3, Flexbox, and Google Fonts (Roboto).
- **Validation:** Pydantic v2 schemas.

---

## 🚀 Project Workflow & Epics

The project follows the standard SmartBridge milestone workflow:

| Epic | Milestone | Description |
| :--- | :--- | :--- |
| **Epic 1** | Model Selection & Architecture | Evaluation of generative models and defining FastAPI + Gemini modular architecture. |
| **Epic 2** | Core Functionalities Development | Core logic for workout generation, nutrition tips, feedback updating, and DB persistence. |
| **Epic 3** | App.py / routes.py Development | Route handlers for form processing, REST API endpoints, and template rendering. |
| **Epic 4** | Frontend Development | UI design using responsive CSS and Jinja2 templates (`index.html`, `result.html`, `all_users.html`). |
| **Epic 5** | Deployment | Local Uvicorn server setup, verification, Docker containerization, and configuration. |

---

## 📂 Folder Structure

```
fitbuddy/
├── .env.example              # Environment variables template
├── .gitignore                # Git ignore rules
├── .dockerignore             # Docker build ignore rules
├── Dockerfile                # Production containerization
├── README.md                 # Complete project documentation
├── requirements.txt          # Python dependencies
├── app.py                    # Top-level runner and application alias
├── app/
│   ├── __init__.py           # Application package initializer
│   ├── main.py               # FastAPI application setup, static mounts, lifespan
│   ├── routes.py             # Web template routes and REST API endpoints
│   ├── database.py           # SQLAlchemy engine, models (User, WorkoutPlan), CRUD helpers
│   ├── schemas.py            # Pydantic schemas (UserInput, WorkoutRequest, FeedbackRequest)
│   ├── gemini_generator.py   # Gemini 1.5 Pro workout plan generation logic
│   ├── gemini_flash_generator.py # Gemini 1.5 Flash nutrition tip generation logic
│   ├── updated_plan.py       # Gemini 1.5 Pro feedback-based plan revision logic
│   ├── nutrition.py          # Nutrition utilities & macronutrient calculator
│   ├── templates/            # Jinja2 HTML templates
│   │   ├── index.html        # Main input form page
│   │   ├── result.html       # Workout plan, nutrition tip, feedback form
│   │   └── all_users.html    # Admin oversight table
│   └── static/
│       ├── css/
│       │   └── style.css     # Responsive CSS stylesheet
│       └── images/
│           └── gym-bg.jpg    # Gym background image
└── tests/
    ├── __init__.py
    ├── test_database.py      # Unit tests for SQLAlchemy models and database operations
    ├── test_routes.py        # Integration tests for web routes and REST API endpoints
    └── test_ai_generators.py # Unit tests for AI prompt logic and nutrition utilities
```

---

## ⚙️ Prerequisites

1. **Python:** Version 3.10, 3.11, or 3.12 installed.
2. **Google Gemini API Key:** (Free tier available at [Google AI Studio](https://aistudio.google.com/app/apikey)).
3. **Git:** For version control.

---

## 🛠️ Installation & Setup (VS Code)

### 1. Clone or Open the Project
Open VS Code and navigate to the project directory:
```bash
cd d:\FitBuddy
```

### 2. Create and Activate a Virtual Environment
```bash
# Windows (Command Prompt or PowerShell)
python -m venv fitbuddy-env
fitbuddy-env\Scripts\activate

# macOS / Linux
python3 -m venv fitbuddy-env
source fitbuddy-env/bin/activate
```

### 3. Install Required Dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables
Copy `.env.example` to `.env`:
```bash
copy .env.example .env     # Windows
cp .env.example .env       # macOS / Linux
```
Open `.env` and set your Google Gemini API Key:
```env
GOOGLE_API_KEY=AIzaSy...your_gemini_api_key_here
GEMINI_PRO_MODEL=gemini-1.5-pro
GEMINI_FLASH_MODEL=gemini-1.5-flash
DATABASE_URL=sqlite:///./fitbuddy.db
HOST=127.0.0.1
PORT=8000
```
*(Note: If `GOOGLE_API_KEY` is not provided, the application activates an intelligent built-in fallback engine so that all forms and endpoints continue to function for testing).*

---

## 🚀 Running the Application

Start the Uvicorn development server:

```bash
# Method 1 (Direct Uvicorn command)
uvicorn app.main:app --reload --host 127.0.0.1 --port 8000

# Method 2 (Using top-level app.py)
python app.py
```

Open your browser:
- **Web Application:** [http://127.0.0.1:8000](http://127.0.0.1:8000)
- **Admin Dashboard:** [http://127.0.0.1:8000/view-all-users](http://127.0.0.1:8000/view-all-users)
- **Interactive Swagger API Docs:** [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)
- **Alternative ReDoc Docs:** [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc)

---

## 📡 API & Web Endpoints Reference

### Web Routes (Jinja2 HTML Views)
| Method | Endpoint | Description |
| :--- | :--- | :--- |
| `GET` | `/` | Homepage input form (`index.html`) |
| `POST` | `/generate-workout` | Processes user form, saves user & plan, renders `result.html` |
| `POST` | `/submit-feedback` | Submits feedback, updates plan with Gemini, renders `result.html` |
| `GET` | `/view-all-users` | Admin panel listing all users and plans (`all_users.html`) |
| `POST` | `/delete-user/{user_id}`| Admin action to delete a user and their workout plans |

### REST API Endpoints (JSON)
| Method | Endpoint | Description | Payload / Params |
| :--- | :--- | :--- | :--- |
| `POST` | `/generate-workout/gemini` | Generates workout plan via Gemini Pro | `{"goal": "weight loss", "intensity": "high"}` |
| `GET` | `/nutrition-tip` | Returns nutrition/recovery tip via Gemini Flash | Query: `?goal=muscle+gain` |
| `POST` | `/generate-plan` | Registers user and creates stored plan | `{"user_id": 1, "username": "John", "age": 25, "weight": 75, "goal": "strength", "intensity": "high"}` |
| `POST` | `/update-plan/{user_id}` | Updates existing plan with feedback | `{"feedback": "Add yoga and more rest"}` |
| `GET` | `/api/users` | Lists all users and plans in JSON format | None |
| `GET` | `/health` | Application health check endpoint | None |

---

## 🧪 Running Automated Tests

Run the full test suite with `pytest`:
```bash
pytest -v
```

All 13 tests verify:
- Database CRUD (`User` and `WorkoutPlan` creation, updates, and cascading deletion)
- Web form submissions (`/generate-workout` and `/submit-feedback`)
- Admin dashboard rendering (`/view-all-users`)
- REST API contracts and status codes
- AI generator prompts and fallback behavior
- Nutrition and macronutrient calculations

---

## 🐳 Docker Deployment

Build and run the container locally:

```bash
# Build the Docker image
docker build -t fitbuddy-app .

# Run the container
docker run -d -p 8000:8000 --env-file .env --name fitbuddy-container fitbuddy-app

# Check logs
docker logs -f fitbuddy-container
```
Navigate to `http://localhost:8000` to access the running app.

---

## ❓ Troubleshooting & FAQs

1. **`TemplateResponse` TypeError: unhashable type: 'dict'**
   - In Starlette 1.0.1+ and FastAPI 0.133+, `TemplateResponse` requires keyword arguments: `templates.TemplateResponse(request=request, name="index.html", context={...})`. This is fully handled in `app/routes.py`.

2. **Can I use the app without a Gemini API Key?**
   - Yes! FitBuddy includes an intelligent offline fallback engine for both workout plans and nutrition tips. You can test all UI forms, plan generations, feedback revisions, and admin operations immediately without providing an API key.

3. **Where is SQLite data stored?**
   - Database tables are automatically initialized on startup in `fitbuddy.db` located at the root of the project.
