# terraform-alicloud-cloud-sso

本 Module 用于创建阿里云云产品 Cloud SSO 的所有资源，包括目录 Directory，用户 User，用户组 Group，以及访问配置 AccessConfiguration。

## 用法

```terraform
data "alicloud_cloud_sso_directories" "default" {}
module "cloud_sso" {
   source           = "terraform-alicloud-modules/cloud-sso/alicloud"
   create_directory = false
   directory_id     = data.alicloud_cloud_sso_directories.default.ids.0
   create_group     = true
   group_name       = "sso-xiaozhutestdev-xztest"
   description      = "sso group description "

   create_user = true
   users = [
      {
         user_name = "user-01"
      },
      {
         user_name    = "user-02"
         description  = "cloud user"
         display_name = "foo"
         email        = "abc@163.com"
         first_name   = "zhu"
         last_name    = "xiao"
         status       = "Enabled"
      },
      {
         user_name = "user-03"
         status    = "Disabled"
      }
   ]

   create_access_configuration = true
   access_configurations = [
      {
         access_configuration_name = "ac-01"
         description               = "ac 01"
         permission_policies = [
            {
               permission_policy_document = <<EOF
            {
              "Statement":[
                {
                  "Action":"ecs:Get*",
                  "Effect":"Allow",
                  "Resource":[
                    "*"
                  ]
                }
              ],
              "Version": "1"
            }
          EOF
               permission_policy_type     = "Inline"
               permission_policy_name     = "ecs-policy"
            }
         ]
         relay_state                      = "https://cloudsso.console.aliyun.com/test1"
         session_duration                 = 1800
         force_remove_permission_policies = true
      },
      {
         access_configuration_name = "ac-02"
         description               = "ac 02"
         permission_policies = [
            {
               permission_policy_document = <<EOF
            {
              "Statement":[
                {
                  "Action":"vpc:Get*",
                  "Effect":"Allow",
                  "Resource":[
                    "*"
                  ]
                }
              ],
              "Version": "1"
            }
          EOF
               permission_policy_type     = "Inline"
               permission_policy_name     = "vpc-policy"
            }
         ]
         relay_state                      = "https://cloudsso.console.aliyun.com/test2"
         session_duration                 = 1800
         force_remove_permission_policies = true
      }
   ]
}
```

## 目录

目录是所有Cloud SSO其他资源的前提条件。本Module支持通过如下参数指定一个存量的目录ID：

```terraform
create_directory = false
directory_id     = "rd-xxxxxxx"
```

通过，也可以通过如下的参数新建一个目录：

