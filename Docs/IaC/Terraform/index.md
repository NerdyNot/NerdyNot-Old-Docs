Terraform을 사용하여 Azure에서 Virtual Network, Subnet, Virtual Machine, Load Balancer를 구성하는 예제는 아래와 같습니다. 이 예제는 Terraform HCL(HashiCorp Configuration Language)을 사용하여 Azure 인프라를 선언적으로 정의하고 프로비저닝하는 과정을 보여줍니다.

### Terraform 설정 파일

#### 1. `main.tf` - 리소스 정의

```hcl
# Azure Provider 설정
provider "azurerm" {
  features {}
}

# 리소스 그룹 생성
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

# 가상 네트워크 생성
resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"
  address_space       = ["10.0.0.0/16"]
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
}

# 서브넷 생성
resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
}

# 공용 IP 주소 생성
resource "azurerm_public_ip" "example" {
  name                = "example-pip"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  allocation_method   = "Static"
  domain_name_label   = "example-loadbalancer"

  sku {
    name = "Standard"
  }
}

# 네트워크 보안 그룹 생성
resource "azurerm_network_security_group" "example" {
  name                = "example-nsg"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  security_rule {
    name                       = "Allow-HTTP-Inbound"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range          = "*"
    destination_port_range     = "80"
    source_address_prefix      = "*"
    destination_address_prefix = "10.0.1.0/24"
  }
}

# 네트워크 인터페이스 생성
resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}

# 가상 머신 생성
resource "azurerm_linux_virtual_machine" "example" {
  name                  = "example-vm"
  resource_group_name   = azurerm_resource_group.example.name
  location              = azurerm_resource_group.example.location
  size                  = "Standard_B1s"
  admin_username        = var.admin_username
  admin_password        = var.admin_password
  network_interface_ids = [azurerm_network_interface.example.id]

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
    disk_size_gb         = 30
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }

  custom_data = base64encode("#!/bin/bash\n echo 'Hello, World!' > index.html\n nohup python3 -m http.server 80 &")
}

# 로드 밸런서 생성
resource "azurerm_lb" "example" {
  name                = "example-lb"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  sku                 = "Standard"

  frontend_ip_configuration {
    name                 = "example-frontend"
    public_ip_address_id = azurerm_public_ip.example.id
  }
}

# 백엔드 주소 풀 생성
resource "azurerm_lb_backend_address_pool" "example" {
  name                = "example-backend"
  loadbalancer_id     = azurerm_lb.example.id
  resource_group_name = azurerm_resource_group.example.name
}

# 로드 밸런서 프로브 생성
resource "azurerm_lb_probe" "example" {
  name                = "example-probe"
  resource_group_name = azurerm_resource_group.example.name
  loadbalancer_id     = azurerm_lb.example.id
  protocol            = "Http"
  port                = 80
  request_path        = "/"
  interval_in_seconds = 5
  number_of_probes    = 2
}

# 로드 밸런서 규칙 생성
resource "azurerm_lb_rule" "example" {
  name                             = "example-rule"
  resource_group_name              = azurerm_resource_group.example.name
  loadbalancer_id                  = azurerm_lb.example.id
  frontend_ip_configuration_name   = "example-frontend"
  backend_address_pool_id          = azurerm_lb_backend_address_pool.example.id
  protocol                         = "Tcp"
  frontend_port                    = 80
  backend_port                     = 80
  idle_timeout_in_minutes          = 4
  enable_floating_ip               = false
  load_distribution                = "Default"
  probe_id                         = azurerm_lb_probe.example.id
}
```

#### 2. `variables.tf` - 변수 정의

```hcl
variable "admin_username" {
  description = "The admin username for the VM"
  type        = string
}

variable "admin_password" {
  description = "The admin password for the VM"
  type        = string
  sensitive   = true
}
```

#### 3. `outputs.tf` - 출력 정의

```hcl
output "public_ip_address" {
  description = "The public IP address of the load balancer"
  value       = azurerm_public_ip.example.ip_address
}

output "fqdn" {
  description = "The fully qualified domain name of the load balancer"
  value       = azurerm_public_ip.example.fqdn
}
```

### 실행 방법

1. **Terraform 설치**:
   
   Terraform을 설치하려면 공식 웹사이트의 [설치 가이드](https://learn.hashicorp.com/tutorials/terraform/install-cli)를 참조하면 된다.

2. **Terraform 초기화**:
   
   프로젝트 디렉토리에서 다음 명령어를 실행하여 Terraform을 초기화한다:
   
   ```bash
   terraform init
   ```

3. **Terraform 계획**:
   
   변경 사항을 미리 보고 검토하려면 다음 명령어를 실행한다:
   
   ```bash
   terraform plan
   ```

4. **Terraform 적용**:
   
   변경 사항을 실제로 적용하고 인프라를 프로비저닝하려면 다음 명령어를 실행한다:
   
   ```bash
   terraform apply
   ```

   이 명령어를 실행하면 Terraform이 인프라를 배포한다. 프롬프트에서 확인을 요청할 경우 'yes'를 입력한다.

5. **결과 확인**:
   
   `outputs.tf` 파일에 정의된 출력 값을 확인하려면 다음 명령어를 실행한다:
   
   ```bash
   terraform output
   ```

6. **자원 제거**:
   
   생성된 모든 자원을 제거하려면 다음 명령어를 실행한다:
   
   ```bash
   terraform destroy
   ```

   이 명령어를 실행하면 Terraform이 배포된 리소스를 삭제한다. 프롬프트에서 확인을 요청할 경우 'yes'를 입력한다.

이 예제는 Azure에서 Virtual Network, Subnet, Virtual Machine 및 Load Balancer를 설정하고 관리하는 방법을 보여준다. Terraform을 사용하면 인프라를 선언적으로 정의하고 관리할 수 있어, 인프라의 배포 및 관리가 더욱 효율적이다.