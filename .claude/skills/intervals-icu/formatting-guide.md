# Intervals.icu Data Formatting Guide

This guide provides templates for formatting Intervals.icu API data for human-readable output.

## Speed and Pace Formatting

### By Activity Type

**Cycling** - Convert m/s to km/h:
```
speed_kmh = speed_ms * 3.6
Format: "32.5 km/h"
```

**Running** - Convert m/s to min/km:
```
if speed_ms > 0:
    pace_min_km = 1000 / (speed_ms * 60)
    minutes = floor(pace_min_km)
    seconds = (pace_min_km - minutes) * 60
Format: "4:30 min/km"
```

**Swimming** - Convert m/s to min/100m:
```
if speed_ms > 0:
    pace_min_100m = 100 / (speed_ms * 60)
    minutes = floor(pace_min_100m)
    seconds = (pace_min_100m - minutes) * 60
Format: "1:45 min/100m"
```

**Generic** - Show as m/s:
```
Format: "4.17 m/s"
```

## Duration Formatting

Convert seconds to human-readable format:

```
hours = duration // 3600
remaining = duration % 3600
minutes = remaining // 60
seconds = remaining % 60

Examples:
- 3665 seconds → "1h 1m 5s"
- 180 seconds → "3m"
- 45 seconds → "45s"
- 7200 seconds → "2h"
```

## Distance Formatting

```
if distance < 1000:
    Format: "{distance}m" (e.g., "500m")
else:
    Format: "{distance/1000}km" (e.g., "42.2km")
```

## Activity Summary Template

```
Activity: {name}
ID: {id}
Type: {type}
Date: {startTime formatted as YYYY-MM-DD HH:MM:SS}
Description: {description}
Distance: {distance} meters
Duration: {duration} seconds
Moving Time: {moving_time} seconds
Elevation Gain: {elevation_gain} meters

Power Data:
- Average Power: {avg_power} W
- Weighted Avg Power: {weighted_avg_power} W
- Training Load: {training_load}
- FTP: {ftp} W
- Kilojoules: {kilojoules}
- Intensity: {intensity}
- Power:HR Ratio: {power_hr_ratio}

Heart Rate Data:
- Average Heart Rate: {avg_hr} bpm
- Max Heart Rate: {max_hr} bpm
- LTHR: {lthr} bpm
- Decoupling: {decoupling}

Other Metrics:
- Cadence: {cadence} rpm
- Calories: {calories}
- Average Speed: {formatted_speed}
- Max Speed: {formatted_max_speed}
- RPE: {perceived_exertion}/10
- Feel: {feel}/5 (lower is better)

Environment:
- Trainer: {trainer}
- Average Temp: {average_temp}°C

Training Metrics:
- Fitness (CTL): {ctl}
- Fatigue (ATL): {atl}
- TRIMP: {trimp}
```

## Event Summary Template

```
Date: {start_date_local}
ID: {id}
Type: {type}
Name: {name}
Description:
{description}
```

## Event Details Template

```
Event Details:

ID: {id}
Date: {date}
Name: {name}
Description:
{description}

Workout Information:
Workout ID: {workout.id}
Sport: {workout.sport}
Duration: {workout.duration} seconds
TSS: {workout.tss}
Intervals: {len(workout.intervals)}

Race Information: (if applicable)
Priority: {priority}
Result: {result}
```

## Wellness Entry Template

```
Wellness Data:
Date: {id}

Training Metrics:
- Fitness (CTL): {ctl}
- Fatigue (ATL): {atl}
- Ramp Rate: {rampRate}

Sport-Specific Info:
{for each sport in sportInfo}
- {type}: eFTP = {eftp} W
{end for}

Vital Signs:
- Weight: {weight} kg
- Resting HR: {restingHR} bpm
- HRV: {hrv}
- HRV SDNN: {hrvSDNN}
- SpO2: {spO2}%
- Blood Pressure: {systolic}/{diastolic} mmHg

Sleep & Recovery:
- Sleep: {sleepSecs/3600} hours
- Sleep Score: {sleepScore}
- Sleep Quality: {sleepQuality}/4
- Readiness: {readiness}

Subjective Feelings:
- Soreness: {soreness}/14
- Fatigue: {fatigue}/4
- Stress: {stress}/4
- Mood: {mood}/4
- Motivation: {motivation}/4
- Injury: {injury}/4

Nutrition & Hydration:
- Calories Consumed: {kcalConsumed} kcal
- Hydration Volume: {hydrationVolume} ml

Activity:
- Steps: {steps}
```

