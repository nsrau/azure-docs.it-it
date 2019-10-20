---
title: API di evasione SaaS V2 | Azure Marketplace
description: Questo articolo illustra come creare e gestire un'offerta SaaS in AppSource e in Azure Marketplace usando le API di Fulfillment V2 associate.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 75e806e56fa94916f76f9e7fa6572ae07987e017
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595548"
---
# <a name="saas-fulfillment-apis-version-2"></a>API di evasione SaaS, versione 2 

Questo articolo illustra in dettaglio le API che consentono ai partner di vendere le proprie applicazioni SaaS in AppSource Marketplace e in Azure Marketplace. Queste API sono un requisito per le offerte SaaS transazionali in AppSource e Azure Marketplace.

## <a name="managing-the-saas-subscription-life-cycle"></a>Gestione del ciclo di vita della sottoscrizione SaaS

SaaS di Azure gestisce l'intero ciclo di vita di un acquisto di una sottoscrizione SaaS. Usa le API di evasione come meccanismo per favorire l'evasione effettiva, le modifiche ai piani e l'eliminazione della sottoscrizione con il partner. La fattura del cliente si basa sullo stato della sottoscrizione SaaS gestita da Microsoft. Il diagramma seguente illustra gli Stati e le operazioni che determinano le modifiche tra gli Stati.

