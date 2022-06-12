# kubectl
Changing default namespace
kubectl config set-context --current --namespace=NAMESPACE_NAME
kubectl config set-context --current --namespace=orange
================================================================
# Pods

Creating pod from CLI:

# kubectl run single-pod --image busybox -- sleep infinity
pod/single-pod created

# kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
single-pod             1/1     Running   0          3s

Generating Manifest:
# kubectl run single-pod --image busybox --dry-run=client -o yaml -- sleep infinity
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: single-pod
  name: single-pod
spec:
  containers:
  - args:
    - sleep
    - infinity
    image: busybox
    name: single-pod
    resources: {​}​
  dnsPolicy: ClusterFirst
  restartPolicy: Always
```
================================================================

#Deployments
Generating Manifest:

# kubectl create deployment web --image nginx:1.16-alpine --dry-run=client -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: web
  name: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: web
    spec:
      containers:
      - image: nginx:1.16-alpine
        name: nginx
        
# kubectl create deployment web --image nginx:1.16-alpine
# kubectl scale deployment web --replicas=5
# kubectl set image deployment web nginx=nginx:1.17-alpine

# Let's see how it updates pods
# kubectl get rs
NAME             DESIRED   CURRENT   READY   AGE
web-7994bbc845   4         4         4       2m36s
web-7f88b757fb   3         3         0       3s

# kubectl get deployments.apps web -o jsonpath='{​.spec.template.spec.containers[].image}​'
nginx:1.17-alpine

# kubectl rollout undo deployment web 
deployment.apps/web rolled back
================================================================
# Services
kubectl run test --image busybox:1.28 -i --tty --rm -- wget -q -O- web-app-service
kubectl create deployment myapp --image sbeliakou/web-echo:2
kubectl expose deployment myapp --name myapp-headless --cluster-ip=None
kubectl expose deployment myapp --name myapp --port 80
kubectl create service externalname db \
  --external-name=prodb.a1b2c3d4wxyz.eu-west-1.rds.playpit.net

cat << EOF | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
 labels:
   app: hello-hello
 name: hello-hello-service
spec:
 ports:
 - nodePort: 31572
   port: 80
   protocol: TCP
   targetPort: 8080
 selector:
   app: hello-hello
 type: NodePort
EOF
 
kubectl patch svc hello-hello-service \
  --patch='{​
     "spec": {​
       "selector": {​
         "app": "hello"
        }​
     }​
  }​'
 
cat << END | kubectl apply -f -
apiVersion: v1
kind: Service
metadata:
 name: my-web-service
spec:
 selector:
  app: simple-web-service
 ports:
 - name: http
   protocol: TCP
   port: 80
 targetPort: 80
 externalIPs:
 - 172.31.0.2
END
 
kubectl expose deployment -n prod book-shelf \
  --name book-shelf-service \
  --port=80 \
  --type LoadBalancer




