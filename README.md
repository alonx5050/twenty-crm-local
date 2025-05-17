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

After that , you can see you dashboard.

![Image](https://github.com/user-attachments/assets/b68cb1e0-912b-41f5-a6cf-c3edea13733f)

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
![Image](https://github.com/user-attachments/assets/08c2a4f4-ff07-4952-b14b-24f928c890fe)

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

## 🔄 Part 4: Zapier Integration

### 🔗 Step 1: Connect to Twenty App in Zapier

1. Searched for the **Twenty** app *in Za*pier
2. Created a new connection with:

   * `API Key` from Part 2
   * `ngrok` URL from Part 3
     
![Image](https://github.com/user-attachments/assets/c691f723-a347-4e7d-94c0-44d5a9a7c43d)
   
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


## 📬 Summary

This project demonstrates end-to-end integration of a modern monorepo CRM (Twenty) with external automation tools (Zapier). All steps were completed with production-ready practices in mind — from configuration, tunneling, authentication, to automation triggers and filtering.

---

📁 *For full screenshots, video screencast, and **`.env`** configs, refer to the attached folder or screencast link.*
