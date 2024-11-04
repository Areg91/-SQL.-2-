Домашнее задание к занятию «SQL. Часть 2» Задание Огумцяна Арега 
Инструкция по выполнению домашнего задания
Сделайте fork репозитория c шаблоном решения к себе в Github и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw).
Выполните клонирование этого репозитория к себе на ПК с помощью команды git clone.
Выполните домашнее задание и заполните у себя локально этот файл README.md:
впишите вверху название занятия и ваши фамилию и имя;
в каждом задании добавьте решение в требуемом виде: текст/код/скриншоты/ссылка;
для корректного добавления скриншотов воспользуйтесь инструкцией «Как вставить скриншот в шаблон с решением»;
при оформлении используйте возможности языка разметки md. Коротко об этом можно посмотреть в инструкции по MarkDown.
После завершения работы над домашним заданием сделайте коммит (git commit -m "comment") и отправьте его на Github (git push origin).
Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
Любые вопросы задавайте в чате учебной группы и/или в разделе «Вопросы по заданию» в личном кабинете.
Желаем успехов в выполнении домашнего задания.

Задание можно выполнить как в любом IDE, так и в командной строке.

Задание 1
Одним запросом получите информацию о магазине, в котором обслуживается более 300 покупателей, и выведите в результат следующую информацию:

фамилия и имя сотрудника из этого магазина;
город нахождения магазина;
количество пользователей, закреплённых в этом магазине.

Решение 1
Для выполнения данного задания предполагаем, что у нас есть две таблицы: staff (информация о сотрудниках) и store (информация о магазинах). Также есть связь между этими таблицами через внешний ключ, позволяющий определить, какой сотрудник работает в каком магазине.
Дополнительно у нас может быть таблица customer (данные о клиентах), где также хранится информация о том, какому магазину принадлежит каждый клиент.
### Структура таблиц:
- Таблица staff
  - Поля: id, first_name, last_name, ...
  - Внешний ключ: store_id (связь с таблицей магазинов)

- Таблица store
  - Поля: store_id, city, ...

- Таблица customer
  - Поле: customer_id, ...
  - Внешний ключ: store_id
### Запрос:
Мы будем объединять три таблицы: сотрудников, магазины и клиентов, чтобы получить необходимую информацию. Условием будет то, что магазин обслуживает более 300 клиентов.
SQL
SELECT s.last_name, s.first_name, st.city, COUNT(c.customer_id) AS customer_count
FROM staff s
JOIN store st ON s.store_id = st.store_id
JOIN customer c ON st.store_id = c.store_id
GROUP BY s.staff_id, st.store_id
HAVING COUNT(c.customer_id) > 300;
### Разбор запроса:
1. SELECT: Мы выбираем фамилию и имя сотрудника, город, а также считаем количество клиентов.
2. JOIN: Объединение таблиц staff, store и customer по соответствующим внешним ключам.
3. GROUP BY: Группировка результатов по сотрудникам и магазинам.
4. HAVING: Фильтрация групп, в которых количество клиентов больше 300.
Такой запрос позволит получить требуемую информацию об интересующем нас магазине.

Задание 2
Получите количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

Решение 2
Для выполнения этого задания нам понадобится таблица films (или аналогичная), содержащая информацию о фильмах, включая их продолжительность. Допустим, что поле с продолжительностью фильма называется duration.
Нам нужно вычислить среднюю продолжительность всех фильмов, а затем посчитать количество фильмов, длительность которых превышает среднее значение.
### Шаги решения:
1. Сначала найдем среднюю продолжительность фильмов.
2. Затем выберем фильмы, у которых продолжительность больше среднего значения.
3. Посчитаем количество таких фильмов.
### Запрос:
SQL
WITH average_duration AS (
    SELECT AVG(duration) AS avg_duration
    FROM films
)
SELECT COUNT(*)
FROM films
WHERE duration > (SELECT avg_duration FROM average_duration);
### Разбор запроса:
1. CTE (Common Table Expression) average_duration: Мы создаем временную таблицу, которая хранит среднюю продолжительность всех фильмов.
2. Основной запрос: Сравниваем продолжительность каждого фильма с найденной средней и считаем количество фильмов, которые длиннее среднего значения.
Таким образом, запрос возвращает количество фильмов, продолжительность которых больше средней продолжительности всех фильмов.

Задание 3
Получите информацию, за какой месяц была получена наибольшая сумма платежей, и добавьте информацию по количеству аренд за этот месяц.

Решение 3

WITH monthly_summary AS (
    SELECT 
        DATE_FORMAT(payment_date, '%Y-%m') AS payment_month,
        SUM(amount) AS total_amount,
        COUNT(*) AS rent_count
    FROM payments
    GROUP BY payment_month
)
SELECT 
    payment_month, 
    total_amount, 
    rent_count
FROM monthly_summary
ORDER BY total_amount DESC
LIMIT 1;
