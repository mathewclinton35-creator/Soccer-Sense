# ⚽ SoccerSense — AI World Cup Companion

A personalized, multilingual AI-powered companion that helps fans understand soccer before, during, and after the match — powered by **IBM watsonx.ai**.

## What It Does

SoccerSense bridges the gap between casual fans and the deep tactical, cultural, and emotional world of World Cup soccer. It gives fans plain-language, context-aware explanations of:

- 🎯 **Tactical shifts** — Why did the coach switch formations? What does a high press mean?
- 🖥️ **VAR decisions** — Why was that goal disallowed? What does offside by 2cm actually mean?
- 📖 **Match context** — What changed in the second half? Why is momentum shifting?
- 🌍 **Multilingual support** — Ask in English, Spanish, French, Portuguese, German, or Arabic

## Architecture

```
SoccerSense/
├── backend/          # Node.js/Express API + IBM watsonx.ai integration
│   ├── src/
│   │   ├── routes/   # /api/explain, /api/var, /api/tactics, /api/match
│   │   ├── services/ # watsonxService, promptBuilder, matchDataService
│   │   └── data/     # Mock match events, lineups, VAR incidents
│   └── package.json
├── frontend/         # React + Vite chat companion UI
│   ├── src/
│   │   ├── components/
│   │   └── pages/
│   └── package.json
└── README.md
```

## Tech Stack

| Layer | Technology |
|-------|-----------|
| AI | IBM watsonx.ai (ibm/granite-13b-chat-v2 or meta-llama/llama-3-8b-instruct) |
| Backend | Node.js 18+, Express 4 |
| Frontend | React 18, Vite 5, TailwindCSS |
| Data | Mocked World Cup match events (JSON) |

## Quick Start

### Prerequisites
- Node.js 18+
- IBM watsonx.ai API key + Project ID ([get one here](https://www.ibm.com/watsonx))

### 1. Backend

```bash
cd backend
cp .env.example .env
# Fill in WATSONX_API_KEY and WATSONX_PROJECT_ID in .env
npm install
npm run dev
```

Backend runs on `http://localhost:3001`

### 2. Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend runs on `http://localhost:5173`

## API Endpoints

### POST `/api/explain`
Ask any free-form question about the match.

```json
{
  "question": "Why did Brazil switch to a 4-3-3 in the 60th minute?",
  "language": "en",
  "matchId": "bra-vs-arg-2026-final"
}
```

### POST `/api/var`
Get an explanation of a specific VAR incident.

```json
{
  "incidentId": "var-001",
  "language": "es"
}
```

### POST `/api/tactics`
Explain a tactical concept or decision.

```json
{
  "concept": "high press",
  "context": "Germany used it in the 70th minute when trailing 1-0",
  "language": "fr"
}
```

### GET `/api/match/:matchId`
Retrieve structured match context (events, lineups, key moments).

## IBM watsonx.ai Integration

The app uses the **watsonx.ai Text Generation API** with carefully engineered prompts that:

1. Set the AI as a knowledgeable but approachable soccer companion
2. Inject structured match context (events, formations, scores) into every prompt
3. Request plain-language responses appropriate for casual fans
4. Instruct the AI to respond in the user's chosen language

Model: `ibm/granite-13b-chat-v2` (falls back to `meta-llama/llama-3-8b-instruct`)

## Demo Mode

Without a watsonx.ai API key, the app runs in **demo mode** using pre-written explanations for the included mock match data. Set `DEMO_MODE=true` in your `.env`.

## Challenge Alignment

| Requirement | How SoccerSense addresses it |
|-------------|--------------------------|
| AI as core component | IBM watsonx.ai drives all explanations |
| Human-centered | Plain language, cultural context, emotional framing |
| Explainability | Every answer shows *why*, not just *what* |
| Accessibility | Multilingual, jargon-free by default |
| Fan understanding | Designed for casual fans, not analysts |
| Trust & transparency | VAR companion reconstructs decisions step-by-step |
