# OpsPilot AI - API Documentation

## API Overview

**Base URL**: `https://api.opspilot.ai/api/v1`  
**Authentication**: Bearer Token (JWT)  
**Content-Type**: `application/json`  
**Rate Limit**: 1000 requests/hour per API key

---

## Authentication

### 1. Register User
```http
POST /auth/register
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe"
}
```

**Response** (201 Created):
```json
{
  "id": "user_123",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

### 2. Login
```http
POST /auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```

**Response** (200 OK):
```json
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "eyJhbGciOiJIUzI1NiIs...",
  "expiresIn": 3600
}
```

### 3. Refresh Token
```http
POST /auth/refresh
Content-Type: application/json

{
  "refreshToken": "eyJhbGciOiJIUzI1NiIs..."
}
```

---

## SOPs Management

### 1. Create SOP
```http
POST /sops
Authorization: Bearer {token}
Content-Type: application/json

{
  "workspaceId": "workspace_123",
  "title": "Customer Onboarding",
  "description": "Process for onboarding new customers",
  "steps": [
    {
      "order": 1,
      "title": "Send welcome email",
      "description": "Send personalized welcome email",
      "duration": 5
    },
    {
      "order": 2,
      "title": "Create account",
      "description": "Set up customer account in system",
      "duration": 10
    }
  ],
  "tags": ["onboarding", "customer"],
  "category": "Customer Success"
}
```

**Response** (201 Created):
```json
{
  "id": "sop_123",
  "workspaceId": "workspace_123",
  "title": "Customer Onboarding",
  "description": "Process for onboarding new customers",
  "steps": [...],
  "tags": ["onboarding", "customer"],
  "category": "Customer Success",
  "status": "draft",
  "createdAt": "2026-05-17T10:30:00Z",
  "updatedAt": "2026-05-17T10:30:00Z",
  "createdBy": "user_123",
  "version": 1
}
```

### 2. List SOPs
```http
GET /sops?workspaceId=workspace_123&limit=20&offset=0&sort=createdAt&order=desc
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "data": [
    {
      "id": "sop_123",
      "title": "Customer Onboarding",
      "status": "published",
      "stepsCount": 5,
      "createdAt": "2026-05-17T10:30:00Z"
    }
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 45
  }
}
```

### 3. Get SOP by ID
```http
GET /sops/sop_123
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "id": "sop_123",
  "title": "Customer Onboarding",
  "description": "...",
  "steps": [...],
  "status": "published",
  "version": 2,
  "createdAt": "2026-05-17T10:30:00Z",
  "updatedAt": "2026-05-17T11:45:00Z"
}
```

### 4. Update SOP
```http
PATCH /sops/sop_123
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "Customer Onboarding Process",
  "status": "published"
}
```

**Response** (200 OK):
```json
{
  "id": "sop_123",
  "title": "Customer Onboarding Process",
  "status": "published",
  "version": 3,
  "updatedAt": "2026-05-17T12:00:00Z"
}
```

### 5. Delete SOP
```http
DELETE /sops/sop_123
Authorization: Bearer {token}
```

**Response** (204 No Content)

---

## Voice Processing

### 1. Upload Voice Note
```http
POST /voice/upload
Authorization: Bearer {token}
Content-Type: multipart/form-data

- file: [binary audio file]
- workspaceId: workspace_123
- metadata: {"duration": 120, "language": "en"}
```

**Response** (202 Accepted):
```json
{
  "id": "voice_123",
  "status": "processing",
  "jobId": "job_456",
  "estimatedDuration": 30,
  "createdAt": "2026-05-17T12:00:00Z"
}
```

### 2. Get Voice Processing Status
```http
GET /voice/voice_123/status
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "id": "voice_123",
  "status": "completed",
  "transcription": "First step is to send the customer...",
  "confidence": 0.95,
  "completedAt": "2026-05-17T12:02:30Z"
}
```

### 3. Convert Voice to SOP
```http
POST /voice/voice_123/generate-sop
Authorization: Bearer {token}
Content-Type: application/json

{
  "title": "Auto-generated SOP",
  "category": "Customer Success"
}
```

**Response** (202 Accepted):
```json
{
  "sopId": "sop_789",
  "status": "generating",
  "jobId": "job_789",
  "estimatedDuration": 45
}
```

---

## Checklists

### 1. Create Checklist
```http
POST /checklists
Authorization: Bearer {token}
Content-Type: application/json

{
  "workspaceId": "workspace_123",
  "title": "Daily Onboarding Checklist",
  "description": "Daily tasks for onboarding",
  "items": [
    {
      "order": 1,
      "title": "Send welcome email",
      "completed": false
    },
    {
      "order": 2,
      "title": "Set up account",
      "completed": true
    }
  ],
  "sopId": "sop_123"
}
```

**Response** (201 Created):
```json
{
  "id": "checklist_123",
  "title": "Daily Onboarding Checklist",
  "items": [...],
  "progress": 50,
  "createdAt": "2026-05-17T12:00:00Z"
}
```

### 2. Update Checklist Item
```http
PATCH /checklists/checklist_123/items/item_1
Authorization: Bearer {token}
Content-Type: application/json

