# Certified Kubernetes Security Specialist

## Must go through docs

## 10% - Cluster Setup
    [Network Security Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
    CIS benchmark for Security Configuration
    [Security Control in Ingress Objects](https://kubernetes.io/docs/concepts/services-networking/ingress/)
    Node Metadata and Endpoints
    Minimize use of GUI
    Binaries verification
## 15% - Cluster Hardening
    Restrict access to Kubernetes API
    RBAC to minimize exposure
    disable default SA, minimize permissions of new SA
    Update Kubernetes frequently
## 15% - System Hardening
    Minimize host OS footprint
    Minimize IAM roles
    Minimize external access to network
    Appropriately use kernel hardening tools like AppArmor, seccomp
## 20% - Minimize Microservice Vulnerabilities
    Setup OS level security domains
    Manage Kubernetes secrets
    Use container runtime sandboxes in multi-tenant envt. (gvisor, kata containers)
    Implement pod ot pod encryption using mTLS
## 20% - Supply Chain Security
    Minimize base iamge footprint
    Whitelist allowed image registries, signa and validate images
    Use static analysis of user workloads
    Scan images for known vulnerabilities
## 20% - Monitoring Logging and Runtime Security
    Perform behavioural analytics of syscall process and file activities at the host and container level to detect malicious activities
    Detect threats within physical infra, apps, networks, data, users and workloads
    Detect all phases of attack regardless where it occures and how it spreads
    Perform deep analytical investigation and identification of bad actors within environment
    Ensure immutability of containers at runtime
    Use Audit Logs to monitor access

## Bookmarks

kubectl Cheat Sheet -- https://kubernetes.io/docs/reference/kubectl/cheatsheet/
Kubectl Commands -- https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands
Network Policies -- https://kubernetes.io/docs/concepts/services-networking/network-policies/
Security Context -- https://kubernetes.io/docs/tasks/configure-pod-container/security-context/
Secrets -- https://kubernetes.io/docs/concepts/configuration/secret/
RBAC -- https://kubernetes.io/docs/reference/access-authn-authz/rbac/
Seccomp -- https://kubernetes.io/docs/tutorials/clusters/seccomp/
Apparmor -- https://kubernetes.io/docs/tutorials/clusters/apparmor/
ImageWebhookPolicy -- https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
Kubelet Config -- https://kubernetes.io/docs/reference/config-api/kubelet-config.v1beta1/
RuntimeClaas -- https://kubernetes.io/docs/concepts/containers/runtime-class/
Admission Controllers -- https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/#podsecuritypolicy
automountServiceAccountToken -- https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/
Pod Volumes -- https://kubernetes.io/docs/concepts/storage/volumes/
PV PVC -- https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/
Ingress -- https://kubernetes.io/docs/concepts/services-networking/ingress/
