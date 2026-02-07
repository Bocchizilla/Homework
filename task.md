<details>
  <summary> задачи 1–50</summary>

# [1] Задача

SELECT first_name,
       last_name,
       department_id,
       salary,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
FROM employees
WHERE salary >= 30000;

# [2] Задача
SELECT e.first_name,
       e.last_name,
       e.department_id,
       e.salary - a.avg_salary AS diff_from_avg
FROM employees e
JOIN (
    SELECT department_id, AVG(salary) AS avg_salary
    FROM employees
    GROUP BY department_id
) a ON e.department_id = a.department_id
WHERE e.salary > a.avg_salary;

# [3] Задача
SELECT first_name,
       last_name,
       department_id,
       salary
FROM (
    SELECT *,
           COUNT(*) OVER (PARTITION BY department_id) AS cnt,
           RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE cnt >= 3 AND rnk = 1;

# [4] Задача
SELECT c.customer_name,
       o.amount,
       RANK() OVER (PARTITION BY o.customer_id ORDER BY o.amount DESC) AS order_rank
FROM orders o
JOIN customers c ON o.customer_id = c.customer_id
WHERE o.amount > 1000;

# [5] Задача
SELECT first_name,
       last_name,
       department_id,
       hire_date
FROM (
    SELECT *,
           RANK() OVER (PARTITION BY department_id ORDER BY hire_date DESC) AS rnk
    FROM employees
) t
WHERE rnk = 1;
# [6] Задача
SELECT customer_id,
       SUM(amount) AS total_amount,
       SUM(amount) / SUM(SUM(amount)) OVER () AS share
FROM orders
GROUP BY customer_id
HAVING COUNT(*) > 2;

# [7] Задача
SELECT *
FROM (
    SELECT *,
           NTILE(10) OVER (PARTITION BY department_id ORDER BY salary DESC) AS tile
    FROM employees
) t
WHERE tile = 1;

# [8] Задача
SELECT order_id,
       customer_id,
       order_date,
       order_date - LAG(order_date) OVER (
           PARTITION BY customer_id ORDER BY order_date
       ) AS days_from_prev
FROM orders;

# [9] Задача
SELECT first_name,
       last_name,
       department_id,
       salary,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    HAVING AVG(salary) > 50000
);

# [10] Задача
SELECT customer_id,
       COUNT(*) AS orders_cnt
FROM orders
GROUP BY customer_id
HAVING MAX(amount) > (SELECT AVG(amount) FROM orders);

# [11] Задача
SELECT first_name,
       last_name,
       salary,
       SUM(salary) OVER (PARTITION BY department_id) AS dept_total
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    HAVING COUNT(*) > 5
);

# [12] Задача
SELECT o.order_id,
       o.customer_id,
       o.amount
FROM orders o
JOIN (
    SELECT DATE_TRUNC('month', order_date) AS m,
           AVG(amount) AS avg_amount
    FROM orders
    GROUP BY m
) t ON DATE_TRUNC('month', o.order_date) = t.m
WHERE o.amount > t.avg_amount;

# [13] Задача
SELECT first_name,
       last_name,
       department_id,
       salary,
       SUM(salary) OVER (
           PARTITION BY department_id
           ORDER BY hire_date
       ) AS cumulative_salary
FROM employees;

# [14] Задача
SELECT customer_id,
       amount,
       RANK() OVER (PARTITION BY customer_id ORDER BY amount DESC) AS rnk
FROM orders
WHERE customer_id IN (
    SELECT customer_id
    FROM orders
    GROUP BY customer_id
    HAVING MAX(amount) > 5000
);
# [15] Задача
SELECT *
FROM (
    SELECT *,
           salary - LAG(salary) OVER (
               PARTITION BY department_id ORDER BY hire_date
           ) AS diff
    FROM employees
) t
WHERE diff > 0;
# [16] Задача
SELECT customer_id,
       COUNT(*) AS cnt,
       COUNT(*)::numeric / SUM(COUNT(*)) OVER () AS share
