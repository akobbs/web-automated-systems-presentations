# **Лабораторна робота №9**

**Тема:** Маніпуляції з DOM

**Мета:** Закріпити практичні навички роботи з Document Object Model: пошук елементів, читання та зміна вмісту, управління класами та стилями, динамічне створення та видалення елементів.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер (Chrome, Firefox, Edge); текстовий редактор (VS Code, Sublime Text або інший)

---

## **Контрольні запитання**

1. Що таке DOM і чому JavaScript взаємодіє зі сторінкою лише через нього?
2. Яка різниця між `querySelector` та `getElementById`? Коли доцільно використовувати кожен?
3. Чим відрізняється `textContent` від `innerHTML`? Коли небезпечно використовувати `innerHTML`?
4. Що таке `classList`? Поясніть різницю між методами `add`, `remove` та `toggle`.
5. Яка різниця між `element.remove()` та `element.style.display = 'none'`? Коли варто застосовувати кожен підхід?

---

## **Теоретичні відомості**

### **DOM та об'єкт `document`**

DOM (Document Object Model) — це деревоподібне представлення HTML-документа, через яке JavaScript отримує доступ до елементів сторінки та може їх змінювати. Точкою входу є глобальний об'єкт `document`.

```javascript
// document — точка входу до всього DOM
console.log(document.title); // заголовок вкладки
console.log(document.body); // елемент <body>
```

### **Пошук елементів**

| Метод                                   | Повертає                |
| --------------------------------------- | ----------------------- |
| `document.getElementById('id')`         | Один елемент або `null` |
| `document.querySelector('selector')`    | Перший збіг або `null`  |
| `document.querySelectorAll('selector')` | NodeList (усі збіги)    |

```javascript
const title = document.getElementById("main-title");
const btn = document.querySelector(".btn-primary");
const inputs = document.querySelectorAll('input[type="text"]');
```

### **Зміна вмісту**

```javascript
const el = document.querySelector("h1");

el.textContent = "Новий заголовок"; // безпечно — лише текст
el.innerHTML = "<em>Курсив</em>"; // вставляє HTML (лише з перевіреними даними)
```

### **Управління класами — `classList`**

```javascript
const card = document.querySelector(".card");

card.classList.add("active"); // додати клас
card.classList.remove("hidden"); // видалити клас
card.classList.toggle("selected"); // додати або видалити залежно від наявності
card.classList.contains("active"); // true / false
```

### **Зміна стилів та атрибутів**

```javascript
const box = document.querySelector(".box");

box.style.backgroundColor = "#e3f2fd"; // пряма зміна стилю
box.setAttribute("data-status", "ok"); // встановити атрибут
box.removeAttribute("disabled"); // видалити атрибут
console.log(box.getAttribute("id")); // прочитати атрибут
```

### **Створення та видалення елементів**

```javascript
// Створення
const li = document.createElement("li");
li.textContent = "Новий пункт";
document.querySelector("ul").appendChild(li); // додати в кінець

// Видалення
li.remove(); // видалити повністю з DOM
li.style.display = "none"; // приховати (залишається в DOM)
```

---

## **Хід роботи**

### **Етап 1: Підготовка**

1.1. Створіть папку `lab9`.

1.2. Всередині створіть файли відповідно до структури:

```
lab9/
├── task1.html
├── task2.html
└── task3.html
```

---

### **Етап 2: Виконання завдань**

---

#### **Завдання 1: Картка студента (task1.html)**

Створіть сторінку з карткою студента та панеллю керування. Вся логіка — у тегу `<script>` в кінці `<body>`.

