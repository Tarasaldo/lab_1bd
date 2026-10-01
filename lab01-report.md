# Лабораторна робота 1. Робота з СУБД PostgreSQL та основи SQL

## Загальна інформація

**Здобувач освіти:** Самойло Тарас
**Група:** ІПЗ-22
**Обраний рівень складності:** 2

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

<img width="322" height="481" alt="image" src="https://github.com/user-attachments/assets/b3d788aa-3127-4bfb-911a-565755bc4285" />




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

<img width="597" height="758" alt="image" src="https://github.com/user-attachments/assets/5aa4efff-fa32-4426-9c92-8b2f48693ad1" />


### 1.2 Назви товарів і їхні ціни

```sql
SELECT product_name, unit_price FROM products;
```

Результат: Отримано лише два стовпці для всіх товарів каталогу.

<img width="768" height="964" alt="image" src="https://github.com/user-attachments/assets/d8b71a63-c6b6-417e-9e23-8ee64b5213c6" />


### 1.3 Контактні дані співробітників

```sql
SELECT first_name, last_name, phone, email FROM employees;
```

Результат: Отримано контактні дані 8 співробітників.

<img width="1431" height="330" alt="image" src="https://github.com/user-attachments/assets/d688e96c-fb68-462e-9e22-3eee3ddbb238" />


### 2.1 Клієнти з міста Київ

```sql
SELECT * FROM customers WHERE city = 'Київ';
```

Результат: Знайдено 4 клієнти з Києва.

<img width="967" height="537" alt="image" src="https://github.com/user-attachments/assets/0750636e-5db9-46c6-a3dc-4efe03c8653c" />


### 2.2 Товари дорожчі за 25000 грн

```sql
SELECT product_name, unit_price
FROM products
WHERE unit_price > 25000;
```

Результат: Знайдено 13 товарів (смартфони-флагмани, ноутбуки, телевізори, холодильник, планшет).

<img width="483" height="542" alt="image" src="https://github.com/user-attachments/assets/0571f653-eb5a-4521-a67a-4ebf2a5eef32" />


### 2.3 Замовлення зі статусом 'delivered'

```sql
SELECT * FROM orders WHERE order_status = 'delivered';
```

Результат: Знайдено 26 доставлених замовлень.

<img width="483" height="542" alt="image" src="https://github.com/user-attachments/assets/d1351e70-3a09-432f-9a7b-484ba27df24a" />


### 2.4 Співробітники відділу продажів

```sql
-- посада міститься у стовпці title; ILIKE ігнорує регістр
SELECT first_name, last_name, title
FROM employees
WHERE title ILIKE '%продаж%';
```

Результат: Знайдено 3 менеджерів з продажу (Коваленко, Мельник, Гриценко).

<img width="539" height="370" alt="image" src="https://github.com/user-attachments/assets/7cb0ec17-939f-4386-8aa4-e004874236f6" />


### 3.1 Товари за зростанням ціни

```sql
SELECT product_name, unit_price
FROM products
ORDER BY unit_price ASC;
```

Результат: Найдешевший товар - USB-C кабель (699 грн), найдорожчий - LG OLED (62999 грн).

<img width="960" height="191" alt="image" src="https://github.com/user-attachments/assets/8e37653a-ad9a-4419-9c15-ba9728c03100" />


### 3.2 Клієнти за алфавітом

```sql
SELECT contact_name, city
FROM customers
ORDER BY contact_name;
```

Результат: Клієнти впорядковані за прізвищем (contact_name починається з прізвища).

<img width="438" height="486" alt="image" src="https://github.com/user-attachments/assets/c821c242-14de-4ff2-a90e-fd78c0cf3a8f" />


### 3.3 Замовлення від найновіших до найстаріших

```sql
SELECT order_id, customer_id, order_date, order_status
FROM orders
ORDER BY order_date DESC;
```

Результат: Першим іде замовлення від 2024-08-20.

<img width="961" height="506" alt="image" src="https://github.com/user-attachments/assets/031b4140-941b-408d-a9a6-220fd5414e2c" />



### 4.1 Топ-10 найдорожчих товарів

```sql
SELECT product_name, unit_price
FROM products
ORDER BY unit_price DESC
LIMIT 10;
```

Результат: Отримано 10 найдорожчих товарів.

<img width="442" height="459" alt="image" src="https://github.com/user-attachments/assets/3d8ce558-8abc-4b96-a56c-a5d3076513a0" />


### 4.2 П'ять останніх замовлень

```sql
SELECT order_id, order_date, order_status
FROM orders
ORDER BY order_date DESC
LIMIT 5;
```

Результат: Отримано 5 замовлень серпня 2024 року.

