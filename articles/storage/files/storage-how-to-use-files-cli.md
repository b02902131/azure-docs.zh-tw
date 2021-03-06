---
title: 使用 Azure CLI 管理 Azure 檔案共用
description: 了解如何使用 Azure CLI 來管理 Azure 檔案服務。
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 066a43b553be18a5a0bc889fff441824df301b98
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-using-the-azure-cli"></a>使用 Azure CLI 管理 Azure 檔案共用
[Azure 檔案服務](storage-files-introduction.md)是 Microsoft 易於使用的雲端檔案系統。 Azure 檔案共用可在 Windows、Linux 和 macOS 中掛接。 本指南會逐步說明透過 Azure CLI 來使用 Azure 檔案共用的基本概念。 了解如何： 

> [!div class="checklist"]
> * 建立資源群組和儲存體帳戶
> * 建立 Azure 檔案共用 
> * 建立目錄
> * 上傳檔案 
> * 下載檔案
> * 建立及使用共用快照集

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果您決定在本機安裝和使用 Azure CLI，本指南會要求您執行 Azure CLI 2.0.4 版或更新版本。 執行 **az --version** 以尋找版本。 如果您需要安裝或升級，請參閱[安裝 Azure CLI 2.0](/cli/azure/install-azure-cli)。 

