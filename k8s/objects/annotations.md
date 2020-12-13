# Аннотации

key/value пары. Используются для прикрепления произвольных метаданных к объектам. К примеру, тулзы и либы могут использовать эти данные. **Не используются для выборки или индентификации объектов.**

## Attaching metadata to objects

```
"metadata": {
  "annotations": {
    "key1" : "value1",
    "key2" : "value2"
  }
}
```

```
apiVersion: v1
kind: Pod
metadata:
  name: annotations-demo
  annotations:
    imageregistry: "https://hub.docker.com/"
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80

```