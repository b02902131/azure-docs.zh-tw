---
title: 包含檔案
description: 包含檔案
services: virtual-machines
author: zivraf
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: 9666a8fde808981dd798ff712b96a7c620c9003a
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/16/2018
---
## <a name="view-vms-scheduled-for-maintenance-in-the-portal"></a>在入口網站中檢視排定維護的 VM

一旦排定了計劃的一波維護，並傳送通知，您就可以觀察受到即將到來之一波維護影響的虛擬機器清單。 

您可以使用 Azure 入口網站並尋找已排程維護的 VM。

1. 登入 [Azure 入口網站](https://portal.azure.com)。

2. 在左側導覽中，按一下 [虛擬機器]。

3. 在 [虛擬機器] 窗格中，按一下 [資料行] 按鈕，開啟可用資料行的清單。

4. 選取並新增下列資料行︰

   **維護** - 顯示 VM 的維護狀態。 以下是可能值：
      
      | 值 | 說明 |
      |-------|-------------|
      | 立即開始 | VM 處於自助維護視窗，可讓您在這裡自行起始維護。 請參閱下述，以瞭解如何在您的 VM 上開始維護 | 
      | 已排程 | 已排定 VM 進行維護，沒有選項供您起始維護。 您可以在此檢視中選取 [自動排程] 視窗或按一下 VM，來了解維護視窗 | 
      | Completed | 您已成功起始，並在 VM 上完成維護。 | 
      | Skipped| 您已選取起始維護，但是沒有成功。 您將無法使用自助式維護選項。 您的虛擬機器必須由 Azure 在排定維護階段期間重新啟動。 | 

   **主動維護** - 顯示您可以在 VM 上自行開始維護的時間範圍。
   
   **維護排程** - 顯示 Azure 將重新啟動您的 VM，以便完成維護的時間範圍。 




## <a name="notification-and-alerts-in-the-portal"></a>入口網站中的通知和警示

Azure 會將電子郵件傳送至訂用帳戶擁有者和共同擁有者群組，來傳達計劃性維護排程。 您可以建立 Azure 活動記錄警示，將其他收件者和通道新增到這個通訊。 如需詳細資訊，請參閱 [使用 Azure 活動記錄監視訂用帳戶活動] (../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md)

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在左側功能表中，選取 [監視]。 
3. 在 [監視 - 活動記錄] 窗格中，選取 [警示]。
4. 在 [監視 - 通知] 窗格中，按一下 [+ 新增活動記錄警示]。
5. 完成 [新增活動記錄警示]頁面中的資訊，並確定您在 [準則] 中設定下列項目：[類型]：維護  [狀態]：所有 (請勿將狀態設為 [使用中] 或 [已解決])  [層級]：所有
    
若要進一步了解如何設定活動記錄警示，請參閱[建立活動記錄警示](../articles/monitoring-and-diagnostics/monitoring-activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-vm-from-the-portal"></a>從入口網站在 VM 上開始維護

查閱 VM 詳細資料時，您將能夠看到其他與維護相關的詳細資料。  
在 VM 詳細資料檢視的頂端，如果您的 VM 已包含在計劃的一波維護中，則將會新增通知功能區。 此外，還會新增選項以在可能時開始維護。 


按一下維護通知，以查看對計劃性維護具有其他詳細資料的維護頁面。 從這裡您將能夠在您的 VM 上**開始維護**。

一旦開始維護，虛擬機器將會重新啟動，而且維護狀態將在幾分鐘內更新以反映結果。

如果遺漏了可以開始維護的視窗，您仍然可以在 Azure 重新啟動 VM 時看到視窗。 
