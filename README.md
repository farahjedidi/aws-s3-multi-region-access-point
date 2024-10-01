# Demo : S3 Multi-Region Access Points 

In this demo, I will demonstrate how to create an **S3 Multi-Region Access Point (MRAP)**, which allows traffic to be distributed to the closest S3 bucket based on the user's location. The demo uses two S3 buckets, one in **Sydney (ap-southeast-2)** and another in **Canada Central (ca-central-1)**, with requests routed to the nearest bucket. I also set up replication between these buckets.

## Prerequisites

- **AWS Management Account**: I used the same management account as in my previous demos.

## Steps

### 1. Create and Configure Two S3 Buckets

- I created two S3 buckets using the **management account**:
  - **Bucket 1**: `multi-region-demo-sydney` in **ap-southeast-2 (Sydney)**.
  - **Bucket 2**: `multi-region-demo-canada` in **ca-central-1 (Canada Central)**.
  - I enabled **Bucket Versioning** for both buckets.

### 2. Create the Multi-Region Access Point (MRAP)

- I created a new **Multi-Region Access Point (MRAP)**, named `reallyreallycriticalcatdatafarah`, and added both buckets (`multi-region-demo-sydney-farah` and `multi-region-demo-canada-farah`) to it.

- ![Screenshot](https://imgur.com/csAnhsR.png)

- I waited for a few minutes for the MRAP to be ready for use.

- ![Screenshot](https://imgur.com/8q2WPKL.png)

### 3. Configure Replication Between Buckets

- I navigated to the **Replication and Failover** section under the MRAP settings and created replication rules (will be applied on both buckets within the MRAP) to ensure that all objects uploaded to either bucket are automatically replicated across both :

- ![Screenshot](https://imgur.com/O1eaEzS.png)
  
- ![Screenshot](https://imgur.com/yMv643W.png)
  
- ![Screenshot](https://imgur.com/7KSOe0m.png)
  
- ![Screenshot](https://imgur.com/2ypTebM.png)

- ![Screenshot](https://imgur.com/39VUGci.png)
  

### 4. Upload Files to Test the MRAP

To test the Multi-Region Access Point (MRAP), I used **AWS CloudShell**, which allowed me to simulate connections from different AWS regions to observe how S3 requests are routed.

1. **Access the MRAP ARN**:
- First, I navigated to the [Multi-Region Access Points dashboard], selected the MRAP I created, and copied its ARN. This ARN will be used to target the MRAP in the next steps.

- ![Screenshot](https://imgur.com/WkRVd88.png)

2. **Test from the Tokyo region**:  
- I connected to the **Tokyo (ap-northeast-1)** region, which is geographically closer to the Sydney bucket.Then, using CloudShell,  I uploaded a test file to the MRAP.
   
- ![Screenshot](https://imgur.com/BuYYFw9.png)

- As expected, the file was routed to the **Sydney (ap-southeast-2)** bucket, and it was also replicated to the Canada bucket after a short delay.

4. **Test from the Ohio region**:  
- Next, I switched to the **Ohio (us-east-2)** region and using CloudShell repeated the process. This time, the file was routed to the **Canada (ca-central-1)** bucket as it was the closest, with replication occurring to the Sydney bucket after a brief period.

- ![Screenshot](https://imgur.com/benA7Js.png)

- ![Screenshot](https://imgur.com/FPntJEJ.png)

- ![Screenshot](https://imgur.com/4gPFnxa.png)

6. **Test from the Mumbai region**:  
- To see how MRAP behaves from a region located between the two buckets, I connected from **Mumbai (ap-south-1)**. In this case, the file was again routed to the Sydney bucket, but the results may vary depending on network factors.

7. **Handling Replication Delays**:  
- To test replication delays, I uploaded a file in one region and immediately attempted to retrieve it from the other region before replication completed.

- ![Screenshot](https://imgur.com/9gY9O8O.png)

   Initially, I received a `404` error, indicating that the file had not yet been replicated.
   After waiting a few moments, I was able to successfully retrieve the file, demonstrating the potential lag in replication.

- ![Screenshot](https://imgur.com/2EITCaz.png)

> Note: If an application requires immediate object availability across all regions, I would consider enabling **Replication Time Control (RTC)** when creating the replication rules to guarantee faster replication times. Otherwise, applications should be designed to handle replication delays and possible `404` errors.

### 5. Cleanup

After completing the test, I deleted the Multi-Region Access Point (MRAP) and emptied both S3 buckets to prevent any additional costs. Finally, I deleted the buckets.

## Conclusion

This demo successfully demonstrated how to create and configure an **S3 Multi-Region Access Point (MRAP)** to route traffic to the nearest S3 bucket based on the user's location. By leveraging MRAP, I was able to simplify multi-region data management across two S3 buckets in **Sydney** and **Canada Central**, ensuring high availability and redundancy through replication. 

Testing the MRAP from different AWS regions using CloudShell confirmed that requests were routed to the nearest bucket, and the replication configuration ensured that data was consistently copied between buckets, though with occasional delays in synchronization. This highlights the importance of considering replication delays in real-world scenarios, especially for applications requiring immediate cross-region access to data. For such cases, enabling **Replication Time Control (RTC)** can mitigate potential issues by speeding up replication.

The demo also underscored the flexibility MRAP provides for simplifying access to distributed resources across multiple regions, making it an effective solution for applications requiring global scale, resiliency, and optimized latency.

This exercise showcased the powerful capabilities of MRAP in managing multi-region data access and replication, offering valuable insights into its potential for enterprise-scale applications.

