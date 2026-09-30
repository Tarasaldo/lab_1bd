# Лабораторна робота 1. Робота з СУБД PostgreSQL та основи SQL

## Загальна інформація

**Здобувач освіти:** [Ваше ПІБ]
**Група:** [Номер групи]
**Обраний рівень складності:** 3

## Виконання завдань

### Список таблиць

```sql
-- Запит для отримання списку таблиць
SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public'
ORDER BY table_name;
```

Результат: У базі даних створено 8 основних таблиць (categories, customers, employees, order_items, orders, products, regions, suppliers) та 4 представлення (views).

Скріншот: `![](screenshots/00-tables.png)`

### Структура таблиць

```sql
-- Стовпці та типи даних усіх таблиць
SELECT table_name, column_name, data_type, is_nullable
FROM information_schema.columns
WHERE table_schema = 'public'
ORDER BY table_name, ordinal_position;
```

Результат: Отримано структуру всіх таблиць. Ключові для роботи стовпці: `customers` (contact_name, city, customer_type, company_name), `products` (product_name, unit_price, units_in_stock, category_id, discontinued), `orders` (order_date, shipped_date, order_status), `employees` (title, reports_to).

Скріншот: `![](screenshots/00-columns.png)`

---

# РІВЕНЬ 1

### 1.1 Отримати всі записи з таблиці customers

```sql
SELECT * FROM customers;
```

Результат: Отримано 15 записів клієнтів: фізичні особи (`individual`) та юридичні особи (`company`) з 5 міст України.

Скріншот: `![](screenshots/1-1.png)`

### 1.2 Назви товарів і їхні ціни

```sql
SELECT product_name, unit_price FROM products;
```

Результат: Отримано лише два стовпці для всіх товарів каталогу.

Скріншот: `![](screenshots/1-2.png)`

### 1.3 Контактні дані співробітників

```sql
SELECT first_name, last_name, phone, email FROM employees;
```

Результат: Отримано контактні дані 8 співробітників.

Скріншот: `![](screenshots/1-3.png)`

### 2.1 Клієнти з міста Київ

```sql
SELECT * FROM customers WHERE city = 'Київ';
```

Результат: Знайдено 4 клієнти з Києва.

Скріншот: `![](screenshots/2-1.png)`

### 2.2 Товари дорожчі за 25000 грн

```sql
SELECT product_name, unit_price
FROM products
WHERE unit_price > 25000;
```

Результат: Знайдено 13 товарів (смартфони-флагмани, ноутбуки, телевізори, холодильник, планшет).

Скріншот: `![](screenshots/2-2.png)`

### 2.3 Замовлення зі статусом 'delivered'

```sql
SELECT * FROM orders WHERE order_status = 'delivered';
```

Результат: Знайдено 26 доставлених замовлень.

Скріншот: `![](screenshots/2-3.png)`

### 2.4 Співробітники відділу продажів

```sql
-- посада міститься у стовпці title; ILIKE ігнорує регістр
SELECT first_name, last_name, title
FROM employees
WHERE title ILIKE '%продаж%';
```

Результат: Знайдено 3 менеджерів з продажу (Коваленко, Мельник, Гриценко).

Скріншот: `![](screenshots/2-4.png)`

### 3.1 Товари за зростанням ціни

```sql
SELECT product_name, unit_price
FROM products
ORDER BY unit_price ASC;
```

Результат: Найдешевший товар - USB-C кабель (699 грн), найдорожчий - LG OLED (62999 грн).

Скріншот: `![](screenshots/3-1.png)`

### 3.2 Клієнти за алфавітом

```sql
SELECT contact_name, city
FROM customers
ORDER BY contact_name;
```

Результат: Клієнти впорядковані за прізвищем (contact_name починається з прізвища).

Скріншот: `![](screenshots/3-2.png)`

### 3.3 Замовлення від найновіших до найстаріших

```sql
SELECT order_id, customer_id, order_date, order_status
FROM orders
ORDER BY order_date DESC;
```

Результат: Першим іде замовлення від 2024-08-20.

