---
title: 使用 SendGrid 服務和 Azure Functions 在門戶開啟時接收電子郵件 | Microsoft Docs
description: 監視磁性感應器，以在門戶開啟時加以偵測並使用 Azure Functions 傳送電子郵件通知。
services: iot-hub
documentationcenter: ''
author: liydu
manager: timlt
tags: ''
keywords: ''
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2018
ms.author: liydu
ms.openlocfilehash: ba02e877fe7152d759085e3d25090459cd2459fc
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
# <a name="door-monitor"></a>門戶監視          

MXChip IoT DevKit 包含內建的磁性感應器。 在此專案中，您會偵測附近是否有強力磁場存在 -- 在此案例下，此磁場來自於小型的 永久磁鐵。

## <a name="what-you-learn"></a>您學到什麼

在此專案中，您將了解：
- 如何使用 MXChip IoT DevKit 的磁性感應器偵測附近的磁性移動。
- 如何使用 SendGrid 服務傳送通知給您的電子郵件地址。

> [!NOTE]
> 針對此專案的實際用途：
> - 在門緣裝上磁鐵。
> - 在門框上將 DevKit 裝在靠近磁鐵之處。 開啟或關閉門戶將會觸發感應器，而使您接收到事件的電子郵件通知。

## <a name="what-you-need"></a>您需要什麼

完成[使用者入門指南]({{"/docs/get-started/" | absolute_url }})以便：

* 讓 DevKit 連線至 Wi-Fi
* 準備開發環境

有效的 Azure 訂用帳戶。 如果沒有，您可以透過下列方法之一來註冊：

