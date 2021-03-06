---
title: Windows 虛擬桌面 (傳統) 疑難排解總覽-Azure
description: 針對在設定 Windows 虛擬桌面 (傳統) 租使用者環境中的問題進行疑難排解的總覽。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 7b2220ffe92446338e1b9db9b329847d93830334
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005426"
---
#  <a name="windows-virtual-desktop-classic-troubleshooting-overview-feedback-and-support"></a>Windows 虛擬桌面 (傳統) 疑難排解總覽、意見反應和支援

>[!IMPORTANT]
>此內容適用於不支援 Azure Resource Manager Windows 虛擬桌面物件的 Windows 虛擬桌面 (傳統)。 如果您嘗試管理 Azure Resource Manager Windows 虛擬桌面物件，請參閱[這篇文章](../troubleshoot-set-up-overview.md)。

本文概述您在設定 Windows 虛擬桌面租使用者環境時可能會遇到的問題，並提供解決問題的方法。

## <a name="provide-feedback"></a>提供意見反應

請瀏覽 [Windows 虛擬桌面技術社群](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)，與產品小組和活躍的社群成員一起討論 Windows 虛擬桌面服務。

## <a name="escalation-tracks"></a>呈報追蹤

使用下表來找出並解決在使用遠端桌面用戶端設定租使用者環境時，可能會遇到的問題。 租使用者設定之後，您就可以使用新的 [診斷服務](diagnostics-role-service-2019.md) 來找出一般案例的問題。

>[!NOTE]
> 我們有一個技術相關論壇，可供您討論產品小組和活躍的社區成員的問題。 造訪 [Windows 虛擬桌面技術小組](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) 以開始討論。

