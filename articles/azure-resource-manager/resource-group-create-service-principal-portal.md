---
title: 在入口網站中建立 Azure App 的身分識別 | Microsoft Docs
description: 描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。描述如何建立可以與 Azure 資源管理員中的角色型存取控制搭配使用來管理資源存取權的新 Active Directory 應用程式和服務主體。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/21/2018
ms.author: tomfitz
ms.openlocfilehash: 264befc6c60b87d41658b4da763e477fbb7e3f8c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/23/2018
---
# <a name="use-portal-to-create-an-azure-active-directory-application-and-service-principal-that-can-access-resources"></a>使用入口網站來建立可存取資源的 Active Directory 應用程式和服務主體

如果您擁有需要存取或修改資源的程式碼，您就必須設定一個 Active Directory (AD) 應用程式。 您可以將必要的權限指派給 AD 應用程式。 此方法適用於在您自己的認證下執行應用程式，因為您可以將自有權限以外的權限指派給應用程式識別碼。 一般而言，這些權限只會限制為 App 必須執行的確切權限。

本文說明如何透過入口網站執行這些步驟。 其中著重在說明單一租用戶應用程式，此應用程式的目的是只在一個組織內執行。 您通常會將單一租用戶應用程式用在組織內執行的企業營運系統應用程式。

> [!IMPORTANT]
> 若不要建立服務主體，可考慮將 Azure AD 受控服務識別用於您的應用程式識別碼。 Azure AD MSI 是 Azure Active Directory 的公開預覽功能，可簡化建立程式碼身分識別的程序。 若您的程式碼在支援 Azure AD MSI 的服務上執行，且存取的資源支援 Azure Active Directory 驗證，則 Azure AD MSI 是您更好的選項。 若要深入了解 Azure AD MSI，以及其目前受到哪些服務的支援，請參閱 [Azure 資源的受控服務識別](../active-directory/managed-service-identity/overview.md)。

## <a name="required-permissions"></a>所需的權限

若要完成本文，您必須有足夠權限向 Azure AD 租用戶註冊應用程式，並將應用程式指派給 Azure 訂用帳戶中的角色。 讓我們來確定您具有適當的權限可執行這些步驟。

### <a name="check-azure-active-directory-permissions"></a>檢查 Azure Active Directory 權限

