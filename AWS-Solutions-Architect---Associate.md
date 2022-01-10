# AWS Solutions Architect - Associate

# AWS Global Infrastructure

## Overview

- 26 Regions
    - Physical location in the world with 2 to 6 AZs, usually 3
    - Most AWS services are region-scoped
- 84 AZ
    - Discrete data centers with redundant power, networking and connectivity
    - Housed in separate facilities and isolated from disasters
    - Connected with high bandwidth, low latency networking
- 225 Edge Locations
    - Endpoints for AWS used for caching CloudFront content
- 12 Regional Edge Caches
    - Help reduce the load on CloudFront origin resources
    - Larger cache-width than any individual edge location so TTL can be longer

# Well-Architected Framework

## Well-Architected Framework Overview

- Describes design principles and architectural best practices for designing and running workloads in the cloud
- General principles
    - No capacity guessing
    - Test systems at production scale
    - Automate processes for better infrastructure experiments
    - Design architectures based on changing requirements
    - Drive architectures using data
    - Simulate big spikes and one-time-off events
- Based on five pillars that work in synergy and are not something to make trade-offs with

## Well-Architected Tool

- Service that tracks performance of workloads against the Well-Architected framework principles
- Works by defining existing cloud workloads and get advice on improving their architecture
- Based on a set of questions related to the pillars and system performance over time

## AWS Trusted Advisor

- High-level automated account assessment
- Provides recommendation on performance, fault tolerance, security, cost optimization and service limits
- Core checks available for everyone, advanced checks for Business and Enterprise plans

## Pillar 1: Operational Excellence

- Ability to run and monitor systems to deliver business value and continually improve supporting processes and procedures
- Infrastructure as Code
- Automatic annotated documentation after every build
- Small, frequent and reversible changes
- Refine operations frequently
- Design for failure and learn from failures
- Key services
    - CloudFormation
    - AWS Config
    - CloudWatch, CloudTrail
    - X-Ray
    - CodeCommit, CodeBuild, CodeDeploy, CodePipeline

## Pillar 2: Security

- Ability to protect information, systems and assets while delivering business value through risk assessment and mitigation strategies
- Strong identity foundation ⇒ Centralized privilege management, least privilege, IAM roles
- Traceability ⇒ Log and metrics that trigger alarms to respond to events
- Security at every layer
- Automate security best practices
- Protect data in transit and at rest while keeping people away from data
- Prepare for security events
- Key services
    - IAM, STS, Organizations, KMS
    - AWS Config
    - CloudWatch, CloudTrail
    - CloudFront, VPC, WAF, Shield, Inspector, ELB
    - S3, RDS, EBS

## Pillar 3: Reliability

- Ability to recover from disruptions, dynamically scale to meet demand and mitigate disruptions such as misconfigurations or network issues
- Automated tests of recovery procedures
- Automatic recover from failure
- Horizontal scaling to increase system availability
- Don't guess capacity but provision as necessary
- Key services
    - IAM, VPC, Trusted Advisor
    - Auto Scaling, Backups, CloudFormation
    - CloudWatch, CloudTrail
    - Route 53, S3

## Pillar 4: Performance Efficiency

- Ability to use computing resources efficiently to meet system requirements and maintain efficiency as technology evolves and demand changes
- Quickly adapt to advanced technologies
- Leverage serverless architectures
- Experiment often
- Be aware of all AWS services that can solve certain needs
- Key services
    - Auto Scaling, Lambda, SAM, EBS, Aurora
    - CloudFormation, Config
    - CloudWatch, CloudTrail

## Pillar 5: Cost Optimization

- Ability to run system that deliver business value at the lowest price point
- Adopt a consumption model and pay for what you use
- Measure overall efficiency with metrics and logs
- Move workflows to the cloud to reduce CAPEX
- Analyze and attribute expenditures across all systems users
- Use managed applications to reduce cost of ownership
- Key services
    - Budgets, Cost and Usage Reports, Cost Explorer
    - Spot instances/fleets, reserved instances, Glacier
    - Auto Scaling, Lambda, Trusted Advisor Checks

# **Identity Access Management (IAM)**

## Key Features of IAM

- Allows to manage users and their level of access to AWS console
- Centralized control of AWS account
- Global, does not apply to regions
- Shared access to AWS account
- Granular permission
- Identity Federation
    - Users can log-in via third-party credentials such as work credentials, Active Directory, Facebook, LinkedIn, etc...)
- Multifactor Authentication can be set up
- Temporary access for user/devices when necessary
- Set up custom password rotation policies

## Key Terminology for IAM

- Users
    - End users such as people and employees
    - Are created and exist within IAM service
    - Can login to Management Console
    - Can have long-term access keys
    - Can enable per-user MFA device
    - New users have no permissions at the beginning
    - New users are assigned an Access Key ID and a Secret Access Key on creation
- Groups
    - Collection of users that inhering a shared set of permissions
    - Can't be nested
- Roles
    - Use temporary credentials
    - Internal usage
    - Delegate permissions to
        - EC2 instance
        - AWS service
        - A user (elevate privileges)
        - Separate accounts (cross account access or sharing)
            - One you own
            - Third party
        - Roles for EC2 instances
            - Security Token Service provides temporary credentials that the EC2 hypervisor retrieves via the Instance Metadata Service
            - Credentials are rotated 5 minutes prior to expiration
        - Role for Cross Account Access
            - Accounts that need a trust relationship to access different resources owned by other accounts
            - Policy on the role to allow access to resources and on the user to be allowed to assume the role
- Policies
    - Determine authorizations
    - Written in JSON
    - Are attached to users, groups or roles
    - Policy types
        - Managed Policy
            - AWS Managed
            - Customer Managed
        - Inline Policies
            - Policies written directly on IAM users, groups or roles
    - Can be created via
        - Generator
        - Hand written policies
    - Evaluation logic
        - By default, all requests are implicitly denied while by default, the AWS account root user has full access
        - An explicit allow in an identity-based or resource-based policy overrides this default.
        - If a permissions boundary, Organizations SCP, or session policy is present, it might override the allow with an implicit deny
        - An explicit deny in any policy overrides any allows.

## IAM Permission Boundaries

- A managed policy to set the maximum permissions that an identity-based policy can grant to an IAM entity
- Applies to Users/Roles and not groups
- Limits the user's permissions but does not provide permissions on its own
- A user with permission boundaries can never perform operations in any other service even if she has additional IAM policies that would otherwise allow it

## IAM Conditions

- Portion of policies that make them more restrictive based on certain sets of conditions
- Common conditions
    - `aws:SourceIP` ⇒ IP groups that are allowed/denied to make API calls
    - `aws:RequestedRegion` ⇒ Regions that users are allowed/denied to make API calls to
    - `aws:*Tag` ⇒ Tagged resources or users that are allowed/denied to make API calls
    - `aws:MultiFactorAuthPresent` ⇒ Allow/deny API calls only if MFA is active for the user

## AWS New Account first steps

1. Enable CloudTrail (service that records all calls made to AWS API)
2. Create admin user in IAM
3. Enable MFA on root account
4. Enable Cost and Usage Report (detailed cost reports)
5. Log out of root account
6. Log back in as admin user
7. Create additional users/groups

## IAM Federation

- Allows users outside of AWS to assume temporary IAM roles in our account
- No need to create IAM users as the users are managed outside AWS
- Multiple ways of using Federation
    - Web Identity Federation without Cognito
        - Not recommended ⇒ Use Cognito instead
        - Uses API calls `AssumeRoleWithWebIdentity`
    - Web Identity Federation with Cognito
        - Direct access to AWS resources from client side
        - Uses Federated Identity Pool with pre-defined IAM Policies
    - SAML 2.0
        - For Active Directory/ADFS or any other SAML 2.0 provider
        - Access to console and CLI
        - Used to integrate your own repository of users with IAM
    - Single Sign-On (SSO)
        -  **Centrally manage SSO access to multiple AWS accounts and business applications**
        - Integrated with AWS Organizations, SAML 2.0 and on-premise AD
        - Centralized permission management and auditing with CloudTrail
        - Can access AWS Resources and 3rd party applications with one Sign-On
        - AWS provides a catalog of 3rd party apps that integrate already with AWS SSO
    - Custom Identity Broker
        - For non-SAML compadible IdP
        - Uses STS API calls `AssumeRole` and `GetFederationToken`
    - AWS Directory Services
        - Microsoft Active Directory
            - Centralized security management for Windows-based servers
            - Database of objects organized in trees, grouped in forests
        - Three types of AD-compatible directory services in AWS
            - AWS Managed Microsoft AD ⇒ Create AD in AWS and manage it there, can trust-connect to on-premise AD
            - AD Connector ⇒ Proxy to connect to on-premise AD, users are managed on-prem
            - Simple AD ⇒ AD-compatible managed directory on AWS, no connection to on-premise
        - Directory Services also has Cognito User Pools, which leverages Cognito to add user registration and sign-in so it does not count as Active Directory

## AWS STS - Security Token Service

- **create and provide trusted users with temporary(up to 1 hour) security credentials that can control access to your AWS resources**
- Most important STS API calls
    - `AssumeRole` ⇒ Role access within- or cross-account
    - `AssumeRoleWithSAML` ⇒ Role access for federated Identities
    - `AssumeRoleWithWebIdentity` ⇒ Role access for users logged with Identity Providers (FB...)
    - `GetSessionToken` ⇒ For MFA from user or AWS root user
- STS checks permissions with IAM when a user calls `AssumeRole` and if the user can assume the role, it returns the temporary security credentials to access the role and resources

## IAM Best Practices

- Root credentials
    - Email address + password
    - Protect at all costs
    - Do not use for day-to-day
- Follow principle of least privilege ⇒ Provide users with the minimal amount of permissions to perform their job
- One IAM User per physical person
- One IAM Role per application
- Rotate access keys
- Enable MFA
- Enable CloudTrail
- What not to do
    - Embed credentials in
        - Code
        - ENV variables
    - Share with
        - Third parties
        - Hundreds of enterprise users
        - Millions of web users

# AWS Organizations

## AWS Organizations Overview

- Global service that eases management of multiple AWS accounts
- Automate creation of AWS accounts via API
- Create accounts per department, per cost center, per dev / test / prod
- Can enable CloudTrail and CloudWatch logs to send logs to central account
- Service Control Policies (SCPs) ⇒ Explicit allows
    - Control who can use what services at Organizational Unit or Account level
    - Does not apply to master account or service-linked roles
    - Apply to all Users and Roles of Member Accounts, even the Root user
    - Explicit denies > Explicit Allows
- Organization Breakdown
    - Master Account ⇒ Root of organization
        - Organizational units ⇒ Business/project-based/environment units
            - Member accounts ⇒ Can be a part of only one organization
- Consolidated billing
    - One bill, many accounts
    - Aggregated volume pricing
    - Reserved instances apply to all accounts
- Detailed billing
    - Published to S3 buckets
    - Import into spreadsheets/Redshift
    - Filter/Sort by services, tags, etc...

## AWS Resource Access Manager (RAM)

- Allows to share AWS resources co-owned with other accounts
- Can share resources with other accounts or within your Organization
- Used to avoid resource duplication
- Can share
    - VPC Subnets ⇒ Most likely to be in the exam
        - Must be from the same organization
        - Allows to launch and share resources in the same subnets
        - Cannot share security groups
        - Can manage own resources in shared subnets but can't view, modify or delete resources owned by other accounts
    - AWS Transit Gateway
    - Route53 Resolvers
    - License Manager Configurations
    - Aurora DB Clusters
    - EC2 Reservations
    - CodeBuild projects and Report Groups
    - Image Builders
    - Dedicated hosts

# Elastic Compute Cloud (EC2)

## What is EC2?

- Most popular AWS offering
- Allows you to launch instances of virtual machines

## Methods of connection to EC2 instances

- SSH ⇒ Mac, Linux, Windows 10
- Putty ⇒ Windows ≤ 10
- EC2 Instance Connect ⇒ All platforms
    - Browser-based SSH connections
    - Creates a temporary key that lets you log in without the secret key
    - Only works on Amazon Linux 2 AMIs
    - Does not work if SSH Port 22 is deleted
- SSH connection ⇒ `ssh -i path/to/your/private/key ec2-user@public-ip`

## Security Groups

- Firewalls at the instance level
- Outside of instances, so you can't check unathorized inbound from inside an instance
- Control inbound and outbound traffic of EC2 instances
- Sets controls on allow, inbound and outbound ports
- Defaults to inbound traffic blocked and outbound traffic authorized
- Act on
    - Port access
    - Authorized IP ranges (IPv4, IPv6)
    - Inbound/outbound network
- Can be attached to multiple instances
- Locked to region/VPC combinations
- If application times out it is an security group issue
- If application returns a "connection error" it's an app-level error or instance error (Not launched/terminated/stopped)
- Can reference other security groups for load-balancing purposes

## Private, Public and Elastic IPs

- Two types of IPs in networking
    - IPv4 ⇒ 122.168.1.1
        - Most common format
        - 2^32 unique addresses (~3.7 billion)
    - IPV6 ⇒ 2001 : 0db8 : 85a3 : 0000 : 0000 : 8a2e : 0370 : 7334
        - Most recent version
        - 2^128 unique addresses (~340 undecillion)
        - Solves IoT issues
- Public IP
    - Machine can be identified on the network
    - Has to be unique globally
    - Can be easily geolocated
- Private IP
    - Machine can be identified on the private network only
    - Has to be unique in the network
    - Two different private neworks can have the same IPs
- Elastic IP
    - A public IP for an instance that does not change when the instance is stopped
    - It is a public IPv4 address that you own as long as you don't delete it
    - Can be attached only to one instance at a time
    - Can be remapped to another instance in case of an instance or software failure
    - Up to 5 Elastic IPs per account (soft limit)
    - Avoid using Elastic IPs as they are usually a sign of a poor architectural decision somewhere
- Default EC2 configuration
    - A private IP for AWS network ⇒ can't use this to SSH in the machine because we are not in the same network
    - A public IP for the WWW
    - To allow HTTP access, an HTTP rule to allow all inboud traffic on port 80 has to be added to the security group

## EC2 User Data

- A script that can be attached to an instance when the machine starts
- It only runs on the instance's first start with the root user ⇒ No need for `sudo su`
- Used to automate boot tasks such as
    - Updating the OS
    - Installing packages
- Need a #!/bin/bash shebang at the top of the script

## EC2 Instance Launch Types

- **On-Demand (least commitment)**
    - Default pricing structure for EC2 instances
    - No upfront cost and no commitment
    - Pay per hour (forward hour) or per second (after first minute) depending on instance
    - Best for short-term, spikey or unpredictable workloads and for testing/experimenting
- **Reserved (best long-term)**
    - For apps that have steady-state, predictable usage or need reserved capacity (eg: databases)
    - **When a Reserved Instance expires, any instances that were covered by the Reserved Instance are billed at the on-demand price**
    - Price is dependent on
        - Term: length of commitment (1 or 3 year)
        - Payment option: all upfront, partial upfront or no upfront
        - Class offering:
            - Standard ⇒ up to 75% cost reduction but can't change instance types
            - Convertible ⇒ up to 54% and can change instance type to greater or equal in value
            - Scheduled ⇒ used for specific time windows and for fraction of day/week/month
    - Unused RIs can be sold in the RI Marketplace
    - RIs can be shared between multiple accounts within an organization.
- **Spot (biggest savings)**
    - Spot instances provide up to 90% discount compared to on-demand
    - AWS has unused compute capacity that it can sell to maximize utility of unused servers
    - It is sold at a spot price that is defined by supply/demand
    - You bid for a maximum price you want to pay
    - Can be terminated at any time if the computing is needed by an on-demand customer and your max price is less than the current spot price
    - Charged on hourly base, if your instance is terminated by AWS you don't get charged for partial hours.
    - If spot price gets above your max price, you have a 2-minute grace period where you can choose to either stop or terminate the instance
        - Stop if the workload needs to be resumed and the state of the machine is important
        - Terminate if the workload is stateless and can be restarted with no problem
    - Spot block
        - Reserve the spot for a specified time frame without interruptions
        - In extremely rare cases this might be reclaimed anyways
    - Most useful for applications that are feasible only at very low computing costs
        - Batch processing
        - Image processing
        - Flexible workloads
        - Big data workloads
    - Best combo ⇒ reserved instance for baseline + spot instances/on-demand for peaks
    - Spot requests
        - Need to specify
            - Max price
            - Number of instances
            - Launch specification ⇒ AMI
            - Validity period
            - Request type
                - One time
                    - Request is fullfilled ⇒ Intance is launched ⇒ request is completed and closed
                - Persistent
                    - If instances are terminated requests are recreated until the validity period ends
                    - To fully cancel a persistent request, cancel the request first and then terminate the instances otherwise the request gets fulfilled again
        - If a request is canceled it does not automatically terminate the instances that were launched at earlier stages with it
- **Dedicated (most expensive)**
    - Used to meet regulatory requirements of server-bound licensing that does not support multitenancy or cloud deployment
    - 3-year period reservations
    - Basically like buying a dedicated hardware and you are physically separated from other customers
    - Can't share across accounts

## EC2 Instance Types

- A good mnemonics is from ACloudGuru ⇒ FIGHT DR MC-PXZ (I pronounce it Fight Doctor McPixie)
- F ⇒ Field Programmable Gate Array (FPGA)
    - Big data, real-time video processing, financial analytics, etc...
- I ⇒ High Speed Storage
    - NoSQL, databases, data warehousing
- G ⇒ Graphics Intensive
    - Video encoding, 3D streaming
- H ⇒ High throughput
    - MapReduce, HDFS distributed file systems
- T ⇒ Burstable general purpose
    - General OK performance that adapt CPU to the needs of the process
    - Comes with burst credits that accumulate over time
        - Burstable
            - CPU increases performance for spikes
            - Uses a burst credit each burst
            - If it bursts when out of credits, CPU performance drops to minimum
        - Unlimited
            - Unlimited CPU performance bursts
            - More expensive
    - Good for Web servers and databases with unexpected/spiky traffic
- D ⇒ Dense storage
    - Hadoop, file servers
- R ⇒ Memory optimized
    - Memory intensive apps
- M ⇒ General purpose
    - App servers
