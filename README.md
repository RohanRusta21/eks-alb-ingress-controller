# eks-alb-ingress-controller

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
