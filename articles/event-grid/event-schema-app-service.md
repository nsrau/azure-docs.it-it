---
title: Servizio app di Azure come origine di Griglia di eventi
description: Questo articolo descrive come usare il servizio app di Azure come origine evento di Griglia di eventi. Offre lo schema e i collegamenti ad articoli di esercitazione e procedure.
author: jasonfreeberg
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jafreebe
ms.openlocfilehash: 2465b2f260ed6c174b762fcf64a71100a148254d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86106712"
---
# <a name="azure-app-service-as-an-event-grid-source"></a>Servizio app di Azure come origine di Griglia di eventi

Questo articolo illustra le proprietà e lo schema per gli eventi del servizio app di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md). Mette anche a disposizione un elenco di avvii rapidi ed esercitazioni per usare il servizio app di Azure come origine evento.

## <a name="event-grid-event-schema"></a>Schema di eventi di Griglia di eventi

### <a name="available-event-types"></a>Tipi di evento disponibili

Il servizio app di Azure genera i tipi di evento seguenti

|    Tipo evento                                             |    Descrizione                                                     |
|-----------------------------------------------------------|--------------------------------------------------------------------|
|    Microsoft.Web/sites.BackupOperationStarted             |    Attivato all'avvio di un backup                             |
|    Microsoft.Web/sites.BackupOperationCompleted           |    Attivato al termine di un backup                           |
|    Microsoft.Web/sites.BackupOperationFailed              |    Attivato in caso di errore di un backup                              |
|    Microsoft.Web/sites.RestoreOperationStarted            |    Attivato all'avvio di un ripristino da un backup        |
|    Microsoft.Web/sites.RestoreOperationCompleted          |    Attivato al termine di un ripristino da un backup      |
|    Microsoft.Web/sites.RestoreOperationFailed             |    Attivato se un ripristino da un backup non è riuscito         |
|    Microsoft.Web/sites.SlotSwapStarted                    |    Attivato all'avvio di uno scambio di slot                          |
|    Microsoft.Web/sites.SlotSwapCompleted                  |    Attivato al termine di uno scambio di slot                      |
|    Microsoft.Web/sites.SlotSwapFailed                     |    Attivato se uno scambio di slot non è riuscito                           |
|    Microsoft.Web/sites.SlotSwapWithPreviewStarted         |    Attivato all'avvio di uno scambio di slot con anteprima           |
|    Microsoft.Web/sites.SlotSwapWithPreviewCancelled       |    Attivato se uno scambio di slot con anteprima è stato annullato    |
|    Microsoft.Web/sites.AppUpdated.Restarted               |    Attivato al riavvio di un sito                      |
|    Microsoft.Web/sites.AppUpdated.Stopped                 |    Attivato all'arresto di un sito                          |
|    Microsoft.Web/sites.AppUpdated.ChangedAppSettings      |    Attivato se le impostazioni delle app del sito sono state modificate             |
|    Microsoft.Web/serverfarms.AppServicePlanUpdated        |    Attivato se un piano del servizio app è stato aggiornato                 |

### <a name="the-contents-of-an-event-response"></a>Contenuto della risposta di un evento

Quando viene attivato un evento, il servizio Griglia di eventi invia i dati relativi all'evento all'endpoint di sottoscrizione.
Questa sezione contiene un esempio che illustra l'aspetto dei dati per ogni evento. Ogni evento presenta i dati di primo livello seguenti:

|     Proprietà          |     Type     |     Descrizione                                                                                                                                |
|-----------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------|
|    argomento              |    string    |    Percorso completo della risorsa all'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi.                                      |
|    subject            |    string    |    Percorso definito dall'editore all'oggetto dell'evento.                                                                                              |
|    eventType          |    string    |    Uno dei tipi di evento registrati per l'origine evento.                                                                                  |
|    eventTime          |    string    |    Ora di generazione dell'evento in base all'ora UTC del provider.                                                                         |
|    id                 |    string    |    Identificatore univoco dell'evento.                                                                                                            |
|    data               |    object    |    Dati relativi all'evento di archiviazione BLOB.                                                                                                                    |
|    dataVersion        |    string    |    Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore.                                                          |
|    metadataVersion    |    string    |    Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi.    |

#### <a name="backupoperationstarted-backupoperationcompleted-backupoperationfailed"></a>BackupOperationStarted, BackupOperationCompleted, BackupOperationFailed