- C ⇒ Compute optimized
    - CPU intensive apps
- P ⇒ Graphics and general purpose GPU
    - Machine learning, deep learning, bitcoin mining
- X ⇒ Memory optimized
    - SAP Hana, Spark
- Z ⇒ Sustained all-core frequency
    - Electronic Design Automation (EDA), gaming, database workloads with high per-core licensing costs

## EC2 Spot fleet

- Combination of Spot instances and optional On-Demand instances
- The Spot Fleet tries to meet targets within price constraints
- Allows to dynamically request the most cost effective Spot instance
- Spot fleet requests can have multiple launch pools
- Stops launching instances when reaching either target capacity or max price
- Launch pool
    - Set of definitions of instances
    - Includes
        - Type
        - AMI
        - AZ
- Spot Fleet strategies
    - Lowest Price
        - Lowest price pool
        - Best for short workloads and cost optimization
    - Diversified
        - Multiple pools
        - Best for consistent availability and long workloads
    - Capacity Optimized
        - Pool for optimal capacity for instance number

## EC2 AMI

- A required base image for the launch of an instance
- Can be customized with instructions on specific settings and software
- Can be use to launch multiple instances with the same configuration
- Region-specific
- Benefits of AMI
    - Pre-installed packages and software
    - Faster boot ⇒ faster than using User data
    - Better security
    - Known configurations
    - Changes can be done in one place instead of reconfiguring multiple instances
    - Many pre-made public free or rentable AMIs optimized for specific softwares/workloads
- AMIs are stored in S3 and are by default private and locked to account/region
- AMIs can be shared with other AWS accounts by modifying the image permission, but ownership does not change when it is shared
- Copying an AMIs that has been shared makes you the owner of that AMI
- You can't directly copy encrypted AMIs or AMIs that have an associated `billingProduct` code
    - Encrypted ⇒ If you have the underlying encryption key, you can use it to re-encrypt it with a key of your own
    - billingProduct ⇒ Can only copy from a running instance

## EC2 Placement Groups

- Custom EC2 placement strategies
- Different strategies available
    - Cluster
        - Instances are in a low latency group in a single rack and a single AZ
        - If a rack fails, all instances fail
        - Good for low-latency, high network throughput dependent applications
    - Spread
        - Instances are spread across different hardware in different AZs (max 7 instances per AZ)
        - Reduced risk for simultaneous failure
        - Good for critical, high-available applications
    - Partition
        - Instances are spread across different partitions and racks within an AZ
        - Partitions are failure-isolated, max 7 per AZ
        - Scales to 100s of instances per group
        - Instances access partition information as metadata
        - Good for Hadoop, Kafka, Cassandra, HDFS

## Elastic Network Interfaces (ENI)

- Representation of a Virtual Network Card in a VPC through a logical component
- Linked to a specific AZ
- Each ENI has different attributes
    - Primary private IPv4 IP address
    - One or more secondary IPv4 IP addresses
    - One Elastic IP (IPv4) per private IP
    - One Public IPv4 IP
    - A MAC address
    - One or more security groups
- ENIs are independent of instances and can be attached dynamically to EC2 instances for failover

## EC2 Hibernate

- Preserves in-memory state
- **You will be billed when your On-Demand instance is preparing to hibernate with a stopping state**
- Instance boot is extremely fast
- It works by making a copy of the RAM state to the root EBS volume
- Requirements
    - Root volume has to be EBS encrypted and large enough to store the RAM dump
    - C, M and R instance families (3 to 5)
    - RAM < 150 GB
    - Not supported for bare metal instances
    - Amazon Linux 2, Linux, Ubuntu and Windows AMIs
    - On-Demand and Reserved instances
    - Can't hibernate longer than 60 days

## EC2 Strategies for High Availability

- Standby instances to enable failover in case of unhealthy instances via CloudWatch events
- Lambda functions to automate instance management in response to CloudWatch events
- Leverage Auto Scaling Groups to create instances in multiple AZ and automate provisioning of failover instances, Elastic IPs transferring, EBS movements and so on

## Exam questions related to EC2 for Solutions Architect

- EC2 Instances are billed by the second, t2.micro is free tier
- SSH on Linux/Mac, PuTTY on Windows
- SSH on Port 22, lock down security group to known IPs
- Timeout issue ⇒ security group issue
- Permission issue ⇒ `chmod 0400` issue
- Security groups can target other security groups or IPs
- Difference between Public, Private and Elastic IP
- Use EC2 User Data to customize the first boot of an instance
- EC2 Launch Types
- EC2 Instance families
- Custom AMIs (region scoped, but can be copied) ⇒ faster boots
- EC2 Placement groups ⇒ cluster, spread, partition
- EC2 Hibernate

# Elastic Load Balancer (ELB)

## Scalability Overview

- Scalability means that a system can handle greater loads by adapting
- Scalability comes in two flavors
    - Vertical scalability
        - Increasing compute capacity (size of instance)
        - Good for non-distributed servers like RDS, ElastiCache
    - Horizontal scalability ⇒ Elasticity
        - Increasing number of servers ⇒ Scale out, scale in
        - Implies distributed systems
        - Used for Load Balancers and Auto Scaling Groups
        - Good for modern web applications
- Scalability has ties to high availability but they are two different concepts
    - HA means having fault tolerance to the loss of data centers
    - Achieved by running the same instances across multiple AZs
    - Can be active (I deploy multiple EC2 instances in different AZs) or passive (I enable multi-AZ deployment in RDS)
    - User for multi-AZ ASG and multi-AZ Load Balancer

## Load Balancing Overview

- Servers that direct internet traffic to and from multiple EC2 instances and spreads the load
- End users interface with one DNS endpoint, the Load Balancer, while the backend can be made of many instances
- Performs health checks of instances to adaptively handle point of failures
- Provides SSL termination for websites (HTTPS)
- High availability across AZs
- Enforce cookie stickiness
- Separates public and private traffic
- **Access logging** is an optional feature of Elastic Load Balancing that is disabled by default.** After you enable access logging for your load balancer, Elastic Load Balancing captures the logs and stores them in the Amazon S3 bucket that you specify as compressed files**

## Elastic Load Balancer (ELB) Overview

- Managed load balancer
- AWS guaranteed
- AWS provides limited configuration but handles upgrades, maintenance and high availability
- Not the cheapest option (setting up your own LB can be cheaper) but easy to set up and integrated with AWS ecosystem
- Load balancers have security groups
    - From the load balancer, the appropriate security group is to allow HTTP traffic from anywhere on port 80 and HTTPS traffic on port 443
    - EC2 instances should expect traffic only from the load balancer, so the source should be the security group on the load balancer
    - A correctly working security group should show the application when pinging the LB endpoint but timeout on the EC2 endpoint
- LBs can scale but not instantaneously
- Error troubleshooting
    - 4xx errors are client induced errors
    - 5xx errors are application induced errors
    - 503 error means at capacity or no registered targets
    - If LB can't connect to instances it is a security group misconfiguration
- Monitoring
    - **ELB Access logs** log all access requests
    - CloudWatch Metrics give aggregate statistics

## ELB Health Checks

- Allow the ELB to know whether instances are available to handle traffic and reply to requests
- Done on a specific port and a specific route
- If HTTP response is not 200, then the instance is unhealthy
- Repeatedly done every 5 seconds

## ELB Target Groups

- Can target multiple services
    - EC2 instances (ASG managed) ⇒ HTTP
    - EC2 tasks (ECS managed) ⇒ HTTP
    - Lambda functions ⇒ HTTP translated to JSON event
    - IP Addresses ⇒ Must be private
- Health checks are at group level

## ELB Types

- All ELBs can be internal (private) or external (public)
- Classic (CLB)
    - Supports HTTP, HTTPS (Layer 5), TCP (Layer 4)
    - Health checks are HTTP or TCP
    - Fixed hostname: `name.region.elb.amazon.aws`
    - Steps for creation
        1. Choose name, VPC, internal/external and configure the listener
        2. Assign security group
        3. Configure health checks
            - Ping port and path
            - Response timeout
            - Intervals
            - Healthy/Unhealthy thresholds
- Application (ALB)
    - Support HTTP, HTTPS, WebSocket
    - Level 7 only ⇒ rule-based URI targeting
    - Load balance to multiple HTTP applications across machines
    - Load balance to multiple applications on the same machine
    - Works on target groups and can target multiple
    - Supports Redirect
    - Allows routing tables to separate target groups
        - URL-based routing
        - Hostname-based routing
        - Query String/Header-based routing
    - Optimal for microservices and container-based applications
    - Dynamic port mapping in ECS
    - Fixed hostname: `name.region.elb.amazon.aws`
    - Application servers don't see client details directly because they receive traffic from private ALB IP so they have to use extra headers if they need connection detail
        - IP ⇒ inserted in `X-Forwarded-For` header
        - Port ⇒ inserted in `X-Forwarded-Port` header
        - Protocol ⇒ inserted in `X-Forwarded-Proto` header
    - Steps for creation
        1. Choose name, listeners, VPC, AZs
        2. Choose security group
        3. Create target group (type, protocol, port)
        4. Create target group (type, protocol, port)
        5. Configure health checks
        6. Register targets
        7. Configure routing rules
- Network (NLB)
    - Supports TCP, TLS, UDP
    - Level 4 only ⇒ Network based targeting
    - Works on target groups
    - Handles millions of requests per second
    - Improves latency (~100ms) and handles long-running connections
    - One static IP per AZ and supports Elastic IPs
    - Applications can see client details (network does not get cut at the LB level) so security groups for instances have to accept TCP traffic
    - Steps for creation
        1. Choose name, listeners, VPC, AZs and IP per AZ (can be autoassigned or Elastic IP)
        2. Create target group (type, protocol, port)
        3. Configure health checks
        4. Register targets
        5. Configure routing rules
- Gateway Load Balancer
    - Uses the GENEVE protocol on port 6081
    - Operates at Layer 3 (Network Layer) – IP
    - Works on target groups: EC2, IP(private)
    - Deploy, scale, and manage a fleet of 3rd party network virtual appliances in AWS
    
## LB Stickiness

- Option to have the same client always be redirected to the same instance behind a load balancer
- Works on CLB and ALB
- Use cookies with controlled expiration date
- Can lead to load imbalances if many requests are consistently routed to the same instance
- Works on a target group level in ALB

## Cross-zone Load Balancing

- Allows each ELB to distribute traffic across all registered instances in any AZ
- On CLB, disabled by default, but it incurs in no charges if enabled
- On ALB, always on and no charges
- On NLB, disabled by default and it incurs in charges

## Connection Draining

- Different names per ELB type
    - CLB ⇒ Connection Draining
    - ALB/NLB ⇒ Deregistration Delay
- Time to complete in-transit requests while instance is de-registering or unhealthy
- Stop sending new requests to the de-registering instance\
- Delay can be between 0 (disabled) and 3600 seconds, defaults to 300 seconds
- Delay setting should depend on length of requests, low if short requests and high if long requests

## SSL/TLS Certificates

- Enables traffic between client and LB to be encrypted in transit
- SSL ⇒ Secure Socket Layer
- TLS ⇒ Transport Layer Security, currently the mainly used ones
- Public SSL certificates are issued by Certificate Authorities (GoDaddy, Symantec, etc...)
- Certificates have expiration dates and must be renewed
- The LB uses X.509 certificate (SSL server certificate) managed through AWS Certificate Manager
- Added as default certificate on the HTTP listener
- Can specify security policies to support legacy clients
- ELB support
    - CLB
        - Only one SSL certificate
        - Must use multiple CLBs to host multiple hostnames with multiple SSL certificates
    - ALB
        - Uses SNI to handle multiple SSL certificates
    - NLB
        - Uses SNI to handle multiple SSL certificates

## Server Name Indication (SNI)

- Handles multiple SSL certificate on one web server to serve multiple websites
- Client indicates in the SSL handshake which website they want to connect to and the server automatically knows which certificate to load
- Only works on ALB, NLB and CloudFront

# Auto Scaling Groups (ASG)

## ASG Overview

- Scale out/in to match an increase/decrease in traffic load
- Ensure minimum/maximum number of instances running
- Free to use, but you pay for the launched instances
- Automatic instance registration to ELB
- IAM roles attached to ASG are inherited by EC2 instances launched by it

## ASG Attributes

- Launch configuration (old)/Launch Templates (new, can specify spot fleets)
    - AMI and Instance Type
    - EC2 User Data
    - EBS volume
    - Security Group
    - SSH Key Pair
    - New launch configurations/templates must be provided to update an ASG
- Minimum/maximum size and desired capacity
    - If an instance fails or is terminated, ASG will replace it automatically
- Network/Subnet information
- ELB information
    - Can terminate unhealthy instances and replace them
- Scaling policies
    - Scale in response to Auto Scaling Alarms
    - ASG can scale based on CloudWatch Alarms (metrics-based)
    - Metrics are across overall ASG
    - Can scale in/out
    - Can be any type of metric
        - CPU usage
        - Average Network in/out
        - ELB requests
    - Can be custom metric
        - Created via PutMetric API and sent to CloudWatch
- Scaling cooldown
    - A period between the trigger of a scaling activity and the time when a new scaling activity can take place
    - Scaling-specific cooldowns override default cooldown (300 seconds)
    - Scale-in policies benefit from shorter scaling-specific cooldowns
    - If up and down scaling is frequent, specify cooldown and CloudWatch alarm periods

## Scaling Policies Implementations

- Target Tracking Scaling
    - Set a target metric to track and scale in order to maintain that target
    - Eg: average CPU usage at 50% (scale in when below, scale out when above)]
- Simple Scaling
    - Specify steps of a target CloudWatch Alarm and scale when it is triggered
    - Increase or decrease the current capacity of the group based on **a single scaling adjustment**
- Step Scaling
    - Specify steps of a target CloudWatch Alarm and scale when it is triggered
    - Increase or decrease the current capacity of the group based on **a set of scaling adjustments**
- Scheduled actions
    - Specific actions at specific time frames
    - Useful for highly predictable patterns

## ASG for Solutions Architect Exam

- ASG Default Termination Policy
    1. Find AZ with most number of instances
    2. If multiple instances are available, delete the ones with the oldest launch config/template
- Lifecycle Hooks
    - As soon as an instance is launched in ASG, it is in service
    - You can perform tasks before the instance goes in service during the pending state and before the instance is terminated during the terminating state
    - Can be used either to install software or configure setting during the pending state or to retrieve logs/data before instance termination
- Launch Template vs. Launch Configuration
    - Both allow to specify AMI, instance type, key pair, security groups, tags and other attributes
    - Launch Configurations
        - Must be recreated every time
    - Launch templates can have multiple versions
        - Multiple version
        - Parameter subsets (partial configurations that can be reused or inherited)
        - Can instantiate both On-Demand and Spot instances
        - Can use T2 Unlimited Burst features
        - Is recommended by AWS

# Elastic Block Storage (EBS)

## EBS Overview

- Network drive used for persistent storage of EC2 data
- Independent from EC2 instance but can be attached to one
- AZ scoped
- Billed per provisioned capacity (GB or IOPS)
- Can scale the size over time

## EBS Volume Types

- Each is characterized in size, IOPS and throughput
- Boot volumes( **High IOPS => SSD** )
    - GP2 (SSD) ⇒ General purpose
        - Recommended for most workloads
            - Virtual desktops
            - Low-latency apps
            - Dev/test environments
        - 1GB - 16TB
        - Small GP2s can burst to 3000 IOPS
        - Max IOPS 16,000 for volume sizes greater than 5,334GB
        - Below 5,334 we have up to 3 IOPS/GB
    - IO1 (SSD) ⇒ High performance
        - For critical business applications with sustained IOPS performance or more than 16,000 IOPS
        - Large databases (MySQL, MongoDB, Cassandra,...)
        - 4GB -16TB
        - Provisioned IOPS 100 to **64,000 (for Nitro)** or** 32,000 (for non-Nitro instances)**
        - **Max IOPS to volume ratio is 50:1**
- Non-Boot volumes( **High throughput => HDD** )
    - ST1 (HDD) ⇒ Low-cost, frequently accessed high throughput
        - For streaming workloads with fast throughput at low costs
        - Big data, data warehouses (Apache Kafka)
        - 500GB - 16TB
        - Max IOPS is 500
        - Max throughput is 500 MB/s and it can burst
    - SC1 (HDD) ⇒ Low-cost, infrequently accessed
        - Throughput optimized storage for large, infrequently accessed volumes of data
        - Optimal if goal is low cost
        - 500GB - 16TB
        - Max IOPS is 250
        - Max throughput is 250MB/s and it can burst

## EBS Encryption

- On EBS creation
    - Data at rest is encrypted
    - Data In flight is encrypted
    - Volumes created from (un)encrypted snapshots are (un)encrypted
    - Snapshots of (un)encrypted volumes are (un)encrypted
    - Copies of unencrypted snapshot can be encrypted
- Low impact on latency
- Automated
- Uses KMS (AES-256)
- Steps to encrypt unencrypted EBS Volume
    1. Create snapshot
    2. Encrypt the snapshot (copying it will automatically encrypt it)
    3. Create an EBS from the snapshot (will inherit encryption)

## EBS Snapshots

- Incremental ⇒ backups only changed blocks
- EBS backup should not be used while there is a lot of traffic because it consumes IO
- Stored in S3 but hidden
- Recommended, but not necessary, to detach the volume to take a snapshot
- Max 10,000 snapshots
- Can copy snapshots across AZs/Regions
- Can make AMI from snapshot
- **Snapshot taking can be automated with AWS Data Lifecycle Manager**
- EBS volumes restored by snapshot need to be pre-warmed (`fio` or `dd` to read full volumes)

## EBS Migration

- Steps to migration
    1. Snapshot the volume
    2. Optionally copy the volume to a different region
    3. Create a volume from a snapshot in the AZ you want

## EBS RAID

- Used for increasing IOPS or to mirror EBS volumes
- Support is OS dependent
- RAID 0 and 1 are most common options
    - RAID 0 ⇒ Increased performance
        - Combine two or more volumes and get combined disk space and I/O
        - If any disk fail, all data fails
        - Good for high IOPS tasks without fault-tolerance or already replicated DBs
    - RAID 1 ⇒ Increase fault tolerance
        - Mirror data to two or more volumes
        - If any disk fail, data will persist in others
        - Uses x times the network, where x is the number of volumes in the RAID
        - Good for fault-tolerance dependent applications
