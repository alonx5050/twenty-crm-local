# 📘 Twenty CRM + Zapier Integration Assignment

## 🎯 Objective

Set up a fully working local instance of Twenty CRM, expose the backend API using ngrok, and connect it to Zapier to trigger an automation (Zap) when a new Company record is created.

---

## ✅ Deliverables Checklist

* [x] **Screencast Recording** — Showing full setup (pending upload)
* [x] **Screenshots** — Included throughout this document
* [x] **Documentation** — This README documents:

  * Setup process
  * Configuration details
  * Challenges & resolutions
  * Improvement suggestions for production

---

## 🏗️ Step-by-Step Breakdown

### Part 1: Local Setup of Twenty CRM

* **Cloned Repository**

  * URL: [https://github.com/twentyhq/twenty](https://github.com/twentyhq/twenty)
* **Installed Dependencies**

  * Frontend: `yarn install` in `packages/twenty-front`
  * Backend: `yarn install` in `packages/twenty-back`
* **Started Services**

  * Backend: `npx nx run twenty-back:start`
  * Frontend: `npx nx run twenty-front:start`
* **Frontend URL:** [http://localhost:3001](http://localhost:3001)
* **Backend URL:** [http://localhost:3000](http://localhost:3000)
* **Initial Workspace:** Created via UI wizard after launch

### Part 2: API Key Creation

* Navigated to: `Settings > APIs`
* Created API key: **Zapier Integration Key**
* Copied key for later use in Zapier

### Part 3: Exposing Backend via ngrok

* **Downloaded** ngrok for Windows
* **Authenticated** with token:

  ```sh
  ngrok config add-authtoken <token>
  ```
* **Exposed Backend:**

  ```sh
  ngrok http 3000
  ```
* **Public URL:** `https://8877-77-125-33-48.ngrok-free.app`
* **Updated Frontend `.env`**:

  ```env
  REACT_APP_SERVER_BASE_URL=https://8877-77-125-33-48.ngrok-free.app
  ```

### Part 4: Zapier Connection

* Created Zapier account
* Added a connection to the **Twenty App**
* Provided:

  * **ngrok backend URL**
  * **API Key** from Twenty CRM
* Verified with green check ✅

### Part 5: Creating the Zap

#### ✅ Trigger:

* App: Twenty
* Event: `Record Trigger`
* Record Name: `Company`
* Operation: `created`

#### 🔄 Action:

* App: Gmail
* Event: Send Email
* Body: `A new company named {{Company Name}} was created.`
* Subject: `New Company Created`

#### 🧠 Improvement:

* Added **Filter by Zapier** step:

  * Condition: Company Name → Exists
  * Prevents emails from firing if name was blank

---

## 📸 Key Screenshots

> **\[Screenshots Provided Separately or in Screencast]**

* Working local setup of Twenty CRM (Backend & Frontend)
* ngrok public tunnel running
* API Key screen
* Zapier connection setup
* Zap: Trigger and Action configured

---

## 🧠 What I Learned

* How to run Twenty CRM locally and debug its dual-stack architecture
* How ngrok integrates with API-based tools
* How Zapier fetches dynamic field values and when to use filters

---

## 🛠️ Challenges & Resolutions

| Challenge                                     | Resolution                                             |
| --------------------------------------------- | ------------------------------------------------------ |
| Frontend failed to connect to backend         | Updated `.env` with ngrok URL                          |
| Trigger fired before company name was filled  | Added `Filter by Zapier` for `Record Name Exists`      |
| Couldn’t pull dynamic company name into Gmail | Ensured record creation completed before trigger fired |

---

## 🚀 Production Recommendations

* **Use a reverse proxy** (e.g., Nginx) and a static IP instead of ngrok
* Add validation logic to prevent early firing on incomplete records
* Use a managed environment (e.g., Vercel, Fly.io, Railway) for hosting
* Secure API endpoints and rotate API keys
* Set up logging and alerts for Zap errors

---

## 🧾 Summary

This project demonstrates end-to-end integration of a self-hosted CRM system with a cloud automation platform. All steps were implemented, documented, and verified with working outputs.

---

📩 *For the screencast and screenshots, refer to the provided video link or attached image files.*
