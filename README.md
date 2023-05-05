# BikeStores

![diagram](images/Screenshot%202023-05-05%20at%2013.58.50.png)

**1. Liệt kê các loại sản phẩm có bắt đầu bằng kí tự 'C'**

```sql
SELECT * FROM products WHERE product_name LIKE 'C%';
```

**2. Liệt kê các thông tin: tên sản phẩm, mã sản phẩm, tên loại sản phẩm, mã loại sản phẩm mà tên loại sản phẩm chứ kí tự 'ou'**

```sql
SELECT product_id, product_name FROM products WHERE product_name LIKE '%ou%';
```

**3. Liệt kê các thông tin: tên đầy đủ của khách hàng, email mà đã đặt hàng vào ngày 04.01.2016**


```sql
SELECT concat(first_name, ' ', last_name) as full_name, email FROM orders LEFT JOIN customers ON orders.customer_id = customers.customer_id WHERE order_date = '2016-01-04';
```

**4. Liệt kê các thông tin: tên đầy đủ khách hàng, email, ngày đặt hàng, tên cửa hàng, số điện thoại cửa hàng mà tên cửa hàng là Santa Cruz Bikes**

```sql
SELECT CONCAT(first_name, ' ', last_name) as full_name, customers.email, order_date, store_name, stores.phone FROM orders
INNER JOIN customers ON orders.customer_id = customers.customer_id
INNER JOIN stores ON orders.store_id = stores.store_id
WHERE store_name = 'Santa Cruz Bikes';
```

**5. Liệt kê các thông tin: tên sản phẩm, mã sản phẩm, số lượng sản phẩm đó được đặt mua nhiều hơn 1**

```sql
SELECT order_products.product_id, product_name, total_quantity
FROM (SELECT product_id, SUM(quantity) as total_quantity FROM order_items GROUP BY product_id HAVING SUM(quantity) > 1) as order_products
LEFT JOIN products ON order_products.product_id = products.product_id;
```

**6. Liệt kê tất cả thông tin quản lý, ngày giao hàng trong khoảng từ ngày 3 đến ngày 5**

```sql
SELECT staffs.* FROM staffs
INNER JOIN orders
ON staffs.staff_id = orders.staff_id
WHERE orders.shipped_date BETWEEN '2016-01-03' AND '2016-01-05';
```

**7. Liệt kê tất cả các thông tin khách hàng, ngày đặt hàng đã mua hoặc không mua sản phẩm**

**8. Liệt kê thông tin: tên cửa hàng, mã cửa hàng, số lượng tồn kho của tất cả các cửa hàng**

```sql
SELECT store_id, store_name, COALESCE(SUM(quantity), 0) as total_quantity FROM
(
	SELECT stores.store_id, stores.store_name, stocks.quantity FROM stores
	LEFT JOIN stocks
	ON stores.store_id = stocks.store_id
) AS joined_table
GROUP BY store_id, store_name;
```

**9. Liệt kê các thông tin: mã loại hàng, tên loại hàng, mã sản phẩm, tên sản phẩm, năm sản phẩm mà có đầy đủ các mã loại sản phẩm được sắp xếp tăng dần**

```sql
SELECT products.product_id, products.product_name, products.model_year, categories.* FROM products
LEFT JOIN categories
ON products.category_id = categories.category_id
ORDER BY product_id ASC;
```

**10. Liệt kê các thông tin: tên đầy đủ khách hàng, tên cửa hàng, ngày đặt hàng mà có đầy đủ các loại cửa hàng với mã cửa hàng được sắp xếp giảm dần**

```sql

```

**11. Liệt kê các thông tin: mã nhãn hàng, tên nhãn hàng, mã sản phẩm mà có đầy đủ các mã nhãn hàng**


```sql
```

**12. Liệt kê các thông tin: tên cửa hàng, số điện thoại, tên sản phẩm, mã sản phẩm mà có đầy đủ các sản phẩm trưng bày để bán**

```sql
```

**13. In các thông tin các khách hàng đã đặt hàng theo tích đề cát**

```sql
SELECT customers.* FROM customers
CROSS JOIN orders; 
```

**14. In tên các sản phẩm, tên loại sản phẩm, mã loại sản phẩm mà sản phẩm đó không được phân loại trong bảng sản phẩm**

```sql
```

**15. In tên các nhãn hàng, mã nhãn hàng, tên sản phẩm mà sản phẩm đó không có thông tin nhãn hàng sản xuất**

```sql

```

**16. In các thông tin: tên cửa hàng, số điện thoại, số lượng hàng tồn kho mà số lượng hàng tồn kho còn trên 20**

```sql
SELECT store_name, phone, quantity FROM stores
LEFT JOIN
(SELECT store_id, SUM(quantity) as quantity FROM stocks GROUP BY store_id) AS store_stocks_table
ON stores.store_id = store_stocks_table.store_id
WHERE quantity > 20;
```

**17. In mã khách hàng, tên đầy đủ khách hàng và tổng số lượng sản phẩm đã mua ở các đơn hàng**

```sql
SELECT order_id, concat(customers.first_name, ' ', customers.last_name) AS full_name, customers.customer_id, total_quantity FROM customers
INNER JOIN
(SELECT orders.order_id, customer_id, coalesce(sum(quantity), 0) AS total_quantity
FROM orders
LEFT JOIN order_items ON orders.order_id = order_items.order_id
GROUP BY orders.order_id, customer_id) AS total_order_quantity
ON customers.customer_id = total_order_quantity.customer_id;
```

**18. In ra tên cửa hàng, mã cửa hàng và số lượng đơn hàng mà các cửa hàng đang có**

```sql
SELECT stores.store_id, store_name, COALESCE(total_order, 0) as total_order FROM stores
LEFT JOIN
(SELECT store_id, SUM(order_id) as total_order FROM orders GROUP BY store_id) AS total_order_tables
ON stores.store_id = total_order_tables.store_id;
```

**19. In ra tên đầy đủ khách hàng, ngày đặt hàng, ngày giao hàng, tên nhân viên bán hàng mà nhân viên có tên là "Boyer"**

```sql
SELECT CONCAT(customers.first_name, ' ', customers.last_name) AS full_name, order_date, shipped_date, CONCAT(staffs.first_name, ' ', staffs.last_name) AS staff_name
FROM customers
LEFT JOIN orders ON customers.customer_id = orders.customer_id
LEFT JOIN staffs ON orders.staff_id = staffs.staff_id
WHERE staffs.last_name = 'Boyer';
```

**20. In ra tên nhãn hàng, mã nhãn hàng và tổng số lượng hàng tồn kho còn lại được sắp xếp giảm dần theo số lượng**

```sql
SELECT brands.brand_id, brand_name, COALESCE(SUM(quantity), 0) AS total_stock
FROM brands
LEFT JOIN products ON brands.brand_id = products.brand_id
LEFT JOIN stocks ON products.product_id = stocks.product_id
GROUP BY brands.brand_id, brand_name
ORDER BY total_stock DESC;
```