- RAID 5, 6 and 10 are not recommended

## Instance Store (ephemeral storage)

- Some instances use Instance stores instead of EBS volumes
- Instance store is block storage that is destroyed when the machine is stopped or terminated
- Physically attached to a machine
- Significantly high IOPS performance because they are physical (up to 2 million+)
- Good for buffer/cache data
- Data persists reboot
- Can't be resized or backed up automatically
- Risk of data loss if hardware fails ⇒ Replicate across instances if data is sensible/important
- Up to 7TB but can be stripped up to 30TB

# Elastic File System (EFS)

## EFS Overview

- Fully managed network file system that can be mounted on many EC2**(up to 1000)** instances in many AZ
- Highly available and scales automatically, but very expensive and on a pay-per-use basis ⇒ No capacity planning
- Uses **NFSv4.1** protocol
- **rapidly changing data**
- Attach a security group to the EFS to accept connections from EC2 instances
- All EC2 instances that are connected share the same files
- Useful for content management, data sharing and web servers
- Only on Linux AMIs ⇒ Uses POSIX FS with standard API
- Can be encrypted by KMS

## EFS Performance and Storage Classes

- EFS scale
    - 1000s of concurrent NFS clients with 10GB/s+ throughput
    - Can grow to PB-scale NFS automatically
- EFS performance modes
    - General purpose ⇒ Latency optimized (web served, CMS)
    - Max I/O ⇒ Higher latency, higher throughput, higly parallel (Big data, etc..)
- EFS storage tiers ⇒ Can move around with Lifecycle Management
    - Standard: regular frequently accessed files
    - Infrequent Access (EFS-IA): fee on file retrieval, lower storage costs

## EBS, EFS and Instance Store for Solution Architect Exam

- EBS
    - Can be attached to one instance at a time
    - Locked at the AZ level
    - GP2 ⇒ IO increases as disk size increases
    - IO1 ⇒ IO can increase independently of disk size
    - Migration to different AZ works via snapshot restoration
    - Root EBS volumes get terminated by default if instance is terminated
- EFS
    - Can be attached to 100s of instances across AZ
    - Files are shared across instances
    - Only for Linux
    - More expensive than EBS
    - Can cost-manage via Lifecycle management and EFS-IA
- Instance Store
    - Physical storage attached to EC2 instance
    - Files are lost on stop or termination
    - Faster I/O because of physical drive
    - Data persists on reboot
    - Can't be resized or automatically backupped

# Simple Storage Service (S3)

## S3 Overview

- Secure, durable, highly-scalable object storage
- Safe place to store files
- Data is spread across multiple devices and facilities
- Object-based ⇒ upload files into buckets
    - Between 0 bytes and 5 TB
- Unlimited storage

## S3 Basic Concepts

- Universal namespace
    - Bucket names must be globally unique
    - If created in US-West1, ***bucketname**.s3.amazonaws.com*
    - If outside, ***bucketname**.**region-code**.amazonaws.com*
- Objects
    - Key ⇒ name of the object
    - Value ⇒ data of the object
    - Version ID ⇒ important for versioning
    - Metadata ⇒ data about data you are storing
    - Subresources
        - Access Control Lists (ACLs)
        - Torrents
    - On successful upload, returns an HTTP 200 response
    - Objects in a bucket are private by default
    - Opening them creates a pre-signed URL
    - Not for operating systems or databases ⇒ block storage is more appropriate
- Guarantees
    - Built for 99.99% availability
    - Amazon guarantees 99.9% availability
    - Amazon guarantees 99.99999999999% durability (11 9s)
- Features
    - Tiered Storage Available
    - Lifecycle Management
    - Versioning
    - Encryptions

## S3 Data Consistency Model

- Read after Write consistency for PUTS
    - As soon as you upload a new object,  you can immediately retrieve it via GET request on
    - PUT 200 ⇒ GET 200
- Eventual consistency for overwrite PUTS and DELETES
    - If we read an object after updating it or deleting it, you might still get the old version
    - PUT 200 ⇒ GET 200 (old version) or DELETE 200 ⇒ GET 200
- There are no ways of requesting Strong consistency, it is built this way in the S3 data model

## S3 Storage Classes

- S3 Standard
    - 99.99% availability
    - 99.99999999999% durability
    - Stored redundantly across multiple devices in multiple facilities (≥ 3 AZs)
    - Can sustain loss of 2 concurrent facilities
    - Charged in 50 TB / 450 TB / 500 TB increments
    - Use cases
        - Big data analytics
        - Content distribution
- S3 - Infrequent Access (IA)
    - 99.9% availability
    - **Same low latency and high throughput performance of Standard**
    - Minimum 30 days storage
    - **For data that is not accessed frequently but needs rapid access when needed**
    - Lower fee compared to standard S3 but fee on retrieval per GB
    - Can sustain loss of 2 concurrent
    - Charged at constant price GB/Month
    - Use cases
        - Disaster recovery
        - Backups
- S3 - Intelligent Tiering
    - 99.9% availability
    - Minimum 30 days storage
    - Automatically moves data to most cost-effective access tier with no performance impact
    - Monitoring and auto-tiering monthly fee
    - Can sustain AZ-wide outage event
    - Charged in 50 TB / 450 TB / 500 TB increments
    - Use cases
        - Both S3 Standard and S3 IA use cases
- S3 OneZone - IA
    - 99.5% availability
    - Minimum 30 days storage
    - For data that is not accessed frequently and does not require multi-AZ resilience
    - Even lower cost but fee on retrieval per GB
    - Supports SSL for in-transit and encryption at rest
    - Charged at constant price GB/Month
    - Use cases
        - Secondary backup copies of on-premise data
        - Storing data that can be recreated
- S3 Glacier
    - Secure, durable and low cost storage for data archiving
    - Minimum storage of 90 days
    - Very cheap but fee on retrieval per GB
    - Different retrieval options
        - Expedited ⇒ 1 to 5 minutes
        - Standard ⇒ 3 to 5 hours
        - Bulk ⇒ 5 to 12 hours
    - Each item is called an Archive (up to 40GB) and archives are stored in Vaults
    - Charged at constant price GB/Month
    - Long term data retaining
    - Use cases
        - Alternative to on-premise magnetic tape storage
        - Data archival
- S3 Glacier Deep Archive
    - Super-long term storage, extremely cheap
    - Minimum storage of 180 days
    - Different retrieval options
        - Standard ⇒ 12 hours
        - Bulk ⇒ 48 hours
    - Charged at constant price GB/Month
    - Use cases
        - Long-term compliance data archival

## S3 Charges

- Storage
- Requests
- Storage Management Pricing ⇒ tiers
- Data transfer pricing
-** No data transfer cost between S3 and EC2 in the same AWS Region**
- Transfer Acceleration
    - Fast, easy and secure transfer of files over long distances between an S3 bucker and end users
    - Uses CloudFront edge locations
    - As data arrives to edge location it is routed to S3 over optimized network paths
- Cross Region Replication
    - Automatically replicate buckets in different regions for redundancy or disaster recovery

## S3 Lifecycle Rules

- Used to manage automated or deletion of stored objects or or transition to different storage classes through lifecycle rules
- **You can only change the storage class of your objects from S3 Standard storage class to STANDARD_IA or ONEZONE_IA storage after 30 days. This limitation does not apply on INTELLIGENT_TIERING, GLACIER, and DEEP_ARCHIVE storage class**
- Different types of actions
    - Transition actions ⇒ When objects are transitioned to another storage class
    - Expiration actions ⇒ When objects expire (are deleted)
- Can be applied to specific sets of prefixes or certain object tags
- Can be used in conjunction with versioning with separate rules for current and old versions of files
- Can clean incomplete multi-part uploads

## S3 Encryption

- S3 offers 4 different type of at-rest encryptions
    - S3 Managed Keys (SSE-S3)
        - Server side
        - Uses AES-256 encrypted keys managed by AWS
        - Uses header `"x-amz-server-side-encryption":"AES256"`
    - AWS Key Managed Service (SSE-KMS)
        - Server side
        - Uses KMS encrypted keys
        - Leverages KMS user control and audit trail
        - Uses header `"x-amz-server-side-encryption":"aws:kms"`
    - Server Side Encryption with Customer Provided Keys (SSE-C)
        - Uses data keys fully managed by customer outside of AWS
        - S3 doesn't store the key you provide
        - Transfer data only via HTTPS and key has to be provided in HTTP header with every request
    - Client Side Encryption
        - Uses client libraries such as Amazon S3 Encryption Client
        - Client must encrypt/decrypt data when uploading/retrieving from S3
        - Customer fully manages keys and encryption cycle
- Encryption in-flight
    - S3 also exposes a non-encrypted HTTP endpoint and a SSL/TLS encrypted HTTPS endpoint for data transfer from client to S3
    - HTTPS endpoint offers encryption in-flight and is recommended
- Default encryption
    - Allows you to automatically encrypt objects as they are uploaded to S3
    - Evaluated after Bucket Policies

## S3 Security

- User Based
    - IAM policies ⇒ IAM-based rulesets
        - IAM principle can access S3 objects if IAM permissions ALLOW it or the resource policy ALLOWs it AND there are no explicit DENY
- Resource Based
    - Bucket policies ⇒ Allow cross account, bucket wide policies
        - JSON-based
        - Follow the structure
            - Sid ⇒ Policy name
            - Effect ⇒ Allow/Deny
            - Principal ⇒ AWS account or IAM User/Group/Role
            - Action ⇒ Set of actions that are allowed/denied (Eg: `s3:GetObject`)
            - Resource ⇒ AWS ARNs to which the policy is applied (in S3, buckets and objects)
            - Condition (optional) ⇒ Situations where policy applies or not
        - Used to enforce rules (like encryption at upload) or grant access to public or another account
    - Bucket setting ⇒ Created to prevent data leaks
        - Can be set at account level
        - Block public access to buckets and objects granted via
            - New Access Control Lists
            - Any Access Control List
            - New public bucket or access point policy
        - Block public and cross-account access through any public bucket or access point policy
        - Best setting if you know that the bucket should never be public
    - Access Control lists ⇒ Not as used anymore
        - Within Amazon S3, you can use ACLs to give read or write access on buckets or objects to groups of users
        - You can only grant **other AWS accounts** (not specific users) access to your Amazon S3 resources
- General security settings
    - Networking
        - Supports VPC endpoints
    - Logging and Auditing
        - S3 Access Logs can be stored in other buckets'
        - API calls logged by CloudTrail
    - User security
        - MFA delete in versioned S3 Buckets
        - Pre-signed URLS for limited time access

## S3 Pre-Signed URLs

- Can be for downloads (via CLI) or uploads (more complicated, only via SDK)
- Valid for a default 3600 seconds but can change via `—expires-in <seconds>` argument
- Any user with a pre-signed URL inherits permissions of the user that generated that pre-signed URL for GET/PUT
- Use cases
    - Download premium content for logged-in users
    - Dynamic URL generation to download files
    - Allow temporary upload rights to specific buckets
- Generated through CLI or SDK
    - `aws configure default.s3.signature_version s3v4` ⇒ KMS-compatible signature type
    - `aws s3 presign <AWS ARN> —expires-in <seconds> —region <region>` ⇒ Creates the signed URL for the bucket in the specified region

## S3 MFA-Delete

- Forces user to go through MFA before doing certain actions on S3 buckets and objects
    - Object deletion
    - Suspending Versioning
- Reversible actions such as enabling Versioning or placing delete markers don't need MFA
- Needs Versioned S3 buckets
- Can be enabled or disabled only by the root account, not by administrator accounts
- Can be enabled only via CLI ⇒ Implies having Access Keys for root account which is not suggested
- Needs to be operated via CLI, actions via the console would just do nothing

## S3 Websites

- S3 can host static websites at URLs like `<bucket-name>.s3-website.<region>.amazonaws.com`
- **The bucket must have the same name as your domain** 
- In case of a 403 error, it means that the bucket policy does not allow public reads and this must be changed

## S3 Cross-Origin Resource Sharing (CORS)

- An origin is scheme (protocol), host (domain) and port ⇒ https://example.com (HTTPS port 443)
- CORS is web browser-based mechanism to allow other origins while visiting the main origin
    - Requesting http://app.example.com from http://example.com
    - The browser sends preflight requests to see if the origin website is allowed to make requests
    - These requests are by denied unless a CORS header is provided
    - If the CORS is allowed, the cross origin sends a preflight response with the HTTP methods that are allowed
- S3 CORS
    - Uses header is `Access-Control-Allow-Origin`
    - Used to allow clients to do cross-origin requests
        - Use case: we have a bucket that serves HTML, CSS, JS and another that serves the assets
    - Can be allowed for specific origins or for all origins

## S3 Versioning

- Stores all versions of an object, including writes and even deletions
- Uploading a new version adds up to the storage
- Total storage used is the total size of every version of every file
- Best practice to version the bucket
- Once enabled, it can only be suspended - not disabled
- Suspending versioning does not delete previous versions of the file but affects future versions
- Any file that is not versioned prior to enabling versioning will have version "null"
- Integrates with lifecycle rules
- When a new version of a file is uploaded, it defaults to private
- Can permanently delete individual versions
- Can enable MFA Delete capability for additional security

## S3 Object Lock

- A method to store objects using the Write Once, Read Many (WORM) model
- Prevents objects from being deleted or modified
- Can be for a specified amount of time or indefinitely
- Used for regulatory requirements or added security
- Retention periods
    - A fixed amount of time during which an object version is protected
    - Object retains timestamp in the metadata
    - After expiration object can be overwritten/deleted unless it has also a legal hold
- Legal hold
    - Prevents an object version from being overwritten/deleted without associated retention period
    - Remains in place until removed
    - Can be freely placed or removed by users who have `s3:PutObjectLegalHold` permission
- Multiple modes
    - Governance mode
        - Can't overwrite or delete an object version or alter its lock unless you have special permissions
        - Used to protect from deletion by most users while granting permissions to certain users to alter settings if needed
    - Compliance
        - Objects can't be deleted or overwritten by anyone, including root user
        - Retention mode and period can't be altered
        - Used for regulatory reasons

## Glacier Vault Lock

- Deploy and enforce compliance controls for individual S3 Glacier vaults
- Uses Vault lock policies that can't be changed once locked
- Can specify controls such as WORM in the policy

## S3 Performance Optimization

- S3 auto-scales performance to high request rates with 100-200ms latency
- S3 prefixes
    - The pathway between bucket name and object's key
    - S3 has extremely low latency, 1st byte out within 100-200 ms
        - High number of requests per second per prefix
            - **3,500 PUT/COPY/POST/DELETE**
            - **5,500 GET/HEAD**
- Optimization strategies
    - Spreading reads across prefixes
        - More prefixes means more requests per seconds that can be handled
        - Limitations with KMS
            - Separate KMS API calls for upload and downloads
                - Upload ⇒ `GenerateDataKey`
                - Download ⇒ `Decrypt`
            - The API has hard limits with requests per second (KMS Quota)
                - Region specific, but either 5,500, 10,000 or 30,000 requests per second
    - Multipart Uploads
        - **For files over 100MB**
        - **Required for files over 5GB**
        - Allows for upload parellelization
    - S3 Byte-Range Fetches
        - Parallelize downloads by specifying byte ranges
        - If download fails, it fails only in certain byte ranges
        - Can be used to download partial files

## S3 Select

- Use SQL expressions to retrieve subset of data from objects
- Uses server side filtering
- Can filter by rows and columns
- Performance boost of up to 400% and up to 80% cheaper than manipulating the full object

## Glacier Select

- Run SQL queries against Glacier directly
- **Provisioned capacity ensures that your retrieval capacity for expedited retrievals is available when you need it**
- **Without provisioned capacity Expedited retrievals are accepted, except for rare situations of unusually high demand**
- Used by companies in highly regulated industries where companies write data directly to Glacier for compliance purposes

## S3 – Requester Pays

In general, bucket owners pay for all storage and data transfer
costs associated with their bucket.
With Requester Pays buckets, the requester pays the cost of the request and the data download from the bucket.
The requester must be authenticated in AWS (cannot be anonymous).

## Cross-Account S3 Bucket Sharing with AWS Organizations

- Three ways to share S3 buckets across accounts
    - Bucket Policies and IAM roles (across entire bucket) ⇒ Programmatic access only
    - Bucket ACLs and IAM roles (individual objects) ⇒ Programmatic access only
    - Cross-Account IAM roles ⇒ Programmating and console access

## Same- and Cross-Region Replication (SSR/CRR)

- Used to copy objects across Amazon S3 buckets in the same or different AWS Regions
- Requires versioning enabled in the bucket that we are trying to replicate
- Copy is asynchronous and needs appropriate IAM role to S3
- SSR use cases
    - Log Aggregation
    - Live replication between two accounts
- CRR use cases
    - Meeting compliance requirements that dictate data storage to be in very distant locations
    - Minimize latency if end users are in two geographic locations, so that they can access objects faster if there is a copy in a nearby region
    - Increased operational efficiency by separating compute clusters in different regions, as they can operate more efficiently on locally available data copies
- On creation of the replication rule, we can change storage class for replicated objects and we can change object ownership
- New buckets inherit the permission from source bucket
- Objects are already in the bucket are not automatically replicated to the target bucket, so they have to be uploaded manually
- File modifications in one bucket are replicated to the other bucket
- Delete markers and individual version deletions are not replicated across buckets
- No chain replication

## S3 Event Notifications

- Rules that react to certain actions happening in the bucket
- Can be bucket-wide, folder-specific or object-specific
- Can filter on object name
- Sends a notification to SNS, SQS or Lambda Functions
- Typically deliver event notification in seconds but could take up to minutes
- Simultaneous writes to non-versioned objects might trigger only one notification
- Enabling Versioning ensures that every successful write will send a notification
- Use cases
    - Generate thumbnails from uploaded images
    - Tag images based on name

## Transfer Acceleration

- Uses CloudFront Edge Network to accelerate uploads to S3
- Can use distinct URL to upload directly to edge location
    - `bucketname.s3-accelerate.amazonaws.com`

## DataSync

