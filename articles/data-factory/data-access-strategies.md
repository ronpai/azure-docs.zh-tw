---
title: 資料存取策略
description: Azure Data Factory 現在支援靜態 IP 位址範圍。
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 785381e0a42f2b502e4ea7054753d5f3fb67f385
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632765"
---
# <a name="data-access-strategies"></a>資料存取策略

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

組織的重要安全性目標是保護其資料存放區，使其無法透過網際網路進行隨機存取，無論是內部部署或雲端/SaaS 資料存放區。 

通常雲端資料存放區會使用下列機制來控制存取：
* 從虛擬網路 Private Link 到啟用私人端點的資料來源
* 依 IP 位址限制連線的防火牆規則
* 要求使用者證明其身分識別的驗證機制
* 將使用者侷限於特定動作和資料的授權機制

> [!TIP]
> 隨著[引進靜態 IP 位址範圍](./azure-integration-runtime-ip-addresses.md)，您現在可以允許特定 Azure 整合執行階段區域的清單 IP 範圍，以確保您不需要允許雲端資料存放區中的所有 Azure IP 位址。 如此一來，您就可以限制允許存取資料存放區的 IP 位址。

> [!NOTE] 
> IP 位址範圍會封鎖 Azure Integration Runtime，且目前僅用於資料移動、管線和外部活動。 啟用受控虛擬網路的資料流程和 Azure Integration Runtime，現在不會使用這些 IP 範圍。 

這應該適用於許多案例，而且我們確實了解每個整合執行階段都要有唯一的靜態 IP 位址，但是目前在使用 Azure Integration Runtime (無伺服器) 的情況下並不可行。 如有需要，您一律可以設定自我裝載整合執行階段，並且與靜態 IP 搭配使用。 

## <a name="data-access-strategies-through-azure-data-factory"></a>透過 Azure Data Factory 的資料存取策略

* **[Private Link](../private-link/private-link-overview.md)** -您可以在 Azure Data Factory 受控虛擬網路內建立 Azure Integration Runtime，它將利用私人端點安全地連線至支援的資料存放區。 受控虛擬網路與資料來源之間的流量會傳送 Microsoft 骨幹網路，而不會暴露在公用網路上。
* **[信任的服務](../storage/common/storage-network-security.md#exceptions)** - Azure 儲存體 (Blob、ADLS Gen2) 支援防火牆設定，可以選取信任的 Azure 平台服務以安全地存取儲存體帳戶。 受信任的服務會強制執行受控識別驗證，以確保其他 data factory 無法連線到此儲存體，除非使用其受控識別進行核准。 如需更多詳細資料，請參閱 **[此部落格](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** 。 因此，這是非常安全且建議的做法。 
* **唯一的靜態 IP** - 您將需要設定自我裝載整合執行階段，以取得 Data Factory 連接器的靜態 IP。 此機制可確保您可以封鎖所有其他 IP 位址的存取。 
* **[靜態 IP 範圍](./azure-integration-runtime-ip-addresses.md)** - 您可以使用 Azure Integration Runtime 的 IP 位址，以允許在您的儲存體 (例如 S3、Salesforce 等等) 中將其列出。 確定會限制可以連線到資料存放區，但是也依賴驗證/授權規則的 IP 位址。
* **[服務標籤](../virtual-network/service-tags-overview.md)** - 服務標籤代表來自指定 Azure 服務 (例如 Azure Data Factory) 的一組 IP 位址前置詞。 Microsoft 會管理服務標籤包含的位址前置詞，並隨著位址變更自動更新服務標籤，而盡可能簡化網路安全性規則頻繁的更新。 篩選虛擬網路中 IaaS 裝載資料存放區的資料存取時，這非常有用。
* **允許 Azure 服務** - 部分服務可讓您在選擇此選項時，允許所有 Azure 服務連線到該服務。 

如需 Azure Integration Runtime 和自我裝載整合執行階段中資料存放區上支援網路安全性機制的詳細資訊，請參閱以下兩個資料表。  
* **Azure Integration Runtime**

    | 資料存放區                  | 資料存放區上支援的網路安全性機制 | Private Link     | 信任的服務     | 靜態 IP 範圍 | 服務標籤 | 允許 Azure 服務 |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure PaaS 資料存放區       | Azure Cosmos DB                                     | 是              | -                   | 是             | -            | 是                  |
    |                              | Azure 資料總管                                 | -                | -                   | 是*            | 是*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | 是             | -            | 是                  |
    |                              | 適用於 MariaDB、MySQL、PostgreSQL 的 Azure 資料庫       | -                | -                   | 是             | -            | 是                  |
    |                              | Azure 檔案儲存體                                  | 是              | -                   | 是             | -            | .                    |
    |                              | Azure 儲存體 (Blob、ADLS Gen2)                      | 是              | 是 (僅限 MSI 驗證) | 是             | -            | .                    |
    |                              | Azure SQL DB，Azure Synapse Analytics) ，SQL Ml  | 是 (僅適用于 Azure SQL DB/DW)         | -                   | 是             | -            | 是                  |
    |                              | Azure Key Vault (適用於擷取祕密/連接字串) | 是      | 是                 | 是             | -            | -                    |
    | 其他 PaaS/SaaS 資料存放區 | AWS S3、SalesForce、Google Cloud Storage 等等    | -                | -                   | 是             | -            | -                    |
    | Azure laaS                   | SQL Server、Oracle 等等                          | -                | -                   | 是             | 是          | -                    |
    | 內部部署 laaS              | SQL Server、Oracle 等等                          | -                | -                   | 是             | -            | -                    |
    
    **僅適用於當 Azure 資料總管是插入的虛擬網路，且 IP 範圍可以在 NSG/防火牆上套用時。* 

* **自我裝載整合執行階段 (在 Vnet/內部部署中)**
    
    | 資料存放區                  | 資料存放區上支援的網路安全性機制         | 靜態 IP | 信任的服務  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS 資料存放區       | Azure Cosmos DB                                               | 是       | -                   |
    |                                | Azure 資料總管                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | 是       | -                   |
    |                                | 適用於 MariaDB、MySQL、PostgreSQL 的 Azure 資料庫               | 是       | -                   |
    |                                | Azure 檔案儲存體                                            | 是       | -                   |
    |                                | Azure 儲存體 (Blob、ADLS Gen2)                             | 是       | 是 (僅限 MSI 驗證) |
    |                                | Azure SQL DB，Azure Synapse Analytics) ，SQL Ml          | 是       | -                   |
    |                                | Azure Key Vault (適用於擷取祕密/連接字串) | 是       | 是                 |
    | 其他 PaaS/SaaS 資料存放區 | AWS S3、SalesForce、Google Cloud Storage 等等              | 是       | -                   |
    | Azure laaS                     | SQL Server、Oracle 等等                                  | 是       | -                   |
    | 內部部署 laaS              | SQL Server、Oracle 等等                                  | 是       | -                   |    

## <a name="next-steps"></a>後續步驟

如需詳細資訊，請參閱下列相關文章：
* [支援的資料存放區](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault「信任的服務」](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure 儲存體「信任的服務」](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [適用於 Data Factory 的受控識別](./data-factory-service-identity.md)