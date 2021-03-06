---
title: Azure Key Vault 安全性
description: 針對 Azure Key Vault、金鑰和祕密進行存取權限管理。 包含 Key Vault 的驗證和授權模型以及如何保護金鑰保存庫。
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c3dd4e5138741a3c035507358830f3572cf92751
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739685"
---
# <a name="azure-key-vault-security"></a>Azure Key Vault 安全性

您可以使用 Azure Key Vault 來保護雲端中的加密金鑰和秘密，例如憑證、連接字串和密碼。 當您儲存機密和商務關鍵資料時，您需要採取步驟，將保存庫的安全性和儲存在其中的資料最大化。

## <a name="identity-and-access-management"></a>身分識別和存取管理

當您在 Azure 訂用帳戶中建立金鑰保存庫時，它會自動與該訂用帳戶的 Azure AD 租用戶建立關聯。 嘗試從保存庫管理或擷取內容的任何人都必須由 Azure AD 進行驗證。

- 驗證會建立呼叫者的身分識別。
- 授權則會判斷呼叫者可以執行的作業。 Key Vault 中的授權使用[角色型存取控制](../../role-based-access-control/overview.md) (RBAC) 和 Azure Key Vault 存取原則的組合。

### <a name="access-model-overview"></a>存取模型概觀

保存庫的存取會透過兩個介面或平面進行。 這些平面包括管理平面和資料平面。

- *管理平面*是您管理 Key Vault 本身的位置，而且這是用來建立和刪除保存庫的介面。 您也可以讀取 Key Vault 屬性並管理存取原則。
- *資料平面*可讓您處理金鑰保存庫中儲存的資料。 您可以新增、刪除和修改金鑰、祕密和憑證。

若要在任一平面存取金鑰保存庫，所有呼叫者 (使用者或應用程式) 都必須經過驗證和授權。 兩個平面都會使用 Azure Active Directory (Azure AD) 來進行驗證。 至於授權，管理平面會使用角色型存取控制 (RBAC)，資料平面則會使用 Key Vault 存取原則。

這兩個平面的單一驗證機制模型有幾個優點：

- 組織可以集中控制其組織內所有金鑰保存庫的存取權。
- 使用者如果離職，就會立即失去組織中所有金鑰保存庫的存取權。
- 組織可以藉由使用 Azure AD 中的選項 (例如，啟用多重要素驗證以提升安全性) 來自訂驗證。

### <a name="managing-administrative-access-to-key-vault"></a>管理 Key Vault 的系統管理存取權

當您在資源群組中建立金鑰保存庫時，可以使用 Azure AD 管理存取權。 您可以對使用者或群組授與在資源群組中管理金鑰保存庫的能力。 您可以指派適當的 Azure 角色，以授與特定範圍層級的存取權。 若要對使用者授與管理金鑰保存庫的權限，您可以在特定範圍對使用者指派預先定義的 `key vault Contributor` 角色。 下列範圍層級可以指派給 Azure 角色：

- **訂**用帳戶：在訂用帳戶層級指派的 Azure 角色會套用到該訂用帳戶內的所有資源群組和資源。
- **資源群組**：在資源群組層級指派的 Azure 角色會套用至該資源群組中的所有資源。
- **特定資源**：指派給特定資源的 Azure 角色會套用至該資源。 在此情況下，資源會是特定的金鑰保存庫。

有數個預先定義的角色。 如果預先定義的角色不符合您的需求，您可以定義您自己的角色。 如需詳細資訊，請參閱[RBAC：內建角色](../../role-based-access-control/built-in-roles.md)。

> [!IMPORTANT]
> 如果使用者具有金鑰保存庫管理平面的 `Contributor` 權限，就可以透過設定 Key Vault 存取原則，對自己授與資料平面的存取權。 因此，請嚴格控制誰可以有金鑰保存庫的 `Contributor` 角色存取權。 請確定只有獲得授權的人員可以存取和管理您的金鑰保存庫、金鑰、祕密和憑證。

