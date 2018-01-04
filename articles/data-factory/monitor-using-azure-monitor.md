---
title: Monitorare le data factory con Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare Monitoraggio di Azure per monitorare le pipeline di Data Factory abilitando i log di diagnostica con informazioni da Azure Data Factory.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: f30042ad8d687db59e1aaa092c46cee371e8c7fb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="monitor-data-factories-using-azure-monitor"></a>Monitorare le data factory con Monitoraggio di Azure  
Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

Attualmente Monitoraggio di Azure offre metriche e log dell'infrastruttura di livello base per la maggior parte dei servizi in Microsoft Azure, Per informazioni dettagliate, vedere [Panoramica di Monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). I log di diagnostica di Azure sono log generati da una risorsa che forniscono dati completi e frequenti sul funzionamento di tale risorsa. Data Factory visualizza i log di diagnostica in Monitoraggio di Azure. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere [Monitorare e gestire le pipeline di Azure Data Factory con il portale di Azure e PowerShell](v1/data-factory-monitor-manage-pipelines.md).

## <a name="diagnostic-logs"></a>Log di diagnostica

* Salvarli in un **account di archiviazione** per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni usando le impostazioni di diagnostica.
* Trasmettere i log a **Hub eventi** per l'inserimento tramite un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con **Log Analytics di Operations Management Suite (OMS)**

È possibile usare un account di archiviazione o un hub eventi dello spazio dei nomi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre avere il controllo degli accessi in base al ruolo appropriato a entrambe le sottoscrizioni.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica
I log di diagnostica per le risorse non di calcolo vengono configurati usando le impostazioni di diagnostica. Le impostazioni di diagnostica per una risorsa controllano:

* Destinazione dei log di diagnostica, ad esempio un account di archiviazione, Hub eventi e/o Log Analytics di OMS.
* Categorie di log da inviare.
* Periodo di tempo in cui ogni log di categoria deve essere mantenuto nell'account di archiviazione
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
* Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio sono selezionate solo le opzioni Hub eventi o OMS, i criteri di conservazione non hanno alcun effetto.
* I criteri di conservazione vengono applicati su base giornaliera. Al termine della giornata (UTC), i log relativi a tale giornata che non rientrano più nei criteri di conservazione verranno eliminati. Se, ad esempio, è presente un criterio di conservazione di un giorno, all'inizio della giornata vengono eliminati i log relativi al giorno precedente.

### <a name="enable-diagnostic-logs-via-rest-apis"></a>Abilitare i log di diagnostica tramite le API REST

Creare o aggiornare un'impostazione di diagnostica nell'API REST di Monitoraggio di Azure

**Richiesta**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Intestazioni**
* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si vogliono modificare le impostazioni di diagnostica. Per altre informazioni, vedere [Uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/resource-group-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per altre informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/active-directory-authentication-scenarios.md).

**Corpo**
```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
} 
```

| Proprietà | type | DESCRIZIONE |
| --- | --- | --- |
| storageAccountId |string | ID risorsa dell'account di archiviazione a cui si vogliono inviare i log di diagnostica |
| serviceBusRuleId |string | ID regola del bus di servizio dello spazio dei nomi del bus di servizio in cui creare gli hub eventi per il flusso dei log di diagnostica. Il formato dell'ID della regola è: "{ID risorsa bus di servizio}/authorizationrules/{nome chiave}".|
| workspaceId | Tipo complesso | Matrice di intervalli di tempo di metrica e relativi criteri di conservazione. Questa proprietà è attualmente vuota. |
|Metriche| Valori di parametri della pipeline eseguita da passare alla pipeline richiamata| Oggetto JSON che esegue il mapping dei nomi di parametro ai valori degli argomenti | 
| logs| Tipo complesso| Nome di una categoria di log di diagnostica per un tipo di risorsa. Per ottenere l'elenco di una categoria di log di diagnostica per una risorsa, eseguire prima un'operazione GET sulle impostazioni di diagnostica. |
| category| string| Matrice di categorie di log e relativi criteri di conservazione |
| timeGrain | string | Granularità delle metriche acquisite nel formato di durata ISO 8601. Deve essere PT1M (un minuto)|
| Enabled| boolean | Specifica se la raccolta di tale metrica o categoria di log è abilitata per questa risorsa|
| retentionPolicy| Tipo complesso| Descrive i criteri di conservazione per una metrica o categoria di log. Usata solo per l'opzione dell'account di archiviazione.|
| days| int| Numero di giorni di conservazione delle metriche o dei log. Se il valore è 0, i log vengono conservati all'infinito. Usata solo per l'opzione dell'account di archiviazione. |

**Risposta**

200 - OK