Скріншот: `![](screenshots/3-3.png)`

### 4.1 Топ-10 найдорожчих товарів

```sql
SELECT product_name, unit_price
FROM products
ORDER BY unit_price DESC
LIMIT 10;
```

Результат: Отримано 10 найдорожчих товарів.

Скріншот: `![](screenshots/4-1.png)`

### 4.2 П'ять останніх замовлень

```sql
SELECT order_id, order_date, order_status
FROM orders
ORDER BY order_date DESC
LIMIT 5;
```

Результат: Отримано 5 замовлень серпня 2024 року.

Скріншот: `![](screenshots/4-2.png)`

### 4.3 Перші 8 клієнтів за алфавітом

```sql
SELECT contact_name, city
FROM customers
ORDER BY contact_name
LIMIT 8;
```

Результат: Отримано 8 клієнтів.

Скріншот: `![](screenshots/4-3.png)`

---

# РІВЕНЬ 2

### 5.1 Клієнти, імена яких починаються на "Іван"

```sql
SELECT * FROM customers WHERE contact_name LIKE 'Іван%';
```

Результат: Знайдено 1 запис - Іванова Марія Сергіївна. Примітка: у БД `contact_name` зберігається у форматі «Прізвище Ім'я По батькові», тому шаблон `'Іван%'` шукає за початком прізвища.

Скріншот: `![](screenshots/5-1.png)`

### 5.2 Товари зі словом "phone" або "телефон"

```sql
SELECT product_name, unit_price
FROM products
WHERE product_name ILIKE '%phone%' OR product_name ILIKE '%телефон%';
```

Результат: Знайдено iPhone 15 (слово "телефон" у назвах не зустрічається).

Скріншот: `![](screenshots/5-2.png)`

### 5.3 Власні запити з LIKE

```sql
-- L1. ПОЧАТОК: товари, назва яких починається з "Samsung".
-- Бізнес-логіка: менеджер хоче швидко переглянути лінійку одного бренду.
SELECT product_name, unit_price
FROM products
WHERE product_name LIKE 'Samsung%';
```

```sql
-- L2. КІНЕЦЬ: клієнти, чиє по батькові закінчується на "ович" (чоловіки).
-- Бізнес-логіка: сегментація за статтю для персоналізованих розсилок.
SELECT contact_name, city
FROM customers
WHERE contact_name LIKE '%ович';
```

```sql
-- L3. МІСТИТЬ: клієнти з поштою на Gmail.
-- Бізнес-логіка: аналіз популярності поштових сервісів серед покупців.
SELECT contact_name, email
FROM customers
WHERE email LIKE '%@gmail.com';
```

Результат: L1 - 3 товари Samsung; L2 - клієнти-чоловіки; L3 - клієнти з Gmail.

Скріншот: `![](screenshots/5-3.png)`

### 6.1 Товари дорожчі за 15000 і дешевші за 50000

```sql
SELECT product_name, unit_price
FROM products
WHERE unit_price > 15000 AND unit_price < 50000
ORDER BY unit_price;
```

Результат: Отримано товари середнього та вищого середнього цінового діапазону.

Скріншот: `![](screenshots/6-1.png)`

### 6.2 Клієнти з Києва або Львова, які є юридичними особами

```sql
SELECT contact_name, company_name, city
FROM customers
WHERE (city = 'Київ' OR city = 'Львів')
  AND customer_type = 'company';
```

Результат: Знайдено 3 компанії, усі з Києва (у Львові юридичних осіб у БД немає).

Скріншот: `![](screenshots/6-2.png)`

### 6.3 Власні запити з AND / OR / NOT

```sql
-- A1. Товари, що є в наявності і не зняті з виробництва.
-- Бізнес-логіка: каталог для сайту має показувати лише доступні позиції.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE units_in_stock > 0 AND NOT discontinued;
```

```sql
-- A2. Товари до 30000 грн, яких залишилось менше 10 штук.
-- Бізнес-логіка: кандидати на термінове поповнення складу.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE unit_price < 30000 AND units_in_stock < 10
ORDER BY units_in_stock;
```

