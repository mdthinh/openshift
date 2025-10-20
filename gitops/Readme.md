# Update local admin password
```bash
ARGOCD_PASSWORD=$(htpasswd -bnBC 10 "" 'Mdt@1311' | tr -d ':\n')
oc -n openshift-gitops patch secret argocd-secret \
  -p "{\"stringData\": {\"admin.password\": \"$ARGOCD_PASSWORD\", \"admin.passwordMtime\": \"$(date +%FT%T%Z)\"}}"
```

# Config argoCD authen - entraID

- Update argoCD secret
```bash
export clientSecret=$(echo -n 'RDZnOFF+ZHdJSExZSWFSQmxlOWMyZX5oSlloRXhpbmhoRWRzamNzdg==')
oc patch secret argocd-secret \
  -n openshift-gitops \
  --type=merge \
  -p "{\"data\": {\"oidc.entraid.clientSecret\": \"${clientSecret}\"}}"
```

- Update argoCD oidcConfig
```bash
## create file argocd-oidc.yaml
cat << 'EOT' > argocd-oidc.yaml
name: EntraID
issuer: https://login.microsoftonline.com/f46345aa-4fa0-4d1e-bc2c-aa469179a252/v2.0
clientID: 5d69f2cc-cd20-4613-a338-bbb745dbda5e
clientSecret: $oidc.entraid.clientSecret
requestedScopes:
  - openid
  - profile
  - email
EOT
## Patching 
oc patch argocd openshift-gitops -n openshift-gitops \
  --type=merge --patch "$(cat <<EOF
{
  "spec": {
    "oidcConfig": "$(awk '{printf "%s\\n", $0}' argocd-oidc.yaml)"
  }
}
EOF
)"
```
- Change route
```bash
oc patch route openshift-gitops-server -n openshift-gitops \
  --type=merge -p '{"spec":{"host":"argocd.apps.prod-ocp.viettechcorp.com.vn"}}'
oc get route openshift-gitops-server -n openshift-gitops -o jsonpath='{.spec.host}{"\n"}{.status.ingress[*].conditions[?(@.type=="Admitted")].status}{"\n"}'
```

- Rollout restart argoCD deployment
```bash
oc project openshift-gitops
oc rollout restart deployment.apps/openshift-gitops-server
```
