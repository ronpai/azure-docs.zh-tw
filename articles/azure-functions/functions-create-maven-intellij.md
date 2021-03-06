---
title: 使用 JAVA 和 IntelliJ 建立 Azure 函數
description: 了解如何使用 Java 和 IntelliJ 在 Azure 上建立及發佈簡單的 HTTP 觸發無伺服器應用程式。
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 149d0ae99975628239f8b08f3987947a99e01cbb
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92893228"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>使用 JAVA 和 IntelliJ 建立您的第一個 Azure 函式

本文說明：
- 如何使用 IntelliJ 構想建立 [無伺服器](https://azure.microsoft.com/overview/serverless-computing/) 函式專案
- 在您自己的電腦上對整合式開發環境 (IDE) 中的函式進行測試和偵錯的步驟
- 將函式專案部署至 Azure Functions 的指示

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>設定開發環境

若要使用 Java 和 IntelliJ 開發函式，請安裝下列軟體：

+ 具有有效訂用帳戶的 Azure 帳戶。 [免費建立帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
+ 適用於 JAVA 8 的 [Azure 支援的 Java 開發套件 (JDK)](/azure/developer/java/fundamentals/java-jdk-long-term-support)
+ 已安裝的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition 或 Community Edition
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ 最新的[函式核心工具](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>安裝和登入

1. 在 IntelliJ IDEA 的 [設定/喜好設定] 對話方塊 (Ctrl+Alt+S) 中，選取 [外掛程式]。 然後，在 **Marketplace** 中尋找 **適用於 IntelliJ 的 Azure 工具組** ，然後按一下 [安裝]。 安裝之後，按一下 [重新啟動] 以啟動此外掛程式。 

    ![Marketplace 中適用於 IntelliJ 的 Azure 工具組外掛程式][marketplace]

2. 若要登入您的 Azure 帳戶，請開啟 **Azure 總管** 資訊看板，然後按一下頂端列中的 [Azure 登入] 圖示 (或從 IDEA 功能表 **工具/Azure/Azure 登入** 點選)。
    ![IntelliJ 的 [Azure 登入] 命令][intellij-azure-login]

3. 在 [Azure 登入] 視窗中選取 [裝置登入]，然後按一下 [登入] ([其他登入選項](/azure/developer/java/toolkit-for-intellij/sign-in-instructions))。

   ![選取了 [裝置登入] 的 [Azure 登入] 視窗][intellij-azure-popup]

4. 按一下 [Azure 裝置登入] 對話方塊中的 [複製並開啟]。

   ![[Azure 登入] 對話方塊視窗][intellij-azure-copycode]

5. 在瀏覽器中，貼上您的裝置程式碼 (您在上一個步驟中按一下 [複製並開啟] 時所複製)，然後按 [下一步]。

   ![裝置登入瀏覽器][intellij-azure-link-ms-account]

6. 在 [選取訂用帳戶] 對話方塊中，選取您要使用的訂用帳戶，然後按一下 [確定]。

   ![[選取訂用帳戶] 對話方塊][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>建立本機專案

在本節中，您會使用 Azure Toolkit for IntelliJ 來建立本機 Azure Functions 專案。 稍後在本文中，您會將函式程式碼發佈至 Azure。 

1. 開啟 IntelliJ 歡迎使用對話方塊，選取 [建立新專案] 以開啟新增專案精靈，並選取 [Azure Functions]。

    ![建立 Functions 專案](media/functions-create-first-java-intellij/create-functions-project.png)

1. 選取 [Http 觸發程序]，然後按一下 [下一 步]，然後依照精靈指示進行以下頁面中的所有組態；確認專案位置然後按一下 [完成]；Intellj IDEA 接著會開啟您的新專案。

    ![建立 Functions 專案完成](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-function-app-locally"></a>在本機執行函式應用程式

1. 導覽至 `src/main/java/org/example/functions/HttpTriggerFunction.java` 以查看產生的程式碼。 在第 *17* 行旁邊，您會注意到綠色的 [執行] 按鈕，請按一下然後選取 [執行 'azure-function-exam...']，您會看到函式應用程式在本機執行，並有一些記錄。

    ![本機執行 Functions 專案](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![本機執行 Functions 輸出](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. 您可以從瀏覽器存取列印的端點 (例如 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`) 來嘗試函式。

    ![本機執行 Functions 測試結果](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. 記錄也會在您的想 IDEA 中列印出來；現在按一下 [停止] 按鈕來停止功能。

    ![本機執行 Functions 測試記錄](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-function-app-locally"></a>在本機對函式應用程式進行偵錯

1. 現在讓我們嘗試在本機上對函式應用程式進行偵錯，按一下工具列中的 [偵錯] 按鈕 (如果沒看到，請按一下 [檢視 > 外觀 > 工具列] 以啟用工具列)。

    ![本機偵錯函數按鈕](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. 按一下檔案 `src/main/java/org/example/functions/HttpTriggerFunction.java` 的第「20」 行新增中斷點並再次存取端點 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`，您會發現系統已叫用中斷點：您可以嘗試更多的偵錯工具功能，例如步驟、監看、評估。 按一下 [停止] 按鈕，以停止偵錯工作階段。

    ![本機偵錯函式中斷](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-function-app-to-azure"></a>將您的函式應用程式部署至 Azure

1. 在 IntelliJ Project 總管中以滑鼠右鍵按一下專案，然後選取 [Azure -> 部署至 Azure Functions]

    ![將函式部署到 Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. 如果您還沒有任何函式應用程式，請按一下 [沒有可用的函式，按一下以建立新的函式]。

    ![將函式部署到 Azure 建立應用程式](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. 輸入函式應用程式名稱，然後選擇適當的訂用帳戶/平台/資源群組/App Service 方案，您也可以在這裡建立資源群組/App Service 方案。 然後，將應用程式設定保持不變，按一下 [確定] 並等候幾分鐘以建立新的函式。 等待「正在建立新函式應用程式...」進度列消失。

    ![將函式部署至 Azure 建立應用程式精靈](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. 選取想要部署的函式應用程式 (系統會自動選取您剛建立的新函式應用程式)。 按一下 [執行] 以部署您的函式。

    ![螢幕擷取畫面顯示 [部署 Azure Functions] 對話方塊。](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![將函式部署到 Azure 記錄](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-azure-functions-from-idea"></a>從 IDEA 管理 Azure Functions

1. 您可以在 IDEA 中使用 *Azure Explorer* 管理函式；按一下 [函式應用程式]可在這裡看到所有功能。

    ![檢視總管中的函式](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. 按一下以選取其中一個函式，然後按一下滑鼠右鍵，選取 [顯示屬性] 以開啟詳細資料頁面。 

    ![顯示函式屬性](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. 在函式 *HttpTrigger-Java* 上按一下滑鼠右鍵，再選取 [觸發函式]，您會看到瀏覽器開啟觸發程序 UR。

    ![螢幕擷取畫面顯示具有 U R L 的瀏覽器。](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>在專案中新增更多 Azure Functions

1. 在套件 org.example.functions 上按一下滑鼠右鍵並選取 [新增 -> Azure 函式類別]。 

    ![在專案中新增函式 - 項目](media/functions-create-first-java-intellij/add-functions-entry.png)

1. 填入類別名稱 HttpTest，然後在 [建立函式類別] 精靈中選取 HttpTrigger，按一下 [確定] 以建立新的函式。

    ![螢幕擷取畫面：顯示 [建立函數類別] 對話方塊。](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![在專案中新增函式 - 輸出](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>清除函式

1. 刪除 Azure 總管中的 Azure Functions
      
      ![螢幕擷取畫面：顯示從內容功能表選取 [刪除]。](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>後續步驟

您已使用 HTTP 觸發的函式建立 Java 函式專案，並在本機電腦上執行，然後部署到 Azure。 現在，請藉由下列作業擴充您的函式...

> [!div class="nextstepaction"]
> [新增 Azure 儲存體佇列輸出繫結](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png