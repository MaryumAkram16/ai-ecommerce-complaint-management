🚀 AI Ecommerce Complaint Management System








An AI-powered complaint management system built with n8n automation, PostgreSQL, and Google Gemini AI.
This system automatically processes customer complaints, analyzes sentiment, routes issues to the correct department, and stores structured complaint data for analytics.

Designed for e-commerce platforms to streamline customer support and improve resolution times.

📚 Table of Contents

Introduction

Problem Statement

Solution

Key Features

Workflow Screenshot

Tech Stack

System Architecture

API Reference

Security & Protection

Setup Guide

Troubleshooting

Future Improvements

Contributing

License

📖 Introduction

Handling customer complaints efficiently is critical for e-commerce businesses.
Manual systems often cause delays, inconsistent responses, and lack of proper tracking.

This project provides a fully automated complaint processing system that integrates AI analysis, workflow automation, and database storage.

The system can:

Accept complaints via API

Validate input fields

Detect duplicate complaints

Analyze sentiment using AI

Suggest actions for support teams

Store complaints in PostgreSQL

Send alerts for urgent issues

⚠️ Problem Statement

Many companies struggle with complaint handling because:

Complaints are handled manually

High-priority complaints are not detected quickly

There is no structured complaint database

Support teams lack analytics and reporting

Duplicate complaints create confusion

These problems result in slow customer support and poor customer satisfaction.

💡 Solution

This project introduces a fully automated complaint management pipeline using n8n workflow automation.

The system workflow:

1️⃣ Receive complaint through webhook API
2️⃣ Validate request fields
3️⃣ Verify API key
4️⃣ Apply rate limiting protection
5️⃣ Detect duplicate complaints
6️⃣ Perform AI sentiment analysis
7️⃣ Suggest support actions
8️⃣ Route complaint to correct department
9️⃣ Store complaint in PostgreSQL
🔟 Send Slack alert for high-priority cases

⭐ Key Features
🤖 AI Complaint Analysis

Uses Google Gemini AI to analyze:

Complaint sentiment

Customer tone

Suggested operational actions

🔐 Security Layer

Includes:

API key authentication

Rate limiting

Duplicate complaint prevention

📊 Complaint Analytics

The system provides:

Complaint summary

Priority breakdown

Department classification

Resolution tracking

🚨 Priority Alerts

High-priority complaints trigger Slack notifications to support teams.

🗂️ Audit Logging

All complaint actions are recorded for compliance and debugging.

📸 Workflow Screenshot

Add your workflow image here.

Example:

docs/complaint_workflow.png


Example preview:

🛠 Tech Stack
Layer	Technology
Automation	n8n
AI Analysis	Google Gemini
Database	PostgreSQL
Notifications	Slack API
Email	Gmail API
Backend	Webhook API
🏗 System Architecture
Customer
   │
   ▼
API Webhook (n8n)
   │
   ▼
Rate Limiter
   │
   ▼
API Key Validation
   │
   ▼
Input Validation
   │
   ▼
Duplicate Complaint Detection
   │
   ▼
AI Sentiment Analysis
   │
   ▼
Department Routing
   │
   ▼
PostgreSQL Storage
   │
   ├── Slack Alert
   ├── Email Notification
   └── Audit Logs

🔌 API Reference
Submit Complaint

Endpoint

POST /webhook/eccomerce_complaints

Headers
x-api-key: YOUR_API_KEY

Request Body
{
  "name": "John Doe",
  "email": "john@email.com",
  "phone": "03001234567",
  "details": "I have not received my refund.",
  "type": "Refund Issue",
  "priority": "High",
  "complaint_id": "CMP123456"
}

Example Response
{
  "status": "success",
  "message": "Complaint submitted successfully",
  "complaint_code": "CMP123456"
}

Complaint Analytics API
POST /webhook/eccomerce_data


Returns analytics summary:

Total complaints

Pending complaints

Resolved complaints

Priority statistics

🔒 Security & Protection

Security measures implemented:

API key authentication

Rate limiting (10 requests/minute)

Input validation

Duplicate complaint detection

Secure database storage

Audit logs

⚙️ Setup Guide
1 Install n8n
npm install n8n -g


or use Docker.

2 Setup PostgreSQL

Create required tables:

ecommerce_complaints

audit_logs

eccomerce_services

3 Configure API Keys

Add credentials in n8n:

PostgreSQL credentials

Slack credentials

Gmail credentials

Google Gemini API key

4 Import Workflow

Import the workflow JSON file into n8n.

Workflows → Import from File

5 Activate Workflow

Enable workflow and test using Postman.

🧪 Troubleshooting
Webhook not working

Check if the workflow is activated in n8n.

Database connection error

Verify PostgreSQL credentials and host.

Slack alerts not sending

Confirm Slack API credentials are valid.

🚀 Future Improvements

Possible enhancements:

Admin dashboard

Complaint sentiment dashboard

ML-based complaint classification

Integration with CRM systems

Real-time complaint analytics

🤝 Contributing

Contributions are welcome.

Steps:

1️⃣ Fork repository
2️⃣ Create new feature branch
3️⃣ Commit changes
4️⃣ Submit pull request

📄 License

This project is licensed under the MIT License.
