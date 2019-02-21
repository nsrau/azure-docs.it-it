---
title: 'Esercitazione: Inserire dati dei log di diagnostica e attività in Esplora dati di Azure senza una riga di codice'
description: In questa esercitazione verrà descritto come inserire dati in Esplora dati di Azure senza una riga di codice e come eseguire query sui dati.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: tutorial
ms.date: 2/5/2019
ms.openlocfilehash: 145a56bee857debdbf028834a3ed378efd8671c8
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447498"
---
# <a name="tutorial-ingest-data-in-azure-data-explorer-without-one-line-of-code"></a>Esercitazione: Inserire dati in Esplora dati di Azure senza una riga di codice

Questa esercitazione illustra come inserire i dati dei log di diagnostica e attività in un cluster di Esplora dati di Azure senza una riga di codice. Questo semplice metodo di inserimento consente di iniziare rapidamente a eseguire query in Esplora dati di Azure per l'analisi dei dati.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Creare tabelle e mapping di inserimento in un database di Esplora dati di Azure.
> * Formattare i dati inseriti usando un criterio di aggiornamento.
> * Creare un [hub eventi](/azure/event-hubs/event-hubs-about) e connetterlo a Esplora dati di Azure.
> * Trasmettere i dati a un hub eventi dai [log di diagnostica di Monitoraggio di Azure](/azure/azure-monitor/platform/diagnostic-logs-overview) e dai [log attività di Monitoraggio di Azure](/azure/azure-monitor/platform/activity-logs-overview).
> * Eseguire query sui dati inseriti usando Esplora dati di Azure.

> [!NOTE]
> Creare tutte le risorse nella stessa località/area di Azure. Questo è un requisito per i log di diagnostica di Monitoraggio di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md). In questa esercitazione il nome del database è *AzureMonitoring*.

## <a name="azure-monitoring-data-provider---diagnostic-and-activity-logs"></a>Provider di dati di Monitoraggio di Azure: log di diagnostica e attività

Visualizzare e interpretare i dati forniti dai log di diagnostica e attività di Monitoraggio di Azure. Verrà creata una pipeline di inserimento in base a questi schemi di dati.

### <a name="diagnostic-logs-example"></a>Esempio di log di diagnostica

I log di diagnostica di Azure sono metriche generate da un servizio di Azure che forniscono dati sul funzionamento del servizio stesso. I dati vengono aggregati con un intervallo di tempo di 1 minuto. Ogni evento dei log di diagnostica contiene un unico record. Di seguito è riportato un esempio di schema di evento di metrica di Esplora dati di Azure sulla durata di una query:

```json
{
    "count": 14,
    "total": 0,
    "minimum": 0,
    "maximum": 0,
    "average": 0,
    "resourceId": "/SUBSCRIPTIONS/F3101802-8C4F-4E6E-819C-A3B5794D33DD/RESOURCEGROUPS/KEDAMARI/PROVIDERS/MICROSOFT.KUSTO/CLUSTERS/KEREN",
    "time": "2018-12-20T17:00:00.0000000Z",
    "metricName": "QueryDuration",
    "timeGrain": "PT1M"
}
```

### <a name="activity-logs-example"></a>Esempio di log attività

I log attività di Azure sono log a livello di sottoscrizione contenenti una raccolta di record. Offrono informazioni approfondite sulle operazioni eseguite sulle risorse nella sottoscrizione. A differenza dei log di diagnostica, un evento dei log attività include una matrice di record. Sarà necessario dividere questa matrice di record più avanti nell'esercitazione. Di seguito è riportato un esempio di evento di log attività per il controllo dell'accesso:

