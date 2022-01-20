# Table of Contents

# Overview
This document covers AWS services in detail. It was not intended to be a reference for any one AWS certification, but as of now the content covers most of what exam-takers are expected to know in order to pass the Developer Associate and Solutions Architect Associate exams. This information is guaranteed to be current as of 2019 however most information is current as of 2021.

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some statements may come from various textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject.

# IAM

# S3

# EC2

# DynamoDB
- High performance NoSQL database system for AWS. It supports key-value data models with support for large objects such as json, html, and xml.

- It is a serverless service that integrates well with lambda.

- Machines running DynamoDB use SSD storage for fast read-writes.

- DynamoDB replicates your database across 3 different availability zones

## Pricing
- Charges apply for reading, writing, and storage of data. 

- The more frequently your application reads and writes to the database, the more capacity units your database will require. You can either specify how many capacity units you want to provision in advance or let AWS scale this amount automatically for you via on-demand capacity.

- Newer applications should typically use the on-demand-capacity since you likely won't have any idea how congested your application is going to be. Established applications with predictable traffic may save more using provisioned capacity.

## Terminologies

- Rows in DynamoDB are called `items`

- Each item has many `attributes` (columns)

- Earlier we stated that DynamoDB supports key-value data models. `Keys` are the column headings. For example a DynamoDB database containing information about a game may have columns for player name, currency, and level. These are all keys. `Values` refer to the actual values that populate these columns.

- `primary key` is just the same here as it is in other database query languages. It is a key used to uniquely identify an item in a table.

- Because DynamoDB is NoSQL, there needs to be a way to resolve cases where a data table is not likely to have any one attribute that is unique across all items. In SQL we would use foreign keys and joins to resolve these situations. DynamoDB uses what is called `composite keys` which takes a combination of 2+ attributes for an item that is guaranteed to be unique and makes keys out of their values.

- DynamoDB also has `indexes` to make querying faster for specific columns in a table. There is a limit of 20 indexes per table which can be expanded by contacting AWS.

- `Local secondary indexes` must be created during table creation and cannot be modified or deleted. It uses the same partition key but a different sort key to your original table.

- `Global secondary indexes` are more flexible than their local counterparts. They do not have to be created at creation time and can be modified or deleted. You can have a different partition key and different sort key to your original table.

- a `query` is a statement that gets specific items from your database table using the partition key attribute. These are highly configurable and use DynamoDB's query langugage to filter for specific outputs

- a `scan` is an operation that examines every item in the table and returnins all attributes. In other words it "dumps" your entire table. You can then use a projection expression paramter to filter through this dump. Scans are more costly in terms of performance than a query due to this dumping.

- the `page size` is the number of results returned from a scan in a single operation. Reducing the page size can help improve the performance of scans carried out on large data sets.

## Types of Reads

- Eventually consistent reads: one of the two options for DynamoDB. Updates to the database will be reflected in all 3 availability zones within 1 second of the upload. This is best for read performance.

- Strongly consistent reads: one of the two options for DynamoDB. Updates to the database will be instantly reflected in all 3 availability zones. This is best if you need more consistent reads.

- ACID Transactions: another option for DynamoDB suitable for financial transaction processing. It follows an all or nothing approach, so it disallows partial completion of transactions. You don't want your customer being charged for a transaction that did not succeed.

## Throughput Management
- when planning to use provisioned throughput, you can estimate how many capacity units you need to provision using simple math.

- Write capacity: 1 x 1kb write / s

- Strong read capacity: 1 x 4kb read / s

- Eventual read capacity: 2 x 4kb reads / s

- how many read capacity units will you need if your application's database needs to read eighty 3kb items per second? The reads must be strongly consistent reads.

1. Math.round(3kb / 4kb) -> 1

2. 1 * 80 reads / s -> 80 capacity units

- if we only needed eventually consistent reads, we would only need to provision 40 capacity units

- if your application has insufficient read or write capacity, `ProvisionedThroughputExceededException` will be thrown. Resolve these issues by increasing the number of capacity units you provision. If reads are causing the issue, you may want to look into DAX.

- AWS SDK will automatically retry requests that were rejected due to this exception. Retries are initiated according to the `exponential backoff` model which means there will be a progressively longer wait between retries. This model is applied to other AWS services as well such as S3, SES, and CloudFormation

## Security
- Access to DynamoDB is controlled using IAM. It is common to use a special IAM condition to restrict users from accessing any records other than their own.

- The condition is placed just after the Resource key and above the dynamodb:Attributes key.

