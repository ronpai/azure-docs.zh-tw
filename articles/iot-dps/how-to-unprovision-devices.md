---
title: 取消布建使用 Azure IoT 中樞裝置布建服務布建的裝置
description: '如何取消布建使用 Azure IoT 中樞裝置布建服務布建的裝置 (DPS) '
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 5683d40e9565068c6cd79eedb08b036eab2c54cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531384"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>如何取消佈建先前自動佈建的裝置 

您可能會發現，有時必須將先前已透過裝置佈建服務自動佈建的裝置取消佈建。 比方說，裝置可能會售出或移至不同的 IoT 中樞，或者可能遺失、遭竊或發生其他遭破壞的狀況。 

一般而言，取消佈建裝置包含兩個步驟：

1. 從您的佈建服務取消註冊裝置，以避免未來自動佈建該裝置。 根據您要暫時或永久撤銷存取權，您可以停用或刪除註冊項目。 針對使用 X.509 證明的裝置，您可以停用/刪除現有註冊群組階層中的項目。  
 
   - 若要了解如何取消註冊裝置，請參閱[如何從 Azure IoT 中樞裝置佈建服務中取消註冊裝置](how-to-revoke-device-access-portal.md)。
   - 若要了解如何以程式設計方式使用其中一種佈建服務 SDK 來取消註冊裝置，請參閱[使用服務 SDK 管理裝置註冊](how-to-manage-enrollments-sdks.md)。

2. 從您的 IoT 中樞取消登錄裝置，以避免未來產生通訊和資料轉送。 同樣地，您可以針對已佈建裝置的 IoT 中樞，暫時停用或永久刪除其身分識別登錄中的裝置項目。 請參閱[停用裝置](/azure/iot-hub/iot-hub-devguide-identity-registry#disable-devices)以深入了解停用作業。 請在 [Azure 入口網站](https://portal.azure.com)中參閱 IoT 中樞資源的「裝置管理/IoT 裝置」。

取消佈建裝置的確切步驟取決於其證明機制，以及其適用的註冊項目與您的佈建服務。 下列各節將根據註冊和證明類型提供程序概觀。

## <a name="individual-enrollments"></a>個別註冊
使用 TPM 證明或 X.509 證明搭配分葉憑證的裝置會透過個別的註冊項目進行佈建。 

若要取消佈建具有個別註冊的裝置： 

1. 從您的佈建服務取消註冊裝置：

   - 對於使用 TPM 證明的裝置，刪除個別註冊項目以永久撤銷裝置對佈建服務的存取權，或停用該項目以暫時撤銷其存取權。 
   - 對於使用 X.509 證明的裝置，您可以刪除或停用項目。 不過請注意，如果您針對使用 X.509 的裝置刪除個別註冊，而且該裝置信任鏈結中的單一憑證有已啟用的註冊群組存在，則該裝置可以重新註冊。 對於這類裝置而言，停用註冊項目可能更安全。 不論裝置的其中一個簽署憑證是否有已啟用的註冊群組存在，這麼做都能防止裝置重新註冊。

2. 在佈建裝置之 IoT 中樞的身分識別登錄中，停用或刪除該裝置。 


## <a name="enrollment-groups"></a>註冊群組
藉由 X.509 證明，也可透過註冊群組佈建裝置。 註冊群組是使用簽署憑證 (中繼或根 CA 憑證) 來加以設定，若裝置的憑證鏈結中具有該憑證，註冊群組會控制裝置對佈建服務的存取權限。 若要深入瞭解註冊群組和 x.509 憑證與布建服務的相關資訊，請參閱 [x.509 憑證證明](concepts-x509-attestation.md)。 

若要檢視已透過註冊群組佈建的裝置清單，您可以檢視註冊群組的詳細資料。 如此便能輕鬆了解每個裝置佈建到哪個 IoT 中樞。 若要檢視裝置清單： 

1. 登入 Azure 入口網站，然後在左側功能表上按一下 [所有資源]****。
2. 按一下資源清單中的佈建服務。
3. 在佈建服務中，按一下 [管理註冊]****，然後選取 [註冊群組]**** 索引標籤。
4. 按一下註冊群組將之開啟。

   ![在入口網站中檢視註冊群組項目](./media/how-to-unprovision-devices/view-enrollment-group.png)

對於註冊群組，有兩個案例要加以考慮：

- 若要取消佈建所有透過註冊群組佈建的裝置：
  1. 停用註冊群組，不允許其簽署憑證。 
  2. 請使用該註冊群組的佈建裝置清單，從個別的 IoT 中樞身分識別登錄中停用或刪除每個裝置。 
  3. 從個別的 IoT 中樞停用或刪除所有裝置之後，可以選擇是否要刪除註冊群組。 不過請注意，如果您刪除註冊群組，而憑證鏈結之中一個或多個更上層裝置的簽署憑證有已啟用的註冊群組，這些裝置可以重新註冊。 

- 若要從註冊群組取消佈建單一裝置：
  1. 為其分葉 (裝置) 憑證建立已停用的個別註冊。 如此會撤銷該裝置的佈建服務存取權限，但是仍允許鏈結之中具有註冊群組簽署憑證的其他裝置進行存取。 請勿刪除裝置已停用的個別註冊。 否則會使得裝置透過註冊群組重新註冊。 
  2. 使用該註冊群組的已佈建裝置清單來尋找佈建裝置的 IoT 中樞，再從該中樞的身分識別登錄停用或刪除裝置。 
