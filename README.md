***Задача 2*** Инициализируем проект и создаем воркспейсы.
Вывод команды terraform workspace list.
Вывод команды terraform plan для воркспейса prod.

**Ответ**
```commandline
   /var/r/me/k/s/share/git/terraform    Terraform !1 ?4  terraform workspace list                                                                        ✔  7m 47s   prod  
  default
* prod
  stage
```

```commandline
    /var/r/me/k/s/share/git/terraform    Terraform !1 ?4  terraform apply                                                                                  ✔  1m 2s   prod  
yandex_iam_service_account.sa: Refreshing state... [id=aje02q63hibnd09cb81u]
yandex_iam_service_account_static_access_key.sa-static-key: Refreshing state... [id=aje04o36a1i0ufk5jo6o]
yandex_resourcemanager_folder_iam_member.sa-editor: Refreshing state... [id=b1gulo66u29jcgbre8a4/storage.editor/serviceAccount:aje02q63hibnd09cb81u]
yandex_storage_bucket.state[0]: Refreshing state... [id=prod]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # yandex_storage_bucket.state[0] must be replaced
  # (moved from yandex_storage_bucket.state)
-/+ resource "yandex_storage_bucket" "state" {
      ~ bucket                = "prod" -> "prod-0" # forces replacement
      ~ bucket_domain_name    = "prod.storage.yandexcloud.net" -> (known after apply)
      ~ default_storage_class = "STANDARD" -> (known after apply)
      ~ folder_id             = "b1gulo66u29jcgbre8a4" -> (known after apply)
      ~ id                    = "prod" -> (known after apply)
      - max_size              = 0 -> null
      - policy                = "" -> null
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)
        # (4 unchanged attributes hidden)

      + anonymous_access_flags {
          + list = (known after apply)
          + read = (known after apply)
        }
      - anonymous_access_flags {
          - list = false -> null
          - read = false -> null
        }

      ~ versioning {
          ~ enabled = false -> (known after apply)
        }
    }

  # yandex_storage_bucket.state[1] will be created
  + resource "yandex_storage_bucket" "state" {
      + access_key            = "**************82B4b"
      + acl                   = "private"
      + bucket                = "prod-1"
      + bucket_domain_name    = (known after apply)
      + default_storage_class = (known after apply)
      + folder_id             = (known after apply)
      + force_destroy         = false
      + id                    = (known after apply)
      + secret_key            = (sensitive value)
      + website_domain        = (known after apply)
      + website_endpoint      = (known after apply)

      + anonymous_access_flags {
          + list = (known after apply)
          + read = (known after apply)
        }

      + versioning {
          + enabled = (known after apply)
        }
    }

Plan: 2 to add, 0 to change, 1 to destroy.

Do you want to perform these actions in workspace "prod"?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_storage_bucket.state[0]: Destroying... [id=prod]
yandex_storage_bucket.state[1]: Creating...
yandex_storage_bucket.state[0]: Still destroying... [id=prod, 10s elapsed]
yandex_storage_bucket.state[1]: Still creating... [10s elapsed]
yandex_storage_bucket.state[0]: Destruction complete after 13s
yandex_storage_bucket.state[0]: Creating...
yandex_storage_bucket.state[1]: Still creating... [20s elapsed]
yandex_storage_bucket.state[0]: Still creating... [10s elapsed]
yandex_storage_bucket.state[1]: Still creating... [30s elapsed]
yandex_storage_bucket.state[0]: Still creating... [20s elapsed]
yandex_storage_bucket.state[1]: Still creating... [40s elapsed]
yandex_storage_bucket.state[0]: Still creating... [30s elapsed]
yandex_storage_bucket.state[1]: Still creating... [50s elapsed]
yandex_storage_bucket.state[0]: Still creating... [40s elapsed]
yandex_storage_bucket.state[1]: Still creating... [1m0s elapsed]
yandex_storage_bucket.state[1]: Creation complete after 1m2s [id=prod-1]
yandex_storage_bucket.state[0]: Still creating... [50s elapsed]
yandex_storage_bucket.state[0]: Still creating... [1m0s elapsed]
yandex_storage_bucket.state[0]: Creation complete after 1m1s [id=prod-0]

Apply complete! Resources: 2 added, 0 changed, 1 destroyed.

Outputs:

access_key = <sensitive>
secret_key = <sensitive>
```
