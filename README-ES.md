<p align="center">
  <img src="https://akton-pahoy.github.io/PaHoy/photos/BigLogo.png" alt="Pa'Hoy Logo" width="200"/>
</p>

<h1 align="center">Pa'Hoy — Talento Local, Pa' Hoy Mismo</h1>

<p align="center">
  <strong>Un marketplace geolocalizado que conecta el talento local con clientes que necesitan servicios y productos — reconstruyendo comunidades a través del intercambio económico.</strong>
</p>

<p align="center">
  <a href="https://pahoy.online">Landing</a> •
  <a href="https://app.pahoy.online">App en Vivo</a> •
  <a href="https://api.pahoy.online">API</a> •
  <a href="https://akton-pahoy.github.io/PaHoy/">Presentación</a> •
  <a href="./README.md">🇺🇸 English</a>
</p>

---

## Tabla de Contenidos

- [Sobre el Proyecto](#sobre-el-proyecto)
- [El Problema que Resolvemos](#el-problema-que-resolvemos)
- [Qué Nos Diferencia](#qué-nos-diferencia)
- [Arquitectura](#arquitectura)
- [Stack Tecnológico](#stack-tecnológico)
- [Repositorios](#repositorios)
- [Funcionalidades Principales](#funcionalidades-principales)
- [Resumen de la API](#resumen-de-la-api)
- [Infraestructura y Despliegue](#infraestructura-y-despliegue)
- [Estrategias de Desarrollo](#estrategias-de-desarrollo)
- [Seguridad y Compliance](#seguridad-y-compliance)
- [Diseño](#diseño)
- [Equipo](#equipo)
- [Roadmap Futuro](#roadmap-futuro)

---

## Sobre el Proyecto

**Pa'Hoy** es una plataforma mobile-first diseñada para conectar el **talento local con clientes que necesitan servicios y productos** en su comunidad. Permitimos que emprendedores y trabajadores independientes — especialmente aquellos excluidos del mercado laboral tradicional — moneticen sus habilidades utilizando la **proximidad geográfica** como mecanismo principal de descubrimiento.

Nuestro objetivo es reconectar comunidades fragmentadas con el talento que existe dentro de ellas, fortaleciendo el tejido social a través del trabajo honesto y el intercambio económico local.

### Visión

Consolidarnos como la plataforma líder de servicios locales y herramientas para emprendedores, reconocida por fomentar la reconstrucción del tejido social y sanar comunidades a través de la reconexión, el trabajo honesto, el impulso al talento humano y el intercambio económico.

### Misión

Visibilizar el talento que existe dentro de las comunidades, conectarlo con quienes necesitan productos, servicios y soluciones personalizadas e inmediatas, impulsando el trabajo independiente y local, priorizando el apoyo a individuos y comunidades vulnerables.

---

## El Problema que Resolvemos

En muchas comunidades — particularmente en Latinoamérica — los trabajadores capacitados permanecen invisibles para potenciales clientes. El mercado informal (boca a boca, grupos de WhatsApp, búsquedas en redes sociales) domina, creando:

- **Alta fricción** al contratar prestadores de servicios desconocidos
- **Sin rendición de cuentas** ni sistemas de reseñas
- **Exclusión** de adultos mayores, personas con bajo conocimiento tecnológico y poblaciones vulnerables de la actividad económica
- **Comunidades fragmentadas** desconectadas del talento local

Pa'Hoy resuelve esto creando un **marketplace confiable basado en ubicación** donde cualquiera puede ofrecer sus habilidades y cualquiera puede encontrarlas cerca.

---

## Qué Nos Diferencia

| Característica | Pa'Hoy | Competidores Tradicionales |
|---------------|--------|---------------------------|
| **Marketplace dual** | Contrata Y ofrece servicios desde una sola cuenta | Generalmente unidireccional |
| **Descubrimiento por geolocalización** | Búsqueda por proximidad PostGIS (5km por defecto) | Listados a nivel ciudad |
| **UX inclusiva** | Diseñada para personas con bajo conocimiento tecnológico y adultos mayores | Interfaces complejas |
| **Validación social** | Reseñas y reputación impulsadas por la comunidad | Controladas por la plataforma |
| **Economía circular** | Los trabajadores también son clientes, creando flujo económico local | Modelo de servicio lineal |

---

## Arquitectura

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

## Stack Tecnológico

### Backend
| Tecnología | Propósito |
|-----------|-----------|
| Python 3.12 | Runtime |
| Django 6.0 | Framework web |
| Django REST Framework | Capa de API |
| GeoDjango + PostGIS | Consultas geoespaciales |
| PostgreSQL 18.3 | Base de datos |
| django-storages | Almacenamiento de medios en S3 |

### Frontend
| Tecnología | Propósito |
|-----------|-----------|
| React 19 + TypeScript 5.9 | Framework de UI |
| Vite 8 | Herramientas de build |
| Tailwind CSS 4 | Estilos |
| React Aria Components | Accesibilidad |
| Leaflet + React Leaflet | Mapas interactivos |
| Motion (Framer Motion) | Animaciones |
| Capacitor | Bundle APK Android |

### Infraestructura
| Tecnología | Propósito |
|-----------|-----------|
| AWS CDK (TypeScript) | Infraestructura como Código |
| CloudFront | CDN + HTTPS |
| S3 | Hosting estático + medios |
| Elastic Beanstalk | Cómputo para la API |
| RDS | Base de datos administrada |
| Route 53 | DNS |
| GitHub Actions + OIDC | CI/CD (sin credenciales estáticas) |

---

## Repositorios

| Repositorio | Descripción | Enlace |
|------------|-------------|--------|
| **pahoy-backend** | API REST Django con descubrimiento geoespacial de talentos | [GitHub](https://github.com/AKTon-PaHoy/pahoy-backend) |
| **pahoy-front** | SPA mobile-first con React 19 | [GitHub](https://github.com/AKTon-PaHoy/pahoy-front) |
| **pahoy-infra** | Definiciones de infraestructura AWS CDK | [GitHub](https://github.com/AKTon-PaHoy/pahoy-infra) |

---

## Funcionalidades Principales

- **Registro y Autenticación** — Auth basada en tokens con soporte de sesión
- **Perfiles Duales** — Cada usuario recibe automáticamente un perfil de Cliente y uno de Talento
- **Gestión de Habilidades** — Crear, actualizar y buscar habilidades por ubicación
- **Marketplace de Gigs** — Publicar y descubrir ofertas de servicios con búsqueda por distancia
- **Ciclo de Vida de Contratos** — Flujo Propuesta → Activo → Concluido con gestión de estados
- **Sistema de Reseñas** — Reseñas cruzadas entre clientes y talentos por contrato
- **Chat en Tiempo Real** — Mensajería basada en salas vinculada a gigs y contratos
- **Mapas Interactivos** — Selección de ubicación y descubrimiento de servicios con Leaflet
- **Búsqueda Geolocalizada** — Consultas por proximidad con PostGIS (radio de 5km por defecto)
- **Carga de Imágenes** — Almacenamiento de medios respaldado por S3 para perfiles y gigs
- **APK Android** — Experiencia nativa empaquetada con Capacitor

---

## Resumen de la API

El backend expone **41 rutas y 42 operaciones** en 8 grupos de API:

| Grupo | Prefijo | Operaciones | Descripción |
|-------|---------|:-----------:|-------------|
| Autenticación | `/api/auth/` | 11 | Registro, login, gestión de tokens, ubicación |
| Perfiles | `/api/profile/` | 2 | Obtener y actualizar perfil |
| Habilidades | `/api/skills/` | 7 | CRUD + búsqueda geoespacial |
| Gigs | `/api/gigs/` | 7 | CRUD + búsqueda geoespacial |
| Contratos | `/api/contracts/` | 6 | Gestión del ciclo de vida de propuestas |
| Reseñas | `/api/reviews/` | 4 | Sistema de reseñas cruzadas |
| Talentos | `/api/talents/` | 1 | Búsqueda de talentos por ubicación |
| Chat | `/api/chat/` | 4 | Creación de salas + sincronización de mensajes |

Contrato completo de la API disponible en el repo backend: [`api-spec.json`](https://github.com/AKTon-PaHoy/pahoy-backend/blob/main/api-spec.json) (OpenAPI 3.0)

---

## Infraestructura y Despliegue

### Ambientes en Vivo

| Dominio | Servicio | Propósito |
|---------|----------|-----------|
| `pahoy.online` | CloudFront + S3 | Landing page |
| `app.pahoy.online` | CloudFront + S3 | SPA React |
| `api.pahoy.online` | CloudFront + Elastic Beanstalk | API Django |

### Servicios AWS Utilizados

- **Amazon S3** — Hosting estático (landing + SPA) y almacenamiento de medios
- **Amazon RDS** — PostgreSQL con PostGIS (db.t4g.micro, 20GB gp3)
- **Amazon EC2 / Elastic Beanstalk** — Cómputo de API (t3.micro, instancia única)
- **Amazon CloudFront** — CDN, terminación HTTPS, HTTP/2+3
- **Amazon Route 53** — Gestión de DNS
- **AWS IAM + OIDC** — Despliegues de GitHub Actions sin credenciales
- **AWS Secrets Manager** — Gestión de credenciales de base de datos
- **Amazon CloudWatch + Budgets + SNS** — Monitoreo de costos y alertas

### Pipeline CI/CD

Los tres repositorios se despliegan automáticamente al hacer push a `main` mediante **GitHub Actions** usando **federación OIDC** (sin credenciales estáticas de AWS):

- **Backend** → Paquete de despliegue a Elastic Beanstalk
- **Frontend** → Build + sync a S3 + invalidación de CloudFront
- **Landing** → Sync a S3 + invalidación de CloudFront

---

## Estrategias de Desarrollo

- Metodología de desarrollo **Agile**
- Desarrollo asistido por **IA Agéntica** con **Kiro** (IDE con IA)
- **Infraestructura como Código** via AWS CDK (TypeScript)
- **CI/CD** con GitHub Actions + OIDC (cero credenciales estáticas)
- **Angular Conventional Commits** para historial de git estandarizado
- **OpenAPI Specification 3.0** para documentación de la API
- **Aislamiento de entornos** y separación de responsabilidades
- Capacidades de **auto-escalado** y balanceo de cargas AWS

---

## Seguridad y Compliance

### Estrategia de Protección de Datos

Pa'Hoy aborda la protección de datos de forma proactiva, partiendo de la Ley 21.719 de Chile como base (la más estricta de Latinoamérica), lo que proporciona ~90% de cumplimiento automático con otras regulaciones regionales y casi-cumplimiento con GDPR.

**Principios clave:**
- Todos los datos loggeables se almacenan cifrados
- La ubicación se trata como dato personal en todos los territorios
- Un Oficial de Protección de Datos (DPO) designado entre los miembros fundadores
- Marco de compliance adaptable para expansión internacional

### Seguridad Técnica

- Autenticación basada en tokens (tokens DRF)
- HTTPS en todas partes via CloudFront + certificados ACM
- Base de datos en subnet aislada (sin acceso público)
- Despliegues basados en OIDC (sin credenciales AWS almacenadas)
- Buckets S3 con Origin Access Control (privados por defecto)
- Protección WAFv2 Web ACL en la distribución de la SPA

---

## Diseño

Todas las decisiones de UI/UX siguen un sistema de diseño dedicado en Figma creado por nuestro equipo de diseño:

**[Ver Diseño en Figma](https://www.figma.com/design/21azV7Zha4GR2qbyTrCOBk/Pa--Hoy---App-Design)**

Herramientas de diseño: Figma, After Effects, Premiere, Illustrator, Claude Design

---

## Equipo

| Miembro | Rol |
|---------|-----|
| **Fares Akel** | Desarrollo Frontend y DevOps |
| **Rizcalla Akel** | Desarrollo Backend y Gestión de Base de Datos |
| **Guillermo Herrera** | Diseño, Producción e Identidad Visual |
| **Aidnes Sánchez** | QA / Estratega |
| **Juan Hernández** | InfraSec / Compliance Advisor |

**Nombre del equipo:** AKTon

---

## Roadmap Futuro

- Sistema de procesamiento de pagos y facturación
- Flujo de verificación KYC (Conoce a tu Cliente)
- Blog y base de conocimiento
- Portal de atención al cliente
- Perfiles públicos
- Chat en tiempo real basado en WebSocket
- Expansión a nuevos mercados geográficos

---

<p align="center">
  Construido con ☕ y <a href="https://kiro.dev">Kiro</a> para el Hackathon AWS + Kiro 2026
</p>
