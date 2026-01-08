# Continuous 2FA Backend (with Real Audio)

This backend provides APIs for Continuous Two-Factor Authentication using Wi-Fi, Ambient Sound, and Smartwatch data.  
Now updated to include **real ambient sound recording** via microphone.

---

## 🚀 Quick Start

### 1. Create & Activate Virtual Environment
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate
```

### 2. Install Dependencies
```bash
pip install -r backend/requirements.txt
```

If using the audio module, also ensure:
```bash
pip install sounddevice librosa
```

- On Linux: you may need `sudo apt-get install portaudio19-dev`
- On Windows: `pip install pipwin && pipwin install pyaudio`
- On macOS: `brew install portaudio`

### 3. Train the Demo Model
```bash
python backend/models/train_model.py
```

### 4. Run the Backend
```bash
python backend/app.py
```
Now your API runs on **http://127.0.0.1:5000**

---

## 🎤 Audio Feature Extraction

The updated `backend/utils/audio_analyzer.py` will:
1. Record 3 seconds of ambient sound from your default microphone.
2. Extract MFCC (Mel-Frequency Cepstral Coefficients) using `librosa`.
3. Compute a simple **entropy-like score** (0.0 – 1.0).

- **Quiet environment** → Low entropy (e.g., 0.4–0.5)
- **Noisy environment** → High entropy (e.g., 0.7–0.9)

This entropy is used as one of the trust factors in continuous authentication.

### Example (quiet room)
```json
"audio": {"mfcc_entropy": 0.42}, "trust": 0.89, "result": "safe"
```

### Example (noisy cafeteria)
```json
"audio": {"mfcc_entropy": 0.81}, "trust": 0.47, "result": "anomalous"
```

---

## 🔗 API Endpoints

- `GET /api/config/` → System configuration (intervals, thresholds)
- `GET /api/metrics/` → Current Wi-Fi, Audio, Watch metrics + trust score
- `GET /api/decisions/` → Recent authentication decisions

---

## 📂 Project Structure
- `backend/utils/audio_analyzer.py` → Ambient sound capture + MFCC entropy
- `backend/utils/wifi_scanner.py` → Simulated Wi-Fi RSSI values
- `backend/utils/watch_reader.py` → Simulated smartwatch proximity
- `backend/models/` → ML model training + preprocessing
- `backend/routes/` → API routes for metrics, config, decisions
- `data/processed/decisions_store.json` → Rolling decision history

---

## ⚠️ Notes
- Ensure your microphone is accessible to Python when running the backend.
- If microphone access fails, the system returns a fallback entropy value of `0.6`.


========================================================
# Continuous 2FA Backend (Final Version)

Backend server for Continuous Two-Factor Authentication project.
It integrates Wi-Fi RSSI, Ambient Sound MFCC entropy, and Smartwatch proximity
to generate a trust score in real time.

## Setup
python -m venv .venv

.venv\Scripts\activate    # Windows

pip install -r backend/requirements.txt
```

For audio:
```bash
pip install sounddevice librosa
```

Linux: `sudo apt-get install portaudio19-dev`  
Mac: `brew install portaudio`  
Windows: `pip install pipwin && pipwin install pyaudio`

## Run
```bash
python backend/models/train_model.py   # train demo model
python backend/app.py                  # run backend at http://127.0.0.1:5000
```

## Endpoints
- GET `/api/config/`
- GET `/api/metrics/`
- GET `/api/decisions/?limit=12`
"# college_project" 
python -m backend.app


============================================================================================================================

# Continuous Two-Factor Authentication Using Wi-Fi and Ambient Sound

A prototype **Continuous 2FA (Two-Factor Authentication) system** that enhances user verification by leveraging **Wi-Fi fingerprints**, **ambient audio entropy**, and **wearable device proximity**.  
It provides a **web-based dashboard** for both **users** and **admins** to monitor trust scores, recent authentication decisions, and system statistics in real time.

