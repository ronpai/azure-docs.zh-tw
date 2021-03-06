---
title: 教學課程 - 使用 Azure 流量管理員設定子網路流量路由
description: 本教學課程說明如何設定流量管理員，以將流量從使用者子網路路由傳送到特定端點。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: duau
ms.openlocfilehash: 348f1d779f8ea67860726b8f8b7739921e7ad54a
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/19/2020
ms.locfileid: "92205943"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>教學課程：使用流量管理員根據使用者子網路將流量導向特定端點

本文說明如何設定子網路流量路由方法。 **子網路** 流量路由方法可讓您將一組 IP 位址範圍對應至特定端點，而當流量管理員收到要求時，會檢查要求的來源 IP，並傳回與其相關聯的端點。

在本教學課程中，使用子網路路由時，流量會根據使用者的查詢 IP 位址而路由傳送至內部網站或生產網站。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 在 IIS 上建立兩部執行基本網站的 VM
> * 建立兩部測試 VM 以檢視流量管理員的運作
> * 為執行 IIS 的 VM 設定 DNS 名稱
> * 建立流量管理員設定檔來根據使用者的子網路路由傳送流量
> * 在流量管理員設定檔中新增 VM 端點
> * 檢視流量管理員的運作

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

若要查看流量管理員的運作，本教學課程會要求您部署下列項目：

- 在不同 Azure 區域中執行的兩個基本網站，分別是 **美國東部** (作為內部網站) 和 **西歐** (作為生產網站)。
- 用於測試流量管理員的兩個測試 VM - 一個 VM 位於 **美國東部** ，另一個 VM 位於 **西歐** 。

測試 VM 可用來說明流量管理員如何根據使用者查詢的來源子網路，將使用者流量路由傳送至內部網站或生產網站。

### <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 入口網站：[https://portal.azure.com](https://portal.azure.com)。

### <a name="create-websites"></a>建立網站

在本節中，您會建立兩個網站執行個體，這兩個執行個體可在兩個 Azure 區域中為流量管理員設定檔提供兩個服務端點。 請執行下列步驟來建立這兩個網站：

1. 建立兩個 VM 來執行基本網站 - 一個位於 **美國東部** ，另一個位於 **西歐** 。
2. 在每個 VM 上安裝 IIS 伺服器並更新預設網站頁面，該頁面描述使用者在造訪網站時所連線的 VM 名稱。

#### <a name="create-vms-for-running-websites"></a>建立 VM 以供執行網站

在本節中，您會在 **美國東部** 和 **西歐** 的 Azure 區域中建立兩個 VM (myIISVMEastUS  和 myIISVMWestEurope  )。

1. 在 Azure 入口網站的左上角，選取 [建立資源]   > [計算]   > [Windows Server 2019 Datacenter]  。
2. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：

   - [訂用帳戶]   > [資源群組]  ：選取 [新建]  ，然後輸入 **myResourceGroupTM1** 。
   - **執行個體詳細資料** > **虛擬機器名稱** ：輸入 myIISVMEastUS  。
   - **執行個體詳細資料** > **區域** ：選取 [美國東部]  。
   - **系統管理員帳戶** > **使用者名稱** ：輸入您選擇的使用者名稱。
   - **系統管理員帳戶** > **密碼** ：輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。
   - **輸入連接埠規則** > **公用輸入連接埠** ：選取 [允許選取的連接埠]  。
   - **輸入連接埠規則** > **選取輸入連接埠** ：在下拉式方塊中選取 [RDP]  和 [HTTP]  。

3. 選取 [管理]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路功能]** ，然後選取 **[下一步：管理]** 。 在 [監視]  下，將 [開機診斷]  設定為 [關閉]  。
4. 選取 [檢閱 + 建立]  。
5. 檢閱設定，然後按一下 [建立]  。  
6. 請依照步驟來建立名為 myIISVMWestEurope  的第二個 VM，並將 [資源群組]  命名為 myResourceGroupTM2  、將 [地區]  設定為 [西歐]  ，然後將其他設定設成跟 myIISVMEastUS  一樣。
7. 可能需要數分鐘才會建立虛擬機器。 請等到這兩個虛擬機器都已建立，再繼續進行其餘步驟。

#### <a name="install-iis-and-customize-the-default-web-page"></a>安裝 IIS 和自訂預設網頁

在本節中，您會在兩部 VM ( *myIISVMEastUS* & *myIISVMWestEurope* ) 上安裝 IIS 伺服器，然後更新預設網站頁面。 自訂的網站頁面會顯示當您從網頁瀏覽器瀏覽網站時，您所連線的 VM 名稱。

