# Рекомендуемые лейблы

> Shared labels and annotations share a common prefix: app.kubernetes.io. Labels without a prefix are private to users. The shared prefix ensures that shared labels do not interfere with custom user labels.

Кей | Описание | Пример | Тип
---------|--------------|------------------------------|--------
`app.kubernetes.io/name` | Имя приложения | `mysql` | string
`app.kubernetes.io/instance` | Уникальное имя, идентифицирующее инстанс приложения | `mysql-abcxzy` | string
`app.kubernetes.io/version` | Версия приложения | `5.7.21` | string
`app.kubernetes.io/component` | Архитектурный компонент | `database` | string
`app.kubernetes.io/part-of` | Часть чего-то более высокоуровнего | `wordpress` | string
`app.kubernetes.io/managed-by` | Инструмент по управлению этого объекта | `helm` | string

```
apiVersion: apps/v1
kind: StatefulSet
metadata:
  labels:
    app.kubernetes.io/name: mysql
    app.kubernetes.io/instance: mysql-abcxzy
    app.kubernetes.io/version: "5.7.21"
    app.kubernetes.io/component: database
    app.kubernetes.io/part-of: wordpress
    app.kubernetes.io/managed-by: helm

```