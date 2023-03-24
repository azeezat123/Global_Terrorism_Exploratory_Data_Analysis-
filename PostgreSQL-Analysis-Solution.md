## Questions
1. How many successful attacks were carried out, and what was the total number of deaths?
2. Which target types were the most prominent in successful terrorist attacks?
3. What was the most common attack type in successful terrorist attacks?
4. Which regions have the highest number of successful terrorist activities?
5. Which countries had the most terrorist attacks, and what were the number of fatalities, kidnappings, and injuries in those countries?
6. What happened to the hostages who were kidnapped after the attacks?
7. What are the top six weapon types used in successful terrorist attacks, based on the number of fatalities and injuries?
8. What is the trend of successful terrorist attacks over the years?

***

## Solutions
1. How many successful attacks were carried out, and what was the total number of deaths?
```sql
SELECT count(success) AS successful_attack,
       count(nkill) AS total_death
FROM attack
WHERE attack.success = 1;
```
| successful_attack | total_death |
|------------------|-------------|
|     161632      |   151721   |

***

2.  Which target types were the most prominent in successful terrorist attacks?
```sql
SELECT count(*) AS target_count, targtype1
FROM attack
WHERE success = 1 AND targtype1 IS NOT NULL
GROUP BY targtype1
ORDER BY (count(*)) DESC
LIMIT 6;
```
| target_count | targtype1                     |
|--------------|-----------                    |
|    40658     |   Private Citizens & Property |
|    25210     |   Military                    |
|    22273     |   Police                      |
|    19245     |   Business                    |
|    17957     |   Government (General)        |
|    6114      |   Transportation              |

***

3. What was the most common attack type in successful terrorist attacks?
```sql
SELECT count(*) AS number_of_attacks, attacktype1
FROM attack
WHERE success = 1 AND attacktype1 IS NOT NULL
GROUP BY attacktype1
ORDER BY (count(*)) DESC
LIMIT 6;
```
| number_of_attacks | attacktype1                  |
|--------------     |-----------                       |
|    77530          |   Bombing/Explosion              |
|    40345          |   Armed Assault                  |
|    14615          |   Assassination                  |
|    10907          |   Hostage Taking (Kidnapping)    |
|    9779           |   Facility/Infrastructure Attack |
|    6015           |   Unknown                        |

***

4. Which regions have the highest number of successful terrorist activities?
```sql
SELECT count(*) AS number_of_attacks, locations.region
FROM locations
JOIN attack ON locations.event_id = attack.event_id
WHERE attack.success = 1 
GROUP BY locations.region
ORDER BY (count(*)) DESC
LIMIT 6;
```
| number_of_attacks | region                 |
|--------------     |-----------                       |
|    44319          |   Middle East & North Africa              |
|    39369          |   South Asia                 |
|    17620          |  South America                |
|    16277          |   Sub-Saharan Africa    |
|    14161           |   Western Europe |
|    11151           |   Southeast Asia                       |

***

5. Which countries had the most terrorist attacks, and what were the number of fatalities, kidnappings, and injuries in those countries?
```sql
SELECT locations.country,
       count(attack.nkill) AS total_deaths,
       count(attack.nwound) AS total_wounded,
       count(event.nhostkid) AS total_kidnapped
FROM attack
JOIN locations ON attack.event_id = locations.event_id
JOIN event ON attack.event_id = event.id
WHERE attack.success = 1 AND (attack.nkill > 0 OR attack.nwound > 0) AND locations.country IS NOT NULL
GROUP BY locations.country
ORDER BY (count(*)) DESC
LIMIT 5;
```
|Country                | total_deaths | total_wounded | total_kidnapped |
|-----------------------|------------------|--------------|----------------|
| Iraq| 44319           |19163     | 18684       |487
| Afghanistan            | 9450          | 9117     | 613       |
| Pakistan         | 8415          | 8228      | 346        |
|India    | 6852          | 6703      | 706        |
| Colombia        | 4050           | 3938       |257         |

***

6. What happened to the hostages who were kidnapped after the attacks?
```sql
SELECT count(*) AS no_of_hostages, hostkidoutcome AS hostage_outcome
FROM event
WHERE ishostkid = 1 AND hostkidoutcome IS NOT NULL
GROUP BY hostage_outcome
ORDER BY (count(*)) DESC;
```
| no_of_hostages | hostage_outcome                 |
|--------------     |-----------                       |
|    3598         |  Unknown             |
|    3095         |   Hostage(s) released by perpetrators                |
|   2506         |  Hostage(s) killed (not during rescue attempt)               |
|   1013         |   Combination    |
|   534          |   Successful Rescue |
|   157           |   Hostage(s) escaped (not during rescue attempt)                      |
|   19          |   Attempted Rescue                     |

***

7. What are the top six weapon types used in successful terrorist attacks, based on the number of fatalities and injuries?
```sql
SELECT weapsubtype1,
       count(nkill) AS total_killed,
       count(nwound) AS total_wounded
FROM attack
WHERE attack.success = 1 AND (nkill > 0 OR nwound > 0) AND weapsubtype1 IS NOT NULL
GROUP BY weapsubtype1
ORDER BY (count(*)) DESC
LIMIT 6;
```
|Country                | total_killed | total_wounded |
|-----------------------|------------------|--------------|
| Unknown Gun Type| 25621           |23280    | 
| Unknown Explosive Type           | 17373          |16956    | 
| Automatic or Semi-Automatic Rifle        | 12141          | 11790    | 
|Vehicle   | 7705         | 7327      |
| Projectile (rockets, mortars, RPGs, etc.)       |5224           | 4921      |
| Handgun      | 5161           | 5093       |

***

8. What is the trend of successful terrorist attacks over the years?
```sql
SELECT 
    CASE 
        WHEN year BETWEEN 1970 AND 1979 THEN '1970-1979'
        WHEN year BETWEEN 1980 AND 1989 THEN '1980-1989'
        WHEN year BETWEEN 1990 AND 1999 THEN '1990-1999'
        WHEN year BETWEEN 2000 AND 2009 THEN '2000-2009'
        WHEN year BETWEEN 2010 AND 2017 THEN '2010-2017'
    END AS year_grouped,
    COUNT(*) AS number_of_attacks
FROM event
JOIN attack ON event.id = attack.event_id
WHERE attack.success = 1
GROUP BY year_bin
ORDER BY MIN(year);

```
| number_of_attacks | year_grouped                |
|--------------     |-----------                       |
|   8975         |   1970-1979           |
|   28834         |   1980-1989                |
|   26137         |  1990-1999               |
|    23293          |  2000-2009    |
|       74393      |  2010-2017 |