{
  "completed": true,
  "notes": "Completed at 2pm"
}
```

**Response** (200 OK):
```json
{
  "id": "item_1",
  "title": "Send welcome email",
  "completed": true,
  "notes": "Completed at 2pm",
  "updatedAt": "2026-05-17T14:00:00Z"
}
```

---

## Workflow Diagrams

### 1. Generate Workflow Diagram
```http
POST /workflows/generate
Authorization: Bearer {token}
Content-Type: application/json

{
  "sopId": "sop_123",
  "format": "svg",
  "includeDecisions": true
}
```

**Response** (202 Accepted):
```json
{
  "jobId": "job_workflow_123",
  "status": "processing",
  "format": "svg",
  "estimatedDuration": 20
}
```

### 2. Get Workflow Diagram
```http
GET /workflows/job_workflow_123
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "jobId": "job_workflow_123",
  "status": "completed",
  "svg": "<svg>...</svg>",
  "png": "https://s3.amazonaws.com/...",
  "completedAt": "2026-05-17T12:05:00Z"
}
```

---

## Documents

### 1. Export SOP
```http
POST /documents/export
Authorization: Bearer {token}
Content-Type: application/json

{
  "sopId": "sop_123",
  "format": "pdf",
  "includeChecklist": true,
  "includeWorkflow": true
}
```

**Response** (202 Accepted):
```json
{
  "jobId": "doc_export_123",
  "status": "processing",
  "format": "pdf",
  "estimatedDuration": 30
}
```

### 2. Get Document Download URL
```http
GET /documents/doc_export_123/download
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "url": "https://s3.amazonaws.com/documents/sop_123_export.pdf",
  "expiresIn": 3600,
  "fileName": "sop_123_export.pdf"
}
```

---

## Workspaces

### 1. Create Workspace
```http
POST /workspaces
Authorization: Bearer {token}
Content-Type: application/json

{
  "name": "Sales Team",
  "description": "Sales processes and procedures",
  "members": ["user_123", "user_456"]
}
```

**Response** (201 Created):
```json
{
  "id": "workspace_123",
  "name": "Sales Team",
  "description": "Sales processes and procedures",
  "owner": "user_123",
  "members": [...],
  "createdAt": "2026-05-17T12:00:00Z"
}
```

### 2. List Workspaces
```http
GET /workspaces
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "data": [
    {
      "id": "workspace_123",
      "name": "Sales Team",
      "role": "owner"
    }
  ]
}
```

---

## Analytics

### 1. Get Workspace Analytics
```http
GET /analytics/workspace/workspace_123?period=30d
Authorization: Bearer {token}
```

**Response** (200 OK):
```json
{
  "workspaceId": "workspace_123",
  "period": "30d",
  "metrics": {
    "totalSOPs": 45,
    "totalChecklists": 120,
    "totalUsers": 12,
    "activeUsers": 8,
    "documentsGenerated": 234
  },
  "trends": {
    "sopCreationRate": 2.1,
    "checklistCompletionRate": 0.87
  }
}
```

---

## Error Handling

### Error Response Format
```json
{
  "error": {
    "code": "INVALID_REQUEST",
    "message": "Missing required field: title",
    "details": {
      "field": "title",
      "reason": "required"
    }
  }
}
```

### Common Error Codes
| Code | Status | Description |
|------|--------|-------------|
| UNAUTHORIZED | 401 | Missing/invalid token |
| FORBIDDEN | 403 | Insufficient permissions |
| NOT_FOUND | 404 | Resource not found |
| INVALID_REQUEST | 400 | Invalid request format |
| RATE_LIMITED | 429 | Too many requests |
| INTERNAL_ERROR | 500 | Server error |

---

## Webhooks

### 1. Register Webhook
```http
POST /webhooks
Authorization: Bearer {token}
Content-Type: application/json

{
  "url": "https://example.com/webhooks/opspilot",
  "events": ["sop.created", "sop.updated", "voice.completed"],
  "active": true
}
```

**Response** (201 Created):
```json
{
  "id": "webhook_123",
  "url": "https://example.com/webhooks/opspilot",
  "events": ["sop.created", "sop.updated", "voice.completed"],
  "active": true,
  "createdAt": "2026-05-17T12:00:00Z"
}
```

### 2. Webhook Payload Example
```json
{
  "id": "evt_123",
  "event": "sop.created",
  "timestamp": "2026-05-17T12:00:00Z",
  "data": {
    "sopId": "sop_123",
    "title": "Customer Onboarding",
    "workspaceId": "workspace_123"
  }
}
```

---

## Rate Limiting

All API endpoints are rate-limited to **1000 requests/hour**.

**Headers**:
```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 999
X-RateLimit-Reset: 1621350000
```

---

## SDKs & Client Libraries

- **JavaScript/TypeScript**: `npm install @opspilot/sdk`
- **Python**: `pip install opspilot-sdk`
- **Go**: `go get github.com/opspilot/sdk-go`

---

**Last Updated**: May 17, 2026
