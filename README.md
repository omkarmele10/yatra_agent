# Yatra Planner API

Yatra Planner API is a FastAPI service that generates a trip plan by combining destination weather data, travel suggestions, and currency exchange rates into a single response. It also supports streaming progress updates over Server-Sent Events (SSE) while the plan is being assembled.

## Features

- Travel plan generation for a destination and date range
- Weather forecast lookup for the requested trip dates
- Curated places and attractions for supported destinations
- Currency conversion rate lookup for a base currency
- In-memory caching to reduce repeated API calls
- Streaming event responses for real-time progress updates

## Tech Stack

- Python 3
- FastAPI
- Pydantic
- HTTPX
- Uvicorn

## Project Structure

```text
.
├── app/
│   ├── main.py               # FastAPI app entrypoint
│   ├── model.py              # Request/response models
│   ├── routes/
│   │   ├── planner.py        # Travel plan endpoint
│   │   └── stream.py         # SSE stream endpoint
│   └── services/
│       ├── cache.py          # In-memory TTL cache
│       ├── currency.py       # Exchange rate fetcher
│       ├── places.py         # Destination place data
│       └── weather.py        # Weather forecast fetcher
├── tests/
│   └── test_stream_serialization.py
├── requirements.txt
├── .env
└── README.md
```

## Getting Started

### 1. Create a virtual environment

```bash
cd yatra-app
python3 -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Run the application

From the project root:

```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

You can also run it from inside the `app` directory:

```bash
cd app
uvicorn main:app --reload --host 0.0.0.0 --port 8000
```

## API Endpoints

### Root

```http
GET /
```

Returns metadata about the API and available routes.

### Create a travel plan

```http
POST /plan/
```

Request body:

```json
{
  "destination": "goa",
  "start_date": "2026-06-10",
  "end_date": "2026-06-12",
  "base_currency": "INR"
}
```

Response example:

```json
{
  "message": "Travel plan created successfully",
  "weather_data": [
    {
      "date": "2026-06-10",
      "condition": "Sunny",
      "temperature_high": 32.0,
      "temperature_low": 26.0,
      "humidity": 60.0,
      "rain_chance": 10.0
    }
  ],
  "places_data": [
    {
      "name": "Baga Beach",
      "description": "A popular beach in Goa known for its nightlife and water sports",
      "category": "Beach",
      "rating": 4.5,
      "estimated_time_hours": 3,
      "entry_fee": null
    }
  ],
  "currency_rates": {
    "USD": 0.012,
    "EUR": 0.011,
    "INR": 1.0
  }
}
```

### Stream a travel plan

```http
POST /stream/plan
```

This endpoint streams progress updates using SSE events such as `start`, `weather`, `options`, `currency`, and `complete`.

## Supported Destinations

The place recommendations are curated for the following destinations in `app/services/places.py`:

- `manali`
- `goa`
- `jaipur`

## Data Sources

The app fetches data from external APIs for:

- Weather: WeatherAPI
- Currency rates: Exchange Rate API

Results are cached in memory for one hour to reduce repeated network calls.

## Validation

Run the test suite with:

```bash
python3 -m unittest discover -s tests -v
```

## Notes

- Valid trip dates must be within 1 to 14 days.
- `start_date` must be earlier than or equal to `end_date`.
- External API responses depend on network access and service availability.

## License

This project does not currently specify a license.
