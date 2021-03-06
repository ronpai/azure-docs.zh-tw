---
title: 受保護的 web API-總覽
titleSuffix: Microsoft identity platform
description: 瞭解如何 (總覽) 建立受保護的 web API。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9ff9ae811a29685937b922f04a277e663e26f1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91257359"
---
# <a name="scenario-protected-web-api"></a>案例：受保護的 web API

在此案例中，您會瞭解如何公開 web API。 您也會瞭解如何保護 web API，讓只有經過驗證的使用者可以存取它。

若要使用您的 web API，您必須啟用具有公司和學校帳戶的已驗證使用者，或啟用 Microsoft 個人帳戶。

## <a name="specifics"></a>特性

以下是您需要知道用來保護 web Api 的特定資訊：

- 您的應用程式註冊必須公開至少一個 *範圍* 或一個 *應用程式角色*。
  - 範圍是由代表使用者呼叫的 web Api 所公開。
  - 應用程式角色是由 daemon 應用程式所呼叫的 web Api 所公開， (會代表自己呼叫您的 web API) 。
- 如果您建立新的 web API 應用程式註冊，請選擇您的 web API 所接受的 [存取權杖版本](reference-app-manifest.md#accesstokenacceptedversion-attribute) `2` 。 針對舊版 web Api，可接受的權杖版本可以是 `null` ，但此值會限制只有組織的登入物件，且不支援 (MSA) 的個人 Microsoft 帳戶。
- Web API 的程式碼設定必須驗證呼叫 web API 時所使用的權杖。
- 控制器動作中的程式碼必須驗證權杖中的角色或範圍。

## <a name="recommended-reading"></a>建議閱讀資料

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [應用程式註冊](scenario-protected-web-api-app-registration.md)
