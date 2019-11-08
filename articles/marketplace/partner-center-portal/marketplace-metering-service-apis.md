---
title: API del servizio di misurazione del Marketplace | Azure Marketplace
description: Evento di utilizzo per le offerte SaaS in Azure Marketplace.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 36ca95191e0e6422bd93360b98243393acad8147
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825484"
---
# <a name="marketplace-metering-service-apis"></a>API del servizio di misurazione di Marketplace

L'API dell'evento Usage consente di generare eventi di utilizzo per un'entità acquistata specifica. La richiesta di evento Usage fa riferimento alla dimensione servizi di misurazione definita dal server di pubblicazione durante la pubblicazione dell'offerta.

## <a name="usage-event"></a>Evento di utilizzo

**Post**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. La versione più recente dell'API è 2018-08-31. |

*Intestazioni della richiesta:*

| Tipo di contenuto       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valore stringa univoco per il rilevamento della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione nel client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
| `authorization`   | [Ottenere il token Web JSON (JWT) bearer token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: quando si esegue la richiesta HTTP, il prefisso `Bearer` al token ottenuto dal collegamento a cui si fa riferimento. |

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
Richiesta non valida, dati mancanti o non validi forniti o scaduti

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
Richiesta non valida, dati mancanti o non validi forniti o scaduti

```json
{
  "code": "Forbidden",
  "message": "User is not allowed authorized to call this"
}
```

Codice: 409<br>
Conflitto, quando si riceve la chiamata di utilizzo per l'ID della risorsa di utilizzo e l'utilizzo effettivo, che esiste già. La risposta conterrà `additionalInfo` campo che contiene informazioni sul messaggio accettato.

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

## <a name="batch-usage-event"></a>Evento utilizzo batch

L'API dell'evento utilizzo batch consente di generare eventi di utilizzo per più di un'entità acquistata in una sola volta. La richiesta di evento di utilizzo batch fa riferimento alla dimensione servizi di misurazione definita dal server di pubblicazione durante la pubblicazione dell'offerta.

>[!Note]
>È possibile registrare più offerte SaaS nel Marketplace commerciale di Microsoft. Ogni offerta SaaS registrata ha un'applicazione Azure AD univoca registrata a scopo di autenticazione e autorizzazione. Gli eventi generati in batch devono appartenere a offerte con la stessa applicazione Azure AD al momento della registrazione dell'offerta.

**Post:** `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. La versione più recente dell'API è 2018-08-31. |

*Intestazioni della richiesta:*

| Tipo di contenuto       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valore stringa univoco per il rilevamento della richiesta dal client, preferibilmente un GUID. Se questo valore non viene specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione nel client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non viene specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
| `authorization`      | [Ottenere il token Web JSON (JWT) bearer token.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: quando si esegue la richiesta HTTP, il prefisso `Bearer` al token ottenuto dal collegamento a cui si fa riferimento.  |

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

Descrizione del codice di stato a cui viene fatto riferimento nella risposta `BatchUsageEvent` API:

| Codice di stato  | Descrizione |
| ---------- | -------------------- |
| `Accepted` | Codice accettato. |
| `Expired` | Utilizzo scaduto. |
| `Duplicate` | Uso duplicato fornito. |
| `Error` | Codice di errore. |
| `ResourceNotFound` | La risorsa di utilizzo specificata non è valida. |
| `ResourceNotAuthorized` | Non si dispone delle autorizzazioni necessarie per fornire l'utilizzo di questa risorsa. |
| `InvalidDimension` | La dimensione per cui viene passato l'utilizzo non è valida per questa offerta/piano. |
| `InvalidQuantity` | La quantità passata è < 0. |
| `BadArgument` | Input mancante o non valido. |

Codice: 400<br>
Richiesta non valida, dati mancanti o non validi forniti o scaduti

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

Per altre informazioni, vedere [fatturazione a consumo Saas](./saas-metered-billing.md).
