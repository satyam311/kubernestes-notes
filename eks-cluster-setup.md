# prerequisites

kubectl – A command line tool for working with Kubernetes clusters. For more information, see Installing or updating kubectl.
https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html

eksctl – A command line tool for working with EKS clusters that automates many individual tasks. For more information, see Installing or updating.
https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html

AWS CLI – A command line tool for working with AWS services, including Amazon EKS. For more information, see Installing, updating, and uninstalling the AWS CLI
https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html in the AWS Command Line Interface User Guide. 

After installing the AWS CLI, I recommend that you also configure it. For more information, see Quick configuration with aws configure in the AWS Command Line Interface User Guide.
https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html#cli-configure-quickstart-config 

--- 
# Install EKS

Please follow the prerequisites doc before this.

## Install using Fargate

```
eksctl create cluster --name demo-cluster-three-tier-1 --region us-east-1
```

## Delete the cluster

```
eksctl delete cluster --name demo-cluster-three-tier-1 --region us-east-1
```

---

# commands to configure IAM OIDC provider 

```
export cluster_name=<CLUSTER-NAME>
```

```
oidc_id=$(aws eks describe-cluster --name $cluster_name --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5) 
```

## Check if there is an IAM OIDC provider configured already

```
aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4
```

If not, run the below command

```
eksctl utils associate-iam-oidc-provider --cluster $cluster_name --approve
```tch (push metrics)
- Route53 (update DNS)
- EC2 / EKS APIs (Datadog, autoscaler, etc.)
```


### Intution :

1. You have Pods running inside Kubernetes, but those pods sometimes need to call AWS services like:
   - S3 (store files)
   - SQS (read/write messages)
   - CloudWatch (push metrics)
   - Route53 (update DNS)
   - EC2 / EKS APIs (Datadog, autoscaler, etc.)

The Solution — IRSA

IRSA = IAM Roles for Service Accounts

It lets AWS verify:

This Pod belongs to this Kubernetes ServiceAccount
That ServiceAccount is linked to this one IAM Role
Issue temporary AWS credentials for it.

**Kubernetes ServiceAccount Token = OIDC Token**
```
AWS must trust that identity.
So we tell AWS:
Trust tokens coming from: https://oidc.eks.<region>.amazonaws.com/id/<OIDC-ID>
That’s why we create the IAM OIDC provider.
```

**Set your cluster name**

```export cluster_name=<CLUSTER-NAME>```

**Fetch the OIDC ID from AWS**
```
oidc_id=$(aws eks describe-cluster --name $cluster_name \
  --query "cluster.identity.oidc.issuer" --output text | cut -d '/' -f 5)

echo $oidc_id
```

**Check if the OIDC provider exists**
```
aws iam list-open-id-connect-providers | grep $oidc_id
```


**If OIDC provider does NOT exist, create it:**

```
eksctl utils associate-iam-oidc-provider \
  --cluster $cluster_name \
  --approve
```

```
You are telling AWS:

“Hey AWS, trust this cluster’s identity provider
so that Kubernetes service accounts inside it can act as IAM roles.”

```

https://github.com/iam-veeramalla/three-tier-architecture-demo/blob/master/EKS/01-prerequisites.md






