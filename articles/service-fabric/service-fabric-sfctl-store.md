---
title: Azure Service Fabric CLI-sfctl store
description: 瞭解 Azure Service Fabric 命令列介面的 sfctl。 包含用來在叢集映射存放區上執行檔案層級作業的命令清單。
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: af5399be44d2946775622598c5c1db3d8355f7cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257065"
---
# <a name="sfctl-store"></a>sfctl store
在叢集映像存放區上執行基本檔案層級作業。

## <a name="commands"></a>命令

|命令|說明|
| --- | --- |
| [刪除] | 刪除現有映像存放區內容。 |
| root-info | 取得映像存放區根的內容資訊。 |
| stat | 取得映像存放區內容資訊。 |

## <a name="sfctl-store-delete"></a>sfctl store delete
刪除現有映像存放區內容。

刪除在指定映像存放區相對路徑內找到的現有映像存放區內容。 此命令可以用來刪除已上傳且佈建的應用程式套件。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --content-path [必要] | 映像存放區中檔案或資料夾與其根的相對路徑。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
取得映像存放區根的內容資訊。

傳回位於映像存放區根目錄中映像存放區內容的相關資訊。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |

## <a name="sfctl-store-stat"></a>sfctl store stat
取得映像存放區內容資訊。

傳回位於所指定 contentPath 映像存放區內容的相關資訊。 contentPath 是相對於映像存放區根目錄的路徑。

### <a name="arguments"></a>引數

|引數|說明|
| --- | --- |
| --content-path [必要] | 映像存放區中檔案或資料夾與其根的相對路徑。 |
| --timeout -t | 用來執行作業的伺服器超時（以秒為單位）。 此超時時間指定用戶端願意等候要求的作業完成的持續時間。 此參數的預設值為60秒。  預設值\: 60。 |

### <a name="global-arguments"></a>全域引數

|引數|說明|
| --- | --- |
| --debug | 增加記錄詳細資訊，以顯示所有偵錯記錄。 |
| --help -h | 顯示此說明訊息並結束。 |
| --output -o | 輸出格式。  允許的值\:json、jsonc、table、tsv。  預設值\:json。 |
| --query | JMESPath 查詢字串。 如需詳細資訊和範例，請參閱 http\://jmespath.org/。 |
| --verbose | 增加記錄詳細資訊。 使用 --debug 來取得完整偵錯記錄。 |


## <a name="next-steps"></a>後續步驟
- [設定](service-fabric-cli.md) Service Fabric CLI。
- 了解如何使用[範例指令碼](./scripts/sfctl-upgrade-application.md)來使用 Service Fabric CLI。
