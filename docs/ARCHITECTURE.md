# OpsPilot AI - Architecture & System Design

## System Architecture Overview

```
┌─────────────────────────────────────────────────────────────┐
│                        Client Layer                          │
├─────────────────────────────────────────────────────────────┤
│  Web App (Next.js)  │  Admin Dashboard  │  Mobile App (RN)  │
└──────────┬──────────┴──────────┬─────────┴────────┬──────────┘
           │                     │                   │
           └─────────────────────┴───────────────────┘
                         │
                    API Gateway
                  (Express.js Port 4000)
                         │
        ┌────────────────┼────────────────┐
        │                │                │
    ┌───▼────┐      ┌───▼────┐      ┌───▼────┐
    │  Auth   │      │  Voice │      │   SOP  │
    │Service  │      │ Engine │      │Generator│
    └────┬────┘      └────┬───┘      └────┬───┘
        │                 │              │
    ┌───▼─────────────────▼──────────────▼────┐
    │          Message Queue (RabbitMQ)       │
    └───┬──────────────────────────────────────┘
        │
    ┌───▼─────────────────────────────────────┐
    │    Data Layer (PostgreSQL + Redis)      │
    │    Search (Elasticsearch)               │
    │    Storage (S3/MinIO)                   │
    └─────────────────────────────────────────┘
```

## Microservices Architecture

### API Gateway
- **Port**: 4000
- **Framework**: Express.js
- **Responsibilities**:
  - Request routing
  - Authentication/Authorization
  - Rate limiting
  - API versioning
  - Error handling

### Auth Service
- **Port**: 4001
- **Technologies**: Node.js, JWT, OAuth2
- **Responsibilities**:
  - User registration/login
  - Session management
  - Token generation
  - SSO/SAML
  - Permission management

### Voice Engine
- **Port**: 4002
- **Technologies**: Python, OpenAI, ffmpeg
- **Responsibilities**:
  - Audio processing
  - Speech-to-text transcription
  - Audio validation
  - Format conversion

### SOP Generator
- **Port**: 4003
- **Technologies**: Node.js, GPT-4, LangChain
- **Responsibilities**:
  - Process steps extraction
  - SOP formatting
  - Template application
  - Content enhancement

### Checklist Generator
- **Port**: 4004
- **Technologies**: Node.js, GPT-4
- **Responsibilities**:
  - Task extraction
  - Checklist creation
  - Task dependencies
  - Progress tracking

### Workflow Engine
- **Port**: 4005
- **Technologies**: Node.js, Graphviz, D3.js
- **Responsibilities**:
  - Diagram generation
  - Process visualization
  - Flow optimization
  - Activity tracking

### Document Service
- **Port**: 4006
- **Technologies**: Node.js, LibreOffice, Pandoc
- **Responsibilities**:
  - Document storage
  - Format conversion (PDF, Word, HTML)
  - Versioning
  - Sharing/Permissions

### Analytics Service
- **Port**: 4007
- **Technologies**: Node.js, Elasticsearch, Kibana
- **Responsibilities**:
  - Usage tracking
  - Performance metrics
  - User behavior analysis
  - Business intelligence

### Notification Service
- **Port**: 4008
- **Technologies**: Node.js, SendGrid, Twilio
- **Responsibilities**:
  - Email notifications
  - SMS alerts
  - Slack integration
  - Webhook dispatching

## Data Flow Architecture

### Voice Note to SOP Flow
```
1. User uploads voice file
   ↓
2. API Gateway validates request
   ↓
3. Voice Engine processes audio
   ├─ Audio transcription (OpenAI)
   ├─ Text processing
   └─ Output stored in S3
   ↓
4. SOP Generator processes text
   ├─ Step extraction (GPT-4)
   ├─ Content enhancement
   ├─ Template application
   └─ Output stored in Database
   ↓
5. Document Service prepares output
   ├─ PDF generation
   ├─ Word export
   └─ Web display
   ↓
6. Notification sent to user
   ├─ Email confirmation
   ├─ Dashboard update
   └─ Analytics logged
```

## Technology Stack

### Frontend
- **Web**: React 18+ / Next.js 13+
- **Mobile**: React Native
- **UI**: Tailwind CSS, shadcn/ui
- **State**: Redux Toolkit, TanStack Query
- **Testing**: Jest, React Testing Library

### Backend
- **Runtime**: Node.js 18+ LTS
- **API**: Express.js, Apollo GraphQL
- **Language**: TypeScript 5+
- **Package Manager**: npm Workspaces
- **Process**: PM2 (production)

### AI/ML
- **LLM**: OpenAI GPT-4
- **Speech**: OpenAI Whisper
- **Vector DB**: Pinecone (future)
- **Orchestration**: LangChain, Llama Index

### Database
- **Primary**: PostgreSQL 14+
- **Cache**: Redis 7+
- **Search**: Elasticsearch 8+
- **Time-series**: InfluxDB (future)

### Infrastructure
- **Cloud**: AWS (us-east-1, us-west-2, eu-west-1)
- **Container**: Docker 20.10+
- **Orchestration**: Kubernetes (EKS)
- **CI/CD**: GitHub Actions
- **Monitoring**: DataDog, Sentry
- **Logging**: CloudWatch, ELK Stack

### Storage
- **Object Storage**: AWS S3
- **Local Dev**: MinIO
- **File Sync**: Git-LFS (future)

### Message Queue
- **Primary**: RabbitMQ
- **Alternative**: AWS SQS
- **Events**: Event Emitter Pattern

## Deployment Architecture

