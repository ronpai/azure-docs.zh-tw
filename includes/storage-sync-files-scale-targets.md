---
title: 包含檔案
description: 包含檔案
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 10/13/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 47eae616ffc62e42448da23fb02152dae17aa548
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116729"
---
| 資源 | 目標 | 固定限制 |
|----------|--------------|------------|
| 每個區域的儲存體同步服務數目 | 100 個儲存體同步服務 | 是 |
| 每個儲存體同步服務的同步群組 | 200 個同步群組 | 是 |
| 每個儲存體同步服務的已註冊伺服器 | 99 部伺服器 | 是 |
| 每個同步群組的雲端端點 | 1 個雲端端點 | 是 |
| 每個同步群組的伺服器端點 | 50 個伺服器端點 | 否 |
| 每部伺服器的伺服器端點 | 30 個伺服器端點 | 是 |
| 每個同步群組的檔案系統物件 (目錄和檔案) | 1 億個物件 | 否 |
| 目錄中的檔案系統物件 (目錄和檔案) 數目上限 | 500 萬個物件 | 是 |
| 物件 (目錄和檔案) 安全性描述元大小上限 | 64 KiB | 是 |
| 檔案大小 | 100 GiB | 否 |
| 要分層之檔案的檔案大小下限 | V9 和更新版本：根據檔案系統叢集大小 (雙重檔案系統叢集大小)。 例如，若檔案系統叢集大小為 4 KB，則最小檔案大小會是 8 KB。<br> V8 和較舊版本：64 KiB  | 是 |

> [!Note]  
> Azure 檔案同步端點可以擴大至 Azure 檔案共用的大小。 如果達到 Azure 檔案共用大小限制，同步處理將無法運作。
