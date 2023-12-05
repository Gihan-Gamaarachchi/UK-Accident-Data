# UK-Accident-Data
Demonstrating analysis and cleaning of data using SQL

### 1) What are the earliest and latest data entries in this dataset?

```
SELECT 
	min([Accident Date]) AS earliest_date,
	max([Accident Date]) AS latest_date
FROM accident_data..data;
```
**Answer:**

The data consists of entries from 01/01/2019 to 31/12/2019.

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/fc82d012-5b09-4499-abd3-c0b1c819cfe0)



### 2) Which distrcits are used in this dataset?

```
SELECT
  DISTINCT([District Area])
FROM accident_data..data;
```
**Answer:**

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/c5aea3a4-ed31-4537-b363-33d96b29304b)


### 3) What were the top 5 disctricts with the most accidents?

```
SELECT
  TOP 5 ([District Area]),
  COUNT(*) AS AccidentCount
FROM accident_data..data
GROUP BY [District Area]
ORDER BY AccidentCount DESC;
```
**Answer:**

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/69e621e8-002f-4a27-9049-6c2a18dcd196)


### 4) What were the number of accidents recorded for each weather condition?

```
SELECT
  [Weather_Conditions],
  COUNT(*) AS Number_of_accidents
FROM accident_data..data
WHERE [Weather_Conditions] IS NOT NULL
GROUP BY [Weather_Conditions]
ORDER BY Number_of_accidents DESC;
```
**Answer:**

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/889c68b9-9dc1-44ea-b67d-fed4a1a37b27)


### 5) What vehicle type was recorded in the most accidents?

```
SELECT
  [Vehicle_Type],
  COUNT(*) AS Number_of_accidents
FROM accident_data..data 
GROUP BY [Vehicle_Type]
ORDER BY Number_of_accidents DESC;
```
**Answer:**

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/e5766a48-40ea-48ac-b3db-55ec0b9ef64d)


### 6) Does the average number of casualties change depending on the vehicle type?

```
SELECT
  [Vehicle_Type],
  COUNT(*) AS Number_of_accidents
FROM accident_data..data 
GROUP BY [Vehicle_Type]
ORDER BY Number_of_accidents DESC;
```
**Answer:**  

No, the number of casualties per accident averaged 1 for each vehicle type.

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/3acad5b2-df56-4dbf-8695-191469fc829e)


## Creating a new table for grouping specific vehicle types and storing accident averages and totals.

```
USE SQL_Portfolio
CREATE TABLE VehicleGroup (
Group_ID INT PRIMARY KEY IDENTITY(1,1),
Group_Name VARCHAR(50),
Group_Description VARCHAR(255),
Total_Accidents INT,
Total_Casualties INT,
Percentage_of_Total DECIMAL(5, 2));
```


## Inserting the vehicle group names and descriptions.

```
INSERT INTO SQL_Portfolio..VehicleGroup (Group_Name, Group_Description) VALUES
('Car', 'Types of car including private and taxi vehicles'),
('Motorcycle', 'types of motorcycles including different engine capacities'),
('Bus or coach', 'Buses or coaches with17 or more passenger seats'),
('Vans / Goods vehicle', 'Vans and goods vehicles of various sizes and weights'),
('Other vehicle', 'Various other vehicle types including minibuses and agricultural vehicles'),
('Non-Motorized', 'Non-motorized vehicles including pedal cycles and ridden horses'),
('Unknown', 'Vehicle types with data missing or out of range');
```


## Adding VehicleGroup_ID column to AccidentData table.

```
ALTER TABLE SQL_Portfolio..AccidentData
ADD VehicleGroup_ID INT;
```
**Answer:**  


## Defining a foreign key constraint.

```
ALTER TABLE SQL_Portfolio..AccidentData
ADD CONSTRAINT FK_VehicleGroup
FOREIGN KEY (VehicleGroup_ID) 
REFERENCES SQL_Portfolio..VehicleGroup (Group_ID);
```


## Assigning the Group ID to each vehicle type

```
UPDATE SQL_Portfolio..AccidentData
SET VehicleGroup_ID = CASE
WHEN Vehicle_Type IN ('Car') OR Vehicle_Type LIKE 'Taxi%' THEN 1
WHEN Vehicle_Type LIKE 'Motorcycle%' THEN 2
WHEN Vehicle_Type LIKE 'Bus%' THEN 3
WHEN Vehicle_Type LIKE 'Van%' OR Vehicle_Type LIKE 'Goods%' THEN 4
WHEN Vehicle_Type LIKE 'Minibus%' OR Vehicle_Type LIKE 'Agricultural%' OR Vehicle_Type LIKE 'Other vehicle%' THEN 5
WHEN Vehicle_Type LIKE 'Pedal cycle%' OR Vehicle_Type LIKE 'Ridden horse%' THEN 6
ELSE 7
END;
```


## Calculating the average accidents per vehicle group

```
SELECT VehicleGroup_ID, COUNT(*) AS Number_of_Accidents,
SUM(Number_of_Casualties) AS Total_Casualties
FROM SQL_Portfolio..AccidentData
GROUP BY VehicleGroup_ID ;
```


## Calculating total accidents and casualties per vehicle group ID

```
WITH AggregatedData AS (
	SELECT
	ad.VehicleGroup_ID,
	COUNT(*) AS Total_Accidents,
	SUM(ad.Number_of_Casualties) AS Total_Casualties
	FROM SQL_Portfolio..AccidentData ad
	GROUP BY ad.VehicleGroup_ID
	),

	-- Calculate the total accidents for all groups
	TotalAccidents AS (
	SELECT
	SUM(Total_Accidents) AS Total_All_Accidents
	FROM AggregatedData
	)

	-- Update the VehicleGroup table with calculated values
	UPDATE vg
	SET
		Total_Accidents = ad.Total_Accidents,
		Total_Casualties = ad.Total_Casualties,
		Percentage_of_Total = CAST((ad.Total_Accidents * 100.0 / ta.Total_All_Accidents) AS DECIMAL(5, 2))
	FROM SQL_Portfolio..VehicleGroup vg

	JOIN AggregatedData ad ON vg.Group_ID = ad.VehicleGroup_ID
	CROSS JOIN TotalAccidents ta;
```


## Selecting VehicleGroup Table.

```
SELECT * FROM SQL_Portfolio..VehicleGroup;
```
**Answer:**  

![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/769dbaf9-51cf-4fb5-a81e-bc1e23de7841)
