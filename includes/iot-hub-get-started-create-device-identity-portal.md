---
title: 包含檔案
description: 包含檔案
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/05/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b08bfcd4cb9e85f9e682efe0f599b6dd88897962
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2018
---
## <a name="create-a-device-identity"></a>建立裝置識別

在本節中，您會使用 [Azure 入口網站][lnk-azure-portal]在 IoT 中樞的身分識別登錄中建立裝置識別。 裝置無法連線到 IoT 中樞，除非它在身分識別登錄中具有項目。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]的＜身分識別登錄＞一節。 使用入口網站中的 [IoT 裝置] 窗格為您的裝置產生唯一的裝置識別碼和金鑰，用來向 IoT 中樞識別本身。 裝置識別碼會區分大小寫。

1. 確定您已登入 [Azure 入口網站][lnk-azure-portal]。

1. 在 Jumpbar 中，按一下 [所有資源] 並尋找您的 IoT 中樞資源。

    ![瀏覽至您的 IoT 中樞][img-find-iothub]

1. 當您的 IoT 中樞資源開啟時，按一下 [IoT 裝置] 工具，然後按一下頂端的 [新增]。 提供新裝置的名稱 (例如 **myDeviceId**)，然後按一下 [儲存]。

    ![在入口網站中建立裝置識別][img-create-device]

   此動作會為您的 IoT 中樞建立新的裝置身分識別。

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 在 [IoT 裝置] 的裝置清單中，按一下新建立的裝置並記下 [連接字串---主索引鍵]。

    ![裝置連接字串][img-connection-string]

> [!NOTE]
> IoT 中樞身分識別登錄只會儲存裝置身分識別，以啟用對 IoT 中樞的安全存取。 它會儲存裝置識別碼和金鑰來作為安全性認證，以及啟用或停用旗標，讓您用來停用個別裝置的存取。 如果您的應用程式需要儲存其他裝置特定的中繼資料，它應該使用應用程式專用的存放區。 如需詳細資訊，請參閱 [IoT 中樞開發人員指南][lnk-devguide-identity]。

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md

