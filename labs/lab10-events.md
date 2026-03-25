# **Лабораторна робота №10**

**Тема:** Обробка подій у браузері

**Мета:** Закріпити практичні навички роботи з подіями у JavaScript: призначення обробників через `addEventListener`, робота з об'єктом `event`, делегування подій та валідація форми в реальному часі.

**Обладнання:** IBM-сумісні ПК

**Програмне забезпечення:** ОС Windows 10/11, macOS або Linux; веб-браузер (Chrome, Firefox, Edge); текстовий редактор (VS Code, Sublime Text або інший)

---

## **Контрольні запитання**

1. Яка різниця між призначенням обробника через властивість `onclick` та через `addEventListener`? Яке обмеження має перший підхід?
2. Що таке об'єкт `event`? Яка різниця між `event.target` та `event.currentTarget` — і чому ця різниця є ключовою при делегуванні подій?
3. Що таке bubbling? Як зупинити поширення події? Чому не варто зловживати `stopPropagation()`?
4. Для чого використовується `event.preventDefault()`? Наведіть два приклади стандартних дій браузера, які можна скасувати.
5. Що таке делегування подій і чому воно є ефективним патерном при роботі з динамічно доданими елементами?

---

## **Теоретичні відомості**

### **Призначення обробників через `addEventListener`**

`addEventListener` — рекомендований спосіб призначення обробників подій. На відміну від властивості `onclick`, він дозволяє додавати кілька обробників на одну подію та явно видаляти їх через `removeEventListener`.

```javascript
const btn = document.querySelector('#myBtn');

btn.addEventListener('click', function(event) {
  console.log('Тип події:', event.type);
  console.log('Ціль:', event.target);
});
```

### **Об'єкт `event`**

Браузер автоматично передає обробнику об'єкт події як перший аргумент. Найважливіші властивості:

| Властивість / Метод | Що містить або робить |
|---|---|
| `event.type` | Назва події: `'click'`, `'input'` тощо |
| `event.target` | Елемент, на якому безпосередньо відбулась подія |
| `event.currentTarget` | Елемент, до якого прив'язаний обробник |
| `event.key` | Натиснута клавіша: `'Enter'`, `'Escape'` тощо |
| `event.preventDefault()` | **Метод:** скасовує стандартну дію браузера |
| `event.stopPropagation()` | **Метод:** зупиняє поширення події по DOM |

### **Bubbling та делегування подій**

Коли подія відбувається на елементі, вона «спливає» вгору по DOM-дереву, послідовно викликаючи обробники на батьківських елементах. Це явище — **bubbling** — є основою патерну **делегування подій**: замість того щоб додавати обробник до кожного дочірнього елемента, достатньо одного обробника на батьківському елементі.

```javascript
// Делегування: один обробник для всіх кнопок у списку
document.querySelector('#list').addEventListener('click', function(event) {
  const btn = event.target.closest('button');
  if (!btn) return; // клік не по кнопці — ігноруємо
  console.log('Натиснута кнопка:', btn.dataset.id);
});
```

### **Події форм**

| Подія | Коли спрацьовує |
|---|---|
| `input` | Миттєво при кожній зміні символу |
| `change` | Після завершення редагування і втрати фокусу |
| `focus` | Коли поле отримує фокус |
| `blur` | Коли поле втрачає фокус |
| `submit` | При відправці форми |

---

## **Хід роботи**

### **Етап 1: Підготовка**

1.1. Створіть папку для лабораторної роботи, наприклад `lab10`.

1.2. Створіть файли для кожного завдання відповідно до структури нижче.

**Очікувана структура:**

```
lab10/
├── task1.html
├── task2.html
└── task3.html
```

---

### **Етап 2: Виконання завдань**

---

#### **Завдання 1: Інтерактивна панель керування освітленням (task1.html)**

Реалізуйте сторінку, що імітує панель керування освітленням у розумному домі. Сторінка містить три «кімнати», кожна з яких має кнопку вмикання/вимикання світла та індикатор стану.

**Скопіюйте розмітку у файл `task1.html`. Розмітку та стилі не змінювати — весь код пишіть лише у блоці `<script>`:**

