---
title: "Block Pod Exec by Pod Label"
category: Sample
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    The `exec` command may be used to gain shell access, or run other commands, in a Pod's container. While this can be useful for troubleshooting purposes, it could represent an attack vector and is discouraged. This policy blocks Pod exec commands to Pods having the label `exec=false`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/block-pod-exec-by-pod-label/block-pod-exec-by-pod-label.yaml" target="-blank">/other/block-pod-exec-by-pod-label/block-pod-exec-by-pod-label.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: deny-exec-by-pod-label
  annotations:
    policies.kyverno.io/title: Block Pod Exec by Pod Label
    policies.kyverno.io/category: Sample
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      The `exec` command may be used to gain shell access, or run other commands, in a Pod's container. While this can
      be useful for troubleshooting purposes, it could represent an attack vector and is discouraged.
      This policy blocks Pod exec commands to Pods having the label `exec=false`.
spec:
  validationFailureAction: Enforce
  background: false
  rules:
  - name: deny-exec-by-label
    match:
      any:
      - resources:
          kinds:
          - Pod/exec
    context:
    - name: podexeclabel
      apiCall:
        urlPath: "/api/v1/namespaces/{{request.namespace}}/pods/{{request.name}}"
        jmesPath: "metadata.labels.exec || ''"
    preconditions:
      all:
      - key: "{{ request.operation || 'BACKGROUND' }}"
        operator: Equals
        value: CONNECT
    validate:
      message: Exec'ing into Pods protected with the label `exec=false` is forbidden.
      deny:
        conditions:
          all:
          - key: "{{ podexeclabel }}"
            operator: Equals
            value: "false"

```
