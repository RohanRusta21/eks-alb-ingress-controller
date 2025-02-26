# AWS Load Balancer Controller (formerly known as eks-alb-ingress-controller)


![alb-ingress](https://github.com/user-attachments/assets/5e139245-0835-4346-8acc-ebd0d3f248d5)




### Associates an OIDC provider with your EKS cluster

 - Associates an OIDC provider with your EKS cluster
 - Allows IAM roles to be used inside the Kubernetes cluster.

```
eksctl utils associate-iam-oidc-provider --cluster alb-demo-cluster  --approve --region us-east-2
```


# Step 1: Create IAM Role using eksctl

### Download an IAM policy for the AWS Load Balancer Controller that allows it to make calls to AWS APIs.
 
  - Since the AWS Load Balancer Controller needs permissions to manage AWS Load Balancers, you need to create an IAM policy and associate it     with a Kubernetes service account.

```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

### Create an IAM policy using the policy downloaded in the previous step.

 - Creates a new IAM policy named AWSLoadBalancerControllerIAMPolicy.
 - This policy allows the Load Balancer Controller to make AWS API calls.

```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

### Set up an IAM service account in an EKS cluster, allowing the AWS Load Balancer Controller to manage AWS Load Balancers on behalf of the Kubernetes cluster.

 - Creates a Kubernetes ServiceAccount named aws-load-balancer-controller.
 - Associates it with an IAM Role (AmazonEKSLoadBalancerControllerRole).
 - Attaches the AWSLoadBalancerControllerIAMPolicy.
 - Allows Kubernetes to use AWS IAM for authentication.
 

```
eksctl create iamserviceaccount \
  --cluster=alb-demo-cluster \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
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

 - Installs the AWS Load Balancer Controller in the kube-system namespace.
 - Links it to the existing aws-load-balancer-controller service account.

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

```
kubectl get deployment -n kube-system aws-load-balancer-controller
```
