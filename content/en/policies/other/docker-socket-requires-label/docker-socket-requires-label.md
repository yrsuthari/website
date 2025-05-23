---
title: "Docker Socket Requires Label"
category: Other
version: 
subject: Pod
policyType: "validate"
description: >
    Accessing a container engine's socket is for highly specialized use cases and should generally be disabled. If access must be granted, it should be done on an explicit basis. This policy requires that, for any Pod mounting the Docker socket, it must have the label `allow-docker` set to `true`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/docker-socket-requires-label/docker-socket-requires-label.yaml" target="-blank">/other/docker-socket-requires-label/docker-socket-requires-label.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: docker-socket-check
  annotations:
    policies.kyverno.io/title: Docker Socket Requires Label
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.8.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Accessing a container engine's socket is for highly specialized use cases and should generally
      be disabled. If access must be granted, it should be done on an explicit basis. This policy
      requires that, for any Pod mounting the Docker socket, it must have the label `allow-docker` set
      to `true`.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: conditional-anchor-dockersock
    match:
      any:
      - resources:
          kinds:
          - Pod
    validate:
      message: "If a hostPath volume exists and is set to `/var/run/docker.sock`, the label `allow-docker` must equal `true`."
      pattern:
        metadata:
          labels:
            allow-docker: "true"
        (spec):
          (volumes):
          - (hostPath):
              path: "/var/run/docker.sock"
```