```json
{
    "records": [
    {
        "time": "2018-12-26T16:23:06.1090193Z",
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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
        "resourceId": "/SUBSCRIPTIONS/F80EB51C-C534-4F0B-80AB-AEBC290C1C19/RESOURCEGROUPS/CLEANUPSERVICE/PROVIDERS/MICROSOFT.WEB/SITES/CLNB5F73B70-DCA2-47C2-BB24-77B1A2CAAB4D/PROVIDERS/MICROSOFT.AUTHORIZATION",
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

## <a name="set-up-ingestion-pipeline-in-azure-data-explorer"></a>Configurare la pipeline di inserimento in Esplora dati di Azure 

La configurazione della pipeline di Esplora dati di Azure implica vari passaggi, che includono la [creazione di una tabella e l'inserimento dei dati](/azure/data-explorer/ingest-sample-data#ingest-data). È anche possibile manipolare, mappare e aggiornare i dati.

### <a name="connect-to-azure-data-explorer-web-ui"></a>Connettersi all'interfaccia utente Web di Esplora dati di Azure

1. Nel database *AzureMonitoring* di Esplora dati di Azure selezionare **Query** per aprire l'interfaccia utente Web di Esplora dati di Azure.

    ![Query](media/ingest-data-no-code/query-database.png)

### <a name="create-target-tables"></a>Creare le tabelle di destinazione

Usare l'interfaccia utente Web di Esplora dati di Azure per creare le tabelle di destinazione nel database di Esplora dati di Azure.

#### <a name="diagnostic-logs-table"></a>Tabella dei log di diagnostica

1. Creare la tabella *DiagnosticLogsRecords* nel database *AzureMonitoring* che riceverà i record dei log di diagnostica usando il comando di controllo `.create table`:

    ```kusto
    .create table DiagnosticLogsRecords (Timestamp:datetime, ResourceId:string, MetricName:string, Count:int, Total:double, Minimum:double, Maximum:double, Average:double, TimeGrain:string)
    ```

1. Selezionare **Run** (Esegui) per creare la tabella.

    ![Eseguire la query](media/ingest-data-no-code/run-query.png)

#### <a name="activity-logs-tables"></a>Tabelle dei log attività

Poiché la struttura dei log attività non è tabulare, sarà necessario manipolare i dati ed espandere ogni evento in uno o più record. I dati non elaborati verranno inseriti in una tabella intermedia, *ActivityLogsRawRecords*. A quel punto, i dati verranno manipolati ed espansi. I dati espansi verranno quindi inseriti nella tabella *ActivityLogsRecords* usando un criterio di aggiornamento. Pertanto, per l'inserimento dei log attività sarà necessario creare due tabelle distinte.

1. Creare la tabella *ActivityLogsRecords* nel database *AzureMonitoring* che riceverà i record dei log attività. Eseguire la query di Esplora dati di Azure seguente per creare la tabella:

    ```kusto
    .create table ActivityLogsRecords (Timestamp:datetime, ResourceId:string, OperationName:string, Category:string, ResultType:string, ResultSignature:string, DurationMs:int, IdentityAuthorization:dynamic, IdentityClaims:dynamic, Location:string, Level:string)
    ```

1. Creare la tabella dati intermedia *ActivityLogsRawRecords* nel database *AzureMonitoring* per la manipolazione dei dati:

    ```kusto
    .create table ActivityLogsRawRecords (Records:dynamic)
    ```

<!--
     ```kusto
     .alter-merge table ActivityLogsRawRecords policy retention softdelete = 0d
    <[Retention](/azure/kusto/management/retention-policy) for an intermediate data table is set at zero retention policy.
-->

### <a name="create-table-mappings"></a>Creare i mapping della tabella

 Il formato dei dati è `json`, quindi è necessario eseguirne il mapping. Il mapping `json` mappa ogni percorso JSON al nome di una colonna della tabella.

#### <a name="diagnostic-logs-table-mapping"></a>Mapping della tabella dei log di diagnostica

Usare la query seguente per eseguire il mapping dei dati con la tabella:

```kusto
.create table DiagnosticLogsRecords ingestion json mapping 'DiagnosticLogsRecordsMapping' '[{"column":"Timestamp","path":"$.time"},{"column":"ResourceId","path":"$.resourceId"},{"column":"MetricName","path":"$.metricName"},{"column":"Count","path":"$.count"},{"column":"Total","path":"$.total"},{"column":"Minimum","path":"$.minimum"},{"column":"Maximum","path":"$.maximum"},{"column":"Average","path":"$.average"},{"column":"TimeGrain","path":"$.timeGrain"}]'
```

#### <a name="activity-logs-table-mapping"></a>Mapping della tabella dei log attività

Usare la query seguente per eseguire il mapping dei dati con la tabella:

```kusto
.create table ActivityLogsRawRecords ingestion json mapping 'ActivityLogsRawRecordsMapping' '[{"column":"Records","path":"$.records"}]'
```

### <a name="create-update-policy"></a>Creare il criterio di aggiornamento

1. Creare una [funzione](/azure/kusto/management/functions) che espande la raccolta di record in modo che ogni valore riceva una riga distinta. Usare l'operatore [`mvexpand`](/azure/kusto/query/mvexpandoperator):

    ```kusto
    .create function ActivityLogRecordsExpand() {
        ActivityLogsRawRecords
        | mvexpand events = Records
        | project
            Timestamp = todatetime(events["time"]),
            ResourceId = tostring(events["resourceId"]),
            OperationName = tostring(events["operationName"]),
            Category = tostring(events["category"]),
            ResultType = tostring(events["resultType"]),
            ResultSignature = tostring(events["resultSignature"]),
            DurationMs = toint(events["durationMs"]),
            IdentityAuthorization = events.identity.authorization,
            IdentityClaims = events.identity.claims,
            Location = tostring(events["location"]),
            Level = tostring(events["level"])
    }
    ```

2. Aggiungere un [criterio di aggiornamento](/azure/kusto/concepts/updatepolicy) nella tabella di destinazione. Eseguirà automaticamente la query sui nuovi dati inseriti nella tabella dati intermedia *ActivityLogsRawRecords* e inserirà i relativi risultati nella tabella *ActivityLogsRecords*:

    ```kusto
    .alter table ActivityLogsRecords policy update @'[{"Source": "ActivityLogsRawRecords", "Query": "ActivityLogRecordsExpand()", "IsEnabled": "True"}]'
    ```

## <a name="create-an-event-hub-namespace"></a>Creare uno spazio dei nomi di hub eventi

I log di diagnostica di Azure consentono l'esportazione delle metriche in un account di archiviazione o in un hub eventi. In questa esercitazione le metriche verranno instradate tramite un hub eventi. Con i passaggi seguenti verrà creato uno spazio dei nomi di hub eventi e un hub eventi per i log di diagnostica. Monitoraggio di Azure creerà il componente *insights-operational-logs* dell'hub eventi per i log attività.

