# Intervals.icu API Skill

This skill enables Claude to interact with the Intervals.icu training platform API to manage athlete data, activities, events, workouts, and wellness metrics.

## Prerequisites

Before using this skill, ensure the following environment variables are set:
- `API_KEY`: Your Intervals.icu API key (from Settings > API in Intervals.icu)
- `ATHLETE_ID`: Your athlete ID (from URL: intervals.icu/athlete/i12345/...)

The athlete ID can be in either format: `123456` or `i123456`

## Authentication

All API requests use HTTP Basic Authentication:
- **Username**: `API_KEY` (literal string)
- **Password**: Your actual API key value

## Base URL

```
https://intervals.icu/api/v1
```

## Making API Requests

Use curl or similar tools with the following pattern:

```bash
curl -X GET "https://intervals.icu/api/v1/athlete/{athlete_id}/activities" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json" \
  -H "User-Agent: claude-intervals-skill/1.0"
```

For POST/PUT requests, add:
```bash
-H "Content-Type: application/json" \
-d '{"key": "value"}'
```

## Available Operations

### 1. Get Activities

Retrieve a list of activities for an athlete.

**Endpoint**: `GET /athlete/{athlete_id}/activities`

**Query Parameters**:
- `oldest`: Start date (YYYY-MM-DD)
- `newest`: End date (YYYY-MM-DD)
- `limit`: Maximum number of activities (default: 10)

**Example**:
```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/activities?oldest=2024-01-01&newest=2024-01-31&limit=10" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

**Response Fields**:
- `id`: Activity ID
- `name`: Activity name
- `type`: Activity type (Ride, Run, Swim, etc.)
- `startTime`: Start date/time
- `distance`: Distance in meters
- `duration`: Duration in seconds
- `moving_time`: Moving time in seconds
- `avg_power`, `weighted_avg_power`: Power metrics
- `avg_hr`, `max_hr`: Heart rate data
- `elevation_gain`: Elevation gained
- `training_load`: Training load value
- `ctl`, `atl`: Fitness (CTL) and Fatigue (ATL)

### 2. Get Activity Details

Get detailed information for a specific activity.

**Endpoint**: `GET /activity/{activity_id}`

**Example**:
```bash
curl -X GET "https://intervals.icu/api/v1/activity/{activity_id}" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

### 3. Get Activity Intervals

Get interval data for a specific activity.

**Endpoint**: `GET /activity/{activity_id}/intervals`

**Response Contains**:
- `icu_intervals`: Array of individual intervals with power, HR, cadence, speed metrics
- `icu_groups`: Grouped intervals if applicable

### 4. Get Events

Retrieve events (workouts, races) in a date range.

**Endpoint**: `GET /athlete/{athlete_id}/events`

**Query Parameters**:
- `oldest`: Start date (YYYY-MM-DD), defaults to today
- `newest`: End date (YYYY-MM-DD), defaults to +30 days

**Example**:
```bash
curl -X GET "https://intervals.icu/api/v1/athlete/${ATHLETE_ID}/events?oldest=2024-01-01&newest=2024-01-31" \
  -u "API_KEY:${API_KEY}" \
  -H "Accept: application/json"
```

### 5. Get Event by ID

Get detailed information for a specific event.

**Endpoint**: `GET /athlete/{athlete_id}/events/{event_id}`

### 6. Create Event (Workout)

Create a new workout event.

**Endpoint**: `POST /athlete/{athlete_id}/events`

**Request Body**:
```json
{
  "start_date_local": "2024-06-15T00:00:00",
  "category": "WORKOUT",
  "name": "Threshold Intervals",
  "description": "Workout description with steps",
  "type": "Ride",
  "moving_time": 3600,
  "distance": 40000
}
```

**Workout Types**: `Ride`, `Run`, `Swim`, `Walk`, `Row`, `VirtualRide`, `VirtualRun`

### 7. Update Event

Update an existing event.

**Endpoint**: `PUT /athlete/{athlete_id}/events/{event_id}`

Uses same body structure as create.

### 8. Delete Event

Delete a single event.

**Endpoint**: `DELETE /athlete/{athlete_id}/events/{event_id}`

### 9. Get Wellness Data

Retrieve wellness metrics (sleep, HRV, weight, etc.).

**Endpoint**: `GET /athlete/{athlete_id}/wellness`

**Query Parameters**:
- `oldest`: Start date (YYYY-MM-DD)
- `newest`: End date (YYYY-MM-DD)

**Response Fields**:
- `ctl`, `atl`: Fitness and fatigue
- `weight`: Body weight in kg
- `restingHR`: Resting heart rate
- `hrv`, `hrvSDNN`: Heart rate variability
- `sleepSecs`, `sleepScore`, `sleepQuality`: Sleep data
- `fatigue`, `stress`, `mood`, `motivation`: Subjective feelings (1-4 scale)
- `soreness`: Muscle soreness (1-14 scale)

### 10. Get Athlete Profile

Get comprehensive athlete data including training zones.

**Endpoint**: `GET /athlete/{athlete_id}`

**Response Includes**:
- Basic info: name, weight, height, resting HR
- `sportSettings`: Array of sport-specific settings with:
  - `ftp`: Functional Threshold Power
  - `lthr`: Lactate Threshold Heart Rate
  - `max_hr`: Maximum heart rate
  - `threshold_pace`: Threshold pace (m/s)
  - `power_zones`, `power_zone_names`: Power zone definitions
  - `hr_zones`, `hr_zone_names`: HR zone definitions
  - `pace_zones`, `pace_zone_names`: Pace zone definitions

