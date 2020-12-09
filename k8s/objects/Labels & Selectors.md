# Лейблы и селекторы

Лейблы это key/value пары, которые привязываются к объекту. Можно использовать для выборки нескольких объектов. Каждый ключ лейбла должен быть уникален относительно объекта.

```
"metadata": {
  "labels": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

## Syntax and character set

Имя (ключ) лейбла должно быть не больше 63 символов (`[a-z0-9A-Z]` + `['-', '_', '.']`). Также, у него может быть префикс, который разделяется  `/`.

*Префиксы `kubernetes.io/` & `k8s.io/` зарезервированы кор компонентами кубера*

Значение лейбла валидируется по такому же принципе, за исключением того, что у него не может быть префикса.

```
apiVersion: v1
kind: Pod
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80

```

## Label selectors

Селектор - своего рода группировка объектов в кубере через лейблы.  При необходимости выборки объектов через несколько лейблов, разделяй их `,` (является логическим оператором &&). Также, можно использовать `[=,==,!=]` & `[in, notin]`.

> For some API types, such as ReplicaSets, the label selectors of two instances must not overlap within a namespace, or the controller can see that as conflicting instructions and fail to determine how many replicas should be present.

Выберет по лейблу `environment`, у которого будет значение `production`, **И** по лейблу `tier`, у которого значение отличное от `frontend`:
```
environment = production
tier != frontend
```

`environment` со значением `production` или `qa`, во 2 строке такой же, но негативный сценарий, в 3 все объекты с лейблом `partition`, без учета его значения, в 4 негативный сценарий:

```
environment in (production, qa)
tier notin (frontend, backend)
partition
!partition
```

Выберет ноды по  `accelerator=nvidia-tesla-p100`:

```
apiVersion: v1
kind: Pod
metadata:
  name: cuda-test
spec:
  containers:
    - name: cuda-test
      image: "k8s.gcr.io/cuda-vector-add:v0.1"
      resources:
        limits:
          nvidia.com/gpu: 1
  nodeSelector:
    accelerator: nvidia-tesla-p100

```

## API

```
kubectl get pods -l environment=production,tier=frontend

kubectl get pods -l 'environment in (production),tier in (frontend)'

kubectl get pods -l 'environment in (production, qa)'

kubectl get pods -l 'environment,environment notin (frontend)'
```

Некоторые объекты (services или replicationcontrollers) используют лейблы в качестве выборки нужных подов:

```
selector:
    component: redis
```

Некоторые штуки (Job, Deployment, ReplicaSet, and DaemonSet) поддерживают и такой формат:

```
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - {key: tier, operator: In, values: [cache]}
    - {key: environment, operator: NotIn, values: [dev]}
```