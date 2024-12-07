How many pods fit on an AWS EKS node?

Managing Kubernetes workloads on AWS EKS (Elastic Kubernetes Service) is much like managing a city, you need to know how many “tenants” (Pods) you can fit into your “buildings” (EC2 instances). This might sound straightforward, but a bit more is happening behind the scenes. Each type of instance has its characteristics, and understanding the limits is key to optimizing your deployments and avoiding resource headaches.

Why Is there a pod limit per node in AWS EKS?
Imagine you want to deploy several applications as Pods across several instances in AWS EKS. You might think, “Why not cram as many as possible onto each node?” Well, there’s a catch. Every EC2 instance in AWS has a limit on networking resources, which ultimately determines how many Pods it can support.

Each EC2 instance has a certain number of Elastic Network Interfaces (ENIs), and each ENI can hold a certain number of IPv4 addresses. But not all these IP addresses are available for Pods, AWS reserves some for essential services like the AWS CNI (Container Network Interface) and kube-proxy, which helps maintain connectivity and communication across your cluster.

Think of each ENI like an apartment building, and the IPv4 addresses as individual apartments. Not every apartment is available to your “tenants” (Pods), because AWS keeps some for maintenance. So, when calculating the maximum number of Pods for a specific instance type, you need to take this into account.

For example, a t3.medium instance has a maximum capacity of 17 Pods. A slightly bigger t3.large can handle up to 35 Pods. The difference depends on the number of ENIs and how many apartments (IPv4 addresses) each ENI can hold.

Formula to calculate Max pods per EC2 instance
To determine the maximum number of Pods that an instance type can support, you can use the following formula:

Max Pods = (Number of ENIs × IPv4 addresses per ENI) – Reserved IPs

Let’s apply this to a t2.medium instance:

Number of ENIs: 3
IPv4 addresses per ENI: 6

Using these values, we get:

Max Pods = (3 × 6) – 1

Max Pods = 18 – 1

Max Pods = 17

So, a t2.medium instance in EKS can support up to 17 Pods. It’s important to understand that this number isn’t arbitrary, it reflects the way AWS manages networking to keep your cluster running smoothly.

Why does this matter?
Knowing the limits of your EC2 instances can be crucial when planning your Kubernetes workloads. If you exceed the maximum number of Pods, some of your applications might fail to deploy, leading to errors and downtime. On the other hand, choosing an instance that’s too large might waste resources, costing you more than necessary.

Suppose you’re running a city, and you need to decide how many tenants each building can support comfortably. You don’t want buildings overcrowded with tenants, nor do you want them half-empty. Similarly, you need to find the sweet spot in AWS EKS, enough Pods to maximize efficiency, but not so many that your node runs out of resources.

The apartment analogy
Consider an m5.large instance. Let’s say it has 4 ENIs, and each ENI can support 10 IP addresses. But, AWS reserves a few apartments (IPv4 addresses) in each building (ENI) for maintenance staff (essential services). Using our formula, we can estimate how many Pods (tenants) we can fit.

Number of ENIs: 4
IPv4 addresses per ENI: 10

Max Pods = (4 × 10) – 1

Max Pods = 40 – 1

Max Pods = 39

So, an m5.large can support 39 Pods. This limit helps ensure that the building (instance) doesn’t get overwhelmed and that the essential services can function without issues.

Automating the calculation
Manually calculating these limits can be tedious, especially if you’re managing multiple instance types or scaling dynamically. Thankfully, AWS provides tools and scripts to help automate these calculations. You can use the kubectl describe node command to get insights into your node’s capacity or refer to AWS documentation for Pod limits by instance type. Automating this step saves time and helps you avoid deployment issues.

Best practices for scaling
When planning the architecture of your EKS cluster, consider these best practices:

Match instance type to workload needs: If your application requires many Pods, opt for an instance type with more ENIs and IPv4 capacity.
Consider cost efficiency: Sometimes, using fewer large instances can be more cost-effective than using many smaller ones, depending on your workload.
Leverage autoscaling: AWS allows you to set up autoscaling for both your Pods and your nodes. This can help ensure that you have the right amount of capacity during peak and off-peak times without manual intervention.

Key takeaways
Understanding the Pod limits per EC2 instance in AWS EKS is more than just a calculation, it’s about ensuring your Kubernetes workloads run smoothly and efficiently. By thinking of ENIs as buildings and IP addresses as apartments, you can simplify the complexity of AWS networking and better plan your deployments.

Like any good city planner, you want to make sure there’s enough room for everyone, but not so much that you’re wasting space. AWS gives you the tools, you just need to know how to use them.
