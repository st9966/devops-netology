***Задача 1*** (Вариант с Yandex.Cloud). Регистрация в ЯО и знакомство с основами (необязательно, но крайне желательно).

**Ответ**

```commandline
[k55 terraform]# yc config list
token: **********************Flm0Y
cloud-id: b1gf9amaic6lr0gpjder
folder-id: b1g161nke1kutmr5qu3g
compute-default-zone: ru-central1-b
```

***Задача 2*** Создание aws ec2 или yandex_compute_instance через терраформ.

**Ответ**

С помощью Packer

```commandline
[k55 terraform]# terraform apply
data.yandex_compute_image.ubuntu_image: Reading...
data.yandex_compute_image.ubuntu_image: Read complete after 1s [id=fd8qps171vp141hl7g9l]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # yandex_compute_instance.test1 will be created
  + resource "yandex_compute_instance" "test1" {
      + created_at                = (known after apply)
      + folder_id                 = (known after apply)
      + fqdn                      = (known after apply)
      + hostname                  = (known after apply)
      + id                        = (known after apply)
      + name                      = "test1"
      + network_acceleration_type = "standard"
      + platform_id               = "standard-v1"
      + service_account_id        = (known after apply)
      + status                    = (known after apply)
      + zone                      = (known after apply)

      + boot_disk {
          + auto_delete = true
          + device_name = (known after apply)
          + disk_id     = (known after apply)
          + mode        = (known after apply)

          + initialize_params {
              + block_size  = (known after apply)
              + description = (known after apply)
              + image_id    = "fd8qps171vp141hl7g9l"
              + name        = (known after apply)
              + size        = (known after apply)
              + snapshot_id = (known after apply)
              + type        = "network-hdd"
            }
        }

      + network_interface {
          + index              = (known after apply)
          + ip_address         = (known after apply)
          + ipv4               = true
          + ipv6               = (known after apply)
          + ipv6_address       = (known after apply)
          + mac_address        = (known after apply)
          + nat                = true
          + nat_ip_address     = (known after apply)
          + nat_ip_version     = (known after apply)
          + security_group_ids = (known after apply)
          + subnet_id          = (known after apply)
        }

      + placement_policy {
          + host_affinity_rules = (known after apply)
          + placement_group_id  = (known after apply)
        }

      + resources {
          + core_fraction = 100
          + cores         = 2
          + memory        = 2
        }

      + scheduling_policy {
          + preemptible = (known after apply)
        }
    }

  # yandex_vpc_network.network_terraform will be created
  + resource "yandex_vpc_network" "network_terraform" {
      + created_at                = (known after apply)
      + default_security_group_id = (known after apply)
      + folder_id                 = (known after apply)
      + id                        = (known after apply)
      + labels                    = (known after apply)
      + name                      = "net_terraform"
      + subnet_ids                = (known after apply)
    }

  # yandex_vpc_subnet.subnet_terraform will be created
  + resource "yandex_vpc_subnet" "subnet_terraform" {
      + created_at     = (known after apply)
      + folder_id      = (known after apply)
      + id             = (known after apply)
      + labels         = (known after apply)
      + name           = "sub_terraform"
      + network_id     = (known after apply)
      + v4_cidr_blocks = [
          + "192.168.1.0/24",
        ]
      + v6_cidr_blocks = (known after apply)
      + zone           = "ru-central1-b"
    }

Plan: 3 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

yandex_vpc_network.network_terraform: Creating...
yandex_vpc_network.network_terraform: Creation complete after 1s [id=enpn975dngj96ocsgoj7]
yandex_vpc_subnet.subnet_terraform: Creating...
yandex_vpc_subnet.subnet_terraform: Creation complete after 1s [id=e2ltb52ad7qgj9de57n3]
yandex_compute_instance.test1: Creating...
yandex_compute_instance.test1: Still creating... [10s elapsed]
yandex_compute_instance.test1: Still creating... [20s elapsed]
yandex_compute_instance.test1: Still creating... [30s elapsed]
yandex_compute_instance.test1: Still creating... [40s elapsed]
yandex_compute_instance.test1: Still creating... [50s elapsed]
yandex_compute_instance.test1: Still creating... [1m0s elapsed]
yandex_compute_instance.test1: Still creating... [1m10s elapsed]
yandex_compute_instance.test1: Creation complete after 1m10s [id=epd754qseq51nq2ivf7h]

Apply complete! Resources: 3 added, 0 changed, 0 destroyed.
```

file:///home/k55/Изображения/Screenshot_20220629_185255.png