### 11. Download Workout as ZWO

Download a workout in Zwift format.

**Endpoint**: `GET /athlete/{athlete_id}/events/{event_id}/downloadzwo`

Returns XML content compatible with Zwift.

## Workout Description Format

When creating workouts, the `description` field supports a structured format for defining workout steps. This is parsed by Intervals.icu to create structured workouts.

### Step Syntax

Each step can define:
- Duration (seconds) or Distance (meters)
- Power target (% FTP, watts, or zone)
- Heart rate target (% HR, % LTHR, or zone)
- Pace target (% threshold or zone)
- Cadence target (rpm)

### Example Workout Description

```
High-intensity threshold workout

Warmup
- 15m 75% ftp

3x
- 5m 100% ftp Threshold effort
- 3m 60% ftp Recovery

Cooldown
- 10m 65% ftp
```

### Structured Workout JSON (workout_doc)

For precise workout definitions, use a JSON structure:

```json
{
  "description": "VO2 Max Intervals",
  "steps": [
    {"power": {"value": 75, "units": "%ftp"}, "duration": 900, "warmup": true},
    {"reps": 5, "steps": [
      {"power": {"value": 120, "units": "%ftp"}, "duration": 180, "text": "VO2 Max"},
      {"power": {"value": 55, "units": "%ftp"}, "duration": 180, "text": "Recovery"}
    ]},
    {"power": {"value": 65, "units": "%ftp"}, "duration": 600, "cooldown": true}
  ]
}
```

### Step Properties

| Property | Description | Example |
|----------|-------------|---------|
| `duration` | Duration in seconds | `"duration": 300` |
| `distance` | Distance in meters | `"distance": 1000` |
| `power` | Power target | `{"value": 80, "units": "%ftp"}` |
| `hr` | Heart rate target | `{"value": 75, "units": "%lthr"}` |
| `pace` | Pace target | `{"value": 90, "units": "%pace"}` |
| `cadence` | Cadence target | `{"value": 90, "units": "rpm"}` |
| `warmup` | Mark as warmup | `"warmup": true` |
| `cooldown` | Mark as cooldown | `"cooldown": true` |
| `ramp` | Gradual intensity change | `"ramp": true` |
| `freeride` | Free ride segment | `"freeride": true` |
| `text` | Step label/description | `"text": "Threshold effort"` |
| `reps` | Number of repetitions | `"reps": 5` |
| `steps` | Nested steps for repeats | Array of step objects |

### Value Units

| Unit | Description | Example |
|------|-------------|---------|
| `%ftp` | Percentage of FTP | `{"value": 100, "units": "%ftp"}` |
| `w` | Absolute watts | `{"value": 250, "units": "w"}` |
| `power_zone` | Power zone | `{"value": 4, "units": "power_zone"}` |
| `%hr` | Percentage of max HR | `{"value": 80, "units": "%hr"}` |
| `%lthr` | Percentage of LTHR | `{"value": 95, "units": "%lthr"}` |
| `hr_zone` | HR zone | `{"value": 3, "units": "hr_zone"}` |
| `%pace` | Percentage of threshold pace | `{"value": 100, "units": "%pace"}` |
| `pace_zone` | Pace zone | `{"value": 2, "units": "pace_zone"}` |
| `rpm` | Cadence in RPM | `{"value": 95, "units": "rpm"}` |

### Range and Ramp Definitions

For power/HR ranges:
```json
{"power": {"start": 80, "end": 90, "units": "%ftp"}}
```

For ramps (gradual changes):
```json
{"ramp": true, "power": {"start": 70, "end": 100, "units": "%ftp"}, "duration": 300}
```

## Speed/Pace Formatting

Convert speeds appropriately by activity type:
- **Cycling**: m/s → km/h (multiply by 3.6)
- **Running**: m/s → min/km (divide 1000 by (speed * 60))
- **Swimming**: m/s → min/100m (divide 100 by (speed * 60))

## Error Handling

Common HTTP status codes:
- `401`: Invalid API key
- `403`: No permission to access resource
- `404`: Resource not found
- `422`: Invalid parameters
- `429`: Rate limited
- `500/503`: Server error

## Date Utilities

When working with dates:
- Always use YYYY-MM-DD format
- Default date ranges:
  - Activities: Last 30 days
  - Events: Today + 30 days
  - Wellness: Last 30 days

## Formatting Output

When presenting data to users:

### Activity Summary Format
```
Activity: [name]
ID: [id]
Type: [type]
Date: [startTime]
Distance: [distance] meters
Duration: [duration] seconds

Power Data:
- Average Power: [avg_power] W
- Weighted Avg Power: [weighted_avg_power] W
- Training Load: [training_load]

Heart Rate Data:
- Average Heart Rate: [avg_hr] bpm
- Max Heart Rate: [max_hr] bpm
```

### Wellness Data Format
```
Wellness Data:
Date: [id]

Training Metrics:
- Fitness (CTL): [ctl]
- Fatigue (ATL): [atl]

Vital Signs:
- Weight: [weight] kg
- Resting HR: [restingHR] bpm
- HRV: [hrv]

Sleep & Recovery:
- Sleep: [sleepHours] hours
- Sleep Score: [sleepScore]
```

## Best Practices

1. **Filter Activities**: By default, filter out unnamed activities for cleaner results
2. **Batch Operations**: When deleting multiple events, iterate through the date range
3. **Workout Types**: Infer workout type from name if not specified (bike/cycle→Ride, run→Run, swim→Swim)
4. **Pagination**: Use the `limit` parameter and fetch more if needed
5. **Date Validation**: Always validate date format before API calls
