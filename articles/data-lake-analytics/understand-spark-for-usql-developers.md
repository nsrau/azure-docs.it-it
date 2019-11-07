---
title: Informazioni Apache Spark per gli sviluppatori Azure Data Lake Analytics U-SQL.
description: Questo articolo descrive Apache Spark concetti per facilitare le differenze tra gli sviluppatori U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73648428"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Informazioni Apache Spark per gli sviluppatori U-SQL

Microsoft supporta diversi servizi di analisi, ad esempio [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) e [Azure HDInsight](../hdinsight/hdinsight-overview.md) , oltre a Azure Data Lake Analytics. Gli sviluppatori hanno a disposizione una chiara preferenza per le soluzioni open source Man mano che compilano pipeline di analisi. Per consentire agli sviluppatori di U-SQL di comprendere Apache Spark e come è possibile trasformare gli script U-SQL in Apache Spark, abbiamo creato questa guida.

Include una serie di passaggi che è possibile eseguire e diverse alternative.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Passaggi per trasformare U-SQL in Apache Spark

1. Trasformare le pipeline di orchestrazione dei processi.

   Se si usa [Azure Data Factory](../data-factory/introduction.md) per orchestrare gli script di Azure Data Lake Analytics, è necessario modificarli per orchestrare i nuovi programmi Spark.
2. Informazioni sulle differenze tra la modalità di gestione dei dati in U-SQL e Spark

   Se si desidera spostare i dati da [Azure Data Lake storage Gen1](../data-lake-store/data-lake-store-overview.md) a [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md), sarà necessario copiare i dati dei file e dei dati gestiti dal catalogo. Si noti che Azure Data Lake Analytics supporta solo Azure Data Lake Storage Gen1. Vedere [informazioni sui formati di dati Spark](understand-spark-data-formats.md)
3. Trasformare gli script U-SQL in Spark

   Prima di trasformare gli script U-SQL, sarà necessario scegliere un servizio di analisi. Alcuni dei servizi di calcolo disponibili sono:
      - [Flusso di Azure Data Factory](../data-factory/concepts-data-flow-overview.md) I flussi di dati di mapping sono trasformazioni di dati progettate visivamente che consentono agli ingegneri di dati di sviluppare una logica di trasformazione dei dati con interfaccia grafica senza scrivere codice. Sebbene non sia adatto a eseguire codice utente complesso, può rappresentare facilmente le tradizionali trasformazioni del flusso di tempo di tipo SQL
      - [Hive di Azure HDInsight](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive su HDInsight è adatto per operazioni di estrazione, trasformazione e caricamento (ETL). Ciò significa che si intende convertire gli script U-SQL in Apache Hive.
      - Apache Spark motori come [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) o [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) questo significa che si intende convertire gli script U-SQL in Spark. Per altre informazioni, vedere [comprendere i formati di dati Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Sia [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) che [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) sono servizi cluster e non processi senza server come Azure Data Lake Analytics. È necessario considerare come effettuare il provisioning dei cluster per ottenere il rapporto costi/prestazioni appropriato e come gestirne la durata per ridurre al minimo i costi. Questi servizi hanno caratteristiche di prestazioni diverse con il codice utente scritto in .NET, pertanto sarà necessario scrivere wrapper o riscrivere il codice in una lingua supportata. Per altre informazioni, vedere informazioni sui [formati di dati Spark](understand-spark-data-formats.md), informazioni sui [concetti del codice Apache Spark per sviluppatori U-SQL](understand-spark-code-concepts.md), [.NET per Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui formati di dati Spark per sviluppatori U-SQL](understand-spark-data-formats.md)
- [Informazioni sui concetti di codice Spark per gli sviluppatori U-SQL](understand-spark-code-concepts.md)
- [Aggiornare le soluzioni di analisi di Big Data da Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET per Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Trasformare i dati usando l'attività hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Trasformare i dati usando l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Che cos'è Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
