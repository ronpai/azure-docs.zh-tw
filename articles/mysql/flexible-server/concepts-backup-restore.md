---
title: 適用於 MySQL 的 Azure 資料庫彈性伺服器中的備份與還原
description: 瞭解適用於 MySQL 的 Azure 資料庫彈性伺服器的備份與還原概念
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 44cfe9bc6cd357cc0c649cecd022d3955bb5a2ce
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545866"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>適用於 MySQL 的 Azure 資料庫彈性的伺服器 (預覽版備份和還原) 

> [!IMPORTANT] 
> 適用於 MySQL 的 Azure 資料庫 - 彈性伺服器目前處於公開預覽狀態。

適用於 MySQL 的 Azure 資料庫彈性的伺服器，會自動建立伺服器備份，並安全地將其儲存在區域內的本機多餘儲存體中。 備份可以用來將伺服器還原至某個時間點。 備份和還原可保護資料免於意外損毀或刪除，是商務持續性策略中不可或缺的一部分。

## <a name="backup-overview"></a>備份概觀

有彈性的伺服器會取得資料檔案的快照集備份，並將它們儲存在本機多餘的儲存體中。 伺服器也會執行交易記錄備份，並將它們儲存在本機多餘的儲存體中。 在您設定的備份保留期限內，這些備份可讓您將伺服器還原至任何時間點。 預設的備份保留期限是七天。 您可以選擇性地將資料庫備份設定為1到35天。 所有備份都會使用 AES 256 位加密來加密待用儲存的資料。

這些備份檔案無法匯出。 備份只能用在彈性伺服器的還原作業。 您也可以使用 MySQL 用戶端的 [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) 來複製資料庫。

## <a name="backup-frequency"></a>備份頻率

彈性伺服器上的備份是以快照集為基礎。 建立伺服器之後，會立即排程第一次完整快照集備份。 第一次完整快照集備份會保留為伺服器的基礎備份。 後續的快照集備份只是差異備份。

差異快照集備份一天至少會執行一次。 差異快照集備份不會依固定排程執行。 差異快照集備份會每隔 24 小時執行一次，除非 MySQL 中的二進位記錄在上次差異備份之後超過 50 GB。 一天內最多允許六個差異快照集。 交易記錄備份會每五分鐘執行一次。

## <a name="backup-retention"></a>備份保留

資料庫備份會儲存在本機的多餘儲存體 (LRS) --儲存在區域內的三個複本。 備份會根據伺服器上的備份保留期限設定來保留。 您可以選取1到35天的保留期限，預設保留期間為7天。 您可以使用 Azure 入口網站更新備份設定，在伺服器建立期間或之後設定保留期限。

備份保留期限會控制執行時間點還原作業所需的時間，因為它是以可用的備份為基礎。 您也可以從還原的觀點，將備份保留期限視為修復時段。 在備份保留期限內執行時間點還原所需的所有備份都會保留在備份儲存體中。 例如-如果備份保留期限設定為七天，則會將修復時間範圍視為過去七天。 在此案例中，會保留過去七天內還原伺服器所需的所有備份。 當備份保留期間為七天，資料庫快照集與交易記錄備份會儲存過去八天 (在視窗) 之前的1天。

## <a name="backup-storage-cost"></a>備份儲存體成本

