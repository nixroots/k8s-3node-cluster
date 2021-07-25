
If you Google how to run WordPress on AWS, you might come across a white paper from Amazon that shows the following:AWS Wordpress Reference Architecture


![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/aws_refarch_full.png>)

For a beginner or someone looking to run a small blog or e-commerce site, this diagram is crazy complicated. I started writing this post because I wanted to understand the reasons behind recommending an architecture like this by breaking it down into the individual components and services.

* What is all that stuff for?
* What do I really need?
* How much is it going to cost me?

This post works through an example that starts with a simple single-server deployment and adds one AWS service or feature at a time to build up to the complex diagram at the top. We will look into the benefit and the cost of each one so that you can make educated decisions on how to deploy your architecture. The estimated costs are for comparative purposes; every deployment will be different. Be sure to analyze your unique situation before deploying services that will cost you money.

In reality, most implementations will not be the simplest nor the most complex, but somewhere in the middle.

There are sites like wordpress.com, SquareSpace, and Wix, that will host your site for you. Amazon also offers LightSail, a managed solution for deploying WordPress (and other types of sites). One of these may be the cheapest and easiest solution, but there are also several reasons an individual or small business would want to run their own site. You may or may not want to use Wordpress, and I’m not going to debate whether on not that is the right idea. This post has information that is applicable to many different types of sites hosted on AWS.

In its simplest form, we could run Wordpress on a single server like the diagram below. All we need is an EC2 instance running Wordpress, a simple VPC with an internet gateway, and DNS configured to point our domain name to this server.

**Basic Website Architecture**

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/01-Basic.png>)

So how does this simple diagram become the complicated one above? The short answer is that all that stuff provides scalability, availability, redundancy, speed, and configurability.

If you are considering deploying or scaling up your site, I hope this post will help you decide the most cost-effective way to do it. What AWS Services can you take advantage of that will give you the best bang for your buck? And what services will leave you feeling taken advantage of with high costs and little return?

Wordpress Architecture
Looking at the Wordpress application itself, it does four main things (architecturally speaking).

1. Hosts static content (CSS, javascript, theme files)
2. Hosts dynamic content (using PHP - see below)
3. Stores database content (blog posts and page content)
4. Stores uploaded files (i.e., pictures) on the file system

In the simple setup, all of these functions are provided by the single EC2 host. As the architecture grows, you will see that we can offload each of these functions to other AWS services, as shown in the diagram below.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/services_offload.png>)

**Services Offloading**

Also, we need to know a little bit about PHP and how it works. Wordpress is written in PHP, which is a server-side scripting language. This means that as users visit the site, the PHP code loads data from the database and dynamically creates the HTML needed for our web browsers to show content. The web server has to compute the HTML code every time for every page visited by every user. For high traffic sites, this can be a lot of computation.

**Assumptions & Cost Estimations**

For this example, we need to make some assumptions. Let's say we are planning on building a personal blog with a healthy amount of traffic - around 10,000 visitors per month. This is a good amount of traffic for a personal blog or small business website, but still much less traffic than what some WordPress sites see. Costs for some of the services we add below are proportional to the traffic volume the site receives, while others are not dependent on traffic volume at all. I will point this out as we add the various services.

EC2, RDS, and ElastiCache are all priced by instances and offer many different pricing options and payment plans. We will look at different instance types and sizes, but the price will always be based on the 1-year standard plan reserved instance. This will help us to compare apples to apples. However, you could potentially save more money with a different payment plan.

Amazon also has different rates for some services based on region. For all the cost estimations in this post, I used the us-east-1 region prices, but this shouldn't affect things that much.

# 1. The Simple Setup

The simple setup is Wordpress running on a single EC2 instance with your domain name pointed to your server's public IP address.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/01-Basic.png>)