**HTML-структура (надається повністю):**

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Картка студента</title>
    <style>
      .card {
        border: 2px solid #ccc;
        padding: 20px;
        width: 300px;
        border-radius: 8px;
      }
      .card.highlight {
        border-color: #1565c0;
        background: #e3f2fd;
      }
      .status {
        font-weight: bold;
        margin-top: 8px;
      }
      .status.active {
        color: #2e7d32;
      }
      .status.inactive {
        color: #c62828;
      }
      button {
        margin: 4px;
        padding: 6px 12px;
        cursor: pointer;
      }
    </style>
  </head>
  <body>
    <div class="card" id="student-card">
      <h2 id="student-name">Іван Петренко</h2>
      <p>Група: <span id="student-group">КН-31</span></p>
      <p>Середній бал: <span id="student-gpa">4.2</span></p>
      <p class="status active" id="student-status">✅ Активний</p>
    </div>

    <div style="margin-top: 16px;">
      <button onclick="highlightCard()">🎨 Виділити картку</button>
      <button onclick="toggleStatus()">🔄 Змінити статус</button>
      <button onclick="updateGpa()">📈 Оновити бал</button>
    </div>

    <script>
      // Завдання 1.1
      // Реалізуйте функцію highlightCard():
      // - знайдіть елемент з id "student-card"
      // - перемикайте на ньому клас "highlight" за допомогою classList.toggle
      function highlightCard() {
        // ??? ваш код тут
      }

      // Завдання 1.2
      // Реалізуйте функцію toggleStatus():
      // - знайдіть елемент з id "student-status"
      // - якщо елемент має клас "active":
      //   - видаліть клас "active", додайте клас "inactive"
      //   - встановіть textContent = "❌ Неактивний"
      // - інакше (клас "inactive"):
      //   - видаліть клас "inactive", додайте клас "active"
      //   - встановіть textContent = "✅ Активний"
      function toggleStatus() {
        // ??? ваш код тут
      }

      // Завдання 1.3
      // Реалізуйте функцію updateGpa():
      // - знайдіть елемент з id "student-gpa"
      // - зчитайте поточне значення через textContent і перетворіть на число (parseFloat)
      // - збільшіть значення на 0.1, але не більше 5.0 (використайте Math.min)
      // - округліть результат до 1 знаку після коми (toFixed(1))
      //   (увага: toFixed повертає рядок, але для textContent це нормально —
      //   браузер відобразить його як текст; при наступному натисканні parseFloat знову розпарсить)
      // - запишіть нове значення назад у textContent
      function updateGpa() {
        // ??? ваш код тут
      }
    </script>
  </body>
</html>
```

**Очікуваний результат:**

- Кнопка «Виділити картку» перемикає синє виділення картки
- Кнопка «Змінити статус» перемикає між «✅ Активний» (зелений) та «❌ Неактивний» (червоний)
- Кнопка «Оновити бал» збільшує бал на 0.1 з кроком до максимуму 5.0

---

#### **Завдання 2: Каталог товарів (task2.html)**

Реалізуйте динамічний каталог: додавання нових товарів та їх фільтрацію за категорією.

**HTML-структура (надається повністю):**

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Каталог товарів</title>
    <style>
      .product {
        border: 1px solid #ddd;
        padding: 12px;
        margin: 8px 0;
        border-radius: 6px;
        display: flex;
        justify-content: space-between;
        align-items: center;
      }
      .tag {
        font-size: 12px;
        background: #e3f2fd;
        padding: 2px 8px;
        border-radius: 4px;
      }
      .controls {
        margin-bottom: 16px;
        display: flex;
        gap: 8px;
      }
      button {
        padding: 6px 12px;
        cursor: pointer;
      }
    </style>
  </head>
  <body>
    <h2>🛒 Каталог товарів</h2>

    <div class="controls">
      <input id="name-input" type="text" placeholder="Назва товару" />
      <input id="price-input" type="number" placeholder="Ціна (₴)" min="1" />
      <select id="cat-input">
        <option value="Електроніка">Електроніка</option>
        <option value="Одяг">Одяг</option>
        <option value="Книги">Книги</option>
      </select>
      <button onclick="addProduct()">➕ Додати</button>
    </div>

    <div class="controls">
      <button onclick="filterProducts('Електроніка')">📱 Електроніка</button>
      <button onclick="filterProducts('Одяг')">👕 Одяг</button>
      <button onclick="filterProducts('Книги')">📚 Книги</button>
      <button onclick="filterProducts('')">🔄 Усі</button>
    </div>

    <div id="product-list"></div>

    <script>
      // Початкові дані (масив об'єктів)
      const products = [
        { name: "Навушники", price: 1200, category: "Електроніка" },
        { name: "Футболка", price: 350, category: "Одяг" },
        { name: "Кобзар", price: 180, category: "Книги" },
      ];

      // Завдання 2.1
      // Реалізуйте функцію renderProducts(list):
      // - знайдіть контейнер з id "product-list"
      // - очистіть його вміст (innerHTML = '')
      // - для кожного товару з масиву list створіть div з класом "product"
      // - всередині div розмістіть:
      //   а) span з текстом: "${товар.name} — ${товар.price} ₴"
      //   б) span з класом "tag" і текстом товар.category
      //   в) button з текстом "🗑️", при кліку на який цей div видаляється з DOM
      //      (підказка: всередині onclick-функції кнопки використайте closest('.product')
      //      щоб знайти батьківський div-товар і викличте на ньому remove())
      // - додайте div у контейнер
      function renderProducts(list) {
        // ??? ваш код тут
      }

      // Завдання 2.2
      // Реалізуйте функцію addProduct():
      // - зчитайте значення з полів "name-input", "price-input", "cat-input"
      // - якщо назва порожня або ціна не є числом > 0 — завершіть функцію достроково (return)
      // - додайте новий об'єкт { name, price: Number(price), category } до масиву products
      // - викличте renderProducts(products) для оновлення списку
      // - очистіть поля name-input та price-input (value = '')
      function addProduct() {
        // ??? ваш код тут
      }

      // Завдання 2.3
      // Реалізуйте функцію filterProducts(category):
      // - якщо category порожній рядок — викличте renderProducts(products) і завершіть
      // - інакше відфільтруйте масив products, залишивши лише товари з відповідною категорією
      // - викличте renderProducts з відфільтрованим масивом
      // Увага: масив products не змінюється — фільтрація лише для відображення
      function filterProducts(category) {
        // ??? ваш код тут
      }

      // Початкова відрисовка
      renderProducts(products);
    </script>
  </body>
</html>
```

