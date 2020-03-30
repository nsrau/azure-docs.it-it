---
title: Monitorare le operazioni di inserimento in Esplora dati di Azure usando i log di diagnostica
description: Informazioni su come configurare i log di diagnostica per Azure Data Explorer per monitorare le operazioni di inserimento.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 3e10979e26cacdc0c2071a6030c945adad21a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277434"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorare le operazioni di inserimento di Esplora dati di Azure usando i log di diagnostica (anteprima)Monitor Azure Data Explorer ingestion operations using diagnostic logs (Preview)

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi è possibile inserire (caricare) i dati in una tabella in un database in modo da poter eseguire query su di esso. I log di diagnostica di Monitoraggio di Azure forniscono dati sul funzionamento delle risorse di [Azure.Azure Monitor diagnostic logs](/azure/azure-monitor/platform/diagnostic-logs-overview) provide data about the operation of Azure resources. Azure Data Explorer usa i log di diagnostica per informazioni dettagliate sui successi e gli errori di inserimento. È possibile esportare i log delle operazioni in Archiviazione di Azure, Hub eventi o Log Analytics per monitorare lo stato di inserimento. I log da Archiviazione di Azure e Hub eventi di Azure possono essere indirizzati a una tabella nel cluster di Azure Data Explorer per un'ulteriore analisi.

## <a name="prerequisites"></a>Prerequisiti

* Se non si dispone di una sottoscrizione di Azure, creare un [account Azure gratuito.](https://azure.microsoft.com/free/)
* Creare un [cluster e](create-cluster-database-portal.md)un database .

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/)di Azure .

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurare i log di diagnostica per un cluster di Azure Data ExplorerSet up diagnostic logs for an Azure Data Explorer cluster

I log di diagnostica possono essere utilizzati per configurare la raccolta dei seguenti dati di log:
* Operazioni di inserimento riuscite: questi log conto di informazioni sulle operazioni di inserimento completate correttamente.
* Operazioni di inserimento non riuscite: questi log conto di informazioni dettagliate sulle operazioni di inserimento non riuscite, inclusi i dettagli dell'errore. 

I dati vengono quindi archiviati in un account di archiviazione, trasmessi in un hub eventi o inviati a Log Analytics, in base alle specifiche.

### <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica

I log di diagnostica sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica, eseguire la procedura seguente:To enable diagnostic logs, do the following steps:

