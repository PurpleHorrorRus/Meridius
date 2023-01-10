# Настройка веб-сервера Meridius

>## **TL;DR**: Если вы не знаете, что это такое, то это вам и не нужно.


В этом руководстве кратко рассказывается про работу с веб-сервером Meridius и показаны примеры для подключения к нему.

Веб-сервер необходим для интеграции сторонних приложений с Meridius. Также это позволит вам управлять плеером удалённо. Веб-сервер включает в себя три способа взаимодействия:

* Trivials (GET) запросы возвращают запрашиваемые данные.
* Actions (POST) запросы позволяют управлять плеером.
* Sockets (WS) позволяют отслеживать данные в реальном времени.

Выберите сетевой интерфейс, на котором будет запускаться сервер. Выбирайте внимательно и для своих целей. Например, интерфейс с адресом 127.0.0.1 (localhost) будет недоступен для доступа удалённо с другого устройства, а ваш интерфейс по-умолчанию (например, 192.168.0.100 для роутеров или ваш IP адрес) даст удалённый доступ, но вы будете уязвимы к различным видам атак. Для защиты в приложении предусмотрено использование пароля. Пароль в открытом виде не сохраняется, лишь единожды шифруется специальным ключом и расшифровывается для валидации при запросах, поэтому его необходимо где-нибудь записать. Пароль для GET и POST запросов передаётся в заголовке ```Authorization```, а для WS в ```query``` при подключении.

## Примеры

Примеры будут на языке Javascript. Как составлять запросы на вашем языке программирования, смотрите документации.

```javascript
// Инициализируем необходимые переменные
const host = "127.0.0.1"; // Адрес сервер
const port = 3000; // Порт сервера
const password = "root"; // Пароль сервера
```

### GET

```javascript
(async () => {
    const response = await fetch(`http://${host}:${port}/player/song`, {
        headers: {
            Authorization: password
        }
    });

    const json = await response.json();
    console.log(json);
})();
```

### POST

```javascript
(async () => {
    const response = await fetch(`http://${host}:${port}/player/NEXT`, {
        method: "POST",
        headers: {
            Authorization: password
        }
    });

    const json = await response.json();
    console.log(json);
})();
```

### WS
```javascript
const WebSocket = require("ws");

const createSocket = endpoint => {
    return new WebSocket(`ws://${host}:${port}/${endpoint}?password=${password}`);
};

const socket = createSocket("socket/player/song");
socket.onmessage = message => {
    const data = JSON.parse(message.data);
    console.log(data);
};
```
