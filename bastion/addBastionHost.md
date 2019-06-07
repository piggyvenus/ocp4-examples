## Add Bastion Host to AWS to access masters

This version there is no public access to the master out of the installation. Here are the steps to create a bastion host to access one of the masters.
1. Create a security group that allows SSH access into the VPC created by the installer
2. Create an EC2 instance yourself. I use t2.micro for mine:
    * inside the VPC that the installer created
    * on one of the public subnets the installer created
3. Associate a public IP address with the EC2 instance that you created.
4. copy the ssh key that you used to create the cluster to the bastion host.
5. ssh -i <ec2 key> ec2-user@bastion-public-ip
6. once you login to, you then ssh to one of the masters: ssh -i <sshkey that you use for creating cluster> core@master-host-name
to get the master hostname via 'oc get nodes'

Steps are in https://github.com/openshift/training/blob/master/docs/03-explore.md
