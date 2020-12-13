# Селекторы полей

Выборка сурсов кубера на основе их полей.

```
kubectl get pods --field-selector status.phase=Running
```

> Field selectors are essentially resource filters. By default, no selectors/filters are applied, meaning that all resources of the specified type are selected. This makes the `kubectl` queries `kubectl get pods` and `kubectl get pods --field-selector ""` equivalent.

```
kubectl get pods --field-selector=status.phase!=Running,spec.restartPolicy=Always
```

```
kubectl get statefulsets,services --all-namespaces --field-selector metadata.namespace!=default
```