## Install tools

`brew install`

- `awscli`
- `eksctl`
- `aws-iam-authenticator`
- `kubernetes-cli`
- `k9s`
- `kubectx`

## Create EKS cluster

```shell
# Create a cluster, takes about 15 minutes
eksctl create cluster \
--name demo-cluster \
--version 1.14 \
--nodegroup-name demo-cluster-workers \
--node-type t3.medium \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3 \
--node-ami auto

# Verify the cluster is running
kubectl get pods --all-namespaces
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

- `kubectl get service hello-service`
- Copy load balancer address from `EXTERNAL-IP`
- Browse to http://balancer-name:8080

## Clean up

```shell
eksctl delete cluster --name demo-cluster
```

## References

- <https://eksworkshop.com/>
