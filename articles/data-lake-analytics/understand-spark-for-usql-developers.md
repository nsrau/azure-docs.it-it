---
title: Comprendere Apache Spark per gli sviluppatori U-SQL di Azure Data Lake Analytics.Understand Apache Spark for Azure Data Lake Analytics U-SQL developers.
description: In questo articolo vengono descritti i concetti di Apache Spark che consentono di individuare le differenze tra gli sviluppatori U-SQL.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: understand-apache-spark-for-usql-developers
ms.date: 10/15/2019
ms.openlocfilehash: 594e1055c4c063e4e151fefa3e183e6e799c90b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73648428"
---
# <a name="understand-apache-spark-for-u-sql-developers"></a>Informazioni su Apache Spark per sviluppatori U-SQL

Microsoft supporta diversi servizi di Analytics, ad esempio Azure Databricks e [Azure HDInsight,](../hdinsight/hdinsight-overview.md) nonché Azure Data Lake Analytics.Microsoft supports several Analytics services such as [Azure Databricks](../azure-databricks/what-is-azure-databricks.md) and Azure HDInsight as well as Azure Data Lake Analytics. Abbiamo sentito dagli sviluppatori che hanno una chiara preferenza per le soluzioni open source durante la compilazione di pipeline di analisi. Per aiutare gli sviluppatori U-SQL a comprendere Apache Spark e come trasformare gli script U-SQL in Apache Spark, abbiamo creato questa guida.

Include una serie di passaggi che è possibile eseguire e diverse alternative.

## <a name="steps-to-transform-u-sql-to-apache-spark"></a>Passaggi per trasformare U-SQL in Apache Spark

1. Trasformare le pipeline di orchestrazione dei processi.

   Se si usa Azure Data Factory per orchestrare gli script di Analisi di Azure Data Lake, è necessario modificarli per orchestrare i nuovi programmi Spark.If you use [Azure Data Factory](../data-factory/introduction.md) to orchestrate your Azure Data Lake Analytics scripts, you'll have to adjust them to orchestrate the new Spark programs.
2. Comprendere le differenze tra la gestione dei dati tra U-SQL e Spark

   Se si desidera spostare i dati da [Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md) ad Azure Data Lake Storage [Gen2,](../storage/blobs/data-lake-storage-introduction.md)sarà necessario copiare sia i dati dei file che i dati gestiti dal catalogo. Si noti che Azure Data Lake Analytics supporta solo Azure Data Lake Storage Gen1.Note that Azure Data Lake Analytics only supports Azure Data Lake Storage Gen1. Vedere [Informazioni sui formati di dati Spark](understand-spark-data-formats.md)
3. Trasformare gli script U-SQL in Spark

   Prima di trasformare gli script U-SQL, è necessario scegliere un servizio di analisi. Alcuni dei servizi di calcolo disponibili sono:
      - [DataFlow di Azure Data FactoryAzure Data Factory DataFlow](../data-factory/concepts-data-flow-overview.md) Il mapping dei flussi di dati è progettato visivamente le trasformazioni di dati che consentono agli ingegneri di dati di sviluppare una logica di trasformazione dei dati grafica senza scrivere codice. Anche se non sono adatti per eseguire codice utente complesso, possono facilmente rappresentare le trasformazioni del flusso di dati tradizionali di tipo SQL
      - [Hive di Azure HDInsightAzure HDInsight Hive](../hdinsight/hadoop/apache-hadoop-using-apache-hive-as-an-etl-tool.md) Apache Hive in HDInsight è adatto per le operazioni di estrazione, trasformazione e caricamento (ETL). Ciò significa che si sta per tradurre i tuoi script U-SQL in Apache Hive.
      - Motori Apache Spark come Azure HDInsight Spark o Azure Databricks Ciò significa che si intende convertire gli script U-SQL in Spark.Apache Spark Engines such as [Azure HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) [or Azure Databricks](../azure-databricks/what-is-azure-databricks.md) This means you are going to translate your U-SQL scripts to Spark. Per ulteriori informazioni, vedere [Informazioni sui formati di dati Spark](understand-spark-data-formats.md)

> [!CAUTION]
> Sia [Azure Databricks che](../azure-databricks/what-is-azure-databricks.md) Azure HDInsight Spark sono servizi cluster e non processi senza server come Azure Data Lake Analytics.Both Azure Databricks and Azure [HDInsight Spark](../hdinsight/spark/apache-spark-overview.md) are cluster services and not serverless jobs like Azure Data Lake Analytics. È necessario considerare come eseguire il provisioning dei cluster per ottenere il rapporto costo/prestazioni appropriato e come gestirne la durata per ridurre al minimo i costi. Questi servizi hanno caratteristiche di prestazioni diverse con il codice utente scritto in .NET, pertanto sarà necessario scrivere wrapper o riscrivere il codice in un linguaggio supportato. Per ulteriori informazioni, vedere [Understand Spark data formats](understand-spark-data-formats.md), Understand [Apache Spark code concepts for U-SQL developers](understand-spark-code-concepts.md), [.Net for Apache Spark](https://dotnet.microsoft.com/apps/data/spark)

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui formati di dati Spark per gli sviluppatori U-SQLUnderstand Spark data formats for U-SQL developers](understand-spark-data-formats.md)
- [Comprendere i concetti relativi al codice Spark per gli sviluppatori U-SQLUnderstand Spark code concepts for U-SQL developers](understand-spark-code-concepts.md)
- [Aggiornare le soluzioni di analisi dei Big Data da Azure Data Lake Storage Gen1 ad Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [.NET per Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Trasformare i dati usando l'attività Hive di Hadoop in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Trasformare dati tramite l'attività Spark in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Informazioni su Apache Spark in Azure HDInsight](../hdinsight/spark/apache-spark-overview.md)
