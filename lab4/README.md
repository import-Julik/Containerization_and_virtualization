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

<img width="488" height="64" alt="Снимок экрана 2025-12-16 в 17 07 33" src="https://github.com/user-attachments/assets/7527c8ee-8c75-40c2-ae5b-c008460e3b3f" />


### 5. Проверка Spark UI 

```
kubectl port-forward svc/spark-master 8080:8080
```

<img width="1438" height="459" alt="Снимок экрана 2025-12-16 в 17 09 10" src="https://github.com/user-attachments/assets/be0f3cf4-0e89-4bcb-a455-ccfceb4f7e94" />
