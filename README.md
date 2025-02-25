# eks-alb-ingress-controller

### Activate OIDC provider for cluster

```
eksctl utils associate-iam-oidc-provider --cluster alb-demo-cluster  --approve --region us-east-2
```


# Step 1: Create IAM Role using eksctl

### Download an IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

### Create an IAM policy using the policy downloaded in the previous step.
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

### Set up an IAM service account in an EKS cluster, allowing the AWS Load Balancer Controller to manage AWS Load Balancers on behalf of the Kubernetes cluster.

```
eksctl create iamserviceaccount \
  --cluster=alb-demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::251620460948:policy/AWSLoadBalancerControllerIAMPolicy \
  --region us-east-2 \
  --approve
```

# Step 2: Install AWS Load Balancer Controller

### Add the eks-charts Helm chart repository.

```
helm repo add eks https://aws.github.io/eks-charts
```

### Update your local repo to make sure that you have the most recent charts (If Not first time).

```
helm repo update eks
```

### Install the AWS Load Balancer Controller.

```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=alb-demo-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller
```

### If you use helm upgrade, you must manually install the CRDs.

```
wget https://raw.githubusercontent.com/aws/eks-charts/master/stable/aws-load-balancer-controller/crds/crds.yaml
kubectl apply -f crds.yaml
```

# Step 3: Verify that the controller is installed

### Verify that the controller is installed.

```
kubectl get deployment -n kube-system aws-load-balancer-controller
```
