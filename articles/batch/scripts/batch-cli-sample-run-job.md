---
title: "Azure CLI 指令碼範例 - 執行 Batch 作業 | Microsoft Docs"
description: "Azure CLI 指令碼範例 - 使用 Batch 執行作業"
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.service: batch
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/29/2018
ms.author: danlep
ms.openlocfilehash: d63854b213b33a3c26358df17761999f5270d7a2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/09/2018
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>CLI 範例：使用 Azure Batch 執行作業和工作

此指令碼會建立 Batch 工作，並將一系列作業加入至工作。 它也示範如何監視工作和其作業。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您選擇在本機安裝和使用 CLI，本文會要求您執行 Azure CLI 2.0.20 版或更新版本。 執行 `az --version` 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

## <a name="example-script"></a>範例指令碼

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

## <a name="clean-up-deployment"></a>清除部署

執行下列命令來移除資源群組及其所有相關聯的資源。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>指令碼說明

此指令碼會使用下列命令。 下表中的每個命令都會連結至命令特定的文件。

| 命令 | 注意 |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | 建立用來存放所有資源的資源群組。 |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | 建立 Batch 帳戶。 |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | 對指定的 Batch 帳戶驗證以進行進一步的 CLI 互動。  |
| [az batch pool create](https://docs.microsoft.com/cli/azure/batch/pool#az_batch_pool_create) | 建立計算節點的集區。  |
| [az batch job create](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_create) | 建立 Batch 工作。  |
| [az batch task create](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_create) | 將作業加入至指定的 Batch 工作。  |
| [az batch job set](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_set) | 更新 Batch 工作的屬性。  |
| [az batch job show](https://docs.microsoft.com/cli/azure/batch/job#az_batch_job_show) | 擷取指定的 Batch 工作的詳細資料。  |
| [az batch task show](https://docs.microsoft.com/cli/azure/batch/task#az_batch_task_show) | 從指定的 Batch 工作擷取作業的詳細資料。  |
| [az group delete](/cli/azure/group#az_group_delete) | 刪除資源群組，包括所有的巢狀資源。 |

## <a name="next-steps"></a>後續步驟

如需 Azure CLI 的詳細資訊，請參閱 [Azure CLI 文件](/cli/azure)。