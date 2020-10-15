# Kubernetes相关操作
## 1、Deployment
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-deployment
  labels:
    app: hello
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello
  template:
    metadata:
      labels:
        app: hello
    spec:
      containers:
        - name: hello
          image: ruolihello:v1.0.0
          ports:
            - containerPort: 8080
```
>创建deployment  
kubectl create -f deployment.yaml

>删除deployment
kubectl delete deploy hello-deployment

>查看deployment  
kubectl get deploy
