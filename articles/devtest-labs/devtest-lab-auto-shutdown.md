---
title: 在 Azure DevTest Labs 中管理自動關機原則 |Microsoft Docs
description: 瞭解如何設定實驗室的自動關機原則，讓虛擬機器在不使用時自動關機。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 3532e4859bd63c81cb9b9cda973064e5ef8357ac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320705"
---
# <a name="configure-autoshutdown-for-lab-and-compute-virtual-machines-in-azure-devtest-labs"></a>在 Azure DevTest Labs 中設定實驗室和計算虛擬機器的自動關機

本文說明如何在 DevTest Labs 和計算 Vm 中設定實驗室 Vm 的自動關機設定。 

## <a name="configure-autoshutdown-for-lab-vms-devtest-labs"></a> (DevTest Labs 設定實驗室 Vm 的自動關機) 
Azure DevTest Labs 可讓您管理每個實驗室的原則 (設定)，以控制實驗室的成本並儘可能避免浪費。 本文說明如何設定實驗室帳戶的自動關機原則，並在實驗室帳戶中設定實驗室的自動關機設定。 若要了解如何設定每個實驗室原則，請參閱[在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)。  

### <a name="set-auto-shutdown-policy-for-a-lab"></a>設定實驗室的自動關機原則
身為實驗室擁有者，您可以在實驗室中設定所有 VM 的關機排程。 如此一來，您可經由執行未使用 (閒置) 的機器來節省成本。 您可以在所有的實驗室 VM 上集中強制執行關機原則，但也可讓實驗室使用者免於為其個別機器設定排程。 這項功能可讓您設定實驗室排程的原則，從完全無法控制，以至完全控制實驗室使用者。 身為實驗室擁有者，您可以採取下列步驟來設定此原則：

