---
title: Sincronizzare Apache Spark per le definizioni di tabella esterna di Azure Synapse in SQL su richiesta (anteprima)
description: Panoramica su come eseguire query su tabelle Spark con SQL su richiesta (anteprima)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: db6b2d95bf8f38495296885d14260b9900af1d51
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86247048"
---
# <a name="synchronize-apache-spark-for-azure-synapse-external-table-definitions-in-sql-on-demand-preview"></a>Sincronizzare Apache Spark per le definizioni di tabella esterna di Azure Synapse in SQL su richiesta (anteprima)

SQL su richiesta (anteprima) consente di sincronizzare automaticamente i metadati da Apache Spark per i pool di Azure Synapse. Per ogni database esistente nei pool di Spark verrà creato un database di SQL su richiesta (anteprima). 

Per ogni tabella esterna Spark basata su Parquet e inclusa in Archiviazione di Azure, viene creata una tabella esterna nel database di SQL su richiesta. Di conseguenza, è possibile arrestare i pool di Spark e continuare a eseguire query sulle tabelle esterne Spark da SQL su richiesta.

Quando una tabella viene partizionata in Spark, i file nell'account di archiviazione sono organizzati in base a cartelle. Per la query, SQL su richiesta utilizzerà i metadati della partizione e solo le cartelle e i file pertinenti come destinazione.

La sincronizzazione dei metadati viene configurata automaticamente per ogni pool di Spark con provisioning nell'area di lavoro di Azure Synapse. È possibile iniziare immediatamente a eseguire query sulle tabelle esterne Spark.

Ogni tabella esterna Spark basata su Parquet che si trova in Archiviazione di Azure viene rappresentata con una tabella esterna in uno schema dbo che corrisponde a un database di SQL su richiesta. 

Per le tabelle esterne Spark, eseguire una query destinata a una tabella esterna [spark_table]. Prima di eseguire l'esempio seguente, assicurarsi di avere l'[accesso corretto all'account di archiviazione](develop-storage-files-storage-access-control.md) in cui si trovano i file.

```sql
SELECT * FROM [db].dbo.[spark_table]
```

> [!NOTE]
> L'aggiunta, l'eliminazione o la modifica della colonna della tabella esterna Spark non verrà applicata alla tabella esterna in SQL su richiesta.

## <a name="apache-spark-data-types-to-sql-data-types-mapping"></a>Mapping tra tipi di dati Apache Spark e tipi di dati SQL

| Tipo di dati Spark | Tipo di dati SQL               |
| --------------- | --------------------------- |
| ByteType        | smallint                    |
| ShortType       | smallint                    |
| IntegerType     | int                         |
| LongType        | bigint                      |
| FloatType       | real                        |
| DoubleType      | float                       |
| DecimalType     | decimal                     |
| TimestampType   | datetime2                   |
| DateType        | date                        |
| StringType      | varchar(max)*               |
| BinaryType      | varbinary                   |
| BooleanType     | bit                         |
| ArrayType       | varchar(max)* (into JSON)** |
| MapType         | varchar(max)* (into JSON)** |
| StructType      | varchar(max)* (into JSON)** |

\* Le regole di confronto usate sono Latin1_General_100_BIN2_UTF8.

**ArrayType, MapType e StructType sono rappresentati come JSON.



## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul controllo di accesso all'archiviazione, vedere l'articolo [corrispondente](develop-storage-files-storage-access-control.md).
