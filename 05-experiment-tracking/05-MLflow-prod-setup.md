Please refer to the below document for the next lecture

https://community-charts.github.io/docs/charts/mlflow/postgresql-backend-installation

A. mlflow-with-internal-postgres

1. helm install postgres bitnami/postgresql -n mlflow --set auth.postgresPassword=password --set auth.database=mlflow --set primary.persistence.size=10Gi
2. export POSTGRES_PASSWORD=$(kubectl get secret --namespace mlflow postgres-postgresql -o jsonpath="{.data.postgres-password}" | base64 -d)
3. export POSTGRES_HOST=$(kubectl get svc --namespace mlflow postgres -o jsonpath="{.spec.clusterIP}")
4. helm install mlflow community-charts/mlflow \
  --namespace mlflow \
  --set backendStore.databaseMigration=true \
  --set backendStore.postgres.enabled=true \
  --set backendStore.postgres.host=$POSTGRES_HOST \
  --set backendStore.postgres.port=5432 \
  --set backendStore.postgres.database=mlflow \
  --set backendStore.postgres.user=postgres \
  --set backendStore.postgres.password=$POSTGRES_PASSWORD

helpful-cmds:
1. echo $POSTGRES_HOST

2. kubectl run postgres-postgresql-client --rm --tty -i --restart='Never' --namespace mlflow --image registry-1.docker.io/bitnami/postgresql:latest --env="PGPASSWORD=$POSTGRES_PASSWORD"       --command -- psql --host postgres-postgresql -U postgres -d mlflow -p 5432

\l
\c database
\dt
\d table


3. kubectl port-forward --namespace mlflow svc/postgres-postgresql 5432:5432 &
PGPASSWORD="$POSTGRES_PASSWORD" psql --host 127.0.0.1 -U postgres -d mlflow -p 5432

B. Referencing db-creds from secret

1. kubectl create secret generic postgres-database-secret \
  --namespace mlflow \
  --from-literal=username=postgres \
  --from-literal=password=password

2. helm get values mlflow -n mlflow > values-mflow.yaml

3. vi values-mlflow.yaml

backendStore:
  databaseMigration: true
  postgres:
    enabled: true
    host: postgresql-instance1.cg034hpkmmjt.eu-central-1.rds.amazonaws.com
    port: 5432
    database: mlflow

  existingDatabaseSecret:
    name: postgres-database-secret
    usernameKey: username
    passwordKey: password

4. helm upgrade mlflow community-charts/mlflow -n mlflow -f values-mlflow.yaml
