---
title: 'Esercitazione: Inserire dati di monitoraggio in Esplora dati di Azure senza una riga di codice'
description: Questa esercitazione illustra come inserire dati di monitoraggio in Esplora dati di Azure senza una riga di codice e come eseguire query su tali dati.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: tutorial
ms.date: 11/17/2019
ms.openlocfilehash: 97faa445a286574aa5fc05d084d21c0740bc8a8b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173864"
---
# <a name="tutorial-ingest-and-query-monitoring-data-in-azure-data-explorer"></a>Esercitazione: Inserire ed eseguire query sui dati di monitoraggio in Esplora dati di Azure 

Questa esercitazione illustra come inserire i dati dei log di diagnostica e attività in un cluster di Esplora dati di Azure senza scrivere codice. Questo semplice metodo di inserimento consente di iniziare rapidamente a eseguire query in Esplora dati di Azure per l'analisi dei dati.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare tabelle e mapping di inserimento in un database di Esplora dati di Azure.
> * Formattare i dati inseriti usando un criterio di aggiornamento.
> * Creare un [hub eventi](/azure/event-hubs/event-hubs-about) e connetterlo a Esplora dati di Azure.
> * Trasmettere i dati in streaming a un hub eventi dai [log e le metriche di diagnostica](/azure/azure-monitor/platform/diagnostic-settings) e dai [log attività](/azure/azure-monitor/platform/activity-logs-overview) di Monitoraggio di Azure.
> * Eseguire query sui dati inseriti usando Esplora dati di Azure.

> [!NOTE]
> Creare tutte le risorse nella stessa località o area di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md). In questa esercitazione il nome del database è *TestDatabase*.

## <a name="azure-monitor-data-provider-diagnostic-metrics-and-logs-and-activity-logs"></a>Provider di dati di Monitoraggio di Azure: log e metriche di diagnostica e log attività

Visualizzare e interpretare i dati forniti dai log e dalle metriche di diagnostica e dai log attività seguenti di Monitoraggio di Azure. Verrà creata una pipeline di inserimento in base a questi schemi di dati. Si noti che ogni evento in un log ha una matrice di record. Questa matrice di record verrà suddivisa più avanti nell'esercitazione.

### <a name="examples-of-diagnostic-metrics-and-logs-and-activity-logs"></a>Esempi di metriche e log di diagnostica e di log attività