1. Creare un hub eventi usando un modello di Resource Manager nel portale di Azure. Usare il pulsante seguente per avviare la distribuzione. Fare clic con il pulsante destro del mouse e selezionare **Apri in una nuova finestra** per poter seguire il resto dei passaggi di questo articolo. Il pulsante **Distribuzione in Azure** consente di passare al portale di Azure.

    [![Distribuzione in Azure](media/ingest-data-no-code/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

1. Creare uno spazio dei nomi di hub eventi e un hub eventi per i log di diagnostica.

    ![Creazione dell'hub eventi](media/ingest-data-no-code/event-hub.png)

    Compilare il modulo con le informazioni seguenti. Usare i valori predefiniti per tutte le impostazioni non elencate nella tabella seguente.

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Sottoscrizione | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'hub eventi.|
    | Gruppo di risorse | *test-resource-group* | Creare un nuovo gruppo di risorse. |
    | Località | Selezionare l'area più appropriata in base alle esigenze. | Creare lo spazio dei nomi di hub eventi nella stessa posizione delle altre risorse.
    | Nome spazio dei nomi | *AzureMonitoringData* | Scegliere un nome univoco per identificare lo spazio dei nomi.
    | Nome hub eventi | *DiagnosticLogsData* | L'hub eventi si trova nello spazio dei nomi, che fornisce un contenitore di ambito univoco. |
    | Consumer group name (Nome gruppo di consumer) | *adxpipeline* | Creare un nome del gruppo di consumer. Consente a più applicazioni di avere ognuna una visualizzazione distinta del flusso di eventi. |
    | | |

## <a name="connect-azure-monitoring-logs-to-event-hub"></a>Connettere i log di Monitoraggio di Azure all'hub eventi

### <a name="diagnostic-logs-connection-to-event-hub"></a>Connessione dei log di diagnostica all'hub eventi

Selezionare una risorsa da cui esportare le metriche. Esistono diversi tipi di risorse che consentono l'esportazione dei log di diagnostica, tra cui lo spazio dei nomi di hub eventi, KeyVault, hub IoT e il cluster di Esplora dati di Azure. In questa esercitazione verrà usato il cluster di Esplora dati di Azure come risorsa.

1. Selezionare il cluster Kusto nel portale di Azure.

    ![Impostazioni di diagnostica](media/ingest-data-no-code/diagnostic-settings.png)

1. Scegliere **Impostazioni di diagnostica** dal menu a sinistra.
1. Fare clic sul collegamento **Abilita diagnostica**. Viene visualizzata la finestra **Impostazioni di diagnostica**.

    ![Finestra Impostazioni di diagnostica](media/ingest-data-no-code/diagnostic-settings-window.png)

1. Nel riquadro **Impostazioni di diagnostica**:
    1. Assegnare ai dati del log di diagnostica il nome *ADXExportedData*.
    1. Selezionare la casella di controllo **AllMetrics** (facoltativo).
    1. Selezionare la casella di controllo **Streaming in un hub eventi**.
    1. Fare clic su **Configura**.

1. Nel riquadro **Selezionare l'hub eventi** configurare l'esportazione nell'hub eventi creato:
    1. **Selezionare lo spazio dei nomi dell'hub eventi** *AzureMonitoringData* dal menu a discesa.
    1. **Selezionare il nome dell'hub eventi** *diagnosticlogsdata* dal menu a discesa.
    1. **Selezionare il nome dei criteri per l'hub eventi** dal menu a discesa.
    1. Fare clic su **OK**.

1. Fare clic su **Save**. Nella finestra vengono visualizzati lo spazio dei nomi, il nome e il nome del criterio per l'hub eventi.

    ![Salvare le impostazioni di diagnostica](media/ingest-data-no-code/save-diagnostic-settings.png)

### <a name="activity-logs-connection-to-event-hub"></a>Connessione dei log attività all'hub eventi

1. Scegliere **Log attività** dal menu a sinistra del portale di Azure.
1. Viene visualizzata la finestra **Log attività**. **Fare clic su Esporta in Hub eventi**.

    ![Log attività](media/ingest-data-no-code/activity-log.png)

1. Nella finestra **Esporta log attività**:
 
    ![Esporta log attività](media/ingest-data-no-code/export-activity-log.png)

    1. Selezionare la propria sottoscrizione.
    1. Nel menu a discesa **Aree** scegliere **Seleziona tutto**
    1. Selezionare la casella di controllo **Esporta in un hub eventi**.
    1. Fare clic su **Selezionare uno spazio dei nomi del bus di servizio** per aprire il riquadro **Selezionare l'hub eventi**.
    1. Nei menu a discesa del riquadro **Selezionare l'hub eventi** selezionare la propria sottoscrizione, lo spazio dei nomi dell'evento *AzureMonitoringData* e il nome del criterio dell'hub eventi predefinito.
    1. Fare clic su **OK**.
    1. Fare clic su **Salva** nell'angolo in alto a destra della finestra. Verrà creato un hub eventi denominato *insights-operational-logs*.

### <a name="see-data-flowing-to-your-event-hubs"></a>Esaminare il flusso di dati verso gli hub eventi

1. Attendere alcuni minuti finché non viene definita la connessione. L'esportazione dei log attività nell'hub eventi è completata. Passare allo spazio dei nomi di hub eventi per vedere gli hub eventi creati.

    ![Hub eventi creati](media/ingest-data-no-code/event-hubs-created.png)

1. Esaminare il flusso di dati verso l'hub eventi:

    ![Dati degli hub eventi](media/ingest-data-no-code/event-hubs-data.png)

## <a name="connect-event-hub-to-azure-data-explorer"></a>Connettere l'hub eventi a Esplora dati di Azure

### <a name="diagnostic-logs-data-connection"></a>Connessione dati dei log di diagnostica

1. Nel cluster di Esplora dati di Azure *kustodocs* scegliere **Database** dal menu a sinistra.
1. Nella finestra **Database** selezionare il nome del database *AzureMonitoring*.
1. Scegliere **Inserimento dati** dal menu a sinistra.
1. Nella finestra **Inserimento dati** fare clic su **+ Aggiungi connessione dati**.
1. Nella finestra **Connessione dati** immettere le informazioni seguenti:

    ![Connessione all'hub eventi](media/ingest-data-no-code/event-hub-data-connection.png)

    Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Data connection name (Nome connessione dati) | *DiagnosticsLogsConnection* | Nome della connessione da creare in Esplora dati di Azure.|
    | Spazio dei nomi dell'hub eventi | *AzureMonitoringData* | Nome scelto in precedenza che identifica lo spazio dei nomi. |
    | Hub eventi | *diagnosticlogsdata* | Hub eventi creato. |
    | Gruppo di consumer | *adxpipeline* | Gruppo di consumer definito nell'hub eventi creato. |
    | | |

    Tabella di destinazione:

    Sono disponibili due opzioni per il routing: *statico* e *dinamico*. Per questa esercitazione viene usato il routing statico (impostazione predefinita), in cui vengono specificati il nome della tabella, il formato di file e il mapping. Lasciare deselezionato **My data includes routing info** (I miei dati includono le informazioni di routing).

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Tabella | *DiagnosticLogsRecords* | La tabella creata nel database *AzureMonitoring*. |
    | Formato dati | *JSON* | Formato nella tabella. |
    | Mapping di colonne | *DiagnosticLogsRecordsMapping* | Il mapping creato nel database *AzureMonitoring*, che mappa i dati JSON in ingresso con i nomi di colonna e i tipi di dati di *DiagnosticLogsRecords*.|
    | | |

1. Fare clic su **Crea**  

### <a name="activity-logs-data-connection"></a>Connessione dei dati dei log attività

Ripetere i passaggi descritti nella sezione [Connessione dati dei log di diagnostica](#diagnostic-logs-data-connection) per creare una connessione dati dei log attività.

1. Nella finestra **Connessione dati** immettere le informazioni seguenti:

    Origine dati:

    **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Data connection name (Nome connessione dati) | *ActivityLogsConnection* | Nome della connessione da creare in Esplora dati di Azure.|
    | Spazio dei nomi dell'hub eventi | *AzureMonitoringData* | Nome scelto in precedenza che identifica lo spazio dei nomi. |
    | Hub eventi | *insights-operational-logs* | Hub eventi creato. |
    | Gruppo di consumer | *$Default* | Il gruppo di consumer predefinito. Se necessario, è possibile crearne uno diverso. |
    | | |

    Tabella di destinazione:

    Sono disponibili due opzioni per il routing: *statico* e *dinamico*. Per questa esercitazione viene usato il routing statico (impostazione predefinita), in cui vengono specificati il nome della tabella, il formato di file e il mapping. Lasciare deselezionato **My data includes routing info** (I miei dati includono le informazioni di routing).

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Tabella | *ActivityLogsRawRecords* | La tabella creata nel database *AzureMonitoring*. |
    | Formato dati | *JSON* | Formato nella tabella. |
    | Mapping di colonne | *ActivityLogsRawRecordsMapping* | Il mapping creato nel database *AzureMonitoring*, che mappa i dati JSON in ingresso con i nomi di colonna e i tipi di dati di *ActivityLogsRawRecords*.|
    | | |

1. Fare clic su **Crea**  

## <a name="query-the-new-tables"></a>Eseguire una query sulle nuove tabelle

È stata configurata una pipeline con un flusso di dati. L'inserimento tramite il cluster richiede 5 minuti per impostazione predefinita, quindi aspettare alcuni minuti durante il flusso di dati prima di avviare la query.

### <a name="diagnostic-logs-table-query-example"></a>Esempio di query sulla tabella dei log di diagnostica

La query seguente analizza i dati sulla durata della query dai record dei log di diagnostica di Esplora dati di Azure:

```kusto
DiagnosticLogsRecords
| where Timestamp > ago(15m) and MetricName == 'QueryDuration'
| summarize avg(Average)
```

Risultati della query:

|   |   |
| --- | --- |
|   |  avg_Average |
|   | 00:06,156 |
| | |

### <a name="activity-logs-table-query-example"></a>Esempio di query sulla tabella dei log attività

La query seguente analizza i dati dai record dei log attività di Esplora dati di Azure:

```kusto
ActivityLogsRecords
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

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per informazioni su come scrivere molte più query sui dati estratti da Esplora dati di Azure:

> [!div class="nextstepaction"]
> [Scrivere query per Esplora dati di Azure](write-queries.md)
