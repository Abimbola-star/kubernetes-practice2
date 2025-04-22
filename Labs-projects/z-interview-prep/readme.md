# Set 1
#### 1. what is kubernetes and why is it so popular?

Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It’s popular because of its features which are mainly:

- **Scalability:** Easily scales applications up or down.
- **High Availability:** Self-healing and fault tolerance.
- **Portability:** Runs on any cloud or on-premises.
- **Automation:** Manages load balancing, rolling updates, and self-recovery.

#### 2. what exactly is self healing in kubernetes?

Self-healing means Kubernetes automatically detects and replaces failed containers or pods to maintain the desired state of applications.

Example: When a node fails, Kubernetes replaces or reschedules the affected pods to maintain the desired state of the application

#### 3. do you think kubernetes is portable, if yes how?

Yes, Kubernetes is highly portable because:
- Cloud-Agnostic: Runs on AWS, GCP, Azure, or on-prem.
- CNCF Standards: Uses open APIs and YAML manifests, making deployments consistent across environments.
- Kubernetes Distributions: Options like EKS, AKS, GKE, or Rancher ensure flexibility.

#### 4. in kubernetes basics we have pods, why do we use them?

Pods are the smallest deployable units in Kubernetes and they contain one or more containers. With the pods specification, you define how to run the container(s).
They enable co-located and co-managed containers, facilitating efficient resource sharing and communication.

#### 5. how does kubernetes ensure the availability of an application?

Kubernetes ensures application availability through features like
- **Deployments and ReplicaSets**, which maintain the desired number of pod replicas running. If a pod fails, Kubernetes automatically replaces it.
- **Services** which help in load balancing
- **Health checks (Probes)** to ensure the traffic is not sent to unhealthy or failing pods
- **Node Auto-recovery:** Reschedules pods if a node fails.

#### 6. what's the difference between kubernetes deployment and kubernetes service?

- A Deployment manages the desired state of application pods, handling rolling updates, rollbacks and scaling.
- A Service, on the other hand, provides a stable network endpoint (IP/DNS name) to access a set of pods, enabling load balancing and service discovery.

#### 7. do you have any experience on creating a cluster? Explain, walk me thru one example. Was the cluster unmanaged or completely managed? Any idea about self hosted clusters

Yes, I've created clusters using both managed services like EKS/AKS/GKE and self-hosted solutions with kubeadm. 
- **Managed (EKS):**
  - Used eksctl to create a cluster with worker nodes.
  - Configured IAM roles, VPC, and node groups.

- **Self-Hosted (kubeadm):**
  - setup and initialized control plane with kubeadm init.
  - Joined worker nodes with kubeadm join.
  - Installed CNI (Calico/Flannel) and ingress controllers.

Self-hosted clusters offer more control but require manual maintenance.


#### 8. any idea on scaling kubernetes deployments?

Kubernetes supports both manual and automatic scaling. Manual scaling can be done using `kubectl scale`, while automatic scaling is achieved through the **Horizontal Pod Autoscaler**, which adjusts the number of pods based on metrics like CPU utilization, memory utilization and more. 

There is also a possibility of automatically scale a workload vertically using a **VerticalPodAutoscaler (VPA)** but this is not part of Kubernetes core

#### 9. what is k8s namespace and why is it useful?

Namespaces in Kubernetes provide a mechanism to isolate and organize resources within a cluster. They are particularly useful in multi-tenant environments, allowing different teams or projects to share the same cluster without interfering with each other's resources.

Example: With Namespaces, you can:
- ​Isolate environments: dev, staging, prod, etc
- Define resource Quotas: Limit CPU/memory per team
- Setup RBAC policies for access control per namespace

#### 10. how does kubernetes handle the persistent storage requirements of an application?

Kubernetes uses PersistentVolumes (PVs) and PersistentVolumeClaims (PVCs) to manage storage.
- PVs are storage resources provisioned by an administrator or dynamically using StorageClasses
- PVCs are requests for storage by users. The PVC is bound to a suitable PV  

This allows pods to use the storage by just defining the PersistentVolumeClaim in their specification.

