# Лабораторная работа №3: Kubernetes 

## Ход выполнения

### Часть 1. Установка и запуск minikube на macOS

1. Установлен **Docker Desktop** и запущен как основной runtime.
2. Установлены CLI-инструменты:
   - `kubectl` через Homebrew,
   - `minikube` через Homebrew.
3. Запущен локальный кластер:

```
minikube start --driver=docker
```

Проверена работоспособность:
- `kubectl config view` — подтверждает подключение к кластеру minikube,
- `docker ps` — отображает контейнер minikube.

Кластер готов к развёртыванию приложений.

### Часть 2. Создание и применение манифестов

**PostgreSQL:**
- `postgres-secret.yml` — хранит `POSTGRES_USER` и `POSTGRES_PASSWORD` (чувствительные данные вынесены из ConfigMap в Secret).
- `postgres-configmap.yml` — содержит только `POSTGRES_DB`.
- `postgres-service.yml` — сервис типа ClusterIP для внутреннего доступа.
- `postgres-deployment.yml` — деплоймент с одним репликой, использующий и Secret, и ConfigMap.

**Nextcloud:**
- `nextcloud-configmap.yml` — вынесены переменные: `NEXTCLOUD_UPDATE`, `ALLOW_EMPTY_PASSWORD`, `NEXTCLOUD_TRUSTED_DOMAINS`, `NEXTCLOUD_ADMIN_USER`.
- `nextcloud-secret.yml` — хранит `NEXTCLOUD_ADMIN_PASSWORD`.
- `nextcloud-deployment.yml` — добавлены:
- Liveness probe: проверка `/status.php` с задержкой 120 сек,
- Readiness probe: та же эндпоинт, задержка 30 сек.
- `nextcloud-service.yml` — сервис типа NodePort для внешнего доступа.

**Порядок применения манифестов:**

Манифесты применены в следующем порядке:
1. Secrets и ConfigMaps,
2. Deployments,
3. Services.

Это гарантирует, что при создании Pod'ов все зависимые объекты уже существуют.

```
kubectl apply -f postgres-secret.yml
kubectl apply -f postgres-configmap.yml
kubectl apply -f nextcloud-configmap.yml
kubectl apply -f nextcloud-secret.yml
kubectl apply -f postgres-deployment.yml
kubectl apply -f postgres-service.yml
kubectl apply -f nextcloud-deployment.yml
kubectl apply -f nextcloud-service.yml
```

После завершения инициализации Nextcloud стал доступен через:

```
minikube start --driver=docker
```

### Часть 3. Дополнительные проверки

- Подтверждена работа проб: Pod переходит в состояние Ready только после успешного ответа `/status.php`.
- Переменные окружения корректно подгружены из ConfigMap и Secret.
- Связь между Nextcloud и PostgreSQL работает: приложение успешно подключается к БД.

## Ответы на вопросы

### 1. Важен ли порядок выполнения манифестов? Почему?

**Да, порядок важен.**

Если сначала применить Deployment, а потом Secret или ConfigMap, поды не смогут стартовать — Kubernetes не найдёт указанные ресурсы.

Поэтому сначала создаются зависимости (Secret, ConfigMap), затем — поды, которые их используют.

### 2. Что произойдёт, если отскейлить количество реплик postgres-deployment в 0, затем обратно в 1, после чего попробовать снова зайти на Nextcloud?

**При 0:**
- Pod с PostgreSQL полностью удаляется,
- Данные теряются, так как в манифесте не используется PersistentVolume.

**При 1**
- Запускается новый Pod с чистой БД,
- Nextcloud не находит свои таблицы → отображает экран первоначальной установки.


