# How to Diagnose a Crashed API Server

Steps to take

1.  Restart `kubelet` so you don't have to wait too long in the following steps</br></br>
    ```
    systemctl restart kubelet
    ```

1.  Determine if the kubelet can even start the API server</br></br>
    If there is a syntax error in the YAML manifest, then kubelet will not be able to parse it and will eventually complain. Do the following and watch the output for up to 60 seconds. Note that if you have errors in your pod manifest, kubelet will report them exactly the same way using the [same kind of error messages](./yaml-faq.md#dealing-with-errors) that kubectl does!

    ```
    journalctl -fu kubelet | grep apiserver
    ```

    There are normally three classes of error we will see here

    1. Could not process manifest file

        This is indicative of an error in the YAML of `/etc/kubernetes/manifests/kube-apiserver.yaml`. You should go directly to edit that file and correct the issue.<br/>Note that YAML parsers only report the first error they find. If you have more than one error - i.e. the apiserver doesn't come up after fixing whatever you've found, then repeat this diagnostic process from the top.

    1.  Structure or argument error

        The YAML has been parsed successfully, however you get some cryptic message about something or other. This means that although there's no syntax issues, what you've put in the manifest does not correctly represent the spec for the API server pod, or an argument for something like a volume mount is an invalid path. You need to find and fix this in the manifest file. See also [YAML - Dealing with errors](./yaml-faq.md#dealing-with-errors).

    1. CrashLoopBackOff

        This means that the YAML was successfully parsed, the pod started and then exited with an error. To fix this, continue reading.

1.  Kubelet does launch API server, but it crashes immediately.</br></br>
    This means there is likely an issue with one or more arguments to the `kube-apiserver` command. There is a location where the last output of the pod is stored, which can help you to get information about why the pod is not starting.

    ```
    cd /var/log/pods
    ls -ld *apiserver*
    ```

    This should return something like

    ```
    drwxr-xr-x 3 root root 4096 Oct 26 04:29 kube-system_kube-apiserver-controlplane_02d13ddeddf8e935ec2407132767aeaa
    ```

    If there's more than one match, choose the one with the most recent timestamp.

    **NOTE**: This directory can change name frequently. If you have to repeat the diagnostic process, don't assume it is the same as last time you did this in the same session. Repeat this step from the top.

    ```
    cd kube-system_kube-apiserver-controlplane_02d13ddeddf8e935ec2407132767aeaa
    cd kube-api-server
    ls -l
    cat 1.log
    ```