**Очікуваний результат:**

- Під час завантаження відображаються 3 початкові товари
- Кнопка «Додати» створює новий елемент у списку та очищає поля
- Кнопка «🗑️» поруч із товаром видаляє саме його
- Кнопки фільтра показують лише товари обраної категорії; «Усі» повертає повний список

> **Примітка:** кнопка «🗑️» видаляє товар лише візуально — з масиву `products` він не прибирається. Якщо після видалення натиснути кнопку фільтра або «Усі», список перемалюється і видалений товар з'явиться знову. Це очікувана поведінка для даного завдання.

---

#### **Завдання 3: Динамічна таблиця (task3.html)**

Реалізуйте таблицю з даними про погоду, до якої можна додавати та видаляти рядки.

**HTML-структура (надається повністю):**

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Погода</title>
    <style>
      table {
        border-collapse: collapse;
        width: 100%;
        max-width: 500px;
      }
      th,
      td {
        border: 1px solid #ccc;
        padding: 8px 12px;
        text-align: left;
      }
      th {
        background: #e3f2fd;
      }
      tr.cold td {
        background: #f3e5f5;
      }
      .controls {
        margin-bottom: 12px;
        display: flex;
        gap: 8px;
      }
      input {
        padding: 6px;
        width: 120px;
      }
      button {
        padding: 6px 12px;
        cursor: pointer;
      }
    </style>
  </head>
  <body>
    <h2>🌤️ Журнал погоди</h2>

    <div class="controls">
      <input id="date-input" type="date" />
      <input id="temp-input" type="number" placeholder="Температура (°C)" />
      <input id="desc-input" type="text" placeholder="Опис (Сонячно...)" />
      <button onclick="addRow()">➕ Додати</button>
    </div>

    <table id="weather-table">
      <thead>
        <tr>
          <th>📅 Дата</th>
          <th>🌡️ Температура</th>
          <th>☁️ Опис</th>
          <th>🗑️</th>
        </tr>
      </thead>
      <tbody id="weather-body">
        <!-- рядки генеруються через JS -->
      </tbody>
    </table>

    <script>
      // Початкові дані
      const weatherData = [
        { date: "2026-05-01", temp: 18, desc: "Сонячно" },
        { date: "2026-05-02", temp: 12, desc: "Хмарно" },
        { date: "2026-05-03", temp: -2, desc: "Сніг" },
      ];

      // Завдання 3.1
      // Реалізуйте функцію renderTable(data):
      // - знайдіть tbody з id "weather-body"
      // - очистіть його вміст
      // - для кожного запису з масиву data створіть <tr>
      // - якщо temp < 0 — додайте до tr клас "cold"
      // - додайте 4 клітинки <td>: дата, температура + " °C", опис, кнопка "🗑️"
      // - кнопка при кліку видаляє батьківський <tr> з DOM
      //   (підказка: кнопка знаходиться всередині <td>, тому parentElement від кнопки — це <td>,
      //   а не <tr>; використайте closest('tr') для надійного пошуку рядка)
      // - додайте рядок у tbody
      function renderTable(data) {
        // ??? ваш код тут
      }

      // Завдання 3.2
      // Реалізуйте функцію addRow():
      // - зчитайте значення з полів "date-input", "temp-input", "desc-input"
      // - якщо хоча б одне поле порожнє — завершіть функцію достроково (return)
      //   (увага: для поля температури перевіряйте саме рядкове значення на порожність
      //   до перетворення на число — Number('') дає 0, а не помилку)
      // - додайте новий об'єкт { date, temp: Number(temp), desc } до масиву weatherData
      // - викличте renderTable(weatherData)
      // - очистіть усі три поля введення (value = '')
      function addRow() {
        // ??? ваш код тут
      }

      // Початкова відрисовка
      renderTable(weatherData);
    </script>
  </body>