根據預設，Azure CLI 命令會傳回 JSON (JavaScript 物件標記法)，這是透過 REST API 傳送和接收訊息的既定方法。 為了方便處理 JSON 回應，本指南中有些範例會在 Azure CLI 命令上使用查詢參數。 這個參數會使用 [JMESPath 查詢語言](http://jmespath.org/) 來剖析 JSON。 您可以進一步了解如何管理 Azure CLI 命令的結果，依照[JMESPath 教學課程](http://jmespath.org/tutorial.html)。

## <a name="create-a-resource-group"></a>建立資源群組
資源群組是在其中部署與管理 Azure 資源的邏輯容器。 如果您還沒有 Azure 資源群組，您可以使用 [az group create](/cli/azure/group#create) 命令新建一個。 

下列範例會在 [美國東部] 位置建立名為 myResourceGroup 的資源群組。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>建立儲存體帳戶
儲存體帳戶是您可在其中部署 Azure 檔案共用或其他儲存體資源 (例如 blob 或佇列) 的共用儲存體集區。 儲存體帳戶可包含無限制數目的檔案共用，而共用可儲存無限制數目的檔案，最多可達儲存體帳戶的容量限制。

此範例會使用 [az storage account create](/cli/azure/storage/account#create) 建立名為 `mystorageaccount<random number>` 的儲存體帳戶，然後在變數 `$STORAGEACCT` 中放入該儲存體帳戶的名稱。 儲存體帳戶名稱必須是唯一的，請使用 `$RANDOM` 在儲存體帳戶名稱的尾端附加一個數字，使其成為唯一名稱。 

```azurecli-interactive 
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

### <a name="get-the-storage-account-key"></a>取得儲存體帳戶金鑰
儲存體帳戶金鑰可用來控制儲存體帳戶中的資源存取。 當您建立儲存體帳戶時，系統會自動建立其金鑰。 您可以使用 [az storage account keys list](/cli/azure/storage/account/keys#list) 命令取得儲存體帳戶的儲存體帳戶金鑰。 

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-an-azure-file-share"></a>建立 Azure 檔案共用
現在，您可以建立第一個 Azure 檔案共用。 您可以使用 [az storage share create](/cli/azure/storage/share#create) 命令來建立檔案共用。 此範例會建立名為 `myshare` 的 Azure 檔案共用。 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

> [!Important]  
> 共用名稱必須全部使用小寫字母、數字和單一連字號，但開頭不可以是連字號。 如需有關為檔案共用與檔案命名的完整詳細資料，請參閱 [命名和參考共用、目錄、檔案及中繼資料](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)。

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>處理 Azure 檔案共用的內容
您現在已建立 Azure 檔案共用，並且可使用 SMB 將檔案共用掛接在 [Windows](storage-how-to-use-files-windows.md)、[Linux](storage-how-to-use-files-linux.md) 或 [macOS](storage-how-to-use-files-mac.md) 上。 或者，您可以使用 Azure CLI 來處理 Azure 檔案共用。 此做法會比使用 SMB 掛接檔案共用更有利，因為使用 Azure CLI 提出的所有要求都會使用檔案 REST API 來執行，而讓您能夠從下列途徑建立、修改及刪除檔案共用中的檔案和目錄：

- Bash Cloud Shell (無法透過 SMB 掛接檔案共用)。
- 無法掛接 SMB 共用的用戶端，例如，未解除封鎖連接埠 445 的內部部署用戶端。
- 無伺服器案例，例如在 [Azure Functions](../../azure-functions/functions-overview.md) 中。 

### <a name="create-directory"></a>建立目錄
若要在 Azure 檔案共用的根目錄建立名為 *myDirectory* 的新目錄，請使用 [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) 命令。

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --name "myDirectory" 
```

### <a name="upload-a-file"></a>上傳檔案
為了示範如何使用 [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) 命令上傳檔案，我們必須先在 Azure CLI Cloud Shell 的可用磁碟機內建立要上傳的檔案。 在下列範例中，我們會建立並刪除此檔案。

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --source "~/clouddrive/SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

如果您要在本機執行 Azure CLI，則應將 `~/clouddrive` 替換為存在於您的機器上的路徑。

上傳檔案之後，您可以使用 [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) 命令來確認檔案已上傳至 Azure 檔案共用。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory" \
    --output table
```

### <a name="download-a-file"></a>下載檔案
您可以使用 [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) 密令，將您上傳的檔案複本下載到 Cloud Shell 的可用磁碟機。

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before
rm -rf ~/clouddrive/SampleDownload.txt

az storage file download \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt" \
    --dest "~/clouddrive/SampleDownload.txt"
```

### <a name="copy-files"></a>複製檔案
常見工作之一，是在不同的檔案共用之間複製檔案，或是對 (從) Azure Blob 儲存體容器複製檔案。 為了示範這項功能，您可以建立新的共用，並使用 [az storage file copy](/cli/azure/storage/file/copy) 命令將您上傳的檔案複製到這個新的共用。 

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2"

az storage directory create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --name "myDirectory2"

az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-share "myshare" \
    --source-path "myDirectory/SampleUpload.txt" \
    --destination-share "myshare2" \
    --destination-path "myDirectory2/SampleCopy.txt"
```

現在，當您列出新共用中的檔案時，您應該會看到您所複製的檔案。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare2" \
    --output table
```

雖然 `az storage file copy start` 命令方便用來進行 Azure 檔案共用與 Azure Blob 儲存體容器之間的臨機操作檔案移動，但我們建議較大型的移動 (就移動的檔案數目和大小而言) 應使用 AzCopy。 深入了解 [適用於 Linux 的 AzCopy](../common/storage-use-azcopy-linux.md) 和[適用於 Windows 的 AzCopy](../common/storage-use-azcopy.md)。 AzCopy 必須安裝於本機 - 它不適用於 Cloud Shell 

## <a name="create-and-modify-share-snapshots"></a>建立及修改共用快照集
可以使用 Azure 檔案共用來執行的另一項實用工作，是建立共用快照集。 快照集會保留 Azure 檔案共用的時間點。 共用快照集類似於您可能已經很熟悉的下列作業系統技術：
- Linux 系統的[邏輯磁碟區管理員 (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) 快照集
- macOS 的 [Apple 檔案系統 (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) 快照集。 
- Windows 檔案系統的[磁碟區陰影複製服務 (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636)，例如 NTFS 和 ReFS

您可以使用 [`az storage share snapshot`](/cli/azure/storage/share#az_storage_share_snapshot) 命令來建立共用快照集。

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --query "snapshot" | tr -d '"')
```

### <a name="browse-share-snapshot-contents"></a>瀏覽共用快照集內容
您可以將我們在 `$SNAPSHOT` 變數中擷取之共用快照集的時間戳記傳遞至 `az storage file list` 命令，以瀏覽共用快照集的內容。

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

### <a name="list-share-snapshots"></a>列出共用快照集
您可以使用下列命令，檢視您為共用建立的快照集清單。

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot \
    --query "[? name=='myshare' && snapshot!=null]" | tr -d '"'
```

### <a name="restore-from-a-share-snapshot"></a>從共用快照集還原
您可以使用我們先前使用的 `az storage file copy start` 命令來還原檔案。 首先，我們會先刪除我們所上傳的 `SampleUpload.txt` 檔案，以便從快照集加以還原。

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --query "primaryEndpoints.file" | tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>刪除共用快照集
您可以使用 [`az storage share delete`](/cli/azure/storage/share#az_storage_share_delete) 命令，並指定包含 `--snapshot` 參數之 `$SNAPSHOT` 參考的變數，來刪除共用快照集。

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --snapshot $SNAPSHOT
```

## <a name="clean-up-resources"></a>清除資源
完成作業後，您可以使用 [`az group delete`](/cli/azure/group#delete) 命令來移除資源群組和所有相關資源。 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```

或者，您可以逐一移除資源：
- 以移除我們為此快速入門建立的 Azure 檔案共用。

```azurecli-interactive
az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" \
    --delete-snapshots include

az storage share delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare2" \
    --delete-snapshots include
```

- 以移除儲存體帳戶本身 (這表示將會移除我們建立的 Azure 檔案共用，以及您已建立的任何其他儲存體資源，例如 Azure Blob 儲存體容器)。

```azurecli-interactive
az storage account delete \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT \
    --yes
```

## <a name="next-steps"></a>後續步驟
- [使用 Azure 入口網站管理檔案共用](storage-how-to-use-files-portal.md)
- [使用 Azure PowerShell 管理檔案共用](storage-how-to-use-files-powershell.md)
- [使用儲存體總管來管理檔案共用](storage-how-to-use-files-storage-explorer.md)
- [規劃 Azure 檔案部署](storage-files-planning.md)