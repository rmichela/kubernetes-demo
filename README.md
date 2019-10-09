## Install tools

`brew install`

- `awscli`
- `eksctl`
- `aws-iam-authenticator`
- `kubernetes-cli`
- `k9s`

## Create EKS cluster

```shell
eksctl create cluster \
--name demo-cluster \
--version 1.14 \
--nodegroup-name demo-cluster-workers \
--node-type t3.medium \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3 \
--node-ami auto
kubectl get pods
```

## Create pods and a deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: "hello-service"
spec:
  replicas: 2
  selector:
    matchLabels:
      app: "hello-service"
  template:
    metadata:
      labels:
        app: "hello-service"
    spec:
      containers:
        - name: "hello-service"
          image: "tutum/hello-world"
          imagePullPolicy: "IfNotPresent"
          ports:
            - name: "http"
              containerPort: 80
              protocol: TCP
```

## Create a service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: "hello-service"
spec:
  type: LoadBalancer
  ports:
    - name: "http"
      port: 8080
      targetPort: 80
      protocol: TCP
  selector:
    app: "hello-service"
```

## Find the external ELB name

Then browse to <http://localhost:8080/>

## References

- <https://eksworkshop.com/>
