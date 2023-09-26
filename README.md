
1. Instalar FluxCD:

  brew install fluxcd/tap/flux


2. Crear llave SSH:

  ssh-keygen \
      -m PEM \
      -t rsa \
      -b 4096 \
      -C "user@mail.com" \
      -f /Users/hocknas/Documents/CLOUDCAMP/septiembre-flux

3. Installar FluxCD en el Cluster

    flux bootstrap git \
      --url=ssh://git@github.com/cloudcampla/iac-fluxcd-demo \
      --branch=main \
      --private-key-file=/Users/hocknas/Documents/CLOUDCAMP/septiembre-flux \
      --path=clusters/demo \
      --context=agosto-eks-cluster

