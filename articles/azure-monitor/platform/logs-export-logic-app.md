---
title: Archiviare dati da un'area di lavoro Log Analytics ad archiviazione di Azure tramite l'app per la logica
description: Descrive un metodo per usare app per la logica di Azure per eseguire query sui dati da un'area di lavoro Log Analytics e inviarli ad archiviazione di Azure.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/02/2020
ms.openlocfilehash: ed9942fa7b73418e3ef1ddf0651781d32b662995
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92049790"
---
# <a name="archive-data-from-log-analytics-workspace-to-azure-storage-using-logic-app"></a>Archiviare dati da un'area di lavoro Log Analytics ad archiviazione di Azure tramite l'app per la logica
Questo articolo descrive un metodo per usare [app](../../logic-apps/index.yml) per la logica di Azure per eseguire query sui dati da un'area di lavoro log Analytics in monitoraggio di Azure e inviarli ad archiviazione di Azure. Usare questo processo quando è necessario esportare i dati di log di monitoraggio di Azure per gli scenari di controllo e conformità o per consentire a un altro servizio di recuperare questi dati.  

## <a name="other-export-methods"></a>Altri metodi di esportazione
Il metodo descritto in questo articolo descrive un'esportazione pianificata da una query di log usando un'app per la logica. Di seguito sono riportate altre opzioni per esportare i dati per scenari specifici:

- Per esportare tutti i dati dall'area di lavoro di Log Analytics a un account di archiviazione di Azure o a un hub eventi, usare la funzionalità di esportazione dei dati Log Analytics area di lavoro dei log di monitoraggio di Azure Vedere [log Analytics l'esportazione dei dati dell'area di lavoro in monitoraggio di Azure (anteprima)](logs-data-export.md)
- Esportare una volta usando un'app per la logica. Vedere [connettore dei log di monitoraggio di Azure per app per la logica e automazione dell'alimentazione](logicapp-flow-connector.md).
- Esportare una volta nel computer locale usando lo script di PowerShell. Vedere [Invoke-AzOperationalInsightsQueryExport]] ( https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport) .

