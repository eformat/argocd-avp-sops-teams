# argocd-avp-sops-teams

Prerequisites

- OpenShift Cluster 4.14+
- Download Helm, Kustomize, Age go binaries

## Bootstrap

Install Subscriptions

```bash
oc apply -f bootstrap/setup-subs.yaml
```

Install CR

```bash
oc apply -f bootstrap/setup-cr.yaml
```

`FIXME` - Setup Users & Groups

```bash
# Add user admin to `admin` group, cluser-admin
# Add users a-user, b-user
```

## Cluster ArgoCD

## SOPS key setup

```bash
mkdir -p secrets
age-keygen -o secrets/age-key-admin.txt
echo 'secrets/age-key-admin.txt' >> .gitignore
```

```bash
oc apply -f - <<EOF
apiVersion: v1
stringData:
  age-key.txt: |
    $(grep AGE-SECRET-KEY secrets/age-key-admin.txt)
kind: Secret
metadata:
  name: sops-age-key
  namespace: openshift-gitops
type: Opaque
EOF
```

## Team ArgoCD

A Team

```bash
oc create -k applications/argocd/overlay/a-team
```

```bash
mkdir -p secrets
age-keygen -o secrets/age-key-a-team.txt
echo 'secrets/age-key-a-team.txt' >> .gitignore
```

```bash
oc apply -f - <<EOF
apiVersion: v1
stringData:
  age-key.txt: |
    $(grep AGE-SECRET-KEY secrets/age-key-a-team.txt)
kind: Secret
metadata:
  name: sops-age-key
  namespace: a-team
type: Opaque
EOF
```

```bash
oc apply -f app-of-apps/a-team-app-of-apps.yaml
```
