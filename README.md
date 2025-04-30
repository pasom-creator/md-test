
<h1 align="center" style="font-weight: bold;">Сервис лотерейных тиражей 💻</h1>

<p align="center">
<a href="#tech">Технологии</a>
<a href="#started">Настойка</a>
<a href="#routes">API Endpoints</a>
 
</p>


<p align="center">Создание системы для проведения лотерейных тиражей с возможностью покупки билетов, определения выигрышей и интеграции с платежными системами.</p>

<h2 id="#tech">💻 Технологии</h2>

Для реализации проекта использовались:
- Java 17 
- Spring Boot 3.x (Spring Web, Spring Data JPA, Spring Security)
- PostgreSQL
- Аутентификация с использованием JWT-токена.
- Система логирования slf4j + logback
- Mock SMTP-сервис для эмуляции отправки почтовых писем
- Docker


<h3>Требования к системе</h3>

Для корректной работы и тестирования сервиса необходимо наличие следующего установленного и настроенного программного обеспечения:


- Docker
- Система контроля версий Git
- Программа командной строки curl или ПО Postman для тестирования API
- DBeaver или pgAdmin для просмотра сущностей в базе данных

<h2 id="started">🚀 Настройка</h2>

1. Первоначально необходимо склонировать репозиторий сервиса с помощью команды:

```bash
git clone https://github.com/euchekavelo/lottery-draw-service
```

2. Для корректоной работы mock-Email service на сайте https://ethereal.email/ создать почтовый ящик и записать его в конфигурационный файл docker-compose.yml

```yml
environment:
      - SMTP_USERNAME=адрес почтого ящика
      - SMTP_PASSWORD=пароль почтого ящика
```

3. Затем перейти в корневую директорию проекта и запустить сборку проекта командой:

```bash
./gradlew clean bootJar
```

4. После завершения сборки запустить связку докер-контейнеров командой:

```bash
docker-compose up -d
```

<h2 id="routes">📍 API Endpoints</h2>