---

## 🚀 Features

- **Multi-sensor Authentication**
  - Wi-Fi RSSI patterns (fingerprint stability).
  - Ambient audio MFCC entropy.
  - Smartwatch / wearable proximity.
- **Trust Score Computation**
  - Rolling average trust calculation.
  - Configurable thresholds for safe / challenge / block decisions.
- **User Dashboard**
  - Live trust score visualization.
  - Recent authentication decisions.
  - Real-time stream charts.
- **Admin Dashboard**
  - Manage users and devices.
  - Monitor decision trends and statistics.
  - Trust trend (line chart) and decision breakdown (pie chart).
  - Auto-refresh and manual refresh support.
- **Backend API**
  - RESTful endpoints for users, metrics, and admin controls.
  - PostgreSQL database with structured tables (`users`, `metrics`, `decisions`).
- **Scalable Architecture**
  - Flask backend + PostgreSQL.
  - Frontend in vanilla JS + Chart.js.
  - Modular file structure (backend routes, models, utils).



---

## 🛠️ Setup

### 1. Clone the Repository
```bash
git clone https://github.com/MAHALAKSHMI837/continuous-2fa-auth.git
cd continuous-2fa-auth

2. Backend Setup
cd backend
python -m venv venv
source venv/bin/activate   # On Windows: venv\Scripts\activate
pip install -r requirements.txt


3. Database Setup (PostgreSQL)
Create a new database in pgAdmin or psql:
CREATE DATABASE auth_system;

Inside it, create tables:

CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(100) NOT NULL,
    device VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE decisions (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    trust_score FLOAT NOT NULL,
    result VARCHAR(20) NOT NULL,
    note TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE metrics (
    id SERIAL PRIMARY KEY,
    user_id INT REFERENCES users(id),
    wifi_rssi FLOAT,
    audio_entropy FLOAT,
    watch_proximity FLOAT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);


Update your db.py with correct credentials:

import psycopg2

def get_db():
    return psycopg2.connect(
        dbname="auth_system",
        user="postgres",
        password="your_password",
        host="localhost",
        port="5432"
    )


4. Run Backend
cd backend
python -m app
Runs at: http://127.0.0.1:5000

5. Run Frontend
Open:
User Dashboard → frontend/user/index.html
Admin Dashboard → frontend/admin/index.html

API Endpoints
User Routes

POST /api/user/register → Register new user
GET /api/user/metrics/<user_id> → Fetch latest metrics
GET /api/user/decisions/<user_id> → Recent decisions for user

Admin Routes

GET /api/admin/users → List users
GET /api/admin/decisions → List all decisions
GET /api/admin/stats → Statistics (total users, avg trust, breakdown)

Auth Simulation
POST /api/auth/decision → Insert a decision (used by simulator)

📊 Dashboards
User Dashboard:-
Trust Score (line chart).
Wi-Fi RSSI variation.
Audio entropy detection.
Recent decisions table.
Live stream simulation.

Admin Dashboard:-
Total Users, Total Decisions, Avg Trust.
Users table.
Trust trend (24h line chart).
Decision breakdown (pie chart).
Recent decisions (latest 10).
Auto-refresh toggle.

🧪 Testing
Insert fake data:
INSERT INTO users (username, device) VALUES ('Maha', 'EEC-CSE-LAP-29');
INSERT INTO decisions (user_id, trust_score, result, note)
VALUES (1, 0.72, 'ALLOW', 'Wi-Fi stable');


🌱 Future Enhancements
Integration with real Wi-Fi scanning tools (instead of simulated metrics).
Real-time audio fingerprinting using Python audio libraries.
Wearable device integration via Bluetooth API.
Authentication policies configurable from Admin UI.
Production-ready deployment with Docker.

📜 License
This project is for research & academic purposes.
If using for commercial applications, please ensure compliance with patent & intellectual property laws.
