
#win Actisha_infra
Actisha Infra repository


#### #HW1 Git
First PR

#### #HW2 ChatOps
Integration: GitHub, Slack, Travis
travis encrypt "devops-team-otus:<token>#<user-group>" --add notifications.slack.rooms --com

#### #HW3 GCP
[actisha@localhost ~]$ cat /etc/hosts
*nix
34.76.93.197 bastion
10.132.0.3  someinternal

Win
C:\Users\Hanna>type C:\Windows\System32\drivers\etc\hosts
34.76.93.197 bastion
10.132.0.3  someinternal

*nix
[actisha@localhost ~]$ cat ~/.ssh/config
Host bastion
  Hostname bastion
  User appuser
  IdentityFile ~/.ssh/appuser
Host someinternal
  Hostname someinternal
  User appuser
  ProxyCommand ssh -W %h:%p bastion
  IdentityFile ~/.ssh/appuser

[actisha@localhost ~]$ ssh someinternal
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-1042-gcp x86_64)
 
  
Win
C:\Users\Hanna>type C:\Users\Hanna\.ssh\config
Host bastion
  Hostname bastion
  User appuser
  IdentityFile ~/.ssh/appuser
Host someinternal
  Hostname someinternal
  User appuser
  ProxyCommand ssh -W %h:%p bastion
  IdentityFile ~/.ssh/appuser

Hanna@LAPTOP /c/repos
$ ssh someinternal
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-1042-gcp x86_6

34.76.93.197 bastion
10.132.0.3  someinternal

bastion_IP = 34.76.93.197
someinternalhost_IP = 10.132.0.3

---------------------------
Развертывание mongodb и printul с помощью готового setupvpn.sh (файл, описывающий установку VPN-сервера)
$ sudo bash setupvpn.sh

Подключение по конфигу:
sudo openvpn --config /etc/openvpn/client/otus_test_testsrv.ovpn

Last login: Tue Oct  1 15:25:42 2019 from laptop-nkm50cmp.mymifi
[actisha@localhost ~]$ ssh -i ~/.ssh/appuser appuser@10.132.0.3
Welcome to Ubuntu 16.04.6 LTS (GNU/Linux 4.15.0-1042-gcp x86_64)

---------------------------
Web-морда VPN (большое спасибо Svetozar за предоставленную возможность :))
https://hannamonty.systemctl.tech/

#### #HW4 GCP testapp
Установка Google Cloud SDK и создание нового инстанса reddit-app

gcloud compute instances create reddit-app
--boot-disk-size=10GB
--image-family ubuntu-1604-lts
--image-project=ubuntu-os-cloud
--machine-type=g1-small
--tags puma-server
--restart-on-failure

testapp_IP = 34.77.75.82 
testapp_port = 9292  


Подключение к серверу
ssh appuser@reddit-app

Установка Ruby и Bundler

---------------------------
$ sudo apt update
$ sudo apt install -y ruby-full ruby-bundler build-essential

ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
Bundler version 1.11.2

Установка MongoDB

---------------------------
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv EA312927
sudo bash -c 'echo "deb http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" > /etc/apt/sources.list.d/mongodb-org-3.2.list'
sudo apt update
sudo apt install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod

Деплой приложения

---------------------------
Выполнить в домашней директории appuser git clone -b monolith https://github.com/express42/reddit.git
Устанавливаем зависимости приложения cd reddit && bundle install
Запуск СП puma -d
Проверка порта ps aux | grep puma
9292

Открываем порт в файрволе (GPC)
Для проверки перейти http://34.77.75.82:9292/

Создаем исполняемые скрипты для запуска команд выше (*.sh)
Копируем на наш инстанс все необходимые файлы
scp install_ruby.sh appuser@reddit-app:/home/appuser/

Создание инстансов и установка необходимого ПО

---------------------------
gcloud compute instances create reddit-app\
  --boot-disk-size=10GB \
  --image-family ubuntu-1604-lts \
  --image-project=ubuntu-os-cloud \
  --machine-type=g1-small \
  --tags puma-server \
  --restart-on-failure
  --metadata-from-file startup-script-url=/home/appuser/startup_script.sh

Работа с правилами

---------------------------
default-puma-server

gcloud compute firewall-rules create default-puma-server\
  --allow=tcp:9292 \
  --source-ranges="0.0.0.0/0" \
  --target-tags=puma-server \

#### #HW5 GCP. Packer
Выполнен перенос скриптов из предыдущего ДЗ в каталог config-scripts.

В каталог packer/scripts/  скопированы скрипты: 
install_mongodb.sh  
install_ruby.sh   

Установлен packer.
Создан Packer шаблон (ubuntu16.json), с помощью которого собираем наш образ с предустановленными Ruby и MongoDB.
Выполнена проверка валидности созданного шаблона - ```packer validate ./ubuntu16.json.```
Запуск билда - ```packer build ubuntu16.json```
Запуск приложения:
```
ssh appuser@104.199.106.99
git clone -b monolith https://github.com/express42/reddit.git  
cd reddit && bundle install  
puma -d
```
Проверка подключения - http://104.199.106.99:9292/
35.205.128.239

Параметризованы переменные в variables.json, файл добавлен в .gitignore.
Проверка запуска - packer build -var-file variables.json ubuntu16.json.
Полет нормальный. 

---------------------------
HW5 - 1*
Создание ```immutable.json``` с описанием образа reddit-full.
В ```packer/files/deploy.sh``` описана установка приложения.
Запуск установки:
 ```sh
 packer build -var-file variables.json immutable.json
 ```
HW5 - 2*
Cкрипт config-scripts/create-reddit-vm.sh  
 ```sh
 gcloud compute instances create reddit-app-full\
  --image-family reddit-full \
  --image-project=infra-254414 \
  --tags puma-server \
  --restart-on-failure
```
Проверка подключения - http://35.205.128.239:9292/

#### #HW6 Terraform 1

Скачивание Terraform:
```sudo wget https://releases.hashicorp.com/terraform/0.12.18/terraform_0.12.18_linux_amd64.zip```

Распаковка:
```sudo unzip ./terraform_0.12.18_linux_amd64.zip –d /usr/local/bin```

Загружен провайдер ```terraform init``` Terraform, созданы файлы описания инфраструктуры **main.tf**, описание выходных переменных **outputs.tf**, директория files, файлы **puma.service**, **deploy.sh**.
Структура **main.tf**:
- *Provider* - позволяет управлять ресурсами GCP через API.
- *Resource* - управление ресурсами различных сервисов GCP.
   - ресурс "google_compute_instance" "name" - для управления инстансами.
      provisioner "name" - для запуска инструментов управления конфигурацией или начальной настройки системы.
      connection - параметры подключения провиженеров к VM.
   - ресурс "google_compute_firewall" "name" - определяет правило для firewall.

