# **Лабораторна робота №13**

**Тема:** Збереження даних у браузері за допомогою `localStorage`

**Мета:** Закріпити практичні навички роботи з API `localStorage`: збереження та зчитування простих рядкових значень, серіалізація складних структур даних через `JSON.stringify`/`JSON.parse`, відновлення стану сторінки після перезавантаження.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер (Chrome, Firefox, Edge); текстовий редактор (VS Code, Sublime Text або інший)

## **Контрольні запитання**

1. Які типи даних можна напряму зберігати в `localStorage`? Що станеться, якщо передати в `setItem` число або об'єкт?
2. Що повертає `localStorage.getItem(key)`, якщо запису з таким ключем не існує?
3. Для чого використовують `JSON.stringify` та `JSON.parse` при роботі з `localStorage`?
4. Чому зчитування з `localStorage` та виклик `JSON.parse` доцільно загортати у `try...catch`?
5. Як переглянути вміст `localStorage` у браузері без написання коду?

## **Теоретичні відомості**

### **API `localStorage`**

`localStorage` — вбудований об'єкт браузера, що надає постійне сховище типу "ключ–значення" для конкретного домену. Дані зберігаються навіть після закриття вкладки чи браузера — до явного видалення.

| Виклик                             | Призначення                                    |
| ---------------------------------- | ---------------------------------------------- |
| `localStorage.setItem(key, value)` | Зберігає рядок `value` під ключем `key`        |
| `localStorage.getItem(key)`        | Повертає рядок або `null`, якщо ключ відсутній |
| `localStorage.removeItem(key)`     | Видаляє один запис за ключем                   |
| `localStorage.clear()`             | Повністю очищує сховище для поточного домену   |

`localStorage` зберігає **лише рядки**. Якщо передати число — воно буде автоматично перетворене на рядок, тому при зчитуванні потрібно явно привести тип назад через `Number(...)`. Якщо ж передати об'єкт напряму — у сховищі опиниться рядок `"[object Object]"`, а оригінальні дані буде втрачено.

### **Серіалізація складних даних**

Масиви та об'єкти неможливо зберігати напряму — їх потрібно перетворювати на рядок JSON:

```js
// Запис
const user = { name: "Марія", age: 22 };
localStorage.setItem("user", JSON.stringify(user));

// Читання
const raw = localStorage.getItem("user");
const restored = raw ? JSON.parse(raw) : null;
console.log(restored.name); // "Марія"
```

Якщо у сховищі опиниться некоректний JSON (наприклад, через ручне редагування в DevTools), `JSON.parse` кине `SyntaxError`. Тому читання варто загортати у `try...catch` і повертати значення за замовчуванням у випадку помилки.

### **Перегляд `localStorage` у DevTools**

Відкрийте DevTools (`F12`) → вкладка **Application** (у Chrome/Edge) або **Storage** (у Firefox) → розділ **Local Storage** → оберіть домен поточної сторінки. Там ви побачите таблицю всіх збережених пар «ключ–значення» і зможете вручну їх редагувати або видаляти.

## **Хід роботи**

У цій лабораторній роботі ви виконаєте два невеликих завдання. Кожне завдання — окремий HTML-файл. HTML і CSS уже надані у стартовому коді — ваша задача написати лише JavaScript.

### **Етап 1: Підготовка**

1.1. Створіть папку для лабораторної роботи, наприклад `lab13`.

1.2. Створіть файли для кожного завдання відповідно до структури нижче.

**Очікувана структура:**

```
lab13/
├── settings.html
└── favorites.html
```

### **Етап 2: Виконання завдань**

---

#### **Завдання 1: Налаштування профілю (settings.html)**

Створіть сторінку з формою налаштувань, яка запам'ятовує введені користувачем значення та автоматично відновлює їх при наступному відкритті сторінки.

**Що повинна робити сторінка:**

1. Відображати форму з трьома полями: ім'я (`text`), мова інтерфейсу (`<select>` з варіантами «Українська», «Англійська», «Польська»), розмір шрифту (`range` від 12 до 24).
2. При натисканні кнопки «Зберегти» — зберігати значення всіх трьох полів у `localStorage` під окремими ключами.
3. При відкритті/перезавантаженні сторінки — автоматично читати збережені значення зі сховища та підставляти їх у поля форми.
4. Кнопка «Скинути» — видаляє записи зі сховища та очищує форму (повертає поля до значень за замовчуванням).