* 啟動 [30 天免費試用 Microsoft Azure 帳戶](https://azure.microsoft.com/en-us/free/)。
* 如果您是 MSDN 或 Visual Studio 訂閱者，請認領您的 [Azure 點數](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)。

## <a name="deploy-sendgrid-service-in-azure"></a>在 Azure 中部署 SendGrid 服務

[SendGrid](https://sendgrid.com/) 是雲端式電子郵件傳遞平台。 此服務將會用來傳送電子郵件通知。

> [!NOTE]
> 如果您已部署 SendGrid 服務，您可以直接跳至[在 Azure 中部署 IoT 中樞](#deploy-iot-hub-in-azure)。

### <a name="sendgrid-deployment"></a>SendGrid 部署

若要佈建 Azure 服務，請使用 [部署至 Azure] 按鈕。 此按鈕可讓您輕鬆快速地將開放原始碼專案部署至 Microsoft Azure。

按一下下方的 [部署至 Azure] 按鈕。 

[![部署至 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2FSendGridDeploy%2Fazuredeploy.json)

您會看見下列頁面。

> [!NOTE]
> 若未看到下列頁面，您可能需要先登入 Azure 帳戶。

完成註冊表單：

  * **資源群組**：建立用以裝載 SendGrid 服務的資源群組，或使用現有資源群組。 請參閱[使用資源群組來管理您的 Azure 資源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal)。

  * **名稱**：SendGrid 服務的名稱。 請選擇與您可能有的其他服務不同的唯一名稱。

  * **密碼**：此服務需要不會用於此專案中任何項目的密碼。

  * **電子郵件**：SendGrid 服務會傳送驗證到此電子郵件地址。

  > [!NOTE]
  > 請勾選 [釘選到儀表板] 選項，以方便日後尋找此應用程式。
 
![SendGrid 部署](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-deploy.png)

### <a name="sendgrid-api-key-creation"></a>SendGrid API 金鑰建立

部署成功後，請加以點選，然後按一下 [管理] 按鈕。 您會進入 SendGrid 頁面，且需要驗證您的電子郵件地址。

![SendGrid 管理](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-manage.png)

在 SendGrid 頁面上，按一下 [設定] > [API 金鑰] > [建立 API 金鑰]。 輸入 [API 金鑰名稱]，然後按一下 [建立並檢視]。

![SendGrid 建立 API 的第一個畫面](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-first.png)

![SendGrid 建立 API 的第二個畫面](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/sendgrid-create-api-second.png)

您的 API 金鑰只會顯示一次。 請務必加以複製並儲存於安全之處，因為在下一個步驟中將用到此金鑰。

## <a name="deploy-iot-hub-in-azure"></a>在 Azure 中部署 IoT 中樞

下列步驟會佈建其他 Azure IoT 相關服務，並部署此專案的 Azure Functions。

按一下下方的 [部署至 Azure] 按鈕。 

[![部署至 Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FVSChina%2Fdevkit-door-monitor%2Fmaster%2Fazuredeploy.json)

您會看見下列頁面。

> [!NOTE]
> 若未看到下列頁面，您可能需要先登入 Azure 帳戶。

完成註冊表單：

  * **資源群組**：建立用以裝載 SendGrid 服務的資源群組，或使用現有資源群組。 請參閱[使用資源群組來管理您的 Azure 資源](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-portal)。

  * **IoT 中樞名稱**：IoT 中樞的名稱。 請選擇與您可能有的其他服務不同的唯一名稱。

  * **Iot 中樞 Sku**：F1 (每個訂閱限用一個) 是免費的。 您可以在[定價與級別層](https://azure.microsoft.com/pricing/details/iot-hub/)檢視更多定價資訊。

  * **寄件者電子郵件**：這應該是您在設定 SendGrid 服務時所使用的相同電子郵件地址。

  > [!NOTE]
  > 請勾選 [釘選到儀表板] 選項，以方便日後尋找此應用程式。
 
![IoTHub 部署](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/iot-hub-deploy.png)

## <a name="build-and-upload-the-code"></a>建置並上傳程式碼

### <a name="start-vs-code"></a>啟動 VS Code

- 確定 DevKit **未**連線至您的電腦。
- 啟動 VS Code。
- 將 DevKit 連接到您的電腦。

VS Code 會自動偵測您的 DevKit，並開啟簡介頁面：

![VSCode](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-start.png)

> [!NOTE]
> 當您啟動 VS Code 時，您可能會收到錯誤訊息，指出找不到 Arduino IDE 或相關面板套件。 如果收到此錯誤，請關閉 VS Code，再重新啟動 Arduino IDE，VS Code 應該會正確找出 Arduino IDE 路徑。

### <a name="open-arduino-examples-folder"></a>開啟 Arduino 範例資料夾

展開左側的 [ARDUINO 範例] 區段，瀏覽至 [MXCHIP AZ3166 的範例] > [AzureIoT]，然後選取 [DoorMonitor]。 此動作會開啟一個內含專案資料夾的新 VS Code 視窗。

![mini-solution-examples](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/vscode-examples.png)

如果您不小心關閉該窗格，可予以重新開啟。 請使用 `Ctrl+Shift+P` (macOS：`Cmd+Shift+P`) 來開啟命令選擇區，輸入 **Arduino**，然後尋找並選取 [Arduino: Examples] \(Arduino: 範例\)。

### <a name="provision-azure-services"></a>佈建 Azure 服務

在解決方案方案視窗中，執行雲端佈建工作：
- 輸入 `Ctrl+P` (macOS: `Cmd+P`)。
- 在提供的文字方塊中輸入 `task cloud-provision`。

VS Code 終端機中會有互動式命令列引導您佈建所需的 Azure 服務。 從您先前在[在 Azure 中部署 IoT 中樞](#deploy-iot-hub-in-azure)中佈建的提示清單中，選取所有相同的項目。

![雲端佈建](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/cloud-provision.png)

> [!NOTE]
> 如果在嘗試登入 Azure 時，頁面於載入中狀態停止回應，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#page-hangs-when-log-in-azure)以解決此問題。 

### <a name="build-and-upload-the-device-code"></a>組建並上傳裝置程式碼

#### <a name="windows"></a>Windows

1. 使用 `Ctrl+P` 來執行 `task device-upload`。
2. 終端機會提示您進入設定模式。 若要這樣做，請按住按鈕 A，然後按下並放開 [重設] 按鈕。 畫面會顯示 DevKit 識別碼和*組態*一詞。

此程序會設定從[佈建 Azure 服務](#provision-azure-services)步驟中擷取的連接字串。

然後，VS Code 會開始驗證 Arduino 草圖並將其上傳至 DevKit：

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 在少數情況下，您可能會收到「錯誤：AZ3166：未知的套件」錯誤訊息。 未正確重新整理面板套件索引時，就會發生此錯誤。 若要解決此錯誤，請參閱此[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

#### <a name="macos"></a>macOS

1. 讓 DevKit 進入設定模式：按住 A 按鈕，然後按下再放開 [重設] 按鈕。 畫面會顯示 [Configuraiton] \(設定\)。
2. 使用 `Cmd+P` 來執行 `task device-upload`。

此程序會設定從[佈建 Azure 服務](#provision-azure-services)步驟中擷取的連接字串。

然後，VS Code 會開始驗證 Arduino 草圖並將其上傳至 DevKit：

![device-upload](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/device-upload.png)

DevKit 會重新開機，然後開始執行程式碼。

> [!NOTE]
> 在少數情況下，您可能會收到「錯誤：AZ3166：未知的套件」錯誤訊息。 未正確重新整理面板套件索引時，就會發生此錯誤。 若要解決此錯誤，請參閱此[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)。

## <a name="test-the-project"></a>測試專案

程式首先初始化 DevKit 有穩定磁場存在時的情況。

初始化之後，`Door closed` 會顯示在畫面上。 磁場有所變更時，狀態會變更為 `Door opened`。 每當門戶狀態變更時，您就會收到電子郵件通知。 (最久可能需要五分鐘才會收到這些電子郵件訊息)。

![磁鐵接近感應器：門戶關閉](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-closed.jpg "磁鐵接近感應器：門戶關閉")

![磁鐵離開感應器：門戶開啟](media/iot-hub-arduino-iot-devkit-az3166-door-monitor/test-door-opened.jpg "磁鐵離開感應器：門戶開啟")

## <a name="problems-and-feedback"></a>問題與意見反應

如果您遇到問題，請參閱[常見問題集](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)或透過下列管道與我們連絡：

* [Gitter.im](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stackoverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>後續步驟

您已了解如何將 DevKit 裝置連線至 Azure IoT 套件，並使用 SendGrid 服務傳送電子郵件。 以下是建議的後續步驟：

* [Azure IoT 套件概觀](https://docs.microsoft.com/azure/iot-suite/)
* [將 MXChip IoT DevKit 裝置連線到您的 Microsoft IoT Central 應用程式](https://docs.microsoft.com/en-us/microsoft-iot-central/howto-connect-devkit)