```javascript
// the partition key value must match the user's ID
"Condition": {
  "ForAllValues:StringEquals": {
    "dynamodb:LeadingKeys": [
      "${www.mygame.com:user_id}"
    ],
  }
}
```

## DynamoDB Accelerator
- Also called DAX, this additional feature speeds up databse **reads** by using a special write-through cache called a DAX cluster. The DAX cluster operates like any other cache, which stores frequently or recently accessed data closer to the CPU.

- DynamoDB can use a similar caching service called ElastiCache however DAX is specifically optimized for DynamoDB whereas ElastiCache is not.

- DAX is suitable for things like game servers and online reatail websites during busy times such as the holidays where there is likely to be a high volume of reads.

- DAX is not worth its price however if your database does not require microsecond response times and if your database is more write-heavy than read-heavy (since DAX only accelerates reads). You won't notice a performance boost if your application doesn't perform that many database reads in the first place.

- Also, DAX only supports eventually consistent reads, so applications that use strongy consistent reads will not be able to use DAX.

## Expring Data
- All database items have a time to live or TTL. Once the TTL is reached, the item will be marked for deletion. Items marked for deletions will be removed in the next 48 hours.

- This feature greatly reduces costs by allowing you to automatically delete data that is no longer relevant to your application. Such data might include session data JSON files and event logs.

## Streams
- DynamoDB streams is an optional feature that records all operations made to the database. These operations can be used as triggers for lambda functions. For example, if a user makes a payment in your application, the subsequent write to DynamoDB can be used to trigger a lambda that sends an automated email to the user confirming their payment.

- All of these records are encrypted at rest and stored for 24 hours.

# VPC

# Route 53

# Serverless
 - Serverless allows users to run code in the cloud without the hassle of having to manage servers. Eliminating this overhead frees up more time for businesses to focus on coding rather than configuring infrastructure. Serverless technologies include...

 - S3: object and storage web hosting

 - DynamoDB: a fully managed NoSQL database

 - API Gateway: publish and secure APIs at any scale

 - Lambda: enables running code as functions without the need to provision any servers

 - SQS: message queue service that allows decoupling and scaling of applications
 
 - SNS: messaging service for sending text messages, emails, and mobile notifications

 - All of these serverless technologies are scalable and support logging to cloudwatch

 - To Illustrate, let's say you connect your web browser to a serverless subscription video hosting website. First, your browser sends a request to API Gateway and the gateway routes your request to the appropriate lambda function. The lambda function you are routed to depends on the service you request. Requesting to access the login feature will route you to a different lambda than requesting to access the video player. The video files themselves may be stored in an S3 bucket which only becomes readable to paying subscribers through an authentication service.

 ## Lambda
 - lambda is basically where you upload your code on an AWS managed architecture. It takes care of everything required to run your code, including the runtime environment and supports Java, Go, PowerShell, Node.js, C#, Python, and Ruby. It's no wonder why these are popular industry languages for writing backend code.

 - code can be directly copy pasted into the lambda code editor, uploaded from a zip, or deployed from a CloudFormation template

 - Lambda is paid for by number of executions, the duration of those executions, and the amount of memory used. Your first 1 million executions / month are free. Afterwards you are charged only 20 cents per month for each additional 1 million executions.

 - The service charge for execution duration and amount of memory is calculated by multiplying the memory of the function in GB by the runtime of the function in ms and then multiplying that by $0.00001667. Even a 10G function that runs for 1000ms is going to barely even cost a penny, making serverless a more cost effective choice than EC2 for running code.

 - Lambda functions can be triggered by events, such as changes to data in an S3 bucket, a table in DynamoDB or even user-triggered requests via HTTP endpoint. There are countless other AWS services that can trigger lambda functions

 - Logs from lambda functions are stored in AWS CloudWatch

  - Oftentimes, real applications will be composed of multiple lambda functions where one function's output serves as the input for the next function. Each lambda in this case can be seen as a single "step". AWS provides an easy way to visualize the workflow of these steps. The state of each step is logged to make it easy to pinpoint where and where errors are ocurring.

 - When creating a new lambda in AWS, it must be assigned an IAM policy to define its permissions. If one is not specified, the default IAM for lambda is used which only allows the lambda to send logs to cloudwatch.

 - You can have multiple versions of the same lambda. The latest version will be annotated with $LATEST. You can use Aliases to point your application to a specific version instead if you do not want to use the latest version. Aliases are just custom names you give to your versions. If you are pointing to an alias instead of $LATEST however, AWS will not default to using the newest code you upload to that lambda.

 - Know that by default there is a limit to the number of concurrent executions for any lambda. You will know you hit this 1,000 concurrent execution limit when your app starts throwing 429 HTTP error codes. To resolve this, contact AWS to extend your limit or purchase Reserved Concurrency for your most critical functions. Reserved Concurrency allows you to define exactly the number of concurrent executions you want for a particular lambda function.

 - It is possible to allow Lambda to access resources that are protected by a private VPC such as a relational database or EC2 instance. Note that this feature is disabled by default. Ensure you have the private subnet ID and security group ID from the VPCs config on hand.

 ## API Gateway

 - API gateway allows you to publish, maintain, monitor, and secure APIs at any scale, but what even are APIs.

 - APIs are application programming interfaces that are resopnsible for passing data between the user and backend services. They are essentially just lines in your code that do this specific task.
 
 - When you enter travel details into a travel planning website, the website makes a series of API calls to display results (about flights, ticke prices, hotel options etc.) to you.

 - Of particular interest to AWS users are RESTful APIs. REST stands for Representational State Transfer. These APIs are optimized for serverless and web applications

 - API Gateway supports throttling. This feature can be used to prevent your web application from being overloaded with too many requests.

 - If in any case you update your API Gateway and something goes wrong, AWS supports rolling back your gateway to a previous state

 - APIs can be imported from solutions outside of AWS using external definition files like OpenAPI.

 - When dealing with legacy applications which use SOAP, which returns XML, you can use API gateway to convert XML to JSON or have API Gateway configured as a SOAP web service passthrough

 - API caching can be used to improve the performance of your APIs by storing API outputs into faster memory. Cached responses will live for 5 minutes default


 ## X-Ray

 - X-Ray is a convenient tool to help developers debug distributed software applications. It provides a visual roadmap of your application that tracks all the possible locations where requests are routed, supplying information like latency, HTTP status codes, and errors.

 - X-Ray integrates with DynamoDB, Lambda, API Gateway, Elastic Load Balancer, S3, and many other AWS services. Monitored applications can be on EC2, ECS, on-premise, or Elastic Beansalk.

 - X-ray agent must be installed on an EC2 instance. You must use the X-ray SDK and X-ray daemon to send data. The X-Ray daemon must be placed inside its own Docker iamge running alongside the application