Перед внесением изменений - проверить какие изменения планируется провести:
```terraform plan```
Принять изменения:
```terraform apply```
Поиск атрибутов из state файла:
```terraform show | grep nat_ip```
Определение ssh ключа:
```metadata = {
     ssh-keys = "appuser:${file("~/.ssh/appuser.pub")}"
  }```
Обновление значения выходной переменной:
```terraform refresh```
Просмотр значения выходной переменной:
```terraform output
terraform output app_external_ip
```
Чтобы пометить ресурс, который terraform должен пересоздать, при следующем запуске terraform apply:
```terraform taint google_compute_instance.app
terraform plan
terraform apply
```
Создан файл для описания входных переменных (**variables.tf**).
Создан файл для определения входных переменных (**terraform.tfvars**).
Проверка подключения - http://104.199.106.99:9292/

Команда для удаления всех созданных ресурсов:
```terraform destroy```

Проверка подключения после пересоздания - http://35.241.186.18:9292/

---------------------------
##Самостоятельное задание

Определена input переменная для приватного ключа, использующегося в определении подключения для провижинеров (connection). Для этого:
 - в **terraform.tfvars** добавлен параметр private_key_path с указанием пути до приватного ключа ```private_key_path = "~/.ssh/appuser"```
 - в **variables.tf** добавлено
 
variable private_key_path {
  description = "Path to the private key used for connection"
}

- в **main.tf** получаем значение пользовательской переменной
```private_key = file(var.private_key_path)```

Аналогично для переменной Zone.

Форматирование конфигурационных файлов:
```terraform fmt```

Создан файл **terraform.tfvars.example** с переменными.

---------------------------
## Задание со *

Добавлен ssh ключ для нескольких user-ов в метаданные проекта. 
Для этого в файле **main.tf** создана секция resource "google_compute_project_metadata_item" "ssh-keys" с описанием:
```value = "appuser:${file(var.public_key_path)} appuser1:${file(var.public_key_path)} appuser2:${file(var.public_key_path)}"```

Добавлен в веб-интерфейсе в метаданные проекта ключ ssh для юзера appuser_web. 
После выполнения в консоли ```terraform apply```, после чего юзер appuser_web удалился.

#### #HW7 Terraform 2

Создана ветка terraform-2, создана инфраструктура по шаблонам из HW6 в директории terraform (```terraform apply```).
Проверено дефолтное правило для ssh:
```gcloud compute firewall-rules list```

В **main.tf** создан ресурс firewall resource "google_compute_firewall" "firewall_ssh" с описанием:
``` 
   name = "default-allow-ssh"   network = "default" 
   allow {     protocol = "tcp"     ports = ["22"]   } 
   source_ranges = ["0.0.0.0/0"] }
```

Выполнено ```terraform apply```, соответственно, возникла ошибка, т.к. terraform не знает, что данное правило firewall уже существует. 
Для того, чтобы ее избежать необходимо импортировать информацию о созданных без помощи terraform ресурсах.
```terraform import google_compute_firewall.firewall_ssh default-allow-ssh```
Снова выполнено ```terraform apply```. Теперь всё отработало корректно.

В **main.tf** добавлен ресурс google_compute_address (используется для определения IP-адреса).
*В случае, если после добавления этого ресурса на следующем шаге падает терраформ с ошибкой Quota 'STATIC_ADDRESSES' exceeded, необходимо зайти в VPC Network -> external ip addresses и удалить static ip, который был зарезервирован под инстанс бастиона.

Удалена и создана заново инфраструктура.

*Ссылку в одном ресурсе на атрибуты другого тераформ понимает как зависимость одного ресурса от другого. Это влияет на очередность создания и удаления ресурсов при применении изменений.

Соответственно, в ресурсе "google_compute_instance" был определен IP-адрес для создаваемого инстанса (это не явная зависимость):
```
network_interface {
 network = "default"
 access_config { nat_ip = google_compute_address.app_ip.address } 
 }
```

Также Terraform поддерживает также явную зависимость используя параметр ```depends_on```:
https://www.terraform.io/docs/configuration/resources.html

--------------------------- 
##Структуризация ресурсов

БД вынесена на отдельный инстанс VM. 
Для этого в директории packer, были созданы шаблоны **db.json**(с установкой Mongodb) и **app.json** (с установкой Ruby).

Провалидировали файлы:
```
packer -var-file variables.json validate app.json
packer -var-file variables.json validate db.json
```

Запекли образы:
```
packer build -var-file variables.json app.json
packer build -var-file variables.json db.json
```

В директории terrafor конфиг **main.tf** разбит на три:
**app.tf**(приложение),
**db.tf**(БД),
**vpc.tf**(firewall rules для ssh).

В **variables.tf** добавлено описание переменных image-ей для БД и APP:
```
variable app_disk_image {   description = "Disk image for reddit app"   default = "reddit-app-base" }
```
```
variable db_disk_image {   description = "Disk image for reddit db"   default = "reddit-db-base" }
```

В **vpc.tf** выносим правило firewall_ssh:
```
resource "google_compute_firewall" "firewall_ssh" {   
name = "default-allow-ssh"   network = "default"   
allow {    
 protocol = "tcp"     
 ports = ["22"]   
 }   
source_ranges = ["0.0.0.0/0"] 
}
```
 
в **main.tf** осталось:
```
provider "google" 
{  
version = "~> 2.15"   
project = var.project   
region = var.region 
}
```
 
Выполнено:
```
terraform fmt
terraform plan
terraform apply
``` 

Результат: всё успешно созадлось. 

--------------------------- 
##Модули

В директории terraform создана директория *modules->db*. В неё перенесены файлы **main.tf**, **variables.tf**, **outputs.tf**.
В **variables.tf**:
```
variable public_key_path {   description = "Path to the public key used to connect to instance" } 
variable zone {   description = "Zone" } 
variable db_disk_image {   description = "Disk image for reddit db"   default     = "reddit-db-base" }
```
В директории terraform создана директория *modules->app*. В неё перенесены файлы **main.tf**, **variables.tf**, **outputs.tf**.
В **variables.tf**:
```
variable public_key_path {   description = "Path to the public key used to connect to instance" } 
variable zone {   description = "Zone" } 
variable app_disk_image {   description = "Disk image for reddit app"   default     = "reddit-app-base" }
```
В **outputs.tf**:
```
output "app_external_ip" {   value = google_compute_instance.app.network_interface.0.access_config.0.assigned_nat_ip }
```