| **問題**                                                            | **建議的解決方案**  |
|----------------------------------------------------------------------|-------------------------------------------------|
| 建立 Windows 虛擬桌面租使用者                                                    | 如果發生 Azure 中斷，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/);否則，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **Windows 虛擬桌面** ，針對問題類型選取 [ **部署** ]，然後針對問題子類型選取 [ **建立 Windows 虛擬桌面租** 使用者的問題]。|
| 存取 Azure 入口網站中的 Marketplace 範本       | 如果發生 Azure 中斷，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> Azure Marketplace Windows 虛擬桌面範本可免費使用。|
| 從 GitHub 存取 Azure Resource Manager 範本                                  | 請參閱[租使用者和主機集區建立](troubleshoot-set-up-issues-2019.md)的[建立 Windows 虛擬桌面工作階段主機 vm](troubleshoot-set-up-issues-2019.md#creating-windows-virtual-desktop-session-host-vms)一節。 如果問題仍無法解決，請洽詢 [GitHub 支援小組](https://github.com/contact)。 <br> <br> 如果在存取 GitHub 中的範本之後發生此錯誤，請聯絡 [Azure 支援](https://azure.microsoft.com/support/create-ticket/)。|
| 工作階段主機集區 Azure 虛擬網路 (VNET) 和 Express Route 設定               | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後在 [網路] 類別下選取適當的服務 () 。 |
| 當未使用 Windows 虛擬桌面提供的 Azure Resource Manager 範本時，工作階段主機集區虛擬機器 (VM) 建立 | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，然後為服務選取執行 **Windows 的虛擬機器** 。 <br> <br> 針對 Windows 虛擬桌面所提供 Azure Resource Manager 範本的問題，請參閱建立 [租使用者和主機集](troubleshoot-set-up-issues-2019.md)區的 Windows 虛擬桌面租使用者一節。 |
| 從 Azure 入口網站管理 Windows 虛擬桌面工作階段主機環境    | [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)。 <br> <br> 針對使用遠端桌面服務/Windows 虛擬桌面 PowerShell 時發生的管理問題，請參閱 [Windows 虛擬桌面 powershell](troubleshoot-powershell-2019.md) 或 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)、為服務選取 [ **windows 虛擬桌面** ]、針對問題類型選取 [設定 **和管理** ]，然後選取 [ **使用 PowerShell** 針對問題子類型設定租使用者的問題]。 |
| 管理系結至主機集區和應用程式群組的 Windows 虛擬桌面設定 (應用程式群組)       | 請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell-2019.md)，或 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **windows 虛擬桌面** ，然後選取適當的問題類型。|
| 部署及管理 FSLogix 設定檔容器 | 請參閱 [FSLogix 產品的疑難排解指南](/fslogix/fslogix-trouble-shooting-ht/) ，如果無法解決問題，請 [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **Windows 虛擬桌面** ，針對問題類型選取 [ **FSLogix** ]，然後選取適當的問題子類型。 |
| 遠端桌面用戶端在啟動時無法正常運作                                                 | 請參閱 [疑難排解遠端桌面用戶端](../troubleshoot-client.md) ，如果無法解決問題，請  [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，選取服務的 **Windows 虛擬桌面** ，然後針對問題類型選取 [ **遠端桌面用戶端** ]。  <br> <br> 如果這是網路問題，您的使用者必須與網路系統管理員聯絡。 |
| 已連線但未饋送                                                                 | 使用使用者連線進行疑難排解 [，但不顯示任何 (](troubleshoot-service-connection-2019.md#user-connects-but-nothing-is-displayed-no-feed) [Windows 虛擬桌面服務](troubleshoot-service-connection-2019.md)連線) 區段的內容。 <br> <br> 如果您的使用者已指派至應用程式群組，請  [開啟 Azure 支援要求](https://azure.microsoft.com/support/create-ticket/)，並選取服務的 [ **Windows 虛擬桌面** ]，然後針對問題類型選取 [ **遠端桌面用戶端** ]。 |
| 由於網路的摘要探索問題                                            | 您的使用者需要聯繫其網路系統管理員。 |
| 連接用戶端                                                                    | 請參閱 [Windows 虛擬桌面服務](troubleshoot-service-connection-2019.md) 連線，如果無法解決您的問題，請參閱 [工作階段主機虛擬機器](troubleshoot-vm-configuration-2019.md)設定。 |
| 遠端應用程式或桌面的回應                                      | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 授權訊息或錯誤                                                          | 如果問題系結至特定的應用程式或產品，請洽詢負責該產品的小組。 |
| 在 GitHub 上使用 Windows 虛擬桌面工具 (Azure Resource Manager 範本、診斷工具、管理工具) 的問題 | 請參閱 [Azure Resource Manager 範本，遠端桌面服務](https://github.com/Azure/RDS-Templates/blob/master/README.md) 報告問題。 |

## <a name="next-steps"></a>後續步驟

- 若要針對在 Windows 虛擬桌面環境中建立租用戶與主機集區時的問題進行疑難排解，請參閱[租用戶和主機集區建立](troubleshoot-set-up-issues-2019.md)。
- 若要針對在 Windows 虛擬桌面中設定虛擬機器 (VM) 時的問題進行疑難排解，請參閱[工作階段主機虛擬機器設定](troubleshoot-vm-configuration-2019.md)。
- 若要對 Windows 虛擬桌面用戶端連線問題進行疑難排解，請參閱 [Windows 虛擬桌面服務連接](troubleshoot-service-connection-2019.md)。
- 若要疑難排解遠端桌面用戶端的問題，請參閱 [遠端桌面用戶端疑難排解](../troubleshoot-client.md)
- 若要針對使用 PowerShell 搭配 Windows 虛擬桌面時的問題進行疑難排解，請參閱 [Windows 虛擬桌面 PowerShell](troubleshoot-powershell-2019.md)。
- 若要深入瞭解此服務，請參閱 [Windows 虛擬桌面環境](environment-setup-2019.md)。
- 若要進行疑難排解教學課程，請參閱[教學課程：針對 Resource Manager 範本部署進行疑難排解](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md)。
- 若要了解稽核動作，請參閱 [使用 Resource Manager 來稽核作業](../../azure-resource-manager/management/view-activity-logs.md)。
- 若要瞭解在部署期間判斷錯誤的動作，請參閱 [查看部署作業](../../azure-resource-manager/templates/deployment-history.md)。
