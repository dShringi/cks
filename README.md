# [Certified Kubernetes Security Specialist](https://github.com/cncf/curriculum/blob/master/CKS_Curriculum_%20v1.29.pdf)

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
Admission Controllers -- https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/  
automountServiceAccountToken -- https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/  
Ingress -- https://kubernetes.io/docs/concepts/services-networking/ingress/  

## Kubernetes Components

### Control Plane Components

kube-apiserver  
kube-controller-manager  
kube-scheduler  
etcd  

### Node Components

kubelet  
kube-proxy 

## vi Shortcuts

Go to start of line - ^  
Go to end of line - $

Mark lines: Esc+V (then arrow keys)
Copy marked lines: y
Cut marked lines: d
Past lines: p or P

To indent multiple lines press Esc and type :set shiftwidth=2. First mark multiple lines using Shift v and the up/down keys. Then to indent the marked lines press > or <. You can then press . to repeat the action.

## Tips & Tricks

    export do="--dry-run=client -o yaml"
    k run pod1 --image=nginx $do

    export now="--force --grace-period 0"
    k delete pod1 $now

update the above in ~/.bashrc

    alias kn='kubectl config set-context --current --namespace'
    kn default        # set default to default
    kn my-namespace   # set default to my-namespace

Kubectl important commands
    
    k get po -o=custom-columns=:.metadata.name,.spec.containers[*].image --no-headers
    k get po -ojsonpath='{range .items[]}{.metadata.name},{.spec.containers[].image}{"\n"}{end}'

    for image in $(k get po -ocustom-columns=":.spec.containers[*].image"); do trivy image ${image} --severity CRITICAL -q | grep Total; done
    
    images=("rancher/alpine-git:1.0.4" "nginx:1.19" "nginx:1.17" "nginx:1.20" "gcr.io/google-containers/nginx" "bitnami/jenkins:2.414.3")
    for image in ${images[@]}; do trivy image ${image} --severity CRITICAL -q | grep Total; done

    k config view -o jsonpath="{.contexts[*].name}" | tr " " "\n" # new lines

    grep -i "something" -A3 -B3
    journalctl -u falco -S -30s

Crictl

    crictl ps -id 7a5ea6a080d1 [Get pod id from container id, used in falco where journalctl logs gives container id]
    CONTAINER ID        IMAGE              NAME        ...         POD ID
    7a5ea6a080d1b       6f715d38cfe0e      nginx       ...         7a864406b9794

    crictl pods -id 7a864406b9794
    POD ID              ...          NAME                             NAMESPACE        ...
    7a864406b9794       ...          webapi-6cfddcd6f4-ftxg4          team-blue        ...

    crictl inspect <container-id> | grep args -A1
        "args": [
          "./collector1-process"

    ➜ root@cluster1-node1:~# ps aux | grep collector1-process
    root       35039  0.0  0.1 702208  1044 ?        Ssl  13:37   0:00 ./collector1-process
    root       35059  0.0  0.1 702208  1044 ?        Ssl  13:37   0:00 ./collector1-process

    ➜ root@cluster1-node1:~# strace -p 35039
    strace: Process 35039 attached
    futex(0x4d7e68, FUTEX_WAIT_PRIVATE, 0, NULL) = 0
    kill(666, SIGTERM)                      = -1 ESRCH (No such process)