## Intervals Analysis Template

```
Intervals Analysis:
ID: {id}
Analyzed: {analyzed}

Individual Intervals:
{for each interval in icu_intervals}
[{index}] {label} ({type})
Duration: {elapsed_time} seconds (moving: {moving_time} seconds)
Distance: {distance} meters

Power Metrics:
- Average Power: {average_watts} watts ({average_watts_kg} W/kg)
- Max Power: {max_watts} watts
- Weighted Avg Power: {weighted_average_watts} watts
- Training Load: {training_load}
- Power Zone: {zone}

Heart Rate:
- Heart Rate: Avg {average_heartrate}, Max {max_heartrate} bpm

Speed & Cadence:
- Speed: Avg {formatted_average_speed}
- Cadence: Avg {average_cadence} rpm

{end for}

Interval Groups:
{for each group in icu_groups}
[{index}] {label} ({type})
Duration: {elapsed_time} seconds
Power: Avg {average_watts} W, Max {max_watts} W
HR: Avg {average_heartrate} bpm
{end for}
```

## Athlete Profile Template (Markdown)

```markdown
# Athlete Profile: {name}

## Basic Information
- **ID**: {id}
- **Gender**: {sex}
- **Location**: {city}, {country}
- **Height**: {height} m
- **Weight**: {icu_weight} kg
- **Resting HR**: {icu_resting_hr} bpm
- **Date of Birth**: {icu_date_of_birth}
- **Timezone**: {timezone}

## Sport-Specific Training Zones

### Cycling
**Activity Types**: Ride, VirtualRide

**Heart Rate**:
- LTHR: {lthr} bpm
- Max HR: {max_hr} bpm
- HR Zones:
  - **Z1**: {hr_zones[0]+1}-{hr_zones[1]} bpm
  - **Z2**: {hr_zones[1]+1}-{hr_zones[2]} bpm
  ...

**Power**:
- FTP: {ftp} watts
- W': {w_prime} joules
- Sweet Spot: {sweet_spot_min}-{sweet_spot_max}% FTP
- Power Zones:
  - **Z1 Recovery**: 0-{power_zones[0]}% FTP (0-{ftp*power_zones[0]/100} watts)
  - **Z2 Endurance**: {power_zones[0]+1}-{power_zones[1]}% FTP
  ...

### Running
**Activity Types**: Run, VirtualRun

**Pace**:
- Threshold Pace: {formatted_threshold_pace}
- Pace Zones:
  - **Z1**: {pace_zones[0]+1}%+ threshold
  ...

## Bio
{bio}
```

## Workout Step Formatting

### Text Representation

```
{description}

{if warmup}Warmup
{end if}
{if cooldown}Cooldown
{end if}

{if reps}
{reps}x
  {for each step in nested steps}
  - {formatted_step}
  {end for}
{else}
- {duration_formatted or distance_formatted} {power_formatted} {hr_formatted} {text}
{end if}
```

### Example Formatted Workout

```
High-intensity VO2 Max intervals

Warmup
- 15m 70% ftp Easy spinning

5x
- 3m 120% ftp VO2 Max effort
- 3m 50% ftp Recovery

Cooldown
- 10m 55% ftp Easy cooldown
```

## Value Formatting

### Power Values
```
%ftp: "85%"
watts: "250W"
power_zone: "Z4"
range: "85%-95%"
```

### Heart Rate Values
```
%hr: "80%"
%lthr: "95%"
hr_zone: "Z3"
bpm: "160 bpm"
```

### Pace Values
```
%pace: "100%"
pace_zone: "Z4"
actual: "4:30 min/km"
```

## Conditional Sections

Only include sections if data is present:

```python
# Pseudocode pattern
if data.get('avg_power'):
    output += f"Average Power: {data['avg_power']} W\n"

# Section pattern
if any([data.get('avg_power'), data.get('weighted_avg_power'), ...]):
    output += "\nPower Data:\n"
    if data.get('avg_power'):
        output += f"- Average Power: {data['avg_power']} W\n"
    # ... more fields
```

## Best Practices

1. **Omit null values**: Don't show fields with null/None values
2. **Use appropriate units**: km/h for cycling, min/km for running
3. **Round appropriately**: Power to whole numbers, pace to seconds
4. **Group related data**: Use sections for power, HR, environment, etc.
5. **Include context**: Show units with values (W, bpm, km/h)
6. **Format dates consistently**: YYYY-MM-DD HH:MM:SS
7. **Use indentation**: For nested data and sections
