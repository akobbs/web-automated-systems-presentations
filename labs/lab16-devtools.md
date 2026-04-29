# **Лабораторна робота №16**

**Тема:** Налагодження веб-додатків за допомогою DevTools

**Мета:** Закріпити практичні навички використання інструментів розробника браузера (DevTools): інспекція DOM та CSS, виконання JavaScript у Console, аналіз мережевих запитів та налагодження коду через точки зупинки.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер Google Chrome; текстовий редактор (VS Code, Sublime Text або інший).

## **Контрольні запитання**

1. Які основні вкладки DevTools та яке призначення кожної з них?
2. У чому різниця між методами `console.log()`, `console.warn()`, `console.error()` та `console.table()`?
3. Як за допомогою Network визначити, що запит до API повернув помилку, та де подивитись її деталі?
4. Що таке breakpoint? У чому різниця між кнопками Step over (`F10`) та Step into (`F11`) під час налагодження?
5. Які можливості надає панель Scope під час паузи на breakpoint?

## **Теоретичні відомості**

### **Відкриття DevTools**

DevTools викликаються клавішею `F12` або комбінацією `Ctrl + Shift + I` (Windows/Linux) / `Cmd + Option + I` (macOS). Для швидкого вибору елемента на сторінці використовується `Ctrl + Shift + C` / `Cmd + Option + C`.

### **Основні вкладки**

| Вкладка     | Призначення                                                |
| ----------- | ---------------------------------------------------------- |
| Elements    | Інспекція DOM та CSS, редагування у режимі реального часу  |
| Console     | Виконання JavaScript, перегляд логів, помилок, попереджень |
| Network     | Моніторинг мережевих запитів та відповідей                 |
| Sources     | Перегляд вихідного коду, точки зупинки, налагодження       |
| Application | Перегляд LocalStorage, SessionStorage, Cookies             |

### **Корисні методи Console**

```javascript
console.log("Звичайне повідомлення");
console.warn("Попередження");
console.error("Помилка");
console.table([
  { name: "Anna", age: 25 },
  { name: "Petro", age: 30 },
]);
```

Швидкий доступ до елементів через Console:

- `$('селектор')` як еквівалент `document.querySelector()`
- `$$('селектор')` як еквівалент `document.querySelectorAll()`

### **Точки зупинки (Breakpoints)**

**Line breakpoint** встановлюється кліком по номеру рядка у вкладці Sources. Виконання зупиняється перед виконанням цього рядка. Кнопки керування виконанням під час паузи:

- ▶️ **Resume** (`F8`): продовжити до наступної точки зупинки
- ⤵️ **Step over** (`F10`): наступний рядок без заходу у виклик функції
- ⬇️ **Step into** (`F11`): зайти всередину виклику функції

Під час паузи на breakpoint панель **Scope** відображає поточні значення усіх змінних, доступних у поточному контексті виконання (локальні змінні функції, замикання, глобальні). Це дозволяє побачити фактичний стан програми у момент зупинки та виявити некоректні значення.

## **Хід роботи**

У цій лабораторній роботі ви отримаєте готову HTML-сторінку із вбудованими завданнями для дослідження. Усі дії виконуються в інструментах розробника браузера, а **вихідний код файлу не змінюється**.

### **Етап 1: Підготовка**

1.1. Створіть папку для лабораторної роботи, наприклад `lab16`.

1.2. Створіть у ній файл `index.html` та скопіюйте у нього розмітку з блоку нижче. Код не змінювати, усі дослідження виконуються виключно засобами DevTools.