```html
<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <title>Розумний дім</title>
  <style>
    body { font-family: sans-serif; padding: 24px; background: #f5f5f5; }
    h1 { margin-bottom: 24px; }

    .rooms { display: flex; gap: 16px; flex-wrap: wrap; }

    .room {
      width: 200px;
      padding: 20px;
      border-radius: 12px;
      background: #fff;
      border: 2px solid #e0e0e0;
      text-align: center;
      transition: background 0.3s;
    }
    .room.on { background: #fffde7; border-color: #f9a825; }

    .room-name { font-size: 18px; font-weight: bold; margin-bottom: 8px; }

    .indicator {
      width: 32px; height: 32px;
      border-radius: 50%;
      background: #bdbdbd;
      margin: 0 auto 16px;
      transition: background 0.3s;
    }
    .room.on .indicator { background: #fdd835; box-shadow: 0 0 10px #fdd835; }

    .toggle-btn {
      padding: 8px 20px;
      border: none; border-radius: 6px;
      background: #1565c0; color: #fff;
      font-size: 14px; cursor: pointer;
    }
    .toggle-btn:hover { background: #0d47a1; }

    #log {
      margin-top: 32px; padding: 16px;
      background: #fff; border-radius: 8px;
      border: 1px solid #e0e0e0;
      max-height: 150px; overflow-y: auto;
      font-size: 14px; color: #333;
    }
    #log p { margin: 4px 0; }
  </style>
</head>
<body>
  <h1>💡 Панель керування освітленням</h1>

  <div class="rooms" id="rooms">
    <div class="room" data-room="Вітальня">
      <div class="room-name">🛋️ Вітальня</div>
      <div class="indicator"></div>
      <button class="toggle-btn">Увімкнути</button>
    </div>
    <div class="room" data-room="Кухня">
      <div class="room-name">🍳 Кухня</div>
      <div class="indicator"></div>
      <button class="toggle-btn">Увімкнути</button>
    </div>
    <div class="room" data-room="Спальня">
      <div class="room-name">🛏️ Спальня</div>
      <div class="indicator"></div>
      <button class="toggle-btn">Увімкнути</button>
    </div>
  </div>

  <div id="log"><p>📋 Журнал подій</p></div>

  <script>
    // ??? Додайте один обробник кліку на елемент #rooms (делегування)
    // При кліку перевірте, що натиснуто саме .toggle-btn:
    //   1. const btn = event.target.closest('.toggle-btn')
    //   2. if (!btn) return  ← ігноруємо кліки не по кнопці
    //   3. Знайдіть батьківський .room: btn.closest('.room')
    //   4. Перемкніть клас 'on' на .room через classList.toggle('on')
    //   5. Змініть текст кнопки: 'Увімкнути' ↔ 'Вимкнути'
    //      (перевіряйте наявність класу 'on' після toggle, щоб визначити новий стан)
    //   6. Додайте запис у #log у форматі:
    //      "⏱ [час] — [назва кімнати]: увімкнено/вимкнено"
    //      Час отримайте через new Date().toLocaleTimeString()
  </script>
</body>
</html>
```

**Завдання:**

Реалізуйте JavaScript-код у блоці `<script>`:

- Використайте **делегування подій**: один обробник `click` на елементі `#rooms`
- При кліку на кнопку перемикайте стан кімнати (клас `on`) та текст кнопки
- Додавайте до блоку `#log` новий рядок після кожного перемикання у форматі: `⏱ [час] — [назва кімнати]: увімкнено/вимкнено`
- `data-room` на `.room` містить назву кімнати — використайте `dataset.room`

**Очікуваний результат:** Натискання кнопки у будь-якій кімнаті перемикає стан (жовте підсвічування + тінь індикатора), змінює текст кнопки і додає запис у журнал. Усі три кімнати керуються одним обробником.

---

#### **Завдання 2: Клавіатурний тренажер (task2.html)**

Реалізуйте мінімалістичний тренажер введення тексту: на екрані відображається слово, яке користувач має надрукувати. Після правильного введення — нове слово.

**Скопіюйте розмітку у файл `task2.html`. Розмітку та стилі не змінювати — весь код пишіть лише у блоці `<script>`:**

