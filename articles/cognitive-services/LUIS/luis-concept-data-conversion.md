---
title: 資料轉換-LUIS
titleSuffix: Azure Cognitive Services
description: 了解在 Language Understanding (LUIS) 中的預測之前如何改變語句
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: b305be693f59b65a62570f656a0132f4f03cf099
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541793"
---
# <a name="convert-data-format-of-utterances"></a>轉換語句的資料格式
LUIS 會在預測之前，提供下列使用者語句的轉換：

* 使用 [認知服務語音](../Speech-Service/overview.md) 服務的語音轉換文字。

## <a name="speech-to-text"></a>語音轉換文字

語音轉換文字提供為與 LUIS 的整合。

### <a name="intent-conversion-concepts"></a>意圖轉換概念
LUIS 的語音轉換文字功能可讓您將口頭語句傳送到端點並接收 LUIS 預測回應。 此流程整合了[語音](https://docs.microsoft.com/azure/cognitive-services/Speech)服務與 LUIS。 透過[教學課程](../speech-service/how-to-recognize-intents-from-speech-csharp.md)，深入了解語音轉換到意圖的更多資訊。

### <a name="key-requirements"></a>重要需求
您不必為此整合建立 **Bing 語音 API** 金鑰。 在 Azure 入口網站中建立的 **Language Understanding** 金鑰適用於此整合。 請勿使用 LUIS 入門金鑰。

### <a name="pricing-tier"></a>定價層
此整合會使用與一般 Language Understanding 定價層不同的[定價](luis-limits.md#key-limits)模型。

### <a name="quota-usage"></a>配額使用方式
相關資訊請參閱[重要限制](luis-limits.md#key-limits)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [擷取資料](luis-concept-data-extraction.md)

