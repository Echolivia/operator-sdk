# Operator SDK

### Project Status: pre-alpha

The project is currently pre-alpha and it is expected that breaking changes to the API will be made in the upcoming releases.

See the [design docs][design_docs] for planned work on upcoming milestones.

## Overview

[Operators][operator_link] make it easy to manage complex stateful applications on top of Kubernetes. However writing an operator today can be difficult because of challenges such as using low level APIs, writing boilerplate, and a lack of modularity which leads to duplication.

The Operator SDK is a framework designed to make writing operators easier by providing:
- High level APIs and abstractions to write the operational logic more intuitively
- Tools for scaffolding and code generation to bootstrap a new project fast
- Modular functionality to improve reusability

## Workflow

The SDK provides the following workflow to develop a new operator:
1. Create a new operator project using the SDK Command Line Interface(CLI)
2. Define new resource APIs by adding Custom Resource Definitions(CRD)
3. Specify resources to watch using the SDK API
4. Define the operator reconciling logic in a designated handler and use the SDK API to interact with resources
5. Use the SDK CLI to build and generate the operator deployment manifests

At a high level an operator using the SDK processes events for watched resources in a user defined handler and takes actions to reconcile the state of the application.

## Quick Start

First, checkout and install the operator-sdk CLI:

```sh
$ git checkout tags/v0.0.2
$ go install github.com/coreos/operator-sdk/commands/operator-sdk
```

Create and deploy an app-operator using the SDK CLI:

```sh
# Create an app-operator project that defines the App CR.
$ cd $GOPATH/src/github.com/example-inc/
$ operator-sdk new app-operator --api-version=app.example.com/v1alpha1 --kind=App
$ cd app-operator

# Build and push the app-operator image to a public registry such as quay.io
$ operator-sdk build quay.io/example/app-operator
$ docker push quay.io/example/app-operator

# Deploy the app-operator
$ kubectl create -f deploy/rbac.yaml
$ kubectl create -f deploy/operator.yaml

# By default, creating a custom resource (App) triggers the app-operator to deploy a busybox pod
$ kubectl create -f deploy/cr.yaml

# Verify that the busybox pod is created
$ kubectl get pod -l app=busy-box
NAME            READY     STATUS    RESTARTS   AGE
busy-box   1/1       Running   0          50s

# Cleanup
$ kubectl delete -f deploy/cr.yaml
$ kubectl delete -f deploy/operator.yaml
$ kubectl delete -f deploy/rbac.yaml
```

To learn more about the operator-sdk, see the [user guide][guide].


[operator_link]: https://coreos.com/operators/
[design_docs]: ./doc/design
[guide]: ./doc/user-guide.md