FROM orders
WHERE EXTRACT(YEAR FROM order_date) = 2024
GROUP BY customer_id;
# [17] Задача
SELECT *
FROM employees e
WHERE salary >
(
    SELECT PERCENTILE_CONT(0.5)
    WITHIN GROUP (ORDER BY salary)
    FROM employees
    WHERE department_id = e.department_id
);
# [18] Задача
SELECT *
FROM (
    SELECT *,
           amount - LEAD(amount) OVER (
               PARTITION BY customer_id ORDER BY order_date
           ) AS diff
    FROM orders
) t
WHERE diff > 0;
# [19] Задача
SELECT department_id,
       AVG(salary) AS avg_salary,
       RANK() OVER (ORDER BY AVG(salary) DESC) AS rnk
FROM employees
GROUP BY department_id
HAVING COUNT(*) > 4;

# [20] Задача
SELECT *
FROM (
    SELECT *,
           salary / SUM(salary) OVER (PARTITION BY department_id) AS share
    FROM employees
) t
WHERE share > 0.2;

# [21] Задача
SELECT *,
       MAX(amount) OVER (PARTITION BY customer_id) AS max_amount
FROM orders
WHERE order_date >= CURRENT_DATE - INTERVAL '6 months';

# [22] Задача
SELECT e.first_name,
       e.last_name,
       e.department_id
FROM employees e
JOIN (
    SELECT EXTRACT(YEAR FROM hire_date) AS y,
           AVG(salary) AS avg_salary
    FROM employees
    GROUP BY y
) t ON EXTRACT(YEAR FROM e.hire_date) = t.y
WHERE e.salary > t.avg_salary;

# [23] Задача
SELECT order_id,
       customer_id,
       COUNT(*) OVER (
           PARTITION BY customer_id
           ORDER BY order_date
           ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
       ) AS orders_before
FROM orders;

# [24] Задача
SELECT first_name,
       last_name,
       department_id,
       salary / MAX(salary) OVER (PARTITION BY department_id) AS share_of_max
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    HAVING MAX(salary) > 100000
);

# [25] Задача
SELECT e.first_name,
       e.last_name,
       e.salary -
       (
           SELECT AVG(salary)
           FROM employees e2
           WHERE e2.hire_date > e.hire_date
       ) AS diff
FROM employees e;

# [26] Задача
SELECT customer_id,
       COUNT(*) AS cnt,
       RANK() OVER (ORDER BY COUNT(*) DESC) AS rnk
FROM orders
GROUP BY customer_id
HAVING SUM(amount) > 10000;

# [27] Задача
SELECT *
FROM (
    SELECT *,
           AVG(amount) OVER (
               PARTITION BY customer_id
               ORDER BY order_date
               ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
           ) AS prev_avg
    FROM orders
) t
WHERE amount > prev_avg;

# [28] Задача
SELECT *
FROM (
    SELECT *,
           NTILE(4) OVER (PARTITION BY department_id ORDER BY salary) AS tile
    FROM employees
) t
WHERE tile = 1;

# [29] Задача
SELECT customer_id,
       COUNT(*) AS cnt
FROM orders
GROUP BY customer_id
HAVING AVG(amount) > (SELECT AVG(amount) FROM orders);

# [30] Задача
SELECT *
FROM (
    SELECT *,
           NTILE(10) OVER (PARTITION BY department_id ORDER BY hire_date) AS tile
    FROM employees
) t
WHERE tile = 1;

# [31] Задача
SELECT *
FROM orders
WHERE amount > 0.5 * MAX(amount) OVER (PARTITION BY customer_id);

# [32] Задача
SELECT first_name,
       last_name,
       department_id,
       RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
FROM employees
WHERE department_id IN (
    SELECT department_id
    FROM employees
    GROUP BY department_id
    HAVING MIN(salary) > 20000
);

