# Intervals.icu Workout Format Documentation

This document describes the format needed by intervals.icu to create structured workouts.

## Overview

Intervals.icu accepts structured workout data through its event API. The workout is defined using a `WorkoutDoc` object containing a description and a list of steps.

## WorkoutDoc Structure

The main workout document containing description, duration, steps, and settings.

### Properties

- `description` (Optional[str]): Overall workout description
- `description_locale` (Optional[Dict[str, str]]): Localized descriptions
- `duration` (Optional[int]): Total workout duration in seconds
- `distance` (Optional[float]): Total workout distance in meters
- `ftp` (Optional[int]): Functional Threshold Power in watts
- `lthr` (Optional[int]): Lactate Threshold Heart Rate in bpm
- `threshold_pace` (Optional[float]): Threshold pace in meters/sec
- `pace_units` (Optional[PaceUnits]): Units for pace (SECS_100M, SECS_100Y, MINS_KM, MINS_MILE, SECS_500M)
- `sport_settings` (Optional[SportSettings]): Sport-specific settings
- `category` (Optional[str]): Workout category
- `target` (Optional[WorkoutTarget]): Target type (AUTO, POWER, HR, PACE)
- `steps` (Optional[List[Step]]): List of workout steps
- `zone_times` (Optional[List]): Time in each zone
- `options` (Optional[Dict[str, str]]): Additional options
- `locales` (Optional[List[str]]): Supported locales

## Step Structure

Each step in the workout can have the following properties:

### Basic Properties

- `text` (Optional[str]): Descriptive text for the step
- `text_locale` (Optional[Dict[str, str]]): Localized text
- `duration` (Optional[int]): Duration of step in seconds
- `distance` (Optional[float]): Distance of step in meters
- `until_lap_press` (Optional[bool]): Wait for lap button press

### Repeat/Interval Properties

- `reps` (Optional[int]): Number of repetitions (requires nested `steps`)
- `steps` (Optional[List[Step]]): Nested steps for repeats

### Intensity Modifiers

- `warmup` (Optional[bool]): Mark as warmup
- `cooldown` (Optional[bool]): Mark as cooldown
- `intensity` (Optional[Intensity]): Intensity level (active, rest, warmup, cooldown, recovery, interval, other)

### Target Values

- `power` (Optional[Value]): Power target
- `hr` (Optional[Value]): Heart rate target
- `pace` (Optional[Value]): Pace target
- `cadence` (Optional[Value]): Cadence target

### Special Modifiers

- `ramp` (Optional[bool]): Gradual change in intensity (useful for ERG workouts)
- `freeride` (Optional[bool]): Segment without ERG control
- `maxeffort` (Optional[bool]): Maximum effort segment
- `hidepower` (Optional[bool]): Hide power display

## Value Structure

The Value object represents target values with optional ranges or ramps:

- `value` (Optional[float]): Single target value
- `start` (Optional[float]): Start value for ranges/ramps
- `end` (Optional[float]): End value for ranges/ramps
- `units` (Optional[ValueUnits]): Unit type
- `target` (Optional[HrTarget]): Heart rate averaging (lap, 1s, 3s, 10s, 30s)

### Available Units

- **Percentage-based:**
  - `%ftp`: Percentage of Functional Threshold Power
  - `%hr`: Percentage of max heart rate
  - `%lthr`: Percentage of Lactate Threshold Heart Rate
  - `%pace`: Percentage of threshold pace
  - `%mmp`: Percentage of Mean Maximal Power

- **Zone-based:**
  - `power_zone`: Power zone (Z1, Z2, Z3, etc.)
  - `hr_zone`: Heart rate zone
  - `pace_zone`: Pace zone

- **Absolute values:**
  - `w`: Watts
  - `cadence`: RPM

## Step Examples

### Distance or Duration

Set distance or duration as appropriate for step:

```json
{"distance": "5000"}
{"duration": "1800"}
```

### Power/HR/Pace Intensity

Define step intensity using one of these:

```json
// Percentage of FTP
{"power": {"value": "80", "units": "%ftp"}}

// Absolute power
{"power": {"value": "200", "units": "w"}}

// Heart rate
{"hr": {"value": "75", "units": "%hr"}}

// Heart rate (LTHR)
{"hr": {"value": "85", "units": "%lthr"}}

// Cadence
{"cadence": {"value": "90", "units": "rpm"}}

// Pace by FTP
{"pace": {"value": "80", "units": "%pace"}}

// Pace by zone
{"pace": {"value": "Z2", "units": "pace_zone"}}

// Zone by power
{"power": {"value": "Z2", "units": "power_zone"}}

// Zone by heart rate
{"hr": {"value": "Z2", "units": "hr_zone"}}
```

