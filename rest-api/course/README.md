# Приложения Битрикс24.Маркет
https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=99

## Приложения и интеграции с Битрикс24
Приложения в `Битрикс24` можно делать как для конкретного `Битрикс24`, так и для всех `Битрикс24.Маркет`.

Всего есть три вида приложений:
- Вебхуки *(конкретный проект)* - следи за правами,
- Локальные приложения *(конкретный проект)* - нужен OAuth 2.0,
- Публичные тиражные решения Битрикс24 *(для всех)*.

## Вебхуки
Вебхуки идеально подходят:

- Для организации одноразового импорта-экспорта данных,
- Простых интеграций с внешними и внутренними системами компании *(ERP, учет рабочего времени, авто-мониторинг программных и аппаратных средств и т.д.)*,
- Использования в автоматизации обработки лидов и сделок *(триггеры и действия в CRM-роботах)*.

Использование вебхуков проще для технической реализации, поскольку `не требует` реализации протокола `OAuth 2.0`. Каждый пользователь может добавлять вебхуки *для себя* и REST API, вызываемый в рамках таких вебхуков, будет `ограничиваться правами` конкретного пользователя-владельца. 

## Локальные приложения
Локальные приложения лучше подходят для тех задач, которые требуют создания `пользовательского интерфейса`:

- Различные отчеты,
- Дополнительные автообработчики в рамках специфической бизнес-логики,
- Решения, требующие управления доступом пользователей,
- Чат-боты и приложения, расширяющие функционал мессенджера Битрикс24,
- Дополнительные операции для автоматических бизнес-процессов Битрикс24.

Локальные приложения поддерживают все возможности REST API и вызывают REST по протоколу авторизации `OAuth 2.0`, но устанавливаются только на конкретном Битрикс24, без публикации в каталоге решений. Для добавления локального приложения требуется административный доступ.

## Раздел разработчика в Битрикс24
