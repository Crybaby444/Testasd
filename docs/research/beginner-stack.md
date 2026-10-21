# Какой стек проще всего освоить новичку для сайта с frontend, backend и базой

Исследование к тикету [#2](https://github.com/Crybaby444/Testasd/issues/2) (карта: [#1](https://github.com/Crybaby444/Testasd/issues/1)).
Итоговое решение принимается в тикете [#5 «Выбор стека»](https://github.com/Crybaby444/Testasd/issues/5); здесь — только факты и рекомендация.

Дата проверки источников: 2026-10-21.

## Исходные условия

- Один разработчик, срок ~1 месяц, учебный проект «КнигоОбмен» (каталог книг, «отдам»/«обменяю», запросы на обмен, отзывы).
- Уровень: HTML/CSS и Python — есть; JavaScript и SQL — совсем чуть-чуть; фреймворки — не трогал.
- Машина: macOS, Python 3.13.2, Homebrew. Node.js, Docker и PostgreSQL не установлены.

## Сводная таблица

| | Django (серверные шаблоны) | Flask + Jinja | FastAPI + frontend на чистом JS | Node.js (Express) |
|---|---|---|---|---|
| Язык, который нужно знать | Python + HTML/CSS (+ язык шаблонов) | Python + HTML/CSS (+ Jinja) | Python + **заметный объём JS** (fetch, DOM, состояние на клиенте) | **JavaScript везде** |
| Порог входа для этого автора | Средний: много понятий сразу (проект/приложения, модели, URLconf, views, шаблоны), но всё по одному учебнику | Низкий на старте, растёт к концу: сборку из расширений (БД, вход, формы, миграции) делаешь сам | Высокий: два отдельных приложения, JSON-API, CORS, токены/куки, JS-фронтенд | Высокий: новый язык + установка Node + выбор библиотек |
| Вход в аккаунт | **Есть** (`django.contrib.auth`: пользователи, хеширование паролей, формы и view для входа) [1] | Нет в ядре; в учебнике пишется вручную, иначе расширение (Flask-Login) [3][4] | Нет готового входа; в доках — рецепт OAuth2 + JWT, собирается вручную [5] | Нет; отдельные пакеты (сессии, Passport и т.п.) [7] |
| Админка | **Есть** (`django.contrib.admin`, включена в шаблоне проекта) [2] | Нет (сторонние расширения) | Нет | Нет |
| ORM | **Есть** (Django ORM) | Нет в ядре — «Flask will never have a database layer» [4]; учебник использует сырой SQL через `sqlite3` [3]; ORM — Flask-SQLAlchemy | Нет в ядре; в доках — SQLModel (над SQLAlchemy) [5] | Нет; ставится отдельно (Prisma, Sequelize, Mongoose…) [7] |
| Миграции | **Есть** (`makemigrations` / `migrate`) [6] | Нет; учебник пересоздаёт таблицы `init-db` [3]; иначе Flask-Migrate/Alembic | Нет; доки советуют Alembic напрямую [5] | Нет; зависит от выбранного ORM |
| Документация (англ.) | Отличная, официальный учебник из нескольких частей + учебник MDN [6][8] | Хорошая, официальный учебник (блог с регистрацией и входом) [3] | Отличная по API, но фронтенд не покрывает вовсе | Хорошая (Express + учебник MDN, там MongoDB/Mongoose) [7][8] |
| Документация (рус.) | Официальные доки на русский **не переведены** [6]; есть русский учебник **Django Girls** (с нуля: HTML, CSS, Python, Django, деплой) [9] | Официальных переводов нет; русские материалы — только сторонние | Есть русский перевод доков, помечен как ИИ-перевод «могут быть ошибки» [10] | В основном англ. |
| Считается ли «frontend + backend + database» | Да: HTML/CSS-шаблоны (+ немного JS) = frontend, Django = backend, SQLite/PostgreSQL = БД. Риск: преподаватель может считать серверные шаблоны «не настоящим frontend» — уточнить требования | Да, по той же логике, что и Django; тот же риск | **Нагляднее всего**: frontend и backend — физически разные части, общаются по API | Да (при любом варианте разделения) |
| Что нужно установить | `pip install django` | `pip install flask` (+ расширения) | `pip install "fastapi[standard]"` + ничего для чистого JS | Node.js через Homebrew + npm-пакеты |

Ссылки в квадратных скобках — в разделе «Источники».

## Подробнее по вариантам

### Django

- Python 3.13 поддерживается текущей версией Django 6.1 (поддерживаемые: 3.12–3.15) [11].
- `startproject` сразу включает `auth`, `sessions`, `admin`; `python manage.py migrate` создаёт таблицы пользователей и прав [1][2].
- Админка — это внутренний инструмент для доверенных пользователей, а не публичная часть сайта [2]. Для «КнигоОбмена» она бесплатно даёт модерацию книг/пользователей и удобна для наполнения тестовыми данными.
- Официальный учебник (приложение «опросы»: публичная часть + админка) и учебник MDN «LocalLibrary» (каталог книг — очень близко к нашему домену; модели, админка, формы, аутентификация, тесты, деплой) [6][8].
- Минус: «магии» больше, чем во Flask; ошибки поначалу непонятны. Зато почти всё, что нужно MVP (регистрация, вход, CRUD книг, формы, миграции), делается стандартными средствами без выбора библиотек.

### Flask + Jinja

- Сам себя описывает как «micro»: ядро — только маршрутизация (Werkzeug) и шаблоны (Jinja); «By default, Flask does not include a database abstraction layer, form validation…» [4].
- Официальный учебник Flaskr — блог с регистрацией, входом, CRUD постов; БД — SQLite через стандартный `sqlite3` и **сырой SQL** (`schema.sql`, `CREATE TABLE …`) [3]. Плюс: автор реально потрогает SQL. Минус: при SQL «чуть-чуть» это дополнительная нагрузка, а миграций нет — изменение схемы = пересоздание таблиц.
- Для объёма «КнигоОбмена» (пользователи, книги, запросы на обмен, отзывы) придётся самостоятельно выбрать и связать 3–4 расширения (Flask-SQLAlchemy, Flask-Migrate, Flask-Login, Flask-WTF) — каждое со своей документацией. Новичку без опыта фреймворков это даёт больше точек отказа, чем Django.

### FastAPI + frontend на чистом JS

- FastAPI — «фреймворк для создания API» [10]; HTML-страницы он по замыслу не рендерит, значит весь интерфейс (каталог, формы, личный кабинет) пишется на JS: `fetch`, разбор JSON, отрисовка DOM, хранение токена, обработка ошибок.
- Вход: в доках — OAuth2 с паролем и JWT, собирается по рецепту [5]. Админки нет. ORM — SQLModel, миграции — Alembic отдельно [5].
- Официальный «Full Stack FastAPI Template» использует React + TypeScript + PostgreSQL + Docker Compose [12] — это далеко за пределами уровня автора.
- Плюс: самое наглядное разделение на три слоя и реальный опыт с API. Минус: при «JS чуть-чуть» основная часть месяца уйдёт на frontend, а не на функции сайта.

### Node.js (кратко)

- Express требует Node.js 18+ (не установлен) [7]; ORM, вход, админку и шаблонизатор нужно выбрать и поставить отдельно [7].
- Учебник MDN по Express использует MongoDB/Mongoose — не SQL [8].
- Для автора, чья сильная сторона — Python, это смена языка без выигрыша. Не рекомендуется.

## SQLite или PostgreSQL

| | SQLite | PostgreSQL |
|---|---|---|
| Установка | Ничего: встроен в Python (`sqlite3`), БД — один файл [3][6] | Отдельный сервер (Homebrew), пользователь/пароль, драйвер `psycopg` |
| Поддержка во фреймворках | По умолчанию в Django [6]; в учебниках Flask и FastAPI [3][5] | Рекомендован Django для продакшена [11] |
| Нагрузка | «any site that gets fewer than 100K hits/day should work fine with SQLite» [13]; одновременно пишет только один процесс, писатели ждут очереди [13] | Высокая конкуренция записей |
| Ограничения | При высокой конкуренции — `database is locked` [14] | — |
| Для «КнигоОбмена» | Учебный сайт с единицами пользователей — нагрузки заведомо хватит | Избыточно для MVP; полезно, только если курс явно требует «серверную СУБД» или деплой на хостинг, где файл SQLite не сохраняется |

Django сам говорит: SQLite — «the easiest choice» для новичка, но «when starting your first real project… you may want to use a more scalable database like PostgreSQL» [6]. Поскольку ORM Django (и SQLAlchemy) скрывает различия, переход SQLite → PostgreSQL позже — это в основном смена настроек `DATABASES` и повторный `migrate`, а не переписывание кода.

**Вывод по БД:** начинать с SQLite; PostgreSQL — только если этого требует курс или выбранный хостинг.

## Рекомендация (для обсуждения в #5)

**Django + серверные шаблоны (HTML/CSS + немного JS) + SQLite.**

Почему: автор знает Python и HTML/CSS — Django позволяет писать именно на них; вход в аккаунт, админка, ORM и миграции идут из коробки, поэтому месяц уходит на функции «КнигоОбмена», а не на сборку инфраструктуры; есть русский учебник с нуля (Django Girls) и англоязычный учебник MDN про каталог книг, почти совпадающий с нашим доменом; ничего, кроме `pip install django`, ставить не нужно.

Запасные варианты:
- **Flask + Jinja + SQLite** — если Django покажется слишком «магическим» и хочется понимать каждую строчку (ценой ручной сборки расширений и сырого SQL).
- **FastAPI + чистый JS** — только если курс прямо требует отдельного frontend, общающегося с backend по API. Компромисс внутри Django: сделать 1–2 страницы (например, отправку запроса на обмен) через `fetch` к JSON-view — это покажет JS-слой без отдельного приложения.

Открытые вопросы для #5:
1. Требует ли курс отдельного frontend-приложения/API или серверные шаблоны засчитываются как frontend?
2. Требует ли курс конкретную СУБД (PostgreSQL/MySQL) или деплой на хостинг?

## Источники

1. Django — User authentication: https://docs.djangoproject.com/en/stable/topics/auth/
2. Django — The admin site: https://docs.djangoproject.com/en/stable/ref/contrib/admin/
3. Flask — Tutorial / Define and Access the Database: https://flask.palletsprojects.com/en/stable/tutorial/ , https://flask.palletsprojects.com/en/stable/tutorial/database/
4. Flask — Design Decisions («What does "micro" mean?», «What Flask is, What Flask is Not»): https://flask.palletsprojects.com/en/stable/design/
5. FastAPI — SQL (Relational) Databases: https://fastapi.tiangolo.com/tutorial/sql-databases/ ; Security: https://fastapi.tiangolo.com/tutorial/security/
6. Django — Tutorial part 1 и part 2 (БД по умолчанию, миграции; список переводов доков без русского): https://docs.djangoproject.com/en/stable/intro/tutorial01/ , https://docs.djangoproject.com/en/stable/intro/tutorial02/
7. Express — Installing: https://expressjs.com/en/starter/installing.html
8. MDN — Express Local Library tutorial (и раздел Django в том же разделе): https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Express_Nodejs/Tutorial_local_library_website , https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Server-side/Django
9. Django Girls Tutorial (рус.): https://tutorial.djangogirls.org/ru/
10. FastAPI (рус. перевод, с пометкой об ИИ-переводе): https://fastapi.tiangolo.com/ru/
11. Django — FAQ: Installation (версии Python, поддерживаемые БД): https://docs.djangoproject.com/en/stable/faq/install/
12. Full Stack FastAPI Template: https://github.com/fastapi/full-stack-fastapi-template
13. SQLite — Appropriate Uses For SQLite: https://www.sqlite.org/whentouse.html
14. Django — Databases, SQLite notes («database is locked»): https://docs.djangoproject.com/en/stable/ref/databases/
