# DeGraff_Week_4_Coursera_Final

## 1. Data Quality Check

First step is to review data
```SQL
SELECT * 
FROM dsv1069.final_assignments_qa;
```

## 2. Reformat the Data
I reformatted the table by unioning cases where each item is assigned.
```SQL
SELECT

  *
  
FROM
(
        SELECT 
          item_id
          ,(CASE WHEN test_a = 1 THEN 'test_a' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_a = 1 THEN 1 ELSE NULL
          END) as test_assignment
          ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
      UNION
        SELECT 
          item_id
          ,(CASE WHEN test_b = 1 THEN 'test_b' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_b = 1 THEN 1 ELSE NULL
          END) as test_assignment
          ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
      UNION
        SELECT 
          item_id
          ,(CASE WHEN test_c = 1 THEN 'test_c' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_c = 1 THEN 1 ELSE NULL
          END) as test_assignment
          ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
      UNION
        SELECT 
          item_id
          ,(CASE WHEN test_d = 1 THEN 'test_d' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_d = 1 THEN 1 ELSE NULL
          END) as test_assignment
          ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
      UNION 
        SELECT 
          item_id
          ,(CASE WHEN test_e = 1 THEN 'test_e' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_e = 1 THEN 1 ELSE NULL
          END) as test_assignment
          ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
      UNION 
        SELECT 
          item_id
          ,(CASE WHEN test_f = 1 THEN 'test_f' ELSE NULL
          END) as test_id
          ,(CASE WHEN test_f = 1 THEN 1 ELSE NULL
          END) as test_assignment
         ,NOW() AS test_start_date
        FROM 
          dsv1069.final_assignments_qa
        ) sub
WHERE test_id is not null
ORDER BY item_id, test_id
```

## 3. Compute Order Binary

```SQL
SELECT 
  test_assignment
  ,COUNT(DISTINCT itemid)
  ,SUM(order_binary) AS total_orders_30d
FROM
    (
    SELECT 
     *--itemid
     --,test_assignment
     --,test_start_date
     --,price
     --,paid_at
     ,(CASE WHEN (time_difference) < 31 AND time_difference >= 0 THEN 1 ELSE 0 END) AS order_binary
     
    FROM
    (
    SELECT
      *,
      EXTRACT(day from paid_at - test_start_date ) AS time_difference
      ,fa.item_id AS itemid
    FROM 
      dsv1069.final_assignments fa
    LEFT JOIN 
      dsv1069.orders
    ON 
      orders.item_id = fa.item_id
    WHERE 
      test_number = 'item_test_2'
    ) sub
      ) sub2
GROUP BY 
  test_assignment
```
![image](https://github.com/mwadegraff/DeGraff_Week_4_Coursera_Final/assets/7571381/c434668d-d64b-44ab-aeee-291a86ada29f)
![image](https://github.com/mwadegraff/DeGraff_Week_4_Coursera_Final/assets/7571381/f552980b-fb7e-4057-bab4-fd9593328e21)

## 4. Compute View Binary

```SQL
-- Use this table to 
-- compute view_binary for the 30 day window after the test_start_date
-- for the test named item_test_2
SELECT 
  test_assignment
  --,test_number
  --,event_time
  --,platform
  --,referrer
  ,COUNT(DISTINCT itemid) AS number_of_items
  ,SUM(view_binary) AS view_binary_30d
FROM
    (
    SELECT 
      itemid
      ,test_assignment
      ,MAX(CASE WHEN (time_difference) < 31 AND time_difference >= 0 THEN 1 ELSE 0 END) AS view_binary
    FROM
    (
    SELECT
      *,
      EXTRACT(day from event_time - test_start_date ) AS time_difference
      ,fa.item_id AS itemid
    FROM 
      dsv1069.final_assignments fa
    LEFT JOIN 
      dsv1069.view_item_events
    ON 
      view_item_events.item_id = fa.item_id
    WHERE 
      test_number = 'item_test_2'
    ) sub
    GROUP BY 
      itemid
      ,test_assignment
  ) sub2
GROUP BY 
  test_assignment

```
![image](https://github.com/mwadegraff/DeGraff_Week_4_Coursera_Final/assets/7571381/6eca085f-ebeb-4570-a18c-5310c0fb4e94)
![image](https://github.com/mwadegraff/DeGraff_Week_4_Coursera_Final/assets/7571381/72d281ca-7cad-4103-8f95-c008cdbfef42)

