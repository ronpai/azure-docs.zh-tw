---
title: '將資料複製到 Azure Synapse Analytics (先前為 SQL 資料倉儲) '
description: 瞭解如何使用 Azure Data Factory，將資料複製到 Azure Synapse Analytics (先前的 SQL 資料倉儲) 或從中複製資料
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: b7324115c880fb1ee4d5a1730a3b84a289cee4b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89490134"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-formerly-sql-data-warehouse-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure Synapse Analytics (先前的 SQL 資料倉儲) ）
> [!div class="op_single_selector" title1="選取您目前使用的 Data Factory 服務版本："]
> * [第 1 版](data-factory-azure-sql-data-warehouse-connector.md)
> * [第 2 版 (目前的版本)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> 本文適用於 Data Factory 第 1 版。 如果您使用目前版本的 Data Factory 服務，請參閱第 [2 版中的 Azure Synapse Analytics 連接器](../connector-azure-sql-data-warehouse.md)。

本文說明如何使用 Azure Data Factory 中的「複製活動」，將資料移入/移出 Azure Synapse Analytics。 它是以「 [資料移動活動](data-factory-data-movement-activities.md) 」一文為基礎，提供使用複製活動來移動資料的一般總覽。

> [!TIP]
> 若要達到最佳效能，請使用 PolyBase 將資料載入 Azure Synapse Analytics。 如需詳細資訊，請參閱 [使用 PolyBase 將資料載入 Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics)小節。 如需使用案例的逐步解說，請參閱[使用 Azure Data Factory 在 15 分鐘內將 1 TB 載入至 Azure Synapse Analytics](data-factory-load-sql-data-warehouse.md)。

## <a name="supported-scenarios"></a>支援的案例
您可以將資料 **從 Azure Synapse Analytics** 複製到下列資料存放區：

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

您可以從下列資料存放區將資料複製 **到 Azure Synapse Analytics**：

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> 從 SQL Server 或 Azure SQL Database 複製資料到 Azure Synapse Analytics 時，如果資料表不存在於目的地存放區中，Data Factory 可以使用來源資料存放區中資料表的架構，在 Synapse Analytics 中自動建立資料表。 請參閱[自動建立資料表](#auto-table-creation)以取得詳細資料。

## <a name="supported-authentication-type"></a>支援的驗證類型
Azure Synapse Analytics 連接器支援基本驗證。

## <a name="getting-started"></a>開始使用
您可以使用不同的工具/Api，建立具有複製活動的管線，以將資料移進/移出 Azure Synapse Analytics。

若要建立將資料複製到 Azure Synapse Analytics 或從中複製資料的管線，最簡單的方式是使用「複製資料嚮導」。 請參閱 [教學課程：使用 Data Factory 將資料載入 Synapse 分析](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md) ，以取得使用複製資料嚮導建立管線的快速逐步解說。

您也可以使用下列工具來建立管線： **Visual Studio**、 **Azure PowerShell**、 **Azure Resource Manager 範本**、 **.net API**和 **REST API**。 請參閱「 [複製活動」教學](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) 課程，以取得使用複製活動建立管線的逐步指示。

不論您是使用工具還是 API，都需執行下列步驟來建立將資料從來源資料存放區移到接收資料存放區的管線：

1. 建立 **資料**處理站。 資料處理站可包含一或多個管線。 
2. 建立**連結服務**，將輸入和輸出資料存放區連結到資料處理站。 例如，如果您要將資料從 Azure blob 儲存體複製到 Azure Synapse Analytics，您會建立兩個連結服務，以將您的 Azure 儲存體帳戶和 Azure Synapse Analytics 連結至您的 data factory。 針對 Azure Synapse Analytics 特定的連結服務屬性，請參閱 [連結的服務屬性](#linked-service-properties) 一節。 
3. 建立 **資料集** 以代表複製作業的輸入和輸出資料。 在上一個步驟所述的範例中，您會建立資料集來指定 blob 容器和包含輸入資料的資料夾。 此外，您會建立另一個資料集來指定 Azure Synapse Analytics 中的資料表，以保存從 blob 儲存體複製的資料。 針對 Azure Synapse Analytics 特定的資料集屬性，請參閱 [資料集屬性](#dataset-properties) 區段。
4. 建立具有複製活動的 **管線** ，該活動會採用資料集做為輸入，並使用資料集做為輸出。 在稍早所述的範例中，您會使用 BlobSource 作為來源，以及使用 SqlDWSink 作為複製活動的接收器。 同樣地，如果您要從 Azure Synapse Analytics 複製到 Azure Blob 儲存體，您可以在複製活動中使用 SqlDWSource 和 BlobSink。 如需 Azure Synapse Analytics 特定的複製活動屬性，請參閱 [複製活動屬性](#copy-activity-properties) 一節。 如需有關如何使用資料存放區作為來源或接收器的詳細資訊，按一下上一節中資料存放區的連結。

使用精靈時，精靈會自動為您建立這些 Data Factory 實體 (已連結的服務、資料集及管線) 的 JSON 定義。 使用工具/API (.NET API 除外) 時，您需使用 JSON 格式來定義這些 Data Factory 實體。 如需 Data Factory 用來將資料複製到 Azure Synapse Analytics 或從中複製資料的 JSON 定義範例，請參閱本文的 [json 範例](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) 一節。

下列各節提供 JSON 屬性的相關詳細資料，這些屬性是用來定義 Azure Synapse Analytics 特定的 Data Factory 實體：

## <a name="linked-service-properties"></a>連結服務屬性
下表提供 Azure Synapse Analytics 連結服務專屬 JSON 元素的描述。

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| type |Type 屬性必須設定為： **>azuresqldw** |是 |
| connectionString |指定連接到 connectionString 屬性 Azure Synapse Analytics 實例所需的資訊。 僅支援基本驗證。 |是 |

> [!IMPORTANT]
> 設定 [Azure SQL Database 防火牆](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)和資料庫伺服器，以[允許 Azure 服務存取伺服器](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)。 此外，如果您要將資料從 Azure 外部（包括從使用 data factory 閘道的內部部署資料來源）複製到 Azure Synapse Analytics，請為傳送資料至 Azure Synapse Analytics 的電腦設定適當的 IP 位址範圍。

## <a name="dataset-properties"></a>資料集屬性
如需定義資料集的區段和屬性完整清單，請參閱[建立資料集](data-factory-create-datasets.md)一文。 資料集 JSON 的結構、可用性和原則等區段類似於所有的資料集類型 (SQL Azure、Azure Blob、Azure 資料表等)。

每個資料集類型的 typeProperties 區段都不同，可提供資料存放區中資料的位置相關資訊。 **AzureSqlDWTable** 類型資料集的 **typeProperties** 區段具有下列屬性：

| 屬性 | 描述 | 必要 |
| --- | --- | --- |
| tableName |連結服務所參考之 Azure Synapse Analytics 資料庫中的資料表或視圖名稱。 |是 |

## <a name="copy-activity-properties"></a>複製活動屬性
如需定義活動的區段和屬性完整清單，請參閱[建立管線](data-factory-create-pipelines.md)一文。 屬性 (例如名稱、描述、輸入和輸出資料表，以及原則) 適用於所有類型的活動。

> [!NOTE]
> 複製活動只會採用一個輸入，而且只產生一個輸出。

而活動的 typeProperties 區段中可用的屬性會隨著每個活動類型而有所不同。 就「複製活動」而言，這些屬性會根據來源和接收器的類型而有所不同。

### <a name="sqldwsource"></a>SqlDWSource
如果來源類型為 **SqlDWSource**，則 **typeProperties** 區段可使用下列屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| sqlReaderQuery |使用自訂查詢來讀取資料。 |SQL 查詢字串。 例如：select * from MyTable。 |否 |
| sqlReaderStoredProcedureName |從來源資料表讀取資料的預存程序名稱。 |預存程序的名稱。 最後一個 SQL 陳述式必須是預存程序中的 SELECT 陳述式。 |否 |
| storedProcedureParameters |預存程序的參數。 |名稱/值組。 參數的名稱和大小寫必須符合預存程序參數的名稱和大小寫。 |否 |

如果針對 SqlDWSource 指定了 **sqlReaderQuery** ，複製活動就會針對 Azure Synapse Analytics 來源執行此查詢以取得資料。

或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。

如果您未指定 sqlReaderQuery 或 >sqlreaderstoredprocedurename，則會使用資料集 JSON 的結構區段中定義的資料行來建立查詢，以針對 Azure Synapse Analytics 執行。 範例： `select column1, column2 from mytable`. 如果資料集定義沒有結構，則會從資料表中選取所有資料行。

#### <a name="sqldwsource-example"></a>SqlDWSource 範例

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**預存程序定義：**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>管線
**SqlDWSink** 支援下列屬性：

| 屬性 | 描述 | 允許的值 | 必要 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |指定要讓「複製活動」執行的查詢，以便清除特定分割的資料。 如需詳細資訊，請參閱 [可重複性](#repeatability-during-copy)一節。 |查詢陳述式。 |否 |
| allowPolyBase |指出是否使用 PolyBase (適用的話) 而不是使用 BULKINSERT 機制。 <br/><br/> **使用 PolyBase 是將資料載入 Azure Synapse Analytics 的建議方式。** 如需條件約束和詳細資料，請參閱 [使用 PolyBase 將資料載入 Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics) 一節。 |True <br/>FALSE (預設值) |否 |
| polyBaseSettings |可以在 **allowPolybase** 屬性設定為 **true** 時指定的一組屬性。 |&nbsp; |否 |
| rejectValue |指定在查詢失敗前可以拒絕的資料列數目或百分比。 <br/><br/>在[CREATE EXTERNAL TABLE (transact-sql) ](https://msdn.microsoft.com/library/dn935021.aspx)主題的**引數**區段中，深入瞭解 PolyBase 的拒絕選項。 |0 (預設值)、1、2、… |否 |
| rejectType |指定要將 rejectValue 選項指定為常值或百分比。 |值 (預設值)、百分比 |否 |
| rejectSampleValue |決定在 PolyBase 重新計算已拒絕的資料列百分比之前，所要擷取的資料列數目。 |1、2、… |是，如果 **rejectType** 是 **percentage** |
| useTypeDefault |指定當 PolyBase 從文字檔擷取資料時，如何處理分隔符號文字檔中遺漏的值。<br/><br/>從 [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) 的＜引數＞一節深入了解這個屬性。 |True/False (預設值為 False) |否 |
| writeBatchSize |當緩衝區大小達到 writeBatchSize 時，將資料插入 SQL 資料表中 |整數 (資料列數目) |否 (預設值：10000) |
| writeBatchTimeout |在逾時前等待批次插入作業完成的時間。 |時間範圍<br/><br/> 範例：「00:30:00」(30 分鐘)。 |否 |

#### <a name="sqldwsink-example"></a>SqlDWSink 範例

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>使用 PolyBase 將資料載入 Azure Synapse Analytics
使用 **[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** 是將大量資料載入高輸送量 Azure Synapse Analytics 的有效方式。 使用 PolyBase 而不是預設的 BULKINSERT 機制，即可看到輸送量大幅提升。 請參閱[複製效能參考編號](data-factory-copy-activity-performance.md#performance-reference)了解詳細的比較。 如需使用案例的逐步解說，請參閱[使用 Azure Data Factory 在 15 分鐘內將 1 TB 載入至 Azure Synapse Analytics](data-factory-load-sql-data-warehouse.md)。

* 如果您的來源資料是在 **Azure Blob 或 Azure Data Lake 存放區**中，且格式與 polybase 相容，您可以使用 polybase 直接複製到 Azure Synapse Analytics。 請參閱**[使用 PolyBase 直接複製](#direct-copy-using-polybase)** 了解詳細資料。
* 如果您的來源資料存放區與格式不受 PolyBase 支援，您可以改為使用[使用 PolyBase 分段複製](#staged-copy-using-polybase)**** 功能。 它也能透過將資料自動轉換為 PolyBase 相容的格式，並將資料儲存在 Azure Blob 儲存體中，來提供更佳的輸送量。 然後，它會將資料載入 Azure Synapse Analytics 中。

`allowPolyBase`如下列範例所示，將屬性設為**true** ，Azure Data Factory 使用 PolyBase 將資料複製到 Azure Synapse Analytics。 當您將 allowPolyBase 設定為 true 時，您可以使用 `polyBaseSettings` 屬性群組來指定 PolyBase 特定屬性。 如需您可搭配 polyBaseSettings 使用之屬性的詳細資訊，請參閱 [SqlDWSink](#sqldwsink) 一節。

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>使用 PolyBase 直接複製
Azure Synapse Analytics PolyBase 直接支援 Azure Blob 和 Azure Data Lake 存放區 (使用服務主體) 作為來源，並使用特定的檔案格式需求。 如果您的來源資料符合本節所述的準則，您可以直接從來源資料存放區複製到使用 PolyBase 的 Azure Synapse Analytics。 否則，您可以使用 [使用 PolyBase 分段複製](#staged-copy-using-polybase)。

> [!TIP]
> 若要有效率地將資料從 Data Lake Store 複製到 Azure Synapse Analytics，深入瞭解 [Azure Data Factory 讓您可以更輕鬆且方便地在搭配使用 Data Lake Store 與 Azure Synapse Analytics 時發現資料的見解](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)。

如果不符合需求，Azure Data Factory 會檢查設定，並自動切換回適用於資料移動的 BULKINSERT 機制。

1. **來源連結服務** 的類型為： **AzureStorage** 或 **具有服務主體驗證的 >azuredatalakestore**。
2. 「輸入資料集」**** 的類型為：**AzureBlob** 或 **AzureDataLakeStore**，而 `type` 屬性底下的格式類型為 **OrcFormat**、**ParquetFormat** 或具備下列設定的 **TextFormat**：

   1. `rowDelimiter` 必須為 **\n**。
   2. `nullValue` 設定為「空字串」**** ("") 或將 `treatEmptyAsNull` 設定為 **true**。
   3. `encodingName` 設定為 **utf-8**，也就是「預設」**** 值。
   4. 未指定 `escapeChar`、`quoteChar`、`firstRowAsHeader` 和 `skipLineCount`。
   5. `compression` 可以是「無壓縮」、**GZip** 或 **Deflate**。

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. 管線中複製活動的 **BlobSource** 或 **AzureDataLakeStore** 之下沒有 `skipHeaderLineCount` 設定。
4. 管線中複製活動的 **SqlDWSink** 之下沒有 `sliceIdentifierColumnName` 設定。 (PolyBase 保證所有資料都已更新，或在單一執行未更新任何項目。 若要達到「重複性」****，您可以使用 `sqlWriterCleanupScript`)。
5. 目前沒有任何 `columnMapping` 使用於相關聯的複製活動。

### <a name="staged-copy-using-polybase"></a>使用 PolyBase 分段複製
當來源資料不符合上一節所介紹的準則時，您可以透過暫時暫存 Azure Blob 儲存體啟用複製資料， (無法進階儲存體) 。 在此情況下，Azure Data Factory 會自動執行資料轉換，以符合 PolyBase 的資料格式需求，然後使用 PolyBase 將資料載入至 Azure Synapse Analytics，並在最後從 Blob 儲存體清除暫存資料。 如需透過暫存 Azure Blob 複製資料通常如何運作的詳細資訊，請參閱 [分段複製](data-factory-copy-activity-performance.md#staged-copy) 。

> [!NOTE]
> 使用 PolyBase 和預備將資料從內部部署資料存放區複製到 Azure Synapse Analytics 時，如果您的資料管理閘道版本低於2.4，則您的閘道電腦上需要 JRE (JAVA Runtime Environment) ，以將來源資料轉換成適當的格式。 建議您將閘道升級為最新的版本，以避免這類相依性。
>

若要使用此功能，請建立 [Azure 儲存體連結服務](data-factory-azure-blob-connector.md#azure-storage-linked-service)，這是指具有過渡 Blob 儲存體的 Azure 儲存體帳戶，然後針對複製活動指定 `enableStaging` 和 `stagingSettings` 屬性，如下列程式碼所示：

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>使用 PolyBase 時的最佳作法
下列各節提供在 [Azure Synapse Analytics 的最佳作法](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)中提及的其他最佳做法。

### <a name="required-database-permission"></a>必要的資料庫權限
若要使用 PolyBase，您必須使用使用者將資料載入 Azure Synapse Analytics 具有目標資料庫的「 [控制」許可權](https://msdn.microsoft.com/library/ms191291.aspx) 。 達到此目標的其中一個方法是將該使用者新增為 "db_owner" 角色的成員。 依照[本節](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)了解如何進行這項動作。

### <a name="row-size-and-data-type-limitation"></a>資料列大小和資料類型限制
PolyBase 載入被限制為只能載入小於 **1 MB**，且不能載入至 VARCHR(MAX)、NVARCHAR(MAX) 或 VARBINARY(MAX) 的資料列。 請參閱[這裡](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)。

如果您的來源資料包含大於 1 MB 的資料列，建議您將來源資料表垂直分割成數個小型資料表，而每個資料表的最大資料列大小均不超過限制。 然後可以使用 PolyBase 載入較小的資料表，並在 Azure Synapse Analytics 中合併在一起。

### <a name="azure-synapse-analytics-resource-class"></a>Azure Synapse Analytics 資源類別
若要達到最佳的可能輸送量，請考慮透過 PolyBase 將更大的資源類別指派給用來將資料載入 Azure Synapse Analytics 的使用者。 遵循[變更使用者資源類別範例](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)，了解如何執行這項作業。

### <a name="tablename-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中的 tableName
下表提供的範例是關於如何針對各種結構描述和資料表名稱組合，在資料集 JSON 中指定 **tableName** 屬性。

| DB 結構描述 | 資料表名稱 | tableName JSON 屬性 |
| --- | --- | --- |
| dbo |MyTable |MyTable 或 dbo.MyTable 或 [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable 或 [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] 或 [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

如果您看到下列錯誤，可能是您為 tableName 屬性指定的值有問題。 請參閱資料表，以正確的方式指定 tableName JSON 屬性的值。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>包含預設值的資料行
Data Factory 中的 PolyBase 功能目前只接受與目標資料表中相同的資料行數目。 假設您有內含四個資料行的資料表，且其中一個資料行已使用預設值進行定義。 輸入資料應該仍會包含四個資料行。 提供 3 個資料行的輸入資料集會產生類似下列訊息的錯誤︰

```
All columns of the table must be specified in the INSERT BULK statement.
```
NULL 值是一種特殊形式的預設值。 如果資料行可為 null，該資料行的輸入資料 (在 Blob 中) 可以是空的 (不能在輸入資料集中遺漏)。 PolyBase 會在 Azure Synapse Analytics 中插入 Null。

## <a name="auto-table-creation"></a>自動建立資料表
如果您使用「複製嚮導」將資料從 SQL Server 或 Azure SQL Database 複製到 Azure Synapse Analytics，而對應至來源資料表的資料表不存在於目的地存放區中，則 Data Factory 可以使用來源資料表架構，在資料倉儲中自動建立資料表。

Data Factory 會以和來源資料存放區中的資料表相同的名稱，在目的地存放區中建立資料表。 資料行的資料類型會根據下列類型對應來選擇。 它會視需要執行類型轉換，以修正來源和目的地存放區之間所有不相容的情況。 它也會使用循環配置資源資料表散發。

| 來源 SQL Database 資料行類型 | 目的地 Azure Synapse Analytics 資料行類型 (大小限制)  |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| bit | bit |
| Decimal | Decimal |
| 數值 | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binary | Binary |
| Varbinary | Varbinary (最多 8000) |
| Date | Date |
| Datetime | Datetime |
| DateTime2 | DateTime2 |
| 時間 | 時間 |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (最多 8000) |
| NText | NVarChar (最多 4000) |
| 映像 | VarBinary (最多 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (最多 8000) |
| NVarChar | NVarChar (最多 4000) |
| Xml | Varchar (最多 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Azure Synapse Analytics 的類型對應
如同 [資料移動活動](data-factory-data-movement-activities.md) 一文所述，複製活動會使用下列 2 個步驟的方法，執行自動類型轉換，將來源類型轉換成接收類型：

1. 從原生來源類型轉換成 .NET 類型
2. 從 .NET 類型轉換成原生接收類型

從 Azure Synapse Analytics 將資料移至 & 時，會使用下列從 SQL 類型到 .NET 類型的對應，反之亦然。

對應與 [ADO.NET 的 SQL Server 資料類型對應](https://msdn.microsoft.com/library/cc716729.aspx)相同。

| SQL Server Database Engine 類型 | .NET Framework 類型 |
| --- | --- |
| BIGINT |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

您也可以在複製活動定義中，將來自來源資料集的資料行與來自接收資料集的資料行對應。 如需詳細資料，請參閱[在 Azure Data Factory 中對應資料集資料行](data-factory-map-columns.md)。

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>將資料複製到 Azure Synapse Analytics 以及從中複製資料的 JSON 範例
下列範例提供您可用來使用 [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 或 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)建立管線的範例 JSON 定義。 它們示範如何將資料複製到 Azure Synapse Analytics 和 Azure Blob 儲存體。 不過，您可以在 Azure Data Factory 中使用複製活動，從任何來源 **直接** 將資料複製到 [這裡](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 所說的任何接收器。

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>範例：將資料從 Azure Synapse Analytics 複製到 Azure Blob
此範例會定義下列 Data Factory 實體：

1. [AzureSqlDW](#linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [AzureSqlDWTable](#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [SqlDWSource](#copy-activity-properties) 和 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將時間序列 (每小時、每天等 ) 資料從 Azure Synapse Analytics 資料庫中的資料表複製到 blob。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure Synapse Analytics 連結服務：**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob 儲存體連結服務：**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Synapse Analytics 輸入資料集：**

此範例假設您已在 Azure Synapse Analytics 中建立資料表 "MyTable"，其中包含時間序列資料的資料行（稱為 "timestampcolumn) "）。

設定 "external": "true" 會通知 Data Factory 服務：這是 Data Factory 外部的資料集而且不是由 Data Factory 中的活動所產生。

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Blob 輸出資料集：**

資料會每小時寫入至新的 Blob (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑。 資料夾路徑會使用開始時間的年、月、日和小時部分。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**管線中使用 SqlDWSource 和 BlobSink 的複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **SqlDWSource**，而 **sink** 類型設為 **BlobSink**。 針對 **SqlReaderQuery** 屬性指定的 SQL 查詢會選取過去一小時內要複製的資料。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> 在此範例中，已為 SqlDWSource 指定 **sqlReaderQuery** 。 複製活動會針對 Azure Synapse Analytics 來源執行此查詢以取得資料。
>
> 或者，您可以藉由指定 **sqlReaderStoredProcedureName** 和 **storedProcedureParameters** (如果預存程序接受參數) 來指定預存程序。
>
> 如果您未指定 sqlReaderQuery 或 >sqlreaderstoredprocedurename，則會使用資料集 JSON 的結構區段中定義的資料行來建立查詢 (select column1、column2 from mytable) ，以針對 Azure Synapse Analytics 執行。 如果資料集定義沒有結構，則會從資料表中選取所有資料行。
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>範例：將資料從 Azure Blob 複製到 Azure Synapse Analytics
此範例會定義下列 Data Factory 實體：

1. [AzureSqlDW](#linked-service-properties)類型的連結服務。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)類型的連結服務。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 類型的輸入[資料集](data-factory-create-datasets.md)。
4. [AzureSqlDWTable](#dataset-properties) 類型的輸出[資料集](data-factory-create-datasets.md)。
5. 具有使用 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 和 [SqlDWSink](#copy-activity-properties) 之複製活動的[管線](data-factory-create-pipelines.md)。

此範例會每小時將時間序列資料從 Azure blob 複製到 Azure Synapse Analytics 資料庫中的資料表 (每小時、每天等 ) 。 範例後面的各節會說明這些範例中使用的 JSON 屬性。

**Azure Synapse Analytics 連結服務：**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob 儲存體連結服務：**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure Blob 輸入資料集：**

每小時從新的 Blob 挑選資料 (頻率：小時，間隔：1)。 根據正在處理之配量的開始時間，以動態方式評估 Blob 的資料夾路徑和檔案名稱。 資料夾路徑會使用開始時間的年、月及日部分，而檔案名稱則使用開始時間的小時部分。 "external"： "true" 設定會通知 Data Factory 服務，這是 data factory 外部的資料表，而不是由 data factory 中的活動所產生。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure Synapse Analytics 輸出資料集：**

此範例會在 Azure Synapse Analytics 中將資料複製到名為 "MyTable" 的資料表。 使用與您預期 Blob CSV 檔案要包含的資料行數目相同的資料行，在 Azure Synapse Analytics 中建立資料表。 此資料表會每小時加入新的資料列。

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**具有 BlobSource 和 SqlDWSink 的管線中複製活動：**

此管線包含複製活動，該活動已設定為使用輸入和輸出資料集並排定為每小時執行。 在管線 JSON 定義中，**source** 類型設為 **BlobSource**，而 **sink** 類型設為 **SqlDWSink**。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
如需逐步解說，請參閱 Azure Synapse Analytics 檔中的「在 [15 分鐘內將 1 TB 載入 Azure Synapse Analytics](data-factory-load-sql-data-warehouse.md) ，並使用 Azure Data Factory 和 [載入資料的 Azure Data Factory](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) 文章。

## <a name="performance-and-tuning"></a>效能和微調
請參閱[複製活動的效能及微調指南](data-factory-copy-activity-performance.md)一文，以了解在 Azure Data Factory 中會影響資料移動 (複製活動) 效能的重要因素，以及各種最佳化的方法。
