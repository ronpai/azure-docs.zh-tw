---
title: Azure 磁碟儲存體上的應用程式基準測試
description: 請參閱使用 Azure 進階儲存體磁片布建的標準 DS14 VM 基準測試範例。
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: how-to
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: cc17ce49517a7af22dcc357fa5f050d28a8ed551
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91279683"
---
# <a name="benchmark-your-application-on-azure-disk-storage"></a>Azure 磁碟儲存體上的應用程式基準測試

效能評定的過程中會在應用程式上模擬不同工作負載，並測量應用程式在每個工作負載上達到的效能。 使用[為高效能而設計](../premium-storage-performance.md)一文中描述的步驟。 您可以在裝載應用程式的 VM 上執行效能評定工具，以判斷應用程式利用進階儲存體可達成的效能層級。 在本文中，我們針對以 Azure 進階儲存體磁碟佈建的標準 DS14 VM，提供效能評定範例。

我們分別在 Windows 和 Linux 上使用一般效能評定工具 Iometer 和 FIO。 這些工具會繁衍多個執行緒來模擬類似實際執行的工作負載，並測量系統效能。 您也可以使用這些工具來設定參數，例如區塊大小和佇列深度，您通常無法在應用程式中變更這些參數。 針對不同類型的應用程式工作負載，這可讓您在以高階磁碟佈建的高延展性 VM 上，更靈活地發揮最大效能。 若要深入了解每個效能評定工具，請參閱 [Iometer](http://www.iometer.org/) 和 [FIO](http://freecode.com/projects/fio)。

若要執行下列範例，請建立標準 DS14 VM，並將 11 個進階儲存體磁碟連接至 VM。 在 11 個磁碟中，將 10 個磁碟的主機快取設定為「無」，並將它們串接成一個稱為 NoCacheWrites 的磁碟區。 在剩下的磁碟上，將主機快取設定為「唯讀」，並使用此磁碟建立一個稱為 CacheReads 的磁碟區。 使用此設定，您可以看到標準 DS14 VM 發揮最大的讀取和寫入效能。 如需使用進階磁碟建立 DS14 VM 的詳細步驟，請移至[高效能設計為高效能而設計](../premium-storage-performance.md)。

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>後續步驟

繼續閱讀我們 [針對高效能設計的](../premium-storage-performance.md)文章。

在該文章中，您會建立類似于原型的現有應用程式的檢查清單。 您可以使用效能評定工具，在原型應用程式上模擬工作負載並測量效能。 這樣做可讓您判斷哪一個磁碟供應項目可符合或超越您的應用程式效能需求。 然後，您可以對實際執行的應用程式運用相同的指導方針。