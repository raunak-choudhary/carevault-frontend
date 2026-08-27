# CareVault Frontend

The React client for **CareVault**, an AI-powered platform that brings medical records, appointments, medications, and health metrics into one place, with a conversational assistant that works across all of them.

This repository holds the frontend only. The Flask API lives in [carevault-backend](https://github.com/raunak-choudhary/carevault-backend).

## What CareVault Is

Managing personal healthcare usually means juggling separate systems: one portal for records, another for scheduling, a third for prescriptions, and nothing that connects them. CareVault was built as a single platform where those functions share a data layer, and where an AI chat assistant can answer questions that span all of them rather than being bolted onto one feature.

The platform also supports caregivers, so one account can manage healthcare for dependents as well as for the account holder.

## Evaluated Against a Commercial Product

CareVault was not just built, it was tested. A within-subjects user study with 10 NYU graduate students compared it directly against **JaneApp**, an established healthcare practice management platform, measuring task efficiency, usability, and cognitive workload.

| Measure | CareVault | JaneApp | Significance |
| --- | --- | --- | --- |
| Task completion time | 83.2s (SD 14.5) | 135.8s (SD 22.1) | t(9)=5.87, p<0.001, d=1.64 |
| Errors per task | 0.9 (SD 0.6) | 3.1 (SD 1.4) | t(9)=5.02, p<0.001, d=1.37 |
| SUS usability score | **86.2** (SD 7.5) | 63.5 (SD 10.9) | t(9)=6.51, p<0.001 |
| NASA-TLX workload | 36.1 (SD 8.8) | 58.4 (SD 11.2) | t(9)=-5.23, p<0.001 |

A System Usability Scale score of 86.2 falls in the "excellent" band; JaneApp's 63.5 falls in the "marginal" band. Lower NASA-TLX is better, indicating less mental demand and frustration. The effect sizes above 1.3 indicate substantial practical differences, not just statistical ones.

The advantage was largest on tasks involving navigation to specific data or information retrieval, where the AI assistant replaced manual searching. Full methodology and qualitative findings are in `Carevault_Research_Paper.pdf`.

## Features

**Records and documents.** Upload, browse, and view medical documents. Documents can be sent into the chat assistant for analysis.

**AI chat assistant.** A conversational interface spanning every part of the platform, grounded in the user's own documents rather than general knowledge. Supports in-chat document upload.

**Appointments.** Book, edit, and view appointments, with a browsable directory of healthcare providers.

**Medications.** Track medications and manage reminders.

**Health insights.** A metrics dashboard with charts, including per-metric detail views and manual data entry.

**Caregiver mode.** A caregiver can manage a dependent's records, appointments, medications, and insights through a parallel set of patient-scoped routes.

**Account management.** Registration with email verification, login, password reset, and profile and settings screens.

**Light and dark themes**, handled through a dedicated theme context.

## Architecture

A Create React App project on React 19, organised by feature rather than by file type.

```
src/
  pages/            One directory per feature area
    landing  auth  dashboard  documents  chat  appointments
    medications  insights  patients  profile  settings  demo  not-found
  components/       Presentational and feature components
    common  layout  navigation  auth  documents  chat
    appointments  medications  insights  patients  caregiver
    settings  landing
  context/          Nine React context providers
    AuthContext  ChatContext  DocumentContext  AppointmentContext
    MedicationContext  HealthContext  PatientContext
    DependentContext  ThemeContext
  services/         API layer, one module per domain
    authService  chatService  documentService
    appointmentService  medicationService  patientService  providerService
  hooks/  utils/  styles/  assets/
```

**State management** uses React Context rather than an external store, with nine providers each owning one slice of application state.

**The service layer** isolates every backend call behind a per-domain module, so components never call the API directly. All requests go through a shared Axios client in `src/utils/apiClient.js`.

**Routing** covers 48 routes via React Router 7, including protected routes and a parallel `/patient/:patientId/...` tree that mirrors the main application for caregiver access.

`Carevault_Architecture_Diagram.png` in the repository root shows the full system design.

## Tech Stack

- React 19
- React Router 7
- React Context for state management
- Axios for HTTP
- Chart.js with react-chartjs-2 and chartjs-adapter-date-fns for the insights dashboard
- React Icons
- Create React App (react-scripts 5)
- Prettier
- React Testing Library and jest-dom

## Setup

Requires Node.js and npm.

**1. Install dependencies**

```bash
npm install
```

**2. Start the backend**

The client expects the CareVault API at `http://localhost:1999/api`, which is where [carevault-backend](https://github.com/raunak-choudhary/carevault-backend) runs by default. Start it before the frontend, or API calls will fail. The base URL is set in `src/utils/apiClient.js`.

**3. Run the development server**

```bash
npm start
```

The app opens at `http://localhost:3000`, which is the origin the backend's CORS configuration allows.

**Other commands**

```bash
npm run build      # Production build
npm test           # Test runner
npm run prettier   # Format src/
```

## Team Roles and Responsibilities

CareVault was built by a two-person team at NYU Tandon School of Engineering. The work was divided by layer from the start, with both members collaborating on integration once the halves were ready.

| Member | Responsibility |
| --- | --- |
| **Raunak Choudhary** | **Frontend lead.** Designed and implemented this React client: application and routing architecture, the nine-context state model, the service layer, and all user-facing screens including the chat interface, insights dashboard, and caregiver mode. |
| **Aninda Ghosh** | **Backend lead.** Designed and implemented the Flask API: route and service architecture, Supabase integration for auth, database and storage, the RAGFlow conversational retrieval pipeline, and OpenAI document analysis. See [carevault-backend](https://github.com/raunak-choudhary/carevault-backend). |

Integration, API contract design, the user evaluation study, and the research paper were joint work.

Both members are Computer Science students at New York University, Tandon School of Engineering.

## Repository Contents

```
src/                              React application
public/                           Static assets and HTML shell
Carevault_Architecture_Diagram.png    Full system architecture
Carevault_Research_Paper.pdf          User evaluation study
package.json
```

## Related

- [carevault-backend](https://github.com/raunak-choudhary/carevault-backend) - the Flask API this client consumes
