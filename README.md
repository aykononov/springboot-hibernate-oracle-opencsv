<small>

## Использование базы данных Oracle в веб-приложении Spring Boot через Hibernate

Maven + Spring + Hibernate + Oracle -> CRUD Microservice

## Use a Oracle database in a Spring Boot Web Application through Hibernate

Прооэкт в стадии разработки ...

### Применение

- Запустите приложение и перейдите по адресу http://localhost:8081/ 

    *(порт 8080 занимает Oracle Apex)*

- В POSTMAN используйте следующие URL-адреса для вызова методов контроллеров и просмотра взаимодействия с базой данных:
    * POST `http://localhost:8081/addProducts`: добавить продукты 
        JSON контент:
        `[{"name":"phone"},
         {"name":"battery"},
         {"name":"case"}]`
         
    * GET `http://localhost:8081/products`: получить все продукты
    * DELETE `http://localhost:8081/delete/1`: удалить по индексу 1
      
<details><summary>Задача ...</summary>

>Реализовать приложение на базе фреймворка Spring Boot 2 и Java минимум 8 версии. 
>
>```
>Пример структуры таблиц в БД Oracle.
>1. Таблица товар. Хранит название товара.
>   Колонки: id, name.
>2. Таблица цена товара. Хранит цену на товар и дату с которой цена актуальная. 
>   По каждому товару может быть несколько цен с разными датами.
>   Колонки: id, price, date, product_id.
>
>Таблицы должны создаваться автоматически в БД при старте приложения или в приложении 
>должен быть приложен файл со скриптом создания необходимых сущностей.
>
>Функционал приложения.
>1. Загрузка товаров и цен из csv-файла.
>   Приложение должно уметь загружать данные из csv-файла. 
>   Путь директории с файлами настраивается в конфигурационном файле приложения. 
>   Пример формат данных csv-файла:
>       product_id;product_name;price_id;price;price_date
>   В логах должен быть отмечен факт старта обработки файла и результат обработки файла 
>   с количеством обработанных записей(товаров и цен).
>   Загрузка файла стартует при появлении файла в указанной директории.
>
>2. Получение списка товаров и актуальных цен.
>   Приложение должно предоставлять Rest метод, возвращающий из БД список товаров с актуальными ценами, 
>   на указанный в запросе день.
>       GET /products?date=yyyy-mm-dd 
>   Формат данных ответа - json. Список {"name": "Товар 1", "price": 100.99} 
>
>3. Получение статистики.
>   Приложение должно предоставлять Rest метод, возвращающий статистику по загруженным товарам и ценам.
>       GET /products/statistic 
>   Формат данных ответа - json.
>   Параметры статистики:
>   - Количество товаров в БД. Формат - просто цифра.
>   - Как часто менялась цена товара. Группировка по товарам. Формат - список {"name": "Товар 1", "frequency": 2} 
>   - Как часто менялась цена товара. Группировка по дням. Формат - список {"date": "yyyy-mm-dd", "frequency": 6} 
>   Каждый параметр статистики необходимо запрашивать в отдельном параллельном потоке.
>```
</details></small>

<details><summary>Скрипты структуры БД ...</summary>

>```sql
>DROP TABLE products PURGE;
>/
>CREATE TABLE products
>(
>  id INT NOT NULL,
>  name VARCHAR2(128) NOT NULL,
>	PRIMARY KEY (id)
>);
>/
>DROP TABLE prices PURGE;
>/
>CREATE TABLE prices
>(
>  id INT NOT NULL,
>  price VARCHAR2(128) NOT NULL,
>  dates DATE DEFAULT SYSDATE,
>  PRIMARY KEY (id)
>);
>/
> DROP SEQUENCE id_seq;
>/
>CREATE SEQUENCE id_seq
> MINVALUE 1
> START WITH 1
> INCREMENT BY 1;
> /
> DROP TRIGGER products_id_bri;
> /
> CREATE OR REPLACE TRIGGER products_id_bri
> BEFORE INSERT ON products
> REFERENCING NEW AS NEW OLD AS OLD
> FOR EACH ROW
> WHEN (new.id IS NULL)
> BEGIN
> SELECT id_seq.nextval
> INTO :new.id
> FROM dual;
> END;
> /
> INSERT ALL
> INTO products(NAME) VALUES('phone')
> INTO products(NAME) VALUES('battery')
> INTO products(NAME) VALUES('case')
> SELECT *
> FROM dual;
> /
> -- проверка
> SELECT * FROM products;
>```
</details></small>