```html
<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <title>Клавіатурний тренажер</title>
  <style>
    body { font-family: sans-serif; padding: 40px; text-align: center; background: #f5f5f5; }

    #word-display {
      font-size: 48px; font-weight: bold;
      letter-spacing: 6px; margin-bottom: 24px;
      color: #1565c0;
    }

    #input-field {
      font-size: 32px; padding: 8px 16px;
      width: 320px; text-align: center;
      border: 3px solid #ccc; border-radius: 8px;
      outline: none; letter-spacing: 4px;
    }
    #input-field.correct { border-color: #388e3c; background: #f1f8e9; }
    #input-field.incorrect { border-color: #c62828; background: #ffebee; }

    #stats { margin-top: 24px; font-size: 18px; color: #555; }
    #message { margin-top: 12px; font-size: 20px; min-height: 28px; }
  </style>
</head>
<body>
  <h1>⌨️ Клавіатурний тренажер</h1>

  <div id="word-display"></div>
  <input type="text" id="input-field" placeholder="Друкуйте тут..." autocomplete="off" />

  <div id="stats">✅ Правильно: <span id="correct-count">0</span> &nbsp; ❌ Помилок: <span id="error-count">0</span></div>
  <div id="message"></div>

  <script>
    const words = ['алгоритм', 'браузер', 'валідація', 'делегування', 'елемент',
                   'функція', 'змінна', 'інтерфейс', 'каскад', 'масив'];

    let correctCount = 0;
    let errorCount = 0;
    let currentWord = '';

    // ??? Реалізуйте функцію showNextWord():
    //   - Оберіть випадкове слово з масиву words
    //   - Щоб уникнути повторення, перевіряйте що нове слово відрізняється від currentWord
    //   - Запишіть його у currentWord
    //   - Відобразіть у #word-display
    //   - Очистіть поле #input-field, приберіть класи correct/incorrect
    //   - Встановіть фокус на #input-field

    // ??? Викличте showNextWord() при завантаженні сторінки

    // ??? Додайте обробник події 'input' на #input-field:
    //   - Зчитайте поточне значення поля (event.target.value)
    //   - Перевірки виконуйте у такому порядку:
    //     1. Якщо значення збігається з currentWord повністю:
    //        · Збільшіть correctCount, оновіть #correct-count
    //        · Через 500 мс завантажте наступне слово (setTimeout)
    //     2. Інакше якщо введений текст є початком слова (currentWord.startsWith(...)):
    //        · Клас 'correct' (правильно поки що)
    //     3. Інакше:
    //        · Клас 'incorrect', збільшіть errorCount, оновіть #error-count

    // ??? Додайте обробник події 'keydown' на document:
    //   - Якщо натиснута клавіша Escape І поле #input-field не порожнє:
    //     · Збільшити errorCount, оновити #error-count
    //     · Показати повідомлення у #message: "⏭ Слово пропущено"
    //     · Завантажити наступне слово через showNextWord()
    //     · Сховати повідомлення через 1500 мс (setTimeout)
  </script>
</body>
</html>
```

**Завдання:**

Реалізуйте JavaScript-код у блоці `<script>`:

- `showNextWord()` — вибирає випадкове слово, відображає та фокусує поле введення
- Обробник `input` — порівнює введений текст із поточним словом, застосовує класи `correct`/`incorrect`, фіксує успішне введення
- Обробник `keydown` на `document` — клавіша `Escape` пропускає слово і рахується як помилка

**Очікуваний результат:** Поле підсвічується зеленим при правильному введенні і червоним при помилці. При повному збігу автоматично з'являється наступне слово. Статистика оновлюється після кожного слова.

---

#### **Завдання 3: Форма реєстрації з валідацією (task3.html)**

Реалізуйте форму реєстрації з перевіркою полів у реальному часі. Форма не повинна відправлятись, доки всі поля не заповнені коректно.

**Скопіюйте розмітку у файл `task3.html`. Розмітку та стилі не змінювати — весь код пишіть лише у блоці `<script>`:**

