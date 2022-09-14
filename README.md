# aws-dev-associate-DVA-C01

# Notes

## Deployment (22%)
 - Topics
    - CICD
    - AWS CodeCommit, AWS CodeBuild, AWS CodePipeline, AWS CodeStar, and AWS CodeDeploy 
        - rolback plan
    - Elastic Beanstalk 
    - AWS SAM, Environemnts

### CodeStar
 - allows devs to create, build, deploy applications 
 - CodeStar connects CodeCommit/git, CodeBuild, CodePipeline, and CodeDeploy together using predefined templates.
 - like AzureDevop pipline or Github Action
 - come with templates for Ec2, Lambda, Beanstalk
 - security
 - can integrate 3rd-party tool like JIRA
 - free only pay for Ec2 and etc.
 - has project dashboard
 - APN member (partners) can do the integration program.
 - can't launch the app other region.

### Code Commit
 - managed source control service like github
 - compliance with many progrms like US health insurance
 - supports git commands as well as AWS CLI
 - A single file in a repository cannot be more than 2 GB in size
 - can use webhook to do SNS or AWS chatbot
 - can audit via CloudTrail
 - all repo are encrypted at REST.
 - supports the cross-account users.

### Code Build
 - compile the code, run tests, create the artifacts.. (Not for publishing the code)
 - supports the standard image or custom docker images (like runners)
 - integrates with other AWS service (e.g. can upload the artifact to S3, ship logs to CloudWatch, can use SNS to nofity)
 - can use CodeBuild local for debugging too.
 - can use KMS to encrypt the artifacts

### Code Pipeline
 - automate the release process
 - revision(code), stage, action, artifact, transition (The stages in a pipeline are connected by transitions)
 - admin:repo scope with Github access token and webhook

### Code Deploy
 - automates code deployment to any AWS resources or on-prem resource
    - needs the code deploy agent installed on Ec2
 - application, revision (code, AppSpec file), deployment group (either Ec2s or lambdas)
    - AppSpec 
        - a configuration file that specifies the files to be copied and scripts to be execute
        -  AppSpec file is used to manage each deployment as a series of lifecycle event hooks, which are defined in the file. (e.g. auto scaling group across multiple AZ)      
 - Lifecycle (ApplicationStop, DownloadBundle, BeforeInstall, Install, AfterInstall, ApplicationStart, ValidateService)
 - it works with other configuration mgmt tools such as Chef, Puppet, Ansible
 - Deployment Types
    1. In-place deployment 
        - stope the resouces and redeploy. 
        - AWS Lambda and Amazon ECS deployments cannot use an in-place deployment type.
    2. Blue/green deployment
        - Blue/green Ec2/on-prem: The instance are replaced iwth a diff set of instance
        - Blue/green Lambda/ECS
            - AllAtOnce - replace all at once, downtime, cheap
            - Linear - Traffic is shifted in equal increments based on predefined linear options (Linear10PercentEvery10Minutes, Linear10PercentEvery1Minute, Linear10PercentEvery2Minutes, Linear10PercentEvery3Minutes)
            - Canary - Traffic is shifted in two increments based on pre-defined canary options (Canary10Percent30Minutes, Canary10Percent5Minutes, Canary10Percent10Minutes, Canary10Percent15Minutes)
                - *Explanation: Canary10Percent10Minutes deployment configuration -will first shift 10 percent of the traffic, and then after 10 minutes it will shift the other 90 percent of the traffic to the new version.*

### AWS OpsWorks/AWS OpsWorks Stack 
- AWS OpsWorks
    - a configuration management service that provides managed instances of Chef and Puppet
    - lets you use Chef and Puppet to automate how servers are configured, deployed, and managed across your Amazon EC2 instances or on-premises compute environments.
- OpsWorks Staks
    - lets u model your app as a stack containing different layers.
    - Automate tasks
        - preset schedules can scale the app based on traffic levels
        - use the lifecyle hooks to orchestrae the changes
    - By default, you can create up to 40 Stacks, and each stack can hold up to 40 layers, 40 instances, and 40 apps
    - lifecycle events (setup, configure, deploy, undeploy, shutdown)


 ### Elastic Beanstalk
 - simply deployment (esp: Web) PaaS- You just need to upload the file and Beanstalk handles the details of capacity provisioning, load balancing, scaling, and application health monitoring.
 - deployment service - supports a lot of languages (java, .net, go and etc.)
 - similiar to Web App in Azure but you can see the host and etc. on AWS but Azure hide them. 
