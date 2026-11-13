<div align="center">

# ✈️ TripPilot AI

### A multi-agent travel planner powered by LangGraph

Describe your trip in plain English and get flight options, hotel ideas and a day-by-day itinerary, all in one place and downloadable as a PDF.

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)
![LangGraph](https://img.shields.io/badge/LangGraph-multi--agent-1C3C3C)
![Groq](https://img.shields.io/badge/LLM-Groq-F55036)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)

Deployed On Render | Live Link : https://travel-planner-gww0.onrender.com

<img width="953" height="563" alt="image" src="https://github.com/user-attachments/assets/a41bab79-f838-4f2a-a896-cd15283fb051" />


</div>

---




## Overview

Planning a trip usually means juggling flight sites, hotel listings, blogs and spreadsheets. **TripPilot AI** replaces that with one prompt. A team of specialised agents, coordinated by a LangGraph workflow, researches each part of the trip and combines the results into a single, readable plan.

> **Example prompt:** *Plan a complete 7 days Japan trip from India including flights, hotels and sightseeing under 2 lakhs.*

## Highlights

- **Multi-agent workflow:** separate agents for flights, hotels, itinerary and the final answer, orchestrated with LangGraph.
- **Live flight data:** flight research through the AviationStack API.
- **Fresh hotel research:** accommodation suggestions through Tavily web search.
- **Day-by-day itineraries:** budget-aware plans with sightseeing, timing and practical tips.
- **Conversation memory:** threads are persisted in PostgreSQL, so follow-ups such as *"make it cheaper"* keep their context.
- **Fast LLM responses:** inference through Groq.
- **Polished web UI:** a responsive light-theme interface with one-tap quick prompts, rendered Markdown, a **Copy** button and **Download PDF**.
- **Keyboard shortcut:** press `Ctrl + Enter` to generate a plan.

## How It Works

```text
                ┌────────────────────┐
  User request ─►  FastAPI /api/travel│
                └─────────┬──────────┘
                          ▼
                ┌────────────────────┐
                │  LangGraph workflow │◄──── PostgreSQL (thread state)
                └─────────┬──────────┘
        ┌─────────────────┼─────────────────┐
        ▼                 ▼                 ▼
  ✈️ Flight agent   🏨 Hotel agent   🗺️ Itinerary agent
  (AviationStack)     (Tavily)          (Groq LLM)
        └─────────────────┼─────────────────┘
                          ▼
                 ✍️ Final response agent
                          ▼
              Formatted Markdown travel plan
```

1. The user submits a travel request from the web UI.
2. The **flight agent** gathers flight information.
3. The **hotel agent** searches for accommodation options.
4. The **itinerary agent** builds a practical day-by-day plan around the results.
5. The **final agent** merges everything into a clean Markdown answer.
6. The conversation is stored under a `thread_id`, so the user can continue refining the plan.

## Tech Stack

| Layer | Technology |
| --- | --- |
| Backend | Python 3.10+, FastAPI |
| Agent orchestration | LangGraph, LangChain |
| LLM | Groq |
| Persistence | PostgreSQL |
| Search and data | Tavily API, AviationStack API |
| Frontend | HTML, CSS, vanilla JavaScript, Jinja2 |
| UI libraries | marked (Markdown rendering), html2pdf.js (PDF export) |

## Project Structure

```text
.
├── app.py                # FastAPI app entry point
├── backend.py            # LangGraph travel workflow
├── requirements.txt      # Python dependencies
├── static/
│   ├── style.css         # UI styling
│   └── script.js         # Frontend logic (requests, Markdown, PDF, copy)
├── templates/
│   └── index.html        # Main page template
└── tools/                # Flight and web search integrations
```

## Getting Started

### Prerequisites

- Python **3.10** or newer
- A running **PostgreSQL** instance
- API keys for **Groq**, **Tavily** and **AviationStack**

### 1. Clone and install

```bash
git clone <your-repo-url>
cd <your-repo-folder>

python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
```

### 2. Configure environment variables

Create a `.env` file in the project root:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/travel_db
GROQ_API_KEY=your_groq_api_key
AVIATIONSTACK_API_KEY=your_aviationstack_api_key
TAVILY_API_KEY=your_tavily_api_key
DEFAULT_ORIGIN_IATA=DAC
```

| Variable | Description |
| --- | --- |
| `DATABASE_URL` | PostgreSQL connection string used for conversation state |
| `GROQ_API_KEY` | API key for Groq LLM inference |
| `AVIATIONSTACK_API_KEY` | API key for flight data |
| `TAVILY_API_KEY` | API key for hotel and web search |
| `DEFAULT_ORIGIN_IATA` | Fallback departure airport code (for example `DAC`, `DEL`, `HYD`) |

> Never commit your `.env` file. Add it to `.gitignore`.

### 3. Run the app

```bash
python app.py
```

Open **http://127.0.0.1:8000/** in your browser.

## Using the App

1. Type your trip request, or tap a quick prompt (Japan, Dubai, Thailand, Global Flights).
2. Click **Generate Plan** or press `Ctrl + Enter`.
3. Read the formatted plan, then **Copy** it or **Download PDF**.
4. Send another message to refine it. The same thread is reused automatically.

## API Reference

| Method | Endpoint | Description |
| --- | --- | --- |
| `GET` | `/health` | Health check |
| `POST` | `/api/travel` | Submit a travel request |

### `POST /api/travel`

**Request body**

```json
{
  "message": "Plan a 3-day trip to Tokyo with a budget of $1200",
  "thread_id": null
}
```

Pass the `thread_id` from a previous response to continue the same conversation. Use `null` to start a new one.

**Example**

```bash
curl -X POST http://127.0.0.1:8000/api/travel \
  -H "Content-Type: application/json" \
  -d '{"message":"Plan a 3-day trip to Tokyo with a budget of $1200"}'
```

**Success response**

```json
{
  "success": true,
  "thread_id": "abc123",
  "answer": "# Your Tokyo Trip\n..."
}
```

**Error response**

```json
{
  "success": false,
  "error": "Something went wrong."
}
```

## Troubleshooting

| Problem | What to check |
| --- | --- |
| Server won't start | Confirm your virtual environment is active and dependencies are installed |
| Database errors | Verify `DATABASE_URL` and that PostgreSQL is running and reachable |
| No flight results | Check your AviationStack key and plan limits. Free tiers are restricted |
| Empty hotel suggestions | Check your Tavily key and quota |
| LLM errors or timeouts | Check your Groq key and rate limits |
| UI changes not showing | Hard refresh with `Ctrl + Shift + R` to clear the cached CSS and JS |

## Roadmap

- [ ] Streaming responses
- [ ] Multi-city and round-trip planning
- [ ] Currency conversion and budget breakdown
- [ ] Map view for itinerary stops
- [ ] Saved trips and shareable links
- [ ] Docker setup for one-command start

## Contributing

Contributions are welcome.

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes
4. Push the branch and open a pull request

Please keep changes focused and describe what you tested.

## Acknowledgments

Built with [LangGraph](https://github.com/langchain-ai/langgraph), [LangChain](https://github.com/langchain-ai/langchain), [FastAPI](https://fastapi.tiangolo.com/), [Groq](https://groq.com/), [Tavily](https://tavily.com/) and [AviationStack](https://aviationstack.com/). TripPilot AI is a practical example of combining LangGraph agents with real-world travel APIs.

> **Disclaimer:** Prices, schedules and availability come from third-party APIs and AI-generated content, and can be incomplete or out of date. Always verify details with the airline or hotel before booking.






## travel-planner


## How to run ?

1. Create the virtual environment

```
python -m venv travel
```

2. Activate environment

```
.\travel\Scripts\Activate.ps1
```

3. Install the requirements


Before installing actual packages, dry run to check if any conflicts are occuring with package versions.
It only simulates the installation.

```
pip install --dry-run -r requirements.txt
```

If everything has run smoothly, then run below command to actually install packages.

```
python -m pip install -r requirements.txt
```