```sql
-- A3. Фізичні особи не з Києва.
-- Бізнес-логіка: регіональна розсилка для роздрібних покупців.
SELECT contact_name, city
FROM customers
WHERE customer_type = 'individual' AND city <> 'Київ';
```

```sql
-- A4. Замовлення, які ще не завершені (ні доставлені, ні скасовані).
-- Бізнес-логіка: "активні" замовлення, за якими потрібен контроль менеджера.
SELECT order_id, order_date, order_status
FROM orders
WHERE NOT (order_status = 'delivered' OR order_status = 'cancelled');
```

Результат: A1 - усі активні товари; A2 - OnePlus 12, Samsung холодильник, PlayStation 5, Xbox; A3 - роздрібні клієнти регіонів; A4 - 5 замовлень (pending, processing, shipped).

Скріншот: `![](screenshots/6-3.png)`

### 7.1 Клієнти з Києва, Харкова, Одеси, Дніпра

```sql
SELECT contact_name, city
FROM customers
WHERE city IN ('Київ', 'Харків', 'Одеса', 'Дніпро')
ORDER BY city;
```

Результат: Знайдено 12 клієнтів (3 клієнти зі Львова відсіяні).

Скріншот: `![](screenshots/7-1.png)`

### 7.2 Товари в діапазоні від 10000 до 30000 грн

```sql
SELECT product_name, unit_price
FROM products
WHERE unit_price BETWEEN 10000 AND 30000;
```

Результат: Отримано товари з ціною від 10000 до 30000 включно (межі входять).

Скріншот: `![](screenshots/7-2.png)`

### 7.3 Власні запити з IN, BETWEEN, IS NULL

```sql
-- IN-1. Товари категорій "Смартфони", "Ноутбуки", "Телевізори".
-- Бізнес-логіка: огляд ключових категорій для квартального звіту.
SELECT product_name, category_id
FROM products
WHERE category_id IN (1, 2, 3);
```

```sql
-- IN-2. Замовлення, що зараз перебувають в обробці або в дорозі.
-- Бізнес-логіка: замовлення, які ще не дійшли до клієнта.
SELECT order_id, order_status
FROM orders
WHERE order_status IN ('processing', 'shipped');
```

```sql
-- BETWEEN-1. Замовлення за перший квартал 2024 року.
-- Бізнес-логіка: квартальна звітність.
SELECT order_id, order_date
FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-03-31';
```

```sql
-- BETWEEN-2. Бюджетні товари від 1000 до 5000 грн.
-- Бізнес-логіка: добірка для акції "все до 5 тисяч".
SELECT product_name, unit_price
FROM products
WHERE unit_price BETWEEN 1000 AND 5000;
```

```sql
-- IS NULL. Замовлення, які ще не відправлені.
-- Бізнес-логіка: черга на відправку для складу.
SELECT order_id, order_date, order_status
FROM orders
WHERE shipped_date IS NULL;
```

```sql
-- IS NOT NULL. Клієнти-компанії (є назва компанії).
-- Бізнес-логіка: база B2B-клієнтів для відділу корпоративних продажів.
SELECT company_name, contact_name, contact_title
FROM customers
WHERE company_name IS NOT NULL;
```

Результат: IS NULL повертає 4 невідправлені замовлення; IS NOT NULL - 6 компаній.

Скріншот: `![](screenshots/7-3.png)`

### 8. Комбінування умов (5 запитів)

```sql
-- C1. LIKE + OR + BETWEEN: Samsung або iPhone у діапазоні 20000-60000.
-- Бізнес-логіка: флагманські смартфони двох брендів для банерної реклами.
SELECT product_name, unit_price
FROM products
WHERE (product_name LIKE '%Samsung%' OR product_name LIKE '%iPhone%')
  AND unit_price BETWEEN 20000 AND 60000;
```