В директории terraform удалены **db.tf** и **app.tf**. 
Скорректирован файл **main.tf**:
```
  provider "google" {
  version = "~> 2.15"
  project = var.project
  region  = var.region
}
module "app" {
  source          = "./modules/app"
  project         = var.project
  public_key_path = var.public_key_path
  zone            = var.zone
  app_disk_image  = var.app_disk_image
}
module "db" {
  source          = "./modules/db"
  project         = var.project
  public_key_path = var.public_key_path
  zone            = var.zone
  db_disk_image   = var.db_disk_image
}
```

Для использования модулей они были загружены в *.terraform* командой ```terraform get```.
Выполнена команда ```terraform plan```.
Если возникнет ошибка  *output 'app_external_ip': unknown resource 'google_compute_instance.app'*, то необходимо переопределить переменную.
```
output "app_external_ip" {   value = module.app.app_external_ip }
```
Повторно выполнена команда ```terraform plan```.

Аналогично предыдущим модулям создан модуль vpc, в котором определены настройки firewall_ssh:
Cоздана директория *terraform->modules->vpc*. 
В неё перенесены файлы **main.tf**, **variables.tf**, **outputs.tf**.
Описан вызов модуля в основном конфиге main.tf:
```
  module "vpc" {
  source          = "./modules/vpc"
  project         = var.project
  public_key_path = var.public_key_path
  zone            = var.zone
}
```
В директории terraform удален **vpc.tf**. 
Выполнены команды: 
```terraform get```
```terraform apply```

Результат - в GCP появилось правило фаерволла для ssh, к обоим инстансам можно подключиться по ssh.

--------------------------- 
### Параметризация модулей
В **vpc.tf** параметрезирован модуль за счёт использования input переменных.
В **main.tf** в ресурс ```resource "google_compute_firewall" "firewall_ssh"```добавлено описание:
```source_ranges = var.source_ranges```
В **variables.tf** добавлено описание IP-адресов, с которых возможен доступ.
```
variable source_ranges {
  description = "Allowed IP addresses"
  default     = ["0.0.0.0/0"]
}
```

Проведен эксперимент, в блоке, описывающем подключение модуля vpc, добавлено правило, позволяющее доступ только с моего IP:
```source_ranges = ["my_ip/32"]```
Доступ был разрешен только с моего IP. 
Возвращено значение 0.0.0.0/0 в source_ranges.

Ресурсы были удалены:
```terraform destroy``` 
  
--------------------------- 
## Создание Stage & Prod
В директории terraform создана директории *modules->stage* и *modules->prod*.
Из директории terraform в них скопированы файлы: **main.tf**,**terraform.tfvars**, **variables.tf**, **outputs.tf**.  

В **main.tf** для *stage* открыт ssh доступ для всех IP-адресов:
```
  module "vpc" {
  source          = "../modules/vpc"
  project         = var.project
  public_key_path = var.public_key_path
  source_ranges   = ["0.0.0.0/0"]
}
```

В **main.tf** для *prod* открыт ssh доступ для конкретного IP-адреса:
```
  module "vpc" {
  source          = "../modules/vpc"
  project         = var.project
  public_key_path = var.public_key_path
  source_ranges   = ["94.25.168.212/32"]
}
```
Также в **main.tf** были скорректированы пути ко всем модулям "../modules/app" и др.

Выполнена проверка правильности настроек конфигурации для каждого окружения:
выполнено получение модулей ```terraform get```,
произведена инициализация ```terraform init```,
применены изменения ```terraform apply```,
удалены созданные ресурсы ```terraform destroy```.

--------------------------- 
## Самостоятельное задание
Удалены файлы **main.tf**,**terraform.tfvars**, **variables.tf**, **outputs.tf** из директории terraform. 
Выполнена частичная параметризация конфигурации модулей.
Отформатированы конфигурационные файлы ```terraform fmt```.

--------------------------- 
## Реестр модулей
Ссылка на публичный реестр модулей от HashiCorp - https://registry.terraform.io/
Для создания бакета в сервисе Storage использован модуль *storage-bucket* - https://registry.terraform.io/modules/SweetOps/storage-bucket/google/0.3.0
В директории terraform был созадн файл **storage-bucket.tf**.
Затем туда же были скопированы файлы **variables.tf** и **terraform.tfvars**. 
Выполнено проверка и применени. 
```terraform fmt```
```terraform get```
```terraform init```
```terraform plan```
```terraform apply```
Проверено в GSP - созадлся Storage - https://console.cloud.google.com/storage/browser/sweetops-storage-hanna / gs://sweetops-storage-hanna

--------------------------- 
## Задание*
В каталогах stage и prod создан файл **backend.tf**.
в каждом каталоге выполнена команда ```terraform init```.
Конфиги были вынесены в другую директорию, по очереди в каждом каталоге запущен Terraform.
При запуске конфигурации одновременно срабатывают блокировки.

#### #HW8 Ansible 1

Выполнена установка/проверка наличия:
Python 2.7.5
pip 19.3.1 from /usr/lib/python2.7/site-packages/pip (python 2.7)
ansible 2.9.4 (с помощью предварительно созданного файла **requirements.txt** - ```pip install -r requirements.txt```)

--------------------------- 
##Управление хостом при помощи Ansible 
Создана инфраструктура из окружения *stage* -  ```terraform apply```.
В директории *ansible* создан файл **inventory**.
```appserver ansible_host=@my_ip@ ansible_user=appuser ansible_private_key_file=~/.ssh/appuser```

Запускаем проверку, что ansible имеет доступ к нашей инфраструктуре в части app:
```ansible appserver -i ./inventory -m ping```
Вывод:
```
  appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

```-m ping``` - вызываемый модуль 
```-i ./inventory``` - путь до файла инвентори 
```appserver``` - Имя хоста, которое указали в инвентори, откуда Ansible yзнает, как подключаться к хосту.

Аналогично проделано для инстанса с БД.
```dbserver ansible_host=35.233.123.223 ansible_user=appuser ansible_private_key_file=~/.ssh/appuser```
Запускаем проверку, что ansible имеет доступ к нашей инфраструктуре в части db:
```ansible dbserver -i ./inventory -m ping```
Вывод:
```
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Создан конфиг **ansible.cfg** в директории *ansible*.
```
inventory = ./inventory 
remote_user = appuser 
private_key_file = ~/.ssh/appuser 
host_key_checking = False retry_files_enabled = False
```

