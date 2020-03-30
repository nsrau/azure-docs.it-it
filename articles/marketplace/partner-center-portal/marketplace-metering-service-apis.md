---
title: API del servizio di misurazione del Marketplace Azure Marketplace
description: Evento di utilizzo per le offerte SaaS in Azure Marketplace.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 315f36e5aed9dee0a89e1f9f504b18a6bed806e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275748"
---
# <a name="marketplace-metering-service-apis"></a>API del servizio di misurazione di Marketplace

L'API degli eventi di uso consente di generare eventi di uso per una specifica entità acquistata. La richiesta di evento di uso fa riferimento alla dimensione dei servizi di misurazione definita dall'autore durante la pubblicazione dell'offerta.

## <a name="usage-event"></a>Evento di utilizzo

**POST**:`https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |          |
| ---------- | ---------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. Ultima versione API è 2018-08-31. |

*Intestazioni richiesta:*

| Tipo di contenuto       | `application/json`    |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro correla tutti gli eventi dal funzionamento client con gli eventi sul lato server. Se questo valore non viene fornito, ne verrà generato uno e fornito nelle intestazioni di risposta. |
| `authorization`   | [Ottenere il token di connessione JSON Web (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: quando si effettua `Bearer` la richiesta HTTP, prefisso al token ottenuto dal collegamento di riferimento. |

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
Conflitto, quando si riceve la chiamata di utilizzo per l'ID risorsa di utilizzo e l'utilizzo effettivo, che esiste già. La risposta `additionalInfo` conterrà il campo che contiene informazioni sul messaggio accettato.

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

## <a name="batch-usage-event"></a>Evento di utilizzo batch

L'API degli eventi di uso batch consente di generare eventi di uso per più di un'entità acquistata contemporaneamente. La richiesta di evento di utilizzo batch fa riferimento alla dimensione dei servizi di misurazione definita dall'autore durante la pubblicazione dell'offerta.

>[!Note]
>È possibile registrare più offerte SaaS nel marketplace commerciale di Microsoft. Ogni offerta SaaS registrata ha un'applicazione Azure AD univoca registrata per scopi di autenticazione e autorizzazione. Gli eventi generati in batch devono appartenere a offerte con la stessa applicazione Azure AD al momento della registrazione dell'offerta.

**POST:**`https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametri di query:*

|            |     |
| ---------- | -------------------- |
| `ApiVersion` | La versione dell'operazione da usare per questa richiesta. Ultima versione API è 2018-08-31. |

*Intestazioni richiesta:*

| Tipo di contenuto       | `application/json`       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non viene fornito, ne verrà generato uno e fornito nelle intestazioni di risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro correla tutti gli eventi dal funzionamento client con gli eventi sul lato server. Se questo valore non viene fornito, ne verrà generato uno e fornito nelle intestazioni di risposta. |
| `authorization`      | [Ottenere il token di connessione JSON Web (JWT).](https://docs.microsoft.com/azure/marketplace/partner-center-portal/pc-saas-registration#get-a-token-based-on-the-azure-ad-app) Nota: quando si effettua `Bearer` la richiesta HTTP, prefisso al token ottenuto dal collegamento di riferimento.  |

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

Descrizione del codice di `BatchUsageEvent` stato a cui viene fatto riferimento nella risposta dell'API:Description of status code referenced in API response:

| Codice di stato  | Descrizione |
| ---------- | -------------------- |
| `Accepted` | Codice accettato. |
| `Expired` | Utilizzo scaduto. |
| `Duplicate` | Utilizzo duplicato fornito. |
| `Error` | Codice di errore. |
| `ResourceNotFound` | La risorsa di utilizzo fornita non è valida. |
| `ResourceNotAuthorized` | Non si è autorizzati a fornire l'utilizzo per questa risorsa. |
| `InvalidDimension` | La dimensione per la quale viene passato l'utilizzo non è valida per questa offerta/piano. |
| `InvalidQuantity` | La quantità passata è < 0. |
| `BadArgument` | L'input è mancante o in formato non corretto. |

Codice: 400<br>
Richiesta non valida, dati mancanti o non validi forniti o Scaduti

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

Per ulteriori informazioni, vedere [Fatturazione a consumo SaaS](./saas-metered-billing.md).
