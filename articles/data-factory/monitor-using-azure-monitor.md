---
title: Monitorare le data factory con Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare monitoraggio di Azure per monitorare le pipeline di/Azure Data Factory abilitando i log di diagnostica con le informazioni provenienti da Data Factory.
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
ms.openlocfilehash: 2a707eda6a7e32a95666dd70e196c8da3c3b7834
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815953"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>Avvisi e monitoraggio di data factory con monitoraggio di Azure

Le applicazioni cloud sono complesse e hanno molte parti mobili. I monitoraggi forniscono i dati per garantire che le applicazioni restino operativi in uno stato integro. I monitoraggi consentono inoltre di evitare potenziali problemi e risolvere i problemi precedenti.

È possibile usare i dati di monitoraggio per ottenere informazioni approfondite sulle applicazioni. Queste informazioni consentono di migliorare le prestazioni e la gestibilità delle applicazioni. Consente inoltre di automatizzare le azioni che altrimenti richiedono un intervento manuale.

Monitoraggio di Azure offre metriche e log dell'infrastruttura di livello base per la maggior parte dei servizi di Azure. I log di diagnostica di Azure vengono generati da una risorsa e forniscono dati avanzati e frequenti sul funzionamento di tale risorsa. E Azure Data Factory scrive i log di diagnostica in monitoraggio.

