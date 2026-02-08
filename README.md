# -plsql_window_functions_27290_Rutaganda

This assignment has two main technical parts:<br>
Part A: SQL JOINs, <br>
Part B: SQL Window Functions

## Business problem 
Business Context<br>
  Company: Small Fruit Shop<br>
Data Challenge:<br>
  Track what fruits sell best each month<br>
Expected Outcome: <br>
  Small fruit shop needs to know which fruits sell best.
  Know which fruits to stock more of

## Schema and ER diagram
<img width="943" height="540" alt="erd" src="https://github.com/user-attachments/assets/e346ca22-c0eb-4c49-a9c9-d97fc087dcf3" /> <br>
Tables<br>
1. customers (id, name, city)<br>
2. fruits (id, name, price)<br>
3. sales (id, customer_id, fruit_id, date, quantity)

## JOIN queries
1. INNER JOIN - Retrieve transactions with valid customers and products<br>
SELECT s.id, c.name, f.name as fruit, s.quantity, s.sale_date<br>
FROM sales s<br>
INNER JOIN customers c ON s.customer_id = c.id<br>
INNER JOIN fruits f ON s.fruit_id = f.id;<br>
<img width="1366" height="730" alt="join 1" src="https://github.com/user-attachments/assets/09ed42cc-1a8f-45cb-8410-25b25653f3f5" /><br>

2. LEFT JOIN - Identify customers who have never made a transaction<br>
SELECT c.name, c.city<br>
FROM customers c<br>
LEFT JOIN sales s ON c.id = s.customer_id<br>
WHERE s.id IS NULL;<br>
<img width="1366" height="732" alt="join 2" src="https://github.com/user-attachments/assets/5bf42f58-0b0f-4610-bbdd-020238347e85" /><br>

3. RIGHT JOIN - (or FULL JOIN if RIGHT JOIN is avoided) Detect products with no sales activity<br>
SELECT f.name, f.price<br>
FROM sales s<br>
RIGHT JOIN fruits f ON s.fruit_id = f.id<br>
WHERE s.id IS NULL;<br>
<img width="1366" height="729" alt="join 3" src="https://github.com/user-attachments/assets/38309fb9-f059-447b-9a67-ad433dcc45b2" /><br>

4. FULL OUTER JOIN - Compare customers and products including unmatched records<br>
SELECT c.name, f.name as fruit<br>
FROM customers c<br>
FULL OUTER JOIN sales s ON c.id = s.customer_id<br>
FULL OUTER JOIN fruits f ON s.fruit_id = f.id;<br>
<img width="1366" height="732" alt="join 4" src="https://github.com/user-attachments/assets/17e811e6-e694-45b1-b456-4fc421dabe8e" /><br>

5. SELF JOIN - Compare customers within the same region or transactions within the same time period<br>
SELECT a.name as customer1, b.name as customer2, a.city<br>
FROM customers a<br>
JOIN customers b ON a.city = b.city<br>
WHERE a.id < b.id;<br>
<img width="1360" height="730" alt="join 5" src="https://github.com/user-attachments/assets/4182de76-c119-40a7-b921-5d38f8401d38" /><br>


## Window function queries
1. Ranking Functions ROW_NUMBER(), RANK(), DENSE_RANK(), PERCENT_RANK() Use case: Top N customers or products by revenue<br>
SELECT <br>
    f.name,<br>
    SUM(s.quantity) as total_sold,<br>
    RANK() OVER(ORDER BY SUM(s.quantity) DESC) as rank,<br>
    ROW_NUMBER() OVER(ORDER BY SUM(s.quantity) DESC) as row_num<br>
FROM sales s<br>
JOIN fruits f ON s.fruit_id = f.id<br>
GROUP BY f.name;<br>
<img width="1366" height="731" alt="w1" src="https://github.com/user-attachments/assets/eeba6ee9-36a2-48c0-a4f3-822b931f1d7d" /><br>

2. Aggregate Window Functions SUM(), AVG(), MIN(), MAX() Use both ROWS and RANGE frames Use case: Running totals and trends
SELECT <br>
    sale_date,<br>
    SUM(quantity) as daily_sales,<br>
    SUM(SUM(quantity)) OVER(ORDER BY sale_date) as running_total<br>
FROM sales<br>
GROUP BY sale_date<br>
ORDER BY sale_date;<br>
<img width="1366" height="730" alt="w2" src="https://github.com/user-attachments/assets/b3efc249-479f-47e5-8f38-7646156e3f83" /><br>

3. Navigation Functions LAG(), LEAD() Use case: Period-to-period comparison and growth<br>
WITH daily AS (<br>
    SELECT sale_date, SUM(quantity) as sales<br>
    FROM sales GROUP BY sale_date<br>
)<br>
SELECT <br>
    sale_date,<br>
    sales,<br>
    LAG(sales) OVER(ORDER BY sale_date) as previous_day,<br>
    sales - LAG(sales) OVER(ORDER BY sale_date) as change<br>
FROM daily;<br>
<img width="1366" height="726" alt="w3" src="https://github.com/user-attachments/assets/9212fe83-d2e8-4303-b960-eb17b5ec04b1" /><br>

4. Distribution Functions NTILE(4), CUME_DIST() Use case: Customer segmentation<br>
SELECT <br>
    c.name,<br>
    SUM(s.quantity) as total_fruits_bought,<br>
    NTILE(3) OVER(ORDER BY SUM(s.quantity) DESC) as segment<br>
FROM customers c<br>
LEFT JOIN sales s ON c.id = s.customer_id<br>
GROUP BY c.id, c.name;<br>
<img width="1366" height="728" alt="w4" src="https://github.com/user-attachments/assets/d82f2333-9466-4bed-a27e-193b4cfa2146" /><br>

## Key insights
All 5 customers made purchases (no inactive customers)<br>
One fruit had zero sales (all other 5 fruits sold at least once)<br>
Performers:<br>
1.Banana - Highest units sold (6)<br>
2.Apple - High units sold (6) + good price point<br>
3.Orange - Strong performance (5 units at premium price)<br>

## References
https://www.youtube.com/watch?v=1hosboTh53I<br>
https://www.youtube.com/watch?v=4qH-7w5LZsA<br>
https://www.youtube.com/watch?v=G3lJAxg1cy8&t=106s<br>
https://www.youtube.com/watch?v=lXQzD09BOH0<br>
https://www.youtube.com/watch?v=rIcB4zMYMas<br>
https://www.youtube.com/watch?v=0OQJDd3QqQM<br>
https://www.youtube.com/watch?v=RehbnzKHS28<br>
https://www.youtube.com/watch?v=Ww71knvhQ-s<br>

## Integrity statement
All sources used in this project were properly cited.  
The database design, SQL queries, analysis, and documentation represent my original work.  
No AI-generated content was copied directly or submitted as-is.  
This project complies fully with the academic integrity guidelines of the course.