```sql
-- C2. IN + IS NULL + BETWEEN: фізособи з великих міст, зареєстровані у Q1 2023.
-- Бізнес-логіка: "перші клієнти" магазину для програми лояльності.
SELECT contact_name, city, registration_date
FROM customers
WHERE city IN ('Київ', 'Харків', 'Одеса')
  AND company_name IS NULL
  AND registration_date BETWEEN '2023-01-01' AND '2023-03-31';
```

```sql
-- C3. Ноутбуки (категорія 2) до 50000 грн, що згадуються як "ноутбук" в описі.
-- Бізнес-логіка: підбір ноутбука за бюджетом для консультанта.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE category_id = 2
  AND unit_price BETWEEN 25000 AND 50000
  AND description ILIKE '%ноутбук%'
  AND units_in_stock > 0;
```

```sql
-- C4. LIKE + NOT IN: клієнти з прізвищем на "М", не з Києва.
-- Бізнес-логіка: сегмент для регіональної кампанії.
SELECT contact_name, city
FROM customers
WHERE contact_name LIKE 'М%'
  AND city NOT IN ('Київ');
```

```sql
-- C5. BETWEEN + IS NULL: замовлення 2024 року, які досі не відправлені.
-- Бізнес-логіка: виявлення проблемних (прострочених) замовлень.
SELECT order_id, order_date, order_status
FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31'
  AND shipped_date IS NULL;
```

Результат: Кожен запит поєднує щонайменше два різні типи умов і повертає змістовну вибірку для бізнес-задачі.

Скріншот: `![](screenshots/8.png)`

### 9. Складне сортування та пагінація

```sql
-- S1. Категорія за зростанням, у межах категорії ціна за спаданням.
-- Бізнес-логіка: каталог, де в кожній категорії дорогі товари зверху.
SELECT category_id, product_name, unit_price
FROM products
ORDER BY category_id ASC, unit_price DESC;
```

```sql
-- S2. Клієнти: тип, місто, ім'я.
-- Бізнес-логіка: зручний реєстр для відділу продажів.
SELECT customer_type, city, contact_name
FROM customers
ORDER BY customer_type, city, contact_name;
```

```sql
-- S3. Товари: спочатку найменший залишок, потім за назвою.
-- Бізнес-логіка: список для складу - що закінчується першим.
SELECT product_name, units_in_stock
FROM products
ORDER BY units_in_stock ASC, product_name ASC;
```

```sql
-- P1. Пагінація товарів, сторінка 2 (розмір 10): OFFSET = (2-1)*10 = 10.
SELECT product_name, unit_price
FROM products
ORDER BY product_name
LIMIT 10 OFFSET 10;
```

```sql
-- P2. Пагінація клієнтів, сторінка 3 (розмір 5): OFFSET = (3-1)*5 = 10.
SELECT contact_name, city
FROM customers
ORDER BY contact_name
LIMIT 5 OFFSET 10;
```

Результат: Сортування за кількома полями працює послідовно; P1 повертає записи 11-20, P2 - клієнтів 11-15.

Скріншот: `![](screenshots/9.png)`

---

# РІВЕНЬ 3

### 10.1 Samsung або Apple, але не чохол

```sql
SELECT product_name, unit_price
FROM products
WHERE (product_name ILIKE '%Samsung%' OR product_name ILIKE '%Apple%')
  AND product_name NOT ILIKE '%чохол%';
```

Результат: Знайдено 5 товарів (4 Samsung та AirPods Pro 2 від Apple). Дужки навколо OR обов'язкові, інакше NOT ILIKE стосувався б лише другої умови.

Скріншот: `![](screenshots/10-1.png)`

### 10.2 Власні запити: LIKE + логічні оператори

```sql
-- LL1. Аксесуари (кабель, навушники, клавіатура) дешевші за 10000 грн.
-- Бізнес-логіка: товари для імпульсних покупок біля каси.
SELECT product_name, unit_price
FROM products
WHERE (product_name ILIKE '%кабель%'
       OR product_name ILIKE '%навушники%'
       OR product_name ILIKE '%клавіатура%')
  AND unit_price < 10000;
```