```js
{
    id:'7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject:'/Microsoft.Web/sites/<site-name>',
    eventType:'Microsoft.Web.BackupOperationStarted',
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
|    appEventTypeDetail      |    object    |    Dettaglio di action per l'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId         |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="restoreoperationstarted-restoreoperationcompleted-restoreoperationfailed"></a>RestoreOperationStarted, RestoreOperationCompleted, RestoreOperationFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.RestoreOperationStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: { 
            action: "Started" 
        },
        siteName: "<site-name>",
        clientRequestId: "None",
        correlationRequestId: "None",
        requestId: "292f499d-04ee-4066-994d-c2df57b99198",
        address: "None",
        verb: "POST"
    }
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio di action per l'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId         |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="slotswapstarted-slotswapcompleted-slotswapfailed"></a>SlotSwapStarted, SlotSwapCompleted, SlotSwapFailed

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio di action per l'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId         |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |
|    sourceSlot              |    string    |    Slot di origine dello scambio                                                                                       |

#### <a name="slotswapwithpreviewstarted-slotswapwithpreviewcancelled"></a>SlotSwapWithPreviewStarted, SlotSwapWithPreviewCancelled

```js
{
    id: '7c5d6de5-eb70-4de2-b788-c52a544e68b8',
    subject: '/Microsoft.Web/sites/<site-name>',
    eventType: 'Microsoft.Web.SlotSwapWithPreviewStarted',
    eventTime: '2020-01-28T18:26:51.7194887Z',
    data: {
        appEventTypeDetail: null,
        siteName: '<site-name>',
        clientRequestId: '922f4841-20d9-4dd6-8c5b-23f0d85e5592',
        correlationRequestId: '9ac46505-2b8a-4e06-834c-05ffbe2e8c3a',
        requestId: '765117aa-eaf8-4bd2-a644-1dbf69c7b0fd',
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/slots?Command=SWAP&targetSlot=production',
        verb: 'POST'
        sourceSlot: "staging",
        targetSlot: "production"
    },
    topic: '/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

L'oggetto dati contiene le proprietà seguenti:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio di action per l'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId         |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="appupdatedrestarted-appupdatedstopped-appupdatedchangedappsettings"></a>AppUpdated.Restarted, AppUpdated.Stopped, AppUpdated.ChangedAppSettings

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
        address: '/websystems/WebSites/web/subscriptions/<id>/webspaces/<webspace>/sites/<site-name>/stop',
        verb: 'POST'
    },
    topic: '/subscriptions/<id>/resourceGroups/<group>/providers/Microsoft.Web/sites/<site-name>',
    dataVersion: '1',
    metaDataVersion: '1'
}
```

Di seguito sono elencate le proprietà dell'oggetto dati:

|    Proprietà                |    Type      |    Descrizione                                                                                                       |
|----------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appEventTypeDetail      |    object    |    Dettaglio di action per l'app                                                                                       |
|    action                  |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                    |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId         |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId    |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId               |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                 |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                    |    string    |    Verbo HTTP di questa operazione                                                                                       |

#### <a name="serverfarmsappserviceplanupdated"></a>Serverfarms.AppServicePlanUpdated

```js
{
   id: "56501672-9150-40e1-893a-18420c7fdbf7",
   subject: "/Microsoft.Web/serverfarms/<plan-name>",
   eventType: "Microsoft.Web.AppServicePlanUpdated",
   eventTime: "2020-01-28T18:22:23.5516004Z",
   data: {
        serverFarmEventTypeDetail: {
            stampKind: "Public",
            action: "Updated",
            status: "Started"
        },
        serverFarmId: "0",
        sku: {
            name: "P1v2",
            tier: "PremiumV2",
            size: "P1v2",
            family: "Pv2",
            capacity: 1
        },
        clientRequestId: "8f880321-a991-45c7-b743-6ff63fe4c004",
        correlationRequestId: "1995c3be-ba7f-4ccf-94af-516df637ec8a",
        requestId: "b973a8e6-6949-4783-b44c-ac778be831bb",
        address: "/websystems/WebSites/serverfarms/subscriptions/<id>/webspaces/<webspace-id>/serverfarms/<plan-name>/async",
        verb: "PUT"
   },
   topic: "/subscriptions/<id>/resourceGroups/<rg>/providers/Microsoft.Web/serverfarms/<serverfarm-name>",
   dataVersion: "1",
   metaDataVersion: "1"
}
```

Di seguito sono elencate le proprietà dell'oggetto dati:

|    Proprietà                         |    Type      |    Descrizione                                                                                                       |
|-------------------------------------|--------------|----------------------------------------------------------------------------------------------------------------------|
|    appServicePlanEventTypeDetail    |    object    |    Dettaglio di action per il piano di servizio app                                                                          |
|    stampKind                        |    string    |    Tipo di ambiente in cui si trova il piano di servizio app                                                                     |
|    action                           |    string    |    Tipo di azione per il piano di servizio app                                                                            |
|    status                           |    string    |    Stato dell'operazione per il piano di servizio app                                                                   |
|    sku                              |    object    |    SKU del piano di servizio app                                                                                       |
|    name                             |    string    |    nome del piano di servizio app                                                                                      |
|    Livello                             |    string    |    livello del piano di servizio app                                                                                      |
|    Dimensione                             |    string    |    dimensione del piano di servizio app                                                                                      |
|    Famiglia                           |    string    |    famiglia del piano di servizio app                                                                                        |
|    Capacità                         |    string    |    capacità del piano di servizio app                                                                                      |
|    action                           |    string    |    Tipo di azione dell'operazione                                                                                   |
|    name                             |    string    |    nome del sito Web in cui si è verificato questo evento                                                                          |
|    clientRequestId                  |    string    |    ID della richiesta client generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento         |
|    correlationRequestId             |    string    |    ID della richiesta di correlazione generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento    |
|    requestId                        |    string    |    ID della richiesta generato dal servizio app per l'operazione dell'API del sito che ha attivato questo evento                |
|    address                          |    string    |    URL della richiesta HTTP di questa operazione                                                                                |
|    verb                             |    string    |    Verbo HTTP di questa operazione                                                                                       |