1. Nel [portale](https://portal.azure.com)di Azure selezionare la risorsa cluster di Azure Data Explorer che si vuole monitorare.
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
  
    ![Aggiungere log di diagnosticaAdd diagnostics logs](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selezionare **Aggiungi impostazione diagnostica**.
1. Nella finestra **Impostazioni di diagnostica:**
 
    ![Configurazione delle impostazioni di diagnostica](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selezionare **Nome** per l'impostazione di diagnostica.
    1. Selezionare una o più destinazioni: un account di archiviazione, un hub eventi o Log Analytics.Select one or more targets: a Storage account, Event Hub, or Log Analytics.
    1. Selezionare i registri `SucceededIngestion` `FailedIngestion`da raccogliere: o .
    1. Selezionare [le metriche](using-metrics.md#supported-azure-data-explorer-metrics) da raccogliere (facoltativo).  
    1. Selezionare **Salva** per salvare le nuove impostazioni e metriche dei log di diagnostica.
    1. Creare una **nuova richiesta** di supporto nel portale di Azure per richiedere l'attivazione dei log di diagnostica.

Le nuove impostazioni verranno impostate in pochi minuti. I log vengono quindi visualizzati nella destinazione di archiviazione configurata (account di archiviazione, hub eventi o Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica

Tutti i log di diagnostica di Monitoraggio di [Azure condividono uno schema di primo livello comune.](/azure/azure-monitor/platform/diagnostic-logs-schema) Azure Data Explorer ha proprietà univoche per i propri eventi. Tutti i log vengono archiviati in un formato JSON.

### <a name="ingestion-logs-schema"></a>Schema dei log di inserimento

Le stringhe JSON di log includono elementi elencati nella tabella seguente:Log JSON strings include elements listed in the following table:

|Nome               |Descrizione
|---                |---
|time               |Ora della relazione
|resourceId         |ID della risorsa Azure Resource Manager
|operationName      |Nome dell'operazione: 'MICROSOFT. KUSTO/CLUSTERS/INGEST/ACTION'
|operationVersion   |Versione dello schema: '1.0'Schema version: '1.0' 
|category           |Categoria dell'operazione. `SucceededIngestion` o `FailedIngestion`. Le proprietà differiscono per il [corretto funzionamento](#successful-ingestion-operation-log) o [l'operazione non riuscita.](#failed-ingestion-operation-log)
|properties         |Informazioni dettagliate sull'operazione.

#### <a name="successful-ingestion-operation-log"></a>Log delle operazioni di inserimento riuscite

**Esempio:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "SucceededIngestion",
    "properties":
    {
        "succeededOn": "2019-05-27 07:55:05.3693628",
        "operationId": "b446c48f-6e2f-4884-b723-92eb6dc99cc9",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "66a2959e-80de-4952-975d-b65072fc571d",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events8347293.json",
        "rootActivityId": "d0bd5dd3-c564-4647-953e-05670e22a81d"
    }
}
```
**Proprietà di un log di diagnostica dell'operazione riuscitaProperties of a successful operation operation diagnostic log**

|Nome               |Descrizione
|---                |---
|succeededOn        |Tempo di completamento dell'inserimento
|operationId        |Azure Data Explorer ingestion operation ID
|database           |Nome del database di destinazione
|tabella              |Nome della tabella di destinazione
|ingestionSourceId  |ID dell'origine dati di inserimento
|ingestionSourcePath|Percorso dell'origine dati di inserimento o dell'URI BLOB
|rootActivityId (idattività)     |ID attività

#### <a name="failed-ingestion-operation-log"></a>Log dell'operazione di inserimento non riuscita

**Esempio:**

```json
{
    "time": "",
    "resourceId": "",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "properties":
    {
        "failedOn": "2019-05-27 08:57:05.4273524",
        "operationId": "5956515d-9a48-4544-a514-cf4656fe7f95",
        "database": "Samples",
        "table": "StormEvents",
        "ingestionSourceId": "eee56f8c-2211-4ea4-93a6-be556e853e5f",
        "ingestionSourcePath": "https://kustoingestionlogs.blob.core.windows.net/sampledata/events5725592.json",
        "rootActivityId": "52134905-947a-4231-afaf-13d9b7b184d5",
        "details": "Permanent failure downloading blob. URI: ..., permanentReason: Download_SourceNotFound, DownloadFailedException: 'Could not find file ...'",
        "errorCode": "Download_SourceNotFound",
        "failureStatus": "Permanent",
        "originatesFromUpdatePolicy": false,
        "shouldRetry": false
    }
}
```

**Proprietà di un log di diagnostica dell'operazione non riuscita**

|Nome               |Descrizione
|---                |---
|failedOn           |Tempo di completamento dell'inserimento
|operationId        |Azure Data Explorer ingestion operation ID
|database           |Nome del database di destinazione
|tabella              |Nome della tabella di destinazione
|ingestionSourceId  |ID dell'origine dati di inserimento
|ingestionSourcePath|Percorso dell'origine dati di inserimento o dell'URI BLOB
|rootActivityId (idattività)     |ID attività
|dettagli            |Descrizione dettagliata dell'errore e del messaggio di errore
|errorCode          |Codice di errore 
|failureStatus (Stato errore)      |`Permanent` o `Transient`. La ripetizione di un errore temporaneo può avere esito positivo.
|originatesFromUpdatePolicy|True se l'errore ha origine da un criterio di aggiornamentoTrue if failure originates from an update policy
|dovrebbeRiprovare        |True se il nuovo tentativo può avere esito positivo

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Eseguire l'inserimento dei dati e il monitoraggio delle query in Esplora dati di AzureTutorial: Ingest and query monitoring data in Azure Data Explorer](ingest-data-no-code.md)
* [Usare le metriche per monitorare l'integrità del cluster](using-metrics.md)

