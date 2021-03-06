This solution is suitable for websites/APPs deployed based on clusters.

If your business meets the following conditions, just spend 5 minutes deploying this solution and you can save 30% in costs:

- Your website is running based on a cluster, and the cluster has more than 1 server;
- Your website has long idle time. According to the statistics of Tencent Cloud, for 90% of clusters, the load from 00:00 to 09:00 is less than 30%.

Most of the websites have a peak period of not more than 8 hours. So, for the remaining 16 hours, you can absolutely scale down idle servers to reduce costs.

This document takes a leisure website as an example, for which the peak period is from 20:00 to 24:00.

## Solution Overview

- CVMs with an annual or monthly plan can be used for deploying fixed resources based on the load in off-peak hours;
- Pay-by-usage CVMs can be used for insufficient part in peak hours. With the scheduled task, the server increases one CVM at 20:00 and scales down at 24:00.

Comparison of old and new solutions:
![](https://mc.qcloudimg.com/static/img/46cc4300131254282b894c9b1ed691ac/AS-Best+Practise-Cluster+Solution%281%29.png)

## Revenue
Assume that the original solution needs two 4-core 8GB CVMs working for 24 hours every day. The new solution changes it to one 4-core 4GB CVM working for 24 hours every day and one temporary CVM working for 4 hours every day, which can save you around 41% in expenses.

The small website in Guangzhou in this example thus can save 1440 USD per year:

<table>
        <tbody>
						<tr>
            <th></th>
            <th colspan="3">Original Solution </th>
            <th colspan="3">Auto Scaling Solution </th>
					</tr>
        <tr>
            <th rowspan="4">Resources Required</th>
                  <th>Resources</th>
                  <th>Quantity</th>
									<th>Fee </th>
									<th>Resources</th>
                  <th>Quantity</th>
									<th>Fee</th> 
        </tr>
        <tr>
									<th> 4-core 8GB CVM per month</th>
                  <th>2</th>
									<th>288</th>
									<th> 4-core 8GB CVM per month</th>
                  <th>1</th>
									<th>144</th> 
        </tr>
        <tr>
								<th></th>
                  <th></th>
									<th></th>
									<th> 4-core 4GB temporary CVM for 120 hours</th>
                  <th>1</th>
									<th>24</th> 
        </tr>
				<tr>
								<th>load balance</th>
                  <th>1</th>
									<th>2.1</th>
									<th>load balance</th>
                  <th>1</th>
									<th>2.1</th> 
        </tr>
				 <tr>
            <th>Total Cost</th>
                  <th></th>
                  <th></th>
									<th>290.1</th>
									<th></th>
                  <th></th>
									<th>170.1</th> 
        </tr>
				<tr>
            <th>Save</th>
                  <th colspan="7">120 USD/month</th>
        </tr>
    </tbody>
	</table>

## Specific Procedure

The website for an instance is simply structured - there is only the application server cluster. For complex websites, there will be application server cluster, frontend server cluster, and cache server cluster, etc. Each cluster can carry out similar operations and each cluster corresponds to one scaling group.

![](https://mc.qcloudimg.com/static/img/76238c1b282534b67e80a7e4d04bba17/AS-Best+Practise-Cluster+Solution%282%29.png)

### Step 1. Create a custom image for the cluster CVM

This step is very simple. The custom image can be created based on an existing cluster CVM. If you have any questions, refer to [Create Custom Image >>](https://www.cloud.tencent.com/document/product/213/4942)

> Note:
> You need to deploy the environment in the image in advance, ensuring that the application of the image can be activated with the operating system. In such case, the scaled CVM can work directly without manual intervention.

### Step 2. Create scaling configuration

AS uses the scaling configuration as a template to create a CVM for scale-up. Therefore, we can specify the region, model, and image through scaling configuration in advance.

1. Log in to [Auto Scaling Console](https://console.cloud.tencent.com/autoscaling/config), and click "Scaling Configuration" in the navigation bar.

2. Select a project and a region. Note that you must select the project and region of your Web application.
![](https://mc.qcloudimg.com/static/img/9a39d87fa90f3ae5995073a6077b1057/1.jpg)

3. The following procedure is similar to purchasing CVMs. You can follow the guidelines to complete the creation of scaling configuration. Please note that you should specify the image you have just created in the custom image.
![](https://mc.qcloudimg.com/static/img/02220977468b12ef47c9aeb30a26b06d/2.jpg)


### Step 3. Create a scaling group for the CVM

In the [Auto Scaling Console](https://console.cloud.tencent.com/autoscaling), click "New" and fill in the cluster management information as follows:

- **Name**: Fill in an appropriate name, such as "Application Server Cluster" in here.
- **Minimum group size**: Lower limit of the number of cluster servers. Fill in 0 in this example.
- **Initial number of instances**: Number of CVMs **automatically created** upon the creation of the scaling group. Generally, no CVM will be created automatically when a scaling group is created. It is recommended to enter 0.
- **Maximum group size**: Upper limit of the number of cluster servers. Fill as needed. We enter 5 here, which means the scaling group has a maximum of 5 CMVs.
- **Scaling Configuration**: Select the scaling configuration you have just created.
- **Supported network**: The network environment of the session server. Select "Basic Network" generally.
- **Supported availability zone**: Select an availability zone for the CVM to be scaled. Check the availability zone of the session server.
- **Remove policy**: Select the default value.
- **Cloud load balancer**: Select the cloud load balancer of the cluster.
![](https://mc.qcloudimg.com/static/img/e82f2ce171a0cff61f075b0cd7bd17f0/23.jpg)
![](https://mc.qcloudimg.com/static/img/f3ab676105c79c61ce877b92e4e6ca7c/24.jpg)

Click **OK** to finish the creation.

### Step 4. Add an existing CVM to the scaling group

1. In the [Auto Scaling Console](https://console.cloud.tencent.com/autoscaling), click on the scaling group name to enter the management page, and click "Add CVM" at the bottom of the page.
![](https://mc.qcloudimg.com/static/img/3ff1beba2621d68ef939d7fd0df2de11/5.jpg)

2. In the pop-up dialog box, select an existing server in the cluster to add it to the scaling group. During the off-peak hours, the server that is not fully utilized in the cluster can be returned to save costs.
![](https://mc.qcloudimg.com/static/img/be11ceec587195b7cf39f9183a051769/6.jpg)

3. Set the added server as "Scale-down exemption", so as to protect it from being removed for scale-down by the scaling group. In this way, this CVM is always in service in the cluster and AS will not change it.
![](https://mc.qcloudimg.com/static/img/2c19119e95beb4eb820757939578c656/7.jpg)

### Step 5. Set the scaling policy (important!)

AS supports scheduled scale-up or dynamic scale-up based on the alarm, and can allow you to receive scaling notifications and check the scaling history. Everything is under your control.
![](https://mc.qcloudimg.com/static/img/f348d96c002cdf79ac2033abcd7e6e4c/8.jpg)

-  **First, set a scheduled scale-up task at 20:00**
![](https://mc.qcloudimg.com/static/img/0721f68f33dca49f7175d1f4fd3a80aa/t1.jpg)

> Note:
> It takes about 1 minute to create a Tencent Cloud CVM, and may take more time if the custom image is large. You can set the execution start time 5 minutes earlier.

- **Then, set a scheduled scale-down task at 24:00**
![](https://mc.qcloudimg.com/static/img/a86e797fa59c66d304837b3017d7bc78/t2.jpg)

Now you're done!

The backend cluster of the website is changed to " 1 fixed application server + 1 application server that will be created as scheduled during peak hours".
Other cluster CVMs that are not added to the scaling group are not fully utilized most of the time. You can return them for cost saving.

