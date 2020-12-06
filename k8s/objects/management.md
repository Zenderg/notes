# Управление объектами

## Management techniques

> Нельзя использовать смешивание техник для одного и того же объекта.

Техника | Работает с | Рекомендуемое окружение | Люди | Уровень обучения
---------|--------------|------------------------------|--------|---------------------
Imperative commands | Live objects | Development projects | 1+ | Lowest
Imperative object configuration | Individual files | Production projects | 1 | Moderate
Declarative object configuration | Directories of files | Production projects | 1+ | Highest

## Imperative commands

Во время этой техники юзер оперирует напрямую живыми объектами в кластере, через `kubectl` команды.

*Поскольку этот метод работает непосредственно с живыми объектами, он не обеспечивает историей предыдущих конфигураций*

Запуск инстанса nginx контейнера через создание деплоймент объекта:

```
kubectl create deployment nginx --image nginx
```

Плюсы в сравнении с object configuration:

- Команды простые, их легко изучить и запомнить
- Буквально один шаг для внесения изменений в кластер

Минусы в сравнении с object configuration:

- Не интегрируется с процессами проверки изменений
- Не обеспечивает историей изменений
- Не обеспечивает сурс записей
- Нет шаблонов для создания новых объектов

## Imperative object configuration

Описание конфигурации через yaml файлы и накатывание их на кластер через `kubectl`.

Создание объекта:

```
kubectl create -f nginx.yaml
```

Плюсы в сравнении с imperative commands:

- Конфигурацию объекта можно сохарнить в VCS (Git)
- Конфигурации можно интегрировать с различными процессами, например изменение конфигурации (динамическая подстановка значений) "налету"
- Предоставляет шаблон для создания новых объектов

Минусы в сравнении с imperative commands:

- Требует базового понимание схемы объектов
- Дополнительное действие для внесения изменений в кластер (написание yaml конфигурации)

Плюсы в сравнении с declarative object configuration:

- Поведение объектов проще и понятнее

Минусы в сравнении с declarative object configuration:

- Техника работает лучше всего с файлами, но не с каталогами
- Обновления живых объектов должны быть отражены в файлах конфигурации, иначе они будут потеряны при следующей замене

## Declarative object configuration

Юзеру не нужно операции, которые нужно выполнить с файлами конфигурации. `kubectl` все сделает за вас.

> Declarative object configuration retains changes made by other writers, even if the changes are not merged back to the object configuration file. This is possible by using the patch API operation to write only observed differences, instead of using the replace API operation to replace the entire object configuration.

Обработка всех файлов в папке configs и на основе этого создание или апдейт объектов:

```
kubectl diff -f configs/   <--- show diffs (not required)
kubectl apply -f configs/
```

Рекурсивно:

```
kubectl diff -R -f configs/
kubectl apply -R -f configs/
```

Плюсы в сравнении с imperative object configuration:

- Изменения, внесенные непосредственно в живые объекты, сохраняются, даже если они не объединяются обратно в файлы конфигурации
- Улучшенная поддержка работы с каталогами и автоматического определения типов операций (создание, исправление, удаление) для каждого объекта

Минусы в сравнении с imperative object configuration:

- Сложнее дебажить
- Частичные обновления с использованием различий создают сложные операции изменения