# Объекты

Объекты - это постоянные сущности в системе кубера. Кубер использует эти сущности для представления состояния вашего кластера. Например:

- Какие контейнерезированные приложения сейчас запущенны и на какой ноде
- Ресурсы, которые доступны этим приложениям
- поведение приложений при релоаде, рефреше и пр.

*Обычно, управление объектами кубера происходит через `kubectl`, который под капотом юзает [Kubernetes API](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). Но можно юзать и напрямую [Client libraries](https://kubernetes.io/docs/reference/using-api/client-libraries/)*

#### Spec and Status

Почти у каждого объекта кубера есть поля, которые управляют его состоянием:  `spec` and `status`. `status` описывает текущее состояние объекта, обновляется системой кубера. `spec` - желаемое состояние + базовая информация об объекте.

*Для поддержания заданного состояния используется Control plane, который менеджит каждый объект*

#### Create Kubernetes object

При создании объекта для кубера нужно использовать его API. В теле запроса должен быть конфиг в формате JSON, если вы используете его напрямую. Если делать это через `kubectl` , то следует описывать этот конфиг в yaml файле и применять уже его.

*Тем не менее, `kubectl` под капотом все равно конвертирует инфу в JSON формат и отправит API запрос*

**Deployment** - некий объект, который представляет запущенное приложение в вашем кластере.

Пример yaml конфигурации деплоймента:

```
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2 # tells deployment to run 2 pods matching the template
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80

```

Команда для создания объекта:

```
kubectl apply -f https://k8s.io/examples/application/deployment.yaml --record
```

#### Required Fields

- `apiVersion` - Версия API для создания объекта
- `kind` - типа объекта
- `metadata` - данные, которые позволят идентифицировать ваш объект
- `spec` - желаемое состояние объекта

Узнать какие поля могут быть у различных объектов можно [здесь](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/).