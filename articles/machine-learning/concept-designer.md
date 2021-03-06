---
title: 使用設計工具建立 ML 模型
titleSuffix: Azure Machine Learning
description: 了解構成 Azure Machine Learning 設計工具的術語、概念和工作流程。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: c92351131a60b571a102b85f7d11b79ab29d939e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90883125"
---
# <a name="what-is-azure-machine-learning-designer"></a>什麼是 Azure Machine Learning 設計工具？ 


Azure Machine Learning 設計工具可讓您以視覺化方式連結[資料集](#datasets) 和互動式畫布上的[模組](#module)，進而建立機器學習模型。 若要了解如何開始使用設計工具，請參閱[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)來試用設計工具

![Azure Machine Learning 設計工具範例](./media/concept-designer/designer-drag-and-drop.gif)

設計工具會使用您的 Azure Machine Learning [工作區](concept-workspace.md)來組織共用的資源，例如：

+ [管線](#pipeline)
+ [資料集](#datasets)
+ [計算資源](#compute)
+ [已註冊的模型](concept-azure-machine-learning-architecture.md#models)
+ [已發佈的管線](#publish)
+ [即時端點](#deploy)

## <a name="model-training-and-deployment"></a>模型訓練和部署

設計工具會提供您建置、測試及部署機器學習模型的視覺化畫布。 透過設計工具，您可以：

+ 將[資料集](#datasets)和[模組](#module)拖放到畫布上。
+ 連接模組以建立 [管線草稿](#pipeline-draft)。
+ 使用 Azure Machine Learning 工作區中的計算資源，提交[管線執行](#pipeline-run)。
+ 將您的**訓練管線**轉換為**推斷管線**。
+ 將管線[發佈](#publish)至 REST**管線端點**，以提交使用不同參數和資料集執行的新管線。
    + 發佈**訓練管線**，以在變更參數和資料集時重複使用單一管線來訓練多個模型。
    + 發佈**批次推斷管線**，以使用先前訓練的模型對新資料進行預測。
+ 將**即時推斷管線**[部署](#deploy)至即時端點，以即時對新資料進行預測。

![設計工具中的訓練、批次推斷和即時推斷工作流程圖](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>管線

[管線](concept-azure-machine-learning-architecture.md#ml-pipelines)是由您所連接的資料集和分析模組所組成。 管線有許多用途：您可以建立用來訓練單一模型的管線，或是用來訓練多個模型的管線。 您可以建立管線，以即時或批次方式進行預測，或建立只會清除資料的管線。 管線可讓您重複使用工作及組織您的專案。

### <a name="pipeline-draft"></a>管線草稿

當您在設計工具中編輯管線時，您的進度會儲存為**管線草稿**。 您可以在任何時間點編輯管線草稿，例如新增或移除模組、設定計算目標和建立參數等等。

有效的管線具有下列特性：

* 資料集只能連結到模組。
* 模組只能連結到資料集或其他模組。
* 模組的所有輸入連接埠必須有資料流程的某些連線。
* 必須設定每個模組的所有必要參數。

當您準備好要執行管線草稿時，即可提交管線執行。

### <a name="pipeline-run"></a>管線執行

每次執行管線時，管線的設定和其結果都會以**管線執行**的形式儲存在工作區中。 您可以回到任何管線執行，檢查其是否有問題，以進行疑難排解或稽核。 **複製**管線執行，以建立新的管線草稿來進行編輯。

管線執行會分組到各個[實驗](concept-azure-machine-learning-architecture.md#experiments)，以組織執行歷程記錄。 您可以設定每個管線執行的實驗。 

## <a name="datasets"></a>資料集

機器學習資料集可讓您輕鬆地存取和使用您的資料。 設計工具中包含數個範例資料集，可供您進行實驗。 您可以視需要來[註冊](how-to-create-register-datasets.md)更多資料集。

## <a name="module"></a>模組

模組是指您在資料上可執行的演算法。 設計工具有數個模組，範圍從資料輸入函數到定型、評分和驗證程式。

模組可能有一組參數可用來設定模組的內部演算法。 當您在畫布上選取模組時，模組的參數會顯示在畫布右邊的 [屬性] 窗格中。 您可以在此窗格中修改參數來調整模型。 您可以在設計工具中設定個別模組的計算資源。 

:::image type="content" source="./media/concept-designer/properties.png"alt-text="模組屬性":::


如需瀏覽可用機器學習服務演算法程式庫的協助，請參閱[演算法和模組參考概觀](algorithm-module-reference/module-reference.md)。 如需演算法選擇的協助，請參閱 [Azure Machine Learning 演算法功能提要](algorithm-cheat-sheet.md)。

## <a name="compute-resources"></a><a name="compute"></a> 計算資源

使用您工作區中的計算資源來執行管線，並將部署的模型裝載為即時端點或管線端點 (適用於批次推斷)。 支援的計算目標包括：

| 計算目標 | 訓練 | 部署 |
| ---- |:----:|:----:|
| Azure Machine Learning Compute | ✓ | |
| Azure Machine Learning 計算執行個體 | ✓ | |
| Azure Kubernetes Service | | ✓ |

計算目標會附加至您的 [Azure Machine Learning 工作區](concept-workspace.md)。 您可以在 [Azure Machine Learning Studio](https://ml.azure.com) 的工作區中管理計算目標。

## <a name="deploy"></a>部署

若要執行即時推斷，您必須將管線部署為**即時端點**。 即時端點會在外部應用程式和您的評分模型之間建立介面。 即時端點的呼叫會將預測結果即時傳回給應用程式。 若要對即時端點進行呼叫，請傳遞部署端點時所建立的 API 金鑰。 端點會以 REST 為基礎，這是一種常見的 Web 程式設計專案架構。

即時端點必須部署到 Azure Kubernetes Service 叢集。

若要了解如何部署您的模型，請參閱[教學課程：透過設計工具部署機器學習模型](tutorial-designer-automobile-price-deploy.md)。

## <a name="publish"></a>發佈

您也可以將管線發佈到**管線端點**。 與即時端點類似，管線端點可讓您使用 REST 呼叫從外部應用程式提交新的管線執行。 不過，您無法使用管線端點即時傳送或接收資料。

已發佈的管線具有彈性，可用於訓練或重新訓練模型、[執行批次推斷](how-to-run-batch-predictions-designer.md)、處理新資料等等。 您可以將多個管線發佈至單一管線端點，並指定要執行哪個管線版本。

已發佈的管線會在每個模組管線草稿中所定義的計算資源上執行。

設計工具會建立與 SDK 相同的 [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py&preserve-view=true) 物件。

## <a name="next-steps"></a>後續步驟

* 透過以下教學課程，了解預測性分析和機器學習的基礎概念：[教學課程：使用設計工具預測汽車價格](tutorial-designer-automobile-price-train-score.md)來試用設計工具
* 了解如何修改現有的[設計工具範例](samples-designer.md)，以符合您的需求。
