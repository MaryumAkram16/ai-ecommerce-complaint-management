# AI Ecommerce Complaint Management & Resolution System

An intelligent complaint management system designed to automatically collect, validate, analyze, and route customer ecommerce complaints using AI-powered workflows and automation.

The system uses **n8n workflow automation**, **AI sentiment analysis**, and **structured PostgreSQL databases** to efficiently manage complaint handling, reduce response time, and improve customer satisfaction.

---

## Table of Contents

- [Introduction](#introduction)
- [Problem Statement](#problem-statement)
- [Solution](#solution)
- [Workflow Screenshot](#workflow-screenshot)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Database Schema](#database-schema)
- [Workflow Logic](#workflow-logic)
- [API Reference](#api-reference)
- [Error Responses](#error-responses)
- [Setup Guide](#setup-guide)
- [Troubleshooting](#troubleshooting)
- [Future Improvements](#future-improvements)
- [License](#license)

---

## Introduction

The **AI Ecommerce Complaint Management System** automates the full lifecycle of customer complaint handling — from submission to resolution — for ecommerce businesses.

Instead of manual complaint tracking, this system uses **n8n automation workflows and AI analysis** to:

- validate and deduplicate complaints
- classify complaint types by department
- detect sentiment
- recommend resolution actions
- store complaints and audit logs
- alert teams on high-priority issues
- notify customers via email

This allows businesses to respond to issues **faster, more consistently, and with full traceability**.

---

## Live Demo

## Demo Video

[![Watch the demo](https://img.youtube.com/vi/IuP8mQ4_Gz4/maxresdefault.jpg)](https://youtu.be/IuP8mQ4_Gz4)

## Problem Statement

Traditional ecommerce complaint management systems often face several issues:

- **Manual Processing**
  Complaints must be reviewed and categorized manually by staff.

- **Slow Resolution Time**
  Teams take longer to analyze complaints and assign actions without automation.

- **Lack of Prioritization**
  Urgent high-priority complaints get mixed in with low-priority issues and are missed.

- **No Duplicate Detection**
  The same complaint can be submitted multiple times, wasting support team resources.

- **Poor Data Analysis**
  Businesses struggle to extract sentiment and actionable insights from raw complaint text.

- **Inefficient Tracking**
  Customers have limited visibility into complaint status and receive no automated confirmation.

---

## Solution

The system automates ecommerce complaint management using **n8n workflow automation and Google Gemini AI analysis**.

When a complaint is submitted via the API:

1. The request is rate-limited and validated for API key authentication
2. Required fields are validated
3. The database is checked for duplicate complaints
4. AI analyzes sentiment and recommends a resolution action
5. The responsible department is identified automatically
6. The complaint is saved to PostgreSQL with full AI output
7. An audit log entry is recorded
8. High-priority complaints trigger an immediate Slack alert
9. A confirmation email is sent to the customer
10. The customer receives a complaint code in the API response

This creates a **fully automated, end-to-end complaint handling pipeline**.

---

## Workflow Screenshot
**AI Ecommerce complaint management N8N workflow**
![Workflow Screenshot](https://raw.githubusercontent.com/MaryumAkram16/ai-ecommerce-complaint-management/main/ecom-comp.PNG?raw=true)

---

## Key Features

- **Rate Limiting**
  Limits each IP to a maximum of 10 requests per minute to prevent abuse.

- **API Key Authentication**
  All requests require a valid API key in the `x-api-key` header.

- **Field Validation**
  Ensures all required fields (`name`, `email`, `phone`, `details`, `type`, `priority`) are present before processing.

- **Duplicate Complaint Detection**
  Checks the database for existing complaints matching the same name, phone, and email — returns a 409 if found.

- **AI-Powered Sentiment Analysis**
  Google Gemini AI determines whether the complaint sentiment is **Positive**, **Neutral**, or **Negative**.

- **Suggested Resolution Actions**
  The AI generates a specific, actionable next step for resolving the complaint (e.g., "Issue refund after verifying transaction details.").

- **Department Routing**
  Automatically looks up the responsible department based on the complaint type from the `eccomerce_services` table.

- **Database Storage**
  Saves all complaint data including AI output, department assignment, and timestamps to the `ecommerce_complaints` table.

- **Audit Logging**
  Records every complaint action in the `audit_logs` table for full traceability.

- **High-Priority Slack Alerts**
  Sends an immediate alert to the `all-complaint-management` Slack channel when priority is `High`.

- **Customer Email Confirmation**
  Sends a confirmation email to the customer with their complaint ticket ID via Gmail.

- **Complaints Summary Endpoint**
  A separate webhook endpoint (`/eccomerce_data`) fetches and aggregates all complaints with breakdowns by status, priority, category, and department.

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Workflow Automation | n8n |
| Database | PostgreSQL / Supabase |
| AI Model | Google Gemini 2.5 Flash Lite |
| Email | Gmail (OAuth2) |
| Messaging | Slack (OAuth2) |
| Backend Logic | JavaScript (n8n Code nodes) |
| API Integration | Webhooks |
| Data Processing | JSON |

---

## System Architecture

The system follows an **event-driven automation architecture** with two main workflows.

### Workflow 1: Complaint Submission (`POST /eccomerce_complaints`)

#### 1. Security Layer
Incoming requests pass through two security checks:

- **Rate Limiting** — in-memory check per IP, max 10 requests/min → `429` if exceeded
- **API Key Validation** — validates `x-api-key` header against expected key → `401` if invalid

#### 2. Validation Layer
- Field validation ensures `name`, `email`, `phone`, `details`, `type`, and `priority` are all present → `400` if missing
- Duplicate check queries the database for matching `name`, `phone`, and `email` → `409` if found

#### 3. Intelligence Layer
Google Gemini AI analyzes the complaint text and returns:

- **Sentiment**: Positive, Neutral, or Negative
- **Suggested Action**: A specific, practical resolution step

#### 4. Department Routing Layer
Queries the `eccomerce_services` table using the complaint `type` to identify the responsible department ID.

#### 5. Data Layer
All data — complaint fields, AI output, and department — is merged and inserted into the `ecommerce_complaints` PostgreSQL table. A corresponding audit log is written to `audit_logs`.

#### 6. Notification Layer
- **Gmail** sends a confirmation email to the customer with ticket ID
- **Slack** sends a high-priority alert if `priority = High`
- **API response** returns the complaint code to the caller

---

### Workflow 2: Complaints Summary (`POST /eccomerce_data`)

Fetches all complaints from PostgreSQL, aggregates them, and computes a structured summary including:

- Total complaint count
- Status breakdown (Pending / Resolved)
- Priority counts (Low / Medium / High)
- Grouping by category
- Grouping by department

Returns the full summary along with raw complaint data and a timestamp.

---

## Database Schema

### `ecommerce_complaints`

```sql
CREATE TABLE public.ecommerce_complaints (
  complaint_id   BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
  complaint_code TEXT,
  name           TEXT,
  email          TEXT,
  phone          TEXT,
  type           TEXT,
  category       TEXT,
  details        TEXT,
  priority       TEXT DEFAULT 'Medium',
  sentiment      TEXT DEFAULT 'Neutral',
  status         TEXT DEFAULT 'Pending',
  suggested_actions TEXT,
  department_id  TEXT,
  created_at     TIMESTAMP DEFAULT NOW(),
  updated_at     TIMESTAMP DEFAULT NOW()
);
```

### `audit_logs`

```sql
CREATE TABLE public.audit_logs (
  audit_id       BIGINT GENERATED BY DEFAULT AS IDENTITY PRIMARY KEY,
  complaint_code TEXT NOT NULL,
  action         TEXT NOT NULL,
  performed_by   TEXT NOT NULL,
  details        TEXT,
  created_at     TIMESTAMP DEFAULT NOW()
);
```

### `eccomerce_services`

```sql
CREATE TABLE public.eccomerce_services (
  service_id   TEXT PRIMARY KEY,
  service_name TEXT,
  name         TEXT
);
```

---

## Workflow Logic

### Complaint Submission Flow

```
POST /eccomerce_complaints
        │
        ▼
  Rate Limit Check (10 req/min per IP)
        │
   [Exceeded] ──► 429 Too Many Requests
        │
        ▼
  API Key Validation (x-api-key header)
        │
   [Invalid] ──► 401 Unauthorized
        │
        ▼
  Field Validation (name, email, phone, details, type, priority)
        │
   [Missing] ──► 400 Validation Error
        │
        ▼
  Duplicate Check (DB query by name + phone + email)
        │
   [Duplicate] ──► 409 Duplicate Complaint
        │
        ▼
  Prepare & Finalize Complaint Fields
        │
    ┌───┴──────────────┐
    ▼                  ▼
  AI Analysis     Department Lookup
  (Gemini)        (eccomerce_services)
    │                  │
    └───────┬──────────┘
            ▼
     Merge: AI + Fields + Department
            │
            ▼
     Save Complaint to DB
            │
     ┌──────┴──────┐
     ▼             ▼
  Send Email    Check DB Success
  (Gmail)            │
              [Error] ──► Log Failed ──► 500 Server Error
                    │
              [Success]
                    │
           ┌────────┴──────────┐
           ▼                   ▼
     Record Audit Log   Check Priority Level
                               │
                        [High] ─► Send Slack Alert
                               │
                        Generate Confirmation Message
                               │
                        Send Response to User
```

---

## API Reference

### Submit a Complaint

**POST** `/webhook/eccomerce_complaints`

**Headers:**

| Header | Required | Description |
|--------|----------|-------------|
| `x-api-key` | Yes | API authentication key |
| `Content-Type` | Yes | `application/json` |

**Request Body:**

```json
{
  "name": "John Smith",
  "email": "john@example.com",
  "phone": "03001234567",
  "details": "I have not received my refund after 30 days.",
  "type": "Refund Issue",
  "priority": "High",
  "complaint_id": "CMP123456"
}
```

**Field Descriptions:**

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | string | Yes | Customer full name |
| `email` | string | Yes | Customer email address |
| `phone` | string | Yes | Customer phone number |
| `details` | string | Yes | Full complaint description |
| `type` | string | Yes | Complaint type (must match a service in `eccomerce_services`) |
| `priority` | string | Yes | `Low`, `Medium`, or `High` |
| `complaint_id` | string | No | Custom complaint reference code |

**Success Response (200):**

```text
Your complaint has been submitted. Your complaint code is CMP123456
```

---

### Get Complaints Summary

**POST** `/webhook/eccomerce_data`

No request body or authentication required.

**Success Response (200):**

```json
{
  "summary": {
    "total": 42,
    "pending": 18,
    "resolved": 24,
    "lowPriority": 10,
    "mediumPriority": 20,
    "highPriority": 12,
    "byCategory": {
      "Refund Issue": 15,
      "Delivery Problem": 12
    },
    "byDepartment": {
      "dept-001": 20,
      "dept-002": 22
    }
  },
  "data": [...],
  "timestamp": "2025-03-15T10:00:00.000Z",
  "recordCount": 42
}
```

---

## Error Responses

| Code | Error Code | Description |
|------|-----------|-------------|
| `400` | `VALIDATION_ERROR` | One or more required fields are missing |
| `401` | `UNAUTHORIZED` | Invalid or missing API key |
| `409` | `DUPLICATE_COMPLAINT` | A complaint with the same name, phone, and email already exists |
| `429` | `RATE_LIMIT_EXCEEDED` | Too many requests from this IP (max 10/min) |
| `500` | `DATABASE_ERROR` | Internal server error while saving the complaint |

---

## Setup Guide

### Prerequisites

- n8n instance (self-hosted or cloud)
- PostgreSQL database (Supabase or standalone)
- Google Gemini API key
- Gmail OAuth2 credentials
- Slack OAuth2 credentials

### Steps

1. **Import the workflow** into your n8n instance by copying the JSON and using **Import from JSON**.

2. **Create the database tables** using the SQL schemas provided in the [Database Schema](#database-schema) section.

3. **Seed the `eccomerce_services` table** with your complaint types and department mappings:

```sql
INSERT INTO eccomerce_services (service_id, service_name, name)
VALUES ('dept-001', 'Refund Issue', 'Refunds Team');
```

4. **Configure credentials** in n8n for:
   - PostgreSQL (`Postgres account`)
   - Google Gemini (`Google Gemini(PaLM) Api account`)
   - Gmail OAuth2 (`Gmail account 2`)
   - Slack OAuth2 (`Slack account`)

5. **Update the API key** in the `API Key Validation` node — replace `hoAsB5yO7F90KXhQ` with your own secure key.

6. **Update the Slack channel ID** in the `Send High Priority Alert` node to match your target channel.

7. **Activate both workflows** in n8n.

8. **Test** by sending a POST request to the webhook URL with the required headers and body.

---

## Troubleshooting

| Issue | Likely Cause | Fix |
|-------|-------------|-----|
| `401 Unauthorized` | Missing or wrong `x-api-key` header | Check the key matches what is set in the `API Key Validation` node |
| `409 Duplicate Complaint` | Same name/phone/email already in DB | Use a different email or clear test data from the database |
| `400 Validation Error` | Missing required field in request body | Ensure all six required fields are present |
| `429 Too Many Requests` | More than 10 requests/min from same IP | Wait 60 seconds and retry |
| `500 Database Error` | DB connection issue or schema mismatch | Verify PostgreSQL credentials and confirm all tables exist |
| Slack alert not firing | Wrong channel ID or missing Slack credentials | Confirm the channel ID and that Slack OAuth2 credentials are linked |
| Email not sending | Gmail OAuth2 not authorized | Re-authenticate the Gmail credential in n8n |
| AI output missing | Gemini API key expired or quota exceeded | Refresh the Google Gemini API key |

---

## Future Improvements

- **Dashboard Integration** — Connect the `/eccomerce_data` endpoint to a live admin dashboard with charts for complaint metrics.
- **Status Update Webhook** — Add a workflow to allow support teams to update complaint status via API.
- **Customer Portal** — Enable customers to look up complaint status using their complaint code.
- **Multi-language Sentiment** — Extend AI analysis to support complaint text in multiple languages.
- **SLA Monitoring** — Add automated alerts when high-priority complaints exceed a defined resolution time.
- **Cluster-safe Rate Limiting** — Replace in-memory rate limiting with Redis for multi-instance deployments.
- **WhatsApp / SMS Notifications** — Add customer notifications via WhatsApp or SMS in addition to email.

---

## License

This project is licensed under the MIT License.
