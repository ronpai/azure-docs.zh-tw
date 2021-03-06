---
title: 教學課程：Azure Active Directory 與 Sansan 整合 | Microsoft Docs
description: 了解如何設定 Azure Active Directory 與 Sansan 之間的單一登入。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2020
ms.author: jeedes
ms.openlocfilehash: fa4e91a087c7dcfce247cacc2dff83458bc87f64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079977"
---
# <a name="tutorial-integrate-sansan-with-azure-active-directory"></a>教學課程：整合 Sansan 與 Azure Active Directory

在本教學課程中，您將了解如何整合 Sansan 與 Azure Active Directory (Azure AD)。 在整合 Sansan 與 Azure AD 時，您可以︰

* 在 Azure AD 中控制可存取 Sansan。
* 讓使用者使用其 Azure AD 帳戶自動登入 Sansan。
* 在 Azure 入口網站集中管理您的帳戶。

若要深入了解 SaaS 應用程式與 Azure AD 整合，請參閱[什麼是搭配 Azure Active Directory 的應用程式存取和單一登入](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>Prerequisites

若要開始，您需要下列項目：

* Azure AD 訂用帳戶。 如果沒有訂用帳戶，您可以取得[免費帳戶](https://azure.microsoft.com/free/)。
* 已啟用 SanSan 單一登入 (SSO) 的訂用帳戶。

## <a name="scenario-description"></a>案例描述

在本教學課程中，您會在測試環境中設定和測試 Azure AD SSO。
* Sansan 支援 **SP** 起始的 SSO。
* 設定 Sansan 後，您可以強制執行工作階段控制項，以即時防止組織的敏感資料遭到外洩和滲透。 工作階段控制項會從條件式存取延伸。 [了解如何使用 Microsoft Cloud App Security 來強制執行工作階段控制項](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-sansan-from-the-gallery"></a>從資源庫新增 Sansan

若要設定將 Sansan 整合到 Azure AD 中，您需要從資源庫將 Sansan 新增到受控 SaaS 應用程式清單。

1. 使用公司或學校帳戶或個人的 Microsoft 帳戶登入 [Azure 入口網站](https://portal.azure.com)。
1. 在左方瀏覽窗格上，選取 [Azure Active Directory] 服務。
1. 巡覽至 [企業應用程式]，然後選取 [所有應用程式]。
1. 若要新增應用程式，請選取 [新增應用程式]  。
1. 在 [從資源庫新增]**** 區段的搜尋方塊中輸入 **Sansan**。
1. 從結果面板選取 [Sansan]****，然後新增應用程式。 當應用程式新增至您的租用戶時，請等候幾秒鐘。

## <a name="configure-and-test-azure-ad-sso"></a>設定並測試 Azure AD SSO

以名為 **Britta Simon** 的測試使用者，設定及測試與 Sansan 搭配運作的 Azure AD SSO。 若要讓 SSO 能夠運作，您必須建立 Azure AD 使用者與 Sansan 中相關使用者之間的連結關聯性。

若要設定及測試與 Sansan 搭配運作的 Azure AD SSO，請完成下列建置組塊：

1. **[設定 Azure AD SSO](#configure-azure-ad-sso)** ，讓您的使用者能夠使用此功能。
   * **[建立 Azure AD 測試使用者](#create-an-azure-ad-test-user)** ，以使用 Britta Simon 測試 Azure AD 單一登入。
   * **[指派 Azure AD 測試使用者](#assign-the-azure-ad-test-user)** ，讓 Britta Simon 能夠使用 Azure AD 單一登入。
1. **[設定 Sansan](#configure-sansan)** 以在應用程式端設定 SSO 設定。
   * **[建立 Sansan 測試使用者](#create-sansan-test-user)**，讓 Sansan 中對應的 Britta Simon 連結到使用者在 Azure AD 中的代表項目。
1. **[測試 SSO](#test-sso)** ，以驗證組態是否能運作。

## <a name="configure-azure-ad-sso"></a>設定 Azure AD SSO

依照下列步驟在 Azure 入口網站中啟用 Azure AD SSO。

1. 在 [Azure 入口網站](https://portal.azure.com/)的 [Sansan]**** 應用程式整合頁面上，尋找 [管理]**** 區段並選取 [單一登入]****。
1. 在 [選取單一登入方法] 頁面上，選取 [SAML]。
1. 在 [以 SAML 設定單一登入] 頁面上，按一下 [基本 SAML 設定] 的編輯/畫筆圖示，以編輯設定。

   ![編輯基本 SAML 組態](common/edit-urls.png)

1. 在 [基本 SAML 設定]  頁面上，輸入下列欄位的值：

    1. 在 [登入 URL] 文字方塊中，輸入 URL：`https://ap.sansan.com/`

   1. 在 [識別碼 (實體識別碼)]**** 文字方塊中，輸入 URL：  
   `https://ap.sansan.com/saml2/<company name>`

   1. 在 [回覆 URL] 文字方塊中，以下列模式輸入任一 URL：

    
       | 環境 | URL |
      |:--- |:--- |
      | PC |`https://ap.sansan.com/v/saml2/<company name>/acs` |
      | 智慧型手機應用程式 |`https://internal.api.sansan.com/<company name>/acs` |
      | 智慧型手機網頁 |`https://ap.sansan.com/s/saml2/<company name>/acs` |

    > [!NOTE]
    > 這些都不是真正的值。 檢查 **Sansan 管理員設定**中的實際值。

1. 在 [以 SAML 設定單一登入]  頁面的 [SAML 簽署憑證]  區段中，尋找 [憑證 (Base64)]  並選取 [下載]  ，以下載憑證並將其儲存在電腦上。

   ![憑證下載連結](common/certificatebase64.png)

1. 在 [設定 Sansan]**** 區段上，根據您的需求複製適當的 URL。

   ![複製組態 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>建立 Azure AD 測試使用者

在本節中，您會在 Azure 入口網站中建立名稱為 Britta Simon 的測試使用者。

1. 在 Azure 入口網站的左窗格中，依序選取 [Azure Active Directory]  、[使用者]  和 [所有使用者]  。
1. 在畫面頂端選取 [新增使用者]。
1. 在 [使用者] 屬性中，執行下列步驟：
   1. 在 [名稱] 欄位中，輸入 `Britta Simon`。  
   1. 在 [使用者名稱] 欄位中，輸入 username@companydomain.extension。 例如： `BrittaSimon@contoso.com` 。
   1. 選取 [顯示密碼] 核取方塊，然後記下 [密碼] 方塊中顯示的值。
   1. 按一下 [建立]。

### <a name="assign-the-azure-ad-test-user"></a>指派 Azure AD 測試使用者

在本節中，您會將 Sansan 的存取權授與 Britta Simon，讓她能夠使用 Azure 單一登入。

1. 在 Azure 入口網站中，選取 [企業應用程式]  ，然後選取 [所有應用程式]  。
1. 在應用程式清單中，選取 [Sansan]****。
1. 在應用程式的概觀頁面中尋找 [管理]  區段，然後選取 [使用者和群組]  。

   ![[使用者和群組] 連結](common/users-groups-blade.png)

1. 選取 [新增使用者]，然後在 [新增指派] 對話方塊中選取 [使用者和群組]。

    ![[新增使用者] 連結](common/add-assign-user.png)

1. 在 [使用者和群組] 對話方塊中，從使用者清單選取 **Britta Simon**，然後按一下畫面底部的 [選取] 按鈕。
1. 如果您在 SAML 判斷提示中需要任何角色值，請在 [選取角色] 對話方塊的清單中為使用者選取適當的角色，然後按一下畫面底部的 [選取] 按鈕。
1. 在 [新增指派] 對話方塊中，按一下 [指派] 按鈕。

## <a name="configure-sansan"></a>設定 Sansan

若要在 **Sansan** 端執行**單一登入設定**，請根據您的需求遵循下列步驟。

   * [日文](https://jp-help.sansan.com/hc/ja/articles/900001551383 )版。

   * [英文](https://jp-help.sansan.com/hc/en-us/articles/900001551383 )版。


### <a name="create-sansan-test-user"></a>建立 Sansan 測試使用者

在本節中，您要在 Sansan 中建立名為 Britta Simon 的使用者。 如需有關如何建立使用者的詳細資訊，請參閱[這些](https://jp-help.sansan.com/hc/en-us/articles/206508997-Adding-users)步驟。

## <a name="test-sso"></a>測試 SSO

當您在存取面板中選取 Sansan 圖格時，應該會自動登入您設定 SSO 的 Sansan。 如需「存取面板」的詳細資訊，請參閱[存取面板簡介](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)。

## <a name="additional-resources"></a>其他資源

- [如何與 Azure Active Directory 整合 SaaS 應用程式的教學課程清單](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什麼是搭配 Azure Active Directory 的應用程式存取和單一登入？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什麼是 Azure Active Directory 中的條件式存取？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)