**Стартовий код:**

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>⚙️ Налаштування профілю</title>
    <style>
      body {
        font-family: sans-serif;
        padding: 30px;
        max-width: 400px;
      }
      label {
        display: block;
        margin-top: 12px;
        font-weight: bold;
      }
      input[type="text"],
      select {
        width: 100%;
        padding: 8px;
        margin-top: 4px;
        box-sizing: border-box;
      }
      input[type="range"] {
        width: 100%;
      }
      button {
        margin-top: 16px;
        margin-right: 8px;
        padding: 10px 20px;
        cursor: pointer;
      }
      #status {
        margin-top: 12px;
        color: #2e7d32;
        font-weight: bold;
      }
    </style>
  </head>
  <body>
    <h1>⚙️ Налаштування профілю</h1>

    <label for="nameInput">Ім'я:</label>
    <input type="text" id="nameInput" placeholder="Введіть ім'я" />

    <label for="langSelect">Мова інтерфейсу:</label>
    <select id="langSelect">
      <option value="uk">Українська</option>
      <option value="en">Англійська</option>
      <option value="pl">Польська</option>
    </select>

    <label for="sizeRange"
      >Розмір шрифту: <span id="sizeValue">16</span>px</label
    >
    <input type="range" id="sizeRange" min="12" max="24" value="16" />

    <button id="saveBtn">💾 Зберегти</button>
    <button id="resetBtn">🔄 Скинути</button>

    <p id="status"></p>

    <script>
      const NAME_KEY = "profile_name";
      const LANG_KEY = "profile_lang";
      const SIZE_KEY = "profile_size";

      const nameInput = document.getElementById("nameInput");
      const langSelect = document.getElementById("langSelect");
      const sizeRange = document.getElementById("sizeRange");
      const sizeValue = document.getElementById("sizeValue");
      const saveBtn = document.getElementById("saveBtn");
      const resetBtn = document.getElementById("resetBtn");
      const statusEl = document.getElementById("status");

      // Оновлення підпису поруч з повзунком (уже готово)
      sizeRange.addEventListener("input", () => {
        sizeValue.textContent = sizeRange.value;
      });

      // TODO 1: При завантаженні сторінки прочитати збережені значення
      //         з localStorage за трьома ключами (NAME_KEY, LANG_KEY, SIZE_KEY).
      //         Якщо значення існує (не null) — підставити його у відповідне поле.
      //         Не забудьте після оновлення sizeRange.value оновити і sizeValue.textContent.

      // TODO 2: Додати обробник кліку на saveBtn,
      //         який зберігає поточні значення трьох полів у localStorage
      //         та виводить у statusEl повідомлення "Налаштування збережено ✅".

      // TODO 3: Додати обробник кліку на resetBtn,
      //         який видаляє всі три ключі з localStorage (removeItem),
      //         повертає поля до значень за замовчуванням
      //         (порожній рядок для імені, "uk" для мови, 16 для розміру)
      //         та очищує statusEl.
    </script>
  </body>
