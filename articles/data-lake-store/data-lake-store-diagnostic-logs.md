---
title: Visualizzazione dei log di diagnostica per Azure Data Lake Store | Documentazione Microsoft
description: 'Informazioni su come configurare e accedere ai log di diagnostica per Archivio Data Lake di Azure  '
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: f6496fb62670c480ce543a51225856f0fb5d89b5
ms.sourcegitcommit: 651a6fa44431814a42407ef0df49ca0159db5b02
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Accesso ai log di diagnostica per Archivio Data Lake di Azure
Informazioni su come abilitare la registrazione diagnostica per l'account Data Lake Store e visualizzare i log raccolti per l'account.

Le organizzazioni possono abilitare la registrazione diagnostica per il loro account di Archivio Data Lake di Azure per raccogliere gli audit trial di accesso ai dati che forniscono varie informazioni, come l’elenco di utenti che hanno avuto accesso ai dati, la frequenza di accesso ai dati, la quantità di dati archiviati nell’account, ecc.

## <a name="prerequisites"></a>Prerequisiti
* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Account di Archivio Data Lake di Azure**. Seguire le istruzioni fornite in [Introduzione ad Archivio Azure Data Lake tramite il portale di Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Abilitare la registrazione diagnostica per l'account di Archivio Data Lake
1. Accedere al nuovo [portale di Azure](https://portal.azure.com).
2. Aprire l'account di Data Lake Store e nel pannello corrispondente fare clic su **Impostazioni** e quindi su **Log di diagnostica**.
3. Nel pannello **Log di diagnostica**, fare clic su **Attivare la diagnostica**.

    ![Abilitare la funzionalità di registrazione diagnostica](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Abilitare i log di diagnostica")

3. Nel pannello **Diagnostica** , apportare le modifiche seguenti per configurare la registrazione diagnostica.
   
    ![Abilitare la funzionalità di registrazione diagnostica](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Abilitare i log di diagnostica")
   
   * Per **Nome** immettere un valore per la configurazione del log di diagnostica.
   * È possibile scegliere di archiviare/elaborare i dati in modi diversi.
     
        * Selezionare l'opzione per **archiviare in un account di archiviazione** per archiviare i log in un account di archiviazione di Azure. Utilizzare questa opzione se si desidera archiviare i dati che saranno elaborati in batch in un secondo momento. Se si seleziona questa opzione è necessario fornire un account di archiviazione di Azure in cui salvare i log.
        
        * Selezionare l'opzione per eseguire lo **streaming in Hub eventi** per trasmettere i dati di log a un Hub eventi di Azure. Molto probabilmente questa opzione viene utilizzata se si dispone di una pipeline di elaborazione a valle per analizzare in tempo reale i log in ingresso. Se si seleziona questa opzione, è necessario fornire i dettagli dell'Hub eventi di Azure che si desidera utilizzare.

        * Selezionare l'opzione per **inviare a Log Analytics** per usare il servizio Log Analytics di Azure per analizzare i dati di log generati. Se si seleziona questa opzione, è necessario fornire i dettagli per l'area di lavoro di Operations Management Suite che si vuole usare per eseguire l'analisi dei log.
     
   * Specificare se si desidera ottenere i log di controllo, i log delle richieste o entrambi.
   * Specificare il numero di giorni per cui devono essere conservati i dati. La conservazione dei dati è disponibile solo se si usano account di archiviazione di Azure per archiviare i dati del log.
   * Fare clic su **Save**.

Dopo aver attivato le impostazioni di diagnostica, è possibile controllare i log nella scheda **Log di diagnostica** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Visualizzare i log di diagnostica dell'account di Archivio Data Lake
Esistono due modi per visualizzare i dati di log dell'account Data Lake Store.

* Dalla visualizzazione delle impostazioni dell'account Data Lake Store
* Dall'account di Archiviazione di Azure dove sono archiviati i dati

### <a name="using-the-data-lake-store-settings-view"></a>Usando la visualizzazione Impostazioni dell'account Data Lake Store
1. Nel pannello **Impostazioni** dell'account Data Lake Store fare clic su **Log di diagnostica**.
   
    ![Visualizzare la registrazione diagnostica](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Visualizzare i log di diagnostica") 
2. Nel pannello **Log di diagnostica** vengono visualizzati i log classificati come **log di controllo** e **log delle richieste**.
   
   * I log delle richieste acquisiscono tutte le richieste API fatte nell’account di Archivio Data Lake.
   * I log di controllo sono simili a quelli delle richieste ma forniscono una suddivisione più dettagliata delle operazioni eseguite nell'account di Archivio Data Lake. Ad esempio, una singola chiamata API di caricamento nei log delle richieste potrebbe risultare in molteplici operazioni di "Aggiungi" nei log di controllo.
3. Per scaricare i log, fare clic sul collegamento **Scarica** per ogni voce di log.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Dall'account di Archiviazione di Azure che contiene i dati di log
1. Aprire il pannello Account di Archiviazione di Azure associato a Data Lake Store per la registrazione e quindi fare clic su BLOB. Il pannello **Servizio BLOB** elenca due contenitori.
   
    ![Visualizzare la registrazione diagnostica](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Visualizzare i log di diagnostica")
   
   * Il contenitore **insights-logs-audit** contiene i log di controllo.
   * Il contenitore **insights-logs-requests** contiene i log delle richieste.
2. All'interno di questi contenitori i log vengono archiviati con la struttura seguente.
   
    ![Visualizzare la registrazione diagnostica](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Visualizzare i log di diagnostica")
   
    Ad esempio, il percorso completo a un log di controllo potrebbe essere `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Analogamente, il percorso completo di un log richieste potrebbe essere `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Informazioni sulla struttura dei dati del log
I log di controllo e delle richieste sono in formato JSON. In questa sezione, viene esaminata la struttura di JSON per i log delle richieste e di controllo.

### <a name="request-logs"></a>Request Logs
Di seguito viene riportata una voce di esempio nel log delle richieste in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schema del log delle richieste
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| time |String |Il timestamp del log (fusorario UTC) |
| resourceId |String |L’ID della risorsa interessata dall’operazione |
| category |String |La categoria di log. Ad esempio, **Richieste**. |
| operationName |String |Il nome dell'operazione registrata. Ad esempio, getfilestatus. |
| resultType |String |Lo stato dell'operazione, ad esempio 200. |
| callerIpAddress |String |L’indirizzo IP del client che esegue la richiesta |
| correlationId |String |L'ID del log utilizzato per raggruppare un set di voci di log correlate |
| identity |Oggetto |L'identità che ha generato il log |
| properties |JSON |Vedere di seguito per ulteriori dettagli |

#### <a name="request-log-properties-schema"></a>Schema delle proprietà del log di richiesta
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| HttpMethod |String |Il metodo HTTP utilizzato per l'operazione. Esempio: GET. |
| Path |String |Il percorso coinvolto nell'operazione |
| RequestContentLength |int |La lunghezza del contenuto della richiesta HTTP |
| ClientRequestId |String |L’ID che identifica in modo univoco la richiesta |
| StartTime |String |L'ora in cui il server ha ricevuto la richiesta |
| EndTime |String |L'ora in cui il server ha inviato una risposta |

### <a name="audit-logs"></a>Log di controllo
Di seguito viene riportata una voce di esempio nel log di controllo in formato JSON. Ogni BLOB ha un oggetto radice denominato **record** che contiene una matrice di oggetti di log.

    {
    "records": 
      [        
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schema del log di controllo
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| time |String |Il timestamp del log (fusorario UTC) |
| resourceId |String |L’ID della risorsa interessata dall’operazione |
| category |String |La categoria di log. Ad esempio, **Audit**. |
| operationName |String |Il nome dell'operazione registrata. Ad esempio, getfilestatus. |
| resultType |String |Lo stato dell'operazione, ad esempio 200. |
| correlationId |String |L'ID del log utilizzato per raggruppare un set di voci di log correlate |
| identity |Oggetto |L'identità che ha generato il log |
| properties |JSON |Vedere di seguito per ulteriori dettagli |

#### <a name="audit-log-properties-schema"></a>Schema delle proprietà del log di controllo
| Nome | Tipo | Descrizione |
| --- | --- | --- |
| StreamName |String |Il percorso coinvolto nell'operazione |

## <a name="samples-to-process-the-log-data"></a>Esempi per elaborare i dati di log
Azure Data Lake Store fornisce un esempio su come elaborare e analizzare i dati di log. È possibile trovare l'esempio all'indirizzo [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 

## <a name="see-also"></a>Vedere anche
* [Panoramica di Archivio Data Lake di Azure](data-lake-store-overview.md)
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)