# CloudWatch

# KMS And Encryption
- KMS (Key Management Service) is used to encrypt your sensitive data on AWS. It fully integrates with most AWS services including Dev Tools, S3, RDS, DynamoDB, Lambda, EBS, EFS, and CloudTrail.

- All keys are single-region by default. They are not shared across multiple regions.

- There are two main types of keys in AWS. The first is an `AWS managed key`. These keys are created, managed, and used on your behalf by an AWS service integrated with AWS KMS. For example, starting an instance of AWS Lambda will instruct AWS to automatically create an AWS managed key for lambda.

- The other is a `customer managed keys` (CMK), which are keys created by you, the AWS console user. They can be either `symmetric` or `asymmetric`. Symmetric keys are single keys which are used for encrypt and decrypt operations in AWS. Asymmetric keys are private / public key pairs that can be used to encrypt data outside of AWS or to sign / verify.

- CMKs must be assigned an `administrative permissions user` (a user who can administer keys but not use them) as well as `key users` (who cannot administer keys but can use them). A key policy JSON is generated for every CMK. You should also give the key an alias, or a simplified name that you can use in your application to refer to the key.

- All keys `cannot be exported` and must stay within the KMS service for obvious security reasons

- All keys have state, meaning they can be enabled, disabled, and marked for deletion at the control of the KMS administrator

- CMKs can be deleted any time, but they will take up to a week to delete. This feature is implemented in case you ever change your mind and want to restore the key. Deleting a key unintentionally can be disastrous as resources that were encrypted with that key will be permanently inaccessible.

## KMS API
- To encrypt a file, we first need to ssh into our AWS CLI EC2 and aws config to provide the access keys and login of the key user. We will upload the file to EC2 and then use a CMK to encrypt the file.

- Below is the terminal command used to encrypt a plain text file. In this example we use --plaintext but for file types other than .txt a different argument may need to be used. Encrypting will convert the file to an unreadable format.

```bash
aws kms encrypt --key-id [KEYID] --plaintext fileb://myfile.txt --output text -- query CiphertextBlob | base64 --decode > myencryptedfile.txt
```

- To decrypt the data use this shell script. Decrypting will convert the encrypted file back to its readable format.

```bash
aws kms decrypt --ciphertext-blob file://myencryptedfile.txt --output text --query Plaintext | base64 --decode > mydecryptedfile.txt
```