В таблице приведены основные API для взаимодействия с веб-приложением, с использованием программы командной строки **CURL**.
​
|                API              |                                 Описание
|:----------------------:|:-----------------------------------------------------:|
| POST /auth/register     | Создать пользователя с ролью USER или ADMIN [пример команды](#post-registration)
| POST /auth/login    | Аутентификация пользователя в системе (выдача токена для доступа) [пример команды](#post-auth-detail)
| POST /api/draws/admin     | Создать тираж лотерии (доступна роли ADMIN) [пример команды](#post-create-draw)
| GET /api/draws/active     | Получить список активных тиражей лотереи [пример команды](#get-active-draw)
| PUT /api/draws/{id}/cancel/admin     | Отменить тираж лотереи по id (доступна роли ADMIN) [пример команды](#put-cancel-draw)
| GET /api/draws/completed | Получить список завершённых тиражей лотереи [пример команды](#get-complete-draw)
| GET /api/draws/{id}/results | Получить выигрышную комбинацию тиража лотереи по id [пример команды](#get-winningcombination-draw)
| GET /api/tickets| Получить список всех билетов пользователя [пример команды](#get-user-tickets)
| GET /api/tickets/{id}| Получить информацию о билете пользователя по id [пример команды](#get-ticket-detail)
| GET /api/tickets/{id}/check-result| Проверка результата билета пользователя по id [пример команды](#get-ticket-result)
| POST /api/invoice| Создать инвойс [пример команды](#post-create-invoice)
| POST /api/payments| Оплатить покупку билета [пример команды](#post-buy-ticket)
| POST /api/exports/winnings| Начислить выигрыш (доступна роли ADMIN) [пример команды](#post-credit-winnings)
||
||


<h3 id="post-registration">Создание пользователя с ролью ADMIN или USER</h3>

**REQUEST**
```bash
curl -X POST http://localhost:8080/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "fullName": "Admin A",
    "login": "administrator",
    "password": "password123",
    "role": "ADMIN"
  }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   181    0    68  100   113    102    170 --:--:-- --:--:-- --:--:--   273{"id":1,"login":"administrator","fullName":"Admin A","role":"ADMIN"}
```
<br/><br/>

<h3 id="post-auth-detail">Аутентификация пользователя в системе</h3>

**REQUEST**
```bash
  curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "login": "administrator",
    "password": "password123"
  }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   261    0   196  100    65   1625    539 --:--:-- --:--:-- --:--:--  2175{"token":"eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIiwiaWF0IjoxNzQ1OTMxNjgxLCJleHAiOjE3NDU5MzUyODF9.VgrYLaaHYQK76vDTsF7MAqYM2qatesM5nluL5Gk_72uWjiF5MXuv92yrcOIIfQLeugobQZ-NdEFTO2AhL6g6wQ"}
```
<br/><br/>

<h3 id="post-create-draw">Создание тиража лотереи</h3>

**REQUEST**
```bash
curl -X POST "http://localhost:8080/api/draws/admin" \
   -H "Content-Type: application/json"  \
   -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIiwiaWF0IjoxNzQ1OTMxNjgxLCJleHAiOjE3NDU5MzUyODF9.VgrYLaaHYQK76vDTsF7MAqYM2qatesM5nluL5Gk_72uWjiF5MXuv92yrcOIIfQLeugobQZ-NdEFTO2AhL6g6wQ" \
   -d '{
    "lotteryType": "AUTO",
    "startTime": "2025-04-29T16:05:00",
    "finishTime": "2025-05-01T20:00:00"
  }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   210    0    98  100   112   1485   1698 --:--:-- --:--:-- --:--:--  3230{"id":1,"lotteryType":"AUTO","startTime":"2025-04-29T16:05:00","finishTime":"2025-05-01T20:00:00"}
```
\
\
<h3 id="get-active-draw">Получение списка активных тиражей лотереи</h3>

**REQUEST**
```bash
curl -X GET "http://localhost:8080/api/draws/active"
```

**RESPONSE**
```bash
 % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   118    0   118    0     0   4614      0 --:--:-- --:--:-- --:--:--  4538[{"id":1,"lotteryType":"AUTO","startTime":"2025-04-29T16:05:00","finishTime":"2025-05-01T20:00:00","status":"ACTIVE"}]
```
<br/><br/>

<h3 id="put-cancel-draw">Отмена тиража лотереи</h3>

**REQUEST**
```bash
curl -X PUT "http://localhost:8080/api/draws/1/cancel/admin"   -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIiwiaWF0IjoxNzQ1OTkwMzkzLCJleHAiOjE3NDU5OTM5OTN9.DIGYUtoiUf_sUyXeLdKtt5zhxxQQ87-nROREgkZodMu-XdX9Iq2Zhh7hpnbvDvBhQBuCHP-7J8oDRV8xf4yqcg"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
```
<br/><br/>

<h3 id="get-complete-draw">Получение списока завершённых тиражей лотереи</h3>

**REQUEST**
```bash
curl -X GET "http://localhost:8080/api/draws/completed"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   121    0   121    0     0  12291      0 --:--:-- --:--:-- --:--:-- 13444[{"id":2,"lotteryType":"AUTO","startTime":"2025-04-29T16:10:00","finishTime":"2025-04-30T09:05:00","status":"COMPLETED"}]
```
<br/><br/>

<h3 id="get-winningcombination-draw">Получение выигрышной комбинации тиража лотереи</h3>

**REQUEST**
```bash
curl -X GET "http://localhost:8080/api/draws/1/results"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    39    0    39    0     0   2060      0 --:--:-- --:--:-- --:--:--  2166{"winningCombination":"45 88 82 16 18"}
```
<br/><br/>

<h3 id="get-user-tickets">Получние списока всех билетов пользователя</h3>

**REQUEST**
```bash
curl -X GET http://localhost:8080/api/tickets -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ0ZXN0dXNlciIsImlhdCI6MTc0NTk1NjE5MSwiZXhwIjoxNzQ1OTU5NzkxfQ.82dQQadhIXuxVQaQaY8Q_6on2DwMvdVVUGh0SdzoM-aDgxsti3dFGUZuu2hGDk4Dukc8N5GpBgZhhj7Dje7NWg"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   148    0   148    0     0   5610      0 --:--:-- --:--:-- --:--:--  5692[{"id":1,"userId":2,"drawId":1,"data":"9 13 27 45 85","status":"PENDING"},{"id":2,"userId":2,"drawId":2,"data":"58 61 74 75 86","status":"PENDING"}]
```
<br/><br/>

<h3 id="get-ticket-detail">Получение информацию о билете пользователя</h3>

**REQUEST**
```bash
curl -X GET http://localhost:8080/api/tickets/3 -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjI5OTksImV4cCI6MTc0NjAyNjU5OX0.iltIEGZj1-EARHiMKBzDtR65n9yTTVoNCeFFrZPaLLYXlWCv0kVXdkgy0SMNz3VuNVt_vDXO4o5NfAibmQToZw"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    69    0    69    0     0   2023      0 --:--:-- --:--:-- --:--:--  2029{"id":3,"userId":4,"drawId":2,"data":"45 88 82 16 18","status":"WIN"}
```
<br/><br/>

<h3 id="get-ticket-result">Проверка результата билета пользователя</h3>

**REQUEST**
```bash
curl -X GET http://localhost:8080/api/tickets/3/check-result -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjI5OTksImV4cCI6MTc0NjAyNjU5OX0.iltIEGZj1-EARHiMKBzDtR65n9yTTVoNCeFFrZPaLLYXlWCv0kVXdkgy0SMNz3VuNVt_vDXO4o5NfAibmQToZw"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    69    0    69    0     0   3125      0 --:--:-- --:--:-- --:--:--  3136{"id":3,"userId":4,"drawId":2,"data":"45 88 82 16 18","status":"WIN"}
```
<br/><br/>

<h3 id="post-create-invoice">Создание инвойса</h3>

**REQUEST**
```bash
curl -X POST \
  "http://localhost:8080/api/invoice" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjY4NjEsImV4cCI6MTc0NjAzMDQ2MX0.3mWMSgKkFcYcmN_EaqWpwPywU2Ws_gvhKNMhifwtJschj3nMsqjOj07IroJLtO1FGR1ys0_t263i8K34LkeNqg" \
  -d '{
    "ticketData": {
	     "userId": 4,
      "drawId": 3,
      "numbers": "45 43 21 8 18"
    }
  }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   243    0   144  100    99   4452   3061 --:--:-- --:--:-- --:--:--  7593{"id":5,"ticketData":"{\"userId\":4,\"drawId\":3,\"numbers\":\"45 43 21 8 18\"}","registerTime":"2025-04-30T18:38:07.917201","status":"PENDING"}
```
<br/><br/>

<h3 id="post-buy-ticket">Покупка билета</h3>

**REQUEST**
```bash
curl -X POST \
  "http://localhost:8080/api/payments" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjY4NjEsImV4cCI6MTc0NjAzMDQ2MX0.3mWMSgKkFcYcmN_EaqWpwPywU2Ws_gvhKNMhifwtJschj3nMsqjOj07IroJLtO1FGR1ys0_t263i8K34LkeNqg" \
  -d '{
    "invoiceId": 5,
    "cardNumber": "4276450011113411",
        "cvc": "123",
    "amount": 123.25
    }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100   344    0   243  100   101   3583   1489 --:--:-- --:--:-- --:--:--  5134{"id":4,"amount":123.25,"status":"SUCCESS","paymentTime":"2025-04-30T18:50:01.661327","invoice":{"id":5,"ticketData":"{\"userId\":4,\"drawId\":3,\"numbers\":\"45 43 21 8 18\"}","registerTime":"2025-04-30T18:38:07.917201","status":"COMPLETED"}}
```
<h3 id="post-credit-winnings">Выплата выигрыша</h3>

**REQUEST**
```bash
curl -X POST \
  "http://localhost:8080/api/exports/winnings" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbmlzdHJhdG9yIiwiaWF0IjoxNzQ2MDAyMTczLCJleHAiOjE3NDYwMDU3NzN9.R9b_CWkL7Mnh-YBE_524a7B1-HqaiMQevfZV-Z1suTaFyV74jiNKLB54F1Yb-pNfWXidRvdl7mYRBF31xBHeTQ" \
  -d '{
    "drawId": 1,
    "winningAmount": 3000
    }'
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    69    0    69    0     0   3125      0 --:--:-- --:--:-- --:--:--  3136{"id":3,"userId":4,"drawId":2,"data":"45 88 82 16 18","status":"WIN"}
```
<br/><br/>

<h3 id="get-ticket-result">Проверка результата билета пользователя</h3>

**REQUEST**
```bash
curl -X GET http://localhost:8080/api/tickets/3/check-result -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjI5OTksImV4cCI6MTc0NjAyNjU5OX0.iltIEGZj1-EARHiMKBzDtR65n9yTTVoNCeFFrZPaLLYXlWCv0kVXdkgy0SMNz3VuNVt_vDXO4o5NfAibmQToZw"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    69    0    69    0     0   3125      0 --:--:-- --:--:-- --:--:--  3136{"id":3,"userId":4,"drawId":2,"data":"45 88 82 16 18","status":"WIN"}
```
<br/><br/>

<h3 id="get-ticket-result">Проверка результата билета пользователя</h3>

**REQUEST**
```bash
curl -X GET http://localhost:8080/api/tickets/3/check-result -H "Authorization: Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJib2xkbWFuQG1haWwucnUiLCJpYXQiOjE3NDYwMjI5OTksImV4cCI6MTc0NjAyNjU5OX0.iltIEGZj1-EARHiMKBzDtR65n9yTTVoNCeFFrZPaLLYXlWCv0kVXdkgy0SMNz3VuNVt_vDXO4o5NfAibmQToZw"
```

**RESPONSE**
```bash
% Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
Dload  Upload   Total   Spent    Left  Speed
100    69    0    69    0     0   3125      0 --:--:-- --:--:-- --:--:--  3136{"id":3,"userId":4,"drawId":2,"data":"45 88 82 16 18","status":"WIN"}
```
<br/><br/>
