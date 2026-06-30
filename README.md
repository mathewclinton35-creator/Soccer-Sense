# ⚽ SoccerSense — AI World Cup Companion

> **FIFA World Cup 2026** · AI-powered match companion for fans of all levels  
> Powered by **IBM watsonx.ai** · React + Vite · Node.js + Express

![SoccerSense Desktop UI](https://upload.wikimedia.org/wikipedia/commons/thumb/6/67/MetLife_Stadium_-_exterior.jpg/960px-MetLife_Stadium_-_exterior.jpg)
*MetLife Stadium, New York/NJ — FIFA World Cup 2026 Final Venue*

---

## What Is SoccerSense?

SoccerSense is a full-stack AI web application that helps casual and new soccer fans understand what's happening in World Cup matches — in real time, in their own language.

It bridges the gap between the action on the pitch and the fan in the stands (or on the couch) by providing plain-language, context-aware explanations powered by IBM watsonx.ai.

### Features

| Tab | What it does |
|-----|-------------|
| 💬 **Ask AI** | Ask anything about the match — tactics, momentum shifts, player decisions. Chat history persists per match. |
| 🎯 **Tactics** | Explain any tactical concept (high press, false nine, gegenpress) with current match context |
| 🖥️ **VAR** | Step-by-step explanation of every VAR decision — which rule was applied and why it may be controversial |
| 📖 **Learn** | Soccer basics for new fans — offside, formations, cards, penalties — in any language |

### Other Highlights

- 🌍 **10 languages** — English, Spanish, French, Portuguese, German, Arabic, Italian, Dutch, Japanese, Mandarin
- 🖥️ **Desktop-first UI** — 3-column layout with live stadium image rotator and WC2026 facts ticker
- ♻️ **Auto-retry** — Handles watsonx.ai rate limits with automatic exponential backoff
- 💾 **Chat persistence** — Conversations saved per match in `localStorage`
- 🔍 **Match search** — Filter 6 matches by team, stage, or stadium
- 📱 **PWA-ready** — Installable as a web app with `manifest.json`
- 🟡 **Demo mode** — Full app works offline with pre-written expert answers (no API key needed)

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| **AI** | IBM watsonx.ai — `ibm/granite-3-8b-instruct` |
| **Backend** | Node.js 18+, Express 4, Axios |
| **Frontend** | React 18, Vite 5, TailwindCSS 3 |
| **Data** | 6 World Cup matches + 6 VAR incidents (JSON) |
| **Auth** | IBM Cloud IAM token (auto-cached + refresh) |

---

## Project Structure

```
Soccer Sense/
├── backend/
│   ├── src/
│   │   ├── routes/
│   │   │   ├── explain.js      # POST /api/explain
│   │   │   ├── tactics.js      # POST /api/tactics
│   │   │   ├── var.js          # GET /api/var/incidents · POST /api/var
│   │   │   ├── learn.js        # POST /api/learn
│   │   │   └── match.js        # GET /api/match · GET /api/match/:id
│   │   ├── services/
│   │   │   ├── watsonxService.js   # IBM watsonx.ai + IAM token + retry
│   │   │   ├── promptBuilder.js    # Granite 3 prompt format + multilingual
│   │   │   ├── matchDataService.js # Match & VAR data access
│   │   │   └── demoService.js      # Demo mode pre-written responses
│   │   ├── data/
│   │   │   ├── matches.json        # 6 WC2026 match datasets
│   │   │   └── varIncidents.json   # 6 VAR decisions with rule analysis
│   │   └── index.js            # Express app entry point
│   ├── .env.example
│   └── package.json
│
├── frontend/
│   ├── public/
│   │   └── manifest.json       # PWA manifest
│   ├── src/
│   │   ├── api/client.js       # Backend API wrapper
│   │   ├── components/
│   │   │   ├── AnswerBubble.jsx
│   │   │   ├── LanguageSelector.jsx
│   │   │   ├── MatchCard.jsx
│   │   │   └── SuggestedQuestions.jsx
│   │   ├── pages/
│   │   │   ├── AskTab.jsx
│   │   │   ├── TacticsTab.jsx
│   │   │   ├── VARTab.jsx
│   │   │   ├── LearnTab.jsx
│   │   │   └── MatchesPanel.jsx
│   │   ├── utils/formatAnswer.js
│   │   ├── App.jsx
│   │   ├── index.css
│   │   └── main.jsx
│   ├── index.html
│   ├── vite.config.js          # Proxy /api + /health → backend
│   └── package.json
│
├── .gitignore
└── README.md
```

---

## Quick Start

### Prerequisites

- **Node.js 18+** — [nodejs.org](https://nodejs.org)
- **IBM watsonx.ai** account — [ibm.com/watsonx](https://www.ibm.com/watsonx) (free tier available)
- IBM Cloud **API key** — [cloud.ibm.com/iam/apikeys](https://cloud.ibm.com/iam/apikeys)
- watsonx **Project ID** — [dataplatform.cloud.ibm.com/projects](https://dataplatform.cloud.ibm.com/projects)

> **No watsonx account?** Set `DEMO_MODE=true` in `.env` and the full app works with pre-written responses.

---

### 1. Clone & Install

```bash
git clone https://github.com/YOUR_USERNAME/soccer-sense.git
cd soccer-sense

# Install backend dependencies
cd backend && npm install

# Install frontend dependencies
cd ../frontend && npm install
```

### 2. Configure Environment

```bash
cd backend
cp .env.example .env
```

Edit `backend/.env`:

```env
WATSONX_API_KEY=your_ibm_cloud_api_key
WATSONX_PROJECT_ID=your_watsonx_project_id
WATSONX_URL=https://us-south.ml.cloud.ibm.com
WATSONX_MODEL_ID=ibm/granite-3-8b-instruct
PORT=3001
DEMO_MODE=false
FOOTBALL_DATA_KEY=your_football_data_key
```

### 3. Run

Open **two terminals**:

**Terminal 1 — Backend:**
```bash
cd backend
npm run dev
# → http://localhost:3001
```

**Terminal 2 — Frontend:**
```bash
cd frontend
npm run dev
# → http://localhost:5173
```

Open **[http://localhost:5173](http://localhost:5173)** in your browser.

---

## API Reference

### `GET /health`
Returns server status and mode.
```json
{ "status": "ok", "mode": "live", "service": "SoccerSense API" }
```

### `POST /api/explain`
Free-form match question.
```json
{
  "question": "Why did Brazil switch formations at 56 minutes?",
  "language": "es",
  "matchId": "bra-vs-arg-2026-final"
}
```

### `POST /api/tactics`
Tactical concept explanation.
```json
{
  "concept": "high press",
  "context": "Germany trailing 1-0 in the 70th minute",
  "matchId": "ger-vs-esp-2026-semi",
  "language": "de"
}
```

### `POST /api/var`
VAR decision explanation.
```json
{ "incidentId": "var-001", "language": "fr" }
```

### `POST /api/learn`
Soccer basics for new fans.
```json
{ "topic": "offside rule", "language": "pt" }
```

### `GET /api/match`
All available matches (summary).

### `GET /api/match/:matchId`
Single match with full events, lineups, and key moments.

### `GET /api/var/incidents`
All VAR incidents (list for UI selector).

---

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `WATSONX_API_KEY` | ✅ | IBM Cloud API key |
| `WATSONX_PROJECT_ID` | ✅ | watsonx.ai project ID |
| `WATSONX_URL` | ✅ | Regional endpoint (default: `us-south`) |
| `WATSONX_MODEL_ID` | ✅ | Model to use (default: `ibm/granite-3-8b-instruct`) |
| `PORT` | ✅ | Backend port (default: `3001`) |
| `DEMO_MODE` | ✅ | `true` = skip watsonx, use pre-written answers |
| `FOOTBALL_DATA_KEY` | ⬜ | [football-data.org](https://football-data.org) key for live data (optional) |

---

## Demo Mode

Set `DEMO_MODE=true` in `backend/.env` to run the full app with **no watsonx.ai account needed**. Pre-written expert responses cover all four tabs.

---

## IBM watsonx.ai Integration

### Prompt Engineering
All prompts use the **Granite 3 chat format** (`<|system|>`, `<|user|>`, `<|assistant|>`) with:
- A strong soccer companion persona
- Native-language enforcement phrases (e.g. `Responde ÚNICAMENTE en español`)
- Structured match context injected into every prompt (score, lineups, events)

### Rate Limit Handling
The `watsonxService` automatically retries on `429 Too Many Requests` using the `x-requests-limit-reset-after` header, with up to 4 retries and exponential backoff.

### Supported Models
Any model available in your watsonx project can be configured via `WATSONX_MODEL_ID`:
- `ibm/granite-3-8b-instruct` ✅ (recommended)
- `ibm/granite-3-1-8b-base`
- `meta-llama/llama-3-3-70b-instruct`
- `mistralai/mistral-small-3-1-24b-instruct-2503`

---

## Matches Included

| Match | Stage | Score |
|-------|-------|-------|
| Brazil vs Argentina | Final | 2–3 |
| England vs France | Semi-Final | 1–2 |
| Germany vs Spain | Semi-Final | 2–2 (pens) |
| Morocco vs Portugal | Quarter-Final | 1–0 |
| USA vs Mexico | Round of 16 | 2–1 |
| Japan vs South Korea | Round of 16 | 3–2 |

---

## Contributing

1. Fork the repo
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit: `git commit -m 'Add my feature'`
4. Push: `git push origin feature/my-feature`
5. Open a Pull Request

---

## Security

⚠️ **Never commit your `.env` file or `apikey.json`.** Both are listed in `.gitignore`.  
If you accidentally commit a secret, rotate it immediately at [cloud.ibm.com/iam/apikeys](https://cloud.ibm.com/iam/apikeys).

---

## License

MIT © 2026 Mathew Simmers

---

*Built for the IBM watsonx.ai World Cup 2026 Challenge · Powered by IBM Granite*