- NOT a serverless approach, use .ebextensions folder for custom configuration files
- Policies: 
   1. All at Once (fastest, but with downtime)
   2. Rolling (still downtime, reused the instance, not full capacity, no additional cost)
   3. Rolling with Additional Batch (long, but no downtime, add extra batch instance before taking out, full capacity but additional cost for extra batch)
   4. Immutable (new instance scaling, no-reused for instance, full capacity, no downtime but most expensive )
   5. Traffic Splitting (for testing)
 - Automatically launches an environment and creates and configures the AWS resources needed to run your code (platform, application, preset(free, custom))
 - Concepts
    1. application: logical collection of Beanstalk components (includes env, version, env config)
    2. App version: 
    3. Environemnt: collection of AWS resources. Each env runs only one app ver but can run diff ver on diff env.
    4. environemnt tier: types of apps. e.g. app web server, backend SQS
    5. Saved configuration: a starting point template. You can modify it and save it. 
    6. Platform: combination of OS, languages runtime, web server, Beanstalk components and target
    7. health: monitoring
    8. Worker Enviornment - long running process
        - include cron.yaml if it needs to be executed based ons schedule
 - 512 MB is max source bundle size
 - must not include the parent folder in the source bundle
 - RDS instance should be outside of environment (so the db won't get deleted when u delete the app) - 
 - .ebextensions - custom config - can contain the config file to configure the context and resources
		- e.g. healthcheckurl.config, env.config
 - DOES NOT support EC2 Capacity Reservation Instance
 - The updates are applied using an immutable deployment mechanism (it won't install the updates to the existing resouces. Will install to new instance and swape.)
    - can update OS, managed platform automatically if opt-in.
    - maintenance window is a weekly two-hour-long time slot
 - supports Change history
 - PostTraffice property - can be used for validation check
  - .ipa = Xamarin app, Device Farm needs the zip

### CloudFormation
 - Like Terraform
 - Infrastructure (no web applications) management via templates (JSON), up to 100 resources. Provision and manage a collection of resources (stack, e.g., auto scaling group, load balancer, and database) as a single unit.
 - Deploying Lambda - YAML template and deployment package on S3


### AWS CloudFormation Vs OpsWork Stack Vs Beanstalk
 - CF: provisioning of AWS resources using the templates, mostly used by DevOps
 - Beanstalk: can deploy, automae operations, mostly used by dev
 - OpsWork is a higher level service, uses Chef/Puppet, can automate tasks, can be used by IT admins and devops
 - CloudFormation supports OpsWorks components too.

### Serverless Application Model (SAM)
 - Extension of CloudFormation, no web applications, just resources
 - A framework for building serverless applications (e.g., lambda + APIs + database). It provides shorthand syntax to express functions, APIs, databases, and event source mappings.
 - consists of two components 
    1. Template: YAML, transforms and expands the SAM syntax into AWS CloudFormation syntax
    2. SAM CLI
 - single deployment for all related resources
 - local testing/debugging - provides a lambda-like execution env locally 
 - sam local start-api - will start the api
 - sam local invoke - call the func but exit
 - sam local start-lambda - will call func but not api
 - sam local generate-event - will generate sample payload
 - sam init 
 - sam build - will build and create the artifect (** important and weired)
 - sam package - will compress and upload the app to S3 (weired)
 - sam deploy - deploy the app


## Security (26%)
- Topic
    - Auth call (policy based, Assume IAM roles, credential provider)
    - Encryption (at rest (client-side, server-side, envelope), in transit)
    - auth/authorization 0 cognito identity/user poools

### IAM/Access
 - provides fine-grained access control across all of AWS.
 - IAM User = like AD users (user with long-term credentials), Roles = like AD User Role/Group, Policy = Permissions/Set of permissoin for a role
 - Assume Role = like impersanation to see something (like cross account) Delegate access for cross account
      - 'trusted' account and 'trusting' account (someone from dev tenant acess to prod using assume-role. dev is a trusted account and the prod is a trusting account.)
 - Web identity = facebook/google
 - MFA 
 - SAML 2.0 = it includes Active directory
 - Policies => 
    1. Identity-based policies (attached to IAM),
    2. Resource-based policies (attached to resources) 
 - AWS Security Token Service (AWS STS) 
    - provide trusted users with temporary security credentials that can control access to your AWS resources
    - short-term
    - can be used with SAML (like SSO), Web identity (Facebook), Roles for cross-account and EC2
 - RBAC - role-based access control (can assign the permission based on a person's job function)
 - ABAC - attribute-based access control (attributes are called tags in AWS), define/assign the permissoin based on the tags.
 - SCPs - Organizations service control policies - similiar to IAM policyt but allow/deny acces to AWS for individual AWS accounts. 
 -  IAM Access Analyzer - generates policies based on activity captured in logs.
 identity pool (federated identities) (NOT user pool) - it's for giving the temp access to unauthenticated user
 - IAM policy simulator - for testing/troubleshooting 
 - CreateServiceLinkedRole - can't autoscale

### General
 - Basic AWS Credential - can set the acess key and secret in ctr and can retrieve anywhere.
 - CredentialProfileOptions - can set the acess key and secret
 - StringToSign - Amazon-specific algorithm that includes other algorithms. AWS wants the auth header with Sha1 (Not TripleDES, MD5, AES)
 - CredentialProfileStoreChain
 - profile location must be set on the chain var. 
 - All application on Ec2 share the same roles and permission.
 - updating the role that is attached to Ec2 doesn't affect immediately. U need to de-attach and re-attach it. 

### Cognito
 - AWS identity provider (provides authentication, authorization, and user management for your web and mobile apps)
 - two main components
    1. User Pools
        - user directories (signup, sign-in, profile, MFA)
        - build-in UI
        - social sign-in via SAML or OIDC (OpenID Connect)
            - when using with public identity provider, AWS does't store the user info. Only use the token. 
        - customized workflow/user migration thru AWS lambda trigger.
    2. Identity Pools 
        - can obtain the temporary credential
        - support user pools, socail sign-in, OIDC provider, SAM identity provider
        - developer authenticated identity
 - can use the user pool and identity pool seperately or together.
 - Can store user profile in User Pool
 - if u want unauthenticated user to access your app, you need to grant the access that user as a guest and define a seperate IAM role.
 -  free tier of 50,000 MAU
 - AWS Cognito Sync 
    - sync users across mobile device and web app. it's a key/value pair store.
    - max of 20MB per user info
    - 1 MB for data set
    - can use lamdba to validate the data 
    - silent push notification: Cognito uses SNS to push the noti but users won't see it. 
    - data conflict: maintain the last-written data by default. but u can change it. 
    - can't store the data but can be saved to SQLite.


 - ServerSideCustomerProvidedKey = Conver.ToBase64(aes.Key)

### API Gateway 
 - I have skipped most of them since we have used it in real projects. 
 - AWS Signature Version 4 - access with header (e.g. AWS4-HMAC-SHA256 Credential=AKIDEXAMPLE/20150830/us-east-1/iam/aws4_request, SignedHeaders=content-type;host;x-amz-date, Signature=5d672d79c15b13162d9279b0855cfba6789a8edb4c)
 - 429 HTTP response - Throttling
 - throtting order: 1) per-client per-method throttling limits that you set for an API stage in a usage plan, 2) per-client throttling limits that you set in a usage plan, 3) default per-method limits and individual per-method limits that you set in API stage settings, 4) account-level throttling per region.
 - max msg size: 128 KB for web socket
 - doesn't support http