#### 11. how and why do you use config maps?

ConfigMaps are used to decouple configuration from container images, making applications more portable. They store non-confidential data in key-value pairs and can be consumed by pods as environment variables, command-line arguments, or configuration files. They can also be mounted as volumes in pods.

#### 12. any idea about kubernetes service account?

Service accounts provide an identity for processes running in pods to interact with the Kubernetes API. They are essential for assigning permissions and are used in scenarios where pods need to authenticate to the API server or access other resources securely.

#### 13. as a DevOps you have surely come across some deployment strategies like rolling updates. How do you perform the rolling update in kubernetes deployment? Do you remember the command that can help you do that? Kubectl set or edit. How do you initiate rollback on a deployment?

Yes, Rolling update is a deployment strategy that updates the pods incrementally to minimize downtime. To perform a rolling update, you can use the `kubectl set` or the `kubectl edit` to edit the deployment configuration.

To rollback, use `kubectl rollout undo deployment/<deployment-name>`

#### 14. when do we use statefulsets, and how does it differ from deployment?

StatefulSets are for stateful apps (databases like MySQL, Kafka) needing:
- Stable and unique network IDs (pod-0, pod-1).
- Persistent storage (each pod gets its own PV).
- Ordered scaling (sequential pod creation).

Deployments are suited for stateless apps with random pod names.

#### 15. are you kubernetes certified or something?

Yes/No/ or working toward it

Example: Yes, I hold the Certified Kubernetes Administrator (CKA) certification, which validates my skills in managing Kubernetes clusters.

#### 16. as a k8s admin if I ask you to secure my cluster and I do not have any information about that, how will you do it. Just walk me thru one or two ways

Security in a kubernetes cluster can be done at different levels:
- RBAC: Limit user/service account permissions in the cluster
- Network policies: Restrict pod-to-pod traffic.
- Security context/Pod Security Policies (PSP)/Pod Security Admission: defines privileges and access control settings for a Pod or Container / Enforce least privilege.
- ETCD Encryption: Enable at-rest encryption.
- Audit Logging: Monitor API server actions.

**Example of personalized answer:**
- RBAC (Role-Based Access Control): I'd start by reviewing the roles and bindings in the cluster to ensure the principle of least privilege is applied. Only allow users and service accounts access to the resources they absolutely need.
- Network Policies: I’d implement network policies to restrict pod-to-pod communication. By default, Kubernetes allows all traffic; network policies can enforce segmentation between services, minimizing lateral movement if a pod is compromised.

Bonus: I’d also disable anonymous access to the API server and enable audit logging to track what’s happening in the cluster.

#### 18. any experience with monitoring pods in kubernetes cluster?

Yes, I’ve set up monitoring using Prometheus and Grafana. Prometheus scrapes metrics from the Kubernetes API, node exporters, and custom app endpoints, while Grafana visualizes them through dashboards.

I’ve also used tools like Kube-state-metrics and Lens IDE for real-time visibility. For alerting, we often integrate Prometheus Alertmanager or external services like PagerDuty.

#### 19. what's the current release of k8s?

The latest stable release of Kubernetes is v1.32, codenamed Penelope, which was released on December 11, 2024. 

But we currently use the version 1.xx in the company

# Set 2

#### 1. how do you package kubernetes applications?

We typically package Kubernetes applications using **Helm charts**, which allow us to templatize Kubernetes YAML files and manage releases easily. Helm supports values injection, dependency management, and versioning.

For more complex setups, I also use **Kustomize** when I need layered configuration without needing a separate package manager.

#### 2. what are init containers and what are they generally used for

Init containers are specialized containers that run before the main application containers in a pod. They’re used for setup tasks that need to be completed before the main container starts—like waiting for a service to be ready, setting permissions, or downloading necessary configuration files. They always run to completion before the main containers start.

#### 3. what are some kubernetes scheduling techniques you've used?

I've used several scheduling strategies, such as:

- Node selectors / node affinity: To control which nodes pods are scheduled on.

