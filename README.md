
# Publishing an Amazon SNS message from Amazon VPC Privately

<br>
<br>
<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/afa95beb-280f-4d06-9b74-4e407389e6d5)

<br>

 
### Step 1: Create an Amazon EC2 Key Pair

<br>

A key pair is used to log in to an Amazon EC2 instance. It consists of a public key that's used to encrypt your login information, and a private key that's used to decrypt it. When you create a key pair, you download a copy of the private key. Later in this tutorial, you use the key pair to log in to an Amazon EC2 instance. To log in, you specify the name of the key pair, and you provide the private key.

<br>

### To create the key pair: 

<br>

1. Sign in to the AWS Management Console and open the Amazon EC2 console at https://console.aws.amazon.com/ec2/ 
2. In the navigation menu on the left, find the Network & Security section. Then, choose Key Pairs 
3. Choose Create Key Pair 
4. In the Create Key Pair window, for Key Pair name, type VPCE-Tutorial-KeyPair. Then, choose Create.

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/f0154935-22cf-48ac-b09e-01af923397ce)

<br> 

### Step 2: Create the AWS Resources

<br>

To set up the infrastructure that supports this tutorial, you use an AWS
CloudFormation template. A template is a file that acts as a blueprint for building
AWS resources, such as Amazon EC2 instances and Amazon SNS topics. 

<br>

The template for this tutorial is provided on GitHub for you to download.
You provide the template to AWS CloudFormation, and AWS CloudFormation
provisions the resources that you need as a stack in your AWS account. 

<br>

A stack is a collection of resources that you manage as a single unit. When you finish the
tutorial, you can use AWS CloudFormation to delete all of the resources in the
stack at once. These resources don't remain in your AWS account, unless you
want them to.

<br>

### The stack for this tutorial includes the following resources:

● A VPC and the associated networking resources, including a subnet, a security
group, an internet gateway, and a route table.

<br>

An Amazon EC2 instance that's launched into the subnet in the VPC.

<br>

● An Amazon SNS topic.

● Two AWS Lambda functions. These functions receive messages that are published
to the Amazon SNS topic, and they log events in CloudWatch Logs.

● Amazon CloudWatch metrics and logs.

● An IAM role that allows the Amazon EC2 instance to use Amazon SNS, and an IAM
role that allows the Lambda functions to write to CloudWatch logs





### To create the AWS resources

1. Download the template file from their Github site
2. Sign in to the AWS Management Console
3. Choose Create Stack
4. On the Select Template page, choose Upload a template to Amazon S3,
select the file, and choose Next
5. On the Specify Details page, specify stack and key names:

    a. For Stack name, type VPCE-Tutorial-Stack
    b. For KeyName, choose VPCE-Tutorial-KeyPair
    c. For SSHLocation, keep the default value of 0.0.0.0/0

<br> 

 ![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/aa0e1366-fc5c-48c8-8695-f4f9ea5ae5e4)

<br>
 
![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/93c69a9a-ff95-4ae3-a524-3c36893993e9)

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/899e0be9-6074-4822-9895-3e3e1eda3010)

<br>


  d. Choose Next
  
6. On the Options page, keep all of the default values, and choose Next
7. On the Review page, verify the stack details
8. Under Capabilities, select the check box that acknowledges that AWS
CloudFormation might create IAM resources with custom names
9. Choose Create

<br>

The AWS CloudFormation console opens the Stacks page. The VPCE-Tutorial-
Stack has a status of **CREATE_IN_PROGRESS**. In a few minutes, after the
creation process completes the status changes to **CREATE_COMPLETE**.

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/0b7e8788-599e-4d78-8fac-009da652bdf9)

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/abc4cf10-0fff-4712-8553-b4c44760b952)

<br>
 

### 3: Confirm that your Amazon EC2 Instance lacks internet access

<br>

The Amazon EC2 instance that was launched in your VPC in the previous step
lacks internet access. It disallows outbound traffic, and it's unable to publish
messages to Amazon SNS. Verify this by logging in to the instance. Then,
attempt to connect to a public endpoint, and attempt to message Amazon SNS.
At this point in the tutorial, the publish attempt fails. In a later step, after you
create a VPC endpoint for Amazon SNS, your publish attempt succeeds.

### To connect to your Amazon EC2 instance:

<br>


1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/
2. In the navigation menu on the left, find the **Instances** section. Then, choose
**Instances**
3. In the list of instances, select **VPCE-Tutorial-EC2Instance**
4. Copy the hostname that's provided in the **Public DNS (IPv4)** column
5. Open a terminal. From the directory that contains the key pair, connect to
the instance by using the following command, where instance-hostname is
the hostname that you copied from the Amazon EC2 console:

### To verify that the instance lacks internet connectivity:

<br>

In your terminal, attempt to connect to any public endpoint, such as
amazon.com:

 <br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/575dc2e2-40da-4216-94da-6b70b94d810a)

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/ca2fb225-a3f4-476f-b33c-0628b6772b03)

<br>


 


### Step 4: Create an Amazon VPC Endpoint for Amazon SNS

<br>

To connect the VPC to Amazon SNS, you define an interface VPC endpoint.
After you add the endpoint, you can log in to the Amazon EC2 instance in your
VPC, and from there you can use the Amazon SNS API. You can publish
messages to the topic, and the messages are published privately. They stay
within the AWS network, and they don't travel the public internet.
Note that the instance still lacks access to other AWS services and endpoints
on the internet.

