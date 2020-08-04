---
title: Domande frequenti - Azure Synapse Analytics (anteprima delle aree di lavoro)
description: Domande frequenti su Azure Synapse Analytics (anteprima delle aree di lavoro)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: ba6f79fffe5287be7574d422f026489d4da2795e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87287504"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Domande frequenti su Azure Synapse Analytics (anteprima delle aree di lavoro)

Questa guida include le domande frequenti su Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Generale

### <a name="q-what-is-azure-synapse-analytics"></a>D: Che cos'è Azure Synapse Analytics?

A: Azure Synapse è una piattaforma di dati integrata per business intelligence, intelligenza artificiale e intelligenza continua. Connette vari runtime di analisi, ad esempio SQL e Spark, tramite una singola piattaforma che fornisce una soluzione unificata per:

- Proteggere le risorse di analisi, tra cui la rete, gestendo l'accesso Single Sign-On a pool, dati e artefatti di sviluppo.
- Monitorare e ottimizzare, reagire e sottoporre a debug rapidamente gli eventi che si verificano nelle attività dell'area di lavoro a qualsiasi livello.
- Gestire i metadati tra motori. Creare una tabella Spark che sarà automaticamente disponibile nei database di Azure Synapse.
- Interagire con i dati tramite un'esperienza utente unificata. Synapse Studio raggruppa nella stessa piattaforma sviluppatori di Big Data, ingegneri dei dati, amministratori di database, analisti di dati e data scientist.

Per altre informazioni, vedere [Informazioni su Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>D: Quali sono i componenti principali di Azure Synapse Analytics?

A: Azure Synapse Analytics include le funzionalità seguenti:

- Funzionalità di analisi offerte tramite pool SQL o SQL su richiesta (anteprima) (serverless).
- Pool di Apache Spark (anteprima) con supporto completo per Scala, Python, SparkSQL e C#
- Flusso di dati che offre un'esperienza di trasformazione di Big Data senza codice
- Integrazione e orchestrazione dei dati per integrare i dati e operazionalizzare tutto lo sviluppo di codice
- Studio per accedere a tutte queste funzionalità tramite una singola interfaccia utente Web

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>D: Qual è la relazione tra Azure Synapse Analytics e Azure SQL Data Warehouse?

A: Azure Synapse Analytics rappresenta l'evoluzione di Azure SQL Data Warehouse in una piattaforma di analisi, che include il pool SQL come soluzione di data warehouse. Questa piattaforma combina le attività di esplorazione, inserimento, trasformazione e preparazione dei dati, oltre a un livello di analisi di supporto.

## <a name="use-cases"></a>Casi d'uso

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>D: Come si rinomina un artefatto pubblicato (set di dati, notebook, script SQL e così via) in Azure Synapse?

A: Per rinominare un file di artefatto pubblicato, clonare prima il file e rinominare il nuovo file assegnandogli il nome che si preferisce. È necessario aggiornare manualmente tutti i riferimenti dell'artefatto al nuovo nome file ed eliminare quello precedente.

### <a name="q-what-is-a-good-use-case-for-synapse-sql-pool"></a>D: Qual è un caso d'uso valido per il pool SQL Synapse?

A: Il pool SQL rappresenta la base delle esigenze di data warehouse. Si tratta della soluzione di data warehouse leader nel rapporto [prezzo/prestazioni](https://azure.microsoft.com/services/sql-data-warehouse/compare/). Il pool SQL è la soluzione cloud di data warehouse nel cloud leader nel settore perché consente di:

- Gestire un'ampia varietà di carichi di lavoro misti senza influire sulle prestazioni grazie alla concorrenza elevata e all'isolamento dei carichi di lavoro
- Proteggere facilmente i dati tramite funzionalità avanzate che vanno dalla sicurezza di rete all'accesso con granularità fine
- Trarre vantaggio da un ampio ecosistema

### <a name="q-what-is-a-good-use-case-for-spark-in-synapse"></a>D: Qual è un caso d'uso valido per Spark in Synapse?

A: Il nostro primo obiettivo è fornire un'esperienza di ingegneria dei dati straordinaria per trasformare i data del lake in batch o flussi. Grazie alla stretta integrazione con l'orchestrazione dei dati, l'operazionalizzazione delle attività di sviluppo diventa semplice e diretta.

Un altro caso d'uso per Spark è quello che consente ai data scientist di:

- Estrarre una funzionalità
- Esplorare i dati
- Eseguire il training di un modello

### <a name="q-what-is-a-good-use-case-for-sql-on-demand-in-synapse"></a>D: Qual è un caso d'uso valido per SQL su richiesta in Synapse?

A: SQL su richiesta è un servizio di query sui dati del data lake. Consente di democratizzare l'accesso a tutti i dati fornendo una sintassi T-SQL familiare per l'esecuzione di query sul posto, senza la necessità di copiare i dati o caricarli in un archivio speciale.

Gli esempi di casi d'uso includono i seguenti:

- Individuazione ed esplorazione di base: analisti di dati, data scientist emergenti e ingegneri dei dati ottengono un percorso facile per l'acquisizione delle prime informazioni dettagliate dai dati residenti nel data lake con query T-SQL di tipo schema su lettura
- Data warehouse logico: gli analisti di dati possono sfruttare l'espressività completa del linguaggio T-SQL per eseguire query e analizzare direttamente i dati residenti in Archiviazione di Azure e usare i familiari strumenti di business intelligence (ad esempio Azure Analysis Services, Power BI Premium e così via) per aggiornare i dashboard riseguendo le query di Starlight Query
- Processi ETL con "singola query": consentono agli ingegneri dei dati di trasformare i dati basati su Archiviazione di Azure da un formato a un altro, di filtrarli, di aggregarli e così via in modalità di elaborazione massicciamente parallela, di rendere persistenti i risultati delle query in Archiviazione di Azure e di renderli immediatamente disponibili per un'ulteriore elaborazione in Starlight Query o altri servizi di interesse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-synapse"></a>D: Qual è un caso d'uso valido per il flusso di dati in Synapse?

A: Il flusso di dati consente agli ingegneri dei dati di sviluppare una logica di trasformazione grafica dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno delle operazioni di integrazione e orchestrazione dei dati. Le attività del flusso di dati possono essere operazionalizzate tramite funzionalità esistenti di pianificazione, controllo, flusso e monitoraggio.

## <a name="security-and-access"></a>Sicurezza e accesso

A: L'esperienza Single Sign-On end-to-end è un processo di autenticazione importante in Synapse Analytics. La gestione e il pass-through dell'identità tramite la piena integrazione di AAD sono requisiti imprescindibili.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>D: Come si accede ai file e alle cartelle in ADLS Gen2?

A: L'accesso a file e cartelle è attualmente gestito tramite ADLS Gen2. Per altre informazioni, vedere [Controllo di accesso all'archiviazione di Data Lake](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>D: È possibile usare strumenti di business intelligence di terze parti per accedere ad Azure Synapse Analytics?

A: Sì, è possibile usare applicazioni aziendali di terze parti, come Tableau e Power BI, per connettersi a un pool SQL e a SQL su richiesta. Spark supporta IntelliJ.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'area di lavoro](quickstart-create-workspace.md)
- [Usare Synapse Studio](quickstart-synapse-studio.md)
- [Creare un pool SQL](quickstart-create-sql-pool-portal.md)
- [Usare SQL su richiesta](quickstart-sql-on-demand.md)
- [Creare un pool di Apache Spark](quickstart-create-apache-spark-pool-portal.md) 
