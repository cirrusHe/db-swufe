# 本周作业（第5次作业）

考虑关系模式`product(product_no, name, price)`，完成下面的题目：

## 题目一（4分）

在数据库中创建该关系，并自建上面关系的txt数据文件：

1. 使用`COPY`命令导入数据库（PostgreSQL）；或使用`LOAD DATA`命令导入数据库（MySQL）。
2. 将该关系导出为任意文件（如SQL、Txt、CSV、JSON等）。

答：
```sql
-- 创建 product 表
CREATE TABLE product (
    product_no INT,
    name VARCHAR(25),
    price DECIMAL(10, 2)
);
-- 从文件导入数据到 product 表
COPY product (product_no, name, price)
FROM '/path/to/product_data.txt'
DELIMITER '|';
-- 导出数据到 CSV 文件
COPY product TO '/path/to/exported_product_data.csv'
WITH (FORMAT CSV, HEADER);
```
## 题目二（6分）

1. 添加一个新的商品，编号为`666`，名字为`cake`，价格不详。
2. 使用一条SQL语句同时添加3个商品，内容自拟。
3. 将商品价格统一打8折。
4. 将价格大于100的商品上涨2%，其余上涨4%。
5. 将名字包含`cake`的商品删除。
6. 将价格高于平均价格的商品删除。

答：
```sql
INSERT INTO product (product_no, name, price)
VALUES (666, 'cake', NULL);

INSERT INTO product (product_no, name, price)
VALUES 
(777, 'chocolate', 5.99),
(888, 'ice cream', 3.49),
(999, 'cookies', 2.99);

UPDATE product
SET price = price * 0.8
WHERE price IS NOT NULL;

UPDATE product
SET price = 
    CASE 
        WHEN price > 100 THEN price * 1.02
        ELSE price * 1.04
    END
WHERE price IS NOT NULL;

DELETE FROM product
WHERE name LIKE '%cake%';

DELETE FROM product
WHERE price > (SELECT AVG(price) FROM product WHERE price IS NOT NULL);
```

## 题目三（5分）

### 针对PostgreSQL

使用参考下面的语句添加10万条商品，

```sql
-- PostgreSQL Only
INSERT INTO product (name, price)
SELECT
    'Product' || generate_series, -- 生成名称 Product1, Product2, ...
    ROUND((random() * 1000)::numeric, 2) -- 生成0到1000之间的随机价格，保留2位小数
FROM generate_series(1, 100000);
```

比较`DELETE`和`TRUNCATE`的性能差异。

答：
```sql
-- 创建 product 表
CREATE TABLE product (
    product_no SERIAL PRIMARY KEY,
    name VARCHAR(255),
    price DECIMAL(10, 2)
);
-- 插入 10 万条商品数据
INSERT INTO product (name, price)
SELECT
    'Product' || generate_series, -- 生成名称 Product1, Product2, ...
    ROUND((random() * 1000)::numeric, 2) -- 生成0到1000之间的随机价格，保留2位小数
FROM generate_series(1, 100000);
-- 分析 DELETE 操作的性能
EXPLAIN ANALYZE DELETE FROM product;
-- 重新插入 10 万条商品数据
INSERT INTO product (name, price)
SELECT
    'Product' || generate_series, -- 生成名称 Product1, Product2, ...
    ROUND((random() * 1000)::numeric, 2) -- 生成0到1000之间的随机价格，保留2位小数
FROM generate_series(1, 100000);
-- 分析 TRUNCATE 操作的性能
EXPLAIN ANALYZE TRUNCATE TABLE product;
```
DELETE：DELETE会逐行删除表中的数据，操作比较慢，尤其是在处理大量数据时。
TRUNCATE：TRUNCATE会直接删除整个表的数据，而不会逐行删除，因此通常比 DELETE 操作快得多。
### 针对MySQL

参考`generate_data.py`生成数据，在MySQL比较`LOAD DATA`和[SELECT INTO](https://dev.mysql.com/doc/refman/8.0/en/select-into.html)的性能差异。

