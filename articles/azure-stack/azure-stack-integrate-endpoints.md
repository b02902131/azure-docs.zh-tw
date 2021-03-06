---
title: Azure Stack 資料中心整合 - 發佈端點
description: 了解如何在您的資料中心內發佈 Azure Stack 端點
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 03/27/2018
ms.author: jeffgilb
ms.reviewer: wamota
keywords: ''
ms.openlocfilehash: 136d78be3cddfd6fd4e491d5ea3f5d51d0dc611f
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack 資料中心整合 - 發佈端點
Azure Stack 會為其基礎結構角色設定虛擬 IP 位址 (VIP)。 這些 VIP 是從公用 IP 位址集區配置的。 針對每個 VIP，都會藉由軟體定義網路層中的存取控制清單 (ACL) 來提供保護。 ACL 也用於各個實體交換器 (TOR 和 BMC) 來進一步強化解決方案。 系統會針對在部署階段所指定外部 DNS 區域中的每個端點，都建立一個 DNS 項目。


以下架構圖顯示各種不同的網路層和 ACL：

![結構化圖片](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>連接埠和通訊協定 (輸入)

將 Azure Stack 端點發佈到外部網路時需有一組基礎結構 VIP。 「端點 (VIP)」資料表會顯示每個端點、所需的連接埠以及通訊協定。 請參閱特定資源提供者部署文件，了解需要其他資源提供者 (例如 SQL 資源提供者等) 的端點。

此處並未列出內部基礎結構 VIP，因為它們並非發佈 Azure Stack 所需的 VIP。

> [!NOTE]
> 使用者 VIP 是動態的，由使用者自己定義，並不受 Azure Stack 操作員控制。


|端點 (VIP)|DNS 主機 A 記錄|通訊協定|連接埠|
|---------|---------|---------|---------|
|AD FS|Adfs.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|入口網站 (系統管理員)|Adminportal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015|
|Azure Resource Manager (系統管理員)|Adminmanagement.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|入口網站 (使用者)|Portal.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (使用者)|Management.*&lt;region>.&lt;fqdn>*|HTTPS|443<br>30024|
|圖形|Graph.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|憑證撤銷清單|Crl.*&lt;region>.&lt;fqdn>*|HTTP|80|
|DNS|&#42;.*&lt;region>.&lt;fqdn>*|TCP 和 UDP|53|
|Key Vault (使用者)|&#42;.vault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|Key Vault (系統管理員)|&#42;.adminvault.*&lt;region>.&lt;fqdn>*|HTTPS|443|
|儲存體佇列|&#42;.queue.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|儲存體資料表|&#42;.table.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|儲存體 Blob|&#42;.blob.*&lt;region>.&lt;fqdn>*|HTTP<br>HTTPS|80<br>443|
|SQL 資源提供者|sqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|MySQL 資源提供者|mysqladapter.dbadapter.*&lt;region>.&lt;fqdn>*|HTTPS|44300-44304|
|App Service 方案|&#42;.appservice.*&lt;region>.&lt;fqdn>*|TCP|80 (HTTP)<br>443 (HTTPS)<br>8172 (MSDeploy)|
|  |&#42;.scm.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)|
|  |api.appservice.*&lt;region>.&lt;fqdn>*|TCP|443 (HTTPS)<br>44300 (Azure Resource Manager)|
|  |ftp.appservice.*&lt;region>.&lt;fqdn>*|TCP、UDP|21、1021、10001-101000 (FTP)<br>990 (FTPS)|

## <a name="ports-and-urls-outbound"></a>連接埠和 URL (輸出)

Azure Stack 僅支援 Transparent Proxy 伺服器。 在 Transparent Proxy 上行連結至傳統 Proxy 伺服器的部署中，您必須允許下列連接埠和 URL 才能進行連出通訊：


|目的|URL|通訊協定|連接埠|
|---------|---------|---------|---------|
|身分識別|login.windows.net<br>login.microsoftonline.com<br>graph.windows.net|HTTP<br>HTTPS|80<br>443|
|Marketplace 摘要整合|https://management.azure.com<br>https://&#42;.blob.core.windows.net<br>https://*.azureedge.net<br>https://&#42;.microsoftazurestack.com|HTTPS|443|
|修補程式和更新|https://&#42;.azureedge.net|HTTPS|443|
|註冊|https://management.azure.com|HTTPS|443|
|使用量|https://&#42;.microsoftazurestack.com<br>https://*.trafficmanager.com|HTTPS|443|


## <a name="next-steps"></a>後續步驟

[Azure Stack PKI 需求](azure-stack-pki-certs.md)