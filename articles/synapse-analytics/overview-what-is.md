---
title: Che cos'è Azure Synapse Analytics?
description: Panoramica di Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 16e13a18f93da9063a7eb08e3a2df27db9e3090f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321698"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Che cos'è Azure Synapse Analytics (anteprima per aree di lavoro)?

[!INCLUDE [preview](includes/note-preview.md)]

L'analisi aziendale deve operare su larga scala con qualsiasi tipo di dati, che siano dati non elaborati, perfezionati o altamente curati. Per ottenere questo risultato, le aziende devono unire tecnologie di Big Data e di data warehousing in pipeline di dati complesse in grado di elaborare i dati di archivi relazionali e data lake. Questi tipi di soluzioni sono difficili da creare, gestire e proteggere. La loro complessità comporta un ritardo nella disponibilità dei dati analitici di cui le aziende hanno bisogno.

**Azure Synapse** è un servizio di analisi integrato che riduce il tempo necessario per estrarre informazioni dettagliate da data warehouse e sistemi di Big Data. Raggruppa il meglio delle tecnologie **SQL** usate nel data warehousing aziendale, le tecnologie **Spark** usate per i Big Data e le **pipeline** per l'integrazione dei dati e le operazioni ETL/ELT. **Synapse Studio** offre un'esperienza unificata per la gestione, il monitoraggio, la codifica e la sicurezza. È strettamente integrato con altri servizi di Azure, come **Power BI** , **CosmosDB** e **AzureML**.

## <a name="key-features--benefits"></a>Principali funzionalità e vantaggi

### <a name="industry-leading-sql"></a>SQL leader di settore

* **Synapse SQL** è un sistema di query distribuito che consente alle aziende di implementare scenari di data warehousing e virtualizzazione dei dati usando esperienze T-SQL standard e ben note. Espande inoltre le funzionalità di SQL per rispondere ai requisiti di streaming e Machine Learning.

* Synapse SQL offre modelli di risorse **serverless** e **dedicati** , rendendo disponibili opzioni a consumo e su fatturazione in base a specifiche esigenze. Per prestazioni e costi prevedibili, creare pool SQL dedicati per riservare la potenza di elaborazione per i dati archiviati in tabelle SQL. Per i carichi di lavoro non pianificati o con picchi, usare l'endpoint SQL serverless sempre disponibile.
* Usare le funzionalità predefinite di **streaming** per trasferire dati da origini cloud in tabelle SQL
* Integrare l'intelligenza artificiale con SQL usando modelli di **Machine Learning** per assegnare punteggi ai dati tramite la [funzione T-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

### <a name="industry-standard-apache-spark"></a>Apache Spark standard di settore

**Apache Spark per Azure Synapse** si integra pienamente e facilmente con Apache Spark, il motore di Big Data open source più diffuso usato per la preparazione dei dati, l'ingegneria dei dati, i processi ETL e Machine Learning.

* Modelli ML con algoritmi SparkML e integrazione di AzureML per Apache Spark 2.4 con supporto predefinito per Linux Foundation Delta Lake.
* Modello di risorse semplificato che evita di preoccuparsi della gestione dei cluster.
* Avvio rapido di Spark e completa scalabilità automatica.
* Supporto predefinito per .NET per Spark, che consente di riutilizzare le competenze in C# e l'attuale codice .NET all'interno di un'applicazione Spark.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interoperabilità tra SQL e Apache Spark nel data lake

Azure Synapse rimuove le tradizionali barriere tecnologiche che ostacolano l'uso simultaneo di SQL e Spark. È possibile combinare facilmente le piattaforme in base alle esigenze e alle competenze.

* Un sistema di metadati condiviso compatibile con Hive consente di utilizzare in Spark o Hive le tabelle definite nei file del data lake.
* SQL e Spark possono esplorare e analizzare direttamente i file Parquet, CSV, TSV e JSON archiviati nel data lake.
* Procedure veloci e scalabili di caricamento e scaricamento dei dati trasferiti tra database SQL e Spark

### <a name="built-in-data-integration-via-pipelines"></a>Integrazione dei dati incorporata tramite pipeline

Azure Synapse include per impostazione predefinita lo stesso motore di integrazione dei dati e le stesse esperienze di Azure Data Factory, consentendo di creare pipeline di estrazione, trasformazione e caricamento su larga scala senza uscire da Synapse Analytics.

* Inserimento di dati da più di 90 origini dati
* Processi ETL senza codice con attività del flusso di dati
* Orchestrazione di notebook, processi Spark, stored procedure, script SQL e altro ancora

### <a name="unified-management-monitoring-and-security"></a>Soluzione unificata di gestione, monitoraggio e sicurezza

Azure Synapse offre alle aziende una singola soluzione per gestire le risorse di analisi, monitorare l'utilizzo e l'attività e applicare la sicurezza.

* Assegnazione agli utenti di un ruolo per semplificare l'accesso alle risorse di analisi
* Controllo degli accessi con granularità fine per dati e codice
* Un singolo dashboard per monitorare risorse, utilizzo e utenti tra SQL e Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** è l'esperienza nativa del Web che unisce tutto il necessario per consentire agli ingegneri dei dati di eseguire da un'unica posizione ogni attività richiesta per lo sviluppo di una soluzione completa.

* Sviluppo di una soluzione di analisi end-to-end in un'unica posizione, con inserimento, esplorazione, preparazione, orchestrazione, visualizzazione
* Produttività leader di settore per gli ingegneri dei dati che scrivono codice SQL o Spark, tra cui creazione, debug e ottimizzazione delle prestazioni
* Integrazione con processi CI/CD aziendali

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
