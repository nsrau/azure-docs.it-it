---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: eabfca64a4bdf1f72375575161111ddaff55eba3
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66123440"
---
Azure Data Factory supporta le seguenti attività di trasformazione che possono essere aggiunte alle pipeline singolarmente o con un'altra attività concatenata.

| Attività di trasformazione dei dati | Ambiente di calcolo |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Attività di Machine Learning: Esecuzione batch e Aggiorna risorsa](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Macchina virtuale di Azure |
| [Stored procedure](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse o SQL Server |
| [Attività U-SQL di Data Lake Analytics](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics. |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] o Batch di Azure |

> [!NOTE]
> È possibile usare l'attività MapReduce per eseguire i programmi Spark nel cluster HDInsight Spark. Per informazioni dettagliate, vedere [Chiamare i programmi Spark da Data Factory](../articles/data-factory/v1/data-factory-spark.md) .
> È possibile creare un'attività personalizzata per eseguire gli script R nel cluster HDInsight con R installato. Vedere [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)(Esempio relativo all'esecuzione di script R con Azure Data Factory).
> 
> 

