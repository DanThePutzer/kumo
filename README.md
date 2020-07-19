# kumo
**kumo (雲)** - Japanese for ***"cloud"***

This represents my first dive into Infrastrucure as Code (IaC) as part of an effort to learn more about cloud services. Specifically, in this repo you can find  scripts to deploy a highly available web app on AWS CloudFormation. You can find more details about the exact infrastructure in the diagram below.

While I am already working with a variety of programming languages and technologies, I've never known much about how to actually deploy the things I build properly and at scale. Given that Amazon Web Services is currently the biggest player in the field by quite the margin, I decided to look into AWS and what it has to offer. Taking advantage of Udacity's free month I enrolled in the [Cloud DevOps Engineer](https://www.udacity.com/course/cloud-dev-ops-nanodegree--nd9991) Nano Degree, which helped get me up-to-speed on the basics of AWS quickly.

![undraw_going_up_ttm5](https://user-images.githubusercontent.com/25454503/87872457-1532b980-c9b9-11ea-8927-e4f871fd6c3c.png)

### Details

As mentioned earlier, what we are deploying here is a highly available web app. What does *highly available* mean? There are a few key components that make this infrastructure highly available:
- **Auto Scaling:** Taking advantage of AWS' AutoScaling Groups we are making sure that our web app isn't just running on one server, but multiple. As the name already says, auto scaling means that if load increases (for example due to more users visiting our web app) more servers will be spun up to handle the additional load. Furthermore, it also means that if one server goes bust another one will be launched automatically to replace it.
- **Load Balancing:** Given that we are running on multiple servers, but want users to access our web app through only one URL we need a load balancer to automatically distribute traffic evenly between the servers in our AutoScaling Group.
- **Multiple Availability Zones:** If the data center where our web app is hosted goes down, so does our app. We don't want that. To prevent such issues we are going for a setup spanning multiple availability zones. This means our AutoScaling Group will deploy servers in two separate data centers in different locations to ensure maximum redundancy and uptime.
- **Secure Network Setup:** To guarantee the safety of our customer's traffic and data, a proper network setup is necessary for our servers to be deployed into. All our server instances are running within private subnets, protected from outside traffic. Communication with the load balancer happens through secure NAT gateways.

Here's a little diagram outlining the complete infrastructure at hand created in LucidChart. As can be seen our **AutoScaling Group** contains a minimum of four servers spread over two **Availability Zones**.

<img width="1191" alt="InfrastructureDiagram" src="https://user-images.githubusercontent.com/25454503/87872770-c5a1bd00-c9bb-11ea-9f25-ecc4862858b0.png">


### Prerequisites

In order to run the CloudFormation scripts you need to have an AWS account set up and running. In case you've never used AWS you can sign up for one [here](https://aws.amazon.com).

You also need to set up the AWS CLI, [here](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html)'s a guide on how to get that going.

### Deployment & Usage

AWS CloudFormation work with something called stacks. A stack is basically a collection of infrastructure resources that are deployed in linked together. To keep the scripts as clean as possible, the network and server deployment were split into two separate stacks. Given that they are connected and the server stack depends on the network stack, please deploy the server stack only after successfull deployment of the network stack. Below you can find the commands to deploy the whole thing.

#### Creating a stack

```bash
# Deploying the network stack
aws cloudformation create-stack --stack-name 'YourStackName' --template-body file://network.yml --parameters file://networkParams.yml --region 'YourRegion' --capabilities CAPABILITY_IAM

# Deploying the server stack (after successful network deployment)
aws cloudformation create-stack --stack-name 'YourStackName' --template-body file://server.yml --parameters file://serverParams.yml --region 'YourRegion' --capabilities CAPABILITY_IAM
```

#### Updating a stack

```bash
# Updating the network stack
aws cloudformation update-stack --stack-name 'YourStackName' --template-body file://network.yml --parameters file://networkParams.yml --region 'YourRegion' --capabilities CAPABILITY_IAM

# Deploying the server stack
aws cloudformation update-stack --stack-name 'YourStackName' --template-body file://server.yml --parameters file://serverParams.yml --region 'YourRegion' --capabilities CAPABILITY_IAM
```

#### Deleting a stack

```bash
# Deleting a stack
aws cloudformation delete-stack --stack-name 'YourStackName'
```

#### Helper Scripts

The *helpers* directory contains bash scripts to make stack creation, updates and deletion a bit more comfortable. If you want to use those helper scripts you can do so as follows.

```bash
# Creating a stack with create.sh
helpers/create.sh YourStackName network.yml networkParams.json

# Updating a stack with update.sh
helpers/create.sh YourStackName network.yml networkParams.json

# Deleting a stack with delete.sh
helpers/delete.sh YourStackName
```

### Additional Resources

Seeing that you made it this far, here are some additional resources on CloudFormation. If you are interested in Infrastructure as Code (IaC), specifically on AWS, check out some of the links below to get up and running.

- [Official AWS CloudFormation Page](https://aws.amazon.com/de/cloudformation/)
- [AWS CloudFormation Docs](https://docs.aws.amazon.com/cloudformation/)
- [VSCode CloudFormation Plugin](https://marketplace.visualstudio.com/items?itemName=aws-scripting-guy.cform)

&nbsp;

![Daniel Putzer, 2020](https://i.ibb.co/LSxTsY3/dan.png "Daniel Putzer, 2020")  
<https://danielputzer.com>
