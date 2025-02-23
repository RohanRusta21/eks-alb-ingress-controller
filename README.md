# eks-alb-ingress-controller

## Activate OIDC provider for cluster

```
eksctl utils associate-iam-oidc-provider --cluster alb-demo-cluster  --approve --region us-east-2
```


### Trust relationship policy

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::251620460948:oidc-provider/oidc.eks.us-east-2.amazonaws.com/id/8867CF29797145BF9899A85AD49F75CF"
      },                                                     
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "oidc.eks.us-east-2.amazonaws.com/id/8867CF29797145BF9899A85AD49F75CF:aud": "sts.amazonaws.com",
          "oidc.eks.us-east-2.amazonaws.com/id/8867CF29797145BF9899A85AD49F75CF:sub": "system:serviceaccount:kube-system:aws-load-balancer-controller"
        }
      }
    }
  ]
}
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