- An online data transfer service that simplifies, automates, and accelerates copying **large amounts** of data **to and from** AWS storage services over the **internet** or **AWS Direct Connect**. 
- **Maximum throughput per task  10 Gbps**
- Install an AWS DataSync agent on your server and connect to the NAS to copy data to/from AWS
- Automatically **encrypts data** and accelerates transfer over the WAS
- Automatic data integrity checks in-transit and at-rest
- In the AWS region, DataSync **securely** connects to **S3(All Classes), EFS, NFS, or FSx for Windows File Server** to copy data and metadata

## S3 Access Logs

- Any API request made to an S3 bucket from any account, authorized or denied, are logged in another S3 bucket
- Best practice is to create a bucket for logging purposes only
- If you log S3 accesses in the bucket itself it will loop forever and grow exponentially
- Used for auditing or analytical purposes

## S3 for Solution Architect

- Operations
    - No operations needed
- Security
    - Access Contro Lists, Bucket Policies, IAM, Server/Client Encryption and SSL
- Reliability
    - 99.99999999999% durability, 99.99% availability, Multi-AZ, SRR/CRR
- Performance
    - Scales to 1000s of reads/writes, Transfer Acceleration, multi-part upload, Byte-range fetches
- Cost
    - Pay-per-usage of storage, network and request numbers

# AWS Athena

## Athena Overview

- Serverless service to perform analytics directly on S3 files
- Data is never copied somewhere else for analytics but remains in S3 buckets
- Best option to avoid provisioning RDS databases for S3 analytics
- SQL to query files
- JDBC/ODBC drivers
- Charged per query and per amount of data scanned
- Built on Presto, works on CSV, JSON, ORC, Avro and Parquet
- Saves results to a new S3 Bucket

## Athena Use cases

- Business Intelligence
- Exploratory work
- Analytics and reporting
- Log query and analytics
    - ELB logs
    - CloudTrail trails
    - VPC Flow logs
    - S3 Access logs
    - CloudFront logs

## Athena for Solution Architect

- Operations
    - Serverless, no operations needed
- Security
    - IAM, S3 security
- Reliability
    - Managed service, highly available
- Performance
    - Built on Presto DB, scales on data size
- Cost
    - Pay-per-query and per TB scanned

# AWS Storage Gateway

## Storage Gateway Overview

- Used for hybrid cloud solutions(Part of your infrastructure on the cloud and part on-premises)
- Gives connection to S3 data on-premise
- Three types of Storage Gateway
    - File Gateway ⇒ File access or NFS
        - Used to expose S3 buckets via NFS or SMB protocols
        - Can be mounted on many servers via NFS
        - Supports S3 Standard, S3 IA, S3 One-Zone IA
        - Each File Gateway has its own IAM Role
        - Caches recently accessed data in the File Gateway
        - Integrated with Active Directory (AD) for user authentication
    - Volume Gateway ⇒ Block storage, iSCSI
        - Used for block storage backed by S3 via iSCSI protocol
        - Uses EBS snapshot for on-premise volume restore
        - Two classes
            - Cached volumes ⇒ Low latency for recent data
            - Stored volumes ⇒ Scheduled backups on S3 for whole on-premise dataset
    - Tape Gateway ⇒ Tape backup, VTL, Backup with iSCSI
        - Transfer backup jobs from tape to the cloud while keeping tools and processes on-premise
        - Virtual Tape Library (VTL) backed by S3 and Glacier
-Hardware appliance
    -Using Storage Gateway means you need on-premises virtualization Otherwise, you can use a Storage Gateway Hardware Appliance

## Storage Gateway Use cases

- Hybrid cloud
- Backup storage
- Tiered storage
- Disaster Recovery

# AWS FSx (File Server)

## FSx Overview

- Fully managed file system share drive
- Launch 3rd party high-performance file systems on AWS
- Scalable distributed file system
- Two types
    - FSx for Windows
        - Windows alternative to EFS (which is POSIX ⇒ Linux only)
        - Share storage between Windows servers
        - Supports SMB protocol and Windows NTFS
        - Integrates with Microsoft Active Directory, ACL, user quotas
        - Built on SSD, millions of IOPS, 100s PB of data, 10s GB/s
        - Can be accessed on-premise and can be Multi-AZ
        - Backed daily to S3
    - FSx for Lustre
        - Parallel distributed file system for large-scale computing
        - For Machine Learning and **High Performance Computing (HPC)**
        - A way to expose S3 buckets as a file system as well to your Linux instances
        - 100s GB/s, millions of IOPS, sub-ms latency
        - Integrates with S3 for read/write
        - Can be used on on-premise servers
- Pay for provisioned storage and throughput capacity (automatic selection for Lustre)
- Encrypted at rest, security via VPC and security groups

## Amazon FSx File Gateway

-The File Gateway is just a proxy and it will access your file systems on the cloud directly. But the feature that the File Gateway has that is the local cache for frequently accessed data And that means that your data on the FSx File Gateway is going to be cached.

# AWS Snow Family

## Snow Overview

- Physical box used for transporting TBs or PBs of data in or out of AWS
- Also used for providing edge computing and access to AWS services
- Alternative to network transfer
- Secure, tamper resistant, encrypted (KMS), tracked via SNS/text
- Pay per job
- Different type of jobs
    - Snow Transfer ⇒ For moving large amounts of data
    - Snow Compute ⇒ For using AWS capability in edge locations
    - Snow Storage ⇒ Aggregating data in edge location
- Different classes
    - Snowcone
        - Up to 8TB for all Snow jobs
        - Can be sent back to AWS offline, or connect it to internet and use **AWS DataSync** to send data
    - Snowball
        - Up to 80TB for all Snow jobs
    - Snowball Edge
        - Improved Snowball, up to 100TB, storage/compute/compute w/GPU optimized
        - Supports EC2 AMIs and Lambda functions for processing on the go
        - types:
            - Snowball Edge Storage Optimized: 80 TB of HDD capacity for block volume and S3 compatible object storage
            - Snowball Edge Compute Optimized: 42 TB of HDD capacity for block volume and S3 compatible object storage 
    
    - Snowmobile
        - Exabyte-scale data transfer service for extremely large data transfers **(more than 10 PB)**
- Used when data transfer would be too slow/expensive over the network
- Used for disaster recovery, large data transfers, decommission of data centers, on-the-go computing
- Cannot import directly into Glacier, has to use S3 lifecycle policies

## How does it work

1. Request Snow device for delivery
2. Install Snow client on-premise
3. Transfer data
4. Ship back to AWS

## Edge Computing 

- Process data while it’s being created on an edge location
- Snow Family Types
    - Snowcone (smaller)
        - 2 CPUs, 4 GB of memory, wired or wireless access
        - USB-C power using a cord or the optional battery
    - Snowball Edge – Compute Optimized
        - 52 vCPUs, 208 GiB of RAM
        - Optional GPU (useful for video processing or machine learning)
        - 42 TB usable storage
    - Snowball Edge – Storage Optimized
        - Up to 40 vCPUs, 80 GiB of RAM
        - Object storage clustering available     
     - AWS OpsHub
        - a software you install on your (computer/laptop) to manage your Snow Family Device

## Snowball into Glacier

- Snowball cannot import to Glacier directly
- You must use Amazon S3 first, in combination with an S3 lifecycle policy           

# AWS Databases

## Questions to ask when choosing a database

- Read-heavy, write-heavy or balanced?
- Throughput needs?
- Does it need to scale for short periods of times?
- How much data do you need to store and for how long?
- Is it likely to grow over time?
- What is the average size of the object stored in the database?
- How frequently are they accessed and who can access them?
- Latency requirements?
- Concurrent users?
- Data durability?
- Source of truth?
- Data model?
- Schema?
- RDBMS or NoSQL?
- Transactional, analytical or search?
- Licensing costs?

## Common Database Solutions

- RDBMS
    - RDS, Aurora
- NoSQL
    - DynamoDB, ElastiCache, Neptune
- Object Store
    - S3 or S3 Glacier
- Data Warehouse
    - Redshift, Athena
- Search
    - ElasticSearch
- Graph
    - Neptune

# Relational Database Service (RDS)

## RDS Overview

- Managed DB service for relational databases
- DBs managed by AWS in the cloud ⇒ Advantages over deploying own DB on EC2
    - Automatic OS patching
    - Automatic provisioning
    - Automatic DB software updates
    - Continuous backups with point-in-time recovery
    - Read replicas for read performance
        - Key goal is to scale reads
        - Work only with SELECT statements (read-only)
        - Read replicas can be Within AZ, Cross AZ and Cross Region
        - Async reads with eventual read consistency
        - Replicas can be transformed in fully independent DB and lose replication
        - Applications using read replicas must have connections to all read replicas
        - Main use case ⇒ Two applications using the same database for different workload
        - Network costs associated with cross-AZ/cross-Region data transfer ⇒ Cheaper to make Within AZ Read Replicas
        - Can set Read Replicas as Multi-AZ for disaster recovery
    - Multi-AZ for disaster recovery
        - Key goal is fault-tolerance
        - Sync replication ⇒ When change happens in master it happens automatically in all standby
        - Single DNS name ⇒ Automatic failover to standby ⇒ Increased availability
        - Automatic failover in AZ failure, network failure, instance or storage failure
        - Self-healing app dynamics ⇒ Automatic interventions
        - Not used for scaling, just fault-tolerance
    - Can scale vertically and horizontally but does not auto-scale
    - Backed by EBS
- Can use multiple DB engines
    - PostgreSQL
    - MySQL
    - Oracle
    - Microsoft SQL Server
    - MariaDB
    - AWS Aurora (Proprietary, fully-managed)

## RDS Backups

- Auto-enabled
- Daily full backups
- Transaction logs updated every 5 minutes
- **7 day retention, max 35 days**
- DB snapshots are manually triggered by user but can be retained as long as you want

## RDS – Storage Auto Scaling

- Helps you increase storage on your RDS DB instance **dynamically**
- When RDS detects you are **running out of free database storage**, it scales automatically
- Avoid manually scaling your database storage 
- You have to set **Maximum Storage Threshold** (maximum limit for DB storage)


## RDS Multi-AZ:

- Your application talks to one DNS name and in case there is a problem with the Master, there will be an automatic failover to the standby database.
- The standby database is just for standby.No one can read to it. No one can write to it, it's just here as a failover in case anything goes on with your Master database.
- Disaster recovery in AWS always looks to ensure standby copies of resources are maintained in a separate geographical area. This way, if a disaster (natural disaster, political conflict, etc.) ever struck where your original resources are, the copies would be unaffected.
- When you provision a Multi-AZ DB Instance, Amazon RDS automatically creates a primary DB instance and synchronously replicates the data to a standby instance in a different Availability Zone (AZ). Each AZ runs on its own physically distinct, independent infrastructure, and is engineered to be highly reliable.
- With a Multi-AZ configuration, EC2 connects to its RDS data store using a DNS address masked as a connection string. If the primary DB fails, Multi-AZ is smart enough to detect that failure and automatically update the DNS address to point at the secondary. No manual intervention is required and AWS takes care of swapping the IP address in DNS.
- Multi-AZ is supported for all DB flavors except aurora. This is because Aurora is completely fault-tolerant on its own.
- Multi-AZ feature allows for high availability across availability zones and not regions.
- During a failover, the recovered former primary becomes the new secondary and the promoted secondary becomes primary. Once the original DB is recovered, there will be a sync process kicked off where the two DBs mirror each other once to sync up on the new data that the failed former primary might have missed out on.
- You can force a failover for a Multi-AZ setup by rebooting the primary instance
- With a Multi-AZ RDS configuration, backups are taken from the standby.


## RDS Read Replicas:
- Read Replication is exclusively used for performance enhancement.
- With a Read Replica configuration, EC2 connects to the RDS backend using a DNS address and every write that is received by the master database is also passed onto a DB secondary so that it becomes a perfect copy of the master. This has the overall effect of reducing the number of transactions on the master because the secondary DBs can be queried for the same data. 
- However, if the master DB were to fail, there is no automatic failover. You would have to manually create a new connection string to sync with one of the read replicas so that it becomes a master on its own. Then you’d have to update your EC2 instances to point at the read replica. You can have up to have copies of your master DB with read replication.
- You can promote read replicas to be their very own production database if needed.
- Read replicas are supported for all six flavors of DB on top of RDS.
- Each Read Replica will have its own DNS endpoint. 
- Automated backups must be enabled in order to use read replicas.
- You can have read replicas with Multi-AZ turned on or have the read replica in an entirely separate region. You can have even have read replicas of read replicas, but watch out for latency or replication lag.
The caveat for Read Replicas is that they are subject to small amounts of replication lag. This is because they might be missing some of the latest transactions as they are not updated as quickly as primaries. Application designers need to consider which queries have tolerance to slightly stale data. Those queries should be executed on the read replica, while those demanding completely up-to-date data should run on the primary node.

## RDS Security and Encryption

- IAM and Networking
    - Best practice is for RDS to be deployed in private subnets, not public
    - Security is driven by security groups
    - IAM policies control who can manage the RDS, using RDS API
    - IAM-based authentication for MySQL and PostgreSQL
        - Uses Auth Tokens obtained by IAM and RDS API calls
        - Auth Tokens last 15 minutes
        - Enforces network in/out SSL encryption
        - Uses IAM to manage users instead of DBMS ⇒ Easier integration with IAM Roles
- At rest encryption
    - Can encrypt master and read replicas using KMS (AES-256)
    - Defined at launch
    - Master needs to be encrypted for read replicas to be encrypted
    - Transparent Data Encryption (TDE) for Oracle and Microsoft SQL Server
- In flight encryption
    - SSL certificates
    - Provide SSL options when connecting to DB
    - If SSL needs to be enforced
        - PostgreSQL
            - `rds.enforce_ssl=1` in Parameter group of RDS Console
        - MySQL
            - `GRANT USAGE ON *.* TO 'mysqluser'@'%' REQUIRE SSL;` in the DB
- General Encryption Operations
    - RDS operates on EBS ⇒ EBS encryption rules are enforced
    - To encrypt an unencrypted RDS
        - Take snapshot of unencrypted DB
        - Encrypt the copy and restore the database from the encrypted copy
        - Migrate applications to restored version and delete the old one

## Creating an RDS database

1. Standard create (custom settings) or Easy create (recommended best practices)
2. Choose DB Engine (PostgreSQL and MySQL are the only free-tier eligible)
3. Templates
    - Production
    - Dev/Test
    - Free Tier
4. Credential Settings (regular DBMS settings such as admin, passwords and similar)
5. EC2 Instances to back the DB
6. Storage (can set automatic scaling)
7. Multi-AZ deployment (not available in free tier)
8. Connectivity (VPC , security groups, public access, AZ, ports)
9. Database options (Authentication, backups, monitoring, automatic maintenance, deletion protection)

## RDS Use Cases

- Store relational datasets (RDBMS, OLTP)
- SQL queries
- Transactional inserts/update/delete

## RDS for Solution Architect

- Operations
    - Small downtime in most operations (failover, maintenance) but some operations are manual (scaling read replicas, EC2, EBS restore)
- Security
    - KMS, security groups, IAM policies, user authorizations and SSL
- Reliability
    - Multi AZ feature, failover in case of failure
- Performance
    - Depends on EC2 instances, EBS volumes and Read Replicas types
- Cost
    - Pay per hour based on EC2/EBS

# Amazon Aurora

## Aurora Overview

- Proprietary software
- Fully managed ⇒ More expensive than RDS but more efficient
- Compatible with drivers for MySQL and PostgreSQL
- Cloud-native and optimized when compared to MySQL (5x) and PostgreSQL (3x)
- Automatically scales in 10GB increments up to 128TB ⇒ Push-button scaling
- Automatically patched with zero downtime and maintained
- Advanced monitoring features
- Backtrack ⇒ point-in-time restore without using backups
- Default multi-AZ deployment, native high availability
    - 6 copies of data across 3 AZs
    - Only needs 4/6 copies for writes, 3/6 for reads
    - Self-healing with P2P replication in case of data corruption
    - Storage is striped across 100s of volumes
- Up to 15 read replicas and super low-latency replication (sub 10ms replica lag)
    - Write to one instance and replicates to all other AZs
    - Replicas can auto-scale
    - If master fails, replicas become new master
    - Fast failover (<30s)
    - Supports Cross-Region Replication
- Aurora DB Cluster
    - Writer endpoint**(also known as a Cluster endpoint)** ⇒ DNS endpoint that always points at the master
    - Reader endpoint ⇒ DNS endpoint that helps with connection load balancing and connects automatically to all the Read Replicas
- Aurora Security
    - Very similar to RDS
    - You are responsible for correct configuration of Security Groups
    - Possibility to authenticate using IAM token (same method as RDS)


### Aurora Cluster Endpoints:
- Using cluster endpoints, you map each connection to the appropriate instance or group of instances based on your use case.
- You can connect to cluster endpoints associated with different roles or jobs across your Aurora DB. This is because different instances or groups of instances perform different functions.
- For example, to perform DDL statements you can connect to the primary instance. To perform queries, you can connect to the reader endpoint, with Aurora automatically performing load-balancing among all the Aurora Replicas behind the reader endpoint. For diagnosis or tuning, you can connect to a different endpoint to examine details. 
- Since the entryway for your DB Instance remains the same after a failover, your application can resume database operation without the need for manual administrative intervention for any of your endpoints.

### Aurora Reader Endpoints:
- Aurora Reader endpoints are a subset of the above idea of cluster endpoints. Use the reader endpoint for read operations, such as queries. By processing those statements on the read-only Aurora Replicas, this endpoint reduces the overhead on the primary instance. 
- There are up 15 Aurora Read Replicas because a Reader Endpoint to help handle read-only query traffic.
- It also helps the cluster to scale the capacity to handle simultaneous SELECT queries, proportional to the number of Aurora Replicas in the cluster. Each Aurora DB cluster has one reader endpoint.
- If the cluster contains one or more Aurora Replicas, the reader endpoint load-balances each connection request among the Aurora Replicas. In that case, you can only perform read-only statements such as SELECT in that session. If the cluster only contains a primary instance and no Aurora Replicas, the reader endpoint connects to the primary instance directly. In that case, you can perform write operations through the endpoint.

## Aurora Serverless

