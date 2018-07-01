# SQL_project
SELECT COUNT (DISTINCT user_id) AS 'HTO'
FROM home_try_on;

SELECT COUNT (DISTINCT user_id) AS 'Purchase'
FROM purchase;
  
SELECT COUNT (DISTINCT user_id) AS 'Quiz'
FROM quiz;
        
SELECT COUNT (DISTINCT user_id) AS 'Survey'
FROM survey;


SELECT 
	COUNT (DISTINCT CASE 
         WHERE home_try_on.number_of_pairs = '5 pairs' 
         THEN user_id END) AS '5 Pairs',
  COUNT (DISTINCT CASE 
         WHERE home_try_on.number_of_pairs = '3 pairs' 
         THEN user_id END) AS '3 Pairs'
FROM home_try_on;

WITH funnel AS (
		SELECT p.user_id,
			number_of_pairs
		FROM purchase AS 'p'
		LEFT JOIN home_try_on AS 'h'
			ON h.user_id = p.user_id)
SELECT 
COUNT (DISTINCT CASE
    WHEN number_of_pairs = '5 pairs' THEN user_id END) AS '5 pairs',
COUNT (DISTINCT CASE
    WHEN number_of_pairs = '3 pairs' THEN user_id END) AS '3 pairs'
FROM funnel;


WITH quiz_to_purchase AS
(SELECT q.user_id,
    h.user_id IS NOT NULL AS 'is_home_try_on',
    h.number_of_pairs,
    p.user_id IS NOT NULL AS 'is_purchase'
FROM quiz q
LEFT JOIN home_try_on h
    ON q.user_id = h.user_id
LEFT JOIN purchase p
    ON p.user_id = q.user_id)
SELECT count(*),
SUM(is_home_try_on),
SUM(is_purchase)
FROM quiz_to_purchase;

WITH q_p AS (
	SELECT *
	FROM purchase AS 'p'
	LEFT JOIN quiz AS 'q'
		ON q.user_id = p.user_id)
SELECT style,
	COUNT (DISTINCT user_id)
FROM q_p
GROUP BY style;

WITH q_p AS (
	SELECT *
	FROM purchase AS 'p'
	LEFT JOIN quiz AS 'q'
		ON q.user_id = p.user_id)
SELECT price,
	COUNT (DISTINCT user_id),
  model_name
FROM q_p
GROUP BY price;

WITH q_p AS (
	SELECT *
	FROM purchase AS 'p'
	LEFT JOIN quiz AS 'q'
		ON q.user_id = p.user_id)
	
SELECT model_name,
	COUNT (DISTINCT user_id),
  style
FROM q_p
GROUP BY model_name;

WITH funnel AS (
  SELECT DISTINCT q.user_id,
     h.user_id IS NOT NULL AS 'is_checkout',
     p.user_id IS NOT NULL AS 'is_purchase'
  FROM quiz AS 'q'
  LEFT JOIN home_try_on AS 'h'
    ON h.user_id = q.user_id
  LEFT JOIN purchase AS 'p'
    ON p.user_id = h.user_id)
SELECT COUNT (*) AS 'num_quiz',
SUM (is_checkout) AS 'num_hto',
SUM (is_purchase) AS 'num_purchase',
	1.0 * SUM (is_checkout)/
	COUNT(user_id),
  1.0 * SUM (is_purchase) /
  SUM (is_checkout)
FROM funnel;