<img width="394" height="329" alt="image" src="https://github.com/user-attachments/assets/ee636e5e-a6ae-4522-913d-1bd6e9895d1d" />


### 4.3 Перші 8 клієнтів за алфавітом

```sql
SELECT contact_name, city
FROM customers
ORDER BY contact_name
LIMIT 8;
```

Результат: Отримано 8 клієнтів.

<img width="379" height="448" alt="image" src="https://github.com/user-attachments/assets/f2ca302d-66bd-44dd-9248-7740a087f39c" />


---

# РІВЕНЬ 2

### 5.1 Клієнти, імена яких починаються на "Іван"

```sql
SELECT * FROM customers WHERE contact_name LIKE 'Іван%';
```

Результат: Знайдено 1 запис - Іванова Марія Сергіївна. Примітка: у БД `contact_name` зберігається у форматі «Прізвище Ім'я По батькові», тому шаблон `'Іван%'` шукає за початком прізвища.

<img width="1007" height="233" alt="image" src="https://github.com/user-attachments/assets/daf2b21c-29f0-4bdc-9681-77e1b6c14a40" />


### 5.2 Товари зі словом "phone" або "телефон"

```sql
SELECT product_name, unit_price
FROM products
WHERE product_name ILIKE '%phone%' OR product_name ILIKE '%телефон%';
```

Результат: Знайдено iPhone 15 (слово "телефон" у назвах не зустрічається).

<img width="352" height="201" alt="image" src="https://github.com/user-attachments/assets/beff324d-25f5-47f5-8d21-7bfa88643f8e" />


### 5.3 Власні запити з LIKE

```sql
-- L1. ПОЧАТОК: товари, назва яких починається з "Samsung".
-- Бізнес-логіка: менеджер хоче швидко переглянути лінійку одного бренду.
SELECT product_name, unit_price
FROM products
WHERE product_name LIKE 'Samsung%';


<img width="490" height="262" alt="image" src="https://github.com/user-attachments/assets/8d30cff6-6afd-4fe0-86b7-f3146ee87f10" />

```

```sql
-- L2. КІНЕЦЬ: клієнти, чиє по батькові закінчується на "ович" (чоловіки).
-- Бізнес-логіка: сегментація за статтю для персоналізованих розсилок.
SELECT contact_name, city
FROM customers
WHERE contact_name LIKE '%ович';
```
<img width="395" height="423" alt="image" src="https://github.com/user-attachments/assets/f47e83b7-8a6f-41d9-876c-4e07803c27db" />


  
```sql
-- L3. МІСТИТЬ: клієнти з поштою на Gmail.
-- Бізнес-логіка: аналіз популярності поштових сервісів серед покупців.
SELECT contact_name, email
FROM customers
WHERE email LIKE '%@gmail.com';
```
<img width="519" height="255" alt="image" src="https://github.com/user-attachments/assets/2c0364df-4910-40a2-8df6-9a9d3a0aaf0d" />

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

<img width="428" height="551" alt="image" src="https://github.com/user-attachments/assets/c8b88240-8477-4481-9b4d-d945eff123f2" />


### 6.2 Клієнти з Києва або Львова, які є юридичними особами

```sql
SELECT contact_name, company_name, city
FROM customers
WHERE (city = 'Київ' OR city = 'Львів')
  AND customer_type = 'company';
```

Результат: Знайдено 3 компанії, усі з Києва (у Львові юридичних осіб у БД немає).

<img width="547" height="258" alt="image" src="https://github.com/user-attachments/assets/b08ec9e3-e94c-4df3-86c3-77e6b2f4de83" />


### 6.3 Власні запити з AND / OR / NOT

```sql
-- A1. Товари, що є в наявності і не зняті з виробництва.
-- Бізнес-логіка: каталог для сайту має показувати лише доступні позиції.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE units_in_stock > 0 AND NOT discontinued;
```
<img width="722" height="581" alt="image" src="https://github.com/user-attachments/assets/2c9373f5-dbed-49be-ba0f-f95afc896711" />

```sql
-- A2. Товари до 30000 грн, яких залишилось менше 10 штук.
-- Бізнес-логіка: кандидати на термінове поповнення складу.
SELECT product_name, unit_price, units_in_stock
FROM products
WHERE unit_price < 30000 AND units_in_stock < 10
ORDER BY units_in_stock;
```
<img width="722" height="581" alt="image" src="https://github.com/user-attachments/assets/c1ba9fd3-1eee-4f7b-a674-76fe2f38b0ae" />