- Automated database instantiation on a pay-per-second basis
- Scales based on usage ⇒ No capacity planning
- Good for intermittent and unpredictable workloads

## Aurora Global Database

- Best way to do Cross-Region Replication
- One primary Region for Read/Write
- Up to 5 secondary read-only Regions with less than 1 second replication lag
- Up to 16 read replica per secondary region ⇒ Decrease latency
- Promoting another region to primary has Recovery Time Objective (RTO) of < 1 min

## Aurora Multi-Master

All the Aurora instances can do writes and in case one Aurora instance fails then you can automatically failover to another one giving you immediate failover for the writer node.

## Aurora Machine Learning

- Enables you to add ML-based predictions to your applications via SQL
- Simple, optimized, and secure integration between Aurora and AWS ML services

## Aurora Use Cases

- Same as RDS, but more flexible and performant while needing less maintenance
- **for online transactional processing (OLTP)**

## Aurora for Solution Architect

- Operations
    - Fully managed, so less operations
    - Auto-scaling storage
- Security
    - KMS, security groups, IAM policies, user authorizations and SSL
- Reliability
    - Multi AZ feature, highly available, Serverless options
- Performance
    - Up to 15 Read Replicas, up to 5x performance of RDS
- Cost
    - Pay per hour based on EC2/EBS

# AWS ElastiCache

## ElastiCache Overview

- Managed Redis or Memcached caches
- In-memory key-value databases with really high performance and low latency
- Reduce load on read-intensive workloads ⇒ Reads from RAM are faster
- Used for stateless applications
- Write scaling with sharding, read scaling with Read Replicas
- Multi-AZ with failover
- Automatically patched with zero downtime and maintained
- Advanced monitoring features

## Cache Security

- Does not support IAM authentication
- IAM policies on ElastiCache are for API-level security
- SSL in-flight encryption for Redis
- Redis AUTH ⇒  You can set a password/token on cluster creation
- Memcached supports SASL-based authentication

## Redis vs Memcached

- Redis ⇒ More RDS like, possible backup
    - Multi-AZ with auto-failover
    - Data durability with Redis AOF persistence
        - Every write operation received by the server is logged
        - At server startup, they are replayed
    - Read Replicas for high availability
    - Backup and restore features (Redis RDB)
- Memcached ⇒ Pure cache, built to speed up applications, lost on server down
    - Multi-node partitioning ⇒ Sharding
    - Non persistent cache
    - No backup or restore
    - Multithreaded
    - Architectural Patterns
        - Lazy Loading
            - All read data is cached (can be stale)
        - Write through
            - Update cache data when written to DB (no stale)
        - Session Store
            - Temporary session data (using time to live settings)
      
     - Redis really is for high availability, backup, Read Replicas
     - Memcached is a pure case of partitioning of data (sharding)
     - Memcached is really intended to serve as a pure cache. While Redis can also be used as a database.

 
## ElastiCache Use Cases

- Query storage ⇒ reduce same read workloads
    - ElastiCache sits in front of an RDS
    - An application first queries ElastiCache
    - If the queried data is present in ElastiCache, it returns it
    - If the queried data is not present in Elasticache, it queries RDS and writes the query results in ElastiCache
    - Need to implement invalidation rules in the cached queries to ensure correct data is returned
- User Session ⇒ Stateless applications need to know that a user is logged in
    - An user logs in in an application
    - The application writes session data to ElastiCache
    - All other applications attempt to retrieve the session data before relogging the user in

## ElastiCache for Solution Architect

- Operations
    - Small downtime in most operations (failover, maintenance) but some operations are manual (scaling read replicas, EC2, EBS restore)
- Security
    - KMS, security groups, IAM policies, no IAM Auth but Redis Auth and SSL
- Reliability
    - Clustering, sharing, multi-AZ
- Performance
    - Sub-ms performance, read replicas for sharding, in-memory
- Cost
    - Pay per hour based on EC2/EBS

# **DynamoDB**

## DynamoDB Overview

- Fully managed serverless NoSQL data store
- Exclusively backed by SSD volumes
- Single-digit millisecond response time at any scale
- Built-in security, resiliency, fault-tolerance, durability
- Replicated across multiple AZs (3 default)
- No limits to storage and throughput
- Read/Write Capacity Modes
    - **Provisioned Mode (default)**
        - Need to provision reads and writes throughput (pay for provisioned)
            - Read Capacity Unit (RCU) ⇒ Throughput for read ($0.00013 per RCU)
                - 1 strongly consistent read and 2 eventually consistent read of 4KB/S per RCU
            - Write Capacity Unit (WCU) ⇒ Throughput for write ($0.00065 per WCU)
                - 1 write of 1KB/S per WCU
            - Throughput can auto-scale or can be exceeded temporarily with Burst credits
                - `ProvisionedThrougputException` error when out of credits
    - **On-Demand Mode**
        - Read/writes automatically scale up/down with your workloads
- Point-in-time restore
- Global Table ⇒ Multi-region fully-replicated tables
- Can use Database Migration Service (DMS) to migrate from other DBs
- Can launch DynamoDB on local machines for local development purposes
- Recent features
    - Transactions ⇒ all or nothing operations
    - On demand capacity ⇒ Scales automatically, good for unpredictable or extremely low throughput

## DynamoDB Tables

- Tables, items, attribute
- No joins/relationships ⇒ Schemaless
- Key value and documents
- Unique primary key is required and decided at creation time
- Secondary indexes
- Query on primary key, sort key or indexes
- No table size limit but **400KB** item size limit
- Infinite number of items per table
- Each item has attributes that can be added over time and can be null
- Item-level TTL
- Supports scalar (String, number, bool, binary, null), document (list, map) and set data types
- Global Table can be enabled when using DynamoDB Streams ⇒ Streams create changelog, use changelog to replicate data across Regions

## DynamoDB Accelerator (DAX)

- Cache for DynamoDB, writes go through DAX and to DynamoDB
- Microseconds latency for cached reads and queries
- 5 minute cache TTL
- Up to 10 multi-AZ DAX nodes in the cluster
- Encryption at-rest (KMS, VPC, IAM, CloudTrail)

## DynamoDB Streams

- Changelog of all events in DynamoDB that are retained for 24 hours
- Enable event driven programming
- Integrate with Lambda for real-time computing
- Necessary for Cross-Region Replication

## DynamoDB Global Tables

- Make a DynamoDB table accessible with low latency in **multiple-regions**
- Active-Active replication
- Applications can **READ** and **WRITE** to the table in any region

## DynamoDB –Time To Live (TTL)

 Automatically delete items after an expiry timestamp

## DynamoDB - Indexes

 High level: allow to query on attributes other than the Primary Key


## DynamoDB Security

- VPC Endpoints
- IAM access control
- KMS encryption at-rest
- SSL/TLS encryption in-flight

## DynamoDB Use Cases

- Serverless applications, distributed serverless cache
- Ad impression/clickthrough
- Gaming leaderboards
- Shopping carts
- Operational state/history (Jenkins jobs logs, CI/CD logs)
- Session/state storage

## DynamoDB for Solution Architect

- Operations
    - No operations needed, auto-scaling, serverless
- Security
    - KMS, security groups, IAM policies, and SSL in-flight
- Reliability
    - Backups, multi-AZ
- Performance
    - Single-digit ms performance at any scale
- Cost
    - Pay per provisioned capacity and storage usage, can auto-scale

# AWS Neptune

## Neptune Overview

- Fully managed graph database
- Highly available across 3 AZs and up to 15 Read Replicas
- Point-in-time recovery
- KMS, IAM, SSL

## Neptune Use Cases

- Social network
- High relationship data
- Knowledge graph

## Neptune for Solution Architect

- Operations
    - Similar to RDS
- Security
    - VPC, IAM, KMS, SSL, IAM Authentication
- Reliability
    - Multi AZ, clustering
- Performance
    - Best when using clusters
- Cost
    - Pay per provisioned node

# AWS ElasticSearch

## ElasticSearch Overview

- Fully managed Elasticsearch service
- Used for search, full or partial, of any field
- Complementary to other databases
- Used for Big Data applications
- Can be provisioned on clusters of instances
- Integrates with Kinesis Data Firehose, AWS IoT and CloudWatch logs
- Security via AWS Cognito (for third-party use), IAM, KMS, SSL & VPC
- ELK stack ⇒ Kibana for visualization and Logstash for log ingestion

## ElasticSearch Use Cases

- Ideal for search and analysis of data
- Search engines

## ElasticSearch for Solution Architect

- Operations
    - Similar to RDS
- Security
    - Cognito, IAM, VPC, KMS, SSL
- Reliability
    - Multi-AZ, clustering
- Performance
    - Based on open-source Elasticsearch, scales to petabytes
- Cost
    - Pay per provisioned node

# AWS Redshift

## Redshift Overview

- Fully managed petabyte-scale data warehouse
- Columnar data storage
- SQL interface for querying
- Pay as you go based on provisioned instance
- Integrates with visualization/dashboarding tools like AWS Quicksight and Tableau

## Redshift Technology

- Fork of PostgreSQL 8.0.2 and SQL compliant
- Loads data from S3, RDS, DynamoDB, external DBs
- Integrates with AWS Database Migration Service
- Can connect via JDBC/ODBC
- Distributed ⇒ High availability
- **Massively Parallel** Query Execution (MPP) ⇒ High performance
- 1 to 128 nodes with 160GB per node
- Works on a leader - follower architecture
    - Leader node ⇒ query planning and result aggregation
    - Compute node ⇒ listen for leader instruction, perform queries and send results back
- Redshift Spectrum
    - • Query data that is already in S3 without loading it
    - Not serverless, need to provision clusters ⇒ Different from Athena
    - Query is executed by Spectrum nodes
- Backup and restore options
    - Snapshots
        - Stored in S3
        - Point-in-time backup of a cluster
        - Can be automated and configured with specific retention periods
        - Incremental ⇒ Save only what changes and doesn't rewrite the whole thing
        - Can restore snapshot in a new cluster
        - Can automatically copy snapshots to other Regions ⇒ Disaster Recovery
- Monitoring with CloudWatch/CloudTrail
- VPC, IAM, KMS level security
- Enhanced VPC Routing
    - COPY/UNLOAD goes via VPC and not public internet ⇒ Better security/performance

## Redshift Use Cases

- Ideal for **OLAP(online analytical processing)** and BI applications

## Redshift for Solution Architect

- Operations
    - Similar to RDS
- Security
    - Similar to RDS
- Reliability
    - Highly available, self-healing
- Performance
    - 10x performance compared to other DW tools, possibility to compress data
- Cost
    - Pay per provisioned node

# AWS Database Migration Service

## DMS Overview

- Quickly migrate databases from on-prem to resilient, self-healing AWS services
- Source database is still available
- Supports homogeneous and heterogeneous migrations and continuous data replication
- Works on EC2 instances that are set up to perform the migration tasks
- The DMS-ready EC2 instance pulls data from sources and moves them to targets
    - Sources ⇒ On-Premise or EC2-based DBs, Azure SQL DBs, RDS, S3
    - Targets ⇒ On-Premise or EC2-based DBs, RDS, Redshift, DynamoDB, S3, ElasticSearch Service, Kinesis Data Streams, DocumentDB

## AWS Schema Conversion Tool (SCT)

- A software that converts schemas from one engine to another for heterogeneous migrations
- Can transform both OLTP and OLAP

# AWS DataSync

## DataSync Overview

- Used to move large datasets from on-premise to AWS
- Data can be moved to S3, EFS or FSx for Windows
- Can move data from NAS or FS via NFS or SMB protocols
- Can move data from EFS in one region to EFS in another region
- Can schedule data replication hourly, daily or weekly
- Needs the DataSync agent installed on premise to work

# AWS Route 53

## Route 53 Overview

- Managed DNS
- Domain Name Registrar
    - Can buy domains
    - Can import 3rd party domains and use Route 53 as DNS provider by updating 3rd party registrar NS records
- AWS most common DNS records
    - A ⇒ IPv4
    - AAAA ⇒ IPv6
    - CNAME ⇒ Hostname to another hostname
    - Alias ⇒ Hostname to AWS resource
- Can use public domains (www.example.com) or private domains (resolved in your VPC)
- Different features
    - Load balancing
    - Health checks
        - Default interval is 30s (can be set to 10s for $$$)
        - 15 health checkers are launched in the backend ⇒ avg 1 request every 2 seconds
        - An endpoint is considered
            - Unhealthy if it fails 3 checks in a row
            - Healthy if it passes 3 checks in a row
        - HTTP, HTTPS and TCP checks with no SSL certificate
        - Can be integrated in CloudWatch
    - Routing policies
- Pay $0.50/month/hosted zone

## Records

- How you want to route traffic for a domain
- Each record contains:
    - Domain/subdomain Name – e.g., example.com
    - Record Type – e.g., A or AAAA
    - Value – e.g., 123.456.789.123
    - Routing Policy – how Route 53 responds to queries
    - TTL – amount of time the record cached at DNS Resolvers
    
## Hosted Zones

 - Container for records that define how to route traffic to a domain and its subdomains
 - A public Hosted Zones
 – contains records that specify how to rout traffic on the Internet (public domain names) application1.mypublicdomain.com
 - Private Hosted Zones 
 - contain records that specify how you route
 - traffic within one or more VPCs (private domain names) application1.company.internal
 - You pay $0.50 per month per hosted zone
 
## DNS TTL

- Along with the DNS response, we send a TTL that specifies how long that response will live in the browser cache
- High TTL ⇒ Less traffic on DNS but chances of outdated records
- Low TTL ⇒ More traffic on DNS but low chances for outdated records/easy to change records

## CNAME vs Alias

- CNAME
    - Point hostname to another hostname
    - Only for non-root domain (xxx.domain.com, not directly domain.com)
- Alias
    - Hostname to AWS Resource
    - Works for both root and non-root domains
    - Free, comes with health checks
    - You cannot set an ALIAS record for an EC2 Dns name.

## Active-Active Failover VS Active-Passive Failover

- **Active-Active Failover uses all available resources all the time without a primary nor a secondary resource**
- Active-Passive Failover: a primary resource or group of resources is available most of the time and a secondary resource is on standby in case all the primary resources become unavailable.
## Route 53 – Health Checks Types

- Monitor an Endpoint
    - About 15 global health checkers will check the endpoint health
    - Health Checks can be setup to pass / fail based on the text in the first 5120 bytes of the response
- Calculated Health Checks
    - Combine the results of multiple Health Checks into a single Health Check
    - You can use OR, AND, or NOT
-Private Hosted Zones
    -Route 53 health checkers are outside the VPC
    -They can’t access private endpoints (private VPC or on-premises resource)
    -You can create a CloudWatch Metric and associate a CloudWatch Alarm, then create a Health Check that checks the alarm itself 

## Routing Policies

- Simple
    - Use when routing a single resource
    - Can't attach health checks
    - If multiple values are returned, client chooses a ranom one
- Weighted
    - Control % of requests that go to a specific endpoint
    - Can be associated with health checks
    - Use cases
        - Test % of traffic on new application versions
        - Split traffic among regions
- Latency
    - Redirect to server with the least latency to the end user
    - Determined in terms of user to their AWS Region
    - Can be associated with health checks
- Failover
    - Redirect traffic from a primary unhealthy endpoint to a healty secondary one
    - Needs a health check associated with it
- Geolocation
    - Redirect to specific endpoints based on location
    - Different from latency because it does not check whether that endpoint has the best performance for the user
    - Best approach is to create a base case if there's no match for the user's location
    - Ideal for serving localized and location-sensitive content
- Geo-proximity Routing
   lets you choose where traffic will be sent based on the geographic location of your users *and* your resources. You can choose to route more or less traffic based on a specified weight which is referred to as a bias. This bias either expands or shrinks the availability of a geographic region which makes it easy to shift traffic from resources in one location to resources in another. To use this routing method, you must enable Route53 traffic flow. If you want to control global traffic, use Geo-proximity routing. If you want traffic to stay in a local region, use Geolocation routing.
- Multi-Value
    - Routing to multiple resources ⇒ More advanced version of Simple routing
    - Can attach health checks
    - Up to 8 healthy records are returned for each Multi-Value query
    - Useful in conjunction with ELB but not a substitute

# CloudFront and Global Accelerator

## CloudFront Overview

- Content delivery network
- Improves read performance by caching content in edge locations for specific TTL
- DDoS protection, integrates with AWS Shield and AWS Application Web Platform
- Can expose external HTTPS and can talk to internal HTTPS backend
- Geo Restriction
    - Can allow/deny countries from accessing your content
        - **Whitelist**: Allow your users to access your content only if they're in one of the countries on a list of approved countries.
        - **Blacklist:** Prevent your users from accessing your content if they're in one of the countries on a blacklist of banned countries.
    - Enforced by 3rd party Geo-IP database
    - Used to enforce Copyright laws
- Great for static content that needs to be available everywhere

## CloudFront Origins

- S3 buckets
    - For distribution of files
    - Enhanced security with CloudFront Origin Access Identity (OAI) as IAM Roles
    - Can be used as a way to upload files to S3
    - to access your S3 buckets the edge location is going to use an OAI, it's an IAM role for your CloudFront origin. And using that role is going to access your S3 buckets and the bucket policy is going to accept.
- Custom Origin (HTTP)
    - Application Load Balancer ⇒ Security group needs to allow Edge Location IP traffic
    - EC2 Instance ⇒ Security group needs to allow Edge Location IP traffic
    - S3 Website (must enable S3 static website hosting)
    - Any HTTP backend

## CloudFront at a high level

- The client will send an HTTP request directly into CloudFront,  then the edge location will forward the request to your origin and that includes the query strings. 
- The edge location will cache the response and return the response back to our clients.
- The next time another client makes a similar request, the edge location will first look into the cache before forwarding the request to the origin.


## CloudFront vs S3 Cross Region Replication:

- CloudFront:
    - Global Edge network
    - Files are cached for a TTL (maybe a day)
    - **Great for caching static content that must be available everywhere**
- S3 Cross Region Replication:
    - Must be setup for each region you want replication to happen
    - Files are updated in near real-time
    - Read only
    - **Great for dynamic content that needs to be available at low-latency in few regions**


## CloudFront Signed URL/Cookie

