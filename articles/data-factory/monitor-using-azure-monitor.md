---
title: Monitorare le data factory con Monitoraggio di Azure
description: Informazioni su come usare Monitoraggio di Azure per monitorare le pipeline /Azure Data Factory abilitando i log di diagnostica con informazioni da Data Factory.Learn how to use Azure Monitor to monitor /Azure Data Factory pipelines by enabling diagnostic logs with information from Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419460"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Avvisare e monitorare le data factory tramite Monitoraggio di AzureAlert and monitor data factories by using Azure Monitor

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le applicazioni cloud sono complesse e hanno molte parti in movimento. I monitoraggi forniscono dati che contribuiscono a garantire che le applicazioni rimangano in funzione in uno stato integro. I monitoraggi consentono inoltre di evitare potenziali problemi e di risolvere quelli passati.

È possibile usare i dati di monitoraggio per ottenere informazioni approfondite sulle applicazioni. Questa conoscenza consente di migliorare le prestazioni e la manutenibilità dell'applicazione. Consente inoltre di automatizzare le azioni che altrimenti richiedono un intervento manuale.

Monitoraggio di Azure offre metriche dell'infrastruttura di livello base e log per la maggior parte dei servizi di Azure.Azure Monitor provides base-level infrastructure metrics and logs for most Azure services. I log di diagnostica di Azure vengono generati da una risorsa e forniscono dati frequenti e dettagliati sul funzionamento di tale risorsa. Azure Data Factory scrive inoltre i log di diagnostica in Monitor.And Azure Data Factory writes diagnostic logs in Monitor.

Per informazioni dettagliate, vedere Panoramica di Monitoraggio di Azure .For [details,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)see Azure Monitor overview .

## <a name="keeping-azure-data-factory-data"></a>Mantenere i dati di Azure Data FactoryKeeping Azure Data Factory data

Data Factory archivia i dati eseguiti dalla pipeline per soli 45 giorni. Utilizzare Monitor se si desidera mantenere i dati per un periodo di tempo più lungo. Con Monitor è possibile instradare i log di diagnostica per l'analisi. È anche possibile conservarli in un account di archiviazione in modo da disporre di informazioni di fabbrica per la durata scelta.

## <a name="diagnostic-logs"></a>Log di diagnostica

* Salvare i log di diagnostica in un account di archiviazione per il controllo o l'ispezione manuale. È possibile utilizzare le impostazioni di diagnostica per specificare il tempo di conservazione in giorni.
* Trasmettere i log agli hub eventi di Azure.Stream the logs to Azure Event Hubs. I log diventano input per un servizio partner o per una soluzione di analisi personalizzata come Power BI.
* Analizzare i log con Log Analytics.

È possibile usare un account di archiviazione o uno spazio dei nomi dell'hub di eventi che non si trovi nella sottoscrizione della risorsa che genera i log. L'utente che configura l'impostazione deve disporre dell'accesso appropriato al controllo degli accessi in base al ruolo a entrambe le sottoscrizioni.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica

Usare le impostazioni di diagnostica per configurare i log di diagnostica per le risorse non di calcolo. Le impostazioni per un controllo delle risorse dispongono delle funzionalità seguenti:The settings for a resource control have the following features:

* Specificano dove vengono inviati i log di diagnostica. Gli esempi includono un account di archiviazione di Azure, un hub eventi di Azure o registri di monitoraggio.
* Specificano quali categorie di log vengono inviate.
* Specificano per quanto tempo ogni categoria di log deve essere mantenuta in un account di archiviazione.
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 2.147.483.647.
* Se i criteri di conservazione sono impostati ma l'archiviazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. Ad esempio, questa condizione può verificarsi quando sono selezionate solo le opzioni Hub eventi o Registri di monitoraggio.
* I criteri di conservazione vengono applicati al giorno. Il limite tra i giorni si verifica a mezzanotte ora UTC (Coordinated Universal Time). Alla fine di una giornata, i registri di giorni che vanno oltre i criteri di conservazione vengono eliminati. Ad esempio, se si dispone di un criterio di conservazione di un giorno, all'inizio di oggi i registri di ieri vengono eliminati.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Abilitare i log di diagnostica tramite l'API REST di Monitoraggio di AzureEnable diagnostic logs via the Azure Monitor REST API

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Creare o aggiornare un'impostazione di diagnostica nell'API REST di monitoraggioCreate or update a diagnostics setting in the Monitor REST API

