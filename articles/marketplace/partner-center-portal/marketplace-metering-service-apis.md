---
title: API del servizio di analisi - Marketplace commerciale Microsoft
description: L'API dell'evento di uso consente di generare eventi di uso per le offerte SaaS in Microsoft AppSource e in Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/26/2020
ms.openlocfilehash: 8a6636b0fc6c3e67ec171d738efb3fd8a93de30c
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120773"
---
# <a name="marketplace-metered-billing-apis"></a>API di fatturazione a consumo del Marketplace

Le API di fatturazione a consumo devono essere usate quando l'editore crea dimensioni di misurazione personalizzate per un'offerta da pubblicare nel centro per i partner. L'integrazione con le API di fatturazione a consumo è obbligatoria per qualsiasi offerta acquistata con uno o più piani con dimensioni personalizzate per generare eventi di utilizzo.

Per altre informazioni sulla creazione di dimensioni di misurazione personalizzate per SaaS, vedere [fatturazione a consumo Saas](saas-metered-billing.md).

Per altre informazioni sulla creazione di dimensioni di misurazione personalizzate per un'offerta di applicazione Azure con un piano di app gestite, vedere la [sezione relativa alla configurazione tecnica di creare una nuova offerta di app di Azure](create-new-azure-apps-offer.md#technical-configuration).

## <a name="enforcing-tls-12-note"></a>Applicazione della nota TLS 1,2

La versione 1,2 di TLS viene applicata come versione minima per le comunicazioni HTTPS. Assicurarsi di usare questa versione di TLS nel codice. Le versioni di TLS 1,0 e 1,1 sono deprecate e i tentativi di connessione verranno rifiutati.

## <a name="metered-billing-single-usage-event"></a>Evento utilizzo singolo fatturazione a consumo

L'API dell'evento Usage deve essere chiamata dal server di pubblicazione per generare eventi di utilizzo su una risorsa attiva (sottoscritta) per il piano acquistato dal cliente specifico. L'evento Usage viene emesso separatamente per ogni dimensione personalizzata del piano definito dal server di pubblicazione durante la pubblicazione dell'offerta.

È possibile emettere un solo evento di utilizzo per ogni ora di un giorno del calendario. Ad esempio, a 8:05:15 oggi è possibile generare un evento di utilizzo. Se questo evento viene accettato, l'evento di utilizzo successivo verrà accettato da oggi 9:00. Se oggi si invia un evento aggiuntivo compreso tra 8:15 e 8:59:59, questo verrà rifiutato come duplicato. È necessario accumulare tutte le unità utilizzate in un'ora e quindi generarle in un singolo evento.

È possibile emettere un solo evento di utilizzo per ogni ora di un giorno del calendario per ogni risorsa. Se più unità vengono utilizzate in un'ora, accumulare tutte le unità utilizzate nell'ora e quindi generarle in un singolo evento. Gli eventi di utilizzo possono essere emessi solo per le ultime 24 ore. Se si genera un evento di utilizzo in qualsiasi momento compreso tra 8:00 e 8:59:59 (e viene accettato) e si invia un evento aggiuntivo per lo stesso giorno tra 8:00 e 8:59:59, questo verrà rifiutato come duplicato.

**PUBBLICAZIONE**: `https://marketplaceapi.microsoft.com/api/usageEvent?api-version=<ApiVersion>`

*Parametri di query:*

| Parametro | Recommendation          |
| ---------- | ---------------------- |
| `ApiVersion` | Utilizzare 2018-08-31. |
| | |

*Intestazioni della richiesta:*

| Tipo di contenuto       | Utilizzare `application/json`.  |
| ------------------ | ---------------------------- |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| `authorization`   | Un token di accesso univoco che identifica l'ISV che effettua questa chiamata API. Il formato è `"Bearer <access_token>"` quando il valore del token viene recuperato dal server di pubblicazione come illustrato per <br> <ul> <li> SaaS in [ottenere il token con un post http](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Applicazione gestita in [strategie di autenticazione](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |

*Esempio di corpo della richiesta:*

```json
{
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. 
  "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, from now and until 24 hours back
  "planId": "plan1", // id of the plan purchased for the offer
}
```

>[!NOTE]
>`resourceId`ha un significato diverso per l'app SaaS e per l'applicazione gestita che emette il contatore personalizzato. 

Per i piani delle app gestite dell'applicazione Azure, il valore di `resourceId` è il valore di `resourceUsageId` individuato in `billingDetails` dell'oggetto metadati dell'app gestita. Uno script di esempio per recuperarlo è disponibile quando si [usa il token delle identità gestite da Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Per le offerte SaaS, il valore di `resourceId` è l'ID della sottoscrizione SaaS. Per altre informazioni sulle sottoscrizioni SaaS, vedere l'[elenco delle sottoscrizioni](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Risposte

