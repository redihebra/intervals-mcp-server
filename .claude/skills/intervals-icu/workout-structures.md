# Intervals.icu Workout Structures Reference

This document provides detailed reference for workout data structures used in the Intervals.icu API.

## Workout Target Types

```
AUTO   - Automatically determine target type
POWER  - Target by power (watts or %FTP)
HR     - Target by heart rate
PACE   - Target by pace
```

## Heart Rate Target Averaging

When specifying HR targets, you can define the averaging window:

```
lap    - Average over the lap
1s     - Instant/1-second average
3s     - 3-second rolling average
10s    - 10-second rolling average
30s    - 30-second rolling average
```

## Intensity Levels

```
active    - Active/working intensity
rest      - Rest interval
warmup    - Warmup phase
cooldown  - Cooldown phase
recovery  - Recovery interval
interval  - Main interval effort
other     - Other/unspecified
```

## Pace Units

```
SECS_100M  - Seconds per 100 meters (swimming)
SECS_100Y  - Seconds per 100 yards (swimming)
MINS_KM    - Minutes per kilometer (running)
MINS_MILE  - Minutes per mile (running)
SECS_500M  - Seconds per 500 meters (rowing)
```

## Value Units

| Unit | Description | Use Case |
|------|-------------|----------|
| `%ftp` | Percentage of FTP | Power targets for cycling |
| `%mmp` | Percentage of MMP | Power targets |
| `w` | Absolute watts | Fixed power targets |
| `power_zone` | Power zone number | Zone-based power |
| `%hr` | Percentage of max HR | Heart rate targets |
| `%lthr` | Percentage of LTHR | Lactate threshold HR |
| `hr_zone` | HR zone number | Zone-based HR |
| `%pace` | Percentage of threshold pace | Pace targets |
| `pace_zone` | Pace zone number | Zone-based pace |
| `cadence` | RPM | Cadence targets |

## Value Object Structure

A value object can represent a single value or a range:

### Single Value
```json
{
  "value": 85,
  "units": "%ftp"
}
```

### Range
```json
{
  "start": 80,
  "end": 90,
  "units": "%ftp"
}
```

### With HR Averaging
```json
{
  "value": 160,
  "units": "%lthr",
  "target": "10s"
}
```

## Step Object Structure

A workout step defines a single segment of the workout:

```json
{
  "text": "Main Interval",
  "duration": 300,
  "distance": null,
  "reps": null,
  "warmup": false,
  "cooldown": false,
  "intensity": "interval",
  "ramp": false,
  "freeride": false,
  "maxeffort": false,
  "power": {"value": 100, "units": "%ftp"},
  "hr": null,
  "pace": null,
  "cadence": {"value": 90, "units": "rpm"},
  "steps": null
}
```

### Step Fields

| Field | Type | Description |
|-------|------|-------------|
| `text` | string | Label or description for the step |
| `duration` | int | Duration in seconds |
| `distance` | float | Distance in meters |
| `reps` | int | Number of repetitions (for nested steps) |
| `warmup` | bool | Mark as warmup segment |
| `cooldown` | bool | Mark as cooldown segment |
| `intensity` | string | Intensity level (active, rest, etc.) |
| `ramp` | bool | Gradual intensity change |
| `freeride` | bool | Uncontrolled/free ride segment |
| `maxeffort` | bool | Maximum effort segment |
| `power` | Value | Power target |
| `hr` | Value | Heart rate target |
| `pace` | Value | Pace target |
| `cadence` | Value | Cadence target |
| `steps` | Step[] | Nested steps for repeats |
| `hidepower` | bool | Hide power display |
| `until_lap_press` | bool | Continue until lap button pressed |

## Workout Document Structure

A complete workout document:

```json
{
  "description": "High-intensity threshold workout",
  "duration": 3600,
  "distance": 40000,
  "ftp": 250,
  "lthr": 165,
  "threshold_pace": 4.17,
  "pace_units": "MINS_KM",
  "category": "WORKOUT",
  "target": "POWER",
  "steps": [
    // Array of Step objects
  ],
  "zone_times": [0, 0, 600, 1800, 600, 0, 0]
}
```

### Workout Document Fields

| Field | Type | Description |
|-------|------|-------------|
| `description` | string | Workout description |
| `duration` | int | Total duration in seconds |
| `distance` | float | Total distance in meters |
| `ftp` | int | FTP used for calculations |
| `lthr` | int | LTHR used for calculations |
| `threshold_pace` | float | Threshold pace in m/s |
| `pace_units` | string | Pace display units |
| `category` | string | Workout category |
| `target` | string | Primary target type |
| `steps` | Step[] | Array of workout steps |
| `zone_times` | int[] | Time in each zone (seconds) |

## Example Workouts

### Sweet Spot Intervals

