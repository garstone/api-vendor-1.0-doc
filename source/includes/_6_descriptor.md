## Дескриптор приложения

Дескриптор приложения представляет собой XML-структуру, которая описывает технические параметры встраивания/интеграции 
приложения вендора в МойСклад.

Содержимое дескриптора должно соответствовать версии XSD-схемы.
Актуальной версией считается [2.0.0](https://online.moysklad.ru/xml/ns/appstore/app/v2/application-2.0.0.xsd).

**История версий XSD-схемы дескриптора:**

| Версия | Описание  | Разрешенное содержимое дескриптора  |  Поддерживаемые типы приложений  |  
|----|----|----|----|
|[1.0.0](https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.0.0.xsd)|Серверные и простые iFrame-приложения | vendorApi, access, iframe | Iframe, Серверные
|[1.1.0](https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd)|Расширение iFrame (тег expand) |  vendorApi, access, iframe(c expand) | Iframe, Серверные
|[2.0.0](https://online.moysklad.ru/xml/ns/appstore/app/v2/application-2.0.0.xsd)|Виджеты. Прекращена поддержка приложений с типом Iframe.  |vendorApi, access, iframe(c expand), widgets | Серверные


Сейчас рассмотрим дескриптор приложения на примерах.


### Содержимое дескриптора приложения

```xml
<application xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v1"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                  
      xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v1 
      https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd">
    <iframe>
        <sourceUrl>https://example.com/iframe.html</sourceUrl>
        <expand>true</expand>
    </iframe>
    <vendorApi>
        <endpointBase>https://example.com/dummy-app</endpointBase>
    </vendorApi>
    <access>
        <resource>https://online.moysklad.ru/api/remap/1.2</resource>
        <scope>admin</scope>
    </access>
</application>
```

На текущий момент в актуальной версии дескриптора приложения есть четыре блока: **iframe**, **vendorApi**, **access**, **widgets**. 
Порядок расположения этих блоков относительно друг друга в дескрипторе может быть произвольный.

|Блок|Назначение|Доступно для типов приложений|Требует наличия других блоков|
|----|----------|-----------------------------|-----------------------------|
|iframe|Описывает iframe-часть приложения.|iframe-приложения, серверные приложения|Не требует|
|vendorApi|Описывает взаимодействие по Vendor API.|серверные приложения|Не требует|
|access|Описывает требуемый доступ приложения к ресурсам пользовательского аккаунта.|серверные приложения|vendorApi|
|widgets|Описывает виджеты.|серверные приложения|Не требует|
#### Блок iframe

В теге **iframe/sourceUrl** указывается URL, по которому будет загружаться содержимое iframe внутри UI МоегоСклада. 
В URL допускается использование только протокола HTTPS.

В случае отсутствия блока **iframe** в дескрипторе считается, что у приложения отсутствует iframe-часть.

В теге **iframe/expand** указывается `boolean` значение, которое отвечает, будет ли iframe расширяться в основном приложении 
МоегоСклада. Под расширением подразумевается автоматическое масштабирование iframe элемента в зависимости от контента.
Данный элемент является опциональным со значением `false` по умолчанию. Чтобы расширение iframe работало верно, 
на странице, которая указана в **sourceUrl**, должен работать скрипт, оповещающий страницу МоегоСклада об изменении высоты 
его контента, то есть:

* Реализовать посылку сообщения “EventMessage” при любом изменении высоты контента, причем
    * сообщение послать parent окну
    * данные сообщения должны содержать свойство “height” - высоту содержимого, страницы, которая сейчас отображается (в px)

Для удобства можно добавить следующий [js скрипт](https://online.moysklad.ru/js/ns/appstore/app/v1/moysklad-iframe-expand-2.js) 
на свою страницу. Пример:


```html
<!doctype html>
<html>

<head>
...
</head>

<body>
...
<script type="text/javascript" src="https://online.moysklad.ru/js/ns/appstore/app/v1/moysklad-iframe-expand-2.js"></script>
</body>

</html>
```



#### Блок vendorApi

В теге **vendorApi/endpointBase**  указывается базовый URL эндпоинта на стороне вендора, к которому будет обращаться 
МойСклад. В URL допускается использование только протокола HTTPS.

Для получения полного адреса конкретного эндпоинта Vendor API на стороне вендора к базовому URL’у добавляется суффикс 
`/api/moysklad/vendor/1.0` и путь эндпоинта. То есть шаблон формирования полного URL ресурса в общем случае такой:

`{endpointBase}/api/moysklad/vendor/1.0/{endpointPath}/…`

Для эндпоинта активации/деактивации приложений на аккаунте шаблон следующий (endpointPath = apps):

`{endpointBase}/api/moysklad/vendor/1.0/apps/{appId}/{accountId}`

Например, если:

+ <endpointBase>https://example.com/dummy-app</endpointBase>
+ appId = 5f3c5489-6a17-48b7-9fe5-b2000eb807fe
+ accountId = f088b0a7-9490-4a57-b804-393163e7680f
+ endpointPath = apps

то полный URL ресурса на стороне вендора, к которому будет выполнять запросы МойСклад при активации (и деактивации) 
приложения на аккаунте, будет следующим: 

`https://example.com/dummy-app/api/moysklad/vendor/1.0/apps/5f3c5489-6a17-48b7-9fe5-b2000eb807fe/f088b0a7-9490-4a57-b804-393163e7680f`
 
В случае отсутствия блока vendorApi в дескрипторе не выполняется активация и деактивация приложения на серверах вендора.

#### Блок access

В тегах **access/resource** (в перспективе их может быть несколько) указываются ресурсы, к которым приложению нужен доступ.

В тегах **access/scope** (в перспективе их тоже может быть несколько) указываются требуемые разрешения/требуемый уровень 
 доступа. При наличии блока **access** должны присутствовать как минимум по одному тегу **resource** и **scope**.
 
Наличие блока **access** требует наличия блока **vendorApi** для передачи токена(ов) к ресурсам аккаунта при активации 
приложения по Vendor API.

В случае отсутствия этого блока в дескрипторе приложения при установке на аккаунт приложению не выдаются никакие доступы 
к ресурсам аккаунта.

На текущий момент для **access/resource** возможно только одно значение: **https://online.moysklad.ru/api/remap/1.2**

Для **access/scope** на текущий момент доступно тоже только одно значение: **admin**

Другими словами, на текущий момент блок **access** может иметь только такой вид:

```xml
<access>
    <resource>https://online.moysklad.ru/api/remap/1.2</resource>
    <scope>admin</scope>
</access>
```

#### Блок widgets


Сначала необходимо определить в блоке **widgets** точку расширения - указать страницу, где будет 
расположен виджет. 

Сейчас доступна только одна точка расширения:
 
- **entity.counterparty.view** - карточка контрагента. 

В перспективе точек расширения может быть указано несколько, то есть одно приложение сможет создать
 сразу несколько виджетов - на разных страницах. При этом одно приложение создает только по 
 одному виджету для каждой точки расширения. 
 
Тем не менее, в итоге на одной странице может оказаться 
несколько виджетов (от разных приложений)

<u>Теги entity.counterparty.view:</u>

Тег **sourceUrl** - обязательный. Содержит URL, по которому загружается код виджета в iframe.
В URL допускается использование только протокола HTTPS.

Тег **height** - обязательный.  В теге **height/fixed** задается фиксированная высота виджета
в пикселях, в формате **150px**.

Тег **supports** - опциональный. Предназначен для  дополнительных протоколов, поддерживаемых 
виджетом. На данный момент в нем можно указать только протокол **supports/open-feedback**. При открытии экрана обеспечивает 
скрытие содержимого виджета до явного уведомления от виджета о готовности. Подробнее 
про протокол **open-feedback** можно прочитать в разделе [Жизненный цикл виджета](#zhiznennyj-cikl-widzheta).
 Параметры у протокола отсутствуют.

Пример заполненного блока **widgets**:

> Блок widgets


```xml
    <widgets>
        <entity.counterparty.view>
            <sourceUrl>https://example.com/widget.php</sourceUrl>
            <height>
                <fixed>200px</fixed>
            </height>
            <supports><open-feedback></open-feedback></supports>
        </entity.counterparty.view>
    </widgets>
```

### Примеры дескрипторов

#### Для iframe-приложений

> Дескриптор для iframe-приложения:

```xml
<application xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v1"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                  
      xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v1 
      https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd">
    <iframe>
        <sourceUrl>https://example.com/iframe.html</sourceUrl>
    </iframe>
</application>
```

> Дескриптор для iframe-приложения с расширением окна (expand):

```xml
<application xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v1"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                  
      xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v1 
      https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd">
    <iframe>
        <sourceUrl>https://example.com/iframe.html</sourceUrl>
        <expand>true</expand>
    </iframe>
</application>
```

#### Для серверных приложений

> Минимальный дескриптор для серверных приложений (без возможности настройки параметров приложения пользователем 
МоегоСклада, так как у приложения отсутствует iframe-часть)

```xml
<application xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v1"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                  
      xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v1 
      https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd">
    <vendorApi>
        <endpointBase>https://example.com/dummy-app</endpointBase>
    </vendorApi>
    <access>
        <resource>https://online.moysklad.ru/api/remap/1.2</resource>
        <scope>admin</scope>
    </access>
</application>
```

> Дескриптор для серверных приложений с iframe-частью

```xml
<application xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v1"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"                                  
      xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v1 
      https://online.moysklad.ru/xml/ns/appstore/app/v1/application-1.1.0.xsd">
    <iframe>
        <sourceUrl>https://example.com/iframe.html</sourceUrl>
    </iframe>
    <vendorApi>
        <endpointBase>https://example.com/dummy-app</endpointBase>
    </vendorApi>
    <access>
        <resource>https://online.moysklad.ru/api/remap/1.2</resource>
        <scope>admin</scope>
    </access>
</application>
```
> Дескриптор для серверных приложений с виджетом в карточке КА и протоколом openfeedback

```xml
<ServerApplication  xmlns="https://online.moysklad.ru/xml/ns/appstore/app/v2"             
                    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"             
                    xsi:schemaLocation="https://online.moysklad.ru/xml/ns/appstore/app/v2      
                    https://online.moysklad.ru/xml/ns/appstore/app/v2/application-2.0.0.xsd">
    <iframe>
        <sourceUrl>https://example.com/iframe.html</sourceUrl>
    </iframe>
    <vendorApi>
        <endpointBase>https://example.com/dummy-app</endpointBase>
    </vendorApi>
    <access>
        <resource>https://online.moysklad.ru/api/remap/1.2</resource>
        <scope>admin</scope>
    </access>
    <widgets>        
        <entity.counterparty.view>            
            <sourceUrl>https://example.com/widget.php</sourceUrl>            
            <height>                
                <fixed>150px</fixed>            
            </height>            
            <supports><open-feedback/></supports>        
        </entity.counterparty.view>    
    </widgets>
</ServerApplication>
```


#### Для телефонии

Для приложений телефонии дескриптор на текущий момент не требуется (не поддерживается).



#### Для систем лояльности

Для интеграций с системами лояльности дескриптор на текущий момент не требуется (не поддерживается).