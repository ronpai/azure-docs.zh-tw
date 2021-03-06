---
title: 使用 SQL VM 資源提供者自動註冊
description: 瞭解如何啟用自動註冊功能，以使用 Azure 入口網站來自動向 SQL VM 資源提供者註冊所有過去和未來的 SQL Server Vm。
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.openlocfilehash: b986832e5febbb2a0f88b65213f9acf0dd4c5ab5
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996896"
---
# <a name="automatic-registration-with-sql-vm-resource-provider"></a>使用 SQL VM 資源提供者自動註冊
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

在 Azure 入口網站中啟用自動註冊功能，即可在輕量模式中，使用 SQL VM 資源提供者自動註冊 Azure Vm 上的所有目前和未來的 SQL Server。

本文將指導您啟用自動註冊功能。 或者，您可以 [註冊單一 VM](sql-vm-resource-provider-register.md)，或使用 SQL VM 資源提供者 [大量註冊您的 vm](sql-vm-resource-provider-bulk-register.md) 。 

## <a name="overview"></a>概觀

[SQL VM 資源提供者](sql-vm-resource-provider-register.md#overview)可讓您從 Azure 入口網站管理 SQL Server VM。 此外，資源提供者也啟用了強大的功能集，包括 [自動修補](automated-patching.md)、 [自動備份](automated-backup.md)，以及監視和管理功能。 還會解除鎖定[授權](licensing-model-azure-hybrid-benefit-ahb-change.md)和[版本](change-sql-server-edition.md)彈性。 先前，這些功能僅適用於從 Azure Marketplace 部署的 SQL Server VM 映像。 

自動註冊功能可讓客戶使用 SQL VM 資源提供者，自動註冊其 Azure 訂用帳戶中的所有目前和未來 SQL Server Vm。 這與手動註冊不同，只著重于目前 SQL Server Vm。 

自動註冊會以輕量模式註冊 SQL Server Vm。 您仍然需要以 [手動方式升級為完整的管理模式](sql-vm-resource-provider-register.md#upgrade-to-full) ，以充分利用完整的功能集。 

## <a name="prerequisites"></a>必要條件

若要向資源提供者註冊 SQL Server VM，您將需要： 

- [Azure 訂用帳戶](https://azure.microsoft.com/free/)。
- Azure 資源模型 [Windows 虛擬機器](../../../virtual-machines/windows/quick-create-portal.md) ，其中 [SQL Server](https://www.microsoft.com/sql-server/sql-server-downloads) 部署至公用或 Azure Government 雲端。 


## <a name="enable"></a>啟用

若要在 Azure 入口網站中啟用 SQL Server Vm 的自動註冊，請遵循下列步驟：

1. 登入[Azure 入口網站](https://portal.azure.com)。
1. 流覽至 [ [**SQL 虛擬機器**](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) ] 資源頁面。 
1. 選取 [ **自動 SQL SERVER VM 註冊** ] 以開啟 [ **自動註冊** ] 頁面。 

   :::image type="content" source="media/sql-vm-resource-provider-automatic-registration/automatic-registration.png" alt-text="選取 [自動 SQL Server VM 註冊] 以開啟 [自動註冊] 頁面":::

1. 從下拉式清單中選擇您的訂用帳戶。 
1. 請仔細閱讀條款，如果您同意，請選取 [ **我接受**]。 
1. 選取 [ **註冊** ] 以啟用此功能，並使用 SQL VM 資源提供者自動註冊所有目前和未來的 SQL Server vm。 這不會重新開機任何 Vm 上的 SQL Server 服務。 

## <a name="disable"></a>停用

使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/install-az-ps) 停用自動註冊功能。 當自動註冊功能停用時，新增至訂用帳戶的 SQL Server Vm 必須以手動方式向 SQL VM 資源提供者註冊。 這不會取消註冊現有的 SQL Server 已註冊的 Vm。



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要使用 Azure CLI 停用自動註冊，請執行下列命令： 

```azurecli-interactive
az feature unregister --namespace Microsoft.SqlVirtualMachine --name BulkRegistration
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要使用 Azure PowerShell 停用自動註冊，請執行下列命令： 

```powershell-interactive
Unregister-AzProviderFeature -FeatureName BulkRegistration -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="enable-for-multiple-subscriptions"></a>針對多個訂用帳戶啟用

您可以使用 PowerShell 來啟用多個 Azure 訂用帳戶的自動註冊功能。 

若要這樣做，請依照下列步驟執行：

1. 將 [此腳本](https://github.com/microsoft/tigertoolbox/blob/master/AzureSQLVM/RegisterSubscriptionsToSqlVmAutomaticRegistration.ps1) 儲存至檔案 `.ps1` ，例如 `EnableBySubscription.ps1` 。 
1. 使用系統管理命令提示字元或 PowerShell 視窗，流覽至您儲存腳本的位置。 
1. 連接到 Azure (`az login`) 。
1. 執行腳本，以參數的形式傳入 Subscriptionid，例如   
   `.\EnableBySubscription.ps1 -SubscriptionList SubscriptionId1,SubscriptionId2`

   例如： 

   ```console
   .\EnableBySubscription.ps1 -SubscriptionList a1a1a-aa11-11aa-a1a1-a11a111a1,b2b2b2-bb22-22bb-b2b2-b2b2b2bb
   ```

失敗的註冊錯誤會儲存在與 `RegistrationErrors.csv` 您儲存和執行腳本的相同目錄中 `.ps1` 。 

## <a name="next-steps"></a>接下來的步驟

將您的管理性模式升級為 [full](sql-vm-resource-provider-register.md#upgrade-to-full) ，以利用 SQL VM 資源提供者所提供的完整功能集。 
