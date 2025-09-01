# Developer Task Specification

## Project: Voice AI Agent Backend Implementation

### Overview
Implement backend functionality for a voice AI web application. The frontend UI is complete and uses Next.js 14 with TypeScript. You will connect voice processing APIs and set up real-time communication.

## Required Deliverables

### 1. API Routes (Priority: HIGH)

Create the following Next.js API routes in `/src/app/api/`:

#### `/api/voice/transcribe`
```typescript
// POST endpoint
// Accepts: Audio blob (webm/wav)
// Returns: { text: string, confidence: number }
// Integration: OpenAI Whisper API
```

#### `/api/voice/synthesize`  
```typescript
// POST endpoint
// Accepts: { text: string, voice?: string }
// Returns: Audio stream (mp3)
// Integration: ElevenLabs API
```

#### `/api/chat/complete`
```typescript
// POST endpoint
// Accepts: { messages: Message[], context?: string }
// Returns: { response: string, tokens: number }
// Integration: OpenAI GPT-4 API
```

### 2. WebSocket Implementation (Priority: HIGH)

Set up real-time audio streaming:

```typescript
// File: /src/lib/websocket.ts
- Initialize Socket.io server
- Handle audio chunk streaming
- Implement reconnection logic
- Add heartbeat/ping mechanism
```

### 3. Database Schema (Priority: MEDIUM)

Using Prisma with PostgreSQL:

```prisma
// schema.prisma
model User {
  id            String         @id @default(cuid())
  email         String         @unique
  conversations Conversation[]
  createdAt     DateTime       @default(now())
}

model Conversation {
  id        String    @id @default(cuid())
  userId    String
  user      User      @relation(fields: [userId], references: [id])
  messages  Message[]
  createdAt DateTime  @default(now())
  updatedAt DateTime  @updatedAt
}

model Message {
  id             String       @id @default(cuid())
  conversationId String
  conversation   Conversation @relation(fields: [conversationId], references: [id])
  role           String       // 'user' | 'assistant'
  content        String
  audioUrl       String?
  createdAt      DateTime     @default(now())
}
```

### 4. Error Handling (Priority: HIGH)

Add comprehensive error handling:

```typescript
// /src/lib/errors.ts
- API rate limit errors
- Network timeout handling  
- Invalid audio format errors
- Token limit exceeded
- Database connection errors
```

### 5. Frontend Integration (Priority: HIGH)

Update existing component at `/src/app/page.tsx`:

```typescript
// Replace mock functions with real API calls
- processAudio(): Call /api/voice/transcribe
- getAIResponse(): Call /api/chat/complete  
- synthesizeSpeech(): Call /api/voice/synthesize
- Add loading states
- Add error boundaries
```

### 6. Testing (Priority: MEDIUM)

Create tests in `/tests/`:

```typescript
// API endpoint tests
- test-transcribe.test.ts
- test-synthesize.test.ts
- test-chat.test.ts

// Integration tests
- test-voice-pipeline.test.ts
```

## Technical Requirements

### Environment Variables Needed
```env
# You'll receive development keys
OPENAI_API_KEY=
ELEVENLABS_API_KEY=
DATABASE_URL=postgresql://...
NEXTAUTH_SECRET=
WEBSOCKET_URL=ws://localhost:3001
```

### Dependencies to Install
```json
{
  "dependencies": {
    "openai": "^4.0.0",
    "socket.io": "^4.0.0",
    "socket.io-client": "^4.0.0",
    "@prisma/client": "^5.0.0",
    "axios": "^1.0.0"
  },
  "devDependencies": {
    "prisma": "^5.0.0",
    "@types/socket.io": "^3.0.0"
  }
}
```

## Code Quality Requirements

### Must Have:
- TypeScript with strict mode
- Proper error handling (try/catch)
- Input validation using Zod
- Rate limiting on API routes
- Proper async/await usage
- Environment variable validation

### Nice to Have:
- JSDoc comments for functions
- Request/response type definitions
- Logging for debugging
- Performance monitoring hooks

## Definition of Done

Each feature is considered complete when:

- [ ] Code is written and working
- [ ] TypeScript has no errors
- [ ] Basic error handling implemented
- [ ] Tested manually with the UI
- [ ] At least one automated test
- [ ] Pull request created with description
- [ ] No hardcoded values (use env vars)

## API Documentation Examples

### Transcribe Audio
```bash
curl -X POST http://localhost:3000/api/voice/transcribe \
  -H "Content-Type: multipart/form-data" \
  -F "audio=@recording.webm"

Response:
{
  "text": "Hello, how can I help you?",
  "confidence": 0.95,
  "duration": 3.2
}
```

### Generate Chat Response
```bash
curl -X POST http://localhost:3000/api/chat/complete \
  -H "Content-Type: application/json" \
  -d '{
    "messages": [
      {"role": "user", "content": "Hello"}
    ]
  }'

Response:
{
  "response": "Hello! How can I assist you today?",
  "tokens": 12
}
```

## File Structure Expected

```
src/
├── app/
│   ├── api/
│   │   ├── voice/
│   │   │   ├── transcribe/route.ts
│   │   │   └── synthesize/route.ts
│   │   └── chat/
│   │       └── complete/route.ts
│   └── page.tsx (update existing)
├── lib/
│   ├── websocket.ts
│   ├── errors.ts
│   ├── prisma.ts
│   └── api-client.ts
└── types/
    └── index.ts
```

## Communication

### Questions?
- Open an issue in the repository
- Comment on the pull request
- Use Upwork messaging

### Daily Updates Expected:
1. What you completed yesterday
2. What you're working on today
3. Any blockers or questions

## Timeline

- **Day 1-2**: API routes implementation
- **Day 3-4**: WebSocket setup
- **Day 5-6**: Database integration
- **Day 7-8**: Testing and documentation
- **Day 9-10**: Bug fixes and polish

Total estimate: 10 working days

## Resources

- [OpenAI API Docs](https://platform.openai.com/docs)
- [ElevenLabs API Docs](https://docs.elevenlabs.io)
- [Next.js API Routes](https://nextjs.org/docs/app/building-your-application/routing/route-handlers)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Socket.io Documentation](https://socket.io/docs/v4)