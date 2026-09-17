# ConfGo

<p align="center">
  <img src="assets/confgo-logo.png" alt="ConfGo" width="360" />
</p>

<p align="center">
  A multi-agent AI tool that plans your trip to attend tech conferences.
</p>

Type in the conference name - for example, "Plan a trip to attend PyTorch Conference 2026 in North America with mid-range budget" - and a team of AI agents goes and researches it for you, getting back flights, hotels, weather, and a day-by-day itinerary in about a minute.

- **Flights** - airports, airlines, duration, fare range
- **Hotels** - options matched to your budget and location
- **Weather** - conditions and forecast for your travel dates
- **Itinerary** - a realistic day-by-day plan
- **Budget** - estimated cost breakdown

## Architecture Overview

<p align="center">
  <img src="assets/confgo_arch.svg" alt="ConfGo Architecture" width="860"/>
</p>

*The user sends a natural-language conference trip request through the ConfGo UI. The FastAPI server resolves credentials, then dispatches the request through a LangGraph pipeline of five specialized agents. Each agent calls external services via MCP (Model Context Protocol) and the Groq LLM, building up flight, hotel, weather, and itinerary data. The final agent formats everything into a polished response, and the conversation is checkpointed to PostgreSQL for multi-turn continuity.*

<details>
<summary><b>Detailed Agent Flow</b></summary>

```
User Request
     |
     v
[FastAPI Server]
     |
     v
[LangGraph Pipeline]
     |
     +---> [Flight Agent] ---> AviationStack MCP ---> Groq LLM
     |
     +---> [Hotel Agent] ---> Tavily MCP
     |
     +---> [Weather Agent] ---> OpenWeather MCP ---> Groq LLM
     |
     +---> [Itinerary Agent] ---> Groq LLM (reads all above)
     |
     +---> [Final Agent] ---> Groq LLM (formats response)
     |
     v
[PostgreSQL Checkpoint]
     |
     v
Response to User
```
</details>

## Getting started

### What you need

- Python 3.11
- [uv](https://docs.astral.sh/uv/) for installing dependencies
- A PostgreSQL database (free Render instance works fine)
- API keys from Groq, Tavily, AviationStack, and OpenWeather (all have free tiers)

### Setup

Clone and install:

```bash
git clone https://github.com/nimblenitin/ConfGo.git
cd ConfGo
uv sync
```

Create a `.env` file with your keys:

```dotenv
GROQ_API_KEY=your_groq_key
DATABASE_URL=postgresql://user:password@host:5432/dbname
TAVILY_API_KEY=your_tavily_key
AVIATIONSTACK_API_KEY=your_aviationstack_key
OPENWEATHER_API_KEY=your_openweather_key
```

Start the app:

```bash
uv run python app.py
```

Open http://127.0.0.1:8000 in your browser. You should see a green "API connected" dot at the bottom.

## Tech stack

- FastAPI + Uvicorn
- LangGraph for agent orchestration
- Groq for AI inference
- Tavily for hotel search
- AviationStack for flight data
- OpenWeather for weather
- PostgreSQL for saving trips

## Contributing

Happy to collaborate and open to receiving contributions to improve.
