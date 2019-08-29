---
title: Monitorare le data factory con Monitoraggio di Azure | Microsoft Docs
description: Informazioni su come usare Monitoraggio di Azure per monitorare le pipeline di Data Factory abilitando i log di diagnostica con informazioni da Azure Data Factory.
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
ms.openlocfilehash: 0614de8bbb1429c84bf5f2e55c1765f3e4863f3a
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141138"
---
# <a name="alert-and-monitor-data-factories-using-azure-monitor"></a>Avvisi e monitoraggio delle data factory con Monitoraggio di Azure
Le applicazioni cloud sono complesse e hanno molte parti mobili. Il monitoraggio offre la possibilità di garantire il funzionamento e l'integrità dell'applicazione. Consente anche di prevenire i problemi potenziali o di risolvere quelli precedenti. Inoltre, è possibile usare i dati di monitoraggio per ottenere informazioni approfondite sull'applicazione, utili per migliorarne le prestazioni o la manutenibilità oppure per automatizzare azioni che altrimenti richiederebbero un intervento manuale.

Attualmente Monitoraggio di Azure offre metriche e log dell'infrastruttura di livello base per la maggior parte dei servizi in Microsoft Azure, Per informazioni dettagliate, vedere [Panoramica di Monitoraggio](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor). I log di diagnostica di Azure sono log generati da una risorsa che forniscono dati completi e frequenti sul funzionamento di tale risorsa. Data Factory visualizza i log di diagnostica in Monitoraggio di Azure.

## <a name="persist-data-factory-data"></a>Rendere persistenti i dati di Data Factory
Data Factory memorizza i dati di esecuzione della pipeline solo per 45 giorni. Se si vogliono rendere persistenti i dati di esecuzione della pipeline per più di 45 giorni, tramite Monitoraggio di Azure, non è solo possibile inviare i log di diagnostica per l'analisi, ma è anche possibile renderli persistenti in un account di archiviazione in modo da disporre delle informazioni di Data Factory per l'intervallo di tempo desiderato.

## <a name="diagnostic-logs"></a>Log di diagnostica

* Salvarli in un **account di archiviazione** per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione in giorni usando le impostazioni di diagnostica.
* Trasmetterli a **Hub eventi** per l'inserimento da parte di un servizio di terze parti o una soluzione di analisi personalizzata come Power BI.
* Analizzarli con **Log Analytics**

È possibile usare un account di archiviazione o un hub eventi dello spazio dei nomi che non si trovi nella stessa sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre avere il controllo degli accessi in base al ruolo appropriato a entrambe le sottoscrizioni.

## <a name="set-up-diagnostic-logs"></a>Configurare i log di diagnostica

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica
I log di diagnostica per le risorse non di calcolo vengono configurati usando le impostazioni di diagnostica. Le impostazioni di diagnostica per una risorsa controllano:

* Dove vengono inviati i log di diagnostica (account di archiviazione, Hub eventi o log di monitoraggio di Azure).
* Categorie di log da inviare.
* Periodo di tempo durante il quale ogni categoria di log deve essere mantenuta nell'account di archiviazione.
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. Se impostato su zero giorni, i log vengono conservati all'infinito.
* Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, ad esempio se sono selezionate solo le opzioni Hub eventi o log di monitoraggio di Azure, i criteri di conservazione non hanno alcun effetto.
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
* Sostituire `{resource-id}` con l'ID della risorsa per cui si vogliono modificare le impostazioni di diagnostica. Per altre informazioni, vedere [Uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/manage-resource-groups-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione dell'autorizzazione su un token JSON Web ottenuto da Azure Active Directory. Per altre informazioni, vedere [Autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

**Corpo**
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

| Proprietà | Type | DESCRIZIONE |
| --- | --- | --- |
| storageAccountId |string | ID risorsa dell'account di archiviazione a cui si vogliono inviare i log di diagnostica |
| serviceBusRuleId |string | ID regola del bus di servizio dello spazio dei nomi del bus di servizio in cui creare gli hub eventi per il flusso dei log di diagnostica. Il formato dell'ID della regola è: "{ID risorsa bus di servizio}/authorizationrules/{nome chiave}".|
| workspaceId | Tipo complesso | Matrice di intervalli di tempo di metrica e relativi criteri di conservazione. Questa proprietà è attualmente vuota. |
|metrics| Valori di parametri della pipeline eseguita da passare alla pipeline richiamata| Oggetto JSON che esegue il mapping dei nomi di parametro ai valori degli argomenti |
| logs| Tipo complesso| Nome di una categoria di log di diagnostica per un tipo di risorsa. Per ottenere l'elenco di una categoria di log di diagnostica per una risorsa, eseguire prima un'operazione GET sulle impostazioni di diagnostica. |
| category| string| Matrice di categorie di log e relativi criteri di conservazione |
| timeGrain | string | Granularità delle metriche acquisite nel formato di durata ISO 8601. Deve essere PT1M (un minuto)|
| enabled| Boolean | Specifica se la raccolta di tale metrica o categoria di log è abilitata per questa risorsa|
| retentionPolicy| Tipo complesso| Descrive i criteri di conservazione per una metrica o categoria di log. Usata solo per l'opzione dell'account di archiviazione.|
| days| Int| Numero di giorni di conservazione delle metriche o dei log. Se il valore è 0, i log vengono conservati all'infinito. Usata solo per l'opzione dell'account di archiviazione. |

**Risposta**

200 - OK


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
[Altre informazioni sono disponibili qui](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)

## <a name="schema-of-logs--events"></a>Schema di log ed eventi

### <a name="azure-monitor-schema"></a>Schema di monitoraggio di Azure

#### <a name="activity-run-logs-attributes"></a>Attributi dei log di esecuzione attività

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

| Proprietà | Type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Il livello 4 è sempre valido per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| string| ID dell'esecuzione attività | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| string| ID dell'esecuzione pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "ActivityRuns" | `ActivityRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome dell'attività con lo stato. Se lo stato è l'heartbeat di inizio, è impostato su `MyActivity -`. Se lo stato è l'heartbeat di fine, è impostato su `MyActivity - Succeeded` con lo stato finale | `MyActivity - Succeeded` |
|pipelineName| string | Nome della pipeline | `MyPipeline` |
|activityName| string | Nome dell'attività | `MyActivity` |
|start| string | Inizio dell'esecuzione attività nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Fine dell'esecuzione attività nell'intervallo di tempo, in formato UTC. Se l'attività non è ancora terminata (log di diagnostica per l'avvio di un'attività), viene impostato il valore predefinito `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-logs-attributes"></a>Attributi dei log di esecuzione pipeline

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

| Proprietà | Type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Il livello 4 è valido per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| string| ID dell'esecuzione pipeline | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "PipelineRuns" | `PipelineRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome della pipeline con lo stato. "Pipeline - Succeeded" con lo stato finale indicante quando l'esecuzione pipeline viene completata| `MyPipeline - Succeeded` |
|pipelineName| string | Nome della pipeline | `MyPipeline` |
|start| string | Inizio dell'esecuzione attività nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|end| string | Fine delle esecuzioni attività nell'intervallo di tempo, in formato UTC. Se l'attività non è ancora terminata (log di diagnostica per l'avvio di un'attività), viene impostato il valore predefinito `1601-01-01T00:00:00Z`.  | `2017-06-26T20:55:29.5007959Z` |
|status| string | Stato finale dell'esecuzione pipeline (Succeeded o Failed) | `Succeeded`|

#### <a name="trigger-run-logs-attributes"></a>Attributi dei log di esecuzione trigger

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

| Proprietà | Type | DESCRIZIONE | Esempio |
| --- | --- | --- | --- |
| Level |string | Livello dei log di diagnostica. Impostare sul livello 4 per i log di esecuzione attività. | `4`  |
| correlationId |string | ID univoco per tenere traccia di una particolare richiesta end-to-end | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | string | Ora dell'evento nell'intervallo di tempo, formato UTC`YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| string| ID dell'esecuzione del trigger | `08587023010602533858661257311` |
|resourceId| string | ID della risorsa associata di Data Factory | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| string | Categoria dei log di diagnostica. Impostare questa proprietà su "PipelineRuns" | `PipelineRuns` |
|level| string | Livello dei log di diagnostica. Impostare questa proprietà su "Informational" | `Informational` |
|operationName| string |Nome del trigger con lo stato finale indicante se è stato attivato correttamente. "MyTrigger - Succeeded" se l'heartbeat è riuscito| `MyTrigger - Succeeded` |
|triggerName| string | Nome del trigger | `MyTrigger` |
|triggerType| string | Tipo del trigger (trigger manuale o trigger di pianificazione) | `ScheduleTrigger` |
|triggerEvent| string | Evento del trigger | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| string | Avvio dell'attivazione del trigger nell'intervallo di tempo, in formato UTC | `2017-06-26T20:55:29.5007959Z`|
|status| string | Stato finale indicante se il trigger è stato attivato correttamente (Succeeded o Failed) | `Succeeded`|

### <a name="log-analytics-schema"></a>Schema Log Analytics

Log Analytics eredita lo schema da monitoraggio di Azure con le eccezioni seguenti:

* La prima lettera del nome di ogni colonna verrà in maiuscolo, ad esempio *CorrelationId* in monitoraggio di Azure sarà *CorrelationId* in log Analytics.
* Il *livello* della colonna verrà eliminato.
* Le *Proprietà* della colonna dinamica verranno mantenute come il tipo di BLOB JSON dinamico seguente:

    | Colonna monitoraggio di Azure | Log Analytics colonna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamic |
    | $. Properties. Annotazioni | annotazioni | Dynamic |
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

Il monitoraggio di Azure consente di usare la telemetria per ottenere visibilità sulle prestazioni e sull'integrità dei carichi di lavoro in Azure. Il tipo di dati di telemetria Azure più importante è rappresentato dalle metriche (altrimenti dette contatori delle prestazioni) generate dalla maggior parte delle risorse di Azure. Il monitoraggio di Azure offre alcuni modi per configurare e usare queste metriche per il monitoraggio e la risoluzione dei problemi.

ADFV2 emette le metriche seguenti:

| **Metrica**           | **Nome visualizzato metrica**         | **Unità** | **Tipo di aggregazione** | **Descrizione**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite) | Conteggio    | Totale                | Esecuzioni pipeline totali riuscite in un minuto |
| PipelineFailedRuns   | Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)    | Conteggio    | Totale                | Esecuzioni pipeline totali non riuscite in un minuto    |
| ActivitySucceededRuns | Succeeded activity runs metrics (Metrica esecuzioni attività riuscite) | Conteggio    | Totale                | Esecuzioni attività totali riuscite in un minuto  |
| ActivityFailedRuns   | Failed activity runs metrics (Metrica esecuzioni attività non riuscite)    | Conteggio    | Totale                | Esecuzioni attività totali non riuscite in un minuto     |
| TriggerSucceededRuns | Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)  | Conteggio    | Totale                | Esecuzioni trigger totali riuscite in un minuto   |
| TriggerFailedRuns    | Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)     | Conteggio    | Totale                | Esecuzioni trigger totali non riuscite in un minuto      |

Per accedere alle metriche, seguire le istruzioni riportate nella [piattaforma dati di monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>Monitoraggio delle metriche di Data Factory con Monitoraggio di Azure

È possibile usare l'integrazione di Azure Data Factory con Monitoraggio di Azure per indirizzare i dati a Monitoraggio di Azure. Questa integrazione è utile negli scenari seguenti:

1.  Per scrivere query complesse in un set completo di metriche pubblicate da Data Factory in Monitoraggio di Azure e per creare avvisi personalizzati per queste query tramite Monitoraggio di Azure.

2.  Per eseguire il monitoraggio nelle data factory e indirizzare i dati da più data factory a una singola area di lavoro di Monitoraggio di Azure.

Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>Configurare le impostazioni di diagnostica e l'area di lavoro

Abilitare le impostazioni di diagnostica per una data factory.

1. Nel portale passare a monitoraggio di Azure e fare clic su **impostazioni di diagnostica** nel menu **Impostazioni** .

2. Selezionare il data factory per cui si desidera impostare un'impostazione di diagnostica.
    
3. Se nella data factory selezionata non sono presenti impostazioni, viene richiesto di creare un'impostazione. Fare clic su "Attiva diagnostica".

   ![Aggiungi impostazione di diagnostica - Nessuna impostazione esistente](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se nel data factory sono presenti impostazioni esistenti, verrà visualizzato un elenco di impostazioni già configurate in questa data factory. Fare clic su "Add diagnostic setting" (Aggiungi impostazione di diagnostica).

   !["Add diagnostic setting" (Aggiungi impostazione di diagnostica) - impostazioni esistenti](media/data-factory-monitor-oms/add-diagnostic-setting.png)

4. Assegnare all'impostazione un nome e selezionare la casella **Invia a Log Analytics**, quindi selezionare un'area di lavoro Log Analytics.

    ![monitor-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

5. Fare clic su **Save**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa data factory e i log di diagnostica vengono trasmessi a tale area di lavoro non appena vengono generati nuovi dati degli eventi. Potrebbero essere necessari fino a 15 minuti tra il momento in cui viene generato un evento e il momento in cui viene visualizzato in Log Analytics.

> [!NOTE]
> A causa di un limite esplicito di una tabella dei log di Azure specifica che non contiene più di 500 colonne, **è consigliabile usare la modalità specifica della risorsa**. Per ulteriori informazioni, vedere [log Analytics limitazioni note](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics).

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>Installare il pacchetto di Azure Data Factory Analytics dal Marketplace di Azure

![monitor-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![monitor-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Fare clic su **Crea** e selezionare l'area di lavoro e le relative impostazioni.

![monitor-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>Monitoraggio delle metriche di Data Factory

L'installazione di **Azure Data Factory Analytics** crea un set predefinito di visualizzazioni che abilita le metriche seguenti:

- Esecuzioni di Azure Data Factory - 1) Esecuzioni di pipeline di Data Factory

- Esecuzioni di Azure Data Factory - 2) Esecuzioni di attività di Data Factory

- Esecuzioni di Azure Data Factory - 3) Esecuzioni di trigger di Data Factory

- Errori di Azure Data Factory - 1) Primi 10 errori di pipeline di Data Factory

- Errori di Azure Data Factory - 2) Prime 10 esecuzioni di attività di pipeline di Data Factory

- Errori di Azure Data Factory - 3) Primi 10 errori di trigger di Data Factory

- Statistiche di Azure Data Factory - 1) Esecuzioni di attività per tipo

- Statistiche di Azure Data Factory - 2) Esecuzioni di trigger per tipo

- Statistiche di Azure Data Factory - 3) Durata massima esecuzioni di pipeline

![monitor-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![monitor-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

È possibile visualizzare tutte le metriche indicate sopra, esaminare le query alla base di queste metriche, modificare le query, creare avvisi e così via.

![monitor-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="alerts"></a>Avvisi

Accedere al portale di Azure e fare clic su **monitoraggio** > **avvisi** per creare avvisi.

![Avvisi nel menu del portale](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Creare avvisi

1.  Fare clic su **+ Nuova regola di avviso** per creare un nuovo avviso.

    ![Nuova regola di avviso](media/monitor-using-azure-monitor/alerts_image4.png)

2.  Definire la **condizione di avviso**.

    > [!NOTE]
    > Selezionare **Tutto** in **Filtra per tipo di risorsa**.

    ![Condizione di avviso, schermata 1 di 3](media/monitor-using-azure-monitor/alerts_image5.png)

    ![Condizione di avviso, schermata 2 di 3](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Condizione di avviso, schermata 3 di 3](media/monitor-using-azure-monitor/alerts_image7.png)

3.  Definire i **dettagli dell'avviso**.

    ![Dettagli dell'avviso](media/monitor-using-azure-monitor/alerts_image8.png)

4.  Definire il **gruppo di azioni**.

    ![Gruppo di azioni, schermata 1 di 4](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Gruppo di azioni, schermata 2 di 4](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Gruppo di azioni, schermata 3 di 4](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Gruppo di azioni, schermata 4 di 4](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul monitoraggio e sulla gestione delle pipeline, vedere l'articolo [Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md).