##### <a name="request"></a>Richiesta

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere Uso dei gruppi di risorse per gestire le risorse di Azure.For more information, see [Using Resource groups to manage your Azure resources.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione di autorizzazione sul token Web JSON ottenuto da Azure Active Directory (Azure AD). Per ulteriori informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Corpo

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

| Proprietà | Type | Descrizione |
| --- | --- | --- |
| **storageAccountId** |string | ID risorsa dell'account di archiviazione a cui si desidera inviare i log di diagnostica. |
| **serviceBusRuleId** |string | ID della regola del bus di servizio dello spazio dei nomi del bus di servizio in cui si desidera creare hub eventi per lo streaming dei log di diagnostica. L'ID regola `{service bus resource ID}/authorizationrules/{key name}`ha il formato .|
| **workspaceId** | Tipo complesso | Matrice di granularità metrica e dei relativi criteri di conservazione. Il valore di questa proprietà è vuoto. |
|**Metriche**| Valori di parametri della pipeline eseguita da passare alla pipeline richiamata| Oggetto JSON che esegue il mapping dei nomi dei parametri ai valori degli argomenti. |
| **Registri**| Tipo complesso| Nome di una categoria di log di diagnostica per un tipo di risorsa. Per ottenere l'elenco delle categorie di log di diagnostica per una risorsa, eseguire un'operazione GET sulle impostazioni di diagnostica. |
| **Categoria**| string| Matrice di categorie di log e relativi criteri di conservazione. |
| **timeGrain** | string | La granularità delle metriche, acquisite nel formato di durata ISO 8601. Il valore della `PT1M`proprietà deve essere , che specifica un minuto. |
| **Abilitato**| Boolean | Specifica se la raccolta della metrica o della categoria di log è abilitata per questa risorsa. |
| **retentionPolicy**| Tipo complesso| Descrive i criteri di conservazione per una metrica o categoria di log. Questa proprietà viene usata solo per gli account di archiviazione. |
|**Giorni**| Int| Numero di giorni per cui mantenere le metriche o i log. Se il valore della proprietà è 0, i registri vengono mantenuti per sempre. Questa proprietà viene usata solo per gli account di archiviazione. |

##### <a name="response"></a>Risposta

200 OK.


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Ottenere informazioni sulle impostazioni di diagnostica nell'API REST di monitoraggioGet information about diagnostics settings in the Monitor REST API

##### <a name="request"></a>Richiesta

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere Uso dei gruppi di risorse per gestire le risorse di Azure.For more information, see [Using Resource groups to manage your Azure resources.](../azure-resource-manager/management/manage-resource-groups-portal.md)
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione di autorizzazione su un token Web JSON ottenuto da Azure AD. Per ulteriori informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Risposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
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
Per ulteriori informazioni, vedere [Impostazioni di diagnostica](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema dei registri e degli eventi

### <a name="monitor-schema"></a>Monitorare lo schema

#### <a name="activity-run-log-attributes"></a>Attributi del log di esecuzione dell'attivitàActivity-run log attributes

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
   "properties":
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

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione dell'attività, impostare il valore della proprietà su 4.For activity-run logs, set the property value to 4. | `4` |
| **Correlationid** |string | ID univoco per tenere traccia di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC dell'intervallo di tempo. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| string| ID dell'esecuzione dell'attività. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| string| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| string | ID associato alla risorsa data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `ActivityRuns`. | `ActivityRuns` |
|**Livello**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**Nomeoperazione**| string | Nome dell'attività con il relativo stato. Se l'attività è l'heartbeat `MyActivity -`di avvio, il valore della proprietà è . Se l'attività è l'heartbeat `MyActivity - Succeeded`finale, il valore della proprietà è . | `MyActivity - Succeeded` |
|**pipelineName**| string | Nome della pipeline | `MyPipeline` |
|**activityName**| string | Nome dell'attività. | `MyActivity` |
|**start**| string | Ora di inizio dell'attività viene eseguita in formato UTC dell'intervallo di tempo. | `2017-06-26T20:55:29.5007959Z`|
|**end**| string | Ora di fine dell'attività viene eseguita in formato UTC dell'intervallo di tempo. Se il log di diagnostica indica che un'attività è `1601-01-01T00:00:00Z`iniziata ma non ancora terminata, il valore della proprietà è . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Attributi del log di esecuzione della pipelinePipeline-run log attributes

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

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione dell'attività, impostare il valore della proprietà su 4.For activity-run logs, set the property value to 4. | `4` |
| **Correlationid** |string | ID univoco per tenere traccia di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC dell'intervallo di tempo. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| string| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**Resourceid**| string | ID associato alla risorsa data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `PipelineRuns`. | `PipelineRuns` |
|**Livello**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**Nomeoperazione**| string | Nome della pipeline insieme al relativo stato. Al termine dell'esecuzione della pipeline, il valore della proprietà è `Pipeline - Succeeded`. | `MyPipeline - Succeeded`. |
|**pipelineName**| string | Nome della pipeline | `MyPipeline` |
|**start**| string | Ora di inizio dell'attività viene eseguita in formato UTC dell'intervallo di tempo. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| string | Ora di fine dell'attività viene eseguita in formato UTC dell'intervallo di tempo. Se il log di diagnostica indica che un'attività `1601-01-01T00:00:00Z`è iniziata ma non ancora terminata, il valore della proprietà è .  | `2017-06-26T20:55:29.5007959Z` |
|**status**| string | Stato finale dell'esecuzione della pipeline. I valori `Succeeded` di `Failed`proprietà possibili sono e . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Attributi del log di esecuzione del triggerTrigger-run log attributes

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

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione dell'attività, impostare il valore della proprietà su 4.For activity-run logs, set the property value to 4. | `4` |
| **Correlationid** |string | ID univoco per tenere traccia di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC dell'intervallo di tempo. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| string| ID dell'esecuzione del trigger. | `08587023010602533858661257311` |
|**Resourceid**| string | ID associato alla risorsa data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**Categoria**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `PipelineRuns`. | `PipelineRuns` |
|**Livello**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**Nomeoperazione**| string | Nome del trigger con stato finale, che indica se il trigger è stato attivato correttamente. Se l'heartbeat ha avuto `MyTrigger - Succeeded`esito positivo, il valore della proprietà è . | `MyTrigger - Succeeded` |
|**triggerName (nome trigger)**| string | Nome del trigger. | `MyTrigger` |
|**triggerType (Tipo di trigger)**| string | Tipo di trigger. I valori `Manual Trigger` di `Schedule Trigger`proprietà possibili sono e . | `ScheduleTrigger` |
|**triggerEvent**| string | Evento del trigger. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| string | Ora di inizio dell'attivazione del trigger in formato UTC dell'intervallo di tempo. | `2017-06-26T20:55:29.5007959Z`|
|**status**| string | Stato finale che indica se il trigger è stato attivato correttamente. I valori `Succeeded` di `Failed`proprietà possibili sono e . | `Succeeded`|

### <a name="log-analytics-schema"></a>Schema di Log Analytics

Log Analytics eredita lo schema da Monitor con le eccezioni seguenti:Log Analytics inherits the schema from Monitor with the following exceptions:

* La prima lettera in ogni nome di colonna è maiuscola. Ad esempio, il nome di colonna "correlationId" in Monitor è "CorrelationId" in Log Analytics.For example, the column name "correlationId" in Monitor is "CorrelationId" in Log Analytics.
* Non c'è nessuna colonna "Livello".
* La colonna dinamica "proprietà" viene mantenuta come tipo di BLOB JSON dinamico seguente.

    | Colonna Monitoraggio di Azure | Colonna Log Analytics | Type |
    | --- | --- | --- |
    | Proprietà .properties. UserProperties (Proprietà utente) | UserProperties (Proprietà utente) | Dinamico |
    | Proprietà .properties. Annotazioni | annotazioni | Dinamico |
    | Proprietà .properties. Input | Input | Dinamico |
    | Proprietà .properties. Output | Output | Dinamico |
    | Proprietà .properties. Error.errorCode (Codice Errore) | ErrorCode | INT |
    | Proprietà .properties. Error.message | ErrorMessage | string |
    | Proprietà .properties. Errore | Errore | Dinamico |
    | Proprietà .properties. Predecessori | Predecessori | Dinamico |
    | Proprietà .properties. Parametri | Parametri | Dinamico |
    | Proprietà .properties. Systemparameters | SystemParameters | Dinamico |
    | Proprietà .properties. Tag | Tag | Dinamico |
    
## <a name="metrics"></a>Metriche

Con Monitor è possibile ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro di Azure.With Monitor, you can gain visibility on the performance and health of your Azure workloads. Il tipo più importante di dati di Monitor è la metrica, detta anche contatore delle prestazioni. Le metriche vengono generate dalla maggior parte delle risorse di Azure.Metrics are emitted by most Azure resources. Monitor offre diversi modi per configurare e utilizzare queste metriche per il monitoraggio e la risoluzione dei problemi.

Azure Data Factory versione 2 genera le metriche seguenti.

| **Metrica**           | **Nome visualizzato metrica**         | **Unità** | **Tipo di aggregazione** | **Descrizione**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite) | Conteggio    | Totale                | Numero totale di esecuzioni della pipeline che hanno avuto esito positivo in un intervallo di minuti. |
| PipelineFailedRuns   | Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)    | Conteggio    | Totale                | Numero totale di esecuzioni della pipeline non riuscite entro un intervallo di minuti.    |
| ActivitySucceededRuns | Succeeded activity runs metrics (Metrica esecuzioni attività riuscite) | Conteggio    | Totale                | Numero totale di esecuzioni di attività eseguite in un intervallo di minuti.  |
| ActivityFailedRuns   | Failed activity runs metrics (Metrica esecuzioni attività non riuscite)    | Conteggio    | Totale                | Numero totale di esecuzioni di attività non riuscite entro un intervallo di minuti.     |
| TriggerSucceededRuns | Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)  | Conteggio    | Totale                | Numero totale di esecuzioni di trigger eseguite in un intervallo di minuti.   |
| TriggerFailedRuns    | Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)     | Conteggio    | Totale                | Numero totale di esecuzioni di trigger non riuscite entro un intervallo di minuti.      |

