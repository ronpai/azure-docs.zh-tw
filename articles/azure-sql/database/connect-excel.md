---
title: 使用 Excel 進行連接
description: 瞭解如何將 Microsoft Excel 連接到 Azure SQL Database 或 Azure SQL 受控執行個體中的資料庫。 將資料匯入 Excel 中進行報告和資料探索。
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 304ad51bc40c8165c2dc11bb70287491e3d6dfb9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91444042"
---
# <a name="connect-excel-to-a-database-in-azure-sql-database-or-azure-sql-managed-instance-and-create-a-report"></a>將 Excel 連接到 Azure SQL Database 或 Azure SQL 受控執行個體中的資料庫，並建立報表
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

您可以將 Excel 連接到資料庫，然後根據資料庫中的值匯入資料及建立資料表和圖表。 在本教學課程中，您將設定 Excel 與資料庫資料表之間的連接、儲存可存放 Excel 資料和連接資訊的檔案，然後根據資料庫值建立樞紐分析圖。

開始之前，您必須先建立資料庫。 如果您沒有帳戶，請參閱在 [Azure SQL Database 中建立資料庫](single-database-create-quickstart.md) ，並 [建立伺服器層級 IP 防火牆](firewall-create-server-level-portal-quickstart.md) ，以在幾分鐘內讓範例資料啟動並執行。

在本文中，您會將該文章中的範例資料匯入 Excel 中，但是您可以依照類似的步驟並使用您自己的資料來執行。

您也會需要 Excel。 本文使用 [Microsoft Excel 2016](https://products.office.com/)。

## <a name="connect-excel-and-load-data"></a>連接 Excel 和載入資料

1. 若要將 Excel 連接到 SQL Database 中的資料庫，請開啟 Excel，然後建立新的活頁簿，或開啟現有的 Excel 活頁簿。
2. 在頁面頂端的功能表列中，依序選取 [資料]**** 索引標籤、[取得資料]****、[從 Azure]，然後選取 [從 Azure SQL Database]****。

   ![選取資料來源：將 Excel 連接到 SQL Database。](./media/connect-excel/excel_data_source.png)

3. 在 [ **SQL Server 資料庫**] 對話方塊的 [伺服器名稱] 中，輸入您要連接的**伺服器名稱**<*servername* > **. database.windows.net**。 例如，**msftestserver.database.windows.net**。 選擇性地輸入您的資料庫名稱。 選取 [確定]**** 以開啟認證視窗。

   ![連接到資料庫伺服器對話方塊](./media/connect-excel/server-name.png)

4. 在 [ **SQL Server 資料庫** ] 對話方塊中，選取左側的 [ **資料庫** ]，然後輸入您要連接之伺服器的 **使用者名稱** 和 **密碼** 。 選取 [連線]**** 以開啟 [導覽器]****。

   ![輸入伺服器名稱和登入認證](./media/connect-excel/connect-to-server.png)

   > [!TIP]
   > 視您的網路環境而定，您可能無法連線，如果伺服器不允許來自您用戶端 IP 位址的流量，您可能會失去連線。 移至 [Azure 入口網站](https://portal.azure.com/)，按一下 SQL Server，按一下您的伺服器中，按一下設定下的防火牆並新增您的用戶端 IP 位址。 如需詳細資訊，請參閱 [如何設定防火牆設定](firewall-configure.md) 。

5. 在 [導覽器]**** 中，從清單選取所要使用的資料庫，選取所要使用的資料表或檢視 (我們選擇 **vGetAllCategories**)，然後選取 [載入]**** 以將資料從您的資料庫移至 Excel 試算表。

    ![選取資料庫和資料表。](./media/connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>將資料匯入 Excel 中並建立樞紐分析圖

建立連線之後，您有數種資料載入選項可選擇。 例如，下列步驟會根據在 SQL Database 的資料庫中找到的資料來建立樞紐分析表。

1. 遵循上一節的步驟，但這次不選取 [載入]****，而是改為選取 [載入]**** 下拉式清單的 [載入至]****。
2. 接下來，選取您要如何在活頁簿中檢視此資料。 我們選擇 [樞紐分析圖]****。 您也可以選擇建立**新工作表** 或**將此資料加入至資料模型**。 如需資料模型的詳細資訊，請參閱[在 Excel 中建立資料模型](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B)。

    ![在 Excel 中選擇資料的格式](./media/connect-excel/import-data.png)

    工作表現在有空白的樞紐分析表和圖表。
3. 在 [樞紐分析表欄位] **** 之下，選取所有您要檢視的欄位的核取方塊。

    ![設定資料庫報告。](./media/connect-excel/power-pivot-results.png)

> [!TIP]
> 如果您想要將其他 Excel 活頁簿和工作表連線到資料庫，請選取 [資料]**** 索引標籤，然後選取 [最近使用的來源]**** 以啟動 [最近使用的來源]**** 對話方塊。 從對話方塊中的清單選擇您已建立的連線，然後按一下 [開啟]****。
> ![最近使用的來源對話方塊](./media/connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>使用 .odc 檔案建立永久連線

若要永久儲存連線詳細資料，您可以建立 .odc 檔案，並且讓此連線在 [現有連線]**** 對話方塊中成為可選取的選項。

1. 在頁面頂端的功能表列中，選取 [資料]**** 索引標籤，然後選取 [現有連線]**** 以啟動 [現有連線]**** 對話方塊。
   1. 選取 [瀏覽其他]**** 以開啟 [選取資料來源]**** 對話方塊。
   2. 選取 [+NewSqlServerConnection.odc]**** 檔案，然後選取 [開啟]**** 以開啟 [資料連線精靈]****。

      ![新增連接對話方塊](./media/connect-excel/new-connection.png)

2. 在 [資料連線精靈]**** 中，輸入您的伺服器名稱和 SQL Database 認證。 選取 [下一步]  。
   1. 從下拉式清單中選取包含您資料的資料庫。
   2. 選取您感興趣的資料表或檢視。 我們選擇 vGetAllCategories。
   3. 選取 [下一步]  。

      ![資料連線精靈](./media/connect-excel/data-connection-wizard.png)

3. 在資料連線精靈的下一個畫面中，選取您的檔案位置、[檔案名稱]**** 和 [易記名稱]****。 您也可以選擇在檔案中儲存密碼，但這可能會使您的資料公開給不必要的存取。 準備好時，選取 [完成]****。

    ![儲存資料連線](./media/connect-excel/save-data-connection.png)

4. 選取您要匯入資料的方式。 我們選擇以樞紐分析表進行。 您也可以選取 [屬性]**** 以修改連線的屬性。 準備好時，選取 [確定]****。 如果您未選擇在檔案中儲存密碼，便會看到輸入認證的提示。

    ![匯入資料](./media/connect-excel/import-data2.png)

5. 展開 [資料]**** 索引標籤並選取 [現有連線]****，確認您的新連線是否已儲存。

    ![現有連線](./media/connect-excel/existing-connection.png)

## <a name="next-steps"></a>後續步驟

* 瞭解如何 [使用 SQL Server Management Studio 進行連接和查詢](connect-query-ssms.md) ，以進行先進的查詢和分析。
* 了解 [彈性集區](elastic-pool-overview.md)的優點。
* 瞭解如何 [建立 web 應用程式，以連接到後端的 Azure SQL Database](../../app-service/app-service-web-tutorial-dotnet-sqldatabase.md)。
