## 20% - Minimize Microservice Vulnerabilities
    Setup OS level security domains
    Manage Kubernetes secrets
    Use container runtime sandboxes in multi-tenant envt. (gvisor, kata containers)
    Implement pod to pod encryption using mTLS
---
## 1. Admission Controllers
```
    - kube-apiserver -h | grep enable-admission-plugins [enabled admission controllers]
    - --enable-admission-plugins=NodeRestriction,NamespaceAutoProvision
    - --disable-admission-plugins=DefaultStorageClass
```
## 2. Validating & Mutating Admission Controllers
```
    - Mutating followed by Validating always e.g. NamespaceAutoProvision and Namespace Exists
    - apiVersion: admission.k8s.io/v1
      kind: AdmissionReview
      response: {
        allowed: true
      }
    - Deploy Webhook Server [custom admission controller]
```
## 3. Open Policy Agent
```
    - curl -L -o opa <github-path>/opa_linux_amd64
    - chmod 755 ./opa
    - ./opa run -s
    - curl -X PUT --data-binary @example.rego http://localhost:8181/v1/policies/example1 [put the policy in opa]
    - curl http://localhost:8181/v1/policies [view policies]
    - apiVersion: admissionregistration.k8s.io/v1beta1
      kind: ValidatingWebhookConfiguration
      metadata:
        name: opa-validating-webhook
      webhooks:
        - name: validating-webhook.openpolicyagent.org
          rules:
          - operations: ["CREATE", "UPDATE"]
            apiGroups: ["*"]
            apiVersions: ["*"]
            resources: ["*"]
          clientConfig:
            caBunde: $(cat ca.crt | base64 | tr -d '\n')
            service:
              namespace: opa
              name: opa
```
## 4. Kubernetes Secrets
```
    - env:
      - name: DB_Password
        valueFrom: 
          secretKeyRef:
            name: app-secret
            key: DB_Password
    - envFrom:
      - secretRef:
          name: app-config
    - volumes:
      name: app-secret-volume
      secret:
        secretName: app-secret
    - ls /opt/app-secret-volumes -> DB_HOST DB_Password DB_User
    - cat /opt/app-secret-volumes/DB_Password -> password
```
## 5. Encrypting Secret Data at Rest
```
    - ps -aux | grep kube-api | grep "encryption-provider-config" [check if encryption is enabled]
    - apiVersion: apiserver.config.k8s.io/v1
      kind: EncryptionConfiguration
      resources:
        - resources:
            - secrets
          providers:
            - aescbc:
                keys:
                  - name: key1
                    secret: <BASE 64 ENCODED SECRET>
            - identity: {} # this fallback allows reading unencrypted secrets; # for example, during initial migration
    - Encryption key: head -c 32 /dev/urandom | base64
    - mv enc.yaml /etc/kubernetes/enc
    - --encryption-provider-config=<path-to-enc.yaml>
    - add volume and volume-mounts https://kubernetes.io/docs/tasks/administer-cluster/encrypt-data/#use-the-new-encryption-configuration-file
    - k get secrets --all-namespaces -o json | k replace -f - [ensure all secrets are encrypted]
```
## 6. Container Sandboxing [gVisor / Kata Containers / Container Runtime]
```
    - seccomp / apparmor
    - provides layer of isolation between container and kernel
    - Sentry + Gofer
    - Kata: nested virtualization
    - runC runs the container [default container runtime]
    - docker run --runtime kata -d nginx [kata]
    - docker run --runtime runsc -d nginx [gVisor]
    - gvisor.yaml
      apiVersion: node.k8s.io/v1
      kind: RuntimeClass
      metadata:
        name: gvisor
      handler: runsc
    - nginx.yaml
      apiVersion: v1
      kind: Pod
      metadata:
        name: nginx
      spec: 
        runtimeClassName: gvisor
        container:
        - image: nginx
```
## 7. Implement pod to pod encyrption by use of mTLS
```
    - Istio and Linkerd
```