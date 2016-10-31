# Требования к API
### Базовые требования:
1. URL запросов формируется так: 
<*Базовый URL*>/<*Версия запроса*>/<*путь*>
Базовый URL = https://dricom.ru/api/
Например: https://dricom.ru/api/1/register
2. Каждый запрос должен завершиться с соответствующим кодом и вернуть JSON объект. 
    - В случае успеха 
        - код 2xx (например 200 OK) или 3xx (например 304 Not modified)
        - тип возвращаемого объекта определяется для конкретного запроса
    - В случае ошибки
        - код 4xx (например 401 Unauthorized) или 5xx в случае ошибки на сервере 
        - объект Error с полями *code* (Int) и *message* (String). Пример:
    ```json
	{
		"error": {
		    // Int, коды соответствуют кодам ошибок Http
			"code": 401, 
			// String, сообщение для пользователя
			"message": "Необходима авторизация в приложении"
		}
	}
	```
### Описание методов API
#### Регистрация нового пользователя.
`POST /1/register`
|Параметр|Обязательность|Расположение|Тип    |Описание
|--------|--------------|------------|-------|---
|name    |      v       |formData    |String |Имя пользователя
|license |      v       |formData    |String |Номер автомобиля
|phone   |      v       |formData    |String |Номер телефона
|email   |      v       |formData    |String |Адрес электронной почты
|password|      v       |formData    |String |Пароль
|token   |              |formData    |String |токен для push-уведомлений

Схема ответа: `user.register.v.1`
```json
{
	"user": {	// получаем объект зарегистрированного пользователя
		"name": "Ivan Bondar",
		"license": "P245YC77RUS",
		"phone": "+79055656146"
		"email": "ivan@mail.ru"
	},
	"session": "kjdf8374jdf9893843jjdff98"
}
```
> По поводу сессии надо подумать и обсудить этот вопрос отдельно. Суть в том, чтобы каким-то образом подписывать запросы авторизованной зоны.

Код ответа|Описание
----------|---
200       |OK
403       | Неверные параметры регистрации.

>Нужно проверить что все значения уникальны.
Должно приходить нормальное сообщение, например "Пользователь с таким мобильным телефоном уже зарегистрирован" - тексты думаю самим можно придумывать, потом Федор подправит если что.
Здесь нужно обсудить как делать валидацию формата полей (телефон, номер, email, пароль возможно). Правильный вариант - на сервере, но для экономии времени можем сначала сделать на клиенте. Кроме того, пароль не стоит передавать и хранить в открытом виде, можно передавать hash пароля.

Пока хватит. С картинками потом придумаем.
#### Аутентификация
`POST /1/auth`
|Параметр|Обязательность|Расположение|Тип    |Описание
|--------|--------------|------------|-------|---
|name    |      v       |formData    |String |Имя пользователя
|password|      v       |formData    |String |Пароль
|token   |              |formData    |String |токен для push-уведомлений
Схема ответа: `user.login.v.1` 
```json
{
	"user": {	// получаем объект зарегистрированного пользователя
		"name": "Ivan Bondar",
		"license": "P245YC77RUS",
		"phone": "+79055656146"
		"email": "ivan@mail.ru"
	},
	"session": "kjdf8374jdf9893843jjdff98"
}
```
>формат совпадает с регистрацией, но лучше сделать 2 разных типа

Код ответа|Описание
----------|---
200       |OK
403       | Неверная пара логин/пароль. Или не заполнено одно из полей.