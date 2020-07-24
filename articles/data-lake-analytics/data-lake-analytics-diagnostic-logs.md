---
title: Abilitare e visualizzare i log di diagnostica per Azure Data Lake Analytics
description: Informazioni su come configurare e accedere ai log di diagnostica per Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: c8c24134c4694a9a2df36ac278452a532a5125ad
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87132603"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accesso ai log di diagnostica per Azure Data Lake Analytics

La registrazione diagnostica consente di raccogliere audit trail di accesso ai dati. Questi log includono informazioni quali:

* Un elenco di utenti che hanno avuto accesso ai dati.
* La frequenza con cui si accede ai dati.
* La quantità di dati archiviati nell'account.

## <a name="enable-logging"></a>Abilitazione della registrazione

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Aprire l'account Data Lake Analytics e selezionare **Log di diagnostica** dalla sezione __Monitoraggio__. Selezionare quindi __Turn on diagnostics__ (Attiva diagnostica).

    ![Attivare la diagnostica per acquisire i log di controllo e i log delle richieste.](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. Dalle __Impostazioni di diagnostica__ immettere un __Nome__ per la configurazione della registrazione e selezionare le opzioni di registrazione.

    ![Attivare la diagnostica per acquisire i log di controllo e i log delle richieste.](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Abilitare i log di diagnostica")

   * È possibile scegliere di archiviare/elaborare i dati in tre modi diversi.

     * Selezionare __Archive to a storage account__ (Archivia in un account di archiviazione) per archiviare i log in un account di archiviazione di Azure. Usare questa opzione se si vogliono archiviare i dati. Se si seleziona questa opzione è necessario fornire un account di archiviazione di Azure in cui salvare i log.

     * Selezionare **Stream to an Event Hub** (Esegui streaming in un Hub eventi) per trasmettere i dati di log a un Hub eventi di Azure. Usare questa opzione se si ha una pipeline di elaborazione downstream che analizza in tempo reale i log in ingresso. Se si seleziona questa opzione, è necessario fornire i dettagli dell'Hub eventi di Azure che si desidera utilizzare.

     * Selezionare __Invia a log Analytics__ per inviare i dati al servizio monitoraggio di Azure. Usare questa opzione se si vogliono usare i log di monitoraggio di Azure per raccogliere e analizzare i log.
   * Specificare se si desidera ottenere i log di controllo, i log delle richieste o entrambi.  Un log delle richieste acquisisce tutte le richieste API. Un log di controllo registra tutte le operazioni attivate dalla richiesta dell'API.

   * Per __Archivia in un account di archiviazione__ specificare il numero di giorni per cui i dati verranno conservati.

   * Fare clic su __Salva__.

        > [!NOTE]
        > È necessario selezionare una tra le opzioni __Archivia in un account di archiviazione__, __Streaming in un hub eventi__ o __Invia a Log Analytics__ prima di fare clic sul pulsante __Salva__.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Usare l'account di archiviazione di Azure contenente i dati di log

1. Per visualizzare i contenitori BLOB che contengono i dati della registrazione, aprire l'account di archiviazione di Azure associato a Data Lake Analytics per la registrazione e quindi fare clic su __BLOB__.

   * Il contenitore **insights-logs-audit** contiene i log di controllo.
   * Il contenitore **insights-logs-requests** contiene i log delle richieste.

2. All'interno dei contenitori i log vengono archiviati con la struttura file seguente:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > Il pannello `##` nel percorso contengono l'anno, il mese, il giorno e l'ora in cui è stato creato il log. Data Lake Analytics crea un file ogni ora, in modo `m=` contenga sempre un valore di `00`.

    Ad esempio, il percorso completo a un log di controllo potrebbe essere:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Analogamente, il percorso completo a un log della richiesta potrebbe essere:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Struttura di log

I log di controllo e delle richieste sono in formato JSON strutturato.

### <a name="request-logs"></a>Request Logs

Di seguito viene riportata una voce di esempio nel log delle richieste in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.

```json
{
"records":
  [
    . . . .
    ,
    {
         "time": "2016-07-07T21:02:53.456Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schema del log delle richieste

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| time |Stringa |Il timestamp del log (fusorario UTC) |
| resourceId |Stringa |Identificatore della risorsa interessata dall'operazione |
| category |Stringa |La categoria di log. Ad esempio, **Richieste**. |
| operationName |Stringa |Il nome dell'operazione registrata. Ad esempio, GetAggregatedJobHistory. |
| resultType |Stringa |Lo stato dell'operazione, ad esempio 200. |
| callerIpAddress |Stringa |L’indirizzo IP del client che esegue la richiesta |
| correlationId |Stringa |Identificatore del log. Questo valore può essere usato per raggruppare un set di voci di log correlate. |
| identity |Oggetto |L'identità che ha generato il log |
| properties |JSON |Per informazioni dettagliate, vedere la sezione successiva (Schema delle proprietà del log di richiesta) |

#### <a name="request-log-properties-schema"></a>Schema delle proprietà del log di richiesta

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| HttpMethod |Stringa |Il metodo HTTP utilizzato per l'operazione. Esempio: GET. |
| Percorso |Stringa |Il percorso coinvolto nell'operazione |
| RequestContentLength |INT |La lunghezza del contenuto della richiesta HTTP |
| ClientRequestId |Stringa |Identificatore che identifica in modo univoco la richiesta |
| StartTime |Stringa |L'ora in cui il server ha ricevuto la richiesta |
| EndTime |Stringa |L'ora in cui il server ha inviato una risposta |

### <a name="audit-logs"></a>Log di controllo

Di seguito viene riportata una voce di esempio nel log di controllo in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Schema del log di controllo

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| time |Stringa |Il timestamp del log (fusorario UTC) |
| resourceId |Stringa |Identificatore della risorsa interessata dall'operazione |
| category |Stringa |La categoria di log. Ad esempio, **Audit**. |
| operationName |Stringa |Il nome dell'operazione registrata. Ad esempio, JobSubmitted. |
| resultType |Stringa |Stato secondario per lo stato del processo (operationName). |
| resultSignature |Stringa |Informazioni aggiuntive sullo stato di processo (operationName). |
| identity |Stringa |L'utente che ha richiesto l'operazione. Ad esempio: susan@contoso.com. |
| properties |JSON |Per informazioni dettagliate, vedere la sezione successiva (Schema delle proprietà del log di controllo) |

> [!NOTE]
> **resultType** e **resultSignature** offrono informazioni sul risultato di un'operazione e contengono un valore solo se un'operazione è stata completata. Ad esempio, contengono un valore solo quando **operationName** contiene un valore **JobStarted** o **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schema delle proprietà del log di controllo

| Nome | Tipo | Descrizione |
| --- | --- | --- |
| JobId |Stringa |L'ID assegnato al processo |
| JobName |Stringa |Il nome fornito per il processo |
| JobRunTime |Stringa |Il runtime usato per l'elaborazione del processo |
| SubmitTime |Stringa |Ora (UTC) in cui è stato inviato il processo |
| StartTime |Stringa |Ora di avvio dell'esecuzione del processo dopo l'invio (in UTC) |
| EndTime |Stringa |Ora di fine del processo |
| Parallelismo |Stringa |Numero di unità di Data Lake Analytics richieste per questo processo durante l'invio |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** e **Parallelism** forniscono informazioni su un'operazione. Queste voci contengono un valore solo se l'operazione è stata avviata o completata. Ad esempio, **SubmitTime** contiene un valore solo dopo che **operationName** ha il valore **JobSubmitted**.

## <a name="process-the-log-data"></a>Elaborare i dati di log

Azure Data Lake Analytics fornisce un esempio su come elaborare e analizzare i dati di log. È possibile trovare l'esempio in [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md)
