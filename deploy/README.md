# Kubelet Serving Certificate Approver Installation Manifests

The manifests are automatically generated by `kustomize`. If you have specific need, then you can use `kustomize` to generate your own manifests.

## Standalone Installation

* [standalone-install.yaml](standalone-install.yaml) - installs Kubelet Serving Certificate Approver into `kubelet-serving-cert-approver` namespace following the Principle of Least Privilege.

In nutshell:

* Configured Container Security Context
  * All capabilities dropped
  * Read-only root filesystem
  * Runs unprivileged with disallowed privilege escalation
* Configured Pod Security Context
  * Runs with non-root user
* No shell, uses [distroless](https://github.com/GoogleContainerTools/distroless) image
* Configured Pod Security Policy
  * Enforces all mentioned configuration and adds addititional contstraints
  * Only active when your cluster is enabled to use any `PodSecurityPolicy`

## High Availability Installation

* [ha-install.yaml](ha-install.yaml) - the same as **Standalone Installation** but with multiple replicas.

## How to enable debug logging?

You can add extra argument to `cert-approver` container:

```yaml
containers:
- name: cert-approver
  args:
  - --debug
```

## How to create ServiceMonitor for Prometheus Operator?

You can install `ServiceMonitor` by the following example:

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: kubelet-serving-cert-approver
  namespace: kubelet-serving-cert-approver
  labels:
    app.kubernetes.io/instance: kubelet-serving-cert-approver
    app.kubernetes.io/name: kubelet-serving-cert-approver
spec:
  selector:
    matchLabels:
      app.kubernetes.io/instance: kubelet-serving-cert-approver
      app.kubernetes.io/name: kubelet-serving-cert-approver
  endpoints:
  - interval: 60s
    path: /metrics
    port: metrics
  namespaceSelector:
    matchNames:
    - kubelet-serving-cert-approver
```