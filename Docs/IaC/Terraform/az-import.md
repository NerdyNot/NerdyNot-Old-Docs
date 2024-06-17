# Azure 기존 리소스 가져오기

Terraform을 사용하여 기존에 구성된 Azure 리소스를 가져오는 방법은 'import' 기능을 통해 이루어진다. 이는 이미 존재하는 리소스를 Terraform의 상태 파일에 등록하여 관리할 수 있게 하는 중요한 기능이다. 아래의 단계를 따라 진행하면, 기존 Azure 리소스를 Terraform으로 가져와서 관리할 수 있다.

## 1. Terraform 프로젝트 구성

Terraform 프로젝트를 구성하려면 프로젝트 디렉토리를 설정하고 필요한 파일을 생성해야 한다. 프로젝트는 인프라 정의를 포함하는 하나 이상의 `.tf` 파일로 구성된다.

### 프로젝트 디렉토리 설정

1. **새 디렉토리 생성**:
   새로운 디렉토리를 생성하고 이동하는 것이 필요하다.
   ```bash
   $ mkdir terraform-import-azure
   $ cd terraform-import-azure
   ```

2. **파일 생성**:
   이 프로젝트에서는 `main.tf` 파일을 사용하여 기존 리소스를 정의하고 가져오는 구성 요소를 포함한다.

## 2. `main.tf` 파일 작성

`main.tf` 파일은 기존 Azure 리소스를 정의하고 가져오는 데 사용된다. 각 리소스를 Terraform의 리소스 블록으로 정의하고, 해당 리소스의 ID를 사용하여 Terraform 상태 파일에 가져온다.

### `main.tf` 파일의 예시 내용

아래는 기존 Azure 리소스를 정의하고 가져오기 위한 `main.tf` 파일의 예시이다.

```hcl
# Azure Provider 설정
provider "azurerm" {
  features {}
}

# 기존 리소스 그룹 가져오기
resource "azurerm_resource_group" "example" {
  name     = "example-resource-group"  # 기존 리소스 그룹의 이름
  location = "West Europe"             # 기존 리소스 그룹의 위치
}

# 기존 Virtual Network 가져오기
resource "azurerm_virtual_network" "example" {
  name                = "example-vnet"               # 기존 VNet의 이름
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}

# 기존 Subnet 가져오기
resource "azurerm_subnet" "example" {
  name                 = "example-subnet"            # 기존 서브넷의 이름
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
}

# 기존 가상 머신 가져오기
resource "azurerm_virtual_machine" "example" {
  name                  = "example-vm"               # 기존 VM의 이름
  resource_group_name   = azurerm_resource_group.example.name
  location              = azurerm_resource_group.example.location
}

# 기존 Load Balancer 가져오기
resource "azurerm_lb" "example" {
  name                = "example-lb"                 # 기존 Load Balancer의 이름
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
}
```

## 3. Terraform 상태에 기존 리소스 가져오기

Terraform의 `import` 명령을 사용하여 기존 Azure 리소스를 Terraform 상태 파일로 가져올 수 있다. 이는 Terraform이 기존 리소스의 상태를 추적하고 관리할 수 있게 한다.

### 리소스 가져오기

1. **리소스 ID 찾기**:
   Azure 포털 또는 CLI를 사용하여 가져오려는 리소스의 ID를 찾는다. 예를 들어, `azurerm_virtual_network`의 ID를 찾으려면 Azure CLI를 사용할 수 있다.
   ```bash
   $ az network vnet show --resource-group example-resource-group --name example-vnet --query id
   ```

2. **리소스 가져오기 명령 실행**:
   Terraform CLI를 사용하여 기존 리소스를 상태 파일에 가져온다. 아래 명령어는 `azurerm_virtual_network` 리소스를 Terraform으로 가져온다.
   ```bash
   $ terraform import azurerm_virtual_network.example /subscriptions/{subscription-id}/resourceGroups/example-resource-group/providers/Microsoft.Network/virtualNetworks/example-vnet
   ```

   여기서 `{subscription-id}`는 Azure 구독 ID이고, 리소스 ID는 Azure에서 조회한 실제 리소스의 ID로 대체해야 한다.

3. **다른 리소스도 동일하게 가져오기**:
   위와 같은 방법으로 다른 리소스도 `import` 명령을 사용하여 Terraform 상태 파일에 가져온다. 아래는 몇 가지 추가 예시이다.
   
   - 리소스 그룹 가져오기:
     ```bash
     $ terraform import azurerm_resource_group.example /subscriptions/{subscription-id}/resourceGroups/example-resource-group
     ```

   - 서브넷 가져오기:
     ```bash
     $ terraform import azurerm_subnet.example /subscriptions/{subscription-id}/resourceGroups/example-resource-group/providers/Microsoft.Network/virtualNetworks/example-vnet/subnets/example-subnet
     ```

   - 가상 머신 가져오기:
     ```bash
     $ terraform import azurerm_virtual_machine.example /subscriptions/{subscription-id}/resourceGroups/example-resource-group/providers/Microsoft.Compute/virtualMachines/example-vm
     ```

   - 로드 밸런서 가져오기:
     ```bash
     $ terraform import azurerm_lb.example /subscriptions/{subscription-id}/resourceGroups/example-resource-group/providers/Microsoft.Network/loadBalancers/example-lb
     ```

## 4. Terraform 구성 확인 및 적용

Terraform에 기존 리소스를 성공적으로 가져왔으면, Terraform 구성을 검토하고 필요한 경우 수정할 수 있다. 이후 Terraform의 상태를 최신 상태로 유지하고 리소스 관리를 계속할 수 있다.

### 구성 확인 및 적용

1. **계획 작성**:
   Terraform 계획을 작성하여 가져온 리소스의 상태를 검토하고 Terraform의 구성이 올바른지 확인한다.
   ```bash
   $ terraform plan
   ```

2. **변경 사항 적용**:
   가져온 리소스를 Terraform이 관리하도록 설정하기 위해 `apply` 명령을 실행한다. 이 명령어는 계획에 따라 Terraform 상태 파일을 업데이트한다.
   ```bash
   $ terraform apply
   ```