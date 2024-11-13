# Roaming-Project
AWS Region:  europe(London) eu-west-2


Creating network components, starting with a VPC using Terraform


We'll start by creating four different subnets, two private and two public, in  two different availability zones(eu-west-2a, eu-west-2b). EKS requires us to provide multiple subnets in at least two different availability zones.

Private subnets to deploy kubernetes nodes
Public subnets to provision network and application load balancer.

We'll use native Ingress provided by the AWS LoadBalancer Controller and secure it with a TLS certificate.
Use of subnet tags that must be present for EKS to  properly discover subnets.

Create an internet  gateway and attach it to our VPC. It is used to provide internet access to any virtual machines that have a public IP address and  are located in public subnets.
Create a NAT gateway and  place it in one of the public subnets. It is used to translate private virtual  machine IP addresses located in private subnets to a public one, and it allows  internet access within private subnets.

Create a public route table with  a default route to the internet gateway. For example, when the destination IP address  of the request is located inside the VPC, the main route is used, but when the  destination IP address is outside the VPC, the request will be routed using the default  route, and in this case, the internet gateway.

Create another private route table  with a default route to the NAT gateway, which allows virtual machines with private  IP addresses to reach the internet.
Attach these route tables to our four subnets.


Creating EKS Cluster on AWS using Terraform

* Main kubernetes components
  Etcd Database, scheduler, Controller Manager, Legacy Cloud Controler Manager
  
* Identify what IAM permissions to grant to EKS
  
* Permissions Kubernetes workers would require:
  
* AmazonEKSWorkerNodePolicy: It  also allows running Pod Identity Agent, which is used to grant granular  access to your application. For example, you can grant your application  read and write access to a specific S3 bucket.
* AmazonEKS-CNI_POLICY: grant EKS access to modify the  IP address configuration on your EKS worker nodes.For example, when you create a pod,   the IP is allocated from the secondary IP  address range assigned to the worker node. So, we are not using virtual 
  networks  such as Flannel or Calico anymore;   you get native AWS IP addresses for each pod.Later, you’ll see a benefit when  we start creating load balancers   and use IP mode to route traffic  directly to the pod IP address.Before that cloud providers had 
  to use NodePorts behind the scenes when you created  services of type load balancers. Now, it’s direct. By reducing  the number of network hops,   we reduce the latency of the requests
* AmazonEC2ContainerRegistryReadOnly:used to grant EKS permission to pull Docker images from the AWS-managed  container service called ECR.

* Creation of the Control Plane
* 
