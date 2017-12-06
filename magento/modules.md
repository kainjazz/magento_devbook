# Модули

## [Создание нового модуля](http://devdocs.magento.com/videos/fundamentals/create-a-new-module)

Создать каталог модуля - app/code/&lt;Vendor&gt;/&lt;moduleName&gt;

Добавить описание модуля etc/module.xml

Создать файл регистрации registartion.php

Запустить команду для установки нового модуля в систему

```bash
bin/magento setup:upgrade
```



## Операции с модулем

Отключение модуля:

```bash
bin/magento module:disable <moduleName>
```

Включение модуля:

```
bin/magento module:enable <moduleName>
```





