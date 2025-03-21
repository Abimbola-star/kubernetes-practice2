# Cluster setup
## Killercoda kubernetes playgrounds
Most of the practice in this repository can be done using kubernetes playgrounds. A precision will be made on labs that should not be performed on Killercoda playgrounds.

### How to use Killercoda kubernetes playgrounds

**Note:** A recommended prerequisite to using Killercoda is to have a Github account. If you are at this level of the class, you should have an account.
1- In your browser, go to the Killercoda official website at [https://killercoda.com/]().
2- Click on Playgrounds.
3- Select the latest Kubernetes playground (Kubernetes 1.32 at the moment of writing this documentation).
4- Sign in using your GitHub account. (You can also sign in using google or your email).
5- Killercoda will authenticate with your GitHub account. Validate if required.
6- Your environment is ready. You can run the command `kubectl get nodes` in the terminal displayed and start practicing.

## Create EKS cluster via eksctl
This is the production ready cluster we will use throughout the course.

### Prerequisites
Before proceeding, ensure that you have the following prerequisites:

- **AWS Account:** You need to have an AWS account with the appropriate permissions to create and manage EKS clusters.

- **IAM Permissions:** Ensure that your IAM user has sufficient permissions to create an EKS cluster, EC2 instances, and other resources required by EKS. An administrator account works well!

- **AWS CLI:** The AWS Command Line Interface (CLI) must be installed and configured ()`aws configure`) with your AWS credentials.

### Install kubectl

**kubectl:** is the Kubernetes command-line tool used to interact with the cluster. You can install it following [the instructions here](https://kubernetes.io/docs/tasks/tools/#kubectl).
**Note:** You can use [choco on windows](https://kubernetes.io/docs/tasks/tools/install-kubectl-windows/#install-nonstandard-package-tools) or [brew on Mac](https://kubernetes.io/docs/tasks/tools/install-kubectl-macos/#install-with-homebrew-on-macos)

After successful installation, run the following command to verify the version installed:
```bash
kubectl version --client
```

### Install eksctl

**eksctl** is a simple CLI tool for creating and managing clusters on EKS.
#### Install eksctl on Windows
Open Powershell as administrator and install eksctl using choco with the command:
```bash
choco install eksctl
```

#### Install eksctl on MAC
You can use Homebrew to install eksctl with the command:
 ```bash
brew tap weaveworks/tap
brew install weaveworks/tap/eksctl
```

Refer to the following documentation to install eksctl on your system if the above commands does not work for you: [Link here](https://eksctl.io/installation/)

### Create the cluster in EKS
Use the following command to create a cluster:
```bash
eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --node-type t3.small --nodes 2 --nodes-min 1 --nodes-max 2
```
**Note:** This command may complete in 10 to 15 minutes depending on your computer and network connection. Be patient and wait till the end of the process

### Update the Kubeconfig

After creating the cluster, you need to update the context in the local Kubeconfig file in order to interact with the cluster.

```bash
aws eks --region us-east-1 update-kubeconfig --name my-cluster
```
Verify that you can access the cluster. You should see nodes READY.
```bash
kubectl get nodes
```

### Basic commands
| **Command**                                      | **Description**                                                                 |
|--------------------------------------------------|---------------------------------------------------------------------------------|
| `kubectl cluster-info dump`                      | Get details about the cluster (API server, components, etc.).                   |
| `kubectl config view`                            | view the Kubeconfig file                   |
| `kubectl get nodes`                              | List all nodes in the Kubernetes cluster.                                       |
| `kubectl get nodes -o wide`                      | Show more details on node                                  |
| `kubectl get pods`                               | List all pods in the default namespace.                                        |
| `kubectl get pods -o wide`                       | Show more details (e.g., pod IP, node, etc.).                                   |
| `kubectl get deployments`                        | List all deployments in the default namespace.                                  |
| `kubectl get services`                           | List all services in the defaut namespace                                            |
| `kubectl proxy`                                  | Access the Kubernetes cluster dashboard (if set up).                            |

### Delete the EKS Cluster

When done with practice, always delete resources to avoid further charges in the cloud. Run this command only when you are done practicing 
```bash
eksctl delete cluster --name my-cluster --region us-east-1
```