Скорретирован **inventory**:
```
$ cat inventory
appserver ansible_host=35.195.74.19
dbserver ansible_host=35.233.123.223
```

Для проверки выполнено:
```ansible dbserver -m command -a uptime```
Модуль *command* позволяет запускать произвольные команды на удаленном хосте.
Команда *uptime* показывает время работы инстанса. 
Команд *uptime* передается как аргумент для данного модуля, с помощью опции *-a*.

Вывод:
```
dbserver | CHANGED | rc=0 
 13:01:22 up  1:56,  1 user,  load average: 0.07, 0.02, 0.00
 ```
 
--------------------------- 
##Работа с группами хостов

Скорректировала **inventory** файл для упрощения работы с группами хостов:
```
[app]
appserver ansible_host=35.195.74.19
[db]
dbserver ansible_host=35.233.123.223
```
Проверяем результат ```ansible app -m ping```:

```
  appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

*app* - имя группы, 
*-m ping* - имя модуля Ansible, 
*appserver* - имя сервера в группе, для которого применился модуль.

--------------------------- 
##Создание inventory.yml

```
app:
  hosts:
    appserver:
      ansible_host: 35.195.74.19

db:
  hosts:
    dbserver:
      ansible_host: 35.233.123.223 
```

Проверяем корректность ```ansible all -m ping -i inventory.yml```:

```
appserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

