Please refer to the below GitHub repository for this lecture

https://github.com/iam-veeramalla/Intent-classifier-model

Helm trobleshooting:

kserve:

```
helm status kserve -n kserve -> to get the status of helm release
helm get all kserve -n kserve -> to get manifest, notes, values etc for helm release
helm get values kserve -n kserve -> to get user supplied values for helm release
helm get manifest kserve -n kserve -> to get manifest for helm release

helm template kserve oci://ghcr.io/kserve/charts/kserve --version v0.16.0 -n kserve -f values.yaml -> to get template/k8s files according to user supplied values

helm uninstall kserve -n kserve
helm install kserve oci://ghcr.io/kserve/charts/kserve   --version v0.16.0   -n kserve   --set kserve.controller.deploymentMode=RawDeployment   --wait

values.yaml
  controller:
  defaultDeploymentMode: RawDeployment

helm upgrade kserve oci://ghcr.io/kserve/charts/kserve --version v0.16.0 -f values.yaml  -n kserve
```

Troubleshooting inferenceservice
```
kubectl describe inferenceservice intent-classifier -n intent

kubectl edit inferenceservice intent-classifier -n intent
  spec:
    predictor:
      deploymentMode: RawDeployment

kubectl delete inferenceservice intent-classifier -n intent

cat <<EOF | kubectl apply -n intent -f -
apiVersion: serving.kserve.io/v1beta1
kind: InferenceService
metadata:
  name: intent-classifier
spec:
  predictor:
    deploymentMode: RawDeployment
    model:
      modelFormat:
        name: sklearn
      storageUri: https://github.com/iam-veeramalla/Intent-classifier-model/releases/download/3.0/intent_model.pkl
      resources:
        requests:
          cpu: "100m"
          memory: "512Mi"
        limits:
          cpu: "1"
          memory: "1Gi"
EOF

```

Commands for cert-manager resources check:

```
  778  kubectl get certificate -n kserve
  779  kubectl describe certificate serving-cert -n kserve
  780  kubectl get issuer -n kserve
  781  kubectl describe issuer selfsigned-issuer -n kserve
  782  kubectl get clusterissuer
  783  kubectl get secret -n kserve
  784  kubectl get secret kserve-webhook-server-cert -n kserve -o yaml

kubectl get certificate -A
```

traefik ingress commands:

```
kubectl get crds | grep traefik

  742  kubectl get ingress -n intent-ns
  744  kubectl get ingressclass -n traefik
  745  kubectl describe ingressclass traefik -n traefik

```

Notes:

cert-manager
- Manages certificate lifecycle automatically, issue, renew , register with cluster signing authority etc
- Certificate custom resource for cert
- Issuer and ClusterIssuer resource -> provide this in certificate reource, can be self-signed, public like LetsEncrypt, Vault, or paid ones
- There is a secret too that holds crt , tls cert , key

traefik ingress-controller
- creates a load balancer in cloud that points to ingress controller pod ips
- create a ingress resource with rules, rule has host name, path prefix and pointing service
- when a user hits dns say example.com , dns record example.com points to ingress load balancer, traffic then moves to ingress controller pod, -> then ingress controller forwards to specific pod according to ingress service rule
- ingressclass is a named resource that we can define in ingress resource , help to use multipe, ingress controller like traefik, nginx in same k8s cluster

alb vs ingress:
- alb for a single service routing but ingress for a path or host based routing
- ingress is more complex and configurable

istio vs ingress
- east-west traffic mainly i.e service to service communication
- collect logs, metrics, traces through sidecar container with each pod and can be viewed in kiali
- routimg rules and crds of its own
- can use otel with this, as logs , metrics, traces etc collected can be exported in opentelemetry format
- can use prom , jaegar etc for viewing monitoring data collected

knative
- helps in serverless resource management on k8s cluster through its crds
- pod can be scaled to 0, but cold start problem
- richer monitoring setup with gateway like istio and otel setup
