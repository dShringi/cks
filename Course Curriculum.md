Course Curriculum

## 10% - Cluster Setup
    Network Security Policies
    CIS benchmark for Security Configuration
    Security Control in Ingress Objects
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
---
