---
title: Scheda di riferimento rapido - Azure Synapse Analytics (anteprima delle aree di lavoro)
description: Guida di riferimento per l'uso di Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a592b1b160edef1ed1f41478187d989d087e9617
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844983"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Scheda di riferimento rapido di Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

La scheda di riferimento rapido di Azure Synapse Analytics illustra i concetti di base del servizio e i comandi importanti. Questo articolo è utile per i nuovi utenti e per coloro che vogliono informazioni aggiornate sugli argomenti essenziali relativi ad Azure Synapse.

## <a name="basics"></a>Operazioni di base

Un'**area di lavoro di Synapse** è un limite di collaborazione a protezione diretta per l'analisi aziendale basata sul cloud in Azure. Un'area di lavoro viene distribuita in un'area specifica e con un account e un file system di ADLS Gen2 associati (per l'archiviazione di dati temporanei). Un'area di lavoro in un gruppo di risorse.

Un'area di lavoro consente di eseguire analisi con SQL e Apache Spark. Le risorse disponibili per SQL e Spark Analytics sono organizzate in **pool** SQL e Spark. 

Un'area di lavoro può contenere un numero indefinito di **servizi collegati**, essenzialmente stringhe di connessione che definiscono le informazioni necessarie per la connessione dell'area di lavoro a risorse esterne.

## <a name="synapse-sql-terminology"></a>Terminologia di Synapse SQL

**Synapse SQL** offre la possibilità di eseguire analisi basate su T-SQL nell'area di lavoro di Synapse. Synapse SQL ha due modelli di utilizzo: dedicato e serverless.  Per il modello dedicato, usare **pool SQL dedicati**. Un'area di lavoro può contenere un numero qualsiasi di questi pool. Per il modello serverless, usare i **pool SQL serverless**. Ogni area di lavoro dispone di uno di questi pool.

* **Richiesta SQL**: operazione, come una query, eseguita tramite un pool SQL dedicato o un pool SQL serverless.
* **Script SQL**: set di comandi SQL salvati in un file. Uno script SQL può contenere una o più istruzioni SQL. Può essere usato per eseguire richieste SQL tramite un pool SQL dedicato o un pool SQL serverless.

## <a name="apache-spark-for-synapse-terminology"></a>Terminologia di Apache Spark per Synapse

Per usare Spark Analytics, creare e usare **pool di Apache Spark serverless** nell'area di lavoro di Synapse.


* **Apache Spark per Synapse**: runtime Spark usato in un pool di Spark serverless. La versione corrente supportata è Spark 2.4 con supporto di Python 3.6.1, Scala 2.11.12, .NET per Apache Spark 0.5 e Delta Lake 0.3.  
* **Pool di Apache Spark**: in un'area di lavoro possono essere distribuite da 0 a N risorse con provisioning Spark con i database corrispondenti. Un pool di Spark può essere sospeso automaticamente, ripreso e ridimensionato.  
* **Applicazione Spark**: consiste in un processo driver e un set di processi executor. Un'applicazione Spark viene eseguita in un pool di Spark serverless.            
* **Sessione Spark**: punto di ingresso unificato di un'applicazione Spark. Offre la possibilità di interagire con varie funzionalità di Spark e con un numero minore di costrutti. Per eseguire un notebook, è necessario creare una sessione. Una sessione può essere configurata per l'esecuzione in un numero specifico di executor di dimensioni specifiche. La configurazione predefinita per una sessione di notebook consiste nell'esecuzione su due executor di medie dimensioni.
* **Notebook**: interfaccia interattiva e reattiva di data science e ingegneria che supporta Scala, PySpark, C# e SparkSQL.
* **Definizione di processo Spark**: interfaccia per l'invio di processi Spark con un file JAR di assembly contenente il codice e le relative dipendenze.

## <a name="pipelines-terminology"></a>Terminologia di Pipelines
* **Integrazione dei dati**: offre la possibilità di inserire dati tra varie origini e di orchestrare le attività in esecuzione all'interno o all'esterno di un'area di lavoro.
* **Flusso di dati**: fornisce un'esperienza completamente visiva senza la necessità di codice per eseguire trasformazioni di Big Data. Tutte le attività di ottimizzazione ed esecuzione vengono gestiti in modalità serverless.
* **Pipeline**: raggruppamento logico di attività che insieme svolgono una funzione.
* **Attività**: definisce le azioni da eseguire sui dati, ad esempio la copia, l'esecuzione di un notebook o uno script SQL.
* **Trigger**: esegue una pipeline. Può essere eseguito manualmente o automaticamente (pianificazione, finestra a cascata o basata su eventi).
* **Set di dati di integrazione**: vista denominata di dati che semplicemente punta o fa riferimento ai dati da usare come input e output di un'attività. Appartiene a un servizio collegato.

## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Azure Synapse Analytics](get-started.md)
* [Creare un'area di lavoro](quickstart-create-workspace.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)

