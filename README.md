# tekton-cnb

The project aims to demonstrate auxiliary staging mechanism for CloudFoundry applications using [Tekton](https://github.com/tektoncd/pipeline) and [Cloud Native Buildpacks(CNB)](https://github.com/buildpacks/).

## Requirements:

- k8s cluster(v1.15+)

## Install Tekton:

- Install tekton on your k8s cluster by following instructions [here](https://github.com/tektoncd/pipeline/blob/master/docs/install.md).

```
kubectl apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.yaml
```

- You can optionally install tekton [dashboard](https://github.com/tektoncd/dashboard) and [cli](https://github.com/tektoncd/cli).

- In order to access tekton Dashboard run the following command and access it on http://localhost:9097:

```
kubectl --namespace tekton-pipelines port-forward svc/tekton-dashboard 9097:9097
```

## Setup CNB builds:

Clone the repo and install the tekton task which builds the app image using CNB:

```
kubectl create namespace build
kubectl apply -f task/buildpacks-v3.yaml
```

## Build App Image:

- Create a secret and a service account which will store your docker registry credentials:

```
kubectl apply -f auth/secret.yaml
kubectl apply -f auth/serviceaccount.yaml
```

- Replace the appropriate values for `serviceAccountName`, git repo of your app, and `outputs.resources.image.url` to be built in `taskruns/build-run.yaml` and run following command to build and push app image to the specificed docker registry.

```
kubectl apply -f taskruns/build-run.yaml
```

## Deploy App:

- In order to deploy the app container image you need to have a running CF deployment like [kubecf](https://github.com/suse/kubecf).

- Install the tekton task to deploy app on CF:

```
kubectl create namespace deploy
kubectl apply -f tasks/deploy.yaml
```

- Substibute the appropriate param values in `taskruns/deploy-run.yaml` and deploy the app container image by using following command:

```
kubectl apply -f taskruns/deploy-run.yaml
```
