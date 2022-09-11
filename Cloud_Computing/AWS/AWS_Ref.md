# Table of Contents

[Overview](#overview)

[Identity Access Management](#iam)

[S3](#s3)

[EC2](#ec2)

[Virtual Private Cloud](#vpc)

[Route 53](#route-53)

[Serverless Technologies](#serverless)

[DynamoDB](#dynamodb)

[Encryption](#kms-and-encryption)

[Code Integration and Deployment](#developer-theory)

[Command Line](#aws-linux-cli)

# Overview
This document covers AWS services in detail. It was not intended to be a reference for any one AWS certification, but as of now the content covers most of what exam-takers are expected to know in order to pass the Developer Associate and Solutions Architect Associate exams. This information is guaranteed to be current as of 2019 however most information is current as of 2021.

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some statements and examples may come from various textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject.

[Return To Top](#table-of-contents)

# IAM

## Policy Types
- There are two general "classes" of policies in AWS: identity-based policies and resource based policies

- `Identity-based policies` are ones that are assigned to entities within AWS (users, groups, and roles)

- `Resource-based policies` are ones that are assigned to AWS services like S3, SQS, EC2 etc.

## Entities
- There are three types of entities that you can assign policies to in IAM: Users, Groups, and Roles. It is crucial to know the distinction between these three entities.

- `Users` are self-explanatory. They are representations of the people or services that interact with AWS

- `Groups` are simply collections of users.

- `Roles` are just holders for policies that can be assigned to users and groups. Unlike users and groups however, roles can also be used to hold resource-based policies that are assigned to AWS services like S3 and EC2. Any user, group, or resource instance can only have one role assigned to it at a time. Multiple users, groups, or instances can have the same role however.

## Types of Identity-Based Policies
- There are three categories of policies used to define user access permissions within AWS: AWS managed policies, customer managed policies, and inline policies.

- `AWS managed policies` are pre-formulated by aws. These are usually identified by the orange box icon next to the IAM policy name. Some examples of AWS managed policies include S3ReadOnlyAccess, AWSCodeCommitPowerUser, and AmazonDynamoDBFullAcces. These policies are often commonly used ones and amazon provides these to save you the time and effort of writing the JSON yourself. You can attatch an AWS managed policity to multiple users, groups, or roles in the same AWS account or even across different AWS accounts. You cannot edit AWS managed policies

- `Customer managed policies` are custom policies that you create and administer. You can apply these to multiple users, groups, and roles, but only within your own account. When making your own custom policies, it is helpful to copy over the JSON of an existing AWS Managed policy and make modifications to it.

- `Inline policies` are embedded within the user, group or role to which it applies. These policies cannot be applied to multiple users groups or roles and users outside of the group or role will not be able to see the inline in the policy search box. Inline policies will be deleted along with the users, policies, or roles they are attached to. These are typically only used over customer managed policies when you do not want to risk inadvertently assigning the policy to another user, group, or role. For this reason, you need to be in the user, group or role you want to create the inline policy for before you have the option to create the inline policy.

## Cognito and Web Identity Federation
- `Web identity federation` is used to dynamically distribute temporary AWS access credentials to users of your mobile app. These credentials are mapped to an IAM role that has only the permissions the user needs to use the app

- Identity federation allows users to sign up for or sign into your application using credentials from a trusted identity provider called an `IpD`. Well known 0auth IpDs include Facebook, Amazon, Twitter, Github, and Linkedin. This is the AWS recommended authentication method for mobile apps.

- When a user signs into your app via google, they acquire a token that can be exchanged for temporary AWS credentials. These credentials are not stored locally on the device, which is favorable for security reasons.

- Amazon `cognito` takes care of setting up web identity federation for your mobile app so that you do not have to code your own authentication system that integrates with Facebook, Google, Github etc. You don't even need to code your own login page, though you can customize it if you'd like.

- It also helps sycronize your users app data across all of their devices. This is accomplished via a technology called `Cognito Push Synchronization`. It sends silent SNS notifications to all devices associated with a given user identity whenever data stored in the cloud changes.

- `user pools` are directories used to manage sign-up and sign-in functionality for mobile and web applications. The user pool is the entity that gives the user the access token.

- `identity pools` are what provide the temporary AWS credentials enabling access to services like S3 or Dynamo DB. The identity pool is the entity that gives the user AWS credentials and an IAM role once they recieve a token.

- To get started with cognito, you first need to make a user pool. Much like any IAM group, things like a password policy, MFA, and verification method can be configured for a user pool.

- `App Clients` are used to call the various APIs that handle creating new user accounts and signing in users. When setting up an app client, ensure you select `generate client secret`. For the app client, you will also need to set up a `callback URL` (the url that the user will be redirected to after signing in) and a `sign out URL` (the url that the user will be redirected to after signing out). You will also want to select `Authorization code grant` and `implicit grant` as these will provide an authorization code and JWT token respectively.

- Under the hood, cognito actually uses the `STS AssumeRoleWithWebIdentity` API to return temporary security credentials for mobile users authenticated by an IpD. For users not on mobile however, you will need to use STS explicitly to allow users to login or sign up using IpDs. In this scenario, STS does what the identity pool typically does: return AWS credentials in exchange for a JWT token.

## Cross Account Access
- Allows you to share resources in one account with users in a different account that you own. You must create a role in one account to allow acces and grant permissions to users in the other account.

- There are many reasons why you may want to do this. In many companies, separate AWS accounts will be made for developers, deployers, testers etc. One team may need access to the resources in another team's AWS account.

[Return To Top](#table-of-contents)

# S3
- Object storage suitable for documents, images, video media, code files, and webpages

- Different from a database which is for storing datatypes like employee names, phone numbers, street addresses, account balances, anything that can be represented as a primitive.

- S3 cannot be used to run databases or operating systems as it is not a computing environment

- Features unlimited object storage and storage volume, so there is no need to know how much storage you need to provision upfront. There is a 5TB size limit for each object however.

- Data in S3 has high `availability` as it is replicated across multiple storehouses

- Data in S3 is also `durable` as there is a 99.99999999999% (11 9's) chance that an object will not be corrupted

## Costs
- Cost of storing objects on S3 depends on the S3 tier the objects are stored at as well as how many TB worth of objects you store a month.

- For example, the first 50TB of objects in a month will charge you $0.023/GB whereas the next 450TB will charge you 0.022/GB

## Buckets
- S3 objects are stored in what AWS calls `buckets`. This is just an address where files are stored. It's comparable to a directory address on your computer's filesystem

- S3 buckets can contain multiple objects. Unlike folders in a filesystem, buckets cannot be nested within buckets

- S3 buckets share universal namespace, so they must have unique names. This is because they can be retrieved via a web address. A typical address looks like `https://bucket-name.s3.Region.amazonaws.com/object-name`

- Predictably, uploading an object to an S3 bucket returns an HTTP 200 code if the upload was successful

## Key-Value Store Model
- In AWS, an object's name can be more formally referred to as a `key` (like profile.png)
- The `value` associated with a key, on the other hand, is the actual sequence of bytes that composes the object
- Each object has an associated version ID since S3 objects can be versioned
- S3 objects can also contain metadata, or data about data. Useful metadata may include last-modified date, file author, and content-type

## Tiers
- Applied at the object level, not the bucket level

- **`S3 Standard`** stores data redundantly across at least 3 availability zones. It is the default storage class, suitable for most workloads. Objects that reequire `frequent` access should be stored in this tier. 

- **`S3 Standard-IA`** where IA stands for infrequent access is suitable for objects that may only be accessed a few times a month. Low per-GB storage price and per-GB retrieval fee however you must store you data in IA for a minimum of 30 days. Data is still replicated across at least 3 availability zones

- **`S3 One-Zone-IA`** is similar to Standard-IA, but the data is stored reduntantly in a single availabilty zone rather than multiple. It costs 20% less than Standard-IA at the cost of lower availability (but the same 11 9's of durability). Objects stored here are still subject to the 30 day minimum

- **`Glacier`** is cost optimised for data accessed a few times a year. Retrieval times are long, ranging from 1min-12hrs and you are charged for each retrieval. Objects stored here must remain for a minimum of 90 days. 99.99% durability and 11 9's availability

- **`Glacier Deep Archive`** is even more cost optimised for rarely accessed data. Great for archives that need to be kept for compliance purposes. Retreival time is 12hrs on average. Objects stored here must remain for a minimum of 180 days. 99.99% durability and 11 9's availability

> There is an object retreival fee for all tiers except S3 Standard

>All tiers feature 11 9's durability and only S3 One-Zone-IA does not feature 99.99% availability (99.5% instead)

> All tiers feature data redundancy across at least three AZs except S3 One-Zone-IA

## S3 Intelligent Tiering
- A service that automatically moves your data to the most cost-effective tier based on how frequently each object is accessed. It is best used for unknown access patterns

- Increases monthly fee by $0.0025 for every 1000 objects stored.

- Guarantees 99.99% availability and 11 9's of durability

## Lifecycle Management
- You can define rules to move objects that are not frequently used into lower tiers of storage or delete them altogether. These rules are highly customisable and can be configured to run automatically according to specific criteria

## Versioning and Deleted Objects
- Versioning in S3 allows you to rollback to previous versions if you accidentally delete or modify an object

## Security Features
- A bucket can be configured to encrypt all new objects when they are stored in the bucket

- By default, all buckets are created with `private` access level, meaning only the bucket owner has read/write priviledges. Also, nobody can access them on the web. To grant public access, you must configure it manually via access control lists or bucket policies.

- S3 supports `acccess control lists`, which are used to define whicch AWS accounts or groups have access to the bucket or individual object and the type of access.

- `Bucket Policies` can also be applied to buckets to specify which actions a user is allowed to perform on all of the objects in the bucket (ex. allowing a team to PUT but not DELETE objects in a bucket). Bucket policies do not apply permissions to individual objects.

- Logs can be generated for each S3 bucket whenever someone request to upload, read, or delete an object in the bucket. `S3 Access Logs` must be enabled manually.

## On the AWS Dashboard
- S3 on the AWS Dashboard shows all your buckets across all availability zones.

- You can enable or disable versioning for buckets. Although the buckets themselves cannot be versioned, the objects held within them can. Extra protection from unintended modifications and deletions to your objects

- You may add tags to a bucket (you can also add tags to individual objects). These are simply used to organise your buckets and objects into categories that can be filtered for later.

- You can disable or enable encryption and select the encryption type. The default is SSE-S3. SSE-KMS can be used instead and is suitable for those who wish to have more control over how their keys are managed and audited.

- You also have the option to enable write once read many (WORM) which prevents your objects from being written after their initial upload, providing additional safeguards against accidental modification. The effect duration of WORM can be configured.

- After creating the bucket, uploading any object to the bucket will prompt you to specify the storage tier for the object as well as the access control list.

- Clicking on an object on the Dashboard will bring up its information, including its web address. Try clicking on the web address to simulate the outcome when a public user is trying to access it. This is great for quickly testing if your buckets have the access levels you want them to have.

## Making an Object Public
- First, check the bucket policy for the bucket the object is stored in. It must be configured to allow public access and will prevent public access even if your object has public enabled on its access control list
- Next, select public on the object's access control list
- Click on the object and access its web address to see that the changes were properly made


[Return To Top](#table-of-contents)
# EC2

[Return To Top](#table-of-contents)

# VPC

[Return To Top](#table-of-contents)

# Route 53

[Return To Top](#table-of-contents)

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


 [Return To Top](#table-of-contents)


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

[Return To Top](#table-of-contents)

# CloudWatch
- A monitoring service to monintor the health and performance of your AWS resources. It can monitor pretty much everything, making it a key tool for system administrators.

- All EC2 instances send health and performance metrics to CloudWatch including CPU usage, disk usage, and netowrk status. Metrics are stored indefinitely and can be retrieved from any EC2 instance or load balancer even after they have been terminated

- EC2 does not send `os-level metrics` to CloudWatch by default. You will need to download the `CloudWatch agent` in order to achieve this. Operating system metrics include free disk space, CPU idle time, and memory usage.

- Metrics are sent in `5 minute intervals`, however this can be changed by paying additional fees for `detailed monitoring` which sends metrics in `1-minute intervals`. You could even get `1-second inteveral `sends by paying for `high resultion monitoring`.

- With the `CloudWatch Agent` you can also monitor system and application logs and filter them for specific phrases, values, or patterns. 

## Alarms
- CloudWatch can be configured to send notifications to your phone or email via alarms. 

- For instance, you can configure an alarm that sends you an email when your AWS account bill exceeds $100. 

- Another possible use case is to have an phone notification sent to your system administrator whenver CloudWatch detects greater than 90% CPU utilization in your load balancer for over 5 minutes. It's a fantastic way to wake up your sysadmin in the middle of the night.

## Metrics
- A metric is a variable that can be monitored over an interval of time. In CloudWatch metrics are displayed as graphs. Each plot point in a graph consists of a timestamp and a unit of measurement.

- Metrics should be associated with namespaces. Every unit of your application should have its own dedicated namespace that it can publish its metrics to. This provides a better way to orgainze your metrics.

- For instance, AWS automatically configures namespaces for some of its services like EC2, EBS, Billing, SNS, and Lambda. All CloudWatch metrics from EC2 will be published under the EC2 namespace etc.

- When making custom metrics, you will need to make a custom namespace.

- In CloudWatch you can filter for metrics using a variety of search criteria, such as a string pattern, namespace, or instance id. These are called `dimensions`.

- You can conveniently monitor your metrics of interest in a single page via the CloudWatch `dashboard`. It is similar in nature to the performance tab of a Windows task manager, but highly customizable. 

## Installing CloudWatch Agent in EC2
- To install the CloudWatch agent on your EC2 instance, be sure to give it the CloudWatchAgentServer managed policy in IAM via a role. 

- In the EC2 CLI use these commands to install and configure the agent.

```bash
sudo yum install amazon-cloudwatch -agent -y
```
```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

- If you have an "empty" EC2 instance you can test that everything has been configured properly by running a CPU stress test. Install the amazon linux extras package if you have not already, you will need it to download the stress application. The third command runs the stress application

```bash
sudo amazon-linux-extras install epel - y
```
```bash
sudo yum install stress -y
```
```bash
stress --cpu 1
```

- View your CloudWatch metrics to see the results.

## Actions
- CloudWatch actions are commands that can be used to publish, monitor, and alert on a variety of metrics.

- Refer to the AWS CloudWatch API for an up to date list of API calls

- `PutMetricData` publishes metric data points to CloudWatch. It takes a name, namespace, value, and timestamp as arguments

```bash
aws cloudwatch put-metric-data \
--metric-name PageViewCount \
--value 25 \
--timestamp 2022-01-10T12:00:00.000Z
```

- `PutMetricAlarm` creates an alarm associated with a metric to alert you if a threshold has been reached. It is highly customizable.


[Return To Top](#table-of-contents)

# CloudTrail
- Records user activity in your AWS account. This is unlike CloudWatch which monitors the finer performance-related details of your running applications.

- CloudTrail records the creation, modification, and deletion of AWS resources (like IAM users, S3 buckets, and EC2 instances), giving details on who carried out these actions and when. It can also detect failed logins or any other API calls.

- By default, you can view the last 90 days of account activity

- CloudTrail does produce logs which can be integrated with CloudWatch Logs to keep track of user activity.

[Return To Top](#table-of-contents)

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

[Return To Top](#table-of-contents)


# Developer Theory

## CI/CD
- CI/CD stands for continuous delivery, continuous deployment/delivery

- It is a developer best practice that is based around automating as much of the build, test, and deployment process as possible. Large companies like Google, AWS, and Facebook use CI/CD to deliver thousands of code changes a day.

- AWS provides three tools to support CI/CD: Code Commit, Code Build, and Code Deploy

## Cloud Formation
- Allows you to programatically provision your AWS infrastructure so that you don't have to click around the AWS dashboard and set up everything manually. This is great if you already know everything you need to set up your AWS system. You will provide all your instructions in a Cloud Formation template file either .yaml or .json.

- For example, you can write a could formation template to provision an EC2 instance, define its permissions, and create an ssh keypair for it. The template must be uploaded to S3. The resources provisioned by a template are referred to collectively as a `stack`.

- Every template must have a resources section. This is the only mandatory section. The transform section is used to reference additional code storedin S3, allowing for code re-use.

- Transform: allows you to reference code located in S3 like Lambda code or reusable snippets of Cloud Formation code

- Mappings: allows you to create custom mappings like region AMI

- Resources: This section is mandatory and describes the AWS resources that cloudformation will create

- Infrastructure as Code: Cloud formation allows you to manage, configure, and privisoin aws infrastructure as yaml or json code.

- Parameters; input custom values

- Conditions: provision resources based on environment

- In cloud formation, click on create new stack and design a new template if you do not have one prepared already. You can also get a template that is already on S3. Below is a sample yaml file which provisions a single EC2 instance and enables ssh.

```yaml
AWSTemplateFormatVersion: 2010-09-09

Description: Template to create an EC2 instance and enable SSH

Parameters: 
  KeyName:
    Description: Name of SSH KeyPair
    Type: 'AWS::EC2::KeyPair::KeyName'
    ConstraintDescription: Provide the name of an existing SSH key pair

Resources:
  MyEC2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      InstanceType: t2.micro
      ImageId: ami-0bdb1d6c15a40392c
      KeyName: !Ref KeyName
      SecurityGroups:
       - Ref: InstanceSecurityGroup
      Tags:
        - Key: Name
          Value: My CF Instance
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        IpProtocol: tcp
        FromPort: 22
        ToPort: 22
        CidrIp: 0.0.0.0/0

Outputs: 
  InstanceID:
    Description: The Instance ID
    Value: !Ref MyEC2Instance
```
- AWSTEmplateFormatVersion should always be 2010-09-09 as this is latest version of the template

- In paramters you include any additional information such as the name you want to give to your keypair that will be used to ssh into EC2.

- Under resources you specify the resources you want to provision. In our example, the EC2 instance and its accompanying security group to enable ssh on port 22 are the resources we need to specify. Documentation on AWS is provided on what additional properties you need to nest within these resources.

- the outputs section is used to define variables that you wouldl ike to export to other CloudFormation templates. In the recieving template, include the value you want to import under the resources group. Prefix the value with the name of the sending stack (template).

- below is a sample of a template that outputs some network information after provisioning and configuring a VPC. Only the outputs section is shown for brevity. Note that unlike the previous example, this time the template is shown in json format. Both json and yaml are acceptable formats for writing the template file.

```json
"Outputs" : {
    "VPCId" : {
      "Description" : "VPC ID",
      "Value" :  { "Ref" : "VPC" },
      "Export" : { "Name" : {"Fn::Sub": "${AWS::StackName}-VPCID" }}
    },
    "PublicSubnet" : {
      "Description" : "The subnet ID to use for public web servers",
      "Value" :  { "Ref" : "PublicSubnet" },
      "Export" : { "Name" : {"Fn::Sub": "${AWS::StackName}-SubnetID" }}
    },
    "WebServerSecurityGroup" : {
      "Description" : "The security group ID to use for public web servers",
      "Value" :  { "Fn::GetAtt" : ["WebServerSecurityGroup", "GroupId"] },
      "Export" : { "Name" : {"Fn::Sub": "${AWS::StackName}-SecurityGroupID" }}
    }
  }
```

- below is an example of the template that recieves these values. Note that the values to import can be clearly identified by the Fn::ImportValue key. Only the resources section is shown for brevity.

```json
"Resources": {
    "WebServerInstance": {
      "Type": "AWS::EC2::Instance",
      "Properties": {
        "InstanceType": "t2.micro",
        "ImageId": "ami-0ed9277fb7eb570c9",
        "NetworkInterfaces": [
          {
            "GroupSet": [
              {
                "Fn::ImportValue": {
                  "Fn::Sub": "${NetworkStackParameter}-SecurityGroupID"
                }
              }
            ],
            "AssociatePublicIpAddress": "true",
            "DeviceIndex": "0",
            "DeleteOnTermination": "true",
            "SubnetId": {
              "Fn::ImportValue": {
                "Fn::Sub": "${NetworkStackParameter}-SubnetID"
              }
            }
          }
        ]
      }
    }
  }
}
```
## Failed Provisions

- if a cloud formation deployment ever fails due to mistakes in configuration, by default, your entire stack will be rolled back. This happense because the automatic rollback on error feature is enabled by default.

- in order to preserve the resources that were successfully provisioned and only delete those resources in a failed state, you need to go to the Cloud formation console and select "preserve successfully provisioned resources" under the stack failure options. You also need to use the aws cli to set the --disable-rollback flag to false.



## Nested Stacks
- Nested stacks provide a way for you to reuse cloud formation code for common use cases. For instance, instead of manually copying and pasting your yaml files for every single instance of a load balancer, you can store it in a cloud formation template and have that template be referenced by other templates.

- To do this, in your yaml file for the load balancer, include the TemplateURL under tags with a link to your s3 bucket containing the standard template you want to reuse. 

## Serverless Application Model (SAM)
- If you are notrunning your application on EC2 instances however, and are using serverless technologies instead, you must use the extension of cloud formation called SAM to provision serverless resources. SAM uses its own template style and its CLI is used to package code and upload it to S3 where it can be then deployed by cloud formation.

- it uses simplified syntax for defining serverless resources such as APIs, Lambdas, and DynamoDB tables

- the two commands you will use are `sam package` to package your application and `sam deploy` to deploy your application. The sam package command takes 3 args: a yaml template, an output sam yaml template, and the s3 bucket to store this output. The sam deploy command takes 3 args: your template file, the name of your stack, and an IAM role to be created

- To start using SAM, you must first install the SAM cli on your OS. Ensure that your Cloud Formation template and your application code files are available either on your local machine or on S3. Your template file in yaml format may look like this. Note that in transform we specify serverless.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Transform: AWS::Serverless-2016-10-31
Resources:
  TestFunction:
    Type: AWS::Serverless::Function
    Properties:
      Handler: index.handler
      Runtime: nodejs12.x
      Environment:
        Variables: 
          S3_BUCKET: cfsambucket
```

- Next use the sam package and sam deploy commands. Afterwards you will see that this sam cloud formation template has automatically placed your code into Lambda as expected.

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
- Automates the code build, test, and deployment process. Every time there is a change to your code, the pipline will execute this workflow which consists of updating the source in the CodeCommit repository, builiding; packaging; and running automated tests for the code in CodeBuild, and deploying the application into a staging or production environment using CodeDeploy 

- It integrates with CodeCommit, CodeBuild, CodeDeploy, Github, Jenkins, Elastic Beanstalk, CloudFormation, Lambda, and Elastic Container Service

- To prepare to use code pipeline, you want to have at least one S3 bucket for your cloudformation json and another for your application. The CloudFormation json is simply a file used to automate provisioning instances and defining their access and roles. You will need to copy the url of this S3 bucket. For your S3 bucket containing your application code, be sure to enable versioning.

- Next, create an EC2 keypair and access the AWS cli. Enter your cloud formation script. Ensure that you replace
the placeholder --template-url with the url of your S3 bucket containing the cloud formation file. This script
can take around 5 minutes to complete

```bash
aws cloudformation create-stack --stack-name CodeDeployDemoStack \
--template-url http://s3-eu-west-1.amazonaws.com/cftemplates-faye/CF_Template.json \
--parameters ParameterKey=InstanceCount,ParameterValue=1 \
ParameterKey=InstanceType,ParameterValue=t2.micro \
ParameterKey=KeyPairName,ParameterValue=irkp \
ParameterKey=OperatingSystem,ParameterValue=Linux \
ParameterKey=SSHLocation,ParameterValue=0.0.0.0/0 \
ParameterKey=TagKey,ParameterValue=Name \
ParameterKey=TagValue,ParameterValue=CodeDeployDemo \
--capabilities CAPABILITY_IAM
```
- check if the command is complete by entering this in the command line

```bash
aws cloudformation describe-stacks --stack-name CodeDeployDemoStack --query "Stacks[0].StackStatus" --output text
```

## Code Artifact
- A repository for developers that makes it easy to find the software packages they need. It integrates with npm, maven, and python package index.

- artifacts include documentation, libraries, compiled applications, and deployable packages

- engineering teams can set up a list of approved packages, or packages that have been tested extensively for compatability with their software

- to pull packages from an external repository like npm, you must first create an upstream repository with an extenral connetion to the npm registry (public repository)

## Containers and Deployment
- standardized units with everything the software needs to run including libraries, system tools, code, and runtime. Theya re similar to virtual machines, but could more accurately be described as virtual runtime environments. Use Docker to create Linux containers and Windows Containers for Windows workloads.

- provide the foundation for microservice-based architectures. This is where a software application is divided into smaller units called "microservices" which are stateless, so if one fails, the others should still function. This architecture is much less error prone, more scalable, and easier to maintain than monolithic applications. 

- ECS is used to deploy and scale containerized workloads without having to install, configure, and manage and scale your own orchestration platform. Kubernetes is a similar, widely used deployment tool, but unlike ECS it does not have deep integration with other AWS services like IAM, VPC, or Route53

- ECS runs containers on clusters of virtual machines. Fargate can be used for serverless containers that don't need underlying EC2 instances. EC2 however gives the best control options for installation, configuration, and management of your compute environment.

- ECR (Elastic Container Registry) is where you can store your container images.

- Elastic Beanstalk also supports the deployment of docker containers. It handles the capacity provisioning, load balancing, scaling, and application health monitoring. It can run just a single docker container on a provisioned EC2 instance or run multiple containers within an ECS cluster.

- In elastic beanstalk, you can update and rollback your application in a few easy clicks. Your application can be uploaded to elastic beanstalk either directly from your local machine as a zip file or from S3.

[Return To Top](#table-of-contents)


# AWS Linux CLI