# События
https://dev.1c-bitrix.ru/rest_help/rest_sum/events/events.php

Интерфейс REST позволяет устанавливать свои обработчики серверных событий.

Обработчиком служит URL, который будет вызван после того, как пользователь произведет запрошенное действие на портале Битрикс24, на который установлено приложение. Поскольку запросы будут идти с серверов Битрикс, то любой URL должен быть доступен для GET/POST запросов извне.

Обработчик получает на вход следующие данные:
- `event` - имя сработавшего события,
- `data` - массив данных события, зависит от события,
- `auth` - набор данных авторизации.
