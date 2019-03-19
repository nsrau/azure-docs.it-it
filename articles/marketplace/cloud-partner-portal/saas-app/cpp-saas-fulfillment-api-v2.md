---
title: SaaS evasione API V2 - Azure Marketplace | Microsoft Docs
description: Viene illustrato come creare un'offerta SaaS in Azure Marketplace con l'API V2 di evasione degli ordini associati.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: pbutlerm
ms.openlocfilehash: 7e1fa82e71738ac113bfe748a7117d0c3a10b14b
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57319026"
---
# <a name="saas-fulfillment-api-version-2"></a>Versione dell'API SaaS evasione 2

Questo articolo illustra in dettaglio le API che consente a fornitori di software indipendenti (ISV) di integrare le proprie applicazioni SaaS con Azure Marketplace. Questa API consente alle applicazioni di ISV di far parte di tutti i canali di commercio elettronico: diretto, a cura del partner (rivenditori) e a cura del campo.  Questa API è un requisito per l'elenco che SaaS transactable alle offerte in Azure Marketplace.


## <a name="managing-the-saas-subscription-lifecycle"></a>La gestione del ciclo di vita di sottoscrizione di SaaS

Microsoft SaaS Service gestisce l'intero ciclo di vita di un acquisto dell'abbonamento SaaS e Usa l'API di evasione come un meccanismo per guidare l'evasione di ordini effettivo, modifiche ai piani e l'eliminazione della sottoscrizione con il fornitore di software indipendente. Il cliente viene addebitato lo stato della sottoscrizione SaaS gestito da Microsoft. Il diagramma seguente illustra gli Stati e le operazioni che determinano le modifiche tra stati.

![Stati del ciclo di vita della sottoscrizione SaaS](./media/saas-subscription-lifecycle-api-v2.png)


### <a name="states-of-a-saas-subscription"></a>Stati di una sottoscrizione di SaaS

La tabella seguente elenca gli stati di provisioning per una sottoscrizione di SaaS, tra cui un diagramma di sequenza e descrizione per ogni (se applicabile). 

#### <a name="provisioning"></a>Provisioning

Quando un cliente avvia un acquisto, l'ISV riceve queste informazioni in un AuthCode in una pagina in web interattiva dei clienti usando un parametro URL. L'AuthCode può essere convalidate e scambiati per i dettagli di cosa è necessario eseguirne il provisioning.  Al termine del servizio SaaS di provisioning, invia una chiamata di attivazione per segnalare che è stata completata l'evasione di ordini e l'addebito al cliente può.  Il diagramma seguente mostra la sequenza di chiamate API per uno scenario di provisioning.  

![Chiamate API per il provisioning di un servizio SaaS.](./media/saas-post-provisioning-api-v2-calls.png)

#### <a name="provisioned"></a>Sottoposto a provisioning

Questo stato è lo stato stazionario di un servizio di provisioning.

#### <a name="provisioning-for-update"></a>Il provisioning per l'aggiornamento
(da marketplace) 

Questo stato rappresenta un aggiornamento a un oggetto esistente il servizio è in sospeso. L'aggiornamento può essere avviata dal cliente nel marketplace o nel servizio SaaS (solo per diretto alle transazioni del cliente.) Il diagramma seguente mostra le azioni durante un aggiornamento viene avviato dal marketplace.

![Quando si avvia un aggiornamento dal marketplace di chiamate API.](./media/saas-update-api-v2-calls-from-marketplace-a.png)

#### <a name="provisioning-for-update"></a>Il provisioning per l'aggiornamento  
(dal servizio SaaS) 

