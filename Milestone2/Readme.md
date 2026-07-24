# ⚡ FreightQuote AI — Milestone 2
### Enterprise Multi-Agent Franchise Operations Platform

This milestone builds on Milestone 1 and delivers a fully modular, Colab-deployable, LLM-powered franchise operations platform. It combines three specialized ML agents, a locally-hosted quantized LLM (Qwen-2.5-3B-Instruct), a Streamlit UI, SQLite persistence, and secure authentication into a single Streamlit application, published live via ngrok.

---

## 📁 Repository Structure

```
Infosys Repository/
├── Milestone1/
└── Milestone2/
    ├── FreightQuote_AI_Milestone2.ipynb   # Main Colab notebook (build + launch)
    ├── README.md                          # This file
    ├── auth.py                            # Authentication & session management
    ├── db.py                              # SQLite schema & connection layer
    ├── ui_theme.py                        # Neo-brutalist Streamlit theme
    ├── admin_dash.py                      # Admin dashboard renderer
    ├── train_ml_freight.py                # Multi-algorithm ML training pipeline
    ├── llm_engine_freight.py              # Qwen-2.5-3B (4-bit) LLM engine
    ├── requirements.txt                   # Python dependencies
    └── screenshots/                       # Captures listed in the report spec
```

> Files such as `config.py`, `weather_context.py`, `notifications.py`, `seed_data.py`, `agent2_franchise.py`, `agent3_franchise.py`, and `app.py` are also written out by the notebook (via `%%writefile` cells) at runtime and are required for the app to run end-to-end alongside the modules above.

---

## 🧠 What This Notebook Does

1. **Installs dependencies** — Streamlit, PyTorch/Transformers stack, bitsandbytes, plotly, auth libraries, etc.
2. **Loads secrets & mounts storage** — reads Colab Secrets (or environment variables) for API keys, and mounts Google Drive for persistent model/database storage.
3. **Loads the LLM** — downloads and quantizes `Qwen/Qwen2.5-3B-Instruct` to 4-bit NF4 precision for fast, low-memory inference on a T4 GPU.
4. **Writes all application modules** — each core `.py` file is generated via `%%writefile` cells, keeping the notebook self-contained and reproducible.
5. **Initializes the database & seeds sample data** — outlets, staff, and inventory records.
6. **Trains the ML agents** — compares multiple algorithms per agent and persists the best-performing models.
7. **Launches the Streamlit app** — publishes a public URL via ngrok for live access.
8. **Shuts down cleanly** — terminates the Streamlit process and ngrok tunnel to free GPU memory.

---

## 🤖 Multi-Agent System

| Agent | Purpose | Models Compared | Output |
|---|---|---|---|
| **Agent 1 — Workforce / Attrition** | Predicts employee attrition risk | Calibrated Logistic Regression, Random Forest, Gradient Boosting, SVM | Best model selected by ROC-AUC |
| **Agent 2 — Outlet Territory Clustering** | Segments outlets into performance tiers & forecasts revenue | KMeans (k=3,4,5 via silhouette score), Random Forest / Gradient Boosting / Extra Trees regressors | `kmeans_outlets.joblib` + revenue regressor |
| **Agent 3 — Supply Chain & Inventory** | Predicts stockout risk and demand | Random Forest, Gradient Boosting, Extra Trees, Ridge | Best model selected by R² |

An **AI Copilot** tab orchestrates all three agents together, using the locally-hosted LLM to synthesize a unified, multi-agent response (debate + synthesis) to natural-language queries.

---

## 🔐 Authentication & Security