</html>
```

**Перевірка:**

- Введіть ім'я, оберіть мову, змініть розмір шрифту → натисніть «Зберегти» → з'явиться повідомлення.
- Натисніть `F5` → усі три поля повинні автоматично містити раніше збережені значення.
- Відкрийте DevTools → **Application** → **Local Storage** → переконайтесь, що там присутні три ключі: `profile_name`, `profile_lang`, `profile_size`.
- Натисніть «Скинути» → поля очищуються, записи у DevTools зникають.

**Довідка по API — Завдання 1:**

| Виклик                                  | Що робить                                      |
| --------------------------------------- | ---------------------------------------------- |
| `localStorage.setItem(key, value)`      | Зберігає рядок `value` під ключем `key`        |
| `localStorage.getItem(key)`             | Повертає рядок або `null`, якщо ключ відсутній |
| `localStorage.removeItem(key)`          | Видаляє один запис за ключем                   |
| `element.value`                         | Поточне значення поля введення або `<select>`  |
| `element.textContent = value`           | Встановлює текстовий вміст елемента            |
| `element.addEventListener("click", fn)` | Додає обробник кліку                           |

> **Підказка:** розмір шрифту у `localStorage` зберігається як рядок. Для підстановки в `input[type="range"]` це не проблема — присвоєння `sizeRange.value = "18"` працює коректно.

---

#### **Завдання 2: Список обраних книг (favorites.html)**

Створіть сторінку зі списком обраних книг. Список повинен зберігатися у `localStorage` у вигляді **масиву об'єктів**, що вимагає серіалізації через `JSON.stringify`/`JSON.parse`.

**Що повинна робити сторінка:**

1. Відображати форму з двома полями: назва книги та автор.
2. При натисканні «Додати» — створювати об'єкт `{ title, author }`, додавати його до масиву та зберігати оновлений масив у `localStorage`.
3. Відображати всі збережені книги у списку `<ul>` у форматі «Назва — Автор».
4. Біля кожної книги — кнопка «🗑️», що видаляє саме цю книгу зі списку та зі сховища.
5. При перезавантаженні сторінки — список автоматично відновлюється зі сховища.

**Стартовий код:**

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>📚 Обрані книги</title>
    <style>
      body {
        font-family: sans-serif;
        padding: 30px;
        max-width: 500px;
      }
      input {
        padding: 8px;
        margin-right: 6px;
      }
      button {
        padding: 8px 14px;
        cursor: pointer;
      }
      ul {
        list-style: none;
        padding: 0;
        margin-top: 20px;
      }
      li {
        padding: 10px;
        background: #f5f5f5;
        margin-bottom: 6px;
        border-radius: 4px;
        display: flex;
        justify-content: space-between;
        align-items: center;
      }
      li button {
        background: #ffcdd2;
        border: none;
        border-radius: 4px;
      }
      #empty {
        color: #888;
        font-style: italic;
      }
    </style>
  </head>
  <body>
    <h1>📚 Обрані книги</h1>

    <input
      type="text"
      id="titleInput"
      placeholder="Назва книги"
      maxlength="60"
    />
    <input type="text" id="authorInput" placeholder="Автор" maxlength="40" />
    <button id="addBtn">➕ Додати</button>

    <ul id="bookList"></ul>
    <p id="empty" style="display: none">Список порожній</p>

    <script>
      const BOOKS_KEY = "favorite_books";

      const titleInput = document.getElementById("titleInput");
      const authorInput = document.getElementById("authorInput");
      const addBtn = document.getElementById("addBtn");
      const bookList = document.getElementById("bookList");
      const empty = document.getElementById("empty");

      // TODO 1: Написати функцію loadBooks(),
      //         яка читає рядок з localStorage за ключем BOOKS_KEY.
      //         Якщо його немає (null) — повернути порожній масив [].
      //         Якщо є — розпарсити рядок через JSON.parse у блоці try...catch
      //         та повернути результат. У catch-блоці — теж повернути порожній масив.

      // TODO 2: Написати функцію saveBooks(books),
      //         яка серіалізує масив через JSON.stringify
      //         та записує його у localStorage під ключем BOOKS_KEY.

      // TODO 3: Написати функцію renderBooks(books), яка:
      //         - очищує вміст bookList
      //         - керує видимістю елемента empty (показувати, коли books порожній)
      //         - для кожної книги (з її індексом) створює <li>
      //           з текстом у форматі "Назва — Автор" та кнопкою "🗑️".
      //         - при кліку на кнопку: видалити книгу з масиву за індексом,
      //           викликати saveBooks(books) та перемалювати через renderBooks(books).

      // TODO 4: Ініціалізація — завантажити масив через loadBooks
      //         у змінну books та викликати renderBooks(books).

      // TODO 5: Додати обробник на addBtn:
      //         - прочитати та обрізати (trim) значення обох полів
      //         - якщо хоча б одне порожнє — нічого не робити (return)
      //         - додати об'єкт { title, author } до масиву books
      //         - викликати saveBooks і renderBooks
      //         - очистити обидва поля введення
    </script>
  </body>
</html>
```

**Перевірка:**

- Додайте 2–3 книги → кожна з'являється у списку.
- Натисніть `F5` → усі книги залишаються на місці.
- Натисніть «🗑️» біля однієї з книг → вона зникає зі списку та зі сховища.
- Видаліть усі книги → з'явиться напис «Список порожній».
- Відкрийте DevTools → **Application** → **Local Storage** → перевірте, що під ключем `favorite_books` зберігається рядок формату `[{"title":"...","author":"..."}, ...]`.
- У DevTools → **Console** виконайте: `JSON.parse(localStorage.getItem("favorite_books"))` — повинен вивестися масив об'єктів.

**Довідка по API — Завдання 2:**

