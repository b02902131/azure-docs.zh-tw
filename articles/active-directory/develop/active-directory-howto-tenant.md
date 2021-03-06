---
title: 如何取得 Azure AD 租用戶 | Microsoft Docs
description: 如何取得 Azure Active Directory 租用戶，以供註冊及建置應用程式使用。
services: active-directory
documentationcenter: ''
author: mtillman
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/23/2018
ms.author: mtillman
ms.custom: aaddev
ms.openlocfilehash: ab7db49fa07f260de6ebbe4b2cee943b64cab7fe
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>如何取得 Azure Active Directory 租用戶
在 Azure Active Directory (Azure AD) 中， [租用戶](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) 代表組織。  它是組織在建立與 Microsoft 的關係 (例如，藉由註冊 Azure、Microsoft Intune 或 Office 365 等 Microsoft 雲端服務) 時所獲得和擁有的專屬 Azure AD 服務執行個體。  每個 Azure AD 租用戶都不同，並與其他 Azure AD 租用戶分開。  

租用戶可裝載公司中的使用者及其相關資訊 (密碼、使用者設定檔資料、權限等)。  它還包含群組、應用程式和關於組織及其安全性的其他資訊。

若要允許 Azure AD 使用者登入您的應用程式，您必須在您自己的租用戶中註冊您的應用程式。  在其中建立 Azure AD 租用戶及發佈應用程式是**完全免費**的 (但也您可以選擇在租用戶中支付進階功能的費用)。  事實上，大部分的開發人員都會建立數個租用戶和應用程式，以供實驗、開發、預備和測試之用。

## <a name="use-an-existing-azure-ad-tenant"></a>使用現有的 Azure AD 租用戶

許多開發人員都已透過繫結至 Azure AD 租用戶的服務或訂用帳戶 (例如：Office 365 或 Azure 訂用帳戶) 取得租用戶。  若要確認您是否已有租用戶，請使用您要用來管理應用程式的帳戶登入 [Azure 入口網站](https://portal.azure.com)，並在右上角查看顯示帳戶資訊的位置。  如果您有租用戶，您將會自動登入，並且在您的帳戶名稱正下方看到租用戶名稱。  如果您的帳戶與多個租用戶相關聯，您可以按一下帳戶名稱以開啟功能表，以在租用戶之間切換。

如果您目前沒有與帳戶相關聯的租用戶，則會在您的帳戶名稱下方看到一個 GUID，且您在[建立新的租用戶](#create-a-new-azure-ad-tenant)之前，將無法執行註冊應用程式之類的動作。

## <a name="create-a-new-azure-ad-tenant"></a>建立新的 Azure AD 租用戶

如果您還沒有 Azure AD 租用戶，或是想要建立一個新的，您可以在 [Azure 入口網站](https://portal.azure.com)中使用[目錄建立經驗](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)來達成此目的。  此程序約需要一分鐘，且在結束後，系統將會提示您瀏覽至新建立的租用戶。