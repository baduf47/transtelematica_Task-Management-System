
# Суверенная система управления задачами (Транстелематика)

[![Python](https://img.shields.io/badge/python-3.11-blue)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.104-green)](https://fastapi.tiangolo.com/)
[![React](https://img.shields.io/badge/React-19-blue)](https://reactjs.org/)

**Прототип веб-приложения для планирования, контроля и анализа задач** внутри компании.  
Поддерживает иерархию периодов (год → квартал → месяц → неделя), дашборды и **локального AI-помощника** (без отправки данных во внешние сервисы).

## 🚀 Быстрый старт

### 📦 Требования

- **Docker** и **Docker Compose** (или `docker compose` plugin)
- **Node.js** (рекомендуется версия 22.x)
- **Linux** (Ubuntu/Debian) или **WSL2** на Windows

### 🖥️ Установка Docker и Node.js (пример для Ubuntu)

<details>
<summary>Показать инструкцию установки Docker</summary>

```bash
# Обновление списка пакетов
sudo apt update

# Установка зависимостей
sudo apt install -y ca-certificates curl

# Добавление официального GPG-ключа Docker
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Добавление репозитория Docker
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Установка Docker Engine и Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Добавление пользователя в группу docker (перелогиньтесь после)
sudo usermod -aG docker $USER
```
</details>

<details>
<summary>Показать инструкцию установки Node.js 22.x</summary>

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
```
</details>

### 📂 Запуск проекта

1. **Распакуйте архив** `ttm-hackathon(with_ico).tar.gz`  
2. **Перейдите в папку проекта**  
   ```bash
   cd ttm-hackathon
   ```
3. **Запустите контейнеры** (PostgreSQL, Ollama, бэкенд)  
   ```bash
   docker compose up -d --build
   ```
   > Если у вас старая версия Docker Compose, используйте `docker-compose up -d --build`.
4. **Скачайте AI-модель** (один раз, ~2 ГБ)  
   ```bash
   docker exec -it ollama ollama pull llama3.2:3b
   ```
   > Для ускорения можно использовать `tinyllama`:  
   > `docker exec -it ollama ollama pull tinyllama`
5. **(Опционально) Восстановите тестовые задачи из дампа**  
   ```bash
   docker exec -i taskdb psql -U taskuser taskdb < backup_db.sql
   ```
6. **Запустите фронтенд**  
   ```bash
   cd frontend
   npm install        # только при первом запуске
   npm run dev
   ```
7. **Откройте в браузере**  
   - Фронтенд: [http://localhost:5173](http://localhost:5173)  
   - API-документация: [http://localhost:8000/docs](http://localhost:8000/docs)

### 🧪 Тестовые учётные данные (ЭТАП НА СТАДИИ УЛУЧШЕНИЯ, В ДАННОМ ПРОЕКТЕ НЕ ПРЕДСТАВЛЕН)

| Роль         | Email                | Пароль   | MFA код |
|--------------|----------------------|----------|---------|
| Администратор| admin@example.com    | admin123 | 000000  |
| Тимлид       | ivan@example.com     | lead123  | 000000  |
| Сотрудник    | maria@example.com    | emp123   | 000000  |

> **Примечание:** MFA-код в текущей версии – заглушка (`000000`). В реальном проекте заменим на TOTP.

### ⚠️ Устранение возможных ошибок

Если в процессе возникли проблемы, выполните **полную перезагрузку** (будут удалены все данные БД и модели AI):

```bash
docker compose down -v
docker compose up -d --build
docker exec -it ollama ollama pull llama3.2:3b
```

Затем повторите шаги запуска фронтенда.

### 🛠️ Команды для остановки и очистки

- **Остановить контейнеры** (без удаления данных):  
  ```bash
  docker compose down
  ```
- **Полностью удалить всё** (включая базу данных и модели AI):  
  ```bash
  docker compose down -v
  ```

---

## 📋 Основной функционал

- ✅ **Управление задачами** с периодами: год, квартал, месяц, неделя  
- ✅ **Назначение ответственных** и привязка к отделам  
- ✅ **Фильтрация и поиск** по статусу, периоду, отделу, сотруднику  
- ✅ **Дашборды** с круговыми и столбчатыми диаграммами (статусы, загрузка сотрудников, просрочки)  
- ✅ **Локальный AI-помощник** на основе Ollama + llama3.2:3b (без интернета)  
На стадии улучшения (в данном проекте не будет):
-  **Ролевая модель** (администратор, тимлид, сотрудник) и разграничение прав  
-  **Двухфакторная аутентификация (MFA)** – заглушка (код `000000`)  
-  **Блокировка аккаунта** после 3 неудачных попыток входа  

---

## 🧠 AI-помощник

- Работает полностью локально, используя модель `llama3.2:3b`.  
- Отвечает на вопросы о задачах и даёт рекомендации руководителю.  
- При нажатии кнопки «Анализ задач» формирует краткую сводку по текущим задачам.  

---

## 🐳 Архитектура (Docker)

| Сервис      | Контейнер     | Порт          | Назначение                     |
|-------------|---------------|---------------|--------------------------------|
| PostgreSQL  | `taskdb`      | 5432          | Хранение задач и пользователей |
| Ollama      | `ollama`      | 11434         | Локальный AI‑сервер            |
| FastAPI     | `task_backend`| 8000          | Бэкенд API                     |
| React + Vite| (вне Docker)  | 5173          | Фронтенд                       |

---

