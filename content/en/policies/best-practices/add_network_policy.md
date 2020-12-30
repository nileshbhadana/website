---
type: "docs"
title: Add Networkpolicy
linkTitle: Add Networkpolicy
weight: 4
description: >
    By default, Kubernetes allows communications across all pods within a cluster. Network policies and, a CNI that supports network policies, must be used to restrict communinications. A default NetworkPolicy should be configured for each namespace to default deny all ingress traffic to the pods in the namespace. Application teams can then configure additional NetworkPolicy resources to allow desired traffic to application pods from select sources.
---

## Category
Workload Management

## Definition
[/best-practices/add_network_policy.yaml](https://github.com/kyverno/policies/raw/main//best-practices/add_network_policy.yaml)

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: add-networkpolicy
  annotations:
    policies.kyverno.io/category: Workload Management
    policies.kyverno.io/description: By default, Kubernetes allows communications across 
      all pods within a cluster. Network policies and, a CNI that supports network policies, 
      must be used to restrict communinications. A default NetworkPolicy should be configured 
      for each namespace to default deny all ingress traffic to the pods in the namespace. 
      Application teams can then configure additional NetworkPolicy resources to allow 
      desired traffic to application pods from select sources.
spec:
  validationFailureAction: audit
  rules:
  - name: default-deny-ingress
    match:
      resources: 
        kinds:
        - Namespace
        name: "*"
    exclude:
      resources:
        namespaces:
          - "kube-system"
          - "default"
          - "kube-public"
          - "kyverno"
    generate: 
      kind: NetworkPolicy
      name: default-deny-ingress
      namespace: "{{request.object.metadata.name}}"
      synchronize : true
      data:
        spec:
          # select all pods in the namespace
          podSelector: {}
          policyTypes: 
          - Ingress
```