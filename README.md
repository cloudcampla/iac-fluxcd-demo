
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



# CREAR ROLE LOKI 

eksctl create iamserviceaccount \
  --cluster=agosto-eks-cluster \
  --namespace=observability \
  --name=demo-grafana-loki-sa \
  --role-name demo-grafana-loki-role \
  --role-only \
  --attach-policy-arn=arn:aws:iam::aws:policy/AmazonS3FullAccess \
  --approve


# KARPENTER

eksctl create iamserviceaccount \
  --cluster=agosto-eks-cluster \
  --namespace=karpenter \
  --name=karpenter \
  --role-name=agosto-eks-cluster-karpenter-role \
  --role-only \
  --attach-policy-arn=arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly \
  --attach-policy-arn=arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy \
  --attach-policy-arn=arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy \
  --attach-policy-arn=arn:aws:iam::aws:policy/AmazonSSMManagedInstanceCore \
  --approve

curl -fsSL https://raw.githubusercontent.com/aws/karpenter/v0.31.0/website/content/en/preview/getting-started/getting-started-with-karpenter/cloudformation.yaml  > $(pwd)/temp.yaml \
&& aws cloudformation deploy \
  --stack-name "Karpenter-agosto-eks-cluster" \
  --template-file "$(pwd)/temp.yaml" \
  --capabilities CAPABILITY_NAMED_IAM \
  --parameter-overrides "ClusterName=agosto-eks-cluster"

- groups:
  - system:bootstrappers
  - system:nodes
  rolearn: arn:aws:iam::628924030472:role/agosto-eks-cluster-karpenter-role
  username: system:node:{{EC2PrivateDNSName}}