```
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

Ключ -i переопределяет путь к инвентори файлу.

--------------------------- 
##Выполнение команд через ansible

Проверка версии Ruby на app-сервере через ansible ```ansible app -m command -a 'ruby -v'```:
```
appserver | CHANGED | rc=0 >>
ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
```

Проверка bundler на app-сервере через ansible```ansible app -m command -a 'bundler -v'```:
```
appserver | CHANGED | rc=0 >>
Bundler version 1.11.2
```

Проверка запуска двух команд через модуль command:
```ansible app -m command -a 'ruby -v; bundler -v'```

```
appserver | FAILED | rc=1 >>
ruby: invalid option -;  (-h will show valid options) (RuntimeError)non-zero return code
```

Завершилось с ошибкой, т.к. модуль *command* выполняет команды, не используя оболочку (sh, bash), поэтому в нем не работают перенаправления потоков и нет доступа к некоторым переменным окружения.

Проверка выполнения через модуль *shell* ```ansible app -m shell -a 'ruby -v; bundler -v'```:
```
appserver | CHANGED | rc=0 >>
ruby 2.3.1p112 (2016-04-26) [x86_64-linux-gnu]
Bundler version 1.11.2
```

Проверка на хосте с БД статус сервиса MongoDB с помощью модуля command или shell(эта операция аналогична запуску на хосте команды systemctl status mongod).```ansible db -m command -a 'systemctl status mongod'```:

```
dbserver | CHANGED | rc=0 >>
● mongod.service - High-performance, schema-free document-oriented database
```
Через модуль *shell* ```ansible db -m shell -a 'systemctl status mongod'```:
```
dbserver | CHANGED | rc=0 >>
● mongod.service - High-performance, schema-free document-oriented database
```

И еще разочек, но с помощью модуля *systemd*, который предназначен для управления сервисами ```ansible db -m systemd -a name=mongod```:
```
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "name": "mongod",
    "status": {
...
        "ActiveState": "active"
```
И совсем последний разочек с помощью модуля *service*, который более универсален и будет работать и в более старых ОС с init.d инициализацией
 ```ansible db -m service -a name=mongod```:

```
dbserver | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "name": "mongod",
    "status": {
...
        "ActiveState": "active"
```
Используем модуль *git* для клонирования репозитория на app-сервер ```ansible app -m git -a 'repo=https://github.com/express42/reddit.git dest=/home/appuser/reddit'```:

```
appserver | CHANGED => {
    "after": "5c217c565c1122c5343dc0514c116ae816c17ca2",
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "before": null,
    "changed": true
}
```

Повторно выполняем команду:

```
appserver | SUCCESS => {
    "after": "5c217c565c1122c5343dc0514c116ae816c17ca2",
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "before": "5c217c565c1122c5343dc0514c116ae816c17ca2",
    "changed": false,
    "remote_url_changed": false
}
```

Разница в значениях переменных (это значит, что изменения не произошли). Само выполнение повторное команды проходит успешно.
   * "before": null,
   * "changed": true
 и при втором выполнении эти параметры уже стали:
   * "before": "5c217c565c1122c5343dc0514c116ae816c17ca2",
   * "changed": false,
   * "remote_url_changed": false
   
Аналогично выполним с модулем *command* ```ansible app -m command -a 'git clone https://github.com/express42/reddit.git /home/appuser/reddit'```:

Повторное выполнение завершается с ошибкой.
```
appserver | FAILED | rc=128 >>
fatal: destination path '/home/appuser/reddit' already exists and is not an empty directory.non-zero return code
```

--------------------------- 
##Создание простого playbook для ansible
Созан файл **clone.yml**, выполняющий клонирование репозитория аналогично предыдущим командам:
```
- name: Clone
  hosts: app
  tasks:
    - name: Clone repo
      git:
        repo: https://github.com/express42/reddit.git
        dest: /home/appuser/reddit
```

Выполнена команда ```ansible-playbook clone.yml```:
PLAY RECAP ********************************************************************************************************************
appserver                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Выполнено удаление каталога с репозиторием  ```ansible app -m command -a 'rm -rf ~/reddit'```:
```
appserver | CHANGED | rc=0 >>
```

Повторно выполнен playbook, клонирующий репозиторий:
PLAY RECAP ********************************************************************************************************************
appserver                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

Как видим, выполнилось одно изменение. 

#### #HW9 Ansible 2

Скорревтирован .gitignore - прописаны временные файлы ansible *.retry.
В директории *ansible* создан плейбук - **reddit_app.yml**

Коротко о плейбуке:
 - name <-- cловесное описание сценария 
 - hosts <-- для каких хостов будут выполняться описанные ниже таски  
 - tasks <-- блок тасков (заданий), которые будут выполняться для указанных данных хостов
 - plays <-- сценарий,для группировки tasks, которые должны быть выполнены на конкретном хосте/группе хостов
 - tasks <-- задание/набор заданий, которые будут выполнены на хосте/группе хостов
 - tags <-- возможность запускать отдельные таски, имеющие определенный тег
 - template <-- модуль, чтобы скопировать конфиг mongoDB на удаленную машину
 - become <-- Выполнить задание от root(true/false)       
 - src <-- (например, *templates/mongod.conf.j2*) путь до локального файла-шаблона         
 - dest <-- (например, */etc/mongod.conf*) путь на удаленном хосте        
 - mode <-- (например, 0644) права на файл, которые нужно установить
   
Создана директория *ansible->templates*, в ней шаблон конфига MongoDB **mongod.conf.j2**.
Выплонена проверка:
```
ansible-playbook reddit_app.yml --check --limit db
```
*--check* - опция для пробного прогона
*--limit* - опция для ограничения группы хостов, для которых применить плейбук

Результат выполнения:
```
PLAY RECAP ********************************************************************************************************************
dbserver                   : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
Добавилен *handlers*, выполняющий *restart mongod*.
Применен плейбук 
```
ansible-playbook reddit_app.yml --limit db
```
Результат выполнения:
```
PLAY RECAP ********************************************************************************************************************
dbserver                   : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
--------------------------- 
##Настройка нстанса приложений

Создана директория *ansible->files*. В ней файл **puma.service**.
В него добавилась строка чтения переменных окружения из файла:
```
EnvironmentFile=/home/appuser/db_config
```
Через  эту переменную окружения передается адрес инстанса БД, чтобы приложение знало, куда ему обращаться для хранения данных.

Скорректирован плейбук:
 - copy <-- модуль для копирования файла на удаленный хост
 - systemd <-- модуль для настройки автостарта puma-сервера
 - новый handler <-- для reload puma
 
 Добавлен новый шаблон в директорию *templates* -> **db_config.j2** - содержит присвоение переменной DATABASE_URL.
В неё подставляется значение из переменной db_host (внутренний ip инстанса с mongoDB).
Также в плейбук добавили переменную   db_host: 10.132.0.2  # <-- подставьте сюда ваш IP (внутренние IP инстанса с MongoDB).
Выполнили проверку плейбука, выполнили плейбук.
```
PLAY RECAP ********************************************************************************************************************
appserver                  : ok=6    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```
--------------------------- 
##Деплой

Добавление в плейбук модулей:
 - git <-- модуль для клонирования последней версии кода приложения
 - bundler <-- модуль для установки зависимых Ruby Gems через bundle  
После добавления деплоя выполнили проверку плейбука и сам плейбук.
```
ansible-playbook reddit_app.yml --check --limit app --tags deploy-tag 
ansible-playbook reddit_app.yml --limit app --tags deploy-tag
```

Плейбук разбит на три сценария App, DB, приложение.
Выполнили проверку плейбука и сам плейбук.

ansible-playbook reddit_app2.yml --tags db-tag
ansible-playbook reddit_app2.yml --tags app-tag
ansible-playbook reddit_app2.yml --tags deploy-tag

Новый пост http://34.76.80.27:9292/post/5e36b613566a7818fb52ca00 :)

Плейбуки переиенованы:
reddit_app.yml -> reddit_app_one_play.yml 
reddit_app2.yml -> reddit_app_multiple_plays.yml

--------------------------- 
##Создание нескольких плейбуков

Созданы и перенесены в файлы необходимые сценарии **app.yml**, **db.yml**, **deploy.yml**.
Также удалены теги и создан основной плейбук **site.yml**.
Пересоздана инфраструктура.
Проверен и исполнен плейбук:
```
ansible-playbook site.yml --check
ansible-playbook site.yml
```
Проверен результат работы приложения - создан пост.

--------------------------- 
##Провижиниг в Packer

Мы уже создали плейбуки для конфигурации и деплоя приложения. 
Создадим теперь на их основе плейбуки *ansible/packer_app.yml* и *ansible/packer_db.yml*. 

Каждый из них должен реализовывать функционал bash-скриптов, которые использовались в Packer ранее. 
- packer_app.yml - устанавливает Ruby и Bundler 
- packer_db.yml - добавляет репозиторий MongoDB, устанавливает ее и включает сервис

Модули в packer_db.yml:
   - apt - устанавливает необходимые пакеты
   - apt_key - добавляет ключ для репозитория
   - apt_repository - устанавливает репозиторий
   - systemd - активирует и запускает монгу

Прописаны вместо bash-скриптов - ansible-плейбуки. 
В *packer/app.json*:
```
"provisioners": [
    {
        "type": "ansible",
        "playbook_file": "ansible/packer_app.yml"
    }
]
```
В *packer/db.json*:
```
"provisioners": [
    {
        "type": "ansible",
        "playbook_file": "ansible/packer_db.yml"
    }
]
```
В GCP удалены образы, запечены новые образы с измененными провижинерами:
```
packer build -var-file packer/variables.json packer/app.json
packer build -var-file packer/variables.json packer/db.json
```
Пересоздано stage окружение(в каталоге terraform/stage/):
```
terraform apply
```
Повторно выполнен плейбук для конфигурации окружения и деплоя:
```
ansible-playbook site.yml
```
Проверка:
http://35.205.216.35:9292/post/5e36d401566a780c63de9245


#### #HW10 Ansible 3

Получение справки по ansible-galaxy:
```
ansible-galaxy -h
```

Создана директория *roles* в директории *ansible*.

В ней выполнены команды по созданию заготовок для ролей:
```
ansible-galaxy init app
ansible-galaxy init db
```

[actisha@localhost db]$ ll
итого 4
drwxrwxr-x. 2 actisha actisha   22 янв 20 01:38 defaults
drwxrwxr-x. 2 actisha actisha    6 янв 20 01:38 files
drwxrwxr-x. 2 actisha actisha   22 янв 20 01:38 handlers
drwxrwxr-x. 2 actisha actisha   22 янв 20 01:38 meta
-rw-rw-r--. 1 actisha actisha 1328 янв 20 01:38 README.md
drwxrwxr-x. 2 actisha actisha   22 янв 20 01:38 tasks
drwxrwxr-x. 2 actisha actisha    6 янв 20 01:38 templates
drwxrwxr-x. 2 actisha actisha   39 янв 20 01:38 tests
drwxrwxr-x. 2 actisha actisha   22 янв 20 01:38 vars

Была скопирована секция tasks из *ansible/db.yml* в *roles/db/tasks/main.yml*.
Затем из директории *ansible/templates* был скопирован шаблон **mongod.conf.j2** в директорию *roles/db/templates/*.
Был скопирован хендлер из *ansible/db.yml* в *roles/db/handlers/main.yml*.
Определены переменные в *roles/db/defaults/main.yml*.

Аналогично пошагово настраиваем роль для приложения.

Пересоздаyj stage окружение.

Скорректированы ip адреса для db и app в *inventory* и в **app.yml**. 
Выполнен плейбук **site.yml**.  
Проверена работа приложения.

--------------------------- 
## Настройка окружения

В каталоге *ansible* были созданы директории *environments->stage* и *environments->prod*.
Скопирован файл *ansible/inventory* в каталоги окружений и удален из корневой диреткории.
Сейчас два файла инвентори (т.к. два окружения), то при запуске плейбуков необходимо указывать какой конкретно использовать:
 ```
 ansible-playbook -i environments/prod/inventory deploy.yml
 ```
Скорректирован файл **ansible.cfg** (указано окружение по умолчанию).
Создана директория *group_vars* в директориях наших окружений *environments/prod* и *environments/stage*.
В файл *environments/stage/group_vars/app* скопирована переменная со значением *db_host* из **app.yml**.
Из **app.yml** удален раздел *vars*.
В файл *environments/stage/group_vars/db* скопирована переменная со значением *mongo_bind_ip* из **db.yml**.
Из **db.yml** удален раздел *vars*.
Создан файл *environments/stage/group_vars/all* с переменными для группы *all*.
Аналогично для окружения *prod*.
Добавлен *env: local*  в **roles/app/defaults/main.yml**, аналогично для роли *db*.
В tasks для ролей app и db добавлен вывод окружения, в котором работаем (**roles/app/tasks/main.yml** и в *db*):
 ```
 - name: Show info about the env this host belongs to
  debug:
    msg: "This host is in {{ env }} environment!!!"
 ```
Скорректирован файл **ansible.cfg**:
 ```
roles_path = ./roles 
 
[diff] # Включим обязательный вывод diff при наличии изменений и вывод 5 строк контекста 
always = True 
context = 5
  ```
 ```
Выполнен playbook.
 ok: [appserver] => {
    "msg": "This host is in stage environment!!!"
}

 ```

Проверена работоспособность http://34.76.21.226:9292/.

Аналогично выполнена настройка prod окружения.

--------------------------- 
## Работа с Community-ролями

Созданы файлы **environments/stage/requirements.yml** и **environments/prod/requirements.yml** с записью:
```
 - src: jdauphant.nginx    
 version: v2.21.1
```
Установлена роль:
```
 ansible-galaxy install -r environments/stage/requirements.yml
```
Т.к. community-роли не стоит коммитить в свой репозиторий, для этого добавим в *.gitignore* запись: *jdauphant.nginx*.
Документация по роли https://github.com/jdauphant/ansible-role-nginx.

Добавлены переменные в **environments/stage/group_vars/app** и **environments/prod/group_vars/app**:
 ```
 nginx_sites:
  default:
    - listen 80
    - server_name "reddit"
    - location / {
        proxy_pass http://127.0.0.1:9292;
      }
 ```
В модуле *app* конфигурации Terraform добавлено открытие 80 порта.
Добавлен вызов роли *jdauphant.nginx* в плейбук **app.yml**.
Пересоздана инфраструктура stage.
Выполнен плейбук **site.yml**:
```
ansible-playbook playbooks/site.yml --check
ansible-playbook playbooks/site.yml
``` 
Приложение работает http://34.77.7.91:9292/.
Проверено, что приложение также доступно через порт 80.

--------------------------- 
## Работа с Ansible Vault

Создан **vault.key**, его использование прописано в **ansible.cfg**: 
``` 
vault_password_file = ~/.ansible/vault.key
``` 
Также в **.gitignore** указано *vault.key*, чтобы мастер-пароль не попал в git.
Создан playbook **users.yml**:
``` 
---
- name: Create users
  hosts: all
  become: true

  vars_files:
    - "{{ inventory_dir }}/credentials.yml"

  tasks:
    - name: create users
      user:
        name: "{{ item.key }}"
        password: "{{ item.value.password|password_hash('sha512', 65534|random(seed=inventory_hostname)|string) }}"
        groups: "{{ item.value.groups | default(omit) }}"
      with_dict: "{{ credentials.users }}"
``` 
Созданы файлы для пользователей prod и stage окружений:
**ansible/environments/stage/credentials.yml**
**ansible/environments/prod/credentials.yml**

Зашифрованы файлы:
```
ansible-vault encrypt environments/stage/credentials.yml
ansible-vault encrypt environments/prod/credentials.yml
```
Добавлен вызов playbook **users.yml** в *playbooks/site.yml*.
Выполнен playbook **site.yml**.
Проверен вход под созданными пользователями.
Для редактирования переменных нужно использовать команду 
```ansible-vault edit <file>
```
Для расшифровки: 
```
ansible-vault decrypt <file>
```

#### #HW11 Ansible 4

Выполнена установка  VirtualBox:
```
yum -y install VirtualBox-6.0
Выполнено!

VBoxManage -v 
6.0.18r136238

systemctl status vboxdrv
active (exited)
```
Установка Vargant:
```
sudo yum install https://releases.hashicorp.com/vagrant/2.2.6/vagrant_2.2.6_x86_64.rpm
```
Проверим версию Vargant:
```
vagrant -v
Vagrant 2.2.6
```
Добавлены в `.gitignore` исключения для Vagrant и Molecule.
Создан **Vagrantfile** в директории *ansible*.

Выполнили команду *vargant up*.
Продолжение в следующий раз, т.к. Win10->Hyper-V->Centos7->VirtualBox->Bubunta... сказали, иди нафиг:) Антон был прав:)


#### #HW12 Docker 1

Настройка интеграции с TraviCI:
```
travis encrypt "devops-team-otus:<token>#<channel>" \       --add notifications.slack.rooms --com
```

Для установки:
```
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```
Затем:
```
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```
Выполняем установку:
```
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose
```
Запуск:
```
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker actisha
```

Установлен Docker *docker version*:
```
Client: Docker Engine - Community
 Version:           19.03.6
 API version:       1.40
Server: Docker Engine - Community
 Engine:
  Version:          19.03.6
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.16
```
*Docker-machine*:
```
base=https://github.com/docker/machine/releases/download/v0.16.0 &&
  curl -L $base/docker-machine-$(uname -s)-$(uname -m) >/tmp/docker-machine &&
  sudo mv /tmp/docker-machine /usr/local/bin/docker-machine &&
  chmod +x /usr/local/bin/docker-machine
```

Проверили *docker-compose -v*:
```docker-compose version 1.18.0, build 8dd22a9```

Проверили *docker-machine -v*:
```docker-machine version 0.16.0, build 702c267f```

Запустили первый контейнер *docker run hello-world*:
```
Hello from Docker!
```
Список запущенных контейнеров *sudo docker ps -a*:
```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Список всех контейнеров *sudo docker ps -a*:
```
71027b6baa1d        hello-world         "/hello"            13 minutes ago      Exited (0) 13 minutes ago  
```
Список сохраненных образов *docker images*:
```
hello-world         latest              fce289e99eb9     ..
```
Запуск и вход внутрь конта:
```
docker run -it ubuntu:16.04 /bin/bash
```
Команда *docker run* каждый раз запускает новый контейнер.

Если не указывать флаг *--rm* при запуске *docker run*, то после остановки контейнер вместе с содержимым остается на диске.

--------------------------- 
## Docker ps

*start* запускает остановленный(уже созданный) контейнер.
*attach* подсоединяет терминал к созданному контейнеру.

Найдем ранее созданный контейнер:
```
docker ps -a --format "table {{.ID}}\t{{.Image}}\t{{.CreatedAt}}\t{{.Names}}"
```

Запуск созданного контейнера:
```
docker start 3738f80ebbe0 
```
Присоединить терминал к созданному конту
```
docker attach 3738f80ebbe0 
root@3738f80ebbe0:/#
Ctrl + p, Ctrl + q 
```
--------------------------- 
## Docker run

Docker run vs start:
```
docker run = docker create + docker start + docker attach
```
*Через параметры передаются лимиты(cpu/mem/disk), ip, volumes. 
*При наличии опции -i, -d – запускает контейнер в background режиме, -t создает TTY
Команда *docker create* используется, когда не нужно стартовать контейнер сразу.
*docker run -it ubuntu:16.04 bash*
*docker run -dt nginx:latest*

--------------------------- 
## Docker exec

Запускает новый процесс внутри контейнера.

*docker exec -it <u_container_id> bash*

--------------------------- 
## Docker commit

Создает image из контейнера, контейнер при этом остается запущенным.
```
docker commit <u_container_id> yourname/ubuntu-tmp-file
```
Пример:
```
docker commit 3738f80ebbe0 actisha/ubuntu-tmp-file
sha256:772b8c8aace9a76889a3b50dba6d69d065f1423bd9961a29074871053329d442
```
Посмотрим:
```
docker images
REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
actisha/ubuntu-tmp-file   latest              772b8c8aace9        42 seconds ago      124MB
```
Для сдачи дз создан файл docker-1.log:
```
docker images > docker-1.log
```
docker inspect 772b8c8aace9
--------------------------- 
## Задание со *

Сравните вывод двух следующих команд:
>docker inspect <u_container_id> 
>docker inspect <u_image_id> 

Отличия контейнера и образа в файле docker-monolith/docker-1.log

--------------------------- 
## Docker kill & stop 

- *kill* сразу посылает SIGKILL
- *stop* посылает SIGTERM, и через 10 секунд посылает SIGKILL
- *SIGTERM* - сигнал остановки приложения 
- *SIGKILL* - безусловное завершение процесса

Команда *docker ps -q* для просмотра списка запущенных контейнеров.
Команда *docker kill $(docker ps -q)*  позволяет убить все запущенные.

--------------------------- 
## Docker system df

Отображает сколько дискового пространства занято образами, контейнерами и volume’ами.
Отображает сколько из них не используется и возможно удалить.

```
docker system df
```
--------------------------- 
## Docker rm & rmi

rm удаляет контейнер, можно добавить флаг -f, чтобы удалялся работающий container(будет послан sigkill).
rmi удаляет image, если от него не зависят запущенные контейнеры.
```
docker rm $(docker ps -a -q) # удалит все незапущенные контейнеры
b8dd1250eab6
3738f80ebbe0

```
```
docker rmi $(docker images -q) # удалит все загруженные образы
Untagged: actisha/ubuntu-tmp-file:latest
Deleted: sha256:772b8c8aace9a76889a3b50dba6d69d065f1423bd9961a29074871053329d442
```
--------------------------- 
## Docker rm & rmiDocker контейнеры. GCE.

Создан новый проект docker-270013.
GCloud SDK был установлен ранее https://cloud.google.com/sdk/docs/downloads-yum .
Изменена конфигурация для нового проекта GCE.

--------------------------- 
## Docker machine 

*docker-machine* - встроенный в Docker инструмент для создания хостов и установки на них docker engine. 
Имеет поддержку облаков и систем виртуализации (Virtualbox, GCP и др.)  

Команда создания:
```
docker-machine create <имя>
```
Переключение между именами:
```
eval $(docker-machine env <имя>)
```
Переключение на локальный докер:
```
eval $(docker-machine env --unset)
```
Удаление:
```
docker-machine rm <имя>
```
*docker-machine* создает хост для докер демона с указываемым образом в *--google-machine-image*. 
Образы, которые используются для построения докер контейнеров, к этому никак не относятся.
Все докер команды, которые запускаются в той же консоли после *eval $(docker-machine env <имя>)* работают с удаленным докер демоном в GCP.  

```
export GOOGLE_PROJECT=docker-270013
```
Создаем Docker-хост:
```
docker-machine create --driver google \
--google-machine-image https://www.googleapis.com/compute/v1/projects/ubuntu-os-cloud/global/images/family/ubuntu-1604-lts \
--google-machine-type n1-standard-1 \
--google-zone europe-west1-b \
docker-host
...

Docker is up and running!
```

Проверяем, что успешно создался Dcoker-хост:
```
docker-machine ls
NAME          ACTIVE   DRIVER   STATE     URL                       SWARM   DOCKER     ERRORS
docker-host   -        google   Running   tcp://34.77.209.64:2376           v19.03.6
```
Переключение Docker-машин на данное окружение:
```
eval $(docker-machine env docker-host)
```
--------------------------- 
## Повторение практики из демо на лекции 

Выводит только PID1, с которым запущен htop (т.е. процессы в namespace контейнера):
```
docker run --rm -ti tehbilly/htop 
``` 
Выводит процессы хост-машины, на которой запущен docker (т.е. процессы namespace хост-машины)  
```
docker run --rm --pid host -ti tehbilly/htop
```
--------------------------- 
##  Структура репозитория

Создаем файлы с указанным в практикуме наполенением:
**Dockerﬁle** - текстовое описание нашего образа. 
**mongod.conf** - подготовленный конфиг для mongodb.
**db_conﬁg** - содержит переменную окружения со ссылкой на mongodb.
**start.sh** - скрипт запуска приложения.

Запускаем сборку образа (из каталога docker-monolith)
```
docker build -t reddit:latest .
```
Точка в конце обязательна, она указывает на путь до Docker-контекста 
Флаг *-t* задает тег для собранного образа.
```
Successfully built a81e8fd96d32
Successfully tagged reddit:latest
```
Просмотр всех образов *docker images -a*:
```
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
reddit              latest              a81e8fd96d32        47 seconds ago       693MB
<none>              <none>              1cb38bfc8d52        47 seconds ago       693MB
<none>              <none>              ae9969d40e5b        48 seconds ago       693MB
<none>              <none>              d60d6c459cba        About a minute ago   647MB
<none>              <none>              53319cbea507        About a minute ago   647MB
<none>              <none>              eab707e5886a        About a minute ago   647MB
<none>              <none>              631091712f38        About a minute ago   647MB
<none>              <none>              91c01f8105b8        About a minute ago   647MB
<none>              <none>              daaa71e419c2        About a minute ago   643MB
<none>              <none>              74743f9d5458        2 minutes ago        150MB
ubuntu              16.04               77be327e4b63        10 days ago          124MB
tehbilly/htop       latest              4acd2b4de755        23 months ago        6.91MB
```
 Запуск созданного контейнера:
```
docker run --name reddit -d --network=host reddit:latest
...

2962eb7dbe19482f260e4edf712d71299320f209890d4ae9c8f64297e47f05d6
```
Проверяем:
```
docker ps -a

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
2962eb7dbe19        reddit:latest       "/start.sh"         51 seconds ago      Up 50 seconds                           reddit
```
```
docker-machine ls

NAME          ACTIVE   DRIVER   STATE     URL                       SWARM   DOCKER     ERRORS
docker-host   *        google   Running   tcp://34.77.209.64:2376           v19.03.6
```
Разрешим входящий TCP-трафик на порт 9292 выполнив команду: 
```
$ gcloud compute firewall-rules create reddit-app \
--allow tcp:9292 \
--target-tags=docker-machine \
--description="Allow PUMA connections" \
--direction=INGRESS
```

Проверка - http://34.77.209.64:9292/ .
Ура!

--------------------------- 
##  Docker hub

Выполнена регистрация на https://hub.docker.com/  - облачный registry сервис от компании Docker.
В него можно выгружать и загружать из него докер образы. 
Docker по умолчанию скачивает образы из докер хаба.  

Прошла аутентификацию на DockerHub:
```
docker login

Login Succeeded
```
Ставим тэг *docker tag reddit:latest <your-login>/otus-reddit:1.0*:
```
docker tag reddit:latest actisha/otus-reddit:1.0
```

Пушим образ в DockerHub*docker push <my-login>/otus-reddit:1.0*:
```
docker push actisha/otus-reddit:1.0

1.0: digest: sha256:135346c2f3ef9beb2ef437b8524a7384f8bece5385e958ea7af9070de58ea1c5 size: 3035
```
Выполнена проверка, скачен и запущен образ на локальной машине *docker run --name reddit -d -p 9292:9292 <my-login>/otus-reddit:1.0*:
```
docker run --name reddit -d -p 9292:9292 actisha/otus-reddit:1.0


Status: Downloaded newer image for actisha/otus-reddit:1.0
3bbd7244c907ae8e5c4b325142a3399dfda66f09f48a1939db6fa0f5307a43fd
```

--------------------------- 
##  Команды

```docker ps -a
CONTAINER ID        IMAGE                     COMMAND             CREATED              STATUS              PORTS                    NAMES
3bbd7244c907        actisha/otus-reddit:1.0   "/start.sh"         About a minute ago   Up About a minute   0.0.0.0:9292->9292/tcp   reddit
```
Смотрим логи контейнера:
```
docker logs 3bbd7244c907 -f
```
Зайти в контейнер и запустить bash:
```
docker exec -it reddit bash 

root@3bbd7244c907:/#
```
Смотрим, что в процессах в контейнере:
```
root@3bbd7244c907:/# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  18020  1404 ?        Ss   14:41   0:00 /bin/bash /start.sh
```
Убьем контенер (или же означает, что убьем процесс с PID 1):
```
killall5 1
```
Проверим, что контейнер остановлен:
```
docker ps -a

      STATUS   
      Exited (0) 23 seconds ago  
```
Создание контейнер из образа и проверка того, что он запустился:
```
docker start 3bbd7244c907 && docker ps -a
```
Остановить и удалить, не удаляя образ:
```
docker stop reddit && docker rm reddit
```
Можно писать reddit или 3bbd7244c907.

Запуск контейнера без запуска приложения *docker run --name reddit --rm -it <my-login>/otus-reddit:1.0 bash*:
```
docker run --name reddit --rm -it actisha/otus-reddit:1.0 bash
```
*ps aux* - убедились, что ничто не запустилось.
*exit* - выход из контейнера.

Просмотр сведений об образе:
```
docker inspect actisha/otus-reddit:1.0
```
Просмотр информации об основном процессе в CMD:
```
docker inspect actisha/otus-reddit:1.0 -f '{{.ContainerConfig.Cmd}}'

[/bin/sh -c #(nop)  CMD ["/start.sh"]]
```
Запуск контейнера с приложением из образа на localhost с биндом порта приложения на 9292 localhost-а:
```
docker run --name reddit -d -p 9292:9292 actisha/otus-reddit:1.0 

d35dc0a70ced424edb3f241fe4cdd1a23d1d233d2d9830852c73d98ca002f974
```
Заходим в запущенный контейнер:
```
docker exec -it reddit bash
```
Выполняем создание директории и создание файла в ней:
```
mkdir /test1234 && touch test1234/test.txt
```
Выполняем удаление директории:
```
rmdir /opt 
```
Выходим *exit*.
Выполняем просмотр изменение *docker diff reddit*:
```
C /tmp
A /tmp/mongodb-27017.sock
C /root
A /root/.bash_history
A /test1234
A /test1234/test.txt
C /var
C /var/lib
C /var/lib/mongodb
A /var/lib/mongodb/_tmp
A /var/lib/mongodb/journal
A /var/lib/mongodb/journal/j._0
A /var/lib/mongodb/journal/prealloc.1
A /var/lib/mongodb/journal/prealloc.2
A /var/lib/mongodb/local.0
A /var/lib/mongodb/local.ns
A /var/lib/mongodb/mongod.lock
C /var/log
A /var/log/mongod.log
D /opt
```
Где:
А-изменения
С-создание
D-удаление
Выполним остановку и удаление контейнера:
```
docker stop reddit && docker rm reddit 
```
Повторный запуск контейнера без запуска приложения и проверка, что изменения не сохранились:
```
docker run --name reddit --rm -it actisha/otus-reddit:1.0 bash -c ls
```













