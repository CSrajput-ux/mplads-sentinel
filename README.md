# MPLADS Sentinel 🛡️

**MPLADS Sentinel** is an AI/ML-driven analytics and tracking platform designed to monitor, audit, and analyze the execution of Member of Parliament Local Area Development Scheme (MPLADS) funds. The project aims to enhance transparency, detect potential risk factors/anomalies in fund allocation, and provide actionable insights through an interactive web dashboard.

---

## 🚀 Features

- **Data Pipeline & ML Analytics:** Automated data processing and machine learning pipelines to detect anomalies, risk scores, and irregularities in fund utilization.
- **FastAPI Backend:** High-performance, async RESTful APIs powering data retrieval, risk analytics, and model predictions.
- **Modern Interactive Dashboard:** Built with Next.js/React & TypeScript for a seamless and responsive user experience.
- **Multi-Dataset Support:** Capability to upload, process, and analyze multiple datasets dynamically.
- **Production Ready Infrastructure:** Powered by Neon PostgreSQL database, error boundary protections, and configurable risk logic (`risk_config.json`).

---

## 🛠️ Tech Stack

- **Frontend:** TypeScript, React, Next.js, Tailwind CSS
- **Backend:** Python, FastAPI, Uvicorn
- **Machine Learning & Data:** Scikit-Learn, Pandas, NumPy
- **Database:** Neon PostgreSQL
- **Package Managers & Tools:** `pnpm` / `npm`, `pip` / `requirements.txt`

---

## 📁 Repository Structure

```text
mplads-sentinel/
├── backend/          # FastAPI server, API routes, database models, and logic
├── frontend/         # Next.js / React TypeScript dashboard application
├── data_pipeline/    # Data cleaning, feature engineering, and ingestion scripts
├── models/           # Machine learning model definitions, training, and artifacts
├── data/             # Raw & processed datasets / sample files
├── tests/            # Automated unit and integration test suites
├── risk_config.json  # Configuration file for risk criteria & thresholds
├── start.py          # Unified entrypoint script (optimized for cloud deployment like Render)
├── start-production.sh / .bat # Shell & Batch scripts for production launcher
└── DEPLOYMENT.md     # In-depth guide for deploying to production environments

🏁 Getting Started
Prerequisites
Ensure you have the following installed on your machine:

Python: 3.9+

Node.js: 18.x+

pnpm (recommended) or npm

PostgreSQL (or a Neon PostgreSQL connection string)

📥 Installation & Setup
Clone the Repository

Bash
git clone [https://github.com/CSrajput-ux/mplads-sentinel.git](https://github.com/CSrajput-ux/mplads-sentinel.git)
cd mplads-sentinel
Backend Setup

Bash
# Create a virtual environment
python -m venv venv

# Activate virtual environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install Python dependencies
pip install -r requirements.txt
Frontend Setup

Bash
# Navigate to the frontend directory
cd frontend

# Install Node packages
pnpm install
# or
npm install

cd ..
Environment Configuration
Create a .env file in the root directory (or in backend/ and frontend/ as needed) with your environment variables:

Code snippet
DATABASE_URL=postgresql://user:password@ep-example.neon.tech/mplads_db
PORT=8000
🚀 Running the Application
1. Unified Production Mode
To run the full stack via the optimized entrypoint:

Bash
python start.py
(Or use start-production.sh on Linux/macOS or start-production.bat on Windows)

2. Development Mode
Start Backend API:

Bash
uvicorn backend.main:app --reload --port 8000
Start Frontend Dashboard:

Bash
cd frontend
pnpm dev
# or
npm run dev
🌐 Live Deployment
Live Application: mplads-sentinel-alpha.vercel.app

For comprehensive deployment instructions, refer to DEPLOYMENT.md.

🧪 Running Tests
To run the full test suite for data pipelines and backend endpoints:

Bash
pytest tests/
📜 License
This project is licensed under the MIT License.