![Stati del ciclo di vita della sottoscrizione SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stati di una sottoscrizione SaaS

La tabella seguente elenca gli Stati di provisioning per una sottoscrizione SaaS, inclusi una descrizione e un diagramma di sequenza per ognuno di essi (se applicabile). 

#### <a name="provisioning"></a>Provisioning

Quando un cliente avvia un acquisto, il partner riceve queste informazioni in un codice di autorizzazione in una pagina Web interattiva del cliente che usa un parametro URL. Un esempio è `https://contoso.com/signup?token=..`, mentre l'URL della pagina di destinazione nel centro per i partner è `https://contoso.com/signup`. Il codice di autorizzazione può essere convalidato e scambiato per i dettagli del servizio di provisioning chiamando l'API Resolve.  Al termine del provisioning di un servizio SaaS, viene inviata una chiamata Activate per segnalare che l'evasione è stata completata e che è possibile fatturare il cliente. 

Il diagramma seguente illustra la sequenza di chiamate API per uno scenario di provisioning.  

![Chiamate API per il provisioning di un servizio SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Con provisioning

Questo stato è lo stato stabile di un servizio di cui è stato effettuato il provisioning.

##### <a name="provisioning-for-update"></a>Provisioning per l'aggiornamento 

Questo stato indica che un aggiornamento a un servizio esistente è in sospeso. Un aggiornamento di questo tipo può essere avviato dal cliente, sia dal Marketplace che dal servizio SaaS (solo per le transazioni dirette a Customer).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Provisioning per l'aggiornamento (quando viene avviato dal Marketplace)

Il diagramma seguente illustra la sequenza di azioni quando un aggiornamento viene avviato dal Marketplace.

![Chiamate API quando l'aggiornamento viene avviato dal Marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Provisioning per l'aggiornamento (quando viene avviato dal servizio SaaS)

Il diagramma seguente illustra le azioni eseguite quando un aggiornamento viene avviato dal servizio SaaS. La chiamata al webhook viene sostituita da un aggiornamento della sottoscrizione avviata dal servizio SaaS. 

![Chiamate API quando l'aggiornamento viene avviato dal servizio SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Questo stato indica che non è stato ricevuto alcun pagamento da parte del cliente. Per criterio, si fornirà al cliente un periodo di tolleranza prima di annullare la sottoscrizione. Quando una sottoscrizione è in questo stato: 

- In qualità di partner, è possibile scegliere di ridurre o bloccare l'accesso dell'utente al servizio.
- La sottoscrizione deve essere mantenuta in uno stato reversibile che consente di ripristinare funzionalità complete senza alcuna perdita di dati o impostazioni. 
- Si prevede di ottenere una richiesta di reintegro per questa sottoscrizione tramite le API di evasione o una richiesta di deprovisioning alla fine del periodo di tolleranza. 

#### <a name="unsubscribed"></a>Annullata 

Le sottoscrizioni raggiungono questo stato in risposta a una richiesta esplicita del cliente o al mancato pagamento delle quote. L'aspettativa del partner è che i dati del cliente vengono conservati per il ripristino su richiesta per un determinato numero di giorni e quindi eliminati. 


## <a name="api-reference"></a>Informazioni di riferimento sulle API

Questa sezione illustra l'API di *sottoscrizione* SaaS e l' *API per le operazioni*.  Il valore del parametro `api-version` per le API della versione 2 è `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definizioni di parametri ed entità

La tabella seguente elenca le definizioni per i parametri e le entità comuni usati dalle API di evasione.

|     Entità/parametro     |     Definizione                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificatore GUID per una risorsa SaaS.  |
| `name`                   | Nome descrittivo fornito per questa risorsa dal cliente. |
| `publisherId`            | Identificatore di stringa univoco per ogni server di pubblicazione (ad esempio: "contoso"). |
| `offerId`                | Identificatore di stringa univoco per ogni offerta (ad esempio: "offer1").  |
| `planId`                 | Identificatore di stringa univoco per ogni piano/SKU (ad esempio: "Silver"). |
| `operationId`            | Identificatore GUID per una particolare operazione.  |
|  `action`                | Azione eseguita su una risorsa, ovvero `unsubscribe`, `suspend`, `reinstate` o `changePlan` `changeQuantity` `transfer`.  |
|   |   |

Gli identificatori univoci globali ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sono numeri a 128 bit (32-esadecimale) che vengono in genere generati automaticamente. 

#### <a name="resolve-a-subscription"></a>Risolvere una sottoscrizione 

L'endpoint di risoluzione consente al server di pubblicazione di risolvere un token del Marketplace in un ID di risorsa permanente. L'ID risorsa è l'identificatore univoco per una sottoscrizione SaaS. Quando un utente viene reindirizzato al sito Web di un partner, l'URL contiene un token nei parametri di query. È previsto che il partner usi questo token ed effettui una richiesta di risoluzione. La risposta contiene l'ID sottoscrizione SaaS univoco, il nome, l'ID offerta e il piano per la risorsa. Questo token è valido solo per un'ora. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Inserisci<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Ad esempio: "`Bearer <access_token>`". |
|  x-ms-marketplace-token  |  Il parametro di query del token nell'URL quando l'utente viene reindirizzato al sito Web del partner SaaS da Azure (ad esempio: `https://contoso.com/signup?token=..`). *Nota:* L'URL decodifica il valore del token dal browser prima di usarlo.  |

*Codici di risposta:*

Codice: 200<br>
Risolve il token opaco in una sottoscrizione SaaS. Corpo della risposta:
 

```json
{
    "id": "<guid>",  
    "subscriptionName": "Contoso Cloud Solution",
    "offerId": "offer1",
    "planId": "silver",
    "quantity": "20" 
}
```

Codice: 400<br>
Richiesta non valida. x-ms-Marketplace-token mancante, non valido o scaduto.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

### <a name="subscription-api"></a>API della sottoscrizione

L'API di sottoscrizione supporta le operazioni HTTPS seguenti: **Get**, **post**, **patch**ed **Delete**.


#### <a name="list-subscriptions"></a>Elencare le sottoscrizioni

Elenca tutte le sottoscrizioni SaaS per un server di pubblicazione.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Ottieni<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametri di query:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
| x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
| authorization      |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Ad esempio: "`Bearer <access_token>`".  |

*Codici di risposta:*

Codice: 200 <br/>
Ottiene il server di pubblicazione e le sottoscrizioni corrispondenti per tutte le offerte del server di pubblicazione, in base al token di autenticazione.

>[!Note]
>Le [API fittizie](#mock-apis) vengono usate quando si sviluppa l'offerta per la prima volta, mentre le API effettive devono essere usate quando si pubblica effettivamente l'offerta.  Le API reali e le API fittizie sono diverse dalla prima riga del codice.  Nell'API reale è presente la sezione `subscription`, mentre questa sezione non esiste per l'API fittizia.

Payload di risposta per l'API fittizia:<br>

```json
{
  [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": "true", // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```
E per l'API reale: <br>

```json
{
  "subscriptions": [
      {
          "id": "<guid>",
          "name": "Contoso Cloud Solution",
          "publisherId": "contoso",
          "offerId": "offer1",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant, object id and email address for which SaaS subscription is purchased.
              "emailId": "<email>",
              "objectId": "<guid>",                     
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant, object id and email address that purchased the SaaS subscription. These could be different for reseller scenario
              "emailId": "<email>",
              "objectId": "<guid>",                      
              "tenantId": "<guid>"
          },
            "term": {
                "startDate": "2019-05-31",
                "endDate": "2019-06-29",
                "termUnit": "P1M"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "isFreeTrial": true, // true – the customer subscription is currently in free trial, false – the customer subscription is not currently in free trial.(optional field – default false)
          "isTest": false, //indicating whether the current subscription is a test asset
          "sandboxType": "None", // Possible Values: None, Csp (Csp sandbox purchase)
          "saasSubscriptionStatus": "Subscribed" // Indicates the status of the operation: [NotStarted, PendingFulfillmentStart, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "@nextLink": ""
}
```
Il token di continuazione sarà presente solo se sono presenti "pagine" aggiuntive dei piani da recuperare. 

Codice: 403 <br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. 

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="get-subscription"></a>Ottieni una sottoscrizione

Ottiene la sottoscrizione SaaS specificata. Usare questa chiamata per ottenere informazioni sulle licenze e informazioni sul piano.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Ottieni<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API di risoluzione.   |
|  ApiVersion        |   La versione dell'operazione da usare per questa richiesta.   |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Ad esempio: "`Bearer <access_token>`".  |

*Codici di risposta:*

Codice: 200<br>
Ottiene la sottoscrizione SaaS dall'identificatore. Payload risposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"Contoso Cloud Solution",
        "publisherId": "contoso",
        "offerId": "offer1",
        "planId": "silver",
        "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "<guid>"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "<guid>"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP-initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "isFreeTrial": "true", // true – customer subscription is currently in free trial, false – customer subscription is not currently in free trial.
        "status": "Subscribed", // Indicates the status of the operation.
          "term": { //This gives the free trial term start and end date
            "startDate": "2019-05-31",
            "endDate": "2019-06-29",
            "termUnit": "P1M" //where P1M: Monthly, P1Y: Yearly 
        },
}
```

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.<br> 

Codice: 500<br>
Errore interno del server.<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Elenca i piani disponibili

Usare questa chiamata per verificare se sono presenti offerte private o pubbliche per l'editore corrente.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Ottieni<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`". |

*Codici di risposta:*

Codice: 200<br>
Ottiene un elenco di piani disponibili per un cliente. Corpo della risposta:

```json
{
    "plans": [{
        "planId": "Platinum001",
        "displayName": "Private platinum plan for Contoso",
        "isPrivate": true
    }]
}
```

Codice: 404<br>
Non trovato.<br> 

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. <br> 

Codice: 500<br>
Errore interno del server.<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="activate-a-subscription"></a>Attivare una sottoscrizione

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidactivateapi-versionapiversion"></a>Inserisci<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questa stringa mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`". |

*Payload della richiesta:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Codici di risposta:*

Codice: 200<br>
Attiva la sottoscrizione.<br>

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="change-the-plan-on-the-subscription"></a>Modificare il piano nella sottoscrizione

Aggiornare il piano nella sottoscrizione.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.    |
| authorization      |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`".  |

*Payload della richiesta:*

```json
Request Body:
{
    "planId": "gold"
}
```

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
Il piano di richiesta di modifica è stato accettato. Si prevede che il partner esegua il polling dell'operazione-location per determinare un esito positivo o negativo. <br>

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>È possibile applicare patch solo a un piano o una quantità alla volta, non a entrambe. Le modifiche apportate a una sottoscrizione con **aggiornamento** non sono in `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Modificare la quantità nella sottoscrizione

Aggiornare la quantità nella sottoscrizione.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.    |
| authorization      |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`".  |

*Payload della richiesta:*

```json
Request Body:
{
    "quantity": 5
}
```

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
La richiesta di modifica della quantità è stata accettata. Si prevede che il partner esegua il polling dell'operazione-location per determinare un esito positivo o negativo. <br>

Codice: 400<br>
Richiesta non valida: errori di convalida.


Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

>[!Note]
>È possibile applicare patch solo a un piano o una quantità alla volta, non a entrambe. Le modifiche apportate a una sottoscrizione con **aggiornamento** non sono in `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Eliminare una sottoscrizione

Annulla la sottoscrizione ed elimina la sottoscrizione specificata.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Eliminazione<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.   |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.   |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`".  |

*Codici di risposta:*

Codice: 202<br>
Il partner ha avviato una chiamata per annullare la sottoscrizione di una sottoscrizione SaaS.<br>

Codice: 400<br>
Elimina in una sottoscrizione con **Delete** non in `allowedCustomerOperations`.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```


### <a name="operations-api"></a>API operazioni

L'API Operations supporta le operazioni patch e Get seguenti.

#### <a name="list-outstanding-operations"></a>Elencare le operazioni in attesa 

Elenca le operazioni in attesa per il server di pubblicazione corrente. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Ottieni<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametri di query:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   La versione dell'operazione da usare per questa richiesta.                |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`".  |

*Codici di risposta:*

Codice: 200<br> Ottiene l'elenco delle operazioni in sospeso in una sottoscrizione. Payload risposta:

```json
[{
    "id": "<guid>",  
    "activityId": "<guid>",
    "subscriptionId": "<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```


Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 500<br>
Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Ottenere lo stato dell'operazione

Consente al server di pubblicazione di tenere traccia dello stato dell'operazione asincrona attivata specificata, ad esempio `subscribe`, `unsubscribe`, `changePlan` o `changeQuantity`.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Ottieni<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). Ad esempio: "`Bearer <access_token>`".  |

*Codici di risposta:*<br>

Codice: 200<br> Ottiene l'operazione SaaS in sospeso specificata. Payload risposta:

```json
Response body:
{
    "id  ": "<guid>",
    "activityId": "<guid>",
    "subscriptionId":"<guid>",
    "offerId": "offer1",
    "publisherId": "contoso",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}

```

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.
 
Codice: 404<br>
Non trovato.

Codice: 500<br> Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```
#### <a name="update-the-status-of-an-operation"></a>Aggiornare lo stato di un'operazione

Aggiornare lo stato di un'operazione per indicare l'esito positivo o negativo con i valori forniti.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione SaaS ottenuta dopo la risoluzione del token tramite l'API Resolve.  |
|  operationId       | Operazione completata. |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione client con gli eventi sul lato server. Se questo valore non è specificato, ne verrà generato uno e specificato nelle intestazioni della risposta. |
|  authorization     |  [Ottenere il token Web JSON (JWT) Bearer token](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  Ad esempio: "`Bearer <access_token>`".  |

*Payload della richiesta:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Codici di risposta:*

Codice: 200<br> Una chiamata a informa del completamento di un'operazione sul lato del partner. Questa risposta, ad esempio, potrebbe segnalare la modifica di postazioni o piani.

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato fornito o non è valido oppure la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 409<br>
Conflitto. Ad esempio, è già stata soddisfatta una transazione più recente.

Codice: 500<br> Errore interno del server.

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="implementing-a-webhook-on-the-saas-service"></a>Implementazione di un webhook nel servizio SaaS

Il server di pubblicazione deve implementare un webhook in questo servizio SaaS per notificare in modo proattivo agli utenti le modifiche apportate al servizio. È previsto che il servizio SaaS chiami l'API Operations per convalidare e autorizzare prima di eseguire un'azione sulla notifica del webhook.


```json
{
  "id": "<this is a GUID operation id, you can call operations API with this to get status>",
  "activityId": "<this is a Guid correlation id>",
  "subscriptionId": "<Guid to uniquely identify this resource>",
  "publisherId": "<this is the publisher’s name>",
  "offerId": "<this is the offer name>",
  "planId": "<this is the plan id>",
  "quantity": "<the number of seats, will be null if not per-seat saas offer>",
  "timeStamp": "2019-04-15T20:17:31.7350641Z",
  "action": "Unsubscribe",
  "status": "NotStarted"  

}
```
Dove l'azione può essere una delle seguenti: 
- `unsubscribe` (quando la risorsa è stata eliminata)
- `changePlan` (al termine dell'operazione di modifica del piano)
- `changeQuantity` (al termine dell'operazione di modifica della quantità)
- `suspend` (quando la risorsa è stata sospesa)
- `reinstate` (quando la risorsa è stata ripristinata dopo la sospensione)

Dove lo stato può essere uno dei seguenti: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Completato** <br>
- **Non riuscito** <br>
- **Conflitto** <br>

In una notifica webhook, gli Stati interoperabili sono **succeeded** e **failed**. Il ciclo di vita di un'operazione è da **NotStarted** a uno stato terminale come **succeeded**, **failed**o **Conflict**. Se si riceve **NotStarted** o in **corso**, continuare a richiedere lo stato tramite Get API fino a quando l'operazione non raggiunge uno stato terminale prima di eseguire un'azione. 

## <a name="mock-apis"></a>API fittizie

È possibile usare le API fittizie per iniziare a sviluppare, in particolare per la creazione di prototipi e per i progetti di test. 

Endpoint host: `https://marketplaceapi.microsoft.com/api` (nessuna autenticazione necessaria)<br/>
Versione API: `2018-09-15`<br/>
URI di esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

I percorsi dell'endpoint API sono gli stessi per le API fittizie e reali, ma le versioni API sono diverse. La versione è `2018-09-15` per la versione fittizia e `2018-08-31` per la versione di produzione. 

Tutte le chiamate API in questo articolo possono essere apportate all'endpoint dell'host fittizio. In generale, si prevede di recuperare i dati fittizi come risposta. Le chiamate ai metodi di sottoscrizione di aggiornamento nell'API fittizia restituiscono sempre 500. 

## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori possono anche recuperare e modificare a livello di codice i carichi di lavoro, le offerte e i profili di pubblicazione usando le [API REST di portale cloud partner](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
