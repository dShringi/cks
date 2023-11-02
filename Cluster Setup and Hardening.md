Curriculum

## 10% - Cluster Setup
    Network Security Policies - Easy
    CIS benchmark for Security Configuration - Hard
    Security Control in Ingress Objects - Intermediate
    Node Metadata and Endpoints - Easy
    Minimize use of GUI - Easy
    Binaries verification - Easy
## 15% - Cluster Hardening
    Restrict access to Kubernetes API
    RBAC to minimize exposure
    disable default SA, minimize permissions of new SA
    Update Kubernetes frequently

---
KodeKloud

1. CIS benchmark
2. Kube bench
3. Kubernetes Security Primitives
4. Authentication
5. Service Accounts
6. TLS Intro
7. TLS Basics
8. TLS in Kubernetes
9. TLS in Kubernetes - Certificate Creation
10. View Certificate Details
11. Certificates API
12. KubeConfig
13. API Groups
14. Authorization
15. RBAC
16. CR & CRB
17. Kubelet Security
18. Kubelet Proxy & Port Forward
19. Kubernetes Dashboard
20. Securing Kubernetes Dashboard
21. Verify Platform Binaries
22. Kubernetes Software Versions
23. Cluster Upgrade Process
24. Network Policy
25. Ingress
26. Ingress- Annotations & rewrite-target
27. Docker Service Configuration
28. Docker - Securing the Daemon
29. Ciphers and the Kubernetes Control Plane
---
21. Verify Platform Binaries
```
    - shasum -a 512 kubernetes.tar.gz [Mac]
    - sha512sum kubernetes.tar.gz [Linux]
```
---
22. Kubernetes Software Versions
```
    - https://github.com/kubernetes/sig-release/blob/master/release-engineering/versioning.md
    - https://kubernetes.io/releases/version-skew-policy/
```
---
23. Cluster Upgrade Process
```
    - https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
    - Upgrade kubeadm
    - Upgrade controlplane nodes
    - Upgrade worker nodes
```
---
24. Network Policy
```
    - Easy, ignore for now
```
---
25. Ingress
```
    - kubectl create ingress NAME --rule=host/path=service:port[,tls[=secret]]
    - https://kubernetes.io/docs/concepts/services-networking/ingress/#the-ingress-resource
    - the above link is must read
```
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx-example
  rules:
  - http:
      paths:
      - path: /testpath
        pathType: Prefix
        backend:
          service:
            name: test
            port:
              number: 80
---
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-resource-backend
spec:
  defaultBackend:
    resource:
      apiGroup: k8s.example.com
      kind: StorageBucket
      name: static-assets
  rules:
    - http:
        paths:
          - path: /icons
            pathType: ImplementationSpecific
            backend:
              resource:
                apiGroup: k8s.example.com
                kind: StorageBucket
                name: icon-assets

```
---
26. Ingress - Annotations & rewrite-target
```
apiVersion: networking.k8s.io/v1 
kind: Ingress
metadata:
  name: test-ingress
  namespace: critical-space
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /pay
        pathType: Prefix
        backend:
          service:
            name: pay-service
            port: 
              number: 8282
```
```
    - The rewrite-target will remove the `/pay` from the incoming request and map it to /
```
---
27. Docker Service Configuration
```
    - default location of the configuration file is /etc/docker/daemon.json
    - --config-file flag can be used to specify a non-default location
    - example file: https://docs.docker.com/engine/reference/commandline/dockerd/#on-linux
```
---
28. Docker - Securing the Daemon: If you don't want to have complete two-way authentication, you can run Docker in various other modes by mixing the flags.
```
    dockerd \
    --tlsverify \
    --tlscacert=ca.pem \
    --tlscert=server-cert.pem \
    --tlskey=server-key.pem \
    -H=0.0.0.0:2376
```
    - Daemon modes
      * tlsverify, tlscacert, tlscert, tlskey set: Authenticate clients
      * tls, tlscert, tlskey: Do not authenticate clients
    - Client modes
      * tls: Authenticate server based on public/default CA pool
      * tlsverify, tlscacert: Authenticate server based on given CA
      * tls, tlscert, tlskey: Authenticate with client certificate, do not authenticate server based on given CA
      * tlsverify, tlscacert, tlscert, tlskey: Authenticate with client certificate and authenticate server based on given CA
    - https://docs.docker.com/engine/security/protect-access/#other-modes
---

29. Ciphers and the Kubernetes Control Plane
```
    - Control plane components has two options arguments
      * --tls-min-version: sets the minimum version of TLS
      * --tls-cipher-suites: sets a comma-separated list of cipher suites
    - etcd cli arg to set cipher suites
      * --cipher-suites: sets a comma-separated list of cipher suites
```
---

