# Pulumi를 사용하여 기존 Azure 리소스를 가져오는 방법

이 문서는 Pulumi를 사용하여 이미 구성된 Azure 구독의 리소스를 가져오는 방법을 설명한다. 이를 통해 기존의 클라우드 리소스를 Pulumi로 관리할 수 있다.

## 사전 준비

Pulumi를 사용하여 Azure 리소스를 가져오기 전에 몇 가지 준비 단계가 필요하다.

### Pulumi CLI 설치

Pulumi CLI는 Pulumi의 핵심 도구이다. 터미널에서 아래 명령어를 실행하면 설치할 수 있다.

```bash
curl -fsSL https://get.pulumi.com | sh
```

설치가 완료되면 `pulumi` 명령어를 사용할 수 있게 된다.

### Azure 계정 설정

Azure CLI를 사용하여 Azure 계정에 인증해야 한다. 설치 후 아래 명령어를 사용하여 로그인할 수 있다.

```bash
az login
```

현재 로그인된 계정을 확인하려면 아래 명령어를 실행한다.

```bash
az account show
```

### Pulumi 프로젝트 생성

Pulumi 프로젝트를 생성하여 시작해야 한다. 새로운 디렉토리를 만들고 해당 디렉토리로 이동한 후, `pulumi new` 명령어를 사용하여 프로젝트를 생성한다. 여기서는 Python 프로젝트를 예로 들겠다.

```bash
mkdir pulumi-azure-import
cd pulumi-azure-import
pulumi new azure-python
```

## Azure 리소스 정보 수집

가져오려는 Azure 리소스의 세트를 식별하고, 각 리소스의 ID를 수집해야 한다. Azure CLI를 사용하여 리소스의 ID를 확인할 수 있다. 예를 들어, 특정 리소스 그룹의 모든 리소스를 나열하고 각 리소스의 ID를 출력하려면 다음 명령어를 사용할 수 있다.

```bash
az resource list --resource-group <your-resource-group> --query "[].{id:id, name:name, type:type}" -o table
```

여기서 `<your-resource-group>`을 실제 리소스 그룹 이름으로 대체한다.

```bash
# 이 코드는 특정 리소스 그룹의 모든 리소스를 나열하여 리소스 ID를 확인한다.
# 실행하려면 <your-resource-group>을 실제 리소스 그룹 이름으로 대체하세요.
!az resource list --resource-group <your-resource-group> --query "[].{id:id, name:name, type:type}" -o table
```

## Pulumi 프로그램 작성

### Pulumi 리소스 구성

Pulumi 프로그램 내에서 가져올 리소스를 정의해야 한다. Pulumi는 리소스를 가져오기 위해 `import` 기능을 제공한다. 이를 통해 기존 리소스의 상태를 Pulumi의 관리 하에 두게 된다.

```python
import pulumi
import pulumi_azure as azure

# 기존 리소스들을 가져오기 위한 ID
resource_group_id = "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>"
storage_account_id = "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
vnet_id = "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"

# ResourceGroup 리소스 가져오기
resource_group = azure.core.ResourceGroup.get("existingResourceGroup", resource_group_id)

# StorageAccount 리소스 가져오기
storage_account = azure.storage.Account.get("existingStorageAccount", storage_account_id)

# Virtual Network 리소스 가져오기
vnet = azure.network.VirtualNetwork.get("existingVNet", vnet_id)

# 가져온 리소스의 속성을 Pulumi 프로그램에 내보내기
pulumi.export("resource_group_name", resource_group.name)
pulumi.export("storage_account_name", storage_account.name)
pulumi.export("vnet_name", vnet.name)
```

위 예제에서 `<subscription-id>`, `<resource-group-name>`, `<storage-account-name>`, `<vnet-name>`은 실제 Azure 리소스 정보를 대체해야 한다.

### Pulumi CLI를 사용하여 리소스 가져오기

가져온 리소스를 Pulumi 상태로 추가하기 위해 `pulumi import` 명령어를 사용한다. 예를 들어, Azure 스토리지 계정을 가져오려면 다음과 같이 명령어를 실행한다.

```bash
pulumi import azure:storage/account:Account myStorageAccount /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<account-name>
```

위 명령어에서 `<subscription-id>`, `<resource-group-name>`, `<account-name>`을 실제 값으로 대체한다.

## 가져온 리소스 관리

Pulumi로 가져온 리소스를 이제 Pulumi 프로그램 내에서 관리할 수 있다. 추가적으로, 가져온 리소스와 새롭게 생성한 리소스를 함께 관리할 수 있다. 아래는 기존의 Azure 스토리지 계정과 새로운 스토리지 컨테이너를 Pulumi로 관리하는 예제이다.

```python
import pulumi
import pulumi_azure as azure

# 기존 스토리지 계정 리소스 가져오기
storage_account = azure.storage.Account.get("existingStorageAccount", "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<account-name>")

# 새 스토리지 컨테이너 생성
container = azure.storage.Container("newContainer",
    storage_account_name=storage_account.name,
    container_access_type="private")

# 리소스 내보내기
pulumi.export("container_name", container.name)
```

위 예제에서 `<subscription-id>`, `<resource-group-name>`, `<account-name>`은 실제 Azure 리소스 정보를 대체해야 한다.

**참고 문서**:
- [Pulumi Import Documentation](https://www.pulumi.com/docs/guides/adopting/import/)
- [Pulumi Azure Provider Documentation](https://www.pulumi.com/docs/intro/cloud-providers/azure/)
