# Pod
* k8s không chạy container trực tiếp mà thông qua Pod
* Các container chia sẻ tài nguyên và mạng cục bộ trong Pod
* Mặc định k8s không tạo và chạy Pod trên Master Node
* Cấu hình thăm dò container còn sống:
```
apiVersion: v1
kind: Pod
metadata:
name: mypod
labels:
    app: mypod
spec:
containers:
- name: mycontainer
    image: ichte/swarmtest:php
    ports:
    - containerPort: 8085
    resources: {}

    livenessProbe:
    httpGet:
        path: /healthycheck
        port: 8085
    initialDelaySeconds: 10
    periodSeconds: 10
```
* Có thể có nhiều container:
```
apiVersion: v1
kind: Pod
metadata:
name: nginx-swarmtest
labels:
    app: myapp
spec:
containers:
- name: n1
    image: nginx:1.17.6
    resources:
    limits:
        memory: "128Mi"
        cpu: "100m"
    ports:
    - containerPort: 80
- name: s1
    image: ichte/swarmtest:node
    resources:
    limits:
    memory: "150Mi"
    cpu: "100m"
    ports:
    - containerPort: 8085
```
* Cổng host 8080 được chuyển hướng truy cập đến cổng 8085 của POD mypod:
```
kubectl port-forward mypod 8080:8085
```
* Volume trong Pod:
```
apiVersion: v1
kind: Pod
metadata:
name: nginx-swarmtest-vol
labels:
    app: myapp
spec:
volumes:
    # Định nghĩa một volume - ánh xạ thư mục /home/www máy host
    - name: "myvol"
    hostPath:
        path: "/home/html"
containers:
- name: n1
    image: nginx:1.17.6
    resources:
    limits:
        memory: "128Mi"
        cpu: "100m"
    ports:
    - containerPort: 80
    volumeMounts:
    - mountPath: /usr/share/nginx/html
        name: "myvol"
- name: s1
    image: ichte/swarmtest:node
    resources:
    limits:
    memory: "150Mi"
    cpu: "100m"
    ports:
    - containerPort: 8085
    volumeMounts:
    - mountPath: /data/
        name: "myvol"
```




