1. 選取 **Azure Active Directory**。

   ![選取 Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 在 Azure Active Directory 中，選取 [使用者設定]。

   ![選取使用者設定](./media/resource-group-create-service-principal-portal/select-user-settings.png)

1. 檢查 [應用程式註冊] 設定。 如果設定為 [是]，非系統管理使用者可以註冊 AD 應用程式。 這個設定表示 Azure AD 租用戶中的任何使用者都可以註冊應用程式。 您可以繼續到[檢查 Azure 訂用帳戶權限](#check-azure-subscription-permissions)。

   ![檢視應用程式註冊](./media/resource-group-create-service-principal-portal/view-app-registrations.png)

1. 如果應用程式註冊設定為 [否]，則只有系統管理員使用者才能註冊應用程式。 檢查帳戶是否為 Azure AD 租用戶的系統管理員。 選取 [概觀] 並查看您的使用者資訊。 如果您的帳戶指派給「使用者」角色，但應用程式註冊設定 (從先前步驟中) 僅限於系統管理員使用者，請洽詢系統管理員將您指派至系統管理員角色，或讓使用者可以註冊應用程式。

   ![尋找使用者](./media/resource-group-create-service-principal-portal/view-user-info.png)

### <a name="check-azure-subscription-permissions"></a>檢查 Azure 訂用帳戶權限

在您的 Azure 訂用帳戶中，您的帳戶必須具有 `Microsoft.Authorization/*/Write` 存取權，才能將 AD 應用程式指派給角色。 此動作是[擁有者](../active-directory/role-based-access-built-in-roles.md#owner)角色或[使用者存取系統管理員](../active-directory/role-based-access-built-in-roles.md#user-access-administrator)角色來授與。 如果您的帳戶已指派給**參與者**角色，則您沒有足夠的權限。 當您嘗試將服務主體指派給角色時，您會收到錯誤。

若要檢查訂用帳戶權限：

1. 在右上角中，選取您的帳戶，然後選取 [我的權限]。

   ![選取使用者權限](./media/resource-group-create-service-principal-portal/select-my-permissions.png)

1. 從下拉式清單中，選取訂用帳戶。 選取 [按一下這裡以詳細檢視此訂用帳戶的完整存取權]。

   ![尋找使用者](./media/resource-group-create-service-principal-portal/view-details.png)

1. 檢視指派的角色，並判斷是否有足夠的權限可將 AD 應用程式指派給角色。 如果沒有，請洽詢訂用帳戶管理員，將您新增至「使用者存取系統管理員」角色。 在下圖中，使用者已指派給「擁有者」角色，這表示該使用者具有足夠的權限。

   ![顯示權限](./media/resource-group-create-service-principal-portal/view-user-role.png)

## <a name="create-an-azure-active-directory-application"></a>建立 Azure Active Directory 應用程式

1. 透過 [Azure 入口網站](https://portal.azure.com)登入 Azure 帳戶。
1. 選取 **Azure Active Directory**。

   ![選取 Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 選取 [應用程式註冊]。

   ![select app registrations](./media/resource-group-create-service-principal-portal/select-app-registrations.png)

1. 選取 [新增應用程式註冊]。

   ![新增應用程式](./media/resource-group-create-service-principal-portal/select-add-app.png)

1. 提供應用程式的名稱和 URL。 針對您想要建立的應用程式類型，選取 [Web 應用程式/API]。 您無法建立[原生應用程式](../active-directory/active-directory-application-proxy-native-client.md)的認證，因此，該類型無法用於自動化應用程式。 設定值之後，選取 [建立]。

   ![名稱應用程式](./media/resource-group-create-service-principal-portal/create-app.png)

您已建立好應用程式。

## <a name="get-application-id-and-authentication-key"></a>取得應用程式識別碼和驗證金鑰

以程式設計方式登入時，您需要應用程式識別碼和驗證金鑰。 若要取得這些值，請使用下列步驟︰

1. 在 Azure Active Directory 中，從 [應用程式註冊]選取您的應用程式。

   ![選取應用程式](./media/resource-group-create-service-principal-portal/select-app.png)

1. 複製 [應用程式識別碼] 並儲存在您的應用程式碼中。 某些[應用程式範例](#log-in-as-the-application)會將這個值視為用戶端識別碼。

   ![用戶端識別碼](./media/resource-group-create-service-principal-portal/copy-app-id.png)

1. 若要產生驗證金鑰，請選取 [設定]。

   ![選取設定](./media/resource-group-create-service-principal-portal/select-settings.png)

1. 若要產生驗證金鑰，請選取 [金鑰]。

   ![選取金鑰](./media/resource-group-create-service-principal-portal/select-keys.png)

1. 提供金鑰的描述和金鑰的持續時間。 完成時，選取 [儲存]。

   ![儲存金鑰](./media/resource-group-create-service-principal-portal/save-key.png)

   儲存金鑰之後會顯示金鑰的值。 請複製此值，因為您之後就無法擷取金鑰。 您提供金鑰值和應用程式識別碼，能夠以應用程式方式登入。 將金鑰值儲存在應用程式可擷取的地方。

   ![儲存的金鑰](./media/resource-group-create-service-principal-portal/copy-key.png)

## <a name="get-tenant-id"></a>取得租用戶識別碼

以程式設計方式登入時，您需要將租用戶識別碼與您的驗證要求一起傳送。

1. 選取 **Azure Active Directory**。

   ![選取 Azure Active Directory](./media/resource-group-create-service-principal-portal/select-active-directory.png)

1. 若要取得租用戶識別碼，請選取 Azure AD 租用戶的 [屬性]。

   ![選取 Azure AD 屬性](./media/resource-group-create-service-principal-portal/select-ad-properties.png)

1. 複製 [目錄識別碼]。 這個值是您的租用戶識別碼。

   ![租用戶識別碼](./media/resource-group-create-service-principal-portal/copy-directory-id.png)

## <a name="assign-application-to-role"></a>將應用程式指派給角色

若要存取您的訂用帳戶中的資源，您必須將應用程式指派給角色。 決定哪個角色代表應用程式的正確權限。 若要深入了解可用的角色，請參閱 [RBAC：內建角色](../active-directory/role-based-access-built-in-roles.md)。

您可以針對訂用帳戶、資源群組或資源的層級設定範圍。 較低的範圍層級會繼承較高層級的權限。 例如，為資源群組的讀取者角色新增應用程式，代表該角色可以讀取資源群組及其所包含的任何資源。

1. 瀏覽至您想要讓應用程式指派至的範圍層級。 例如，若要在訂用帳戶範圍指派角色，請選取 [訂用帳戶]。 您可以改為選取資源群組或資源。

   ![選取訂用帳戶](./media/resource-group-create-service-principal-portal/select-subscription.png)

1. 選取特定訂用帳戶作為指派應用程式的對象 (資源群組或資源)。

   ![選取要指派的訂用帳戶](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

1. 選取 [存取控制 (IAM)]。

   ![選取存取](./media/resource-group-create-service-principal-portal/select-access-control.png)

1. 選取 [新增] 。

   ![選取 [新增]](./media/resource-group-create-service-principal-portal/select-add.png)

1. 選取您想要將應用程式指派給哪個角色。 下圖顯示**讀者**角色：

   ![選取角色](./media/resource-group-create-service-principal-portal/select-role.png)

1. 根據預設，Azure Active Directory 應用程式不會顯示在可用選項中。 若要尋找您的應用程式，您必須在搜尋欄位中提供其名稱。 請選取它。

   ![搜尋應用程式](./media/resource-group-create-service-principal-portal/search-app.png)

1. 選取 [儲存] 以完成角色指派。 您在使用者清單中看到應用程式已指派給該範圍的角色。

## <a name="next-steps"></a>後續步驟
* 若要設定多租用戶應用程式，請參閱 [利用 Azure Resource Manager API 進行授權的開發人員指南](resource-manager-api-authentication.md)。
* 若要了解如何指定安全性原則，請參閱 [Azure 角色型存取控制](../active-directory/role-based-access-control-configure.md)。  
* 如需可授與或拒絕使用者的可用動作清單，請參閱 [Azure Resource Manager 資源提供者作業](../active-directory/role-based-access-control-resource-provider-operations.md)。
