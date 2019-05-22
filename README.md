# Anchore Enterprise EKS Installation

The purpose of this document is to walkthrough the installation of Anchore Enterprise on EKS with Helm. Anchore currently maintains a [Helm Chart](https://github.com/helm/charts/tree/master/stable/anchore-engine) which I will use to install the necessary Anchore components. 

## Prerequisites

- A running Amazon EKS cluster with worker nodes launched. See [EKS Documentation](https://docs.aws.amazon.com/eks/latest/userguide/getting-started.html)for more information.
- [Helm](https://helm.sh/) client and server installed and configured to your EKS cluster.

**Note:** We've written a blog post titled [Introduction to Amazon EKS](https://anchore.com/introduction-to-amazon-eks/) which details how to get started on the above prerequisites.

The prerequisites for getting up and running are the most difficult part of the installation in my opinion, the Anchore Helm chart makes the installation process straightforward. 

Once you have a EKS cluster up and running and worker nodes launched, you can verify via the following command:

```
$ kubectl get nodes
NAME                                       STATUS    ROLES     AGE       VERSION
ip-10-0-1-66.us-east-2.compute.internal    Ready     <none>    1d        v1.12.7
ip-10-0-3-15.us-east-2.compute.internal    Ready     <none>    1d        v1.12.7
ip-10-0-3-157.us-east-2.compute.internal   Ready     <none>    1d        v1.12.7
```

### Anchore Helm Chart Configuration

To make proper configurations to the Helm chart, create a custom `anchore_values.yaml` file and utilize it when installing. There are many options for configuration with Anchore, for the purposes of this document, I will only change the minimum to get Anchore Enterprise installed. For reference, there is an anchore_values.yaml` [file](anchore_values.yaml) in this repository, that you may include in your installation. 

**Note:** For this installation, I will be configuring ingress and using an ALB ingress controller. You can read more about [Kubernetes Ingress with AWS ALB Ingress Controller](https://aws.amazon.com/blogs/opensource/kubernetes-ingress-aws-alb-ingress-controller/).

#### Configurations

##### Ingress

I've added the following to my `anchore_values.yaml` file under the ingress section:

```
ingress:
  enabled: true
  # Use the following paths for GCE/ALB ingress controller
  apiPath: /v1/*
  uiPath: /*
    # apiPath: /v1/
    # uiPath: /
    # Uncomment the following lines to bind on specific hostnames
    # apiHosts:
    #   - anchore-api.example.com
    # uiHosts:
    #   - anchore-ui.example.com
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
```

##### Anchore Engine API service

I've added the following to my `anchore_values.yaml` file under the Anchore API section:

```
# Pod configuration for the anchore engine api service.
anchoreApi:
  replicaCount: 1

  # Set extra environment variables. These will be set on all api containers.
  extraEnv: []
    # - name: foo
    #   value: bar

  # kubernetes service configuration for anchore external API
  service:
    type: NodePort
    port: 8228
    annotations: {}
```

**Note:** Changed service type to NodePort.

##### Anchore Enterprise Global

I've added the following to my `anchore_values.yaml` file under the Anchore Enterprise global section:

```
anchoreEnterpriseGlobal:
  enabled: true
```

**Note:** Enabled enterprise components.

##### Anchore Enterprise UI

I've added the following to my `anchore_values.yaml` file under the Anchore Enterprise UI section:

```
anchoreEnterpriseUi:
  # kubernetes service configuration for anchore UI
  service:
    type: NodePort
    port: 80
    annotations: {}
    sessionAffinity: ClientIP
```

**Note:** Changed service type to NodePort.