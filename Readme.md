# docker-testapp

Simple versioned app for canary deployment examples

## Usage with Docker

     docker run -ti -p 3000:80 cyrilbkr/testapp


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


