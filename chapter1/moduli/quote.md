# Quote
В публичной части отображается на странице оформления заказа */checkout*
Макет в файле *Magento/Checkout/view/frontend/layout/checkout_index_index.xml*
Построен на ui компонентах.
Раздел методы доставки подключается в узле

```xml
<item name="component" xsi:type="string">Magento_Checkout/js/view/shipping</item>
```
Шаблон описан в *Magento/Checkout/view/frontend/web/js/view/shipping.js*  
Все взаимодействие проходит через REST API  
Адреса api прописаны в *Magento/Checkout/view/frontend/web/js/model/resource-url-manager.js*

##API доставки
### Методы доставки

Маршруты для получения методов доставки относятся к модулю Magento_Quote и определены в файле
*Magento/Quote/etc/webapi.xml*, в котором осуществляется привязка метода к интерфейсу

```xml
    <route url="/V1/guest-carts/:cartId/estimate-shipping-methods" method="POST">
        <service class="Magento\Quote\Api\GuestShipmentEstimationInterface" method="estimateByExtendedAddress"/>
        <resources>
            <resource ref="anonymous" />
        </resources>
    </route>
``` 
Для получения методов доставки используются 4 интерфейса:

|интерфейс|реализация|url запроса|метод|тип запроса|
|---|---|---|
|Magento\Quote\Api\ShippingMethodManagementInterface|Magento\Quote\Model\ShippingMethodManagement|/V1/carts/:cartId/shipping-methods|getList|GET|
|||/V1/carts/mine/shipping-methods|getList|GET|
|||/V1/carts/:cartId/estimate-shipping-methods-by-address-id|estimateByAddressId|POST|
|||/V1/carts/mine/estimate-shipping-methods-by-address-id|estimateByAddressId|POST|
|Magento\Quote\Api\ShipmentEstimationInterface|Magento\Quote\Model\ShippingMethodManagement|/V1/carts/:cartId/estimate-shipping-methods|estimateByExtendedAddress|POST|
|||/V1/carts/mine/estimate-shipping-methods|estimateByExtendedAddress|POST|
|Magento\Quote\Api\GuestShippingMethodManagementInterface|Magento\Quote\Model\GuestCart\GuestShippingMethodManagement|/V1/guest-carts/:cartId/shipping-methods|getList|GET|
|Magento\Quote\Api\GuestShipmentEstimationInterface|Magento\Quote\Model\GuestCart\GuestShippingMethodManagement|/V1/guest-carts/:cartId/estimate-shipping-methods|estimateByExtendedAddress|POST|


##Подмена реализации API

Конкретный класс реализации интерфейса определяется через DI.  
*Magento/Quote/etc/di.xml*
```xml
<preference for="Magento\Quote\Api\GuestShipmentEstimationInterface" type="Magento\Quote\Model\GuestCart\GuestShippingMethodManagement" />
```

Для подключения другой реализации нужно в своем модуле прописать нужные зависимости  
Так, для получения списка методов доставки создаем файл *DIY/Shipping/etc/di.xml*
```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <preference for="Magento\Quote\Api\GuestShippingMethodManagementInterface" type="DIY\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
    <preference for="Magento\Quote\Api\GuestShipmentEstimationInterface" type="DIY\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
    <preference for="Magento\Quote\Model\GuestCart\GuestShippingMethodManagementInterface" type="DIY\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
</config>
```

Пересобираем DI.
```bash
bin/magento setup:di:compile
```

Теперь при обращении к api с прописанными интерфейсами будет вызываться класс модуля DIY_Shipping
 


##Модуль Magento_Shipping
Расположение app/code/Magento/Shipping

###Конфигурационные файлы
####etc
*module.xml* зависимости от Magento_Store, Magento_Catalog, Magento_Ui, Magento_User
*config.xml* по-умолчанию настроено расположение магазина
*acl.xml* 
*crontab.xml* Magento\Shipping\Model\Observer::aggregateSalesReportShipmentData в 00:00
*di.xml* прописаны зависимости для
- Magento\Shipping\Model\Shipping
- Magento\Shipping\Model\CarrierFactory
- Magento\Shipping\Model\Carrier\Source\GenericDefault

###etc/adminhtml
*routes.xml* 
*page_types.xml*

