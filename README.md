# ElevenLabs-Revamp: Resonance

<p align="center">
  <strong>AI-Powered Text-to-Speech & Voice Cloning Platform</strong>
</p>

<p align="center">
  A modern, production-ready TTS platform with custom voice cloning, subscription billing, and real-time audio generation capabilities.
</p>

<p align="center">
  <strong>Created by <a href="https://mwaleedkhalil.com">Muhammad Waleed Khalil</a></strong><br/>
  📧 <a href="mailto:mwaleedkhalil@gmail.com">mwaleedkhalil@gmail.com</a> | 🌐 <a href="https://mwaleedkhalil.com">mwaleedkhalil.com</a>
</p>

---

## 📋 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Data Flow](#data-flow)
- [API Reference](#api-reference)
- [Database Schema](#database-schema)
- [Configuration](#configuration)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [Scripts](#scripts)
- [License](#license)

---

## 🎯 Overview

**ElevenLabs-Revamp: Resonance** is a full-stack text-to-speech application that enables users to generate high-quality speech from text using AI-powered voice synthesis. The platform supports both built-in system voices and custom voice cloning, allowing organizations to create personalized voice experiences.

### Key Capabilities

- **Text-to-Speech Generation**: Convert text up to 5,000 characters into natural-sounding speech
- **Voice Cloning**: Create custom voices from audio samples (minimum 10 seconds)
- **Voice Library**: Access 20+ built-in system voices across multiple categories
- **Subscription Billing**: Integrated pay-as-you-go pricing via Polar
- **Organization-based Access**: Multi-tenant architecture with Clerk authentication

---

## ✨ Features

### 🎙️ Text-to-Speech Engine

| Feature | Description |
|---------|-------------|
| **Voice Selection** | Choose from system voices or custom-created voices |
| **Parameter Tuning** | Adjust creativity, variety, expression, and flow |
| **Real-time Preview** | Listen to generated audio instantly |
| **History Tracking** | Access all previous generations |

#### Generation Parameters

| Parameter | Range | Default | Description |
|-----------|-------|---------|-------------|
| Temperature | 0 - 2 | 0.8 | Controls expressiveness vs consistency |
| Top P | 0 - 1 | 0.95 | Voice variety and dynamics |
| Top K | 1 - 10,000 | 1000 | Expression range (subtle to dramatic) |
| Repetition Penalty | 1 - 2 | 1.2 | Natural flow and variation |

### 🎭 Voice Management

- **Upload Audio**: Support for all audio formats up to 20MB
- **Record Voice**: Built-in audio recording functionality
- **Voice Categories**: 12 categories including Audiobook, Conversational, Podcast, etc.
- **Language Support**: Comprehensive locale/language selection
- **Voice Preview**: Preview voices before selection

### 💳 Billing & Subscription

- **Pay-as-you-go**: $0.30 per 1,000 characters
- **Polar Integration**: Seamless subscription management
- **Usage Tracking**: Real-time cost estimation
- **Portal Access**: Self-service subscription management

---

## 🏗️ Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        CLIENT LAYER                              │
├─────────────────────────────────────────────────────────────────┤
│  Next.js App Router  │  React Components  │  TanStack Query     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                        API LAYER                                 │
├─────────────────────────────────────────────────────────────────┤
│  tRPC Routers        │  REST API Routes    │  Clerk Auth        │
│  - voices            │  - /api/audio/[id]  │  - Organizations   │
│  - generations       │  - /api/voices/     │  - User Sessions   │
│  - billing           │    create           │                    │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                     SERVICE LAYER                                │
├─────────────────────────────────────────────────────────────────┤
│  Chatterbox TTS API  │  Polar Billing API   │  Cloudflare R2    │
│  - Voice Synthesis   │  - Subscriptions     │  - Audio Storage  │
│  - Audio Generation  │  - Usage Metering    │  - Signed URLs    │
└─────────────────────────────────────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                      DATA LAYER                                  │
├─────────────────────────────────────────────────────────────────┤
│  PostgreSQL Database  │  Prisma ORM                             │
│  - Voices             │  - Type-safe queries                    │
│  - Generations        │  - Migrations                           │
└─────────────────────────────────────────────────────────────────┘
```

### Application Flow

#### Text-to-Speech Generation Flow

```
User Input → Form Validation → tRPC Mutation
                                    │
                                    ▼
                    Subscription Check (Polar)
                                    │
                                    ▼
                    Voice Lookup (Database)
                                    │
                                    ▼
                    Audio Generation (Chatterbox API)
                                    │
                                    ▼
                    Upload to Storage (Cloudflare R2)
                                    │
                                    ▼
                    Save Generation Record (Database)
                                    │
                                    ▼
                    Usage Event (Polar Metering)
                                    │
                                    ▼
                    Return Generation ID → Client Redirect
```

#### Voice Creation Flow

```
Audio Upload/Recording → Validation (Size, Duration)
                                    │
                                    ▼
                    Subscription Check (Polar)
                                    │
                                    ▼
                    Create Voice Record (Database)
                                    │
                                    ▼
                    Upload Audio (Cloudflare R2)
                                    │
                                    ▼
                    Update Voice with R2 Key
                                    │
                                    ▼
                    Usage Event (Polar Metering)
```

---

## 🔧 Technology Stack

### Frontend

| Technology | Purpose |
|------------|---------|
| Next.js 16 | React framework with App Router |
| React 19 | UI library |
| TypeScript | Type safety |
| TanStack Query | Server state management |
| TanStack Form | Form handling with validation |
| Tailwind CSS | Utility-first styling |
| Shadcn UI | Component library |
| Radix UI | Accessible primitives |
| WaveSurfer.js | Audio visualization |
| RecordRTC | Audio recording |

### Backend

| Technology | Purpose |
|------------|---------|
| tRPC | End-to-end type-safe API |
| Prisma | Database ORM |
| PostgreSQL | Primary database |
| Clerk | Authentication & organizations |
| Polar | Subscription billing |

### Infrastructure

| Technology | Purpose |
|------------|---------|
| Cloudflare R2 | Object storage for audio |
| Sentry | Error monitoring |
| OpenAPI | API type generation |

---

## 📁 Project Structure

```
resonance/
├── prisma/
│   ├── schema.prisma           # Database schema
│   └── migrations/             # Database migrations
├── public/
│   └── system-voices/          # System voice audio files
├── scripts/
│   ├── seed-system-voices.ts   # Seed system voices
│   └── sync-api.ts             # API type sync
├── src/
│   ├── app/                    # Next.js App Router
│   │   ├── (dashboard)/        # Dashboard routes
│   │   │   ├── layout.tsx      # Dashboard layout with sidebar
│   │   │   ├── page.tsx        # Dashboard home
│   │   │   ├── text-to-speech/ # TTS feature routes
│   │   │   └── voices/         # Voice management routes
│   │   ├── api/                # API routes
│   │   │   ├── audio/[id]/     # Audio streaming endpoint
│   │   │   ├── trpc/[trpc]/    # tRPC handler
│   │   │   └── voices/create/  # Voice upload endpoint
│   │   ├── org-selection/      # Organization selection
│   │   ├── sign-in/            # Sign in page
│   │   ├── sign-up/            # Sign up page
│   │   ├── layout.tsx          # Root layout
│   │   └── globals.css         # Global styles
│   ├── components/
│   │   ├── ui/                 # UI components (Shadcn)
│   │   ├── voice-avatar/       # Voice avatar generator
│   │   └── page-header.tsx     # Page header component
│   ├── features/               # Feature-based modules
│   │   ├── billing/            # Billing feature
│   │   │   ├── components/
│   │   │   │   └── usage-container.tsx
│   │   │   └── hooks/
│   │   │       └── use-checkout.ts
│   │   ├── dashboard/          # Dashboard feature
│   │   │   ├── components/
│   │   │   ├── data/
│   │   │   └── views/
│   │   ├── text-to-speech/     # TTS feature
│   │   │   ├── components/
│   │   │   │   ├── generate-button.tsx
│   │   │   │   ├── history-drawer.tsx
│   │   │   │   ├── settings-panel.tsx
│   │   │   │   ├── text-input-panel.tsx
│   │   │   │   ├── text-to-speech-form.tsx
│   │   │   │   ├── voice-selector.tsx
│   │   │   │   └── ...
│   │   │   ├── contexts/
│   │   │   │   └── tts-voices-context.tsx
│   │   │   ├── data/
│   │   │   │   ├── constants.ts
│   │   │   │   └── sliders.ts
│   │   │   ├── hooks/
│   │   │   │   └── use-wavesurfer.ts
│   │   │   └── views/
│   │   └── voices/             # Voice management feature
│   │       ├── components/
│   │       │   ├── voice-card.tsx
│   │       │   ├── voice-create-dialog.tsx
│   │       │   ├── voice-create-form.tsx
│   │       │   ├── voice-recorder.tsx
│   │       │   └── ...
│   │       ├── data/
│   │       │   ├── voice-categories.ts
│   │       │   └── voice-scoping.ts
│   │       ├── hooks/
│   │       │   └── use-audio-recorder.ts
│   │       ├── lib/
│   │       │   └── params.ts
│   │       └── views/
│   ├── hooks/                  # Global hooks
│   │   ├── use-app-form.ts
│   │   ├── use-audio-playback.ts
│   │   └── use-mobile.ts
│   ├── lib/                    # Core libraries
│   │   ├── chatterbox-client.ts # TTS API client
│   │   ├── db.ts               # Database client
│   │   ├── env.ts              # Environment validation
│   │   ├── polar.ts            # Billing client
│   │   ├── r2.ts               # Storage client
│   │   └── utils.ts            # Utility functions
│   ├── trpc/                   # tRPC configuration
│   │   ├── routers/
│   │   │   ├── _app.ts         # Root router
│   │   │   ├── billing.ts      # Billing procedures
│   │   │   ├── generations.ts  # Generation procedures
│   │   │   └── voices.ts       # Voice procedures
│   │   ├── client.tsx          # Client provider
│   │   ├── init.ts             # tRPC initialization
│   │   └── server.tsx          # Server caller
│   └── types/                  # Type definitions
│       └── chatterbox-api.d.ts
├── package.json
├── next.config.ts
├── tailwind.config.ts
└── tsconfig.json
```

---

## 🔄 Data Flow

### State Management

The application uses a layered state management approach:

1. **Server State**: TanStack Query manages server state via tRPC
2. **Form State**: TanStack Form handles form state with Zod validation
3. **URL State**: nuqs manages search params for filters and pagination
4. **Context State**: React Context for feature-specific state (e.g., TTS voices)

### Authentication Flow

```
Clerk Provider → Organization Selection → Protected Routes
                                    │
                                    ▼
                    tRPC Context (orgId, userId)
                                    │
                                    ▼
                    Database Queries (org-scoped)
```

### API Request Flow

```
Client Component → useQuery/useMutation
                        │
                        ▼
              tRPC Client → HTTP Request
                        │
                        ▼
              tRPC Handler → Procedure
                        │
                        ▼
              Clerk Auth Check → orgId Extraction
                        │
                        ▼
              Business Logic → Database/External APIs
                        │
                        ▼
              Response → TanStack Query Cache
                        │
                        ▼
              UI Update
```

---

## 🔌 API Reference

### tRPC Routers

#### Voices Router

| Procedure | Type | Description |
|-----------|------|-------------|
| `getAll` | Query | Get all voices (custom + system) with optional search |
| `delete` | Mutation | Delete a custom voice |

#### Generations Router

| Procedure | Type | Description |
|-----------|------|-------------|
| `getById` | Query | Get a single generation by ID |
| `getAll` | Query | Get all generations for organization |
| `create` | Mutation | Create a new TTS generation |

#### Billing Router

| Procedure | Type | Description |
|-----------|------|-------------|
| `createCheckout` | Mutation | Create Polar checkout session |
| `createPortalSession` | Mutation | Create customer portal session |
| `getStatus` | Query | Get subscription status and usage |

### REST API Routes

#### `GET /api/audio/[generationId]`

Stream generated audio file.

**Response**: Audio stream (WAV format)

#### `POST /api/voices/create`

Create a new custom voice.

**Query Parameters**:
- `name`: Voice name
- `category`: Voice category
- `language`: Language code
- `description`: Optional description

**Body**: Audio file (binary)

**Response**: `{ name: string, message: string }`

---

## 💾 Database Schema

### Voice Model

```prisma
model Voice {
  id          String         @id @default(cuid())
  orgId       String?        // Organization ID (null for system voices)
  name        String
  description String?
  category    VoiceCategory  @default(GENERAL)
  language    String         @default("en-US")
  variant     VoiceVariant   // SYSTEM or CUSTOM
  r2ObjectKey String?        // Storage key for audio
  generations Generation[]
  createdAt   DateTime       @default(now())
  updatedAt   DateTime       @updatedAt
}
```

### Generation Model

```prisma
model Generation {
  id                String   @id @default(cuid())
  orgId             String
  voiceId           String?
  voice             Voice?   @relation(...)
  text              String
  voiceName         String
  r2ObjectKey       String?
  temperature       Float
  topP              Float
  topK              Int
  repetitionPenalty Float
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}
```

### Enums

```prisma
enum VoiceVariant {
  SYSTEM
  CUSTOM
}

enum VoiceCategory {
  AUDIOBOOK
  CONVERSATIONAL
  CUSTOMER_SERVICE
  GENERAL
  NARRATIVE
  CHARACTERS
  MEDITATION
  MOTIVATIONAL
  PODCAST
  ADVERTISING
  VOICEOVER
  CORPORATE
}
```

---

## ⚙️ Configuration

### Environment Variables

| Variable | Description | Required |
|----------|-------------|----------|
| `DATABASE_URL` | PostgreSQL connection string | ✅ |
| `APP_URL` | Application URL | ✅ |
| `CHATTERBOX_API_URL` | TTS API endpoint | ✅ |
| `CHATTERBOX_API_KEY` | TTS API key | ✅ |
| `R2_ACCOUNT_ID` | Cloudflare R2 account ID | ✅ |
| `R2_ACCESS_KEY_ID` | R2 access key | ✅ |
| `R2_SECRET_ACCESS_KEY` | R2 secret key | ✅ |
| `R2_BUCKET_NAME` | R2 bucket name | ✅ |
| `POLAR_ACCESS_TOKEN` | Polar API token | ✅ |
| `POLAR_SERVER` | Polar environment (sandbox/production) | ✅ |
| `POLAR_PRODUCT_ID` | Polar product ID | ✅ |

### External Services Setup

1. **Clerk**: Configure organization support
2. **Polar**: Create product and webhooks
3. **Cloudflare R2**: Create bucket with CORS configuration
4. **Chatterbox API**: Obtain API credentials

---

## 🚀 Getting Started

### Prerequisites

- Node.js 18+
- PostgreSQL 14+
- npm, yarn, or pnpm

### Installation

1. **Clone the repository**

```bash
git clone <repository-url>
cd resonance
```

2. **Install dependencies**

```bash
npm install
```

3. **Configure environment**

```bash
cp .env.example .env
# Edit .env with your configuration
```

4. **Run database migrations**

```bash
npx prisma migrate dev
```

5. **Seed system voices** (optional)

```bash
npm run sync-api
tsx scripts/seed-system-voices.ts
```

6. **Start development server**

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000) to view the application.

---

## 📜 Scripts

| Script | Description |
|--------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Build for production |
| `npm run start` | Start production server |
| `npm run lint` | Run ESLint |
| `npm run sync-api` | Sync Chatterbox API types |

---

## 🔒 Security Features

- **Authentication**: Clerk handles user authentication with session management
- **Authorization**: Organization-based access control via tRPC middleware
- **File Validation**: Audio file validation for size, format, and duration
- **Signed URLs**: Time-limited signed URLs for audio streaming
- **Rate Limiting**: Subscription-based usage limits via Polar

---

## 🎨 UI Components

The application uses a comprehensive UI component library built on Shadcn UI:

- **Forms**: Input, Textarea, Select, Combobox, Slider
- **Navigation**: Sidebar, Tabs, Breadcrumb, Pagination
- **Feedback**: Toast, Dialog, Alert, Progress
- **Data Display**: Table, Card, Badge, Avatar
- **Audio**: WaveSurfer visualization, Audio player

---

## 📈 Monitoring & Observability

- **Sentry**: Error tracking and performance monitoring
- **Polar Events**: Usage metering for billing
- **Structured Logging**: Contextual logging via Sentry

---

## 📄 License

This project is private and proprietary.

---

