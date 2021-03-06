---
title: 連線至適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽)
description: 如何使用 Azure Synapse Link 將 Azure Cosmos DB 連線至 Synapse 工作區
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 50717c7e8a7b0f748df98c1896e1b7eb64cd7be3
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819352"
---
# <a name="connect-to-azure-synapse-link-preview-for-azure-cosmos-db"></a>連線至適用於 Azure Cosmos DB 的 Azure Synapse Link (預覽)

本文說明如何使用 Azure Synapse Link 從 Azure Synapse Analytics Studio 存取 Azure Cosmos DB 資料庫。

## <a name="prerequisites"></a>必要條件

要將 Azure Cosmos DB 資料庫連線至您的工作區，您將需要：

> [!IMPORTANT]
> 適用於 Azure Cosmos DB 的 Azure Synapse Link 目前支援未啟用受控虛擬網路的工作區。 

* 現有的 Azure Cosmos DB 資料庫，或依照此[快速入門](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)的指示建立新帳戶
* 現有的 Synapse 工作區，或依照此[快速入門](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)的指示建立新的工作區 

## <a name="enable-azure-cosmos-db-analytical-store"></a>啟用 Azure Cosmos DB 分析存放區

若要對 Azure Cosmos DB 執行大規模的分析，而不影響您的作業效能，建議您啟用適用於 Azure Cosmos DB 的 Synapse Link。 Synapse Link 可將 HTAP 功能導入 Azure Synapse 的容器和內建支援中。

## <a name="navigate-to-synapse-studio"></a>瀏覽至 Synapse Studio

從您的 Synapse 工作區中，選取 [啟動 Synapse Studio]。 在 Synapse Studio 首頁上，選取 [資料]，這會使您進入 [資料物件總管]。

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>將 Azure Cosmos DB 資料庫連線至 Synapse 工作區

Azure Cosmos DB 資料庫連線會以連結服務的形式完成。 Azure Cosmos DB 連結服務可讓使用者瀏覽及探索資料，以及從適用於 Azure Synapse Analytics 的 Apache Spark 或 SQL 讀取和或寫入至 Azure Cosmos DB。

在資料物件總管中，您可以執行下列步驟以直接連線至 Azure Cosmos DB 資料庫：

1. 選取 [資料] 附近的 ***+*** 圖示
2. 選取 [連線至外部資料]
3. 選取要連線到的 API：SQL API 或 MongoDB 的 API
4. 選取 [繼續]
5. 為連結服務命名。 此名稱會顯示在物件總管中，且供 Synapse 執行階段用來連線至資料庫和容器。 建議使用易記名稱。
6. 選取 [Azure Cosmos DB 帳戶名稱] 和 [資料庫名稱]
7. (選擇性) 若未指定區域，則 Synapse 執行階段作業將會路由至已啟用分析存放區的最近區域。 不過，您可以手動設定您想要讓使用者存取 Azure Cosmos DB 分析存放區的區域。 選取 [其他連線屬性]，然後選取 [新增]。 在 [屬性名稱] 底下，寫入 ***PreferredRegions***，並將 [值] 設定為您要的區域 (例如：WestUS2，文字與數字之間沒有空格)
8. 選取 [建立] 

Azure Cosmos DB 資料庫會顯示在 [Azure Cosmos DB] 區段的 [已連結] 索引標籤底下。 透過 Azure Cosmos DB，您可以使用下列圖示來區分已啟用 HTAP 的容器與僅限 OLTP 的容器：

**僅限 OLTP 的容器**：

![OLTP 容器](../media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

**已啟用 HTAP 的容器**：

![HTAP 容器](../media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>與程式碼產生的動作快速互動

以滑鼠右鍵按一下容器時，您會看到將觸發 Spark 或 SQL 執行階段的手勢清單。 寫入容器的作業會透過 Azure Cosmos DB 的交易存放區執行，且將會使用要求單位。  

## <a name="next-steps"></a>後續步驟

* [了解 Synapse 與 Azure Cosmos DB 之間支援的項目](./concept-synapse-link-cosmos-db-support.md)
* [了解如何使用 Spark 查詢分析存放區](./how-to-query-analytical-store-spark.md)
