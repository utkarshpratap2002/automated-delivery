# Implementing CI/CD Pipeline using Kubernetes

### Sample application is deployed using the Kubernetes. 

```
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

### Automated deployment using `helm`.

```
helm install mywebsite ./mywebsite-chart
```

### GitHub Setup

```
# initialise
git init

# add to staging area
git add .

git commit -m "version:v1"

```

### Adding GitHub action



