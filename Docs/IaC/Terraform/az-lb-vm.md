# Terraform을 활용한 Azure 인프라 구성

이 문서에서는 Terraform을 사용하여 Azure에서 Virtual Network, Subnet, Virtual Machine, Load Balancer를 구성하는 방법을 단계별로 안내한다.

## 1. Terraform 설치

Terraform을 사용하려면 먼저 Terraform CLI를 설치해야 한다.

### 설치 방법

1. **Terraform 다운로드**:
   HashiCorp의 공식 웹사이트에서 Terraform 설치 파일을 다운로드한다. 운영 체제에 맞는 버전을 선택하여 설치 파일을 다운로드한다.

   ```bash
   $ curl -fsSL https://releases.hashicorp.com/terraform/1.3.7/terraform_1.3.7_linux_amd64.zip -o terraform.zip
   ```

2. **압축 해제**:
   다운로드한 파일의 압축을 해제한다.

   ```bash
   $ unzip terraform.zip
   ```

3. **실행 파일 이동**:
   Terraform 실행 파일을 시스템의 `PATH`에 포함된 디렉토리로 이동한다.

   ```bash
   $ sudo mv terraform /usr/local/bin/
   ```

4. **설치 확인**:
   Terraform 설치가 완료되었는지 확인하려면 다음 명령어를 실행한다.

   ```bash
   $ terraform -v
   ```

   이 명령어는 설치된 Terraform의 버전을 출력할 것이다.

## 2. 프로젝트 구성

Terraform 프로젝트를 구성하려면 프로젝트 디렉토리를 설정하고 필요한 파일을 생성해야 한다. 프로젝트는 인프라 정의를 포함하는 하나 이상의 `.tf` 파일로 구성된다.

### 프로젝트 디렉토리 설정

1. **새 디렉토리 생성**:
   프로젝트를 위한 새 디렉토리를 생성하고 이동한다.

   ```bash
   $ mkdir terraform-azure-project
   $ cd terraform-azure-project
   ```

2. **파일 생성**:
   이 프로젝트에서는 `main.tf`, `variables.tf`, `outputs.tf`의 세 가지 주요 파일을 사용한다. 각각의 파일은 특정한 목적을 가진다.

   - `main.tf`: Azure 리소스를 정의하는 주요 구성 파일이다.
   - `variables.tf`: 변수 선언을 위한 파일이다.
   - `outputs.tf`: 출력 값을 정의하는 파일이다.

## 3. `main.tf` 파일 작성

`main.tf` 파일은 Azure에서 가상 네트워크, 서브넷, 가상 머신 및 로드 밸런서를 정의하는 주요 구성 파일이다. 각 리소스를 정의하고 관련 설정을 포함한다.

### `main.tf` 파일의 내용

아래는 `main.tf` 파일의 예시 내용이다.

```hcl
# Azure Provider 설정
provider "azurerm" {
  features {}
}

# 리소스 그룹 생성
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "Korea Cental"
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

## 4. `variables.tf` 파일 작성

`variables.tf` 파일은 프로젝트에 사용될 변수들을 정의한다. 이는 구성 요소의 재사용성과 유연성을 높인다.

### `variables.tf` 파일의 내용

아래는 `variables.tf` 파일의 예시 내용이다.

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

## 5. `outputs.tf` 파일 작성

`outputs.tf` 파일은 인프라 구성의 결과를 출력한다. 이 파일은 배포 후 확인할 수 있는 중요한 정보를 제공한다.

### `outputs.tf` 파일의 내용

아래는 `outputs.tf` 파일의 예시 내용이다.

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

## 6. Terraform으로 인프라 배포

이제 모든 파일이 준비되었으므로 Terraform을 사용하여 Azure에 인프라를 배포할 수 있다. 이 단계에서는

 Terraform 명령어를 사용하여 인프라를 프로비저닝하고 관리한다.

### 배포 과정

1. **Terraform 초기화**:
   Terraform을 사용하기 전에 프로젝트 디렉토리에서 Terraform을 초기화해야 한다. 이 명령어는 필요한 플러그인 및 모듈을 다운로드하고 설정한다.

   ```bash
   $ terraform init
   ```

2. **계획 작성**:
   인프라 변경 사항을 계획하고 검토하려면 다음 명령어를 사용한다. 이 명령어는 현재 상태와의 차이를 계산하고 적용할 변경 사항을 표시한다.

   ```bash
   $ terraform plan
   ```

3. **변경 사항 적용**:
   인프라를 실제로 배포하려면 다음 명령어를 사용한다. 이 명령어는 Terraform 계획에 따라 리소스를 생성하고 변경 사항을 적용한다.

   ```bash
   $ terraform apply
   ```

   명령어 실행 후 변경 사항을 확인하고 적용할지 묻는 프롬프트가 나타난다. 'yes'를 입력하여 적용을 확정한다.

4. **결과 확인**:
   배포가 완료된 후, `outputs.tf` 파일에 정의된 출력 값을 확인하려면 다음 명령어를 실행한다.

   ```bash
   $ terraform output
   ```

5. **자원 제거**:
   생성된 모든 리소스를 제거하려면 다음 명령어를 사용한다. 이 명령어는 Terraform 상태 파일을 기준으로 모든 자원을 삭제한다.

   ```bash
   $ terraform destroy
   ```

   자원 삭제를 확인하는 프롬프트가 나타나면 'yes'를 입력하여 삭제를 확정한다.