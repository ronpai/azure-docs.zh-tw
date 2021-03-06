---
title: 設定權杖 - Azure Active Directory B2C | Microsoft Docs
description: 瞭解如何在 Azure Active Directory B2C 中設定權杖存留期和相容性設定。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340218"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>設定 Azure Active Directory B2C 中的權杖

在本文中，您將瞭解如何在 Azure Active Directory B2C (Azure AD B2C) 中設定[權杖的存留期和相容性](tokens-overview.md)。

## <a name="prerequisites"></a>Prerequisites

[建立使用者流程](tutorial-create-user-flows.md)，讓使用者註冊並登入您的應用程式。

## <a name="configure-jwt-token-lifetime"></a>設定 JWT 權杖存留期

您可以在任何使用者流程上設定權杖存留期。

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 確定您使用的目錄包含您的 Azure AD B2C 租用戶。 在頂端功能表中選取 [目錄 + 訂閱] 篩選，並選擇包含您 Azure AD B2C 租用戶的目錄。
3. 選擇 Azure 入口網站左上角的 [所有服務]，然後搜尋並選取 [Azure AD B2C]。
4. 選取 [使用者流程 (原則)]。
5. 開啟您先前建立的使用者流程。
6. 選取 [屬性] 。
7. 在 [權杖存留期] 下，調整下列屬性以符合應用程式的需求：

    ![Azure 入口網站中的權杖存留期屬性設定](./media/configure-tokens/token-lifetime.png)

8. 按一下 [檔案] 。

> [!NOTE]
> 使用授權碼流程與 PKCE 的單一頁面應用程式一律會有24小時的重新整理權杖存留期。 [深入瞭解在瀏覽器中重新整理權杖的安全性含意](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)。

## <a name="configure-jwt-token-compatibility"></a>設定 JWT 權杖相容性

1. 選取 [使用者流程 (原則)]。
2. 開啟您先前建立的使用者流程。
3. 選取 [屬性] 。
4. 在 [權杖相容性設定] 下，調整下列屬性以符合應用程式的需求：

    ![Azure 入口網站中的權杖相容性屬性設定](./media/configure-tokens/token-compatibility.png)

5. 按一下 [檔案] 。

## <a name="provide-optional-claims-to-your-app"></a>為您的應用程式提供選擇性宣告

應用程式宣告是傳回給應用程式的值。 更新您的使用者流程以包含所需的宣告。

1. 選取 [使用者流程 (原則)]。
1. 開啟您先前建立的使用者流程。
1. 選取 [應用程式宣告]****。
1. 選擇您想要傳回給應用程式的宣告和屬性。
1. 按一下 [檔案] 。


## <a name="next-steps"></a>後續步驟

深入瞭解如何[要求存取權杖](access-tokens.md)。



