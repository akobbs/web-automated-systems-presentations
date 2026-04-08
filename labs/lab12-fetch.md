# **Лабораторна робота №12**

**Тема:** Отримання даних з API

**Мета:** Набути практичні навички роботи з Fetch API: виконання GET- та POST-запитів, обробка JSON-відповідей, перевірка статусу відповіді та відображення отриманих даних у DOM.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер (Chrome, Firefox, Edge); текстовий редактор (VS Code, Sublime Text або інший)

## **Контрольні запитання**

1. Що таке Fetch API і яку структуру повертає виклик `fetch(url)`?
2. Чому для отримання JSON-даних потрібно два `await` — один для `fetch`, інший для `.json()`?
3. Чому `fetch` не кидає виняток при статусі 404 або 500? Як правильно перевірити успішність відповіді?
4. Чим відрізняється GET-запит від POST-запиту? Які додаткові параметри потрібно передати у `fetch` для POST?
5. Навіщо вказувати заголовок `Content-Type: application/json` при надсиланні даних на сервер?

## **Теоретичні відомості**

### **Fetch API**

`fetch` — вбудована браузерна функція для виконання HTTP-запитів. Вона повертає `Promise`, що дозволяє використовувати її разом з `async/await`. Отримання даних відбувається у два кроки: спочатку сервер надсилає заголовки відповіді, потім — тіло. Саме тому потрібно два `await`:

```js
const response = await fetch("https://api.example.com/users");
// response — об'єкт з метаданими (статус, заголовки)

const users = await response.json();
// users — розпарсений JS-об'єкт або масив
```

### **Перевірка статусу відповіді**

`fetch` завершується успішно (резолвить `Promise`) навіть якщо сервер повернув помилку — наприклад, `404 Not Found` або `500 Internal Server Error`. Виняток кидається лише при мережевій помилці (відсутність з'єднання, недоступний хост).

Тому необхідно явно перевіряти успішність відповіді через `response.ok` (значення `true` для статусів 200–299):

```js
const response = await fetch(url);

if (!response.ok) {
  throw new Error(`Помилка сервера: ${response.status}`);
}

const data = await response.json();
```

### **POST-запит**

Для надсилання даних на сервер потрібно передати у `fetch` другий аргумент — об'єкт з налаштуваннями:

```js
const response = await fetch("https://api.example.com/posts", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
  },
  body: JSON.stringify({ title: "Заголовок", body: "Текст" }),
});
```

- `method` — HTTP-метод (`"POST"`, `"PUT"`, `"DELETE"` тощо)
- `headers` — метадані запиту; `Content-Type: application/json` повідомляє серверу, що тіло є JSON
- `body` — тіло запиту у вигляді рядка; `JSON.stringify` перетворює JS-об'єкт у JSON-рядок

### **JSONPlaceholder**

`https://jsonplaceholder.typicode.com` — публічний навчальний REST API. Він приймає GET-, POST-, PUT-, DELETE-запити та повертає реалістичні відповіді, але нічого не зберігає. Доступні ресурси: `/posts`, `/users`, `/comments`, `/todos`.

## **Хід роботи**

### **Етап 1: Підготовка**

1.1. Створіть папку для лабораторної роботи, наприклад `lab12`.

1.2. Всередині папки створіть два файли: `task1.html` та `task2.html`.

**Очікувана структура:**

```
lab12/
├── task1.html
└── task2.html
```

---

### **Завдання 1: Завантаження та відображення списку користувачів**

Реалізуйте сторінку, яка за натисканням кнопки завантажує список користувачів з API та відображає їх на сторінці.

#### **Заготовка HTML (task1.html)**

Скопіюйте наведену нижче заготовку у файл `task1.html` та **реалізуйте функцію `loadUsers`** відповідно до інструкцій у коментарях:

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Список користувачів</title>
    <style>
      body {
        font-family: sans-serif;
        max-width: 700px;
        margin: 40px auto;
        padding: 0 16px;
      }
      button {
        padding: 10px 24px;
        font-size: 16px;
        cursor: pointer;
      }
      #status {
        margin: 12px 0;
        color: #555;
      }
      .user-card {
        border: 1px solid #ddd;
        border-radius: 8px;
        padding: 12px 16px;
        margin-bottom: 10px;
      }
      .user-card h3 {
        margin: 0 0 4px;
      }
      .user-card p {
        margin: 0;
        color: #555;
        font-size: 14px;
      }
      .error {
        color: #c62828;
      }
    </style>
  </head>
  <body>
    <h1>Користувачі</h1>
    <button id="load-btn">Завантажити користувачів</button>
    <p id="status"></p>
    <div id="user-list"></div>

    <script>
      async function loadUsers() {
        const status = document.getElementById("status");
        const list = document.getElementById("user-list");

        // ??? Відобразіть у #status текст "Завантаження..."
        // ??? Очистіть вміст #user-list

        try {
          // ??? Виконайте GET-запит до "https://jsonplaceholder.typicode.com/users"
          // ??? Перевірте response.ok; якщо хибне — киньте помилку з текстом статусу
          // ??? Отримайте масив користувачів з тіла відповіді
          // ??? Оновіть #status: "Завантажено: N користувачів" (де N — реальна кількість)
          // ??? Для кожного користувача створіть div.user-card з:
          //     <h3> — ім'я (user.name)
          //     <p>  — email (user.email)
          //     <p>  — місто (user.address.city)
          // ??? Додайте кожну картку до #user-list
        } catch (err) {
          // ??? Відобразіть у #status повідомлення про помилку (err.message)
          //     Додайте клас "error" до елемента #status через classList.add("error")
        }
      }

      document.getElementById("load-btn").addEventListener("click", loadUsers);
    </script>
  </body>
