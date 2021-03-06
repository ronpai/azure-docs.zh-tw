---
title: 適用于 MongoDB 的 Azure Cosmos DB API Azure CLI 範例
description: 適用于 MongoDB 的 Azure Cosmos DB API Azure CLI 範例
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5cb8155f541aa6a6eb6c52e1e0bafeb0844e9689
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276923"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-api-for-mongodb"></a>適用于 MongoDB 的 Azure Cosmos DB API Azure CLI 範例

下表包含適用於 Azure Cosmos DB 之範例 Azure CLI 指令碼的連結。 使用右側的連結瀏覽至 API 特定範例。 所有 API 的通用範例皆相同。 您可以在 [Azure CLI 參考](/cli/azure/cosmosdb)中取得所有 Azure Cosmos DB CLI 命令的參考頁面。 您也可以在 [Azure Cosmos DB CLI GitHub 存放庫](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)中找到 Azure Cosmos DB CLI 指令碼範例。

這些範例需要 Azure CLI 2.12.1 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)

## <a name="common-samples"></a>通用範例

這些範例適用於所有 Azure Cosmos DB API

|Task | 描述 |
|---|---|
| [新增或容錯移轉區域](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 新增區域、變更容錯移轉優先順序、觸發手動容錯移轉。|
| [帳戶金鑰和連接字串](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 列出帳戶金鑰、唯讀金鑰、重新產生金鑰及列出連接字串。|
| [使用 IP 防火牆保護安全](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| 建立已設定 IP 防火牆的 Cosmos 帳戶。|
| [使用服務端點保護新的帳戶](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| 建立 Cosmos 帳戶並使用服務端點來保護其安全。|
| [使用服務端點保護現有帳戶](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 終於設定好子網路後，將 Cosmos 帳戶更新為使用服務端點來保護其安全。|
|||

## <a name="mongodb-api-samples"></a>MongoDB API 範例

|Task | 描述 |
|---|---|
| [建立 Azure Cosmos 帳戶、資料庫和集合](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| 建立適用於 MongoDB API 的 Azure Cosmos DB 帳戶、資料庫和集合。 |
| [建立具有自動調整功能及二個具有共用輸送量之集合的 Azure Cosmos 帳戶、資料庫和集合](scripts/cli/mongodb/autoscale.md?toc=%2fcli%2fazure%2ftoc.json)| 針對 MongoDB API，建立具有自動調整功能及二個具有共用輸送量之集合的 Azure Cosmos DB 帳戶和資料庫。 |
| [輸送量作業](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 在資料庫和集合上的自動調整與標準輸送量之間進行讀取、更新和遷移。|
| [鎖定資源避免刪除](scripts/cli/mongodb/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 使用資源鎖定避免刪除資源。|
|||
