# **Лабораторна робота №15**

**Тема:** Робота з формами та валідація на JavaScript

**Мета:** Закріпити практичні навички обробки форм засобами JavaScript: отримання доступу до полів, збір даних через `FormData`, перевірка формату за допомогою регулярних виразів, обробка події `submit` зі скасуванням стандартної поведінки браузера.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер (Chrome, Firefox, Edge); текстовий редактор (VS Code, Sublime Text або інший)

## **Контрольні запитання**

1. Навіщо викликати `event.preventDefault()` в обробнику події `submit`? Що станеться, якщо цього не зробити?
2. Як отримати значення поля `<select>` з JavaScript? Що поверне `.value`, якщо обрана опція з порожнім `value=""`?
3. Що повертає метод `.test()` регулярного виразу? Наведіть приклад його використання.
4. Опишіть одним-двома реченнями, що робить конструкція `Object.fromEntries(new FormData(form).entries())`.
5. Чому клієнтська валідація не є заміною серверної?

## **Теоретичні відомості**

### **Обробка події submit**

За замовчуванням при натисканні кнопки `<button type="submit">` браузер відправляє форму на сервер і перезавантажує сторінку. Для обробки форми засобами JavaScript цю поведінку потрібно скасувати через `event.preventDefault()`:

```js
const form = document.getElementById("my-form");

form.addEventListener("submit", (event) => {
  event.preventDefault();
  // Далі — власна логіка обробки
});
```

Якщо потрібно повністю вимкнути вбудовану HTML5-валідацію (наприклад, щоб замість браузерних повідомлень показувати власні), до тега `<form>` додають атрибут `novalidate`:

```html
<form id="my-form" novalidate>
  <!-- поля -->
</form>
```

### **Збір даних форми через FormData**

Об'єкт `FormData` автоматично збирає всі пари «name — value» з форми. У поєднанні з `Object.fromEntries()` це дає зручний об'єкт з даними:

```js
const formData = new FormData(form);
const data = Object.fromEntries(formData.entries());
// { username: "Anton", email: "user@example.com", ... }
```

### **Регулярні вирази для перевірки формату**

Регулярний вираз — шаблон для перевірки відповідності рядка певній структурі. Метод `.test()` повертає `true` або `false`:

```js
const phonePattern = /^\+380\d{9}$/;
phonePattern.test("+380671234567"); // true
phonePattern.test("0671234567"); // false
```

| Символ  | Значення             |
| ------- | -------------------- |
| `^`     | Початок рядка        |
| `$`     | Кінець рядка         |
| `\d`    | Будь-яка цифра (0–9) |
| `{n}`   | Рівно n разів        |
| `{n,m}` | Від n до m разів     |

### **Відображення помилок біля полів**

Типовий патерн — поруч з кожним полем розмістити порожній `<span>` або `<div>` з унікальним `id`, а при валідації заповнювати його текстом помилки:

```html
<input type="email" id="email" name="email" />
<span class="error" id="email-error"></span>
```

```js
document.getElementById("email-error").textContent = "Некоректний email";
```

## **Хід роботи**

У цій лабораторній роботі ви реалізуєте обробку та валідацію **форми замовлення послуги** засобами JavaScript. HTML та CSS вже підготовлені — ваше завдання написати лише JavaScript-код.

### **Етап 1: Підготовка**

1.1. Створіть папку для лабораторної роботи, наприклад `lab15`.

1.2. Створіть файл `index.html` з кодом, наведеним в Етапі 2.

1.3. Створіть файл `script.js` (спочатку порожній — його ви будете наповнювати в Етапі 3).

**Очікувана структура:**

```
lab15/
├── index.html
└── script.js
```

### **Етап 2: HTML та CSS-розмітка**

Створіть файл `index.html` з наступним вмістом (нічого в цьому файлі змінювати не потрібно):