</html>
```

#### **Очікуваний результат**

Після натискання кнопки на сторінці мають з'явитися 10 карток. Приклад однієї картки:

```
┌─────────────────────────────────────┐
│ Leanne Graham                        │
│ Sincere@april.biz                    │
│ Gwenborough                          │
└─────────────────────────────────────┘
```

> **Підказка:** Структура об'єкта користувача: `{ id, name, email, address: { city, ... }, ... }`. Відкрийте в браузері `https://jsonplaceholder.typicode.com/users`, щоб побачити повну структуру даних.

---

### **Завдання 2: Форма створення публікації**

Реалізуйте сторінку з формою, яка надсилає POST-запит та відображає відповідь сервера.

#### **Заготовка HTML (task2.html)**

Скопіюйте наведену нижче заготовку у файл `task2.html` та **реалізуйте функцію `submitPost`** відповідно до інструкцій у коментарях:

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Нова публікація</title>
    <style>
      body {
        font-family: sans-serif;
        max-width: 600px;
        margin: 40px auto;
        padding: 0 16px;
      }
      label {
        display: block;
        margin-top: 14px;
        font-weight: bold;
      }
      input,
      textarea {
        width: 100%;
        padding: 8px;
        margin-top: 4px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 15px;
        box-sizing: border-box;
      }
      textarea {
        height: 100px;
        resize: vertical;
      }
      button {
        margin-top: 16px;
        padding: 10px 28px;
        font-size: 15px;
        cursor: pointer;
      }
      #result {
        margin-top: 20px;
        padding: 12px 16px;
        border-radius: 6px;
        display: none;
      }
      #result.success {
        background: #e8f5e9;
        border: 1px solid #388e3c;
      }
      #result.error {
        background: #fce4ec;
        border: 1px solid #c62828;
      }
    </style>
  </head>
  <body>
    <h1>Нова публікація</h1>

    <label for="post-title">Заголовок</label>
    <input
      type="text"
      id="post-title"
      placeholder="Введіть заголовок"
      required
    />

    <label for="post-body">Текст публікації</label>
    <textarea id="post-body" placeholder="Введіть текст"></textarea>

    <button id="submit-btn">Опублікувати</button>

    <div id="result"></div>

    <script>
      async function submitPost() {
        const title = document.getElementById("post-title").value.trim();
        const postBody = document.getElementById("post-body").value.trim();
        const result = document.getElementById("result");

        // ??? Перевірте, що title не порожній.
        //     Якщо порожній — виведіть alert з відповідним повідомленням і завершіть функцію

        // ??? Заблокуйте кнопку (#submit-btn) і змініть її текст на "Надсилання..."
        // ??? Приховайте #result (style.display = "none")
        //     Очистіть попередні класи: result.className = ""

        try {
          // ??? Виконайте POST-запит до "https://jsonplaceholder.typicode.com/posts"
          //     Передайте: method, headers (Content-Type: application/json),
          //     body — JSON.stringify об'єкта { title, body: postBody, userId: 1 }
          // ??? Перевірте response.ok; якщо хибне — киньте помилку з текстом статусу
          // ??? Отримайте об'єкт створеної публікації з тіла відповіді
          // ??? Відобразіть у #result:
          //     - Додайте клас "success", встановіть display: "block"
          //     - Виведіть текст: "✅ Публікацію створено! ID: {created.id}, Заголовок: {created.title}"
        } catch (err) {
          // ??? Відобразіть у #result:
          //     - Додайте клас "error", встановіть display: "block"
          //     - Виведіть текст: "❌ Помилка: {err.message}"
        } finally {
          // ??? Розблокуйте кнопку та відновіть її початковий текст "Опублікувати"
        }
      }

      document
        .getElementById("submit-btn")
        .addEventListener("click", submitPost);
    </script>
  </body>