彈性伺服器可提供高達100% 的已布建伺服器儲存體作為備份儲存體，且不會產生額外費用。 使用的任何額外備份儲存體都會依每月 GB 收費。 例如，如果您布建的伺服器具有 250 GB 的儲存體，您可以免費使用 250 GB 的儲存體進行伺服器備份。 如果每日備份使用量是25GB，則您最多可以有10天的免費備份儲存體。 超過 250 GB 的備份所耗用的儲存體會依 [定價模式](https://azure.microsoft.com/pricing/details/mysql/)收費。

您可以使用 Azure 入口網站中可用 Azure 監視器的「 [備份儲存體](../concepts-monitoring.md) 」計量來監視伺服器所耗用的備份儲存體。 **備份儲存體** 使用的計量代表所有資料庫備份所使用的儲存體總和，以及根據為伺服器設定的備份保留期限保留的記錄備份。 不論資料庫大小總計，伺服器上頻繁的交易活動可能會導致備份儲存體使用量增加。

控制備份儲存體成本的主要方法是設定適當的備份保留期限。 您可以選取介於1到35天之間的保留期限。

> [!IMPORTANT]
> 從主資料庫伺服器進列區域冗余高可用性設定中設定之資料庫伺服器的備份，是因為快照備份的負擔最低。

> [!IMPORTANT]
> 彈性的伺服器目前不支援地理區域冗余備份。

## <a name="point-in-time-restore"></a>時間點還原

在適用於 MySQL 的 Azure 資料庫彈性的伺服器中，執行還原時間點會在與來源伺服器相同的區域中，從彈性伺服器的備份建立新的伺服器。 它會以源伺服器的計算層設定、虛擬核心數目、儲存體大小、備份保留期限和備份冗余選項來建立。 此外，也會繼承來源伺服器的標記和設定，例如虛擬網路和防火牆。 還原完成後，您可以變更還原伺服器的計算和儲存層、設定和安全性設定。

> [!NOTE]
> 有兩個伺服器參數會重設為預設值 (而不會從主伺服器複製到還原作業之後) 
> *   time_zone-此值會設定為預設值系統
> *   event_scheduler-已還原伺服器上的 event_scheduler 設定為 OFF

時間點還原適用於多種案例。 一些常見的使用案例包括：
-   當使用者不小心刪除資料庫中的資料時
-   使用者卸載重要的資料表或資料庫
-   由於應用程式缺失，使用者應用程式不小心覆寫有不良資料的良好資料。

您可以透過 [Azure 入口網站](how-to-restore-server-portal.md)，在最新的還原點和自訂還原點之間進行選擇。

-   **最新的還原點** ：最新的還原點可協助您將伺服器還原到在來源伺服器上執行的最後一個備份。 還原的時間戳記也會顯示在入口網站上。 此選項適用于將伺服器快速還原為最新的狀態。
-   **自訂還原點** ：這可讓您選擇為此彈性伺服器定義的保留週期內的任何時間點。 此選項適用于在從使用者錯誤復原的精確時間點還原伺服器。

預估的復原時間取決於數個因素，包括資料庫大小、交易記錄備份大小、SKU 的計算大小，以及還原的時間。 交易記錄復原是還原過程中最耗時的一部分。 如果選擇的還原時間較接近完整或差異快照集備份排程，則還原的速度會比較快，因為交易記錄應用程式很基本。 為了估計伺服器的正確復原時間，強烈建議您在環境中進行測試，因為它具有太多環境特定變數。

> [!IMPORTANT]
> 如果您要還原以區域冗余高可用性設定的彈性伺服器，還原的伺服器將會在與主伺服器相同的區域和區域中進行設定，並部署為非 HA 模式中的單一彈性伺服器。 請參閱彈性伺服器的 [區域冗余高可用性](concepts-high-availability.md) 。

> [!IMPORTANT]
> 已刪除的伺服器 **無法** 還原。 如果您刪除伺服器，所有屬於該伺服器的資料庫也會一併刪除，且無法復原。 若要在部署後避免伺服器資源遭到意外刪除或非預期的變更，系統管理員可以利用[管理鎖定](../../azure-resource-manager/management/lock-resources.md)。

## <a name="perform-post-restore-tasks"></a>執行還原之後的工作

從 **最新的還原點** 或 **自訂還原點** 復原機制還原之後，您應該執行下列工作，讓您的使用者和應用程式恢復運作：

-   如果新伺服器的目的是要取代源伺服器，請將用戶端和用戶端應用程式重新導向至新的伺服器。
-   確定有適當的伺服器層級防火牆和虛擬網路規則可供使用者連接。
-   確定已備妥適當的登入和資料庫層級許可權。
-   適當地設定警示。

## <a name="next-steps"></a>下一步

-   瞭解 [商務持續性](./concepts-business-continuity.md)
-   深入瞭解 [區域冗余高可用性](./concepts-high-availability.md)
-   瞭解[備份和](./concepts-backup-restore.md)復原
