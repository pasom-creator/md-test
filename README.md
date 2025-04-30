
<h1 align="center" style="font-weight: bold;">Учебная задача. Разработка Лотерейной системы. 💻</h1>

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
- JWT authorisation
- Система логирования
- mock-Email service


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

4. После завершения сборки запустить докер-контейнер командой:

```bash
docker-compose up -d
```

<h2 id="routes">📍 API Endpoints</h2>

В таблице приведены основные API для взаимодействия с веб-приложением, с использованием программы командной строки **CURL**.
​
|                API              |                                 Описание
|:----------------------:|:-----------------------------------------------------:|
| POST /auth/register     | Создать пользователя (группа - Admin или User) [пример команды](#post-registration)
| POST /auth/login    | Авторизировать пользователя в системе и выдать JWT токен [пример команды](#post-auth-detail)
| POST /api/draws/admin     | Создать тираж лотерии (доступна группе Admin) [пример команды](#post-create-draw)
| GET /api/draws/active     | Получить список активных тиражей лотереи [пример команды](#get-active-draw)
| PUT /api/draws/{Id}/cancel/admin     | Отменить тираж лотереи по Id (доступна группе Admin) [пример команды](#put-cancel-draw)
\
\
\
<h3 id="post-registration">POST /register</h3>

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
\
\
<h3 id="post-auth-detail">POST /auth/login</h3>

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
\
\
<h3 id="post-create-draw">POST /api/draws/admin</h3>

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
<h3 id="get-active-draw">GET /api/draws/active</h3>

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
\
\
<h3 id="put-cancel-draw">PUT /api/draws/{Id}/cancel/admin</h3>

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
