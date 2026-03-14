# AI Ecommerce Complaint Management System

An automated AI-powered complaint handling system built with n8n, PostgreSQL, and Google Gemini AI.
The system receives customer complaints through an API, analyzes them using AI, routes them to the correct department, stores them in a database, and sends alerts for high-priority issues.

It is designed for e-commerce platforms to streamline complaint handling, improve response time, and provide actionable insights.

Table of Contents

Introduction

Problem Statement

Solution

Key Features

Workflow Screenshot

Tech Stack

System Architecture

API Reference

Security Features

Setup Guide

Troubleshooting

Contributing

License

Introduction

Customer complaints are a critical part of any e-commerce business.
Handling them efficiently requires validation, categorization, prioritization, and routing to the correct support teams.

This project provides an automated complaint management workflow using n8n automation, AI analysis, and database-driven processing.

The system can:

Accept complaints via API

Validate and normalize input

Detect duplicates

Analyze complaint sentiment using AI

Suggest resolution actions

Store data in PostgreSQL

Notify teams for urgent issues

Problem Statement

Traditional complaint management systems face several challenges:

Manual processing delays

Lack of prioritization for urgent issues

Poor tracking of complaint history

No automated analysis of customer sentiment

Limited visibility for management teams

These issues lead to slow resolutions, unhappy customers, and inefficient support operations.

Solution

This system introduces an AI-driven complaint processing workflow that automates the entire lifecycle of a complaint.

The workflow:

Receives complaint data through an API webhook

Validates required fields

Prevents duplicate complaints

Performs sentiment analysis using AI

Suggests operational actions

Routes complaints to the correct department

Stores complaint data in PostgreSQL

Sends alerts for high-priority complaints

Records audit logs for tracking

Key Features
AI Complaint Analysis

Uses Google Gemini AI to detect:

Complaint sentiment (Negative / Neutral / Positive)

Suggested action for support teams

Automated Validation

Ensures all required fields are present:

Name

Email

Phone

Complaint details

Complaint type

Priority

Duplicate Detection

Prevents users from submitting the same complaint multiple times.

Rate Limiting

Protects the API from abuse by limiting requests per IP.

High Priority Alerts

Automatically sends Slack alerts for urgent complaints.

Audit Logging

All complaint actions are recorded for monitoring and compliance.

Complaint Summary API

Provides a dashboard-ready summary endpoint for analytics.

Workflow Screenshot

(Add your n8n workflow screenshot here)

Example:

docs/workflow.png

Tech Stack

Automation

n8n

AI

Google Gemini AI

Database

PostgreSQL

Notifications

Slack API

Gmail API

Backend

Webhook-based API

System Architecture
Client Application
        │
        ▼
Webhook API (n8n)
        │
        ▼
Rate Limiting
        │
        ▼
API Key Authentication
        │
        ▼
Input Validation
        │
        ▼
Duplicate Complaint Check
        │
        ▼
AI Complaint Analysis
        │
        ▼
Department Routing
        │
        ▼
PostgreSQL Storage
        │
        ├── Slack Alert (High Priority)
        ├── Email Confirmation
        └── Audit Logging

API Reference
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


Response

Your complaint has been submitted. Your complaint code is CMP123456

Get Complaint Summary
POST /webhook/eccomerce_data


Returns analytics summary including:

Total complaints

Pending vs resolved

Priority breakdown

Department grouping

Security Features

The system includes several security layers:

API key authentication

Rate limiting (10 requests per minute per IP)

Input validation

Duplicate complaint protection

Secure database storage

Audit logging

Setup Guide
1 Install n8n
npm install n8n -g


or use Docker.

2 Setup PostgreSQL

Create required tables:

ecommerce_complaints

audit_logs

eccomerce_services

3 Configure Credentials

Add credentials in n8n:

PostgreSQL

Slack OAuth

Gmail OAuth

Google Gemini API

4 Import Workflow

Import the provided n8n workflow JSON file into your n8n instance.

5 Activate Workflow

Enable the workflow and test using:

POST /webhook/eccomerce_complaints

Troubleshooting
Database Error

Check PostgreSQL credentials and connection.

API Key Error

Verify the x-api-key header matches the configured key.

Slack Alerts Not Sending

Ensure Slack OAuth credentials are correctly configured.

Contributing

Contributions are welcome.

Steps:

1 Fork the repository
2 Create a feature branch
3 Submit a pull request

License

This project is licensed under the MIT License.
