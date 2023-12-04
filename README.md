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
![image](https://github.com/Gihan-Gamaarachchi/UK-Accident-Data/assets/137648970/2c0baad2-c722-401c-9f56-a821bc38bb64)


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
