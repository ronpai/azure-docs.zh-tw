---
title: 使用 TLS 保護 web 服務
titleSuffix: Azure Machine Learning
description: 瞭解如何啟用 HTTPS，以便保護透過 Azure Machine Learning 部署的 web 服務。 Azure Machine Learning 使用 TLS 1.2 版來保護部署為 web 服務的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 52344b665b00329c80fb651657fbbd19d5ffd7a4
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743083"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>使用 TLS 來透過 Azure Machine Learning 保護 Web 服務


本文說明如何保護透過 Azure Machine Learning 部署的 web 服務。

您可以使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS) 來限制對 web 服務的存取，並保護用戶端所提交的資料。 HTTPS 可以加密兩者之間的通訊，以協助保護用戶端與 web 服務之間的通訊安全。 加密會使用 [傳輸層安全性 (TLS) ](https://en.wikipedia.org/wiki/Transport_Layer_Security)。 TLS 有時仍稱為 *安全通訊端層* (SSL) ，也就是 tls 的前身。

> [!TIP]
> Azure Machine Learning SDK 會針對與安全通訊相關的屬性使用「SSL」一詞。 這並不表示您的 web 服務不會使用 *TLS* 。 SSL 只是較常辨識的詞彙。
>
> 具體而言，透過 Azure Machine Learning 部署的 web 服務只支援適用于 ACI 的 TLS 1.1 版和 AKS 的 TLS 1.2 版。

TLS 和 SSL 都依賴 *數位憑證* ，可協助進行加密和身分識別驗證。 如需有關數位憑證運作方式的詳細資訊，請參閱維琪百科主題 [公開金鑰基礎結構](https://en.wikipedia.org/wiki/Public_key_infrastructure)。

> [!WARNING]
> 如果您的 web 服務未使用 HTTPS，則網際網路上的其他人可能會看到傳送至服務和來自該服務的資料。
>
> HTTPS 也可讓用戶端驗證其所連接之伺服器的真實性。 這項功能可保護用戶端抵禦 [攔截](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) 式攻擊。

這是保護 web 服務的一般程式：

1. 取得網域名稱。

2. 取得數位憑證。

3. 部署或更新啟用 TLS 的 web 服務。

4. 更新您的 DNS 以指向 Web 服務。

> [!IMPORTANT]
> 如果您要部署 Azure Kubernetes Service (AKS) ，您可以購買自己的憑證，或使用 Microsoft 提供的憑證。 如果您使用來自 Microsoft 的憑證，則不需要取得功能變數名稱或 TLS/SSL 憑證。 如需詳細資訊，請參閱本文的「 [啟用 TLS 和部署](#enable) 」一節。

當您在 [部署目標](how-to-deploy-and-where.md)之間保護時，會有些微差異。

## <a name="get-a-domain-name"></a>取得網域名稱

如果您還沒有定義功能變數名稱稱，請向 *網域註冊機構* 購買一個。 註冊機構之間的流程和價格有所不同。 註冊機構提供管理功能變數名稱的工具。 您可以使用這些工具，將完整功能變數名稱 (FQDN)  (例如 www \. contoso.com) ，對應至裝載 web 服務的 IP 位址。

## <a name="get-a-tlsssl-certificate"></a>取得 TLS/SSL 憑證

有許多方法可取得 (數位憑證) 的 TLS/SSL 憑證。 最常見的是從 *憑證授權單位* 單位購買一個 (CA) 。 無論您取得憑證的位置為何，您都需要下列檔案：

* **憑證** 。 憑證必須包含完整的憑證鏈，而且必須是「PEM 編碼」。
* **金鑰** 。 金鑰也必須是 PEM 編碼。

當您要求憑證時，您必須提供您打算用於 web 服務的位址 FQDN (例如，www \. contoso.com) 。 系統會將已加上戳記的位址和用戶端所使用的位址進行比較，以驗證 web 服務的身分識別。 如果這些位址不相符，用戶端會收到錯誤訊息。

> [!TIP]
> 如果憑證授權單位單位無法以 PEM 編碼的檔案形式提供憑證和金鑰，您可以使用 [OpenSSL](https://www.openssl.org/) 之類的公用程式來變更格式。

> [!WARNING]
> 使用 *自我簽署* 憑證僅供開發之用。 請勿在生產環境中使用它們。 自我簽署的憑證可能會造成用戶端應用程式的問題。 如需詳細資訊，請參閱用戶端應用程式所使用網路程式庫的檔。

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> 啟用 TLS 並部署

若要部署 (或重新部署) 已啟用 TLS 的服務，請將 *ssl_enabled* 參數設定為 "True" （不論其適用的位置）。 將 *ssl_certificate* 參數設定為 *憑證* 檔案的值。 將 *ssl_key* 設定為 *金鑰* 檔的值。

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>在 AKS 和可現場程式化閘道陣列上部署 (FPGA) 

  > [!NOTE]
  > 當您為設計工具部署安全的 web 服務時，本節中的資訊也適用。 如果您不熟悉如何使用 Python SDK，請參閱 [什麼是適用于 python 的 AZURE MACHINE LEARNING SDK？](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true)。

當您部署至 AKS 時，您可以建立新的 AKS 叢集或附加現有的叢集。 如需有關建立或附加叢集的詳細資訊，請參閱 [將模型部署到 Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)叢集。
  
-  如果您建立新的叢集，您會使用 **[AksCompute.provisioning_configuration ( # B1](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueprovisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** 。
- 如果您附加現有的叢集，您會使用 **[AksCompute.attach_configuration ( # B1](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py&preserve-view=true#&preserve-view=trueattach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** 。 這兩個方法都會傳回具有 **enable_ssl** 方法的設定物件。

**Enable_ssl** 方法可以使用由 Microsoft 提供的憑證或您所購買的憑證。

  * 當您使用來自 Microsoft 的憑證時，您必須使用 *leaf_domain_label* 參數。 此參數會產生服務的 DNS 名稱。 例如，"contoso" 的值會建立功能變數名稱 "contoso \<six-random-characters> . \<azureregion> 。cloudapp.azure.com "，其中 \<azureregion> 是包含服務的區域。 （選擇性）您可以使用 *overwrite_existing_domain* 參數來覆寫現有的 *leaf_domain_label* 。

    若要部署 (或重新部署) 已啟用 TLS 的服務，請將 *ssl_enabled* 參數設定為 "True" （不論其適用的位置）。 將 *ssl_certificate* 參數設定為 *憑證* 檔案的值。 將 *ssl_key* 設定為 *金鑰* 檔的值。

    > [!IMPORTANT]
    > 當您使用來自 Microsoft 的憑證時，您不需要購買自己的憑證或功能變數名稱。

    下列範例示範如何建立可從 Microsoft 啟用 TLS/SSL 憑證的設定：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * 當您使用 *您所購買的憑證* 時，您會使用 *ssl_cert_pem_file* 、 *ssl_key_pem_file* 和 *ssl_cname* 參數。 下列範例示範如何使用 *pem* 檔案來建立使用您所購買 TLS/SSL 憑證的設定：

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

如需 *enable_ssl* 的詳細資訊，請參閱 [AksProvisioningConfiguration.enable_ssl ( # B1](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) 和 [AksAttachConfiguration.enable_ssl ( # B3](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py&preserve-view=true#&preserve-view=trueenable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-)。

### <a name="deploy-on-azure-container-instances"></a>在 Azure 容器實例上部署

當您部署至 Azure 容器實例時，您會提供 TLS 相關參數的值，如下列程式碼片段所示：

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

如需詳細資訊，請參閱 [AciWebservice.deploy_configuration ( # B1 ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)。

## <a name="update-your-dns"></a>更新您的 DNS

接下來，您必須更新 DNS 以指向 Web 服務。

+ **針對容器實例：**

  使用您的網域註冊機構中的工具，更新功能變數名稱的 DNS 記錄。 記錄必須指向服務的 IP 位址。

  可能會有幾分鐘或幾小時的時間，用戶端可以根據註冊機構和針對功能變數名稱設定的「存留時間」 (TTL) 來解析功能變數名稱。

+ **針對 AKS：**

  > [!WARNING]
  > 如果您使用 *leaf_domain_label* 使用 Microsoft 的憑證來建立服務，請不要手動更新叢集的 DNS 值。 值應該會自動設定。

  在左窗格的 [ **設定** ] 底下的 [設定] 索引標籤 **中，更新** AKS 叢集之公用 IP 位址的 DNS。  (請參閱下圖。 ) 公用 IP 位址是在資源群組下建立的資源類型，其中包含 AKS 代理程式節點和其他網路資源。

  [![Azure Machine Learning：使用 TLS 保護 web 服務](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>更新 TLS/SSL 憑證

TLS/SSL 憑證過期，必須更新。 這通常會在每年發生。 使用下列各節中的資訊，針對部署至 Azure Kubernetes Service 的模型更新和更新您的憑證：

### <a name="update-a-microsoft-generated-certificate"></a>更新 Microsoft 產生的憑證

如果憑證最初是由 Microsoft (在使用 *leaf_domain_label* 建立服務) 時，請使用下列其中一個範例來更新憑證：

> [!IMPORTANT]
> * 如果現有的憑證仍然有效，請使用 `renew=True` (SDK) 或 `--ssl-renew` (CLI) 來強制進行更新。 例如，如果現有的憑證仍有效10天，而您未使用 `renew=True` ，則憑證可能不會更新。
> * 最初部署服務時， `leaf_domain_label` 會使用此模式來建立 DNS 名稱 `<leaf-domain-label>######.<azure-region>.cloudapp.azure.net` 。 若要保留現有的名稱 (包括原本產生的) 6 位數，請使用原始 `leaf_domain_label` 值。 請勿包含產生的6位數。

**使用 SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**使用 CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

如需詳細資訊，請參閱下列參考檔：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

### <a name="update-custom-certificate"></a>更新自訂憑證

如果憑證原本是由憑證授權單位單位所產生，請使用下列步驟：

1. 使用憑證授權單位單位提供的檔來更新憑證。 此進程會建立新的憑證檔案。

1. 使用 SDK 或 CLI，以新的憑證來更新服務：

    **使用 SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **使用 CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

如需詳細資訊，請參閱下列參考檔：

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py&preserve-view=true)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py&preserve-view=true)

## <a name="disable-tls"></a>停用 TLS

若要針對部署至 Azure Kubernetes Service 的模型停用 TLS，請 `SslConfiguration` 使用建立 `status="Disabled"` ，然後執行更新：

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>後續步驟
了解如何：
+ [使用部署為 web 服務的機器學習模型](how-to-consume-web-service.md)
+ [虛擬網路隔離和隱私權總覽](how-to-network-security-overview.md)
