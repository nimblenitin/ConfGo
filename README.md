# ConfGo

A multi-agent AI tool that plans your trip to attend tech conferences. Type in the conference name and get flights, hotels, weather, and a day-by-day itinerary in about a minute.

## What it does

You describe the conference trip you want - for example, "Plan a trip to attend PyTorch Conference 2026 in North America with mid-range budget" - and a team of AI agents goes and researches it for you.

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

## Usage

Just type what you want in the message box. Some examples:

> Plan a trip to attend PyTorch Conference 2026 in North America with mid-range budget

> I need to attend AWS re:Invent 2026 in Las Vegas, 5 days, budget around $3000

A plan takes about 30-90 seconds to build. Your results are split into tabs - Plan, Itinerary, Flights, Hotels, Weather.

Every trip is saved automatically. You can click any saved trip in the sidebar to reopen it and ask follow-up questions.

## Troubleshooting

**Page loads but planning fails** - Check the status indicator at the bottom of the sidebar. If it says "API unreachable", restart the app with `uv run python app.py`.

**Model not found error** - The AI model may have been retired. Check available models:

```bash
curl -s https://api.groq.com/openai/v1/models -H "Authorization: Bearer $GROQ_API_KEY"
```

Pick one and set it as `GROQ_MODEL` in your `.env`.

**DATABASE_URL missing** - Add a PostgreSQL connection string to your `.env` file.

## Tech stack

- FastAPI + Uvicorn
- LangGraph for agent orchestration
- Groq for AI inference
- Tavily for hotel search
- AviationStack for flight data
- OpenWeather for weather
- PostgreSQL for saving trips


