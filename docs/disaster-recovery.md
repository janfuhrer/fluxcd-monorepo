# disaster recovery

## prequisites

* SSH deploykey for flux-system, private-key has to be saved under `~/.ssh/id_ed25519_flux`
* private age key `age.agekey`

## main part

1. Create namespace `flux-system`

```bash
kubectl create ns flux-system
```

2. Create secret with age key in `flux-system` namespace

```bash
cat age.agekey |
kubectl create secret generic sops-age-flux-global-prod \
--namespace=flux-system \
--from-file=age.agekey=/dev/stdin
```

3. Bootstrap flux
```bash
flux bootstrap git -s \
--url=ssh://git@github.com/janfuhrer/fluxcd-monorepo \
--path clusters/prod \
--private-key-file ~/.ssh/id_ed25519_flux \
--author-email "john@doe.com" \
--author-name "John Doe"
```

4. Take a :coffee: and wait until flux has finished it's work

5. Restore apllication data