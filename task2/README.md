# Task2 — Динамическое масштабирование контейнеров (HPA по памяти)

В этой директории лежат манифесты Kubernetes для тестового приложения `scaletestapp` и сценарий нагрузки `locustfile.py`.

## 1) Поднять кластер и включить metrics-server

```bash
minikube start
minikube addons enable metrics-server
kubectl get pods -n kube-system | grep metrics
```

## 2) Собрать образ приложения в docker-демоне minikube

В корне репозитория должен лежать исходный код приложения `scaletestapp/` (из задания).

```bash
# Переключаем docker CLI на docker внутри minikube
eval $(minikube -p minikube docker-env)

# Собираем образ
docker build -t scaletestapp:1.0 ./scaletestapp

# Проверяем
docker images | grep scaletestapp
```

## 3) Применить манифесты (Deployment, Service, HPA)

```bash
kubectl apply -f 01-deployment.yaml
kubectl apply -f 02-service.yaml
kubectl apply -f 03-hpa.yaml

kubectl get deploy,svc,hpa
kubectl get pods -w
```

Проверка доступа:

```bash
minikube service scaletestapp --url
# затем:
curl $(minikube service scaletestapp --url)/
curl $(minikube service scaletestapp --url)/metrics
```

## 4) Сгенерировать нагрузку locust

Установить Locust:

```bash
pip install locust
```

Запустить из директории, где лежит `locustfile.py`:

```bash
locust
```

Открыть UI: http://localhost:8089

В поле **Host** указать URL сервиса из:

```bash
minikube service scaletestapp --url
```

Рекомендованные параметры для старта:
- Users: 50–200 (подбирается)
- Hatch rate: 5–20

## 5) Подтвердить автоскейлинг (что приложить в PR)

Сделайте скриншоты / выгрузку логов, подтверждающих рост реплик:
- `minikube dashboard` (вкладки Workloads → Deployments, HPA)
- `kubectl describe hpa scaletestapp-hpa`
- `kubectl get hpa -w`
- `kubectl top pods` (когда метрики прогрелись)

Команды для логов:

```bash
kubectl get hpa scaletestapp-hpa -w
kubectl describe hpa scaletestapp-hpa
kubectl top pods
kubectl get pods -o wide
```

Скриншоты и/или логи положите в эту же директорию (например, в папку `evidence/`).
