# eks-alb-ingress-controller

## Activate OIDC provider for cluster

```
eksctl utils associate-iam-oidc-provider --cluster alb-demo-cluster  --approve --region us-east-2
```


# Step 1: Create IAM Role using eksctl

### Download an IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```


### aws official documentation to insall alb controller

```
https://docs.aws.amazon.com/eks/latest/userguide/lbc-helm.html
```

### install alb using helm

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=alb-demo-cluster \
  --set serviceAccount.create=true \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set serviceAccount.annotations.eks\\.amazonaws\\.com/role-arn=“arn:aws:iam::251620460948:role/eks-alb”
```
