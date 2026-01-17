# Task: Deploy your own custom-built Docker Image from a private Container Registry as a K8s Deployment+Service

1. **Create Private repo in DockerHub**

![alt text](Img/image.png)
2. **Push the docker image to the private repo in DockerHub**

```
docker tag node-app rexxx9865/node-k8:v1.0.1
docker push rexxx9865/node-k8:v1.0.1
```

3. **Verify**

![alt text](Img/image-1.png)

4. **Create namespace in node**

```
kubectl create ns priv-docker
```

5. **Create Secret in priv-docker namespace**

![alt text](Img/{84293C97-DCFC-4A2D-9D5F-93CA81189957}.png)

6. **Verify Secret**

![alt text](Img/{7D7836EB-88FA-4C1A-B70D-D9D4C7D0E02F}.png)

7. **Create Deployment.yaml file**

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: priv-app
  name: priv-app
  namespace: priv-docker
spec:
  replicas: 2
  selector:
    matchLabels:
      app: priv-app
  template:
    metadata:
      labels:
        app: priv-app
    spec:
      imagePullSecrets:
        - name: my-key
      containers:
        - name: priv-app
          image: rexxx9865/node-k8:v1.0.1
          imagePullPolicy: Always

---
apiVersion: v1
kind: Service
metadata:
  name: priv-service
spec:
  type: NodePort
  selector:
    app: priv-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
      nodePort: 30001
```

8. **Run the YAML file**

```
kubectl apply -f deployment.yaml
```
![alt text](Img/{34F18D9C-5BC6-448B-907E-C91D00BB4CC1}.png)

9. **Verfiy Deployment, Service and Replicaset**

```
kubectl -n priv-docker get deployment
kubectl -n priv-docker get service
kubectl -n priv-docker get replicaset
kubectl -n priv-docker get po
```
![alt text](Img/{7B8DBAC2-A623-45AA-9AA3-24D7436C9B1D}.png)

10. **Access the Application**

```
minikube -n priv-docker service priv-service
```

![alt text](Img/{B9DADDA4-98EA-4547-B01E-25D585E6C524}.png)

![alt text](Img/image-2.png)