```sql
-- LL2. Смартфони (категорія 1), але не iPhone і не Samsung.
-- Бізнес-логіка: альтернативні бренди для клієнтів з обмеженим бюджетом.
SELECT product_name, unit_price
FROM products
WHERE category_id = 1
  AND product_name NOT ILIKE '%iPhone%'
  AND product_name NOT ILIKE '%Samsung%';
```

```sql
-- LL3. Клієнти з Gmail або Ukr.net, прізвище яких закінчується на "енко" або "ова".
-- Бізнес-логіка: цільова аудиторія для розсилки. Прізвище стоїть на початку
-- contact_name, тому шаблон містить пробіл після закінчення.
SELECT contact_name, email
FROM customers
WHERE (email LIKE '%@gmail.com' OR email LIKE '%@ukr.net')
  AND (contact_name LIKE '%енко %' OR contact_name LIKE '%ова %');
```

```sql
-- LL4. Компанії з формою власності ТОВ або ПП, крім тестових записів.
-- Бізнес-логіка: очищена B2B-база для розсилки комерційних пропозицій.
SELECT company_name, city
FROM customers
WHERE (company_name LIKE 'ТОВ%' OR company_name LIKE 'ПП%')
  AND contact_name NOT ILIKE '%тест%';
```

Результат: LL1 - кабель, навушники, клавіатура; LL2 - Xiaomi, Google Pixel, OnePlus; LL3 та LL4 - цільові сегменти клієнтів.

Скріншот: `![](screenshots/10-2.png)`

### 11.1 Товари дорожчі 20000 (категорії 1 або 2) АБО дешевші 5000

```sql
SELECT product_name, category_id, unit_price
FROM products
WHERE (unit_price > 20000 AND category_id IN (1, 2))
   OR unit_price < 5000;
```

Результат: Знайдено 12 товарів: 9 дорогих смартфонів і ноутбуків та 3 дешеві (мікрохвильова піч, кабель, клавіатура).

Скріншот: `![](screenshots/11-1.png)`

### 11.2 Власні запити з вкладеними умовами

```sql
-- N1. Дорогі товари з малим залишком АБО дешеві масові товари.
-- Бізнес-логіка: два різні сценарії управління запасами в одному звіті.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE (unit_price > 40000 AND units_in_stock < 8)
   OR (unit_price < 5000 AND units_in_stock > 20);
```

```sql
-- N2. Пріоритетні клієнти: (компанії з Києва/Львова) АБО (фізособи з Одеси).
-- Бізнес-логіка: різні правила пріоритету для різних сегментів.
SELECT contact_name, city, customer_type
FROM customers
WHERE (customer_type = 'company' AND city IN ('Київ', 'Львів'))
   OR (customer_type = 'individual' AND city = 'Одеса');
```

```sql
-- N3. Замовлення, що потребують уваги: (не відправлені понад 7 днів) АБО (скасовані).
-- Бізнес-логіка: список проблемних замовлень для менеджера.
SELECT order_id, order_date, order_status
FROM orders
WHERE (shipped_date IS NULL
       AND order_date < CURRENT_DATE - INTERVAL '7 days'
       AND order_status <> 'cancelled')
   OR order_status = 'cancelled';
```

Результат: Дужки задають незалежні сценарії; без них через пріоритет AND над OR результат був би іншим.

Скріншот: `![](screenshots/11-2.png)`

### 12.1 Звіт товарів із 6 умовами фільтрації

```sql
-- Бізнес-логіка: "Кандидати на головну сторінку сайту" - актуальні, доступні,
-- популярних категорій, середнього та вищого сегмента, з описом, відомих брендів.
SELECT
    product_name   AS "Назва",
    unit_price     AS "Ціна, грн",
    units_in_stock AS "На складі"
FROM products
WHERE NOT discontinued                                  -- 1. не знято з виробництва
  AND units_in_stock > 0                                -- 2. є в наявності
  AND category_id IN (1, 2, 3, 7)                       -- 3. смартфони, ноутбуки, ТВ, планшети
  AND unit_price BETWEEN 15000 AND 60000                -- 4. ціновий діапазон
  AND description IS NOT NULL                           -- 5. є опис для картки товару
  AND (product_name ILIKE '%Samsung%'
       OR product_name ILIKE '%iPhone%'
       OR product_name ILIKE '%MacBook%'
       OR product_name ILIKE '%Sony%')                  -- 6. відомі бренди
ORDER BY unit_price DESC
LIMIT 10;
```