```html
<!DOCTYPE html>
<html lang="uk">
  <head>
    <meta charset="UTF-8" />
    <title>Замовлення послуги</title>
    <style>
      body {
        font-family: sans-serif;
        max-width: 500px;
        margin: 40px auto;
        padding: 20px;
      }
      h1 {
        color: #1565c0;
      }
      .form-row {
        margin-bottom: 16px;
      }
      label {
        display: block;
        margin-bottom: 4px;
        font-weight: bold;
      }
      input[type="text"],
      input[type="email"],
      input[type="tel"],
      select,
      textarea {
        width: 100%;
        padding: 8px;
        border: 1px solid #ccc;
        border-radius: 4px;
        font-size: 14px;
        box-sizing: border-box;
      }
      textarea {
        resize: vertical;
        min-height: 80px;
      }
      .error {
        display: block;
        color: #c62828;
        font-size: 13px;
        margin-top: 4px;
        min-height: 18px;
      }
      button {
        padding: 10px 20px;
        background: #1565c0;
        color: white;
        border: none;
        border-radius: 4px;
        font-size: 14px;
        cursor: pointer;
      }
      button:hover {
        background: #0d47a1;
      }
      #result {
        margin-top: 20px;
        padding: 12px;
        background: #e8f5e9;
        border: 1px solid #2e7d32;
        border-radius: 4px;
        color: #2e7d32;
        display: none;
      }
      #result.visible {
        display: block;
      }
      #result pre {
        margin: 8px 0 0;
        font-size: 13px;
        white-space: pre-wrap;
      }
    </style>
  </head>
  <body>
    <h1>🛠️ Замовлення послуги</h1>

    <form id="order-form" novalidate>
      <div class="form-row">
        <label for="client-name">Ім'я та прізвище:</label>
        <input type="text" id="client-name" name="clientName" />
        <span class="error" id="client-name-error"></span>
      </div>

      <div class="form-row">
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" />
        <span class="error" id="email-error"></span>
      </div>

      <div class="form-row">
        <label for="phone">Телефон (у форматі +380XXXXXXXXX):</label>
        <input type="tel" id="phone" name="phone" placeholder="+380671234567" />
        <span class="error" id="phone-error"></span>
      </div>

      <div class="form-row">
        <label for="service">Тип послуги:</label>
        <select id="service" name="service">
          <option value="">-- Оберіть послугу --</option>
          <option value="consultation">Консультація</option>
          <option value="development">Розробка</option>
          <option value="support">Технічна підтримка</option>
        </select>
        <span class="error" id="service-error"></span>
      </div>

      <div class="form-row">
        <label for="comment">Коментар:</label>
        <textarea
          id="comment"
          name="comment"
          placeholder="Опишіть деталі замовлення"
        ></textarea>
        <span class="error" id="comment-error"></span>
      </div>

      <button type="submit">Надіслати замовлення</button>
    </form>

    <div id="result">
      <strong>✅ Замовлення успішно надіслано!</strong>
      <pre id="result-data"></pre>
    </div>

    <script src="script.js"></script>
  </body>
</html>
```

> **Примітка:** атрибут `novalidate` у тега `<form>` вимикає вбудовану HTML5-валідацію, щоб весь контроль було реалізовано засобами JavaScript.

### **Етап 3: Реалізація JavaScript**

Відкрийте файл `script.js` та реалізуйте логіку обробки форми. Нижче наведено код-скелет із TODO-коментарями — скопіюйте його у файл та послідовно заповніть кожен блок.

```js
// Отримуємо форму, блок результату та блок для виведення даних
const form = document.getElementById("order-form");
const resultBox = document.getElementById("result");
const resultData = document.getElementById("result-data");

// Регулярні вирази для перевірки формату
const emailPattern = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
const phonePattern = /^\+380\d{9}$/;

// TODO 1: Додайте обробник події 'submit' на форму.
// Всередині обробника виконайте такі кроки:
//   1. Скасуйте стандартну поведінку браузера через event.preventDefault()
//   2. Сховайте блок результату, прибравши клас 'visible' у resultBox
//   3. Очистіть тексти всіх повідомлень про помилки, викликавши clearErrors()
//   4. Зберіть дані форми у об'єкт data через FormData та Object.fromEntries
//   5. Викличте функцію validate(data) і збережіть результат у змінну errors
//   6. Якщо errors не порожній (Object.keys(errors).length > 0) —
//      викличте showErrors(errors) і завершіть роботу (return)
//   7. Якщо errors порожній — покажіть блок результату (додайте клас 'visible'
//      до resultBox) та виведіть дані у resultData через JSON.stringify(data, null, 2)
//   8. Очистіть форму через form.reset()
// ??? ПОЧАТОК

// ??? КІНЕЦЬ

// TODO 2: Реалізуйте функцію validate, яка приймає об'єкт data
// та повертає об'єкт з помилками (ключ — назва поля, значення — текст помилки).
// Якщо всі поля коректні — функція повертає порожній об'єкт {}.
//
// Правила перевірки:
//   - clientName: після .trim() має бути не менше 3 символів.
//     Повідомлення: "Ім'я має містити мінімум 3 символи"
//   - email: має відповідати emailPattern.
//     Повідомлення: "Некоректний формат email"
//   - phone: має відповідати phonePattern.
//     Повідомлення: "Телефон має бути у форматі +380XXXXXXXXX"
//   - service: не має бути порожнім рядком.
//     Повідомлення: "Оберіть тип послуги"
//   - comment: після .trim() має бути не менше 10 символів.
//     Повідомлення: "Коментар має містити мінімум 10 символів"
function validate(data) {
  const errors = {};
  // ??? ПОЧАТОК

  // ??? КІНЕЦЬ
  return errors;
}

// TODO 3: Реалізуйте функцію showErrors, яка приймає об'єкт errors
// та виводить кожне повідомлення у відповідний <span> з класом .error.
// Відповідність ключів об'єкта errors та id елементів:
//   clientName  -> client-name-error
//   email       -> email-error
//   phone       -> phone-error
//   service     -> service-error
//   comment     -> comment-error
// Порада: створіть об'єкт-мапу { clientName: "client-name-error", ... }
// та пройдіться по errors через Object.entries(errors).
// Наприклад: for (const [key, message] of Object.entries(errors)) { ... }
function showErrors(errors) {
  // ??? ПОЧАТОК
  // ??? КІНЕЦЬ
}

// Функція очищення всіх повідомлень про помилки — вже реалізована, змінювати не треба
function clearErrors() {
  const errorIds = [
    "client-name-error",
    "email-error",
    "phone-error",
    "service-error",
    "comment-error",
  ];
  for (const id of errorIds) {
    document.getElementById(id).textContent = "";
  }
}
```

