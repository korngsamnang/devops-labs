# 🎯 Goal

Deploy a container that serves “Hello World” on port 80 using:

-   Deployment
-   Service
-   (Optional: Ingress)

## Step 1: Use a Ready-Made Image

We'll use the public image:
`nginxdemos/hello` — it shows a simple "Hello World" HTML page.

### 📁 Folder Structure (suggested)

```
hello-k8s/
├── deployment.yaml
├── service.yaml
└── ingress.yaml (optional)
```

🧾 `deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: hello-world
spec:
    replicas: 2
    selector:
        matchLabels:
            app: hello-world
    template:
        metadata:
            labels:
                app: hello-world
        spec:
            containers:
                - name: hello-world
                  image: nginxdemos/hello:latest
                  ports:
                      - containerPort: 80
```

🧾 `service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
    name: hello-world-service
spec:
    selector:
        app: hello-world
    ports:
        - protocol: TCP
          port: 80
          targetPort: 80
    type: NodePort # Expose to your browser (outside cluster)
```

> You can change NodePort to LoadBalancer if you're on a cloud provider.

🧾 `ingress.yaml` (Optional — if you use Ingress)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: hello-world-ingress
spec:
    rules:
        - host: hello.local
          http:
              paths:
                  - path: /
                    pathType: Prefix
                    backend:
                        service:
                            name: hello-world-service
                            port:
                                number: 80
```

> 📝 Requires Ingress Controller and /etc/hosts edit (127.0.0.1 hello.local).

## 🚀 Apply to Kubernetes

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Then check:

```bash
kubectl get pods
kubectl get service
```

## 🔍 Let's Break Down What You See:

```
hello-world-service   NodePort    10.102.185.154   <none>        80:30251/TCP   95s
```

-   NodePort: ✅ This means the service is exposed outside the cluster.
-   80:30251/TCP: Inside the cluster it's port 80, but externally it's mapped to port 30251 on your host machine.

## 🚀 How to Access It

### 🖥️ If You’re Using Minikube:

```bash
minikube service hello-world-service
```

✅ This will automatically open a browser to the correct IP and port.
