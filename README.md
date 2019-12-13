# Deploy instructions 
# Prerequisites
- You have ansible installed
- You have your AWS ec2 key pair, access_key and Secret key
- You have a RHEL 7 instance provisioned on AWS with NGINX installed. You can use the repos below to provision and set up the instance(s).
    - https://github.com/muge13/terraform-provision-aws-vpc
    - https://github.com/muge13/terraform-provision-aws-ec2
    - https://github.com/muge13/ansible-configure-base
    - https://github.com/muge13/ansible-configure-nginx-simple 
# Deployment
- On your system set your AWS credentials on your environment
```
export AWS_ACCESS_KEY_ID="" &&  export AWS_SECRET_ACCESS_KEY="" && export AWS_REGION=""
```

- The ansible command below will initiate a deployment on the first server based on the hosts_identifier or the defined hosts tags. It will hence trigger an asyncrounous rollout of the new image through out the autoscaling group
```
ansible-playbook  -u ec2-user  -i ec2.py  -T 60 -f 100--private-key=~/{{user}}.pem  -l {{hosts_identifier}} Deploy.yml -e "host_env='{{vpc_name}}' ec2_Name='test' ec2_role='test'"
```
## Breakdown
- Pull code from master branch of simple SPA repo ("https://github.com/muge13/sample-spa.git")
- *For other systems this would be the part where setup and compiling is performed*
- Create an image of the pre-baked server and use it as a basis to roll out similar servers
- Create a EC2 launch configuration to template the scaling action
- Update an autoscaling group with the new launch configuration
# Rollback
- The ansible command below will initiate a rollback on the ASG to a previous launch configuration. 
```
ansible-playbook  -u ec2-user  -i ec2.py  -T 60 -f 100--private-key=~/{{user}}.pem  -l {{hosts_identifier}} Rollback.yml
```

# Caveats
- The Autoscaling group needs to be created first in the same VPC and subnets as the instances to be rolled out, and the name configured on the post-deployment scripts