Результат: Отримано відсортований список до 10 товарів, що задовольняють усі 6 умов одночасно.

Скріншот: `![](screenshots/12-1.png)`

### 12.2 Аналіз клієнтської бази з множинними критеріями

```sql
-- Бізнес-логіка: сегментація клієнтів для маркетингової кампанії.
SELECT
    contact_name,
    city,
    customer_type,
    CASE
        WHEN customer_type = 'company' AND city = 'Київ'             THEN 'VIP корпоративний'
        WHEN customer_type = 'company'                               THEN 'Корпоративний'
        WHEN city IN ('Київ', 'Харків', 'Львів', 'Одеса', 'Дніпро') THEN 'Клієнт великого міста'
        ELSE 'Регіональний'
    END AS "Сегмент"
FROM customers
WHERE email IS NOT NULL                    -- є email для розсилки
  AND phone IS NOT NULL                    -- є телефон
  AND contact_name NOT ILIKE '%тест%'      -- без тестових записів
  AND registration_date >= '2023-01-01'    -- зареєстровані з 2023 року
ORDER BY customer_type ASC, city, contact_name;
```

Результат: Клієнти розділені на сегменти; спочатку компанії (company < individual за алфавітом), далі за містом та іменем.

Скріншот: `![](screenshots/12-2.png)`

### 13.1 Аналіз товарів за ціновими сегментами

```sql
-- PS1. Бюджетний сегмент (до 5000 грн).
-- Бізнес-логіка: аксесуари та дрібна техніка - швидкий оборот.
SELECT product_name, unit_price FROM products
WHERE unit_price < 5000
ORDER BY unit_price;
```

```sql
-- PS2. Середній сегмент (5000-20000 грн, верхня межа не включена).
-- Бізнес-логіка: консолі, недорогі смартфони, техніка для дому.
SELECT product_name, unit_price FROM products
WHERE unit_price >= 5000 AND unit_price < 20000
ORDER BY unit_price;
```

```sql
-- PS3. Преміум сегмент (від 20000 грн).
-- Бізнес-логіка: основа виручки, потребує уваги до залишків.
SELECT product_name, unit_price FROM products
WHERE unit_price >= 20000
ORDER BY unit_price DESC;
```

```sql
-- PS4. Розмітка всіх товарів сегментом + вартість запасів на складі.
-- Бізнес-логіка: де заморожено найбільше коштів.
SELECT
    product_name,
    unit_price,
    CASE
        WHEN unit_price < 5000  THEN 'Бюджетний'
        WHEN unit_price < 20000 THEN 'Середній'
        WHEN unit_price < 50000 THEN 'Преміум'
        ELSE 'Люкс'
    END AS segment,
    unit_price * units_in_stock AS inventory_value
FROM products
ORDER BY inventory_value DESC;
```

Результат: Бюджетний сегмент - 3 товари, решта розподілена між середнім і преміум. Найбільше коштів на складі заморожено в дорогих смартфонах і ноутбуках.

Скріншот: `![](screenshots/13-1.png)`

### 13.2 Географічний розподіл клієнтів

```sql
-- G1. Скільки клієнтів у Києві.
SELECT COUNT(*) AS kyiv_customers FROM customers WHERE city = 'Київ';
```

```sql
-- G2. Клієнти західних регіонів (Львівська, Івано-Франківська, Тернопільська,
-- Закарпатська, Волинська області за region_id).
-- Бізнес-логіка: планування логістики для західного складу.
SELECT contact_name, city, region_id
FROM customers
WHERE region_id IN (2, 7, 8, 19, 20)
ORDER BY city, contact_name;
```

