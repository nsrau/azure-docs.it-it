---
title: Schema di eventi del gruppo di risorse di Griglia di eventi di Azure
description: "Descrive le proprietà disponibili per gli eventi del gruppo di risorse con Griglia di eventi di Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 109f5af5cc1647cebee805c3141f4bc83c73bcfc
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Schema di eventi di Griglia di eventi di Azure per i gruppi di risorse

Questo articolo illustra le proprietà e lo schema per gli eventi del gruppo di risorse. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

I gruppi di risorse e le sottoscrizioni di Azure generano gli stessi tipi di evento. I tipi di evento sono relativi alle modifiche nelle risorse. La differenza principale è che i gruppi di risorse generano eventi per le risorse all'interno del gruppo di risorse, mentre le sottoscrizioni di Azure generano eventi per le risorse all'interno della sottoscrizione. 

## <a name="available-event-types"></a>Tipi di evento disponibili

I gruppi di risorse generano eventi di gestione da Azure Resource Manager, ad esempio quando viene creata una VM o viene eliminato un account di archiviazione.

| Tipo evento | DESCRIZIONE |
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
    "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
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
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

Lo schema di un evento eliminato da una risorsa è simile:

```json
[{
  "topic":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}",
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
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Proprietà degli eventi

Un evento presenta i seguenti dati di primo livello:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| argomento | stringa | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | stringa | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | stringa | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | stringa | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | stringa | Identificatore univoco dell'evento. |
| data | object | Dati dell'evento del gruppo di risorse. |
| dataVersion | stringa | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | stringa | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Di seguito sono elencate le proprietà dell'oggetto dati:

| Proprietà | type | DESCRIZIONE |
| -------- | ---- | ----------- |
| autorizzazione | stringa | L'autorizzazione richiesta per l'operazione. |
| claims | stringa | Le proprietà delle attestazioni. |
| correlationId | stringa | Un ID operazione per la risoluzione dei problemi. |
| httpRequest | stringa | I dettagli dell'operazione. |
| resourceProvider | stringa | Il provider della risorsa che esegue l'operazione. |
| resourceUri | stringa | L'URI della risorsa nell'operazione. |
| operationName | stringa | L'operazione che è stata eseguita. |
| status | stringa | Lo stato dell'operazione. |
| subscriptionId | stringa | L'ID sottoscrizione della risorsa. |
| TenantId | stringa | L'ID tenant della risorsa. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
