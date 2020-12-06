# Компоненты

Каждый кластер состоит из **нод**, и у каждого есть хотя бы одна нода.

**Нода** - запускает контейнерезированные приложения.

**Под** - набор запущенных контейнеров в кластере.

**Control plane** - управляет воркер нодами и подами.

![Схема кластера со всеми компонентами](https://d33wubrfki0l68.cloudfront.net/2475489eaf20163ec0f54ddc1d92aa8d4c87c96b/e7c81/images/docs/components-of-kubernetes.svg)

## Control plane components

Его компоненты принимают важные решения относительно кластера, а также следят за его событиями и реагируют на них.

#### kube-apiserver

Предоставляет доступ к API кубера. Предназначен для горизонтального масштабирования, то есть масштабируется за счет развертывания большего количества инстансов.

*[официальная документация](https://kubernetes.io/docs/reference/generated/kube-apiserver/))*

#### etcd

key value хранилище, используемое в качестве резервного хранилища кубера для всех данных кластера.

Если кластер использует etcd в качестве резервного хранилища, убедитесь, что у вас есть [слой резервного копирования](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster) для этих данных.

*[официальная документация](https://etcd.io/docs/)*

#### kube-scheduler

Отслеживает только что созданные поды без назначенной ноды и выбирает ноду, на которой они будут крутиться.

#### kube-controller-manager

Компонент, который запускает процессы контроллера.

**Controller** - безостановочный цикл, который регулирует состояние системы.

Каждый контроллер представляет собой отдельный процесс, но для простоты все они скомпилированы в один бинарник и выполняются в одном процессе.

Контроллеры:

- **Node controller**: следит и реагирует, когда ноды выходят из строя.
- **Replication controller**: поддержание корректного числа подов для каждого replication controller объекта в системе.
- **Endpoints controller**: наполняет Endpoints объект (joins Services & Pods).
- **Service Account** & **Token controllers**: создают дефолтный акк и API access tokens для новых неймспейсов.

#### cloud-controller-manager

Позволяет связать ваш кластер с cloud provider's API и и отделяет компоненты, которые взаимодействуют с облачной платформой, от компонентов, которые просто взаимодействуют с кластером. 

Этот компонент запускает только те контроллеры, которые нужны вашему клауд провайдеру.

Контроллеры, которые могут зависеть от клауд провайдера:

- **Node controller**: определяет был ли узел удален в облаке после того, как он перестал отвечать.
- **Route controller**: для настройки роутов в базовой клауд инфре.
- **Service controller**: для создания, редактирования и удаления load balancers клауд провайдера

## Node components

Нод компоненты запускаются на каждой ноде, поддерживая запущенные поды и предоставляя Kubernetes runtime environment.

#### kubelet

Агент, который проверяет, что контейнеры крутятся в подах.

Кублет принимает набор PodSpecs, которые предоставляются через различные механизмы, и гарантирует, что контейнеры, описанные в этих PodSpecs, работают и исправны. 

*не управляет контейнерами, которые не были созданы кубером*

#### kube-proxy

Поддерживает network rules на нодах. А они, в свою очередь, помогают получить доступ к подам внутри или вне кластера.

*использует слой пакетной фильтрации в ОС, если он доступен, иначе перенаправляет трафик сам*

#### Container runtime

Софтина, которая отвечает за запуск контейнеров.

*Кубер поддерживает среды выполнения контейнеров:  [Docker](https://docs.docker.com/engine/), [containerd](https://containerd.io/docs/), [CRI-O](https://cri-o.io/#what-is-cri-o), и любую имплементацию [Kubernetes CRI (Container Runtime Interface)](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-node/container-runtime-interface.md).*