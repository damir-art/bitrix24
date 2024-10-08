# Общее описание работы с REST в Битрикс24

## Использование методов REST
Чтобы использовать методы REST, есть три способа:
- создать вебхук в рамках Битрикс24,
- создать локальное приложение в рамках Битрикс24,
- создать тиражное решение и добавить его в партнёрский кабинет.

Схема вызова метода REST для локальных и тиражных решений:

    https://домен_Б24.bitrix24.{ru|com|de}/rest/имя_метода.транспорт?параметры_метода&auth=ключ_авторизации

- запрос может отправляться как методом GET, так и POST,
- `транспорт` - необязательный параметр, который может иметь значения `json` или `xml` *(по умолчанию - json)*,
- значения `параметров методов` принимаются в кодировке UTF-8,

Для вебхуков синтаксис вызова отличается, однако все равно содержит имя метода, параметры метода и транспорт. Фактически, это означает, что вы можете обращаться к методам REST API Битрикс24 используя любые языки программирования и средства разработки, которые поддерживают работу с HTTPS.

Помните об ограничениях на интенсивные запросы к REST (см. https://dev.1c-bitrix.ru/learning/course/index.php?COURSE_ID=99&LESSON_ID=10275 и видео в `links.md` "Вопросы производительности"). Существует лимит на число запросов. Разрешается два запроса в секунду. Если лимит превышается, то ограничение начинает срабатывать после 50 запросов.

Если Ваше приложение/вебхук создаёт аномальную нагрузку на портал, может сработать блокировка:

    [
      'error' => 'OVERLOAD_LIMIT',
      'error_description' => 'REST API is blocked due to overload.'
    ]

Для разблокировки, обратитесь в тех поддержку.

- все ключи входящих массивов должны передаваться в верхнем регистре,
- запросы нужно отправлять без BOM.

## Права на методы REST (скоупы)
Доступ приложений (и вебхуков) к методам REST API регулируется через доступ к скоупам - модулям Битрикс24, реализующим те или иные методы REST.

В случае с локальными приложениями и вебхуками, пользователь с правами на администрирование Битрикс24 указывает нужные скоупы при добавлении приложения или создании вебхука.

В случае с установкой тиражного приложения, приложение запрашивает у пользователя разрешение на доступ к необходимым скоупам и пользователь либо дает запрашиваемые права, либо прерывает установку приложения.

На текущий момент реализованы следующие скоупы: https://dev.1c-bitrix.ru/rest_help/rest_sum/premissions_scope.php

## Вызов методов с подтверждением
https://dev.1c-bitrix.ru/rest_help/rest_sum/call_confirmation.php

Некоторые методы требуют разрешения администратора портала на вызов. При вызове приложением такого метода администратор портала получит уведомление с предложением разрешить или запретить вызов, а приложение получит ошибку.

Внимание! Разрешение или запрет даются конкретному авторизационному токену, с которым происходит вызов метода. То есть, разрешение действует на время жизни токена и при получении следующего токена нужно получать новое разрешение.

    GET https://portal.bitrix24.ru/rest/voximplant.user.get?auth=fkp963yuv1ggkfbs5z3f5hy8lilm0iw6&USER_ID=1
    HTTP/1.1 401 Unauthorized
    {
      "error": "METHOD_CONFIRM_WAITING", 
      "error_description": "Waiting for confirmation"
    }

## Ограничение времени выполнения запроса
С версии модуля REST 22.0.0, в облачной версии Битрикс24, во всех ответах REST-запросов в массиве `time` с дополнительной информацией о времени выполнения запроса, добавлен дополнительный ключ `operating`, который говорит о времени выполнения запроса к методу конкретным приложением.

Данные о времени выполнения запросов к методу суммируются. При превышении времени выполнения запросов сверх 480 секунд в рамках прошедших 10 минут данный метод блокируется для всех приложений и веб-хуков данного портала. При этом все остальные методы продолжают работать.

Ключ `operating_reset_at` возвращает `timestamp` в которое будет высвобождена часть лимита на данный метод.

## Как правильно выгружать большие объемы данных
https://dev.1c-bitrix.ru/rest_help/rest_sum/start.php

Часто встает задача импорта каких либо сущностей с портала посредством REST. При этом при большом количестве сущностей прямой подход к задаче, с установкой фильтра и передачей в каждый следующий запрос `start = start+50`, не оптимальный.

Так как, при использовании `start >= 0` на каждый запрос выполняется еще и запрос подсчета количества элементов удовлетворяющих фильтру. Что при большом количестве элементов, попадающих в него, или при сложной фильтрации работает медленно.

Поэтому в случае если вам не нужно количество элементов *(например вам нужно просто 10 последних записей)* или вы делаете импорт всех записей по фильтру, передавайте `start = -1`.

Данный параметр отключит выполнение запроса подсчета количества элементов и сильно ускорит выборку.

Для выполнения импорта, при этом необходимо будет отсортировать записи по `ID` и добавить в фильтр условие `ID > значения последнего элемента`. И с каждым шагом увеличивать его значение. Значение же последнего элемента брать из последнего значения полученного результата.

Условием остановки импорта будет пустой ответ, или то, что в ответе элементов меньше `50`.

Ниже приведен пример кода и сравнение времени выполнения с классическим подходом. Так при `2 387 743` лидах, с одинаковыми правами и фильтром, время выполнения уменьшилось с `49.9` секунд до `0,097` секунд. 

## Особенности вызовов REST при изменении адреса Битрикс24
https://dev.1c-bitrix.ru/rest_help/rest_sum/change_domen.php
