## 15% - System Hardening
    Minimize host OS footprint
    Minimize IAM roles
    Minimize external access to network
    Appropriately use kernel hardening tools like AppArmor, seccomp
    
---
1. Limit Node Access
2. SSH Hardening and Privilege Escalation
3. Identify Open Ports & Remove Packages
---
1. Limit Node Access
```
   - id, who and last command
   - /etc/passwd, /etc/shadow, /etc/group
   - passwd <username>
   - userdel / groupdel
   - usermod -s /usr/sbin/nologin himanshi [disable login]
   - deluser <username> <groupname> [delete from group]
```
---
2. SSH Hardening and Privilege Escalation
```
   - ssh <user>@<hostname or IP>
   - adduser jim [Create user]
   - ssh-copy-id -i ~/.ssh/id_rsa.pub jim@node01 [From host machine to node01]
   - usermod rob -G admin [add user to admin group]
   - edit /etc/ssh/sshd_config for PermitRootLogin / PasswordAuthentication
   - service sshd restart
```
---
3. Identify Open Ports & Remove Packages & Restrict Kernel Modules
```
   - systemctl list-units --type service [list active services]
   - cat /etc/modprobe.d/blacklist.conf
   - cat /etc/services | grep -w 53
```
---
4. ufw Firewall
```
   - ufw allow 1000:2000/tcp
```
---
5. Linux Syscalls
```
   - strace -p <pid>
   - strace -c touch /tmp/test.txt
   - aquasec tracee
```
---
6. Seccomp
```
   - securityContext:
         seccompProfile:
            type: RuntimeDefault / Unconfined
      containers:
      - securityContext:
          allowPrivilegeEscalation: false
   - seccompProfile:
       type: Localhost
       localhostProfile: profiles/audit.json [ "defaultAction: "SCMP_ACT_LOG" ]
   - dir for the profile: /var/lib/kubelet/seccomp
   - syscall number: /usr/include/asm/unistd_64.h
```
---
7. AppArmor
```
   - cat /sys/module/apparmor/parameters/enabled
   - cat /sys/kernel/security/apparmor/profiles
   - apparmor-deny-write
     profile apparmor-deny-write flags=(attach_disconnected) {
       file,
       # Deny all file writes.
       deny /** w
     }
   - aa-status [loaded profiles]
   - enforce / complain / unconfined
   - apparmor-utils
   - aa-genprof /root/add_data.sh
   - cat /etc/apparmor.d/root.add_data.sh [generated profile]
   - apparmor_parser /etc/apparmor.d/root.add_data.sh [load profile]
   - apparmor_parser -R /etc/apparmor.d/root.add_data.sh [disable profile]
      ln -s /etc/apparmor.d/root.add_data.sh /etc/apparmor.d/disable
   - annotations:
       container.apparmor.security.beta.kubernetes.io/<container_name>: localhost/<profile-name>
```
---
8. Linux Capabilities
```
   - getcap /usr/bin/ping
     /usr/bin/ping = cap_net_raw+ep
   - getpcaps <pid>
   - securityContext:
       capabilities:
         add: ["SYS_TIME"]
```