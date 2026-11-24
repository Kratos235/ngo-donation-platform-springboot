# HopeBridge — NGO Donation Management Platform

HopeBridge is a full-stack web application designed to connect non-governmental organisations (NGOs) with donors. NGOs can publish funding or item requirements, while donors can discover those needs and record contributions. The platform tracks progress against each requirement so that both sides can see how much support has been received.

## Features

### NGO workflow
- Log in through the NGO account flow.
- Create donation campaigns with a title, description and target amount.
- View campaigns created by the NGO.
- Monitor current contributions against the target using progress indicators.
- Delete campaigns.

### Donor workflow
- Log in through the donor account flow.
- Browse available monetary campaigns and view the associated NGO.
- See current and target amounts and the campaign's funding progress.
- Submit a monetary contribution to a selected need.

### Backend capabilities
- REST endpoints for authentication, NGOs, donors, needs and contributions.
- CRUD operations for needs and contribution records.
- JPA entity relationships between NGOs, needs, donors and contributions.
- Contribution processing updates a need's accumulated monetary amount or received item quantity.
- A need can be marked completed when its configured monetary or item target is reached.
- A progress endpoint returns received/target values, percentage and status.

> **Implementation note:** The backend data model and service logic support both money and item contributions. The current donor-facing React flow primarily exposes monetary donations; item-pledge UI should not be assumed to be complete.

## Technology stack

| Layer | Technologies |
|---|---|
| Frontend | React 19, Vite, React Router, Axios |
| Backend | Java 17, Spring Boot, Spring Web MVC |
| Persistence | Spring Data JPA, Hibernate, MySQL |
| Security components | Spring Security, JWT (JJWT) |
| Build | Maven |

## Architecture

The application is organised into two projects:

- `ngo-frontend/` — React single-page application.
- `ngo-platform/` — Spring Boot REST API.

Typical request flow:

```text
React pages
   |
   | Axios / HTTP
   v
Spring Boot REST controllers
   |
   v
Service layer (business logic)
   |
   v
Spring Data JPA repositories
   |
   v
MySQL database
```

The backend source is separated into controller, service, entity, repository, DTO, JWT and security packages.

## Main domain entities

- **Ngo** — NGO name, email, password, contact details and description.
- **Donor** — donor identity and contact details.
- **Need** — a requirement posted by an NGO; can represent money or items and stores target/received progress.
- **Contribution** — a donor's monetary or item contribution, associated with a need and donor.

Relationships:
- One NGO can publish multiple needs.
- One need can receive multiple contributions.
- One donor can make multiple contributions.

## API overview

The backend controllers expose the following route groups:

| Route | Purpose |
|---|---|
| `POST /api/auth/login` | Authenticate a user and return an authentication response |
| `/api/ngos` | NGO operations |
| `/api/donors` | Donor operations |
| `/api/needs` | Create, list, retrieve, update and delete needs |
| `GET /api/needs/{id}/progress` | Retrieve progress for a need |
| `/api/contributions` | Create, list, retrieve and delete contributions |

Check the controller classes for the exact supported HTTP methods and request fields.

## Run locally

### Prerequisites
- Java 17
- Maven (or use the included Maven Wrapper)
- MySQL
- Node.js and npm

### 1. Configure MySQL

Create a database:

```sql
CREATE DATABASE ngo_platform;
```

Configure the backend connection in `ngo-platform/src/main/resources/application.properties` or environment-specific configuration. Do not commit real credentials.

Recommended configuration pattern:

```properties
spring.datasource.url=${DB_URL:jdbc:mysql://localhost:3306/ngo_platform}
spring.datasource.username=${DB_USERNAME:root}
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=update
server.port=8080
```

### 2. Start the backend

From the backend directory:

```bash
cd ngo-platform
./mvnw spring-boot:run
```

On Windows:

```powershell
cd ngo-platform
.\mvnw.cmd spring-boot:run
```

The API is configured to run on port `8080`.

### 3. Start the frontend

In another terminal:

```bash
cd ngo-frontend
npm install
npm run dev
```

The frontend API client currently uses `http://localhost:8080/api`. Run the frontend and backend on the expected local ports, or update the API base URL for your environment.

## Current limitations and security notes

This repository represents an academic/portfolio project and should not be used to process real donations without additional work.

- The checked-in backend security configuration currently permits all requests. JWT-related classes are present, but the filter is not wired into the active security filter chain in the inspected configuration.
- Role-based authorization and server-side ownership checks need to be enforced before treating NGO and donor routes as protected.
- Add request validation, consistent exception handling, transaction boundaries and automated tests.
- The current frontend donation form sends a contribution request; it is not a payment-gateway integration. A successful API contribution record should not be described as a real payment.

## Suggested next improvements

1. Complete and test JWT authentication and role-based authorization.
2. Add secure registration, password hashing and role-aware route protection.
3. Add donor contribution history and item-pledge screens.
4. Add integration tests for campaign creation, contributions and progress calculations.
5. Add screenshots and a short demo video to the repository.

## Repository structure

```text
ngo-donation-platform-springboot/
├── ngo-platform/       # Spring Boot backend
└── ngo-frontend/       # React + Vite frontend
```

---

Built as a full-stack project to explore REST API development, layered backend architecture, relational data modelling and frontend/backend integration.
