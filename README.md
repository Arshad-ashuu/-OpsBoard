# 🚦 OpsBoard

<div align="center">

**Real-time Service Health Monitoring & Intelligent Alerting**

[![Bun](https://img.shields.io/badge/Bun-1.0-black?logo=bun)](https://bun.sh)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?logo=typescript)](https://www.typescriptlang.org/)
[![React](https://img.shields.io/badge/React-18-61dafb?logo=react)](https://reactjs.org/)
[![Docker](https://img.shields.io/badge/Docker-Ready-2496ed?logo=docker)](https://www.docker.com/)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-336791?logo=postgresql)](https://www.postgresql.org/)

*Production-grade uptime monitoring with zero-spam alerting*

[Features](#-features) • [Quick Start](#-quick-start) • [Architecture](#-architecture) • [API Reference](#-api-reference)

</div>

---

## 📖 Overview

OpsBoard is a **lightweight service monitoring platform** designed for DevOps teams who need reliable uptime tracking without the noise. It continuously monitors your services, provides real-time visibility through a sleek dashboard, and sends intelligent email alerts only when service states actually change.

### Why OpsBoard?

- ⚡ **Zero-Spam Alerts** — Emails sent only on state transitions (UP ↔ DOWN)
- 🎯 **Production-Ready** — Docker Compose setup, Prisma migrations, structured logging
- 🚀 **Fast & Modern** — Built with Bun runtime for blazing performance
- 📊 **Visual Insights** — Mini uptime graphs and real-time status tracking
- 🔧 **DevOps Friendly** — Easy to deploy, scale, and integrate

---

## ✨ Features

### 🔍 **Intelligent Monitoring**
- HTTP-based health checks with configurable intervals
- Multi-environment support (prod, staging, dev)
- Automatic failure tracking and recovery detection
- Configurable timeout and retry policies

### 📊 **Dashboard Experience**
- **Summary Cards** — Total services, UP count, DOWN count at a glance
- **Service Table** — Sortable, filterable list with real-time status
- **Uptime Visualization** — Mini graphs showing service health over time
- **Manual Refresh** — On-demand updates without auto-refresh spam

### 🔔 **Smart Alerting**
- State-change-only notifications (no repeated failure spam)
- Multi-recipient email management
- Both failure and recovery notifications
- Gmail SMTP with app password support
- Alert history tracking (coming soon)

### 🐳 **DevOps Excellence**
- Fully containerized with Docker Compose
- PostgreSQL with persistent volumes
- Prisma ORM with migration support
- Environment-based configuration
- Health check endpoints for orchestration

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         OpsBoard                             │
└─────────────────────────────────────────────────────────────┘

    ┌──────────────┐                ┌──────────────────┐
    │   Frontend   │◄───REST API────│   Backend API    │
    │   (React)    │                │   (Bun/Express)  │
    │   Port: 5173 │                │   Port: 4000     │
    └──────────────┘                └────────┬─────────┘
                                             │
                    ┌────────────────────────┼────────────────┐
                    │                        │                │
          ┌─────────▼─────────┐    ┌────────▼────────┐  ┌───▼────┐
          │   PostgreSQL DB   │    │  Health Checker  │  │  SMTP  │
          │  (Port: 5432)     │    │  (Cron Job)      │  │ Alerts │
          └───────────────────┘    └──────────────────┘  └────────┘
                  │
          Persistent Storage
```

### Data Flow

1. **Service Registration** → Frontend sends service details to API
2. **Health Checking** → Background job periodically pings service URLs
3. **State Detection** → Compares previous vs current status
4. **Alert Triggering** → Sends email only if state changed
5. **Dashboard Update** → Fetches latest service status from database

---

## 🛠️ Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Runtime** | Bun | Ultra-fast JavaScript runtime with built-in TypeScript |
| **Backend** | Express.js | RESTful API with middleware support |
| **Database** | PostgreSQL 15 | Relational database with ACID compliance |
| **ORM** | Prisma | Type-safe database client with migrations |
| **Frontend** | React 18 | Modern UI with hooks and functional components |
| **Styling** | Custom CSS | Dark theme with green accent colors |
| **Email** | Nodemailer | SMTP email delivery with Gmail support |
| **Container** | Docker Compose | Multi-container orchestration |

---

## 🚀 Quick Start

### Prerequisites

```bash
# Required
docker >= 20.10
docker-compose >= 2.0

# Optional (for local development)
bun >= 1.0
node >= 18.0
```

### 1️⃣ Clone & Setup

```bash
git clone https://github.com/your-username/opsboard.git
cd opsboard
```

### 2️⃣ Configure Environment

Create `opsboard-backend/.env`:

```env
# Database
DATABASE_URL=postgresql://opsuser:opspass@db:5432/opsboard

# SMTP Configuration (Gmail)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-16-digit-app-password

# Optional: Monitoring Configuration
HEALTH_CHECK_INTERVAL=60000  # 60 seconds
REQUEST_TIMEOUT=5000         # 5 seconds
```

> 🔐 **Gmail App Password Setup:**
> 1. Enable 2FA on your Google account
> 2. Go to Security → App Passwords
> 3. Generate password for "Mail" app
> 4. Use this 16-digit password in `.env`

### 3️⃣ Start the Stack

```bash
# Start backend + database
cd opsboard-backend
docker-compose up --build

# In another terminal, start frontend
cd opsboard-frontend
npm install
npm run dev
```

### 4️⃣ Run Database Migrations

```bash
# Execute inside running container
docker exec -it opsboard-app bash
bunx prisma db push
exit
```

### 5️⃣ Access the Application

- **Frontend:** http://localhost:5173
- **Backend API:** http://localhost:4000
- **Health Endpoint:** http://localhost:4000/health

---

## 🧪 Testing with Demo Services

### ✅ Always UP (for testing success scenarios)

```
https://httpstat.us/200
https://www.google.com
https://api.github.com/status
```

### ❌ Always DOWN (for testing failure scenarios)

```
https://httpstat.us/500
http://localhost:9999
https://invalid-domain-xyz-123.com
```

### 🔄 Intermittent (for testing state changes)

```
https://httpstat.us/200?sleep=5000  (slow response)
https://httpstat.us/503             (service unavailable)
```

---

## 📡 API Reference

### Services Management

#### Register New Service
```http
POST /services/register
Content-Type: application/json

{
  "name": "API Gateway",
  "environment": "prod",
  "url": "https://api.example.com/health"
}
```

#### Get All Services
```http
GET /dashboard/services
```

**Response:**
```json
{
  "services": [
    {
      "id": "1",
      "name": "API Gateway",
      "environment": "prod",
      "url": "https://api.example.com/health",
      "status": "UP",
      "failureCount": 0,
      "lastChecked": "2026-01-10T12:00:00Z"
    }
  ]
}
```

### Alert Email Management

#### List Alert Emails
```http
GET /alerts/emails
```

#### Add Alert Email
```http
POST /alerts/emails
Content-Type: application/json

{
  "email": "team@example.com"
}
```

#### Remove Alert Email
```http
DELETE /alerts/emails/:id
```

### System Health

```http
GET /health
```

---

## 🏛️ Database Schema

```prisma
model Service {
  id           String   @id @default(uuid())
  name         String
  environment  String
  url          String
  status       String   @default("UNKNOWN")
  failureCount Int      @default(0)
  lastChecked  DateTime?
  createdAt    DateTime @default(now())
  updatedAt    DateTime @updatedAt
}

model AlertEmail {
  id        String   @id @default(uuid())
  email     String   @unique
  createdAt DateTime @default(now())
}
```

---

## 🧠 Design Decisions

### Why Bun?
- **3x faster** than Node.js for I/O operations
- Built-in TypeScript support (no configuration needed)
- Native test runner and bundler included
- Smaller Docker image sizes

### Alert Strategy
- **State-Change-Only Alerts** prevent notification fatigue
- Email recipients stored in DB for dynamic management
- Failure count tracking enables escalation policies (future)

### Health Check Implementation
- Backend scheduler ensures checks run independently of UI
- Configurable intervals prevent API rate limiting
- Timeout handling prevents hanging requests
- Retry logic with exponential backoff (coming soon)

### Frontend Architecture
- No auto-refresh to reduce server load
- Manual refresh gives users control
- Optimistic UI updates for better UX
- Filter/sort without re-fetching data

---

## 🐳 Docker Configuration

### Production Deployment

```bash
# Build and run in detached mode
docker-compose up -d --build

# View logs
docker-compose logs -f app

# Stop services
docker-compose down

# Clean restart with fresh database
docker-compose down -v
docker-compose up --build
```

### Docker Compose Services

| Service | Port | Purpose |
|---------|------|---------|
| `app` | 4000 | Backend API server |
| `db` | 5432 | PostgreSQL database |

### Volume Persistence

```yaml
volumes:
  postgres_data:  # Persists database across container restarts
```

---

## 📈 Scaling Considerations

### Horizontal Scaling
- Stateless API design enables multi-instance deployment
- Use external cron service (e.g., Kubernetes CronJob) for health checks
- Implement Redis for shared state across instances

### Performance Optimization
- Add database indexes on `status`, `lastChecked`
- Implement connection pooling (Prisma handles this)
- Cache frequent queries with Redis
- Rate limit API endpoints

### High Availability
- Run multiple backend replicas behind load balancer
- Use PostgreSQL replication for database redundancy
- Implement circuit breakers for external service calls

---

## 🔮 Roadmap

### Phase 1 (Current)
- [x] HTTP health checks
- [x] Email alerts on state change
- [x] Multi-service dashboard
- [x] Docker deployment

### Phase 2 (In Progress)
- [ ] Slack/Discord webhook integration
- [ ] Alert cooldown periods
- [ ] Service-specific check intervals
- [ ] Alert history/audit logs

### Phase 3 (Planned)
- [ ] TCP/UDP port monitoring
- [ ] SSL certificate expiry tracking
- [ ] Response time trending
- [ ] Custom alert rules engine
- [ ] Multi-user authentication
- [ ] Team/organization support

### Phase 4 (Future)
- [ ] Metrics dashboard (Prometheus/Grafana)
- [ ] Mobile app (React Native)
- [ ] On-call rotation management
- [ ] Incident timeline builder

---

## 🎓 Learning & Interview Talking Points

This project demonstrates:

✅ **Backend Engineering**
- RESTful API design with Express
- Database modeling with Prisma ORM
- Background job scheduling
- Error handling and logging

✅ **DevOps Practices**
- Container orchestration with Docker Compose
- Database migrations in production
- Environment-based configuration
- Health check endpoints for monitoring

✅ **System Design**
- State management patterns
- Event-driven alerting system
- Scalability considerations
- Database schema optimization

✅ **Frontend Development**
- React hooks and functional components
- API integration patterns
- Real-time UI updates
- Responsive design principles

---

## 🤝 Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 👤 Author

**Arshad Mohammad**  
Backend Engineer & DevOps Enthusiast

- GitHub: [@your-username](https://github.com/your-username)
- Email: mohammadarshad01474@gmail.com
- LinkedIn: [Your Profile](https://linkedin.com/in/your-profile)

---

## 🙏 Acknowledgments

- Built with [Bun](https://bun.sh) - The blazing-fast JavaScript runtime
- Styled with inspiration from modern DevOps dashboards
- Icons and design patterns from [Lucide](https://lucide.dev)

---

<div align="center">

**⭐ Star this repository if you find it helpful!**

Made with ❤️ for the DevOps community

</div>
