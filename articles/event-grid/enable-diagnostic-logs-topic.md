---
title: 'Griglia di eventi di Azure: abilitare i log di diagnostica per argomenti o domini'
description: Questo articolo fornisce istruzioni dettagliate su come abilitare i log di diagnostica per un argomento di griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 04/27/2020
ms.author: spelluru
ms.openlocfilehash: 13a2168c854475b841b0ebc52bb678c7ca22a1bb
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82626463"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Abilitare i log di diagnostica per gli argomenti o i domini di griglia di eventi di Azure
Le impostazioni di diagnostica consentono agli utenti di griglia di eventi di acquisire e visualizzare i log degli **errori di pubblicazione e recapito** in un account di archiviazione, un hub eventi o un'area di lavoro log Analytics. In questo articolo vengono fornite istruzioni dettagliate per l'abilitazione di queste impostazioni in un argomento di griglia di eventi.

## <a name="prerequisites"></a>Prerequisiti

- Un argomento di griglia di eventi con provisioning
- Destinazione sottoposta a provisioning per l'acquisizione dei log di diagnostica. Può essere una delle seguenti destinazioni nella stessa posizione dell'argomento di griglia di eventi:
    - Account di archiviazione di Azure
    - Hub eventi
    - Area di lavoro Log Analytics

## <a name="steps-for-enabling-diagnostic-logs-for-a-topic"></a>Passaggi per l'abilitazione dei log di diagnostica per un argomento

> [!NOTE]
> Nella procedura riportata di seguito vengono fornite istruzioni dettagliate per l'abilitazione dei log di diagnostica per un argomento. I passaggi per l'abilitazione dei log di diagnostica per un dominio sono molto simili. Nel passaggio 2 passare al **dominio** di griglia di eventi nel portale di Azure.  

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'argomento di griglia di eventi per il quale si desidera abilitare le impostazioni del log di diagnostica. 
3. Selezionare **impostazioni di diagnostica** in **monitoraggio** nel menu a sinistra.
4. Nella pagina **impostazioni di diagnostica** selezionare **Aggiungi nuova impostazione di diagnostica**. 
    
    ![Pulsante Aggiungi impostazione diagnostica](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Specificare un **nome** per l'impostazione di diagnostica. 
7. Selezionare le opzioni **DeliveryFailures** e **PublishFailures** nella sezione **log** . 
    ![Selezionare gli errori](./media/enable-diagnostic-logs-topic/log-failures.png)
6. Abilitare una o più destinazioni di acquisizione per i log, quindi configurarle selezionando una risorsa di acquisizione creata in precedenza. 
    - Se si seleziona **archivia in un account di archiviazione**, selezionare **account di archiviazione-configura**e quindi selezionare l'account di archiviazione nella sottoscrizione di Azure. 

        ![Archivia in un account di archiviazione di Azure](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se si seleziona **flusso in un hub eventi**, selezionare **Hub eventi-configura**, quindi selezionare lo spazio dei nomi di hub eventi, l'hub eventi e i criteri di accesso. 
        ![Trasmettere a un hub eventi](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se si seleziona **Invia a log Analytics**, selezionare l'area di lavoro log Analytics.
        ![Invia a Log Analytics](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Selezionare **Salva**. Selezionare quindi **X** nell'angolo destro per chiudere la pagina. 
9. A questo punto, nella pagina **impostazioni di diagnostica** , verificare che venga visualizzata una nuova voce nella tabella impostazioni di **diagnostica** . 
    ![Impostazione diagnostica nell'elenco](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     È anche possibile abilitare la raccolta di tutte le metriche per l'argomento. 

## <a name="view-diagnostic-logs-in-azure-storage"></a>Visualizzare i log di diagnostica in archiviazione di Azure 

1. Quando si Abilita un account di archiviazione come destinazione di acquisizione e griglia di eventi avvia la creazione dei log di diagnostica, verranno visualizzati i nuovi contenitori denominati **Insights-logs-deliveryfailures** e **Insights-logs-publishfailures** nell'account di archiviazione. 

    ![Contenitori di archiviazione per i log di diagnostica](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Quando si Esplora uno dei contenitori, si otterrà un BLOB in formato JSON. Il file contiene le voci di log per un errore di recapito o per un errore di pubblicazione. Il percorso di navigazione rappresenta il **resourceId** dell'argomento di griglia di eventi e il timestamp (livello minuto) a partire dal momento in cui sono state emesse le voci di log. Il file BLOB/JSON, che è scaricabile, al termine aderisce allo schema descritto nella sezione successiva. 

    [![File JSON nell'archiviazione](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Nel file JSON dovrebbe essere visualizzato contenuto simile all'esempio seguente: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, systemId=17284f7c-0044-46fb-84b7-59fda5776017, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```

## <a name="next-steps"></a>Passaggi successivi
Per lo schema di log e altre informazioni concettuali sui log di diagnostica per argomenti o domini, vedere [log di diagnostica](diagnostic-logs.md).