### **Етап 4: Перевірка роботи**

Відкрийте `index.html` у браузері та послідовно перевірте наступні сценарії:

4.1. **Порожня форма.** Натисніть «Надіслати замовлення», не заповнюючи жодного поля. Під кожним полем має з'явитися повідомлення про помилку (5 повідомлень). Блок з результатом не повинен з'явитися.

4.2. **Невалідний email.** Введіть у поле email рядок `test` (без `@` і домену). Під полем має з'явитись «Некоректний формат email».

4.3. **Невалідний телефон.** Введіть у поле телефону `0671234567` (без `+380`). Має з'явитись «Телефон має бути у форматі +380XXXXXXXXX».

4.4. **Коректні дані.** Заповніть форму коректно (ім'я ≥ 3 символи, валідний email, телефон у форматі `+380XXXXXXXXX`, оберіть послугу, коментар ≥ 10 символів). Після натискання кнопки:

- Повідомлення про помилки мають зникнути
- Має з'явитись зелений блок «Замовлення успішно надіслано!» з даними у форматі JSON
- Форма має очиститись

4.5. **Повторна відправка.** Знову заповніть форму з помилкою. Переконайтесь, що зелений блок з попереднім успіхом зникає, а замість нього показуються нові помилки.

**Результат:** працююча форма замовлення з клієнтською валідацією засобами JavaScript.

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням

2. **Відповіді на контрольні питання** (коротко, по суті)

3. **Виконання роботи:**

   3.1. Скріншот коду `script.js` (з реалізованими TODO)

   3.2. Скріншот форми у браузері з повідомленнями про помилки (сценарій 4.1 або 4.2)

   3.3. Скріншот форми у браузері після успішної відправки (сценарій 4.4)

4. **Висновки** (що навчились робити, які навички отримали, з якими труднощами зіткнулись)

## **Типові помилки та їх вирішення**

| Проблема                                               | Рішення                                                                                           |
| ------------------------------------------------------ | ------------------------------------------------------------------------------------------------- |
| Сторінка перезавантажується при натисканні кнопки      | Забули викликати `event.preventDefault()` на початку обробника `submit`                           |
| Об'єкт `data` порожній після `Object.fromEntries(...)` | Перевірте, що у всіх полів форми є атрибут `name` — саме він є ключем у `FormData`                |
| Регулярний вираз не відхиляє рядок `+380671234567ABC`  | Забули про символи `^` та `$` — без них шаблон перевіряє лише збіг з частиною рядка               |
| Повідомлення про помилки не з'являються                | Перевірте, що id у `getElementById` збігаються з id у HTML: `client-name-error` (через дефіс)     |
| Результат не виводиться після успішної валідації       | Не додали клас `visible` до `resultBox` — без нього блок залишається прихованим через CSS         |
| Помилки не зникають після виправлення поля             | Забули викликати `clearErrors()` на початку обробника `submit` — попередні повідомлення лишаються |

## **Додаткові матеріали**

1. Форми та керування формами (uk.javascript.info) — https://uk.javascript.info/forms-controls

2. FormData (uk.javascript.info) — https://uk.javascript.info/formdata

3. Регулярні вирази (uk.javascript.info) — https://uk.javascript.info/regular-expressions

4. HTML Tutorial (W3Schools, українською) — https://w3schoolsua.github.io/html/index.html
