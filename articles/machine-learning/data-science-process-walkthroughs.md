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
ms.date: 07/25/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: c0f0ff599909234b2b43f2b8512d77f9053b8a09
ms.contentlocale: it-it
ms.lasthandoff: 07/26/2017

---
# <a name="team-data-science-process-walkthroughs"></a>Procedure dettagliate del Processo di analisi scientifica dei dati per i team
Le **procedure dettagliate end-to-end** esposte illustrano i passaggi del processo di data science per i team in **scenari specifici**. Viene spiegato come combinare strumenti cloud, strumenti locali e servizi in un flusso di lavoro o in una pipeline per creare un'**applicazione intelligente**. Le procedure dettagliate sono raggruppate in base alla **piattaforma** in uso: 

- Spark con PySpark e Scala
- HDInsight (Hadoop)
- Azure Data Lake 
- SQL Server
- SQL Data Warehouse 


## <a name="hdinsight-spark-using-pyspark-and-scala"></a>HDInsight Spark con PySpark e Scala

- La procedura dettagliata relativa all'[uso di Spark in Azure HDInsight](machine-learning-data-science-spark-overview.md) usa il processo di analisi scientifica dei dati per i team in uno scenario con un [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati e progettarne le funzionalità in base al set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York.

- La procedura dettagliata relativa all'[uso di Scala con Spark in Azure](machine-learning-data-science-process-scala-walkthrough.md) illustra come usare Scala per attività di Machine Learning con supervisione con la libreria di Machine Learning (MLlib) per Spark e pacchetti SparkML in un cluster Azure HDInsight Spark. Vengono illustrate le attività che costituiscono il [processo di analisi scientifica dei dati](http://aka.ms/datascienceprocess), ovvero l'inserimento e l'esplorazione dei dati, la visualizzazione, la progettazione, la modellazione e l'utilizzo dei modelli. I modelli creati includono la regressione logistica e quella lineare, foreste casuali e alberi con boosting a gradienti.


## <a name="hdinsight-hadoop"></a>Hadoop HDInsight 

- La procedura dettagliata relativa all'[uso di cluster Hadoop di HDInsight](machine-learning-data-science-process-hive-walkthrough.md) usa un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati e progettarne le funzionalità in base a un set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York.

- La procedura dettagliata relativa all'[uso di cluster Hadoop di Azure HDInsight su un set di dati da 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) illustra uno scenario che usa un [cluster Hadoop di Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) per archiviare, esplorare e sottocampionare i dati e progettarne le funzionalità in base a un set di dati [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibile pubblicamente.


## <a name="azure-data-lake"></a>Azure Data Lake

- La procedura dettagliata relativa all'[uso di Azure Data Lake per l'analisi scientifica dei dati](machine-learning-data-science-process-data-lake-walkthrough.md) illustra come usare Azure Data Lake per eseguire attività di esplorazione dei dati e di classificazione binaria su un campione del set di dati relativo ai taxi nella città di New York, per prevedere se un cliente lascerà o meno una mancia. 


## <a name="sql-server-and-sql-data-warehouse"></a>SQL Server e SQL Data Warehouse 

- La procedura dettagliata relativa all'[uso di SQL Data Warehouse](machine-learning-data-science-process-sqldw-walkthrough.md) illustra come compilare e distribuire modelli di classificazione e regressione di Machine Learning usando SQL Data Warehouse (SQL DW) per un set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York.

- La procedura dettagliata relativa all'[uso di SQL Server](machine-learning-data-science-process-sql-walkthrough.md) illustra come compilare e distribuire modelli di classificazione e regressione di Machine Learning usando SQL Server per un set di dati disponibile pubblicamente relativo a corse e tariffe dei taxi nella città di New York.

- La procedura dettagliata relativa all'[uso di servizi R per SQL Server](https://msdn.microsoft.com/library/mt612857.aspx) offre ai data scientist una combinazione di codice R, dati SQL Server e funzioni SQL personalizzate per compilare e distribuire un modello R in SQL Server.

- La procedura dettagliata relativa all'[uso di T-SQL con i servizi R per SQL Server](https://msdn.microsoft.com/library/mt683480.aspx) offre ai programmatori SQL indicazioni per la compilazione di una soluzione di analisi avanzata con Transact-SQL usando i servizi R per SQL Server per rendere operativa una soluzione R.

- La procedura dettagliata relativa all'[uso di T-SQL con i servizi Python per SQL Server](https://docs.microsoft.com/en-us/sql/advanced-analytics/tutorials/sqldev-in-database-python-for-sql-developers) offre ai programmatori SQL indicazioni per la compilazione di una soluzione di apprendimento automatico in SQL Server. Illustra come incorporare Python in un'applicazione aggiungendo il codice Python alle stored procedure.

## <a name="whats-next"></a>Passaggi successivi
Per una panoramica degli argomenti che forniscono informazioni dettagliate sulle attività che costituiscono il processo di analisi scientifica dei dati in Azure, vedere [Processo di analisi scientifica dei dati per i team](http://aka.ms/datascienceprocess). 


