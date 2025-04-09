本周学习了SQL中对空值的处理，聚集函数的使用。

# 本周作业（第4次作业）

## 题目一（2分）

请问下面的SQL语句是否合法？用实验验证你的想法。你从实验结果能得到什么结论？

```sql
SELECT dept_name, min(salary)
FROM instructor;

SELECT dept_name, min(salary)
FROM instructor
GROUP BY dept_name
HAVING name LIKE '%at%';

SELECT dept_name
FROM instructor
WHERE AVG(salary) > 20000;
```
答：
第一段：不合法。在 SQL 里，在 SELECT 子句中使用了聚合函数，同时还选取了非聚合列，必须使用 GROUP BY 子句对非聚合列进行分组。
第二段：不合法。此语句里的 name 既不在 GROUP BY 子句中，也不是聚合函数，所以会引发错误。
第三段：不合法。WHERE 子句不能直接使用聚合函数，要在分组之后使用。

## 题目二（3分+3分+2分）

1. 找到工资最高员工的名字，假设工资最高的员工只有一位（至少两种写法）。
答：
方法一
SELECT name
FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees)
LIMIT 1;
方法二
SELECT name
FROM employees
ORDER BY salary DESC
LIMIT 1;

2. 找到工资最高员工的名字，假设工资最高的员工有多位（试试多种写法）。
答：
方法一
SELECT name
FROM employees
WHERE salary = (SELECT MAX(salary) FROM employees);
方法二
WITH max_salary AS (
    SELECT MAX(salary) AS max_sal
    FROM employees
)
SELECT name
FROM employees
JOIN max_salary ON employees.salary = max_salary.max_sal;

3. 解释下面四句。

```sql
SELECT 1 IN (1);
此语句用于检查值 1 是否存在于集合 (1) 之中。由于集合 (1) 包含值 1，所以该语句会返回 TRUE。
SELECT 1 = (1);
该语句对两个值 1 和 (1) 进行相等性比较。因为 1 等于 1，所以此语句会返回 TRUE。
SELECT (1, 2) = (1, 2);
这里比较的是两个行值 (1, 2) 和 (1, 2)。由于这两个行值的对应元素都相等（第一个元素都是 1，第二个元素都是 2），所以该语句会返回 TRUE。
SELECT (1) IN (1, 2);
该语句检查值 (1) 是否存在于集合 (1, 2) 中。因为集合 (1, 2) 包含值 1，所以此语句会返回 TRUE。
```