### Secrets Manager
 - Skip
 - max content size:  64 KB 

### KMS
 - Envelope encryption is the practice of encrypting plaintext data with a data key, and then encrypting the data key under another key.  
 - Symmetric encryption – default, used by other services (TripleDES)
 - Asymmetric encryption – public/private pair (RSA)
 - symetric key algorthms - faster/produce smaller chpherteext
 - public ke yalgorithms - provides inherent seperation of roles and easier key management.
 - Use an alias as a friendly name for a KMS
- GenerateDataKeyWithoutPlaintext (asymmetric public/encrypted private key pair) | GenerateDataKeyPair | GenerateDataKey (symmetric) – key used outside of KMS
- Key identifiers (KeyId) act as names for your KMS keys
- region sepcifc
- CMS (Customer Master Key) - contains key material used to encrypt/decrypt the data
- DEK (Data Encryption Key) - used to encrypt the data. (plain text data key, encrypted data key)
- Key Policites - determine who can administroe the key
- Grant - delegeate your permission to anther pricipal - only support only CLI (Not Console.)
- U grant the access to user but can't affect after an hour - it's because of eventual consistency
- KeyId is a required parameter for granting access to CMK API.
- custom key can be shared with other users. (u can't share the default key)
- AMIs created with encrypted snapshot can be shared with other accounts.

### S3 

 - throttles the traffic automatially if there are too many version of file.
 - Server-side encryption (SSE) - only for object data (not meta data), encryption at rest, rotates key regularly, AES-256, x-amz-server-side-encryption heade
   - SSE-S3 - default, cheaper than KMS
   - SSE-KMS - KMS Key must be on the same region, additional charges, no need to add the key in header, can manage the key/rotation, can audit with CloudTrail for key usage
   - SSE-C - customer managed, no charge for key storage, need to add the key in request, if u lost the key, u lost all objects. 
 - Client-side - use AWS encryption SDK (CSE-MKS, CSE-C)
 - pre-signed
 - CORS
 - lifecycle
 - Instances can be seen cross region
 - Standard (frequent access) or Standard (One Zone) IA (infrequent access, once a month) 
 - Intelligent-Tiering (unknow access frequency)
 - Glacier Instant/Flexible/Deep Archive (infrequent access, retrieval rate milliseconds/1-5mins/12hours)
 - Bucket policy (resource based IAM) or ACL (updater owns objects or bucket owner owns object). ACL predates IAM/policy
 - 5500 read requests/second. Use Prefixes to parallelize reads. E.g. 4 prefixes -> 4 x 5500 = 22000 reads/s
 - S3 Inventory - find
 - S3 Select - to extract records from a single CSV/JSON using SQL expression
 - S3 supports 3500 data add per second and 5500 data retrieval per second.

### EBS (Elastic Block Store)
 - General Purpose SSD – used as boot volumes, medium-size single instance database, dev/test environments
 - Provisioned IOPS SSD – used as I/O intensive workloads (sensitive to storage performance and consistency)
 - Throughput Optimized HDD (Magnetic) – large sequential workloads (EMR, ETL, data warehouses and log processing)
 - Cold HDD (Magnetic) – infrequent access, cold data workloads (backup)
 - tightly integrates with KMS
 - can use a customer-managed or CMK to encrypt an EBS volumn at instance launch.
 - can encrypt the snapshot too. 
 - u can create the encrypted snapshot from unencrypted database. but you can't create the unencrypted snapshot from encrypted database. 
 - you can't disable the encryption once it's enabled.
 - you can't restore the backup from cross-region.

 ### AWS Systems Manager Parameter Store

 ## Development with AWS Services  - 30%
  - Topics
    - serverless/lambda
    - api gateway
    - DynamoDB
    - requirement to app design
        - real-time vs batch
        - sync vs async
        - event vs schedule/pool
        - trade off
    - SQS/SNS
    - ElastiCache
    - CLI/SDK

### lambda
 - to deploy - needs the global read permission on code/depends and compres code (not the folder) as a zip
 - lambda RAM: 128MB to 10,240MB
 - max timeout is 15 mins
 - ephemeral storage (\tmp) between 512MB and 10,240MB, in 1MB increments.
 - max file size: 250 MB unzipped
 - lambda default throttle - Burst concurrency quotas
    - 3000 – US West (Oregon), US East (N. Virginia), Europe (Ireland)
    - 1000 – Asia Pacific (Tokyo), Europe (Frankfurt), US East (Ohio)
    - 500 – Other Regions
 - sync invoke:  throttling error (429 error code) if over the limit (limit applied to account (NOT per function))
 - async invoke: You can configure DLQ. 
 - versioning - u can have v1 and v2 for same lambda (need to check)
 - lambda layer (common layer/library that u can share across other lambda)
 - default probation - 1000.. 
 - alias - u can set alias to indivual version or same version of lambda (eg.. PROD for v1 and v2)
 - shift traffic - can't shift the traffic between two alias (should be one alias with two version) (seems like need to use it with AWS CodeDeploy)
 - support Compute Savings Plans to save cost. 
 - integrated with Alexa Skills Kit.
 - can use RDS Proxy
 - Container image: can use lambda provided image, linux based image (alpine/debian)
    - container image must implement the Lambda Runtime API
    - read-only file, wriable to /tmp (512MB)
    - u cant patch the image once deployed to lambda.. 
 - Lambda Runtime Interface Emulator (RIE) - a proxy for the Lambda Runtime API,which allows customers to locally test their Lambda function packaged as a container image
 - AWS Lambda Provisioned Concurrency
 - can use Graviton2 processors
 - can mount EFS to lamdba via  EFS Access Point (need to be in VPC) but can mount only one EFS
 - lambda supports lambda extension
    - Extensions can impact the performance of your lambda
    - AWS Lambda Runtime Logs API - enable you to use extension to send logs from lambda to a destination (New relic)
 - lambda supports https endpoint (not custom domain supported yet)
 - Lambda@Edge - allows you to run code across AWS globally
 - create digitally signed code artifacts using a Signing Profile through the AWS Signer console, the Signer API, SAM CLI or AWS CLI.
 - you cant change the version of LInux OS or any language runtime
 - support Advanced Vector Extensions 2 (AVX2)
 - event source mapping can point to the version ARN or alias ARN (not layer)
    - if you are pointing the source mapping to alias, you don't need to update when promoting new version or rollback.

 ### DynamoDB

 - Primary Key - Partition key and sort key
 - indexes 
 - data consistency models - strongly consistent read, eventual consistent reads, ACID
 - provisioned throughtput - split into many partatition as possible.
 - 40K read/write request units
 - two table classes (Standard and Standard-Infrequent)
 - global table - data replication
 - DynamoDBIgnore attribute - ignore the mapping for DynamoDb
 - ProvisionedThroughtPutExceedException - u can get it sometimes even tho you are not exceeding. It's because you are exceeding your capacity on a particular partition key.
 - two read/write capcity modes (on-demand/provisioned)
     - on-demand 
       - good for  unknown workload, unpredicatable app traffic
       - request rate is limited by dynamodb throughput default table quota. switch between read/write capacity modes once every 24 hours
     - fixed
 - Capacity ** Important **
    - Read
      - one read capacity unit provides one strongly consistent read per section up to 4KB
      - eventual consisten read - one-half read request unit up to 4KB
      - transaction read request - two read unit up to 4kb
    - write
      - one unit for 1 KB, transational write - two units for 1 KB
 - Time To Live - to auto delete
 - Exponential Backoff 
    - Most exponential backoff algorithms use jitter (randomized delay) to prevent successive collisions. 
    - throttle the number of requests 
 - signing request for the user that uses like Facebook account - temp credential with web identity federation  
 - DynamoDB Steam - is used to capture the a time-ordered sequence of item-level modifications 
      - I think it can be used like a SQL INSERT/Update Trigger. (e.g. if a new order is inserted, send the thank-you sms to the customer.)
 - DyanamoDB Accelerator (DAX) - allows in-memroy caching - improve the perf 10x.     
 - Throtting will occur if the previous peak read is doubled within 30 mins
 - peak read per second for subsequent read is the double of previous peak read after 30 mins.
 - DynamoDB Encryption Client is configured to KMS, it use a customer master key. It uses the symemetric CMK.
   - it also provide end to end protection for your data in transit and at rest. 

### SQS
 - Like MSMQ
 - FIFO (ordered, exactly-once processing) and standard (delivered at least once)
 - pull 
   - short pulling is default, long pulling)
   - persisted data while SNS doesn't persisted
   - 1 to 1
 - With short polling, the ReceiveMessage request queries only a subset of the servers (based on a weighted random distribution) to find messages that are available to include in the response. Amazon SQS sends the response right away, even if the query found no messages. (You might get a lot of empty response so more cost)
 - With long polling, the ReceiveMessage request queries all of the servers for messages. Amazon SQS sends a response after it collects at least one available message, up to the maximum number of messages specified in the request. Amazon SQS sends an empty response only if the polling wait time expires. (less cost and prevent getting the empty message)
   - 20 seconds for a long-poll timeout
 - When a consumer receives and processes a message from a queue, the message remains in the queue. Amazon SQS doesn't automatically delete the message. Because Amazon SQS is a distributed system, there's no guarantee that the consumer actually receives the message (for example, due to a connectivity issue, or due to an issue in the consumer application). Thus, the consumer must delete the message from the queue after receiving and processing it.
 - VisibilityTimeout - Immediately after a message is received, it remains in the queue. To prevent other consumers from processing the message again, Amazon SQS sets a visibility timeout, a period of time during which Amazon SQS prevents other consumers from receiving and processing the message. The default visibility timeout for a message is 30 seconds. The minimum is 0 seconds. The maximum is 12 hours.
 - Wait Time
 - MesageRetention Period
 - Delay Seconds 
 - message group : Messages are grouped into distinct, ordered "bundles" within a FIFO queue
 - quota: FIFO queues support up to 3,000 messages per second with batching or up to 300 messages per second (300 send, receive, or delete operations per second) without batching. U can enable the higher thruoughput mode.

