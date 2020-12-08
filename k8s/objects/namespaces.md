# Неймспейсы

По факту это виртуальные кластеры, которые находятся на одном физическом кластере.

## Working with Namespaces

> Не создавай неймспейсы с префиксом `kube-`, зарезервировано кубером

#### Viewing namespaces

Получение списка текущих неймспейсов:

```
kubectl get namespace
```
```
NAME              STATUS   AGE
default           Active   1d
kube-node-lease   Active   1d
kube-public       Active   1d
kube-system       Active   1d
```

По дефолту кубер заводится с 4 неймспейсами:

- `default` Дефолтный неймспейс для объектов без указанного неймспейса
- `kube-system` Для объектов созданных кубером
- `kube-public` Readable всеми юзерами (включая не авторизованных). Этот неймспейс в основном зарезервирован для использования кластера, в случае, если некоторые ресурсы должны быть видны и доступны для чтения публично во всем кластере.
- `kube-node-lease` Для lease objects (?) связанных с каждой нодой. Улучшает производительность node heartbeats во время масштабирования кластера.

#### Setting the namespace

Установка неймспейса для текущей опреации:

```
kubectl run nginx --image=nginx --namespace=<insert-namespace-name-here>
```

Перманентно для всех операций:

```
kubectl config set-context --current --namespace=<insert-namespace-name-here>
```

## Namespaces and DNS

Во время создания Сервиса создается соответствующий [DNS entry](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/). Запись имеет вид `<service-name>.<namespace-name>.svc.cluster.local`, это означает, что если контейнер просто использует `<service-name>`, он будет преобразован в службу, которая является локальной для неймспейса. Это поолезно во время использования одинаковых конфигураций в нескольких неймспейсах. If you want to reach across namespaces, you need to use the fully qualified domain name (FQDN).

## Not All Objects are in a Namespace

Как следует из названия, не все сущности кубера хранятся в неймспейсах (ноды, сами неймспейсы, вольюмы и пр.).

```
# In a namespace
kubectl api-resources --namespaced=true

# Not in a namespace
kubectl api-resources --namespaced=false
```