- SQLite-backed user store with **bcrypt** password hashing and **JWT** session tokens
- **Gmail OTP** based forgot-password flow (falls back to console-printed OTP if email credentials aren't configured)
- OTP resend cooldown escalation (60s → 180s → 300s → 1hr)
- Progressive account lockout (3rd failed attempt = 5 min, 4th = 15 min, 5th = permanent)
- Real-time password strength meter (Weak < 5 chars / Average 5–9 / Good 10+)
- Role-based access (Admin, Franchise Owner, Regional Operations Manager, Store Manager, Supply Chain Analyst)

---

## 🎨 UI / Theme

`ui_theme.py` implements a shared **Neo-Brutalist** design system (bold borders, hard drop shadows, high-contrast accent colors) used consistently across every dashboard, card, badge, and button in the app.

---

## 🖥️ Admin Dashboard

`admin_dash.py` renders:
- Live system health (GPU VRAM usage, GPU utilization, app uptime, LLM load status via `nvidia-smi`)
- User management (add/view users, role assignment)
- Recent notification/alert log

---

## 🗄️ Database Schema (`db.py`)

| Table | Purpose |
|---|---|
| `outlets` | Franchise outlet financials, staffing, and tier/risk classification |
| `staff` | Employee records with attrition probability & intervention status |
| `inventory_records` | SKU-level stock, demand, and stockout risk |
| `merged_datasets` | Unified training data across all three agents |
| `users` | Authentication credentials, roles, security Q&A |
| `ml_models` | Trained model metadata (R², RMSE, accuracy, row counts) |
| `notifications` | Multi-channel (SMS/Email/In-App) alert log |

---

## 🔑 Required Secrets

Set these in **Colab Secrets** (or as environment variables) before running:

| Secret | Required | Purpose |
|---|---|---|
| `HF_TOKEN` | Recommended | Hugging Face model download |
| `NGROK_AUTHTOKEN` | Recommended | Public URL tunneling |
| `KAGGLE_USERNAME` / `KAGGLE_KEY` | Optional | Real dataset downloads (synthetic fallback used if absent) |
| `EMAIL_ID` / `EMAIL_PASSWORD` | Optional | Gmail SMTP OTP delivery (console fallback used if absent) |
| `JWT_SECRET_KEY` | Optional | JWT signing (dev default used if absent) |
| `ADMIN_EMAIL_ID` / `ADMIN_PASSWORD` | Optional | Default admin login (`infosys@ai` / `admin@123` if absent) |

---

## 🚀 How to Run (Google Colab)

1. Open `FreightQuote_AI_Milestone2.ipynb` in Google Colab with a **T4 GPU runtime**.
2. Add the secrets listed above under the 🔑 Colab Secrets panel.
3. Run all cells top to bottom:
   - Installs dependencies
   - Mounts Google Drive & loads secrets
   - Loads the quantized LLM
   - Writes all `.py` modules
   - Initializes & seeds the database
   - Trains all ML agents
   - Launches Streamlit + ngrok
4. Open the printed ngrok URL to access the live app.
5. Run the final cell to stop the app and release GPU memory when done.

---

## 📦 Dependencies

See [`requirements.txt`](./requirements.txt):

```
streamlit
pyngrok
bcrypt
pyjwt
pandas
numpy
scikit-learn
joblib
transformers
accelerate
bitsandbytes
plotly
streamlit-option-menu
faker
kaggle
```

---

## 🖼️ Screenshots

All required captures for this milestone's report are stored in [`screenshots/`](./screenshots), including:
- Login / authentication portal
- AI Copilot multi-agent chat
- Agent 1 — Workforce attrition dashboard
- Agent 2 — Outlet clustering & revenue map
- Agent 3 — Inventory & supply chain advisor
- Admin dashboard (system health & user management)
- Live ngrok-published app URL

---

## 📌 Notes

- Model artifacts (`attrition_lr.joblib`, `kmeans_outlets.joblib`, `revenue_rf.joblib`, `inventory_demand_gb.joblib`) and the SQLite database (`franchiseops.db`) are persisted to Google Drive under `MyDrive/FranchiseOps_AI/` so they survive Colab session restarts.
- If no GPU is available, the LLM load step will fail gracefully — ensure the Colab runtime type is set to GPU (T4) before running.
- This is a continuation of the same Infosys Repository as Milestone 1; no new repository was created for this milestone.
