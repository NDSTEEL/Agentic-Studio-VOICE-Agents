# Developer Task Specification - Voice Agent Platform

## Project: B2B SaaS Platform for Voice Agent Creation

### Overview
Build a no-code platform where businesses can create, customize, and deploy AI voice agents using templates. Think "Shopify for Voice Agents" - businesses select templates, upload knowledge, customize branding, and deploy without technical skills.

## Required Deliverables

### 1. Multi-Tenant Database Schema (Priority: CRITICAL)

Design PostgreSQL schema with Prisma for complete isolation:

```prisma
model Organization {
  id                String          @id @default(cuid())
  name              String
  email             String          @unique
  subscription      String          // starter|business|enterprise
  agents            Agent[]
  knowledgeBases    KnowledgeBase[]
  apiKeys           ApiKey[]
  users             User[]
  createdAt         DateTime        @default(now())
}

model AgentTemplate {
  id                String          @id @default(cuid())
  name              String          // "Customer Service", "Sales", "Booking"
  category          String
  elevenLabsConfig  Json            // Voice template from ElevenLabs
  basePrompt        String          @db.Text // Protected core prompt
  configurableFields Json           // What users can safely modify
  previewUrl        String?
  agents            Agent[]
}

model Agent {
  id                String          @id @default(cuid())
  organizationId    String
  organization      Organization    @relation(fields: [organizationId])
  templateId        String
  template          AgentTemplate   @relation(fields: [templateId])
  name              String
  customization     Json            // Brand, voice settings, safe tweaks
  knowledgeBaseId   String?
  knowledgeBase     KnowledgeBase?  @relation(fields: [knowledgeBaseId])
  status            String          // draft|testing|deployed|paused
  deploymentUrl     String?
  conversations     Conversation[]
  analytics         Analytics[]
  createdAt         DateTime        @default(now())
}

model KnowledgeBase {
  id                String          @id @default(cuid())
  organizationId    String
  organization      Organization    @relation(fields: [organizationId])
  documents         Document[]
  faqs              Json            // Structured Q&A pairs
  products          Json            // Product catalog
  companyInfo       Json            // About, policies, etc.
  agents            Agent[]
  vectorStoreId     String?         // Pinecone/Weaviate ID
  lastProcessed     DateTime?
}
```

### 2. Admin Dashboard Pages (Priority: CRITICAL)

Create Next.js pages for business users:

#### `/dashboard` - Overview
- Active agents with status
- Recent conversations
- Performance metrics
- Quick actions (create agent, view analytics)

#### `/agents/new` - Agent Creation Wizard
```typescript
// Step 1: Template Selection
- Grid of template cards with previews
- Category filters (customer service, sales, healthcare)
- "Listen to Sample" button for each

// Step 2: Knowledge Base Setup
- Upload documents (PDF, DOCX, TXT)
- Link existing resources (websites, Google Docs)
- Guided Q&A builder
- Product catalog import (CSV/JSON)

// Step 3: Brand Customization
- Company name and info
- Voice personality sliders (formal ← → casual)
- Conversation style options
- Restricted word list

// Step 4: Behavior Configuration
- "What should the agent do?" checkboxes
- "What shouldn't the agent say?" inputs
- Business hours settings
- Escalation rules

// Step 5: Test & Deploy
- Live preview with test conversations
- Deployment options (widget, phone, API)
- Integration instructions
```

