"# Kubernetes-101-Deploying-Your-First-Application" 

https://www.youtube.com/watch?v=XltFOyGanYE 

https://raw.githubusercontent.com/RodrigoMvs123/Kubernetes-101-Deploying-Your-First-Application/main/README.md



Getting started with Kubernetes
Overview/Context
Create simple application
Containerize a simple application
Push to Container registry
Create cluster
Create Kubernetes resource definitions 
Apply resources
Add DNS records
Validate
Demo loading balance 

FastAPI
https://fastapi.tiangolo.com/ 

Visual Studio Code
Terminal 
python3 -m venv ./venv
ls
source ./venv/bin/activate 
pip install fastapi
pip freeze
code requirements.txt 

Visual Studio Code
EXPLORER 
OPEN EDITORS
requirements.txt 

requirements.txt 
fastapi~=0.79
uvicorn~=0.18

Visual Studio Code
Terminal 
pip install -r requirements.txt
mkdir app
cd app
touch __init__.py
code main.py

main.py
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}

Visual Studio Code
Terminal
uvicorn main:app --reload

Visual Studio Code
EXPLORER 
OPEN EDITORS
app
__pycache__
__init__.py
main.py
venv
requirements.txt

localhost:8000

{
     “Hello”: “World”
}

Container 
FastAPI in Containers - Docker 
https://fastapi.tiangolo.com/deployment/docker/?h=container 

Visual Studio Code
Terminal
code Dockerfile

Visual Studio Code
EXPLORER 
OPEN EDITORS
app
__pycache__
__init__.py
main.py
venv
Dockefile
requirements.txt


Dockerfile
FROM python:3.9

WORKDIR /code

COPY ./requirements.txt /code/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

COPY ./app /code/app

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80"]

Visual Studio Code
Terminal
docker build .
docker build -t k8s-fast-api
docker run -p 8000:80 k8s-fast-api

Visual Studio Code
EXPLORER 
OPEN EDITORS
app
__pycache__
__init__.py
main.py
venv
Dockefile
requirements.txt

main.py
from fastapi import FastAPI

import os

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": f"From: {os.environ.get('ENV', 'DEFAULT')}"}

Visual Studio Code
Terminal
docker build -t k8s-fast-api
docker run -p 8000:80 k8s-fast-api

localhost:8000

{
     “Hello”: “From: DEFAULT_ENV”
}

Dockerhub
Create Repository
k8s-getting-started
Create
Docker commands
Docker push sidpalas/k8s-getting-started:tagname

Visual Studio Code
Terminal
docker build -t sidpalas/k8s-getting-started:0.0.1
docker push sidpalas/k8s-getting-started:0.0.1

Dockerhub

sidpalas/k8s-getting-started:tagname

Tags and Scans
TAG                   OS
0.0.1                  Linux ( Kernel )

Cluster 
Civo Cloud
https://www.civo.com/ 

Create a new Kubernetes cluster
Name
k8s-getting-started
How many nodes
3
      5. Select size
Small
Create cluster

Visual Studio Code
Terminal
mkdir Kubernetes 
cd Kubernetes
ls
code deployment.yaml

Kubernetes ( Deployment )
https://kubernetes.io/docs/concepts/workloads/controllers/deployment/ 

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt

deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fast-api
  labels:
    app: fast-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fast-api
  template:
    metadata:
      labels:
        app: fast-api
    spec:
      containers:
      - name: fast-api
        image: sidpalas/k8s-getting-started:0.0.1
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
            memory: 300Mi
          limits:
            memory: 400Mi

Visual Studio Code
Terminal
code service.yaml

Kubernetes
Defining a Service 
https://kubernetes.io/docs/concepts/services-networking/service/ 

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt

service.yaml 
apiVersion: v1
kind: Service
metadata:
  name: fast-api
spec:
  selector:
    app: fast-api
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80

Civo UI
Cluster Information
…
Kubeconfig ( Download ) 

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

civo-kubeconfig
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: ...
    server: https://212.2.240.69:6443
  name: k8s-getting-started
contexts:
- context:
    cluster: k8s-getting-started
    user: k8s-getting-started
  name: k8s-getting-started
current-context: k8s-getting-started
kind: config
preferences: {}
users: 
- name: k8s-getting-started
  user: 
    client-certificate-data: ...
    client-key-data: ...

Visual Studio Code
Terminal 
cd ..
ls
export KUBECONFIG=/Matheus\Desktop\Rodrigo\Visual Studio Code\Kubernetes\Kubernetes 101 Deploying Your First Application\Kubernetes/civo-kubeconfig

