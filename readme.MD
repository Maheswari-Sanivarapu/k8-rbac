when new user join the team:-
1. creating the IAM user in aws and giving the desribe permissions to the user using IAM policy
2. creating one instance and configuring the creds using aws configure of the new user
3. creating the role which has permissions to access the resources and attaching the role to user using rolebinding
4. integrating IAM and EKS using aws-auth.yaml then only both can integrate and the new user can access the pods
5. in the created instance of the user in step 2 install kubectl in the new instance using the below commands

curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.33.0/2025-05-01/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv kubectl /usr/local/bin/kubectl
6. then add the kubeconfig in the new instance using below command
aws eks update-kubeconfig --region <region-name> --name <cluster-name>
7. then the new user can access the pod

creating service-account to access the secrets-password from aws secrets manager :-

1. create OIDC Provider in instance 

REGION_CODE=us-east-1 
CLUSTER_NAME=roboshop
ACC_ID=557690617909

eksctl utils associate-iam-oidc-provider \
    --region $REGION_CODE \
    --cluster $CLUSTER_NAME \
    --approve

2. create secret manager in AWS using aws secret manager and attach the ARN of secret manager where our password is stored and attach the ARN by creating the new IAM policy and add it in specific ARN and in text

3. Attach the IAM policy created in step 2 to the newly creating service account

eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=roboshop \
--name=roboshop-password \
--attach-policy-arn=arn:aws:iam::557690617909:policy/fetchsecretmanagerpassword \
--override-existing-serviceaccounts \
--region $REGION_CODE \
--approve

4. create role by creating permissions and attach the service-account to the role using the rolebinding
kubectl get sa -n <namespac-name> -o yaml
5. attach the serviceaccount to the pod


SA
====
-> Access the internal workloads by communication k8 API. SA can have role and rolebinding
-> SA can fetch secrets, create ALB, any AWS service based on the role attached

1. Make sure OIDC provider exists, creates trust and federation between IAM and others
2. Create a policy

3. We can create IAM Role and attach policy.
4. We can create SA and give IAM role in annotation
or
Create SA and role through eksctl command

5. Provide sa in the pod spec.
