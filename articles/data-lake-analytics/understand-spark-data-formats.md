---
title: Comprendere i formati di dati Apache Spark per gli sviluppatori U-SQL di Azure Data Lake Analytics.Understand Apache Spark data formats for Azure Data Lake Analytics U-SQL developers.
description: In questo articolo vengono descritti i concetti di Apache Spark che consentono agli sviluppatori di U_SQL comprendere le differenze tra i formati di dati U-SQL e Spark.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-data-formats
ms.date: 01/31/2019
ms.openlocfilehash: 36f39503ca32f1ee4b422ae7b1cf9abf48716f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648441"
---
# <a name="understand-differences-between-u-sql-and-spark-data-formats"></a>Comprendere le differenze tra i formati di dati U-SQL e Spark

Se si vuole usare [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) o [Azure HDInsight Spark,](../hdinsight/spark/apache-spark-overview.md)è consigliabile eseguire la migrazione dei dati da [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) ad [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)

Oltre a spostare i file, è anche necessario rendere i dati, archiviati in tabelle U-SQL, accessibili a Spark.

## <a name="move-data-stored-in-azure-data-lake-storage-gen1-files"></a>Spostare i dati archiviati nei file Gen1 di Archiviazione di Azure Data Lake

I dati memorizzati nei file possono essere spostati in vari modi:

- Scrivere una pipeline di [Azure Data Factory](../data-factory/introduction.md) per copiare i dati dall'account [Gen1](../data-lake-store/data-lake-store-overview.md) di Archiviazione dati di Azure all'account [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md)
- Scrivere un processo Spark che legge i dati dall'account [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) e lo scrive nell'account Azure Data Lake Storage [Gen2.](../storage/blobs/data-lake-storage-introduction.md) In base al tuo caso d'uso, potresti volerlo scrivere in un formato diverso, come Parquet se non è necessario conservare il formato di file originale.

È consigliabile leggere l'articolo Aggiornare le [soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)

## <a name="move-data-stored-in-u-sql-tables"></a>Spostare i dati archiviati nelle tabelle U-SQLMove data stored in U-SQL tables

Le tabelle U-SQL non sono comprese da Spark. Se si dispone di dati archiviati in tabelle U-SQL, si eseguirà un processo U-SQL che estrae i dati della tabella e li salva in un formato comprensibile a Spark. Il formato più appropriato è quello di creare un set di file di parquet seguendo il layout della cartella del metastore Hive.

L'output può essere ottenuto in U-SQL con l'emittente Parquet incorporato e utilizzando il partizionamento dinamico dell'output con set di file per creare le cartelle di partizione. [Elaborare più file che mai e utilizzare Parquet](https://blogs.msdn.microsoft.com/azuredatalake/2018/06/11/process-more-files-than-ever-and-use-parquet-with-azure-data-lake-analytics) fornisce un esempio di come creare tali dati consumabili Spark.

Dopo questa trasformazione, copiare i dati come descritto nel capitolo Spostare i [dati archiviati nei file Gen1](#move-data-stored-in-azure-data-lake-storage-gen1-files)di Archiviazione di Azure Data Lake .

## <a name="caveats"></a>Precisazioni

- Semantica dei dati Durante la copia dei file, la copia verrà eseguita a livello di byte. Pertanto, gli stessi dati dovrebbero essere visualizzati nell'account [Azure Data Lake Storage Gen2.](../storage/blobs/data-lake-storage-introduction.md) Si noti tuttavia che Spark potrebbe interpretare alcuni caratteri in modo diverso. Ad esempio, può utilizzare un valore predefinito diverso per un delimitatore di riga in un file CSV.
    Inoltre, se si stanno copiando dati tipizzati (da tabelle), Parquet e Spark possono avere precisione e scala diverse per alcuni dei valori tipizzati (ad esempio, un float) e possono trattare i valori null in modo diverso. Ad esempio, U-SQL ha la semantica di C , per i valori null, mentre Spark ha una logica a tre valori per i valori null.

- Le tabelle U-SQL dell'organizzazione dei dati (partizionamento) forniscono il partizionamento a due livelli. Il livello`PARTITIONED BY`esterno ( ) è per valore ed esegue il mapping principalmente nello schema di partizionamento Hive/Spark utilizzando gerarchie di cartelle. È necessario assicurarsi che i valori null siano mappati alla cartella corretta. Il livello`DISTRIBUTED BY`interno ( ) in U-SQL offre 4 schemi di distribuzione: round robin, intervallo, hash e hash diretto.
    Le tabelle Hive/Spark supportano solo il partizionamento del valore o il partizionamento hash, usando una funzione hash diversa da U-SQL. Quando si restituiscono i dati della tabella U-SQL, sarà probabilmente possibile eseguire il mapping solo nel partizionamento del valore per Spark e potrebbe essere necessario eseguire un'ulteriore ottimizzazione del layout dei dati a seconda delle query Spark finali.

## <a name="next-steps"></a>Passaggi successivi

- [Comprendere i concetti relativi al codice Spark per gli sviluppatori U-SQLUnderstand Spark code concepts for U-SQL developers](understand-spark-code-concepts.md)
- [Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET per Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Trasformare dati tramite l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Trasformare i dati usando l'attività Hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Informazioni su Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
