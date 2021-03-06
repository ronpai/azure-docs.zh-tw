---
title: Azure 資訊安全中心的安全性警示 | Microsoft Docs
description: 本主題會說明什麼是安全性警示，以及 Azure 資訊安全中心提供的各種不同類型。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1b71e8ad-3bd8-4475-b735-79ca9963b823
ms.service: security-center
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: memildin
ms.openlocfilehash: 7f5f920f14a556d63f433b36658d7b265d51a28d
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340371"
---
# <a name="security-alerts-in-azure-security-center"></a>Azure 資訊安全中心的安全性警示

在 [安全性中心] 中，有許多不同的資源類型都有各種不同的警示。 資訊安全中心會針對部署在 Azure 上的資源產生警示，也會針對內部部署和部署於混合式雲端環境的資源產生警示。

安全性警示是由 advanced 偵測所觸發，且僅適用于 Azure Defender。 有免費試用版可用。 您可以從 [ **定價 & 設定** ] 頁面進行升級。 深入瞭解資訊 [安全中心的定價](https://azure.microsoft.com/pricing/details/security-center/)。

## <a name="respond-to-todays-threats"></a>回應現今的威脅<a name="respond-threats"> </a>

威脅態勢在過去 20 年來已有重大變更。 在過去，公司通常只需擔心網站遭到大多想要查看「他們能做些什麼」的個別攻擊者竄改。 現今的攻擊者更加複雜且有組織性。 他們通常會有特定的財務和策略性目標。 他們也有更多的資源可供利用，因為它們可能是由民族國家或組織型犯罪所資助。

現實環境不斷變化，造就了前所未有的專業攻擊者等級。 他們不再對 Web 竄改感興趣。 他們現在對於竊取資訊、金融帳戶及私人資料深感興趣。他們可利用這些資料在開放性市場上獲取金錢利益，或利用特定的商務、政治及軍事立場。 相較於具有財務目標的攻擊者，破壞網路以便對基礎結構和人員造成傷害的攻擊者更加令人關切。

回應時，組織通常會部署各種點解決方案，而這類解決方案會藉由尋找已知的攻擊簽章，進而著重於防禦企業周邊或端點。 這些解決方案通常會產生大量的低精確度警示，其需要資訊安全分析師進行分級和調查。 大部分的組織沒有回應這些警示所需的時間和專業知識 – 這麼多警示尚未解決。  

此外，攻擊者的方法已經演變，可以破壞許多以簽章為基礎的防禦措施並且[適應雲端環境](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/)。 需要新的方法，才能更快速地找出新興威脅並加速偵測和回應。

## <a name="what-are-security-alerts-and-security-incidents"></a>什麼是安全性警示和安全性事件？ 

**警示**是當偵測到您資源上的威脅時，資訊安全中心產生的通知。 資訊安全中心會將警示排定優先順序並列出，並且一併提供迅速調查問題所需的資訊。 資訊安全中心也會提供如何修復攻擊的建議。

**安全性事件**是相關警示的集合，而非單獨列出每個警示。 資訊安全中心會使用[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)，將不同的警示和低精確度訊號相互關聯，結合成安全性事件。

透過事件的使用，資訊安全中心就能為您提供統一的檢視畫面，顯示攻擊活動及所有相關警示。 此種檢視方式，可讓您快速了解攻擊者採取的動作，以及受到影響的資源。 如需詳細資訊，請參閱[雲端智慧警示相互關聯](security-center-alerts-cloud-smart.md)。



## <a name="how-does-security-center-detect-threats"></a>資訊安全中心如何偵測威脅？ <a name="detect-threats"> </a>

Microsoft 資訊安全研究人員會持續監視威脅。 由於我們在雲端和內部部署中有全球存在，我們可以存取一組廣泛的遙測。 廣泛且多樣化的資料集集合可讓我們在內部部署消費者和企業產品，以及我們的線上服務之間，探索新的攻擊模式和趨勢。 因此，資訊安全中心可以在攻擊者發行新的和日益複雜的攻擊時，快速地更新其偵測演算法。 這種方法可協助您跟上瞬息萬變的威脅環境。

為偵測出真正的威脅並降低誤判為真的狀況，資訊安全中心會自動收集、分析及整合來自 Azure 資源與網路的記錄資料。 此外也會使用連線的合作夥伴解決方案，例如防火牆和端點保護解決方案。 資訊安全中心會分析這些資訊 (通常是來自多個來源的相互關聯資訊) 以識別威脅。

![資訊安全中心的資料收集和呈現](./media/security-center-alerts-overview/security-center-detection-capabilities.png)

資訊安全中心會運用進階安全性分析，其遠勝於以簽章為基礎的方法。 巨量資料和 [機器學習](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) 技術突破可用來評估整個雲端網狀架構的事件 – 使用手動方式來偵測無法識別的威脅，以及預測攻擊的演化。 這些安全性分析包括︰

* **整合性威脅情報**：Microsoft 有大量全域威脅情報。 遙測會從多個來源（例如 Azure、Microsoft 365、Microsoft CRM online、Microsoft Dynamics AX、outlook.com、MSN.com、Microsoft 數位犯罪單位 (DCU) 和 Microsoft 安全性回應中心 (MSRC) ）流入。 研究人員也會收到主要雲端服務提供者之間共用的威脅情報資訊，以及來自其他協力廠商的摘要內容。 Azure 資訊安全中心可以使用這項資訊來警示您來自已知不良執行者的威脅。

* **行為分析**：行為分析是一種可分析及比較資料與一組已知模式的技術。 不過，這些模式並非簡單的簽章。 它們會透過已套用至大型資料集的複雜機器學習演算法來決定。 它們也能透過專業分析師仔細分析惡意行為來判定。 Azure 資訊安全中心可以使用行為分析，根據虛擬機器記錄、虛擬網路裝置記錄、網狀架構記錄和其他來源的分析，來識別遭到入侵的資源。

* **異常偵測**：Azure 資訊安全中心也會使用異常偵測來識別威脅。 相較於行為分析 (這取決於衍生自大型資料集的已知模式)，異常偵測更加「個人化」，且著重於您的部署專用的基準。 機器學習服務適用於判斷您部署的正常活動，然後產生規則來定義可能代表安全性事件的極端狀況。

## <a name="how-are-alerts-classified"></a>如何分類警示？

資訊安全中心會指派警示的嚴重性，有助於安排每項警示的優先處理順序，如此一來，當資源遭到入侵時就能立即著手應對。 嚴重性的依據，在於資訊安全中心對於據以發出警示的發現結果或分析結果有多少信心，以及認定導致警示的活動背後存在惡意意圖的把握程度。

> [!NOTE]
> 警示嚴重性在入口網站及 2019 年 1 月 1 日之前版本的 REST API 中的顯示方式不同。 如果您使用的是版本較舊的 API，請升級以得到一致的體驗，如下所述。

- **高：** 您的資源很可能遭到破壞。 您應立即加以了解。 資訊安全中心在不良意圖和用來發出警示的調查結果方面都具有高信賴度。 例如，偵測有已知惡意工具 (例如 Mimikatz，這是常用來竊取認證的工具) 正在執行的警示。
- **中：** 這可能是表示資源遭到破壞的可疑活動。
資訊安全中心在分析或調查結果方面具有中信賴度，在不良意圖方面的信賴度則為中至高。 這些通常會是以機器學習或異常偵測為基礎的偵測。 例如，從異常位置登入的嘗試。
- **低：** 這有可能是良性確判或已遭封鎖的攻擊。
   * 資訊安全中心沒有足夠的信賴度可確定意圖是否屬惡意以及活動是否無害。 例如，清除記錄有可能是攻擊者為了嘗試隱藏其追蹤記錄而執行的動作，但在許多情況下，這也是管理員所執行的例行性作業。
   * 資訊安全中心在封鎖攻擊時通常不會通知您，除非是我們建議您查看的特殊案例。 
- **告知性：** 當您向下切入至安全性事件時，或使用 REST API 與特定警示識別碼時，才會看到資訊警示。 事件通常由多個警示組成，其中有些警示單獨來看可能僅具參考價值，但若與其他警示交互參照，則可能有進一步詳查的價值。 

## <a name="continuous-monitoring-and-assessments"></a>持續監視與評量

Azure 資訊安全中心的優勢在於 Microsoft 上上下下都設有資訊安全研究和資料科學小組，負責持續監視威脅態勢中的變化。 這包括下列計劃︰

* **威脅情報監視**︰威脅情報包含關於現有或新興威脅的機制、指標、影響和可採取動作的建議。 安全性社群會共用此資訊，而 Microsoft 會持續監視來自內部和外部來源的威脅情報摘要。
* **訊號共用**︰共用和分析 Microsoft 的資訊安全小組對於各種雲端和內部部署服務、伺服器及用戶端端點裝置組合所提供的深入見解。
* **Microsoft 資訊安全專家**︰持續與擅長特殊資訊安全領域 (例如鑑識與 Web 攻擊偵測) 的 Microsoft 團隊攜手合作。
* **偵測微調**︰對真正的客戶資料集執行演算法，而資訊安全研究人員會與客戶一起驗證結果。 真肯定和誤判可用來縮小機器學習演算法的範圍。

結合上述努力終於獲得全新及改善的偵測功能，您因而立即受惠 – 不需採取任何的動作。

## <a name="export-alerts"></a>匯出警示

您有許多選項可供您在「安全性中心」之外查看您的警示，包括：

- [警示] 儀表板上的 [**下載 csv 報表**] 可提供一次性匯出至 CSV。
- 從定價的**連續匯出**& 設定可讓您將安全性警示和建議的串流設定至 Log Analytics 工作區和事件中樞。 [深入瞭解連續匯出](continuous-export.md)
- **Azure Sentinel 連接器** 會將 Azure Defender 警示從 Azure 資訊安全中心串流至 Azure Sentinel。 [深入瞭解如何使用 Sentinel 連接 Azure 資訊安全中心](../sentinel/connect-azure-security-center.md)


## <a name="next-steps"></a>後續步驟

在本文中，您了解到資訊安全中心中不同類型的警示。 如需詳細資訊，請參閱

- **Azure 活動記錄中的安全性警示**：Azure 入口網站有提供安全性警示，也可透過程式設計方式取得，此外安全性警示和事件還會在 [Azure 活動記錄](../azure-monitor/platform/activity-log.md#view-the-activity-log)中以事件的形式接受稽核。 如需事件結構描述的詳細資訊，請參閱 [Azure 活動記錄中的安全性警示](https://go.microsoft.com/fwlink/?linkid=2114113) (英文)