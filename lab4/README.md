Лабораторна робота №4: Аналітичні SQL-запити (OLAP)
Мета роботи: Навчитися використовувати агрегатні функції, групування даних, різні типи об'єднань таблиць та підзапити для аналізу даних у PostgreSQL.

1. Агрегаційні функції та групування (GROUP BY, HAVING)


Запит 1.1: Загальна кількість фільмів та середня вартість

SQL
SELECT COUNT(*) AS total_movies, AVG(price) AS average_price 
FROM Movie;
 Визначає загальний обсяг медіатеки та середню ціну за одиницю контенту.
<img width="873" height="564" alt="image" src="https://github.com/user-attachments/assets/e38d7ca5-c29b-47a2-af60-0c46a1e90136" />

Запит 1.2: Кількість фільмів у кожному жанрі

SQL
SELECT genre, COUNT(*) AS movies_count 
FROM Movie 
GROUP BY genre;
 Групує фільми за жанрами для аналізу наповненості кожної категорії.
<img width="838" height="603" alt="image" src="https://github.com/user-attachments/assets/f72346e1-47c2-467d-af64-4c2b2b208ee1" />

Запит 1.3: Загальна сума витрат кожного клієнта

SQL
SELECT client_id, SUM(total_price) AS total_spent 
FROM Rental 
GROUP BY client_id;
 Обчислює сумарний дохід, отриманий від кожного окремого клієнта.
<img width="799" height="532" alt="image" src="https://github.com/user-attachments/assets/9170e6a5-f72b-4929-913f-621f36c16a1f" />

Запит 1.4: Пошук популярних жанрів (фільтрація груп)

SQL
SELECT genre, AVG(price) AS avg_genre_price 
FROM Movie 
GROUP BY genre 
HAVING AVG(price) > 100;
 Виводить лише ті жанри, середня вартість фільмів у яких перевищує 100 одиниць.
<img width="810" height="579" alt="image" src="https://github.com/user-attachments/assets/00943a56-5f28-4b4d-adee-2b47df80523a" />

2. Об'єднання таблиць (JOIN)
Використовується для збирання інформації, що зберігається в різних пов'язаних таблицях.

Запит 2.1: Фільми та їх категорії (INNER JOIN)

SQL
SELECT m.title, c.name AS category_name 
FROM Movie m 
INNER JOIN Category c ON m.category_id = c.category_id;
 Об'єднує назви фільмів із назвами їхніх категорій для зручного перегляду.
<img width="862" height="590" alt="image" src="https://github.com/user-attachments/assets/b368f30a-0422-47e1-8cff-2285bf791532" />


Запит 2.2: Аналіз активності клієнтів (LEFT JOIN)

SQL
SELECT cl.full_name, r.rental_date, r.total_price 
FROM Client cl 
LEFT JOIN Rental r ON cl.client_id = r.client_id;
 Виводить усіх клієнтів, включаючи тих, хто ще не здійснював оренду (у них будуть значення NULL), що важливо для маркетингового аналізу.
<img width="774" height="572" alt="image" src="https://github.com/user-attachments/assets/8317c56b-e14c-4c74-83f5-03612f184caf" />

Запит 2.3: Зв'язок акторів із фільмами (Багатотабличний JOIN)

SQL
SELECT a.name AS actor_name, m.title AS movie_title 
FROM Actor a 
JOIN Movie_Actor ma ON a.actor_id = ma.actor_id 
JOIN Movie m ON ma.movie_id = m.movie_id;
 Використовує сполучну таблицю Movie_Actor для відображення повного списку акторів та їхніх ролей у фільмах.
<img width="851" height="502" alt="image" src="https://github.com/user-attachments/assets/97ffae40-8ed8-4573-8f66-4041fe8b00b7" />

3. Використання підзапитів
Дозволяє виконувати складнішу фільтрацію на основі результатів інших запитів.

Запит 3.1: Фільми з ціною вище середньої

SQL
SELECT title, price 
FROM Movie 
WHERE price > (SELECT AVG(price) FROM Movie);
 Динамічно знаходить фільми, вартість яких перевищує середній показник по всій базі.
<img width="787" height="577" alt="image" src="https://github.com/user-attachments/assets/e9285bc5-9409-4e01-9a3e-001964137432" />

Запит 3.2: Список активних клієнтів

SQL
SELECT full_name 
FROM Client 
WHERE client_id IN (SELECT DISTINCT client_id FROM Rental);
 Відбирає лише тих користувачів, які здійснили хоча б одну транзакцію в системі.
<img width="756" height="548" alt="image" src="https://github.com/user-attachments/assets/aa3ad9f7-6f06-4176-995a-b35fdd7c423d" />


Запит 3.3: Кількість акторів у кожному фільмі (Скалярний підзапит)

SQL
SELECT title, 
       (SELECT COUNT(*) FROM Movie_Actor ma WHERE ma.movie_id = m.movie_id) AS actors_count 
FROM Movie m;
 Для кожного фільму в основному списку підраховує кількість задіяних акторів через підзапит до таблиці зв'язків.
<img width="1047" height="663" alt="image" src="https://github.com/user-attachments/assets/062a5798-6d5a-4c8d-aaec-abec694c67e8" />

Висновок: У ході роботи було реалізовано аналітичні запити, які дозволяють проводити OLAP-аналіз бази даних. Було перевірено коректність роботи агрегатних функцій, механізмів об'єднання таблиць (INNER/LEFT JOIN) та складних підзапитів. Отримані результати підтверджують цілісність даних та правильність структури бази, розробленої в попередніх лабораторних роботах.
