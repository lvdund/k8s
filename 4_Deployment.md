# Deployment
* Deployment quản lý một nhóm các Pod - các Pod được nhân bản, nó tự động thay thế các Pod bị lỗi, không phản hồi bằng pod mới nó tạo ra. Như vậy, deployment đảm bảo ứng dụng của bạn có một (hay nhiều) Pod để phục vụ các yêu cầu.
* Deployment sử dụng mẫu Pod (Pod template - chứa định nghĩa / thiết lập về Pod) để tạo các Pod (các nhân bản replica), khi template này thay đổi, các Pod mới sẽ được tạo để thay thế Pod cũ ngay lập tức.
* Exmaple:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  # tên của deployment
  name: deployapp
spec:
  # số POD tạo ra
  replicas: 3

  # thiết lập các POD do deploy quản lý, là POD có nhãn  "app=deployapp"
  selector:
    matchLabels:
      app: deployapp

  # Định nghĩa mẫu POD, khi cần Deploy sử dụng mẫu này để tạo Pod
  template:
    metadata:
      name: podapp
      labels:
        app: deployapp
    spec:
      containers:
      - name: node
        image: ichte/swarmtest:node
        resources:
          limits:
            memory: "128Mi"
            cpu: "100m"
        ports:
          - containerPort: 8085
```
* Cập nhật:

    * Khi một Deployment được cập nhật, thì Deployment dừng lại các Pod, scale lại số lượng Pod về 0, sau đó sử dụng template mới của Pod để tạo lại Pod, Pod cũ không xóa hẳng cho đến khi Pod mới đang chạy, quá trình này diễn ra đến đâu có thể xem bằng lệnh `kubectl describe deploy/namedeploy`. Cập nhật như vậy nó đảm bảo luôn có Pod đang chạy khi đang cập nhật.
    * Có thể thu hồi lại bản cập nhật bằng cách sử dụng lệnh `kubectl rollout undo`
    * Cập nhật image mới trong POD - ví dụ thay image của container node bằng image mới httpd
        ```
        kubectl set image deploy/deployapp node=httpd --record
        ```
    * Để xem quá trình cập nhật của deployment
        ```
        kubectl rollout status deploy/deployapp
        ```
    * Khi cập nhật, ReplicaSet cũ sẽ hủy và ReplicaSet mới của Deployment được tạo, tuy nhiên ReplicaSet cũ chưa bị xóa để có thể khôi phục lại về trạng thái trước (rollback)
    * Bạn cũng có thể cập nhật tài nguyên POD theo cách tương tự, ví dụ giới hạn CPU, Memory cho container với tên app-node
        ```
        kubectl set resources deploy/deployapp -c=node --limits=cpu=200m,memory=200Mi
        ```
    * Revision:
        ```
        $ kubectl rollout history deploy/deployapp
        $ kubectl rollout history deploy/deployapp --revision=1
        $ kubectl rollout undo deploy/mydeploy

        ```
    * Scale
        ```
        $ kubectl scale deploy/deployapp --replicas=10
        $ kubectl autoscale deploy/deployapp --min=2 --max=5 --cpu-percent=50
        $ kubectl get hpa/deployapp -o yaml > 2.hpa.yaml
    











