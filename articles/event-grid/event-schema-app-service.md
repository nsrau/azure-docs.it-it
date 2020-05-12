---
title: Servizio app Azure come origine griglia di eventi
description: Questo articolo descrive come usare app Azure servizio come origine evento di griglia di eventi. Che fornisce lo schema e i collegamenti agli articoli di esercitazione e procedure.
services: event-grid
author: jasonfreeberg
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: jafreebe
ms.openlocfilehash: 19cb7d7cfdb5c5ae61aba0f75d06476b40bdd6d7
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116935"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>app Azure servizio come origine griglia di eventi

Questo articolo fornisce le proprietà e lo schema per gli eventi del servizio app Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Fornisce anche un elenco di guide introduttive ed esercitazioni per usare app Azure servizio come origine evento.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

app Azure servizio genera i tipi di eventi seguenti

|    Tipo evento                                             |    Descrizione                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft. Web/sites. BackupOperationStarted             |    Attivato all'avvio di un backup                             |
|    Microsoft. Web/sites. BackupOperationCompleted           |    Attivato al termine di un backup                           |
|    Microsoft. Web/sites. BackupOperationFailed              |    Attivato in caso di errore di un backup                              |
|    Microsoft. Web/sites. RestoreOperationStarted            |    Attivato al momento dell'avvio di un ripristino da un backup        |
|    Microsoft. Web/sites. RestoreOperationCompleted          |    Attivato al completamento di un ripristino da un backup      |
|    Microsoft. Web/sites. RestoreOperationFailed             |    Attivato quando un ripristino da un backup non è riuscito         |
|    Microsoft. Web/sites. SlotSwapStarted                    |    Attivato al momento dell'avvio di uno scambio di slot                          |
|    Microsoft. Web/sites. SlotSwapCompleted                  |    Attivato quando uno scambio di slot è stato completato                      |
|    Microsoft. Web/sites. SlotSwapFailed                     |    Attivato in caso di errore di uno scambio di slot                           |
|    Microsoft. Web/sites. SlotSwapWithPreviewStarted         |    Attivato quando viene avviato uno scambio di slot con l'anteprima           |
|    Microsoft. Web/sites. SlotSwapWithPreviewCancelled       |    Attivato quando uno scambio di slot con anteprima è stato annullato    |
|    Microsoft. Web/sites. AppUpdated. riavviato               |    Attivato al riavvio di un sito                      |
|    Microsoft. Web/sites. AppUpdated. Stopped                 |    Attivato quando un sito è stato arrestato                          |
|    Microsoft. Web/sites. AppUpdated.ChangedAppSettings      |    Attivato quando vengono modificate le impostazioni dell'app di un sito             |
|    Microsoft. Web/serverfarms. AppServicePlanUpdated        |    Attivato quando viene aggiornato un piano di servizio app                 |

### <a name="the-contents-of-an-event-response"></a>Contenuto di una risposta di evento

Quando viene attivato un evento, il servizio griglia di eventi invia i dati relativi all'evento all'endpoint di sottoscrizione.
Questa sezione contiene un esempio dell'aspetto dei dati per ogni evento. Ogni evento presenta i dati di primo livello seguenti:

|     Proprietà          |     Type     |     Descrizione                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    argomento              |    string    |    Percorso completo della risorsa per l'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi.                                      |
|    subject            |    string    |    Percorso definito dall'editore per l'oggetto dell'evento.                                                                                              |
|    eventType          |    string    |    Uno dei tipi di evento registrati per l'origine evento.                                                                                  |
|    eventTime          |    string    |    Ora di generazione dell'evento in base all'ora UTC del provider.                                                                         |
|    ID                 |    string    |    Identificatore univoco dell'evento.                                                                                                            |
|    data               |    object    |    Dati relativi all'evento di archiviazione BLOB.                                                                                                                    |
|    dataVersion        |    string    |    Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore.                                                          |
|    metadataVersion    |    string    |    Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Griglia di eventi fornisce questo valore.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { "action": "Started" },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio dell'azione nell'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId         |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.RestoreOperationStarted,
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data: {
        "appEventTypeDetail": { 
            "action": "Started" 
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "POST"
    }
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio dell'azione nell'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId         |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data:{
        appEventTypeDetail:null,
        siteName:'<site-name>',
        clientRequestId:'922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId:'9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId:'765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb:'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio dell'azione nell'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId         |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |
|    sourceSlot              |    string    |    Slot di origine dello scambio                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime:'2020-01-28T18:26:51.7194887Z',
    data:{
        appEventTypeDetail:null,
        siteName:'<site-name >',
        clientRequestId:'922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId:'9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId:'765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb:'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic:'/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion:'1',
    metaDataVersion:'1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio dell'azione nell'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId         |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated. restarted, AppUpdated. Stopped, AppUpdated. ChangedAppSettings

```js
{
    id: 'b74ea56b-2a3f-4de5-a5d7-38e60c81cf23',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.AppUpdated',
    eventTime: '2020-01-28T18:22:30.2760952Z',
    data: {
        appEventTypeDetail: {
            action: 'Stopped'
        },
        siteName: '<site-name>',
        clientRequestId: '64a5e0aa-7cee-4ff1-9093-b9197b820014',
        correlationRequestId: '25bb36a5-8f6c-4f04-b615-e9a0ee045756',
        requestId: 'f2e8eb3f-b190-42de-b99e-6acefe587374',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/ <webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/ Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Di seguito sono elencate le proprietà dell'oggetto dati:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio dell'azione nell'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId         |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms. AppServicePlanUpdated

```js
{
   "id":"56501672-9150-40e1-893a-18420c7fdbf7",
   "subject":"/Microsoft.Web/serverfarms/<plan-name>",
   "eventType":"Microsoft.Web.AppServicePlanUpdated",
   "eventTime":"2020-01-28T18:22:23.5516004Z",
   "data":{
        "serverFarmEventTypeDetail":{
            "stampKind":"Public",
            "action":"Updated",
            "status":"Started"
        },
        "serverFarmId":"0",
        "sku":{
            "name":"P1v2",
            "tier":"PremiumV2",
            "size":"P1v2",
            "family":"Pv2",
            "capacity":1
        },
        "clientRequestId":"8f880321-a991-45c7-b743-6ff63fe4c004",
        "correlationRequestId":"1995c3be-ba7f-4ccf-94af-516df637ec8a",
        "requestId":"b973a8e6-6949-4783-b44c-ac778be831bb",
        "address":"/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        "verb":"PUT"
   },
   "topic":"/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/ serverfarms/<serverfarm-name>",
   "dataVersion":"1",
   "metaDataVersion":"1"
}
```

Di seguito sono elencate le proprietà dell'oggetto dati:

|    Proprietà                         |    Type      |    Descrizione                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    object    |    Dettaglio dell'azione nel piano di servizio app                                                                          |
|    stampKind                        |    string    |    Tipo di ambiente in cui il piano di servizio app è                                                                     |
|    action                           |    string    |    Tipo di azione nel piano di servizio app                                                                            |
|    status                           |    string    |    Stato dell'operazione nel piano di servizio app                                                                   |
|    sku                              |    object    |    SKU del piano di servizio app                                                                                       |
|    name                             |    string    |    nome del piano di servizio app                                                                                      |
|    Livello                             |    string    |    livello del piano di servizio app                                                                                      |
|    Dimensione                             |    string    |    dimensioni del piano di servizio app                                                                                      |
|    Famiglia                           |    string    |    famiglia di piani di servizio app                                                                                        |
|    Capacità                         |    string    |    capacità del piano di servizio app                                                                                      |
|    action                           |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                             |    string    |    nome del sito Web che ha avuto questo evento                                                                          |
|    clientRequestId                  |    string    |    ID richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId             |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId                        |    string    |    ID richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                          |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                             |    string    |    Verbo HTTP di questa operazione                                                                                       |