### Development Environment
```
Local Machine
├── Docker Compose (5+ services)
├── PostgreSQL (local)
├── Redis (local)
├── Elasticsearch (local)
├── RabbitMQ (local)
└── MinIO (S3 compatible)
```

### Staging Environment
```
AWS VPC
├── ECS/EKS Cluster
├── ALB (Application Load Balancer)
├── RDS PostgreSQL (Multi-AZ)
├── ElastiCache Redis
├── OpenSearch
├── S3 (object storage)
└── CloudFront (CDN)
```

### Production Environment
```
AWS Multi-Region
├── us-east-1 (Primary)
│   ├── EKS Cluster
│   ├── RDS PostgreSQL (Multi-AZ)
│   ├── S3 + CloudFront
│   └── Route53
├── us-west-2 (Failover)
│   ├── EKS Cluster (standby)
│   ├── RDS PostgreSQL (replica)
│   └── S3 (replica)
├── eu-west-1 (GDPR)
│   ├── EKS Cluster
│   └── RDS PostgreSQL (EU resident)
└── Global
    ├── CloudFront (edge locations)
    ├── Route53 (DNS failover)
    └── Shield (DDoS protection)
```

## Security Architecture

### Authentication Flow
```
User → OAuth/Email/SSO
       ↓
API Gateway (JWT validation)
       ↓
Auth Service (token verification)
       ↓
Request Handler (role-based access)
       ↓
Resource Access (data-level permissions)
```

### Data Security
- **Encryption in Transit**: TLS 1.3
- **Encryption at Rest**: AES-256 (AWS KMS)
- **Database**: Encrypted volumes
- **Sensitive Fields**: Field-level encryption
- **API Keys**: HashiCorp Vault

### Network Security
- **WAF**: AWS WAF rules
- **DDoS**: AWS Shield Advanced
- **VPC**: Private subnets for services
- **Security Groups**: Least privilege
- **VPN**: Corporate access only

## Scalability Architecture

### Horizontal Scaling
- **Stateless Services**: Easy replication
- **Load Balancer**: ALB with auto-scaling
- **Database**: Read replicas for queries
- **Cache**: Redis cluster mode
- **Queue**: RabbitMQ clustering

### Vertical Scaling
- **Resource Limits**: Kubernetes resource requests/limits
- **Auto-scaling**: Metrics-based (CPU, memory, requests)
- **Burst Capacity**: AWS Spot instances

### Performance Optimization
- **CDN**: CloudFront for static assets
- **Cache Layers**: Redis, CloudFront, browser cache
- **Database Indexing**: Strategic indexes
- **Query Optimization**: N+1 prevention
- **API Compression**: Gzip, Brotli

## Monitoring & Observability

### Metrics
- **Infrastructure**: CPU, memory, disk, network
- **Application**: Response time, throughput, errors
- **Business**: Active users, MRR, features used
- **Tool**: DataDog, Prometheus

### Logging
- **Centralized**: CloudWatch, ELK Stack
- **Structured**: JSON logging
- **Levels**: Debug, Info, Warn, Error
- **Retention**: 7 days (hot), 30 days (archive)

### Tracing
- **Distributed**: Jaeger, AWS X-Ray
- **Sampling**: 10% of production traffic
- **Latency**: Identify bottlenecks

### Alerting
- **Critical**: Page on-call engineer
- **High**: Slack notification
- **Medium**: Daily digest
- **Low**: Weekly report

## Disaster Recovery

### Backup Strategy
- **Database**: Daily snapshots (30-day retention)
- **S3**: Versioning enabled, cross-region replication
- **RTO**: 1 hour
- **RPO**: 1 hour

### Failover Process
```
Primary Region Down
   ↓
Health check detects failure (30 seconds)
   ↓
Route53 failover to secondary region
   ↓
RDS failover to replica
   ↓
All services rerouted
   ↓
Estimated downtime: 5-10 minutes
```

## Development Workflow

### Local Development
```bash
# 1. Start services
docker-compose up -d

# 2. Install dependencies
npm install

# 3. Run migrations
npm run db:migrate

# 4. Start dev servers
npm run dev
```

### CI/CD Pipeline
```
Git Push
  ↓
GitHub Actions (triggered)
  ↓
Lint & Format Check
  ↓
Unit Tests
  ↓
Integration Tests
  ↓
Build Docker Images
  ↓
Security Scan (Trivy)
  ↓
Push to Docker Registry
  ↓
Deploy to Staging
  ↓
Smoke Tests
  ↓
Approval Required
  ↓
Deploy to Production
  ↓
Health Checks
```

## API Design

### RESTful Conventions
```
POST   /api/v1/sops                 Create SOP
GET    /api/v1/sops                 List SOPs
GET    /api/v1/sops/:id             Get SOP
PATCH  /api/v1/sops/:id             Update SOP
DELETE /api/v1/sops/:id             Delete SOP
```

### GraphQL (Future)
```graphql
query {
  user(id: "123") {
    sops {
      id
      title
      steps
    }
  }
}
```

### API Versioning
- **Version in URL**: `/api/v1/`, `/api/v2/`
- **Backward Compatibility**: Support 2 versions
- **Deprecation**: 6-month notice

## Database Schema Highlights

### Core Tables
- `users` - User accounts
- `workspaces` - Team workspaces
- `sops` - Procedures/SOPs
- `checklists` - Task checklists
- `documents` - Generated documents
- `audit_logs` - Activity tracking

### Relationships
```
User → [1:N] Workspaces
Workspace → [1:N] SOPs, Checklists, Documents
SOP → [1:N] Versions, Comments, Tags
```

---

**Last Updated**: May 17, 2026
**Maintained by**: Engineering Team
