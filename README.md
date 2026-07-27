<p align="center">
  <img src="https://pahoy-images-bucket.s3.us-east-1.amazonaws.com/pahoy-logo.png" alt="Pa'Hoy Logo" width="200"/>
</p>

<h1 align="center">Pa'Hoy — Local Talent, Right Now</h1>

<p align="center">
  <strong>A location-aware marketplace that connects local talent with clients who need services and products — rebuilding communities through economic exchange.</strong>
</p>

<p align="center">
  <a href="https://pahoy.online">Landing</a> •
  <a href="https://app.pahoy.online">Live App</a> •
  <a href="https://api.pahoy.online">API</a> •
  <a href="https://akton-pahoy.github.io/PaHoy/">Presentation</a> •
  <a href="./README-ES.md">🇪🇸 Español</a>
</p>

---

## Table of Contents

- [About the Project](#about-the-project)
- [The Problem We Solve](#the-problem-we-solve)
- [What Makes Us Different](#what-makes-us-different)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Repositories](#repositories)
- [Key Features](#key-features)
- [API Overview](#api-overview)
- [Infrastructure & Deployment](#infrastructure--deployment)
- [Development Strategies](#development-strategies)
- [Security & Compliance](#security--compliance)
- [Design](#design)
- [Team](#team)
- [Future Roadmap](#future-roadmap)

---

## About the Project

**Pa'Hoy** is a mobile-first platform designed to connect **local talent with clients who need services and products** delivered in their community. We enable entrepreneurs and independent workers — especially those excluded from the traditional labor market — to monetize their skills by leveraging **geographic proximity** as the core discovery mechanism.

Our goal is to reconnect fragmented communities with the talent that exists within them, strengthening the social fabric through honest work and local economic exchange.

### Vision

Become the leading platform for local services and entrepreneurship tools, recognized for rebuilding communities through reconnection, honest work, and direct economic impact on individuals.

### Mission

Make visible the talent within communities, connect it with those who need personalized and immediate solutions, drive independent local work, and prioritize support for vulnerable individuals and communities.

---

## The Problem We Solve

In many communities — particularly in Latin America — skilled workers remain invisible to potential clients. The informal market (word-of-mouth, WhatsApp groups, social media searches) dominates, creating:

- **High friction** when hiring unknown service providers
- **No accountability** or review systems
- **Exclusion** of elderly, low-tech-literacy, and vulnerable populations from economic activity
- **Fragmented communities** disconnected from local talent

Pa'Hoy solves this by creating a **trusted, location-first marketplace** where anyone can offer their skills and anyone can find them nearby.

---

## What Makes Us Different

| Feature | Pa'Hoy | Traditional Competitors |
|---------|--------|------------------------|
| **Dual marketplace** | Both hire AND offer services from one account | Usually one-sided |
| **Geographic-first discovery** | PostGIS proximity search (default 5km) | City-wide listings |
| **Inclusive UX** | Designed for low-tech-literacy and elderly users | Complex interfaces |
| **Social validation** | Community-driven reviews and reputation | Platform-controlled |
| **Circular economy** | Workers are also clients, creating local economic flow | Linear service model |

---

## Architecture

```
                ┌──────────────────────────────────────────────┐
                │           pahoy.online (Route 53)            │
                └───┬──────────────────┬──────────────────┬────┘
                    │                  │                  │
               A/AAAA alias      A/AAAA alias       A/AAAA alias
               pahoy.online      app.pahoy.online   api.pahoy.online
                    │                  │                  │
                    ▼                  ▼                  ▼
          ┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
          │   CloudFront     │ │   CloudFront     │ │   CloudFront     │
          │   (Landing)      │ │   (SPA App)      │ │   (API)          │
          └────────┬─────────┘ └────────┬─────────┘ └────────┬─────────┘
                   │ OAC                │ OAC                │ HTTP origin
                   ▼                    ▼                    ▼
          ┌──────────────────┐ ┌──────────────────┐ ┌──────────────────┐
          │   S3 Bucket      │ │   S3 Bucket      │ │ Elastic Beanstalk│
          │  (Landing HTML)  │ │  (SPA React)     │ │  (Django API)    │
          │                  │ │                  │ │  t3.micro        │
          └──────────────────┘ └──────────────────┘ └────────┬─────────┘
                                                             │
                                                             ▼
                                                    ┌──────────────────┐
                                                    │  RDS PostgreSQL   │
                                                    │  + PostGIS        │
                                                    │  db.t4g.micro     │
                                                    │ (isolated subnet) │
                                                    └──────────────────┘

          ┌──────────────────┐     ┌──────────────────────────────────────┐
          │  GitHub OIDC      │     │  Billing: CloudWatch + Budget + SNS  │
          │  (CI/CD deploy)   │     └──────────────────────────────────────┘
          └──────────────────┘
```

---

## Tech Stack

### Backend
| Technology | Purpose |
|-----------|---------|
| Python 3.12 | Runtime |
| Django 6.0 | Web framework |
| Django REST Framework | API layer |
| GeoDjango + PostGIS | Geospatial queries |
| PostgreSQL 18.3 | Database |
| django-storages | S3 media storage |

### Frontend
| Technology | Purpose |
|-----------|---------|
| React 19 + TypeScript 5.9 | UI framework |
| Vite 8 | Build tooling |
| Tailwind CSS 4 | Styling |
| React Aria Components | Accessibility |
| Leaflet + React Leaflet | Interactive maps |
| Motion (Framer Motion) | Animations |
| Capacitor | Android APK bundle |

### Infrastructure
| Technology | Purpose |
|-----------|---------|
| AWS CDK (TypeScript) | Infrastructure as Code |
| CloudFront | CDN + HTTPS |
| S3 | Static hosting + media |
| Elastic Beanstalk | API compute |
| RDS | Managed database |
| Route 53 | DNS |
| GitHub Actions + OIDC | CI/CD (no static credentials) |

---

## Repositories

| Repository | Description | Link |
|-----------|-------------|------|
| **pahoy-backend** | Django REST API with geospatial talent discovery | [GitHub](https://github.com/AKTon-PaHoy/pahoy-backend) |
| **pahoy-front** | React 19 mobile-first SPA | [GitHub](https://github.com/AKTon-PaHoy/pahoy-front) |
| **pahoy-infra** | AWS CDK infrastructure definitions | [GitHub](https://github.com/AKTon-PaHoy/pahoy-infra) |

---

## Key Features

- **User Registration & Authentication** — Token-based auth with session support
- **Dual Profiles** — Every user gets both a Client and Talent profile automatically
- **Skills Management** — Create, update, and search skills by location
- **Gig Marketplace** — Post and discover service offerings with distance-based search
- **Contract Lifecycle** — Proposal → Active → Concluded workflow with status management
- **Review System** — Cross-reviews between clients and talents per contract
- **Real-time Chat** — Room-based messaging tied to gigs and contracts
- **Interactive Maps** — Leaflet-powered location selection and service discovery
- **Geolocation Search** — PostGIS-powered proximity queries (default 5km radius)
- **Image Upload** — S3-backed media storage for profiles and gigs
- **Android APK** — Capacitor-bundled native app experience

---

## API Overview

The backend exposes **41 paths and 42 operations** across 8 API groups:

| Group | Prefix | Operations | Description |
|-------|--------|:----------:|-------------|
| Authentication | `/api/auth/` | 11 | Register, login, token management, location |
| Profiles | `/api/profile/` | 2 | Retrieve and update profile |
| Skills | `/api/skills/` | 7 | CRUD + geospatial search |
| Gigs | `/api/gigs/` | 7 | CRUD + geospatial search |
| Contracts | `/api/contracts/` | 6 | Proposal lifecycle management |
| Reviews | `/api/reviews/` | 4 | Cross-review system |
| Talents | `/api/talents/` | 1 | Location-based talent search |
| Chat | `/api/chat/` | 4 | Room creation + message sync |

Full API contract available in the backend repo: [`api-spec.json`](https://github.com/AKTon-PaHoy/pahoy-backend/blob/main/api-spec.json) (OpenAPI 3.0)

---

## Infrastructure & Deployment

### Live Environments

| Domain | Service | Purpose |
|--------|---------|---------|
| `pahoy.online` | CloudFront + S3 | Landing page |
| `app.pahoy.online` | CloudFront + S3 | React SPA |
| `api.pahoy.online` | CloudFront + Elastic Beanstalk | Django API |

### AWS Services Used

- **Amazon S3** — Static hosting (landing + SPA) and media storage
- **Amazon RDS** — PostgreSQL with PostGIS (db.t4g.micro, 20GB gp3)
- **Amazon EC2 / Elastic Beanstalk** — API compute (t3.micro, single-instance)
- **Amazon CloudFront** — CDN, HTTPS termination, HTTP/2+3
- **Amazon Route 53** — DNS management
- **AWS IAM + OIDC** — Keyless GitHub Actions deployments
- **AWS Secrets Manager** — Database credential management
- **Amazon CloudWatch + Budgets + SNS** — Cost monitoring and alerting

### CI/CD Pipeline

All three repositories deploy automatically on push to `main` via **GitHub Actions** using **OIDC federation** (no static AWS credentials):

- **Backend** → Elastic Beanstalk deployment package
- **Frontend** → Build + S3 sync + CloudFront invalidation
- **Landing** → S3 sync + CloudFront invalidation

---

## Development Strategies

- **Agile development** methodology
- **AI-assisted development** with **Kiro** (agentic AI IDE)
- **Infrastructure as Code** via AWS CDK (TypeScript)
- **CI/CD** with GitHub Actions + OIDC (zero static credentials)
- **Angular Conventional Commits** for standardized git history
- **OpenAPI 3.0 Specification** for API documentation
- **Environment isolation** and separation of concerns
- **AWS auto-scaling** load balancing capabilities

---

## Security & Compliance

### Data Protection Strategy

Pa'Hoy approaches data protection proactively, starting with Chile's Law 21.719 as the baseline (the strictest in Latin America), which provides ~90% automatic compliance with other regional regulations and near-GDPR compliance.

**Key principles:**
- All loggable data is stored encrypted
- Location is treated as personal data across all territories
- A designated Data Protection Officer (DPO) among founding members
- Adaptable compliance framework for international expansion

### Technical Security

- Token-based authentication (DRF tokens)
- HTTPS everywhere via CloudFront + ACM certificates
- Database in isolated subnet (no public access)
- OIDC-based deployments (no stored AWS credentials)
- S3 buckets with Origin Access Control (private by default)
- WAFv2 Web ACL protection on SPA distribution

---

## Design

All UI/UX decisions follow a dedicated Figma design system created by our design team:

**[View Figma Design](https://www.figma.com/design/21azV7Zha4GR2qbyTrCOBk/Pa--Hoy---App-Design)**

Design tools used: Figma, After Effects, Premiere, Illustrator, Claude Design

---

## Team

| Member | Role |
|--------|------|
| **Fares Akel** | Frontend Development & DevOps |
| **Rizcalla Akel** | Backend Development & Database Management |
| **Guillermo Herrera** | Design, Production & Visual Identity |
| **Aidnes Sánchez** | QA, Product Research & Security |
| **Juan Hernández** | QA & Product Research |

**Team name:** AKTon

---

## Future Roadmap

- Payment processing and billing system
- KYC (Know Your Customer) verification workflow
- Blog and knowledge base
- Customer service portal
- Public profiles
- WebSocket-based real-time chat
- Expanded geographic markets

---

<p align="center">
  Built with ☕ and <a href="https://kiro.dev">Kiro</a> for the AWS + Kiro Hackathon 2026
</p>
