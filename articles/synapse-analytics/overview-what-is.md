---
title: Che cos'è Azure Synapse Analytics?
description: Panoramica di Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 65f6c6627093358f8cbc66055bb9b16561f7c610
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420955"
---
# <a name="what-is-azure-synapse-analytics-workspaces-preview"></a>Che cos'è Azure Synapse Analytics (anteprima per aree di lavoro)?

[!INCLUDE [preview](includes/note-preview.md)]

Oggi l'analisi aziendale richiede di operare su larga scala con qualsiasi tipo di dati, che siano non elaborati, perfezionati o altamente curati. In passato, la creazione di questi tipi di soluzioni di analisi richiedeva alle aziende di riunire tecnologie di Big Data e di data warehousing, ad esempio Spark e SQL. Successivamente, era necessario integrarle in pipeline avanzate che coinvolgevano dati di archivi relazionali e data lake.  

Soluzioni come queste sono difficili da creare, configurare, proteggere e gestire, ritardando l'estrazione rapida di informazioni dettagliate e intelligenti.

**Azure Synapse** è un servizio di analisi integrato che accelera il tempo necessario per estrarre informazioni dettagliate da tutti i dati su qualsiasi scala, tra data warehouse e sistemi di analisi di Big Data. Raggruppa il meglio delle tecnologie **SQL** usate nel data warehousing aziendale, le tecnologie **Spark** usate nell'analisi di Big Data e le **pipeline** per l'orchestrazione di attività e lo spostamento dei dati. 

Azure Synapse include l'esperienza utente **Studio** nativa del Web che offre una singola interfaccia e un singolo modello per la gestione, il monitoraggio, la scrittura di codice e la sicurezza.

Azure Synapse offre alle aziende il modo più semplice e rapido possibile per raccogliere informazioni dai dati di qualsiasi dimensione, usando gli strumenti di analisi più ampiamente diffusi. Si integra perfettamente con **Power BI**, consentendo agli ingegneri dei dati di creare soluzioni di analisi end-to-end che generano business intelligence. 

Inoltre, Azure Synapse facilita la creazione di modelli predittivi e l'analisi avanzata con Machine Learning tramite il supporto predefinito per **AzureML**.

## <a name="key-features--benefits"></a>Principali funzionalità e vantaggi

### <a name="industry-leading-sql"></a>SQL leader di settore

* **Synapse SQL** è un sistema di query distribuito che consente alle aziende di implementare scenari di data warehousing e virtualizzazione dei dati usando esperienze T-SQL standard ben note agli ingegneri dei dati. Espande inoltre le funzionalità di SQL per rispondere ai requisiti di streaming e Machine Learning.

* Synapse SQL offre modelli di risorse **serverless** e **con provisioning**, rendendo disponibili opzioni a consumo e su fatturazione in base a specifiche esigenze. Per prestazioni e costi prevedibili, effettuare il provisioning dei pool per riservare la potenza di elaborazione per i dati archiviati in tabelle SQL. Per i carichi di lavoro non pianificati o con picchi, usare l'endpoint SQL serverless sempre disponibile.
* Usare le funzionalità predefinite di **streaming** per trasferire dati da origini cloud in tabelle SQL
* Integrare l'intelligenza artificiale con SQL, usando modelli di **Machine Learning** per assegnare punteggi ai dati tramite la funzione T-SQL PREDICT

### <a name="industry-standard-apache-spark"></a>Apache Spark standard di settore

**Synapse Spark** si integra pienamente e facilmente con Apache Spark, il motore di Big Data open source più diffuso usato per la preparazione dei dati, l'ingegneria dei dati, i processi ETL e Machine Learning.

* Modelli ML con algoritmi SparkML e integrazione di AzureML per Apache Spark 2.4 con supporto predefinito per Linux Foundation Delta Lake.
* Modello di risorse semplificato che evita di preoccuparsi della gestione dei cluster.
* Avvio rapido di Spark e completa scalabilità automatica.
* Supporto predefinito per .NET per Spark, che consente di riutilizzare le competenze in C# e l'attuale codice .NET all'interno di un'applicazione Spark.

### <a name="interop-of-sql-and-spark-on-your-data-lake"></a>Interoperabilità tra SQL e Spark nel data lake

Azure Synapse rimuove le tradizionali barriere tecnologiche che ostacolano l'uso simultaneo di SQL e Spark. È possibile combinare facilmente le piattaforme in base alle esigenze e alle competenze.

* Un sistema di metadati condiviso compatibile con Hive consente di utilizzare in Spark o Hive le tabelle definite nei file del data lake.
* SQL e Spark possono esplorare e analizzare direttamente i file Parquet, CSV, TSV e JSON archiviati nel data lake.
* Procedure veloci e scalabili di caricamento e scaricamento dei dati trasferiti tra database SQL e Spark

### <a name="built-in-orchestration-via-pipelines"></a>Orchestrazione predefinita tramite pipeline

Azure Synapse include per impostazione predefinita lo stesso motore di integrazione dei dati e le stesse esperienze di Azure Data Factory, consentendo di creare pipeline di dati avanzate senza usare un motore di orchestrazione distinto.

* Spostamento dei dati tra Synapse e più di 85 origini dati locali
* Orchestrazione di notebook, pipeline, processi Spark, script SQL, stored procedure
* Processi ETL senza codice con attività del flusso di dati

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

* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare Synapse Studio](quickstart-synapse-studio.md)
* [Creare un pool SQL](quickstart-create-sql-pool.md)
* [Usare SQL su richiesta](quickstart-sql-on-demand.md)
* [Creare un pool di Apache Spark](quickstart-create-apache-spark-pool.md)