Codice: 200<br>
OK. Le emissioni di utilizzo sono state accettate e registrate sul lato Microsoft per un'ulteriore elaborazione e fatturazione.

Esempio di payload di risposta: 

```json
{
  "usageEventId": <guid>, // unique identifier associated with the usage event in Microsoft records
  "status": "Accepted" // this is the only value in case of single usage event
  "messageTime": "2020-01-12T13:19:35.3458658Z", // time in UTC this event was accepted
  "resourceId": <guid>, // unique identifier of the resource against which usage is emitted. For SaaS it's the subscriptionId.
  "quantity": 5.0, // amount of emitted units as recorded by Microsoft
  "dimension": "dim1", // custom dimension identifier
  "effectiveStartTime": "2018-12-01T08:30:14", // time in UTC when the usage event occurred, as sent by the ISV
  "planId": "plan1", // id of the plan purchased for the offer
}
```

Codice: 400 <br>
Richiesta non valida.

* Sono stati forniti dati della richiesta mancanti o non validi.
* `effectiveStartTime`è più di 24 ore nel passato. L'evento è scaduto.
* La sottoscrizione SaaS non è nello stato sottoscritto.

Esempio di payload di risposta: 

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

Non consentito. Il token di autorizzazione non è specificato, non è valido o è scaduto.  In alternativa, la richiesta sta tentando di accedere a una sottoscrizione per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

Codice: 409<br>
Conflitto. Un evento di utilizzo è già stato segnalato correttamente per l'ID risorsa specificato, la data e l'ora di utilizzo effettive.

Esempio di payload di risposta: 

```json
{
  "additionalInfo": {
    "acceptedMessage": {
      "usageEventId": "<guid>", //unique identifier associated with the usage event in Microsoft records
      "status": "Duplicate",
      "messageTime": "2020-01-12T13:19:35.3458658Z",
      "resourceId": "<guid>", //unique identifier of the resource against which usage is emitted.
      "quantity": 1.0,
      "dimension": "dim1",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "plan1"
    }
  },
  "message": "This usage event already exist.",
  "code": "Conflict"
}
```

## <a name="metered-billing-batch-usage-event"></a>Evento di utilizzo batch di fatturazione a consumo

L'API dell'evento utilizzo batch consente di generare eventi di utilizzo per più di una risorsa acquistata in una sola volta. Consente inoltre di generare diversi eventi di utilizzo per la stessa risorsa purché siano destinati a diverse ore di calendario. Il numero massimo di eventi in un singolo batch è 25.

**PUBBLICAZIONE**: `https://marketplaceapi.microsoft.com/api/batchUsageEvent?api-version=<ApiVersion>`

*Parametri di query:*

| Parametro  | Recommendation     |
| ---------- | -------------------- |
| `ApiVersion` | Utilizzare 2018-08-31. |

*Intestazioni della richiesta:*

| Tipo di contenuto       | Utilizzare `application/json`.       |
| ------------------ | ------ |
| `x-ms-requestid`     | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene specificato, ne verrà generato e inserito uno nelle intestazioni della risposta. |
| `x-ms-correlationid` | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dell'operazione del client con gli eventi sul lato server. Se il valore non viene specificato, ne verrà generato e inserito uno nelle intestazioni della risposta. |
| `authorization`      | Un token di accesso univoco che identifica l'ISV che effettua questa chiamata API. Il formato è `Bearer <access_token>` quando il valore del token viene recuperato dal server di pubblicazione come illustrato per <br> <ul> <li> SaaS in [ottenere il token con un post http](./pc-saas-registration.md#get-the-token-with-an-http-post). </li> <li> Applicazione gestita in [strategie di autenticazione](./marketplace-metering-service-authentication.md). </li> </ul> |
| | |


*Esempio di corpo della richiesta:*

```json
{
  "request": [ // list of usage events for the same or different resources of the publisher
    { // first event
      "resourceId": "<guid1>", // Unique identifier of the resource against which usage is emitted. 
      "quantity": 5.0, // how many units were consumed for the date and hour specified in effectiveStartTime, must be greater than 0, can be integer or float value
      "dimension": "dim1", //Custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",//Time in UTC when the usage event occurred, from now and until 24 hours back
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // next event
      "resourceId": "<guid2>", 
      "quantity": 39.0, 
      "dimension": "email", 
      "effectiveStartTime": "2018-11-01T23:33:10
      "planId": "gold", // id of the plan purchased for the offer
    }
  ]
}
```

>[!NOTE]
>`resourceId`ha un significato diverso per l'app SaaS e per l'applicazione gestita che emette il contatore personalizzato. 

