# Agentic Studio VOICE Agents

Production-ready web application for AI-powered voice agents with real-time processing and multi-agent orchestration.

## Features

- 🎤 Real-time voice input processing
- 🤖 Multi-agent AI orchestration
- 🔊 Natural text-to-speech output
- 💬 Conversation history management
- 🎯 Context-aware responses
- 📊 Analytics and monitoring

## Tech Stack

- **Frontend**: Next.js 14, React 18, TypeScript
- **Backend**: Node.js, Express/FastAPI
- **AI/ML**: OpenAI, ElevenLabs, Whisper
- **Database**: PostgreSQL, Redis
- **Infrastructure**: Docker, GitHub Actions
- **Monitoring**: n8n workflows

## Quick Start

```bash
# Install dependencies
npm install

# Set up environment variables
cp .env.example .env

# Run development server
npm run dev
```

## Project Structure

```
├── src/
│   ├── app/          # Next.js app router
│   ├── components/   # React components
│   ├── lib/          # Utilities and helpers
│   ├── api/          # API routes
│   └── agents/       # Voice agent logic
├── public/           # Static assets
├── tests/            # Test suites
└── .agent-os/        # Agent OS documentation
```

## Development

See [Development Guide](./docs/development.md) for detailed setup instructions.

## License

MIT