1. 選取左側功能表中的 [所有資源]  ，然後從資源清單按一下 *myIISVMEastUS* ，其位於 *myResourceGroupTM1* 資源群組中。
2. 在 [概觀]  頁面上，按一下 [連線]  ，然後在 [連線至虛擬機器]  中選取 [下載 RDP 檔案]  。
3. 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]  。 輸入您在建立 VM 時指定的使用者名稱和密碼。 您可能需要選取 [其他選擇]  ，然後選取 [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。
4. 選取 [確定]  。
5. 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是]  或 [繼續]  以繼續進行連線。
6. 在伺服器桌面上，瀏覽至 [Windows 系統管理工具]  >[伺服器管理員]  。
7. 在 myIISVMEastUS  VM 上啟動 Windows PowerShell，並使用下列命令來安裝 IIS 伺服器及更新預設 htm 檔案。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. 關閉與 myIISVMEastUS  VM 的 RDP 連線。
9. 重複步驟 1-6，方法是與 myResourceGroupTM2  資源群組內的 myIISVMWestEurope  VM 建立 RDP 連線，以安裝 IIS 並自訂其預設網頁。
10. 在 myIISVMWestEurope  VM 上啟動 Windows PowerShell，並使用下列命令來安裝 IIS 伺服器及更新預設 htm 檔。

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>為執行 IIS 的 VM 設定 DNS 名稱

流量管理員會根據服務端點的 DNS 名稱來路由傳送使用者流量。 在本節中，您會設定 IIS 伺服器 myIISVMEastUS  和 myIISVMWestEurope  的 DNS 名稱。

1. 按一下左側功能表中的 [所有資源]  ，然後從資源清單選取 *myIISVMEastUS* ，其位於 *myResourceGroupTM1* 資源群組中。
2. 在 [概觀]  頁面的 [DNS 名稱]  底下，選取 [設定]  。
3. 在 [組態]  頁面的 [DNS 名稱] 標籤之下，新增唯一的名稱，然後選取 [儲存]  。
4. 針對 myResourceGroupTM2  資源群組中名為 myIISVMWestEurope  的 VM，重複步驟 1-3。

### <a name="create-test-vms"></a>建立測試 VM

在本節中，您會在每個 Azure 區域 ( **美國東部** 和 **西歐** ) 中建立 VM (myVMEastUS  和 myVMWestEurope  )。 您將使用這些 VM 來測試流量管理員如何根據使用者查詢的子網路，來路由使用者流量。

1. 在 Azure 入口網站的左上角，選取 [建立資源]   > [計算]   > [Windows Server 2019 Datacenter]  。
2. 在 [建立虛擬機器]  中，輸入或選取 [基本資訊]  索引標籤中的下列值：

   - [訂用帳戶]   > [資源群組]  ：選取 [myResourceGroupTM1]  。
   - **執行個體詳細資料** > **虛擬機器名稱** ：輸入 myVMEastUS  。
   - **執行個體詳細資料** > **區域** ：選取 [美國東部]  。
   - **系統管理員帳戶** > **使用者名稱** ：輸入您選擇的使用者名稱。
   - **系統管理員帳戶** > **密碼** ：輸入您選擇的密碼。 密碼長度至少必須有 12 個字元，而且符合[定義的複雜度需求](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)。
   - **輸入連接埠規則** > **公用輸入連接埠** ：選取 [允許選取的連接埠]  。
   - **輸入連接埠規則** > **選取輸入連接埠** ：在下拉式方塊中選取 [RDP]  。

3. 選取 [管理]  索引標籤，或選取 **[下一步：磁碟]** ，然後選取 **[下一步：網路功能]** ，然後選取 **[下一步：管理]** 。 在 [監視]  下，將 [開機診斷]  設定為 [關閉]  。
4. 選取 [檢閱 + 建立]  。
5. 檢閱設定，然後按一下 [建立]  。  
6. 請依照步驟來建立名為 myVMWestEurope  的第二個 VM，並將 [資源群組]  命名為 myResourceGroupTM2  、將 [地區]  設定為 [西歐]  ，然後將其他設定設成跟 myVMEastUS  一樣。
7. 可能需要數分鐘才會建立虛擬機器。 請等到這兩個虛擬機器都已建立，再繼續進行其餘步驟。

## <a name="create-a-traffic-manager-profile"></a>建立流量管理員設定檔

建立流量管理員設定檔，以便根據要求的來源 IP 傳回特定端點。