```html
<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <title>Реєстрація</title>
  <style>
    body { font-family: sans-serif; padding: 40px; background: #f5f5f5; }

    .form-card {
      max-width: 420px; margin: 0 auto;
      background: #fff; padding: 32px;
      border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }
    h2 { margin-top: 0; margin-bottom: 24px; }

    .field { margin-bottom: 20px; }

    label { display: block; font-weight: bold; margin-bottom: 6px; }

    input[type="text"], input[type="email"], input[type="password"] {
      width: 100%; padding: 10px 12px; box-sizing: border-box;
      border: 2px solid #ccc; border-radius: 6px;
      font-size: 15px; outline: none;
      transition: border-color 0.2s;
    }
    input.valid   { border-color: #388e3c; }
    input.invalid { border-color: #c62828; }

    .hint {
      font-size: 13px; color: #c62828;
      margin-top: 4px; display: none;
    }
    .hint.visible { display: block; }

    #strength-bar {
      height: 6px; border-radius: 3px;
      background: #e0e0e0; margin-top: 8px;
      overflow: hidden;
    }
    #strength-fill {
      height: 100%; width: 0;
      background: #e0e0e0;
      transition: width 0.3s, background 0.3s;
    }

    .submit-btn {
      width: 100%; padding: 12px;
      background: #1565c0; color: #fff;
      border: none; border-radius: 6px;
      font-size: 16px; cursor: pointer;
    }
    .submit-btn:disabled { background: #90a4ae; cursor: not-allowed; }
    .submit-btn:not(:disabled):hover { background: #0d47a1; }

    #result { margin-top: 16px; font-size: 15px; text-align: center; }
  </style>
</head>
<body>
  <div class="form-card">
    <h2>📝 Реєстрація</h2>
    <form id="reg-form" novalidate>

      <div class="field">
        <label for="username">👤 Ім'я користувача</label>
        <input type="text" id="username" name="username" placeholder="від 3 до 20 символів, лише латиниця та цифри" autocomplete="off">
        <div class="hint" id="username-hint">Від 3 до 20 символів: лише латинські літери та цифри</div>
      </div>

      <div class="field">
        <label for="email">📧 Email</label>
        <input type="email" id="email" name="email" placeholder="user@example.com" autocomplete="off">
        <div class="hint" id="email-hint">Введіть коректну адресу електронної пошти</div>
      </div>

      <div class="field">
        <label for="password">🔑 Пароль</label>
        <input type="password" id="password" name="password" placeholder="мінімум 8 символів">
        <div id="strength-bar"><div id="strength-fill"></div></div>
        <div class="hint" id="password-hint">Мінімум 8 символів</div>
      </div>

      <div class="field">
        <label for="confirm">🔑 Підтвердження пароля</label>
        <input type="password" id="confirm" name="confirm" placeholder="повторіть пароль">
        <div class="hint" id="confirm-hint">Паролі не збігаються</div>
      </div>

      <button type="submit" class="submit-btn" id="submit-btn" disabled>Зареєструватись</button>
    </form>
    <div id="result"></div>
  </div>

  <script>
    // Допоміжні функції перевірки:
    function isValidUsername(value) {
      return /^[a-zA-Z0-9]{3,20}$/.test(value);
    }
    function isValidEmail(value) {
      return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(value);
    }
    function isValidPassword(value) {
      return value.length >= 8;
    }
    function getPasswordStrength(value) {
      // Повертає 0, 1, 2 або 3 (слабкий, середній, сильний)
      let score = 0;
      if (value.length >= 8)  score++;
      if (/[A-Z]/.test(value) && /[a-z]/.test(value)) score++;
      if (/[0-9]/.test(value) || /[^a-zA-Z0-9]/.test(value)) score++;
      return score;
    }

    // ??? Реалізуйте функцію setFieldState(input, hintEl, isValid):
    //   - Якщо значення поля порожнє (input.value.length === 0) — прибрати обидва класи, сховати підказку
    //   - Якщо isValid — клас 'valid', сховати підказку
    //   - Якщо !isValid — клас 'invalid', показати підказку (клас 'visible')

    // ??? Реалізуйте функцію updateSubmitButton():
    //   - Кнопка активна (не disabled) лише коли всі 4 поля валідні одночасно
    //   - Перевіряйте через isValidUsername, isValidEmail, isValidPassword
    //     та порівняння password.value з confirm.value

    // ??? Додайте обробники 'input' та 'blur' на кожне поле:
    //   - #username: перевіряти через isValidUsername
    //   - #email: перевіряти через isValidEmail
    //   - #password: перевіряти через isValidPassword;
    //     додатково оновлювати #strength-fill залежно від getPasswordStrength:
    //       score 0 → ширина 0%, колір #e0e0e0  (порожнє поле або пароль коротший за 8 символів без інших критеріїв)
    //       score 1 → ширина 33%, колір #c62828
    //       score 2 → ширина 66%, колір #f57f17
    //       score 3 → ширина 100%, колір #388e3c
    //   - #confirm: перевіряти, що значення збігається з #password
    //     (враховуйте: якщо #password порожній — confirm теж вважається невалідним)
    //   - Кожен обробник має викликати updateSubmitButton()

    // ??? Додайте обробник 'submit' на форму #reg-form:
    //   - event.preventDefault() — заблокувати стандартну відправку
    //   - Відобразити у #result: "✅ Реєстрацію завершено! Ласкаво просимо, [ім'я]!"
  </script>
</body>
</html>
```

