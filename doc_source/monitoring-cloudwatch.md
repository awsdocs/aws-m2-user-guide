# Monitoring AWS Mainframe Modernization with Amazon CloudWatch<a name="monitoring-cloudwatch"></a>

You can monitor AWS Mainframe Modernization using CloudWatch, which collects raw data and processes it into readable, near real\-time metrics\. These statistics are kept for 15 months, so that you can access historical information and gain a better perspective on how your web application or service is performing\. You can also set alarms that watch for certain thresholds, and send notifications or take actions when those thresholds are met\. For more information, see the [Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/)\.

The following tables list the metrics and dimensions for AWS Mainframe Modernization\.

## Metrics<a name="monitoring-cloudwatch-m2-metrics"></a>


| Metric | Description | 
| --- | --- | 
|  CPUUtilization  |  The CPU utilization of instances in the environment\. Units: Percent Valid statistics: Average, Minimum, Maximum  | 
|  MemoryUtilization  |  The memory utilization of instances in the environment\. Units: Percent Valid statistics: Average, Minimum, Maximum  | 
|  InboundNetworkThroughput  |  Inbound network throughput of instances in the environment\. Units: Bytes per second Valid statistics: Average, Minimum, Maximum  | 
|  OutboundNetworkThroughput  |  Outbound network throughput of the instances in the environment\. Units: Bytes per second Valid statistics: Average, Minimum, Maximum  | 

## Dimensions<a name="monitoring-cloudwatch-m2-dimensions"></a>


| Dimension | Description | 
| --- | --- | 
|  EnvironmentId  |  This dimension filters the metric to the identified environment by ID\.  | 