# Intervals.icu API Endpoints Reference

Complete reference for all Intervals.icu API endpoints with curl examples.

## Authentication Header

All requests require Basic Authentication:
```bash
-u "API_KEY:${API_KEY}"
```

## Common Headers

```bash
-H "Accept: application/json"
-H "User-Agent: claude-intervals-skill/1.0"
```

For POST/PUT requests, add:
```bash
-H "Content-Type: application/json"
```

---

## Athlete Endpoints

### GET /athlete/{athlete_id}

Retrieve comprehensive athlete profile including training zones.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response Example:**
```json
{
  "id": "i123456",
  "name": "John Doe",
  "sex": "M",
  "city": "New York",
  "country": "US",
  "timezone": "America/New_York",
  "icu_weight": 75.0,
  "height": 1.80,
  "icu_resting_hr": 52,
  "icu_date_of_birth": "1985-06-15",
  "sportSettings": [
    {
      "types": ["Ride", "VirtualRide"],
      "ftp": 280,
      "lthr": 168,
      "max_hr": 185,
      "power_zones": [55, 75, 90, 105, 120, 150, 999],
      "power_zone_names": ["Z1", "Z2", "Z3", "Z4", "Z5", "Z6", "Z7"],
      "hr_zones": [106, 127, 143, 160, 168, 999],
      "hr_zone_names": ["Z1", "Z2", "Z3", "Z4", "Z5a", "Z5b"]
    },
    {
      "types": ["Run", "VirtualRun"],
      "lthr": 172,
      "max_hr": 190,
      "threshold_pace": 4.17,
      "pace_zones": [75, 85, 95, 100, 110, 999],
      "pace_zone_names": ["Z1", "Z2", "Z3", "Z4", "Z5a", "Z5b"]
    }
  ]
}
```

---

## Activity Endpoints

### GET /athlete/{athlete_id}/activities

List activities in a date range.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/activities?oldest=2024-01-01&newest=2024-01-31&limit=20" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| `oldest` | string | No | Start date (YYYY-MM-DD), default: 30 days ago |
| `newest` | string | No | End date (YYYY-MM-DD), default: today |
| `limit` | int | No | Max activities to return, default: 10 |

**Response:** Array of activity objects

### GET /activity/{activity_id}

Get detailed activity data.

```bash
curl -X GET "https://intervals.icu/api/v1/activity/i123456_789" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response Fields:**
```json
{
  "id": "i123456_789",
  "name": "Morning Ride",
  "type": "Ride",
  "startTime": "2024-01-15T07:30:00Z",
  "distance": 45000,
  "duration": 5400,
  "moving_time": 5200,
  "elevation_gain": 450,
  "avg_power": 185,
  "weighted_avg_power": 195,
  "avg_hr": 145,
  "max_hr": 172,
  "cadence": 88,
  "training_load": 75,
  "intensity": 0.70,
  "ftp": 280,
  "lthr": 168,
  "ctl": 65,
  "atl": 72,
  "kilojoules": 998,
  "calories": 1150,
  "average_speed": 8.33,
  "max_speed": 15.2,
  "average_temp": 18,
  "trainer": false,
  "device_name": "Garmin Edge 540"
}
```

### GET /activity/{activity_id}/intervals

Get interval analysis for an activity.

```bash
curl -X GET "https://intervals.icu/api/v1/activity/i123456_789/intervals" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response:**
```json
{
  "id": "i123456_789",
  "analyzed": true,
  "icu_intervals": [
    {
      "type": "WORK",
      "label": "Interval 1",
      "start_index": 100,
      "end_index": 400,
      "elapsed_time": 300,
      "moving_time": 298,
      "distance": 2500,
      "average_watts": 285,
      "max_watts": 310,
      "average_watts_kg": 3.8,
      "weighted_average_watts": 290,
      "average_heartrate": 165,
      "max_heartrate": 175,
      "average_cadence": 92,
      "average_speed": 8.33,
      "zone": 4,
      "intensity": 1.02,
      "training_load": 15
    }
  ],
  "icu_groups": [
    {
      "type": "INTERVALS",
      "label": "Main Set",
      "start_index": 100,
      "end_index": 2000,
      "elapsed_time": 1900,
      "average_watts": 250,
      "average_heartrate": 158
    }
  ]
}
```

---

## Event Endpoints

### GET /athlete/{athlete_id}/events

List events in a date range.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events?oldest=2024-01-01&newest=2024-01-31" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response:** Array of event objects
```json
[
  {
    "id": 123456,
    "start_date_local": "2024-01-15T00:00:00",
    "type": "Ride",
    "category": "WORKOUT",
    "name": "Threshold Intervals",
    "description": "4x8min @ 100% FTP",
    "moving_time": 3600,
    "distance": 40000
  }
]
```

### GET /athlete/{athlete_id}/events/{event_id}

Get detailed event information.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events/123456" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

### POST /athlete/{athlete_id}/events

Create a new event.

```bash
curl -X POST "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date_local": "2024-02-01T00:00:00",
    "category": "WORKOUT",
    "name": "Sweet Spot Intervals",
    "description": "Sweet spot workout\n\nWarmup\n- 10m 65% ftp\n\n3x\n- 10m 90% ftp\n- 5m 55% ftp\n\nCooldown\n- 10m 55% ftp",
    "type": "Ride",
    "moving_time": 3900,
    "distance": 35000
  }'
```

