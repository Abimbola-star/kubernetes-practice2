# Setting Up RBAC for a New User in Amazon EKS using serviceaccount 

**Important Note**: This lab should be done in the EKS cluster launched with eksctl.

## **Overview**

We have a new user called Paul in dev team and we need to give him access to the cluster resources. As an administrator, setup RBAC using a serviceaccount to allow Adam to list, create and delete pods and services in the dev namespace. 

Steps:
- Create IAM user for Paul with policy for EKS access and access credentials to configure AWS CLI,
- Create Role, Rolebinding, Service account, Secret(to set up the token for the service account),
- Create the Kubeconfig file for Paul,
- Verify the permissions.
- 

---

## **Step 1: Create an IAM User and Get Their ARN**

Here, we must create a user called **paul** in the group **dev-group** then attach the **eks:DescribeCluster** policy to the user to allow authentication to EKS. Use the terraform code in the `iam-user-terraform` folder.

## **Step 2: Create Role, Rolebinding, Service account, Secret(to set up the token for the service account),**
Here, we will use a single manifest for the various objects just for practice. Here is the list of the objects defined:
- The namespace `dev-group`
- The service account `dev-sa` used to identify the subject that will be used in the role
- The secret `dev-secret` to store the token that will be used by the service account for authentication
- The role `create-list-pods-services` used to define the permissions (create and list pods and services)
- The rolebinding `create-list-pods-services-rb` used to bind the role to the serviceaccount.

Apply the manifest to the cluster and verify the objects created:
```bash
kubectl apply -f rbac-sa.yaml
kubectl get secret,serviceaccount,role,rolebinding -n dev-ns
```
Extract the token that will be used in the next step:

```bash
kubectl get secret dev-secret -n dev-ns -o jsonpath='{.data.token}' | base64 --decode
```
---

## **Step 3: Create the Kubeconfig file for Paul**
The file `config-template.yaml`in this folder represent the template used to generate the Kubeconfig file for Paul.
To fill in this template, we need crucial information:
- The cluster name
- The API Server URL
- The cluster certificate data
- The service account token and more

Create a kubeconfig file for Paul from the template. Create a file called `paul-kubeconfig.yaml`, copy the content of the `config-template.yaml` file and paste in it.
Now, we need to replace the placeholders with the correct values.

### Get API server URL

Use the following command:
```bash
aws eks describe-cluster --name <your-cluster-name> --region <your-region> --query "cluster.endpoint" --output text
```
Example:
```bash
aws eks describe-cluster --name my-cluster --region us-east-1 --query "cluster.endpoint" --output text
```
Copy the value you get and replace the corresponding placeholder in the config template file
Alternatively, you could use the following command to get the API server URL:
```bash
kubectl cluster-info | grep 'Kubernetes control plane' | awk '{print $NF}'
```

### Get cluster CA certificate
Use the command:
```bash
aws eks describe-cluster --name <your-cluster-name> --query "cluster.certificateAuthority.data" --output text
```
Example:
```bash
aws eks describe-cluster --name my-cluster --region us-east-1 --query "cluster.certificateAuthority.data" --output text
```
Copy the value you get and replace the corresponding placeholder in the config template file

Alternatively, you could use the following command to get the CA certificate

```bash
kubectl get secret dev-secret -n dev-ns -o jsonpath='{.data.ca\.crt}'
```

### Get the token for Paul
Use the command:
```bash
kubectl get secret dev-secret -n dev-ns -o jsonpath='{.data.token}' | base64 --decode
```
Copy the value you get and replace the corresponding placeholder in the config template file

### Fill in the contexts section

The contexts section should look like the following for this example

```yaml
contexts:
- name: paul-context
  context:
    cluster: my-cluster
    user: paul
    namespace: dev-ns
current-context: paul-context
```

The final config file (`paul-kubeconfig.yaml`) should be sent to Paul via a secure method depending on the company policies. (e.g., encrypted email, private Slack message, secure file transfer, password -protected ZIP etc.)

## **Step 4: Verify the permissions**

**Notes:** Paul should have `kubectl` installed on his computer as a prerequisite.

Paul should save the files on his local environment. Configure AWS CLI with the credentials then set the `KUBECONFIG` environment variable to use the right config file
**Note:** You can move to the directory where you stored `paul-kubeconfig.yaml` file or simply specify the path to that file in the following command:

```bash
export KUBECONFIG=paul-kubeconfig.yaml
## or export KUBECONFIG=/path/to/the/config/file
```
Verify access
```sh
kubectl auth can-i create pods -n dev-ns
kubectl auth can-i list pods -n dev-ns
```

If set up correctly, the user should be able to **create and list pods** in the `dev-ns` namespace but not perform any other actions.

---

## **Step 5: Verify access (Paul side)
Create a context for user Paul just for testing.

```bash
kubectl config set-credentials paul-context --token=$(kubectl get secret dev-secret-sa -n dev-ns -o jsonpath='{.data.token}' | base64 --decode)
kubectl config set-context paul-context --cluster my-cluster --user paul --namespace dev-ns
```
Switch to Paul context

```bash
kubectl config use-context paul-context
```


On Paul's side, he must install `kubectl` to be able to interact with kubernetes clusters.
1. verify kubectl installation
Verify
```bash
kubectl version --client
```
2. Update kubeconfig for Cluster Access
Paul should save the kubeconfig file on his local environment then set the `KUBECONFIG` environment variable to use the right config file
**Note:** You can move to the directory where you stored `paul-kubeconfig.yaml` file or simply specify the path to that file in the following command:

```bash
export KUBECONFIG=paul-kubeconfig.yaml
## or export KUBECONFIG=/path/to/the/config/file
```
verify access
```bash
kubectl get pods
kubectl get pods -n dev-ns
kubectl auth can-i create pods -n dev-ns
kubectl auth can-i delete pods -n dev-ns
kubectl auth can-i create deployment -n dev-ns
kubectl auth can-i delete service -n dev-ns
```
It should return "yes" for the objects he can create or "no" for the ones he cannot create.

If Paul tries to create a deployment or list nodes he will get a Forbidden error.

# Clean Up

At the end of the practice, always delete resources created.

```bash
aws iam get-user --user-name paul
```