- Uses policies with URL expiration, IP ranges and trusted signers to distribute paid shared content to premium users over the world
- URL should be short-lived for shared content and as long as necessary for private content
- Signed URL gives access to one file ⇒ Need one Signed URL per file
- Signed Cookie gives access to multiple files

## CloudFront Advanced Concepts:

- Pricing:
    - The cost of data out per edge location varies
    - The price is per gb of transferred data
- Price Classes: to reduce the number of edge locations for cost reduction
    - 1. **Price Class All**: all regions – best performance
    - 2. **Price Class 200**: most regions, but excludes the most expensive regions
    - 3. **Price Class 100**: only the least expensive regions
- Multiple Origin
    - To route to different kind of origins based on the content type
    - Exp: /api/ => ALB, /* => S3.
- Origin Group
   - To increase high-availability and do failover
   - Origin Group: one primary and one secondary origin
   - If the primary origin fails, the second one is used
- Field Level Encryption
   - Protect user sensitive information through application stack
   - Exp: encrypt a user's credit card on the edge location with a public key, then the web server will decrypt it using a private key.
   
## Unicast IP vs Anycast IP

- Unicast IP: one server holds one IP address
- Anycast IP: all servers hold the same IP address and the client is routed to the nearest one
       
## AWS Global Accelerator

- Uses AWS internal network to serve content from Edge Locations to global clients
- Leverages **2 Anycast IPs** to send directly the traffic to the Edge Locations, which is then routed to your application
- Works with **public or private ALB, NLB, EC2 Instances and Elastic IPs**
- Consistent performance by using AWS nework, fast regional failover and intelligent routing to lowest latency
- Performs Health checks over the system
- Secure due to only 2 IPs that need to be whitelisted and AWS Shield integration for DDoS Protection
- Good for non-HTTP uses such as UDP (like gaming), IoT devices or VoIP or HTTP uses that require static IP globally


## Difference between CloudFront and Global Accelerator

- Both use Edge Locations
- Both integratw with AWS Shield for DDoS Protection
- CloudFront improves content deliver for static (cacheable) and dynamic content by serving content from the Edge Locations
- Global Accelerator proxyes requests at Edge Location to applications running in one or more Regions
- Global Accelerator is a good fit if you have a non-HTTP uses cases such as gaming, IoT or Voice over IP, HTTP use case that requires a static IP addresses globally


# AWS Lambda

## Lambda Overview

- Virtual, serverless functions
- Short executions
- Run on demand
- Automated scaling
- Pay per request and compute time
- Integrated with many AWS services
    - API Gateway
    - Kinesis
    - DynamoDB
    - S3
    - CloudFront
    - CloudWatch EventBridge/Logs
    - SNS/SQS
    - Cognito
    - Etc...
- Supports multiple programming languagues
    - Node.js
    - Python
    - Java 8
    - C#
    - Ruby
    - Custom Runtime API if community-supported
    - NO Docker ⇒ Use ECS or Fargate
- Easy monitoring integration with CloudWatch
- Can provide more resources per functions up to 3GB of RAM per call
- Increasing RAM will improve also CPU and network capabilities
- Per-region limits
    - Execution
        - Between 128MB and 3008MB of RAM in 64MB increments
        - 15 minutes max execution
        - Soft limit of 1000 concurrent executions
        - 4KB ENV variables
        - Disk capacity of 512MB in `/tmp`
    - Deployment
        - Compressed size of ≤ 50MB
        - Uncompressed size of ≤ 250MB
        - Use `/tmp` for additional files at startup

## Lambda Use Cases

- Performing actions on triggers
- Serverless CRON jobs

## Lambda@Edge

- Lambda functions deployed alongside CloudFront
- Advanced management of CDN content via request filtering
- Types of Lambda@Edge request manipulation
    - Viewer requests ⇒ After viewer sends request to CloudFront
    - Origin request ⇒ Before CloudFront forwards request to origin
    - Origin response ⇒ After origin sends response to CloudFront
    - Viewer response ⇒ Before Cloudfront forwards response to viewer

## Lambda@Edge Use Cases

- Build low-latency dynamic applications
- Content security and privacy, bot protection
- SEO
- Data routing
- A/B testing
- User authentication, tracking and analytics
- Real-time content processing

# AWS API Gateway

## API Gateway Overview

- Serverless service to create APIs
- Supports REST and WebSocket protocols
- Handles versioning, deployment environments and authentication/authorization
- API Keys, request throttling
- Open standards for API definitions and exports like Swagger and OpenAPI
- Validate/transform request and responses
- Generate SDK and API specs
- Cache API responses
- Several integrations
    - Lambda ⇒ Invoke Lambda function, Easy way to expose REST API backed by AWS Lambda
    - HTTP ⇒ On-premise APIs, useful to use additional API Gateway features
    - AWS Services ⇒ Useful to use additional API Gateway features
- Endpoint Types
    - Edge optimized ⇒ Global clients, routed through CloudFront
    - Regional ⇒ Clients in the same region as the API
    - Private ⇒ Accessed only by VPC through ENI and resource policies

## API Gateway Security

- IAM Permissions ⇒ For users in your AWS account
    - Create IAM policy and attach it to a IAM User or Role
    - API Gateway verifies the permissions
    - Uses **Sig v4** to identify correct IAM credentials in headers passed on with the call
- Lambda/Custom Authorizer ⇒ 3rd party authorizations
    - Validates the tokens being passed in header via Lambda
    - Caches results of authorization
    - Used for OAuth/SAML/3rd party auth
    - Lambda function must return an IAM policy for the user
- Cognito User Pools ⇒ Custom user pools backed by 3rd party login (FB, Google)
    - Uses Cognito to manage user lifecycle
    - Works only for authentication and not authorization

# AWS Cognito

## Cognito Overview

- Used to provide users an identity to use our applications and servers
- Cognito User Pools
    - Serverless database of users for apps
    - Login with email/password, verify email/phone numbers, MFA capabilities
    - Can use Federated Identities ⇒ Login with Facebook, Google, GitHub, SAML...
    - Returns JSON Web Tokens (JWT) for authentication purposes
    - **Integrates with API Gateway**
    - Sign-in functionality
- Cognito Federated Identity Pools (Federated Users)
    - Provide AWS credentials to users to access AWS resources and services
    - Identities provided by Federated Identity Providers or anonymous
    - Temporary AWS credentials usign Secure Token Service (STS) with pre-defined IAM Policies
    - Integrates with User Pool
- Cognito Sync
    - Sync data from devices to Cognito to store preferences, configurations and states
    - Offline capabilities (sync back when online)
    - Largely replaced by AWS AppSync

# AWS Serverless Application Model (SAM)

## SAM Overview

- YAML-based framework to deploy serverless application infrastructures
- Includes configurations for all serverless services (Lambda, API Gateway, Cognito, DynamoDB...)
- Allows to run serverless environments locally
- Use CodeDeploy to deploy Lambda functions

# Elastic Beanstalk

## Elastic Beanstalk Overview

- Platform as a Service
- Provide the code to your app and Elastic Beanstalk automatically creates and manages the underlying architecture
- Managed service
    - Instance configuration managed by AWS
    - Deployment strategy managed by AWS but can be configured
- Leverage all components such as EC2. RDS, ELB, ASG
- Free, pay only for deployed services
- Three architecture models
    - Single instance ⇒ Good for dev environments
    - ELB + ASG ⇒ Good for production or pre-production of web apps
    - ASG-only ⇒ Good for production non-web apps
- Three main components
    - Application
    - Application Version
    - Environment name
- Deploy application to one environment and promote them across environments
- Rollback to previous application versions
- Full control over lifecycle of environments
- Many platforms are supported
    - Go
    - Python
    - Node
    - Java
    - .NET
    - PHP
    - Ruby
    - Single/Multi container Docker
    - Preconfigured Docker

# AWS Programmatic Access

## Programmatic access Overview

- Ways to interact with AWS and its services without using the console
- CLI, SDK ⇒ via local machines or EC2 instances
- AWS Instance Metadata Service ⇒ EC2
- The AWS CLI is a wrapper around Boto3, the Python AWS SDK
- There are best practices to use CLI and SDK in secure ways

## IAM Roles

- Best way to use the AWS CLI on EC2 instances, do it 100% of the times
- Roles can be attached to the EC2 instance
- The role comes with a policy that specifies what that instance can do

## EC2 Instance Metadata

- Allows EC2 Instances to know what they are able to do without using IAM Roles
- [http://169.254.169.254/latest/meta-data](http://169.254.169.254/latest/meta-data) is an internal AWS IP that only works on EC2 instances
- It lists all the metadata related to the EC2 instance

## SDK Overview

- Methods to perform actions on AWS directly from application code
- Many official SDKs
    - Java
    - .NET
    - Node.js
    - Python (boto3)
    - Go
    - Ruby
    - PHP
    - C++
- Three ways to handle credentials
    - AWS credentials generated by the CLI `aws config`
    - Instance Profile Credentials using an IAM Role
    - Environment Variables (not recommended)
- Never store credentials in your code
- Exponential Backoff
    - Any rate-limited API that fails because of too many calls needs to be retried using EB
    - The calls will be performed with exponential delay, waiting twice as long before calling again

# Application Communication

- In a large system, applications will need to communicate among each other
- Used in serverless and microservices architectures
- Two application communication patterns
    - Synchronous ⇒ Application to application
    1. Asynchronous ⇒ Event based, application to queue to application
- Synchronous communication can be problematic
    - Tight coupling between components
    - Spikes in traffic can overwhelm infrastructure
    - Errors in components avalanche down
- Solution patterns to decoupling applications
    - Queue model ⇒ SQS
    - Pub/Sub model ⇒ SNS
    - Real-Time Streaming model ⇒ AWS Kinesis

# AWS Simple Queue Service (SQS)

## SQS Overview

- A queue based inter-process communication middleware system
- Producers send messages to a queue, which is polled by consumers
- Can be integrated with ASG ⇒ too many messages per instance triggers more instances

## SQS Technology

- SQS Message production
    - Body
        - Up to 256kb
        - String
    - Metadata
        - Optional key-value pairs with message attributes
    - Returns
        - Message identifier
        - MD5 hash of the body
- Message consumption
    - Poll for messages, up to 10 at a time
    - Consume message within visibility timeout period
    - Delete using message ID and receipt handle with `DeleteMessage` API
- Visibility Timeout
    - Period of time during which a message is invisible to all but one consumer
    - Starts when a consumer polls a message from the queue
    - Between 0 seconds and 12 hours (30 seconds default)
    - If it is too long and a consumer fails to process, it takes a long time to be able to process the message again
    - If it is too short and a consumer fails to process in time, it might be processed multiple times
    - Timeout can be dynamically changed via the `ChangeMessageVisibility` API
- Long Polling
    - A period of time that a consumer is willing to wait for if no messages are on the queue
    - Can be between 1 and 20 seconds, 20 seconds preferred
    - Long polling is preferred to short polling
    - Decreases API calls to SQS and improves latency and performance
    - Enabled via `WaitTimeSeconds` API or at the queue level

## SQS Queue Type

- Standard Queue
    - Oldest AWS offering
    - Fully managed
    - Scales from 1 to 10k messages per second
    - **4 to 14-day retention period**
    - No limits of messages in the queue
    -  **120,000 limit** for the number of inflight messages(received from the queue by a consuming component, but have not yet been deleted from the queue)
    - <10ms on produce/consume
    - At-Least-Once Delivery ⇒ Messages are delivered at least once but can have multiples
    - Best-Effort Ordering ⇒ Occasional out of order message delivery
- Delay Queue
    - Delay messages up to 15 minutes
    - Default 0 seconds, can change via `DelaySeconds` parameter
    - Can set default at each queue level
- Dead Letter Queue
    - If a message is returned to the queue too many times because consumers fail to process it in time during the Visibility Timeout, it can be pushed to a separate queue 
    - We can set a threshold on how many times the message can be pushed back via a Redrive policy
    - After the threshold is passed, the message is moved in the Dead Letter Queue (DLQ)
    - It is a fully separate queue with different settings than the original queue
- FIFO Queue
    - Name of the queue must end in `.fifo`
    - Lower throughput ⇒ 3,000/s with batching, 300s without
    - Messages are processed in order ⇒ First In, First Out
    - Messages are sent exactly once
    - **20,000 limit** for the number of inflight messages
    - Queue delay but no per-message delay
    - Content-based deduplication (5 minute interval using Duplication ID)
    - Message groups
        - Extra tag on the message that groups messages for FIFO ordering
        - Only one worker per message group
        - Optimizing Group ID means multiple workers can work in parallel on multiple queues

## SQS - Security

- Encryption:
    - In-flight encryption using HTTPS API
    - At-rest encryption using KMS keys
    - Client-side encryption if the client wants to perform encryption/decryption itself
- Access Controls: IAM policies to regulate access to the SQS API
- SQS Access Policies (similar to S3 bucket policies)    

# AWS Simple Notification Service (SNS)

## SNS Overview

- Pub/Sub model of messaging
- Send one message to many receivers
- The publisher sends message to one SNS topic
- As many subscribers as we want will listen the SNS notification
- All subscribers will receive all the messages from the topic they are subscribed to
- Up to 10M subscribers per topic
- 100K topics limit
- Different type of subscribers
    - SQS
    - HTTP/HTTPS that can retry delivery multiple times
    - Lambda functions
    - Email
    - SMS Messages
    - Mobile notifications
- Integrates with multiple AWS services
    - CloudWatch Alarms
    - ASG Notifications
    - S3 Bucket Events
    - CloudFormation state changes

## SNS Publications

- Topic Publish (via SDK)
    - Create topic
    - Create subscribers
    - Publish to topic
- Direct Publish (for mobile app SDK)
    - Create platform application
    - Create platform endpoint
    - Publish to platform endpoint
    - Works with most notification systems

## SNS Security

- same as SQS 

## SNS – Message Filtering

- JSON policy used to filter messages sent to SNS topic’s subscriptions
- If a subscription doesn’t have a filter policy, it receives every message

## SNS – FIFO Topic

- Similar features as SQS FIFO:
    - Ordering by Message Group ID (all messages in the same group are ordered)
    - Deduplication using a Deduplication ID or Content Based Deduplication
- **Can only have SQS FIFO queues as subscribers**


## SNS/SQS Fan Out

- In case we want to delivery messages to multiple SQS queues
- Publish to SNS Topic ⇒ Each SQS queue subscribes to that topic
- Used for full decoupling and scaling receivers
- Leverages SQS delayed or retrial processing capabilities
- Make sure your SQS queue **access policy** allows for SNS to write

# AWS Kinesis

## Kinesis Overview

- Managed Apache Kafka alternative
- Great for real-time big-data and streaming processing frameworks
- Automatic replication of data to 3 AZs
- Three major products
    - Kinesis Data Stream
        - Low-latency real-time data ingestion
        - Receives real time data streams from click streams, IoT devices, logs/metrics, etc...
        - Billing is per shard provisioned, can have as many shards as you want
        - Data that shares the same partition goes to the same shard (ordering)
    - Kinesis Data Analytics
        - Real-time analytics on streams using SQL
        - Can create new Data Streams from the analyzed queries
        - Serverless, continuous and auto-scaling
        - Pay per consumption rate
    - Kinesis Data Firehose
        - Serverless, fully managed
        - Prepare and load real-time data streams into data stores
        - Load streams in other AWS services (Redshift, ElasticSearch, S3...)
        - Supports multiple data formats
        - Almost real time ⇒ 60 seconds latency for non-full batches or 32MB data at a time
        - Pay only per data that passes through Data Firehose
        - No data storage ⇒ Can't replay data
    - Usual process is 
    Input ⇒ Data Stream ⇒ Data Analytics ⇒ Data Firehose ⇒ One or more AWS store

## Kinesis Data Stream

- Divided in ordered shards/partitions
    - A shard is **1MB/s** or **1000 messages/s** for writes and **2MB/s** per reads
    - Billed per shard provisioned with 200 shards as a soft limit
    - Shards number can increase over time to scale appropriately (reshard/merge)
    - Records are ordered per shard
    - One consumer per shard
- Increase throughput by increasing number of shards
- **Data retention is 1 day by default but can be set to up to 7 days**
- Once data enters stream it can't be deleted/removed
- Can reprocess/replay data as data is retained after processing
- Multiple applications can consume the same stream
- Useful when we have very large amount of data that needs to be ordered in many shards

## Ordering data into Kinesis
- Imagine you have 100 trucks (truck_1, truck_2, … truck_100) on the road sending their GPS positions regularly into AWS.
- You want to consume the data in order for each truck, so that you can track their movement accurately.
- How should you send that data into Kinesis?
- Answer: send using a “Partition Key” value of the “truck_id”
- The same key will always go to the same shard

## Kinesis API

- PutRecords API
    - A record is made of data and message key that gets hashed to determine the Shard ID
    - Same key hash always go to the same partition
    - Data go to one shard at a time
    - Once sent, a message has a sequence number, which is only increasing, for ordering purposes
    - Best practice is to choose highly distributed partition key to avoid overloading one shard
    - Can batch PutRecords to increase throughput and reduce costs
    - The Kinesis API can use CLI, AWS SDK or other producers libraries
- `ProvisionedThroughputExceeded` Exception
    - We are sending too much data and we go over provisioned throughput
    - Can be because of hot shard (overloaded shard) or underprovisioned stream
    - Solved by retries with exponential backoff, shard scaling and use of better partition keys
- Consumer API
    - Can be used by regular consumers (CLI, SDK)
    - Can use the Kinesis Client Library for Java, Node, Python, Ruby and NET that uses DynamoDB to checkpoint offsets and track other workers as well as sharing work among shards

## Kinesis Security

- Uses IAM Policies to control access and authorizations
- HTTPS endpoint in-flight encryption
- KMS for at-rest encryption
- Client-side encryption
- VPC endpoints to access Kinesis in a VPC

## SQS vs SNS vs Kinesis

![Screen Shot 2020-06-18 at 3 02 39 PM](https://github.com/raghebbenhamouda/CloudCertNotes/blob/master/Screenshot%20from%202021-12-29%2015-11-33.png)

# Amazon MQ

## Amazon MQ Overview

- Managed Apache ActiveMQ for messaging applications using open protocols
- Does not scale as much because it needs to be provisioned
- Runs on dedicated machines and can run in high-availability with failover options
- Can be both queue model or pub/sub model
- Generates endpoints for each protocol
- Security group needs to accept Inbound TCP traffic on appropriate ports

## Amazon MQ Use Cases

- Migrating on-premise apps that use messaging applications without moving to SQS/SNS
- Apps using protocols such as MQTT, AMQP, STOMP, Openwire, WSS

# Choosing between SQS, SNS and Kinesis

## SQS

- Consumer poll messages
- Data is deleted after consumption
- As many consumers as we want
- Can delay individual message
- No throughput provisioning
- No ordering guarantees (except FIFO streams)

## SQS

- Consumer subscribe to topics, messages are pushed
- Up to 10M subscribers and 100k topics
- Data is lost if not delivered
- No throughput provisioning
- Integrates with SQS for fan out architecture

## Kinesis

- Consumer pull data
- As many consumers as we want
- Can replay data
- Real-time big-data, ETL and analytics
- Ordering at shard level
- Temporary data retention
- Must provision throughput

# AWS CloudWatch

## CloudWatch Overview

- Monitoring instrument that provides and collects metrics from every service
    - Over/under provisioning
    - Current demand/load
    - Bottlenecks
    - Component performance
    - Idle resources
- Can be set to automatically publish detailed 1-minute metrics and custom metrics with up to 1-second granularity
- Allows to set alarms and automate actions based on predefined thresholds or anomaly detectors

## CloudWatch Metrics

- A time-series variable to monitor in a service
- Belong to a namespace so they can be grouped
- Dimensions are attributes of a metric, up to 10 dimensions per metric
- Some services have specific monitoring capabilities
    - EC2 Detailed Monitoring
        - **1-minute detailed metrics insted of 5 min from EC2 instance**
        - Costs more money than regular 5 minutes
        - Used for faster ASG prompts

- Custom metrics
    - You can define custom metrics for CloudWatch to track
    - Can use dimensions to segment metrics
    - Standard 1 minute detail, can become high resolution up to 1 second at higher costs
    - API call ⇒ `PutMetricData`
    - Exponential backoff in case of errors

## CloudWatch Dashboards

- Quick access for important metrics with auto-refresh
- **Global access**
- Can include **graphs from different regions as well as different time zones and time ranges**
- 3 50-metrics dashboards for free, $3/dashboard/month after

## CloudWatch Logs

- Applications send logs to CloudWatch via SDK
- Certain services directly log to it
    - Elastic Beanstalk ⇒ Collect from app
    - ECS ⇒ Collect from container
    - Lambda ⇒ Collect from function calls
    - VPC ⇒ Specific VPC Flow Logs
    - API Gateway ⇒ Access logs
    - Route 53 ⇒ DNS Queries
    - CloudTrail ⇒ Filtered logs
    - CloudWatch ⇒ Log agents on EC2 instances
- CloudWatch can redirect logs to other services like S3, ElasticSearch, Lambda, Kinesis
- Logs are stored in groups (arbitrary) and streams (instances within application/container/files)
- KMS encryption at group level
- Can define expiration policies for the logs
- Need to have correct IAM permissions to send logs to CloudWatch
- Can use **filter expression on logs** to find specific information or trigger alarms
- Logs Insights can be used to query logs and add queries to Dashboards
- S3 Export
    - Log data can take `up to 12` hours to become available for export
    - The API call is **CreateExportTask**

## CloudWatch Logs for EC2

- By default, no logs from your EC2 machine will go to CloudWatch
- You need to run a **CloudWatch agent** on EC2 to push the log files you want
- Two types of Agents:
    - **CloudWatch Logs Agent** : Old version Can only send to CloudWatch Logs
    - **CloudWatch Unified Agent** : Collect additional system-level metrics such as RAM, processes, etc…

## CloudWatch Alarms

- Used to trigger notifications for metrics
- Can go to multiple services (SNS, Auto Scaling, EC2 Actions)
- Different alarm states
    - OK ⇒ Everything fine
    - INSUFFICIENT_DATA ⇒ Not enough data for the alarm
    - ALARM ⇒ Threshold is passed
- Period of time in seconds to evaluate the metric (only 10/30 sec for custom metrics)

## CloudWatch Events

- Scheduled or pattern-reactive events that trigger Lambda functions or SQS/SNS/Kinesis messages
- Create small JSON documents with event information such as state changes

## Amazon EventBridge

- next evolution of CloudWatch Events
- Default event bus: generated by AWS services (CloudWatch Events)
- Partner event bus: receive events from SaaS service or applications (Zendesk, DataDog, Segment, Auth0…)
- Custom Event buses: for your own applications
- EventBridge can analyze the events in your bus and infer the schema
- The Schema Registry allows you to generate code for your application, that will know in advance how data is structured in the event bus

# Auditing: CloudTrail and AWS Config

## CloudTrail Overview

- Enabled by default
- **By default, CloudTrail event log files are encrypted using Amazon S3 server-side encryption (SSE)**
- Provides a history of events/API calls within the AWS Account made by console, SDK, CLI and services
- Used for governance, compliance and audit purposes
- CloudTrail Logs can be put in CloudWatch Logs
- Track entire lifecycle of AWS resources
- Events are stored for **90 days** in CloudTrail
- To keep events beyond this period, log them to S3 and use Athena

## CloudTrail Events

- **Management Events**
    - Operations that are performed on resources in your AWS account
    - By default, trails are configured to log management events   
- **Data Events **  
    - By default, data events are not logged (because high volume operations) 
    - Allows granular control of data event logging with advanced event selectors
    - You can currently log data events on different resource types such as **Amazon S3 object-level API activity**, **AWS Lambda function execution activity (the Invoke API), DynamoDB Item actions, and many more.**
    - Exp: S3 putObject
- CloudTrail Insights Events
    - Enable CloudTrail Insights to detect unusual activity in your account
    
## AWS Config Overview

- Records configurations and its changes over time
- Can analyze configuration data with Athena by putting them in S3
- Answers question such as SSH access, public access to resources, serve configuration
- Can send SNS notification on changes
- You can view compliance and configuration of resources over time, as well as CloudTrail logs
- Uses AWS managed config rules or custom Lambda-defined rules
- Rules are evaluated or triggered on config change, regular intervals or CloudWatch Alarms
- **Rules can auto-remediate (ex: stop non-complying instances)**
- **Config don't deny permissions so they can't prevent things from happening**
- **Use EventBridge to trigger notifications when AWS resources are noncompliant**
- $2/month per active rule

# AWS Encryption and Security services

## AWS Key Management Service

- Managed service for controlling encryption keys across AWS data stores
- Fully integrated with IAM for authorization
- Full key lifecycle management ⇒ Creation, deletion, rotation...
- Auditing available via CloudTrail
- Can be used through console, CLI and SDK, with payment per 10k API calls
- Keys can be AWS created (free), user created via KMS or user imported ($1/month/key)
- Different type of Customer Master Keys (CMK)
    - Symmetric (AES-256)
        - Single encryption key used for encrypt/decrypt
        - Must use KMS API to use
    - Asymmetric (RSA/ECC key pair)
        - Public and private keys for encryption and decryption
        - For encrypt/decrypt and sign/verify
        - Used for outside-AWS encryption
- CMK can't be retrieved by the user and is always managed by AWS
- **Up to 4KB of data per API call**
- KMS keys are region-bound
- Copying Snapshots across accounts by **attaching a KMS Key Policy to authorize cross-account access**
- To copy KMS-Encrypted data, make a snapshot and transfer the encrypted snapshot specifying a new KMS key
- When attaching KMS Key policies to Snapshots, you need to specify authorization for cross-account access
- Key policies
    - Control access to KMS Keys like S3 bucket policies
    - If no key policy is specified, no one can access it
    - Default key policy gives full access to Root user
    - KMS Keys can be accessed if the Key Policy allows the user and the IAM Policy allows API calls
- Use cases
    - Sharing database passwords, third-party service credentials, SSL certificates' private key
    - Encrypting secrets to safely store them in code/environment variables
- KMS Automatic Key Rotation is for Customer-managed CMK, rotation happened each **1 year**
- KMS Manual Key Rotation,  When you want to rotate key every **90 days, 180 days, etc…**
## AWS Systems Manager (SSM) Parameter Store

- Secure storage for configuration and secrets
- Optional Encryption using KMS
- Serverless, scalable, durable, used via SDK
- Version tracking of configurations and secrets
- Uses folder-like structure for providing configurations for specific users and environments
- Configuration management using path & IAM
- Notifications with CloudWatch Events
- Integrates with CloudFormation
- Two tiers of parameters
    - Standard (free)
        - 10K total parameters of 4KB max size
        - Standard throughput or paid higher throughput (1000/s API transactions)
    - Advanced
        - 100K total parameters of 8KB max size ($0.05 per parameter per month)
        - Paid standard or higher throughput (1000/s API transactions)
        - Can assign TTL to force update or delete sensitive data
        - Can schedule CloudWatch events for various parameters, such as expiration or lack of change in x days

## AWS Secrets Manager

- Newer service, meant for storing secrets
- Capability to **force rotation of secrets every X days**
- Automate generation of secrets on rotation (uses Lambda)
- **Integration with Amazon RDS (MySQL, PostgreSQL, Aurora)**
- **Secrets are encrypted using KMS**

## AWS CloudHSM (Hardware Security Module)

- AWS provisioned dedicated encryption hardware, tamper resistant and FIPS 140-2 Level 3 compliant
- You manage your own keys, can be symmetric or asymmetric
- CloudHSM can be setup to be Multi-AZ
- No free tier, must use CloudHSM Client Software
- Good for use with SSE-C keys

## AWS Firewall Manager

- Manages rules in all the accounts under an AWS Organization
- Define common set of rules to use WAF, Shield and Security Groups across accounts

## AWS **Web Application Firewall (WAF)**

- Layer 7 content filtering
- Support rules to block/allow/count
- Integrates with ALB, API Gateway and CloudFront
- Protects against
    - SQL Injections
    - Cross-Site Scripting XSS
- Block based on Web Access Control Lists (Web ACL)
    - IP address
    - HTTP headers/body
    - URI String
    - Size constraint
    - Geo-matching
    - Rate limiting per client IP (DDoS protection)
- Managed rules for common threats
    - OWASP
    - Bots
    - Common Vulnerabilities and Exposures (CVE)

## **AWS Shield**

- DDoS protection service
- Standard ⇒ Free for everyone
    - UDP reflection
    - SYN floods
    - SSL renegotiation
    - Slow loris attacks
- Advanced ⇒ Additional cost
    - Near real-time visibility
    - Integrates with WAF
    - Access to DDoS response team
    - Protection against attacks on EC2, ELB, CloudFront, Global Accelerator and Route 53
    - Protect against higher usage fees during DDoS attacks
    
## **Amazon GuardDuty**

- **Threat detection service that continuously monitors for malicious activity and unauthorized behavior to protect your AWS accounts and workloads.**
- Uses Machine Learning algorithms, anomaly detection, 3rd party data
- Input data includes: 
    -  CloudTrail Logs: unusual API calls, unauthorized deployments 
    -  VPC Flow Logs: unusual internal traffic, unusual IP address •
    -  DNS Logs: compromised EC2 instances sending encoded data within DNS queries
- **Can protect against CryptoCurrency attacks (has a dedicated “finding” for it)**

## **Amazon Inspector**
- Automated Security Assessments for **EC2 instances** 
- Analyze the running OS against known vulnerabilities
- AWS Inspector Agent must be installed on OS in EC2 instances

## **Amazon Macie**

- machine learning and pattern matching to discover and protect your sensitive data in AWS
- Macie helps identify and alert you to sensitive data, such as personally identifiable information (PII)

# AWS Virtual Private Cloud (VPC)

## Classless Inter-Domain Routing (CIDR-IPv4)

- Used in Security groups
- Define IP ranges
- CIDR has two parts
    - Base IP ⇒ XX.XX.XX.XX
        - An IP contained in the range
    - Subnet Mask ⇒ /24 (most common form) or 255.255.255.0 (less common)
        - How many bits can change in the IP
        - The number of IPs allowed is 2 to the power of (32 - /XX) (ex: /32 ⇒ 2^0 = 1)
        - The main cutoffs are 32, 24, 16, 8 and 0, respectively no IP, last IP number set, last two IP number sets, last three IP number sets and all IP numbers can change
- IPs can be public or private (LAN)
- Private IPs are in the ranges 10.0.0.0/8 for big networks, 172.16.0.0/12 for AWS and 192.168.0.0/16 for home networks

## VPC Overview

- Soft limit of 5 VPC per region
- Maximum CIDR for VPC is ** 5**, each of which has to be between **/28 and /16**
- Being a Virtual **Private** Cloud, only Private IP ranges are allowed
- VPC CIDR should not overlap with your other private networks
- VPCs are divided in public or private subnets that are specific to single AZs
- Can have multiple subnets in the same AZ
- AWS reserves the first 4 IP addresses and the last one in each subnet you create
    - 0 ⇒ Network address
    - 1 ⇒ VPC Router
    - 2 ⇒ Mapping to Amazon-provided DNS
    - 3 ⇒ Future use
    - 255 ⇒ Network broadcast address