#### `/agents/[id]/edit` - Agent Management
- Safe configuration zone (can't break agent)
- Knowledge base updates
- Performance monitoring
- Conversation history
- Danger zone (with warnings)

#### `/knowledge` - Knowledge Base Manager
- Document library with upload
- FAQ editor
- Product catalog
- Company information
- Processing status for each document

#### `/analytics` - Performance Dashboard
- Conversation success rate
- Common questions
- Agent response times
- User satisfaction
- Usage vs plan limits

### 3. API Routes for Platform (Priority: HIGH)

#### Template Management
```typescript
// GET /api/templates
// Returns available templates with metadata

// GET /api/templates/[id]/preview
// Returns sample conversation for template
```

#### Agent Operations
```typescript
// POST /api/agents/create
// Input: templateId, organizationId, customization
// Process: Combines template + knowledge + brand
// Returns: agentId, deploymentUrl

// PUT /api/agents/[id]/customize
// Validates changes against safety rules
// Prevents breaking modifications

// POST /api/agents/[id]/deploy
// Activates agent for production use

// GET /api/agents/[id]/test
// Returns test interface URL
```

#### Knowledge Base Processing
```typescript
// POST /api/knowledge/upload
// Accepts: PDF, DOCX, TXT, CSV
// Process: Extract, chunk, vectorize
// Store: PostgreSQL + vector DB

// POST /api/knowledge/process
// Converts documents to searchable format
// Creates embeddings for retrieval

// GET /api/knowledge/validate
// Checks if knowledge base is sufficient
// Warns about potential gaps
```

### 4. Safety & Validation System (Priority: CRITICAL)

Implement protection layers:

```typescript
// /src/lib/safety/validator.ts

class AgentValidator {
  // Prevent prompt injection
  validateCustomization(input: any): ValidationResult
  
  // Check knowledge base size limits
  validateKnowledgeSize(kb: KnowledgeBase): boolean
  
  // Ensure changes won't break agent
  validateConfiguration(config: any): SafetyCheck
  
  // Monitor performance degradation
  checkPerformanceImpact(changes: any): Risk
}

// /src/lib/safety/guardrails.ts

const PROTECTED_FIELDS = [
  'basePrompt',
  'systemInstructions',
  'apiConfiguration',
  'securitySettings'
]

const SAFE_CUSTOMIZATIONS = [
  'greetingMessage',
  'personalityTone',
  'companyName',
  'workingHours'
]

const DANGER_ZONE = [
  'customPromptAdditions', // With limits
  'advancedFilters',
  'apiWebhooks'
]
```

### 5. Voice Agent Runtime (Priority: HIGH)

Multi-tenant voice processing:

```typescript
// /src/lib/voice/runtime.ts

class VoiceAgentRuntime {
  constructor(agentId: string, orgId: string)
  
  async processVoiceInput(audio: Blob) {
    // 1. Validate org has active subscription
    // 2. Get agent configuration
    // 3. Speech-to-text (Whisper)
    // 4. Retrieve context from knowledge base
    // 5. Generate response (GPT-4 with template)
    // 6. Text-to-speech (ElevenLabs)
    // 7. Log conversation
    // 8. Update analytics
  }
  
  getAgentPrompt(agent: Agent): string {
    // Combine: template base + knowledge + customization
    // Inject: safety constraints
    // Never expose: core prompt to user
  }
}
```

### 6. Deployment System (Priority: MEDIUM)

Agent deployment options:

```typescript
// /src/lib/deployment/index.ts

// Widget Deployment
generateWidgetCode(agentId: string): string
// Returns embeddable JavaScript snippet

// Phone Integration
provisionPhoneNumber(agentId: string): PhoneNumber
// Sets up Twilio integration

// API Access
generateApiCredentials(agentId: string): ApiKey
// Creates secure API access
```

### 7. Template System (Priority: HIGH)

Template management:

```typescript
// /src/lib/templates/manager.ts

interface AgentTemplate {
  id: string
  name: string
  category: string
  basePrompt: string // Never exposed to users
  elevenLabsVoiceId: string
  sampleConversation: string[]
  configurableFields: {
    field: string
    type: 'text' | 'select' | 'range'
    validation: any
    impact: 'safe' | 'moderate' | 'danger'
  }[]
}

// Import templates from ElevenLabs
// Add safety wrappers
// Define customization boundaries
```

### 8. Analytics & Monitoring (Priority: MEDIUM)

Track platform and agent performance:

```typescript
// /src/lib/analytics/tracker.ts

// Platform metrics
- Total organizations
- Active agents
- Daily conversations
- Success rates

// Per-organization metrics  
- Agent performance
- Usage vs limits
- Popular questions
- Failed conversations

// Per-agent metrics
- Response time
- Satisfaction score
- Escalation rate
- Knowledge gaps
```

## Testing Requirements

### Unit Tests
- Template validation
- Safety guardrails
- Knowledge processing
- Multi-tenant isolation

### Integration Tests
- Full agent creation flow
- Voice pipeline end-to-end
- Knowledge base updates
- Deployment process

### Security Tests
- Tenant isolation
- Prompt injection prevention
- API authentication
- Rate limiting

## Technical Stack

### Required Dependencies
```json
{
  "dependencies": {
    "@prisma/client": "^5.0.0",
    "openai": "^4.0.0",
    "elevenlabs": "^0.1.0",
    "pinecone-client": "^1.0.0",
    "bull": "^4.0.0", // Job queues
    "stripe": "^13.0.0", // Billing
    "next-auth": "^4.0.0",
    "zod": "^3.0.0", // Validation
    "react-hook-form": "^7.0.0",
    "recharts": "^2.0.0" // Analytics charts
  }
}
```

## File Structure

```
src/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── signup/
│   ├── (dashboard)/
│   │   ├── agents/
│   │   │   ├── new/
│   │   │   └── [id]/
│   │   ├── knowledge/
│   │   ├── analytics/
│   │   └── settings/
│   └── api/
│       ├── agents/
│       ├── templates/
│       ├── knowledge/
│       └── voice/
├── components/
│   ├── agent-builder/
│   ├── knowledge-manager/
│   ├── template-selector/
│   └── analytics-charts/
├── lib/
│   ├── auth/
│   ├── safety/
│   ├── templates/
│   ├── knowledge/
│   ├── voice/
│   └── deployment/
└── prisma/
    └── schema.prisma
```

## Definition of Done

- [ ] Multi-tenant database with full isolation
- [ ] Complete agent creation wizard
- [ ] Template system with ElevenLabs integration
- [ ] Knowledge base processing pipeline
- [ ] Safety validation on all inputs
- [ ] Voice processing runtime
- [ ] Analytics dashboard
- [ ] Deployment options (widget/API)
- [ ] 90% test coverage
- [ ] Security audit passed

## Success Criteria

- Business can create agent in < 30 minutes
- No technical knowledge required
- Agent cannot be broken by user input
- Each org's data completely isolated
- Platform handles 100+ organizations
- Voice latency < 500ms per conversation