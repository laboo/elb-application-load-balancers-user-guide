# Register Targets with Your Target Group<a name="target-group-register-targets"></a>

You register your targets with a target group\. You can register targets by instance ID or by IP address\. For more information, see [Target Groups for Your Application Load Balancers](load-balancer-target-groups.md)\.

If demand on your currently registered targets increases, you can register additional targets in order to handle the demand\. When your target is ready to handle requests, register it with your target group\. The load balancer starts routing requests to the target as soon as the registration process completes and the target passes the initial health checks\.

If demand on your registered targets decreases, or you need to service a target, you can deregister it from your target group\. The load balancer stops routing requests to a target as soon as you deregister it\. When the target is ready to receive requests, you can register it with the target group again\.

When you deregister a target, the load balancer waits until in\-flight requests have completed\. This is known as *connection draining*\. The status of a target is `draining` while connection draining is in progress\.

When you deregister a target that was registered by IP address, you must wait for the deregistration delay to complete before you can register the same IP address again\.

If you are registering targets by instance ID, you can use your load balancer with an Auto Scaling group\. After you attach a target group to an Auto Scaling group and the group scales out, the instances launched by the Auto Scaling group are automatically registered with the target group\. If you detach the target group from the Auto Scaling group, the instances are automatically deregistered from the target group\. For more information, see [Attaching a Load Balancer to Your Auto Scaling Group](http://docs.aws.amazon.com/autoscaling/ec2/userguide/attach-load-balancer-asg.html) in the *Amazon EC2 Auto Scaling User Guide*\.

## Target Security Groups<a name="target-security-groups"></a>

When you register EC2 instances as targets, you must ensure that the security groups for your instances allow the load balancer to communicate with your instances on both the listener port and the health check port\.


**Recommended Rules**  

|  | 
| --- |
| Inbound | 
|  Source  |  Port Range  |  Comment  | 
| *load balancer security group* | *instance listener* | Allow traffic from the load balancer on the instance listener port | 
| *load balancer security group* | *health check* | Allow traffic from the load balancer on the health check port | 

We also recommend that you allow inbound ICMP traffic to support Path MTU Discovery\. For more information, see [Path MTU Discovery](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/network_mtu.html#path_mtu_discovery) in the *Amazon EC2 User Guide for Linux Instances*\.

## Register or Deregister Targets<a name="register-deregister-targets"></a>

When you create a target group, you specify whether you must register targets by instance ID or IP address\.


+ [Register or Deregister Targets by Instance ID](#register-instances)
+ [Register or Deregister Targets by IP Address](#register-ip-addresses)
+ [Register or Deregister Targets Using the AWS CLI](#register-cli)

### Register or Deregister Targets by Instance ID<a name="register-instances"></a>

The instance must be in the virtual private cloud \(VPC\) that you specified for the target group\. The instance must also be in the `running` state when you register it\.

**To register or deregister targets by instance ID**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\.

1. Select your target group\.

1. On the **Targets** tab, choose **Edit**\.

1. To register instances, select them from **Instances**, modify the default instance port as needed, and choose **Add to registered**\.  
![\[The Register and deregister dialog box.\]](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/images/register_instances.png)

1. To deregister instances, select them from **Registered instances** and choose **Remove**\.  
![\[The Register and deregister dialog box.\]](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/images/deregister_instances.png)

1. Choose **Save**\.

### Register or Deregister Targets by IP Address<a name="register-ip-addresses"></a>

The IP addresses that you register must be from the subnets of the VPC for the target group, the RFC 1918 range \(10\.0\.0\.0/8, 172\.16\.0\.0/12, and 192\.168\.0\.0/16\), and the RFC 6598 range \(100\.64\.0\.0/10\)\. You cannot register publicly routable IP addresses\.

**To register or deregister targets by IP address**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\.

1. Select your target group\.

1. On the **Targets** tab, choose **Edit**\.

1. To register IP addresses, choose the **Register targets** icon \(the plus sign\) in the menu bar\. For each IP address, select the network, type the IP address, type the port, and choose **Add to list**\. When you are finished specifying addresses, choose **Register**\.  
![\[The Register targets screen.\]](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/images/register_ip_addresses.png)

1. To deregister IP addresses, choose the **Deregister targets** icon \(the minus sign\) in the menu bar\. If you have many registered IP addresses, you might find it helpful to add a filter or change the sort order\. Select the IP addresses and then choose **Deregister**\.  
![\[The Deregister targets screen.\]](http://docs.aws.amazon.com/elasticloadbalancing/latest/application/images/deregister_ip_addresses.png)

1. To leave this screen, choose the **Back to target group** icon \(the back button\) in the menu bar\.

### Register or Deregister Targets Using the AWS CLI<a name="register-cli"></a>

Use the [register\-targets](http://docs.aws.amazon.com/cli/latest/reference/elbv2/register-targets.html) command to add targets and the [deregister\-targets](http://docs.aws.amazon.com/cli/latest/reference/elbv2/deregister-targets.html) command to remove targets\.