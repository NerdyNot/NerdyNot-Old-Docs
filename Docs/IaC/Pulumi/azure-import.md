{
 "cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Pulumi를 사용하여 기존 Azure 리소스를 가져오는 방법\n",
    "\n",
    "이 문서는 Pulumi를 사용하여 이미 구성된 Azure 구독의 리소스를 가져오는 방법을 설명한다. 이를 통해 기존의 클라우드 리소스를 Pulumi로 관리할 수 있다."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 사전 준비\n",
    "\n",
    "Pulumi를 사용하여 Azure 리소스를 가져오기 전에 몇 가지 준비 단계가 필요하다."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Pulumi CLI 설치\n",
    "Pulumi CLI는 Pulumi의 핵심 도구이다. 터미널에서 아래 명령어를 실행하면 설치할 수 있다.\n",
    "설치가 완료되면 `pulumi` 명령어를 사용할 수 있게 된다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "!curl -fsSL https://get.pulumi.com | sh"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Azure 계정 설정\n",
    "Azure CLI를 사용하여 Azure 계정에 인증해야 한다. 설치 후 아래 명령어를 사용하여 로그인할 수 있다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "!az login\n",
    "#현재 로그인된 계정을 확인하려면 아래 명령어를 실행한다.\n",
    "!az account show"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Pulumi 프로젝트 생성\n",
    "Pulumi 프로젝트를 생성하여 시작해야 한다. 새로운 디렉토리를 만들고 해당 디렉토리로 이동한 후, `pulumi new` 명령어를 사용하여 프로젝트를 생성한다. 여기서는 Python 프로젝트를 예로 들겠다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "!mkdir pulumi-azure-import\n",
    "!cd pulumi-azure-import\n",
    "!pulumi new azure-python"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Azure 리소스 정보 수집\n",
    "\n",
    "가져오려는 Azure 리소스의 세트를 식별하고, 각 리소스의 ID를 수집해야 한다. Azure CLI를 사용하여 리소스의 ID를 확인할 수 있다. 예를 들어, 특정 리소스 그룹의 모든 리소스를 나열하고 각 리소스의 ID를 출력하려면 다음 명령어를 사용할 수 있다.\n",
    "여기서 `<your-resource-group>`을 실제 리소스 그룹 이름으로 대체한다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "# 이 코드는 특정 리소스 그룹의 모든 리소스를 나열하여 리소스 ID를 확인한다.\n",
    "# 실행하려면 <your-resource-group>을 실제 리소스 그룹 이름으로 대체하세요.\n",
    "!az resource list --resource-group <your-resource-group> --query \"[].{id:id, name:name, type:type}\" -o table"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## Pulumi 프로그램 작성\n",
    "\n",
    "### Pulumi 리소스 구성\n",
    "Pulumi 프로그램 내에서 가져올 리소스를 정의해야 한다. Pulumi는 리소스를 가져오기 위해 `import` 기능을 제공한다. 이를 통해 기존 리소스의 상태를 Pulumi의 관리 하에 두게 된다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "import pulumi\n",
    "import pulumi_azure as azure\n",
    "\n",
    "# 기존 리소스들을 가져오기 위한 ID\n",
    "resource_group_id = \"/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>\"\n",
    "storage_account_id = \"/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>\"\n",
    "vnet_id = \"/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>\"\n",
    "\n",
    "# ResourceGroup 리소스 가져오기\n",
    "resource_group = azure.core.ResourceGroup.get(\"existingResourceGroup\", resource_group_id)\n",
    "\n",
    "# StorageAccount 리소스 가져오기\n",
    "storage_account = azure.storage.Account.get(\"existingStorageAccount\", storage_account_id)\n",
    "\n",
    "# Virtual Network 리소스 가져오기\n",
    "vnet = azure.network.VirtualNetwork.get(\"existingVNet\", vnet_id)\n",
    "\n",
    "# 가져온 리소스의 속성을 Pulumi 프로그램에 내보내기\n",
    "pulumi.export(\"resource_group_name\", resource_group.name)\n",
    "pulumi.export(\"storage_account_name\", storage_account.name)\n",
    "pulumi.export(\"vnet_name\", vnet.name)\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "위 예제에서 `<subscription-id>`, `<resource-group-name>`, `<storage-account-name>`, `<vnet-name>`은 실제 Azure 리소스 정보를 대체해야 한다."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "### Pulumi CLI를 사용하여 리소스 가져오기\n",
    "가져온 리소스를 Pulumi 상태로 추가하기 위해 `pulumi import` 명령어를 사용한다. 예를 들어, Azure 스토리지 계정을 가져오려면 다음과 같이 명령어를 실행한다.\n",
    "`<subscription-id>`, `<resource-group-name>`, `<account-name>`을 실제 값으로 대체한다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "!pulumi import azure:storage/account:Account myStorageAccount /subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<account-name>"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 가져온 리소스 관리\n",
    "Pulumi로 가져온 리소스를 이제 Pulumi 프로그램 내에서 관리할 수 있다. 추가적으로, 가져온 리소스와 새롭게 생성한 리소스를 함께 관리할 수 있다. 아래는 기존의 Azure 스토리지 계정과 새로운 스토리지 컨테이너를 Pulumi로 관리하는 예제이다."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {},
   "outputs": [],
   "source": [
    "import pulumi\n",
    "import pulumi_azure as azure\n",
    "\n",
    "# 기존 스토리지 계정 리소스 가져오기\n",
    "storage_account = azure.storage.Account.get(\"existingStorageAccount\", \"/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<account-name>\")\n",
    "\n",
    "# 새 스토리지 컨테이너 생성\n",
    "container = azure.storage.Container(\"newContainer\",\n",
    "    storage_account_name=storage_account.name,\n",
    "    container_access_type=\"private\")\n",
    "\n",
    "# 리소스 내보내기\n",
    "pulumi.export(\"container_name\", container.name)\n"
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "위 예제에서 `<subscription-id>`, `<resource-group-name>`, `<account-name>`은 실제 Azure 리소스 정보를 대체해야 한다."
   ]
  },
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "## 마무리\n",
    "위 단계를 통해 기존 Azure 리소스를 Pulumi로 가져오고, 이를 Pulumi 프로그램에서 관리할 수 있게 된다. 이를 통해 기존 리소스를 효율적으로 관리하고, 새로운 리소스와 통합하여 사용할 수 있는 유연성을 제공한다.\n",
    "\n",
    "이제 Pulumi를 사용하여 기존 리소스를 가져오고 관리하는 방법에 대해 더 깊이 이해하게 되었을 것이다. 이 방법을 활용하여 클라우드 인프라를 보다 체계적이고 효율적으로 관리할 수 있다.\n",
    "\n",
    "**참고 문서**:\n",
    "- [Pulumi Import Documentation](https://www.pulumi.com/docs/guides/adopting/import/)\n",
    "- [Pulumi Azure Provider Documentation](https://www.pulumi.com/docs/intro/cloud-providers/azure/)\n"
   ]
  }
 ],
 "metadata": {
  "kernelspec": {
   "display_name": "Python 3",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.8.5"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 4
}
