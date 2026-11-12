# Claude Code у Docker-контейнері

Невеликий проєкт, що пакує [Claude Code](https://docs.anthropic.com/en/docs/claude-code) в ізольований Docker-контейнер. Агент працює не під root, бачить лише змонтований робочий каталог, а його конфігурація та авторизація зберігаються в окремому томі між запусками.

## Структура

| Файл | Призначення |
|------|-------------|
| `.devcontainer/Dockerfile` | Образ на базі `node:20` з git, curl, jq, zsh та встановленим `@anthropic-ai/claude-code`; працює під користувачем `node` |
| `docker-compose.yml` | Сервіс `claude`: монтує проєкт у `/workspace`, зберігає `~/.claude` у томі `claude-config`, запускає `claude` в інтерактивному режимі |
| `.claude/settings.json` | Налаштування Claude Code: режим `acceptEdits`, заборона читати `.env` та `~/.ssh`, увімкнений sandbox |

## Запуск у Docker

### Вимоги

- Docker 20.10+ з плагіном Docker Compose v2
- Обліковий запис Anthropic (підписка або API-ключ)

### Швидкий старт

```bash
# 1. Зібрати образ
docker compose build

# 2. Запустити Claude Code в інтерактивному режимі
docker compose run --rm claude
```

Під час першого запуску Claude Code запропонує авторизуватися. Дані збережуться в томі `claude-config`, тож повторно входити не доведеться.

### Авторизація через API-ключ

Замість інтерактивного входу можна передати ключ змінною середовища. Не додавайте його в репозиторій; використовуйте файл `.env` (він ігнорується git):

```bash
echo "ANTHROPIC_API_KEY=sk-ant-..." > .env
docker compose run --rm -e ANTHROPIC_API_KEY claude
```

### Корисні команди

```bash
# Отримати shell у контейнері замість запуску claude
docker compose run --rm claude zsh

# Запустити claude з початковим запитом
docker compose run --rm claude claude "поясни структуру проєкту"

# Перебудувати образ з оновленою версією Claude Code
docker compose build --no-cache

# Видалити контейнери разом із томом конфігурації (вийде з акаунта)
docker compose down -v
```

### Dev Container

Каталог `.devcontainer` також можна відкрити в VS Code через розширення *Dev Containers* (`Reopen in Container`). Контейнер стартує з `sleep infinity`, а `claude` запускається вручну з термінала.

## Нотатки щодо безпеки

- Контейнер працює від імені користувача `node`, а не root.
- Доступ до файлів хоста обмежений каталогом проєкту (`/workspace`).
- Секрети (`.env`, `.env.*`) та особисті налаштування `.claude/settings.local.json` виключені з git.
