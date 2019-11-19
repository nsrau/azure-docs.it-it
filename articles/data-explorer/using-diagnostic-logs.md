---
title: Monitorare le operazioni di inserimento Esplora dati di Azure usando i log di diagnostica
description: Informazioni su come configurare i log di diagnostica per Esplora dati di Azure per monitorare le operazioni di inserimento.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/18/2019
ms.openlocfilehash: 13f86f0156299619d8bf8d92eb92bbcf8b4cb76c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173805"
---
# <a name="monitor-azure-data-explorer-ingestion-operations-using-diagnostic-logs-preview"></a>Monitorare le operazioni di inserimento di Azure Esplora dati usando i log di diagnostica (anteprima)

Esplora dati di Azure è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale di volumi elevati di dati in streaming provenienti da applicazioni, siti Web, dispositivi IoT e altro ancora. Per usare Esplora dati di Azure, è necessario prima creare un cluster e quindi uno o più database al suo interno. Quindi si inseriscono (caricano) i dati in una tabella di un database in modo che sia possibile eseguire query su di essa. I [log di diagnostica di monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) forniscono i dati sul funzionamento delle risorse di Azure. Azure Esplora dati usa i log di diagnostica per ottenere informazioni dettagliate sugli errori e sugli errori di inserimento. È possibile esportare i log delle operazioni in archiviazione di Azure, Hub eventi o Log Analytics per monitorare lo stato di inserimento. I log da archiviazione di Azure e hub eventi di Azure possono essere indirizzati a una tabella nel cluster di Azure Esplora dati per un'ulteriore analisi.

## <a name="prerequisites"></a>prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/).
* Creare un [cluster e un database](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="set-up-diagnostic-logs-for-an-azure-data-explorer-cluster"></a>Configurare i log di diagnostica per un cluster di Esplora dati di Azure

I log di diagnostica possono essere usati per configurare la raccolta dei dati di log seguenti:
* Operazioni di inserimento riuscite: questi log contengono informazioni sulle operazioni di inserimento completate correttamente.
* Operazioni di inserimento non riuscite: questi log contengono informazioni dettagliate sulle operazioni di inserimento non riuscite, inclusi i dettagli dell'errore. 

I dati vengono quindi archiviati in un account di archiviazione, trasmessi a un hub eventi o inviati a Log Analytics, in base alle specifiche.

### <a name="enable-diagnostic-logs"></a>Abilitare i log di diagnostica

I log di diagnostica sono disabilitati per impostazione predefinita. Per abilitare i log di diagnostica, seguire questa procedura:

1. Nella [portale di Azure](https://portal.azure.com)selezionare la risorsa cluster di Azure Esplora dati che si desidera monitorare.
1. Selezionare **Impostazioni di diagnostica** in **Monitoraggio**.
  
    ![Aggiungere i log di diagnostica](media/using-diagnostic-logs/add-diagnostic-logs.png)

1. Selezionare **Aggiungi impostazioni di diagnostica**.
1. Nella finestra **impostazioni di diagnostica** :
 
    ![Configurazione impostazioni di diagnostica](media/using-diagnostic-logs/configure-diagnostics-settings.png) 

    1. Selezionare **nome** per l'impostazione di diagnostica.
    1. Selezionare una o più destinazioni: un account di archiviazione, un hub eventi o un Log Analytics.
    1. Selezionare i log da raccogliere: `SucceededIngestion` o `FailedIngestion`.
    1. Selezionare le [metriche](using-metrics.md) da raccogliere (facoltativo).   
    1. Selezionare **Save (Salva** ) per salvare le impostazioni e le metriche dei nuovi log di diagnostica.
    1. Creare una **nuova richiesta di supporto** nel portale di Azure per richiedere l'attivazione dei log di diagnostica.

Le nuove impostazioni verranno impostate in pochi minuti. I log vengono quindi visualizzati nella destinazione di archiviazione configurata (account di archiviazione, Hub eventi o Log Analytics). 

## <a name="diagnostic-logs-schema"></a>Schema dei log di diagnostica

Tutti i [log di diagnostica di monitoraggio di Azure condividono uno schema di primo livello comune](/azure/azure-monitor/platform/diagnostic-logs-schema). Azure Esplora dati dispone di proprietà univoche per i relativi eventi. Tutti i log vengono archiviati in formato JSON.

### <a name="ingestion-logs-schema"></a>Schema dei log di inserimento

Le stringhe JSON di log includono gli elementi elencati nella tabella seguente:

|Nome               |DESCRIZIONE
|---                |---
|time               |Ora del report
|resourceId         |ID della risorsa Azure Resource Manager
|operationName      |Nome dell'operazione:' MICROSOFT. KUSTO/CLUSTER/INSERIMENTO/AZIONE
|operationVersion   |Versione schema:' 1,0' 
|category           |Categoria dell'operazione. `SucceededIngestion` o `FailedIngestion`. Le proprietà sono diverse per l' [operazione riuscita](#successful-ingestion-operation-log) o [operazione non riuscita](#failed-ingestion-operation-log).
|properties         |Informazioni dettagliate sull'operazione.

#### <a name="successful-ingestion-operation-log"></a>Log delle operazioni di inserimento riuscito

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
**Proprietà di un log di diagnostica operazione completata**

|Nome               |DESCRIZIONE
|---                |---
|succeededOn        |Tempo di completamento dell'inserimento
|operationId        |ID operazione di inserimento Esplora dati Azure
|database           |Nome del database di destinazione
|table              |Nome della tabella di destinazione
|IngestionSourceId  |ID dell'origine dati di inserimento
|IngestionSourcePath|Percorso dell'origine dati di inserimento o dell'URI del BLOB
|RootActivityId     |ID attività

#### <a name="failed-ingestion-operation-log"></a>Log delle operazioni di inserimento non riuscito

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

**Proprietà di un log di diagnostica di un'operazione non riuscita**

|Nome               |DESCRIZIONE
|---                |---
|FailedOn           |Tempo di completamento dell'inserimento
|operationId        |ID operazione di inserimento Esplora dati Azure
|database           |Nome del database di destinazione
|table              |Nome della tabella di destinazione
|IngestionSourceId  |ID dell'origine dati di inserimento
|IngestionSourcePath|Percorso dell'origine dati di inserimento o dell'URI del BLOB
|RootActivityId     |ID attività
|informazioni dettagliate            |Descrizione dettagliata dell'errore e del messaggio di errore
|errorCode          |Codice di errore 
|FailureStatus      |`Permanent` o `Transient`. Il tentativo di un errore temporaneo potrebbe avere esito positivo.
|OriginatesFromUpdatePolicy|True se l'errore proviene da un criterio di aggiornamento
|ShouldRetry        |True se il tentativo potrebbe avere esito positivo

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: inserire ed eseguire query sui dati di monitoraggio in Azure Esplora dati](ingest-data-no-code.md)
* [Usare le metriche per monitorare l'integrità del cluster](using-metrics.md)

