# Setup RBAC for a group of users in EKS
**Important Note**: This lab should be done in the EKS cluster launched with eksctl.

## **Overview**

We have a new user called Adam in dev team and we need to give him access to the cluster resources. 

As an administrator, setup RBAC to allow the users in the dev-group (where Adam will be added) to list, create and delete pods in the dev namespace. 
This guide explains how to configure **Role-Based Access Control (RBAC)** in an **Amazon EKS** cluster to allow users in a group (dev-group) to **only create, delete and list pods** in the `dev` namespace.

---

## Step 1: Create the IAM Group
## Step 2: Create a IAM Role
## Step 3: Set policies to allow users in the Group to assume the Role
## Step 4: Update the aws-auth configmap
## Step 5: Create the namespace, the role and the rolebinding
## Step 6:
