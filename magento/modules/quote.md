# Доставка

В публичной части отображается на странице оформления заказа _/checkout_  
Макет в файле _Magento/Checkout/view/frontend/layout/checkout\_index\_index.xml_  
Построен на ui компонентах.  
Раздел методы доставки подключается в узле

```xml
<item name="component" xsi:type="string">Magento_Checkout/js/view/shipping</item>
```

Шаблон описан в _Magento/Checkout/view/frontend/web/js/view/shipping.js_  
Все взаимодействие проходит через REST API  
Адреса api прописаны в _Magento/Checkout/view/frontend/web/js/model/resource-url-manager.js_

## API доставки

### Методы доставки

#### Модуль Magento\_Quote

Маршруты для получения методов доставки относятся к модулю Magento\_Quote и определены в файле  
_Magento/Quote/etc/webapi.xml_, в котором осуществляется привязка метода к интерфейсу

```xml
    <route url="/V1/guest-carts/:cartId/estimate-shipping-methods" method="POST">
        <service class="Magento\Quote\Api\GuestShipmentEstimationInterface" method="estimateByExtendedAddress"/>
        <resources>
            <resource ref="anonymous" />
        </resources>
    </route>
```

Для получения методов доставки используются интерфейсы:

| интерфейс | реализация | url запроса | метод | тип запроса |
| :--- | :--- | :--- | :--- | :--- |
| Magento\Quote\Api\ShippingMethodManagementInterface | Magento\Quote\Model\ShippingMethodManagement | /V1/carts/:cartId/shipping-methods | getList | GET |
|  |  | /V1/carts/mine/shipping-methods | getList | GET |
|  |  | /V1/carts/:cartId/estimate-shipping-methods-by-address-id | estimateByAddressId | POST |
|  |  | /V1/carts/mine/estimate-shipping-methods-by-address-id | estimateByAddressId | POST |
| Magento\Quote\Api\ShipmentEstimationInterface | Magento\Quote\Model\ShippingMethodManagement | /V1/carts/:cartId/estimate-shipping-methods | estimateByExtendedAddress | POST |
|  |  | /V1/carts/mine/estimate-shipping-methods | estimateByExtendedAddress | POST |
| Magento\Quote\Api\GuestShippingMethodManagementInterface | Magento\Quote\Model\GuestCart\GuestShippingMethodManagement | /V1/guest-carts/:cartId/shipping-methods | getList | GET |
| Magento\Quote\Api\GuestShipmentEstimationInterface | Magento\Quote\Model\GuestCart\GuestShippingMethodManagement | /V1/guest-carts/:cartId/estimate-shipping-methods | estimateByExtendedAddress | POST |

#### Модуль Magento\_Checkout

Файл с маршрутами Magento/Checkout/etc/webapi.xml  
Интерфейсы:

| интерфейс | реализация | url запроса | метод | тип запроса |
| :--- | :--- | :--- | :--- | :--- |
| Magento\Checkout\Api\GuestShippingInformationManagementInterface | Magento\Checkout\Model\GuestShippingInformationManagement | /V1/guest-carts/:cartId/shipping-information | saveAddressInformation | POST |

## Подмена реализации API

Конкретный класс реализации интерфейса определяется через DI.  
_Magento/Quote/etc/di.xml_

```xml
<preference for="Magento\Quote\Api\GuestShipmentEstimationInterface" type="Magento\Quote\Model\GuestCart\GuestShippingMethodManagement" />
```

Для подключения другой реализации нужно в своем модуле прописать нужные зависимости  
Так, для получения списка методов доставки создаем файл _<Vendor>/Shipping/etc/di.xml_

```xml
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:ObjectManager/etc/config.xsd">
    <preference for="Magento\Quote\Api\GuestShippingMethodManagementInterface" type="<Vendor>\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
    <preference for="Magento\Quote\Api\GuestShipmentEstimationInterface" type="<Vendor>\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
    <preference for="Magento\Quote\Model\GuestCart\GuestShippingMethodManagementInterface" type="<Vendor>\Shipping\Model\GuestCart\GuestShippingMethodManagement" />
</config>
```

Пересобираем DI.

```bash
bin/magento setup:di:compile
```

Теперь при обращении к api с прописанными интерфейсами будет вызываться класс модуля <Vendor>\_Shipping

## Модуль Magento\_Shipping

Расположение app/code/Magento/Shipping

### Конфигурационные файлы

#### etc

_module.xml_ зависимости от Magento\_Store, Magento\_Catalog, Magento\_Ui, Magento\_User  
_config.xml_ по-умолчанию настроено расположение магазина  
_acl.xml_  
_crontab.xml_ Magento\Shipping\Model\Observer::aggregateSalesReportShipmentData в 00:00  
_di.xml_ прописаны зависимости для

* Magento\Shipping\Model\Shipping
* Magento\Shipping\Model\CarrierFactory
* Magento\Shipping\Model\Carrier\Source\GenericDefault



### etc/adminhtml

_routes.xml_  
_page\_types.xml_

