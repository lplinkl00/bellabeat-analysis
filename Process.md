# Process

Date: June 5, 2024 → June 6, 2024
Status: Done

Guiding questions
● What tools are you choosing and why?
● Have you ensured your data’s integrity?
● What steps have you taken to ensure that your data is clean?
● How can you verify that your data is clean and ready to analyze?
● Have you documented your cleaning process so you can review and share those results?

Key tasks

1. Check the data for errors.
2. Choose your tools.
3. Transform the data so you can work with it effectively.
4. Document the cleaning process.

Deliverable

Documentation of any cleaning or manipulation of data

## Steps taken

### Check for null values

1. Created queries to check for NULL values in each table
    1. Queries are created with the assistance of GPT-4o
        1. Initial queries are written but then improved with GPT

```sql
SELECT 
    (SELECT COUNT(*) FROM fitbit_data.hourlyIntensities WHERE Id IS NULL) AS Null_Id,
    (SELECT COUNT(*) FROM fitbit_data.hourlyIntensities WHERE ActivityHour IS NULL) AS Null_ActivityHour,
    (SELECT COUNT(*) FROM fitbit_data.hourlyIntensities WHERE TotalIntensity IS NULL) AS Null_TotalIntensity,
    (SELECT COUNT(*) FROM fitbit_data.hourlyIntensities WHERE AverageIntensity IS NULL) AS Null_AverageIntensity;

```

No null values found in the provided dataset. The ecommerce dataset has null values which can be easily handled as 0 or blanks 

### Handling null values

The two columns with null values are Rating and Review.

```sql
UPDATE fitbit_data.fitness_tracker_ecommerce
SET Rating = 0.0
WHERE Rating IS NULL;

```

```sql
UPDATE fitbit_data.fitness_tracker_ecommerce
SET Review = 0
WHERE Review IS NULL;

```

### Convert dates that are in string to date format

The following steps were taken to convert dates in string type to timestamp type

```sql
ALTER TABLE fitbit_data.fitness_tracker_ecommerce
ADD COLUMN new_date_time_column TIMESTAMP;
```

```sql
UPDATE fitbit_data.fitness_tracker_ecommerce
SET new_date_time_column = PARSE_TIMESTAMP('%m/%d/%Y %I:%M:%S %p', DateTimeColumn)
WHERE DateTimeColumn IS NOT NULL;
```

```sql
ALTER TABLE fitbit_data.fitness_tracker_ecommerce
DROP COLUMN DateTimeColumn;
```

```sql
ALTER TABLE fitbit_data.fitness_tracker_ecommerce
RENAME COLUMN new_date_time_column TO DateTimeColumn;
```
