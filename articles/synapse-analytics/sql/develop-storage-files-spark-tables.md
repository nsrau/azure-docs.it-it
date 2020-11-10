---
title: Sincronizzare Apache Spark per le definizioni di tabelle esterne in un pool SQL serverless (anteprima)
description: Panoramica su come eseguire query su tabelle Spark con un pool SQL serverless (anteprima)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: ea4e7cd009be8a78faa0dcfab44371a350b6a200
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93315823"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-serverless-sql-pool-preview"></a>Sincronizzare Apache Spark per le definizioni di tabelle esterne di Azure Synapse in un pool SQL serverless (anteprima)

Il pool SQL serverless (anteprima) consente di sincronizzare automaticamente i metadati di Apache Spark. Per ogni database esistente nei pool di Apache Spark (anteprima) verrà creato un database del pool SQL serverless. 

Per ogni tabella esterna Spark basata su Parquet e inclusa in Archiviazione di Azure, viene creata una tabella esterna nel database del pool SQL serverless. Di conseguenza, è possibile arrestare i pool di Spark e continuare a eseguire query sulle tabelle esterne Spark dal pool SQL serverless.

Quando una tabella viene partizionata in Spark, i file nell'account di archiviazione sono organizzati in base a cartelle. Per la query, il pool SQL serverless userà i metadati della partizione e solo le cartelle e i file pertinenti come destinazione.

La sincronizzazione dei metadati viene configurata automaticamente per ogni pool di Apache Spark serverless nell'area di lavoro di Azure Synapse. È possibile iniziare immediatamente a eseguire query sulle tabelle esterne Spark.

Ogni tabella esterna Spark basata su Parquet inclusa in Archiviazione di Azure viene rappresentata con una tabella esterna in uno schema dbo che corrisponde a un database del pool SQL serverless. 

Per le tabelle esterne Spark, eseguire una query destinata a una tabella esterna [spark_table]. Prima di eseguire l'esempio seguente, assicurarsi di avere l'[accesso corretto all'account di archiviazione](develop-storage-files-storage-access-control.md) in cui si trovano i file.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> I comandi di aggiunta, eliminazione o modifica per una colonna della tabella esterna Spark non verranno riflessi nella tabella esterna nel pool SQL serverless.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mapping tra tipi di dati Apache Spark e tipi di dati SQL

| Tipo di dati Spark | Tipo di dati SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType      | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)\*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)\* (in JSON)\** |
| MapType         | varchar(max)\* (in JSON)\** |
| StructType      | varchar(max)\* (in JSON)\** |

\* Le regole di confronto usate sono Latin1_General_100_BIN2_UTF8.

\** ArrayType, MapType e StructType sono rappresentati come JSON.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul controllo di accesso all'archiviazione, vedere l'articolo [corrispondente](develop-storage-files-storage-access-control.md).