1. 在實驗室首頁上，選取 [設定與原則]****。
2. 左側功能表的 [排程]**** 區段中選取 [自動關機原則]****。
3. 選取其中一個選項。 下列各節提供有關這些選項的更多詳細資訊：所設定的原則只適用於實驗室中建立的新 VM，而不適用於現有的 VM。 

    ![自動關閉原則選項](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="configure-auto-shutdown-settings"></a>設定自動關機設定
自動關機原則可讓您指定此實驗室的 Vm 關機時間，以協助將實驗室浪費降至最低。

若要檢視 (及變更) 實驗室的原則，請依照下列步驟操作：

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [ **所有服務**]，然後從清單中選取 [ **DevTest Labs** ]。
3. 從實驗室清單中，選取所需的實驗室。   
4. 選取 [組態和原則]****。

    ![原則設定窗格](./media/devtest-lab-set-lab-policy/policies-menu.png)
5. 在實驗室的 [設定**與原則**] 窗格中，選取 [排程 **] 下的**[**自動關機**]。
   
    ![自動關機](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
6. 選取 [開啟]**** 來啟用此原則，以及選取 [關閉]**** 來停用它。
7. 如果您啟用這個原則，請指定時間 (和時區) 以關閉目前實驗室中的所有 VM。
8. 針對選項指定 [ **是]** 或 [ **否** ]，在指定的自動關機時間之前的30分鐘傳送通知。 如果您選擇 [是]****，請輸入 Webhook URL 端點或電子郵件地址，指定您要在哪裡張貼或傳送通知。 使用者會收到通知，並且給予延遲關機的選項。 如需詳細資訊，請參閱 [ [通知](#notifications) ] 區段。 
9. 選取 [儲存]****。

    根據預設，這個原則一經啟用，就會套用到目前實驗室的所有 VM。 若要從特定的 VM 移除此設定，請開啟 VM 的管理窗格並變更其 **自動關機** 設定。
    
> [!NOTE]
> 如果您在目前排程時間的30分鐘內更新實驗室或特定實驗室虛擬機器的自動關機排程，更新的關機時間將適用于下一天的排程。 

### <a name="user-sets-a-schedule-and-can-opt-out"></a>使用者可設定排程，且可退出
如果您將實驗室設定為此原則，則實驗室使用者可以覆寫或選擇不使用實驗室排程。 此選項可讓實驗室使用者完全控制其 VM 的自動關機排程。 實驗室使用者在其 VM 自動關機排程頁面中看不到任何變更。

![自動關機原則選項-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>使用者可設定排程，但無法退出
如果您將實驗室設定為此原則，則實驗室使用者可以覆寫實驗室排程。 不過，他們無法選擇不使用自動關機原則。 此選項可確保您實驗室中的每部機器都會依循自動關機排程。 實驗室使用者可以更新其 VM 的自動關機排程，以及設定關機通知。

![自動關機原則選項-2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>使用者無法控制實驗室管理員所設定的排程
如果您將實驗室設定為此原則，則實驗室使用者不能覆寫或選擇不使用實驗室排程。 此選項可讓實驗室管理員完全控制實驗室中每部機器的排程。 實驗室使用者只可以設定其 VM 的自動關機通知。

![自動關閉原則選項-3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

### <a name="notifications"></a>通知
實驗室擁有者設定自動關機之後，如果任何 Vm 將受到影響，則會在自動關機觸發前30分鐘傳送通知給實驗室使用者。 此選項可讓實驗室使用者有機會在關機前儲存其工作。 通知也會提供每個 VM 的連結，以進行下列動作：

- 在這段時間後略過自動關機
- 將自動關機延遲一小時或2小時，讓他們可以繼續使用 VM。

通知是透過已設定的 webhook 端點，或是由實驗室擁有者在自動關機設定中指定的電子郵件地址來傳送。 Webhook 可讓您建立或設定訂閱特定事件的整合。 觸發其中一個事件時，DevTest Labs 會將 HTTP POST 承載傳送至 webhook 的已設定 URL。 如需 webhook 的詳細資訊，請參閱[建立 webhook 或 API Azure 函式](../azure-functions/functions-bindings-http-webhook.md)。 

我們建議您使用 webhook，因為各種應用程式廣泛支援 (例如，可供使用、Azure Logic Apps 等等。 ) 並可讓您實作為傳送通知的方式。 舉例來說，本文將逐步引導您瞭解如何使用 Azure Logic Apps 從電子郵件取得自動關機通知。 首先，讓我們快速完成可在實驗室中啟用自動關機通知的基本步驟。   

### <a name="create-a-logic-app-that-receives-email-notifications"></a>建立會接收電子郵件通知的邏輯應用程式
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 提供許多現成的連接器，可讓您輕鬆地將服務與其他用戶端（例如 Office 365 和 twitter）整合。 概括而言，設定電子郵件通知邏輯應用程式的步驟可以分成四個階段： 

- 建立邏輯應用程式。 
- 設定內建範本。
- 與您的電子郵件客戶程式整合
- 取得 Webhook URL。

### <a name="create-a-logic-app"></a>建立邏輯應用程式
若要開始使用，請使用下列步驟，在您的 Azure 訂用帳戶中建立邏輯應用程式：

1. 在左側功能表上選取 [ **+ 建立資源** ]，選取 [ **整合**]，然後選取 [ **邏輯應用程式**]。 

    ![新增邏輯應用程式功能表](./media/devtest-lab-auto-shutdown/new-logic-app.png)
2. 在 [ **邏輯應用程式-建立** ] 頁面上，依照下列步驟執行： 
    1. 輸入邏輯應用程式的 **名稱** 。
    2. 選取您的 Azure **訂用帳戶**。
    3. 建立新的**資源群組**，或選取現有的資源群組。 
    4. 選取邏輯應用程式的 **位置** 。 

        ![新的邏輯應用程式-設定](./media/devtest-lab-auto-shutdown/new-logic-app-page.png)
3. 在 [ **通知**] 中，選取通知上的 [ **移至資源** ]。 

    ![前往資源](./media/devtest-lab-auto-shutdown/go-to-resource.png)
4. 選取 [**部署工具**] 類別下的 [**邏輯應用程式設計**工具]。

    ![選取 HTTP 要求/回應](./media/devtest-lab-auto-shutdown/select-http-request-response-option.png)
5. 在 [ **HTTP 要求-回應** ] 頁面上，選取 [ **使用此範本**]。 

    ![選取 [使用此範本] 選項](./media/devtest-lab-auto-shutdown/select-use-this-template.png)
6. 將下列 JSON 複製到 **要求主體 JSON 架構** 區段中： 

    ```json
    {
        "$schema": "http://json-schema.org/draft-04/schema#",
        "properties": {
            "delayUrl120": {
                "type": "string"
            },
            "delayUrl60": {
                "type": "string"
            },
            "eventType": {
                "type": "string"
            },
            "guid": {
                "type": "string"
            },
            "labName": {
                "type": "string"
            },
            "owner": {
                "type": "string"
            },
            "resourceGroupName": {
                "type": "string"
            },
            "skipUrl": {
                "type": "string"
            },
            "subscriptionId": {
                "type": "string"
            },
            "text": {
                "type": "string"
            },
            "vmName": {
                "type": "string"
            }
        },
        "required": [
            "skipUrl",
            "delayUrl60",
            "delayUrl120",
            "vmName",
            "guid",
            "owner",
            "eventType",
            "text",
            "subscriptionId",
            "resourceGroupName",
            "labName"
        ],
        "type": "object"
    }
    ```
    
    ![顯示「要求主體 JSON 架構」的螢幕擷取畫面。](./media/devtest-lab-auto-shutdown/request-json.png)
7. 在設計工具中選取 [ **+ 新增步驟** ]，然後依照下列步驟執行：
    1. 搜尋 **Office 365 Outlook-傳送電子郵件**。 
    2. 選取 [從**動作****傳送電子郵件**]。 
    
        ![傳送電子郵件選項](./media/devtest-lab-auto-shutdown/select-send-email.png)
    3. 選取 [登 **入** ] 以登入您的電子郵件帳戶。 
    4. 選取 [ **到** ] 欄位，然後選擇 [擁有者]。
    5. 選取 **[** 主旨]，然後輸入電子郵件通知的主旨。 例如： "Shutdown of machine vmName for Lab： labName"。
    6. 選取 **[** 內文]，並定義電子郵件通知的本文內容。 例如：「vmName 已排程在15分鐘內關機。 按一下 [： URL] 略過這項關機。 延遲關機一小時： delayUrl60。 延遲關機2小時： delayUrl120。」

        ![要求本文 JSON 結構描述](./media/devtest-lab-auto-shutdown/email-options.png)
1. 在工具列上選取 [儲存]  。 現在，您可以複製 **HTTP POST URL**。 選取 [複製] 按鈕，將 URL 複製到剪貼簿。 

    ![WebHook URL](./media/devtest-lab-auto-shutdown/webhook-url.png)

## <a name="configure-autoshutdown-for-compute-vms"></a>設定計算 Vm 的自動關機

1. 在 [**虛擬機器**] 頁面的 [**作業**] 區段中，選取左側功能表上的 [**自動關機**]。 
2. 在 [ **自動關機** ] 頁面上，選取 [ **開啟** ] 以啟用此原則，並選取 [ **關閉** ] 來停用此原則。
3. 如果您啟用此原則，請指定應關閉 VM 的 **時間** ** (和時區**) 。
4. 針對選項指定 [ **是]** 或 [ **否** ]，在指定的自動關機時間之前的30分鐘傳送通知。 如果您選擇 [是]****，請輸入 Webhook URL 端點或電子郵件地址，指定您要在哪裡張貼或傳送通知。 使用者會收到通知，並且給予延遲關機的選項。 如需詳細資訊，請參閱 [ [通知](#notifications) ] 區段。 
9. 選取 [儲存]****。

    ![設定計算 VM 的自動關機](./media/devtest-lab-auto-shutdown/comnpute-auto-shutdown.png)

### <a name="view-activity-logs-for-auto-shutdown-updates"></a>查看自動關機更新的活動記錄
當您更新自動關機設定時，您會看到記錄在 VM 的活動記錄中的活動。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，流覽至您 VM 的首頁。
2. 從左側功能表中選取 [ **活動記錄** ]。 
3. 從篩選準則移除 **資源： mycomputevm** 。
3. 確認您在活動記錄中看到 [ **新增或修改** 排程] 作業。 如果您沒有看到它，請等候一段時間，然後重新整理活動記錄。

    ![活動記錄專案](./media/devtest-lab-auto-shutdown/activity-log-entry.png)
4. 選取 [ **新增或修改** 排程] 作業，在 [ **摘要** ] 頁面上查看下列資訊：

    - 作業名稱 (新增或修改排程) 
    - 更新自動關機設定的日期和時間。
    - 更新設定之使用者的電子郵件地址。 

        ![活動記錄專案摘要](./media/devtest-lab-auto-shutdown/activity-log-entry-summary.png)
5. 切換至 [**新增或修改**排程] 頁面中的 [**變更歷程記錄**] 索引標籤，您會看到設定的變更歷程記錄。 在下列範例中，在2020年4月10日下午6點的關機時間已從下午7點變更為下午6：15:18:47 日。 而且，此設定已在 15:25:09 EST 停用。 

    ![活動記錄-變更歷程記錄](./media/devtest-lab-auto-shutdown/activity-log-entry-change-history.png)
6. 若要查看作業的詳細資料，請在 [**新增或修改**排程] 頁面中，切換至 [ **JSON** ] 索引標籤。

## <a name="next-steps"></a>後續步驟
若要瞭解如何設定所有原則，請參閱 [在 Azure DevTest Labs 中定義實驗室原則](devtest-lab-set-lab-policy.md)。