Per informazioni dettagliate, vedere [Panoramica di monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-data"></a>Conservazione dei dati di Azure Data Factory

Data Factory archivia i dati di esecuzione della pipeline solo per 45 giorni. Utilizzare il monitoraggio se si desidera che i dati vengano mantenuti per un periodo di tempo più lungo. Con monitoraggio è possibile instradare i log di diagnostica per l'analisi. È anche possibile mantenerli in un account di archiviazione in modo da ottenere informazioni sulla factory per la durata scelta.

## <a name="diagnostic-logs"></a>Log di diagnostica

* Salvare i log di diagnostica in un account di archiviazione per il controllo o l'ispezione manuale. È possibile usare le impostazioni di diagnostica per specificare il periodo di conservazione in giorni.
* Trasmettere i log a hub eventi di Azure. I log diventano input per un servizio partner o per una soluzione di analisi personalizzata come Power BI.
* Analizzare i log con Log Analytics.

È possibile usare un account di archiviazione o uno spazio dei nomi dell'hub eventi che non si trovi nella sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso di controllo degli accessi in base al ruolo (RBAC) appropriato a entrambe le sottoscrizioni.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica

Usare le impostazioni di diagnostica per configurare i log di diagnostica per le risorse non di calcolo. Le impostazioni per un controllo risorsa includono le funzionalità seguenti:

* Specificano la posizione in cui vengono inviati i log di diagnostica. Gli esempi includono un account di archiviazione di Azure, un hub eventi di Azure o i log di monitoraggio.
* Specificano le categorie di log da inviare.
* Specificano per quanto tempo ogni categoria di log deve essere conservata in un account di archiviazione.
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 2.147.483.647.
* Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. Questa condizione può verificarsi, ad esempio, quando vengono selezionate solo le opzioni Hub eventi o registri di monitoraggio.
* I criteri di conservazione vengono applicati al giorno. Il limite tra i giorni si verifica a mezzanotte UTC (Coordinated Universal Time). Alla fine di un giorno, i log da giorni che esulano dai criteri di conservazione vengono eliminati. Se, ad esempio, si dispone di un criterio di conservazione di un giorno, all'inizio di oggi vengono eliminati i log da prima di ieri.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Abilitare i log di diagnostica tramite l'API REST di monitoraggio di Azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Creare o aggiornare un'impostazione di diagnostica nell'API REST di monitoraggio

##### <a name="request"></a>Richiedi

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Intestazioni

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere [Uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione Authorization sul token Web JSON ottenuto dalla Azure Active Directory (Azure AD). Per altre informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Body

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
| **storageAccountId** |Stringa | ID risorsa dell'account di archiviazione a cui si vogliono inviare i log di diagnostica. |
| **serviceBusRuleId** |Stringa | ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui si vuole creare hub eventi per la trasmissione dei log di diagnostica. Il formato `{service bus resource ID}/authorizationrules/{key name}`dell'ID regola è.|
| **workspaceId** | Tipo complesso | Matrice di granularità temporale metrica e relativi criteri di conservazione. Il valore di questa proprietà è vuoto. |
|**metrics**| Valori di parametri della pipeline eseguita da passare alla pipeline richiamata| Oggetto JSON che esegue il mapping dei nomi di parametro ai valori degli argomenti. |
| **logs**| Tipo complesso| Nome di una categoria di log di diagnostica per un tipo di risorsa. Per ottenere l'elenco delle categorie di log di diagnostica per una risorsa, eseguire un'operazione GET Diagnostic-Settings. |
| **category**| Stringa| Una matrice di categorie di log e i relativi criteri di conservazione. |
| **timeGrain** | Stringa | Granularità delle metriche, acquisite nel formato di durata ISO 8601. Il valore della proprietà deve `PT1M`essere, che specifica un minuto. |
| **enabled**| Boolean | Specifica se la raccolta della metrica o della categoria di log è abilitata per questa risorsa. |
| **retentionPolicy**| Tipo complesso| Descrive i criteri di conservazione per una metrica o categoria di log. Questa proprietà viene usata solo per gli account di archiviazione. |
|**giorni**| Int| Numero di giorni di conservazione delle metriche o dei log. Se il valore della proprietà è 0, i log vengono conservati per sempre. Questa proprietà viene usata solo per gli account di archiviazione. |

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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Ottenere informazioni sulle impostazioni di diagnostica nell'API REST di monitoraggio

##### <a name="request"></a>Richiedi

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Intestazioni

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere [Uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione dell'autorizzazione su un token Web JSON ottenuto dal Azure AD. Per altre informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

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
Per altre informazioni, vedere [impostazioni di diagnostica](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema di log ed eventi

### <a name="monitor-schema"></a>Schema di monitoraggio

#### <a name="activity-run-log-attributes"></a>Attributi del log di esecuzione attività

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
| **Level** |Stringa | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |Stringa | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Stringa | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC di TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| Stringa| ID dell'esecuzione dell'attività. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| Stringa| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Stringa | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Stringa | Categoria dei log di diagnostica. Impostare il valore della proprietà `ActivityRuns`su. | `ActivityRuns` |
|**level**| Stringa | Livello dei log di diagnostica. Impostare il valore della proprietà `Informational`su. | `Informational` |
|**operationName**| Stringa | Nome dell'attività con lo stato. Se l'attività è l'heartbeat iniziale, il valore della proprietà `MyActivity -`è. Se l'attività è l'heartbeat finale, il valore della proprietà `MyActivity - Succeeded`è. | `MyActivity - Succeeded` |
|**pipelineName**| Stringa | Nome della pipeline. | `MyPipeline` |
|**activityName**| Stringa | Nome dell'attività. | `MyActivity` |
|**start**| Stringa | L'ora di inizio dell'attività viene eseguita nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**end**| Stringa | L'ora di fine dell'attività viene eseguita nel formato UTC di TimeSpan. Se il log di diagnostica Mostra che un'attività è stata avviata ma non ancora terminata, `1601-01-01T00:00:00Z`il valore della proprietà è. | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Pipeline-eseguire gli attributi di log

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
| **Level** |Stringa | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |Stringa | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Stringa | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC di TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**runId**| Stringa| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| Stringa | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Stringa | Categoria dei log di diagnostica. Impostare il valore della proprietà `PipelineRuns`su. | `PipelineRuns` |
|**level**| Stringa | Livello dei log di diagnostica. Impostare il valore della proprietà `Informational`su. | `Informational` |
|**operationName**| Stringa | Nome della pipeline insieme al relativo stato. Al termine dell'esecuzione della pipeline, il valore della proprietà `Pipeline - Succeeded`è. | `MyPipeline - Succeeded` (Indici per tabelle con ottimizzazione per la memoria). |
|**pipelineName**| Stringa | Nome della pipeline. | `MyPipeline` |
|**start**| Stringa | L'ora di inizio dell'attività viene eseguita nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z` (Indici per tabelle con ottimizzazione per la memoria). |
|**end**| Stringa | L'ora di fine dell'attività viene eseguita nel formato UTC di TimeSpan. Se il log di diagnostica Mostra che un'attività è stata avviata ma non ancora terminata `1601-01-01T00:00:00Z`, il valore della proprietà è.  | `2017-06-26T20:55:29.5007959Z` |
|**status**| Stringa | Stato finale dell'esecuzione della pipeline. I possibili valori di `Succeeded` proprietà `Failed`sono e. | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger-eseguire gli attributi del log

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
| **Level** |Stringa | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |Stringa | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | Stringa | Ora dell'evento nel formato `YYYY-MM-DDTHH:MM:SS.00000Z`UTC di TimeSpan. | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| Stringa| ID dell'esecuzione del trigger. | `08587023010602533858661257311` |
|**resourceId**| Stringa | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| Stringa | Categoria dei log di diagnostica. Impostare il valore della proprietà `PipelineRuns`su. | `PipelineRuns` |
|**level**| Stringa | Livello dei log di diagnostica. Impostare il valore della proprietà `Informational`su. | `Informational` |
|**operationName**| Stringa | Nome del trigger con lo stato finale, che indica se il trigger è stato attivato correttamente. Se l'heartbeat ha avuto esito positivo, il `MyTrigger - Succeeded`valore della proprietà è. | `MyTrigger - Succeeded` |
|**triggerName**| Stringa | Nome del trigger. | `MyTrigger` |
|**triggerType**| Stringa | Tipo di trigger. I possibili valori di `Manual Trigger` proprietà `Schedule Trigger`sono e. | `ScheduleTrigger` |
|**triggerEvent**| Stringa | Evento del trigger. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| Stringa | Ora di inizio dell'attivazione del trigger nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**status**| Stringa | Stato finale che indica se il trigger è stato attivato correttamente. I possibili valori di `Succeeded` proprietà `Failed`sono e. | `Succeeded`|

### <a name="log-analytics-schema"></a>Schema Log Analytics

Log Analytics eredita lo schema da monitoraggio con le eccezioni seguenti:

* La prima lettera del nome di ogni colonna è maiuscola. Il nome di colonna "correlationId" in monitor, ad esempio, è "CorrelationId" in Log Analytics.
* Non esiste alcuna colonna "Level".
* La colonna dinamica "Properties" viene mantenuta come il seguente tipo di BLOB JSON dinamici.

    | Colonna monitoraggio di Azure | Log Analytics colonna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamic |
    | $. Properties. Annotazioni | Annotazioni | Dynamic |
    | $. Properties. Input | Input | Dynamic |
    | $. Properties. Output | Output | Dynamic |
    | $. Properties. Errore. errorCode | ErrorCode | int |
    | $. Properties. Errore. messaggio | MessaggioErrore | string |
    | $. Properties. Errore | Errore | Dynamic |
    | $. Properties. Predecessori | Predecessori | Dynamic |
    | $. Properties. Parametri | Parametri | Dynamic |
    | $. Properties. SystemParameters | SystemParameters | Dynamic |
    | $. Properties. Tag | Tag | Dynamic |
    
## <a name="metrics"></a>metrics

Con il monitoraggio è possibile ottenere visibilità sulle prestazioni e l'integrità dei carichi di lavoro di Azure. Il tipo più importante di dati di monitoraggio è la metrica, definita anche contatore delle prestazioni. Le metriche vengono emesse dalla maggior parte delle risorse di Azure. Il monitoraggio offre diversi modi per configurare e utilizzare queste metriche per il monitoraggio e la risoluzione dei problemi.

Azure Data Factory versione 2 genera le metriche seguenti.

| **Metrica**           | **Nome visualizzato metrica**         | **Unità** | **Tipo di aggregazione** | **Descrizione**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite) | Count    | Totale                | Numero totale di esecuzioni di pipeline completate in un intervallo di minuti. |
| PipelineFailedRuns   | Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)    | Count    | Totale                | Numero totale di esecuzioni di pipeline non riuscite in un intervallo di minuti.    |
| ActivitySucceededRuns | Succeeded activity runs metrics (Metrica esecuzioni attività riuscite) | Count    | Totale                | Numero totale di esecuzioni di attività riuscite in un intervallo di minuti.  |
| ActivityFailedRuns   | Failed activity runs metrics (Metrica esecuzioni attività non riuscite)    | Count    | Totale                | Numero totale di esecuzioni di attività non riuscite in un intervallo di minuti.     |
| TriggerSucceededRuns | Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)  | Count    | Totale                | Numero totale di esecuzioni di trigger riuscite in un intervallo di minuti.   |
| TriggerFailedRuns    | Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)     | Count    | Totale                | Numero totale di esecuzioni di trigger non riuscite in un intervallo di minuti.      |

Per accedere alle metriche, seguire le istruzioni riportate nella [piattaforma dati di monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitorare Data Factory metriche con monitoraggio di Azure

È possibile utilizzare Data Factory integrazione con monitoraggio per instradare i dati da monitorare. Questa integrazione è utile negli scenari seguenti:

* Si desidera scrivere query complesse su un set completo di metriche pubblicate da Data Factory per il monitoraggio. È possibile creare avvisi personalizzati per queste query tramite monitoraggio.

* Per eseguire il monitoraggio nelle data factory È possibile indirizzare i dati da più data factory a una singola area di lavoro di monitoraggio.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configurare le impostazioni di diagnostica e l'area di lavoro

Creare o aggiungere impostazioni di diagnostica per il data factory.

1. Nel portale passare a monitoraggio. Selezionare **Impostazioni** > **impostazioni di diagnostica**.

1. Selezionare il data factory per cui si desidera impostare un'impostazione di diagnostica.

1. Se nella data factory selezionata non sono presenti impostazioni, viene richiesto di creare un'impostazione. Selezionare **Attiva diagnostica**.

   ![Crea un'impostazione di diagnostica se non esistono impostazioni](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se nel data factory sono presenti impostazioni esistenti, viene visualizzato un elenco di impostazioni già configurate nel data factory. Selezionare **Aggiungi impostazioni di diagnostica**.

   ![Aggiungere un'impostazione di diagnostica se esistono impostazioni](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Assegnare un nome all'impostazione, selezionare **Invia a log Analytics**e quindi selezionare un'area di lavoro da **log Analytics area di lavoro**.

    ![Denominare le impostazioni e selezionare un'area di lavoro log-Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. Selezionare **Salva**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa data factory. I log di diagnostica vengono trasmessi a tale area di lavoro non appena vengono generati nuovi dati degli eventi. Un massimo di 15 minuti può trascorrere tra il momento in cui viene generato un evento e il momento in cui viene visualizzato in Log Analytics.

* In modalità _specifica della risorsa_ , i log di diagnostica dal flusso Azure Data Factory nelle tabelle _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_
* In modalità _diagnostica di Azure_ , i log di diagnostica scorrono nella tabella _AzureDiagnostics_

> [!NOTE]
> Poiché una tabella dei log di Azure non può contenere più di 500 colonne, è consigliabile selezionare la modalità specifica della risorsa. Per ulteriori informazioni, vedere [log Analytics limitazioni note](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installare il pacchetto di Azure Data Factory Analytics dal Marketplace di Azure

![Passare a "Azure Marketplace", immettere "filtro Analytics" e selezionare "Azure Data Factory Analytics (anteprima")](media/data-factory-monitor-oms/monitor-oms-image3.png)

![Dettagli su "Azure Data Factory Analytics (anteprima)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

Selezionare **Crea** e quindi selezionare **area di lavoro OMS** e **Impostazioni area di lavoro OMS**.

![Creazione di una nuova soluzione](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorare Data Factory metriche

Con l'installazione di Azure Data Factory Analytics viene creato un set predefinito di visualizzazioni in modo che vengano abilitate le metriche seguenti:

- Esecuzioni di ADF-1) esecuzioni pipeline per Data Factory
 
- Esecuzioni di Azure Data Factory - 2) Esecuzioni di attività di Data Factory

- Esecuzioni di ADF-3) il trigger viene eseguito da Data Factory

- Errori di ADF-1) primi 10 errori della pipeline per Data Factory

- Errori di ADF-2) prime 10 esecuzioni attività per Data Factory

- Errori di ADF-3) primi 10 errori trigger per Data Factory

- Statistiche ADF-1) esecuzioni attività per tipo

- Statistiche ADF-2) esecuzioni trigger per tipo

- Statistiche ADF-3) durata massima esecuzioni pipeline

![Finestra con "cartelle di lavoro (anteprima)" e "AzureDataFactoryAnalytics" evidenziato](media/data-factory-monitor-oms/monitor-oms-image6.png)

È possibile visualizzare le metriche precedenti, esaminare le query dietro a queste metriche, modificare le query, creare avvisi e intraprendere altre azioni.

![Rappresentazione grafica delle esecuzioni di pipeline per data factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (anteprima) Invia i log di diagnostica alle tabelle di destinazione _specifiche delle risorse_ . È possibile scrivere query sulle tabelle seguenti: _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_.

## <a name="alerts"></a>Avvisi

Accedere al portale di Azure e selezionare **monitoraggio** > **avvisi** per creare avvisi.

![Avvisi nel menu del portale](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Crea avvisi

1. Selezionare **+ Nuova regola di avviso** per creare un nuovo avviso.

    ![Nuova regola di avviso](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definire la condizione di avviso.

    > [!NOTE]
    > Assicurarsi di selezionare **tutto** nell'elenco a discesa **Filtra per tipo di risorsa** .

    !["Definire la condizione di avviso" > "Seleziona destinazione", che consente di aprire il riquadro "selezionare una risorsa" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definire la condizione di avviso" > "Aggiungi criteri", che consente di aprire il riquadro "Configura logica del segnale"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Riquadro "Configura tipo di segnale"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definire i dettagli dell'avviso.

    ![Dettagli avviso](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definire il gruppo di azioni.

    ![Crea una regola con evidenziato il nuovo gruppo di azioni](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Creare un nuovo gruppo di azioni](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurare posta elettronica, SMS, push e Voice](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definire un gruppo di azione](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passaggi successivi
[Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md)