```terraform
create_directory          = true
directory_name            = "foo"
mfa_authentication_status = "Enabled"

saml_identity_provider_configuration = [
  {
    sso_status                = "Enabled"
    encoded_metadata_document =  "PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4KPG1kOkVudGl0eURlc2NyaXB0b3IgZW50aXR5SUQ9Imh0dHBzOi8vY3kudGVzdC5jb20vc2FtbC9hc3NlcnRpb24vdGVzdCIgeG1sbnM6bWQ9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDptZXRhZGF0YSI+PG1kOklEUFNTT0Rlc2NyaXB0b3IgV2FudEF1dGhuUmVxdWVzdHNTaWduZWQ9ImZhbHNlIiBwcm90b2NvbFN1cHBvcnRFbnVtZXJhdGlvbj0idXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOnByb3RvY29sIj48bWQ6S2V5RGVzY3JpcHRvciB1c2U9InNpZ25pbmciPjxkczpLZXlJbmZvIHhtbG5zOmRzPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwLzA5L3htbGRzaWcjIj48ZHM6WDUwOURhdGE+PGRzOlg1MDlDZXJ0aWZpY2F0ZT5NSUlES3pDQ0FoT2dBd0lCQWdJQkFUQU5CZ2txaGtpRzl3MEJBUXNGQURCWU1Sa3dGd1lEVlFRREV4QmhiR2xpWVdKaFkyeHZkV1F1ClkyOXRNUnd3R2dZRFZRUUxFeE5KWkdWdWRHbDBlU0JOWVc1aFoyVnRaVzUwTVJBd0RnWURWUVFLRXdkQmJHbGlZV0poTVFzd0NRWUQKVlFRR0V3SkRUakFnRncweU1UQTFNRFl3T0RNMk1EQmFHQTh5TVRJeE1EUXhNakE0TXpZd01Gb3dXREVaTUJjR0ExVUVBeE1RWVd4cApZbUZpWVdOc2IzVmtMbU52YlRFY01Cb0dBMVVFQ3hNVFNXUmxiblJwZEhrZ1RXRnVZV2RsYldWdWRERVFNQTRHQTFVRUNoTUhRV3hwClltRmlZVEVMTUFrR0ExVUVCaE1DUTA0d2dnRWlNQTBHQ1NxR1NJYjNEUUVCQVFVQUE0SUJEd0F3Z2dFS0FvSUJBUURabHVOUlc2N1gKQlhVdFJMaG9BcTJ5d3VGRDd5WVpsOEliTUNJZ0NBdnJzekxhRmVQM0VvRWg4RjJ0M2FBWlJyZHJGMDdSbmNjTTZQRC9Ud2d4dzNoVgpDSmh2QnloNjh4UXVENXRja2pLei90Wnl3MVB5NXJPb05Va001N09ZL1AvZWc2MHg1SlJxaXdmZXZFcnJPK0xKWnVxMTNqTmpzRWFNCnRsRnhuTUkrY3FvUW9QbnZSUnVWNFVQdXJ0TEpJemNwQ2QvRnJxQjVvZTg5TjgySFpCcVE4eTdscVNsdHNuOTF0S05xRm44U3lnN3QKWitJQzRweWVsVDJVYit4RkFXRStjTWd4RVloR0R5UzVWSGNaWkc5OC90d0RWUHlRSjV5NWhJM0U2MWE3OW1mTkxHa0IvTGVXODF3MQpTZFM4SldQL3hsNWt6dDR0aXNkd0kyejJmMm8zQWdNQkFBRXdEUVlKS29aSWh2Y05BUUVMQlFBRGdnRUJBTDFseUZIcnRET1R6bUZsCkpScmFsNlVLekRSSlU4djVOcGNPeTdlbVZBR2RMamNqRll5ZzR3cG1FZ3pOUWxscFZZd0VBRi9LbG5nOGxNbC8zekI2cWJEVFNSWnEKbFhpdDU4eHJsM0RiWnJ1OUdVOVMrdW9XYngrdVkyWStVNkpOOWhJSFRkcHc5SmFkTWY3akJzL2pFWWFuRDNObUU0SjltSThMeDNXYwpHL0lFLzNOOTY1NXM1UHJPTkc0MTgySXJFTG1TdzRrRkxDS0gzdi9IUnN0Ym9PSkttTWRuTHc4VGNaYWFNZHp2N1VEK1NoRlRPbFdmCmJrRmhISVZVSGNVSGN3S1kxSitjSmF0VVdPakNmWGZkNVl4Uy9aM3dhbURtbmRxTXJaT3kxUEh2dkhxNHFGQ3dsQTY1V2pzTDF6dkYKcE51MjQ2WFIxSHNRcVo4UTlvVk1kNU09PC9kczpYNTA5Q2VydGlmaWNhdGU+PC9kczpYNTA5RGF0YT48L2RzOktleUluZm8+PC9tZDpLZXlEZXNjcmlwdG9yPjxtZDpTaW5nbGVMb2dvdXRTZXJ2aWNlIEJpbmRpbmc9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDpiaW5kaW5nczpIVFRQLVBPU1QiIExvY2F0aW9uPSJodHRwczovL2N5LnRlc3QuY29tL3NhbWwvbG9nb3V0L3Rlc3QiLz48bWQ6U2luZ2xlTG9nb3V0U2VydmljZSBCaW5kaW5nPSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6YmluZGluZ3M6SFRUUC1SZWRpcmVjdCIgTG9jYXRpb249Imh0dHBzOi8vY3kudGVzdC5jb20vc2FtbC9sb2dvdXQvdGVzdCIvPjxtZDpOYW1lSURGb3JtYXQ+dXJuOm9hc2lzOm5hbWVzOnRjOlNBTUw6Mi4wOm5hbWVpZC1mb3JtYXQ6cGVyc2lzdGVudDwvbWQ6TmFtZUlERm9ybWF0PjxtZDpTaW5nbGVTaWduT25TZXJ2aWNlIEJpbmRpbmc9InVybjpvYXNpczpuYW1lczp0YzpTQU1MOjIuMDpiaW5kaW5nczpIVFRQLVBPU1QiIExvY2F0aW9uPSJodHRwczovL2N5LnRlc3QuY29tL3NhbWwvYXNzZXJ0aW9uL3Rlc3QiLz48bWQ6U2luZ2xlU2lnbk9uU2VydmljZSBCaW5kaW5nPSJ1cm46b2FzaXM6bmFtZXM6dGM6U0FNTDoyLjA6YmluZGluZ3M6SFRUUC1SZWRpcmVjdCIgTG9jYXRpb249Imh0dHBzOi8vY3kudGVzdC5jb20vc2FtbC9hc3NlcnRpb24vdGVzdCIvPjwvbWQ6SURQU1NPRGVzY3JpcHRvcj48L21kOkVudGl0eURlc2NyaXB0b3I+"
  }
]

scim_synchronization_status = "Enabled"
```