1.3. Відкрийте файл у браузері Google Chrome.

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Лабораторія DevTools</title>
    <style>
      body {
        font-family: sans-serif;
        max-width: 760px;
        margin: 24px auto;
        padding: 0 16px;
      }
      h1 {
        color: #1565c0;
      }
      h2 {
        margin-top: 28px;
        color: #0d47a1;
      }
      .secret-box {
        padding: 16px;
        margin: 16px 0;
        background: #e3f2fd;
        border-left: 4px solid #1565c0;
      }
      .hidden-message {
        display: none;
        color: #c62828;
        font-weight: bold;
      }
      button {
        padding: 8px 16px;
        background: #1565c0;
        color: white;
        border: none;
        cursor: pointer;
        margin-right: 8px;
      }
      .product-list {
        list-style: none;
        padding: 0;
      }
      .product-list li {
        padding: 8px;
        border-bottom: 1px solid #e0e0e0;
      }
      .card {
        padding: 12px;
        margin: 12px 0;
        border: 1px solid #e0e0e0;
        border-radius: 6px;
      }
      .card-title {
        font-weight: bold;
        color: #2e7d32;
      }
    </style>
  </head>
  <body>
    <h1>Лабораторія DevTools 🛠️</h1>

    <section>
      <h2>Завдання 1: інспекція DOM</h2>
      <div class="secret-box">
        <p>Десь у цьому блоці заховано повідомлення з кодовим словом.</p>
        <p
          class="hidden-message"
          data-secret="ACTIVATE_DEVTOOLS"
          data-level="advanced"
        >
          🎉 Кодове слово: <span id="secret-word">INSPECTOR</span>
        </p>
      </div>
      <div class="card">
        <p class="card-title">Картка №1</p>
        <p>Ціна: <span class="price">120</span> грн</p>
      </div>
      <div class="card">
        <p class="card-title">Картка №2</p>
        <p>Ціна: <span class="price">340</span> грн</p>
      </div>
      <div class="card">
        <p class="card-title">Картка №3</p>
        <p>Ціна: <span class="price">75</span> грн</p>
      </div>
    </section>

    <section>
      <h2>Завдання 2: робота з Console</h2>
      <p>
        Відкрий вкладку Console та подивись, що вивела сторінка під час
        завантаження.
      </p>
    </section>

    <section>
      <h2>Завдання 3: аналіз мережевих запитів</h2>
      <button id="loadUsersBtn">Завантажити користувачів</button>
      <button id="loadBrokenBtn">Запит з помилкою</button>
      <ul id="data-list" class="product-list"></ul>
    </section>

    <section>
      <h2>Завдання 4: налагодження через breakpoint</h2>
      <button id="calcBtn">Обчислити суму</button>
      <p id="result"></p>
    </section>

    <script>
      // Стартові повідомлення у Console
      console.log("Сторінка завантажена");
      console.warn("Це попередження для дослідження");
      console.error("Це навмисна помилка для прикладу");
      console.table([
        { id: 1, name: "Анна", role: "developer" },
        { id: 2, name: "Петро", role: "designer" },
        { id: 3, name: "Олена", role: "manager" },
      ]);

      // ========= Завдання 3 =========
      const dataList = document.getElementById("data-list");

      document
        .getElementById("loadUsersBtn")
        .addEventListener("click", async () => {
          const response = await fetch(
            "https://jsonplaceholder.typicode.com/users?_limit=5",
          );
          const users = await response.json();
          dataList.innerHTML = "";
          users.forEach((user) => {
            const li = document.createElement("li");
            li.textContent = `${user.name} (${user.email})`;
            dataList.appendChild(li);
          });
        });

      document
        .getElementById("loadBrokenBtn")
        .addEventListener("click", async () => {
          const response = await fetch(
            "https://jsonplaceholder.typicode.com/nonexistent-endpoint",
          );
          const data = await response.json();
          console.log("Дані:", data);
        });

      // ========= Завдання 4 =========
      function calculateSum(items) {
        let total = 0;
        for (let i = 0; i <= items.length; i++) {
          total += items[i].price;
        }
        return total;
      }

      document.getElementById("calcBtn").addEventListener("click", () => {
        const items = [
          { name: "Книга", price: 250 },
          { name: "Ручка", price: 30 },
          { name: "Зошит", price: 45 },
        ];
        const sum = calculateSum(items);
        document.getElementById("result").textContent = `Сума: ${sum} грн`;
      });
    </script>
  </body>
</html>
```

### **Етап 2: Виконання завдань**

---

#### **Завдання 1: Інспекція DOM та CSS (Elements)**

2.1. Відкрийте DevTools (`F12`) та перейдіть на вкладку **Elements**.

2.2. У блоці «Завдання 1» знайдіть параграф з класом `hidden-message`. Він не відображається на сторінці через властивість `display: none`. Зафіксуйте у звіті значення атрибутів `data-secret` та `data-level`.

2.3. На панелі **Styles** зніміть прапорець біля правила `display: none` (або змініть його на `display: block`). Переконайтесь, що приховане повідомлення з'явилось на сторінці.

2.4. Виділіть один з елементів `.card` у дереві Elements. На панелі **Styles** змініть колір тексту заголовка картки (`.card-title`) на червоний (`#c62828`). Переконайтесь, що зміни застосувались миттєво.

**Очікуваний результат:** ви знайшли приховане повідомлення, зафіксували значення data-атрибутів та змінили стилі картки через DevTools.

---

#### **Завдання 2: Робота з Console**

2.5. Перейдіть на вкладку **Console**. Зафіксуйте у звіті, скільки повідомлень кожного типу вивела сторінка під час завантаження (звичайних, попереджень, помилок) та скільки записів містить виведена таблиця користувачів.

2.6. Виконайте у Console такі команди (по черзі, натискаючи `Enter` після кожної):

```javascript
// Підрахуйте кількість карток на сторінці
$$(".card").length;

// Знайдіть текст головного заголовка
$("h1").textContent;
```

2.7. Виконайте код для отримання масиву цін з усіх карток та обчислення їх суми:

