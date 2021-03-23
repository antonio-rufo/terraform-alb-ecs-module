# AWS ALB Terraform Module #

This terraform module creates an Application Load Balancer and security group rules in order allow traffic to it.

## Requirements

| Name      | Version |
| --------- | ------- |
| terraform | >= 0.12 |

## Providers

| Name | Version |
| ---- | ------- |
| aws  | n/a     |

## Inputs

| Name                              | Description                                                         | Type           | Default | Required |
| ----------------------------------| ------------------------------------------------------------------- | -------------- | ------- | :------: |
| name\_prefix                      | Name prefix for resources on AWS                                    | `list`         | n/a     |   yes   |
| vpc\_id                           | ID of the VPC                                                       | `any`          | n/a     |   yes   |
| block\_s3\_bucket\_public\_access | (Optional) If true, public access to the S3 bucket will be blocked. | `bool`         | `false` |   no    |
| internal                          | (Optional) If true, public access to the S3 bucket will be blocked. | `bool`         | `false` |   no    |
| security\_groups                  | (Optional) A list of security group IDs to assign to the LB.        | `list(string)` | `[]`    |   no    |
| drop\_invalid\_header\_fields     | (Optional) Indicates whether HTTP headers with header fields that are not valid are removed by the load balancer (true) or routed to targets (false). The default is false. Elastic Load Balancing requires that message header names contain only alphanumeric characters and hyphens.                                   | `bool` | `false`     |   no    |
| private\_subnets                     | A list of private subnet IDs to attach to the LB if it is INTERNAL.    | `list(string)` | `[]`    |   yes    |
| public\_subnets                      | A list of public subnet IDs to attach to the LB if it is NOT internal. | `list(string)` | `[]`    |   yes    |
| idle\_timeout                        | (Optional) The time in seconds that the connection is allowed to be idle. Default: 60. | `number`         | `60` |   no    |
| enable\_deletion\_protection         | (Optional) If true, deletion of the load balancer will be disabled via the AWS API. This will prevent Terraform from deleting the load balancer. Defaults to false.  | `bool`         | `false` |   no    |
| enable\_cross\_zone\_load\_balancing | (Optional) If true, cross-zone load balancing of the load balancer will be enabled. Defaults to false. | `bool`         | `false` |   no    |
| enable\_http2                        | (Optional) Indicates whether HTTP/2 is enabled in the load balancer. Defaults to true.| `bool`         | `true` |   no    |
| ip\_address\_type                    | (Optional) The type of IP addresses used by the subnets for your load balancer. The possible values are ipv4 and dualstack. Defaults to ipv4 | `string`          | ipv4     |   no   |
| http\_ports                          | Map containing objects to define listeners behaviour based on type field. If type field is `forward`, include listener_port and the target_group_port. For `redirect` type, include listener port, host, path, port, protocol, query and status_code. For `fixed-response`, include listener_port, content_type, message_body and status_code | `map(any)`         | <pre>{<br>    default_http = {<br>        type              = "forward"<br>        listener_port     = 80<br>        target_group_port = 80<br>    }<br>}</pre> |   no    |
| https\_ports                         | Map containing objects to define listeners behaviour based on type field. If type field is `forward`, include listener_port and the target_group_port. For `redirect` type, include listener port, host, path, port, protocol, query and status_code. For `fixed-response`, include listener_port, content_type, message_body and status_code. | `map(any)`         | `TO FOLLOW` |   no    |
| http\_ingress\_cidr\_blocks          | List of CIDR blocks to allowed to access the Load Balancer through HTTP.        | `list(string)` | `["0.0.0.0/0"]`    |   no    |
| http\_ingress\_prefix\_list\_ids     | List of prefix list IDs blocks to allowed to access the Load Balancer through HTTP.        | `list(string)` | `[]`    |   no    |
| https\_ingress\_cidr\_blocks         | List of CIDR blocks to allowed to access the Load Balancer through HTTPS.        | `list(string)` | `["0.0.0.0/0"]`    |   no    |
| https\_ingress\_prefix\_list\_ids    | List of prefix list IDs blocks to allowed to access the Load Balancer through HTTPS.        | `list(string)` | `[]`    |   no    |
| deregistration\_delay                | (Optional) The amount time for Elastic Load Balancing to wait before changing the state of a deregistering target from draining to unused. The range is 0-3600 seconds. The default value is 300 seconds.        | `number` | `300`    |   no    |
| slow\_start                          | (Optional) The amount time for targets to warm up before the load balancer sends them a full share of requests. The range is 30-900 seconds or 0 to disable. The default value is 0 seconds.        | `number` | `0`    |   no    |
| load\_balancing\_algorithm\_type     | (Optional) Determines how the load balancer selects targets when routing requests. The value is round_robin or least_outstanding_requests. The default is round_robin.        | `string` | `round_robin`    |   no    |
| stickiness                           | (Optional) A Stickiness block. Provide three fields. type, the type of sticky sessions. The only current possible value is lb_cookie. cookie_duration, the time period, in seconds, during which requests from a client should be routed to the same target. After this time period expires, the load balancer-generated cookie is considered stale. The range is 1 second to 1 week (604800 seconds). The default value is 1 day (86400 seconds). enabled, boolean to enable / disable stickiness. Default is true.        | <pre>object({<br>    type            = string<br>    cookie_duration = string<br>    enabled         = bool<br>})</pre> | <pre>{<br>    type            = "lb_cookie"<br>    cookie_duration = 86400<br>    enabled         = true<br>}</pre>    |   no    |
| target\_group\_health\_check\_enabled  | (Optional) Indicates whether health checks are enabled. Defaults to true.        | `bool` | `true`    |   no    |
| target\_group\_health\_check\_interval | (Optional) The approximate amount of time, in seconds, between health checks of an individual target. Minimum value 5 seconds, Maximum value 300 seconds. Default 30 seconds.      | `number` | `30`    |   no    |
| target\_group\_health\_check\_path     | The destination for the health check request.        | `string` | `/`    |   no    |
| target\_group\_health\_check\_timeout  | (Optional) The amount of time, in seconds, during which no response means a failed health check. The range is 2 to 120 seconds, and the default is 5 seconds.        | `number` | `5`    |   no    |
| target\_group\_health\_check\_healthy\_threshold   | (Optional) The number of consecutive health checks successes required before considering an unhealthy target healthy. Defaults to 3.        | `number` | `3`    |   no    |
| target\_group\_health\_check\_unhealthy\_threshold | (Optional) The number of consecutive health check failures required before considering the target unhealthy. Defaults to 3.        | `number` | `3`    |   no    |
| target\_group\_health\_check\_matcher              | The HTTP codes to use when checking for a successful response from a target. You can specify multiple values (for example, \"200,202\") or a range of values (for example, \"200-299\"). Default is 200.        | `string` | `200`    |   no    |
| ssl\_policy                                          | (Optional) The name of the SSL Policy for the listener. . Required if var.https_ports is set.        | `string` | `null`    |   no    |
| default\_certificate\_arn                            | (Optional) The ARN of the default SSL server certificate. Required if var.https_ports is set.        | `string` | `null`    |   no    |
| additional\_certificates\_arn\_for\_https\_listeners | (Optional) List of SSL server certificate ARNs for HTTPS listener. Use it if you need to set additional certificates besides default_certificate_arn        | `list(any)` | `[]`    |   no    |

## Outputs

| Name                                              | Description                                                                                |
| ------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| aws\_lb\_lb\_id                                   | The ARN of the load balancer (matches arn).                                                |
| aws\_lb\_lb\_arn                                  | The ARN of the load balancer (matches id).                                                 |
| aws\_lb\_lb\_arn\_suffix                          | The ARN suffix for use with CloudWatch Metrics.                                            |
| aws\_lb\_lb\_dns\_name                            | The DNS name of the load balancer.                                                         |
| aws\_lb\_lb\_zone\_id                             | The canonical hosted zone ID of the load balancer (to be used in a Route 53 Alias record). |
| aws\_security\_group\_lb\_access\_sg\_id          | The ID of the security group                                                               |
| aws\_security\_group\_lb\_access\_sg\_arn         | The ARN of the security group                                                              |
| aws\_security\_group\_lb\_access\_sg\_vpc\_id     | The VPC ID.                                                                                |
| aws\_security\_group\_lb\_access\_sg\_owner\_id   | The owner ID.                                                                              |
| aws\_security\_group\_lb\_access\_sg\_name        | The name of the security group                                                             |
| aws\_security\_group\_lb\_access\_sg\_description | The description of the security group                                                      |
| aws\_security\_group\_lb\_access\_sg\_ingress     | The ingress rules.                                                                         |
| aws\_security\_group\_lb\_access\_sg\_egress      | The egress rules.                                                                          |
| lb\_http\_tgs\_ids                                | List of HTTP Target Groups IDs                                                             |
| lb\_http\_tgs\_arns                               | List of HTTP Target Groups ARNs                                                            |
| lb\_http\_tgs\_names                              | List of HTTP Target Groups Names                                                           |
| lb\_http\_tgs\_ports                              | List of HTTP Target Groups ports                                                           |
| lb\_http\_tgs\_map\_arn\_port                     |                                                                                            |
| lb\_https\_tgs\_ids                               | List of HTTPS Target Groups IDs                                                            |
| lb\_https\_tgs\_arns                              | List of HTTPS Target Groups ARNs                                                           |
| lb\_https\_tgs\_names                             | List of HTTPS Target Groups Names                                                          |
| lb\_https\_tgs\_ports                             | List of HTTPS Target Groups ports                                                          |
| lb\_https\_tgs\_map\_arn\_port                    |                                                                                            |
| lb\_http\_listeners\_ids                          | List of HTTP Listeners IDs                                                                 |
| lb\_http\_listeners\_arns                         | List of HTTP Listeners ARNs                                                                |
| lb\_https\_listeners\_ids                         | List of HTTPS Listeners IDs                                                                |
| lb\_https\_listeners\_arns                        |List of HTTPS Listeners ARNs                                                                |
