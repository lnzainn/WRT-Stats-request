[newcomers_hayden_two.csv](https://github.com/user-attachments/files/17007306/newcomers_hayden_two.csv)## Create table that contains WCA ID of those whose first competition was between Jan 1, 2023 to Feb, 2024

```SQL
CREATE TABLE newcomers_temp_wcaid AS 
(SELECT personId
FROM (SELECT xx.personId, xx.competitionId
FROM (SELECT x.personId, x.competitionId, 
	  ROW_NUMBER() OVER (PARTITION BY x.personid ORDER BY c.year, c.month, c.day) as 'rnk'
	  FROM (SELECT personId, competitionId
			FROM Results r
			GROUP BY personId, competitionId) x
            JOIN Competitions c on x.competitionId = c.id) xx
where xx.rnk = 1) xy
JOIN (SELECT id
FROM Competitions
WHERE (year = 2023) OR (year = 2024 AND endMonth IN (1, 2))) xyn ON xy.competitionId = xyn.id);
```
------
```SQL
SELECT r.personId, COUNT(DISTINCT(competitionId)) AS comps
FROM Results r
JOIN newcomers_temp_wcaid n ON r.personId = n.personId
GROUP BY personId;
```
---
Then export this as CSV and load it into Pandas 

--------
```python
import pandas as pd
df = pd.read_csv('newcomers_hayden_one.csv')

df['comps'].agg(['mean', 'median'])
Mean: 2.42
Median: 1

plt.figure(figsize=(12,6))
plt.bar(comps_count.index, comps_count.values, color='skyblue')
plt.xlabel('Number of Competitions Attended')
plt.ylabel('Number of Newcomers')
plt.title('Number of newcomers who Attended X Competitions Jan 2023 - Feb 2024')

# Set x-axis range from 1 to 15 and adjust ticks accordingly
plt.xticks(range(1, 16), rotation=0, ha='center')  # Ensure ticks are evenly spaced from 1 to 15
plt.xlim(1, 15)  # Limit x-axis to the r[newcomers_hayden_one.csv](https://github.com/user-attachments/files/17007230/newcomers_hayden_one.csv)
ange 1 to 15
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.tight_layout()
plt.show()
```

![WMT](https://github.com/user-attachments/assets/c337f604-a4e4-48d3-87b2-943e20309a5b)


## Total number of newcomers who attended competitions between April - July 2024

```SQL
CREATE TABLE four_mon AS 
(SELECT competitionId, COUNT(DISTINCT(personId)) AS newcomers
FROM (SELECT xx.personId, xx.competitionId
FROM (SELECT x.personId, x.competitionId, 
	  ROW_NUMBER() OVER (PARTITION BY x.personid ORDER BY c.year, c.month, c.day) as 'rnk'
	  FROM (SELECT personId, competitionId
			FROM Results r
			GROUP BY personId, competitionId) x
            JOIN Competitions c on x.competitionId = c.id) xx
where xx.rnk = 1) xyy
WHERE competitionId IN (SELECT id
FROM Competitions 
WHERE year = 2024 AND endmonth IN (4, 5, 6, 7))

GROUP BY competitionId);
```

Then...

```SQL
SELECT endMonth, SUM(newcomers) AS total_newcomers
FROM (SELECT fm.*, c.endMonth
      FROM four_mon fm
      JOIN Competitions c ON fm.competitionId = c.id) x
      
GROUP BY endMonth
ORDER BY endMonth;
```

[Uploading newcomers_haydenendMonth,total_newcomers
4,3073
5,2623
6,3121
7,1735
_two.csv…]()