You can use Amazon's Route 53 or your own registrar's DNS service. Route 53 costs 50 cents per month, and your registrar (i.e., GoDaddy) is usually free. The EC2 instance cost can vary significantly depending on what instance type and purchasing plan you choose. Setting up a simple, low traffic site shouldn't require anything more significant than a t3.small instance, which costs $8.92/mo on the annual plan.

It's worth noting that we have not addressed any backup scheme. This instance could die anytime, and you could lose everything: the entire database, all themes, and configs, all blog posts, etc. You should employ a backup strategy with this setup, but that is a separate topic for another day.

Estimated Monthly Cost: $9.42

# 2. Add CloudFront

Adding CloudFront is one of the best things you can do for your website. CloudFront is a content delivery network (CDN). There are several CDN providers, but CloudFront is the service offered by Amazon. CDNs copy the content on your server to locations around the world.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddCloudFront.png>)

This setup has some distinct advantages to you:

Your users will experience faster load times. With the edge locations likely closer to them than your primary server, they will see a speed boost.

CloudFront serving your webpages means that it offloads compute utilization from your server, allowing you to possibly use a smaller instance size than you would otherwise need. Static content is cached and served to users directly from CloudFront's edge locations. (Your server still has to handle dynamic content and service requests for static content from the CloudFront servers as they update their cache.) CloudFront can also terminate SSL/TLS, saving more compute capacity on your server.

The speed boost seen by users is also recognized by search engines and will help boost your site's SEO. Pages that load faster will be ranked better and have a better chance of showing up at the top of Google search results.

Added Monthly Cost: $2.75 (rough estimate based on 10,000 page views)

Total Monthly Cost: $12.17

Note that CloudFront costs can significantly increase with increased traffic volume. If you have monetized your site, increased traffic should mean more revenue for you, and this won't be an issue.

# 3. Add RDS

Move your database off of the main EC2 instance to Amazon Aurora. Amazon Aurora is a MySQL compliant, fully managed database from Amazon. In doing this, we do need to create a new private subnet for the Aurora instance. Adding the subnet will not cost us any more, but will provide better security for your data.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddRDS.png>)

Using Aurora will give you the following benefits:

As a fully-managed database, you do not need to be worried about OS patches or software upgrades. When you manage the database on your main EC2 instance, you should always make sure these things are up-to-date to avoid security vulnerabilities. Aurora handles this for you.

Simple backups with more features and flexibility than the simple setup.

Better security with a private subnet. Having your database run on a server that is publicly addressable on the internet is generally considered poor security practice. You can better defend an instance running in a private subnet using access control lists and security groups.

Added Monthly Cost: $19.83 (Aurora Instances only; no backup storage)

Total Monthly Cost: $32.00

# 4. Add Multiple Availability Zones w/ Load Balancer


So far, although we have added a couple of new services, we still have a few spots that could be a single point of failure. If the one EC2 instance or the one Aurora DB instance goes down, the site will be unavailable. To guard against this, you can add redundancy with a second EC2 instance and a read-replica instance for Aurora. A good way to implement this is to use a separate availability zone (AZ). This does come with increased costs. We need to add two additional servers as well as an application load balancer.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddMultipleAZs.png>)

Even with the significant cost jump, this solution has the following benefits:

EC2 server redundancy. If one of the EC2 servers fails, the other seamlessly picks up all the traffic.

Aurora DB failover. As a managed database service, Aurora DB will automatically handle any failures. Unlike the EC2 configuration, where both servers are essentially the same, the database servers operate in a master and read-replica configuration. This means that data can be read from either server, but only written to the master. If the read-replica is the one that fails, the master handles all the traffic (both read and write). If the master fails, Aurora recognizes this and converts the read-replica to function as the master.

Reduced server load. The application load balancer distributes users across the EC2 instances. Likewise, with 2 DB instances, reads are distributed between the two, reducing the load on each.

