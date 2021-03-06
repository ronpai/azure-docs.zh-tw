---
title: Azure HDInsight 中的 Apache Hive LLAP 查詢效能不佳
description: Apache Hive LLAP 中的查詢執行速度比 Azure HDInsight 的預期慢。
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: 33fc86c121cd8fb5100530a0939d620dc2c5a36c
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92532861"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>案例： Azure HDInsight 中 Apache Hive LLAP 查詢的效能不佳

本文說明使用 Azure HDInsight 叢集中的 Interactive Query 元件時，所發生之問題的疑難排解步驟和可能的解決方法。

## <a name="issue"></a>問題

預設叢集設定未充分調整您的工作負載。 Hive LLAP 中的查詢執行速度比預期慢。

## <a name="cause"></a>原因

這可能是因為各種原因而發生。

## <a name="resolution"></a>解決方案

LLAP 已針對牽涉到聯結和匯總的查詢進行優化。 下列查詢在互動式 Hive 叢集中的執行效果不佳：

```
select * from table where column = "columnvalue"
```

若要改善 Hive LLAP 中的點查詢效能，請設定下列設定：

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

您也可以使用下列設定變更來增加 LLAP 快取的使用量，以改善效能：

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>後續步驟

如果您沒有看到您的問題，或無法解決您的問題，請瀏覽下列其中一個管道以取得更多支援：

* 透過 [Azure 社群支援](https://azure.microsoft.com/support/community/)獲得由 Azure 專家所提供的解答。

* 與 [@AzureSupport](https://twitter.com/azuresupport) 聯繫 - 專為改善客戶體驗而設的官方 Microsoft Azure 帳戶，協助 Azure 社群連接至適當的資源，例如解答、支援及專家等。

* 如果需要更多協助，您可在 [Azure 入口網站](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) 提交支援要求。 在功能表列選取 [支援] 或開啟 [說明 + 支援] 中心。 如需詳細資訊，請參閱[如何建立 Azure 支援要求](../../azure-portal/supportability/how-to-create-azure-support-request.md) (機器翻譯)。 您可透過 Microsoft Azure 訂用帳戶來存取訂用帳戶管理和帳單支援，並透過其中一項 [Azure 支援方案](https://azure.microsoft.com/support/plans/)以取得技術支援。