Per accedere alle metriche, completare le istruzioni nella piattaforma dati di Monitoraggio di Azure.To access the metrics, complete the instructions in [Azure Monitor data platform](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Vengono generati solo gli eventi di esecuzione di processi completati, attività attivate ed esecuzione della pipeline. Le esecuzioni in corso e sandbox/debug **non** vengono generate. 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitor Data Factory metrics with Azure Monitor

È possibile usare l'integrazione di Data Factory con Monitor per instradare i dati da monitorare. Questa integrazione è utile negli scenari seguenti:

* Si desidera scrivere query complesse in un set completo di metriche pubblicate da Data Factory da Monitorare. È possibile creare avvisi personalizzati su queste query tramite Monitor.You can create custom alerts on these queries via Monitor.

* Per eseguire il monitoraggio nelle data factory È possibile instradare i dati da più data factory a una singola area di lavoro Monitor.You can route data from multiple data factories to a single Monitor workspace.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configurare le impostazioni di diagnostica e l'area di lavoro

Creare o aggiungere impostazioni di diagnostica per la data factory.

1. Nel portale passare a Monitor.In the portal, go to Monitor. Selezionare **Impostazioni** > **di diagnostica**.

1. Selezionare la data factory per la quale si desidera impostare un'impostazione di diagnostica.

1. Se non esistono impostazioni nella data factory selezionata, viene richiesto di creare un'impostazione. Selezionare **Attiva diagnostica**.

   ![Creare un'impostazione di diagnostica se non esistono impostazioniCreate a diagnostic setting if no settings exist](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se sono presenti impostazioni esistenti nella data factory, viene visualizzato un elenco di impostazioni già configurate nella data factory. Selezionare **Aggiungi impostazione diagnostica**.

   ![Aggiungere un'impostazione di diagnostica se esistono impostazioniAdd a diagnostic setting if settings exist](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Assegnare un nome all'impostazione, selezionare **Invia a Log Analytics**e quindi selezionare un'area di lavoro da Log Analytics **Workspace**.

    ![Assegnare un nome alle impostazioni e selezionare un'area di lavoro di log-analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Selezionare **Salva**.

Dopo alcuni istanti, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa data factory. I log di diagnostica vengono trasmessi all'area di lavoro non appena vengono generati nuovi dati degli eventi. Potrebbero trascorrere fino a 15 minuti tra quando viene generato un evento e quando viene visualizzato in Log Analytics.

* In modalità _specifica della risorsa,_ i log di diagnostica da Azure Data Factory scorrono nelle tabelle _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_
* In modalità _diagnostica di Azure_, i log di diagnostica vengono inviati alla tabella _AzureDiagnostics_

> [!NOTE]
> Poiché una tabella di log di Azure non può avere più di 500 colonne, è consigliabile selezionare Modalità specifica della risorsa. Per ulteriori informazioni, vedere [Log Analytics Known Limitations](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installare il pacchetto di Azure Data Factory Analytics dal Marketplace di Azure

![Vai a "Azure Marketplace", immetti "Filtro di analisi" e seleziona "Analisi di Azure Data Factory (anteprima")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Dettagli su "Analisi di Azure Data Factory (anteprima)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selezionare **Crea,** quindi selezionare **Impostazioni area di lavoro OMS** e Area di lavoro **OMS**.

![Creazione di una nuova soluzione](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorare le metriche di Data FactoryMonitor Data Factory metrics

L'installazione di Analisi di Azure Data Factory crea un set predefinito di visualizzazioni in modo che le metriche seguenti vengano abilitate:Installing Azure Data Factory Analytics creates a default set of views so that the following metrics become enabled:

- Corse ADF - 1) Esecuzioni pipeline per data factory
 
- Esecuzioni di Azure Data Factory - 2) Esecuzioni di attività di Data Factory

- Esecuzioni ADF - 3) Esecuzioni trigger per Data Factory

- Errori ADF - 1) Primi 10 errori della pipeline per data factory

- Errori ADF - 2) Top 10 attività eseguite da Data Factory

- Errori ADF - 3) Primi 10 errori di trigger da data factory