<a id="data-plane-access-control"></a>
### <a name="controlling-access-to-key-vault-data"></a>控制對 Key Vault 資料的存取

Key Vault 存取原則可分別授與金鑰、祕密或憑證的權限。 您只能對使用者授與存取金鑰 (而非祕密) 的權限。 金鑰、祕密和憑證的存取權限是在保存庫層級管理的。

> [!IMPORTANT]
> Key Vault 存取原則不支援細微物件層級的權限，例如特定金鑰、祕密或憑證。 對使用者授與金鑰的建立和刪除權限時，其便可對該金鑰保存庫的所有金鑰執行這些作業。

您可以設定金鑰保存庫的存取原則使用 [Azure 入口網站](assign-access-policy-portal.md)、 [Azure CLI](assign-access-policy-cli.md)、 [Azure PowerShell](assign-access-policy-powershell.md)或 [Key Vault 管理 REST api](/rest/api/keyvault/)。

您可以使用 [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)來限制資料平面的存取)。 您可以設定[防火牆和虛擬網路規則](network-security.md)來額外加上一層安全性。

## <a name="network-access"></a>網路存取

您可以指定存取保存庫所使用的 IP 位址來減少保存庫的暴露。 Azure Key Vault 的虛擬網路服務端點可讓您將存取權限制為指定的虛擬網路。 這些端點也可讓您將存取權限制為 IPv4 (網際網路通訊協定第 4 版) 位址範圍的清單。 任何從這些來源之外連線到金鑰保存庫的使用者都會被拒絕存取。

防火牆規則生效後，使用者只能在其要求源自允許的虛擬網路或 IPv4 位址範圍時，才可以讀取 Key Vault 中的資料。 這也適用於從 Azure 入口網站存取 Key Vault。 雖然使用者可以從 Azure 入口網站瀏覽金鑰保存庫，但是如果其用戶端電腦不在允許的清單中，他們就無法列出金鑰、祕密或憑證。 這也會影響其他 Azure 服務的 [金鑰保存庫選擇器]。 使用者可以看到金鑰保存庫清單，但是如果防火牆規則阻止其用戶端電腦，則不會列出金鑰。

如需有關 Azure Key Vault 網路位址的詳細資訊，請檢閱 [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md))

## <a name="tls-and-https"></a>TLS 和 HTTPS

*   Key Vault 前端 (資料平面) 是多租使用者伺服器。 這表示來自不同客戶的金鑰保存庫可以共用相同的公用 IP 位址。 為了達成隔離，每個 HTTP 要求都會與其他要求分開進行驗證和授權。
*   您可以識別舊版的 TLS 來回報弱點，但因為公用 IP 位址是共用的，所以 key vault 服務小組無法針對傳輸層級的個別金鑰保存庫停用舊版 TLS。
*   HTTPS 通訊協定可讓用戶端參與 TLS 協商。 **用戶端可以強制執行最新版本的 TLS**，而且每當用戶端執行此動作時，整個連接都會使用對應的層級保護。 Key Vault 仍然支援舊版 TLS 的事實不會影響使用較新 TLS 版本的連線安全性。
*   儘管 TLS 通訊協定有已知的弱點，但在攻擊者使用具有弱點的 TLS 版本連線時，並沒有任何已知的攻擊可讓惡意代理程式從您的金鑰保存庫中解壓縮任何資訊。 攻擊者仍然需要自行驗證及授權，而且只要合法的用戶端一律與最新的 TLS 版本連線，就無法在舊版的 TLS 版本上洩漏認證。

## <a name="logging-and-monitoring"></a>記錄和監視

Key Vault 記錄會儲存在保存庫上執行之活動的相關資訊。 如需完整的詳細資訊，請參閱 [Key Vault 記錄](logging.md)。

如需安全地管理儲存體帳戶的建議，請參閱 [Azure 儲存體安全性指南](../../storage/blobs/security-recommendations.md)

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 的虛擬網路服務端點](overview-vnet-service-endpoints.md)
- [RBAC：內建角色](../../role-based-access-control/built-in-roles.md)