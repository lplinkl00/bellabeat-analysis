# Analysis

Date: June 6, 2024 → June 8, 2024
Status: Done

## Insights from each table

### dailyActivity

From averages:

- In general very active people that are less sedentary have higher calories used

Has calorie and general activity data, need to look at other tables for the impact of the calorie data on people

### dailyCalories

Same information found in the dailyActivity table

### dailyIntensities

Same information found in the dailyActivity table

### dailySteps

Same information found in the dailyActivity table

### heartrate_seconds

- only 14 IDs are captured in this data, meaning that half the sample size did not submit heartrate data.
- In general each person had moments of intense exercise and rest, as indicated by the minimum and maximum heart rate

Might have to do with the capabilities of the devices

## Insights from combining tables

Ideas when returning:

1. Check how users have used a device through out the day, is there a spike in usage at certain times for each feature?
    1. Are they moving more at a certain time?
    2. What are the sleep patterns?
    3. Does it correlate with Energy usage?
2. Are there models that do not have the ability to track heart rates?
    1. Is this reflected in user reviews?
    

```sql
SELECT
  heartrate.Id,
  EXTRACT(HOUR FROM TIMESTAMP_TRUNC(heartrate.hrdate, HOUR)) AS hour_of_day,
  AVG(heartrate.Value) AS avg_heartrate,
  SUM(sleep.value) AS avg_sleep,
  AVG(steps.Steps) AS avg_steps
FROM
  fitbit_data.heartrate_seconds AS heartrate
  JOIN fitbit_data.minuteSleep AS sleep ON heartrate.Id = sleep.Id
  JOIN fitbit_data.minuteStepsNarrow AS steps ON heartrate.Id = steps.Id
  JOIN fitbit_data.minuteCaloriesNarrow AS calories ON heartrate.Id = calories.Id
GROUP BY heartrate.Id, hour_of_day
```

The above SQL aims to retrieve a common usage among users.

The heartrate table is chosen due to it having the most detailed data. If other data is joined to this table it is likely to find matching values.

Updated SQL Query to perform what the earlier query was trying to do.

```sql
WITH daily_heartrate AS (
    SELECT 
        Id,
        TIMESTAMP_TRUNC(hrdate, HOUR) AS hour,
        AVG(Value) AS avg_heartrate
    FROM 
        fitbit_data.heartrate_seconds
    GROUP BY 
        Id, hour
),
daily_sleep AS (
    SELECT 
        Id,
        TIMESTAMP_TRUNC(sleepdate, HOUR) AS hour,
        IFNULL(SUM(value), 0) AS total_sleep_minutes
    FROM 
        fitbit_data.minuteSleep
    GROUP BY 
        Id, hour
),
daily_steps AS (
    SELECT 
        Id,
        TIMESTAMP_TRUNC(ActivityMinute, HOUR) AS hour,
        SUM(Steps) AS total_steps
    FROM 
        fitbit_data.minuteStepsNarrow
    GROUP BY 
        Id, hour
),
daily_calories AS (
    SELECT 
        Id,
        TIMESTAMP_TRUNC(ActivityMinute, HOUR) AS hour,
        SUM(Calories) AS total_calories
    FROM 
        fitbit_data.minuteCaloriesNarrow
    GROUP BY 
        Id, hour
),

hourly_aggregated AS (
SELECT 
    hr.Id,
    hr.hour,
    hr.avg_heartrate,
    sl.total_sleep_minutes,
    st.total_steps,
    cl.total_calories
FROM 
    daily_heartrate hr
LEFT JOIN 
    daily_sleep sl ON hr.Id = sl.Id AND hr.hour = sl.hour
LEFT JOIN 
    daily_steps st ON hr.Id = st.Id AND hr.hour = st.hour
LEFT JOIN 
    daily_calories cl ON hr.Id = cl.Id AND hr.hour = cl.hour
)

SELECT 
    Id,
    EXTRACT(HOUR FROM hour) AS hour_of_day,
    AVG(avg_heartrate) AS avg_heartrate,
    AVG(total_sleep_minutes) AS avg_sleep_minutes,
    AVG(total_steps) AS avg_steps,
    AVG(total_calories) AS avg_calories
FROM 
    hourly_aggregated
GROUP BY 
    Id, hour_of_day
ORDER BY 
    Id, hour_of_day;
```

The result of the query is saved in google sheets as there is a manageable number of rows created. 

### Insights found

After looking into the data and seeing that most of the data available related to day to day usage, be it in hours or minutes. I decided to investigate if there was any difference in activity throughout the day on average. 

After joining the heartrate, sleep, steps and calorie tables together, then querying the data to extract the average of each of the measurements by hour of the day (0-23). I am able to produce the graph above.

As seen in Fig 1.0 the clear trend is that on average the devices are used throughout the day to measure steps, sleep and calories. What is notable is the steady sleep tracked throughout the day. What this suggests is that there are users that are sleeping at various times throughout the day. The reasons are unclear from this data, however it suggests that Bellabeat could position its products as versatile. Compared to some competitors that may excel in specific activities and be frustrating in others. 

After all Bellabeat already has devices that are designed to be worn all the time.

![Fig 1.0](Images/Average_activities_through_the_day.png)

Fig 1.0

### Supplementary Data

To complement the findings we should discover how much of the market has the technology to be versatile. 

With less than 500 rows this data is analyzed in a Google Sheet

The key technologies to look for are PPG, accelerometers and GPS. These three technologies track heartrate, sleep, and activity (steps and calories) respectively.

  Sheets formula used to calculate number of devices competing with Leaf

```
=COUNTIFS(other_models!Accelerometer, TRUE, other_models!PPG, TRUE, other_models!GPS, TRUE)
```

Sheets formula used to calculate total number of devices

```
=COUNT(other_models!'Device name')
```

Sheets formula used to calculate the percentage of competing devices 

```
=(C2/B2)

followed by formatting the cell as a percentage
```

![Fig 1.1](Images/Comparing_number_of_devices_that_compete_with_Leaf.png)

Fig 1.1

## Story told from the data

The data tells us one main message: users use their wellness trackers through all hours of the day, as intended. However, most devices are not equipped to record this data. This gives Bellabeat the opportunity to market Leaf, their wearable device, as a very versatile product. Highlighting how it can track heartrate and distance over more than 80% of devices on the market. Combined with the existing services in the app intending to get potential customers to feel like they gaining control over their lives with Bellabeat products. 

### Assumptions and disclaimers

That all respondents in the dataset are from a similar time zone. If the data is collected from international participants it would invalidate the findings that the device is used throughout the day.
