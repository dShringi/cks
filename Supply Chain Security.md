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
            defaultAllow: true
    - --enable-admission-plugins=ImagePolicyWebhook [kube-apiserver.yaml]
      --admission-control-config-file=/etc/kubernetes/admission-config.yaml
```

## 3. Static Analysis
```
    - kubesec scan pod.yaml
    - curl -sSX POST --data-binary @"pod.yaml" https://v2.kubesec.io/scan
    - kubesec http 8080 & [local kubesec server]
```

## 4. Scan images for known vulnerabilities
```
    - trivy image nginx:1.18.0
    - trivy image --severity CRITICAL,HIGH nginx:1.18.0
    - trivy image --input archive.tar
```