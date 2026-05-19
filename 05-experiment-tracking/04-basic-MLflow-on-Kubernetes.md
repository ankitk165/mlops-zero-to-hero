Please refer to the below documentation for this lecture.

https://community-charts.github.io/docs/charts/mlflow/basic-installation

1. kind create cluster --name mlow-k8s
2. kubectl config current-context
3. kubectl config set-context kind-mlops-k8s (if context not set already)
4. helm repo add community-charts https://community-charts.github.io/helm-charts
5. helm repo update
6. kubectl create namespace mlflow
7. helm install mlflow community-charts/mlflow --namespace mlflow --set backendStore.defaultSqlitePath=/mlflow/data/mlflow.db
8.  ```kubectl get pods -n mlflow
    kubectl describe pod mlflow-56655c448-xm8tg -n mlflow
    kubectl get all -n mlflow
    kubectl describe service/mlflow -n mlflow
    ```
9. kubectl port-forward svc/mlflow -n mlflow 8080:80
