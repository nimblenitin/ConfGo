# ConfGo

A multi-agent AI tool that plans your trip to attend tech conferences. Type in the conference name - for example, "Plan a trip to attend PyTorch Conference 2026 in North America with mid-range budget" - and a team of AI agents goes and researches it for you, getting back flights, hotels, weather, and a day-by-day itinerary in about a minute.

- **Flights** - airports, airlines, duration, fare range
- **Hotels** - options matched to your budget and location
- **Weather** - conditions and forecast for your travel dates
- **Itinerary** - a realistic day-by-day plan
- **Budget** - estimated cost breakdown

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

## Architecture

```mermaid
flowchart LR
    User([User]) -->|Message| UI[ConfGo UI]
    UI -->|POST /api/travel| API[FastAPI]
    API --> Graph[LangGraph Pipeline]
    
    Graph --> A1[Flight Agent]
    Graph --> A2[Hotel Agent]
    Graph --> A3[Weather Agent]
    Graph --> A4[Itinerary Agent]
    Graph --> A5[Final Agent]
    
    A1 -->|AviationStack MCP| DB1[Airport Data]
    A1 -->|Groq LLM| LLM1[Flight Analysis]
    
    A2 -->|Tavily MCP| WEB[Hotel Search]
    
    A3 -->|OpenWeather MCP| WX[Weather Data]
    A3 -->|Groq LLM| LLM2[Destination Extract]
    
    A4 -->|Groq LLM| LLM3[Itinerary Planning]
    A4 -->|Reads| A1
    A4 -->|Reads| A2
    A4 -->|Reads| A3
    
    A5 -->|Groq LLM| LLM4[Final Response]
    A5 -->|Reads| A4
    
    API -->|Checkpoints| PG[(PostgreSQL)]
    
    style User fill:#4f46e5,stroke:#3730a3,color:#fff
    style API fill:#059669,stroke:#047857,color:#fff
    style Graph fill:#d97706,stroke:#b45309,color:#fff
    style PG fill:#2563eb,stroke:#1d4ed8,color:#fff
```

```mermaid
flowchart TD
    subgraph Request Flow
        U[User sends message] --> R[Runner validates credentials]
        R --> G[LangGraph executes pipeline]
        G --> F[Flight Agent - AviationStack]
        F --> H[Hotel Agent - Tavily]
        H --> W[Weather Agent - OpenWeather]
        W --> I[Itinerary Agent - Groq LLM]
        I --> S[Final Agent - Formats response]
        S --> P[Response sent back]
    end
    
    subgraph External Services
        GQ[Groq API]
        TV[Tavily API]
        AS[AviationStack API]
        OW[OpenWeather API]
    end
    
    F --> GQ
    H --> TV
    W --> OW
    I --> GQ
    S --> GQ
    
    style U fill:#4f46e5,stroke:#3730a3,color:#fff
    style P fill:#059669,stroke:#047857,color:#fff
```
