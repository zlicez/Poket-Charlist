# Poket-Charlist
Pocket Charlist — веб‑приложение для ведения листов персонажей Dungeons & Dragons 5e.

Проект рассчитан на **мобильное использование**, умеет делать базовые D&D‑расчёты и хранить персонажей в базе данных.

---

## Что умеет приложение
- Полный лист персонажа D&D 5e (характеристики, навыки, спасброски).  
- Бой: КД, инициатива, скорость, HP, смерти.  
- Оружие и инвентарь с автоподсчётами.  
- Встроенный бросок кубов.  
- Режимы «редактирование/игра».  
- Несколько персонажей.  
- Светлая/тёмная тема.  

---

## Технологии
**Frontend:** React + TypeScript + TailwindCSS + Shadcn/UI, Wouter, TanStack Query.  
**Backend:** Express + TypeScript.  
**База данных:** PostgreSQL + Drizzle ORM.  
**Аутентификация:** Replit OIDC (в проде) или локальный режим без внешнего провайдера.  

---

## Структура проекта
```
client/              # фронтенд (React)
server/              # backend (Express)
shared/              # общие типы и данные D&D
script/              # сборочные скрипты
drizzle.config.ts    # конфигурация Drizzle
vite.config.ts       # конфигурация Vite
```

Ключевые файлы:
- `client/src/pages/CharactersList.tsx` — список персонажей, создание/удаление.  
- `client/src/pages/CharacterSheet.tsx` — главный лист персонажа.  
- `server/routes.ts` — API‑маршруты.  
- `shared/schema.ts` — типы, данные D&D, расчёты.  

---

## Запуск локально (без Replit/Google)
Это **рекомендованный путь для локальной разработки**, когда внешняя аутентификация не нужна.

### 1. Требования
- Node.js 20+
- PostgreSQL

### 2. Подготовка базы (самый простой вариант)
Если у вас нет PostgreSQL локально, используйте Docker:
```bash
docker compose up -d
```

Если PostgreSQL уже установлен вручную, создайте базу `poket_charlist`.

### 3. Подготовка переменных окружения
Скопируйте пример и при необходимости отредактируйте:
```bash
cp .env.example .env
```
Скрипты `npm run dev` и `npm run db:push` автоматически подхватывают переменные из `.env`.

### 4. Включаем локальную авторизацию
```bash
export LOCAL_AUTH=true
export LOCAL_USER_ID=local-user
```

> Локальный режим **обходит Replit OIDC** и автоматически подставляет пользователя.  
> Это нужно, чтобы все API работали без логина через Google/Replit.

### 5. Устанавливаем зависимости, применяем миграции и стартуем
```bash
npm install
npm run db:push
npm run dev
```

Приложение будет доступно на `http://localhost:5000`.

---

## Локальная учётка (опционально)
В локальном режиме ответ `/api/auth/user` формируется из этих переменных:
```bash
export LOCAL_USER_EMAIL=local@example.com
export LOCAL_USER_FIRST_NAME=Local
export LOCAL_USER_LAST_NAME=User
export LOCAL_USER_AVATAR_URL=
```

---

## Запуск с Replit OIDC (прод/облачный режим)
Если хотите использовать Replit‑аутентификацию:
```bash
export SESSION_SECRET="любая_строка"
export REPL_ID="ваш_replit_id"
export ISSUER_URL="https://replit.com/oidc" # обычно так
```
При этом `LOCAL_AUTH` должен быть выключен (`LOCAL_AUTH=false` или отсутствует).

---

## Скрипты
```bash
npm run dev     # запуск dev-сервера
npm run build   # сборка проекта
npm run start   # запуск production сборки
npm run check   # проверка TypeScript
npm run db:push # применение миграций Drizzle
```

---

## API эндпойнты
Все запросы защищены авторизацией (Replit OIDC или локальный режим):

- `GET /api/characters` — список персонажей  
- `GET /api/characters/:id` — получить персонажа  
- `POST /api/characters` — создать персонажа  
- `PATCH /api/characters/:id` — обновить персонажа  
- `DELETE /api/characters/:id` — удалить персонажа  
- `GET /api/auth/user` — информация о текущем пользователе  

---

## Частые проблемы и решения
**Ошибка `DATABASE_URL must be set`**  
- Проверьте, что переменная окружения `DATABASE_URL` действительно задана.

**Ошибка при логине / редиректе**  
- Проверьте, что `LOCAL_AUTH=true` для локального режима, иначе приложение ждёт Replit OIDC.

---

## Как работает приложение (очень кратко)
1. Фронтенд делает запросы на `/api/...`.  
2. Сервер проверяет авторизацию.  
3. Данные персонажа лежат в PostgreSQL в JSON‑поле таблицы `characters`.  
4. `shared/schema.ts` содержит все D&D‑данные и расчёты.  

---

Если нужен гайд по добавлению новых правил D&D, структуры персонажа или UI — пишите, разберём по шагам.
