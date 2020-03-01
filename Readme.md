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

3.0 return a 500 Not found to simulate a bug in the app and automatic rollback Canary deployment examples



## Usage with Kubernetes

````
apiVersion: v1
kind: Service
metadata:
  name: api
  namespace: testapi
  labels:
    run: api
spec:
  ports:
  - port: 3000
    protocol: TCP
  selector:
    run: api

---

apiVersion: apps/v1
kind: Deployment
metadata:
  name: api
  namespace: testapi
spec:
  selector:
    matchLabels:
      run: api
  replicas: 4
  template:
    metadata:
      labels:
        run: api
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
        ports:
        - containerPort: 3000


````


