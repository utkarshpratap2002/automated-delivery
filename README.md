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

Create .github/workflows and write the yaml file that will define the actions for every commit that you'll make.

```
name: CI
permissions:
  contents: write

on:
  push:
    branches:
      - main
    paths-ignore:
      - 'helm/**'
      - README.md

jobs:
  push:
    runs-on: ubuntu-latest
    steps:
      - name: checkout repository
        uses: actions/checkout@v4

      - name: Setup docker build
        uses: docker/setup-buildx-action@v1

      - name: Login do DockerHub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKERHUB_USERNAME }}
          password: ${{ secrets.DOCKERHUB_TOKEN }}

      - name: Build and push
        uses: docker/build-push-action@v6
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ secrets.DOCKERHUB_USERNAME }}/mywebsite:${{ github.run_id }}
          platforms: linux/amd64,linux/arm64

  update-helm-chart:
    runs-on: ubuntu-latest
    needs: push
    steps:
      - name: checkout repository
        uses: actions/checkout@v4

      - name: Update tag
        run: |
          sed -i 's/tag: .*/tag: "${{github.run_id}}"/' helm/mywebsite-chart/values.yaml

      - name: Commit and push changes
        run: |
          git config --global user.email "itsutkarshpratapon@gmail.com"
          git config --global user.name "Utkarsh Pratap Singh"
          git add helm/mywebsite-chart/values.yaml
          git commit -m "Update tag in Helm chart"
          git push
```

