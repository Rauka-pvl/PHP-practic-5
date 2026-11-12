# Практическая работа № 5: сессии и учётные записи

**Студент:** группа CS-302(С)
**Вариант:** A (техника)

Каталог из работы № 4 дополнен регистрацией, входом и выходом. Гость просматривает и ищет записи, пользователь после входа добавляет новые, администратор также редактирует и удаляет любые записи. После успешной операции сообщение (flash) показывается один раз.

## Среда

| Компонент | Версия |
|---|---|
| PHP | 8.4.11 |
| Composer | 2.8.1 |
| Сервер БД | MySQL 9.4.0 |
| Адрес | http://localhost:8080/ |
| Веб-корень | `practice_04/public` (папка проекта из работы № 4) |

## Запуск через Uniform Server

1. Запустить Apache и MySQL; VirtualHost из работы № 4 не меняется (веб-корень — `public/`).
2. В phpMyAdmin экспортировать БД каталога, создать проверочную БД и импортировать в неё экспорт.
3. В `config.local.php` заменить в DSN имя БД на проверочную (файл не сдаётся).
4. В проверочной БД один раз выполнить `sql/05-accounts.sql`: появляется таблица `users`, записи каталога сохраняются, старые `owner_id` становятся `NULL`.
5. `composer install`
6. Открыть http://localhost:8080/health.php, затем http://localhost:8080/items.

Назначение администратора: зарегистрироваться через `/register`, затем в phpMyAdmin:

```sql
UPDATE users SET role = 'admin' WHERE email = 'admin@example.test';
SELECT id, email, role FROM users WHERE email = 'admin@example.test';
```

## Что сделано

**Подключение дополнения (шаг 1)**

- `Item::$ownerId` стал `?int`, `PdoItemRepository::item()` сохраняет `NULL` владельца.
- `Session::start()`: `lifetime => 0`, `path => '/'`, `httponly`, `samesite => 'Lax'`, `secure` при HTTPS; `use_strict_mode` и `use_only_cookies` = 1; файлы сессий в `var/sessions`.
- `bootstrap.php` возвращает фабрику `p05-compose.php`; маршруты регистрируются только в `p05-routes.php`.

**TODO**

| Код | Файл | Реализация |
|---|---|---|
| `P05-ROUTES` | `p05-routes.php` | 13 маршрутов: каталог, `/items/{id}/edit`, `/items/{id}/delete`, `/register`, `/login`, `/logout` |
| `P05-USERS-FIND`, `P05-USERS-EMAIL` | `src/Accounts/UserRepository.php` | `SELECT … WHERE id = :id` / `WHERE email = :email` |
| `P05-USERS-CREATE` | там же | `INSERT` с ролью `'user'` в SQL; ошибка MySQL 1062 → `HttpError` 422 для поля `email`, остальные ошибки PDO передаются дальше |
| `P05-CURRENT` | `src/Accounts/Auth.php` | пользователь по целочисленному `$_SESSION['user_id']`; удалённый пользователь убирается из сессии |
| `P05-VALIDATION` | `src/Accounts/Registration.php` | имя 2–80 символов; email в нижнем регистре, ASCII, до 254 байтов; пароль от 8 символов, до 72 байтов, без нулевого байта, без `trim`; совпадение подтверждения; посторонние поля (в т. ч. `role`) — ошибка |
| `P05-REGISTER` | `src/Controller/AccountController.php` | `password_hash(…, PASSWORD_DEFAULT)`, `create()`, flash, 303 на `/login` |
| `P05-LOGIN` | `Auth::login()` | `byEmail()` + `password_verify()`; при успехе `AccountSession::renew()` и запись `user_id` |
| `P05-LOGOUT` | `Auth::logout()` | `AccountSession::finish()` |
| `P05-ACCESS` | `Auth::guard()` | гость — 303 на `/login`; не администратор при `guard(true)` — 403 |
| `P05-SAVE-ACCESS`, `P05-DELETE-ACCESS` | `src/Controller/AccountCatalogController.php` | создание — `guard()`, редактирование и удаление — `guard(true)`, ранний возврат `Response` до CSRF и изменений БД; владелец новой записи — ID вошедшего пользователя |
| `P05-UPDATE`, `P05-DELETE` | `src/Accounts/AdminItems.php` | `UPDATE … WHERE id = :id` без изменения владельца; `DELETE … WHERE id = :id` |

## Результаты проверок

- `php checks/check.php http://localhost:8080 routes` — `RESULT PASS 12 checks; step=routes`.
- `php checks/check.php http://localhost:8080 all admin@example.test` — `RESULT PASS 78 checks; step=all`. Полный вывод: [check-result.md](check-result.md).
- Миграция: в `items` было 4 строки, после `05-accounts.sql` — те же 4 строки, `owner_id = NULL`.
- Запись, добавленная пользователем, сохранена с `owner_id`, равным ID этого пользователя.

| Сценарий | Результат |
|---|---|
| Гость открывает `/items/new` или отправляет POST `/items` | 303 на `/login`, БД не меняется |
| Пользователь добавляет запись | 303 на `/items/<id>`, владелец — ID пользователя |
| Пользователь открывает редактирование или удаляет | 403 |
| Администратор изменяет чужую запись | 303, значения сохранены, flash один раз |
| Администратор удаляет запись | 303 на `/items`, повторный просмотр — 404 |
| Неверный CSRF у участника с нужными правами | 403 |
| Некорректные поля | 422, ошибки и непарольные значения в форме |
| Неверный email или пароль при входе | 422 «Неверный email или пароль» |
| После входа | ID сессии меняется, прежний ID не авторизует |
| После выхода | cookie удалена, сессия до выхода не авторизует |
