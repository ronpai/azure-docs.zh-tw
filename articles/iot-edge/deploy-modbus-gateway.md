---
title: 使用閘道來轉譯 Modbus 通訊協定 - Azure IoT Edge | Microsoft Docs
description: 允許使用 Modbus TCP 的裝置藉由建立 IoT Edge 閘道裝置來與 Azure IoT 中樞通訊
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: d07a1d1ab0d3b1f4315c09f1c403126139b34612
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043896"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>透過 IoT Edge 裝置閘道連線 Modbus TCP 裝置

如果您想要將使用 Modbus TCP 或 RTU 通訊協定的 IoT 裝置連線到 Azure IoT 中樞，您可以使用 IoT Edge 裝置作為閘道。 閘道裝置會從您的 Modbus 裝置讀取資料，然後使用支援的通訊協定將該資料傳達至雲端。

![Modbus 裝置透過 IoT Edge 閘道連接到 IoT 中樞](./media/deploy-modbus-gateway/diagram.png)

本文說明如何針對 Modbus 模組建立自己的容器映像 (您也可以使用預先建置的範例)，然後將它部署到將作為閘道的 IoT Edge 裝置。

本文假設您使用 Modbus TCP 通訊協定。 如需有關如何設定此模組以支援 Modbus RTU 的詳細資訊，請參閱 GitHub 上的 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。

## <a name="prerequisites"></a>先決條件

* Azure IoT Edge 裝置。 如需有關如何設定的逐步解說，請參閱[在 Windows 或 Linux 上部署 Azure IoT Edge](quickstart.md) 。 [Linux](quickstart-linux.md)
* IoT Edge 裝置的主索引鍵連接字串。
* 支援 Modbus TCP 的實體或模擬 Modbus 裝置。 您將需要知道其 IPv4 位址。

## <a name="prepare-a-modbus-container"></a>準備 Modbus 容器

如果您想要測試 Modbus 閘道功能，Microsoft 有可供您使用的範例模組。 您可以從 Azure Marketplace、 [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)或使用映射 URI 來存取模組 `mcr.microsoft.com/azureiotedge/modbus:1.0` 。

如果您想要建立自己的模組並針對您的環境進行自訂，GitHub 上有提供開放原始碼 [Azure IoT Edge Modbus 模組](https://github.com/Azure/iot-edge-modbus)專案。 請遵循該專案中的方針，建立您自己的容器映像。 若要建立容器映射，請參閱 [在 Visual Studio 中開發 c # 模組](./how-to-visual-studio-develop-module.md) ，或 [在 Visual Studio Code 中開發模組](how-to-vs-code-develop-module.md)。 這些文章提供有關建立新模組和將容器映射發佈到登錄的指示。

## <a name="try-the-solution"></a>試用解決方案

本節將逐步解說如何將 Microsoft 的範例 Modbus 模組部署到您的 IoT Edge 裝置。

1. 在 [Azure 入口網站](https://portal.azure.com/)上，移至您的 IoT 中樞。

2. 移至 [IoT Edge]****，然後按一下您的 IoT Edge 裝置。

3. 選取 [設定模組]。

4. 在 [ **IoT Edge 模組** ] 區段中，新增 Modbus 模組：

   1. 按一下 [ **新增** ] 下拉式清單，然後選取 [ **Marketplace 模組**]。
   2. 搜尋 `Modbus` 並選取 Microsoft 所 **MODBUS 的 TCP 模組** 。
   3. 系統會自動為您的 IoT 中樞設定模組，並顯示在 IoT Edge 模組清單中。 路由也會自動設定。 選取 [檢閱 + 建立]****。
   4. 檢查部署資訊清單，然後選取 [ **建立**]。

5. 選取清單中的 Modbus 模組， `ModbusTCPModule` 然後選取 [模組對應項 **設定** ] 索引標籤。自動填入模組對應項所需屬性的必要 JSON。

6. 在 JSON 中尋找 **SlaveConnection** 屬性，並將其值設定為 Modbus 裝置的 IPv4 位址。

7. 選取 [更新]。

8. 選取 [ **審核 + 建立**]，檢查部署，然後選取 [ **建立**]。

9. 返回裝置的詳細資料頁面，選取 [重新整理]****。 您應該會看到新的 `ModbusTCPModule` 模組與 IoT Edge 執行時間一起執行。

## <a name="view-data"></a>檢視資料

查看透過 Modbus 模組的資料：

```cmd/sh
iotedge logs modbus
```

您也可以使用 (Visual Studio Code 先前的 Azure IoT 工具組擴充功能) 的 [Azure IoT 中樞擴充](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 功能，來查看裝置所傳送的遙測。

## <a name="next-steps"></a>後續步驟

* 若要深入瞭解 IoT Edge 裝置如何作為閘道，請參閱 [建立作為透明閘道的 IoT Edge 裝置](./how-to-create-transparent-gateway.md)。
* 如需 IoT Edge 模組如何運作的詳細資訊，請參閱 [瞭解 Azure IoT Edge 模組](iot-edge-modules.md)。