1. 在畫面的左上方，選取 [建立資源]   > [網路]   > [流量管理員設定檔]   > [建立]  。
2. 在 [建立流量管理員設定檔]  中，輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [建立]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 名稱                   | 此名稱在 trafficmanager.net 區域內必須是唯一的，而且會產生 DNS 名稱 trafficmanager.net，用以存取您的流量管理員設定檔。                                   |
    | 路由方法          | 選取 [子網路]  路由方法。                                       |
    | 訂用帳戶            | 選取您的訂用帳戶。                          |
    | 資源群組          | 選取 [現有]  ，然後輸入 *myResourceGroupTM1* 。 |
    | |                              |
    |

    ![建立流量管理員設定檔](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>新增流量管理員端點

新增兩個在 IIS 伺服器中執行的 VM，分別是 myIISVMEastUS  和 myIISVMWestEurope  ，以便根據使用者的查詢子網路路由傳送使用者流量。

1. 在入口網站的搜尋列中，搜尋您在上一節建立的流量管理員設定檔名稱，然後在顯示的結果中選取設定檔。
2. 在 [流量管理員設定檔]  的 [設定]  區段中，按一下 [端點]  ，然後按一下 [新增]  。
3. 輸入或選取下列資訊、接受其餘設定的預設值，然後選取 [確定]  ：

    | 設定                 | 值                                              |
    | ---                     | ---                                                |
    | 類型                    | Azure 端點                                   |
    | 名稱           | myInternalWebSiteEndpoint                                        |
    | 目標資源類型           | 公用 IP 位址                          |
    | 目標資源          | **選擇公用 IP 位址** 以顯示具有相同訂用帳戶下公用 IP 位址的資源清單。 在 [資源]  中，選取名為 *myIISVMEastUS-ip* 的公用 IP 位址。 這是美國東部 IIS 伺服器 VM 的公用 IP 位址。|
    |  子網路路由設定    |   新增 *myVMEastUS* 測試 VM 的 IP 位址。 任何源自此 VM 的使用者查詢都會導向至 myInternalWebSiteEndpoint  。    |

4. 重複步驟 2 和 3，以針對與 IIS 伺服器 VM myIISVMWestEurope  相關聯的公用 IP 位址 myIISVMWestEurope-ip  ，新增另一個名為 myProdWebsiteEndpoint  的端點。 針對 [子網路路由設定]  ，新增測試 VM *myVMWestEurope* 的 IP 位址。 來自此測試 VM 的任何使用者查詢都會路由傳送至端點 - myProdWebsiteEndpoint  。
5. 這兩個端點新增完畢後，它們會顯示在 [流量管理員設定檔]  中，而且監視狀態是 [線上]  。

## <a name="test-traffic-manager-profile"></a>測試流量管理員設定檔

在本節中，您會測試流量管理員如何將使用者流量從指定子網路路由傳送至特定端點。 若要查看運作中的流量管理員，請完成下列步驟：

1. 決定流量管理員設定檔的 DNS 名稱。
2. 檢視流量管理員的運作，如下所示：
    - 從位於 **美國東部** 區域的測試 VM ( *myVMEastUS* )，在網頁瀏覽器中瀏覽至您流量管理員設定檔的 DNS 名稱。
    - 從位於 **西歐** 區域的測試 VM (myVMWestEurope  )，在網頁瀏覽器中瀏覽至您流量管理員設定檔的 DNS 名稱。

### <a name="determine-dns-name-of-traffic-manager-profile"></a>決定流量管理員設定檔的 DNS 名稱

在本教學課程中，為了簡單起見，您可使用流量管理員設定檔的 DNS 名稱來瀏覽網站。

您可以決定流量管理員設定檔的 DNS 名稱，如下所示：

1. 在入口網站的搜尋列中，搜尋您在上一節建立的 **流量管理員設定檔** 名稱。 在顯示的結果中，按一下流量管理員設定檔。
2. 按一下 [概觀]  。
3. [流量管理員設定檔]  會顯示新建立之流量管理員設定檔的 DNS 名稱。 在生產部署中，您會使用 DNS CNAME 記錄，將虛名網域名稱設定為指向流量管理員網域名稱。

### <a name="view-traffic-manager-in-action"></a>檢視流量管理員的運作

在本節中，您可以看到流量管理員的運作。

1. 選取左側功能表中的 [所有資源]  ，然後從資源清單按一下 *myVMEastUS* ，其位於 *myResourceGroupTM1* 資源群組中。
2. 在 [概觀]  頁面上，按一下 [連線]  ，然後在 [連線至虛擬機器]  中選取 [下載 RDP 檔案]  。
3. 開啟所下載的 rdp 檔案。 如果出現提示，請選取 [連接]  。 輸入您在建立 VM 時指定的使用者名稱和密碼。 您可能需要選取 [其他選擇]  ，然後選取 [使用不同的帳戶]  ，以指定您在建立 VM 時輸入的認證。
4. 選取 [確定]  。
5. 您可能會在登入過程中收到憑證警告。 如果您收到警告，請選取 [是]  或 [繼續]  以繼續進行連線。
6. 在 VM *myVMEastUS* 的網頁瀏覽器中，輸入流量管理員設定檔的 DNS 名稱，以檢視您的網站。 因為 VM myVMEastUS  IP 位址與端點 myInternalWebsiteEndpoint  相關聯，網頁瀏覽器會啟動測試網站伺服器 - myIISVMEastUS  。

7. 接下來，使用步驟 1-5 來連線到位於 **西歐** 的 VM *myVMWestEurope* ，並從這個 VM 瀏覽到流量管理員設定檔網域名稱。 因為 VM myVMWestEurope  IP 位址與端點 myProductionWebsiteEndpoint  相關聯，網頁瀏覽器會啟動測試網站伺服器 - myIISVMWestEurope  。

## <a name="clean-up-resources"></a>清除資源

如果不再需要，請刪除資源群組 ( **ResourceGroupTM1** 和 **ResourceGroupTM2** )。 若要這麼做，請選取資源群組 ( **ResourceGroupTM1** 或 **ResourceGroupTM2** )，然後選取 [刪除]  。

## <a name="next-steps"></a>後續步驟

若要深入了解子網路路由方法，請參閱：

> [!div class="nextstepaction"]
> [子網路流量路由方法](traffic-manager-routing-methods.md#subnet)