Il diagramma seguente mostra le azioni durante un aggiornamento viene avviato dal servizio SaaS. (La chiamata al webhook è sostituita da un aggiornamento alla sottoscrizione avviato da Service il SaaS. 

![Quando si avvia un aggiornamento dal servizio SaaS chiamate API.](./media/saas-update-api-v2-calls-from-saas-service-a.png) 

#### <a name="suspended"></a>Suspended

Questo stato indica che non è stato ricevuto il pagamento del cliente. Dai criteri, verranno forniti al cliente un periodo di tolleranza prima unfulfilling la sottoscrizione. Quando una sottoscrizione è in questo stato: 

- In qualità di ISV, è possibile ridurre le prestazioni o bloccare l'accesso dell'utente al servizio. 
- La sottoscrizione deve restare in uno stato recuperabile che è possibile ripristinare la funzionalità completa senza alcuna perdita di dati o impostazioni. 
- È possibile prevedere ottenere una richiesta di reintegro per questa sottoscrizione tramite l'API di evasione degli ordini o una richiesta di deprovisioning al termine del periodo di tolleranza. 

#### <a name="unsubscribed"></a>Sottoscrizione annullata 

Le sottoscrizioni raggiungano questo stato in risposta a una richiesta esplicita di clienti o in risposta a mancato pagamento delle quote. L'aspettativa dal sito dell'ISV è che i dati dei clienti vengano mantenuti per il ripristino su richiesta per un minimo di X giorni e quindi eliminati. 

## <a name="api-reference"></a>Informazioni di riferimento sulle API

Questa sezione vengono illustrate le SaaS *API abbonamento* e *operazioni API*.


### <a name="subscription-api"></a>API della sottoscrizione

La sottoscrizione API supporta le operazioni HTTPS seguenti: **Ottenere**, **Post**, **Patch**, e **eliminare**.

#### <a name="list-subscriptions"></a>Elenca sottoscrizioni

Elenca tutte le sottoscrizioni di SaaS per un server di pubblicazione.

**Ottieni:<br>`https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=<ApiVersion>`**

*Parametri di query:*

|             |                   |
|  --------   |  ---------------  |
| ApiVersion  |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Content-Type       |  `application/json`  |
| x-ms-requestid     |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
| x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
| authorization      |  Il token di connessione JSON Web token (JWT).  |

*Codici di risposta:*

Codice: 200<br>
Server di pubblicazione e sottoscrizioni corrispondenti per le offerte dell'editore in base get token auth.<br> Payload della risposta:<br>

```json
{
  "subscriptions": [
      {
          "id": "",
          "name": "CloudEndure for Production use",
          "publisherId": "cloudendure",
          "offerId": "ce-dr-tier2",
          "planId": "silver",
          "quantity": "10",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
          "allowedCustomerOperations": [
              "Read" // Possible Values: Read, Update, Delete.
          ], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
          "sessionMode": "None", // Possible Values: None, DryRun (Dry Run indicates all transactions run as Test-Mode in the commerce stack)
          "status": "Subscribed" // Indicates the status of the operation. [Provisioning, Subscribed, Suspended, Unsubscribed]
      }
  ],
  "continuationToken": ""
}
```

Codice: 403 <br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente. 

Codice: 500 - Errore interno del server

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

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
| subscriptionId     |   Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token tramite l'API di risoluzione   |
|  ApiVersion        |   Versione dell'operazione da usare per questa richiesta   |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  `application/json`  |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  Token JSON web token (JWT) bearer token  |

*Codici di risposta:*

Codice: 200<br>
Ottiene saas sottoscrizione dall'identificatore<br> Payload della risposta:<br>

```json
Response Body:
{ 
        "id":"",
        "name":"CloudEndure for Production use",
        "publisherId": "cloudendure",
        "offerId": "ce-dr-tier2",
        "planId": "silver",
        "quantity": "10"",
          "beneficiary": { // Tenant for which SaaS subscription is purchased.
              "tenantId": "cc906b16-1991-4b6d-a5a4-34c66a5202d7"
          },
          "purchaser": { // Tenant that purchased the SaaS subscription. These could be different for reseller scenario
              "tenantId": "0396833b-87bf-4f31-b81c-c67f88973512"
          },
        "allowedCustomerOperations": ["Read"], // Indicates operations allowed on the SaaS subscription. For CSP initiated purchases, this will always be Read.
        "sessionMode": "None", // Dry Run indicates all transactions run as Test-Mode in the commerce stack
        "status": "Subscribed", // Indicates the status of the operation.
}
```

Codice: 404<br>
Non trovato<br> 

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error<br>

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }  
```

#### <a name="list-available-plans"></a>Elenco di piani disponibili

Usare questa chiamata per scoprire se esistono eventuali offerte pubblica/privata per l'utente corrente.

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/listAvailablePlans?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |   Versione dell'operazione da usare per questa richiesta  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  authorization     |  Token JSON web token (JWT) bearer token |

*Codici di risposta:*

Codice: 200<br>
Ottenere un elenco dei piani disponibili per un cliente.<br>

```json
Response Body:
[{
    "planId": "silver",
    "displayName": "Silver",
    "isPrivate": false
},
{
    "planId": "silver-private",
    "displayName": "Silver-private",
    "isPrivate": true
}]
```

Codice: 404<br>
Non trovato<br> 

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente. <br> 

Codice: 500<br>
Internal Server Error<br>

```json
{ 
    "error": { 
      "code": "UnexpectedError", 
      "message": "An unexpected error has occurred." 
    } 
```

#### <a name="resolve-a-subscription"></a>Risolvere una sottoscrizione 

L'endpoint di risoluzione consente agli utenti di risolvere un token di marketplace a un ID di risorsa permanente. L'ID risorsa è l'identificatore univoco per la sottoscrizione SAAS.  Quando un utente viene reindirizzato al sito Web di un ISV, l'URL contiene un token nei parametri di query. Si presuppone che l'ISV usi questo token ed esegua una richiesta per risolverlo. La risposta contiene l'ID di sottoscrizione SAAS univoco, il nome, l'ID dell'offerta e il piano per la risorsa. Questo token è valido solo per un'ora. 

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/resolve?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versione dell'operazione da usare per questa richiesta  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  authorization     |  Token JSON web token (JWT) bearer token  |
|  x-ms-marketplace-token  |  Parametro di query token nell'URL quando l'utente viene reindirizzato al sito Web dell'ISV SaaS da Azure. *Nota:* L'URL decodifica il valore del token dal browser prima di poterla usare. |

*Codici di risposta:*

Codice: 200<br>
Consente di risolvere il token opaco a una sottoscrizione di SaaS.<br>

```json
Response body:
{
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",  
    "subscriptionName": "My Saas application",
    "offerId": "ce-dr-tier2",
    "planId": "silver",
    "quantity": "20",
    "operationId": " be750acb-00aa-4a02-86bc-476cbe66d7fa"  
}
```

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="activate-a-subscription"></a>Attivare una sottoscrizione

**Post:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/activate?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  Versione dell'operazione da usare per questa richiesta  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risoluzione  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json`  |
|  x-ms-requestid    | Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid  | Valore stringa univoco per l'operazione sul client. Mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  Token JSON web token (JWT) bearer token |

*Richiesta:*

```json
{
    "planId": "gold",
    "quantity": ""
}
```

*Codici di risposta:*

Codice: 202<br>
Attiva la sottoscrizione.<br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="update-a-subscription"></a>Aggiornare una sottoscrizione

Aggiornare o modificare un piano di sottoscrizione con i valori forniti.

**benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      | `application/json` |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid  |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.    |
| authorization      |  Il token di connessione JSON Web token (JWT).  |

*Payload della richiesta:*

```json
Request Body:
{
    "planId": "gold",
    "quantity": ""
}
```

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
| Operation-Location | Collegamento a una risorsa per ottenere lo stato dell'operazione.   |

*Codici di risposta:*

Codice: 202<br>
ISV avvia un piano di modifica o la quantità di modifiche. <br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida delle richieste non valida.

>[!Note]
>Solo un piano o una quantità può essere stato modificato con patch in una sola volta, ma non entrambi. Modifica di una sottoscrizione con **Update** non è presente nel `allowedCustomerOperations`.

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}
```

#### <a name="delete-a-subscription"></a>Elimina una sottoscrizione

Annullare la sottoscrizione ed eliminare la sottoscrizione specificata.

**Delete:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId> ?api-version=<ApiVersion>`**

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
|  authorization     |  Il token di connessione JSON Web token (JWT).   |