- Taints and tolerations: To prevent workloads from being scheduled on specific nodes unless explicitly tolerated.

- Pod affinity/anti-affinity: To ensure pods are placed together or apart from certain pods, often used in HA scenarios.

- Resource requests and limits: To help the scheduler decide where to place pods based on CPU/memory availability.


#### 4. how do you prepare a node for maintenance?

I drain the node using ``kubectl drain <node-name> --ignore-daemonsets --delete-emptydir-data``, which evicts all pods safely.

I make sure workloads are rescheduled elsewhere (especially if using anti-affinity or pod disruption budgets), and once maintenance is done, I uncordon it using ``kubectl uncordon <node-name>``.

#### 5. imagine you have two containers running in the same pod. One for nginx and another for WordPress. How do you access these containers in the browser using IP address?

Since both containers share the same network namespace, they share the same IP and port space. So, you'd access either service using the pod’s IP and the exposed port of the container. To distinguish between them, you'd typically configure one container to use a different port (e.g., Nginx on 80, WordPress on 8080) and then access via ``http://<pod-ip>:80 or :8080``.

#### 6. If I have many containers running inside a pod and I want a specific container to start before starting another one, how can I do? 

To enforce startup order, use init containers. These run sequentially and must finish successfully before the main containers start.

#### 7. what is the impact of upgrading kubelet if we leave the pods running on the worker node? Will it break running pods? Why?

No, if done correctly but there is a risk that should be considered while working on live workloads

Kubelet restarts during upgrade, but containers are managed by the container runtime (e.g. docker, containerd). The runtime keeps containers alive unless explicitly stopped.

However, it’s a best practice to drain the node first, because:

- In-place upgrades may introduce version mismatches.
- If something goes wrong, it might break connectivity to the control plane or disrupt pod health monitoring. So while it might not break running pods instantly, it's risky and should be avoided on live workloads.

#### 8. how are services mapped to pods in kubernetes?

Services map to pods using labels and selectors. The service has a selector that matches the labels on pods. Internally, Kubernetes uses Endpoints to track which pods are backing the service, and kube-proxy helps route the traffic to the correct pod IP and port. This is how services provide stable networking over dynamic pod lifecycles.

### Set 3

#### 1. What size of cluster are you using for your environments?

It depends on the environment. For production, I’ve worked with clusters ranging from 10 to 50 nodes (10+ nodes across multiple AZs, 8-16 vCPUs, 32GB+ RAM per node), typically autoscaled based on resource usage. In dev and staging, clusters are much smaller—maybe 3 to 5 nodes (3-5 worker nodes, 4-8 vCPUs, 16GB RAM per node)—to keep costs down while still mirroring the architecture.

#### 2. How did you manage the databases for your applications running in the cluster?

In most cases, we run databases outside the cluster using managed services like RDS, Cloud SQL, or Azure Database for better reliability and easier backups. When we do run databases inside the cluster (for dev or testing), we use StatefulSets, persistent volumes, and regularly scheduled backups with tools like Velero or custom CronJobs.

#### 3. There are many way to setup ingress in a cluster, what way did you use?
   
- NGINX Ingress: Most common (open-source or K8s community version).
- AWS ALB Ingress Controller: For AWS-native load balancing.
- Traefik: Lightweight, good for microservices.
- Istio Gateway: When using service mesh.

Example of personalized answer:

I typically use **Ingress-NGINX Controller** for its flexibility and community support. In cloud environments, I’ve also used cloud-native ingress (like GKE Ingress or AWS ALB Ingress Controller) for tighter integration with cloud services. TLS termination is handled via cert-manager and Let's Encrypt.

#### 4. How do you troubleshoot a crashing container

I do a step by step troubleshooting
- Describe the pod to check the events
- Checking the pod/container logs
- verify the liveness/readiness probes
- check the resource limits

#### 5. Did you ever had issues with pods stopping?

Yes, especially with resource exhaustion or misconfigured liveness probes. 

One example: A pod kept restarting due to insufficient memory. We increased the resource limits and optimized the app memory usage. In another case, the container image had a bug — rebuilt the image with a fix.