Availability Zone separation. Some failures that could make your EC2 instance or DB unavailable are due to software bugs, running out of disk space, or other similar issues. Simple redundancy can mitigate these types of failures. But what if there's an issue that impacts the entire data center? Amazon availability zones are designed to be independent and separate. An event (power outage, weather, etc.) affecting their data center for one AZ will not impact any other AZ. When you deploy your solution to multiple AZs, you benefit from this logical and physical separation.

The reference architecture diagram shows the use of only two AZs, but you could use more to make your website even more robust. The template generatorfor this architecture assumes you are setting up three AZs. For costing this example, we will assume only two AZs. We need to add only one additional EC2 instance, one additional Aurora instance, and an application load balancer.

Added Monthly Cost: $45.18

Total Monthly Cost: $77.18

# 5. Add Auto-Scaling


In the previous architecture setup, we went from one to two EC2 instances. Adding auto-scaling allows you to spin up as many EC2 instances as you need to meet the current demand. For this relatively small website, two servers are plenty. The nice thing about auto-scaling is that you don't pay for it unless you use it. You can add auto-scaling to your site as a just in case measure to make sure your users always have access. If you are hosting a blog and a specific post goes viral, auto-scaling can provision servers to handle the spike in traffic, and will only create additional cost for you while you have the spike. Auto-scaling will then shutdown unneeded servers when the spike is over.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddAutoScaling.png>)

For this example, we could set up the auto-scaling group to be a minimum of 2 (the instances we set up in the last step) and a max of 10. If you generally have a low amount of traffic, only the two servers will be on, but your site will still handle large spikes in demand.

Added Monthly Cost: $0.00 (unless there's a spike in traffic)

Total Monthly Cost: $77.18

# 6. Add Private Subnets w/ NAT


Move the EC2 instances to a private subnet. In a previous step, We created private subnets for the Aurora DB servers. There, we discussed the security benefit of putting servers into private subnets. We can do this for the EC2 instances as well. In this configuration, the application load balancer is publicly addressable on the internet, while all our other servers (so far) are not. While this adds security, it comes with a catch: the EC2 instances can no longer reach the internet! While this isn't a problem for your users, you need internet access to update and maintain the servers. This includes security and OS patches or software upgrades. To give these servers internet access, we need to add a NAT gateway or NAT instance. The NAT gateway is easier to configure and requires less maintenance; therefore, I recommend the NAT Gateway over the NAT instance.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddNAT.png>)

NAT gateways have a high cost compared to the other services we have implemented. The reference architecture adds a NAT to both subnets. Each will run about $40 per month if they run continuously. Here are a couple of things to know about NATs on AWS:

A NAT gateway in one subnet can be accessed by instances in other private subnets, even in other AZs

You probably don't need to run NAT gateways all the time. Since these are used for outbound internet access only, and the use case for this is likely OS patches and software updates, you can probably turn these on only when performing those duties.

If you can, I recommend not running these instances all the time or only using one instance. If you only turn on these instances when you need them, they will cost you almost nothing per month. However, since we are looking at the reference architecture, which adds two of them (all the time), we will include their full-time use in the cost estimate, but you can do this cheaper.

Added Monthly Cost: $65.70

Total Monthly Cost: $142.88

# 7. Add Caching w/ Memcached


Memcached is an open-source, high-performance, distributed memory caching system. The AWS ElastiCache service provides a choice between Redis and Memcached for in-memory caching. We don't need to discuss the differences here as the reference architecture specified Memcached, so we will use that. It does not make a big difference in the pricing. A cache is meant to speed things up, so is it worth it? The two main benefits you can expect to get from using the cache are:

Faster response times for your users (and for search engine SEO).
Less burden on your database instances that could allow you to select smaller instance types.
![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddMemcached.png>)

The cache in this architecture sits between the EC2 instances and the database servers. This helps speed up the PHP code executing on the EC2 instances by returning database queries faster. This brings the most benefit for sites that are mostly static (like blogs and small business websites). Stress-test analysis demonstrates that using a cache like Memcached can significantly improve the performance of the server.

Added Monthly Cost: $31.67

Total Monthly Cost: $174.55

# 8. Add EFS


Amazon Elastic File System (EFS) is a shared file system (similar to NFS) that allows each of your EC2 instances access to the same shared disk space. Wordpress stores uploaded files (like pictures in blog posts) to the local filesystem. With multiple EC2 instances, an uploaded file may be uploaded on one instance and need to be accessed by another instance. Shared disk space is essential to simplify the EC2 configuration, especially when using autoscaling. Using EFS, you can make the web tier completely stateless, so running and scaling EC2 servers is as easy as just turning them on and off. There is no need to synchronize files between nodes in the cluster.

https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddEFS.png

The main benefit of using EFS is the stateless Web tier. When EC2 instances startup, they mount the EFS drive and are off to the races. Without EFS, you need to find a solution that synchronizes files between your EC2 instances.

Added Monthly Cost: $15.00

Total Monthly Cost: $189.55

# 9. Add Bastion Host


Bastion hosts provide you secure access to your EC2 instances that are in private subnets. While the private subnets offer better security by blocking some attack vectors from the bad guys, they also prevent you from directly accessing the instances over the internet. The Bastion host option could be included with #6 (adding private subnets) in this post, but I did want to discuss it separately.

AWS recommends bastion hosts to provide better security, but they come with additional costs. Both in the actual AWS bill you receive and the additional administrative burden of more EC2 hosts (the whitepaper only shows one Bastion host, but puts it in an autoscaling group suggesting that you might need to add more to handle the increased load).

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddBastionHost.png>)

