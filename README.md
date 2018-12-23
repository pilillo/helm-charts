# A Repo of Helm Charts for various Docker Images

**1. Preparation of Docker Image:**  
  a) push to repo (accessible from the K8s cluster) - [Example 
1](https://stackoverflow.com/questions/40600419/why-am-i-getting-an-errimagepull-error-in-this-kubernetes-deployment), [Example 
2](https://docs.bitnami.com/kubernetes/how-to/deploy-go-application-kubernetes-helm/), [Example 3](https://www.influxdata.com/blog/packaged-kubernetes-deployments-writing-helm-chart/)  
    For minikube, It is also possible to build the Docker image locally available to the host and access it from the minikube K8s cluster (`eval $(minikube docker-env)`).  
  b) use public repo and existing image e.g. "danielvdende/docker-mlflow"

**2. Cluster initialization:**
 - `kubectl create namespace test`
 - `kubectl get namespaces`

**3. Definition of new Helm Chart:**
 - `helm create mlflow`

**4. Debug:**
 - `helm install --dry-run --debug --name mlflow-test --namespace test mlflow/`
 - `helm install --dry-run --debug --name mlflow-test --namespace test mlflow/ --set <key>=<value>`

**5. Installation:**
 - `helm install --name mlflow-test --namespace test mlflow/`
	```$ helm install --name mlflow-test --namespace test mlflow/
	NAME:   mlflow-test
	LAST DEPLOYED: Sat Dec 22 19:02:48 2018
	NAMESPACE: test
	STATUS: DEPLOYED

	RESOURCES:
	==> v1/Pod(related)
	NAME                          READY  STATUS   RESTARTS  AGE
	mlflow-test-6d7bff6657-zdwv7  0/1    Pending  0         0s

	==> v1/Deployment
	NAME         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
	mlflow-test  1        0        0           0          0s


	NOTES:
	1. Get the application URL by running these commands:
	  export POD_NAME=$(kubectl get pods --namespace test -l "app.kubernetes.io/name=mlflow,app.kubernetes.io/instance=mlflow-test" -o
	jsonpath="$
	  echo "Visit http://127.0.0.1:5000 to use your application"
	  kubectl port-forward $POD_NAME 5000:5000
	```

 - `helm ls`
	```$ helm ls
	NAME       	REVISION	UPDATED                 	STATUS  	CHART       	APP VERSION	NAMESPACE
	mlflow-test	1       	Sat Dec 22 19:02:48 2018	DEPLOYED	mlflow-0.1.0	1.0        	test
	```

