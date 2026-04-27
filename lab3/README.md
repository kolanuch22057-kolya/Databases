 Лабораторна робота №3
Тема: Маніпулювання даними SQL (OLTP)

1. Мета роботи
Навчитися виконувати основні операції маніпулювання даними (DML) у середовищі PostgreSQL, включаючи вибірку, додавання, оновлення та видалення записів, а також перевірити цілісність посилань у створеній базі даних.

2. Виконання запитів SELECT (Отримання даних)
Запит 2.1: Вибрати назви та ціни фільмів жанру "Бойовик", ціна яких перевищує 100 грн.

SQL-код:

SQL
SELECT title, price FROM Movie 
WHERE genre = 'Бойовик' AND price > 100;
Мета: Фільтрація контенту за жанром та ціновою категорією.

Очікуваний результат: Список дорогих бойовиків (наприклад, "Темний лицар").
<img width="1005" height="664" alt="image" src="https://github.com/user-attachments/assets/8eacbce0-095d-42ba-8aa7-6c92da63dff4" />



Запит 2.2: Отримати імена акторів, які народилися після 1 січня 1974 року.

SQL-код:

SQL
SELECT name, birth_date FROM Actor 
WHERE birth_date > '1974-01-01';
Результат: Перевірка коректності роботи з типами даних DATE.
<img width="908" height="719" alt="image" src="https://github.com/user-attachments/assets/15a4c78d-6053-4d88-8dad-cec0c29ca573" />


3. Виконання запитів INSERT (Додавання даних)
Запит 3.1: Реєстрація нового клієнта в системі.

SQL-код:

SQL
INSERT INTO Client (full_name, phone, email) 
VALUES ('Андрій Шевченко', '0951112233', 'sheva.a@ukr.net');
<img width="769" height="212" alt="image" src="https://github.com/user-attachments/assets/8e75b81f-a7c8-4c7a-b532-8edc74f5b28a" />



Запит 3.2: Додавання нового фільму "Інтерстеллар".

SQL-код:

SQL
INSERT INTO Movie (title, genre, release_year, price, category_id) 
VALUES ('Інтерстеллар', 'Наукова фантастика', 2014, 130.00, 1);
(Тут додайте скріншот таблиці після виконання SELECT * FROM Movie;, де видно новий фільм)
<img width="973" height="246" alt="image" src="https://github.com/user-attachments/assets/15bd9fcc-d780-4991-aa4f-e32a6003f6d6" />


4. Виконання запитів UPDATE (Оновлення даних)
Запит 4.1: Зміна контактних даних клієнта (Олена Бондар змінила номер телефону).

SQL-код:

SQL
UPDATE Client 
SET phone = '0681112233' 
WHERE full_name = 'Олена Бондар';
<img width="792" height="210" alt="image" src="https://github.com/user-attachments/assets/a2ac9ca3-5228-43d0-b7a1-80aa3ede4476" />



Запит 4.2: Проведення акції (зниження ціни на 10%) для фільму "Початок".

SQL-код:

SQL
UPDATE Movie 
SET price = price * 0.9 
WHERE title = 'Початок';
(Тут додайте скріншот повідомлення "Update 1" та результат SELECT з новою ціною)
<img width="741" height="217" alt="image" src="https://github.com/user-attachments/assets/00e1dbbf-0f7a-4e5c-a80e-03c67021df76" />



5. Виконання запитів DELETE (Видалення даних)
Запит 5.1: Видалення відгуку клієнта.

SQL-код:

SQL
DELETE FROM Review 
WHERE client_id = 1 AND movie_id = 1;
<img width="770" height="213" alt="image" src="https://github.com/user-attachments/assets/1300694e-03b6-428e-8302-019b9c78eb9d" />


Запит 5.2: Видалення фільму "Диявол носить Прада", який вилучено з прокату.

SQL-код:

SQL
DELETE FROM Movie 
WHERE title = 'Диявол носить Прада';
Завдяки налаштуванню ON DELETE CASCADE, усі пов'язані записи в таблиці Movie_Actor були видалені автоматично, що підтверджує цілісність бази.

<img width="1072" height="202" alt="image" src="https://github.com/user-attachments/assets/97644b6b-04f0-4474-8f80-1f6cfdf1eefe" />

Висновок
У ході роботи було відпрацьовано основні SQL-команди для маніпулювання даними. Було перевірено роботу обмежень CHECK та FOREIGN KEY. Всі запити були виконані успішно, дані в таблицях оновлювалися та видалялися згідно з заданими умовами WHERE, що підтверджує правильність логіки спроектованої бази даних.
