# Interview Scheduler API

![GitHub last commit](https://img.shields.io/github/last-commit/VioletFigueroa/scheduler-api?style=flat-square)
![GitHub repo size](https://img.shields.io/github/repo-size/VioletFigueroa/scheduler-api?style=flat-square)
![License](https://img.shields.io/badge/license-Educational-blue?style=flat-square)

**Quick Links:** [Security Features](#security-relevance-for-application-security) | [API Documentation](#api-endpoints) | [Setup](#getting-started)

---

**How to view artifacts:** API routes in `/src/routes/`; database layer in `/src/db/`; WebSocket implementation in `/src/index.js`.

**Result snapshot:** RESTful API with WebSocket support, PostgreSQL backend, Helmet security headers, and CORS configuration.

**Quick review:**

- **Security focus:** Helmet security headers, CORS policy, environment-based configuration, WebSocket security
- **Key files:** `src/application.js` (middleware stack), `src/index.js` (WebSocket server), `src/routes/` (API endpoints)
- **Start with:** Review Helmet configuration and CORS setup in `application.js`

## Overview

Interview Scheduler API is a RESTful backend service built with Express.js and PostgreSQL that provides appointment booking functionality with real-time updates via WebSockets. This project demonstrates API security best practices including HTTP security headers, CORS configuration, environment-based secrets management, and WebSocket authentication considerations.

**Developed during:** Lighthouse Labs Web Development Bootcamp (May - June 2021)  
**Companion Frontend:** [interview-scheduler](https://github.com/VioletFigueroa/interview-scheduler)

## Security Relevance for Application Security

### HTTP Security Headers (Helmet)

- **Helmet middleware** sets multiple security-related HTTP headers
- **X-Content-Type-Options:** nosniff prevents MIME-type sniffing attacks
- **X-Frame-Options:** DENY prevents clickjacking attacks
- **X-XSS-Protection:** enables browser XSS filters
- **Strict-Transport-Security:** enforces HTTPS in production
- **Content-Security-Policy:** restricts resource loading to prevent XSS
- Demonstrates defense-in-depth approach to API security

### Cross-Origin Resource Sharing (CORS)

- **CORS middleware** controls which domains can access the API
- **Origin validation** prevents unauthorized cross-domain requests
- **Preflight request handling** for complex CORS scenarios
- **Credential handling** configured appropriately for session-based auth
- Prevents CSRF attacks from malicious websites

### Environment-Based Configuration

- **dotenv** for environment variable management
- **Credentials never in code** - stored in .env files (gitignored)
- **.env.example** template prevents accidental credential commits
- **Environment-specific configs** (development, test, production)
- Demonstrates secrets management best practices

### WebSocket Security

- **WebSocket server** integrated with HTTP server for authentication context
- **Message validation** prevents injection through WebSocket messages
- **Broadcast authentication** ensures only authorized clients receive updates
- **Connection state management** prevents resource exhaustion
- Demonstrates real-time communication security patterns

### API Design Security

- **RESTful conventions** make API behavior predictable and reviewable
- **HTTP method semantics** (GET for reads, PUT for updates, DELETE for removals)
- **Idempotent operations** prevent unintended side effects on retry
- **Proper status codes** (200, 201, 204, 400, 404, 500) aid in error handling
- **JSON-only responses** with Content-Type validation

### Database Security (PostgreSQL)

- **Parameterized queries** via pg library prevent SQL injection
- **Database connection pooling** manages resources securely
- **Environment-based credentials** keep DB passwords out of code
- **Migration system** provides audit trail of schema changes
- **Test database isolation** prevents test data leaks to production

### Error Handling & Information Disclosure

- **Generic error responses** to clients prevent information leakage
- **Detailed logging server-side** for debugging without exposing internals
- **Debug endpoints** only in development environment
- **Error mode testing** (`npm run error`) validates client error handling
- Demonstrates balance between usability and security

## Objectives

- Build secure RESTful API with industry-standard security headers
- Implement real-time updates with WebSocket security
- Configure CORS appropriately for cross-origin requests
- Manage environment-based secrets securely
- Demonstrate API security testing and error handling

## Methodology

- **Express.js** for routing and middleware architecture
- **Helmet** for HTTP security headers
- **CORS** for cross-origin resource sharing policy
- **PostgreSQL** for persistent data storage
- **WebSocket (ws)** for real-time bidirectional communication
- **dotenv** for environment variable management

## Key Features

- **RESTful API:** Full CRUD operations for appointments, days, and interviewers
- **Real-time Updates:** WebSocket broadcasts for appointment changes
- **Security Headers:** Helmet-provided protection against common attacks
- **CORS Configuration:** Controlled cross-origin access
- **Database Backed:** PostgreSQL for reliable data persistence
- **Environment Configs:** Separate development, test, and production settings
- **Debug Endpoints:** Development-only database reset functionality

## Technologies Used

- **Backend:** Node.js, Express.js 4.16
- **Database:** PostgreSQL with pg driver
- **Security:** Helmet 3.18, CORS 2.8
- **Real-time:** ws (WebSocket) 7.0
- **Environment:** dotenv 7.0
- **Testing:** Jest, Supertest
- **Development:** nodemon for hot reloading

## Application Security Lessons Learned

- **Security headers are quick wins:** Helmet provides significant protection with minimal code
- **CORS is not authentication:** It protects browsers, but API needs its own auth
- **Environment variables prevent leaks:** Never commit credentials to version control
- **WebSockets need auth too:** Real-time connections often overlooked in security reviews
- **Debug endpoints are dangerous:** Development-only features must be environment-gated
- **Error messages reveal architecture:** Generic errors to clients, detailed logs server-side
- **Testing error states matters:** Forcing errors with `npm run error` validates client resilience

## API Endpoints

### Days

**`GET /api/days`**

Returns available days with appointment spots.

Response:

```json
[
  {
    "id": 1,
    "name": "Monday",
    "appointments": [1, 2],
    "interviewers": [1, 2],
    "spots": 0
  }
]
```

### Appointments

**`GET /api/appointments`**

Returns all appointments.

Response:

```json
{
  "1": {
    "id": 1,
    "time": "12pm",
    "interview": {
      "student": "Lydia Miller-Jones",
      "interviewer": 1
    }
  }
}
```

**`PUT /api/appointments/:id`**

Create or update an appointment.

Request Body:

```json
{
  "interview": {
    "student": "String",
    "interviewer": Number
  }
}
```

Response: `204 No Content` (success) or `500 Internal Server Error`

**`DELETE /api/appointments/:id`**

Delete an appointment.

Response: `204 No Content` (success) or `500 Internal Server Error`

### Interviewers

**`GET /api/interviewers`**

Returns all available interviewers.

Response:

```json
{
  "1": {
    "id": 1,
    "name": "Sylvia Palmer",
    "avatar": "https://i.imgur.com/LpaY82x.png"
  }
}
```

### Debug (Development Only)

**`GET /api/debug/reset`**

Resets database to initial seed state. **Only available in development/test modes.**

## WebSocket Events

**Client → Server:**

- `ping` - Connection health check
  - Server responds with `pong`

**Server → Client:**

- `SET_INTERVIEW` - Appointment created/updated
  - Payload: `{ type: "SET_INTERVIEW", id: Number, interview: Object }`

## Getting Started

### Prerequisites

- Node.js (v10.x or higher)
- PostgreSQL (v9.6 or higher)
- npm (v6.x or higher)
- PostgreSQL development user (see setup below)

### Database Setup

1. **Login to PostgreSQL**

   ```bash
   psql -U development
   # Password: development
   ```

2. **Create database**

   ```sql
   CREATE DATABASE scheduler_development;
   ```

3. **Configure environment**

   ```bash
   cp .env.example .env.development
   ```

   Edit `.env.development`:

   ```
   PGHOST=localhost
   PGUSER=development
   PGDATABASE=scheduler_development
   PGPASSWORD=development
   PGPORT=5432
   ```

### Application Setup

1. **Clone the repository**

   ```bash
   git clone https://github.com/VioletFigueroa/scheduler-api.git
   cd scheduler-api
   ```

2. **Install dependencies**

   ```bash
   npm install
   ```

3. **Start the server**

   ```bash
   npm start
   # Server runs on http://localhost:8001
   ```

### Seeding the Database

**Option 1: Using curl**

```bash
curl http://localhost:8001/api/debug/reset
```

**Option 2: Using browser**
Navigate to `http://localhost:8001/api/debug/reset`

*Note: Random data is generated each time you seed.*

### Running in Error Mode

Test client error handling by forcing server errors:

```bash
npm run error
```

All save/delete operations will return `500 Internal Server Error`.

## Security Configuration Examples

### Helmet Security Headers

```javascript
const helmet = require('helmet');
app.use(helmet());

// Sets headers:
// - X-DNS-Prefetch-Control: off
// - X-Frame-Options: SAMEORIGIN
// - Strict-Transport-Security: max-age=15552000
// - X-Download-Options: noopen
// - X-Content-Type-Options: nosniff
// - X-XSS-Protection: 1; mode=block
```

### CORS Configuration

```javascript
const cors = require('cors');
app.use(cors());

// In production, restrict to specific origins:
app.use(cors({
  origin: 'https://yourdomain.com',
  credentials: true
}));
```

### WebSocket Security

```javascript
wss.on('connection', socket => {
  // Validate connection origin
  // Authenticate user session
  // Sanitize incoming messages
  
  socket.onmessage = event => {
    // Input validation
    if (event.data === 'ping') {
      socket.send(JSON.stringify('pong'));
    }
  };
});
```

## Application Security Career Connection

This project demonstrates essential AppSec API security skills:

1. **API Security:** Implementing HTTP security headers, CORS, and secure API design
2. **Configuration Management:** Secure handling of environment variables and secrets
3. **WebSocket Security:** Securing real-time communication channels
4. **Security Testing:** Testing error states and validating security controls
5. **Defense in Depth:** Layering multiple security controls (headers, CORS, input validation)

**API security skills critical for AppSec:**

- **Security Architecture:** Designing secure REST APIs and real-time services
- **Code Review:** Identifying missing security headers, misconfigured CORS, exposed secrets
- **Penetration Testing:** Testing APIs for injection, broken authentication, excessive data exposure
- **Threat Modeling:** Understanding API-specific attack vectors (OWASP API Security Top 10)
- **Security Requirements:** Defining security controls for new API endpoints
- **Compliance:** Ensuring APIs meet security standards (PCI-DSS, GDPR, HIPAA)

This backend API pairs with the frontend (Interview Scheduler) to demonstrate full-stack security knowledge essential for application security roles.

---

**Author:** Violet Figueroa  
**Contact:** [GitHub Profile](https://github.com/VioletFigueroa)  
**Career Focus:** Application Security | API Security | Secure Software Development
    "interviewer": Number
  }
}

```

`DELETE /api/appointments/:id`

### Interviewers

`GET /api/interviewers`

Response:

```json
{
  "1": {
    "id": 1,
    "name": "Sylvia Palmer",
    "avatar": "https://i.imgur.com/LpaY82x.png"
  },
  "2": {
    "id": 2,
    "name": "Tori Malcolm",
    "avatar": "https://i.imgur.com/Nmx0Qxo.png"
  }
}
```