**Завдання:**

Реалізуйте JavaScript-код у блоці `<script>`:

- `setFieldState(input, hintEl, isValid)` — централізована функція керування станом поля
- `updateSubmitButton()` — активує кнопку лише при валідності всіх полів
- Обробники `input` і `blur` на кожне поле з відповідною логікою перевірки
- Індикатор сили пароля через `#strength-fill` (ширина та колір залежно від `getPasswordStrength`)
- Обробник `submit` — блокує стандартну відправку та виводить вітальне повідомлення

**Очікуваний результат:** Поля підсвічуються зеленим/червоним в реальному часі. Індикатор пароля змінює колір і ширину залежно від складності. Кнопка «Зареєструватись» недоступна, доки не всі поля заповнені коректно.

---

### **Етап 3: Перевірка**

3.1. Відкрийте кожен файл у браузері та перевірте коректність роботи.

3.2. **Завдання 1:** переконайтесь, що всі три кімнати керуються одним обробником; перевірте журнал — записи мають з'являтись після кожного перемикання.

3.3. **Завдання 2:** перевірте підсвічування при правильному та неправильному введенні; переконайтесь, що `Escape` пропускає слово і рахується як помилка.

3.4. **Завдання 3:** перевірте, що кнопка залишається заблокованою при будь-якому невалідному полі; перевірте індикатор сили пароля при введенні паролів різної складності.

3.5. Відкрийте DevTools (F12) → вкладка **Console** та переконайтесь у відсутності помилок.

**Результат:** 3 HTML-файли з повністю реалізованою логікою обробки подій.

---

## **Зміст звіту**

1. **Титульна сторінка** з темою, метою роботи, обладнанням, програмним забезпеченням

2. **Відповіді на контрольні запитання** (коротко, по суті)

3. **Виконання завдань:**

   3.1. Для кожного завдання (1–3): скріншот блоку `<script>` у файлі та скріншот сторінки у браузері під час роботи

   3.2. Скріншот консолі DevTools, що підтверджує відсутність помилок

   3.3. Для завдання 1: скріншот журналу подій з кількома записами

   3.4. Для завдання 3: скріншот форми з усіма валідними полями та активною кнопкою

4. **Висновки** (що навчились робити, які навички отримали, з якими труднощами зіткнулись)

---

## **Типові помилки та їх вирішення**

| Проблема | Рішення |
|---|---|
| Обробник не спрацьовує при кліку на дочірній елемент | Перевірте `event.target.closest(selector)` — він повертає `null`, якщо елемент не знайдено; додайте перевірку `if (!el) return` |
| При делегуванні обробник спрацьовує на будь-який клік, навіть поза кнопкою | Перевіряйте `event.target.closest('.toggle-btn')` перед виконанням логіки — якщо `null`, одразу `return` |
| Форма відправляється попри `event.preventDefault()` | Переконайтесь, що обробник `submit` прив'язаний до `<form>`, а не до кнопки |
| Клас `valid`/`invalid` залишається після очищення поля | Перевіряйте, чи поле порожнє, і знімайте обидва класи у цьому випадку |
| `event.key` містить не те значення | Перевірте точну назву клавіші через `console.log(event.key)` у DevTools |
| Кнопка submit залишається активною при невалідному полі | `updateSubmitButton` має перевіряти поточні значення полів, а не кешовані змінні |

---

## **Додаткові матеріали**

1. Вступ до браузерних подій (uk.javascript.info) — https://uk.javascript.info/introduction-browser-events
2. Стандартні дії браузера та preventDefault (uk.javascript.info) — https://uk.javascript.info/default-browser-action
3. Делегування подій (uk.javascript.info) — https://uk.javascript.info/event-delegation
4. JavaScript Events (W3Schools, українською) — https://w3schoolsua.github.io/js/index.html
