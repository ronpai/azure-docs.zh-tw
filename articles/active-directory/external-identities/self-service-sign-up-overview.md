---
title: 適用於外部身分識別的自助式註冊 - Azure AD
description: 了解如何允許外部使用者啟用自助式註冊，以自行註冊您的應用程式。 藉由自訂自助式註冊使用者流程，建立個人化的註冊體驗。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b08e2e530843dd98c87e424812706247388228
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908164"
---
# <a name="self-service-sign-up-preview"></a>自助式註冊 (預覽)

> [!NOTE]
> 自助式註冊是 Azure Active Directory 的公開預覽功能。 如需有關預覽版的詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

與外部使用者共用應用程式時，您不一定都能事先知道哪些人需要存取應用程式。 除了直接將邀請傳送給個人以外，您還可以允許外部使用者藉由啟用自助式註冊，來註冊特定的應用程式。 您可以自訂自助式註冊使用者流程，藉以建立個人化的註冊體驗。 例如，您可以提供使用 Azure AD 或社交識別提供者註冊的選項，並在註冊程序期間收集使用者的相關資訊。

> [!NOTE]
> 您可以將由您的組織所建置的應用程式與使用者流程建立關聯。 使用者流程不能用於 Microsoft 應用程式，例如 SharePoint 或 Teams。

## <a name="user-flow-for-self-service-sign-up"></a>自助式註冊的使用者流程

自助式註冊使用者流程會透過您想要共用的應用程式，為您的外部使用者建立註冊體驗。 使用者流程可與您的一或多個應用程式相關聯。 首先，您將為租用戶啟用自助式註冊，並與您要讓外部使用者用於登入的識別提供者建立同盟。 接著，您將建立及自訂註冊使用者流程，並為其指派您的應用程式。
您可以設定使用者流程設定，以控制使用者註冊應用程式的方式：

- 用於登入的帳戶類型，例如 Facebook 之類的社交帳戶，或 Azure AD 帳戶
- 要在使用者註冊時收集的屬性，例如名字、郵遞區號或所在國家/地區

當使用者想要登入您的應用程式時，無論是 Web、行動、桌面或單頁應用程式 (SPA)，應用程式都會對使用者流程提供的端點起始授權要求。 使用者流程會定義和控制使用者的體驗。 當使用者完成註冊使用者流程時，Azure AD 會產生權杖，然後將使用者重新導向回您的應用程式。 完成註冊時，系統會在目錄中為使用者佈建來賓帳戶。 多個應用程式可使用相同的使用者流程。

## <a name="example-of-self-service-sign-up"></a>自助式註冊的範例

下列範例說明如何將社交識別提供者導入為來賓使用者提供自助式註冊功能的 Azure AD 中。  
Woodgrove 的合作夥伴開啟了 Woodgrove 應用程式。 他們決定要註冊供應商帳戶，因此選取了 [要求您的供應商帳戶]，以起始自助式註冊流程。

![自助式註冊起始頁面的範例](media/self-service-sign-up-overview/example-start-sign-up-flow.png)

他們使用自己選擇的電子郵件進行註冊。

![此範例顯示以 Facebook 帳戶登入的選項](media/self-service-sign-up-overview/example-sign-in-with-facebook.png)

Azure AD 使用合作夥伴的 Facebook 帳戶建立與 Woodgrove 的關聯性，並在使用者註冊後為他們建立新的來賓帳戶。

Woodgrove 想要深入了解使用者，例如姓名、公司名稱、商業登記碼、電話號碼。

![顯示使用者註冊屬性的範例](media/self-service-sign-up-overview/example-enter-user-attributes.png)

使用者在輸入資訊後繼續進行註冊流程，並取得所需資源的存取權。

![顯示使用者已登入的範例](media/self-service-sign-up-overview/example-signed-in.png)

## <a name="next-steps"></a>後續步驟

 如需詳細資訊，請參閱如何[將自助式註冊新增至應用程式](self-service-sign-up-user-flow.md)。