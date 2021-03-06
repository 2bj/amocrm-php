# Клиент для работы с API amoCRM

[![Build Status](https://travis-ci.org/dotzero/amocrm-php.svg?branch=master)](https://travis-ci.org/dotzero/amocrm-php)
[![Latest Stable Version](https://poser.pugx.org/dotzero/amocrm/version)](https://packagist.org/packages/dotzero/amocrm)
[![License](https://poser.pugx.org/dotzero/amocrm/license)](https://packagist.org/packages/dotzero/amocrm)
[![Code Coverage](https://scrutinizer-ci.com/g/dotzero/amocrm-php/badges/coverage.png?b=master)](https://scrutinizer-ci.com/g/dotzero/amocrm-php/?branch=master)
[![Scrutinizer Code Quality](https://scrutinizer-ci.com/g/dotzero/amocrm-php/badges/quality-score.png?b=master)](https://scrutinizer-ci.com/g/dotzero/amocrm-php/?branch=master)

Клиент для работы с API сервиса [amoCRM](https://www.amocrm.ru/)

## Установка

### Через composer:

```bash
$ composer require dotzero/amocrm
```

или добавить

```json
"dotzero/amocrm": ">=0.3.0"
```

в секцию `require` файла composer.json.

## Быстрый старт

```php
try {
    // Создание клиента
    $amo = new \AmoCRM\Client('SUBDOMAIN', 'LOGIN', 'HASH');

    // Получение экземпляра модели для работы с аккаунтом
    $account = $amo->account;

    // Вывод информации об аккаунте
    print_r($account->apiCurrent());

    // Получение экземпляра модели для работы с контактами
    $contact = $amo->contact;

    // Заполнение полей модели
    $contact['name'] = 'ФИО';
    $contact['request_id'] = '123456789';
    $contact['date_create'] = '-2 DAYS';
    $contact['responsible_user_id'] = 697344;
    $contact['company_name'] = 'ООО Тестовая компания';
    $contact['tags'] = ['тест1', 'тест2'];
    $contact->addCustomField(448, [
        ['+79261112233', 'WORK'],
    ]);

    // Добавление нового контакта и получение его ID
    print_r($contact->apiAdd());

} catch (\AmoCRM\Exception $e) {
    printf('Error (%d): %s' . PHP_EOL, $e->getCode(), $e->getMessage());
}
```

## Список доступных моделей

- Account ([документация](https://developers.amocrm.ru/rest_api/#account))
- Contact ([документация](https://developers.amocrm.ru/rest_api/#contact))
- Lead ([документация](https://developers.amocrm.ru/rest_api/#lead))
- Company ([документация](https://developers.amocrm.ru/rest_api/#company))
- Task ([документация](https://developers.amocrm.ru/rest_api/#tasks))
- Note ([документация](https://developers.amocrm.ru/rest_api/#event))

## Описание моделей и методов

- Модель `account` для работы с Аккаунтом

    * `apiCurrent($short = false)` - Получение информации по аккаунту в котором произведена авторизация

- Модель `contact` для работы с Контактами

    * `apiList($parameters, $modified = null)` - Метод для получения списка контактов с возможностью фильтрации и постраничной выборки
    * `apiAdd($contacts = [])` - Метод позволяет добавлять контакты по одному или пакетно
    * `apiUpdate($id, $modified = 'now')` - Метод позволяет обновлять данные по уже существующим контактам
    * `apiLinks($parameters, $modified = null)` - Метод для получения списка связей между сделками и контактами

- Модель `company` для работы с Компаниями

    * `apiList($parameters, $modified = null)` - Метод для получения списка компаний с возможностью фильтрации и постраничной выборки
    * `apiAdd($companies = [])` - Метод позволяет добавлять компании по одной или пакетно
    * `apiUpdate($id, $modified = 'now')` - Метод позволяет обновлять данные по уже существующим компаниям

- Модель `lead` для работы со Сделками

    * `apiList($parameters, $modified = null)` - Метод для получения списка сделок с возможностью фильтрации и постраничной выборки
    * `apiAdd($leads = [])` - Метод позволяет добавлять сделки по одной или пакетно
    * `apiUpdate($id, $modified = 'now')` - Метод позволяет обновлять данные по уже существующим сделкам

- Модель `note` для работы с Примечаниями (Задачами)

    * `apiList($parameters, $modified = null)` - Метод для получения списка примечаний с возможностью фильтрации и постраничной выборки
    * `apiAdd($notes = [])` - Метод позволяет добавлять примечание по одному или пакетно
    * `apiUpdate($id, $modified = 'now')` - Метод позволяет обновлять данные по уже существующим примечаниям

- Модель `task` для работы с Задачами

    * `apiList($parameters, $modified = null)` - Метод для получения списка задач с возможностью фильтрации и постраничной выборки
    * `apiAdd($tasks = [])` - Метод позволяет добавлять задачи по одной или пакетно
    * `apiUpdate($id, $text, $modified = 'now')` - Метод позволяет обновлять данные по уже существующим задачам

## Описание хелпера

Для хранения ID полей можно воспользоваться хелпером `Fields`

```php
try {
    $amo = new \AmoCRM\Client(getenv('DOMAIN'), getenv('LOGIN'), getenv('HASH'));

    // Для хранения ID полей можно воспользоваться хелпером \AmoCRM\Helpers\Fields
    $amo->fields->StatusId = 10525225;
    $amo->fields->ResponsibleUserId = 697344;

    // Добавление сделок с использованием хелпера
    $lead = $amo->lead;
    $lead['name'] = 'Тестовая сделка';
    $lead['status_id'] = $amo->fields->StatusId;
    $lead['price'] = 3000;
    $lead['responsible_user_id'] = $amo->fields->ResponsibleUserId;
    $lead->apiAdd();

    // Также можно просто использовать хелпер без клиента
    $fields = new \AmoCRM\Helpers\Fields();

    // Как объект
    $fields->StatusId = 10525225;
    $fields->ResponsibleUserId = 697344;

    // Или как массив
    $fields['StatusId'] = 10525225;
    $fields['ResponsibleUserId'] = 697344;

} catch (\AmoCRM\Exception $e) {
    printf('Error (%d): %s' . PHP_EOL, $e->getCode(), $e->getMessage());
}
```

## Webhooks

[WebHooks](https://developers.amocrm.ru/rest_api/webhooks.php) – это уведомление сторонних приложений посредством отправки уведомлений о событиях, произошедших в amoCRM. Вы можете настроить HTTP адреса ваших приложений и связанные с ними рабочие правила в настройках своего аккаунта, в разделе «API».

### Список доступных уведомлений

- Контакты
    - `contacts-add` - Создание контакта
    - `contacts-update` - Изменение контакта
    - `contacts-delete` - Удаление контакта

- Компании
    - `companies-add` - Создание компании
    - `companies-update` - Изменение компании
    - `companies-delete` - Удаление компании

- Сделки
    - `leads-add` - Создание сделки
    - `leads-update` - Изменение сделки
    - `leads-delete` - Удаление сделки
    - `leads-status` - Смена статуса сделки
    - `leads-responsible` - Смена ответственного сделки

Обратите внимание, что при смене статуса сделки или при смене ответственного сделки, AmoCRM одновременно посылает информацию и об общем изменении сделки, то есть код для **leads-status** и **leads-responsible** всегда будет выполняться вместе с **leads-update.**

```php
try {
    $listener = new \AmoCRM\Webhooks();

    // Добавление обработчка на уведомление contacts->add
    $listener->on('contacts-add', function ($domain, $id, $data) {
        // $domain Поддомен amoCRM
        // $id Id объекта связаного с уведомленим
        // $data Поля возвращаемые уведомлением
    });

    // Вызов обработчика уведомлений
    $listener->listen();

} catch (\AmoCRM\Exception $e) {
    printf('Error (%d): %s' . PHP_EOL, $e->getCode(), $e->getMessage());
}
```

## Интеграция с фреймворками

- Yii Framework 1.x ([yii-amocrm](https://github.com/dotzero/yii-amocrm))
- Yii Framework 2.x ([yii2-amocrm](https://github.com/dotzero/yii2-amocrm))

## Тестирование

Для начала установить `--dev` зависимости. После чего запустить:

```bash
$ vendor/bin/phpunit
```

## Лицензия

Библиотека доступна на условиях лицензии MIT: http://www.opensource.org/licenses/mit-license.php