```sql
-- G3. Клієнти поза Києвом і Харковом.
-- Бізнес-логіка: оцінка охоплення регіонів.
SELECT contact_name, city
FROM customers
WHERE city NOT IN ('Київ', 'Харків')
ORDER BY city;
```

```sql
-- G4. Юридичні особи за містами.
-- Бізнес-логіка: де концентрується B2B-попит.
SELECT city, company_name
FROM customers
WHERE customer_type = 'company'
ORDER BY city, company_name;
```

```sql
-- G5. Замовлення, доставлені у Дніпро.
-- Бізнес-логіка: навантаження на логістику по напрямку.
SELECT order_id, order_date, ship_city, ship_via
FROM orders
WHERE ship_city = 'Дніпро'
ORDER BY order_date;
```

Результат: Клієнти зосереджені в 5 містах; Київ - 4 клієнти, Львів - 3 (усі фізособи), B2B-клієнти в Києві, Харкові та Дніпрі.

Скріншот: `![](screenshots/13-2.png)`

### 13.3 Часові патерни в замовленнях

```sql
-- T1. Замовлення за останні 30 днів відносно найновішої дати в БД.
-- Бізнес-логіка: "живий" зріз активності (дані навчальні, тому CURRENT_DATE не підходить).
SELECT order_id, order_date, order_status
FROM orders
WHERE order_date >= (SELECT MAX(order_date) FROM orders) - INTERVAL '30 days'
ORDER BY order_date DESC;
```

```sql
-- T2. Замовлення, зроблені у вихідні (0 - неділя, 6 - субота).
-- Бізнес-логіка: чи потрібна робота служби підтримки у вихідні.
SELECT order_id, order_date, TRIM(TO_CHAR(order_date, 'Day')) AS weekday
FROM orders
WHERE EXTRACT(DOW FROM order_date) IN (0, 6)
ORDER BY order_date;
```

```sql
-- T3. Літній сезон (червень-серпень).
-- Бізнес-логіка: порівняння сезонного попиту.
SELECT order_id, order_date, order_status
FROM orders
WHERE EXTRACT(MONTH FROM order_date) IN (6, 7, 8)
ORDER BY order_date;
```

```sql
-- T4. Повільна відправка: від 3 днів між замовленням і відправкою.
-- Бізнес-логіка: контроль якості роботи складу та логістики.
SELECT order_id, order_date, shipped_date,
       shipped_date - order_date AS days_to_ship
FROM orders
WHERE shipped_date IS NOT NULL
  AND shipped_date - order_date >= 3
ORDER BY days_to_ship DESC, order_date;
```

Результат: Дані охоплюють січень-серпень 2024 року; T4 виявляє замовлення, що відправлялись довше 2 днів.

Скріншот: `![](screenshots/13-3.png)`

### 14. Креативні запити

```sql
-- K1. Ціни "на дев'ятках": товари з ціною, що закінчується на 999.
-- Бізнес-логіка: перевірка маркетингового прийому психологічного ціноутворення.
SELECT product_name, unit_price
FROM products
WHERE MOD(unit_price, 1000) = 999
ORDER BY unit_price;
```

```sql
-- K2. "Заморожений капітал": товари, запас яких у 3+ рази перевищує точку замовлення,
-- відсортовані за вартістю запасу.
-- Бізнес-логіка: де надлишкові запаси, які варто розпродавати.
SELECT product_name, units_in_stock, reorder_level,
       unit_price * units_in_stock AS frozen_value
FROM products
WHERE NOT discontinued
  AND units_in_stock > reorder_level * 3
ORDER BY frozen_value DESC
LIMIT 5;
```

```sql
-- K3. Клієнти зі стаціонарними телефонами (не мобільний оператор).
-- Бізнес-логіка: для SMS-розсилки такі номери непридатні - потрібен інший канал.
SELECT contact_name, phone, customer_type
FROM customers
WHERE NOT (phone LIKE '+38050%' OR phone LIKE '+38066%' OR phone LIKE '+38063%'
        OR phone LIKE '+38067%' OR phone LIKE '+38099%');
```

