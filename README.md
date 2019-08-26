<!-- This file was automatically generated by the `build-harness`. Make all changes to `README.yaml` and run `make readme` to rebuild this file. -->

# terraform-aws-ecs-traefik-service [![Build Status](https://travis-ci.org/aleks-fofanov/terraform-aws-ecs-traefik-service.svg?branch=master)](https://travis-ci.org/aleks-fofanov/terraform-aws-ecs-traefik-service) [![Latest Release](https://img.shields.io/github/release/aleks-fofanov/terraform-aws-ecs-traefik-service.svg)](https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service/releases/latest)


Terraform module to provision [Traefik](https://traefik.io/) service in ECS


---


It's 100% Open Source and licensed under the [APACHE2](LICENSE).









## Introduction

Traefik service in ECS is supposed to act as an edge router and route traffic to other containers in your ECS cluster
based on their docker lables.

For more information on which docker labels to set on your container, see
[Traefik documentation](https://docs.traefik.io/configuration/backends/docker/#on-containers).

SSL termination is supposed to be done on AWS ALB. Traefik tasks are launched with `awsvpc` network mode and needs
Internet access to connect to ECS API in order to discover containers in your ECS cluster.

This module is backed by best of breed terraform modules maintained by [Cloudposse](https://github.com/cloudposse).

## Usage


**IMPORTANT:** The `master` branch is used in `source` just as an example. In your code, do not pin to `master` because there may be breaking changes between releases.
Instead pin to the release tag (e.g. `?ref=tags/x.y.z`) of one of our [latest releases](https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service/releases).


This example creates a Traefik service in ECS launched in `us-west-2` region using `FARGATE`.

```hcl
module "traefik" {
  source    = "git::https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service.git?ref=master"
  name      = "traefik"
  namespace = "cp"
  stage     = "prod"

  ecs_cluster_name      = "XXXXXXXXXXX"
  ecs_cluster_arn       = "XXXXXXXXXXX"
  ecs_cluster_region    = "us-west-2"
  alb_security_group_id = "XXXXXXXXXXX"
  alb_target_group_arn  = "XXXXXXXXXXX"
  vpc_id                = "XXXXXXXXXXX"
  subnet_ids            = ["XXXXXXXXXXX", "XXXXXXXXXXX", ..]

  launch_type      = "FARGATE"
  assign_public_ip = "true"
}
```




## Examples

### Example With [Traefik Dashboard](https://docs.traefik.io/configuration/api/) Enabled

This example launches a Traefik setvice in ECS using `FARGATE` with enabled dashboard and API endpoints.
Basic auth is enabled by default for both. You can use `openssl` to generate password for basic auth:
```bash
openssl passwd -apr1
```

```hcl
module "traefik" {
  source    = "git::https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service.git?ref=master"
  name      = "traefik"
  namespace = "cp"
  stage     = "prod"

  ecs_cluster_name      = "XXXXXXXXXXX"
  ecs_cluster_arn       = "XXXXXXXXXXX"
  ecs_cluster_region    = "us-west-2"
  alb_security_group_id = "XXXXXXXXXXX"
  alb_target_group_arn  = "XXXXXXXXXXX"
  vpc_id                = "XXXXXXXXXXX"
  subnet_ids            = ["XXXXXXXXXXX", "XXXXXXXXXXX", ..]

  launch_type      = "FARGATE"
  assign_public_ip = "true"

  dashboard_enabled             = "true"
  dashboard_host                = "traefik.example.com"
  dashboard_basic_auth_user     = "admin"
  dashboard_basic_auth_password = "$$$apr1$$$Rj21EpGU$$$KCwTHCbAIVhw0BiSdU4Me0"
}
```

### Complete Example

This example:

* Launches Traefik service in ECS using FARGATE
* Enables Traefik API and dashboard
* Enables Traefik service autoscaling based on CPU usage

```hcl
module "traefik" {
  source    = "git::https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service.git?ref=master"
  name      = "traefik"
  namespace = "cp"
  stage     = "prod"

  ecs_cluster_name      = "XXXXXXXXXXX"
  ecs_cluster_arn       = "XXXXXXXXXXX"
  ecs_cluster_region    = "us-west-2"
  alb_security_group_id = "XXXXXXXXXXX"
  alb_target_group_arn  = "XXXXXXXXXXX"
  vpc_id                = "XXXXXXXXXXX"
  subnet_ids            = ["XXXXXXXXXXX", "XXXXXXXXXXX", ..]

  launch_type      = "FARGATE"
  assign_public_ip = "true"

  dashboard_enabled             = "true"
  dashboard_host                = "traefik.example.com"
  dashboard_basic_auth_user     = "admin"
  dashboard_basic_auth_password = "$$$apr1$$$Rj21EpGU$$$KCwTHCbAIVhw0BiSdU4Me0"

  autoscaling_enabled               = "true"
  autoscaling_dimension             = "cpu"
  autoscaling_min_capacity          = "1"
  autoscaling_max_capacity          = "3"
  autoscaling_scale_up_cooldown     = "60"
  autoscaling_scale_down_cooldown   = "60"

  ecs_alarms_enabled                        = "true"
  ecs_alarms_cpu_utilization_high_threshold = "20"
  ecs_alarms_cpu_utilization_low_threshold  = "10"
}
```



## Makefile Targets
```
Available targets:

  help                                Help screen
  help/all                            Display help for all targets
  help/short                          This help short screen
  lint                                Lint terraform code

```
## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| alb_security_group_id | ALB security group id. Traefik container will accept traefik from port 80 | string | - | yes |
| alb_target_group_arn | ALB security group id. Traefik container will accept traefik from port 80 | string | - | yes |
| api_port | Port at which Traefik will expose the API and Dashboard | string | `8080` | no |
| assign_public_ip | Assign a public IP address to the ENI (Fargate launch type only). Valid values are true or false. Default false. | string | `false` | no |
| attributes | Additional attributes, e.g. `1` | list | `<list>` | no |
| autoscaling_dimension | Dimension to autoscale on (valid options: cpu, memory) | string | `memory` | no |
| autoscaling_enabled | A boolean to enable/disable Autoscaling policy for ECS Service | string | `false` | no |
| autoscaling_max_capacity | Maximum number of running instances of a Service | string | `2` | no |
| autoscaling_min_capacity | Minimum number of running instances of a Service | string | `1` | no |
| autoscaling_scale_down_adjustment | Scaling adjustment to make during scale down event | string | `-1` | no |
| autoscaling_scale_down_cooldown | Period (in seconds) to wait between scale down events | string | `300` | no |
| autoscaling_scale_up_adjustment | Scaling adjustment to make during scale up event | string | `1` | no |
| autoscaling_scale_up_cooldown | Period (in seconds) to wait between scale up events | string | `60` | no |
| container_name | The name of the container in task definition to associate with the load balancer | string | `traefik` | no |
| dashboard_basic_auth_enabled | Defines whther basic auth is enabled for Traefik dashboard or not | string | `true` | no |
| dashboard_basic_auth_password | Basic auth password for Traefik dashboard. If left empty, a random one will be generated. | string | `` | no |
| dashboard_basic_auth_user | Basic auth username for Traefik dashboard | string | `admin` | no |
| dashboard_enabled | Defines whether traefik dashboard is enabled | string | `false` | no |
| dashboard_host | Traefik dashboard host at which API should be exposed | string | `dashboard.example.com` | no |
| delimiter | Delimiter to be used between `namespace`, `name`, `stage` and `attributes` | string | `-` | no |
| deployment_controller_type | Type of deployment controller. Valid values: `CODE_DEPLOY`, `ECS`. | string | `ECS` | no |
| deployment_maximum_percent | The upper limit of the number of tasks (as a percentage of `desired_count`) that can be running in a service during a deployment | string | `200` | no |
| deployment_minimum_healthy_percent | The lower limit (as a percentage of `desired_count`) of the number of tasks that must remain running and healthy in a service during a deployment | string | `100` | no |
| desired_count | The number of instances of the task definition to place and keep running | string | `1` | no |
| ecs_alarms_cpu_utilization_high_alarm_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on CPU Utilization High Alarm action | list | `<list>` | no |
| ecs_alarms_cpu_utilization_high_evaluation_periods | Number of periods to evaluate for the alarm | string | `1` | no |
| ecs_alarms_cpu_utilization_high_ok_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on CPU Utilization High OK action | list | `<list>` | no |
| ecs_alarms_cpu_utilization_high_period | Duration in seconds to evaluate for the alarm | string | `300` | no |
| ecs_alarms_cpu_utilization_high_threshold | The maximum percentage of CPU utilization average | string | `80` | no |
| ecs_alarms_cpu_utilization_low_alarm_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on CPU Utilization Low Alarm action | list | `<list>` | no |
| ecs_alarms_cpu_utilization_low_evaluation_periods | Number of periods to evaluate for the alarm | string | `1` | no |
| ecs_alarms_cpu_utilization_low_ok_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on CPU Utilization Low OK action | list | `<list>` | no |
| ecs_alarms_cpu_utilization_low_period | Duration in seconds to evaluate for the alarm | string | `300` | no |
| ecs_alarms_cpu_utilization_low_threshold | The minimum percentage of CPU utilization average | string | `20` | no |
| ecs_alarms_enabled | A boolean to enable/disable CloudWatch Alarms for ECS Service metrics | string | `false` | no |
| ecs_alarms_memory_utilization_high_alarm_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on Memory Utilization High Alarm action | list | `<list>` | no |
| ecs_alarms_memory_utilization_high_evaluation_periods | Number of periods to evaluate for the alarm | string | `1` | no |
| ecs_alarms_memory_utilization_high_ok_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on Memory Utilization High OK action | list | `<list>` | no |
| ecs_alarms_memory_utilization_high_period | Duration in seconds to evaluate for the alarm | string | `300` | no |
| ecs_alarms_memory_utilization_high_threshold | The maximum percentage of Memory utilization average | string | `80` | no |
| ecs_alarms_memory_utilization_low_alarm_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on Memory Utilization Low Alarm action | list | `<list>` | no |
| ecs_alarms_memory_utilization_low_evaluation_periods | Number of periods to evaluate for the alarm | string | `1` | no |
| ecs_alarms_memory_utilization_low_ok_actions | A list of ARNs (i.e. SNS Topic ARN) to notify on Memory Utilization Low OK action | list | `<list>` | no |
| ecs_alarms_memory_utilization_low_period | Duration in seconds to evaluate for the alarm | string | `300` | no |
| ecs_alarms_memory_utilization_low_threshold | The minimum percentage of Memory utilization average | string | `20` | no |
| ecs_cluster_arn | ECS cluster ARN | string | - | yes |
| ecs_cluster_name | ECS cluster name | string | - | yes |
| ecs_cluster_region | ECS cluster region | string | `us-east-1` | no |
| health_check_grace_period_seconds | Seconds to ignore failing load balancer health checks on newly instantiated tasks to prevent premature shutdown, up to 7200. Only valid for services configured to use load balancers | string | `10` | no |
| http_port | Port at which Traefik will accept traffic from ALB | string | `80` | no |
| ignore_changes_task_definition | Whether to ignore changes in container definition and task definition in the ECS service | string | `true` | no |
| launch_type | The launch type on which to run your service. Valid values are `EC2` and `FARGATE` | string | `FARGATE` | no |
| log_format | Traefk log format. See https://docs.traefik.io/configuration/logs/ | string | `common` | no |
| log_level | Traefk log level. See https://docs.traefik.io/configuration/logs/ | string | `INFO` | no |
| logs_region | AWS region for storing Cloudwatch logs from traefik container. Defaults to the same as ECS Cluster region. | string | `` | no |
| logs_retention | Defines retention period in days for Traefik logs in Cloudwatch | string | `30` | no |
| mount_points | Container mount points. This is a list of maps, where each map should contain a `containerPath` and `sourceVolume` | list | `<list>` | no |
| name | Solution name, e.g. 'app' or 'jenkins' | string | `traefik` | no |
| namespace | Namespace (e.g. `cp` or `cloudposse`) | string | `cp` | no |
| security_group_ids | Additional security group IDs to allow in Service `network_configuration` | list | `<list>` | no |
| stage | Stage (e.g. `prod`, `dev`, `staging`) | string | `prod` | no |
| subnet_ids | Subnet IDs | list | - | yes |
| tags | Additional tags (e.g. `map(`BusinessUnit`,`XYZ`) | map | `<map>` | no |
| task_cpu | The vCPU setting to control cpu limits of traefik container. (If FARGATE launch type is used below, this must be a supported vCPU size from the table here: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-cpu-memory-error.html) | string | `256` | no |
| task_image | Traefik image | string | `library/traefik:1.7` | no |
| task_memory | The amount of RAM to allow traefik container to use in MB. (If FARGATE launch type is used below, this must be a supported Memory size from the table here: https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-cpu-memory-error.html) | string | `512` | no |
| task_memory_reservation | The amount of RAM (Soft Limit) to allow traefik container to use in MB. This value must be less than container_memory if set | string | `128` | no |
| volumes | Task volume definitions as list of maps | list | `<list>` | no |
| vpc_id | Id of VPC in which Traefik service should be deployed | string | - | yes |

## Outputs

| Name | Description |
|------|-------------|
| ecs_exec_role_policy_id | The ECS service role policy ID, in the form of role_name:role_policy_name |
| ecs_exec_role_policy_name | ECS service role name |
| scale_down_policy_arn | Autoscaling scale up policy ARN |
| scale_up_policy_arn | Autoscaling scale up policy ARN |
| service_name | ECS Service name |
| service_role_arn | ECS Service role ARN |
| service_security_group_id | Security Group ID of the ECS task |
| task_definition_family | ECS task definition family |
| task_definition_revision | ECS task definition revision |
| task_exec_role_arn | ECS Task exec role ARN |
| task_exec_role_name | ECS Task role name |
| task_role_arn | ECS Task role ARN |
| task_role_id | ECS Task role id |
| task_role_name | ECS Task role name |




## Related Projects

Check out these related projects.

- [terraform-aws-ecs-cluster-traefik](https://github.com/aleks-fofanov/terraform-aws-ecs-cluster-traefik) - Terraform module to provision ECS cluster with as an edge router



## Help

**Got a question?**

File a GitHub [issue](https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service/issues).


## Contributing

### Bug Reports & Feature Requests

Please use the [issue tracker](https://github.com/aleks-fofanov/terraform-aws-ecs-traefik-service/issues) to report any bugs or file feature requests.

### Developing

In general, PRs are welcome. We follow the typical "fork-and-pull" Git workflow.

 1. **Fork** the repo on GitHub
 2. **Clone** the project to your own machine
 3. **Commit** changes to your own branch
 4. **Push** your work back up to your fork
 5. Submit a **Pull Request** so that we can review your changes

**NOTE:** Be sure to merge the latest changes from "upstream" before making a pull request!


## Copyright

Copyright © 2017-2019 Aleksandr Fofanov



## License 

[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0) 

See [LICENSE](LICENSE) for full details.

    Licensed to the Apache Software Foundation (ASF) under one
    or more contributor license agreements.  See the NOTICE file
    distributed with this work for additional information
    regarding copyright ownership.  The ASF licenses this file
    to you under the Apache License, Version 2.0 (the
    "License"); you may not use this file except in compliance
    with the License.  You may obtain a copy of the License at

      https://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing,
    software distributed under the License is distributed on an
    "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
    KIND, either express or implied.  See the License for the
    specific language governing permissions and limitations
    under the License.








## Trademarks

All other trademarks referenced herein are the property of their respective owners.


### Contributors

|  [![Aleksandr Fofanov][aleks-fofanov_avatar]][aleks-fofanov_homepage]<br/>[Aleksandr Fofanov][aleks-fofanov_homepage] |
|---|

  [aleks-fofanov_homepage]: https://github.com/aleks-fofanov
  [aleks-fofanov_avatar]: https://github.com/aleks-fofanov.png?size=150