kubectl get nodes
cd Kubernetes
kubectl apply -f .
kubectl get pods
…
kubectl get pods -w
…
kubectl port-forward fast-api-6b55fb995f-tm8dt 8080:80

localhost:8080

{
     “Hello”: “From: DEFAULT_ENV”
}

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fast-api
  labels:
    app: fast-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fast-api
  template:
    metadata:
      labels:
        app: fast-api
    spec:
      containers:
      - name: fast-api
        image: sidpalas/k8s-getting-started:0.0.1
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
            memory: 300Mi
          limits:
            memory: 400Mi
        env:
          - name: ENV
            value: "CIVO"

Visual Studio Code
Terminal
kubectl apply -f .
kubectl get pods
kubectl port-forward fast-api-587df9844c-b9vrk 8080:80

localhost:8080

{
     “Hello”: “From: CIVO”
}

Kubernetes Ingress
https://kubernetes.io/docs/concepts/services-networking/ingress/ 

Traefik and Kubernetes
https://doc.traefik.io/traefik/providers/kubernetes-ingress/ 

Visual Studio Code
Terminal
code ingress.yaml

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
ingress.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: foo
spec:
  rules:
    - host: example.net
      http:
        paths:
          - path: /bar
            pathType: Exact
            backend:
              service:
                name:  service1
                port:
                  number: 80
          - path: /foo
            pathType: Exact
            backend:
              service:
                name:  service1
                port:
                  number: 80

Visual Studio Code
Terminal
kubectl get pods 
k get namespaces

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
ingress.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: fast-api
spec:
  rules:
    - host: k8s.devopsdirective.com
      http:
        paths:
          - path: /
            pathType: Exact
            backend:
              service:
                name:  fast-api
                port:
                  number: 80
      
Visual Studio Code
Terminal
kubectl apply -f .
k get ingress

CIVO UI
…
external IP: https://212.2.24069:6443

Claudeflare UI
https://www.cloudflare.com/ 

devopsdirective.com
Active
DNS
Add record
Name
k8s
IPV4 address ( required )
 212.2.24069
Save

k8s devopsdirective.com

{
     “Hello”: “From: CIVO”
}


Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
ingress.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

main.py
from fastapi import FastAPI

import os

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": f"From: {os.environ.get('HOSTNAME', 'DEFAULT')}"}

Visual Studio Code
Terminal
cd ..
docker build -t sidpalas/k8s-getting-started:0.0.2 .
docker push -t sidpalas/k8s-getting-started:0.0.2 .

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
ingress.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fast-api
  labels:
    app: fast-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fast-api
  template:
    metadata:
      labels:
        app: fast-api
    spec:
      containers:
      - name: fast-api
        image: sidpalas/k8s-getting-started:0.0.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
            memory: 300Mi
          limits:
            memory: 400Mi
        env:
          - name: ENV
            value: "CIVO"

Visual Studio Code
Terminal
cd Kubernetes
kubectl apply -f .
kubectl get pods
kubectl get pods fast-api-6576cbddd-74fpq -o yaml

k8s devopsdirective.com

{
     “Hello”: “From: DEFAULT_ENV”
}

Visual Studio Code
Terminal
kubectl get pods
kubectl exec -it fast-api-6576cbddd-74fpq - -bash
echo $HOSTNAME
python
import os
print(os.environ.get(“HOSTNAME”))
exit()
exit
cd..
docker build -t sidpalas/k8s-getting-started:0.0.3 .
docker push -t sidpalas/k8s-getting-started:0.0.3 .

Visual Studio Code
EXPLORER 
OPEN EDITORS
deployment.yaml
ingress.yaml
service.yaml Kubernetes
app
__pycache__
__init__.py
main.py
Kubernetes
venv
Dockefile
requirements.txt
civo-kubeconfig

deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: fast-api
  labels:
    app: fast-api
spec:
  replicas: 3
  selector:
    matchLabels:
      app: fast-api
  template:
    metadata:
      labels:
        app: fast-api
    spec:
      containers:
      - name: fast-api
        image: sidpalas/k8s-getting-started:0.0.3
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
            memory: 300Mi
          limits:
            memory: 400Mi
        env:
          - name: ENV
            value: "CIVO"

Visual Studio Code
Terminal
cd Kubernetes
kubectl apply -f .
kubectl get pods

k8s devopsdirective.com

{
     “Hello”: “From: fast-api-65d58c9b5-xf894”
}

Refresh Page 

k8s devopsdirective.com

{
     “Hello”: “From: fast-api-65d58c9b5-lpsjn”
}

Refresh Page 

k8s devopsdirective.com

{
     “Hello”: “From: fast-api-65d58c9b5-zpq9j”
}

