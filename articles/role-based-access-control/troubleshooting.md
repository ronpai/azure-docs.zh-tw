---
title: 針對 Azure RBAC 進行疑難排解
description: 針對 Azure 角色型存取控制 (Azure RBAC) 的問題進行疑難排解。
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 069c290de0278202b2e20d67f0ce792a0a79c345
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368225"
---
# <a name="troubleshoot-azure-rbac"></a>針對 Azure RBAC 進行疑難排解

本文將回答一些有關 Azure 角色型存取控制 (Azure RBAC) 的常見問題，讓您知道使用這些角色時所預期的內容，並可針對存取問題進行疑難排解。

## <a name="azure-role-assignments-limit"></a>Azure 角色指派限制

Azure 支援每個訂用帳戶最多 **2000** 個角色指派。 此限制包括訂用帳戶、資源群組和資源範圍的角色指派。 如果您收到錯誤訊息「無法建立更多角色指派 (程式碼： RoleAssignmentLimitExceeded) 」當您嘗試指派角色時，請嘗試減少訂用帳戶中的角色指派數目。

> [!NOTE]
> 每個訂用帳戶的 **2000** 角色指派限制是固定的，而且無法增加。

如果您接近這項限制，以下是您可以減少角色指派數目的一些方法：

- 將使用者新增至群組，並改為將角色指派給群組。 
- 結合多個內建角色與自訂角色。 
- 在較高的範圍（例如訂用帳戶或管理群組）上進行一般角色指派。
- 如果您有 Azure AD Premium P2，請在 [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/pim-configure.md) 中提供角色指派的資格，而不是永久指派。 
- 新增其他訂用帳戶。 

