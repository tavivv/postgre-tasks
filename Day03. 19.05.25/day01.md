## Day03

### exercise 00

task:

Let’s make a simple aggregation, please write a SQL statement that returns person identifiers and corresponding number of visits in any pizzerias and sorting by count of visits in descending mode and sorting in `person_id` in ascending mode. Please take a look at the sample of data below.

```

SELECT 
    person_id, 
    COUNT(*) AS count_of_visits
FROM 
    person_visits
GROUP BY 
    person_id
ORDER BY 
    count_of_visits DESC, 
    person_id ASC;

```

![](00.png)

### exercise 01

task:

Please change a SQL statement from Exercise 00 and return a person name (not identifier). Additional clause is  we need to see only top-4 persons with maximal visits in any pizzerias and sorted by a person name. Please take a look at the example of output data below.

```

SELECT 
    p.name, 
    COUNT(*) AS count_of_visits
FROM 
    person_visits pv
JOIN 
    person p ON pv.person_id = p.id
GROUP BY 
    p.name, pv.person_id
ORDER BY 
    count_of_visits DESC, 
    p.name ASC
LIMIT 4;

```

![](01.png)

### exercise 02

task:

Please write a SQL statement to see 3 favorite restaurants by visits and by orders in one list (please add an action_type column with values ‘order’ or ‘visit’, it depends on data from the corresponding table). Please take a look at the sample of data below. The result should be sorted by action_type column in ascending mode and by count column in descending mode.

```

(
    SELECT 
        pz.name,
        COUNT(*) AS count,
        'order' AS action_type
    FROM 
        person_order po
    JOIN 
        menu m ON po.menu_id = m.id
    JOIN 
        pizzeria pz ON m.pizzeria_id = pz.id
    GROUP BY 
        pz.name
    ORDER BY 
        count DESC
    LIMIT 3
)
UNION ALL
(
    SELECT 
        pz.name,
        COUNT(*) AS count,
        'visit' AS action_type
    FROM 
        person_visits pv
    JOIN 
        pizzeria pz ON pv.pizzeria_id = pz.id
    GROUP BY 
        pz.name
    ORDER BY 
        count DESC
    LIMIT 3
)
ORDER BY 
    action_type ASC,
    count DESC;

```

![](02.png)

### exercise 03

task:

Please write a SQL statement to see restaurants are grouping by visits and by orders and joined with each other by using restaurant name.  
You can use internal SQLs from Exercise 02 (restaurants by visits and by orders) without limitations of amount of rows.

```

WITH orders AS (
    SELECT 
        pz.name,
        COUNT(*) AS order_count
    FROM 
        person_order po
    JOIN 
        menu m ON po.menu_id = m.id
    JOIN 
        pizzeria pz ON m.pizzeria_id = pz.id
    GROUP BY 
        pz.name
),
visits AS (
    SELECT 
        pz.name,
        COUNT(*) AS visit_count
    FROM 
        person_visits pv
    JOIN 
        pizzeria pz ON pv.pizzeria_id = pz.id
    GROUP BY 
        pz.name
)
SELECT 
    COALESCE(o.name, v.name) AS name,
    COALESCE(o.order_count, 0) + COALESCE(v.visit_count, 0) AS total_count
FROM 
    orders o
FULL OUTER JOIN 
    visits v ON o.name = v.name
ORDER BY 
    total_count DESC,
    name ASC;

```

![](03.png)

### exercise 04

task:

Please write a SQL statement that returns the person name and corresponding number of visits in any pizzerias if the person has visited more than 3 times (> 3).Please take a look at the sample of data below.

```

SELECT 
    p.name,
    COUNT(pv.id) AS count_of_visits
FROM 
    person_visits pv
JOIN 
    person p ON pv.person_id = p.id
GROUP BY 
    p.name, p.id
HAVING 
    COUNT(pv.id) > 3
ORDER BY 
    count_of_visits DESC,
    p.name ASC;

```

![](04.png)

### exercise 05

task:

Please write a simple SQL query that returns a list of unique person names who made orders in any pizzerias. The result should be sorted by person name. Please take a look at the sample below.

```

SELECT DISTINCT
    p.name
FROM 
    person_order po
JOIN 
    person p ON po.person_id = p.id
ORDER BY 
    p.name;

```

![](05.png)

### exercise 06

task:

Please write a SQL statement that returns the amount of orders, average of price, maximum and minimum prices for sold pizza by corresponding pizzeria restaurant. The result should be sorted by pizzeria name. Please take a look at the data sample below. 
Round your average price to 2 floating numbers.

```

SELECT 
    pz.name AS name,
    COUNT(*) AS count_of_orders,
    ROUND(AVG(m.price), 2) AS average_price,
    MAX(m.price) AS max_price,
    MIN(m.price) AS min_price
FROM 
    person_order po
JOIN 
    menu m ON po.menu_id = m.id
JOIN 
    pizzeria pz ON m.pizzeria_id = pz.id
GROUP BY 
    pz.name
ORDER BY 
    pz.name;

```

![](06.png)

### exercise 07

task:

Please write a SQL statement that returns a common average rating (the output attribute name is global_rating) for all restaurants. Round your average rating to 4 floating numbers.

```

SELECT 
    ROUND(AVG(m.price)::numeric, 4) AS global_rating
FROM 
    person_order po
JOIN 
    menu m ON po.menu_id = m.id;

```

![](07.png)

### exercise 08

task:

We know about personal addresses from our data. Let’s imagine, that particular person visits pizzerias in his/her city only. Please write a SQL statement that returns address, pizzeria name and amount of persons’ orders. The result should be sorted by address and then by restaurant name. Please take a look at the sample of output data below.

```

SELECT 
    p.address,
    pz.name,
    COUNT(*) AS count_of_orders
FROM 
    person_order po
JOIN 
    person p ON po.person_id = p.id
JOIN 
    menu m ON po.menu_id = m.id
JOIN 
    pizzeria pz ON m.pizzeria_id = pz.id
GROUP BY 
    p.address, pz.name
ORDER BY 
    p.address, pz.name;

```

![](08.png)

### exercise 09

task:

Please write a SQL statement that returns aggregated information by person’s address , the result of “Maximal Age - (Minimal Age  / Maximal Age)” that is presented as a formula column, next one is average age per address and the result of comparison between formula and average columns (other words, if formula is greater than  average then True, otherwise False value).

```

SELECT 
    address,
    MAX(age) - (MIN(age) / MAX(age)) AS formula,
    AVG(age) AS average,
    CASE 
        WHEN MAX(age) - (MIN(age) / MAX(age)) > AVG(age) THEN true
        ELSE false
    END AS comparison
FROM 
    person
GROUP BY 
    address
ORDER BY 
    address;

```

![](09.png)