## 用户组

本Module支持通过如下的参数指定一个存量的用户组：

```terraform
create_group = false
group_id     = "g-xxxxxxx"
```

同时，可以通过如下的参数新建一个用户组：

```terraform
create_group = true
group_name   = "ALIYUN-appnamedev"
description  = "A new cloud sso group"
```

## 用户

可以通过如下的参数指定一组存量的Cloud SSO用户，用于后续加入到用户组中：

```terraform
create_user = false
users = [
   {
      user_id = "u-xxxxxx01"
   },
   {
      user_id = "u-xxxxxx01"
   }
]
```

同时，可以通过指定如下的参数来新建一组用户：

```terraform
create_user = true
users = [
   {
      user_name = "user-01"
   },
   {
      user_name    = "user-02"
      description  = "cloud user"
      display_name = "foo"
      email        = "abc@163.com"
      first_name   = "zhu"
      last_name    = "xiao"
      status       = "Enabled"
   }
]
```

如果，想要将指定的用户或者新建的用户加入到用户组中，可以指定如下的参数：

```terraform
add_user_to_group = true
```

## 访问配置

指定如下的参数，实现一组访问配置的新建：

```terraform
create_access_configuration = true
access_configurations = [
   {
      access_configuration_name = "ac-01"
      description               = "ac 01"
      permission_policies = [
         {
            permission_policy_document = <<EOF
            {
              "Statement":[
                {
                  "Action":"ecs:Get*",
                  "Effect":"Allow",
                  "Resource":[
                    "*"
                  ]
                }
              ],
              "Version": "1"
            }
          EOF
            permission_policy_type     = "Inline"
            permission_policy_name     = "ecs-policy"
         }
      ]
      relay_state                      = "https://cloudsso.console.aliyun.com/test1"
      session_duration                 = 1800
      force_remove_permission_policies = true
   }
]
```

## 示例

- [Complete](https://github.com/terraform-alicloud-modules/terraform-alicloud-cloud-sso/tree/master/examples/complete)

## 贡献

Report issues/questions/feature requests on in the [issues](https://github.com/terraform-alicloud-modules/terraform-alicloud-cloud-sso/issues/new) section.

<!-- BEGINNING OF PRE-COMMIT-TERRAFORM DOCS HOOK -->

## Terraform 版本

| Name | Version |
|------|---------|
| <a name="requirement_terraform"></a> [terraform](#requirement\_terraform) | >= 0.13.1 |
| <a name="requirement_alicloud"></a> [alicloud](#requirement\_alicloud) | >= 1.145.0 |

## 文档

本Module已经被发布到 Terraform Registry 上了，详细请看[文档](https://registry.terraform.io/modules/terraform-alicloud-modules/cloud-sso/alicloud/latest)

## 作者

Created and maintained by Alibaba Cloud Terraform Team(terraform@alibabacloud.com)

## 协议

MIT License. See LICENSE for full details.

## 参考

* [Terraform-Provider-Alicloud Github](https://github.com/aliyun/terraform-provider-alicloud)
* [Terraform-Provider-Alicloud Release](https://releases.hashicorp.com/terraform-provider-alicloud/)
* [Terraform-Provider-Alicloud Docs](https://registry.terraform.io/providers/aliyun/alicloud/latest/docs)