# upgrade flux

1. Update the [flux-cli](https://github.com/fluxcd/flux2/releases)
2. Check with `flux check`
3. Run `flux bootstrap` command (use right path for the cluster!), this will update only the file `gotk-components.yaml`

```bash
flux bootstrap git -s \
--url=ssh://git@github.com/janfuhrer/fluxcd-monorepo \
--path clusters/prod \
--private-key-file ~/.ssh/id_ed25519_flux \
--author-email "john@doe.com" \
--author-name "John Doe"
```