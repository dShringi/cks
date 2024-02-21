## 20% - Supply Chain Security
    Minimize base iamge footprint
    Whitelist allowed image registries, signa and validate images
    Use static analysis of user workloads
    Scan images for known vulnerabilities
    
---
## 1. Image Security
```
    - image: docker.io/library/nginx [registry/user or account/image or repository]
    - docker login private-registry.io
    - docker run private-registry.io/apps/internal-app
    - kubectl create secret docker-registry regcred \
      --docker-server=<> --docker-username=<> \
      --docker-password=<> --docker-email=<>
    - spec:
        imagePullSecrets:
        - name: regcred
```
## 2. Whitelist allowed image registries
```
    - Admission Controllers
    - OPA service
    - ImagePolicyWebhook
    - apiVersion: apiserver.cofig.k8s.io/v1
      kind: AdmissionConfiguration
      plugins:
      - name: ImagePolicyWebhook
        configuration:
          imagePolicy:
            kubeConfigFile: <path-to-kubeconfig-file>
            allowTTL: 50
            denyTTL: 50
            retryBackoff: 500
            defaultAllow: false
    - --enable-admission-plugins=ImagePolicyWebhook [kube-apiserver.yaml]
      --admission-control-config-file=/etc/kubernetes/admission-config.yaml
```

## 3. Static Analysis
```
    - wget <github kubsec release gz>
      tar -xvf <gz>
      mv kubesec /usr/bin
    - kubesec scan pod.yaml
    - curl -sSX POST --data-binary @"pod.yaml" https://v2.kubesec.io/scan
    - kubesec http 8080 & [local kubesec server]
```

## 4. Scan images for known vulnerabilities
```
    - trivy image --format json nginx:1.18.0
    - trivy image --severity CRITICAL,HIGH nginx:1.18.0
    - trivy image --input archive.tar
    - crictl pull <image-name>
```