**6. Debug:**
 - `kubectl get pods --namespace=test`
	```$ kubectl get pods --namespace=test
	NAME                           READY     STATUS    RESTARTS   AGE
	mlflow-test-6d7bff6657-zdwv7   1/1       Running   0          45s
	```
 - `kubectl describe pods <pod-id> --namespace=test`
	```$ kubectl describe pods mlflow-test-6d7bff6657-zdwv7 --namespace=test
	Name:           mlflow-test-6d7bff6657-zdwv7
	Namespace:      test
	Node:           minikube/192.168.122.222
	Start Time:     Sat, 22 Dec 2018 19:02:48 +0100
	Labels:         app.kubernetes.io/instance=mlflow-test
	                app.kubernetes.io/name=mlflow
	                pod-template-hash=2836992213
	Annotations:    <none>
	Status:         Running
	IP:             172.17.0.19
	Controlled By:  ReplicaSet/mlflow-test-6d7bff6657
	Containers:
	  mlflow:
	    Container ID:   docker://91a5cf6305988c3267415f86964fba99f1f0c08f7dc2bf6ede2c9b11f4133ba6
	    Image:          danielvdende/docker-mlflow:latest
	    Image ID:       docker-pullable://danielvdende/docker-mlflow@sha256:8de541bb13eedd3d12348f6e65f97db022330579da1f7ffcd80295e0983b0f9c
	    Port:           5000/TCP
	    Host Port:      0/TCP
	    State:          Running
	      Started:      Sat, 22 Dec 2018 19:02:49 +0100
	    Ready:          True
	    Restart Count:  0
	    Liveness:       http-get http://:http/ delay=0s timeout=1s period=10s #success=1 #failure=3
	    Readiness:      http-get http://:http/ delay=0s timeout=1s period=10s #success=1 #failure=3
	    Environment:    <none>
	    Mounts:
	      /var/run/secrets/kubernetes.io/serviceaccount from default-token-sx8gh (ro)
	Conditions:
	  Type           Status
	  Initialized    True
	  Ready          True
	  PodScheduled   True
	Volumes:
	  default-token-sx8gh:
	    Type:        Secret (a volume populated by a Secret)
	    SecretName:  default-token-sx8gh
	    Optional:    false
	QoS Class:       BestEffort
	Node-Selectors:  <none>
	Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
	                 node.kubernetes.io/unreachable:NoExecute for 300s
	Events:
	  Type    Reason                 Age   From               Message
	  ----    ------                 ----  ----               -------
	  Normal  Scheduled              1m    default-scheduler  Successfully assigned mlflow-test-6d7bff6657-zdwv7 to minikube
	  Normal  SuccessfulMountVolume  1m    kubelet, minikube  MountVolume.SetUp succeeded for volume "default-token-sx8gh"
	  Normal  Pulled                 1m    kubelet, minikube  Container image "danielvdende/docker-mlflow:latest" already present on machine
	  Normal  Created                1m    kubelet, minikube  Created container
	  Normal  Started                1m    kubelet, minikube  Started container
	```

 - `kubectl port-forward --namespace=test deployment/mlflow-test 5000:5000`
  ![mlflow-ui](https://raw.githubusercontent.com/pilillo/helm-charts/master/docs/mlflow_ui.png "MLflow UI")


**7. Cleanup:**
 - `helm ls`
 - `helm delete mlflow-test --purge`
 - `helm ls` should now be empty
 - `kubectl delete namespace test`

**8. Package:**
 - `helm package mlflow`  
 Successfully packaged chart and saved it to: /home/pilillo/Documenti/helm-charts/mlflow-0.1.0.tgz
 - `mv mlflow-0.1.0.tgz docs`
 - `helm repo index docs --url https://pilillo.github.com/helm-charts`
  ```$ ls docs/
 index.html  index.yaml  mlflow-0.1.0.tgz
 ```
 An index.yaml file was created, so we can now use the docs folder as an Helm repository after committing those files to the repo.
 - Set the doc folder as the one to use to store the documentation on Github
  ![github-doc](https://raw.githubusercontent.com/pilillo/helm-charts/master/docs/set_doc.png "Github doc folder")

**9. Use Github as Helm Repository**
 - `helm repo add <name> <URL>`
	```$ helm repo add helm-charts https://pilillo.github.com/helm-charts
	"helm-charts" has been added to your repositories
	```
 - `helm repo list`
	```$ helm repo list
	NAME       	URL
	stable     	https://kubernetes-charts.storage.googleapis.com
	local      	http://127.0.0.1:8879/charts
	helm-charts	https://pilillo.github.com/helm-charts
	```
 - `helm repo update`
	```$ helm repo update
	Hang tight while we grab the latest from your chart repositories...
	...Skip local chart repository
	...Successfully got an update from the "helm-charts" chart repository
	...Successfully got an update from the "stable" chart repository
	Update Complete. ⎈ Happy Helming!⎈
	```
 - `helm search <name>`
	```$ helm search mlflow
	NAME              	CHART VERSION	APP VERSION	DESCRIPTION
	helm-charts/mlflow	0.1.0        	1.0        	A Helm chart for MLflow
	local/mlflow      	0.1.0        	1.0        	A Helm chart for MLflow
	```

Yeeeey, we can now install/delete MLflow directly from helm!

