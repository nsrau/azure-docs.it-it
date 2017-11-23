---
title: Schema di eventi della sottoscrizione per Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi della sottoscrizione con Griglia di eventi di Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7dc10d0cc73960fac4759a0cebec8d294cf1b463
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Schema di eventi di Griglia di eventi di Azure per le sottoscrizioni

Questo articolo illustra le proprietà e lo schema per gli eventi della sottoscrizione di Azure. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

I gruppi di risorse e le sottoscrizioni di Azure generano gli stessi tipi di evento. I tipi di evento sono relativi alle modifiche nelle risorse. La differenza principale è che i gruppi di risorse generano eventi per le risorse all'interno del gruppo di risorse, mentre le sottoscrizioni di Azure generano eventi per le risorse all'interno della sottoscrizione.

## <a name="available-event-types"></a>Tipi di evento disponibili

Le sottoscrizioni di Azure generano eventi di gestione da Azure Resource Manager, ad esempio quando viene creata una macchina virtuale o viene eliminato un account di archiviazione.

| Tipo evento | Descrizione |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito positivo. |
| Microsoft.Resources.ResourceWriteFailure | Generato quando un'operazione di creazione o aggiornamento di una risorsa ha esito negativo. |
| Microsoft.Resources.ResourceWriteCancel | Generato quando un'operazione di creazione o aggiornamento di una risorsa viene annullata. |
| Microsoft.Resources.ResourceDeleteSuccess | Generato quando un'operazione di eliminazione di una risorsa ha esito positivo. |
| Microsoft.Resources.ResourceDeleteFailure | Generato quando un'operazione di eliminazione di una risorsa ha esito negativo. |
| Microsoft.Resources.ResourceDeleteCancel | Generato quando un'operazione di eliminazione di una risorsa viene annullata. Questo evento si verifica quando viene annullata la distribuzione di un modello. |

## <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di un evento creato da una risorsa: 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

Lo schema di un evento eliminato da una risorsa è simile:

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| argomento | string | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. |
| subject | string | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento della sottoscrizione. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | Tipo | Descrizione |
| -------- | ---- | ----------- |
| autorizzazione | string | L'autorizzazione richiesta per l'operazione. |
| claims | string | Le proprietà delle attestazioni. |
| correlationId | string | Un ID operazione per la risoluzione dei problemi. |
| httpRequest | string | I dettagli dell'operazione. |
| resourceProvider | string | Il provider della risorsa che esegue l'operazione. |
| resourceUri | string | L'URI della risorsa nell'operazione. |
| operationName | string | L'operazione che è stata eseguita. |
| status | string | Lo stato dell'operazione. |
| subscriptionId | string | L'ID sottoscrizione della risorsa. |
| TenantId | string | L'ID tenant della risorsa. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md).
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