## Default AWS VPC

- Every AWS account have a default VPC where instances are launched if no VPC is specified
- Default VPC have internet connectivity and instances have public IP and public/private DNS
- The default VPC comes with 3 subnets with non-overlapping CIDR ranges, route tables, IGW and a default NACL that allows all inbound/outbound traffic

## IGW (Internet Gateway)

- Allows VPC instances to connect to the internet when combined with Route Tables
- Must be created separately from VPC
- One IGW can only connect to one VPC and vice versa
- IGWs are NAT devices for the instances with public IPv4
- Can scale horizontally, is highly available and redundant
- Egress Only Internet Gateway ⇒ Access to internet but not reachable by the internet (IPv6 only)
    - All IPv6 addresses are public addresses
    - Functions as a NAT, but NATs are only for IPv4

## Route Tables

- A set of rules used to determine where network traffic from your subnet or gateway is directed
- Routes can be target different services, such as IGWs, NAT Gateways, VP Gateways and other

## DNS Resolution in VPC

- Two main settings
    - `enableDnsSupport` ⇒ Defaults to True
        - Indicates whether the DNS resolution is supported
        -  queries AWS DNS Server at **169.253.169.253** or the reserved IP address **xx.xx.xx.2**
    - `enableDnsHostname` ⇒ Defaults to False for custom VPC, True in default VPC, assigns **public hostname** to EC2 instance if it has a **public IP** when also `enableDnsSupport` is True
- **Both settings** must be true to use custom DNS domain names in **Private Zone( create internal private records for our DNS no need to own the domain name)** in Route 53

## Network Address Translation (NAT) Instances

- EC2-based instances that allow other instances in private subnets to connect to internet
- Must be launched in public subnets and have **elastic IPs** attached to it
- Must disable EC2 flag for Source/Destination Check
- Traffic must be routed from private subnets to NAT Instances via route tables
- Must define HTTP/HTTPS rules to allow traffic from the VPC
- There are pre-defined Amazon Linux 2 AMIs that come with correct NAT instance configuration
- NAT Instances are not HA or resilient, must use ASG and multi-AZ setups
- Instance type dictates network performance

## NAT Gateways

- AWS Managed NAT with high bandwidth, availability and no administrative needs
- Pay for how much bandwdith you use, as well as by the hour
- **AZ specific** and **requires Elastic IP and IGW**
- Can only be used by instances in other subnets than the one it is created in
- **5Gbps** bandwidth, autoscales up to 45Gbps
- **For high availability, deploy NAT Gateways in multiple AZ**

## Network Access Control Lists (NACLs) and Security Groups

- Security groups are firewalls at the instance level
- NACLs are firewalls at the subnet level
- Default NACLs allow everything inbound and outbound
- One NACL per subnet, new subnets are assigned the Default NACL
- NACL rules have precedence numbers, where lower numbers have higher priority
- In case no rules match, * rule denies requests
- New NACLs will deny everything
- NACLs and SGs filter requ
- ests that fit parameters defined in the inbound rules
- SGs are stateful, so if a request passes inbound/outbound, it will also pass outbound/inbound
- NACLs are stateless, so if a request passes inbound/outbound, outbound/inbound rules will still be evaluated
- **Ephemeral Ports**
    - Clients connect to a defined port, and expect a response on an ephemeral port
    - NACL must allow ephermeral port


## Reachability Analyzer

- A network diagnostics tool that troubleshoots network connectivity between two endpoints in your VPC(s)
- It builds a model of the network configuration, then checks the reachability based on these configurations **(it doesn’t send packets)**


## VPC Peering

- Allows to connect two VPC privately using AWS' network
- Must be set such as if they were on the same network, using appropriate CIDR
- Can work c**ross-region and cross-account** and you can reference security groups of peered VPCs
- **You must update route tables in each VPC’s subnets to ensure EC2 instances can communicate with each other**

## VPC Endpoints

- Used to access and use AWS Services such as S3 or DynamoDB using private networks
- Remove need for IGW, NAT Gateways or other networking tools to access AWS services
- Scale horizontally and are redundant
- Two types of endpoint
    - Interface ⇒ Provisioned private IP address with SG as entry point (for most AWS services)
    - Gateway ⇒ Provisioned target to be used in a route table (for S3 and DynamoDB)
- Points of failure are in DNS settings and route tables

## VPC Flow Logs

- Logs about IP traffic going in VPCs
- Can be automatically moved to S3/CloudWatch
- Three kind of logs
    - VPC Flow Kogs ⇒ Everything within VPC
    - Subnet Flow Logs ⇒ Everything within a specific Subnet
    - Elastic Network Interface Flow Log ⇒ Everything with a specific ENI (also AWS Managed)
- Log syntax

    <version> <account-id> <interface-id> <srcaddr> <dstaddr> <srcport> <dstport> <protocol> <packets> <bytes> <start> <end> <action> <log-status>

    - <srcaddr> <dstaddr> ⇒ IP identification
    - <srcport> <dstport> ⇒ Port identification
    - <action> ⇒ Success/Failure based on NACL/SG
- Logs can be queried with Athena

## Bastion Hosts