Le metriche e i log di diagnostica e i log attività di Azure vengono emessi da un servizio di Azure e forniscono dati sul funzionamento del servizio stesso. 

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Metriche di diagnostica](#tab/diagnostic-metrics)
#### <a name="example"></a>Esempio

Le metriche di diagnostica vengono aggregate con un intervallo di tempo di 1 minuto. Di seguito è riportato un esempio di schema di evento di metrica di Esplora dati di Azure sulla durata di una query:

```json
{
    "records": [
    {
        "count": 14,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-20T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    },
    {
        "count": 12,
        "total": 0,
        "minimum": 0,
        "maximum": 0,
        "average": 0,
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
        "time": "2018-12-21T17:00:00.0000000Z",
        "metricName": "QueryDuration",
        "timeGrain": "PT1M"
    }
    ]
}
```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Log di diagnostica](#tab/diagnostic-logs)
#### <a name="example"></a>Esempio

Di seguito è riportato un esempio di [log di inserimento diagnostica](using-diagnostic-logs.md#diagnostic-logs-schema) di Esplora dati di Azure:

```json
{
    "time": "2019-08-26T13:22:36.8804326Z",
    "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/<cluster-name>",
    "operationName": "MICROSOFT.KUSTO/CLUSTERS/INGEST/ACTION",
    "operationVersion": "1.0",
    "category": "FailedIngestion",
    "resultType": "Failed",
    "correlationId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
    "properties": {
        "OperationId": "00000000-0000-0000-0000-000000000000",
        "Database": "Kusto",
        "Table": "Table_13_20_prod",
        "FailedOn": "2019-08-26T13:22:36.8804326Z",
        "IngestionSourceId": "d59882f1-ad64-4fc4-b2ef-d663b6cc1cc5",
        "Details":
        {
            "error": 
            {
                "code": "BadRequest_DatabaseNotExist",
                "message": "Request is invalid and cannot be executed.",
                "@type": "Kusto.Data.Exceptions.DatabaseNotFoundException",
                "@message": "Database 'Kusto' was not found.",
                "@context": 
                {
                    "timestamp": "2019-08-26T13:22:36.7179157Z",
                    "serviceAlias": "<cluster-name>",
                    "machineName": "KEngine000001",
                    "processName": "Kusto.WinSvc.Svc",
                    "processId": 5336,
                    "threadId": 6528,
                    "appDomainName": "Kusto.WinSvc.Svc.exe",
                    "clientRequestd": "DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8",
                    "activityId": "f13e7718-1153-4e65-bf82-8583d712976f",
                    "subActivityId": "2cdad9d0-737b-4c69-ac9a-22cf9af0c41b",
                    "activityType": "DN.AdminCommand.DataIngestPullCommand",
                    "parentActivityId": "2f65e533-a364-44dd-8d45-d97460fb5795",
                    "activityStack": "(Activity stack: CRID=DM.IngestionExecutor;a70ddfdc-b471-4fc7-beac-bb0f6e569fe8 ARID=f13e7718-1153-4e65-bf82-8583d712976f > DN.Admin.Client.ExecuteControlCommand/5b764b32-6017-44a2-89e7-860eda515d40 > P.WCF.Service.ExecuteControlCommandInternal..IAdminClientServiceCommunicationContract/c2ef9344-069d-44c4-88b1-a3570697ec77 > DN.FE.ExecuteControlCommand/2f65e533-a364-44dd-8d45-d97460fb5795 > DN.AdminCommand.DataIngestPullCommand/2cdad9d0-737b-4c69-ac9a-22cf9af0c41b)"
                },
                "@permanent": true
            }
        },
        "ErrorCode": "BadRequest_DatabaseNotExist",
        "FailureStatus": "Permanent",
        "RootActivityId": "00000000-0000-0000-0000-000000000000",
        "OriginatesFromUpdatePolicy": false,
        "ShouldRetry": false,
        "IngestionSourcePath": "https://c0skstrldkereneus01.blob.core.windows.net/aam-20190826-temp-e5c334ee145d4b43a3a2d3a96fbac1df/3216_test_3_columns_invalid_8f57f0d161ed4a8c903c6d1073005732_59951f9ca5d143b6bdefe52fa381a8ca.zip"
    }
}
```
# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
#### <a name="example"></a>Esempio

I log attività di Azure sono log a livello di sottoscrizione che forniscono informazioni sulle operazioni eseguite sulle risorse nella sottoscrizione. Di seguito è riportato un esempio di evento di log attività per il controllo dell'accesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Start",
        "resultSignature": "Started.",
        "durationMs": 0,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            ...
        }
    },
    {
        "time": "2018-12-26T16:23:06.3040244Z",
        "resourceId": "/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resource-group>/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
        "operationName": "MICROSOFT.AUTHORIZATION/CHECKACCESS/ACTION",
        "category": "Action",
        "resultType": "Success",
        "resultSignature": "Succeeded.OK",
        "durationMs": 194,
        "callerIpAddress": "13.66.225.188",
        "correlationId": "0de9f4bc-4adc-4209-a774-1b4f4ae573ed",
        "identity": {
            "authorization": {
                ...
            },
            "claims": {
                ...
            }
        },
        "level": "Information",
        "location": "global",
        "properties": {
            "statusCode": "OK",
            "serviceRequestId": "87acdebc-945f-4c0c-b931-03050e085626"
        }
    }]
}
```
---

## <a name="set-up-an-ingestion-pipeline-in-azure-data-explorer"></a>Configurare una pipeline di inserimento in Esplora dati di Azure

La configurazione di una pipeline di Esplora dati di Azure prevede diversi passaggi come [la creazione di una tabella e l'inserimento dei dati](/azure/data-explorer/ingest-sample-data#ingest-data). È anche possibile manipolare, mappare e aggiornare i dati.

### <a name="connect-to-the-azure-data-explorer-web-ui"></a>Connettersi all'interfaccia utente Web di Esplora dati di Azure

Nel database *TestDatabase* di Esplora dati di Azure selezionare **Query** per aprire l'interfaccia utente Web di Esplora dati di Azure.

![Pagina Query](media/ingest-data-no-code/query-database.png)

### <a name="create-the-target-tables"></a>Creare le tabelle di destinazione

La struttura dei log di Monitoraggio di Azure non è tabellare. Si manipoleranno i dati e si espanderà ogni evento per uno o più record. I dati non elaborati verranno inseriti in una tabella intermedia denominata *ActivityLogsRawRecords* per i log attività e *DiagnosticRawRecords* per le metriche e i log di diagnostica. A quel punto, i dati verranno manipolati ed espansi. Usando un criterio di aggiornamento, i dati espansi verranno inseriti nella tabella *ActivityLogs* per i log attività e *DiagnosticMetrics* per le metriche di diagnostica e *DiagnosticLogs* per i log di diagnostica. Ciò significa che sarà necessario creare due tabelle separate per l'inserimento dei log attività e tre tabelle separate per l'importazione delle metriche e dei log di diagnostica.

Usare l'interfaccia utente Web di Esplora dati di Azure per creare le tabelle di destinazione nel database di Esplora dati di Azure.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Metriche di diagnostica](#tab/diagnostic-metrics)
#### <a name="create-tables-for-the-diagnostic-metrics"></a>Creare tabelle per le metriche di diagnostica

1. Nel database *TestDatabase* creare una tabella denominata *DiagnosticMetrics* in cui archiviare i record delle metriche di diagnostica. Usare il comando di controllo seguente `.create table`:

    ```kusto
    .create table DiagnosticMetrics (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selezionare **Run** (Esegui) per creare la tabella.

    ![Esegui query](media/ingest-data-no-code/run-query.png)

1. Creare la tabella dati intermedia denominata *DiagnosticRawRecords* nel database *TestDatabase* per la manipolazione dei dati usando la query seguente. Selezionare **Run** (Esegui) per creare la tabella.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Impostare zero [criteri di conservazione](/azure/kusto/management/retention-policy) per la tabella intermedia:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Log di diagnostica](#tab/diagnostic-logs)
#### <a name="create-tables-for-the-diagnostic-logs"></a>Creare tabelle per i log di diagnostica 

1. Nel database *TestDatabase* creare una tabella denominata *DiagnosticLogs* in cui archiviare i record di log di diagnostica. Usare il comando di controllo seguente `.create table`:

    ```kusto
    .create table DiagnosticLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Result:string, OperationId:string, Database:string, Table:string, IngestionSourceId:string, IngestionSourcePath:string, RootActivityId:string, ErrorCode:string, FailureStatus:string, Details:string)
    ```

1. Selezionare **Run** (Esegui) per creare la tabella.

1. Creare la tabella dati intermedia denominata *DiagnosticRawRecords* nel database *TestDatabase* per la manipolazione dei dati usando la query seguente. Selezionare **Run** (Esegui) per creare la tabella.

    ```kusto
    .create table DiagnosticRawRecords (Records:dynamic)
    ```

1. Impostare zero [criteri di conservazione](/azure/kusto/management/retention-policy) per la tabella intermedia:

    ```kusto
    .alter-merge table DiagnosticRawRecords policy retention softdelete = 0d
    ```

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
#### <a name="create-tables-for-the-activity-logs"></a>Creare tabelle per i log attività 

1. Creare una tabella denominata *ActivityLogs* nel database *TestDatabase* in cui ricevere i record dei log attività. Per creare la tabella, eseguire la query di Esplora dati di Azure seguente:

    ```kusto
    .create table ActivityLogs (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Creare la tabella dati intermedia denominata *ActivityLogsRawRecords* nel database *TestDatabase* per la manipolazione dei dati:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

1. Impostare zero [criteri di conservazione](/azure/kusto/management/retention-policy) per la tabella intermedia:

    ```kusto
    .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    ```
---

### <a name="create-table-mappings"></a>Creare i mapping della tabella

 Poiché il formato dei dati è `json`, è necessario eseguirne il mapping. Il mapping `json` mappa ogni percorso JSON al nome di una colonna della tabella.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Metriche di diagnostica/log di diagnostica](#tab/diagnostic-metrics+diagnostic-logs) 
#### <a name="map-diagnostic-metrics-and-logs-to-the-table"></a>Eseguire il mapping delle metriche e dei log di diagnostica alla tabella

Per eseguire il mapping dei dati delle metriche e dei log di diagnostica alla tabella, usare la query seguente:

```kusto
.create table DiagnosticRawRecords ingestion json mapping 'DiagnosticRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
#### <a name="map-activity-logs-to-the-table"></a>Eseguire il mapping dei log attività alla tabella

Per eseguire il mapping dei dati dei log attività alla tabella, usare la query seguente:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```
---

### <a name="create-the-update-policy-for-metric-and-log-data"></a>Creare e aggiornare i criteri per i dati di metriche e log

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Metriche di diagnostica](#tab/diagnostic-metrics)
#### <a name="create-data-update-policy-for-diagnostics-metrics"></a>Creare criteri di aggiornamento dei dati per le metriche di diagnostica

1. Creare una [funzione](/azure/kusto/management/functions) che espande la raccolta di record delle metriche di diagnostica in modo che ogni valore della raccolta riceva una riga distinta. Usare l'operatore [`mv-expand`](/azure/kusto/query/mvexpandoperator):
     ```kusto
    .create function DiagnosticMetricsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.metricName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            MetricName = tostring(events.metricName),
            Count = toint(events.count),
            Total = todouble(events.total),
            Minimum = todouble(events.minimum),
            Maximum = todouble(events.maximum),
            Average = todouble(events.average),
            TimeGrain = tostring(events.timeGrain)
    }
    ```

2. Aggiungere il [criterio di aggiornamento](/azure/kusto/concepts/updatepolicy) nella tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti i nuovi dati inseriti nella tabella dati intermedia *DiagnosticRawRecords* e inserirà i relativi risultati nella tabella *DiagnosticMetrics*:

    ```kusto
    .alter table DiagnosticMetrics policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticMetricsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Log di diagnostica](#tab/diagnostic-logs)
#### <a name="create-data-update-policy-for-diagnostics-logs"></a>Creare criteri di aggiornamento dei dati per i log di diagnostica

1. Creare una [funzione](/azure/kusto/management/functions) che espande la raccolta di record del log di diagnostica in modo che ogni valore della raccolta riceva una riga distinta. Abilitare i log di inserimento in un cluster di Esplora dati di Azure e usare lo [schema dei log di inserimento](/azure/data-explorer/using-diagnostic-logs#diagnostic-logs-schema). Verrà creata una tabella per l'inserimento riuscito e non riuscito, mentre alcuni campi saranno vuoti per l'inserimento riuscito (ad esempio, ErrorCode). Usare l'operatore [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function DiagnosticLogsExpand() {
        DiagnosticRawRecords
        | mv-expand events = Records
        | where isnotempty(events.operationName)
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Result = tostring(events.resultType),
            OperationId = tostring(events.properties.OperationId),
            Database = tostring(events.properties.Database),
            Table = tostring(events.properties.Table),
            IngestionSourceId = tostring(events.properties.IngestionSourceId),
            IngestionSourcePath = tostring(events.properties.IngestionSourcePath),
            RootActivityId = tostring(events.properties.RootActivityId),
            ErrorCode = tostring(events.properties.ErrorCode),
            FailureStatus = tostring(events.properties.FailureStatus),
            Details = tostring(events.properties.Details)
    }
    ```

2. Aggiungere il [criterio di aggiornamento](/azure/kusto/concepts/updatepolicy) nella tabella di destinazione. Questo criterio eseguirà automaticamente la query su tutti i nuovi dati inseriti nella tabella dati intermedia *DiagnosticRawRecords* e inserirà i relativi risultati nella tabella *DiagnosticLogs*:

    ```kusto
    .alter table DiagnosticLogs policy update @'[{"Source": "DiagnosticRawRecords", "Query": "DiagnosticLogsExpand()", "IsEnabled": "True"}]'
    ```

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
#### <a name="create-data-update-policy-for-activity-logs"></a>Creare i criteri di aggiornamento per i dati dei log attività

1. Creare una [funzione](/azure/kusto/management/functions) che espande la raccolta di record del log attività in modo che ogni valore della raccolta riceva una riga distinta. Usare l'operatore [`mv-expand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mv-expand events = Records
        | project
            Timestamp = todatetime(events.time),
            ResourceId = tostring(events.resourceId),
            OperationName = tostring(events.operationName),
            Category = tostring(events.category),
            ResultType = tostring(events.resultType),
            ResultSignature = tostring(events.resultSignature),
            DurationMs = toint(events.durationMs),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events.location),
            Level = tostring(events.level)
    }
    ```

2. Aggiungere il [criterio di aggiornamento](/azure/kusto/concepts/updatepolicy) nella tabella di destinazione. Questo criterio eseguirà automaticamente la query sui nuovi dati inseriti nella tabella dati intermedia *ActivityLogsRawRecords* e inserirà i relativi risultati nella tabella *ActivityLogs*:

    ```kusto
    .alter table ActivityLogs policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```
---

## <a name="create-an-azure-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi di Azure

Le impostazioni di diagnostica di Azure consentono l'esportazione delle metriche e dei log in un account di archiviazione o in un hub eventi. In questa esercitazione le metriche e i log verranno instradati tramite un hub eventi. Con i passaggi seguenti verrà creato uno spazio dei nomi di Hub eventi e un hub eventi per le metriche e i log di diagnostica. Monitoraggio di Azure creerà il componente *insights-operational-logs* dell'hub eventi per i log attività.

1. Creare un hub eventi usando un modello di Azure Resource Manager nel portale di Azure. Per seguire il resto dei passaggi di questo articolo, fare clic con il pulsante destro del mouse sul pulsante **Distribuisci in Azure** e quindi selezionare **Apri in una nuova finestra**. Il pulsante **Distribuzione in Azure** consente di passare al portale di Azure.

    [![Pulsante Distribuisci in Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Creare uno spazio dei nomi di hub eventi e un hub eventi per i log di diagnostica.

    ![Creazione dell'hub eventi](media/ingest-data-no-code/event-hub.png)

1. Compilare il modulo con le informazioni seguenti. Per tutte le impostazioni non elencate nella tabella seguente usare i valori predefiniti.

    **Impostazione** | **Valore consigliato** | **Descrizione**
    |---|---|---|
    | **Sottoscrizione** | *Sottoscrizione in uso* | Selezionare la sottoscrizione di Azure da usare per l'hub eventi.|
    | **Gruppo di risorse** | *test-resource-group* | Creare un nuovo gruppo di risorse. |
    | **Posizione** | Selezionare l'area più appropriata in base alle esigenze. | Creare lo spazio dei nomi di hub eventi nella stessa posizione delle altre risorse.
    | **Nome spazio dei nomi** | *AzureMonitoringData* | Scegliere un nome univoco per identificare lo spazio dei nomi.
    | **Nome hub eventi** | *DiagnosticData* | L'hub eventi si trova nello spazio dei nomi, che fornisce un contenitore di ambito univoco. |
    | **Nome gruppo di consumer** | *adxpipeline* | Creare un nome del gruppo di consumer. I gruppi di consumer consentono a più applicazioni di avere ognuna una visualizzazione distinta del flusso di eventi. |
    | | |

## <a name="connect-azure-monitor-metrics-and-logs-to-your-event-hub"></a>Connettere le metriche e i log di Monitoraggio di Azure all'hub eventi

A questo punto è necessario connettere le metriche e i log di diagnostica e i log attività all'hub eventi.

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Metriche di diagnostica/log di diagnostica](#tab/diagnostic-metrics+diagnostic-logs) 
### <a name="connect-diagnostic-metrics-and-logs-to-your-event-hub"></a>Connettere le metriche e i log di diagnostica all'hub eventi

Selezionare una risorsa da cui esportare le metriche. Esistono diversi tipi di risorse che consentono l'esportazione dei dati di diagnostica, tra cui lo spazio dei nomi di Hub eventi, Azure Key Vault, hub IoT di Azure e i cluster di Esplora dati di Azure. In questa esercitazione come risorsa verrà usato un cluster di Esplora dati di Azure e si esamineranno le metriche delle prestazioni delle query e i log dei risultati di inserimento.

1. Selezionare il cluster Kusto nel portale di Azure.
1. Selezionare **Impostazioni di diagnostica** e quindi selezionare il collegamento **Abilita diagnostica**. 

    ![Impostazioni di diagnostica](media/ingest-data-no-code/diagnostic-settings.png)

1. Viene visualizzato il riquadro **Impostazioni di diagnostica**. Eseguire questa procedura:
   1. Assegnare ai dati del log di diagnostica il nome *ADXExportedData*.
   1. In **LOG** selezionare le caselle di controllo **SucceededIngestion** e **FailedIngestion**.
   1. In **METRICA**selezionare la casella di controllo **Prestazioni query**.
   1. Selezionare la casella di controllo **Streaming in un hub eventi**.
   1. Selezionare **Configura**.

      ![Riquadro Impostazioni di diagnostica](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Nel riquadro **Selezionare l'hub eventi** configurare il modo in cui esportare i dati dai log di diagnostica nell'hub eventi creato:
    1. Nell'elenco **Selezionare lo spazio dei nomi dell'hub eventi** selezionare *AzureMonitoringData*.
    1. Nell'elenco **Selezionare il nome dell'hub eventi** selezionare *DiagnosticData*.
    1. Nell'elenco **Selezionare il nome dei criteri per l'hub eventi** selezionare **RootManagerSharedAccessKey**.
    1. Selezionare **OK**.

1. Selezionare **Salva**.

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
### <a name="connect-activity-logs-to-your-event-hub"></a>Collegare i log attività all'hub eventi

1. Scegliere **Log attività** dal menu a sinistra del portale di Azure.
1. Viene visualizzata la finestra **Log attività**. Selezionare **Esporta in Hub eventi**.

    ![Finestra Log attività](media/ingest-data-no-code/activity-log.png)

1. Viene visualizzata la finestra **Esporta log attività**:
 
    ![Finestra Esporta log attività](media/ingest-data-no-code/export-activity-log.png)

1. Nella finestra **Esporta log attività** seguire questa procedura:
      1. Selezionare la propria sottoscrizione.
      1. Nell'elenco **Aree** scegliere **Seleziona tutto**.
      1. Selezionare la casella di controllo **Esporta in un hub eventi**.
      1. Scegliere **Selezionare uno spazio dei nomi del bus di servizio** per aprire il riquadro **Selezionare l'hub eventi**.
      1. Nel riquadro **Selezionare l'hub eventi** selezionare la propria sottoscrizione.
      1. Nell'elenco **Selezionare lo spazio dei nomi dell'hub eventi** selezionare *AzureMonitoringData*.
      1. Nell'elenco **Selezionare il nome dei criteri per l'hub eventi** selezionare il nome dei criteri per l'hub eventi predefinito.
      1. Selezionare **OK**.
      1. Nell'angolo superiore sinistro della finestra selezionare **Salva**.
   Verrà creato un hub eventi denominato *insights-operational-logs*.
---

### <a name="see-data-flowing-to-your-event-hubs"></a>Esaminare il flusso di dati verso gli hub eventi

1. Attendere alcuni minuti finché non viene definita la connessione e non viene completata l'esportazione dei log attività nell'hub eventi. Passare allo spazio dei nomi di hub eventi per vedere gli hub eventi creati.

    ![Hub eventi creati](media/ingest-data-no-code/event-hubs-created.png)

1. Esaminare il flusso di dati verso l'hub eventi:

    ![Dati dell'hub eventi](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-an-event-hub-to-azure-data-explorer"></a>Connettere un hub eventi a Esplora dati di Azure

A questo punto è necessario creare le connessioni dati per le metriche e i log di diagnostica e i log attività.

### <a name="create-the-data-connection-for-diagnostic-metrics-and-logs-and-activity-logs"></a>Creare la connessione dati per le metriche e i log di diagnostica e i log attività

1. Nel cluster di Esplora dati di Azure denominato *kustodocs* scegliere **Database** dal menu a sinistra.
1. Nella finestra **Database** selezionare il database *TestDatabase*.
1. Scegliere **Inserimento dati** dal menu a sinistra.
1. Nella finestra **Inserimento dati** fare clic su **+ Aggiungi connessione dati**.
1. Nella finestra **Connessione dati** immettere le informazioni seguenti:

    ![Connessione dati dell'hub eventi](media/ingest-data-no-code/event-hub-data-connection.png)

# <a name="diagnostic-metrics--diagnostic-logstabdiagnostic-metricsdiagnostic-logs"></a>[Metriche di diagnostica/log di diagnostica](#tab/diagnostic-metrics+diagnostic-logs) 

1. Nella finestra **Connessione dati** usare le impostazioni seguenti:

    Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | **Nome connessione dati** | *DiagnosticsLogsConnection* | Nome della connessione da creare in Esplora dati di Azure.|
    | **Spazio dei nomi dell'hub eventi** | *AzureMonitoringData* | Nome scelto in precedenza che identifica lo spazio dei nomi. |
    | **Hub eventi** | *DiagnosticData* | Hub eventi creato. |
    | **Gruppo di consumer** | *adxpipeline* | Gruppo di consumer definito nell'hub eventi creato. |
    | | |

    Tabella di destinazione:

    Sono disponibili due opzioni per il routing: *statico* e *dinamico*. Per questa esercitazione viene usato il routing statico (impostazione predefinita), in cui vengono specificati il nome della tabella, il formato dati e il mapping. Lasciare deselezionato **My data includes routing info** (I miei dati includono le informazioni di routing).

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | **Tabella** | *DiagnosticRawRecords* | Tabella creata nel database *TestDatabase*. |
    | **Formato dati** | *JSON* | Formato usato nella tabella. |
    | **Mapping di colonne** | *DiagnosticRawRecordsMapping* | Il mapping creato nel database *TestDatabase*, che mappa i dati JSON in ingresso con i nomi di colonna e i tipi di dati della tabella *DiagnosticRawRecords*.|
    | | |

1. Selezionare **Create** (Crea).  

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)

1. Nella finestra **Connessione dati** usare le impostazioni seguenti:

    Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | **Nome connessione dati** | *ActivityLogsConnection* | Nome della connessione da creare in Esplora dati di Azure.|
    | **Spazio dei nomi dell'hub eventi** | *AzureMonitoringData* | Nome scelto in precedenza che identifica lo spazio dei nomi. |
    | **Hub eventi** | *insights-operational-logs* | Hub eventi creato. |
    | **Gruppo di consumer** | *$Default* | Il gruppo di consumer predefinito. Se necessario, è possibile crearne uno diverso. |
    | | |

    Tabella di destinazione:

    Sono disponibili due opzioni per il routing: *statico* e *dinamico*. Per questa esercitazione viene usato il routing statico (impostazione predefinita), in cui vengono specificati il nome della tabella, il formato dati e il mapping. Lasciare deselezionato **My data includes routing info** (I miei dati includono le informazioni di routing).

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | **Tabella** | *ActivityLogsRawRecords* | Tabella creata nel database *TestDatabase*. |
    | **Formato dati** | *JSON* | Formato usato nella tabella. |
    | **Mapping di colonne** | *ActivityLogsRawRecordsMapping* | Il mapping creato nel database *TestDatabase*, che mappa i dati JSON in ingresso con i nomi di colonna e i tipi di dati della tabella *ActivityLogsRawRecords*.|
    | | |

1. Selezionare **Create** (Crea).  
---

## <a name="query-the-new-tables"></a>Eseguire una query sulle nuove tabelle

È stata configurata una pipeline con un flusso di dati. L'inserimento tramite il cluster richiede 5 minuti per impostazione predefinita, quindi aspettare alcuni minuti durante il flusso di dati prima di avviare la query.

# <a name="diagnostic-metricstabdiagnostic-metrics"></a>[Metriche di diagnostica](#tab/diagnostic-metrics)
### <a name="query-the-diagnostic-metrics-table"></a>Eseguire una query sulla tabella delle metriche di diagnostica

La query seguente analizza i dati sulla durata della query dai record delle metriche di diagnostica di Esplora dati di Azure:

```kusto
DiagnosticMetrics
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Risultati della query:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06,156 |
| | |