```json
{
  "description": "Sweet spot intervals to build FTP",
  "steps": [
    {
      "duration": 600,
      "power": {"value": 65, "units": "%ftp"},
      "warmup": true,
      "text": "Warmup - Easy spinning"
    },
    {
      "reps": 3,
      "text": "Sweet Spot Blocks",
      "steps": [
        {
          "duration": 900,
          "power": {"start": 88, "end": 93, "units": "%ftp"},
          "text": "Sweet Spot"
        },
        {
          "duration": 300,
          "power": {"value": 55, "units": "%ftp"},
          "text": "Recovery"
        }
      ]
    },
    {
      "duration": 600,
      "power": {"value": 55, "units": "%ftp"},
      "cooldown": true,
      "text": "Cooldown"
    }
  ]
}
```

### VO2 Max Intervals

```json
{
  "description": "VO2 Max intervals for aerobic capacity",
  "steps": [
    {
      "duration": 900,
      "power": {"value": 70, "units": "%ftp"},
      "warmup": true
    },
    {
      "duration": 180,
      "ramp": true,
      "power": {"start": 70, "end": 110, "units": "%ftp"},
      "text": "Build"
    },
    {
      "reps": 5,
      "steps": [
        {
          "duration": 180,
          "power": {"value": 120, "units": "%ftp"},
          "text": "VO2 Max"
        },
        {
          "duration": 180,
          "power": {"value": 50, "units": "%ftp"},
          "text": "Recovery"
        }
      ]
    },
    {
      "duration": 600,
      "power": {"value": 55, "units": "%ftp"},
      "cooldown": true
    }
  ]
}
```

### Running Tempo Workout

```json
{
  "description": "Tempo run with progressive intervals",
  "steps": [
    {
      "duration": 600,
      "pace": {"value": 75, "units": "%pace"},
      "warmup": true
    },
    {
      "reps": 4,
      "steps": [
        {
          "duration": 480,
          "pace": {"value": 95, "units": "%pace"},
          "text": "Tempo"
        },
        {
          "duration": 120,
          "pace": {"value": 70, "units": "%pace"},
          "text": "Recovery"
        }
      ]
    },
    {
      "duration": 600,
      "pace": {"value": 65, "units": "%pace"},
      "cooldown": true
    }
  ]
}
```

### Swimming Workout

```json
{
  "description": "Threshold swim set",
  "steps": [
    {
      "distance": 400,
      "pace": {"value": 70, "units": "%pace"},
      "warmup": true
    },
    {
      "reps": 8,
      "steps": [
        {
          "distance": 100,
          "pace": {"value": 95, "units": "%pace"},
          "text": "Fast"
        },
        {
          "duration": 20,
          "text": "Rest"
        }
      ]
    },
    {
      "distance": 200,
      "pace": {"value": 60, "units": "%pace"},
      "cooldown": true
    }
  ]
}
```

### Free Ride with Power Suggestions

```json
{
  "description": "Group ride simulation",
  "steps": [
    {
      "duration": 600,
      "power": {"value": 65, "units": "%ftp"},
      "warmup": true
    },
    {
      "duration": 2400,
      "freeride": true,
      "power": {"start": 70, "end": 90, "units": "%ftp"},
      "text": "Ride at comfortable effort"
    },
    {
      "duration": 600,
      "power": {"value": 55, "units": "%ftp"},
      "cooldown": true
    }
  ]
}
```

## Text-Based Workout Description

For simpler workouts, you can use text descriptions that Intervals.icu parses:

```
4x4 VO2 Max Intervals

Warmup
- 15m 65% ftp Easy spinning

4x
- 4m 120% ftp VO2 Max effort
- 4m 50% ftp Easy recovery

Cooldown
- 10m 55% ftp Easy cooldown
```

### Text Format Rules

1. First line is the workout title
2. Use `Warmup` and `Cooldown` headers
3. Duration: `15m` (minutes), `300s` (seconds)
4. Distance: `5km`, `400m`
5. Power: `100% ftp`, `250w`, `Z4`
6. HR: `85% lthr`, `160bpm`, `Z3`
7. Repeats: `4x` followed by indented steps
8. Comments: Add text after the target

## Sport Settings

When working with athlete data, sport settings contain training zones:

```json
{
  "types": ["Ride", "VirtualRide"],
  "ftp": 280,
  "lthr": 168,
  "max_hr": 185,
  "w_prime": 22000,
  "sweet_spot_min": 88,
  "sweet_spot_max": 94,
  "power_zones": [55, 75, 90, 105, 120, 150, 999],
  "power_zone_names": ["Z1 Recovery", "Z2 Endurance", "Z3 Tempo", "Z4 Threshold", "Z5 VO2max", "Z6 Anaerobic", "Z7 Neuromuscular"],
  "hr_zones": [106, 127, 143, 160, 168, 999],
  "hr_zone_names": ["Z1", "Z2", "Z3", "Z4", "Z5a", "Z5b"],
  "warmup_time": 900,
  "cooldown_time": 600
}
```

Use these values to convert zone references to actual watts/bpm values in your workout analysis.
