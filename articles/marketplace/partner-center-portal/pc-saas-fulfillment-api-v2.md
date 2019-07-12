---
title: SaaS evasione API v2 | Azure Marketplace
description: Questo articolo illustra come creare e gestire un'offerta SaaS nel Azure Marketplace e AppSource usando l'evasione di ordini associati API v2.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 05/23/2019
ms.author: evansma
ms.openlocfilehash: 476aaacbe6f1bf6d1920df0f12599976bfcc27b7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67701148"
---
# <a name="saas-fulfillment-apis-version-2"></a>SaaS evasione, API, versione 2 

Questo articolo illustra in dettaglio le API che consentono ai partner di vendere le proprie applicazioni SaaS in Azure Marketplace e nel marketplace di AppSource. Queste API sono che un requisito per SaaS transactable offre sul Azure Marketplace e AppSource.

## <a name="managing-the-saas-subscription-life-cycle"></a>La gestione del ciclo di vita della sottoscrizione di SaaS

SaaS di Azure gestisce l'intero ciclo di vita di un acquisto dell'abbonamento SaaS. Usa l'API di evasione come un meccanismo per guidare l'evasione di ordini effettivo, modifiche ai piani e l'eliminazione della sottoscrizione con il partner. Fattura del cliente si basa sullo stato della sottoscrizione SaaS gestito da Microsoft. Il diagramma seguente illustra gli Stati e le operazioni che determinano le modifiche tra stati.