```sql
-- K4. Топ-менеджмент: співробітники без керівника.
-- Бізнес-логіка: список осіб для ескалації критичних питань.
SELECT first_name, last_name, title, email
FROM employees
WHERE reports_to IS NULL;
```

```sql
-- K5. "Що купити до 40000?": 5 найдорожчих доступних товарів у межах бюджету.
-- Бізнес-логіка: готова відповідь консультанта на типове питання клієнта.
SELECT product_name, unit_price
FROM products
WHERE unit_price <= 40000
  AND units_in_stock > 0
  AND NOT discontinued
ORDER BY unit_price DESC
LIMIT 5;
```

Результат: K3 знаходить клієнтів із міськими номерами (переважно компанії); K4 повертає генерального директора Петренка О.І.

Скріншот: `![](screenshots/14.png)`

---

## Висновки

**Самооцінка**: 5

**Обґрунтування**: Виконано завдання всіх трьох рівнів: базові вибірки, фільтрація, LIKE/ILIKE, логічні оператори з дужками, IN/BETWEEN/IS NULL, сортування за кількома полями, пагінація, аналітичні та креативні запити. До всіх самостійних запитів додано коментарі з бізнес-логікою. Під час роботи врахував особливості БД: `contact_name` зберігається як «Прізвище Ім'я По батькові», тому шаблони LIKE будувалися відповідно.

---

## Відповіді на контрольні запитання

1. **SQL** - стандартна декларативна мова для роботи з реляційними БД. У декларативній мові описуємо, _що_ потрібно отримати (`SELECT ... WHERE city = 'Київ'`), а СУБД сама обирає, _як_ це виконати. В імперативній (Python, C) ми вручну пишемо цикл, умови та порядок кроків.
2. Порядок виконання: `FROM` → `WHERE` → `SELECT` → `ORDER BY` → `LIMIT`. Через це псевдонім із `SELECT` не можна використати у `WHERE`, але можна в `ORDER BY`; а `WHERE` фільтрує рядки ще до вибору стовпців.
3. `=` - точний збіг (`city = 'Київ'`). `LIKE` - збіг за шаблоном (`contact_name LIKE 'Іван%'`). `=` доцільно для ідентифікаторів і точних значень, `LIKE` - для пошуку за частиною тексту.
4. `%` - будь-яка кількість символів (включно з нулем), `_` - рівно один символ. Комбінувати можна: `'А_%'` - рядок від двох символів, що починається з "А".
5. `NULL` - невідоме значення, тому `= NULL` дає `NULL` (а не `true`), і жоден рядок не проходить. Правильно: `IS NULL` / `IS NOT NULL`.
6. `AND` - усі умови істинні, `OR` - хоча б одна. `AND` має вищий пріоритет, тому `A OR B AND C` виконується як `A OR (B AND C)`.
7. `BETWEEN` завжди включає обидві межі. Щоб виключити границі: `x > a AND x < b`.
8. `LIMIT` - скільки рядків повернути, `OFFSET` - скільки пропустити. `OFFSET = (номер_сторінки - 1) * розмір_сторінки`.
9. Без `ORDER BY` порядок рядків не гарантований, тому `LIMIT` може повертати різні записи при різних запусках.
10. `LIKE` чутливий до регістру, `ILIKE` (тільки PostgreSQL) - ні. `ILIKE` зручний для пошуку за текстом, введеним користувачем; `LIKE` - коли регістр важливий.
11. За замовчуванням `NULL` вважається більшим за будь-яке значення: при `ASC` вони в кінці, при `DESC` - на початку. Змінюється через `NULLS FIRST` / `NULLS LAST`.
12. Приклад: знижка на товари категорій 1 або 2 дорожчі за 20000. Запит `category_id = 1 OR category_id = 2 AND unit_price > 20000` без дужок поверне _усі_ товари категорії 1 (навіть дешеві), бо `AND` виконується першим. Правильно: `(category_id = 1 OR category_id = 2) AND unit_price > 20000`.