#### 6. How do you manage scaling in kubernetes

There are various scaling capabilities in kubernetes:
- Horizontal Pod Autoscaler (HPA):
- Vertical Scaling: Adjust CPU/memory requests/limits.
- Cluster Autoscaler: Adds nodes when pods can’t schedule.
- KEDA: Event-driven scaling (e.g., Kafka queue depth).

Example of answer: 

For horizontal scaling, we use the Horizontal Pod Autoscaler based on CPU/memory metrics or custom metrics via Prometheus. For vertical scaling, we monitor with metrics and adjust resource limits. We also use Cluster Autoscaler to scale nodes automatically in cloud environments.

#### 7. How was security set up in your k8s clusters?

- Enabled RBAC for access control.
- Disabled anonymous API access.
- Enforced Pod Security Standards and network policies.
- Used cert-manager for certificate management.
- Secured etcd with TLS.
- Applied image scanning with tools like Trivy or Clair.
- Secrets Management: Vault or K8s Secrets (encrypted at rest).

#### 8. What are some best practices you have used in securing a Kubernetes cluster?

- Use namespaces for isolation.
- Update Regularly: Patch K8s, nodes, and dependencies.
- Apply least privilege through RBAC.
- Enforce network segmentation with NetworkPolicies.
- Restrict use of privileged containers.
- Audit logging and monitoring: Track API server activity.
- Limit Dashboard Access: Avoid exposing kube-dashboard publicly.

#### 9. What was the hardest production issue you faced when using kubernetes?

Example 1:

One of the toughest was a memory leak in a Go service. It didn't crash but slowly ate memory over days. The pod never restarted because the limits were too high. We added better observability (Prometheus/Grafana), set tighter resource limits, and fixed the leak after heap profiling. Lessons learned: always monitor long-running pods closely.


Example 2:

Issue: A StatefulSet (MongoDB) kept crashing during node failures due to:
- PVs stuck in "Terminating" when nodes died.
- Manual intervention needed to force-delete PVs.

Solution:

- Automated pre-stop hooks to gracefully shutdown DBs.
- Used local PVs with node affinity for critical stateful apps.
- Documented recovery playbook for PV issues.


#### 10. What is the function of the `kubectl explain pod --recursive` command?

It shows the entire schema of the Pod object in a nested way. Super useful for understanding all possible fields, especially when writing manifests from scratch or troubleshooting API compatibility.

#### 11. How do you configure certificates in your cluster? Example SSL certificates

We use cert-manager to automate the provisioning and renewal of SSL/TLS certificates from Let’s Encrypt. For internal services, we may issue self-signed certs or use a private CA, applying them via secrets and referencing them in ingress definitions.

#### 12.What is the function of the command `kubectl api-resources`?

It lists all available Kubernetes API resource types in the current cluster, including their short names, whether they are namespaced, and their API groups. It's super helpful when writing manifests or scripting.

#### 13. When should you use daemonsets instead of deployments? specify 2 use cases of daemonsets

DaemonSets ensure that a pod runs on every node (or selected nodes).

Use cases:

- Log collection agents like Fluentd or Filebeat.
- Monitoring agents like Prometheus Node Exporter.
- Network Plugins: Calico/Cilium pods on every node.

#### 14. How do you update a ConfigMap, and what happens to the Pods using it?

You can update it using kubectl edit configmap <name> or reapply it with kubectl apply -f config.yaml.
By default, pods don’t reload updated ConfigMaps unless they’re restarted. For automatic reloading, you can use sidecar reloader patterns or solutions like Reloader or external signal handlers.

#### 15. What are the problems you recently faced while maintaining your kubernetes cluster and how did you resolve them?

Example of answer:

Recently, we hit an issue where Ingress rules stopped applying correctly after a controller upgrade. Root cause was a CRD version mismatch. We rolled back, tested in staging, then re-applied the upgrade with proper CRD migration.
Also had a Cluster Autoscaler bug in EKS where it wasn't releasing nodes—turned out to be a misconfigured annotation on one workload preventing scale-down.
