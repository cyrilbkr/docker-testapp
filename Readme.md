# docker-testapp

Simple Node.js app for Canary deployment test

## Usage with Docker

     docker run -ti -p 3000:80 cyrilbkr/testapp

### Tags / Releases

#### 1.0 & 2.0

* cyrilbkr/testapp:1.0

* cyrilbkr/testapp:2.0

1.0 & 2.0 return an http 200 Hello World type message with the current release running. 

#### 3.0

* cyrilbkr/testapp:3.0

3.0 return a http 5xx code to simulate a bug in the app and automatic rollback Canary deployment examples



## Usage with Kubernetes

### Quick simple setup

````
$ kubectl apply -f https://raw.githubusercontent.com/cyrilbkr/docker-testapp/master/kubernetes/simple/service.yaml
$ kubectl apply -f https://raw.githubusercontent.com/cyrilbkr/docker-testapp/master/kubernetes/simple/deployment.yaml
````

````
apiVersion: v1
kind: Service
metadata:
  name: api
  labels:
    app: api
spec:
  ports:
  - port: 3000
    protocol: TCP
  selector:
    app: api

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  selector:
    matchLabels:
      app: api
  replicas: 1
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: cyrilbkr/testapp
        ports:
        - containerPort: 3000


````

### Production like setup (w/ hpa/limits/rollingupdate)

````
$ kubectl apply -f https://raw.githubusercontent.com/cyrilbkr/docker-testapp/master/kubernetes/prod/service.yaml
$ kubectl apply -f https://raw.githubusercontent.com/cyrilbkr/docker-testapp/master/kubernetes/prod/deployment.yaml
$ kubectl apply -f https://raw.githubusercontent.com/cyrilbkr/docker-testapp/master/kubernetes/prod/hpa.yaml
````



````

apiVersion: v1
kind: Service
metadata:
  name: api
  labels:
    app: api
spec:
  ports:
  - port: 3000
    protocol: TCP
  selector:
    app: api

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
spec:
  selector:
    matchLabels:
      app: api
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      labels:
        app: api
    spec:
      containers:
      - name: api
        image: cyrilbkr/testapp
        resources:
          requests:
            memory: "64Mi"
            cpu: "0.1"
          limits:
            memory: "2048Mi"
            cpu: "1.5"
        livenessProbe:
          tcpSocket:
            port: 3000
          initialDelaySeconds: 5
          periodSeconds: 10
        ports:
        - containerPort: 3000


---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: api
spec:
  maxReplicas: 10
  minReplicas: 3
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: api
  targetCPUUtilizationPercentage: 60

````
