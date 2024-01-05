## 20% - Monitoring Logging and Runtime Security
    Perform behavioural analytics of syscall process and file activities at the host and container level to detect malicious activities
    Detect threats within physical infra, apps, networks, data, users and workloads
    Detect all phases of attack regardless where it occures and how it spreads
    Perform deep analytical investigation and identification of bad actors within environment
    Ensure immutability of containers at runtime
    Use Audit Logs to monitor access
---
## 1. Falco
```
    - systemctl status falco
    - journalctl -fu falco
    - rules.yaml
      - rule: <Name of Rule>
        desc: <Desc of Rule>
        condition: container and proc.name in (linux_shells) <When to filter events matching the rule>
        ouput: <Output to be generated for event>
        priority: <Severity of the event>
      - list: linux_shells
        items: [bash, zsh, ksh, sh, csh]
      - macro: container
        condition: container.id != host
    - /etc/falco/falco.yaml
      rules_file:
      - /etc/falco/falco_rules.yaml
      - /etc/falco/falco_rules.local.yaml
      - /etc/falco/rules.d
      json_output: false
    - last file overrides the previous file rule
    - [Hot reload]
      cat /var/run/falco.pid
      kill -1 $(cat /var/run/falco.pid)
    - https://falco.org/docs/getting-started/installation/
      https://github.com/falcosecurity/charts/tree/master/falco
      https://falco.org/docs/rules/supported-fields/
      https://falco.org/docs/rules/default-macros/
      https://falco.org/docs/configuration/
```
## 2. Immutable Infrastructure
```
    - containers:
      - securityContext:
            readOnlyRootFilesystem: true
            privileged: false
        volumeMounts:
        - name: cache-volume
            mountPath: /var/cache/nginx
        volumes:
        - name: cache-volume
        emptyDir: {}
```
## 3. Use Audit Logs
```
    - audit-policy.yaml
      apiVersion: audit.k8s.io/v1
      kind: Policy
      omitStages: ["RequestReceived"]
      rules:
      - namespaces: ["prod-namespace"]
        verbs: ["delete"]
        resources:
        - groups: ""
          resources: ["pods"]
          resourceNames: ["webapp-pod"]
        level: None / Metadata / Request / RequestResponse
      - level: Metadata
        resources:
          - group: ""
            resources: ["secrets"]
    - --audit-log-path=/var/log/audit.log
      --audit-policy-file=/etc/kubernetes/audit-policy.yaml
      --audit-log-maxage=10
      --audit-log-maxbackup=5
      --audit-log-maxsize=100
    - name: audit
      hostPath:
        path: /etc/kubernetes/prod-audit.yaml
        type: File
    - name: audit-log
        hostPath:
        path: /var/log/prod-secrets.log
        type: FileOrCreate
    - volumeMounts:
      - mountPath: /etc/kubernetes/prod-audit.yaml
        name: audit
        readOnly: true
      - mountPath: /var/log/prod-secrets.log
        name: audit-log
        readOnly: false
```