**Request Fields:**
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `start_date_local` | string | Yes | ISO datetime (YYYY-MM-DDTHH:MM:SS) |
| `category` | string | Yes | Event category: WORKOUT, RACE, NOTE |
| `name` | string | Yes | Event name |
| `description` | string | No | Workout description (parsed for structure) |
| `type` | string | Yes | Sport type: Ride, Run, Swim, etc. |
| `moving_time` | int | No | Expected duration in seconds |
| `distance` | int | No | Expected distance in meters |

### PUT /athlete/{athlete_id}/events/{event_id}

Update an existing event.

```bash
curl -X PUT "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events/123456" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "start_date_local": "2024-02-02T00:00:00",
    "name": "Updated Workout Name",
    "description": "Updated description"
  }'
```

### DELETE /athlete/{athlete_id}/events/{event_id}

Delete an event.

```bash
curl -X DELETE "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events/123456" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

### GET /athlete/{athlete_id}/events/{event_id}/downloadzwo

Download workout in Zwift ZWO format.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events/123456/downloadzwo" \
  -u "API_KEY:${API_KEY}" \
  -o "workout.zwo"
```

**Response:** XML content
```xml
<?xml version="1.0" encoding="UTF-8"?>
<workout_file>
  <name>Sweet Spot Intervals</name>
  <workout>
    <Warmup Duration="600" PowerLow="0.65" PowerHigh="0.65"/>
    <SteadyState Duration="600" Power="0.90"/>
    <SteadyState Duration="300" Power="0.55"/>
    ...
    <Cooldown Duration="600" PowerLow="0.55" PowerHigh="0.55"/>
  </workout>
</workout_file>
```

---

## Wellness Endpoints

### GET /athlete/{athlete_id}/wellness

Get wellness data for a date range.

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/wellness?oldest=2024-01-01&newest=2024-01-31" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response:** Array of wellness entries
```json
[
  {
    "id": "2024-01-15",
    "ctl": 65.2,
    "atl": 72.1,
    "rampRate": 0.5,
    "weight": 75.0,
    "restingHR": 52,
    "hrv": 45,
    "hrvSDNN": 48,
    "sleepSecs": 27000,
    "sleepScore": 85,
    "sleepQuality": 4,
    "readiness": 75,
    "fatigue": 2,
    "stress": 2,
    "mood": 3,
    "motivation": 4,
    "soreness": 3,
    "injury": 1,
    "steps": 8500,
    "kcalConsumed": 2200,
    "hydrationVolume": 2500,
    "spO2": 98,
    "systolic": 120,
    "diastolic": 80,
    "sportInfo": [
      {
        "type": "Ride",
        "eftp": 282
      }
    ]
  }
]
```

**Wellness Fields:**
| Field | Type | Description |
|-------|------|-------------|
| `id` | string | Date (YYYY-MM-DD) |
| `ctl` | float | Chronic Training Load (fitness) |
| `atl` | float | Acute Training Load (fatigue) |
| `rampRate` | float | Rate of CTL change |
| `weight` | float | Body weight in kg |
| `restingHR` | int | Resting heart rate (bpm) |
| `hrv` | int | Heart rate variability (RMSSD) |
| `hrvSDNN` | int | HRV SDNN |
| `sleepSecs` | int | Sleep duration in seconds |
| `sleepScore` | int | Sleep score (0-100) |
| `sleepQuality` | int | Sleep quality (1-4) |
| `fatigue` | int | Fatigue level (1-4) |
| `stress` | int | Stress level (1-4) |
| `mood` | int | Mood level (1-4) |
| `motivation` | int | Motivation level (1-4) |
| `soreness` | int | Muscle soreness (1-14) |
| `steps` | int | Daily step count |
| `spO2` | int | Blood oxygen saturation (%) |
| `systolic` | int | Systolic blood pressure |
| `diastolic` | int | Diastolic blood pressure |

---

## Error Responses

All endpoints return error responses in this format:

```json
{
  "error": true,
  "status_code": 401,
  "message": "Please check your API key."
}
```

**Common Status Codes:**
| Code | Meaning | Resolution |
|------|---------|------------|
| 401 | Unauthorized | Check API key |
| 403 | Forbidden | Check permissions |
| 404 | Not Found | Check resource ID |
| 422 | Unprocessable Entity | Check request body |
| 429 | Too Many Requests | Wait and retry |
| 500 | Server Error | Retry later |
| 503 | Service Unavailable | Retry later |

---

## Rate Limiting

The API has rate limiting. If you receive a 429 response:
1. Wait for the time specified in the `Retry-After` header
2. Implement exponential backoff for retries
3. Consider caching responses where appropriate

---

## Pagination Tips

For large date ranges:
1. Use smaller date windows (e.g., 30 days)
2. Set appropriate `limit` values
3. Make multiple requests if needed

Example for fetching all activities in a year:
```bash
# Fetch month by month
for month in $(seq 1 12); do
  start=$(printf "2024-%02d-01" $month)
  end=$(printf "2024-%02d-28" $month)
  curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/activities?oldest=${start}&newest=${end}&limit=100" \
    -u "API_KEY:${API_KEY}" \
    -H "Accept: application/json"
done
```
