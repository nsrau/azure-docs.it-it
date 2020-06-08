---
title: API del servizio di analisi - Marketplace commerciale Microsoft
description: L'API dell'evento di uso consente di generare eventi di uso per le offerte SaaS in Microsoft AppSource e in Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 95eba648219413923ce27d433a5236877c4953f3
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725466"
---
# <a name="marketplace-metering-service-apis"></a>API del servizio di misurazione di Marketplace

L'API dell'evento di uso consente di emettere eventi di uso per una specifica entità acquistata. La richiesta dell'evento di uso fa riferimento alle dimensioni dei servizi di analisi definite dall'editore al momento della pubblicazione dell'offerta.

## <a name="usage-event"></a>Evento di uso

**PUBBLICAZIONE**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. La versione dell'API più recente è 2018-08-31. |

*Intestazioni della richiesta:*

| Tipo di contenuto       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `authorization`   | [Ottenere il token di connessione del token JSON Web (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Quando si esegue la richiesta HTTP, aggiungere il prefisso `Bearer` al token ottenuto dal collegamento a cui si fa riferimento. |

>[!Note]
>Per i piani delle app gestite dell'applicazione Azure, il valore di `resourceId` è il valore di `resourceUsageId` individuato in `billingDetails` dell'oggetto metadati dell'app gestita.  Uno script di esempio per recuperarlo è disponibile quando si [usa il token delle identità gestite da Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token).  Per le offerte SaaS, il valore di `resourceId` è l'ID della sottoscrizione SaaS.  Per altre informazioni sulle sottoscrizioni SaaS, vedere l'[elenco delle sottoscrizioni](./pc-saas-fulfillment-api-v2.md#list-subscriptions).

*Richiesta:*

```json
{
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

### <a name="responses"></a>Risposte

Codice: 200<br>
OK 

```json
{
  "usageEventId": "Unique identifier associated with the usage event",
  "status": "Accepted",
  "messageTime": "Time this message was created in UTC",
  "resourceId": "Identifier of the resource against which usage is emitted",
  "quantity": 5.0,
  "dimension": "Dimension identifier",
  "effectiveStartTime": "Time in UTC when the usage event occurred",
  "planId": "Plan associated with the purchased offer"
}
```

Codice: 400 <br>
Richiesta non valida, dati inseriti mancanti o non validi oppure scaduti

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "The resourceId is required.",
      "target": "ResourceId",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```

Codice: 403<br>
Richiesta non valida, dati inseriti mancanti o non validi oppure scaduti

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Codice: 409<br>
Conflitto, quando si riceve la chiamata di uso per l'ID della risorsa di uso e l'uso effettivo, che esiste già. La risposta conterrà il campo `additionalInfo` che contiene informazioni sul messaggio accettato.

```json
{
  "code": "Conflict",
  "additionalInfo": {
    "usageEventId": "Unique identifier associated with the usage event",
    "status": "Accepted|NotProcessed|Expired",
    "messageTime": "Time this message was created in UTC",
    "resourceId": "Identifier of the resource against which usage is emitted",
    "quantity": 5.0,
    "dimension": "Dimension identifier",
    "effectiveStartTime": "Time in UTC when the usage event occurred",
    "planId": "Plan associated with the purchased offer"
  }
}
```

## <a name="batch-usage-event"></a>Evento di uso del batch

L'API dell'evento di uso del batch consente di emettere eventi di uso per più di una specifica entità acquistata alla volta. La richiesta dell'evento di uso del batch fa riferimento alle dimensioni dei servizi di analisi definite dall'editore al momento della pubblicazione dell'offerta.

>[!Note]
>È possibile registrare più offerte SaaS nel marketplace commerciale di Microsoft. Ogni offerta SaaS registrata ha un'applicazione Azure AD univoca registrata per l'autenticazione e l'autorizzazione. Gli eventi generati in batch devono appartenere a offerte con la stessa applicazione Azure AD al momento della registrazione dell'offerta.

**PUBBLICAZIONE**: `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. La versione dell'API più recente è 2018-08-31. |

*Intestazioni della richiesta:*

| Tipo di contenuto       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene specificato, ne verrà generato e inserito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server. Se il valore non viene specificato, ne verrà generato e inserito uno nelle intestazioni della risposta. |
| `authorization`      | [Ottenere il token di connessione del token JSON Web (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: Quando si esegue la richiesta HTTP, aggiungere il prefisso `Bearer` al token ottenuto dal collegamento a cui si fa riferimento.  |

*Richiesta:*
```json
{
  "request": [
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    },
    {
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer"
    }
  ]
}
```
### <a name="responses"></a>Risposte

Codice: 200<br>
OK

```json
{
  "count": 2,
  "result": [
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    },
    {
      "usageEventId": "Unique identifier associated with the usage event",
      "status": "Accepted|Expired|Duplicate|Error|ResourceNotFound|ResourceNotAuthorized|InvalidDimension|BadArgument",
      "messageTime": "Time this message was created in UTC",
      "resourceId": "Identifier of the resource against which usage is emitted",
      "quantity": 5.0,
      "dimension": "Dimension identifier",
      "effectiveStartTime": "Time in UTC when the usage event occurred",
      "planId": "Plan associated with the purchased offer",
      "error": "Error object (optional)"
    }
  ]
}
```

Descrizione del codice di stato a cui si fa riferimento nella risposta API `BatchUsageEvent`:

| Codice di stato  | Descrizione |
| ---------- | -------------------- |
| `Accepted` | Codice accettato. |
| `Expired` | Uso scaduto. |
| `Duplicate` | Uso duplicato consentito. |
| `Error` | Codice di errore. |
| `ResourceNotFound` | La risorsa di uso specificata non è valida. |
| `ResourceNotAuthorized` | Non si dispone delle autorizzazioni necessarie per specificare l'uso di questa risorsa. |
| `InvalidDimension` | Le dimensioni per cui viene passato l'uso non sono valide per questa offerta o per questo piano. |
| `InvalidQuantity` | La quantità passata è inferiore a 0. |
| `BadArgument` | L'input è mancante o non valido. |

Codice: 400<br>
Richiesta non valida, dati inseriti mancanti o non validi oppure scaduti

```json
{
  "message": "One or more errors have occurred.",
  "target": "usageEventRequest",
  "details": [
    {
      "message": "Invalid data format.",
      "target": "usageEventRequest",
      "code": "BadArgument"
    }
  ],
  "code": "BadArgument"
}
```
Codice: 403<br>
L'utente non è autorizzato a effettuare questa chiamata

```json
{
  "code": "Forbidden",
  "message": "User is not allowed to call this"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere [Fatturazione a consumo SaaS](./saas-metered-billing.md).
