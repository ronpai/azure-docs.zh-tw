---
title: Azure 安全性記錄和審核 |Microsoft Docs
description: 瞭解 Azure 中可用的記錄，以及您可以取得的資訊安全見解。
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2019
ms.author: terrylan
ms.openlocfilehash: c5ac9daeb741d400a759603c7a3e3e462cc9294f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91398864"
---
# <a name="azure-security-logging-and-auditing"></a>Azure 安全性記錄和稽核 (部分機器翻譯)

Azure 提供各種可設定的安全性稽核和記錄選項，協助您識別安全性原則和機制間的差距。 本文討論如何從 Azure 上所裝載的服務產生、收集及分析安全性記錄。

> [!Note]
> 本文的某些建議可能會導致資料、網路或計算資源使用量增加，並可能增加授權或訂用帳戶成本。

## <a name="types-of-logs-in-azure"></a>Azure 中的記錄類型

雲端應用程式相當複雜，且具有許多移動組件。 記錄資料可提供應用程式的深入解析，並協助您：

- 針對過去的問題或防止潛在的問題進行疑難排解
- 改善應用程式效能或維護性
- 將其他需要手動介入的動作自動化

Azure 記錄可歸類為下列類型：
* **控制/管理記錄**提供 Azure Resource Manager CREATE、UPDATE 和 DELETE 作業的相關資訊。 如需詳細資訊，請參閱[Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)。

* **資料平面記錄**提供使用 Azure 資源時所引發事件的相關資訊。 這個記錄類型的範例是虛擬機器 (VM) 中的 Windows 事件系統、安全性和應用程式記錄，以及透過 Azure 監視器設定的[診斷記錄](../../azure-monitor/platform/platform-logs-overview.md)。

* **已處理的事件**提供分析已代替您處理之事件/警示的相關資訊。 這個類型的範例是 [Azure 資訊安全中心警示](../../security-center/security-center-managing-and-responding-alerts.md)，[Azure 資訊安全中心](../../security-center/security-center-intro.md)已在其中處理和分析您的訂用帳戶，並提供簡要的安全性警示。

下表列出 Azure 中可用的最重要記錄類型：

| 記錄分類 | 記錄類型 | 使用量 | 整合 |
| ------------ | -------- | ------ | ----------- |
|[活動記錄](../../azure-monitor/platform/platform-logs-overview.md)|Azure Resource Manager 資源上控制層面的事件|  讓您了解訂用帳戶中的資源所執行之作業。|    REST API、[Azure 監視器](../../azure-monitor/platform/platform-logs-overview.md)|
|[Azure 資源記錄](../../azure-monitor/platform/platform-logs-overview.md)|關於訂用帳戶中 Azure Resource Manager 作業的經常性資料|   讓您了解資源自行執行的作業。| Azure 監視器|
|[Azure Active Directory 報告](../../active-directory/reports-monitoring/overview-reports.md)|記錄和報告 | 報告使用者登入活動，以及使用者和群組管理相關的系統活動資訊。|[圖形 API](../../active-directory/develop/active-directory-graph-api-quickstart.md)|
|[虛擬機器和雲端服務](../../azure-monitor/learn/quick-collect-azurevm.md)|Windows 事件記錄服務與 Linux Syslog|  在虛擬機器上擷取系統資料和記錄資料，並將該資料傳送到您所選擇的儲存體帳戶。|   Azure 監視器中的 Windows (使用 Windows Azure 診斷儲存體 [[WAD](../../monitoring-and-diagnostics/azure-diagnostics.md)] 儲存體) 和 Linux|
|[Azure 儲存體分析](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)|儲存體記錄，提供儲存體帳戶的計量資料|讓您了解追蹤要求、分析使用趨勢，以及診斷儲存體帳戶的問題。|   REST API 或[用戶端程式庫](https://msdn.microsoft.com/library/azure/mt347887.aspx)|
|[網路安全性群組 (NSG) 流量記錄](../../network-watcher/network-watcher-nsg-flow-logging-overview.md)|JSON 格式，顯示每個規則的輸出和輸入流程|顯示透過網路安全性群組輸入和輸出 IP 流量的相關資訊。|[Azure 網路監看員](../../network-watcher/network-watcher-monitoring-overview.md)|
|[Application Insight](../../azure-monitor/app/app-insights-overview.md)|記錄、例外狀況及自訂診斷|  提供多個平台上的 Web 開發人員所適用的應用程式效能監控 (APM) 服務。| REST API、[Power BI](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)|
|[處理資料 / 安全性警示](../../security-center/security-center-intro.md)|  Azure 資訊安全中心警示、Azure 監視器記錄警示|    提供安全性資訊和警示。|  REST API、JSON|

## <a name="log-integration-with-on-premises-siem-systems"></a>與內部部署之 SIEM 系統整合的記錄
[整合資訊安全中心警示](../../security-center/security-center-export-data-to-siem.md) 討論如何將資訊安全中心警示、azure 診斷記錄所收集的虛擬機器安全性事件，以及使用您的 Azure 監視器記錄檔或 SIEM 解決方案的 azure audit 記錄進行同步處理。

## <a name="next-steps"></a>後續步驟

- [稽核與記錄](management-monitoring-overview.md)：藉由保持可見度並快速回應即時安全性警示來保護資料。

- [Azure 中的安全性記錄和稽核記錄收集](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)：強制執行這些設定，以確保您的 Azure 執行個體正在收集正確的安全性和稽核記錄。

- [設定網站集合的稽核設定](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)：如果您是網站集合管理員，請擷取個別使用者的動作歷程記錄，以及特定日期範圍內所採取的動作歷程記錄。

- [在 Microsoft 365 安全性中心中搜尋審核記錄](/microsoft-365/compliance/search-the-audit-log-in-security-and-compliance)：使用 Microsoft 365 安全性中心在您的組織中搜尋整合的審核記錄和查看使用者和系統管理員活動。