## <a name="overview"></a>Panoramica
Questa procedura usa il [connettore log di monitoraggio di Azure](https://docs.microsoft.com/connectors/azuremonitorlogs/) che consente di eseguire una query di log da un'app per la logica e di usare l'output in altre azioni nel flusso di lavoro. Il [connettore di archiviazione BLOB di Azure](https://docs.microsoft.com/connectors/azureblob/) viene usato in questa procedura per inviare l'output della query ad archiviazione di Azure. Le altre azioni sono descritte nelle sezioni riportate di seguito.

![Panoramica dell'app per la logica](media/logs-export-logicapp/logic-app-overview.png)

Quando si esportano dati da un'area di lavoro di Log Analytics, è necessario filtrare e aggregare i dati di log e ottimizzare la query per limitare la quantità di dati elaborati dal flusso di lavoro dell'app per la logica ai dati necessari. Se ad esempio è necessario archiviare gli eventi di accesso, è possibile filtrare gli eventi richiesti e proiettare solo i campi obbligatori con la query seguente: 

```json
SecurityEvent
| where EventID == 4624 or EventID == 4625
| project TimeGenerated , Account , AccountType , Computer
```

Quando si esportano i dati in base a una pianificazione, usare la funzione ingestion_time () nella query per assicurarsi che non si perdano i dati in arrivo in ritardo. Se i dati vengono posticipati a causa di problemi di rete o della piattaforma, l'uso del tempo di inserimento garantisce che questo venga incluso nella successiva esecuzione dell'app per la logica. Per un esempio, vedere l' [azione Aggiungi log di monitoraggio di Azure](#add-azure-monitor-logs-action) .

## <a name="prerequisites"></a>Prerequisiti
Di seguito sono riportati i prerequisiti che devono essere completati prima di completare questa procedura.

- Area d lavoro Log Analytics. L'utente che crea l'app per la logica deve disporre almeno dell'autorizzazione di lettura per l'area di lavoro. 
- Account di archiviazione di Azure. L'account di archiviazione non deve trovarsi nella stessa sottoscrizione dell'area di lavoro Log Analytics. L'utente che crea l'app per la logica deve avere l'autorizzazione di scrittura per l'account di archiviazione. 


## <a name="connector-limits"></a>Limiti del connettore
Log Analytics area di lavoro e le query di log in monitoraggio di Azure sono servizi di multi-tenant che includono limiti che proteggono e isolano i clienti e mantengono la qualità del servizio. Quando si eseguono query per una grande quantità di dati, è necessario considerare i limiti seguenti, che possono influire sulla modalità di configurazione della ricorrenza dell'app per la logica e della query di log:

- Le query di log non possono restituire più di 500.000 righe.
- Le query di log non possono restituire più di 64 milioni byte.
- Per impostazione predefinita, le query di log non possono essere eseguite più di 10 minuti. 
- Log Analytics connettore è limitato a 100 chiamata al minuto.


## <a name="create-container-in-the-storage-account"></a>Creare un contenitore nell'account di archiviazione
Usare la procedura descritta in [creare un contenitore](../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) per aggiungere un contenitore all'account di archiviazione per conservare i dati esportati. Il nome usato per il contenitore in questo articolo è **loganalytics-data**, ma è possibile usare qualsiasi nome.


## <a name="create-logic-app"></a>Creare un'app per la logica

Passare a **app** per la logica nella portale di Azure e fare clic su **Aggiungi**. Selezionare una **sottoscrizione**, un **gruppo di risorse**e un' **area** per archiviare la nuova app per la logica e assegnarle un nome univoco. È possibile attivare **log Analytics** impostazione per raccogliere informazioni sui dati e gli eventi di runtime, come descritto in [configurare i log di monitoraggio di Azure e raccogliere i dati di diagnostica per le app per la logica di Azure](../../logic-apps/monitor-logic-apps-log-analytics.md). Questa impostazione non è obbligatoria per l'uso del connettore log di monitoraggio di Azure.

![Creare l'app per la logica](media/logs-export-logicapp/create-logic-app.png)


Fare clic su **Verifica + crea** e quindi su **Crea**. Al termine della distribuzione, fare clic su **Vai alla risorsa** per aprire la **finestra di progettazione delle app**per la logica.

## <a name="create-a-trigger-for-the-logic-app"></a>Creare un trigger per l'app per la logica
In **inizia con un trigger comune**selezionare **ricorrenza**. Verrà creata un'app per la logica che viene eseguita automaticamente a intervalli regolari. Nella casella **frequenza** dell'azione selezionare **ora** e nella casella **intervallo** immettere **1** per eseguire il flusso di lavoro una volta al giorno.

![Azione di ricorrenza](media/logs-export-logicapp/recurrence-action.png)


### <a name="add-azure-monitor-logs-action"></a>Azione Aggiungi log di monitoraggio di Azure
Fare clic su **+ nuovo passaggio** per aggiungere un'azione che viene eseguita dopo l'azione di ricorrenza. In **scegliere un'azione**Digitare **monitoraggio di Azure** e quindi selezionare **log di monitoraggio di Azure**.

![Azione log di monitoraggio di Azure](media/logs-export-logicapp/select-azure-monitor-connector.png)

Fare clic su **log Analytics di Azure: eseguire query ed elencare i risultati**.

![Screenshot di una nuova azione aggiunta a un passaggio nella finestra di progettazione dell'app per la logica. Log di monitoraggio di Azure è evidenziato in scegliere un'azione.](media/logs-export-logicapp/select-query-action-list.png)

Verrà richiesto di selezionare un tenant e di concedere l'accesso all'area di lavoro Log Analytics con l'account che verrà usato dal flusso di lavoro per eseguire la query.


## <a name="add-azure-monitor-logs-action"></a>Azione Aggiungi log di monitoraggio di Azure
L'azione log di monitoraggio di Azure consente di specificare la query da eseguire. La query di log usata in questo esempio è ottimizzata per la ricorrenza oraria e raccoglie i dati inseriti per il tempo di esecuzione specifico. Se, ad esempio, il flusso di lavoro viene eseguito a 4:35, l'intervallo di tempo sarà 4:00 a 5:00. Se si modifica l'app per la logica per l'esecuzione con una frequenza diversa, è necessario modificare anche la query. Se, ad esempio, si imposta l'esecuzione giornaliera della ricorrenza, impostare startTime nella query su startofday (make_datetime (anno, mese, giorno, 0, 0)). 

Selezionare la **sottoscrizione** e il **gruppo di risorse** per l'area di lavoro log Analytics. Selezionare *log Analytics area di lavoro* per il **tipo di risorsa** e quindi selezionare il nome dell'area di lavoro in **nome risorsa**.

Aggiungere la query di log seguente alla finestra **Query**.  

```Kusto
let dt = now();
let year = datetime_part('year', dt);
let month = datetime_part('month', dt);
let day = datetime_part('day', dt);
let hour = datetime_part('hour', dt);
let startTime = make_datetime(year,month,day,hour,0)-1h;
let endTime = startTime + 1h - 1tick;
AzureActivity
| where ingestion_time() between(startTime .. endTime)
| project 
    TimeGenerated,
    BlobTime = startTime, 
    OperationName ,
    OperationNameValue ,
    Level ,
    ActivityStatus ,
    ResourceGroup ,
    SubscriptionId ,
    Category ,
    EventSubmissionTimestamp ,
    ClientIpAddress = parse_json(HTTPRequest).clientIpAddress ,
    ResourceId = _ResourceId 
```

L' **intervallo di tempo** specifica i record che verranno inclusi nella query in base alla colonna **TimeGenerated** . Deve essere impostato su un valore uguale o superiore all'intervallo di tempo selezionato nella query. Poiché questa query non usa la colonna **TimeGenerated** , l'opzione **set in query** non è disponibile. Per ulteriori informazioni sull'intervallo di tempo, vedere [ambito della query](../log-query/scope.md) . 

Selezionare le **ultime 4 ore** per l' **intervallo di tempo**. In questo modo, tutti i record con tempi di inserimento maggiori di **TimeGenerated** verranno inclusi nei risultati.
   
![Screenshot delle impostazioni per la nuova azione log di monitoraggio di Azure denominata Esegui query e visualizza risultati.](media/logs-export-logicapp/run-query-list-action.png)


### <a name="add-parse-json-activity-optional"></a>Aggiungi attività parse JSON (facoltativo)
L'output dell'azione **Esegui query e risultati elenco** è formattato in JSON. È possibile analizzare questi dati e manipolarli come parte della preparazione per l'azione **compose** . 

È possibile specificare uno schema JSON che descriva il payload che si prevede di ricevere. La finestra di progettazione consente di analizzare il contenuto JSON con questo schema e genera i token descrittivi che rappresentano le proprietà presenti nel contenuto JSON. È possibile quindi fare riferimento a tali proprietà e usarle facilmente nel flusso di lavoro dell'app per la logica. 


Fare clic su **+ nuovo passaggio**e quindi su **+ Aggiungi un'azione**. In **scegliere un'azione**Digitare **JSON** e quindi selezionare **analizza JSON**.

![Selezionare l'attività analizza JSON](media/logs-export-logicapp/select-parse-json.png)

Fare clic nella casella **contenuto** per visualizzare un elenco di valori delle attività precedenti. Selezionare **corpo** dall'azione **Esegui query ed elenca risultati** . Si tratta dell'output della query di log.

[![Selezionare il corpo](media/logs-export-logicapp/select-body.png)](media/logs-export-logicapp/select-body.png#lightbox)

5.  Fare clic su **USA payload di esempio per generare lo schema**. Eseguire la query di log e copiare l'output da usare per il payload di esempio.  Per la query di esempio, è possibile usare l'output seguente:


```json
{
    "TimeGenerated": "2020-09-29T23:11:02.578Z",
    "BlobTime": "2020-09-29T23:00:00Z",
    "OperationName": "Returns Storage Account SAS Token",
    "OperationNameValue": "MICROSOFT.RESOURCES/DEPLOYMENTS/WRITE",
    "Level": "Informational",
    "ActivityStatus": "Started",
    "ResourceGroup": "monitoring",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000",
    "Category": "Administrative",
    "EventSubmissionTimestamp": "2020-09-29T23:11:02Z",
    "ClientIpAddress": "192.168.1.100",
    "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/monitoring/providers/microsoft.storage/storageaccounts/my-storage-account"
}
```



![Analizza payload JSON](media/logs-export-logicapp/parse-json-payload.png)

## <a name="add-the-compose-action"></a>Aggiungere l'azione compose
L'azione **compose** accetta l'output JSON analizzato e crea l'oggetto che è necessario archiviare nel BLOB.

Fare clic su **+ nuovo passaggio**e quindi su **+ Aggiungi un'azione**. In **scegliere un'azione**Digitare **compose** , quindi selezionare l'azione **Componi** .

![Selezionare l'azione Componi](media/logs-export-logicapp/select-compose.png)


Fare clic sulla casella **input** per visualizzare un elenco di valori delle attività precedenti. Selezionare **corpo** dall'azione **analizza JSON** . Si tratta dell'output analizzato della query di log.

[![Selezionare il corpo per l'azione Componi](media/logs-export-logicapp/select-body-compose.png)](media/logs-export-logicapp/select-body-compose.png#lightbox)


## <a name="add-the-create-blob-action"></a>Aggiungere l'azione crea BLOB
L'azione crea BLOB scrive il JSON composto nell'archivio.

Fare clic su **+ nuovo passaggio**e quindi su **+ Aggiungi un'azione**. In **scegliere un'azione**Digitare **BLOB** , quindi selezionare l'azione **Crea BLOB** .

![Selezionare Crea BLOB](media/logs-export-logicapp/select-create-blob.png)

Digitare un nome per la connessione all'account di archiviazione in **nome connessione** , quindi fare clic sull'icona della cartella nella casella **percorso cartella** per selezionare il contenitore nell'account di archiviazione. Fare clic sul **nome del BLOB** per visualizzare un elenco di valori delle attività precedenti. Fare clic su **espressione** e immettere un'espressione corrispondente all'intervallo di tempo. Per questa query che viene eseguita ogni ora, l'espressione seguente imposta il nome del BLOB in base all'ora precedente: 

```json
subtractFromTime(formatDateTime(utcNow(),'yyyy-MM-ddTHH:00:00'), 1,'Hour')
```

[![Espressione BLOB](media/logs-export-logicapp/blob-expression.png)](media/logs-export-logicapp/blob-expression.png#lightbox)

Fare clic sulla casella **contenuto BLOB** per visualizzare un elenco di valori delle attività precedenti e quindi selezionare **output** nella sezione **compose** .


![Crea espressione BLOB](media/logs-export-logicapp/create-blob.png)


## <a name="test-the-logic-app"></a>Testare l'app per la logica
Testare il flusso di lavoro facendo clic su **Esegui**. Se il flusso di lavoro contiene errori, verrà indicato nel passaggio con il problema. È possibile visualizzare le esecuzioni ed eseguire il drill-down in ogni passaggio per visualizzare l'input e l'output per esaminare gli errori. Vedere [risolvere i problemi e diagnosticare gli errori del flusso di lavoro in app per la logica di Azure](../../logic-apps/logic-apps-diagnosing-failures.md) se necessario

[![Cronologia esecuzioni](media/logs-export-logicapp/runs-history.png)](media/logs-export-logicapp/runs-history.png#lightbox)


## <a name="view-logs-in-storage"></a>Visualizzare i log nell'archiviazione
Passare al menu **account di archiviazione** nel portale di Azure e selezionare l'account di archiviazione. Fare clic sul riquadro **BLOB** e selezionare il contenitore specificato nell'azione crea BLOB. Selezionare uno dei BLOB e quindi **modifica BLOB**.

[![Dati BLOB](media/logs-export-logicapp/blob-data.png)](media/logs-export-logicapp/blob-data.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni sulle [query di log in Monitoraggio di Azure](../log-query/log-query-overview.md).
- Altre informazioni sulle [app](../../logic-apps/index.yml) per la logica
- Altre informazioni su [Power automatizzate](https://flow.microsoft.com).
