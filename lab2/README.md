Лабораторна робота №2
Тема: Перетворення ER-діаграми на схему PostgreSQL

1. Опис реляційної схеми
На основі розробленої ER-діаграми була створена реляційна схема бази даних для сервісу прокату фільмів. Схема складається з 8 взаємопов'язаних таблиць.

Перелік таблиць та їх призначення:

Category: Довідник жанрів/категорій фільмів.

Movie: Основна інформація про фільми. Пов’язана з категоріями (1:N).

Actor: Дані про акторів.

Movie_Actor: Таблиця зв’язку для реалізації відношення "багато-до-багатьох" між фільмами та акторами.

Client: Реєстр клієнтів сервісу.

Rental: Журнал видачі фільмів в оренду. Пов’язує клієнта та фільм.

Payment: Фіксація платежів за конкретну оренду (1:1).

Review: Відгуки та рейтинги фільмів від клієнтів.

2. SQL DDL-скрипт створення структури
У скрипті реалізовано автоматичну генерацію ключів (SERIAL), обмеження цілісності (NOT NULL, UNIQUE) та логічні перевірки (CHECK).


-------------------SQL
-- СТВОРЕННЯ СТРУКТУРИ БАЗИ ДАНИХ

CREATE TABLE Category (
    category_id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL UNIQUE
);

CREATE TABLE Client (
    client_id SERIAL PRIMARY KEY,
    full_name VARCHAR(255) NOT NULL,
    phone VARCHAR(20),
    email VARCHAR(100) UNIQUE NOT NULL
);

CREATE TABLE Actor (
    actor_id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    birth_date DATE
);

CREATE TABLE Movie (
    movie_id SERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL,
    genre VARCHAR(100),
    release_year INTEGER CHECK (release_year > 1895),
    price NUMERIC(10, 2) NOT NULL CHECK (price >= 0),
    category_id INTEGER REFERENCES Category(category_id) ON DELETE SET NULL
);

CREATE TABLE Movie_Actor (
    movie_id INTEGER REFERENCES Movie(movie_id) ON DELETE CASCADE,
    actor_id INTEGER REFERENCES Actor(actor_id) ON DELETE CASCADE,
    PRIMARY KEY (movie_id, actor_id)
);

CREATE TABLE Rental (
    rental_id SERIAL PRIMARY KEY,
    rental_date DATE NOT NULL DEFAULT CURRENT_DATE,
    return_date DATE,
    movie_id INTEGER NOT NULL REFERENCES Movie(movie_id),
    client_id INTEGER NOT NULL REFERENCES Client(client_id),
    CONSTRAINT check_dates CHECK (return_date >= rental_date OR return_date IS NULL)
);

CREATE TABLE Payment (
    payment_id SERIAL PRIMARY KEY,
    amount NUMERIC(10, 2) NOT NULL CHECK (amount > 0),
    payment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    rental_id INTEGER NOT NULL UNIQUE REFERENCES Rental(rental_id) ON DELETE CASCADE
);

CREATE TABLE Review (
    review_id SERIAL PRIMARY KEY,
    client_id INTEGER NOT NULL REFERENCES Client(client_id),
    movie_id INTEGER NOT NULL REFERENCES Movie(movie_id),
    rating INTEGER CHECK (rating BETWEEN 1 AND 10),
    comment TEXT
);



3. Вставлення тестових даних
Для перевірки працездатності схеми в кожну таблицю вставлено по 3 записи.

-----------------------------------------------SQL
-- ЗАПОВНЕННЯ ТАБЛИЦЬ ДАНИМИ

INSERT INTO Category (name) VALUES ('Екшн'), ('Драма'), ('Комедія');

INSERT INTO Client (full_name, phone, email) VALUES 
('Микола Шпак', '0991234567', 'shpak.m@example.com'),
('Олена Бондар', '0677654321', 'olena.b@example.com'),
('Іван Петренко', '0501112233', 'ivan.p@example.com');

INSERT INTO Actor (name, birth_date) VALUES 
('Крістіан Бейл', '1974-01-30'),
('Леонардо Ді Капріо', '1974-11-11'),
('Меріл Стріп', '1949-06-22');

INSERT INTO Movie (title, genre, release_year, price, category_id) VALUES 
('Темний лицар', 'Бойовик', 2008, 150.00, 1),
('Початок', 'Фантастика', 2010, 120.00, 1),
('Диявол носить Прада', 'Комедія', 2006, 90.00, 3);

INSERT INTO Movie_Actor (movie_id, actor_id) VALUES (1, 1), (2, 2), (3, 3);

INSERT INTO Rental (rental_date, movie_id, client_id) VALUES 
('2026-04-01', 1, 1),
('2026-04-05', 2, 2),
('2026-04-10', 3, 3);

INSERT INTO Payment (amount, rental_id) VALUES (150.00, 1), (120.00, 2), (90.00, 3);

INSERT INTO Review (client_id, movie_id, rating, comment) VALUES 
(1, 1, 10, 'Найкращий фільм про Бетмена!'),
(2, 2, 9, 'Дуже заплутано, але круто.'),
(3, 3, 8, 'Легка та приємна комедія.');

4. Результати тестування
Після виконання скриптів у pgAdmin всі таблиці були успішно створені та заповнені. Цілісність даних підтримується за допомогою зовнішніх ключів.
<img width="919" height="713" alt="Снимок экрана 2026-04-26 115905" src="https://github.com/user-attachments/assets/bc6bbbf4-5a0a-4535-9217-a84618034df9" />


Висновок
У ході лабораторної роботи було перетворено графічну ER-модель у реляційну схему бази даних PostgreSQL. Було визначено типи даних, первинні та зовнішні ключі, а також накладено обмеження для забезпечення логічної цілісності бази. Тестування шляхом вставки та вибірки даних підтвердило правильність проектування архітектури.
