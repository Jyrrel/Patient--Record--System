# 🏥 Patient Record System

A web-based clinic management system that covers the full patient journey: **appointments → queue → consultation → vitals, diagnosis, labs & prescriptions → billing → official receipt**. It has role-based access for Admin, Doctor, Staff, and Patient users, a per-role analytics dashboard, and a real-time "Call Next Patient" alert.

> Built as a school project for **System Developer** (BSIT major track).

---

## ✨ Features

### Clinic operations
- **Appointments** with automatic queue numbers (per doctor, per day, first come first served), a daily patient limit per doctor, and a same-patient duplicate guard
- **Consultations** with vital signs, ICD-10 diagnoses, laboratory test requests, and prescriptions
- **Billing** with automatic totals from professional fees, per-doctor consultation fees, and walk-in payments (Cash / Card / GCash / Maya)
- **Printable Official Receipts** (browser print or Save as PDF) and **printable medical records**
- **Appointment change requests** that patients can accept

### Master files (full CRUD with soft delete)
Patients · Doctors · Medicines · Generics · Specializations · Diagnoses (ICD-10) · Laboratory Tests · Roles · User Accounts

### Real-time queue alert
A doctor clicks **📢 Call Next Patient** and every signed-in Staff browser instantly gets a SweetAlert saying which patient to send in (Node.js + WebSocket).

### Patient portal
Patients sign in to see **only their own** records, book or cancel appointments, view bills, and pay online (demo checkout, no real payment).

### Dashboard & UX
- Role-specific **analytics** (Chart.js): appointments by month, revenue, top diagnoses, doctor workload, and more
- **Theme picker**: Light / Auto / Dark mode, 5 accent colors, text size, saved per browser

---

## 👥 Roles

| Role | Can do |
|---|---|
| **Admin** | User accounts, roles, doctor fees, all master files, billing and fee management, clinic-wide analytics |
| **Doctor** | Own appointments and queue, consultations, vitals, diagnoses, labs, prescriptions, "Call Next Patient" |
| **Staff** (Front desk) | Appointments, billing, walk-in payments, receipts, receives queue alerts |
| **Patient** | Own medical history, book or cancel appointments, view and pay own bills, receipts |

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend | HTML5, CSS3, JavaScript (ES modules), Bootstrap 5.3, Axios, Chart.js |
| Backend | PHP (PDO) REST-style API |
| Database | MySQL / MariaDB |
| Real-time | Node.js + [`ws`](https://www.npmjs.com/package/ws) WebSocket server |
| Local server | XAMPP (Apache + MySQL) |

---

## 📁 Project Structure

```
patient-record-system/
├── api/            # PHP endpoints (patients, appointments, billing, ...)
├── css/            # Stylesheet and theming
├── database/       # Main schema + incremental migration scripts
├── js/             # Page scripts and per-module view/update/delete modules
├── ws-server/      # Node.js WebSocket server for "Call Next Patient"
├── login.html      # Entry point
├── home.html       # Role-based dashboard
└── *.html          # One page per module
```

---

## 🚀 Getting Started

### Requirements
- [XAMPP](https://www.apachefriends.org/) (or any Apache + PHP + MySQL stack)
- [Node.js](https://nodejs.org/) (only needed for the real-time queue alert)

### Installation

1. **Clone the repo** into your web server's document root:
   ```bash
   cd C:/xampp/htdocs
   git clone https://github.com/Jyrrel/patient-record-system.git
   ```

2. **Create the database.** Start Apache and MySQL, then import the schema:
   ```
   database/patient_record_system.sql
   ```
   (via phpMyAdmin → Import). Newer tables and columns are added automatically on first API connection by `api/auto-migrate.php`.

3. **Check the DB credentials** in `api/connection-pdo.php` (defaults match XAMPP: `root` with an empty password).

4. **Check the API URL.** The JS files assume:
   ```
   http://localhost/patient-record-system/api
   ```
   If your folder name differs, update `baseApiUrl` in the scripts under `js/`.

5. **Open the app** at `http://localhost/patient-record-system/login.html`
   (use `http://`, not `file://`, so the AJAX calls work).

### Optional: real-time queue alert

```bash
cd ws-server
npm install
npm start        # runs on ws://localhost:8080
```

To use another port, run `PORT=9000 npm start` and update `WS_PORT` in `js/queue-socket.js`.

---

## 🔑 Demo Accounts

| Username | Password | Role |
|---|---|---|
| `admin` | `admin123` | Admin |
| `frontdesk` | `staff123` | Staff |
| `jramirez` | `password123` | Doctor |

Patient logins are created by an Admin under **User Accounts** (pick the *Patient* role and link a patient record).

---

## 🗄️ Database Overview

Main tables: `PATIENT`, `USERS`, `ROLE`, `SPECIALIZATION`, `DOCTOR_SPECIALIZATION`, `APPOINTMENT`, `APPOINTMENT_CHANGE_REQUEST`, `CONSULTATION`, `VITAL_SIGN`, `DIAGNOSIS_ICD10`, `CONSULTATION_DIAGNOSIS`, `LABORATORY`, `LABORATORY_TEST`, `MEDICINE`, `GENERIC`, `PRESCRIPTION`, `BILLING`, `PROFESSIONAL_FEE`, `DOCTOR_FEE`, `PAYMENT`.

- Doctors are `USERS` rows with the `Doctor` role (no separate doctor table); their specializations use a many-to-many junction table.
- Records with clinical or billing history use **soft delete** (`Status = 'Inactive'`) so history is never orphaned.
- The `database/` folder also holds the incremental `.sql` scripts for upgrading an older database.

---

## ⚠️ Notes & Limitations

This is a **learning project**, not production software:

- Passwords are stored and compared as **plain text** (to match the class pattern). A real system should use `password_hash()` / `password_verify()`.
- There are **no server-side sessions**. The client sends `actingUserId` and the API checks it, which is a basic guard, not real authentication.
- **Online payment is a demo checkout.** No money moves and no card details are collected. A real gateway (PayMongo, GCash, Maya, Stripe) would plug in at `payMyBill()` in `api/billing.php`.
- CORS is open (`*`) and DB credentials use the XAMPP defaults. Change both before any real deployment.

---

## 👤 Author

**Jyrrel James N. Estrada**
BSIT Student · Cagayan de Oro City, Philippines
GitHub: [@Jyrrel](https://github.com/Jyrrel)
