---
title: "Require imagePullSecrets"
category: Sample
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Some registries, both public and private, require credentials in order to pull images from them. This policy checks those images and if they come from a registry other than ghcr.io or quay.io an `imagePullSecret` is required.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/require-imagepullsecrets/require-imagepullsecrets.yaml" target="-blank">/other/require-imagepullsecrets/require-imagepullsecrets.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-imagepullsecrets
  annotations:
    policies.kyverno.io/title: Require imagePullSecrets
    policies.kyverno.io/category: Sample
    policies.kyverno.io/minversion: 1.6.0
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Some registries, both public and private, require credentials in order to pull images
      from them. This policy checks those images and if they come from a registry
      other than ghcr.io or quay.io an `imagePullSecret` is required.
spec:
  validationFailureAction: Audit
  background: true
  rules:
  - name: check-for-image-pull-secrets
    match:
      any:
      - resources:
          kinds:
          - Pod
    preconditions:
      all:
      - key: "{{ images.containers.*.registry }}"
        operator: AnyNotIn
        value:
        - ghcr.io
        - quay.io
    validate:
      message: "An `imagePullSecret` is required when pulling from this registry."
      pattern:
        spec:
          imagePullSecrets:
          - name: "?*"

```
