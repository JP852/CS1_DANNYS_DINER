# CS1_DANNYS_DINER

![image](https://github.com/JP852/CS1_DANNYS_DINER/assets/142391590/1b9381b1-5ac4-4638-9edf-e57575dee44e)

#### Link to Challenge

https://8weeksqlchallenge.com/case-study-1/

#### SQL Techniques Used

- CTEs
- Table Joins
- Aggregation
- Group By
- Order By
- Limit
- Where Clause

## Questions

### Question 1 - What is the total amount each customer spent at the restaurant?

```
SELECT 
    S.CUSTOMER_ID
    ,SUM(PRICE) AS TOTAL_SPEND
        FROM SALES AS S
            INNER JOIN MENU AS M
            ON S.PRODUCT_ID = M.PRODUCT_ID
                GROUP BY S.CUSTOMER_ID

```

### Question 2 - How many days has each customer visited the restaurant?

```
SELECT 
    CUSTOMER_ID
    ,COUNT(DISTINCT ORDER_DATE) AS Number_of_Days
        FROM SALES
            GROUP BY CUSTOMER_ID

```

### Question 3 - What was the first item from the menu purchased by each customer?

```
WITH CTE AS
(  
  
SELECT 
    M.PRODUCT_NAME
    ,S.CUSTOMER_ID
    ,S.ORDER_DATE
    ,RANK() OVER(PARTITION BY S.CUSTOMER_ID 
        ORDER BY S.ORDER_DATE ASC) AS RANK
            FROM SALES AS S
                INNER JOIN MENU AS M
                ON S.PRODUCT_ID = M.PRODUCT_ID)
SELECT 
    * 
        FROM CTE
            WHERE RANK = 1

```

### Question 4 - What is the most purchased item on the menu, and how many times was it purchased by all customers?

```
SELECT 
    COUNT(S.ORDER_DATE)
    ,M.PRODUCT_NAME
        FROM SALES AS S
            INNER JOIN MENU AS M
            ON S.PRODUCT_ID = M.PRODUCT_ID
                GROUP BY M.PRODUCT_NAME
                    ORDER BY COUNT(S.ORDER_DATE) DESC
                        LIMIT 1


```

### Question 5 - Which item was the most popular for each customer?

```
WITH CTE AS (
  
SELECT 
    S.CUSTOMER_ID 
    ,COUNT(S.ORDER_DATE) AS ORDERS
    ,M.PRODUCT_NAME
    ,RANK() OVER(PARTITION BY S.CUSTOMER_ID ORDER BY ORDERS DESC) AS RANK
        FROM SALES AS S
            INNER JOIN MENU AS M
            ON S.PRODUCT_ID = M.PRODUCT_ID
                GROUP BY S.CUSTOMER_ID 
                ,M.PRODUCT_NAME
                    ORDER BY ORDERS DESC)
SELECT 
    *
        FROM CTE
            WHERE RANK = 1


```

### Question 6 - Which item was purchased first by the customer after they became a member?

```
WITH CTE AS
(
  
SELECT 
    SAL.CUSTOMER_ID
    ,ORDER_DATE
    ,JOIN_DATE
    ,PRODUCT_NAME
    ,RANK() OVER(PARTITION BY SAL.CUSTOMER_ID ORDER BY ORDER_DATE ASC) AS RANK
        FROM SALES AS SAL
            INNER JOIN MEMBERS AS MEM
            ON SAL.CUSTOMER_ID = MEM.CUSTOMER_ID
                INNER JOIN MENU AS MEN
                ON SAL.PRODUCT_ID = MEN.PRODUCT_ID
                    WHERE ORDER_DATE >= JOIN_DATE)
SELECT 
    * 
        FROM CTE
            WHERE RANK = 1

```

### Question 7 - Which item was purchased just before the customer became a member?

```
WITH CTE AS
(
  
SELECT 
    SAL.CUSTOMER_ID
    ,ORDER_DATE
    ,JOIN_DATE
    ,PRODUCT_NAME
    ,RANK() OVER(PARTITION BY SAL.CUSTOMER_ID ORDER BY ORDER_DATE DESC) AS RANK
        FROM SALES AS SAL
            INNER JOIN MEMBERS AS MEM
            ON SAL.CUSTOMER_ID = MEM.CUSTOMER_ID
                INNER JOIN MENU AS MEN
                ON SAL.PRODUCT_ID = MEN.PRODUCT_ID
                    WHERE ORDER_DATE < JOIN_DATE)
SELECT 
    *
        FROM CTE
            WHERE RANK = 1

```

### Question 8 - What is the total items and amount spent for each member before they became a member?

```
SELECT 
    COUNT(PRODUCT_NAME) AS NUMBER_OF_PRODUCTS
    ,SUM(PRICE) AS TOTAL_AMOUNT
    ,SAL.CUSTOMER_ID
        FROM SALES AS SAL
            INNER JOIN MEMBERS AS MEM
            ON SAL.CUSTOMER_ID = MEM.CUSTOMER_ID
                INNER JOIN MENU AS MEN
                ON SAL.PRODUCT_ID = MEN.PRODUCT_ID
                    WHERE ORDER_DATE<JOIN_DATE
                        GROUP BY SAL.CUSTOMER_ID

```

### Question 9 - If each $1 spent equates to 10 points and sushi has a 2x points multiplier, how many points would each customer have?

```
SELECT 
    CUSTOMER_ID
    ,SUM(CASE WHEN PRODUCT_NAME = 'sushi' THEN PRICE * 10 * 2 ELSE PRICE * 10 END) AS POINTS
        FROM MENU AS M
            INNER JOIN SALES AS S
            ON S.PRODUCT_ID = M.PRODUCT_ID
                GROUP BY CUSTOMER_ID

```

### Question 10 - In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi. How many points do customer A and B have at the end of January?

```
SELECT 
    S.CUSTOMER_ID
    ,SUM(CASE WHEN ORDER_DATE BETWEEN MEM.JOIN_DATE AND DATEADD('days',7,MEM.JOIN_DATE) THEN PRICE * 10 * 2 WHEN PRODUCT_NAME = 'sushi' THEN PRICE * 10 * 2 ELSE PRICE * 10 END) AS POINTS
        FROM MENU AS MEN
            INNER JOIN SALES AS S
            ON S.PRODUCT_ID = MEN.PRODUCT_ID
                INNER JOIN MEMBERS AS MEM
                ON MEM.CUSTOMER_ID = S.CUSTOMER_ID
                    WHERE DATE_TRUNC('month',ORDER_DATE) = '2021-01-01'
                        GROUP BY S.CUSTOMER_ID

```