### SNS
 - topic are region specific. (can be used from the alarm which are in same region)
 - set the source property to App Name - so it will trigger the app
 - can send msg to device, phone number, topic
 - SubsribeRequest - can be used to subscribe other request
    - setting Protocol to Lambda and Endpoint to ARM of Lambda can invoke the lambda func
 - publish request - generate the alert or notification
 - setting the TargetArn to ARN asscoicated with app endpoint will send the notification
 - PhoneNumber property - for sending the text message
 - you can't set both TopicArn and PhoneNumber at the same time.
 - The ListSubscriptionsByTopic API allows a topic owner to see the list of all subscribers actively registered to a topic.
 - The ListSubscriptions API allows a user to get a list of all their active subscriptions (to one or more topics).
 - SNS FIFO topics
 - doesn't support two-way SMS or MMS
 - 10DLC - can send 100 text msg per second in US

### Elastic Cache
 - Memcached – simplest model, scaling, cache objects, large nodes with multi-cores/threads
 - Redis – add/remove shards, authenticate user, tier data between SSD and memory
 - Cluster, Node (ElasticCache deployment - run Redis or Memcached), Endpoint, Replication group

### Step function
 - Task - can do some action like invoking lambda
 - Workflow - manage/coordinate the tasks.

## Refactoring 10%
 - Topics
    - Optimize (Elastic Cache/API gateway caching)
    - S3 naming for optimal read performance
    - migration existing application (isolate dependency, statelss, horizontal scaling, externalize state) 

