# 🧾 Invoice App

A full-stack web application for creating, sending, and managing invoices. Built with **React + TypeScript** on the frontend and **AWS Lambda + DynamoDB** on the backend. Invoices are rendered as styled HTML, converted to PDF, and emailed directly to customers via Gmail SMTP.

---

## ✨ Features

- Create invoices with company branding, logo, and line items
- Live invoice preview before sending
- Auto-generate PDF from styled HTML template
- Email PDF directly to customer via Gmail SMTP
- Save and manage all invoices in DynamoDB
- Team access with AWS Cognito authentication
- Dashboard to view and track all invoices

---

## 🏗️ Architecture Overview

```
React + TypeScript (Frontend)
        │
        │  HTTP API (API Gateway)
        ▼
AWS Lambda (Node.js)
   ├── createInvoice   →  Generate PDF → Send Email → Save to DB
   ├── getInvoices     →  Return all invoices
   └── getInvoiceById  →  Return single invoice
        │
        ▼
AWS DynamoDB (Invoices Table)
```

---

## 🗂️ Project Structure

```
invoice-app/
├── frontend/                          # React + TypeScript (Vite)
│   ├── src/
│   │   ├── components/
│   │   │   ├── InvoiceForm/           # Main form UI
│   │   │   ├── InvoicePreview/        # Live HTML invoice preview
│   │   │   └── InvoiceTemplate/       # Styled HTML template (used for PDF)
│   │   ├── pages/
│   │   │   ├── Dashboard.tsx          # List of all invoices
│   │   │   └── CreateInvoice.tsx      # Form + preview page
│   │   ├── types/
│   │   │   └── invoice.ts             # Shared TypeScript interfaces
│   │   ├── api/
│   │   │   └── invoiceApi.ts          # API calls to Lambda
│   │   └── App.tsx
│   └── package.json
│
├── backend/                           # Lambda functions (Node.js)
│   ├── functions/
│   │   ├── createInvoice/             # Main handler
│   │   ├── getInvoices/               # List all invoices
│   │   └── getInvoiceById/            # Single invoice detail
│   ├── shared/
│   │   ├── dynamodb.ts                # DynamoDB client + helpers
│   │   ├── mailer.ts                  # Nodemailer + Gmail SMTP helper
│   │   └── pdfGenerator.ts            # HTML → PDF logic
│   └── template/
│       └── invoiceTemplate.html       # Styled invoice HTML template
│
└── infrastructure/
    └── template.yaml                  # AWS SAM / serverless config
```

---

## 🗃️ DynamoDB Table Schema

**Table name:** `Invoices`

| Attribute       | Type   | Key        | Notes                              |
|-----------------|--------|------------|------------------------------------|
| `invoiceId`     | String | Partition  | UUID, e.g. `INV-2026-0042`         |
| `createdAt`     | String | Sort       | ISO timestamp                      |
| `customerName`  | String |            |                                    |
| `customerEmail` | String |            | GSI for customer lookups           |
| `companyName`   | String |            | Your branding                      |
| `logoUrl`       | String |            | URL to company logo                |
| `lineItems`     | List   |            | `[{ desc, qty, unitPrice, total }]`|
| `subtotal`      | Number |            |                                    |
| `total`         | Number |            |                                    |
| `status`        | String |            | `draft` / `sent` / `paid`          |

> A **Global Secondary Index (GSI)** on `customerEmail` allows querying invoices by customer.

---

## ⚡ Lambda Functions

| Function         | Method | Route               | Description                              |
|------------------|--------|---------------------|------------------------------------------|
| `createInvoice`  | POST   | `/invoices`         | Generate PDF, send email, save to DB     |
| `getInvoices`    | GET    | `/invoices`         | Return list of all invoices              |
| `getInvoiceById` | GET    | `/invoices/{id}`    | Return a single invoice by ID            |

---

## 🔄 Invoice Creation Flow

1. User fills in the invoice form on the frontend
2. Live preview renders the styled HTML invoice template
3. On submit, a `POST /invoices` request is sent to API Gateway
4. Lambda `createInvoice` handler:
   - Injects form data into the HTML invoice template
   - Converts the HTML to a PDF using Puppeteer
   - Saves the invoice record to DynamoDB
   - Sends the PDF as an email attachment via Gmail SMTP (Nodemailer)
5. Frontend receives success response and redirects to Dashboard

---

## 🛠️ Tech Stack

### Frontend
| Library              | Purpose                    |
|----------------------|----------------------------|
| React + TypeScript   | UI framework               |
| Vite                 | Build tool                 |
| `react-hook-form`    | Form state management      |
| `@aws-amplify/auth`  | Cognito authentication     |
| `axios`              | HTTP requests to Lambda    |

### Backend (Lambda — Node.js)
| Library                        | Purpose                     |
|--------------------------------|-----------------------------|
| `@aws-sdk/client-dynamodb`     | DynamoDB access             |
| `@aws-sdk/lib-dynamodb`        | DynamoDB Document client    |
| `nodemailer`                   | Gmail SMTP email sending    |
| `puppeteer-core`               | HTML → PDF conversion       |
| `chrome-aws-lambda`            | Chromium for Lambda runtime |

### AWS Services
| Service         | Purpose                          | Cost          |
|-----------------|----------------------------------|---------------|
| Lambda          | Backend functions                | Always free   |
| DynamoDB        | Invoice database                 | Always free   |
| API Gateway     | HTTP API to expose Lambda routes | Always free*  |
| Cognito         | Team authentication              | Always free** |

> *API Gateway free tier: 1M requests/month for 12 months
> **Cognito free tier: up to 50,000 MAUs

---
