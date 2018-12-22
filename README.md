# A Repo of Helm Charts for various Docker Images

1. Preparation of Docker Image:  
 a) push to repo - [Example 1](https://stackoverflow.com/questions/40600419/why-am-i-getting-an-errimagepull-error-in-this-kubernetes-deployment), [Example 
2](https://docs.bitnami.com/kubernetes/how-to/deploy-go-application-kubernetes-helm/)  
b) use public repo and existing image i.e. "danielvdende/docker-mlflow"

2. Cluster initialization:
 - `kubectl create namespace test`
 - `kubectl get namespaces`

3. Definition of new Helm Chart:
 - `helm create mlflow`

4. Debug:
 - `helm install --dry-run --debug --name mlflow-test --namespace test mlflow/`
 - `helm install --dry-run --debug --name mlflow-test --namespace test mlflow/ --set <key>=<value>`

5. Installation:
 - `helm install --name mlflow-test --namespace test mlflow/`

6. Debug:
 - `kubectl get pods --namespace=test`
 - `kubectl describe pods <pod-id> --namespace=test`
 - `kubectl port-forward --namespace=test deployment/mlflow-test 5000:5000`

7. Cleanup:
 - `helm ls`
 - `helm delete mlflow-test --purge`
 - `helm ls` should now be empty
 - `kubectl delete namespace test`

8. Package:
 - `helm package mlflow`
  Successfully packaged chart and saved it to: /home/pilillo/Documenti/helm-charts/mlflow-0.1.0.tgz
 - `mv mlflow-0.1.0.tgz docs`
 - `helm repo index docs --url https://pilillo.github.com/helm-charts`
  ```$ ls docs/
 index.html  index.yaml  mlflow-0.1.0.tgz
```

9. Use Github as Helm Repository
 - `helm repo add helm-charts https://pilillo.github.com/helm-charts`
