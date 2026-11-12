# Результат php checks/check.php http://localhost:8080 all admin@example.test

```
PASS Маршрут /login: HTTP 405 (получен 405)
PASS Allow /login
PASS Маршрут /register: HTTP 405 (получен 405)
PASS Allow /register
PASS Маршрут /items: HTTP 405 (получен 405)
PASS Allow /items
PASS Маршрут /items/1/edit: HTTP 405 (получен 405)
PASS Allow /items/1/edit
PASS Маршрут /items/1/delete: HTTP 405 (получен 405)
PASS Allow /items/1/delete
PASS Маршрут /logout: HTTP 405 (получен 405)
PASS Allow /logout
PASS Каталог гостя: HTTP 200 (получен 200)
PASS Гость не видит управление
PASS Гость: форма добавления: HTTP 303 (получен 303)
PASS Гость: форма добавления: Location /login
PASS Гость: прямой POST: HTTP 303 (получен 303)
PASS Гость: прямой POST: Location /login
PASS Cookie HttpOnly
PASS Cookie SameSite=Lax
PASS Cookie без постоянного срока
PASS Регистрация: CSRF: HTTP 403 (получен 403)
PASS Подтверждение пароля: HTTP 422 (получен 422)
PASS Некорректный email: HTTP 422 (получен 422)
PASS Короткий пароль: HTTP 422 (получен 422)
PASS Запрет поля role: HTTP 422 (получен 422)
PASS Регистрация: HTTP 303 (получен 303)
PASS Регистрация: Location /login
PASS Flash регистрации
PASS Flash только один раз
PASS Дубликат email без учёта регистра: HTTP 422 (получен 422)
PASS Неверный пароль: HTTP 422 (получен 422)
PASS Вход: CSRF: HTTP 403 (получен 403)
PASS Вход: HTTP 303 (получен 303)
PASS Вход: Location /items
PASS Обновление ID сессии
PASS Старый ID не авторизован: HTTP 303 (получен 303)
PASS Старый ID не авторизован: Location /login
PASS Каталог после входа: HTTP 200 (получен 200)
PASS Имя экранировано
PASS Пользователь: прямой GET редактирования: HTTP 403 (получен 403)
PASS Пользователь: прямой POST редактирования: HTTP 403 (получен 403)
PASS Пользователь: прямой POST удаления: HTTP 403 (получен 403)
PASS Добавление: CSRF: HTTP 403 (получен 403)
PASS Добавление: проверка полей: HTTP 422 (получен 422)
PASS Добавление: HTTP 303 (получен 303)
PASS Адрес созданной записи
PASS Заголовок экранирован
PASS Flash добавления
PASS Обновление GET не повторяет flash
PASS Отсутствующая запись: HTTP 404 (получен 404)
PASS Поиск находит добавленную запись
PASS Вход администратора: HTTP 303 (получен 303)
PASS Вход администратора: Location /items
PASS Редактирование: CSRF: HTTP 403 (получен 403)
PASS Редактирование: проверка полей: HTTP 422 (получен 422)
PASS Редактирование чужой записи: HTTP 303 (получен 303)
PASS Редактирование чужой записи: Location /items/5
PASS Сохранены новые значения
PASS Flash редактирования
PASS Сохранение без изменения значений: HTTP 303 (получен 303)
PASS Сохранение без изменения значений: Location /items/5
PASS Редактирование отсутствующей записи: HTTP 404 (получен 404)
PASS Удаление: CSRF: HTTP 403 (получен 403)
PASS Удаление чужой записи: HTTP 303 (получен 303)
PASS Удаление чужой записи: Location /items
PASS Удалённая запись отсутствует: HTTP 404 (получен 404)
PASS Flash удаления
PASS Повторное удаление: HTTP 404 (получен 404)
PASS Выход только POST: HTTP 405 (получен 405)
PASS Выход: CSRF: HTTP 403 (получен 403)
PASS Выход: HTTP 303 (получен 303)
PASS Выход: Location /login
PASS Cookie сессии удалена
PASS После выхода добавление запрещено: HTTP 303 (получен 303)
PASS После выхода добавление запрещено: Location /login
PASS Сессия до выхода больше не авторизует: HTTP 303 (получен 303)
PASS Сессия до выхода больше не авторизует: Location /login
RESULT PASS 78 checks; step=all
```
