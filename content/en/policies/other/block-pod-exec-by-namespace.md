---
title: "Block Pod Exec by Namespace Name"
category: Sample
version: 1.4.2
subject: Pod
policyType: "validate"
description: >
    The `exec` command may be used to gain shell access, or run other commands, in a Pod's container. While this can be useful for troubleshooting purposes, it could represent an attack vector and is discouraged. This policy blocks Pod exec commands to Pods in a Namespace called `pci`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/block-pod-exec-by-namespace.yaml" target="-blank">/other/block-pod-exec-by-namespace.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: deny-exec-by-namespace-name
  annotations:
    policies.kyverno.io/title: Block Pod Exec by Namespace Name
    policies.kyverno.io/category: Sample
    policies.kyverno.io/minversion: 1.4.2
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      The `exec` command may be used to gain shell access, or run other commands, in a Pod's container. While this can
      be useful for troubleshooting purposes, it could represent an attack vector and is discouraged.
      This policy blocks Pod exec commands to Pods in a Namespace called `pci`.
spec:
  validationFailureAction: audit
  background: false
  rules:
  - name: deny-exec-ns-pci
    match:
      resources:
        kinds:
        - PodExecOptions
    preconditions:
    - key: "{{ request.operation }}"
      operator: Equals
      value: CONNECT
    validate:
      message: Pods in this namespace may not be exec'd into.
      deny:
        conditions:
          - key: "{{ request.namespace }}"
            operator: Equals
            value: pci

```