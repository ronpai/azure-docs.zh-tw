---
title: 將資源部署至資源群組
description: 描述如何在 Azure Resource Manager 範本中部署資源。 它會顯示如何將多個資源群組設為目標。
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681464"
---
# <a name="resource-group-deployments-with-arm-templates"></a>使用 ARM 範本的資源群組部署

本文說明如何將部署範圍設為資源群組。 您可以針對部署使用 Azure Resource Manager 範本 (ARM 範本) 。 本文也會示範如何在部署作業中擴充資源群組以外的範圍。

## <a name="supported-resources"></a>支援的資源

大部分資源都可以部署至資源群組。 如需可用資源的清單，請參閱 [ARM 範本參考](/azure/templates)。

## <a name="schema"></a>結構描述

針對範本，請使用下列架構：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

針對參數檔案，請使用：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>部署命令

若要部署至資源群組，請使用資源群組部署命令。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

針對 Azure CLI，請使用 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)。 下列範例會部署範本來建立資源群組：

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

針對 PowerShell 部署命令，請使用 [New->new-azresourcegroupdeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)。 下列範例會部署範本來建立資源群組：

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

如需部署 ARM 範本的部署命令和選項的詳細資訊，請參閱：

* [使用 ARM 範本和 Azure 入口網站部署資源](deploy-portal.md)
* [使用 ARM 範本與 Azure CLI 來部署資源](deploy-cli.md)
* [使用 ARM 範本與 Azure PowerShell 來部署資源](deploy-powershell.md)
* [使用 ARM 範本部署資源，並 Azure Resource Manager REST API](deploy-rest.md)
* [使用部署按鈕從 GitHub 存放庫部署範本](deploy-to-azure-button.md)
* [從 Cloud Shell 部署 ARM 範本](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>部署範圍

部署至資源群組時，您可以將資源部署至：

* 作業中的目標資源群組
* 相同訂用帳戶或其他訂用帳戶中的其他資源群組
* [擴充功能資源](scope-extension-resources.md) 可以套用至資源

部署範本的使用者必須擁有指定範圍的存取權。

本節說明如何指定不同的範圍。 您可以將這些不同的範圍結合在單一範本中。

### <a name="scope-to-target-resource-group"></a>將範圍設定為目標資源群組

若要將資源部署至目標資源，請將這些資源新增至範本的資源區段。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>相同訂用帳戶中的資源群組範圍

若要將資源部署至相同訂用帳戶中的不同資源群組，請新增嵌套部署並包含 `resourceGroup` 屬性。 如未指定訂用帳戶識別碼或資源群組，則會使用父代範本中的訂用帳戶及資源群組。 執行部署之前，所有資源群組都必須存在。

在下列範例中，嵌套部署會以名為的資源群組為目標 `demoResourceGroup` 。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>不同訂用帳戶中的資源群組範圍

若要將資源部署至不同訂用帳戶中的資源群組，請新增嵌套的部署，並包含 `subscriptionId` 和 `resourceGroup` 屬性。 在下列範例中，嵌套部署會以名為的資源群組為目標 `demoResourceGroup` 。

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>跨資源群組

您可以在單一 ARM 範本中部署至一個以上的資源群組。 若要以不同於父代範本的資源群組為目標，請使用[巢狀或連結的範本](linked-templates.md)。 在部署資源類型中，指定將成為巢狀範本部署目標的「訂用帳戶識別碼」和「資源群組」的值。 資源群組可以存在於不同的訂用帳戶中。

> [!NOTE]
> 您可以在單一部署中部署至 **800 個資源群組** 。 一般而言，此限制表示您可以部署至一個指定用於父代範本的資源群組，並且可在巢狀或連結的部署中部署至最多 799 個資源群組。 不過，如果父代範本只包含巢狀或連結的範本，本身未部署任何資源，則您可以在巢狀或連結的部署中包含最多 800 個資源群組。

下列範例會部署兩個儲存體帳戶。 第一個儲存體帳戶會部署到部署作業中指定的資源群組。 第二個儲存體帳戶會部署到 `secondResourceGroup` 和 `secondSubscriptionID` 參數指定的資源群組：

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

如果您將 `resourceGroup` 設定為不存在的資源群組名稱，部署就會失敗。

若要測試上述範本並查看結果，請使用 PowerShell 或 Azure CLI。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要將兩個儲存體帳戶部署至 **相同訂用帳戶** 中的兩個資源群組，請使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

若要將兩個儲存體帳戶部署至 **兩個訂用帳戶** ，請使用：

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要將兩個儲存體帳戶部署至 **相同訂用帳戶** 中的兩個資源群組，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

若要將兩個儲存體帳戶部署至 **兩個訂用帳戶** ，請使用：

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="next-steps"></a>後續步驟

* 如需針對 Azure 資訊安全中心部署工作區設定的範例，請參閱 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)。
