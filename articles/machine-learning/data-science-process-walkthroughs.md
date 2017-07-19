---
title: Procedure dettagliate del Processo di analisi scientifica dei dati per i team | Documentazione Microsoft
description: Le procedure dettagliate illustrano come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: aa63d5a5-25ee-4c4b-9a4c-7553b98d7f6e
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: bradsev
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 6df3fc533b0c375cd85743a971c778071629bb7e
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="team-data-science-process-walkthroughs"></a>Procedure dettagliate del Processo di analisi scientifica dei dati per i team
Le **procedure dettagliate end-to-end** esposte illustrano i passaggi del processo di data science per i team in **scenari specifici**. Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'**applicazione intelligente**. Le procedure dettagliate sono raggruppate per **piattaforma** e usano: Spark, HDInsight (Hadoop), Azure Data Lake e SQL Server.


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark con PySpark e Scala

### <a name="data-science-using-python-with-spark-on-azure"></a>Analisi scientifica dei dati mediante Python con Spark in Azure
La procedura dettagliata di [data science con Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md) usa il processo di data science per i team in uno scenario con un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati e progettarne le funzionalità in base al set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York. 

### <a name="data-science-using-scala-with-spark-on-azure"></a>Analisi scientifica dei dati mediante Scala con Spark in Azure
La procedura dettagliata [Data science tramite Scala e Spark in Azure](machine-learning-data-science-process-scala-walkthrough.md) illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) di Spark e pacchetti SparkML in un cluster Azure HDInsight Spark. Vengono illustrate le attività che costituiscono il [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione, la modellazione e l'utilizzo dei modelli. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti.


## <a name="hdinsight-hadoop"></a>Hadoop HDInsight 

### <a name="use-hdinsight-hadoop-clusters"></a>Usare cluster Hadoop di HDInsight
La procedura dettagliata [Processo di analisi scientifica dei dati per i team in azione con cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md) usa un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati e progettarne le funzionalità in base a un set di dati disponibile pubblicamente relativo a corse e tariffe in taxi nella città di New York

### <a name="use-azure-hdinsight-hadoop-clusters-on-a-1-tb-dataset"></a>Usare cluster Hadoop di Azure HDInsight in un set di dati da 1 TB
La procedura dettagliata [Processo di data science per i team in azione: uso dei cluster Hadoop di Azure HDInsight in un set di dati da 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) illustra uno scenario che usa un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e sottocampionare i dati e progettarne le funzionalità in base a un set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibile pubblicamente.


## <a name="azure-data-lake"></a>Azure Data Lake

### <a name="use-azure-data-lake-store-and-analytics"></a>Usare Azure Data Lake Store e Analytics
[Analisi scientifica dei dati scalabile in Azure Data Lake: procedura dettagliata end-to-end](machine-learning-data-science-process-data-lake-walkthrough.md) illustra come usare Azure Data Lake per eseguire attività di esplorazione dei dati e di classificazione binaria su un campione del set di dati relativo ai taxi nella città di New York, per prevedere se un cliente lascerà una mancia. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server e SQL Data Warehouse 

### <a name="use-sql-data-warehouse"></a>Usare SQL Data Warehouse
La procedura dettagliata [Processo di analisi scientifica dei dati per i team in azione con SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) illustra come compilare e distribuire modelli di classificazione e regressione per l'apprendimento automatico usando SQL Data Warehouse (SQL DW) per un set di dati disponibile pubblicamente relativo a corse e tariffe in taxi nella città di New York.

### <a name="use-sql-server"></a>Usare SQL Server
La procedura dettagliata [Processo di analisi scientifica dei dati per i team in azione con SQL Server](machine-learning-data-science-process-sql-walkthrough.md) illustra come compilare e distribuire modelli di classificazione e regressione per l'apprendimento automatico usando SQL Server e un set di dati disponibile pubblicamente relativo a corse e tariffe in taxi nella città di New York.

### <a name="use-r-with-sql-server-r-services"></a>Usare R con i servizi R di SQL Server
[Data Science End-to-End Walkthrough](https://msdn.microsoft.com/library/mt612857.aspx) (Procedura dettagliata di analisi scientifica dei dati end-to-end) fornisce ai data scientist una combinazione di codice R, dati SQL Server e funzioni SQL personalizzate per compilare e distribuire un modello R in SQL Server.

### <a name="use-t-sql-with-sql-server-r-services"></a>Usare T-SQL con i servizi R di SQL Server
La procedura dettagliata [In-Database Advanced Analytics for SQL Developers](https://msdn.microsoft.com/library/mt683480.aspx) (Analisi avanzata nel database per gli sviluppatori SQL) fornisce ai programmatori SQL indicazioni per la compilazione di una soluzione di analisi avanzata con Transact-SQL usando i servizi R di SQL Server per rendere operativa una soluzione R.


### <a name="use-t-sql-with-sql-server-python-services"></a>Usare T-SQL con servizi Python SQL Server
La procedura dettagliata [In-Database Python Analytics for SQL Developers](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) (Analisi Python nel database per sviluppatori SQL) fornisce ai programmatori SQL indicazioni per la creazione di una soluzione di Machine Learning in SQL Server. Illustra come incorporare Python in un'applicazione aggiungendo il codice Python alle stored procedure.

## <a name="whats-next"></a>Passaggi successivi
Per una panoramica degli argomenti che forniscono informazioni dettagliate sulle attività che costituiscono il processo di analisi scientifica dei dati in Azure, vedere [Processo di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess). 