- In many cases, for security reasons you may want to decrypt the file for reading wihout saving it to memory. There is a command that allows you to somewhat perform this action, however it requires you to output a re-encrypted version of the file. This command can also be used to re-encrypt a file using a differnt key than the one originally used to encrypt it.

```bash
aws kms re-encrypt --destination-key-id [KEYID] --ciphertext-blob fileb://myencryptedfile.txt | base64 > reencrypted.txt
```

- For additional security, you can force AWS to automatically rotate your cmk key so that after every year, all your resources are re-encrypted using a new cmk. You can use the first command to enable this feature for a key and use the second command to check whether this feature is enabled for a particular key

```bash
aws kms enable-key-rotation --key-id [KEYID]
```

```bash
aws kms get-key-rotation-status --key-id [KEYID]
```
- Since cmk keys can only encrypt / decrypt a maximum of 4kb of data, you must use your customer key to access a `data key` which will allow you to encrypt much larger amounts of data. This encryption model is known as `envelope encryption`. The data key is not stored anywhere on KMS.

```bash
aws kms generate-data-key --key-id [KEHYID] --key-spec AES_256
```

- Envelope encryption exists for performance reasons, particularly with respect to the network. Encrypting large amounts of data using just the customer key will require lots of data to be sent into KMS over the network. 


# Developer Theory

## CI/CD
- CI/CD stands for continuous delivery, continuous deployment/delivery

- It is a developer best practice that is based around automating as much of the build, test, and deployment process as possible. Large companies like Google, AWS, and Facebook use CI/CD to deliver thousands of code changes a day.

- AWS provides three tools to support CI/CD: Code Commit, Code Build, and Code Deploy

## Code Commit
- AWS version control solution. It functions like a git repository.

- As with any other version control you should never make changes directly to the master branch. You should always make a separate developer branch as a clone of the main branch and then make your edits on that. Afterwards you can merge this branch back with the master provided there are no conflicts.

- You can configure Code Commit to send messages via CloudWatch or SNS to send notifications to users whenever changes to a repository are made.

## Code Build
- Compiles code, produces packages, and runs tests that are ready for deployment

## Code Deploy
- Automates code deployment to any instance, including EC2, Lambda, and on-premise

- In general, there are two approaches to deploying code: In-place and blue / green 

- In most cases, large applications will run on multiple EC2 instances. In the `in-place` method, you stop one instance and install updates, or revisions to it. You can configure code deploy to instruct the load balancer to stop sending requests to the deactivated instance. Once updates are finished you re-activate the instance.

- In the `blue/green` method, you first deactivate your old release ("blue" instances), provision new instances ("green" instances) for your new release and configure your load balancer to route all traffic to the green instances. Once you have done enough testing to confirm that your new release is working properly, you can terminate the blue instances.

- Rolling back to earlier versions of your code can be difficult when using the in-place method. The only solution, which is very time-consuming, is to re-deploy the previous version.

- As long as you have not already terminated your blue instances, rolling back your versions in the blue / green method is as easy as using code deploy to configure your load balancer to route all traffic back to the blue instances.

- The disadvantage of blue / green is that you will need to pay for additional instances during experimental release stages. Otherwise blue green deployment is almost always the much safer deployment option for development.

- To start using code deploy, you first need to identify where your code is stored (usually S3) and give your EC2 instance access to that service through IAM service roles. The same must be done for code deploy in order for it to have permissions to run installations on the EC2 instance.

- EC2 instances should use tags. These can be added when you are first creating the instance. Code Deploy will use these tags to identify which instances are holding what code.

- Next, code deploy must be installed on the EC2 instance. In amazon linux, the installation script is as follows.

```bash
sudo yum update
sudo yum install ruby
sudo yum install wget
cd /home/ec2-user
wget https://aws-codedeploy-eu-central-1.s3.amazonaws.com/latest/install
chmod +x ./install
sudo ./install auto
sudo service codedeploy-agent status
```

- In your cli, you should have only your app as the top level directory. Under your app directory, you should always have an appspec.yaml folder, your code src folder, and a scripts folder.

- appspec.yaml will access the scripts in the scripts folder to carry out the deployment process. A sample appspecs.yaml file is shown below with the src folder being replaced with just a single index.html file for simplicity.

```yaml
version: 0.0
os: linux
files:
  - source: /index.html
    destination: /var/www/html/
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies.sh
      timeout: 300
    - location: scripts/start_server.sh
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server.sh
      timeout: 300
      runas: root
```

## Code Pipeline
- Automates the entire process of building, testing and deploying your application every time changes are made to the code

# AWS Linux CLI