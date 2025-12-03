# yandex-praktikum-kafka-4
yandex-praktikum-kafka-4

curl -X POST -H 'Content-Type: application/json' --data @connector.json http://localhost:8083/connectors | jq

Kafka UI: http://localhost:8080

```bash
docker-compose up -d
```

```bash
docker exec -it postgres psql -h 127.0.0.1 -U postgres-user -d orders
```
```sql
CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(100), email VARCHAR(100), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
```

```sql
CREATE TABLE orders (id SERIAL PRIMARY KEY, user_id INT REFERENCES users(id), product_name VARCHAR(100), quantity INT, order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
```

```bash
curl -X POST -H 'Content-Type: application/json' --data @connector.json http://localhost:8083/connectors | jq
```

```sql
-- Добавление пользователей
INSERT INTO users (name, email) VALUES ('John Doe', 'john@example.com');
INSERT INTO users (name, email) VALUES ('Jane Smith', 'jane@example.com');
INSERT INTO users (name, email) VALUES ('Alice Johnson', 'alice@example.com');
INSERT INTO users (name, email) VALUES ('Bob Brown', 'bob@example.com');


-- Добавление заказов
INSERT INTO orders (user_id, product_name, quantity) VALUES (1, 'Product A', 2);
INSERT INTO orders (user_id, product_name, quantity) VALUES (1, 'Product B', 1);
INSERT INTO orders (user_id, product_name, quantity) VALUES (2, 'Product C', 5);
INSERT INTO orders (user_id, product_name, quantity) VALUES (3, 'Product D', 3);
INSERT INTO orders (user_id, product_name, quantity) VALUES (4, 'Product E', 4); 
```

orders.public.users
orders.public.orders