Per i piani delle app gestite dell'applicazione Azure, il valore di `resourceId` è il valore di `resourceUsageId` individuato in `billingDetails` dell'oggetto metadati dell'app gestita. Uno script di esempio per recuperarlo è disponibile quando si [usa il token delle identità gestite da Azure](./marketplace-metering-service-authentication.md#using-the-azure-managed-identities-token). 

Per le offerte SaaS, il valore di `resourceId` è l'ID della sottoscrizione SaaS. Per altre informazioni sulle sottoscrizioni SaaS, vedere l'[elenco delle sottoscrizioni](./pc-saas-fulfillment-api-v2.md#get-list-of-all-subscriptions).

### <a name="responses"></a>Risposte

Codice: 200<br>
OK. Le emissioni di utilizzo batch sono state accettate e registrate sul lato Microsoft per un'ulteriore elaborazione e fatturazione. L'elenco di risposte viene restituito con lo stato per ogni singolo evento nel batch. È necessario scorrere il payload della risposta per comprendere le risposte per ogni singolo evento di utilizzo inviato come parte dell'evento batch.

Esempio di payload di risposta: 

```json
{
  "count": 2, // number of records in the response
  "result": [
    { // first response
      "usageEventId": "<guid>", // unique identifier associated with the usage event in Microsoft records
      "status": "Accepted" // see list of possible statuses below,
      "messageTime": "2020-01-12T13:19:35.3458658Z", // Time in UTC this event was accepted by Microsoft,
      "resourceId": "<guid1>", // unique identifier of the resource against which usage is emitted.
      "quantity": 5.0, // amount of emitted units as recorded by Microsoft 
      "dimension": "dim1", // custom dimension identifier
      "effectiveStartTime": "2018-12-01T08:30:14",// time in UTC when the usage event occurred, as sent by the ISV
      "planId": "plan1", // id of the plan purchased for the offer
    },
    { // second response
      "status": "Duplicate",
      "messageTime": "0001-01-01T00:00:00",
      "error": {
        "additionalInfo": {
          "acceptedMessage": {
            "usageEventId": "<guid>",
            "status": "Duplicate",
            "messageTime": "2020-01-12T13:19:35.3458658Z",
            "resourceId": "<guid2>",
            "quantity": 1.0,
            "dimension": "email",
            "effectiveStartTime": "2020-01-12T11:03:28.14Z",
            "planId": "gold"
          }
        },
        "message": "This usage event already exist.",
        "code": "Conflict"
      },
      "resourceId": "<guid2>",
      "quantity": 1.0,
      "dimension": "email",
      "effectiveStartTime": "2020-01-12T11:03:28.14Z",
      "planId": "gold"
    }
  ]
}
```

Descrizione del codice di stato a cui si fa riferimento nella risposta API `BatchUsageEvent`:

| Codice di stato  | Descrizione |
| ---------- | -------------------- |
| `Accepted` | Accettato. |
| `Expired` | Uso scaduto. |
| `Duplicate` | Uso duplicato consentito. |
| `Error` | Codice di errore. |
| `ResourceNotFound` | La risorsa di uso specificata non è valida. |
| `ResourceNotAuthorized` | Non si dispone delle autorizzazioni necessarie per specificare l'uso di questa risorsa. |
| `InvalidDimension` | Le dimensioni per cui viene passato l'uso non sono valide per questa offerta o per questo piano. |
| `InvalidQuantity` | La quantità passata è inferiore o uguale a 0. |
| `BadArgument` | L'input è mancante o non valido. |

Codice: 400<br>
Richiesta non valida. Il batch conteneva più di 25 eventi di utilizzo.

Codice: 403<br>
Non consentito. Il token di autorizzazione non è specificato, non è valido o è scaduto.  In alternativa, la richiesta sta tentando di accedere a una sottoscrizione per un'offerta pubblicata con un ID App Azure AD diverso da quello usato per creare il token di autorizzazione.

## <a name="development-and-testing-best-practices"></a>Procedure consigliate per lo sviluppo e il test

Per testare l'emissione del contatore personalizzato, implementare l'integrazione con l'API di controllo, creare un piano per l'offerta SaaS pubblicata con dimensioni personalizzate definite al suo interno con prezzo zero per unità. E pubblicare questa offerta come anteprima, in modo che solo gli utenti limitati possano accedere e testare l'integrazione.

È anche possibile usare un piano privato per un'offerta Live esistente per limitare l'accesso a questo piano durante i test a destinatari limitati.

## <a name="get-support"></a>Ottenere supporto

Seguire le istruzioni in [supporto per il programma Commercial Marketplace nel centro per i partner](./support.md) per comprendere le opzioni di supporto dell'editore e aprire un ticket di supporto con Microsoft.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API del servizio di misurazione, vedere [domande frequenti sulle API del servizio](./marketplace-metering-service-apis-faq.md)di controllo del Marketplace.