*Codici di risposta:*

Codice: 200<br>
Chiamata di ISV avviata per indicare annullare la sottoscrizione in una sottoscrizione di SaaS.<br>

Codice: 404<br>
Non trovato

Codice: 400<br>
Eliminare una sottoscrizione con **eliminare** non in `allowedCustomerOperations`.

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error

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


#### <a name="update-a-subscription"></a>Aggiornare una sottoscrizione

Aggiornare una sottoscrizione con i valori forniti.

**benda:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operation/<operationId>?api-version=<ApiVersion>`**

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
|   x-ms-requestid   |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta. |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta. |
|  authorization     |  Il token di connessione JSON Web token (JWT).  |

*Payload della richiesta:*

```json
{
    "planId": "",
    "quantity": "",
    "status": "Success"    // Allowed Values: Success/Failure. Indicates the status of the operation.
}
```

*Codici di risposta:*

Codice: 200<br> Chiamata a informare del completamento di un'operazione sul lato fornitore di software indipendente. Ad esempio, potrebbe trattarsi di cambiamento dei posti a sedere/piani.

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 409<br>
Conflitto. Ad esempio, una transazione più recente viene già acquisita

Codice: 500<br> Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="list-outstanding-operations"></a>Elenco di operazioni in sospeso 

Elenca le operazioni in sospeso per l'utente corrente. 

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations?api-version=<ApiVersion>`**

*Parametri di query:*

|             |        |
|  ---------------   |  ---------------  |
|    ApiVersion                |   La versione dell'operazione da usare per questa richiesta.                |
| subscriptionId     | Identificatore univoco della sottoscrizione di SaaS che si ottiene dopo avere risolto il token usando l'API di risolvere.  |

*Intestazioni della richiesta:*
 