```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<OMSName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

Ottenere informazioni sull'impostazione di diagnostica nell'API REST di Monitoraggio di Azure

**Richiesta**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**Intestazioni**
* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si vogliono modificare le impostazioni di diagnostica. Per altre informazioni, vedere Uso di gruppi di risorse per gestire le risorse di Azure.
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per altre informazioni, vedere Autenticazione delle richieste.

**Risposta**

200 - OK

```json
{
    "id": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/1e42591f-1f0c-4c5a-b7f2-a268f6105ec5/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/0ee78edb-a0ad-456c-a0a2-901bf542c102/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Altre informazioni qui](https://msdn.microsoft.com/it-it/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>Schema di log ed eventi

### <a name="activity-run-logs-attributes"></a>Attributi dei log di esecuzione attività

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties:" 
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Proprietà | type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Il livello 4 è sempre valido per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, in formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| string| ID dell'esecuzione attività | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| string| ID dell'esecuzione pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "ActivityRuns" | `ActivityRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome dell'attività con lo stato. Se lo stato è l'heartbeat di inizio, è impostato su `MyActivity -`. Se lo stato è l'heartbeat di fine, è impostato su `MyActivity - Succeeded` con lo stato finale | `MyActivity - Succeeded` |
|pipelineName| string | Nome della pipeline | `MyPipeline` |
|activityName| string | Nome dell'attività | `MyActivity` |
|start| string | Inizio dell'esecuzione attività nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Fine dell'esecuzione attività nell'intervallo di tempo, in formato UTC. Se l'attività non è ancora terminata (log di diagnostica per l'avvio di un'attività), viene impostato il valore predefinito `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>Attributi dei log di esecuzione pipeline

```json
{  
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties": 
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Proprietà | type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Il livello 4 è valido per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, in formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| string| ID dell'esecuzione pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|ResourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "PipelineRuns" | `PipelineRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome della pipeline con lo stato. "Pipeline - Succeeded" con lo stato finale indicante quando l'esecuzione pipeline viene completata| `MyPipeline - Succeeded` |
|pipelineName| string | Nome della pipeline | `MyPipeline` |
|start| string | Inizio dell'esecuzione attività nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Fine delle esecuzioni attività nell'intervallo di tempo, in formato UTC. Se l'attività non è ancora terminata (log di diagnostica per l'avvio di un'attività), viene impostato il valore predefinito `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|status| string | Stato finale dell'esecuzione pipeline (Succeeded o Failed) | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>Attributi dei log di esecuzione trigger

```json
{ 
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
} 

```

| Proprietà | type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Impostare sul livello 4 per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, in formato UTC | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| string| ID dell'esecuzione del trigger | `08587023010602533858661257311` |
|ResourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "PipelineRuns" | `PipelineRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome del trigger con lo stato finale indicante se è stato attivato correttamente. "MyTrigger - Succeeded" se l'heartbeat è riuscito| `MyTrigger - Succeeded` |
|triggerName| string | Nome del trigger | `MyTrigger` |
|triggerType| string | Tipo del trigger (trigger manuale o trigger di pianificazione) | `ScheduleTrigger` |
|triggerEvent| string | Evento del trigger | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| string | Avvio dell'attivazione del trigger nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| string | Stato finale indicante se il trigger è stato attivato correttamente (Succeeded o Failed) | `Succeeded`|

### <a name="metrics"></a>Metriche

Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il monitoraggio di Azure offre alcuni modi per configurare e usare queste metriche per il monitoraggio e la risoluzione dei problemi.

ADFV2 genera le metriche seguenti

| **Metrica**           | **Nome visualizzato per la metrica**         | **Unità** | **Tipo di aggregazione** | **Descrizione**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite) | Conteggio    | Totale                | Esecuzioni pipeline totali riuscite in un minuto |
| PipelineFailedRuns   | Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)    | Conteggio    | Totale                | Esecuzioni pipeline totali non riuscite in un minuto    |
| ActiviySucceededRuns | Succeeded activity runs metrics (Metrica esecuzioni attività riuscite) | Conteggio    | Totale                | Esecuzioni attività totali riuscite in un minuto  |
| ActivityFailedRuns   | Failed activity runs metrics (Metrica esecuzioni attività non riuscite)    | Conteggio    | Totale                | Esecuzioni attività totali non riuscite in un minuto     |
| TriggerSucceededRuns | Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)  | Conteggio    | Totale                | Esecuzioni trigger totali riuscite in un minuto   |
| TriggerFailedRuns    | Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)     | Conteggio    | Totale                | Esecuzioni trigger totali non riuscite in un minuto      |

Per le metriche di accesso, seguire le istruzioni nell'articolo - https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo [Monitor and manage pipelines programmatically (Monitorare e gestire le pipeline a livello di codice)](monitor-programmatically.md) per informazioni sul monitoraggio e sulla gestione delle pipeline. 