---
title: Schema di eventi del gruppo di risorse di Griglia di eventi di Azure
description: Descrive le proprietà disponibili per gli eventi del gruppo di risorse con Griglia di eventi di Azure
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 10/12/2018
ms.author: tomfitz
ms.openlocfilehash: ffc9eba251cbf4d9e2542791d90943ecdd1a972a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310573"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Schema di eventi di Griglia di eventi di Azure per i gruppi di risorse

Questo articolo illustra le proprietà e lo schema per gli eventi del gruppo di risorse. Per un'introduzione agli schemi di eventi, vedere [Schema di eventi di Griglia di eventi di Azure](event-schema.md).

I gruppi di risorse e le sottoscrizioni di Azure generano gli stessi tipi di evento. I tipi di evento sono correlati ad azioni o modifiche nelle risorse. La differenza principale è che i gruppi di risorse generano eventi per le risorse all'interno del gruppo di risorse, mentre le sottoscrizioni di Azure generano eventi per le risorse all'interno della sottoscrizione.

Gli eventi per le risorse vengono creati per le operazioni PUT, PATCH, POST e DELETE inviate a `management.azure.com`. Le operazioni GET non creano eventi. Le operazioni inviate al piano dati (ad esempio `myaccount.blob.core.windows.net`) non creano eventi. Gli eventi di azione forniscono i dati dell'evento per operazioni come la creazione di un elenco delle chiavi per una risorsa.

Quando si esegue la sottoscrizione per gli eventi di un gruppo di risorse, l'endpoint riceve tutti gli eventi da tale gruppo. Gli eventi possono includere l'evento che si desidera visualizzare, ad esempio l'aggiornamento di una macchina virtuale, ma anche eventi probabilmente non importanti, come ad esempio la scrittura di una nuova voce nella cronologia di distribuzione. È possibile ricevere tutti gli eventi nell'endpoint e scrivere il codice che elabora gli eventi che si vuole gestire. In alternativa, è possibile impostare un filtro quando si crea la sottoscrizione dell'evento.

Per gestire gli eventi a livello di programmazione, è possibile ordinare gli eventi esaminando il valore `operationName`. Ad esempio, l'endpoint dell'evento potrebbe elaborare solo gli eventi per le operazioni uguali a `Microsoft.Compute/virtualMachines/write` o `Microsoft.Storage/storageAccounts/write`.

L'oggetto dell'evento è l'ID risorsa della risorsa di destinazione dell'operazione. Per filtrare gli eventi per una risorsa, fornire l'ID della risorsa quando si crea la sottoscrizione dell'evento.  Per filtrare in base a un tipo di risorsa, usare un valore nel formato seguente: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Per un elenco di esercitazioni e script di esempio, vedere l'[origine eventi del gruppo di risorse](event-sources.md#resource-groups).

## <a name="available-event-types"></a>Tipi di evento disponibili

I gruppi di risorse generano eventi di gestione da Azure Resource Manager, ad esempio quando viene creata una VM o viene eliminato un account di archiviazione.

| Tipo evento | DESCRIZIONE |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Generato quando l'azione sulla risorsa viene annullata. |
| Microsoft.Resources.ResourceActionFailure | Generato quando l'azione sulla risorsa ha esito negativo. |
| Microsoft.Resources.ResourceActionSuccess | Generato quando l'azione sulla risorsa ha esito positivo. |
| Microsoft.Resources.ResourceDeleteCancel | Generato quando l'operazione di eliminazione viene annullata. Questo evento si verifica quando viene annullata la distribuzione di un modello. |
| Microsoft.Resources.ResourceDeleteFailure | Generato quando l'operazione di eliminazione ha esito negativo. |
| Microsoft.Resources.ResourceDeleteSuccess | Generato quando l'operazione di eliminazione ha esito positivo. |
| Microsoft.Resources.ResourceWriteCancel | Generato quando l'operazione di creazione o aggiornamento viene annullata. |
| Microsoft.Resources.ResourceWriteFailure | Generato quando l'operazione di creazione o aggiornamento ha esito negativo. |
| Microsoft.Resources.ResourceWriteSuccess | Generato quando l'operazione di creazione o aggiornamento ha esito positivo. |

## <a name="example-event"></a>Evento di esempio

L'esempio seguente illustra lo schema di un evento **ResourceWriteSuccess**. Lo stesso schema viene usato per gli eventi **ResourceWriteFailure** e **ResourceWriteCancel** con valori diversi per `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

L'esempio seguente illustra lo schema di un evento **ResourceDeleteSuccess**. Lo stesso schema viene usato per gli eventi **ResourceDeleteFailure** e **ResourceDeleteCancel** con valori diversi per `eventType`.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

L'esempio seguente illustra lo schema di un evento **ResourceActionSuccess**. Lo stesso schema viene usato per gli eventi **ResourceActionFailure** e **ResourceActionCancel** con valori diversi per `eventType`.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
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
| autorizzazione | object | L'autorizzazione richiesta per l'operazione. |
| claims | object | Le proprietà delle attestazioni. Per altre informazioni, vedere [specifiche dei token JWT](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | stringa | Un ID operazione per la risoluzione dei problemi. |
| httpRequest | object | I dettagli dell'operazione. Questo oggetto è incluso soltanto quando si aggiorna una risorsa esistente o la si elimina. |
| resourceProvider | stringa | Provider di risorse per l'operazione. |
| resourceUri | stringa | L'URI della risorsa nell'operazione. |
| operationName | stringa | Operazione eseguita. |
| status | stringa | Lo stato dell'operazione. |
| subscriptionId | stringa | L'ID sottoscrizione della risorsa. |
| TenantId | stringa | L'ID tenant della risorsa. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
