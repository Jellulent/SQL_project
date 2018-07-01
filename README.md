# SQL_project



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
