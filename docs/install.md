# installation of flux

1. Create a ssh-key and load it as a deploy key into the repository (read/write access)

```bash
ssh-keygen -t ed25519 -o -f ~/.ssh/id_ed25519_flux -C "flux-system"
```

2. Download the [flux-cli](https://github.com/fluxcd/flux2/releases)
3. Set the right kubernetes-context and run the flux bootstrap command

```bash
flux bootstrap git -s \
--url=ssh://git@github.com/janfuhrer/fluxcd-monorepo \
--path clusters/prod \
--private-key-file ~/.ssh/id_ed25519_flux \
--author-email "john@doe.com" \
--author-name "John Doe"
```

## Configure Mozilla SOPS

1. Install [sops](https://github.com/mozilla/sops) & [age](https://github.com/FiloSottile/age)

2. Create an age-key

```bash
age-keygen -o age.agekey
```

3. Create secret `sops-age-flux-global-prod` in the `flux-system` namespace

```bash
cat age.agekey |
kubectl create secret generic sops-age-flux-global-prod \
--namespace=flux-system \
--from-file=age.agekey=/dev/stdin
```

4. Add decryption provider in the file `clusters/prod/flux-system/patch.yaml`

```yaml
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: flux-system
  namespace: flux-system
spec:
  decryption:
    provider: sops
    secretRef:
      name: sops-age-flux-global-prod
```

5. Create a SOPS config file

```bash
export PUB_KEY=

cat <<EOF > ./clusters/prod/.sops.yaml
creation_rules:
  - path_regex: .*.yaml
    encrypted_regex: ^(data|stringData)$
    age: ${PUB_KEY}
EOF
```

6. Encrypt a file (example with cert-manager issuer)

```bash
echo -n ${PASSWORD} | kubectl create secret generic cluster-issuer-example-com-secret -n cert-manager --dry-run=client --from-file=api-token=/dev/stdin -o yaml > api-token-example-com.yaml

# use sops with .sops.yaml config file
sops -e -i api-token-example-com.yaml

# use sops with parameters
sops --age=${PUB_KEY} -e --encrypted-regex '^(data|stringData)$' -i api-token-example-com.yaml
```
