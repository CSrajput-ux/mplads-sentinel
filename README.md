# MPLADS Sentinel 🛡️

[![Live Demo](https://img.shields.io/badge/Live_Demo-mplads--sentinel--alpha.vercel.app-brightgreen?style=flat-square)](https://mplads-sentinel-alpha.vercel.app)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=flat-square)](LICENSE)
[![Frontend](https://img.shields.io/badge/Frontend-Next.js_14-blue?style=flat-square&logo=nextdotjs)](https://nextjs.org/)
[![Backend](https://img.shields.io/badge/Backend-FastAPI-009688?style=flat-square&logo=fastapi)](https://fastapi.tiangolo.com/)
[![Database](https://img.shields.io/badge/Database-Neon_PostgreSQL-00e599?style=flat-square&logo=postgresql)](https://neon.tech/)

**MPLADS Sentinel** is an AI/ML-driven analytics and tracking platform designed to monitor, audit, and analyze the execution of Member of Parliament Local Area Development Scheme (MPLADS) funds. 

The project aims to enhance transparency, detect potential risk factors and anomalies in fund allocation, and provide actionable insights to citizens and administrators through an interactive dashboard.

---

## 📌 Table of Contents

- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Repository Structure](#-repository-structure)
- [System Architecture](#-system-architecture)
- [Getting Started](#-getting-started)
  - [Prerequisites](#prerequisites)
  - [Clone Repository](#1-clone-the-repository)
  - [Backend Setup](#2-backend-setup)
  - [Frontend Setup](#3-frontend-setup)
  - [Environment Variables](#4-environment-variables)
- [Running the Application](#-running-the-application)
  - [Development Mode](#1-development-mode)
  - [Production Mode](#2-production-mode)
- [API Documentation](#-api-documentation)
- [Testing](#-testing)
- [Deployment](#-deployment)
- [Contributing](#-contributing)
- [License](#-license)

---

## ✨ Features

- **Automated Data Processing Pipeline:** Cleans, transforms, and ingests multi-dataset MPLADS allocations dynamically.
- **Machine Learning Risk Analytics:** Detects anomalous spending patterns, delay indicators, and project risk scores using custom ML models.
- **Async FastAPI Backend:** High-performance RESTful APIs for real-time querying, anomaly detection, and data streaming.
- **Modern Interactive Dashboard:** Built with Next.js, React, and Tailwind CSS for responsive visualizations and metric highlights.
- **Configurable Risk Scoring:** Modular logic via `risk_config.json` allows easy tuning of risk thresholds without redeploying code.
- **Cloud-Native Database Integration:** Powered by Neon PostgreSQL for scalable serverless storage.

---

## 🛠️ Tech Stack

### **Frontend**
- **Framework:** Next.js 14 / React 18
- **Language:** TypeScript
- **Styling:** Tailwind CSS, Lucide Icons
- **Package Manager:** `pnpm` (or `npm`)

### **Backend**
- **Framework:** FastAPI
- **Server:** Uvicorn
- **Language:** Python 3.9+
- **Data & ML:** Pandas, NumPy, Scikit-Learn
- **Testing:** PyTest

### **Database & Infrastructure**
- **Database:** Neon PostgreSQL
- **Deployment Entrypoint:** Unified launcher script (`start.py`) optimized for platform hosting like Render / Vercel.

---

## 📁 Repository Structure

```text
mplads-sentinel/
├── backend/                  # FastAPI web server, routes, & database models
├── frontend/                 # Next.js frontend application & interactive UI
├── data_pipeline/            # Data cleaning, feature engineering & ETL logic
├── models/                   # ML model definitions, training scripts & artifacts
├── data/                     # Raw, processed, and sample datasets
├── tests/                    # Unit and integration test suites
├── risk_config.json          # Configurable risk assessment thresholds
├── start.py                  # Entrypoint script for unified execution / hosting
├── start-production.sh       # Linux / macOS launcher script
├── start-production.bat      # Windows batch launcher script
└── DEPLOYMENT.md             # Detailed guide for production deployment
