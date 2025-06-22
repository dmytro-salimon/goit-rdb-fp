# Final project

## Task 1.

### 1.1 Create & use schema 
```
CREATE SCHEMA pandemic;
USE pandemic; 
```

### 1.2 Import data


## Task 2. Normalize

### 2.1 Count rows from infectious_cases
```
SELECT COUNT(*) FROM infectious_cases;
```

### 2.2 Create new tables

Creating entities table/
```
CREATE TABLE entities (
	entity_id INT AUTO_INCREMENT PRIMARY KEY,
	entity_name VARCHAR(255),
	code VARCHAR(50)
);
```

Creating infectious_normalized table/
```
CREATE TABLE infectious_normalized (
	id INT AUTO_INCREMENT PRIMARY KEY,
	entity_id INT,
	year INT,
	number_yaws TEXT,
	polio_cases INT,
	cases_guinea_worm INT,
	number_rabies TEXT,
	number_malaria TEXT,
	number_hiv TEXT,
	number_tuberculosis TEXT,
	number_smallpox TEXT,
	number_cholera_cases TEXT,
	FOREIGN KEY (entity_id) REFERENCES entities(entity_id)
);
```

### 2.3 Fill tables with data

Paste unique Entity + Code/
```
INSERT INTO entities (entity_name, code)
SELECT DISTINCT Entity, Code FROM infectious_cases;
```

Paste normalized data/
```
INSERT INTO infectious_normalized (
	entity_id,
	year,
	number_yaws,
	polio_cases,
	guinea_worm_cases,
	number_rabies,
	number_malaria,
	number_hiv,
	number_tuberculosis,
	number_smallpox,
	number_cholera_cases
)
SELECT 
	e.entity_id,
	ic.Year,
	NULLIF(ic.Number_yaws, ''),
    NULLIF(ic.polio_cases, ''),
    NULLIF(ic.cases_guinea_worm, ''),
    NULLIF(ic.Number_rabies, ''),
    NULLIF(ic.Number_malaria, ''),
    NULLIF(ic.Number_hiv, ''),
    NULLIF(ic.Number_tuberculosis, ''),
    NULLIF(ic.Number_smallpox, ''),
    NULLIF(ic.Number_cholera_cases, '')
FROM infectious_cases ic
JOIN entities e ON ic.entity = e.entity_name and ic.code = e.code
```

## Task 3. Data analysis

```
SELECT 
	e.entity_name,
	e.code,
	AVG(inor.number_rabies) AS avg_rabies,
	MIN(inor.number_rabies) AS min_rabies,
	MAX(inor.number_rabies) AS max_rabies,
	SUM(inor.number_rabies) AS total_rabies
FROM infectious_normalized inor
JOIN entities e ON inor.entity_id = e.entity_id
WHERE inor.number_rabies IS NOT NULL
GROUP BY e.entity_name, e.code
ORDER BY avg_rabies DESC
LIMIT 10;
```

## Task 4. Difference in years
```
SELECT 
	id,
	year,
	DATE(CONCAT(year, '-01-01')) AS year_date,
	CURRENT_DATE() AS c_date,
	TIMESTAMPDIFF(YEAR, DATE(CONCAT(year, '-01-01')), CURRENT_DATE()) AS year_diff
FROM infectious_normalized;
```

## Task 5. Create function

### 5.1 Creating function
```
DROP FUNCTION IF EXISTS year_diff;

DELIMITER //

CREATE FUNCTION year_diff(input_year INT)
RETURNS INT
DETERMINISTIC
BEGIN
	RETURN TIMESTAMPDIFF(YEAR, DATE(CONCAT(input_year, '-01-01')), CURRENT_DATE());
END //

DELIMITER ;
```

### 5.2 Testing function
```
SELECT 
	id, 
	year, 
	year_diff(year) AS year_difference
FROM infectious_normalized;
```
