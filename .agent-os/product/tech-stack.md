# Technical Stack

## Frontend
- **Framework:** Next.js 14.2.5
- **UI Library:** React 18.3.1
- **Language:** TypeScript 5.5.2
- **Styling:** TailwindCSS 3.4.4
- **Components:** shadcn/ui with Radix UI
- **Icons:** Lucide React
- **State Management:** React Context + Hooks
- **Real-time:** WebSockets (Socket.io)

## Backend
- **Runtime:** Node.js 20 LTS
- **API Framework:** Next.js API Routes
- **Voice Processing:** OpenAI Whisper API
- **Text-to-Speech:** ElevenLabs API
- **LLM Integration:** OpenAI GPT-4
- **Authentication:** NextAuth.js
- **Validation:** Zod

## Database & Storage
- **Primary Database:** PostgreSQL 17
- **ORM:** Prisma 5.x
- **Cache/Sessions:** Redis 7.x
- **File Storage:** AWS S3 / Vercel Blob
- **Vector Store:** Pinecone (for conversation embeddings)

## Infrastructure
- **Hosting:** Vercel (Application)
- **Database Hosting:** Neon / Supabase
- **Container:** Docker for local development
- **CI/CD:** GitHub Actions
- **Monitoring:** Vercel Analytics + Custom n8n workflows
- **Error Tracking:** Sentry

## Voice & AI Stack
- **Speech Recognition:** OpenAI Whisper
- **Natural Language:** OpenAI GPT-4
- **Text-to-Speech:** ElevenLabs
- **Voice Activity Detection:** WebRTC VAD
- **Audio Processing:** Web Audio API

## Development Tools
- **Package Manager:** npm
- **Testing:** Jest + React Testing Library
- **E2E Testing:** Playwright
- **Linting:** ESLint
- **Formatting:** Prettier
- **Git Hooks:** Husky + lint-staged

## Security
- **Authentication:** NextAuth with JWT
- **API Protection:** Rate limiting with Upstash
- **Secrets Management:** Environment variables
- **CORS:** Configured for production domains
- **Data Encryption:** TLS 1.3 + AES-256