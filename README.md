# yandex-praktikum-kafka-4

## Описание работы проекта
Данный проект имеет настроенный Debezium Connector для передачи данных из базы данных PostgreSQL в Apache Kafka с использованием механизма Change Data Capture (CDC).
Также в проект добавлены компоненты сбора и анализа метрик (Prometheus для сбора метрик из Kafka Connect + Grafana)

## Описание работы компонентов
### [kafka-connect](kafka-connect)
Компонент, который является связующим звеном между Kafka и внешними системами. В данном случае забирает данные из Postgresql и отправляет в соответствующие Кафка топики.

### [confluent-hub-components](confluent-hub-components)
Папка с добавленными плагинами kafka-connect. В данном проекте добавлен только debezium-connector-postgres для интеграции Postgresql с Debezium.

### [postgres](postgres)
База данных, сконфигурированная с включенной логической репликацией (wal_level = 'logical') для возможности использования CDC с Debezium.

### [consumer-app](consumer-app)
Java приложение, которое содержит в себе два консьюмера для обработки сообщений, пришедших в топики.

### [prometheus](prometheus)
Компонент, который использует pull модель для сбора и хранения метрик.

### [grafana](grafana)
Компонент, предназначенный для визуализации метрик из Prometheus

## Проверка работоспособности (инструкция по тестированию)

1. Необходимо из корневой директории выполнить следующую команду:
```bash
docker-compose up -d
```

2. Далее подключаемся к БД
```bash
docker exec -it postgres psql -h 127.0.0.1 -U postgres-user -d orders
```

3. Выполняем SQL запросы на создание таблиц
```sql
CREATE TABLE users (id SERIAL PRIMARY KEY, name VARCHAR(100), email VARCHAR(100), created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
```
```sql
CREATE TABLE orders (id SERIAL PRIMARY KEY, user_id INT REFERENCES users(id), product_name VARCHAR(100), quantity INT, order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP);
```

4. Загружаем конфигурацию Debezium коннектора (`| jq` в команде можно не использовать)
```bash
curl -X POST -H 'Content-Type: application/json' --data @connector.json http://localhost:8083/connectors | jq
```

5. Далее открываем логи контейнера consumer-app

6. Выполняем SQL запросы на вставку данных в таблицы БД
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

7. Убеждаемся, что соответствующие сообщения о вставке в таблицы получены в логах consumer-app (или можно открыть Kafka UI: http://localhost:8080 и посмотреть результат там)

8. Открываем http://localhost:3000/

9. Переходим в Kafka Connect Overview-0 dashboard

10. Видим статистику по запущенным таскам.