# <a name="diagnostic-logstabdiagnostic-logs"></a>[Log di diagnostica](#tab/diagnostic-logs)
### <a name="query-the-diagnostic-logs-table"></a>Eseguire una query sulla tabella dei log di diagnostica

Questa pipeline genera inserimenti tramite un hub eventi. Verranno esaminati i risultati di queste inserimenti.
La query seguente analizza il numero di inserimenti accumulati in un minuto, con un esempio di `Database`, `Table` e `IngestionSourcePath` per ogni intervallo:

```kusto
DiagnosticLogs
| where Timestamp > ago(15m) and OperationName has 'INGEST'
| summarize count(), any(Database, Table, IngestionSourcePath) by bin(Timestamp, 1m)
```

Risultati della query:

|   |   |
| --- | --- |
|   |  count_ | any_Database | any_Table | any_IngestionSourcePath
|   | 00:06,156 | TestDatabase | DiagnosticRawRecords | https://rtmkstrldkereneus00.blob.core.windows.net/20190827-readyforaggregation/1133_TestDatabase_DiagnosticRawRecords_6cf02098c0c74410bd8017c2d458b45d.json.zip
| | |

# <a name="activity-logstabactivity-logs"></a>[Log attività](#tab/activity-logs)
### <a name="query-the-activity-logs-table"></a>Eseguire una query sulla tabella dei log attività

La query seguente analizza i dati dai record dei log attività di Esplora dati di Azure:

```kusto
ActivityLogs
| where OperationName == 'MICROSOFT.EVENTHUB/NAMESPACES/AUTHORIZATIONRULES/LISTKEYS/ACTION'
| where ResultType == 'Success'
| summarize avg(DurationMs)
```

Risultati della query:

|   |   |
| --- | --- |
|   |  avg(DurationMs) |
|   | 768,333 |
| | |

---

## <a name="next-steps"></a>Passaggi successivi

* Leggere l'articolo [Scrivere query per Esplora dati di Azure](write-queries.md) per informazioni su come scrivere molte più query sui dati estratti da Esplora dati di Azure.
* [Monitorare le operazioni di inserimento in Esplora dati di Azure usando i log di diagnostica](using-diagnostic-logs.md)
* [Usare le metriche per monitorare l'integrità del cluster](using-metrics.md)