- Statistiche ADF - 1) Attività viene eseguita per tipo

- Statistiche ADF - 2) Trigger viene eseguito per tipo

- Statistiche ADF - 3) Durata massima esecuzioni pipeline

![Finestra con l'opzione "Cartelle di lavoro (anteprima)" e "AzureDataFactoryAnalytics" evidenziata](media/data-factory-monitor-oms/monitor-oms-image6.png)

È possibile visualizzare le metriche precedenti, esaminare le query alla base di queste metriche, modificare le query, creare avvisi ed eseguire altre azioni.

![Rappresentazione grafica delle esecuzioni della pipeline per data factory"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Analisi di Azure Data Factory (anteprima) invia i log di diagnostica alle tabelle di destinazione _specifiche delle risorse._ È possibile scrivere query sulle tabelle seguenti: _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_.

## <a name="alerts"></a>Avvisi

Accedere al portale di Azure e selezionare **Monitora** > **avvisi** per creare avvisi.

![Avvisi nel menu del portale](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Creare avvisi

1. Selezionare **+ Nuova regola di avviso** per creare un nuovo avviso.

    ![Nuova regola di avviso](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definire la condizione di avviso.

    > [!NOTE]
    > Assicurarsi di selezionare **Tutto** nell'elenco a discesa Filtra per **tipo di risorsa.**

    !["Definisci condizione di avviso" > "Seleziona destinazione", che apre il riquadro "Seleziona una risorsa" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definisci condizione di avviso" >" Aggiungi criteri", che apre il riquadro "Configura logica di segnale"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Riquadro "Configura tipo di segnale"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definire i dettagli dell'avviso.

    ![Dettagli dell'avviso](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definire il gruppo di azioni.

    ![Creare una regola con l'opzione "Nuovo gruppo di azioni" evidenziata](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Creare un nuovo gruppo di azioni](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurare e-mail, SMS, push e voce](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definire un gruppo di azioni](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passaggi successivi
[Monitorare e gestire le pipeline a livello di codiceMonitor and manage pipelines programmatically](monitor-programmatically.md)