### MQ
 - message broker ( Single-instance broker, Active/standby broker pair, which provides high availability by deploying two broker instances in two AZs)
 - mesh net - 3 single instance broker OR 3 interconnected active/standby broker
 - EFS (durability optimized)/EBS (throughtput optimized)
 - point-to-point - queues
 - pub/sub using topic
 - broker failover     
 - lift and shift with minor refactoring
 - managed service, highly avaibable
 - message durability
 - no expensive license, flat rate per broker pricing
 - no serverless (no auto scaling)


## Monitoring and Troubleshooting 12%
 - Topics
    - Custom CloudWatch metrics
    - logging
    - instrumentation using X-ray
    - VPC Flow logs

### CLI
 - "credential_source" pass the user info to CLI 
 - "credential_process" to specify an external auth process

### X-Ray 
 - creates a map of services used by your app with trace data. 

### CloudWatch

 - set alarms based on service metric thresholds (e.g. cpu, billing)
 - Metrics for monitoring resources, CPU, Memory (not free), Network, etc
 - 5 actions per alarm
 - Alarm period no longer than 1 day
 - keep data for 2 weeks (14 days)
 - Memory metric is not free

### CloudTrail 
 - Account auditing
 - Enable operational and risk auditing, governance, and compliance of your AWS account. Actions taken by a user, role, or an AWS service are recorded as events in CloudTrail.


