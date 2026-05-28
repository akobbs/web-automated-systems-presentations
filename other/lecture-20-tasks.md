# 🛠️ Завдання на закріплення матеріалу

**Тема:** Обробка даних та логіка потоків у Node-RED
**Тривалість:** ~30 хвилин

## Сценарій

На виробничій лінії пакування продукції встановлено систему, що надсилає події про роботу: завершення пакування одиниці продукції, виявлення браку, початок перерви. Ваша задача — побудувати потік у Node-RED, який обробляє ці події, веде лічильник виготовленої продукції та формує повідомлення для оператора.

Кожна подія приходить у такому форматі:

```json
{ "lineId": "L-01", "eventType": "package_done", "quantity": 1 }
```

Поле `eventType` може мати значення: `package_done`, `defect`, `break_start`.

## Що потрібно зробити

Створіть нову вкладку у Node-RED (наприклад, `Практика 20`) та побудуйте потік, що виконує всі кроки нижче.

### 1️⃣ Джерела подій

Додайте чотири Inject-ноди — кожна симулює одну подію:

| Назва           | Payload (тип JSON)                                                 |
| --------------- | ------------------------------------------------------------------ |
| `package_done`  | `{"lineId": "L-01", "eventType": "package_done", "quantity": 1}`   |
| `defect`        | `{"lineId": "L-01", "eventType": "defect", "quantity": 1}`         |
| `break_start`   | `{"lineId": "L-01", "eventType": "break_start", "quantity": 0}`    |
| `invalid_event` | `{"lineId": "L-01", "eventType": "package_done"}` (без `quantity`) |

Усі чотири з'єднуються з однією Function-нодою (наступний крок).

### 2️⃣ Function-нода: валідація + лічильник

Function-нода `Валідація + лічильник` повинна:

- Перевірити, що `payload` містить рядкові `lineId`, `eventType` та числове `quantity`. Якщо ні — викликати `node.error("Невалідна подія", msg)` і повернути `null`.
- Для подій типу `package_done` додавати `quantity` до накопичувального лічильника у **flow-контексті** під ключем `totalPackaged_<lineId>` (окремий лічильник для кожної лінії).
- Передавати далі `msg.payload` з полями: `lineId`, `eventType`, `quantity`, `total`, `timestamp` (ISO-рядок).
- Показувати у `node.status()` зелену крапку з текстом `загалом: N`.

Можна почати з такого скелета — заповніть місця з коментарями TODO:

```javascript
const data = msg.payload;

// Валідація
if (
  !data ||
  typeof data.lineId !== "string" ||
  typeof data.eventType !== "string" ||
  typeof data.quantity !== "number"
) {
  node.error("Невалідна подія", msg);
  return null;
}

// Лічильник у flow-контексті
const key = "totalPackaged_" + data.lineId;
let total = flow.get(key) ?? 0;

if (data.eventType === "package_done") {
  // TODO: збільшити total на data.quantity та зберегти у flow
}

// TODO: сформувати msg.payload з полями lineId, eventType, quantity, total, timestamp
msg.payload = {
  /* ... */
};

// TODO: викликати node.status({ fill: ..., shape: ..., text: ... })

return msg;
```

### 3️⃣ Switch: маршрутизація за типом події

Після Function-ноди — нода Switch. У полі **Property** введіть `payload.eventType` (Node-RED автоматично додасть префікс `msg.`). Додайте чотири правила:

- `== package_done` → перший вихід
- `== defect` → другий
- `== break_start` → третій
- `otherwise` → четвертий (для невідомих типів)

Режим — `stopping after first match`.

### 4️⃣ Template: повідомлення для оператора

До кожного виходу Switch підключіть окрему Template-ноду:

**Для `package_done`:**

```
[OK] Лінія {{payload.lineId}}: упаковано {{payload.quantity}} шт.
Загалом на лінії: {{payload.total}} шт.
Час: {{payload.timestamp}}
```

**Для `defect`:**

```
⚠️ БРАК на лінії {{payload.lineId}}
Кількість бракованих: {{payload.quantity}} шт.
Час: {{payload.timestamp}}
```

**Для `break_start`:**

```
⏸️ Лінія {{payload.lineId}} призупинена.
Час: {{payload.timestamp}}
```

**Для `otherwise`:**

```
❓ Невідомий тип події '{{payload.eventType}}' на лінії {{payload.lineId}}.
```

Усі чотири Template-ноди з'єднайте зі **спільною Debug-нодою** з назвою `📨 Для оператора`.

### 5️⃣ Catch: обробка помилок

Додайте ноду Catch. У її налаштуваннях у полі **Catch errors from** оберіть опцію **selected nodes** і виберіть Function-ноду `Валідація + лічильник`. До виходу Catch підключіть Function-ноду `Лог помилки`:

```javascript
const e = msg.error;
msg.payload = {
  level: "ERROR",
  timestamp: new Date().toISOString(),
  source: e.source.name,
  message: e.message,
  rawData: msg.payload,
};
return msg;
```

Далі — окрема Debug-нода `🔴 Журнал помилок`.

## Що має вийти

Після Deploy натискайте Inject-кнопки і перевіряйте Debug-панель:

- Тричі натиснути `package_done` → під Function-нодою статус має змінюватись: `загалом: 1`, потім `загалом: 2`, потім `загалом: 3`. У `📨 Для оператора` щоразу з'являється повідомлення з оновленим значенням `Загалом на лінії`.
- Натиснути `defect` → з'являється повідомлення про брак, але лічильник **не змінюється**.
- Натиснути `break_start` → повідомлення про перерву.
- Натиснути `invalid_event` → нічого не йде у `📨 Для оператора`, натомість у `🔴 Журнал помилок` з'являється запис з `level: "ERROR"`.

## 💡 Підказки

- Якщо нічого не працює — перевірте, чи натиснуто **Deploy**.
- Якщо лічильник скидається при кожному натисканні — перевірте, що використовуєте `flow.get/set`, а не звичайну змінну.
- Якщо всі повідомлення йдуть у `otherwise` — у Switch у полі Property має бути саме `payload.eventType`, а не просто `payload`.
- Якщо `defect` теж збільшує лічильник — перевірте, що `total = total + data.quantity` написано **всередині** `if (data.eventType === "package_done")`.

## 🎯 Якщо залишився час — додайте розширення

Спробуйте розширити потік однією з ідей:

- **Другий лічильник** — `totalDefects_<lineId>` для подій типу `defect`. У шаблоні `defect` додайте рядок з поточною кількістю браку.
- **Друга лінія** — додайте Inject `package_done` з `lineId: "L-02"` і переконайтесь, що лічильник L-02 ведеться окремо від L-01.
- **Попередження замість помилки** — якщо брак перевищив поріг (наприклад, 3 шт.), показати жовтий статус і викликати `node.warn(...)`. На відміну від `node.error(...)`, попередження не активує Catch — повідомлення продовжить рух потоком далі.
