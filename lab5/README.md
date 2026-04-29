Лабораторна робота №5: Нормалізація бази даних системи кінопрокату
Мета: Приведення схеми БД до третьої нормальної форми (3NF) для усунення аномалій вставки, оновлення та видалення.

1. Аналіз початкової схеми (Проблеми)


Надлишковість: Жанри та категорії могли дублюватися текстом у таблиці фільмів.
Транзитивність: Дані про акторів могли бути надто тісно пов'язані з таблицею прокату.

2. Функціональні залежності (ФЗ)
movie_id → {title, release_year, price, category_id}

client_id → {full_name, phone, email}

category_id → {name}

rental_id → {client_id, movie_id, rental_date, price}

3. Процес нормалізації
1NF (Перша нормальна форма):
Усі атрибути є атомарними. Ми відмовилися від ідеї зберігати список акторів у полі таблиці Movie. Замість цього створено таблицю Movie_Actor.

2NF (Друга нормальна форма):
Оскільки таблиця Movie_Actor має складений ключ {movie_id, actor_id}, ми переконалися, що в цій таблиці немає неключових атрибутів, які залежать лише від частини ключа (наприклад, імені актора). Всі такі дані винесені в таблицю Actor.

3NF (Третя нормальна форма):
Ми усунули транзитивні залежності. Назва категорії тепер залежить від category_id, а не напряму від movie_id. Це дозволяє змінювати назву жанру в одному місці (таблиця Category), не чіпаючи сотні записів у таблиці Movie.

2.  SQL-скрипт (schema_3nf.sql)

DROP TABLE IF EXISTS Rental, Movie_Actor, Movie, Actor, Client, Category CASCADE;


CREATE TABLE Category (
    category_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE Actor (
    actor_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE Client (
    client_id SERIAL PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    email VARCHAR(100)
);

CREATE TABLE Movie (
    movie_id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    release_year INTEGER,
    price NUMERIC(10, 2),
    category_id INTEGER REFERENCES Category(category_id) ON DELETE SET NULL
);

CREATE TABLE Movie_Actor (
    movie_id INTEGER REFERENCES Movie(movie_id) ON DELETE CASCADE,
    actor_id INTEGER REFERENCES Actor(actor_id) ON DELETE CASCADE,
    PRIMARY KEY (movie_id, actor_id)
);

CREATE TABLE Rental (
    rental_id SERIAL PRIMARY KEY,
    client_id INTEGER REFERENCES Client(client_id) ON DELETE CASCADE,
    movie_id INTEGER REFERENCES Movie(movie_id) ON DELETE CASCADE,
    rental_date DATE DEFAULT CURRENT_DATE,
    price NUMERIC(10, 2)
);


3. Візуалізація схеми (ER-діаграма)
Після виконання SQL-скрипту була згенерована оновлена ER-діаграма, яка відображає структуру в 3NF. На схемі чітко видно розділення сутностей та зв'язки між ними, що мінімізує дублювання даних.

<img width="1275" height="800" alt="image" src="https://github.com/user-attachments/assets/3ff6d3e3-4bae-42dd-a043-aa49b1c96863" />



4. Тестування та перевірка цілісності
Для перевірки працездатності нормалізованої схеми було виконано контрольне заповнення даними та проведено аналітичні запити. Це підтвердило, що аномалії, які виникали раніше (наприклад, неіснуючі стовпці total_price), були усунені шляхом чіткого іменування атрибутів відповідно до нової схеми.

Приклад контрольного запиту (Аналіз доходів по клієнтах):

SQL
SELECT cl.full_name, SUM(r.price) AS total_spent
FROM Client cl
LEFT JOIN Rental r ON cl.client_id = r.client_id
GROUP BY cl.full_name;
Результат виконання запиту підтверджує коректність роботи зв'язків та агрегатних функцій у новій структурі.
<img width="853" height="640" alt="image" src="https://github.com/user-attachments/assets/1f391ae0-e2a3-408b-8403-537c9e38fcd5" />

5. Аналіз усунення аномалій
Завдяки переходу до 3NF було досягнуто:

Усунення аномалії оновлення: Якщо назва категорії "Фантастика" зміниться на "Наукова фантастика", нам потрібно змінити лише один рядок у таблиці Category, а не редагувати сотні записів у таблиці Movie.

Усунення аномалії вставки: Ми можемо додати нову категорію або нового актора в базу ще до того, як з'явиться перший фільм із ними.

Усунення аномалії видалення: Видалення запису про оренду не призводить до втрати інформації про клієнта або фільм.

Висновки
Під час виконання лабораторної роботи було проведено повну реорганізацію бази даних системи кінопрокату. Початкова схема була проаналізована на предмет функціональних залежностей та приведена до третьої нормальної форми (3NF).

Це дозволило:

Оптимізувати використання пам'яті за рахунок усунення дублювання текстових даних.

Забезпечити високий рівень цілісності даних завдяки використанню зовнішніх ключів (FOREIGN KEY).

Спростити подальшу підтримку та масштабування системи.

Виправити логічні помилки в структурі, що раніше призводили до збоїв у SQL-запитах.