</html>
```

#### **Очікуваний результат**

Після натискання «Опублікувати» з непорожнім заголовком на сторінці з'являється повідомлення:

```
✅ Публікацію створено! ID: 101, Заголовок: Мій заголовок
```

> **Підказка:** JSONPlaceholder при успішному POST-запиті повертає об'єкт зі статусом `201 Created` та полями `id`, `title`, `body`, `userId`. Зверніть увагу: `response.ok` буде `true` для будь-якого статусу 200–299, тому перевірка спрацює коректно. ID завжди буде `101` — це нормальна поведінка навчального сервісу.

---

### **Етап 3: Перевірка**

3.1. Відкрийте `task1.html` у браузері та натисніть кнопку — картки користувачів мають з'явитися без помилок у консолі.

3.2. Відкрийте `task2.html`, введіть заголовок та текст, натисніть «Опублікувати» — має з'явитися повідомлення про успіх.

3.3. Перевірте обробку помилок: у `task2.html` очистіть поле заголовка та натисніть «Опублікувати» — має з'явитися `alert`.

3.4. Відкрийте DevTools → вкладка **Network**. Переконайтесь, що запити відображаються, а їхній статус — `200` (GET) та `201` (POST).

**Результат:** два HTML-файли з робочими асинхронними запитами до публічного API.

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням

2. **Відповіді на контрольні питання** (коротко, по суті)

3. **Виконання роботи:**

   3.1. Скріншот коду функції `loadUsers` (task1.html)

   3.2. Скріншот сторінки task1.html з відображеними картками користувачів

   3.3. Скріншот коду функції `submitPost` (task2.html)

   3.4. Скріншот сторінки task2.html після успішного надсилання форми

   3.5. Скріншот вкладки Network у DevTools з виконаними запитами

4. **Висновки** (що навчились робити, які навички отримали, з якими труднощами зіткнулись)

## **Типові помилки та їх вирішення**

| Проблема                                      | Рішення                                                                        |
| --------------------------------------------- | ------------------------------------------------------------------------------ |
| `fetch` не кидає виняток, хоча статус — 404   | `fetch` не кидає виняток для 4xx/5xx. Перевіряйте `response.ok` явно           |
| Дані не відображаються після другого `await`  | Переконайтесь, що викликаєте `response.json()`, а не `response.text()`         |
| POST-запит надсилає порожнє тіло              | Перевірте, що передали `body: JSON.stringify(...)` та заголовок `Content-Type` |
| Кнопка залишається заблокованою після помилки | Розблокування кнопки має бути у блоці `finally`, а не лише в `try`             |
| Помилка CORS у консолі                        | Використовуйте лише дозволені публічні API (JSONPlaceholder підтримує CORS)    |

## **Додаткові матеріали**

1. Fetch API (основи та обробка помилок) — https://uk.javascript.info/fetch
2. JavaScript Tutorial (W3Schools, українською) — https://w3schoolsua.github.io/js/index.html
