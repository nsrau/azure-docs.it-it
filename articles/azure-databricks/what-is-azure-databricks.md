---
title: Informazioni su Azure Databricks | Microsoft Docs
description: "Azure Databricks è una piattaforma di analisi dei dati basata su Apache Spark."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: nitinme
ms.openlocfilehash: 4361c29f81aae3e22e00f4cd854306c27adc2dbc
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-databricks"></a>Informazioni su Azure Databricks

Azure Databricks è una piattaforma di analisi basata su Apache Spark ottimizzata per la piattaforma dei servizi cloud di Microsoft Azure. Progettato con i fondatori di Apache Spark, Databricks è integrato con Azure per offrire l'installazione con un clic, i flussi di lavoro semplificati e un'area di lavoro interattiva che consente la collaborazione tra data scientist, ingegneri dei dati e business analyst.

![Informazioni su Azure Databricks](./media/what-is-azure-databricks/azure-databricks-overview.png "Informazioni su Azure Databricks")

## <a name="apache-spark-based-analytics-platform"></a>Piattaforma di analisi basata su Apache Spark

Azure Databricks comprende tutte le funzionalità e le tecnologie del cluster Apache Spark open source. Spark in Azure Databricks include i componenti seguenti:

![Apache Spark in Azure Databricks](./media/what-is-azure-databricks/apache-spark-ecosystem-databricks.png "Apache Spark in Azure Databricks")

* **Spark SQL e DataFrame**: Spark SQL è il modulo Spark per lavorare con dati strutturati. Un DataFrame è una raccolta distribuita di dati organizzati in colonne denominate. Dal punto di vista concettuale equivale a una tabella in un database relazionale o a un frame di dati in R/Python.

* **Streaming**: elaborazione e analisi dei dati in tempo reale per applicazioni analitiche e interattive. Si integra con HDFS, Flume e Kafka.

* **MLib**: libreria di Machine Learning costituita da comuni utilità e algoritmi di apprendimento comuni, tra cui classificazione, regressione, clustering, filtro collaborativo, riduzione della dimensionalità, nonché primitive di ottimizzazione sottostanti.

* **GraphX**: grafi e calcolo dei grafi per casi d'uso di più ampio respiro, dall'analisi cognitiva all'esplorazione dei dati.

* **API Core Spark**: include il supporto per R, SQL, Python, Scala e Java.

## <a name="apache-spark-in-azure-databricks"></a>Apache Spark in Azure Databricks

Basato sulle funzionalità di Spark, Azure Databricks offre una piattaforma cloud a gestione zero che include:

- Cluster Spark completamente gestiti
- Un'area di lavoro interattiva per l'esplorazione e la visualizzazione
- Una piattaforma per eseguire le applicazioni preferite basate su Spark

### <a name="fully-managed-apache-spark-clusters-in-the-cloud"></a>Cluster Apache Spark completamente gestiti nel cloud

Azure Databricks include un ambiente di produzione nel cloud sicuro e affidabile, gestito e supportato da esperti Spark. È possibile:

* Creare cluster in pochi secondi.
* Aumentare o ridurre in modo automatico e dinamico il numero di cluster, inclusi quelli senza server, nonché condividerli tra i team. 
* Usare i cluster a livello di programmazione con le API REST. 
* Usare funzionalità sicure di integrazione dei dati basate su Spark che consentono di unificare i dati senza centralizzazione. 
* Ottenere accesso immediato alle funzionalità Apache Spark più recenti con ogni versione.

### <a name="databricks-runtime"></a>Databricks Runtime
Databricks Runtime è basato su Apache Spark e viene creato in modalità nativa per il cloud di Azure. 

Grazie all'opzione **Serverless** (Senza server) di Azure Databricks, l'infrastruttura risulta notevolmente semplificata e non è necessario ricorrere a personale esperto specializzato per installare e configurare l'infrastruttura dati. L'opzione Serverless (Senza server) consente ai data scientist di eseguire rapidamente l'iterazione come team.

Per i data engineer, che si preoccupano delle prestazioni dei processi di produzione, Azure Databricks offre un motore Spark, caratterizzato da una maggiore velocità e da prestazioni elevate nelle varie ottimizzazioni a livello di I/O e di elaborazione (Databricks I/O).

### <a name="workspace-for-collaboration"></a>Area di lavoro per la collaborazione

Grazie a un ambiente integrato che favorisce la collaborazione, Azure Databricks semplifica il processo di esplorazione dei dati, creazione di prototipi ed esecuzione di applicazioni basate sui dati in Spark.

* Stabilire come usare i dati con semplici funzionalità di esplorazione dei dati.
* Documentare lo stato di avanzamento nei blocchi appunti in R, Python, Scala o SQL.
* Visualizzare i dati in pochi clic e usare strumenti familiari come Matplotlib, ggoplot o d3.
* Usare dashboard interattivi per creare report dinamici.
* Usare Spark e interagire contemporaneamente con i dati.

## <a name="enterprise-security"></a>Sicurezza aziendale

Azure Databricks offre funzionalità di sicurezza di Azure di livello aziendale, inclusa l'integrazione di Azure Active Directory, i controlli basati sui ruoli e contratti di servizio di livello aziendale per proteggere i dati e il business.

* L'integrazione con Azure Active Directory consente di eseguire soluzioni complete basate su Azure tramite Azure Databricks.
* Con l'accesso basato sui ruoli di Azure Databricks è possibile definire autorizzazioni utente specifiche per blocchi appunti, cluster, processi e dati.
* Contratti di servizio di livello aziendale. 

## <a name="integration-with-azure-services"></a>Integrazione con servizi di Azure

Azure Databricks si integra perfettamente con archivi e database di Azure: SQL Data Warehouse, Cosmos DB, Data Lake Store e archiviazione BLOB. 

## <a name="integration-with-power-bi"></a>Integrazione con Power BI
Grazie all'integrazione avanzata con Power BI, Azure Databricks consente di individuare e condividere le informazioni dettagliate più importanti in modo semplice e rapido. È anche possibile usare altri strumenti di business intelligence, ad esempio Tableau Software tramite endpoint cluster JDBC/ODBC.

## <a name="next-steps"></a>Passaggi successivi

* [Quickstart: Run a Spark job on Azure Databricks](quickstart-create-databricks-workspace-portal.md) (Guida introduttiva: Eseguire un processo Spark in Azure Databricks)
* [Work with Spark clusters](https://docs.azuredatabricks.net/user-guide/clusters/index.html) (Usare i cluster Spark)
* [Usare i blocchi appunti](https://docs.azuredatabricks.net/user-guide/notebooks/index.html)
* [Create Spark jobs](https://docs.azuredatabricks.net/user-guide/jobs.html) (Creare processi Spark)

 









