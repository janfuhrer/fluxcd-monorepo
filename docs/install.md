# installation of flux

1. Create a ssh-key and load it as a deploy key into the repository (read/write access)

```bash
ssh-keygen -t ed25519 -o -f ~/.ssh/id_ed25519_flux -C "flux-system"
```

2. Download and set up [flux-cli](https://github.com/fluxcd/flux2/releases)
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

tbd
