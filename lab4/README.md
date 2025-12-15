# Лабораторная работа №4: More Kubernetes

##  Цель
Развернуть собственный сервис в Kubernetes, состоящий из нескольких связанных компонентов:
- Spark Master и Worker
- PostgreSQL

## Пошаговый запуск

### 1. Сборка кастомных образов

```
docker build -t spark-app:v1 ./spark
```

### 2. Загрузка образов в Minikube

```
minikube image load spark-app
minikube image load jupyter-spark
```

### 3. Применение Kubernetes-манифестов

```
# Secret и ConfigMap
kubectl apply -f manifests/postgres-secret.yml
kubectl apply -f manifests/spark-config.yml

# PostgreSQL
kubectl apply -f manifests/postgres-deployment.yml
kubectl apply -f manifests/postgres-service.yml

# Spark Master
kubectl apply -f manifests/spark-master-deployment.yml
kubectl apply -f manifests/spark-master-service.yml

# Spark Worker
kubectl apply -f manifests/spark-worker-deployment.yml


```

### 4. Проверка статуса

```
kubectl get pods -w
```

### 5. Проверка Spark UI 

```
kubectl port-forward svc/spark-master 8080:8080
```