## Minimal Kubernetes Setup for a Typical Web App

Imagine you're deploying a web app (e.g., Node.js, Laravel, Django, etc.) — here's the core YAML files you usually need:

### 1. Deployment YAML

This defines your app’s Pods (the containers).

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-app
spec:
    replicas: 2
    selector:
        matchLabels:
            app: my-app
    template:
        metadata:
            labels:
                app: my-app
        spec:
            containers:
                - name: app
                  image: my-app:latest
                  ports:
                      - containerPort: 3000
```

### 2. Service YAML

Expose the Pods so other services (or users) can reach them.

```yaml
apiVersion: v1
kind: Service
metadata:
    name: my-app-service
spec:
    selector:
        app: my-app
    ports:
        - protocol: TCP
          port: 80
          targetPort: 3000
    type: ClusterIP
```

> 👉 You might change type: ClusterIP to NodePort or LoadBalancer if you want external access.

### 3. Ingress YAML (optional but common)

If you're using a domain (e.g., myapp.com) and want routing:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
    name: my-app-ingress
spec:
    rules:
        - host: myapp.local
          http:
              paths:
                  - path: /
                    pathType: Prefix
                    backend:
                        service:
                            name: my-app-service
                            port:
                                number: 80
```

> ⚠️ Requires Ingress Controller (e.g., NGINX Ingress).

### 4. Secret or ConfigMap (if needed)

For environment variables or credentials:
**Secret:**

```yaml
apiVersion: v1
kind: Secret
metadata:
    name: app-secret
type: Opaque
data:
    DB_PASSWORD: cGFzc3dvcmQ= # base64 encoded
```

**ConfigMap:**

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
    name: app-config
data:
    NODE_ENV: production
    PORT: "3000"
```

Then mount them in your Deployment:

```yaml
envFrom:
    - configMapRef:
          name: app-config
    - secretRef:
          name: app-secret
```

## 📦 Optional Add-ons (only if needed)

| Use Case                | Resource Type                               |
| ----------------------- | ------------------------------------------- |
| Persistent file storage | `PersistentVolume`, `PersistentVolumeClaim` |
| Jobs / Cron tasks       | `Job`, `CronJob`                            |
| HTTPS certificates      | `Certificate` (via cert-manager)            |
| Horizontal auto-scaling | `HorizontalPodAutoscaler`                   |

## 💡 Pro Tip: Use a Folder Structure

You can organize your files like:

```
k8s/
├── deployment.yaml
├── service.yaml
├── ingress.yaml
├── configmap.yaml
├── secret.yaml
```

Then apply all at once:

```bash
kubectl apply -f k8s/
```

it feels like overkill to create 4-6 files just to deploy one app — but it gets easier with:

-   Reuse (you'll copy these a lot)
-   Tools (Helm charts, Kustomize)
-   Practice

> 👉 Knowing what files to create is one thing… but knowing how to fill them out correctly is where most people get stuck.

## 🧭 Strategy to Beat Kubernetes YAML Overwhelm

### 1. Start from Templates

You don’t have to write YAML from scratch — use working templates and modify them.
Example: [kubernetes/examples](https://github.com/kubernetes/examples), or use this simplified base:

```yaml
# k8s/deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
    name: my-app
spec:
    replicas: 2
    selector:
        matchLabels:
            app: my-app
    template:
        metadata:
            labels:
                app: my-app
        spec:
            containers:
                - name: my-app
                  image: your-image-name
                  ports:
                      - containerPort: 3000
```

Modify only what you need:

-   `replicas` → how many instances?
-   `image` → your Docker image
-   `containerPort` → what port your app listens on?

### 2. Learn the Minimum YAML Blocks

Here’s a cheat sheet of common fields you’ll see:
| Field | Meaning |
| ------------------------------- | -------------------------------------------- |
| `metadata.name` | The name you assign to the object |
| `spec.replicas` | Number of copies of the app |
| `spec.selector.matchLabels` | What Pods this applies to |
| `spec.template.spec.containers` | Defines the container image and settings |
| `containerPort` | Port the app listens on inside the container |

You can ignore most other fields at the beginning!

### 3. Use kubectl explain

This command explains any part of a manifest directly:

```bash
kubectl explain deployment.spec.template.spec.containers
```

You’ll get documentation in your terminal. Use it like a built-in cheat sheet.

### 4. Use Online Tools to Generate YAML

You don’t need to write everything manually.

-   https://k8syaml.com ← simple generator
-   https://kompose.io ← converts docker-compose.yml to K8s
-   https://www.kubecfg.dev ← visual YAML builder

### 5. Use Comments and Docs in Your YAML

Keep it human:

```yaml
containers:
    - name: my-app
      image: myapp:latest
      ports:
          - containerPort: 3000 # App listens here
      env:
          - name: NODE_ENV
            value: "production"
```

### 6. Start Small, Then Expand

Focus on the core parts first, then add more complexity as you get comfortable.