</html>
```

**Очікуваний результат:**

- Таблиця відображає 3 початкові записи; рядок з від'ємною температурою має фіолетовий фон
- Кнопка «➕ Додати» додає новий рядок до таблиці та очищає поля
- Кнопка «🗑️» у рядку видаляє саме цей рядок з DOM

> **Примітка:** кнопка «🗑️» видаляє рядок лише візуально — з масиву `weatherData` він не прибирається. Якщо після видалення додати новий запис, таблиця перемалюється і видалений рядок з'явиться знову. Це очікувана поведінка для даного завдання.

---

### **Етап 3: Перевірка**

3.1. Відкрийте кожен файл у браузері та перевірте виконання завдань відповідно до описаного очікуваного результату.

3.2. Відкрийте DevTools (F12) → вкладка **Console** — переконайтесь, що під час роботи відсутні помилки.

3.3. Відкрийте DevTools → вкладка **Elements** — переконайтесь, що DOM-дерево оновлюється коректно при додаванні та видаленні елементів.

3.4. Спробуйте граничні випадки: порожні поля введення, від'ємні числа, повторне натискання кнопок фільтру.

**Результат:** три HTML-файли з коректно реалізованими функціями маніпуляції DOM.

---

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням

2. **Відповіді на контрольні питання** (коротко, по суті)

3. **Виконання завдань:**

   3.1. Для кожного завдання (1–3): скріншот реалізованого JavaScript-коду та скріншот результату у браузері

   3.2. Скріншот вкладки **Elements** у DevTools, що демонструє динамічну зміну DOM (наприклад, після додавання елемента)

4. **Висновки** (що навчились робити, які навички отримали, з якими труднощами зіткнулись)

---

## **Типові помилки та їх вирішення**

| Проблема                                               | Рішення                                                                                                                                                           |
| ------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `getElementById(...)` повертає `null`                  | Переконайтесь, що `id` у HTML та в коді написані однаково; `<script>` має бути після відповідного елемента в `<body>`                                             |
| Кнопка видаляє не той елемент                          | Врахуйте вкладеність: кнопка в таблиці знаходиться всередині `<td>`, а не `<tr>` — використайте `closest('tr')` або `closest('.product')` замість `parentElement` |
| `textContent` показує `NaN` після арифметики           | Перед обчисленням перетворіть рядок на число: `parseFloat(...)` або `Number(...)`                                                                                 |
| Після фільтрації дані зникають назавжди                | Фільтрована функція має працювати з копією масиву (або окремою змінною), не змінюючи оригінальний масив                                                           |
| Порожній рядок чи некоректні дані потрапляють у список | Перевіряйте вхідні дані на початку функції та завершуйте виконання через `return` при невалідних значеннях                                                        |

---

## **Додаткові матеріали**

1. DOM — сучасний підручник JavaScript (uk.javascript.info) — https://uk.javascript.info/document
2. JavaScript Tutorial (W3Schools, українською) — https://w3schoolsua.github.io/js/index.html