若要取得角色指派的數目，您可以在 Azure 入口網站中 [的 [存取控制] (IAM) ] 頁面上查看圖表](role-assignments-list-portal.md#list-number-of-role-assignments) 。 您也可以使用下列 Azure PowerShell 命令：

```azurepowershell
$scope = "/subscriptions/<subscriptionId>"
$ras = Get-AzRoleAssignment -Scope $scope | Where-Object {$_.scope.StartsWith($scope)}
$ras.Count
```

## <a name="problems-with-azure-role-assignments"></a>Azure 角色指派的問題

- 如果您因為 [**新增**新增角色指派] 選項已停用，或因為您收到許可權錯誤「具有物件識別碼的用戶端沒有執行動作的授權」，所以無法在存取控制的 Azure 入口網站中新增角色指派** (IAM) ** 。確認  >  **Add role assignment**您目前登入的使用者，具有在 `Microsoft.Authorization/roleAssignments/write` 您嘗試指派角色的範圍內，具有擁有者[Owner](built-in-roles.md#owner)或[使用者存取系統管理員](built-in-roles.md#user-access-administrator)許可權的角色。
- 如果您使用服務主體來指派角色，您可能會收到「許可權不足，無法完成作業」錯誤。 例如，假設您的服務主體已獲指派擁有者角色，而您嘗試使用 Azure CLI 建立下列角色指派作為服務主體：

    ```azurecli
    az login --service-principal --username "SPNid" --password "password" --tenant "tenantid"
    az role assignment create --assignee "userupn" --role "Contributor"  --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

    如果您收到「許可權不足，無法完成作業」錯誤，可能是因為 Azure CLI 嘗試查閱 Azure AD 中的「受託者」身分識別，而服務主體預設無法讀取 Azure AD。

    有兩種方式可以解決此錯誤。 第一種方式是將 [目錄讀取](../active-directory/roles/permissions-reference.md#directory-readers) 者角色指派給服務主體，讓它可以讀取目錄中的資料。

    解決這個錯誤的第二個方法是使用參數而非來建立角色指派 `--assignee-object-id` `--assignee` 。 藉由使用 `--assignee-object-id` ，Azure CLI 將會略過 Azure AD 查閱。 您將需要取得您想要指派角色之使用者、群組或應用程式的物件識別碼。 如需詳細資訊，請參閱 [使用 Azure CLI 新增或移除 Azure 角色指派](role-assignments-cli.md#add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope)。

    ```azurecli
    az role assignment create --assignee-object-id 11111111-1111-1111-1111-111111111111  --role "Contributor" --scope "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}"
    ```

## <a name="problems-with-custom-roles"></a>自訂角色的問題

- 如果您需要如何建立自訂角色的步驟，請參閱使用 [Azure 入口網站](custom-roles-portal.md) (目前處於預覽狀態的自訂角色教學課程) 、 [Azure PowerShell](tutorial-custom-role-powershell.md)或 [Azure CLI](tutorial-custom-role-cli.md)。
- 如果您無法更新現有的自訂角色，請確認您目前登入的使用者已獲指派具有「 `Microsoft.Authorization/roleDefinition/write` [擁有](built-in-roles.md#owner) 者」或「 [使用者存取系統管理員](built-in-roles.md#user-access-administrator)」許可權的角色。
- 如果您無法刪除自訂角色並收到錯誤訊息「有參考角色的現有角色指派 (代碼：RoleDefinitionHasAssignments)」，則有仍在使用自訂角色的角色指派。 請移除這些角色指派，並試著再次刪除自訂角色。
- 如果您收到「已超過角色定義限制」錯誤訊息。 當您嘗試建立新的自訂角色時，無法建立更多角色定義 (程式碼： RoleDefinitionLimitExceeded) 」，請刪除任何未使用的自訂角色。 Azure 支援目錄中最多 **5000** 個自訂角色。 Azure 德國和 Azure 中國世紀的 (，限制為2000個自訂角色。 ) 
- 如果您收到類似于「用戶端有權在範圍 '/subscriptions/{subscriptionid} ' 執行動作 ' roleDefinitions/write '」的錯誤，但找不到連結的訂用帳戶」，請檢查是否已在目錄中刪除一個或多個可指派的 [範圍](role-definitions.md#assignablescopes) 。 如果範圍已刪除，則可以建立支援票證，因為此時已沒有可用的自助解決方案。

## <a name="custom-roles-and-management-groups"></a>自訂角色和管理群組

- 您只能在自訂角色中定義一個管理群組 `AssignableScopes` 。 將管理群組新增至的 `AssignableScopes` 功能目前為預覽狀態。
- `DataActions`無法在管理群組範圍指派具有的自訂角色。
- Azure Resource Manager 不會驗證管理群組是否存在角色定義的可指派範圍中。
- 如需自訂角色和管理群組的詳細資訊，請參閱 [使用 Azure 管理群組來組織您的資源](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)。

## <a name="transferring-a-subscription-to-a-different-directory"></a>將訂用帳戶轉移至不同的目錄

- 如果您需要如何將訂用帳戶轉移至不同 Azure AD 目錄的步驟，請參閱將 [Azure 訂用帳戶轉移至不同的 Azure AD 目錄](transfer-subscription.md)。
- 如果您將訂用帳戶轉移至不同的 Azure AD 目錄，所有角色指派都會從來源 Azure AD 目錄中 **永久** 刪除，而且不會遷移至目標 Azure AD 目錄。 您必須在目標目錄中重新建立角色指派。 您也必須手動重新建立適用于 Azure 資源的受控識別。 如需詳細資訊，請參閱 [受控識別的常見問題和已知問題](../active-directory/managed-identities-azure-resources/known-issues.md)。
- 如果您是 Azure AD 的全域系統管理員，而且在目錄之間傳送訂用帳戶之後，您沒有訂用帳戶的存取權，請使用 **Azure 資源的存取管理** 切換，暫時 [提高存取](elevate-access-global-admin.md) 權以取得訂用帳戶的存取權。

## <a name="issues-with-service-admins-or-co-admins"></a>服務管理員或共同管理員的問題

- 如果您有服務管理員或共同管理員的問題，請參閱 [新增或變更 azure 訂](../cost-management-billing/manage/add-change-subscription-administrator.md) 用 [帳戶系統管理員和傳統訂用帳戶管理員角色、azure 角色和 Azure AD 角色](rbac-and-directory-admin-roles.md)。

## <a name="access-denied-or-permission-errors"></a>拒絕存取或許可權錯誤

- 如果在嘗試建立資源時收到權限錯誤「具有物件識別碼的用戶端沒有在範圍中執行動作的權限 (錯誤碼：AuthorizationFailed)」，請檢查您目前用於登入的使用者是否獲派對所選範圍內的資源具有寫入權限的角色。 例如，若要管理資源群組中的虛擬機器，您應該具有資源群組 (或父範圍) 的[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色。 如需每個內建角色的許可權清單，請參閱 [Azure 內建角色](built-in-roles.md)。
- 如果您在嘗試建立或更新支援票證時收到許可權錯誤「您沒有建立支援要求的許可權」，請確認您目前登入的使用者已獲指派具有許可權的角色 `Microsoft.Support/supportTickets/write` ，例如「 [支援要求參與者](built-in-roles.md#support-request-contributor)」。

## <a name="move-resources-with-role-assignments"></a>使用角色指派來移動資源

如果您將具有直接指派 Azure 角色的資源移至資源 (或子資源) ，則不會移動角色指派，也不會變成孤立的角色指派。 移動之後，您必須重新建立角色指派。 最後，系統會自動移除孤立的角色指派，但在移動資源之前移除角色指派是最佳做法。

如需如何移動資源的詳細資訊，請參閱 [將資源移至新的資源群組或訂用](../azure-resource-manager/management/move-resource-group-and-subscription.md)帳戶。

## <a name="role-assignments-with-identity-not-found"></a>找不到身分識別的角色指派

在 Azure 入口網站的角色指派清單中，您可能會注意到安全性主體 ([使用者]、[群組]、[服務主體] 或 [受控識別]) 列為 [ **找不到** 具有 **未知** 類型的身分識別]。

![Azure 角色指派中找不到身分識別](./media/troubleshooting/unknown-security-principal.png)

可能找不到身分識別的原因有二：

- 您最近在建立角色指派時，邀請了使用者
- 您已刪除具有角色指派的安全性主體

如果您最近在建立角色指派時邀請使用者，此安全性主體可能仍在跨區域的複寫程式中。 如果有，請稍候片刻，並重新整理角色指派清單。

但是，如果此安全性主體並非最近受邀的使用者，則可能是已刪除的安全性主體。 如果您將角色指派給安全性主體，然後在不先移除角色指派的情況下刪除該安全性主體，則會將安全性主體列為 **找不** 到身分識別和 **未知** 的類型。

如果您使用 Azure PowerShell 列出此角色指派，您可能會看到空白 `DisplayName` 且已 `ObjectType` 設定為 **未知**。 例如， [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment) 會傳回類似下列輸出的角色指派：

```
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

同樣地，如果您使用 Azure CLI 列出此角色指派，您可能會看到空白 `principalName` 。 例如， [az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list) 會傳回類似下列輸出的角色指派：

```
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

離開安全性主體已刪除的角色指派並不會造成問題。 如果您想要的話，可以使用與其他角色指派類似的步驟來移除這些角色指派。 如需有關如何移除角色指派的詳細資訊，請參閱 [Azure 入口網站](role-assignments-portal.md#remove-a-role-assignment)、 [Azure PowerShell](role-assignments-powershell.md#remove-a-role-assignment)或 [Azure CLI](role-assignments-cli.md#remove-role-assignment)

在 PowerShell 中，如果您嘗試使用物件識別碼和角色定義名稱移除角色指派，且有一個以上的角色指派符合您的參數，您將會收到錯誤訊息：「提供的資訊未對應至角色指派」。 下列輸出顯示錯誤訊息的範例：

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

如果您收到這個錯誤訊息，請確定您也指定了 `-Scope` 或 `-ResourceGroupName` 參數。

```
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="role-assignment-changes-are-not-being-detected"></a>未偵測到角色指派變更

Azure Resource Manager 有時候會快取組態和資料來改善效能。 當您新增或移除角色指派時，最多可能需要30分鐘的時間，變更才會生效。 如果您使用 Azure 入口網站、Azure PowerShell 或 Azure CLI，您可以藉由登出再登入，來強制重新整理角色指派變更。 如果您使用 REST API 呼叫來變更角色指派，您可以重新整理存取權杖來強制重新整理。

如果您要新增或移除管理群組範圍的角色指派，且該角色具有 `DataActions` ，則可能會有數小時無法更新資料平面上的存取權。 這只適用于管理群組範圍和資料平面。

## <a name="web-app-features-that-require-write-access"></a>需要寫入存取權的 Web 應用程式功能

如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。 下列管理功能需要 web 應用程式的 **寫入** 許可權 (參與者或擁有者) ，而且無法在任何唯讀情況中使用。

* 命令 (像是開始、停止等)
* 變更像是一般組態的設定、調整設定、備份設定與監視設定
* 存取發佈認證與其他機密，像是應用程式設定與連接字串
* 串流記錄
* 資源記錄檔設定
* 主控台 (命令提示字元)
* 有效的近期部署項目 (以便本機 git 持續部署)
* 預估的花費
* Web 測試
* 虛擬網路 (只有當虛擬網路先前是由具備寫入存取權限的使用者所設定，才會顯示出來)。

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得 Web 應用程式的參與者存取權限。

## <a name="web-app-resources-that-require-write-access"></a>需要寫入存取權的 Web 應用程式資源

Web 應用程式因為幾個互有關聯的資源而顯得複雜。 以下是具有多個網站的典型資源群組：

![Web 應用程式資源群組](./media/troubleshooting/website-resource-model.png)

如此一來，如果您只授予某人 Web 應用程式存取權限，Azure 入口網站的網站刀鋒視窗上的諸多功能即會停用。

這些項目需要對應至您網站的「應用程式服務方案」**** 的**寫入**權：  

* 檢視 Web 應用程式的定價層 (免費或標準)  
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)  
* 配額 (儲存容量、頻寬、CPU)  

這些項目都需要包含您網站的整個「資源群組」**** 的**寫入**權：  

* Tls/SSL 憑證和系結 (TLS/SSL 憑證可以在相同資源群組和地理位置中的網站之間共用)   
* 警示規則  
* 自動調整設定  
* 應用程式見解元件  
* Web 測試  

## <a name="virtual-machine-features-that-require-write-access"></a>需要寫入存取權的虛擬機器功能

類似於 Web 應用程式，虛擬機器刀鋒視窗上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」**** 的**寫入**權：

* 端點  
* IP 位址  
* 磁碟  
* 延伸模組  

這些項目都需要同時具備「虛擬機器」**** 與所屬之「資源群組」****(連同網域名稱) 的**寫入**權：  

* 可用性設定組  
* 負載平衡集合  
* 警示規則  

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，以取得資源群組的參與者存取權限。

## <a name="azure-functions-and-write-access"></a>Azure Functions 和寫入權限

[Azure Functions](../azure-functions/functions-overview.md) 的某些功能需要寫入存取權。 例如，如果使用者被指派「 [讀取](built-in-roles.md#reader) 者」角色，他們將無法在函式應用程式中查看函式。 入口網站將顯示 **(無存取權)**。

![函數應用程式無存取權](./media/troubleshooting/functionapps-noaccess.png)

讀者可以按一下 [平台功能]**** 索引標籤，然後按一下 [所有設定]**** 以檢視與函數應用程式相關的一些設定 (類似於 Web 應用程式)，但他們不能修改這些設定。 若要存取這些功能，您將需要 [參與者](built-in-roles.md#contributor) 角色。

## <a name="next-steps"></a>後續步驟

- [針對來賓使用者進行疑難排解](role-assignments-external-users.md#troubleshoot)
- [使用 Azure 入口網站新增或移除 Azure 角色指派](role-assignments-portal.md)
- [查看 Azure RBAC 變更的活動記錄](change-history-report.md)
