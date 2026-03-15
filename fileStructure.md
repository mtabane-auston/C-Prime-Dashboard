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