Since only system administrators use these hosts to monitor and maintain the site, you would only need to set up auto-scaling for very busy sites. As an alternative, you could use public subnets combined with AWS EC2 Systems Manager, access control lists, security groups, CloudWatch, and CloudTrail, which may provide a cheaper way to achieve a similar security posture.

Added Monthly Cost: $8.83

Total Monthly Cost: $198.38

# 10. Add S3 for Static Content


Amazon Simple Storage Service (S3) is one of the more popular services on AWS. It provides a simple interface for storing files in the cloud in "buckets." You are probably already familiar with this service. In addition to simply storing files, it can be used to host static websites at a very low cost.

![alt text](<https://nodeployfriday.com/images/posts/cost-effective-aws-architectures-for-wordpress-and-other-websites/Unwordpress_ref_arch-AddS3.png>)

You can turn on website hosting for a specific bucket and add that bucket as an origin for CloudFront. This allows you to run a static website without any server at all. In this example, we will offload the static content hosting from the EC2 Wordpress instance by moving the static site files (CSS, JavaScript) to S3. This can be done using a WordPress plain like W3 Total Cache. The main benefit of using S3 for static content is to lighten the load on the EC2 instance. Implementing this last step, the EC2 instance is now only charged with processing the PHP and rendering the HTML files for users.

Added Monthly Cost: $5.00

Total Monthly Cost: $203.38

# Conclusion


There we have it. You can run a WordPress site using the Amazon reference architecture for about $200 per month. Remember, I am not recommending this and all of these services for everyone. I hope that this guide helps you choose the right services for you.

Other topics to explore (maybe for another time) as you implement WordPress on AWS:

Backups - Depending on how you back up the database and file system content, you could add high costs.
WAF - Web Application Firewall can provide additional security and defend against denial of service attacks. I am surprised WAF is not included in the reference architecture but may be something to consider.
Thanks for reading! Please leave feedback and let me know if you enjoyed this post. If you are interested in other posts, I have written about other AWS services, cloud solutions, and architectures, check me out on Medium. https://medium.com/@ezrabowman

References https://d1.awsstatic.com/whitepapers/wordpress-best-practices-on-aws.pdf

Original Article: https://nodeployfriday.com/posts/aws-wordpress-reference-architecture/
