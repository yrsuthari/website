---
title: "Deny Commands in Exec Probe in CEL expressions"
category: Other in CEL
version: 1.11.0
subject: Pod
policyType: "validate"
description: >
    Developers may feel compelled to use simple shell commands as a workaround to creating "proper" liveness or readiness probes for a Pod. Such a practice can be discouraged via detection of those commands. This policy prevents the use of certain commands `jcmd`, `ps`, or `ls` if found in a Pod's liveness exec probe.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other-cel/deny-commands-in-exec-probe/deny-commands-in-exec-probe.yaml" target="-blank">/other-cel/deny-commands-in-exec-probe/deny-commands-in-exec-probe.yaml</a>

```yaml
apiVersion: kyverno.io/v2beta1
kind: ClusterPolicy
metadata:
  name: deny-commands-in-exec-probe
  annotations:
    policies.kyverno.io/title: Deny Commands in Exec Probe in CEL expressions
    policies.kyverno.io/category: Other in CEL
    policies.kyverno.io/subject: Pod
    kyverno.io/kyverno-version: 1.11.0
    policies.kyverno.io/minversion: 1.11.0
    kyverno.io/kubernetes-version: "1.26-1.27"
    policies.kyverno.io/description: >-
      Developers may feel compelled to use simple shell commands as a workaround to
      creating "proper" liveness or readiness probes for a Pod. Such a practice can be discouraged
      via detection of those commands. This policy prevents the use of certain commands
      `jcmd`, `ps`, or `ls` if found in a Pod's liveness exec probe.
spec:
  validationFailureAction: Audit
  background: false
  rules:
    - name: check-commands
      match:
        any:
        - resources:
            kinds:
              - Pod
            operations:
            - CREATE
            - UPDATE
      celPreconditions:
        - name: "check-liveness-probes-commands-exist"
          expression: >-
            object.spec.containers.exists(container, size(container.?livenessProbe.?exec.?command.orValue([])) > 0)
      validate:
        cel:
          expressions:
            - expression: >-
                object.spec.containers.all(container, 
                !container.?livenessProbe.?exec.?command.orValue([]).exists(command, 
                command.matches('\\bjcmd\\b') || command.matches('\\bps\\b') || command.matches('\\bls\\b')))
              message: Cannot use commands `jcmd`, `ps`, or `ls` in liveness probes.


```
