---
title: "什麼是 Azure 金鑰保存庫？ | Microsoft Docs"
description: "Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用 Azure 金鑰保存庫之後，客戶可以加密金鑰和密碼 (例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰、.PFX 檔案和密碼)，方法是使用受硬體安全模組 (HSM) 保護的金鑰。"
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/26/2017
ms.author: barclayn
ms.openlocfilehash: d1c7a61522770d5bf590637629ec48ee35151623
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2018
---
# <a name="what-is-azure-key-vault"></a>什麼是 Azure 金鑰保存庫？
大部分地區均提供 Azure 金鑰保存庫。 如需詳細資訊，請參閱 [金鑰保存庫價格頁面](https://azure.microsoft.com/pricing/details/key-vault/)。

## <a name="introduction"></a>簡介
Azure 金鑰保存庫可協助保護雲端應用程式和服務所使用的密碼編譯金鑰和密碼。 使用 Key Vault 之後，您可以使用受硬體安全模組 (HSM) 保護的金鑰來加密金鑰和密碼 (例如驗證金鑰、儲存體帳戶金鑰、資料加密金鑰、.PFX 檔案和密碼)。 為了加強保證，您可以在 HSM 中匯入或產生金鑰。 如果您選擇這麼做，Microsoft 會在進行過 FIPS 140-2 Level 2 驗證的 HSM (硬體和韌體) 中處理您的金鑰。  

金鑰保存庫簡化了金鑰管理程序，並可讓您控管存取和加密資料的金鑰。 開發人員可以在幾分鐘內建立開發和測試的金鑰，然後順利地將他們移轉至生產金鑰。 安全性系統管理員可以視需要授與 (和撤銷) 存取金鑰的權限。

使用下表將有助於瞭解金鑰保存庫可以如何協助達到開發人員和安全性系統管理員的需求。

| 角色 | 問題陳述 | Azure 金鑰保存庫已解決問題 |
| --- | --- | --- |
| Azure 應用程式的開發人員 |「我想要撰寫使用金鑰進行簽署和加密的 Azure 應用程式，但我希望這些金鑰會與我的應用程式分開，如此一來此解決方案便適用於位於不同地點的應用程式。 <br/><br/>我還希望這些金鑰和密碼會受到保護，但我可以不用自己撰寫程式碼。 我也希望從我的應用程式就可以輕鬆使用這些金鑰和密碼，並得到最佳效能。」 |√ 金鑰會儲存在保存庫中，並且視需要由 URI 叫用。<br/><br/> √ Azure 使用會業界標準演算法、金鑰長度和硬體安全性模組 (HSM) 保護金鑰。<br/><br/> √ 金鑰會在與應用程式位於相同 Azure 資料中心的 HSM 中處理。 這麼做會比金鑰位於不同位置 (例如內部部署) 提供更好的可靠性並減少延遲。 |
| 軟體即服務 (SaaS) 的開發人員 |「對於客戶之租用戶的金鑰和密碼，我不想承擔任何實際或潛在法律責任。 <br/><br/>我希望客戶擁有並管理他們自己的金鑰，所以我可以將全部精力集中在我的專長上，也就是提供核心軟體功能。」 |√ 客戶可以將他們自己的金鑰匯入 Azure 並加以管理。 當 SaaS 應用程式必須使用其客戶金鑰來執行加密編譯密碼作業時，金鑰保存庫會代表應用程式執行這些作業。 應用程式不會看到客戶的金鑰。 |
| 資訊安全長 (CSO) |「我想要知道我們的應用程式是否遵守 FIPS 140-2 Level 2 HSM 的安全金鑰管理規定。 <br/><br/>我想要確定我的組織掌控著金鑰生命週期，並可監視金鑰使用方法。 <br/><br/>而且即使我們使用多個 Azure 服務和資源，我仍想要從 Azure 中的單一位置管理金鑰。」 |√ HSM 已通過 FIPS 140-2 Level 2 驗證。<br/><br/>√ 金鑰保存庫經過設計，所以 Microsoft 不會看見或擷取您的金鑰。<br/><br/>√ 近乎即時的金鑰使用情況記錄。<br/><br/>√ 不論您在 Azure 中有幾個保存庫、保存庫支援哪些區域，以及哪些應用程式使用這些保存庫，保存庫均提供單一介面讓您清楚了解。 |

只要擁有 Azure 訂用帳戶，任何人都可以建立和使用金鑰保存庫。 雖然金鑰保存庫有益於開發人員和安全性系統管理員，但管理組織其他 Azure 服務的組織系統管理員也可以實作並管理金鑰保存庫。 例如，此系統管理員可以使用 Azure 訂用帳戶登入、建立組織要用來儲存金鑰的保存庫，然後負責執行營運工作，例如：

* 建立或匯入金鑰或密碼
* 撤銷或刪除金鑰或密碼
* 授權使用者或應用程式存取金鑰保存庫，以便他們管理或使用其金鑰和密碼
* 設定金鑰使用方法 (例如，簽署或加密)
* 監視金鑰使用方法

此系統管理員接著可以為開發人員提供可從其應用程式進行呼叫的 URI，並為其安全性系統管理員提供金鑰使用方法記錄資訊。 

   ![Azure 金鑰保存庫概觀][1]

開發人員也可透過使用 API 直接管理金鑰。 如需詳細資訊，請參閱 [金鑰保存庫開發人員指南](key-vault-developers-guide.md)。

## <a name="next-steps"></a>後續步驟
如需適用於系統管理員的開始使用教學課程，請參閱 [開始使用 Azure 金鑰保存庫](key-vault-get-started.md)。

如需金鑰保存庫使用方法記錄的詳細資訊，請參閱 [Azure 金鑰保存庫記錄](key-vault-logging.md)。

如需搭配 Azure 金鑰保存庫使用金鑰和密碼的詳細資訊，請參閱[關於金鑰、密碼和憑證](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx)。

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