```javascript
const prices = Array.from($$(".price")).map((el) => Number(el.textContent));
console.table(prices);
prices.reduce((a, b) => a + b, 0);
```

Зафіксуйте у звіті отриманий масив цін та обчислену суму.

**Очікуваний результат:** ви виконали команди у Console, отримали масив цін з DOM та обчислили їх суму.

---

#### **Завдання 3: Аналіз мережевих запитів (Network)**

2.8. Перейдіть на вкладку **Network**. Якщо список запитів порожній, перезавантажте сторінку (`F5`).

2.9. Натисніть кнопку **«Завантажити користувачів»**. Знайдіть у списку запит до `jsonplaceholder.typicode.com/users` та зафіксуйте у звіті: HTTP-метод, статус відповіді, повний URL та час виконання (Time). На вкладці **Preview** подивіться розпарсений JSON та зафіксуйте, скільки об'єктів повернув сервер.

2.10. Натисніть кнопку **«Запит з помилкою»**. Знайдіть запит у Network, його статус буде **404 Not Found**. Зафіксуйте URL цього запиту.

2.11. У випадаючому списку **Throttling** оберіть **Slow 4G**, повторно натисніть **«Завантажити користувачів»** та порівняйте час виконання з попереднім значенням. Поверніть **No throttling**.

**Очікуваний результат:** ви проаналізували успішний та помилковий запити, знайшли їх параметри та спостерігали вплив throttling на час виконання.

---

#### **Завдання 4: Налагодження JavaScript (Sources)**

2.12. Натисніть кнопку **«Обчислити суму»** на сторінці. Зверніть увагу, що поле результату залишилось порожнім. Перевірте Console: там має з'явитися повідомлення `TypeError: Cannot read properties of undefined`. Це означає, що функція `calculateSum` зламалась під час виконання. Зафіксуйте у звіті повний текст цього повідомлення.

2.13. Перейдіть на вкладку **Sources** та оберіть `index.html` у дереві файлів зліва. Знайдіть функцію `calculateSum` у редакторі та поставте **line breakpoint** на рядку `total += items[i].price;` (клік по номеру рядка).

2.14. Натисніть кнопку **«Обчислити суму»** ще раз, виконання зупиниться на breakpoint. Використайте **Step over** (`F10`) для покрокового проходження циклу. На кожному кроці спостерігайте у панелі **Scope** значення змінних `i`, `total` та `items[i]`. Перші три ітерації виконуються коректно, а на наступній `items[i]` буде `undefined`, що й призведе до помилки виконання.

2.15. Знайдіть, на якій ітерації `items[i]` стає `undefined`. Зафіксуйте у звіті, у чому полягає помилка (підказка: уважно подивіться на умову циклу та порівняйте з `items.length`).

2.16. Зніміть breakpoint (повторний клік на номер рядка), щоб надалі цей рядок не зупиняв виконання.

**Очікуваний результат:** ви виявили причину помилки виконання за допомогою покрокового проходження циклу та зафіксували проблему у коді.

---

### **Етап 3: Перевірка**

3.1. Переконайтесь, що ви виконали всі чотири завдання та зробили скріншоти результатів кожного з них.

3.2. Закрийте та знову відкрийте сторінку, переконайтесь, що файл `index.html` залишився без змін (всі модифікації були тимчасовими у DevTools).

**Результат:** ви провели дослідження сторінки за допомогою DevTools, не змінюючи її вихідний код.

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням.

2. **Відповіді на контрольні запитання**.

3. **Виконання роботи:** для кожного з чотирьох завдань скріншоти ключових результатів та зафіксована інформація, яку вимагав текст відповідного завдання.

4. **Висновки** про отримані навички та сфери застосування DevTools.

## **Типові помилки та їх вирішення**

| Проблема                                               | Рішення                                                                               |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------- |
| У вкладці Network не видно жодних запитів              | Перезавантажте сторінку (`F5`) при відкритих DevTools, запис починається після цього  |
| Breakpoint не спрацьовує                               | Переконайтесь, що breakpoint встановлено всередині функції; натисніть кнопку повторно |
| Зміни у вкладці Elements зникли після перезавантаження | Це нормальна поведінка, DevTools не змінюють файли на диску, тільки поточну сторінку  |
| Кнопки Step over / Step into неактивні                 | Кнопки доступні лише коли виконання зупинено на breakpoint                            |

## **Додаткові матеріали**

1. Chrome DevTools, офіційна документація: https://developer.chrome.com/docs/devtools

2. Console API Reference: https://developer.chrome.com/docs/devtools/console/api

3. JavaScript Debugging (uk.javascript.info): https://uk.javascript.info/debugging-chrome

4. JSONPlaceholder (тестовий API): https://jsonplaceholder.typicode.com
