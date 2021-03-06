---
title: 使用實體行為分析來偵測 advanced 威脅 |Microsoft Docs
description: 在 Azure Sentinel 中啟用使用者和實體行為分析，並設定資料來源
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2020
ms.author: yelevin
ms.openlocfilehash: c55ea0e7753faa6dc21b955d63a57d96e3849f70
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90994610"
---
# <a name="enable-user-and-entity-behavior-analytics-ueba-in-azure-sentinel"></a>在 Azure Sentinel 中啟用使用者和實體行為分析 (UEBA)  



## <a name="prerequisites"></a>必要條件

- 您必須將 Azure AD 中的「 **全域管理員** 」或「 **安全性系統管理員** 」角色指派給您的使用者，才能啟用或停用 UEBA，但無法執行。

## <a name="how-to-enable-user-and-entity-behavior-analytics"></a>如何啟用使用者和實體行為分析

1. 從 Azure Sentinel 導覽功能表中，選取 [ **實體行為] ([預覽]) **。

1. 在 [ **開啟**] 標題下，將切換開關切換為 [ **開啟**]。

1. 按一下 [ **選取資料來源** ] 按鈕。

1. 在 [**資料來源選取**] 窗格中，標示您要啟用 UEBA 之資料來源旁的核取方塊，然後選取 [套用 **]。**

    > [!NOTE]
    >
    > 在 [ **資料來源選擇** ] 窗格的下半部，您會看到尚未啟用的 UEBA 支援資料來源清單。 
    >
    > 當您啟用 UEBA 之後，當您連接新的資料來源時，就會有選項可讓它們直接從 [資料連線器] 窗格 UEBA，如果它們具有 UEBA 功能的話。

1. 選取 [ **移至實體搜尋**]。 這會將您帶到 [實體搜尋] 窗格，從現在開始，您可以從主要 Azure Sentinel 功能表選擇 **實體行為** 時看到的內容。

## <a name="next-steps"></a>後續步驟
在本檔中，您已瞭解如何在 Azure Sentinel 中啟用和設定使用者和實體行為分析 (UEBA) 。 若要深入了解 Azure Sentinel，請參閱下列文章：
- 深入了解如何[取得資料的可見度以及潛在威脅](quickstart-get-visibility.md)。
- 開始[使用 Azure Sentinel 偵測威脅](tutorial-detect-threats-built-in.md)。
