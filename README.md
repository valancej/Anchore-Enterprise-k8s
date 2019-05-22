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

To make proper configurations to the Helm chart, create a custom `anchore_values.yaml` file and utilize it when installing. There are many options for configuration with Anchore, for the purposes of this document, I will only change the minimum to get Anchore Enterprise installed. There is an [anchore_values.yaml](anchore_values.yaml) file in this repository, that you may include in your installation. 