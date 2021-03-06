---
title: 包含檔案
description: 包含檔案
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743535"
---
若要存取 App Service 中應用程式程式碼內部產生的主控台記錄，請在 [Cloud Shell](https://shell.azure.com) 中執行下列命令，以開啟診斷記錄功能：

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

`--level` 的可能值為：`Error`、`Warning`、`Info` 和 `Verbose`。 後續的每個層級都包含上一個層級。 例如：`Error` 只包含錯誤訊息，而 `Verbose` 包含所有訊息。

開啟診斷記錄後，請執行下列命令來查看記錄資料流：

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

如果您沒有立即看到主控台記錄，請在 30 秒後再查看。

> [!NOTE]
> 您也可以在瀏覽器中的 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 檢查記錄檔。

若要隨時停止記錄資料流，請輸入 `Ctrl`+`C`。
