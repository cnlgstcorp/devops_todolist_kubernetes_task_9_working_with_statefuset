---

## 📄 `INSTRUCTION.md`

````markdown
# INSTRUCTION.md

## ✅ Завдання: Підключити Django ToDo App до MySQL через StatefulSet

---

## 📦 КРОК 1: Створення кластера через kind

```bash
kind create cluster --config .infrastructure/cluster.yml
````

---

## 🧱 КРОК 2: Створити ресурси MySQL

```bash
kubectl apply -f .infrastructure/namespace-mysql.yml
kubectl apply -f .infrastructure/mysql-secret.yml
kubectl apply -f .infrastructure/mysql-init-config.yml
kubectl apply -f .infrastructure/mysql-headless-svc.yml
kubectl apply -f .infrastructure/statefulSet.yml
```

Перевір, що всі 3 поди працюють:

```bash
kubectl get pods -n mysql
```

---

## 🛠 КРОК 3: Підготувати додаток

```bash
kubectl apply -f .infrastructure/namespace.yml
kubectl apply -f .infrastructure/mysql-app-credentials.yml
kubectl apply -f .infrastructure/app-secret.yml
kubectl apply -f .infrastructure/configMap.yml
kubectl apply -f .infrastructure/pv.yml
kubectl apply -f .infrastructure/pvc.yml
kubectl apply -f .infrastructure/clusterIp.yml
kubectl apply -f .infrastructure/nodeport.yml
kubectl apply -f .infrastructure/hpa.yml
kubectl apply -f .infrastructure/deployment.yml
```

---

## 🧪 КРОК 4: Перевірка

### 1. Переконайся, що додаток працює

```bash
kubectl get pods -n todoapp
```

`todoapp` повинен бути в статусі `Running`

---

### 2. Зайди в под і перевір змінні середовища

```bash
kubectl exec -n todoapp -it <todoapp-pod> -- sh
printenv | grep DB
```

Очікується:

```
DB_NAME=todo_db
DB_USER=liquibase_user
DB_PASSWORD=StrongPassword321!
DB_HOST=mysql-0.mysql.mysql.svc.cluster.local
```

---

### 3. Переконайся, що `todoapp` успішно підключився до `mysql-0`

* Додаток працює без помилок
* Зберігає або отримує дані з бази

---

## 🟢 Результат

* ✅ MySQL піднято через `StatefulSet` (3 репліки)
* ✅ Headless Service відкриває доступ до `mysql-0`
* ✅ Django додаток читає всі параметри підключення до БД з `Secret`
* ✅ Додаток використовує `PVC` для `/app/data`
* ✅ `init.sql` був змонтований у `/docker-entrypoint-initdb.d` через ConfigMap

---

✅ **Готово до перевірки**