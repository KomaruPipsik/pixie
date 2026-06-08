# ⚡ PixieTasks — Платформа мікрозавдань

Повнофункціональна платформа мікрозавдань для школярів України з системою
модерації, арбітражу, real-time чатом та безпечними платежами.

---

## 🚀 Швидкий старт

### Варіант 1 — Single-port (Production)
```bash
cd backend
npm install
node index.js
# Відкрий: http://localhost:3001
```
Фронтенд вже вбудований в `backend/public/`.

### Варіант 2 — Dev-режим (два сервери)
```bash
# Термінал 1
cd backend && npm install && node index.js

# Термінал 2
cd frontend && npm install && npm run dev
# Відкрий: http://localhost:5173
```

---

## 👤 Тестові акаунти

| Роль       | Логін  | Пароль           |
|------------|--------|------------------|
| Власник    | Pixie  | pixie_admin_2024 |
| Модератор  | Kuga   | kuga_mod_2024    |

---

## 📁 Структура проєкту

```
pixie-tasks/
├── backend/
│   ├── index.js               ← Express сервер + WebSocket
│   ├── .env                   ← Змінні середовища
│   ├── database.bin           ← SQLite файл (автовід'являється)
│   ├── public/                ← Зібраний фронтенд
│   ├── uploads/               ← Відео від виконавців
│   ├── models/
│   │   ├── db.js              ← sql.js обгортка (синхронний API)
│   │   ├── payment.js         ← Mock платіжний сервіс
│   │   └── notification.js    ← Push через WebSocket
│   ├── middleware/
│   │   ├── auth.js            ← JWT + requireRole()
│   │   └── websocket.js       ← Real-time hub
│   ├── routes/
│   │   ├── auth.js            ← POST /login, /register, GET /me
│   │   ├── profile.js         ← PUT /profile
│   │   ├── tasks.js           ← CRUD + lifecycle завдань
│   │   ├── moderation.js      ← Черга + спори
│   │   ├── messages.js        ← Чат + відео upload
│   │   ├── notifications.js   ← Список + read
│   │   └── admin.js           ← Статистика + ролі
│   └── jobs/
│       └── cron.js            ← Таймери: 48ч, 24ч, авто-закриття
└── frontend/
    └── src/
        ├── pages/             ← 9 сторінок
        ├── components/        ← Layout, ChatPanel, TaskCard
        ├── store/             ← Zustand (auth + WS + chat)
        ├── hooks/             ← useCountdown (live timer)
        └── utils/             ← api, cities, helpers
```

---

## 🔧 Налаштування (.env)

```env
PORT=3001
JWT_SECRET=your_secret_here
OWNER_NICKNAME=Pixie
OWNER_CARD=4441111122223333
COMMISSION_RATE=0.20
DB_PATH=./database.bin
UPLOAD_DIR=./uploads
```

---

## ⚙️ Технології

| Шар          | Технологія                            |
|--------------|---------------------------------------|
| Фронтенд     | React 18, Vite, TailwindCSS, Zustand  |
| Бекенд       | Node.js 20, Express                   |
| База даних   | sql.js (SQLite, pure JS)              |
| Real-time    | WebSocket (ws)                        |
| Аутентифікація| JWT + bcryptjs                       |
| Фонові задачі| node-cron (1 хв інтервал)             |
| Файли        | multer (відео до 100МБ)               |

---

## 📌 Бізнес-логіка

```
Створення → Модерація → Передоплата (100%) → Публікація (48ч таймер)
    ↓
Виконавець бере → Чат відкривається → Відео підтвердження
    ↓
Замовник підтверджує → Виплата (80% виконавцю + 20% Pixie)
    ↓ (якщо відхиляє)
Виконавець має 24ч на апеляцію → Спір → Модератор вирішує
    ↓ (якщо 48ч без рішення)
Авто-повернення коштів замовнику
```
