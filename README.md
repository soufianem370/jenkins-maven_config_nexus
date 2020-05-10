# integrate nexus to maven/jenkins

install jenkins(in your instance jenkins install git and maven

```
helm install my-release --set persistence.storageClass=nfs-client stable/jenkins
```
watch this videos for more details: https://www.youtube.com/watch?v=qbO4MTESiJQ

## pour installer nexus ---todo
### 1)create pvc
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: nexus-pvc
  namespace: devops-tools
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: nfs-client
```
### 2) create deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nexus
  namespace: devops-tools
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nexus-server
  template:
    metadata:
      labels:
        app: nexus-server
    spec:
      containers:
        - name: nexus
          image: sonatype/nexus3:latest
          resources:
            limits:
              memory: "4Gi"
              cpu: "1000m"
            requests:
              memory: "2Gi"
              cpu: "500m"
          ports:
            - containerPort: 8081
          volumeMounts:
            - name: nexus-data
              mountPath: /nexus-data
      volumes:
       - name: nexus-data
         persistentVolumeClaim:
          claimName: nexus-pvc
```
### 3) create svc

```
apiVersion: v1
kind: Service
metadata:
  name: nexus-service
  namespace: devops-tools
  annotations:
      prometheus.io/scrape: 'true'
      prometheus.io/path:   /
      prometheus.io/port:   '8081'
spec:
  selector:
    app: nexus-server
  type: NodePort
  ports:
    - port: 8081
      targetPort: 8081
      nodePort: 32000
```

https://devopscube.com/setup-nexus-kubernetes/

## sonarqube --todo
https://medium.com/@akamenev/running-sonarqube-on-azure-kubernetes-92a1b9051120