```sql
-- A3. Фізичні особи не з Києва.
-- Бізнес-логіка: регіональна розсилка для роздрібних покупців.
SELECT contact_name, city
FROM customers
WHERE customer_type = 'individual' AND city <> 'Київ';
```
<img width="453" height="574" alt="image" src="https://github.com/user-attachments/assets/ddcf4436-3c4f-448c-a8f7-78300d574b72" />

```sql
-- A4. Замовлення, які ще не завершені (ні доставлені, ні скасовані).
-- Бізнес-логіка: "активні" замовлення, за якими потрібен контроль менеджера.
SELECT order_id, order_date, order_status
FROM orders
WHERE NOT (order_status = 'delivered' OR order_status = 'cancelled');
```
<img width="378" height="381" alt="image" src="https://github.com/user-attachments/assets/10ac45f5-5214-451e-80ec-ccc4d23d15c6" />

Результат: A1 - усі активні товари; A2 - OnePlus 12, Samsung холодильник, PlayStation 5, Xbox; A3 - роздрібні клієнти регіонів; A4 - 5 замовлень (pending, processing, shipped).

<img width="634" height="284" alt="image" src="https://github.com/user-attachments/assets/375f08d0-0fcb-4358-be11-7497ffd416b4" />


### 7.1 Клієнти з Києва, Харкова, Одеси, Дніпра

```sql
SELECT contact_name, city
FROM customers
WHERE city IN ('Київ', 'Харків', 'Одеса', 'Дніпро')
ORDER BY city;
```

Результат: Знайдено 12 клієнтів (3 клієнти зі Львова відсіяні).

<img width="384" height="546" alt="image" src="https://github.com/user-attachments/assets/a8e4bf7f-f7e8-41b7-b0a0-1ad99cdc80c2" />


### 7.2 Товари в діапазоні від 10000 до 30000 грн

```sql
SELECT product_name, unit_price
FROM products
WHERE unit_price BETWEEN 10000 AND 30000;
```

Результат: Отримано товари з ціною від 10000 до 30000 включно (межі входять).

<img width="471" height="562" alt="image" src="https://github.com/user-attachments/assets/0e82cd0f-1f28-42d6-9643-fe027460531b" />


### 7.3 Власні запити з IN, BETWEEN, IS NULL

```sql
-- IN-1. Товари категорій "Смартфони", "Ноутбуки", "Телевізори".
-- Бізнес-логіка: огляд ключових категорій для квартального звіту.
SELECT product_name, category_id
FROM products
WHERE category_id IN (1, 2, 3);
```
<img width="566" height="611" alt="image" src="https://github.com/user-attachments/assets/e24ae48d-16f3-484b-9409-2bb34b6665f2" />

```sql
-- IN-2. Замовлення, що зараз перебувають в обробці або в дорозі.
-- Бізнес-логіка: замовлення, які ще не дійшли до клієнта.
SELECT order_id, order_status
FROM orders
WHERE order_status IN ('processing', 'shipped');
```
<img width="566" height="611" alt="image" src="https://github.com/user-attachments/assets/d53758b4-ad7b-453a-9104-398950f9cdae" />

```sql
-- BETWEEN-1. Замовлення за перший квартал 2024 року.
-- Бізнес-логіка: квартальна звітність.
SELECT order_id, order_date
FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-03-31';
```
<img width="265" height="274" alt="image" src="https://github.com/user-attachments/assets/0cb58f13-8427-47a5-b104-cc092752979d" />

```sql
-- BETWEEN-2. Бюджетні товари від 1000 до 5000 грн.
-- Бізнес-логіка: добірка для акції "все до 5 тисяч".
SELECT product_name, unit_price
FROM products
WHERE unit_price BETWEEN 1000 AND 5000;
```
<img width="474" height="169" alt="image" src="https://github.com/user-attachments/assets/f41f599b-ab15-4f29-a6bb-8c5151f33692" />

```sql
-- IS NULL. Замовлення, які ще не відправлені.
-- Бізнес-логіка: черга на відправку для складу.
SELECT order_id, order_date, order_status
FROM orders
WHERE shipped_date IS NULL;
```<img width="493" height="298" alt="image" src="https://github.com/user-attachments/assets/3ced89ad-c63f-477b-8a90-67a14967fdd5" />

<img width="636" height="301" alt="image" src="https://github.com/user-attachments/assets/350611a0-e683-42d8-add4-05360afbc252" />

```sql
-- IS NOT NULL. Клієнти-компанії (є назва компанії).
-- Бізнес-логіка: база B2B-клієнтів для відділу корпоративних продажів.
SELECT company_name, contact_name, contact_title
FROM customers
WHERE company_name IS NOT NULL;
```
<img width="636" height="301" alt="image" src="https://github.com/user-attachments/assets/45f97500-bb7e-42b5-9729-d72ab9768456" />



