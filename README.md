# 📘 Twenty CRM + Zapier Integration Assignment

## 🎯 Objective

Set up a fully working local instance of Twenty CRM, expose the backend API using ngrok, and connect it to Zapier to trigger an automation (Zap) when a new Company record is created.

This README provides a software-architect-level deep dive into the design, configuration, and integration pipeline, covering key decisions, rationale, challenges, and enhancement strategies for production use.

---

## ✅ Deliverables Checklist

*

---

## 🧠 System Architecture Overview

Twenty CRM is a modular monorepo managed with Nx, consisting primarily of:

* `twenty-server` — NestJS backend exposing GraphQL and REST APIs (Port `3000`)
* `twenty-front` — Vite + React frontend (Port `3001`)
* PostgreSQL & Redis dependencies for persistence and caching

For this integration, only the **backend (port 3000)** needs exposure for external systems like Zapier. The frontend can remain local.

---

## 🏗️ Step-by-Step Breakdown

### 🧩 Part 1: Local Setup of Twenty CRM

#### 🗂 Cloned Repository

```bash
git clone https://github.com/twentyhq/twenty
cd twenty
```

#### 📦 Installed Dependencies

```bash
yarn install
```

#### 🖥 Started Backend and Frontend

```bash
npx nx run twenty-server:start
npx nx run twenty-front:start
```

* **Backend** (GraphQL + REST): `http://localhost:3000`
* **Frontend**: `http://localhost:3001`

#### 🧪 Initial Workspace

After launch, created a workspace via the UI wizard to activate the backend functionality.

![Image](https://github.com/user-attachments/assets/a715c315-fc9c-4ae6-bb93-6fe3c9aeda03)

---

### 🔐 Part 2: API Key Generation

Navigated to `Settings > APIs` inside the frontend dashboard, created a new API key named `Zapier Integration`, and securely copied it for use in Postman and Zapier.

This key is used in `Authorization: Bearer <token>` headers for GraphQL access.

![Image](https://github.com/user-attachments/assets/d95b4471-afe1-48e2-a040-6c6db073cdb0)
---

### 🌍 Part 3: Exposing Backend via ngrok

### 🔧 Determine Which Port to Expose

From system logs and architecture:

* Backend runs on port `3000` (NestJS)
* Frontend on port `3001`, **not needed for Zapier**

✅ So we expose **`localhost:3000`**.

### 📡 Setup ngrok

first - sign up to ngrok and obtain your authtoken for the ngrok config. (you only need this once)

![Image](https://github.com/user-attachments/assets/28d9c96a-fd5c-441c-a08d-82aca1eabd11)

```bash
ngrok config add-authtoken <your-token>
ngrok http 3000
```

Resulting URL:

```env
https://8877-77-125-33-48.ngrok-free.app
```

Used this for:

* `REACT_APP_SERVER_BASE_URL` in frontend `.env`
* Base URL in Zapier connection

### ✅ Verification

Tested using curl & Postman:

```bash
curl -X POST https://<ngrok-url>/graphql \
  -H "Authorization: Bearer <api-key>" \
  -H "Content-Type: application/json" \
  -d '{"query":"{ __typename }"}'
```

---

### 📦 Why Docker Would Be Better

Although this assignment used native yarn + ngrok setup, a **Docker-based approach** would be preferable in real-world environments:

#### ✅ Advantages:

* ✅ **Consistency**: Eliminate local dependency/version issues
* ✅ **Isolation**: No port clashes, clean environment
* ✅ **Networking**: Expose only required containers
* ✅ **Scalability**: Easily switch to compose + cloud hosting (Railway, Render)
* ✅ **CI/CD ready**: Simplifies DevOps pipelines

---

## 🔄 Part 4: Zapier Integration

### 🔗 Step 1: Connect to Twenty App in Zapier

1. Searched for the **Twenty** app *in Za*pier
2. Created a new connection with:

   * `API Key` from Part 2
   * `ngrok` URL from Part 3
3. Connection succeeded ✅

---

## ⚡ Part 5: Creating the Zap

### 🟢 Trigger:

* **App**: Twenty
* **Event**: `Record Created`
* **Schema**: `core`
* **Table**: `company`

Zapier pulled test record successfully.

### 🔁 Filter (to improve UX):

* **App**: Filter by Zapier
* **Condition**: `Record Name → Exists`
* Prevents firing on incomplete company records

### ✉️ Action:

* **App**: Gmail
* **Event**: Send Email
* **Subject**: `New Company Created`
* **Body**:

  > A new company named {{Record Name}} was added to Twenty CRM.

![Image](https://github.com/user-attachments/assets/eb822cd6-c3e8-42b0-ad35-226e72b676e9)
---

## 📸 Screenshots Summary

* ✅ Local Twenty CRM (frontend & backend)
* ✅ API Key creation
* ✅ ngrok tunnel
* ✅ Zapier connection config
* ✅ Trigger + Filter + Gmail action
* ✅ Zapier test: Email fired on valid company creation

---

## 🚧 Challenges & Resolutions

| Challenge                            | Resolution                                             |
| ------------------------------------ | ------------------------------------------------------ |
| Frontend couldn't reach backend      | Updated `REACT_APP_SERVER_BASE_URL` to ngrok URL       |
| Zap triggered before name was filled | Added `Filter by Zapier` → `Record Name Exists`        |
| Couldn't access Twenty externally    | Confirmed port 3000 exposure with ngrok + auth headers |

---

## 🧠 What I Learned

* Navigating multi-package Nx workspaces
* Aligning GraphQL schema access between backend & Zapier
* Best practices for secure API key handling
* How to enforce data quality at the automation layer (Zapier filter)
* Benefits of Dockerization for scalable local dev

---

## 🏁 Recommendations for Production

| Area                 | Recommendation                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Deployment           | Use Docker Compose / K8s / Render for stability                    |
| API Security         | Rotate API keys, implement scopes/roles                            |
| HTTPS                | Use custom domain and cert (via Cloudflare/Nginx) instead of ngrok |
| Error Monitoring     | Integrate with Sentry or Datadog                                   |
| Email Service        | Switch from Gmail to SES/SendGrid for scalable email delivery      |
| Trigger Stability    | Use retry logic / polling fallback in Zapier                       |
| Filtering/Validation | Add server-side validation hooks to prevent empty record names     |

---

## 📬 Summary

This project demonstrates end-to-end integration of a modern monorepo CRM (Twenty) with external automation tools (Zapier). All steps were completed with production-ready practices in mind — from configuration, tunneling, authentication, to automation triggers and filtering.

> 🔐 Everything was designed with secure, reproducible, and testable architecture in mind.

---

📁 *For full screenshots, video screencast, and **`.env`** configs, refer to the attached folder or screencast link.*
