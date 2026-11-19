# amazon-aws-new-modules

Creates AWS resources with `amazon.aws` modules that are **not present in amazon.aws 7.3.0**.
Use it to verify that an agent/runner has a newer collection installed: with 7.3.0 the play
fails with `couldn't resolve module/action 'amazon.aws.ec2_vpc_nacl'` (or similar).

| Module | First in amazon.aws | Resource |
|---|---|---|
| `rds_instance_param_group` | 8.0.0 | RDS DB parameter group |
| `rds_cluster_param_group` | 8.0.0 | RDS cluster parameter group |
| `ec2_placement_group` | 9.0.0 | Spread placement group |
| `ec2_launch_template` | 9.0.0 | Launch template |
| `ec2_vpc_nacl` | 9.0.0 | Network ACL |
| `ec2_vpc_egress_igw` | 9.0.0 | Egress-only internet gateway |

Module lists were diffed from the published 7.3.0 / 8.0.0 / 9.0.0 / 10.0.0 tarballs.
The 9.0.0 modules existed before in `community.aws`, but not under the `amazon.aws` namespace.
There is intentionally no `requirements.yaml`: the play runs against whatever amazon.aws the
runner already has, so it fails on 7.3.0 and succeeds on >= 9.0.0.
All resources are free (no instances, NAT/transit gateways or dedicated hosts).

## Contract

- **Hosts**: `localhost` only; no inventory groups required.
- **Credentials**: taken from the environment (agent IAM role, `AWS_*` env vars, or profile).
- **Python**: `boto3`/`botocore` versions supported by amazon.aws 9.x.

| Input | Default | Description |
|---|---|---|
| `name_prefix` | `torque-aws9` | Prefix for all resource names (letters, digits, hyphens) |
| `aws_region` | `eu-west-1` | AWS region |
| `vpc_cidr` | `10.90.0.0/16` | VPC CIDR |
| `instance_type` | `t3.micro` | Launch template instance type |
| `pg_family` | `postgres16` | RDS instance parameter group family |
| `aurora_family` | `aurora-postgresql16` | RDS cluster parameter group family |

**Outputs**: `vpc_id`, `nacl_id`, `egress_igw_id`, `placement_group_name`, `launch_template_id`,
`db_param_group_name`, `db_cluster_param_group_name`.

**Teardown**: `teardown.yaml` takes `name_prefix` and `aws_region` and removes everything by name.

## Run locally

```bash
ansible-playbook playbook.yaml -e name_prefix=my-test -e aws_region=eu-west-1
ansible-playbook teardown.yaml -e name_prefix=my-test -e aws_region=eu-west-1
```
