# Amazon Web Services

### AWS Lambda Layer ARNS
- https://github.com/keithrozario/Klayers/blob/master/deployments/python3.8/arns/us-east-1.csv

### Download and install lambda layer for python
- https://www.youtube.com/watch?v=zrrH9nbSPhQ
- Install on linux:
```
Sudo apt-get update
Sudo apt-get upgrade
mkdir -p build/python/lib/python3.8/site-packages
sudo apt install python3-pip
pip3 install firebase-admin -t build/python/lib/python3.8/site-packages/ --system
sudo apt-get install zip
cd build
zip -r firebase-admin.zip .
sudo apt install awscli
aws s3 cp firebase-admin.zip s3://lightgray-python-packages

pip3 install geopy -t build/python/lib/python3.8/site-packages/ --system
zip -r geopy.zip .
aws s3 cp geopy.zip s3://vae-python-packages
```

### Code Deploy
- https://seanjziegler.com/deploying-code-from-github-to-aws-ec2-with-codepipeline/
- https://docs.aws.amazon.com/codepipeline/latest/userguide/integrations-community-blogposts.html
- https://docs.aws.amazon.com/codedeploy/latest/userguide/codedeploy-agent-operations-install-ubuntu.html
- Check status
```
sudo service codedeploy-agent status
```

## VPC's

### Public load balancer, private VPC
- https://aws.amazon.com/premiumsupport/knowledge-center/public-load-balancer-private-ec2/

### Secure private public subnets
- https://dev.to/raphael_jambalos/secure-aws-environments-with-private-public-subnets-2ei9

### Standard Structure
- VPC
    - IGW
    - public subnet
        - NAT
    - Private - app (subnet)
        - routing table points to NAT
    - Private - data (subnet)
        - routing table points to NAT

### Subnets
- 1 subnet for each tier in each AZ
    1. public
    2. App
        - Subnet app 1a
        - Subnet app 1b
    3. Web
    4. Cache
        - Subnet cache 1a
        - Subnet cache 1b
    5. Data

### Steps to configure

- Always use 10.0.0.0/16 at VPC level
- Use endpoints to communicate from your VPC to s3
- Subnet groups are groups of subnets, which you need in multiple AZs
- All instances in the VPC can talk to each other via the private IP

1. Each VPC has an internet gateway - only one (very easy step)
2. Create VPC
3. Attach internet gateway
4. Create your subnets based on the tiers of your architecture 
5. Duplicate the subnet step for each AZ you want to be in
6. For each subnet, create a routing table
7. For public routing table, destination 0.0.0.0/0 should be targeted at the internet gateway
8. Allocate an elastic IP address
9. Create an NAT Gateway for each AZ you are deploying in
10. Put the NAT Gateway into your public subnet
11. For each private subnet in the AZ with the NAT gateway, destination 0.0.0.0/0 should be targeted at the NAT gateway
12. For security group on an instance basis, it really doesn’t matter for private and NAT gateway does not accept security groups
13. For NACLs you can edit the traffic coming into the NAT Gateway at the public subnet level

### Server metrics with Cloudwatch
- https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch-Agent-custom-metrics-collectd.html
- https://collectd.org/

### Cloudwatch configure
- Collectd → Configure
- Config file - If you're storing the configuration file locally, the configuration file config.json is stored in ```/opt/aws/amazon-cloudwatch-agent/bin/```
- Configure w
```
wget https://s3.amazonaws.com/amazoncloudwatch-agent/ubuntu/amd64/latest/amazon-cloudwatch-agent.deb
$ sudo dpkg -i amazon-cloudwatch-agent.deb
```
- https://omardulaimi.medium.com/export-ec2-logs-to-cloudwatch-and-s3-89285029a345 - exporting the logs

### EC2 image builder config
- https://awsfeed.com/whats-new/management-tools/create-immutable-servers-using-ec2-image-builder-and-aws-codepipeline
- https://www.totalcloud.io/blog/automating-auto-scaling-group-updates#:~:text=AMI%20updates%20keep%20the%20AutoScaling,ll%20need%20an%20AMI%20update - keeping images up to date / updating with new code

### AWS Software
- https://docs.newrelic.com/docs/integrations/amazon-integrations/get-started/introduction-aws-integrations/

## ECS

- https://mherman.org/blog/on-demand-test-environments-with-docker-and-aws-ecs/

## S3

### Deploy JS to S3
- https://blog.cloudthat.com/step-by-step-guide-to-deploy-reactjs-app-on-aws-s3/

## Cloudformation

### VPC
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-vpc.html

### IAM
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/AWS_IAM.html

### Templates
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-ref.html - Reference other just created objects
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getatt.html
- https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-findinmap.html - Return mapped value

## CLI

- be aware of the configuration folder settings
- https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-services.html - EXAMPLES
- https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-troubleshooting.html - errors
- https://awscli.amazonaws.com/v2/documentation/api/latest/index.html - command reference
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-commandstructure.html - command structure
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-alias.html - aliasing
Really nice for scripting sometimes
- https://github.com/awslabs/awscli-aliases - alias examples (also has some docs at the bottom of the doc above to use in depth)
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters.html - formatting parameter types
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-skeleton.html#cli-usage-skeleton-about - generating CLI skeleton —-- GAS
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-shorthand.html - shorthand if you sticking on cli
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-parameters-prompting.html - autoprompt
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-output-format.html - output
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-returncodes.html - return codes >> use to determine state of command
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-wizard.html - wizard
- https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html - install
- https://docs.aws.amazon.com/cli/latest/userguide/getting-started-quickstart.html - config
- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html - config commands
- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html - environment variables
- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-retries.html - retry
- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-metadata.html - Simplifying credentials for EC2 scripts
- https://docs.aws.amazon.com/cli/latest/userguide/cli-usage-help.html - help

## RDS

### Principles
- https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_BestPractices.html
- Backup 1hr before maintenance
- Avoiding pinning
- Need to ensure PSQL client is installed on bastion host so you can ssh into RDS instance

### Questions
- What settings should I have for my postgres instance
- Things to consider when setting IDLE connection timeout
- Under which changes to underlying postgres instance would the maintenance impact all instances in a multiAZ deployment at the same time
- Running a DB instance as a Multi-AZ deployment can further reduce the impact of a maintenance event, because -
- When you modify the database engine for your DB instance in a Multi-AZ deployment, then Amazon RDS upgrades both the primary and secondary DB instances at the same time. In this case, the database engine for the entire Multi-AZ deployment is shut down during the upgrade.
- Should I configure read replicas to my standby instance? What will happen to the reads when my primary source fails over to the standby?
- How long should I retain automatic backups?
