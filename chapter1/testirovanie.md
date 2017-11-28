# Тестирование

## [Интеграционные тесты](http://devdocs.magento.com/guides/v2.0/test/integration/integration_test_execution.html)

### The TESTS\_CLEANUP Constant

Значение по умолчанию

```xml
<const name="TESTS_CLEANUP" value="enabled"/>
```

При любом запуске тестирования, даже если тестов нет, сбрасывается состояние magento и запускается полная установка. Для разработки можно прогнать один раз и переключить в _disable_.

