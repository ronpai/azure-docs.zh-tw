---
title: 什麼是 Azure SQL Edge？
description: 深入瞭解 Azure SQL Edge
keywords: SQL Edge 簡介, 什麼是 SQL Edge, SQL Edge 概觀
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e34cd0907320f96f2846c1f424e678555381cccc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90907129"
---
# <a name="what-is-azure-sql-edge"></a>什麼是 Azure SQL Edge？

Azure SQL Edge 是適用于 IoT 和 IoT Edge 部署的優化關係資料庫引擎。 其提供的功能可為 IoT 應用程式和解決方案建立高效能的資料儲存和處理層。 Azure SQL Edge 提供串流、處理及分析關聯式和非關聯式 (例如 JSON、圖表和時間序列資料) 的功能，使其成為各種現代 IoT 應用程式中的正確選擇。

Azure SQL Edge 是以最新版本的 [SQL Server 資料庫引擎](/sql/sql-server/sql-server-technical-documentation)為基礎，提供領先業界的效能、安全性和查詢處理功能。 由於 Azure SQL Edge 建置於與 [SQL Server](/sql/sql-server/sql-server-technical-documentation) 和 [Azure sql](https://docs.microsoft.com/azure/azure-sql/)相同的引擎上，因此它會提供相同的 transact-sql (t-sql) 程式設計介面區，讓您更輕鬆快速地開發應用程式或解決方案，並讓 IoT Edge 裝置、資料中心和雲端之間的應用程式可攜性更快。

Channel 9 上的 Azure SQL Edge 影片是什麼：
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-SQL-Edge/player]

## <a name="deployment-models"></a>部署模型

Azure SQL Edge 支援兩種部署模式。

- 透過 Azure IoT Edge 連線的部署： Azure SQL Edge 可在 Azure Marketplace 上取得，並且可部署為 [Azure IoT Edge](../iot-edge/about-iot-edge.md)的模組。 如需詳細資訊，請參閱[部署 Azure SQL Edge](deploy-portal.md)。<br>

![SQL Edge 概觀圖表](media/overview/overview.png)

- 已中斷連線的部署：可以從 docker hub 提取 Azure SQL Edge 容器映射，並將其部署為獨立的 docker 容器或 kubernetes 叢集。 如需詳細資訊，請參閱 [使用 Docker 部署 AZURE Sql edge](disconnected-deployment.md) 和 [在 Kubernetes 中部署 azure sql edge 容器](deploy-kubernetes.md)。

## <a name="editions-of-sql-edge"></a>SQL Edge 的版本

SQL Edge 提供兩種不同的版本或軟體方案。 這些版本具有相同的功能集，不同之處在於其使用權限和支援的 CPU/記憶體數量。

   |**規劃**  |**說明**  |
   |---------|---------|
   |Azure SQL Edge 開發人員  |  僅限開發 SKU，每個 SQL Edge 容器限制為最多 4 個核心和 32 GB 的記憶體  |
   |Azure SQL Edge    |  生產 sku，每個 SQL Edge 容器限制為最多8個核心和 64 GB 的記憶體。 |

## <a name="pricing-and-availability"></a>價格與可用性

Azure SQL Edge 現已正式推出。 如需特定區域中定價和可用性的詳細資訊，請參閱 [AZURE SQL Edge](https://azure.microsoft.com/services/sql-edge/)。

> [!IMPORTANT]
> 若要了解 Azure SQL Edge 和 SQL Server 之間的功能差異，以及不同 Azure SQL Edge 選項之間的差異，請參閱 [Azure SQL Edge 支援的功能](features.md)。

## <a name="streaming-capabilities"></a>串流功能  

Azure SQL Edge 提供內建串流功能，以進行即時分析和複雜的事件處理。 串流功能是使用與 [Azure 串流分析](../stream-analytics/stream-analytics-introduction.md)相同的建構以及與 [Azure IoT Edge 串流分析](../stream-analytics/stream-analytics-edge.md)類似的功能來建置。

Azure SQL Edge 的串流引擎設計著眼於低延遲、具復原能力、有效率地使用頻寬和合規性。 

如需有關 SQL Edge 中資料串流的詳細資訊，請參閱[資料串流](stream-data.md)

## <a name="machine-learning-and-artificial-intelligence-capabilities"></a>機器學習與人工智慧功能

Azure SQL Edge 藉由整合開放式格式 ONNX (Open Neural Network Exchange) 執行階段來提供內建的機器學習和分析功能，讓您可以在不同的架構之間交換深度學習和類神經網路模型。 如需 ONNX 的詳細資訊，請參閱[這裡](https://onnx.ai/)。 ONNX 執行階段提供彈性，讓您以您所選擇的語言或工具開發模型，這些選擇隨後可以轉換成 ONNX 格式，以便在 SQL Edge 中執行。 如需詳細資訊，請參閱[在 SQL Edge 中使用 ONNX 的機器學習與人工智慧](onnx-overview.md)。

## <a name="working-with-azure-sql-edge"></a>使用 Azure SQL Edge

Azure SQL Edge 讓應用程式的開發及維護更簡易也更有生產力。 使用者可以使用所有熟悉的工具和技術，針對其 IoT Edge 需求建置絕佳的應用程式和解決方案。 使用者可以使用如下所示的工具，在 SQL Edge 中進行開發：

- [Azure 入口網站](https://portal.azure.com/) - 網頁型應用程式，可用於管理所有 Azure 服務。
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) - 可下載的免費用戶端應用程式，可用於管理任何 SQL 基礎結構 (從 SQL Server 到 SQL Database)。
- [Visual Studio 中的 SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt/) - 可下載的免費用戶端應用程式，可用於開發 SQL Server 關聯式資料庫、SQL 資料庫、Integration Services 套件、Analysis Services 資料模型及 Reporting Services 報告。
- [Azure Data Studio](/sql/azure-data-studio/what-is/) - 免費、可下載、跨平台資料庫工具，適合在 Windows、macOS 和 Linux 上使用 Microsoft 系列內部部署和雲端資料平台的資料專業人員。
- [Visual Studio Code](https://code.visualstudio.com/docs) - 免費、可下載、開放原始碼程式碼編輯器，適用於 Windows、macOS 和 Linux。 它支援延伸模組，包括用於查詢 Microsoft SQL Server、Azure SQL Database 和 Azure Synapse Analytics 的 [mssql 擴充](https://aka.ms/mssql-marketplace) 功能。


## <a name="next-steps"></a>後續步驟

- [透過 Azure 入口網站部署 SQL Edge](deploy-portal.md)
- [使用 SQL Edge 的機器學習與人工智慧](onnx-overview.md)
- [使用 SQL Edge 建置端對端 IoT 解決方案](tutorial-deploy-azure-resources.md)
