- После изменения di.xml запустить
```bash
bin/magento setup:di:compile
```

- Не находит класс в di.xml
```
<item name="slider_simple_listing_data_source" xsi:type="string">
    \DIY\Slider\Model\ResourceModel\Simple\Grid\Collection
</item>
```
Пробельные символы не обрезаются, поэтому без перевода строки 
```
<item name="slider_simple_listing_data_source" xsi:type="string">\DIY\Slider\Model\ResourceModel\Simple\Grid\Collection</item>
```