|                    |                   |
|  ---------------   |  ---------------  |
|   Content-Type     |  `application/json` |
|  x-ms-requestid    |  Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     |  Il token di connessione JSON Web token (JWT).  |

*Codici di risposta:*

Codice: 200<br> Ottiene l'elenco di operazioni su una sottoscrizione in sospeso.<br>
Payload della risposta:

```json
[{
    "id": "be750acb-00aa-4a02-86bc-476cbe66d7fa",  
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId": "cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",  
    "status": "NotStarted"  
}]
```

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.

Codice: 500<br>
Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

#### <a name="get-operation-status"></a>Ottieni stato dell'operazione

Consente all'utente di tenere traccia dello stato di un'operazione asincrona attivate (piano di "Subscribe" / annullamento della sottoscrizione/modifica).

**Ottieni:<br> `https://marketplaceapi.microsoft.com/api/saas/subscriptions/<subscriptionId>/operations/<operationId>?api-version=<ApiVersion>`**

*Parametri di query:*

|                    |                   |
|  ---------------   |  ---------------  |
|  ApiVersion        |  La versione dell'operazione da usare per questa richiesta.  |

*Intestazioni della richiesta:*

|                    |                   |
|  ---------------   |  ---------------  |
|  Content-Type      |  ` application/json`   |
|  x-ms-requestid    |   Valore stringa univoco per tenere traccia della richiesta dal client, preferibilmente un GUID. Se il valore non viene fornito, ne verrà generato e fornito uno nelle intestazioni della risposta.  |
|  x-ms-correlationid |  Valore stringa univoco per l'operazione sul client. Questo parametro mette in correlazione tutti gli eventi dall'operazione del client con gli eventi sul lato server. Se questo valore non è specificato, uno verrà generato e fornito nelle intestazioni della risposta.  |
|  authorization     | Il token di connessione JSON Web token (JWT).  |

*Codici di risposta:* Codice: 200<br> Ottiene l'elenco di tutte le operazioni in sospeso di SaaS<br>
Payload della risposta:

```json
Response body:
[{
    "id  ": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",  
    "planId": "silver",
    "quantity": "20",
    "action": "Convert",
    "timeStamp": "2018-12-01T00:00:00",
    "status": "NotStarted"
}]

```

Codice: 404<br>
Non trovato

Codice: 400<br>
Errori di convalida-richiesta non valida

Codice: 403<br>
Non autorizzato. Non è stato fornito il token di autenticazione, non è valido o la richiesta sta tentando di accedere a un'acquisizione che non appartiene all'utente corrente.
 
Codice: 500<br> Internal Server Error

```json
{
    "error": {
      "code": "UnexpectedError",
      "message": "An unexpected error has occurred."
    }
}

```

## <a name="webhook-on-the-saas-service"></a>Webhook nel servizio SaaS

Il server di pubblicazione deve implementare un webhook nel servizio SaaS per ricevere proattivamente notifiche agli utenti delle modifiche nel proprio servizio. L'API deve essere non autenticato e verrà chiamato dal servizio Microsoft SaaS. Il servizio SaaS è previsto per chiamare le operazioni API per convalidare e l'autorizzazione prima di intraprendere un'azione sulla notifica di webhook.

```json
{
    "operationId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "activityId": "be750acb-00aa-4a02-86bc-476cbe66d7fa",
    "subscriptionId":"cd9c6a3a-7576-49f2-b27e-1e5136e57f45",
    "offerId": "ce-dr-tier2",
    "publisherId": "cloudendure",
    "planId": "silver",
    "quantity": "20"  ,
    "action": "Activate",   // Activate/Delete/Suspend/Reinstate/Change[new]  
    "timeStamp": "2018-12-01T00:00:00"
}

```

<!-- Review following, might not be needed when this publishes -->


## <a name="mock-api"></a>API fittizia

È possibile usare le nostre API fittizie che consentono di iniziare con lo sviluppo, in particolare la creazione di prototipi e progetti di test. 

Endpoint dell'host: https://marketplaceapi.microsoft.com/api Versione dell'API: 2018-09-15 Nessuna autenticazione obbligatorio Uri di esempio: https://marketplaceapi.microsoft.com/api/saas/subscriptions?api-version=2018-09-15

Una delle chiamate API in questo articolo può accadere per l'endpoint host fittizio. È possibile prevedere ottenere dati fittizi nuovamente come risposta.


## <a name="next-steps"></a>Passaggi successivi

Gli sviluppatori possono recuperare anche a livello di codice e la manipolazione dei carichi di lavoro, offerte e server di pubblicazione dei profili utilizzando il [API REST di Cloud Partner Portal](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview).