### VPC
 - block the public access, connect to internal vpn
 - Security Group - like a firewall 
 - routing table - traffic control
 - Network access control list - IP whitelist (super security groups for the whole VPC)
 - subnet (instance launchs into subnet.. not directly into VPC)
     - public net (it has NAT gateway for intnert access)
 - VPC flow log

### AWS Athena
 - allows you to interact with S3 using standard Structured Query language. 
 - serverless

### Kinesis Data Analytics
 - allows processing and analyzing streaming data using standard SQL

### Kinesis Data Stream
 - similiar to Kinesis data analystic but not allow to use SQL

### Kinesis Firehose
 - is used to deliver real-time steaming data to AWS service.

### Kinesis Video Steams
 - for streaming live video from devices to AWS
    - helpful to use with Amazon Recognition Video

### Database Migration Service
 - it's for migration the db

### Data pipeline
 - move data between diff AWS account

### SWF - Simple Workflow Service
 - allows applications to coordinate work across multiple conponents

### AWS Connect
 - cloud-based contact center


# Learning resources

## Main Resources
* [AWS Certified Developer - Associate Exam Guide](https://d1.awsstatic.com/training-and-certification/docs-dev-associate/AWS-Certified-Developer-Associate_Exam-Guide.pdf)
* [AWS Certified Developer - Associate Sample Questions](https://d1.awsstatic.com/training-and-certification/docs-dev-associate/AWS-Certified-Developer-Associate_Sample-Questions.pdf)
* [AWS Certified Developer - Associate Official Practice Question Set](https://explore.skillbuilder.aws/learn/course/external/view/elearning/12484/aws-certified-developer-associate-official-practice-question-set-dva-c01-english?da=sec&sec=prep)
* [AWS Certified Developer - Associate (Exam Readiness)](https://explore.skillbuilder.aws/learn/course/external/view/elearning/42/exam-readiness-aws-certified-developer-associate-digital?da=sec&sec=prep)
* [Webinar - AWS Certified Developer - Associate (Exam Readiness)](https://aws.amazon.com/training/events/?nc2=sb_tr_evt&get-certified-vilt-courses-cards.sort-by=item.additionalFields.startDateSort&get-certified-vilt-courses-cards.sort-order=asc&awsf.get-certified-vilt-courses-type=*all&awsf.get-certified-vilt-courses-series=series%23aws-certification-exam-readiness&awsf.get-certified-vilt-audience=*all&awsf.get-certified-vilt-locations=*all&awsf.get-certified-vilt-countries=*all&awsf.get-certified-vilt-languages=*all&awsf.get-certified-vilt-courses-level=*all&awsf.get-certified-vilt-courses-tech-category=tech-category%23devtools&da=sec&sec=prep)
* [AWS Practice Test DVA-C01: AWS Certified Developer - Associate](https://www.measureup.com/aws-practice-test-dva-c01-aws-certified-developer-associate.html)
* [Office Hours: AWS Certified Developer - Associate | Domain 2 of 5 – Security](https://youtu.be/xPk0VWJs44E)
* [Pluralsight: Cloud Certifications: AWS Certified Developer - Associate](https://app.pluralsight.com/paths/certificate/aws-certified-developer-associate)

## Other Resources
* https://www.freecodecamp.org/news/how-i-passed-the-aws-certified-developer-associate-exam/
  * https://github.com/ExamProCo/TheFreeAWSDeveloperAssociate
* [AWS Certified Developer - Associate 2020 Youtube](https://youtu.be/RrKRN9zRBWs)

Whitepapaers

 * Practicing Continuous Integration and Continuous Delivery on AWS
 * Microservices on AWS
 * Architecting for the Cloud: AWS Best Practices
