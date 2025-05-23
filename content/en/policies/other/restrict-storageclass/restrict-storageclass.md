---
title: "Restrict StorageClass"
category: Other, Multi-Tenancy
version: 
subject: StorageClass
policyType: "validate"
description: >
    StorageClasses allow description of custom "classes" of storage offered by the cluster, based on quality-of-service levels, backup policies, or custom policies determined by the cluster administrators. For shared StorageClasses in a multi-tenancy environment, a reclaimPolicy of `Delete` should be used to ensure a PersistentVolume cannot be reused across Namespaces. This policy requires StorageClasses set a reclaimPolicy of `Delete`.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/restrict-storageclass/restrict-storageclass.yaml" target="-blank">/other/restrict-storageclass/restrict-storageclass.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: restrict-storageclass
  annotations:
    policies.kyverno.io/title: Restrict StorageClass
    policies.kyverno.io/category: Other, Multi-Tenancy
    policies.kyverno.io/severity: medium
    policies.kyverno.io/subject: StorageClass
    policies.kyverno.io/description: >-
      StorageClasses allow description of custom "classes" of storage offered
      by the cluster, based on quality-of-service levels, backup policies, or
      custom policies determined by the cluster administrators. For shared StorageClasses
      in a multi-tenancy environment, a reclaimPolicy of `Delete` should be used to ensure
      a PersistentVolume cannot be reused across Namespaces. This policy requires
      StorageClasses set a reclaimPolicy of `Delete`.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: storageclass-delete
    match:
      any:
      - resources:
          kinds:
          - StorageClass
    validate:
      message: "StorageClass must define a reclaimPolicy of Delete."
      pattern:
        reclaimPolicy: Delete
```