- Used to SSH into private instances
- **Use a small instance because this host will only act as a jump server to connect to other instances in your VPC**
- It is the public subnet that is then connected to other private subnets
- **Bastion Host's security must be extra tight and strict (usually Port 22 traffic from needed IPs)**
- Using an NLB, bastion hosts can be in private subnets
- To maintain high availability, consider deploying multiple bastion hosts in different AZs and fronting NLBs before them (need TCP because of SSH) to failover to in case of a BH failure

## AWS Site-to-Site VPN

- Used to connect on-premise data center to AWS network
- Create a Customer Gateway on the on-premise DC and a VP Gateway on the AWS VPC
- Connected by a site-to-site VPN connection
- The Customer Gateway is a software/hardware on the customer side
- It is connected to the VPN via static internet-routable IP address of the gateway or the public IP address of the NAT if using it

## VPN CloudHub

- Allows to create a secure connection among different DCs with multiple VPN connections
- Low cost hub-and-spoke model for connectivity between locations
- VPN connection ⇒ **Goes through the public internet**

## Direct Connect

- Dedicated **private connection ** from an on-prem DC to AWS network via Direct Connect locations
- Needs dedicated connection on DC and a Virtual Private Gateways in your AWS VPC
- You can access both private and public resources using the same connection
- Supports IPv4/6
- **Data in transit is not encrypted ⇒ AWS Direct Connect + VPN for IPsec-encrypted connections**
- **Direct Connect Gateway**
    - **Used to connect one or more VPCs in different regions to our on-prem DC**
    - Even in separate regions, CIDRs can't overlap
    - Direct Connect Gateway does not create VPC peering
- Two connection types ⇒ **Takes longer than a month to establish new connection**
    - Dedicated connections
        - Physical cables installed by AWS Direct Connect Partners (DCP)
        - 1Gbps and 10Gbps
    - Hosted connections
        - Direct connection requests to AWS Direct Connect Partners (DCP)
        - Capacity can be increased/decreased
        - 50Mbps to 500Mbps for most DCP, 1Gbps to 10Gbps for some DCPs

## AWS ClassicLink

- Deprecated way to connect EC2-Classic instances to a VPC in your account

## AWS PrivateLink/VPC Endpoint Services

- Connect services privately from your service VPC to customers VPC
- Works by leveraging Network Load Balancers on the service VPC and ENI on customer VPC
- All traffic goes through the **AWS Network** and not the public internet

## Transit Gateway

- Simplifies network topology by allowing transitive VPC peering between 1000s of VPCs and DCs
- Hub-and-spoke connection model ⇒ All VPCs, DCs, VPNs connect to the Transit Gateway
- Regional resource but can work cross region by peering across regions
- Can be shared across accounts using Resource Access Management (RAM)
- Route Table can specify which VPCs can talk among each other
- Works with VPCs, Direct Connect Gateway and VPN Connections
- Supports IP Multicast (only AWS service to do so)

## Traffic Mirroring
- Allows you to capture and inspect network traffic in your VPC
- Route the traffic to security appliances that you manage
- Capture the traffic
    - **From (Source)** – ENIs
    - **To (Targets)** – an ENI or a Network Load Balancer

## IPv6 in VPC
- **IPv4 cannot be disabled for your VPC and subnets**
- You can enable IPv6 (they’re public IP addresses) to operate in dual-stack mode
- Your EC2 instances will get at least a private internal IPv4 and a public IPv6

## Egress-only Internet Gateway
- **Used for IPv6 only** (similar to a NAT Gateway but for IPv6)
- You can enable IPv6 (they’re public IP addresses) to operate in dual-stack mode
- Your EC2 instances will get at least a private internal IPv4 and a public IPv6
    
# Other Services and Tools in AWS

## CI/CD Overview

- Continuous Integration (CI) ⇒ Code is built and tested every time it is committed to a repository
- Continuous Delivery (CD) ⇒ Code is immediately deployed after it is built and tested
- Quicker turnaround and faster development time

## CI/CD in AWS

- Code ⇒ CodeCommit
- Build and Test ⇒ CodeBuild
- Deploy and Provision ⇒ Elastic Beanstalk or Amplify or CodeDeploy/CloudFormation
- Pipeline orchestrators ⇒ CodePipeline

## CloudFormation

- Declarative framework to outline infrastructures build out of any AWS service
- Makes it easy to reproduce provisioning of resources and services through code
- Automatically tags infrastructures created by CloudFormation templates to ease cost control
- CloudFormation figures out the order and orchestration of the resources
- Deleting a CloudFormation stack will delete every resource created by the template
- You can destroy and create infrastructures quickly and in a controlled/scheduled way
- Plenty of existing CloudFormation templates are available for common infrastructures/stacks
- A CloudFormation template is made of
    - Resources ⇒ AWS resources declared to be instantiated (only mandatory field)
    - Parameters ⇒ Dynamic inputs
    - Mappings ⇒ Static template variables
    - Outputs ⇒ References to the created stack
    - Conditionals ⇒ Conditions that manipulate the creation of the resources
    - Metadata ⇒ Information about the stack
    - Helpers ⇒ References and functions needed for the template
- StackSets
    - Create, update, or delete stacks across** multiple accounts and regions** with a single operation
    - Need to use root account to create StackSet, trusted account to CRUD stack instances
    - When a StackSet is updated, all the associated stacks are updated

## OpsWorks

- Managed Chef and Puppet instances to perform automatic server configurations
- Fall under the category of Configuration as Code
- Can automate multiple tasks, including cron scheduling, user accounts, package updates, etc...
- Use Recipes/Manifests and are open-source tools that work on any cloud provider

## Elastic Container Service (ECS)

- Containter orchestration service that helps run Docker containers on EC2 machines
- Several services are based on ECS
    - ECS Core: Amazon’s own container platform
        - Launch Docker containers on EC2
        - **You must provision & maintain the infrastructure (the EC2 instances)**
        - AWS takes care of starting/stopping containers
        - Has integrations with the Application Load Balancer
    - Fargate: ECS tasks on serverless computing resources
        - Only set the number of tasks to run and AWS manages the infrastructure
    - EKS: ECS with Kubernetes
        - Managed Kubernetes clusters on AWS
        - Supports EC2 for worker nodes and Fargate for serverless containers
    - ECR: Docker Container Registry hosted by AWS
        - Stores all your containers before running
        - Integrates with IAM
        - Images are encrypted in flight (HTTPS) and at rest
- Used for microservices, batch processing or scheduled tasks or migration of legacy application to the cloud
- To setup, you need to install an ECS agent on the EC2 instance or use ECS-ready Linux AMI
- Important configuration setting for ECS (in the `/etc/ecs/ecs.config` file)
    - `ECS_CLUSTER` ⇒ Assign container to ECS cluster
    - `ECS_ENGINE_AUTH_DATA` ⇒ Used to pull Docker images from private registries
    - `ECS_AVAILABLE_LOGGING_DRIVERS` ⇒ Connect with CloudWatch for logging
    - `ECS_ENABLE_TASK_IAM_ROLE` ⇒ Enable IAM Roles
- ECS terminology
    - ECS Cluster ⇒ A set of EC2 instances
    - ECS Service ⇒ A set of tasks
    - ECS Task and Definition ⇒ Containers running to complete a service
    - ECS IAM Task Role ⇒ Each container has a role assigned to interface with AWS resources
- ALB-ECS integration
    - Allows multiple instances of the same application on one EC2 instance via Port Mapping
    - Optimize instance performances and increase resiliency of the application without scaling

## Amazon EC2 Launch Type for ECS

- The containers belong to one or more Auto Scaling groups. And then we want to register them with the Amazon ECS cluster. We run the ECS agent onto these instances, and we don't have to do this manually. If we're using an AMI made for ECS.
- Thanks to this ECS agent, these container instances are going to be registered to the Amazon ECS cluster, and they can be used to start launching some ECS tasks.

## Fargate Launch Type for ECS

- The Fargate service is going to launch a task. 
- to access this Fargate task, **we get an elastic network interfaces(ENI) per task**.

## IAM Roles for ECS Tasks:

- **EC2 Instance Profile**:
    - Used by the ECS agent to makes API calls to ECS service
    - Send container logs to CloudWatch Logs
    - Pull Docker image from ECR
- **ECS Task Role**:
    - Allow each task to have a specific role   

## ECS Data Volumes – EFS File Systems

- Works for both EC2 Tasks and Fargate tasks
- Ability to mount EFS volumes onto tasks
- Fargate + EFS = serverless + data storage without managing servers
- Use case: persistent multi-AZ shared storage for your containers
    
## ECS Services & Tasks, Load Balancing

- ECS Services & Tasks
    - expose the tasks of a specific service using ALB
-Load Balancing for EC2 Launch Type
    - expose a service's task that are runnig on ec2
    - We get a **dynamic port** 
- Load Balancing for Fargate
    - Each task has a unique IP due to the ENI
    
## Step Functions

- Serverless orchestration for Lambda functions and workflows
- Represent flow as JSON **state machine**
- Integrates with many other services such as ECS, EC2, API Gateway, etc...
- 1 year maximum execution time

##  Amazon Simple Workflow Service (SWF)

- Orchestration of work among applications
- **Basically a non-serverless version of Step Functions (runs on EC2)**
- 1 year maximum execution time
- Effectively phased out for Step Functions

## AppSync

- **GraphQL** as a Service
- Store and sync data across mobile/web apps in real time
- Uses GraphQL
- Integrates with DynamoDB and Lambda Functions
- Offline data sync and real-time subscriptions
- Can set granular levels of security

## Elastic MapReduce (EMR)

- Creates Hadoop clusters for analysis and processing of big data
- Automatically creates EC2 instances to distribute workflows on and autoscales
- Supports Hadoop, Spark, HBase, Presto, Flink and other engines

## Glue

- Fully **serverless** managed ETL(extract, transform, and load) service
- **Run queries against an Amazon S3 data lake without moving your data**
- Automates data preparation for analytics
- Performs schema inference on crawled data and can automatically generate code
- Works with Aurora, RDS, S3 and Redshift
- Glue Data Catalog ⇒ Metadata of the source tables
- **The data that is used as sources and targets of your ETL jobs are stored in the data catalog**

## Workspaces

- Fully managed desktop virtualization service for **Windows** and **Linux** that enables you to **access resources from any supported device**.
- On-demand Virtual Desktops
- Secure and encrypted, integrates with Microsoft Active Directory

## Elastic Transcoder

- Serverless service to convert media files stored in S3 in various formats
- Can manipulate bitrate, thumbnail, watermarks, encryption, DRM, captions, etc...
- To run Elastic Transcoder you need to define
    - Jobs ⇒ What the transcoder has to do
    - Pipeline ⇒ Queue of the various jobs
    - Preset Templates ⇒ Used to convert media from one format to another
    - Notifications ⇒ Via SNS, SQS or others

# Cloud Architectures Topics

## Disaster Recovery

- A disaster is any event that has negative impact on business's continuity or finances
- Disaster Recovery is preparing and recovering from disasters
- DR can be fully on-premise, hybrid (switch to cloud when on-prem goes down) or fully cloud
- Disaster Recovery is measured with two key metrics
    - Recovery Point Objective (RPO)
        - How often we run backups
        - The data between the last backup and the disaster is our data loss
    - Recovery Time Objective (RTO)
        - When did we recover from the disaster
        - The time between the disaster and the recovery is our downtime
    - Optimization of RPO and RTO drives solution architectures and cost structures
- Key DR strategies (Highest RTO to lowest RPO)
    - Backup and Restore
        - High RPO and high RTO
        - Recreate infrastructure when we need it
        - Can be very dangerous if backups are too spaced out
        - Can be slow to recovery if restoring many services/databases
        - Cheap(**the only cost we have is the cost of storing these backups**)
    - Pilot Light
        - Small version of the app is always running in the cloud in the background
        - Ideal and feasible only for the critical core systems
        - Faster than backup and restore because the app is already up, so just failover
    - Warm Standby
        - Full system is up and running at minimum size
        - Can be scaled to production load when disaster hits
    - Hot site/Multi site approach (Active/Active)
        - Very expensive but lowest RPO/RTO
        - Full production scale applications running in two sites
    - All AWS Multi Region
        - Appropriate for fully cloud
        - When disaster hits, failover to a non-affected region
- General DR advice
    - Backups
        - Can be done using EBS or RDS backups and snapshots, etc...
        - Can be cost-optimized by storing them on S3, Glacier, leveraging lifecycle policies, CRR
        - On-Prem Backups leverage Snowball or Storage Gateway
    - High Availability
        - Multi-AZ when can be enabled
        - Route 53 automatic DNS migration
        - Recovery from Direct Connect with site-to-site VPN
    - Replication
        - AWS Aurora Global Databases, RDS Replication
        - On-premise DB replication to RDS
        - Storage Gateway
    - Recovery Automation
        - CloudWatch alarms
        - SAM, CloudFormation, Elastic Beanstalk
        - AWS Lambda to deal with automatic taks
    - Chaos
        - Netflix Chaos Monkeys to test fault tolerance in production environments

## DMS – Database Migration Service
    
- Our source database may be on-premise
- We run an EC2 instance that has the **DMS software**, and it will pull the data from the source database and continuously put it in the target database
- The source database remains available during the migration
- Supports:
    - **Homogeneous migrations**: ex Oracle to Oracle
    - **Heterogeneous migrations**: ex Microsoft SQL Server to Aurora
- **AWS Schema Conversion Tool (SCT)**
    - Convert your Database’s Schema from one engine to another
    - You do not need to use SCT if you are migrating the same DB engine
    
## On-Premise Strategies

- You can use Amazon Linux 2 AMIs on on-premise VMs by downloading the AMI's .iso
- VM Import/Export to migrate applications to/from EC2 or move DR strategies to on-prem
- AWS App Discovery Service to plan migrations from on-prem to EC2
- DMS to migrate or replicate databases to/from AWS Services
- AWS Server Migration Service to incrementally replicate on-prem servers to AWS

## Large Data Transfers

- Over the internet or Site-to-Site VPN
    - Immediate setup
    - Slow to transfer
- Direct Connect
    - Long setup time
    - Takes considerably shorter than OTI
- Snowball
    - Can take multiple Snowball devices
    - Up to a week end-to-end
    - Can be combined with Database Migration Service
- Ongoing replication
    - Site-to-Site VPN with DMS or DataSync

## AWS Backup

- Fully managed service 
- Centrally manage and automate backups across AWS services 
- Supported services:
    - Amazon FSx
    - Amazon EFS
    - Amazon DynamoDB
    - Amazon EC2
    - Amazon EBS
    - Amazon RDS (All DBs engines)
    - Amazon Aurora 
    - AWS Storage Gateway (Volume Gateway)
- Supports cross-region/account backups 

    
## Caching Strategies

- Anticipate data update patterns when choosing cache TTL
- Store frequently accessed queries from RDS and app logic in Redis/Memcached
- The deeper in the backend the caching happens, the more cost/latency it is introduced

## Security Strategies

- Take advantage of Connection Termination capabilities of ALBs when filtering IPs
- Can create more granular filters with Web Application Firewalls either at the ALB level or at the CloudFront distribution level if using CloudFront
- NLB traffic is passthrough and does not have security groups, so security has to be put in effect in the instance's Security Group and in the NACLs



- Cloud is great for HPC because of on-demand nature of resource usage
- Data Management and Transfer
    - Direct Connect ⇒ Private secure network for transferring via the internet
    - Snow family ⇒ Physical transfer of PBs of data
    - DataSync ⇒ Move data between on-premise and AWS
- Computing
    - EC2 ⇒ Compute/GPU Optimized, cost optimization via Spot fleets, Cluster placement groups for better network performance
    - **EC2 Enhanced Networking ⇒ High bandwidth, either via Elastic Network Adapter (100Gbps) or Intel 82599VF (10Gbps, legacy)**
    - Elastic Fabric Adapter ⇒ Improved ENA for distributed HPC that works only **on Linux** and leverages Message Passing Interface standards to provide l**ow-latency** network performace
- Storage
    - Instance-attached storage ⇒ EBS (IO1, up to 64,000 IOPS) or Instance storage (millions of IOPS, physical storage)
    - Network storage ⇒ S3 (non-FS blob), EFS (IOPS based on provisioned IOPS or size) or FSx for Lustre (HPC optimized, backed by S3, millions of IOPS), **For cluster of EC2**
- Automation
    - AWS Batch ⇒ Multinode parallel jobs that span multiple EC2 instances
    - Parallel Cluster ⇒ Automatic creation of VPC, subnet, cluster and instance type for HPC
    
## X-Ray

- AWS X-Ray helps developers analyze and debug production, distributed applications, such as those built using a microservices architecture
- With X-Ray, you can understand how your application and its underlying services are performing to identify and troubleshoot the root cause of performance issues and errors
- You can use X-Ray to collect data across AWS Accounts. The X-Ray agent can assume a role to publish data into an account different from the one in which it is running. This enables you to publish data from various components of your application into a central account.

## AWS Server Migration Service (SMS)

- **An agentless service** for** migrating** thousands of **on-premises workloads to AWS**
- This is the enhanced replacement of Amazon EC2 **VM Import service**.
- Each server volume replicated is saved as a n**ew Amazon Machine Image (AMI)**, which can be launched as an EC2 instance in the AWS cloud.
- AWS SMS creates a n**ew EBS snapshot** with every replication.

## AWS Transfer Family
- secure transfer service for moving files into and out of AWS storage services, such as **Amazon S3 and Amazon EFS** using the **FTP protocol**
- You can provision a Transfer Family server with multiple protocols **(SFTP, FTPS, FTP)**
    
##  AWS Certificate Manager & IAM certificate store
- ACM lets you import third-party certificates from the ACM console, as well as programmatically
- If ACM is not available in your region, use AWS CLI to upload your third-party certificate to the **IAM certificate store**
    
##  Server Access Logging feature of Amazon S3

- give detailed logging information for **object-level access**
    
## ApproximateAgeOfOldestMessage Metric(For SQS)
- The ApproximateAgeOfOldestMessage metric is useful when applications have **time-sensitive messages** and you need to ensure that messages are processed within a specific time period. You can use this metric to set Amazon CloudWatch alarms that issue alerts when messages remain in the queue for extended periods of time. You can also use alerts to take action, such as increasing the number of consumers to process messages more quickly.