### To create the endpoint:

<br>

1. Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.
2. In the navigation menu on the left, choose **Endpoints**.

<br>
 
![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/42e42752-0bc2-4bba-b3f6-36f75ac173b6)

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/41001bb1-65b3-4224-ba93-796f8acca982)
 
<br>

3. Choose Create **Endpoint**.
4. On the **Create Endpoint** page, for the Service **category**, keep the default
choice of **AWS services**.
5. For **Service Name**, choose the service name for Amazon SNS.
The service names vary based on the chosen region. For example, if you
chose US East (N. Virginia), the service name is
**com.amazonaws.us-east-1.sns**.
6. For **VPC**, choose the VPC that has the name **VPCE-Tutorial-VPC**
7. For **Subnets**, select the subnet that has VPCE-Tutorial-Subnet in the
subnet ID

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/225ca217-8b72-41dd-93c9-cb9c64da8bcd)

<br>

8. For **Enable Private DNS Name**, select **Enable for this endpoint**.
9. For the Security **group**, choose the **Select security group**, and select
the one named **VPCE-Tutorial-SecurityGroup**

 <br>
 
![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/1b43ec64-aa67-47bf-a77d-f0b0681946ee)

<br>

10. Choose **Create endpoint**. The Amazon VPC console confirms that a
VPC endpoint was created.

<br>
 
![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/e375ac57-1fc1-4c44-9df6-e557e8751d18)

<br>

### Step 5: Publish a message to your Amazon SNS topic

Now that your VPC includes an endpoint for Amazon SNS, you can log in to the
Amazon EC2 instance and publish messages to the topic.

### To publish a message:

1. If your terminal is no longer connected to your Amazon EC2 instance,
connect again
2. Run the same command that you did previously to publish a message to
your Amazon SNS topic. This time, the publish attempt succeeds, and
Amazon SNS

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/22eb8edc-05d5-443d-b5a3-d27555556e65)

<br> 

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/04886be7-f658-4192-9d6a-f7cf9dc61896)


<br>


### Step 6: Verify your message deliveries

When the Amazon SNS topic receives a message, it fans out the message by
sending it to the two subscribing Lambda functions. When these functions
receive the message, they log the event to CloudWatch logs. To verify that your
message delivery succeeded, check that the functions were invoked, and check
that the CloudWatch logs were updated.

<br>

### To verify that the Lambda functions were invoked

<br>

1. Open the AWS Lambda console at
https://console.aws.amazon.com/lambda/.
2. On the **Functions** page, choose **VPCE-Tutorial-Lambda-1**.
3. Choose **Monitoring**.
4. Check the **Invocation count graph**. This graph shows the number of timesthat the Lambda function has been run.

<br>

The invocation count matches the number of times you published a message to
the topic.

<br>

 ![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/3c511800-3ef9-4fc5-8b7c-719aa6416723)
 
<br>

### To verify that the CloudWatch logs were updated:

<br>

1. Open the CloudWatch console at
https://console.aws.amazon.com/cloudwatch/.
2. In the navigation menu on the left, choose **Logs**.
3. Check the logs that were written by the Lambda functions:
    a. Choose the **/aws/lambda/VPCE-Tutorial-Lambda-1/** log group.
    b. Choose the log stream
    c. Check that the log includes the entry From SNS: Hello.

<br>

![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/c6bce943-9509-43ca-b01b-510c4dd2a64e)

<br> 

d. Choose **Log Groups** at the top of the console to return the **Log Groups**
page. Then, repeat the preceding steps for the /aws/lambda/VPCE-Tutorial-Lambda-2/ log group.

 <br>

 ![image](https://github.com/virajmate7776/Publishing-Private-Message-SNS-VPC/assets/117629972/1bcce9c4-3cc3-49ce-a0d6-a04a628ee9cc)

<br>

The message was published privately without being exposed to the
public internet.

<br>

### Step 7: Clean Up

<br>

Unless you want to retain the resources that you created for this tutorial, you
can delete them now. By deleting AWS resources that you're no longer using,
you prevent unnecessary charges to your AWS account.
First, delete your VPC endpoint by using the Amazon VPC console. Then,
delete the other resources that you created by deleting the stack in the AWS
CloudFormation console. When you delete a stack, AWS CloudFormation
removes the stack's resources from your AWS account.

<br>

### To delete your VPC endpoint:

<br>

1. Open the Amazon VPC console at https://console.aws.amazon.com/vpc/
2. In the navigation menu on the left, choose **Endpoints**
3. Select the endpoint that you created
4. Choose **Actions**, and then choose **Delete Endpoint**
5. In the **Delete Endpoint** window, choose **Yes**, **Delete**
The endpoint status changes to **deleting**. When the deletion completes, the
endpoint is removed from the page.

<br>

### To delete your AWS CloudFormation stack:

<br>

1. Open the AWS CloudFormation console at
https://console.aws.amazon.com/cloudformation.
2. Select the stack **VPCE-Tutorial-Stack**.
3. Choose **Actions**, and then choose **Delete Stack**.
4. In the **Delete Stack** window, choose **Yes**, **Delete**.
The stack status changes to **DELETE_IN_PROGRESS**. When the deletion
completes, the stack is removed from the page.

