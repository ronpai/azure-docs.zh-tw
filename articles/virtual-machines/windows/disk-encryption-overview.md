---
title: 啟用 Windows Vm 的 Azure 磁碟加密
description: 本文提供針對 Windows Vm 啟用 Microsoft Azure 磁片加密的指示。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: ea82724ec534aa896fa012332c10e28050caf825
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92909381"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>適用于 Windows Vm 的 Azure 磁碟加密 

Azure 磁碟加密可協助保護資料安全，以符合貴組織安全性和合規性承諾。 它使用 Windows 的 [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) 功能來為 Azure 虛擬機器的 OS 和資料磁片提供磁片區加密， (vm) ，並與 [Azure Key Vault](../../key-vault/index.yml) 整合，協助您控制及管理磁片加密金鑰和秘密。 

如果您使用 [Azure 資訊安全中心](../../security-center/index.yml)，則會在未加密 VM 時收到警示。 這些警示會顯示為「高嚴重性」，而建議就是加密這些 VM。

![「Azure 資訊安全中心」磁碟加密警示](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - 如果您先前曾使用 Azure 磁碟加密搭配 Azure AD 來加密 VM，則必須繼續使用此選項來加密您的 VM。 如需詳細資料，請參閱 [Azure 磁碟加密與 Azure AD (舊版)](disk-encryption-overview-aad.md)。 
> - 某些建議可能會增加資料、網路或計算資源的使用量，導致額外的授權或訂用帳戶成本。 您必須擁有有效的作用中 Azure 訂用帳戶，才能在 Azure 支援的區域中建立資源。

您可以在短短幾分鐘內學習 Azure 磁碟加密 for Windows 的基本概念：使用 [Azure CLI 快速入門建立和加密 WINDOWS vm](disk-encryption-cli-quickstart.md) ，或使用 [Azure Powershell 快速入門建立和加密 windows vm](disk-encryption-powershell-quickstart.md)。

## <a name="supported-vms-and-operating-systems"></a>支援的 VM 與作業系統

### <a name="supported-vms"></a>支援的 VM

Windows Vm 提供各種 [大小的](../sizes-general.md)支援。 Azure 磁碟加密不適用於 [基本、a 系列 vm](https://azure.microsoft.com/pricing/details/virtual-machines/series/)，或虛擬機器上的記憶體小於 2 GB。

Azure 磁碟加密也適用於具有進階儲存體的 VM。

[第2代 vm](../generation-2.md#generation-1-vs-generation-2-capabilities)上無法使用 Azure 磁碟加密。 如需更多例外狀況，請參閱 [Azure 磁碟加密：不支援的案例](disk-encryption-windows.md#unsupported-scenarios)。

### <a name="supported-operating-systems"></a>支援的作業系統

- Windows 用戶端： Windows 8 和更新版本。
- Windows Server： Windows Server 2008 R2 和更新版本。  
 
> [!NOTE]
> Windows Server 2008 R2 需要安裝 .NET Framework 4.5 才能進行加密;使用適用于 Windows Server 2008 R2 x64 型系統的選擇性更新 Microsoft .NET Framework 4.5.2 安裝 Windows Update ([>kb2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)) 。  
>  
> Windows Server 2012 R2 Core 和 Windows Server 2016 Core 需要在 VM 上安裝 bdehdcfg 元件進行加密。


## <a name="networking-requirements"></a>網路需求
若要啟用 Azure 磁碟加密，Vm 必須符合下列網路端點設定需求：
  - 若要取得權杖以連接到您的金鑰保存庫，Windows VM 必須能夠連線到 Azure Active Directory 端點 \[ login.microsoftonline.com \] 。
  - 若要將加密金鑰寫入金鑰保存庫，Windows VM 必須能夠連線到金鑰保存庫端點。
  - Windows VM 必須能夠連接到裝載 Azure 擴充功能儲存機制的 Azure 儲存體端點，以及裝載 VHD 檔案的 Azure 儲存體帳戶。
  -  如果您的安全性原則會限制從 Azure VM 至網際網路的存取，您可以解析前述的 URI，並設定特定的規則以允許和這些 IP 的輸出連線。 如需詳細資訊，請參閱[防火牆後方的 Azure Key Vault](../../key-vault/general/access-behind-firewall.md)。    


## <a name="group-policy-requirements"></a>群組原則需求

Azure 磁碟加密使用適用于 Windows Vm 的 BitLocker 外部金鑰保護裝置。 對於加入網域的 VM，請勿推送任何會強制使用 TPM 保護裝置的群組原則。 如需「在不含相容 TPM 的情況下允許 BitLocker」群組原則的相關資訊，請參閱 [BitLocker 群組原則參考](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1)。

已加入網域的虛擬機器上使用自訂群組策略的 BitLocker 原則必須包含下列設定：設定 [BitLocker 修復資訊的使用者儲存體-> 允許256位修復金鑰](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)。 當 BitLocker 的自訂群組原則設定不相容時，Azure 磁碟加密將會失敗。 在沒有正確原則設定的電腦上，您可能必須套用新的原則、強制新的原則進行更新 (gpupdate.exe /force)，然後重新啟動。

如果網域層級群組原則封鎖 BitLocker 所使用的 AES CBC 演算法，Azure 磁碟加密將會失敗。

## <a name="encryption-key-storage-requirements"></a>加密金鑰儲存體需求  

Azure 磁碟加密需要 Azure Key Vault 來控制及管理磁碟加密金鑰與祕密。 您的金鑰保存庫和 VM 位於相同的 Azure 區域和訂閱中。

如需詳細資料，請參閱[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。

## <a name="terminology"></a>詞彙
下表定義 Azure 磁碟加密文件中所使用的一些常見詞彙：

| 詞彙 | 定義 |
| --- | --- |
| Azure 金鑰保存庫 | Key Vault 是一個密碼編譯金鑰管理服務，以「美國聯邦資訊處理標準」(FIPS) 已驗證的硬體安全性模組為基礎。 這些標準可協助您保護密碼編譯金鑰和敏感性祕密。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件與[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| Azure CLI | [Azure CLI](/cli/azure/install-azure-cli) 已針對從命令列管理 Azure 資源進行最佳化。|
| BitLocker |[BitLocker](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831713(v=ws.11)) 是一種業界認可的 windows 磁片區加密技術，用來在 Windows vm 上啟用磁片加密。 |
| 金鑰加密金鑰 (KEK) | 可用來保護或包裝密碼的非對稱金鑰 (RSA 2048)。 您可以提供硬體安全性模組 (HSM) 保護的金鑰或軟體保護的金鑰。 如需詳細資訊，請參閱 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) 文件與[建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)。 |
| PowerShell Cmdlet | 如需詳細資訊，請參閱 [Azure PowerShell Cmdlet](/powershell/azure/)。 |


## <a name="next-steps"></a>後續步驟

- [快速入門-使用 Azure CLI 建立和加密 Windows VM ](disk-encryption-cli-quickstart.md)
- [快速入門-使用 Azure Powershell 建立和加密 Windows VM](disk-encryption-powershell-quickstart.md)
- [Windows VM 上的 Azure 磁碟加密案例](disk-encryption-windows.md)
- [Azure 磁碟加密的必要 CLI 指令碼](https://github.com/ejarvi/ade-cli-getting-started)
- [Azure 磁碟加密的必要 PowerShell 指令碼](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [建立及設定適用於 Azure 磁碟加密的金鑰保存庫](disk-encryption-key-vault.md)