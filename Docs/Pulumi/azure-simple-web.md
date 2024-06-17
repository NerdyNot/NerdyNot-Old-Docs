# Pulumi를 사용하여 Azure에서 간단한 웹 인프라 구성하기

이 실습에서는 Pulumi를 사용하여 Azure에 간단한 웹 애플리케이션 인프라를 구성할 것이다. 이를 위해 Azure Virtual Network(VNet), Subnet, 두 대의 Virtual Machine(VM), 그리고 MySQL 서버를 생성할 것이다. Pulumi를 통해 이러한 리소스를 생성하고 관리하는 방법을 단계별로 설명하겠다.

## 사전 준비

Pulumi를 사용하여 Azure 리소스를 구성하기 전에 필요한 준비 단계가 있다.

### Pulumi CLI 설치

Pulumi CLI는 Pulumi의 핵심 도구이다. 설치는 아래 명령어를 사용하면 된다.

```bash
curl -fsSL https://get.pulumi.com | sh
```

설치가 완료되면 `pulumi` 명령어를 사용할 수 있게 된다.

### Azure CLI 설치 및 로그인

Azure CLI를 설치하고 인증을 완료해야 한다. 아래 명령어를 사용하여 설치 및 로그인할 수 있다.

```bash
az login
```

Azure CLI를 사용하여 현재 로그인된 계정을 확인하려면 아래 명령어를 실행한다.

```bash
az account show
```

### Pulumi 프로젝트 생성

새로운 Pulumi 프로젝트를 생성하여 시작한다. Python을 예로 들어 프로젝트를 생성한다.

```bash
mkdir pulumi-azure-web
cd pulumi-azure-web
pulumi new azure-python
```

이 명령어는 새로운 Pulumi 프로젝트를 초기화하고, 기본적인 프로젝트 구조를 생성한다.

## 리소스 생성

Pulumi를 사용하여 Azure 인프라를 구성하는 단계는 다음과 같다.

### Virtual Network 및 Subnet 생성

웹 애플리케이션을 위한 네트워크를 구성하기 위해 Azure Virtual Network와 Subnet을 생성한다.

```python
import pulumi
import pulumi_azure as azure

# 리소스 그룹 생성
resource_group = azure.core.ResourceGroup("web-rg")

# Virtual Network 생성
vnet = azure.network.VirtualNetwork("web-vnet",
    resource_group_name=resource_group.name,
    address_spaces=["10.0.0.0/16"])

# Subnet 생성
subnet = azure.network.Subnet("web-subnet",
    resource_group_name=resource_group.name,
    virtual_network_name=vnet.name,
    address_prefixes=["10.0.1.0/24"])
```

### Virtual Machines 생성

웹 서버로 사용할 두 대의 Virtual Machine을 생성한다. SSH 키를 사용하여 VM에 접근할 수 있도록 설정한다.

```python
# SSH 키 생성 (실제 키를 사용해야 함)
public_key = """
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC7E...example-key... your-email@example.com
"""

# 네트워크 인터페이스 생성
nic1 = azure.network.NetworkInterface("web-nic1",
    resource_group_name=resource_group.name,
    ip_configurations=[{
        "name": "internal",
        "subnet_id": subnet.id,
        "private_ip_address_allocation": "Dynamic"
    }])

nic2 = azure.network.NetworkInterface("web-nic2",
    resource_group_name=resource_group.name,
    ip_configurations=[{
        "name": "internal",
        "subnet_id": subnet.id,
        "private_ip_address_allocation": "Dynamic"
    }])

# 첫 번째 Virtual Machine 생성
vm1 = azure.compute.VirtualMachine("web-vm1",
    resource_group_name=resource_group.name,
    network_interface_ids=[nic1.id],
    vm_size="Standard_B1s",
    delete_data_disks_on_termination=True,
    delete_os_disk_on_termination=True,
    os_profile={
        "computer_name": "webvm1",
        "admin_username": "adminuser",
        "admin_ssh_key": {
            "key_data": public_key,
        }
    },
    os_profile_linux_config={
        "disable_password_authentication": True,
    },
    storage_os_disk={
        "create_option": "FromImage",
        "name": "webvm1osdisk"
    },
    storage_image_reference={
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "18.04-LTS",
        "version": "latest"
    })

# 두 번째 Virtual Machine 생성
vm2 = azure.compute.VirtualMachine("web-vm2",
    resource_group_name=resource_group.name,
    network_interface_ids=[nic2.id],
    vm_size="Standard_B1s",
    delete_data_disks_on_termination=True,
    delete_os_disk_on_termination=True,
    os_profile={
        "computer_name": "webvm2",
        "admin_username": "adminuser",
        "admin_ssh_key": {
            "key_data": public_key,
        }
    },
    os_profile_linux_config={
        "disable_password_authentication": True,
    },
    storage_os_disk={
        "create_option": "FromImage",
        "name": "webvm2osdisk"
    },
    storage_image_reference={
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "18.04-LTS",
        "version": "latest"
    })
```

### MySQL 서버 생성

웹 애플리케이션의 데이터베이스로 사용할 MySQL 서버를 생성한다.

```python
# MySQL 서버 생성
mysql_server = azure.mysql.Server("web-mysql",
    resource_group_name=resource_group.name,
    location=resource_group.location,
    administrator_login="mysqladmin",
    administrator_login_password="PulumiAdmin123!",
    sku={
        "name": "B_Gen5_1",
        "tier": "Basic",
        "capacity": 1,
        "family": "Gen5"
    },
    storage_profile={
        "storage_mb": 5120,
        "backup_retention_days": 7,
        "geo_redundant_backup": "Disabled"
    },
    version="5.7")

# MySQL 데이터베이스 생성
mysql_database = azure.mysql.Database("webdb",
    resource_group_name=resource_group.name,
    server_name=mysql_server.name,
    charset="utf8",
    collation="utf8_general_ci")
```

### 리소스 내보내기

생성된 모든 리소스를 Pulumi에서 내보내어 이후의 참조나 사용에 대비한다.

```python
# 리소스 내보내기
pulumi.export("resource_group_name", resource_group.name)
pulumi.export("vnet_name", vnet.name)
pulumi.export("subnet_name", subnet.name)
pulumi.export("vm1_name", vm1.name)
pulumi.export("vm1_private_ip", nic1.private_ip_address)
pulumi.export("vm2_name", vm2.name)
pulumi.export("vm2_private_ip", nic2.private_ip_address)
pulumi.export("mysql_server_name", mysql_server.name)
```

## Pulumi 명령어 실행

위의 모든 Python 코드를 작성한 후, 아래 명령어를 사용하여 Pulumi 프로그램을 실행하고 리소스를 Azure에 배포한다.

### Pulumi 스택 초기화

```bash
pulumi stack init dev
```

이 명령어는 Pulumi 스택을 초기화한다.

### Azure 자격 증명 설정

Azure CLI를 사용하여 로그인하고 현재 계정과 구독을 Pulumi에 적용한다.

```bash
az login
az account set --subscription "<your-subscription-id>"
```

이 명령어를 통해 Azure에 인증하고, 사용할 구독을 설정할 수 있다.

### Pulumi 프로그램 실행

Pulumi 프로그램을 실행하여 리소스를 생성한다.

```bash
pulumi up
```

이 명령어를 실행하면 Pulumi가 리소스를 구성하고 배포할 것이다.