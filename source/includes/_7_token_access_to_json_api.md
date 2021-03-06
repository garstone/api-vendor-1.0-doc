## Доступ по токену к JSON API 


Токен для доступа к JSON API 1.2 передается вендору при активации приложения через Vendor API в момент установки или 
возобновления приложения. Время жизни токена не ограничено.

Токен аннулируется при удалении и приостановке приложения. На момент деактивации приложения через Vendor API токен уже 
нерабочий (аннулирован).

Токен при доступе к JSON API 1.2 следует передавать как [Bearer-токен](https://dev.moysklad.ru/doc/api/remap/1.2/#mojsklad-json-api-obschie-swedeniq-autentifikaciq),
 а именно в виде заголовка HTTP-запроса:
 
 ```text
Authorization: Bearer <access_token>
 ```

Пример:

```text
Authorization: Bearer 6ab89be1ae6ff147755625ee8da948e42612233b
```

#### Диаграмма последовательности предоставления доступа при подключении приложения

![useful image](diag_install.png)

#### Диаграмма последовательности отзыва доступа при отключении приложения

![useful image](diag_uninstall.png)



 
### Работа с вебхуками

Если ваше приложение может создать вебхук(-и) на аккаунте пользователя, то нужно иметь в виду следующее поведение:

* Вебхуки доступны только на пробном и платных тарифах
* Перед процессом удаления приложения, инициированного пользователем, МойСклад удалит все созданные приложением вебхуки.
* Перед процессом приостановки приложения (для платных приложений) вебхуки будут отключены, а после возобновления - 
включены.

Подробную информацию о работе с вебхуками можно получить по ссылке
[https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-veb-huki](https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-veb-huki) 

### Особенности доступа к некоторым функциям JSON API 1.2

На данный момент для приложений существует ряд ограничений в работе со следующими сущностями JSON API 1.2:

+ приложение не может быть автором Задач [https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-zadacha](https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-zadacha)
+ приложение не может работать с Событиями Контрагентов [https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-kontragent-sobytiq-kontragenta](https://dev.moysklad.ru/doc/api/remap/1.2/dictionaries/#suschnosti-kontragent-sobytiq-kontragenta)
+ приложение не может использовать Шаблоны документов [https://moysklad.github.io/api-remap-1.2-doc/api/remap/1.2/ru/documents/#dokumenty-obschie-swedeniq-shablony-dokumentow](https://moysklad.github.io/api-remap-1.2-doc/api/remap/1.2/ru/documents/#dokumenty-obschie-swedeniq-shablony-dokumentow)