# [33] Задача
SELECT order_id,
       customer_id,
       COUNT(*) OVER (
           PARTITION BY customer_id, DATE_TRUNC('month', order_date)
       ) AS month_cnt
FROM orders;

# [34] Задача
SELECT first_name,
       last_name,
       department_id
FROM employees e
WHERE salary <
(
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.hire_date < e.hire_date
);

# [35] Задача
SELECT *
FROM employees e
WHERE salary >
(
    SELECT PERCENTILE_CONT(0.75)
    WITHIN GROUP (ORDER BY salary)
    FROM employees
    WHERE department_id = e.department_id
);

# [36] Задача
SELECT *
FROM orders
WHERE order_date = (
    DATE_TRUNC('month', order_date)
    + INTERVAL '1 month - 1 day'
);

# [37] Задача
SELECT first_name,
       last_name,
       RANK() OVER (ORDER BY salary DESC) AS company_rank
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);

# [38] Задача
SELECT customer_id
FROM orders
WHERE EXTRACT(YEAR FROM order_date) = 2024
GROUP BY customer_id
HAVING COUNT(DISTINCT EXTRACT(MONTH FROM order_date)) = 12;

# [39] Задача
SELECT *
FROM (
    SELECT *,
           salary / SUM(salary) OVER (PARTITION BY department_id) AS share
    FROM employees
) t
WHERE share < 0.1;

# [40] Задача
SELECT *
FROM (
    SELECT *,
           SUM(amount) OVER (
               PARTITION BY customer_id, EXTRACT(YEAR FROM order_date)
               ORDER BY order_date
               ROWS BETWEEN UNBOUNDED PRECEDING AND 1 PRECEDING
           ) AS prev_sum
    FROM orders
) t
WHERE amount > prev_sum;

# [41] Задача
SELECT first_name,
       last_name
FROM employees e
WHERE salary >
(
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
      AND EXTRACT(YEAR FROM e2.hire_date) =
          EXTRACT(YEAR FROM e.hire_date) - 1
);

# [42] Задача
SELECT *
FROM orders
WHERE (customer_id, DATE_TRUNC('month', order_date)) IN (
    SELECT customer_id,
           DATE_TRUNC('month', order_date)
    FROM orders
    GROUP BY customer_id, DATE_TRUNC('month', order_date)
    HAVING MAX(amount) > 10000
);

# [43] Задача
SELECT *
FROM employees e
WHERE salary = (
    SELECT MIN(salary)
    FROM employees
    WHERE department_id = e.department_id
);

# [44] Задача
SELECT *
FROM (
    SELECT *,
           AVG(amount) OVER (
               ORDER BY order_date
               RANGE BETWEEN INTERVAL '3 months' PRECEDING
               AND INTERVAL '1 day' PRECEDING
           ) AS avg_prev
    FROM orders
) t
WHERE amount > avg_prev;

# [45] Задача
SELECT *
FROM employees e
WHERE salary >
(
    SELECT AVG(salary)
    FROM employees e2
    WHERE e2.department_id = e.department_id
      AND e2.hire_date > e.hire_date
);

# [46] Задача
SELECT customer_id
FROM orders
WHERE EXTRACT(YEAR FROM order_date) = 2024
GROUP BY customer_id, EXTRACT(QUARTER FROM order_date)
HAVING SUM(amount) > 5000;

# [47] Задача
SELECT *
FROM (
    SELECT *,
           DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rnk
    FROM employees
) t
WHERE rnk = 2;

# [48] Задача
SELECT *
FROM orders
WHERE amount < 0.05 * SUM(amount) OVER (PARTITION BY customer_id);

# [49] Задача
SELECT *
FROM employees e
WHERE DATE_TRUNC('month', hire_date) =
(
    SELECT DATE_TRUNC('month', MIN(hire_date))
    FROM employees
    WHERE department_id = e.department_id
);

# [50] Задача

SELECT customer_id
FROM orders
GROUP BY customer_id

HAVING MIN(amount) > AVG(amount);
