# 🔐 Face Authentication System

A **two-factor authentication** web app that combines a traditional
password login with **facial recognition**. Users register with their details
and a set of face captures; on login they authenticate with their password and
then verify their identity in real time through the webcam. Face embeddings are
generated with **DeepFace** and compared using cosine similarity.

<p align="left">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.8-3776AB?logo=python&logoColor=white">
  <img alt="FastAPI" src="https://img.shields.io/badge/FastAPI-0.83-009688?logo=fastapi&logoColor=white">
  <img alt="MongoDB" src="https://img.shields.io/badge/MongoDB-Atlas-47A248?logo=mongodb&logoColor=white">
  <img alt="DeepFace" src="https://img.shields.io/badge/DeepFace-Embeddings-FF6F00">
  <img alt="Docker" src="https://img.shields.io/badge/Docker-ready-2496ED?logo=docker&logoColor=white">
</p>

---

## ✨ Features

- **Two-factor authentication** — password (JWT + bcrypt) **plus** live face verification.
- **Face registration** — capture multiple webcam images that are converted into
  face embeddings and stored in MongoDB.
- **Face login** — capture images at login; the system compares the new
  embeddings against the stored ones and grants access only on a match.
- **JWT-based sessions** — access token issued on password login, stored as an
  HTTP-only cookie.
- **Input validation** — registration data (email, phone, matching passwords) is
  validated before a user is saved.
- **MongoDB storage** — separate collections for user records and face embeddings.
- **Containerised** — ships with a `Dockerfile` for easy deployment.

---

## 🧠 How authentication works

```
REGISTER
  Fill form ──▶ validate ──▶ save user (MongoDB) ──▶ capture 8 face images
                                                       │
                                                       ▼
                                         generate embeddings (DeepFace)
                                                       │
                                                       ▼
                                          store embeddings in MongoDB

LOGIN
  Email + password ──▶ verify (bcrypt) ──▶ issue JWT cookie
                                              │
                                              ▼
                              capture 8 face images ──▶ embeddings
                                              │
                                              ▼
                      compare with stored embedding (cosine similarity)
                                              │
                                  match? ──▶ ✅ authenticated
                                  no    ──▶ ❌ unauthorized
```

---

## 🛠️ Tech Stack

| Layer            | Technology                                        |
|------------------|---------------------------------------------------|
| Web framework    | [FastAPI](https://fastapi.tiangolo.com/) + Uvicorn |
| Templating / UI  | Jinja2, Bootstrap, vanilla JS (`webcam.js`)       |
| Auth / tokens    | `python-jose` (JWT), `passlib` + `bcrypt`         |
| Face recognition | [DeepFace](https://github.com/serengil/deepface)  |
| Database         | MongoDB (Atlas) via `pymongo`                     |
| Config           | `python-dotenv`, `PyYAML`                         |
| Deployment       | Docker                                            |

---

## 📁 Project Structure

```
.
├── app.py                       # FastAPI entry point, mounts routers & static files
├── controller/
│   ├── auth_controller/         # /auth routes: register, login, token, logout
│   └── app_controller/          # /application routes: face register & login embeddings
├── face_auth/
│   ├── business_val/            # validation logic (user + embedding comparison)
│   ├── config/database/         # MongoDB connection
│   ├── constant/                # config constants (auth, database, embedding)
│   ├── data_access/             # DB read/write for users and embeddings
│   ├── entity/                  # User and embedding data models
│   ├── exception/               # custom exception handling
│   ├── logger/                  # logging setup
│   └── utils/                   # helper utilities
├── templates/                   # HTML pages (login, register, embedding capture)
├── static/                      # CSS, JS, images, vendor assets
├── Dockerfile
├── requirements.txt
├── .env.example                 # template for environment variables
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites
- Python **3.8**
- A **MongoDB** database (local or [MongoDB Atlas](https://www.mongodb.com/atlas))
- A webcam (for face capture)

### 1. Clone the repository

```bash
git clone https://github.com/Rajnikant3862/Login-Authentication.git
cd Login-Authentication
```

### 2. Create and activate an environment

```bash
conda create -p ./env python=3.8 -y
conda activate ./env
# or: python -m venv venv && source venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Copy the template and fill in your own values:

```bash
cp .env.example .env
```

| Variable                  | Description                              |
|---------------------------|------------------------------------------|
| `SECRET_KEY`              | Secret used to sign JWT tokens           |
| `ALGORITHM`               | JWT algorithm (e.g. `HS256`)             |
| `MONGODB_URL_KEY`         | MongoDB connection string                |
| `DATABASE_NAME`           | Database name                            |
| `USER_COLLECTION_NAME`    | Collection for user records              |
| `EMBEDDING_COLLECTION_NAME` | Collection for face embeddings         |

### 5. Run the app

```bash
python app.py
```

The server starts at **http://localhost:8000** and redirects to the `/auth` login page.

---

## 🐳 Run with Docker

```bash
# Build
docker build -t face_auth .

# Run (pass your environment variables)
docker run -d -p 8000:8000 --env-file .env face_auth
```

---

## 🧭 Key Routes

| Method | Route                              | Purpose                                |
|--------|------------------------------------|----------------------------------------|
| GET    | `/auth/`                           | Login page                             |
| POST   | `/auth/`                           | Submit email + password                |
| GET    | `/auth/register`                   | Registration page                      |
| POST   | `/auth/register`                   | Create a new user                      |
| GET    | `/auth/logout`                     | Clear session / token                  |
| GET    | `/application/register_embedding`  | Capture face images during registration |
| POST   | `/application/register_embedding`  | Store face embeddings                  |
| POST   | `/application/`                    | Verify face on login                   |

Interactive API docs are available at **`/docs`** (FastAPI Swagger UI).

---

## 🔒 Security Notes

- **Never commit your `.env` file.** Use `.env.example` as a reference and keep
  real credentials out of version control.
- Passwords are hashed with **bcrypt** before storage.
- Access tokens are JWTs delivered via **HTTP-only cookies**.
- If credentials are ever exposed, **rotate them immediately** (database
  password and `SECRET_KEY`).

---

## 🗺️ Roadmap

- [ ] Configurable similarity threshold for face matching
- [ ] Liveness / anti-spoofing detection
- [ ] Rate limiting and account lockout on repeated failures
- [ ] Unit and integration tests
- [ ] CI/CD pipeline

---

## 🙏 Acknowledgements

Built on the open-source Face Authentication architecture using FastAPI and
DeepFace, adapted and extended for learning and portfolio purposes.

---

## 👤 Author

**Rajnikant Totare**
GitHub: [@Rajnikant3862](https://github.com/Rajnikant3862)

If you find this project useful, please ⭐ the repo!