![Stati del ciclo di vita della sottoscrizione SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stati di una sottoscrizione di SaaS

La tabella seguente elenca gli stati di provisioning per una sottoscrizione di SaaS, tra cui un diagramma di sequenza e descrizione per ogni (se applicabile). 

#### <a name="provisioning"></a>Provisioning

Quando un cliente avvia un acquisto, il partner riceve queste informazioni in un codice di autorizzazione in una pagina web interattive cliente che utilizza un parametro URL. Ad esempio `https://contoso.com/signup?token=..`, mentre l'URL della pagina di destinazione nel centro per i Partner è `https://contoso.com/signup`. Il codice di autorizzazione può essere convalidato e scambiato per i dettagli del servizio di provisioning chiamando l'API di risolvere.  Quando un servizio SaaS ha terminato il provisioning, invia una chiamata di attivazione per segnalare che è stata completata l'evasione di ordini e l'addebito al cliente può. 

Il diagramma seguente mostra la sequenza di chiamate API per uno scenario di provisioning.  

![Chiamate API per il provisioning di un servizio SaaS](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Sottoposto a provisioning

Questo stato è lo stato stazionario di un servizio di provisioning.

##### <a name="provisioning-for-update"></a>Il provisioning per l'aggiornamento 

Questo stato indica che è in sospeso un aggiornamento a un servizio esistente. L'aggiornamento può essere avviata dal cliente, dal marketplace o nel servizio SaaS (solo per le transazioni dirette a cliente).

##### <a name="provisioning-for-update-when-its-initiated-from-the-marketplace"></a>Il provisioning per l'aggiornamento (quando viene avviata dal marketplace)

Il diagramma seguente mostra la sequenza di azioni durante un aggiornamento viene avviato dal marketplace.

![Chiamate API durante l'aggiornamento viene avviata dal marketplace](./media/saas-update-api-v2-calls-from-marketplace-a.png)

##### <a name="provisioning-for-update-when-its-initiated-from-the-saas-service"></a>Il provisioning per l'aggiornamento (quando viene avviato dal servizio SaaS)

Il diagramma seguente mostra le azioni quando viene avviato un aggiornamento del servizio SaaS. (La chiamata webhook verrà sostituita da un aggiornamento alla sottoscrizione iniziata dal servizio SaaS). 

![Chiamate API quando viene avviato l'aggiornamento del servizio SaaS](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Questo stato indica che non è stato ricevuto il pagamento del cliente. Dai criteri, verranno fornite al cliente un periodo di tolleranza prima dell'annullamento della sottoscrizione. Quando una sottoscrizione è in questo stato: 

- Un partner, è possibile scegliere di ridurre le prestazioni o bloccare l'accesso dell'utente al servizio.
- La sottoscrizione deve restare in uno stato recuperabile che è possibile ripristinare la funzionalità completa senza alcuna perdita di dati o impostazioni. 
- Si prevede di ricevere una richiesta di reintegro per questa sottoscrizione tramite l'API di evasione degli ordini o una richiesta di deprovisioning al termine del periodo di tolleranza. 

#### <a name="unsubscribed"></a>Annullamento della sottoscrizione 

Le sottoscrizioni raggiungano questo stato in risposta a una richiesta esplicita del cliente o mancato pagamento dei diritti. L'aspettativa dal partner è che i dati dei clienti vengano mantenuti per il ripristino su richiesta per un determinato numero di giorni e quindi eliminati. 


## <a name="api-reference"></a>Informazioni di riferimento sulle API

Questa sezione vengono illustrate le SaaS *API abbonamento* e *operazioni API*.  Il valore della `api-version` parametro per la versione 2 sono API `2018-08-31`.  


### <a name="parameter-and-entity-definitions"></a>Definizioni di parametro ed entità

Nella tabella seguente sono elencate le definizioni per i parametri comuni ed entità utilizzate dall'API di evasione degli ordini.

|     Entità/parametro     |     Definizione                         |
|     ----------------     |     ----------                         |
| `subscriptionId`         | Identificatore GUID per una risorsa di SaaS.  |
| `name`                   | Nome descrittivo fornito per questa risorsa da parte del cliente. |
| `publisherId`            | Un identificatore di stringa univoco per ogni server di pubblicazione (ad esempio: "contoso"). |
| `offerId`                | Un identificatore di stringa univoco per ogni offerta (ad esempio: "offer1").  |
| `planId`                 | Un identificatore di stringa univoco per ogni piano/SKU (ad esempio: "silver"). |
| `operationId`            | Identificatore GUID per una determinata operazione.  |
|  `action`                | L'azione eseguita su una risorsa, ovvero `unsubscribe`, `suspend`, `reinstate`, o `changePlan`, `changeQuantity`, `transfer`.  |
|   |   |

Gli identificatori univoci globali ([GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)) sono numeri (32 cifre esadecimali) a 128 bit che in genere vengono generati automaticamente. 

#### <a name="resolve-a-subscription"></a>Risolvere una sottoscrizione 

L'endpoint di risoluzione consente all'editore risolvere un token di marketplace a un ID risorsa permanente. L'ID risorsa è l'identificatore univoco per una sottoscrizione di SaaS. Quando un utente viene reindirizzato al sito Web di un partner, l'URL contiene un token nei parametri di query. Il partner deve usare questo token e chiedere di risolvere il problema. La risposta contiene l'univoco SaaS ID sottoscrizione, nome, ID offerta e piano per la risorsa. Questo token è valido per solo un'ora. 

##### <a name="postbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsresolveapi-versionapiversion"></a>Inserisci<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |
|  x-ms-marketplace-token  |  Il parametro di query token nell'URL quando l'utente viene reindirizzato al sito Web del partner SaaS di Azure (ad esempio: `https://contoso.com/signup?token=..`). *Nota:* L'URL decodifica il valore del token dal browser prima di poterla usare.  |

*Codici di risposta:*

Codice: 200<br>
Consente di risolvere il token opaco a una sottoscrizione di SaaS. Corpo della risposta:
 

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
Richiesta non valida. x-ms-marketplace-token è mancante, in formato non valido o scaduto.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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

La sottoscrizione API supporta le operazioni HTTPS seguenti: **Ottenere**, **Post**, **Patch**, e **eliminare**.


#### <a name="list-subscriptions"></a>Elencare le sottoscrizioni

Elenca tutte le sottoscrizioni di SaaS per un server di pubblicazione.

##### <a name="getbrhttpsmarketplaceapimicrosoftcomapisaassubscriptionsapi-versionapiversion"></a>Get<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`

*Parametri di query:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
| x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
| authorization      |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Codici di risposta:*

Codice: 200 <br/>
Ottiene il server di pubblicazione e le sottoscrizioni corrispondenti per le offerte dell'editore, in base al token di autenticazione.
Payload della risposta:<br>

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

È presente solo se sono presenti altre "pagine" di piani per recuperare il token di continuazione. 

Codice: 403 <br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. 

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

Ottiene la sottoscrizione di SaaS specificata. Usare questa chiamata per ottenere informazioni sulla licenza e informazioni sul piano.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token tramite l'API di risolvere.   |
|  ApiVersion        |   La versione dell'operazione da usare per questa richiesta.   |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Codici di risposta:*

Codice: 200<br>
Ottiene la sottoscrizione di SaaS dall'identificatore. Payload della risposta:<br>

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
            "termUnit": "P1M"
        },
}
```

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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

#### <a name="list-available-plans"></a>Elenco di piani disponibili

Usare questa chiamata per scoprire se esistono eventuali offerte private o pubbliche per il server di pubblicazione corrente.

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidlistavailableplansapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

*Codici di risposta:*

Codice: 200<br>
Ottiene un elenco dei piani disponibili per un cliente. Corpo della risposta:

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
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente. <br> 

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
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questa stringa mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app). |

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
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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

#### <a name="change-the-plan-on-the-subscription"></a>Modificare il piano della sottoscrizione

Aggiornare il piano della sottoscrizione.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.    |
| authorization      |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

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
| Operation-Location | Il collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
La richiesta di modifica piano è stata accettata. Il partner deve eseguire il polling Operation-Location per determinare un esito positivo o negativo. <br>

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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
>Solo un piano o una quantità può essere stato modificato con patch in una sola volta, ma non entrambi. Modifica di una sottoscrizione con **Update** non è presente nel `allowedCustomerOperations`.

#### <a name="change-the-quantity-on-the-subscription"></a>Modificare la quantità di sottoscrizione

Aggiornare la quantità della sottoscrizione.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidapi-versionapiversion"></a>benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.    |
| authorization      |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

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
La richiesta per modificare la quantità è stata accettata. Il partner deve eseguire il polling Operation-Location per determinare un esito positivo o negativo. <br>

Codice: 400<br>
Richiesta non valida: errori di convalida.


Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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
>Solo un piano o una quantità può essere stato modificato con patch in una sola volta, ma non entrambi. Modifica di una sottoscrizione con **Update** non è presente nel `allowedCustomerOperations`.

#### <a name="delete-a-subscription"></a>Eliminare una sottoscrizione

Annullare la sottoscrizione ed eliminare la sottoscrizione specificata.

##### <a name="deletebr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionid-api-versionapiversion"></a>Eliminare<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.   |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.   |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Codici di risposta:*

Codice: 202<br>
Il partner ha avviato una chiamata per annullare la sottoscrizione di una sottoscrizione di SaaS.<br>

Codice: 400<br>
Eliminare una sottoscrizione con **eliminare** non in `allowedCustomerOperations`.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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


### <a name="operations-api"></a>Operazioni API

L'API di operazioni supporta le seguenti operazioni Patch e Get.

#### <a name="list-outstanding-operations"></a>Elenco di operazioni in sospeso 

Elenca le operazioni in sospeso per il server di pubblicazione corrente. 

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`

*Parametri di query:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   La versione dell'operazione da usare per questa richiesta.                |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Codici di risposta:*

Codice: 200<br> Ottiene l'elenco di operazioni su una sottoscrizione in sospeso. Payload della risposta:

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
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

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

#### <a name="get-operation-status"></a>Ottieni stato dell'operazione

Consente all'editore di tenere traccia dello stato dell'operazione asincrona di attivazione specificato (ad esempio `subscribe`, `unsubscribe`, `changePlan`, o `changeQuantity`).

##### <a name="getbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Get<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`   |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Codici di risposta:*<br>

Codice: 200<br> Ottiene l'oggetto specificato in attesa di funzionamento delle operazioni SaaS. Payload della risposta:

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
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.
 
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

Aggiornare lo stato di un'operazione per indicare esito positivo o negativo con i valori forniti.

##### <a name="patchbr-httpsmarketplaceapimicrosoftcomapisaassubscriptionssubscriptionidoperationsoperationidapi-versionapiversion"></a>Patch<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|   ApiVersion       |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |
|  operationId       | L'operazione in corso di completamento. |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     | `application/json`   |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  authorization     |  [Ottenere un token di connessione JSON web token (JWT)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/saas-app/cpp-saas-registration#get-a-token-based-on-the-azure-ad-app).  |

*Payload della richiesta:*

```json
{
    "planId": "offer1",
    "quantity": "44",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}

```

*Codici di risposta:*

Codice: 200<br> Una chiamata a informare del completamento di un'operazione sul lato partner. Ad esempio, questa risposta è stato possibile segnalare la modifica di postazioni o piani.

Codice: 400<br>
Richiesta non valida: errori di convalida.

Codice: 403<br>
Non autorizzato. Il token di autenticazione non è stato specificato o non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene al server di pubblicazione corrente.

Codice: 404<br>
Non trovato.

Codice: 409<br>
Conflitto. Ad esempio, una transazione più recente è già soddisfatta.

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

Il server di pubblicazione deve implementare un webhook nel servizio SaaS per ricevere proattivamente notifiche agli utenti delle modifiche nel proprio servizio. Il servizio SaaS è previsto per chiamare le operazioni API per convalidare e l'autorizzazione prima di intraprendere un'azione sulla notifica di webhook.

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
In cui l'azione può essere uno dei seguenti: 
- `unsubscribe` (quando la risorsa è stata eliminata)
- `changePlan` (quando ha completato l'operazione del piano di modifica)
- `changeQuantity` (quando ha completato l'operazione di modifica quantità)
- `suspend` (quando risorsa è stata sospesa)
- `reinstate` (quando risorsa è stata reintrodotta dopo la sospensione)

In cui lo stato può essere uno dei seguenti: 
- **NotStarted** <br>
 - **InProgress** <br>
- **Completato** <br>
- **Non riuscito** <br>
- **Conflict** <br>

In una notifica di webhook, entrambi sono gli stati utilizzabili **Succeeded** e **Failed**. Ciclo di vita di un'operazione è da **NotStarted** a uno stato terminale, ad esempio **Succeeded**, **Failed**, oppure **conflitto**. Se si riceve **NotStarted** oppure **InProgress**, continuare a richiedere lo stato tramite l'API OTTIENI fino a quando l'operazione raggiunge uno stato terminale prima di intervenire. 

## <a name="mock-apis"></a>API fittizie

È possibile usare le nostre API fittizie che consentono di introduzione allo sviluppo, in particolare la creazione di prototipi, progetti e di test. 

Host endpoint: `https://marketplaceapi.microsoft.com/api` (Nessuna autenticazione)<br/>
Versione dell'API: `2018-09-15`<br/>
URI di esempio: `https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15` <br/>

I percorsi di endpoint API sono uguali nelle API fittizie sia reali, ma le versioni dell'API sono diverse. La versione viene `2018-09-15` per la versione fittizia e `2018-08-31` per la versione di produzione. 

Una delle chiamate API in questo articolo può accadere per l'endpoint host fittizio. In generale, aspettarsi di ottenere dati fittizi nuovamente come risposta. Chiamate ai metodi di sottoscrizione di aggiornamento sull'API fittizia restituiscono sempre 500. 

## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori possono anche a livello di programmazione recuperare e modificare i carichi di lavoro, offerte e i profili di pubblicazione usando il [API REST di Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
