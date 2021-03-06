---
title: 受控虛擬網路 & 受控私人端點
description: 瞭解 Azure Data Factory 中的受控虛擬網路和受控私人端點。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 07/15/2020
ms.openlocfilehash: 7a0d3c60841cb12f2999a929eb4af351716abda7
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92635774"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure Data Factory 受控虛擬網路 (預覽) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文將說明 Azure Data Factory 中的受控虛擬網路和受控私人端點。


## <a name="managed-virtual-network"></a>受控虛擬網路

當您在 Azure Data Factory 受控虛擬網路 (VNET) 內建立 Azure Integration Runtime (IR) 時，將會使用受控虛擬網路布建整合執行時間，並會利用私人端點安全地連線至支援的資料存放區。 

在受控虛擬網路內建立 Azure IR 可確保資料整合程式是隔離且安全的。 

使用受控虛擬網路的優點：

- 使用受控虛擬網路，您可以卸載管理虛擬網路以 Azure Data Factory 的負擔。 您不需要為可從虛擬網路使用許多私人 Ip 的 Azure Integration Runtime 建立子網，且需要規劃先前的網路基礎結構。 
- 它不需要深入的 Azure 網路知識，就可以安全地進行資料整合。 相反地，資料工程師可以更簡化安全 ETL 的使用者入門。 
- 受控虛擬網路以及受管理的私人端點可防止資料遭到外泄。 

> [!IMPORTANT]
>目前，受管理的 VNet 僅在與 Azure Data Factory 區域相同的區域中受到支援。
 

![ADF 管理的虛擬網路架構](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>受控私人端點

受控私人端點是在 Azure Data Factory 受控虛擬網路中建立的私人端點，可建立 Azure 資源的私人連結。 Azure Data Factory 會代表您管理這些私人端點。 

![新增受控私人端點](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure Data Factory 支援私用連結。 Private link 可讓您存取 Azure (PaaS) 服務 (例如 Azure 儲存體、Azure Cosmos DB、Azure Synapse Analytics (先前的 Azure SQL 資料倉儲) # A5。

當您使用 private link 時，資料存放區與受控虛擬網路之間的流量會完全流經 Microsoft 骨幹網路。 Private Link 可保護您免受資料外洩風險。 您可以建立私人端點來建立資源的私人連結。

私人端點會使用受控虛擬網路中的私人 IP 位址，有效地將服務帶入其中。 私人端點會對應至 Azure 中的特定資源，而不是整個服務。 客戶可以限制其組織所核准之特定資源的連線能力。 深入了解[私人連結和私人端點](../private-link/index.yml)。

> [!NOTE]
> 建議您使用受控私人端點連線到所有資料來源。 
 
> [!WARNING]
> 如果 PaaS 資料存放區 (Blob、ADLS Gen2 Azure Synapse Analytics) 已針對其建立私人端點，而且即使它允許從所有網路存取，則 ADF 只能使用受控私人端點來存取它。 請務必在這類情況下建立私人端點。 

當您在 Azure Data Factory 中建立受控私人端點時，會以「擱置」狀態建立私人端點連線。 已起始核准工作流程。 私人連結資源的擁有者會負責核准或拒絕連線。

![管理私人端點](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

如果擁有者核准連線，就會建立私人連結。 否則，系統將不會建立私人連結。 無論是哪一種情況，受控私人端點都會以連線狀態來更新。

![核准受控私人端點](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

只有處於已核准狀態的受控私人端點可以將流量傳送到指定的私人連結資源。

## <a name="limitations-and-known-issues"></a>限制與已知問題
### <a name="supported-data-sources"></a>支援的資料來源
以下是支援從 ADF 管理的虛擬網路透過私人連結進行連線的資料來源。
- Azure Blob 儲存體
- Azure 表格儲存體
- Azure 檔案
- Azure Data Lake Gen2
- Azure SQL Database (不包含 Azure SQL 受控執行個體) 
- Azure Synapse Analytics (先前稱為 Azure SQL 資料倉儲)
- Azure CosmosDB SQL
- Azure 金鑰保存庫
- Azure Private Link 服務
- Azure 搜尋服務
- 適用於 MySQL 的 Azure 資料庫
- 適用於 PostgreSQL 的 Azure 資料庫
- 適用於 MariaDB 的 Azure 資料庫

### <a name="azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions"></a>下列 Azure 區域提供 Azure Data Factory 受控虛擬網路：
- 美國東部
- 美國東部 2
- 美國中西部
- 美國西部
- 美國西部 2
- 美國中南部
- 美國中部
- 北歐
- 西歐
- 英國南部
- 東南亞
- 澳大利亞東部
- 澳大利亞東南部

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>透過來自 ADF 管理的虛擬網路的公用端點進行輸出通訊
- 只會開啟埠443進行輸出通訊。
- 不支援 Azure 儲存體和 Azure Data Lake Gen2 透過來自 ADF 管理的虛擬網路的公用端點進行連線。

### <a name="linked-service-creation-of-azure-key-vault"></a>Azure Key Vault 的連結服務建立 
- 當您建立 Azure Key Vault 的連結服務時，沒有 Azure Integration Runtime 參考。 因此，您無法在 Azure Key Vault 的連結服務建立期間建立私人端點。 但是，當您針對參考 Azure Key Vault 連結服務的資料存放區建立連結服務，而此連結的服務參考了已啟用受控虛擬網路的 Azure Integration Runtime 時，您就能夠在建立時建立 Azure Key Vault 連結服務的私人端點。 
- Azure Key Vault 連結服務的 **測試連接** 作業只會驗證 URL 格式，但不會進行任何網路作業。

## <a name="next-steps"></a>後續步驟

- 教學課程： [使用受控虛擬網路和私人端點建立複製管線](tutorial-copy-data-portal-private.md) 
- 教學課程： [使用受控虛擬網路和私人端點建立對應資料流程管線](tutorial-data-flow-private.md)