### 8. Комбінування умов (5 запитів)

```sql
-- C1. LIKE + OR + BETWEEN: Samsung або iPhone у діапазоні 20000-60000.
-- Бізнес-логіка: флагманські смартфони двох брендів для банерної реклами.
SELECT product_name, unit_price
FROM products
WHERE (product_name LIKE '%Samsung%' OR product_name LIKE '%iPhone%')
  AND unit_price BETWEEN 20000 AND 60000;
```
<img width="468" height="299" alt="image" src="https://github.com/user-attachments/assets/be40798b-7848-40b8-b350-e88d26b1c3e0" />

```sql
-- C2. IN + IS NULL + BETWEEN: фізособи з великих міст, зареєстровані у Q1 2023.
-- Бізнес-логіка: "перші клієнти" магазину для програми лояльності.
SELECT contact_name, city, registration_date
FROM customers
WHERE city IN ('Київ', 'Харків', 'Одеса')
  AND company_name IS NULL
  AND registration_date BETWEEN '2023-01-01' AND '2023-03-31';
```
<img width="587" height="319" alt="image" src="https://github.com/user-attachments/assets/68c95ee6-8387-4170-b3ea-b0a77e6e26aa" />

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
<img width="602" height="363" alt="image" src="https://github.com/user-attachments/assets/8df8b3e3-4a84-4c86-aa7c-436526c492c4" />

```sql
-- C4. LIKE + NOT IN: клієнти з прізвищем на "М", не з Києва.
-- Бізнес-логіка: сегмент для регіональної кампанії.
SELECT contact_name, city
FROM customers
WHERE contact_name LIKE 'М%'
  AND city NOT IN ('Київ');
```
<img width="406" height="248" alt="image" src="https://github.com/user-attachments/assets/cad1a9b0-5e47-425d-9948-301670f9c732" />

```sql
-- C5. BETWEEN + IS NULL: замовлення 2024 року, які досі не відправлені.
-- Бізнес-логіка: виявлення проблемних (прострочених) замовлень.
SELECT order_id, order_date, order_status
FROM orders
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31'
  AND shipped_date IS NULL;
```

Результат: Кожен запит поєднує щонайменше два різні типи умов і повертає змістовну вибірку для бізнес-задачі.

<img width="396" height="354" alt="image" src="https://github.com/user-attachments/assets/8f8a00ef-28ca-4f12-8b0d-e35930b84cc1" />


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

<img width="433" height="258" alt="image" src="https://github.com/user-attachments/assets/c6afd359-65eb-42c0-8589-5142dfe86de9" />


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

<img width="468" height="299" alt="image" src="https://github.com/user-attachments/assets/74c6edbe-e757-4a71-adf3-fcca0129dcb7" />


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

<img width="381" height="279" alt="image" src="https://github.com/user-attachments/assets/c89a4848-6d0c-4972-ac62-35849f8c5d91" />


### 11.1 Товари дорожчі 20000 (категорії 1 або 2) АБО дешевші 5000

```sql
SELECT product_name, category_id, unit_price
FROM products
WHERE (unit_price > 20000 AND category_id IN (1, 2))
   OR unit_price < 5000;
```

Результат: Знайдено 12 товарів: 9 дорогих смартфонів і ноутбуків та 3 дешеві (мікрохвильова піч, кабель, клавіатура).

<img width="590" height="531" alt="image" src="https://github.com/user-attachments/assets/8aa2d627-7cf4-4b4b-b6a9-5d0d341eb6f4" />


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

<img width="658" height="592" alt="image" src="https://github.com/user-attachments/assets/d931cfff-66cb-41a6-b5e2-526806bba9eb" />


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


<img width="571" height="544" alt="image" src="https://github.com/user-attachments/assets/f043bbca-6438-492b-9371-686b16149fb2" />


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

<img width="468" height="299" alt="image" src="https://github.com/user-attachments/assets/709da2dc-183d-4ec7-b96b-95370c60d799" />


---

## Висновки

**Самооцінка**: 4

Обґрунтування: Виконано всі завдання рівнів 1 і 2: базові вибірки, фільтрація, LIKE/ILIKE, логічні оператори AND/OR/NOT з дужками, IN, BETWEEN, IS NULL / IS NOT NULL, комбіновані умови, сортування за кількома полями та пагінація (LIMIT/OFFSET). До всіх самостійних запитів додано коментарі з поясненням бізнес-логіки. Під час роботи врахував особливості БД: contact_name зберігається як «Прізвище Ім'я По батькові», тому шаблони LIKE будувалися відповідно. Завдання рівня 3 не виконувались, тому оцінка - 4.
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