### Ranges

Specify ranges for power, heart rate, or cadence:

```json
{"power": {"start": "80", "end": "90", "units": "%ftp"}}
```

### Ramps

Instead of a range, indicate a gradual change in intensity (useful for ERG workouts):

```json
{"ramp": true, "power": {"start": "80", "end": "90", "units": "%ftp"}}
```

### Repeats

Include the `reps` property and add nested steps:

```json
{
  "reps": 3,
  "steps": [
    {"power": {"value": "110", "units": "%ftp"}, "distance": "500", "text": "High-intensity"},
    {"power": {"value": "80", "units": "%ftp"}, "duration": "90", "text": "Recovery"}
  ]
}
```

### Free Ride

Include `free` to indicate a segment without ERG control, optionally with a suggested power range:

```json
{"free": true, "power": {"value": "80", "units": "%ftp"}}
```

### Comments and Labels

Add descriptive text to label steps:

```json
{"text": "Warmup"}
{"text": ""}  // Add blank lines for readability
```

## Complete Workout Example

```json
{
  "workout_doc": {
    "description": "High-intensity workout for increasing VO2 max",
    "steps": [
      {
        "power": {"value": "80", "units": "%ftp"},
        "duration": "900",
        "warmup": true
      },
      {
        "reps": 2,
        "text": "High-intensity intervals",
        "steps": [
          {
            "power": {"value": "110", "units": "%ftp"},
            "distance": "500",
            "text": "High-intensity"
          },
          {
            "power": {"value": "80", "units": "%ftp"},
            "duration": "90",
            "text": "Recovery"
          }
        ]
      },
      {
        "power": {"value": "80", "units": "%ftp"},
        "duration": "600",
        "cooldown": true
      },
      {
        "text": ""
      }
    ]
  }
}
```

## Event API Integration

To create a workout event using the intervals.icu API:

### Endpoint
```
POST /athlete/{athlete_id}/events
PUT /athlete/{athlete_id}/events/{event_id}  (for updates)
```

### Required Parameters

- `start_date_local`: Start date in format "YYYY-MM-DDTHH:MM:SS"
- `category`: "WORKOUT"
- `name`: Name of the workout
- `type`: Workout type (Ride, Run, Swim, Walk, Row)

### Optional Parameters

- `description`: String representation of WorkoutDoc
- `moving_time`: Total expected moving time in seconds
- `distance`: Total expected distance in meters

### Example API Call Data

```python
data = {
    "start_date_local": "2025-01-15T00:00:00",
    "category": "WORKOUT",
    "name": "VO2 Max Intervals",
    "description": str(workout_doc),  # WorkoutDoc as JSON string
    "type": "Ride",
    "moving_time": 3600,
    "distance": 30000
}
```

## Intensity Levels

Available intensity levels for steps:
- `active`: Active effort
- `rest`: Rest period
- `warmup`: Warmup phase
- `cooldown`: Cooldown phase
- `recovery`: Recovery period
- `interval`: Interval effort
- `other`: Other intensity

## Heart Rate Target Types

Available heart rate averaging modes:
- `lap`: Lap average
- `1s`: 1-second average
- `3s`: 3-second average
- `10s`: 10-second average
- `30s`: 30-second average

## Best Practices

1. **Set duration OR distance** - Each step should have either a duration (in seconds) or distance (in meters), not both
2. **Use reps with nested steps** - When defining repeat intervals, always include nested steps
3. **Define one intensity target** - Use one of power, hr, or pace per step
4. **Use appropriate units** - Match units to the workout type (power for cycling, pace for running)
5. **Include warmup/cooldown** - Mark warmup and cooldown steps appropriately
6. **Add descriptive text** - Use text labels to make workouts easier to follow
7. **Use ramps for ERG** - Ramps provide smoother transitions for ERG-controlled trainers

## Source Code Location

This format documentation is extracted from:
- `/home/user/intervals-mcp-server/src/intervals_mcp_server/server.py` (lines 698-760)
- `/home/user/intervals-mcp-server/src/intervals_mcp_server/utils/types.py` (complete type definitions)
