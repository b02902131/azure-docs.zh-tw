---
title: Azure SQL 資料倉儲效能層級 | Microsoft Docs
description: Azure SQL 資料倉儲中可用之針對彈性與計算最佳化的效能層級簡介。
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: ''
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 11/10/2017
ms.author: jrj;barbkess
ms.openlocfilehash: 03881c12faed723999e97431e4a69fdeb6bfa10d
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/12/2018
---
# <a name="azure-sql-data-warehouse-performance-tiers-preview"></a>Azure SQL 資料倉儲效能層級 (預覽)
SQL 資料倉儲提供兩個最佳化以用於分析工作負載的效能層級。 本文說明效能層級的概念，可協助您選擇最適合工作負載的效能層級。 

## <a name="what-is-a-performance-tier"></a>什麼是效能層級？
效能層級是可決定您資料倉儲設定的選項。 這個選項是您在建立資料倉儲時最先做出的選擇之一。  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- **針對彈性最佳化的效能層級**會分隔架構中的計算和儲存體層級。 此選項的過人之處在於工作負載可以充分利用計算和儲存體之間的分離，方法是經常調整以支援短期的尖峰活動。 此計算層級具有最低的項目價格點，並可調整來支援大部分的客戶工作負載。

- **針對計算最佳化的效能層級**會使用最新的 Azure 硬體來導入新的 NVMe 固態硬碟快取，將最常存取的資料保留在 CPU 附近，這正是您想要的位置。 藉由對儲存體進行自動階層處理，此效能層級在複雜查詢部分具有優勢，因為所有 IO 都會保持在計算層級的本機。 此外，已增強資料行存放區，以便在 SQL 資料倉儲中儲存無限數量的資料。 針對計算最佳化的效能層級會提供最高層級的延展性，讓您可以相應增加為 30,000 個計算資料倉儲單位 (cDWU)。 為需要連續、快速、效能的工作負載選擇此層級。

## <a name="service-levels"></a>服務等級
服務等級目標 (SLO) 是決定您資料倉儲之成本和效能層級的延展性設定。 針對計算最佳化之效能層級範圍的服務等級會以計算資料倉儲單位 (cDWU) 來測量，例如 DW2000c。 針對彈性最佳化的服務等級則會以 DWU 來測量，例如 DW2000。 如需詳細資訊，請參閱[什麼是資料倉儲單位？](what-is-a-data-warehouse-unit-dwu-cdwu.md)

在 T-SQL 中，SERVICE_OBJECTIVE 設定會決定您資料倉儲適用的服務等級和效能層級。

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="memory-maximums"></a>記憶體最大值
效能層級具有不同的記憶體設定檔，其會針對每個查詢轉譯成不同的記憶體數量。 比起針對彈性最佳化的效能層級，針對計算最佳化的效能層級會為每個查詢提供 2.5 倍以上的記憶體。 這個額外的記憶體可協助針對計算最佳化的效能層級提供它的快速效能。 每個查詢的額外記憶體也可讓您同時執行更多查詢，因為查詢可以使用較低的[資源類別](resource-classes-for-workload-management.md)。 

### <a name="optimized-for-elasticity"></a>針對彈性最佳化

針對彈性最佳化之效能層級的服務等級範圍可從 DW100 到 DW6000。 

| 服務等級 | 並行查詢上限 | 計算節點數量 | 每個計算節點上的散發數量 | 每個散發的最大記憶體 (MB) | 每個資料倉儲的最大記憶體 (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8,000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>針對計算最佳化

針對計算最佳化之效能層級的服務等級範圍可從 DW1000c 到 DW30000c。 

| 服務等級 | 並行查詢上限 | 計算節點數量 | 每個計算節點上的散發數量 | 每個散發的最大記憶體 (GB) | 每個資料倉儲的最大記憶體 (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

最大的 cDWU 是 DW30000c，其中具有 60 個計算節點，而且每個計算節點上都有一個散發。 例如，DW30000c 上 600 TB 的資料倉儲大約可針對每個節點處理 10 TB。

## <a name="concurrency-maximums"></a>並行最大值
SQL 資料倉儲在單一資料倉儲上提供領先業界的並行。 為了確保每個查詢都有足夠的資源可以有效率地執行，系統會藉由將並行位置指派給每個查詢，來追蹤計算資源使用量。 系統會將查詢放入佇列，它們會在其中等候，直到有足夠的並行位置可用為止。 

並行位置也會決定 CPU 優先順序。 如需詳細資訊，請參閱[分析工作負載](analyze-your-workload.md)。

### <a name="concurrency-slots"></a>並行位置
並行位置是追蹤查詢執行可用之資源的便利方式。 它們就像是您因為音樂會的座位有限，而購買來保留座位的門票。 同樣地，SQL 資料倉儲具備有限數目的計算資源。 查詢會藉由取得並行位置來保留計算資源。 在查詢開始執行之前，它必須能夠保留足夠的並行位置。 當查詢完成時，即會釋出它的並行位置。 

* 針對彈性最佳化的效能層級會調整為 240 個並行位置。
* 針對計算最佳化的效能層級會調整為 1200 個並行位置。

每個查詢都將取用零個、一個或多個並行位置。 系統查詢和一些簡單式查詢不會取用任何位置。 根據預設，受到資源類別控管的查詢需要一個並行位置。 更複雜的查詢則需要額外的並行位置。  

- 比起以 2 個並行位置執行的查詢，以 10 個並行位置執行的查詢可以存取 5 倍以上的計算資源。
- 如果每個查詢需要 10 個並行位置且有 40 個並行位置，則只能同時執行 4 個查詢。
 
只有資源控管的查詢可取用並行位置。 取用的並行位置確切數目取決於查詢的[資源類別](resource-classes-for-workload-management.md)。

### <a name="optimized-for-compute"></a>針對計算最佳化
下表針對每個[動態資源類別](resource-classes-for-workload-management.md)顯示並行查詢數量和並行位置數量的最大值。  這些適用於針對計算最佳化的效能層級。

**動態資源類別**
| 服務等級 | 並行查詢上限 | 可用的並行位置數量 | Smallrc 所使用的插槽 | Mediumrc 所使用的插槽 | Largerc 所使用的插槽 | Xlargerc 所使用的插槽 |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**靜態資源類別**

下表針對每個[靜態資源類別](resource-classes-for-workload-management.md)顯示並行查詢數量和並行位置數量的最大值。  

| 服務等級 | 並行查詢上限 | 可用的並行位置數量 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>針對彈性最佳化
下表針對每個[動態資源類別](resource-classes-for-workload-management.md)顯示並行查詢數量和並行位置數量的最大值。  這些適用於針對彈性最佳化的效能層級。

**動態資源類別**

| 服務等級 | 並行查詢上限 | 可用的並行位置數量 | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**靜態資源類別** 下表針對每個[靜態資源類別](resource-classes-for-workload-management.md)顯示並行查詢數量和並行位置數量的最大值。  這些適用於針對彈性最佳化的效能層級。

| 服務等級 | 並行查詢上限 | 並行位置數量最大值 |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

當符合以上其中一個臨界值時，新的查詢將以先進先出順序排入佇列和執行。  當一個查詢完成，且查詢與位置的數目低於限制時，SQL 資料倉儲就會釋出已排入佇列的查詢。 

## <a name="next-steps"></a>後續步驟

若要深入了解如何運用資源類別，進一步將您的工作負載最佳化，請檢閱下列文章：
* [適用於工作負載管理的資源類別](resource-classes-for-workload-management.md)
* [分析工作負載](analyze-your-workload.md)