| Виклик                                | Що робить                                                          |
| ------------------------------------- | ------------------------------------------------------------------ |
| `JSON.stringify(value)`               | Серіалізує об'єкт або масив у рядок JSON                           |
| `JSON.parse(string)`                  | Відновлює об'єкт або масив з рядка JSON; кидає виняток при помилці |
| `array.push(item)`                    | Додає елемент у кінець масиву                                      |
| `array.splice(index, 1)`              | Видаляє один елемент за вказаним індексом                          |
| `array.forEach((item, index) => ...)` | Перебирає масив, надаючи і елемент, і його індекс                  |
| `document.createElement("li")`        | Створює новий DOM-елемент                                          |
| `parent.appendChild(child)`           | Додає дочірній елемент у кінець `parent`                           |
| `element.innerHTML = ""`              | Очищує весь вміст елемента                                         |
| `element.style.display = "none"`      | Приховує елемент (`"block"` — показує знову)                       |

> **Підказка:** щоб кнопка видалення знала індекс своєї книги, використайте замикання — індекс з `forEach((book, index) => { ... })` буде доступний всередині обробника `click`.

---

### **Етап 3: Перевірка**

3.1. Відкрийте обидва файли у браузері та переконайтесь, що вони працюють відповідно до опису.

3.2. Для кожного файлу перевірте, що дані дійсно зберігаються — перезавантажте сторінку (`F5`) та переконайтесь, що стан відновлюється.

3.3. Перегляньте вміст `localStorage` у DevTools → Application → Local Storage для обох сторінок.

**Результат:** 2 HTML-сторінки, що зберігають стан між перезавантаженнями за допомогою `localStorage` — одна з простими рядковими значеннями, друга з масивом об'єктів через JSON.

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням

2. **Відповіді на контрольні питання** (коротко, по суті)

3. **Виконання завдань:**

   3.1. Для кожного завдання (1–2): скріншот коду JavaScript та скріншот сторінки у браузері зі збереженими даними

   3.2. Скріншот вмісту `localStorage` у DevTools (Application → Local Storage) — хоча б для одного завдання

   3.3. Скріншот перевірки відновлення стану після `F5` — хоча б для одного завдання

4. **Висновки** (що навчились робити, які навички отримали, з якими труднощами зіткнулись)

## **Типові помилки та їх вирішення**

| Проблема                                                                                                 | Рішення                                                                                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Після `F5` поля форми порожні, хоча кнопка «Зберегти» працює                                             | Не написано код читання зі сховища при завантаженні сторінки (TODO 1 у Завданні 1)                                                                                                                                                 |
| Після відновлення налаштувань повзунок змістився, але підпис біля нього показує старе значення           | Після `sizeRange.value = savedSize` потрібно також оновити `sizeValue.textContent = savedSize`                                                                                                                                     |
| Кнопка «Скинути» очищує поля, але після `F5` старі значення повертаються                                 | У обробнику `resetBtn` потрібно викликати `localStorage.removeItem` для всіх трьох ключів, а не лише очищати поля форми                                                                                                            |
| У `localStorage` під ключем `favorite_books` замість JSON зберігається `[object Object],[object Object]` | Пропущено виклик `JSON.stringify(books)` у функції `saveBooks` — масив перетворюється на рядок автоматично і втрачає структуру                                                                                                     |
| `Uncaught SyntaxError: Unexpected token ... in JSON` при завантаженні сторінки                           | У сховищі зберігся пошкоджений JSON. Саме для цього `JSON.parse` у `loadBooks` обгортається у `try...catch` — перевірте, що блок `catch` присутній і повертає `[]`                                                                 |
| Після видалення всіх книг напис «Список порожній» не з'являється                                         | У `renderBooks` не оновлюється `empty.style.display` залежно від `books.length` — додайте перевірку на порожній масив                                                                                                              |
| При кліку на кнопку «🗑️» видаляється не та книга, яку було обрано                                        | У обробнику використовується індекс, що обчислюється на момент кліку (наприклад, через `indexOf` або змінну циклу `for`). Використовуйте параметр `index` з `forEach((book, index) => ...)` — він коректно захоплюється замиканням |
| Після додавання книги сторінка відображає дві однакові записи (або дублі при кожному кліку)              | У `renderBooks` не очищується `bookList.innerHTML` перед перемальовуванням — до існуючих `<li>` додаються нові                                                                                                                     |

## **Додаткові матеріали**

1. localStorage (uk.javascript.info) — https://uk.javascript.info/localstorage

2. JSON методи, toJSON (uk.javascript.info) — https://uk.javascript.info/json

3. JavaScript Tutorial (W3Schools, українською) — https://w3schoolsua.github.io/js/index.html
