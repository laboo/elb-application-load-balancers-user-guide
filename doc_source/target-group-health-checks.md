# Health Checks for Your Target Groups<a name="target-group-health-checks"></a>

Your Application Load Balancer periodically sends requests to its registered targets to test their status\. These tests are called *health checks*\.

Each load balancer node routes requests only to the healthy targets in the enabled Availability Zones for the load balancer\. Each load balancer node checks the health of each target, using the health check settings for the target group with which the target is registered\. After your target is registered, it must pass one health check to be considered healthy\. After each health check is completed, the load balancer node closes the connection that was established for the health check\.

If no Availability Zone contains a healthy target, the load balancer nodes route requests to all targets\.

Note that health checks do not support WebSockets\.

## Health Check Settings<a name="health-check-settings"></a>

You configure health checks for the targets in a target group using the following settings\. The load balancer sends a health check request to each registered target every **HealthCheckIntervalSeconds** seconds, using the specified port, protocol, and ping path\. It waits for the target to respond within the response timeout period\. If the health checks exceed the threshold for consecutive failed responses, the load balancer takes the target out of service\. When the health checks exceed the threshold for consecutive successful responses, the load balancer puts the target back in service\.


| Setting | Description | 
| --- | --- | 
| **HealthCheckProtocol** |  The protocol the load balancer uses when performing health checks on targets\. The possible protocols are HTTP and HTTPS\. The default is the HTTP protocol\.  | 
| **HealthCheckPort** |  The port the load balancer uses when performing health checks on targets\. The default is to use the port on which each target receives traffic from the load balancer\.  | 
| **HealthCheckPath** |  The ping path that is the destination on the targets for health checks\. The default is /\.  | 
| **HealthCheckTimeoutSeconds** |  The amount of time, in seconds, during which no response from a target means a failed health check\. The range is 2 to 60 seconds\. The default is 5 seconds\.  | 
| **HealthCheckIntervalSeconds** |  The approximate amount of time, in seconds, between health checks of an individual target\. The range is 5 to 300 seconds\. The default is 30 seconds\.   | 
| **HealthyThresholdCount** |  The number of consecutive successful health checks required before considering an unhealthy target healthy\. The range is 2 to 10\. The default is 5\.  | 
| **UnhealthyThresholdCount** |  The number of consecutive failed health checks required before considering a target unhealthy\. The range is 2 to 10\. The default is 2\.  | 
| **Matcher** |  The HTTP codes to use when checking for a successful response from a target\. You can specify values or ranges of values between 200 and 499\. The default value is 200\.  | 

## Target Health Status<a name="target-health-states"></a>

Before the load balancer sends a health check request to a target, you must register it with a target group, specify its target group in a listener rule, and ensure that the Availability Zone of the target is enabled for the load balancer\. Before a target can receive requests from the load balancer, it must pass the initial health checks\. After a target passes the initial health checks, its status is `Healthy`\.

The following table describes the possible values for the health status of a registered target\.


| Value | Description | 
| --- | --- | 
| `initial` |  The load balancer is in the process of registering the target or performing the initial health checks on the target\.  | 
| `healthy` |  The target is healthy\.  | 
| `unhealthy` |  The target did not respond to a health check or failed the health check\.  | 
| `unused` |  The target is not registered with a target group, the target group is not used in a listener rule for the load balancer, or the target is in an Availability Zone that is not enabled for the load balancer\.  | 
| `draining` |  The target is deregistering and connection draining is in process\.  | 

## Health Check Reason Codes<a name="target-health-reason-codes"></a>

If the status of a target is any value other than `Healthy`, the API returns a reason code and a description of the issue, and the console displays the same description in a tooltip\. Note that reason codes that begin with `Elb` originate on the load balancer side and reason codes that begin with `Target` originate on the target side\.


| Reason code | Description | 
| --- | --- | 
| `Elb.InitialHealthChecking` |  Initial health checks in progress  | 
| `Elb.InternalError` |  Health checks failed due to an internal error  | 
| `Elb.RegistrationInProgress` |  Target registration is in progress  | 
| `Target.DeregistrationInProgress` |  Target deregistration is in progress  | 
| `Target.FailedHealthChecks` |  Health checks failed  | 
| `Target.InvalidState` |  Target is in the stopped state Target is in the terminated state Target is in the terminated or stopped state Target is in an invalid state  | 
| `Target.IpUnusable` |  The IP address cannot be used as a target, as it is in use by a load balancer\.  | 
| `Target.NotInUse` |  Target group is not configured to receive traffic from the load balancer Target is in an Availability Zone that is not enabled for the load balancer  | 
| `Target.NotRegistered` |  Target is not registered to the target group  | 
| `Target.ResponseCodeMismatch` |  Health checks failed with these codes: \[*code*\]  | 
| `Target.Timeout` |  Request timed out  | 

## Check the Health of Your Targets<a name="check-target-health"></a>

You can check the health status of the targets registered with your target groups\.

**To check the health of your targets using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\.

1. Select the target group\.

1. On the **Targets** tab, the **Status** column indicates the status of each target\.

1. If the status is any value other than `Healthy`, view the tooltip for more information\. 

**To check the health of your targets using the AWS CLI**  
Use the [describe\-target\-health](http://docs.aws.amazon.com/cli/latest/reference/elbv2/describe-target-health.html) command\. The output of this command contains the target health state, and it includes a reason code if the status is any value other than `Healthy`\.

## Modify the Health Check Settings of a Target Group<a name="modify-health-check-settings"></a>

You can modify the health check settings for your target group at any time\.

**To modify the health check settings of a target group using the console**

1. Open the Amazon EC2 console at [https://console\.aws\.amazon\.com/ec2/](https://console.aws.amazon.com/ec2/)\.

1. On the navigation pane, under **LOAD BALANCING**, choose **Target Groups**\.

1. Select the target group\.

1. On the **Health checks** tab, choose **Edit**\.

1. On the **Edit target group** page, modify the settings as needed, and then choose **Save**\.

**To modify the health check settings of a target group using the AWS CLI**  
Use the [modify\-target\-group](http://docs.aws.amazon.com/cli/latest/reference/elbv2/modify-target-group.html) command\.